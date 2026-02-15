## 📝 Что такое JSX

**JSX** — синтаксический сахар для `React.createElement(component, props, ...children)`.

Он позволяет писать HTML-подобный код внутри JavaScript.

---

### 🔄 Как он преобразуется:

**Babel транслирует JSX в вызовы `React.createElement`.**

```jsx
// JSX
const element = <h1 className="title">Hello</h1>;

// Преобразуется в:
const element = React.createElement(
  'h1',
  { className: 'title' },
  'Hello'
);
```

---

### 📝 Особенности:

- JSX должен возвращать один корневой элемент (или фрагмент `<>...</>`)
- Используй `className` вместо `class`
- Используй `htmlFor` вместо `for` (в `<label>`)
- Можно встраивать JavaScript-выражения через `{ }`

---

### 💡 Пример:

```jsx
const name = "React";
const element = (
  <div>
    <h1>Hello, {name}!</h1>
    <button onClick={() => alert('Click')}>Click me</button>
  </div>
);
```

**Преобразуется в:**

```js
React.createElement(
  'div',
  null,
  React.createElement('h1', null, 'Hello, ', name, '!'),
  React.createElement('button', { onClick: () => alert('Click') }, 'Click me')
);
```
