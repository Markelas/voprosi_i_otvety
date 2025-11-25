## 💾 Кеш для промисов - cachedFetch(url, ttl)

### Задача

Напишите функцию `cachedFetch(url, ttl)`, которая делает fetch-запрос, но кеширует результат на время `ttl` (time to live в миллисекундах). Если в течение TTL происходит повторный запрос к тому же URL, функция должна вернуть закешированный результат без нового fetch-запроса. После истечения TTL кеш для этого URL должен инвалидироваться.

---

### Зачем это нужно?

Если пользователь переходит туда-сюда между страницами, не загружать одни и те же данные заново. Закешировать на 5 минут — ускоряет приложение, экономит трафик.

---

### Решение:

```js
const cache = new Map();

async function cachedFetch(url, ttl) {
    const now = Date.now();
    const cached = cache.get(url);
    
    // Проверяем, есть ли валидный кеш
    if (cached && (now - cached.timestamp) < ttl) {
        return cached.data;
    }
    
    // Делаем новый запрос
    const response = await fetch(url);
    if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
    }
    const data = await response.json();
    
    // Сохраняем в кеш с временной меткой
    cache.set(url, {
        data: data,
        timestamp: now
    });
    
    return data;
}
```

---

### Пример использования:

```js
// Первый запрос - делает fetch
const fetch1 = await cachedFetch('/api/user', 5000);
console.log('Данные из сети:', fetch1);

// Второй запрос (в течение 5 секунд) - возвращает из кеша
const fetch2 = await cachedFetch('/api/user', 5000);
console.log('Данные из кеша:', fetch2);

// Через 5+ секунд - новый запрос
setTimeout(async () => {
    const fetch3 = await cachedFetch('/api/user', 5000);
    console.log('Новые данные из сети:', fetch3);
}, 6000);
```

---

### Объяснение:

1. **Кеш:** Используем `Map` для хранения кешированных данных
2. **Проверка:** Проверяем наличие кеша и его валидность (не истёк ли TTL)
3. **Возврат из кеша:** Если кеш валиден, возвращаем закешированные данные
4. **Новый запрос:** Если кеша нет или он истёк, делаем новый fetch
5. **Сохранение:** Сохраняем результат в кеш с временной меткой

---

### Улучшенная версия (с автоматической очисткой):

```js
const cache = new Map();

// Очистка устаревших записей
function cleanExpiredCache(ttl) {
    const now = Date.now();
    for (const [url, cached] of cache.entries()) {
        if ((now - cached.timestamp) >= ttl) {
            cache.delete(url);
        }
    }
}

async function cachedFetch(url, ttl) {
    // Периодически очищаем кеш
    cleanExpiredCache(ttl);
    
    const now = Date.now();
    const cached = cache.get(url);
    
    if (cached && (now - cached.timestamp) < ttl) {
        return cached.data;
    }
    
    const response = await fetch(url);
    if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
    }
    const data = await response.json();
    
    cache.set(url, {
        data: data,
        timestamp: now
    });
    
    return data;
}
```

---

### Версия с Promise-кешем (избегает дублирующих запросов):

```js
const cache = new Map();

async function cachedFetch(url, ttl) {
    const now = Date.now();
    const cached = cache.get(url);
    
    // Если есть валидный кеш, возвращаем его
    if (cached && (now - cached.timestamp) < ttl) {
        return cached.data;
    }
    
    // Если уже есть запрос в процессе, возвращаем его промис
    if (cached && cached.promise) {
        return cached.promise;
    }
    
    // Создаём новый запрос
    const promise = fetch(url)
        .then(response => {
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            return response.json();
        })
        .then(data => {
            // Сохраняем результат в кеш
            cache.set(url, {
                data: data,
                timestamp: Date.now(),
                promise: null
            });
            return data;
        })
        .catch(error => {
            // Удаляем промис при ошибке
            const cached = cache.get(url);
            if (cached && cached.promise === promise) {
                cache.delete(url);
            }
            throw error;
        });
    
    // Сохраняем промис в кеш (чтобы избежать дублирующих запросов)
    cache.set(url, {
        data: null,
        timestamp: now,
        promise: promise
    });
    
    return promise;
}
```

---

### Особенности:

- **TTL:** Время жизни кеша в миллисекундах
- **Автоматическая инвалидация:** Кеш истекает после TTL
- **Экономия:** Избегает повторных запросов к одному URL
- **Производительность:** Ускоряет повторные запросы

---

### Пример с разными TTL:

```js
// Кеш на 1 минуту для часто меняющихся данных
const user = await cachedFetch('/api/user', 60000);

// Кеш на 5 минут для стабильных данных
const config = await cachedFetch('/api/config', 300000);

// Кеш на 1 час для редко меняющихся данных
const categories = await cachedFetch('/api/categories', 3600000);
```

