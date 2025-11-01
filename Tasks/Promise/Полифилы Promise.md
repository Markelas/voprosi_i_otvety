## 🔄 Полифилы Promise

### Алгоритм решения задач на полифилы:

1. Все методы класса Promise принимают массив промисов или значений
2. Все методы класса Promise возвращают новый промис (`return new Promise()`)
3. Если массив передан пустым:
   - `any` — Already rejected
   - `all`, `allSettled` — Already fulfilled (`[]`)
   - `race` — остаётся в `pending` навсегда
4. Все входные данные преобразуются к промисам (`Promise.resolve(value)`)
5. Методы, которые возвращают все результаты, возвращают в том же порядке, в котором были переданы (нужен счётчик и индекс)
6. Успешное завершение: `all` — все успешно, `any` — хотя бы один успешно, `race` — первый завершённый
7. Отклонение: `all` — если любой отклоняется, `any` — если все отклоняются (AggregateError), `race` — первый отклонённый

---

### Promise.all()

```js
function promiseAll(promises) {
  return new Promise((resolve, reject) => {
    if (!promises.length) {
      resolve([]);
      return;
    }

    let arr = [];
    let counter = 0;

    for (let i = 0; i < promises.length; i++) {
      Promise.resolve(promises[i])
        .then((res) => {
          arr[i] = res;  // Сохраняем по индексу для сохранения порядка
          counter++;

          if (promises.length === counter) {
            resolve(arr);
          }
        })
        .catch((e) => {
          reject(e);
        });
    }
  });
}
```

**Важно:** Используем счётчик выполненных, а не длину `arr`, чтобы правильно отслеживать завершение.

---

### Promise.allSettled()

```js
function myAllSettled(promises) {
  const result = [];
  let counter = 0;

  return new Promise((resolve) => {
    if (!Array.isArray(promises) || promises.length === 0) {
      resolve([]);
      return;
    }

    for (let i = 0; i < promises.length; i++) {
      Promise.resolve(promises[i])
        .then((data) => {
          result[i] = { status: 'fulfilled', value: data };
        })
        .catch((error) => {
          result[i] = { status: 'rejected', reason: error };
        })
        .finally(() => {
          counter++;

          if (counter === promises.length) {
            resolve(result);
          }
        });
    }
  });
}
```

---

### Promise.race()

```js
function myRace(promises) {
  return new Promise((resolve, reject) => {
    if (!Array.isArray(promises) || !promises.length) {
      return; // Остаётся в pending
    }

    for (let i = 0; i < promises.length; i++) {
      Promise.resolve(promises[i])
        .then((data) => resolve(data))
        .catch((error) => reject(error));
    }
  });
}
```

---

### Promise.any()

```js
function myPromiseAny(promises) {
  return new Promise((resolve, reject) => {
    if (!Array.isArray(promises) || promises.length === 0) {
      reject(new AggregateError([], 'All promises were rejected'));
      return;
    }

    const errors = [];
    let counter = 0;

    for (let i = 0; i < promises.length; i++) {
      Promise.resolve(promises[i])
        .then(resolve)
        .catch((err) => {
          errors[i] = err;
          counter++;

          if (counter === promises.length) {
            reject(new AggregateError(errors, 'All promises were rejected'));
          }
        });
    }
  });
}
```

---

### Памятка к Promise:

**Для `.then(onFulfilled, onRejected)`:**

- Если `onFulfilled` не функция → значение передаётся следующему выполненному промису
- Если `onRejected` не функция → ошибка передаётся следующему отклонённому промису

**Для `.catch(onRejected)`:**

- `onRejected` не функция → ошибка передаётся следующему отклонённому промису

**Для `.finally(callback)`:**

- Выполняется ВСЕГДА (при успехе и ошибке)
- Не получает аргументов — в data приходит `undefined`
- Не изменяет результат — возвращаемое значение игнорируется
- Передаёт исходный результат дальше по цепочке
