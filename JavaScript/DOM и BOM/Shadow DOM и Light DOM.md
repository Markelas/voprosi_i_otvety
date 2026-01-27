# Shadow DOM и Light DOM

## Краткий ответ для собеседования

**Light DOM** — это обычный DOM, с которым мы работаем каждый день. **Shadow DOM** — изолированное DOM-дерево, прикреплённое к элементу, которое не видно снаружи и защищено от внешних стилей. Используется в Web Components для инкапсуляции.

---

## Light DOM

**Light DOM** — это обычное DOM-дерево, которое мы видим в инспекторе браузера.

**Характеристики:**
- **Глобальная область видимости** — все элементы доступны через `document.querySelector()`
- **Стили влияют на всё** — CSS из одного компонента может случайно затронуть другие
- **Нет изоляции** — всё взаимосвязано

**Пример:**

```html
<div class="container">
  <p>Это Light DOM</p>
</div>
```

Любой CSS на странице может стилизовать этот `<p>`.

---

## Shadow DOM

**Shadow DOM** — это отдельное DOM-дерево, прикреплённое к элементу (**shadow host**), которое изолировано от основного документа.

**Зачем нужен:**
- **Инкапсуляция стилей** — CSS внутри shadow DOM не влияет на внешний документ, и наоборот
- **Инкапсуляция структуры** — элементы внутри shadow DOM не видны через `document.querySelector()`
- **Переиспользование** — компоненты не конфликтуют друг с другом

**Пример создания:**

```js
const host = document.querySelector('#my-element');
const shadowRoot = host.attachShadow({ mode: 'open' });

shadowRoot.innerHTML = `
  <style>
    p { color: red; }
  </style>
  <p>Это Shadow DOM</p>
`;
```

**Результат:**
- `<p>` внутри shadow DOM **красный**
- Внешние `<p>` на странице **не изменятся**
- `document.querySelector('p')` **не найдёт** этот `<p>` внутри shadow DOM

---

## Основные концепции Shadow DOM

### 1. Shadow Host (Хост теневого дерева)

**Shadow Host** — элемент, к которому прикреплён shadow DOM.

```html
<div id="my-element"></div> <!-- Это shadow host -->
```

### 2. Shadow Root (Корень теневого дерева)

**Shadow Root** — корень теневого DOM-дерева, точка входа.

```js
const shadowRoot = host.attachShadow({ mode: 'open' });
```

### 3. Shadow Tree (Теневое дерево)

**Shadow Tree** — само DOM-дерево внутри shadow root.

```html
<!-- Внутри shadow root -->
<style>...</style>
<div>...</div>
```

---

## Режимы Shadow DOM

### `mode: 'open'`

**Открытый режим** — shadow root доступен через свойство `.shadowRoot`:

```js
const shadowRoot = host.attachShadow({ mode: 'open' });
console.log(host.shadowRoot); // доступен
```

**Использование:**  
Большинство случаев, когда нужна инкапсуляция, но с возможностью доступа.

---

### `mode: 'closed'`

**Закрытый режим** — shadow root **недоступен** извне:

```js
const shadowRoot = host.attachShadow({ mode: 'closed' });
console.log(host.shadowRoot); // null
```

**Использование:**  
Редко, для максимальной изоляции (но можно обойти, сохранив ссылку).

---

## Инкапсуляция стилей

### Стили внутри Shadow DOM

Стили, определённые в shadow DOM, **не влияют** на внешний документ:

```js
shadowRoot.innerHTML = `
  <style>
    p { color: red; }
  </style>
  <p>Красный текст</p>
`;
```

- `<p>` **внутри shadow DOM** — красный
- `<p>` **снаружи** — не изменится

---

### Стили снаружи НЕ влияют на Shadow DOM

```html
<style>
  p { color: blue; } /* Не повлияет на shadow DOM */
</style>
```

**Исключения:**
- **Наследуемые свойства** — `color`, `font-family` наследуются от хоста
- **CSS-переменные** — проникают в shadow DOM

```css
/* Внешний CSS */
:root {
  --main-color: green;
}

/* Внутри shadow DOM */
p {
  color: var(--main-color); /* Будет зелёный */
}
```

---

## Селекторы для Shadow DOM

### `:host` — стилизация shadow host

Селектор для самого хоста (элемента, к которому прикреплён shadow DOM):

```css
:host {
  display: block;
  border: 1px solid black;
}
```

### `:host()` — условная стилизация хоста

```css
:host(.active) {
  background: yellow;
}
```

### `:host-context()` — стилизация в зависимости от родителя

```css
:host-context(.dark-theme) {
  background: black;
  color: white;
}
```

### `::slotted()` — стилизация slotted-контента

Для элементов, вставленных через `<slot>`:

```css
::slotted(p) {
  color: blue;
}
```

---

## Slots — вставка контента

