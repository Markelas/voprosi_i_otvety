## 🔄 Polling с условием остановки - poll(fn, interval, shouldStop)

### Задача

Напишите функцию `poll(fn, interval, shouldStop)`, которая:
- Выполняет асинхронную функцию `fn` каждые `interval` миллисекунд
- После каждого выполнения проверяет результат через функцию `shouldStop(result)`
- Если `shouldStop` возвращает `true`, останавливает polling и возвращает последний результат
- Если `shouldStop` возвращает `false`, ждёт `interval` миллисекунд и повторяет

---

### Зачем это нужно?

Отслеживать статус долгой операции (загрузка файла, обработка видео, оплата). Каждые 2 секунды проверять статус, пока не станет "готово". Используется в прогресс-барах.

---

### Решение:

```js
async function poll(fn, interval, shouldStop) {
    while (true) {
        const result = await fn();
        
        if (shouldStop(result)) {
            return result;
        }
        
        await new Promise(resolve => setTimeout(resolve, interval));
    }
}
```

---

### Пример использования:

```js
const checkOrderStatus = async () => {
    const response = await fetch('/api/order/123');
    return response.json();
};

const order = await poll(
    checkOrderStatus,
    2000, // проверять каждые 2 секунды
    (result) => result.status === 'delivered' // остановить когда доставлен
);

console.log('Заказ доставлен!', order);
```

---

### Объяснение:

1. **Бесконечный цикл:** Используем `while (true)` для повторения
2. **Выполнение функции:** Вызываем `fn()` и получаем результат
3. **Проверка условия:** Вызываем `shouldStop(result)` для проверки
4. **Остановка:** Если условие выполнено, возвращаем результат
5. **Задержка:** Если нет, ждём `interval` миллисекунд и повторяем

---

### Улучшенная версия (с максимальным количеством попыток):

```js
async function poll(fn, interval, shouldStop, maxAttempts = Infinity) {
    let attempts = 0;
    
    while (attempts < maxAttempts) {
        const result = await fn();
        attempts++;
        
        if (shouldStop(result)) {
            return result;
        }
        
        await new Promise(resolve => setTimeout(resolve, interval));
    }
    
    throw new Error(`Polling остановлен: достигнуто максимальное количество попыток (${maxAttempts})`);
}
```

---

### Версия с обработкой ошибок:

```js
async function poll(fn, interval, shouldStop, options = {}) {
    const {
        maxAttempts = Infinity,
        onError = null,
        retryOnError = false
    } = options;
    
    let attempts = 0;
    
    while (attempts < maxAttempts) {
        try {
            const result = await fn();
            attempts++;
            
            if (shouldStop(result)) {
                return result;
            }
            
            await new Promise(resolve => setTimeout(resolve, interval));
        } catch (error) {
            if (onError) {
                onError(error, attempts);
            }
            
            if (!retryOnError) {
                throw error;
            }
            
            // При ошибке тоже ждём перед повтором
            await new Promise(resolve => setTimeout(resolve, interval));
        }
    }
    
    throw new Error(`Polling остановлен: достигнуто максимальное количество попыток (${maxAttempts})`);
}
```

---

### Версия с возможностью отмены:

```js
async function poll(fn, interval, shouldStop, signal = null) {
    while (true) {
        // Проверяем, не была ли отмена запрошена
        if (signal && signal.aborted) {
            throw new Error('Polling отменён');
        }
        
        const result = await fn();
        
        if (shouldStop(result)) {
            return result;
        }
        
        // Ждём с возможностью отмены
        await new Promise((resolve, reject) => {
            const timeoutId = setTimeout(resolve, interval);
            
            if (signal) {
                signal.addEventListener('abort', () => {
                    clearTimeout(timeoutId);
                    reject(new Error('Polling отменён'));
                });
            }
        });
    }
}

// Использование с AbortController
const controller = new AbortController();
const pollPromise = poll(
    checkOrderStatus,
    2000,
    (result) => result.status === 'delivered',
    controller.signal
);

// Отменить polling
controller.abort();
```

---

### Примеры использования:

#### 1. Отслеживание статуса заказа:

```js
const order = await poll(
    async () => {
        const response = await fetch('/api/order/123');
        return response.json();
    },
    2000,
    (result) => result.status === 'delivered' || result.status === 'cancelled'
);
```

#### 2. Ожидание готовности файла:

```js
const file = await poll(
    async () => {
        const response = await fetch('/api/file/123/status');
        return response.json();
    },
    1000,
    (result) => result.ready === true,
    { maxAttempts: 60 } // максимум 60 попыток (1 минута)
);
```

#### 3. Проверка статуса платежа:

```js
const payment = await poll(
    async () => {
        const response = await fetch('/api/payment/123');
        return response.json();
    },
    3000,
    (result) => result.status === 'completed' || result.status === 'failed',
    {
        maxAttempts: 20, // максимум 20 попыток (1 минута)
        onError: (error) => console.log('Ошибка при проверке:', error)
    }
);
```

---

### Особенности:

- **Интервал:** Проверка происходит каждые `interval` миллисекунд
- **Условие остановки:** Функция `shouldStop` определяет, когда прекратить polling
- **Гибкость:** Можно добавить максимальное количество попыток, обработку ошибок, отмену
- **Применение:** Отслеживание долгих операций, статусов, прогресса

---

### Сравнение с setInterval:

```js
// setInterval (не рекомендуется для async)
const intervalId = setInterval(async () => {
    const result = await checkStatus();
    if (result.ready) {
        clearInterval(intervalId);
    }
}, 2000);

// poll (рекомендуется)
const result = await poll(checkStatus, 2000, (r) => r.ready);
```

**Преимущества poll:**
- Ожидает завершения предыдущего запроса перед следующим
- Легче обрабатывать ошибки
- Можно отменить через AbortController
- Возвращает промис с результатом

