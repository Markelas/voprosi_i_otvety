## 🛑 AbortController — отмена запроса

**`AbortController`** — встроенный класс, который позволяет отменить асинхронную операцию, например `fetch()`.

---

### 🔧 Как использовать:

1. Создаётся объект контроллера:
```js
const controller = new AbortController();
```

2. У него есть два ключевых свойства:
- **`controller.signal`** — специальный объект, который передаётся в функцию (например, `fetch`)
- **`controller.abort()`** — вызывает отмену

После `abort()` — промис `fetch` переходит в состояние `rejected` с ошибкой `AbortError`.

---

### 💡 Пример:

```js
const controller = new AbortController();
const { signal } = controller;  // signal — это "кабель", по которому идёт сигнал отмены

// 1️⃣ При создании запроса мы передаём signal,
//    чтобы fetch "слушал" этот кабель
fetch('https://api.example.com/data', { signal })
  .then(res => res.json())
  .catch(err => {
    if (err.name === 'AbortError') {
      console.log('Запрос отменён');
    }
  });

// 2️⃣ При отмене мы вызываем abort() на самом controller,
//    а не на signal. Controller генерирует сигнал отмены,
//    который автоматически доходит до всех fetch'ей,
//    использующих этот signal.
setTimeout(() => controller.abort(), 100);
```

---

### 🔗 Один signal для нескольких запросов

Один `signal` можно использовать для **нескольких запросов** — тогда `abort()` отменит все сразу.

---

### 🎯 Использование для обработчиков событий

⚠️ **Важно:** можно использовать не только для `fetch`, но и для отмены обработчиков событий. Это современный, чистый способ безопасно снимать слушатели.

```js
const controller = new AbortController();
const { signal } = controller;

document.addEventListener('click', () => {
  console.log('Клик!');
}, { signal });

// потом можно отменить все слушатели, привязанные к этому сигналу
controller.abort();
```
