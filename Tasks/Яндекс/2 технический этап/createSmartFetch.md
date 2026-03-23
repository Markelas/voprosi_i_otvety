```js
/*
Необходимо реализовать функцию createSmartFetch(ms), которая:
- Принимает таймаут в миллисекундах
- Возвращает функцию, которая принимает id и возвращает Promise с данными
- Запросы должны накапливаться в течение ms миллисекунд
- По истечении ms все накопленные id отправляются одним batch-запросом через batchFetch
- Каждый вызов должен получить результат для своего id
*/

function createSmartFetch(delayMs) {
    // Массив для накопления ID запросов, сделанных за период ожидания
    // Пример: [10, 20, 30]
    let ids = [];
    
    // Таймер, который по истечении delayMs отправит все накопленные запросы
    let timer = null;
    
    // Map для хранения функций resolve каждого Promise
    // Ключ: id запроса, значение: функция resolve
    // Позволяет позже вернуть результат конкретному вызову
    let resolvers = new Map();
    
    // Возвращаем функцию, которую будут вызывать для получения данных по id
    return function smartFetch(id) {
        // Каждый вызов возвращает Promise, который будет разрешён,
        // когда придёт результат из batch-запроса
        return new Promise(resolve => {
            // 1. СОХРАНЯЕМ ЗАПРОС В ОЧЕРЕДЬ
            ids.push(id);                    // добавляем ID в массив
            resolvers.set(id, resolve);       // сохраняем resolve для этого ID
            
            // 2. ЗАПУСКАЕМ ТАЙМЕР (ЕСЛИ ЕЩЁ НЕ ЗАПУЩЕН)
            if (!timer) {
                timer = setTimeout(() => {
                    // 3. КОГДА ТАЙМЕР СРАБАТЫВАЕТ - ОТПРАВЛЯЕМ BATCH-ЗАПРОС
                    
                    // Делаем копии текущих данных, чтобы очистить очередь
                    const batch = [...ids];           // копия массива ID
                    const resolves = new Map(resolvers); // копия Map с resolve
                    
                    // Очищаем очередь для следующего батча
                    ids = [];
                    resolvers.clear();
                    timer = null;
                    
                    // 4. ВЫПОЛНЯЕМ BATCH-ЗАПРОС
                    batchFetch(batch).then(results => {
                        // 5. ДЛЯ КАЖДОГО ЗАПРОШЕННОГО ID НАХОДИМ РЕЗУЛЬТАТ
                        batch.forEach(requestId => {
                            // Ищем результат с нужным ID
                            const result = results.find(r => r.id === requestId);
                            
                            // Получаем сохранённую функцию resolve для этого ID
                            const resolveFn = resolves.get(requestId);
                            
                            // Вызываем resolve с результатом
                            resolveFn(result);
                        });
                    });
                }, delayMs);
            }
        });
    };
}
```