**Slots** позволяют вставлять Light DOM в Shadow DOM.

**Пример:**

```html
<!-- Shadow DOM -->
<template id="my-template">
  <style>
    p { color: red; }
  </style>
  <div>
    <slot name="header"></slot>
    <slot></slot> <!-- default slot -->
  </div>
</template>

<script>
  class MyElement extends HTMLElement {
    constructor() {
      super();
      const template = document.querySelector('#my-template');
      const shadowRoot = this.attachShadow({ mode: 'open' });
      shadowRoot.appendChild(template.content.cloneNode(true));
    }
  }
  customElements.define('my-element', MyElement);
</script>

<!-- Использование -->
<my-element>
  <h1 slot="header">Заголовок</h1>
  <p>Основной контент</p>
</my-element>
```

**Результат:**
- `<h1>` вставляется в `<slot name="header">`
- `<p>` вставляется в default `<slot>`

**Важно:**  
Slotted-контент остаётся в Light DOM, но **отображается** в Shadow DOM.

---

## Где используется Shadow DOM

### 1. Встроенные элементы браузера

Многие элементы используют shadow DOM под капотом:
- `<video>`, `<audio>` — кнопки управления
- `<input type="range">` — слайдер
- `<input type="date">` — календарь
- `<details>`, `<summary>` — раскрывающиеся блоки

### 2. Web Components

Пользовательские элементы с инкапсуляцией:

```js
class MyButton extends HTMLElement {
  constructor() {
    super();
    const shadow = this.attachShadow({ mode: 'open' });
    shadow.innerHTML = `
      <style>button { background: blue; color: white; }</style>
      <button><slot></slot></button>
    `;
  }
}
customElements.define('my-button', MyButton);
```

### 3. Библиотеки компонентов

- **Lit** — для создания Web Components
- **Stencil** — компилятор Web Components
- **Salesforce Lightning** — UI-фреймворк

---

## Доступ к элементам

### Элементы внутри Shadow DOM НЕ видны извне

```js
document.querySelector('p'); // НЕ найдёт <p> внутри shadow DOM
```

### Доступ через shadowRoot

```js
const shadowRoot = host.shadowRoot; // если mode: 'open'
const p = shadowRoot.querySelector('p'); // найдёт <p> внутри shadow DOM
```

---

## События в Shadow DOM

События **всплывают** из Shadow DOM в Light DOM, но `event.target` изменяется:

**Внутри shadow DOM:**
- `event.target` — элемент внутри shadow DOM

**Снаружи (в Light DOM):**
- `event.target` — **shadow host** (инкапсуляция сохраняется)

**Пример:**

```js
shadowRoot.innerHTML = `<button>Click</button>`;

document.addEventListener('click', (e) => {
  console.log(e.target); // выведет shadow host, а не <button>
});
```

**Исключения:**  
Некоторые события не всплывают из shadow DOM (например, `focus`, `blur` — используйте `focusin`, `focusout`).

---

## Сравнение Light DOM и Shadow DOM

| Параметр | Light DOM | Shadow DOM |
|----------|-----------|------------|
| **Видимость** | Виден везде | Изолирован |
| **Стили** | Глобальные (влияют на всё) | Локальные (только внутри) |
| **`querySelector`** | Доступны все элементы | Недоступны снаружи |
| **Инкапсуляция** | Нет | Есть |
| **Использование** | Обычные веб-страницы | Web Components, встроенные элементы |

---

## Вопросы на собеседовании

| Вопрос | Краткий ответ |
|--------|----------------|
| Что такое Light DOM? | Обычный DOM, с которым мы работаем, без изоляции |
| Что такое Shadow DOM? | Изолированное DOM-дерево, прикреплённое к элементу |
| Зачем нужен Shadow DOM? | Инкапсуляция стилей и структуры (стили не утекают, элементы не видны снаружи) |
| В чём разница между `mode: 'open'` и `'closed'`? | Open — доступен через `.shadowRoot`, closed — недоступен |
| Влияют ли внешние стили на Shadow DOM? | Нет, кроме наследуемых свойств и CSS-переменных |
| Что такое slot? | Место в Shadow DOM для вставки Light DOM-контента |
| Где используется Shadow DOM? | Web Components, встроенные элементы браузера (`<video>`, `<input type="range">`) |
| Можно ли найти элемент в Shadow DOM через `querySelector`? | Нет, только через `shadowRoot.querySelector()` |

---

## Кратко для ответа

**Light DOM** — обычный DOM без изоляции. **Shadow DOM** — изолированное дерево, прикреплённое к элементу, где стили и структура инкапсулированы (не влияют на внешний документ и наоборот). Используется в Web Components для переиспользования компонентов без конфликтов. Создаётся через `attachShadow()`, может быть `open` (доступен через `.shadowRoot`) или `closed` (недоступен). Slots позволяют вставлять Light DOM в Shadow DOM.
