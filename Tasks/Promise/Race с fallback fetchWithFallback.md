## 🏃 Race с fallback - fetchWithFallback(urls)

### Задача

Напишите функцию `fetchWithFallback(urls)`, которая принимает массив URL (в порядке приоритета). Функция должна попробовать первый URL, если он не отвечает за 3 секунды — попробовать второй, и так далее. Вернуть результат первого успешного запроса. Если все URL не сработали, выбросить ошибку.

---

### Зачем это нужно?

Есть основной быстрый API и запасной медленный. Если основной не ответил за 3 сек — переключиться на запасной. Повышает надёжность приложения.

---

### Решение:

```js
async function fetchWithFallback(urls) {
    for (let i = 0; i < urls.length; i++) {
        try {
            // Пробуем запрос с таймаутом 3 секунды
            const response = await promiseWithTimeout(
                fetch(urls[i]),
                3000
            );
            
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            
            return await response.json();
        } catch (error) {
            // Если это последний URL, выбрасываем ошибку
            if (i === urls.length - 1) {
                throw new Error(`Все URL не сработали. Последняя ошибка: ${error.message}`);
            }
            // Иначе пробуем следующий URL
            continue;
        }
    }
}

// Вспомогательная функция для таймаута
async function promiseWithTimeout(promise, ms) {
    const timeoutPromise = new Promise((_, reject) => {
        setTimeout(() => reject(new Error('Timeout exceeded')), ms);
    });
    return Promise.race([promise, timeoutPromise]);
}
```

---

### Пример использования:

```js
const urls = [
    'https://fast-api.com/data',      // попробовать первым
    'https://backup-api.com/data',    // если первый не ответил за 3с
    'https://slow-backup.com/data'    // последний вариант
];

try {
    const data = await fetchWithFallback(urls);
    console.log('Данные получены:', data);
} catch (error) {
    console.log('Все источники недоступны:', error.message);
}
```

---

### Объяснение:

1. **Цикл по URL:** Пробуем каждый URL по порядку приоритета
2. **Таймаут:** Для каждого запроса устанавливаем таймаут 3 секунды
3. **Успех:** Если запрос успешен, возвращаем результат
4. **Ошибка:** Если запрос не удался, пробуем следующий URL
5. **Исчерпание:** Если все URL не сработали, выбрасываем ошибку

---

### Альтернативное решение (с Promise.race для каждого URL):

```js
async function fetchWithFallback(urls) {
    const errors = [];
    
    for (const url of urls) {
        try {
            const timeoutPromise = new Promise((_, reject) => {
                setTimeout(() => reject(new Error('Timeout')), 3000);
            });
            
            const response = await Promise.race([
                fetch(url),
                timeoutPromise
            ]);
            
            if (!response.ok) {
                throw new Error(`HTTP ${response.status}`);
            }
            
            return await response.json();
        } catch (error) {
            errors.push({ url, error: error.message });
            // Продолжаем со следующим URL
        }
    }
    
    throw new Error(`Все URL не сработали: ${JSON.stringify(errors)}`);
}
```

---

### Улучшенная версия (с логированием):

```js
async function fetchWithFallback(urls, timeout = 3000) {
    for (let i = 0; i < urls.length; i++) {
        const url = urls[i];
        console.log(`Попытка ${i + 1}/${urls.length}: ${url}`);
        
        try {
            const response = await promiseWithTimeout(fetch(url), timeout);
            
            if (!response.ok) {
                throw new Error(`HTTP ${response.status}`);
            }
            
            const data = await response.json();
            console.log(`Успех с ${url}`);
            return data;
        } catch (error) {
            console.log(`Ошибка с ${url}: ${error.message}`);
            
            if (i === urls.length - 1) {
                throw new Error(`Все ${urls.length} URL не сработали`);
            }
        }
    }
}
```

---

### Особенности:

- **Приоритет:** URL пробуются в порядке приоритета
- **Таймаут:** Каждый запрос имеет таймаут 3 секунды
- **Fallback:** Автоматическое переключение на резервный источник
- **Надёжность:** Повышает шансы получить данные

---

### Пример использования в реальном приложении:

```js
// Основной API быстрый, но может быть недоступен
// Резервный API медленный, но более стабильный
const data = await fetchWithFallback([
    'https://api-primary.example.com/data',
    'https://api-backup.example.com/data',
    'https://api-legacy.example.com/data'
]);
```

