## 🔗 Цепочки Promise

### Задача 1: Последовательное выполнение

```js
const f1 = () => new Promise((resolve) => setTimeout(() => resolve('done f1'), 1000));
const f2 = () => new Promise((resolve) => setTimeout(() => resolve('done f2'), 1000));
const f3 = () => new Promise((resolve) => setTimeout(() => resolve('done f3'), 1000));

f1().then((res) => {
  console.log(res);  // done f1
  return f2();
}).then((res) => {
  console.log(res);  // done f2
  return f3();
}).then((res) => {
  console.log(res);  // done f3
});
```

---

### Задача 2: Обработка ошибок

```js
Promise.reject(1)
  .then(data => {
    console.log(data); // Не выполнится, так как reject
  })
  .then(null, data => console.log(data)) // Здесь второй параметр, обработка как catch
  .then(() => console.log('ok')); // Так как прошлый сработал, сработает и этот

// Будет: 1 ok
```

---

### Задача 3: Возврат значений

```js
Promise.resolve("1")
  .then(data => {
    console.log(data); // 1
  })
  .then(data => {
    console.log(data); // undefined, так как не было return
    return "2";
  })
  .then(data => {
    console.log(data); // 2
  });
```

---

### Задача 4: null как обработчик

```js
Promise.resolve("1")
  .then(null) // Это не функция, то промис просто пропускает этот обработчик
  .then(data => console.log(data)) // Отобразится 1
```

---

### Задача 5: Порядок выполнения микротасок

```js
Promise.resolve()
  .then(() => console.log(1))  // Сразу
  .then(() => console.log(2))  // Попадает в микротаску, так как это новая задача

Promise.resolve()
  .then(() => console.log(3)) // Сразу по очереди, после 1
  .then(() => console.log(4)) // Попадает в микротаску, так как это новая задача (после двойки)

// Будет: 1 3 2 4
```

---

### Задача 6: Повторные попытки (retry)

```js
const delay = (ms) => new Promise(resolve => setTimeout(resolve, ms));

async function tryAuth(authFn, retries, delayMs = 0) {
  let count = retries;

  while (count > 0) {
    count--;

    try {
      let data = await authFn();
      return data;
    } catch (e) {
      if (count) {
        await delay(delayMs);
      } else {
        throw e;
      }
    }
  }
}
```
