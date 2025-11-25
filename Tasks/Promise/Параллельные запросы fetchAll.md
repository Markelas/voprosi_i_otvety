## ⚡ Параллельные запросы - fetchAll(urls)

### Задача

Напишите функцию `fetchAll(urls)`, которая принимает массив URL-адресов. Функция должна выполнить fetch-запросы ко всем URL параллельно и вернуть массив результатов в том же порядке. Если хотя бы один запрос упадёт, вся функция должна выбросить ошибку.

---

### Зачем это нужно?

Когда нужно загрузить данные из нескольких источников одновременно (профиль + посты + друзья). Экономит время: вместо 3×2сек = 6сек, всё загрузится за 2сек.

---

### Решение:

```js
async function fetchAll(urls) {
    const promises = urls.map(url => fetch(url).then(response => {
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }
        return response.json();
    }));
    
    return Promise.all(promises);
}
```

---

### Пример использования:

```js
const urls = [
    'https://api.example.com/user',
    'https://api.example.com/posts',
    'https://api.example.com/comments'
];

const results = await fetchAll(urls);
console.log(results); // [userData, postsData, commentsData]
```

---

### Объяснение:

1. Используем `map` для создания массива промисов
2. Каждый промис делает `fetch` и обрабатывает ответ
3. Используем `Promise.all` для параллельного выполнения
4. `Promise.all` сохраняет порядок результатов и выбрасывает ошибку при первом провале

---

### Альтернативное решение (с async/await в map):

```js
async function fetchAll(urls) {
    const promises = urls.map(async (url) => {
        const response = await fetch(url);
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }
        return response.json();
    });
    
    return Promise.all(promises);
}
```

---

### Вариант с обработкой ошибок для каждого запроса:

```js
async function fetchAll(urls) {
    const promises = urls.map(async (url) => {
        try {
            const response = await fetch(url);
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            return await response.json();
        } catch (error) {
            throw new Error(`Ошибка при запросе ${url}: ${error.message}`);
        }
    });
    
    return Promise.all(promises);
}
```

---

### Особенности:

- **Параллельность:** Все запросы выполняются одновременно
- **Порядок:** Результаты возвращаются в том же порядке, что и URL
- **Ошибки:** Если один запрос падает, весь `Promise.all` отклоняется
- **Производительность:** Быстрее последовательного выполнения

---

### Сравнение с последовательным выполнением:

```js
// Параллельно (быстро) - ~2 секунды
const results = await fetchAll([url1, url2, url3]);

// Последовательно (медленно) - ~6 секунд
const result1 = await fetch(url1);
const result2 = await fetch(url2);
const result3 = await fetch(url3);
```

