## ⚡ Задачи на async/await

---

### Задача 1: Ошибка без await

```js
console.log('start'); // 1

async function main() {
  console.log('async'); // 2
  try {
    Promise.reject(new Error('Error inside Promise')); // попадает в микрозадачу
  } catch (error) {
    console.log('Caught:', error.message); // не выполнится, из-за того, что нет await у Promise
  }
}

main();
console.log('end') // 3
```

**Объяснение:** Без `await` промис не будет обработан в `try/catch`. Ошибка уйдёт в unhandled rejection.

**Правильно:**

```js
try {
  await Promise.reject(new Error('Error inside Promise'));
} catch (error) {
  console.log('Caught:', error.message); // Выполнится
}
```

---

### Задача 2: Promise без await

```js
console.log('start'); // 1

const promise = new Promise((resolve) => {
  console.log(1); // Синхронно → 1
});

promise.then(res => {
  console.log(2); // Не выполнится, так как нет resolve()
});

console.log('end'); // 2

// Будет: start 1 end
```

**Объяснение:** Создание промиса — синхронная операция. Без `resolve()` или `reject()` промис остаётся в состоянии `pending`, и `.then()` не выполнится.
