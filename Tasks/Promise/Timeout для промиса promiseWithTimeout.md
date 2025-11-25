## ⏰ Timeout для промиса - promiseWithTimeout(promise, ms)

### Задача

Напишите функцию `promiseWithTimeout(promise, ms)`, которая принимает промис и время в миллисекундах. Если промис не резолвится за указанное время, функция должна выбросить ошибку `'Timeout exceeded'`. Если промис успевает резолвиться — вернуть его результат.

---

### Зачем это нужно?

Если API не отвечает за разумное время, лучше показать ошибку, чем ждать вечно. Улучшает UX — пользователь быстрее узнает о проблеме.

---

### Решение:

```js
async function promiseWithTimeout(promise, ms) {
    // Создаём промис, который отклонится через ms миллисекунд
    const timeoutPromise = new Promise((_, reject) => {
        setTimeout(() => {
            reject(new Error('Timeout exceeded'));
        }, ms);
    });
    
    // Соревнуемся между исходным промисом и таймаутом
    return Promise.race([promise, timeoutPromise]);
}
```

---

### Пример использования:

```js
try {
    const data = await promiseWithTimeout(
        fetch('https://slow-api.com/data').then(r => r.json()),
        5000
    );
    console.log(data);
} catch (error) {
    console.log(error.message); // 'Timeout exceeded' если не успел за 5 сек
}
```

---

### Объяснение:

1. **Таймаут-промис:** Создаём промис, который отклоняется через `ms` миллисекунд
2. **Promise.race:** Соревнуемся между исходным промисом и таймаутом
3. **Победитель:** Кто первый завершится — тот и победит
4. **Ошибка:** Если таймаут выиграл, выбрасываем ошибку

---

### Улучшенная версия (с отменой таймера):

```js
async function promiseWithTimeout(promise, ms) {
    let timeoutId;
    
    const timeoutPromise = new Promise((_, reject) => {
        timeoutId = setTimeout(() => {
            reject(new Error('Timeout exceeded'));
        }, ms);
    });
    
    try {
        const result = await Promise.race([promise, timeoutPromise]);
        clearTimeout(timeoutId); // Отменяем таймер, если промис успел
        return result;
    } catch (error) {
        clearTimeout(timeoutId); // Отменяем таймер в любом случае
        throw error;
    }
}
```

---

### Вариант с AbortController (для fetch):

```js
async function promiseWithTimeout(promise, ms) {
    const controller = new AbortController();
    const timeoutId = setTimeout(() => {
        controller.abort();
    }, ms);
    
    try {
        const result = await promise;
        clearTimeout(timeoutId);
        return result;
    } catch (error) {
        clearTimeout(timeoutId);
        if (error.name === 'AbortError') {
            throw new Error('Timeout exceeded');
        }
        throw error;
    }
}

// Использование с fetch
const data = await promiseWithTimeout(
    fetch('https://api.example.com/data', { signal: controller.signal }),
    5000
);
```

---

### Особенности:

- **Таймаут:** Ограничивает время выполнения промиса
- **Ошибка:** Выбрасывает понятную ошибку при превышении времени
- **Отмена:** Можно отменить таймер, если промис успел завершиться
- **Применение:** Защита от зависших запросов, улучшение UX

---

### Пример с несколькими таймаутами:

```js
async function fetchWithMultipleTimeouts(url, timeouts) {
    for (const timeout of timeouts) {
        try {
            return await promiseWithTimeout(fetch(url), timeout);
        } catch (error) {
            if (error.message === 'Timeout exceeded') {
                continue; // Пробуем следующий таймаут
            }
            throw error;
        }
    }
    throw new Error('Все таймауты исчерпаны');
}
```

