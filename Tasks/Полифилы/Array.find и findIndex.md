## 🔄 Полифилы Array.find и Array.findIndex

### Реализация:

```js
// Array.prototype.find
Array.prototype.myFind = function(callback) {
  for (let i = 0; i < this.length; i++) {
    // Если callback возвращает true, возвращаем элемент
    if (callback(this[i], i, this)) {
      return this[i];
    }
  }
  // Если ничего не найдено, возвращаем undefined
  return undefined;
};

// Array.prototype.findIndex
Array.prototype.myFindIndex = function(callback) {
  for (let i = 0; i < this.length; i++) {
    // Если callback возвращает true, возвращаем индекс
    if (callback(this[i], i, this)) {
      return i;
    }
  }
  // Если ничего не найдено, возвращаем -1
  return -1;
};
```

---

### Пример использования:

```js
const numbers = [1, 2, 3, 4, 5];

// find
const found = numbers.myFind((num) => num > 3); // 4
const notFound = numbers.myFind((num) => num > 10); // undefined

// findIndex
const foundIndex = numbers.myFindIndex((num) => num > 3); // 3
const notFoundIndex = numbers.myFindIndex((num) => num > 10); // -1

// С объектами
const people = [
  { name: 'Alice', age: 25 },
  { name: 'Bob', age: 30 },
  { name: 'Charlie', age: 35 }
];

const person = people.myFind((p) => p.age > 28); // { name: 'Bob', age: 30 }
const personIndex = people.myFindIndex((p) => p.age > 28); // 1
```

---

### Объяснение:

**myFind:**
1. Проходим по массиву
2. Вызываем `callback` для каждого элемента
3. Если `callback` возвращает `true`, возвращаем элемент
4. Если ничего не найдено, возвращаем `undefined`

**myFindIndex:**
1. То же самое, но возвращаем индекс элемента вместо самого элемента
2. Если ничего не найдено, возвращаем `-1`

**Разница:**
- `find` возвращает **элемент** или `undefined`
- `findIndex` возвращает **индекс** или `-1`
