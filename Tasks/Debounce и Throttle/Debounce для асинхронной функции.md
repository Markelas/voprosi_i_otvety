## ⏱️ Debounce для асинхронной функции - debounceAsync(fn, delay)

### Задача

Напишите функцию `debounceAsync(fn, delay)`, которая принимает асинхронную функцию и задержку. Возвращаемая функция должна откладывать выполнение `fn` до тех пор, пока не пройдёт `delay` миллисекунд с момента последнего вызова. Если функция вызывается снова до истечения задержки, предыдущий таймер должен сбрасываться.

---

### Зачем это нужно?

При поиске с автодополнением: если пользователь печатает "JavaScript", не делать 10 запросов (J, Ja, Jav...), а подождать паузу и сделать один запрос. Экономит трафик и нагрузку на сервер.

---

### Решение:

```js
function debounceAsync(fn, delay) {
    let timeoutId;
    
    return async function(...args) {
        // Очищаем предыдущий таймер
        clearTimeout(timeoutId);
        
        // Создаём новый промис, который резолвится после задержки
        return new Promise((resolve, reject) => {
            timeoutId = setTimeout(async () => {
                try {
                    const result = await fn(...args);
                    resolve(result);
                } catch (error) {
                    reject(error);
                }
            }, delay);
        });
    };
}
```

---

### Пример использования:

```js
const searchAPI = async (query) => {
    const response = await fetch(`/api/search?q=${query}`);
    return response.json();
};

const debouncedSearch = debounceAsync(searchAPI, 500);

// При быстром вводе выполнится только последний вызов (через 500мс после него)
input.addEventListener('input', async (e) => {
    try {
        const results = await debouncedSearch(e.target.value);
        console.log(results);
    } catch (error) {
        console.error('Ошибка поиска:', error);
    }
});
```

---

### Объяснение:

1. **Таймер:** Используем `setTimeout` для задержки выполнения
2. **Сброс:** При каждом новом вызове очищаем предыдущий таймер
3. **Промис:** Возвращаем промис, который резолвится после выполнения функции
4. **Обработка ошибок:** Пробрасываем ошибки через `reject`

---

### Улучшенная версия (с отменой предыдущих запросов):

```js
function debounceAsync(fn, delay) {
    let timeoutId;
    let lastPromise = null;
    let lastController = null;
    
    return async function(...args) {
        // Отменяем предыдущий запрос, если он ещё выполняется
        if (lastController) {
            lastController.abort();
        }
        
        // Очищаем предыдущий таймер
        clearTimeout(timeoutId);
        
        // Создаём новый AbortController для этого запроса
        const controller = new AbortController();
        lastController = controller;
        
        return new Promise((resolve, reject) => {
            timeoutId = setTimeout(async () => {
                try {
                    // Проверяем, не был ли запрос отменён
                    if (controller.signal.aborted) {
                        reject(new Error('Request cancelled'));
                        return;
                    }
                    
                    const result = await fn(...args, { signal: controller.signal });
                    resolve(result);
                } catch (error) {
                    if (error.name === 'AbortError') {
                        reject(new Error('Request cancelled'));
                    } else {
                        reject(error);
                    }
                } finally {
                    lastController = null;
                }
            }, delay);
        });
    };
}
```

---

### Версия с сохранением последнего результата:

```js
function debounceAsync(fn, delay) {
    let timeoutId;
    let lastResult = null;
    let lastError = null;
    
    return async function(...args) {
        clearTimeout(timeoutId);
        
        return new Promise((resolve, reject) => {
            timeoutId = setTimeout(async () => {
                try {
                    const result = await fn(...args);
                    lastResult = result;
                    lastError = null;
                    resolve(result);
                } catch (error) {
                    lastError = error;
                    lastResult = null;
                    reject(error);
                }
            }, delay);
        });
    };
}
```

---

### Особенности:

- **Задержка:** Выполнение откладывается на `delay` миллисекунд
- **Сброс таймера:** Каждый новый вызов сбрасывает предыдущий таймер
- **Асинхронность:** Работает с асинхронными функциями
- **Применение:** Поиск, автодополнение, валидация форм

---

### Сравнение с обычным debounce:

```js
// Обычный debounce (синхронный)
function debounce(fn, delay) {
    let timeoutId;
    return function(...args) {
        clearTimeout(timeoutId);
        timeoutId = setTimeout(() => fn(...args), delay);
    };
}

// Асинхронный debounce (возвращает промис)
function debounceAsync(fn, delay) {
    let timeoutId;
    return async function(...args) {
        clearTimeout(timeoutId);
        return new Promise((resolve, reject) => {
            timeoutId = setTimeout(async () => {
                try {
                    resolve(await fn(...args));
                } catch (error) {
                    reject(error);
                }
            }, delay);
        });
    };
}
```

---

### Пример использования в React:

```js
import { useMemo } from 'react';

function SearchComponent() {
    const debouncedSearch = useMemo(
        () => debounceAsync(async (query) => {
            const response = await fetch(`/api/search?q=${query}`);
            return response.json();
        }, 500),
        []
    );
    
    const handleInput = async (e) => {
        const results = await debouncedSearch(e.target.value);
        setSearchResults(results);
    };
    
    return <input onInput={handleInput} />;
}
```

