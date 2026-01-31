## 🎯 Что такое `call`, `apply`, `bind` и `this`

---

### 🔹 `call` и `apply`

Оба вызывают функцию **сразу** с указанным `this`, но:

- **`call`** — принимает аргументы **через запятую**
- **`apply`** — принимает аргументы **массивом**

```js
function greet(greeting, punctuation) {
  console.log(`${greeting}, ${this.name}${punctuation}`);
}

const user = { name: "Alice" };

greet.call(user, "Hello", "!");      // Hello, Alice!
greet.apply(user, ["Hi", "..."]);    // Hi, Alice...
```

---

### 🔹 `bind`

**Не вызывает** функцию, а возвращает **новую функцию** с привязанным `this`.

```js
const boundGreet = greet.bind(user, "Hey");
boundGreet("?");  // Hey, Alice?
```

---

### 🔹 `this`

**`this`** — это ссылка на объект, который вызывает функцию в данный момент.

```js
const obj = {
  name: "John",
  sayHello() {
    console.log(`Hello, ${this.name}`);
  }
};

obj.sayHello(); // Hello, John
```

---

### ⚠️ Важно про стрелочные функции:

**У стрелочных функций своего `this` нет** — они берут его из внешнего контекста.

```js
const obj = {
  name: "John",
  regular: function() {
    console.log(this.name); // John
  },
  arrow: () => {
    console.log(this.name); // undefined (берёт this из глобального контекста)
  }
};
```
