## ⏱️ Задачи на Event Loop

---

### Задача 1

```js
console.log('1'); // Синхронный код → вывод: 1

setTimeout(() => {
  console.log('2'); // Макротаска 1 → вывод: 2
  Promise.resolve().then(() => {
    console.log('3'); // Микротаска после макротаски 1 → вывод: 3
  });
  setTimeout(() => {
    console.log('4'); // Макротаска 4 → вывод: 4 (будет выполнена позже)
  }, 0);
}, 0);

Promise.resolve().then(() => {
  console.log('5'); // Микротаска 1 → вывод: 5
  setTimeout(() => {
    console.log('6'); // Макротаска 3 → вывод: 6 (выполнится после макротаски 1)
  }, 0);
});

console.log('7'); // Синхронный код → вывод: 7

queueMicrotask(() => {
  console.log('8'); // Микротаска 2 → вывод: 8
  Promise.resolve().then(() => {
    console.log('9'); // Вложенная микротаска после микротаски 2 → вывод: 9
  });
});

console.log('10'); // Синхронный код → вывод: 10
```

---

### Порядок вывода:

```
1    // синхронно
7    // синхронно
10   // синхронно
5    // микротаска (Promise)
8    // микротаска (queueMicrotask)
9    // вложенная микротаска (Promise внутри queueMicrotask)
2    // макротаска (первый setTimeout)
3    // микротаска (Promise внутри первого setTimeout)
6    // макротаска (setTimeout внутри микротаски 5)
4    // макротаска (setTimeout внутри первого setTimeout)
```

---

### Объяснение:

1. Вначале идут синхронные `console.log()`
2. Затем микротаски (`Promise` и `queueMicrotask` на одном уровне — что раньше будет, то и выполнится)
3. Выполняются вложенные в них синхронные и микротаски
4. Макротаски `setTimeout` попадают в очередь и выполняются позже
5. После каждой макротаски выполняются все накопившиеся микротаски

---

### Задача 2

```js
console.log(1);

setTimeout(() => console.log(2), 0);

Promise.resolve().then(() => {
  console.log(3);
  return Promise.resolve(4);
}).then(console.log);

(async function() {
  console.log(5);
  await null;
  console.log(6);
})();

console.log(7);
```

---

### Порядок вывода:

```
1, 5, 7, 3, 4, 6, 2
```

---

### Объяснение:

1. `console.log(1)` — синхронно → 1
2. `setTimeout(..., 0)` — макротаска, идёт в очередь
3. `Promise.resolve().then(...)` — микротаска
4. `console.log(5)` — синхронно в async → 5
5. `await null` — переводит оставшийся код (`console.log(6)`) в микротаски
6. `console.log(7)` — синхронно → 7
7. Микротаски: первый `.then` → 3, возвращает `Promise.resolve(4)` → новый `.then(console.log)` → 4
8. После этого микротаска от `await` → 6
9. Затем макротаски — `setTimeout` → 2
