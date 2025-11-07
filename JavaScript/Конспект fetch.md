## 📡 Конспект по `fetch()`

**`fetch()`** — встроенная функция браузера для выполнения HTTP-запросов. Возвращает `Promise` с объектом `Response`.

---

### 🔧 Базовый синтаксис

```js
fetch(url, options?)
```

- **`url`** — адрес запроса (строка или объект URL)
- **`options`** — объект с настройками (метод, заголовки, тело и т.д.)

---

### 📝 Основные методы запросов

#### GET запрос:

```js
fetch('https://api.example.com/users')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

#### POST запрос:

```js
fetch('https://api.example.com/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    name: 'John',
    age: 30
  })
})
  .then(response => response.json())
  .then(data => console.log(data));
```

---

### ⚙️ Основные опции в `options`

| Параметр | Описание | Пример |
|----------|----------|--------|
| **`method`** | HTTP-метод | `'GET'`, `'POST'`, `'PUT'`, `'DELETE'` |
| **`headers`** | Заголовки запроса | `{ 'Content-Type': 'application/json' }` |
| **`body`** | Тело запроса | `JSON.stringify({...})`, `FormData`, строка |
| **`credentials`** | Отправка cookies | `'include'`, `'same-origin'` |
| **`mode`** | Режим CORS | `'cors'`, `'no-cors'`, `'same-origin'` |

---

### 📦 Объект Response

#### Методы чтения данных:

```js
response.json()        // → Promise с JSON объектом
response.text()        // → Promise со строкой
response.blob()        // → Promise с Blob
response.arrayBuffer() // → Promise с ArrayBuffer
response.formData()    // → Promise с FormData
```

#### Полезные свойства:

```js
response.ok          // true если статус 200-299
response.status      // код статуса (200, 404, 500...)
response.statusText  // текст статуса ('OK', 'Not Found'...)
response.headers     // объект с заголовками ответа
```

---

### ✅ Обработка ответа и ошибок

#### Проверка успешного ответа:

```js
fetch(url)
  .then(response => {
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    return response.json();
  })
  .then(data => console.log(data))
  .catch(error => console.error('Ошибка:', error));
```

**Важно:** `fetch` не отклоняет промис при HTTP ошибках (404, 500). Нужно проверять `response.ok`.

---

### 🔐 Заголовки

#### Установка заголовков:

```js
fetch(url, {
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer token123',
    'X-Custom-Header': 'value'
  }
});
```

#### Чтение заголовков ответа:

```js
response.headers.get('Content-Type');
response.headers.get('Set-Cookie');
```

---

### 🛑 Отмена запроса (AbortController)

```js
const controller = new AbortController();

fetch(url, {
  signal: controller.signal
})
  .then(response => response.json())
  .catch(error => {
    if (error.name === 'AbortError') {
      console.log('Запрос отменён');
    }
  });

// Отменить запрос
controller.abort();
```

---

### 📤 Отправка данных

#### JSON:

```js
fetch(url, {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({ name: 'John' })
});
```

#### FormData:

```js
const formData = new FormData();
formData.append('name', 'John');
formData.append('file', fileInput.files[0]);

fetch(url, {
  method: 'POST',
  body: formData
});
```

---

### 🔄 Async/Await синтаксис

```js
async function fetchData() {
  try {
    const response = await fetch(url);
    
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Ошибка:', error);
  }
}
```

---

### 💡 Важные особенности

- ✅ `fetch` всегда асинхронный, возвращает `Promise`
- ⚠️ Не бросает ошибки на HTTP ошибки (404, 500) — нужно проверять `response.ok`
- ✅ По умолчанию не отправляет cookies — нужно указать `credentials: 'include'`
- ✅ Поддерживает отмену через `AbortController`
- ✅ Замена старого `XMLHttpRequest`
