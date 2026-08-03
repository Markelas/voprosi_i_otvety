```js
const reduce = (callback, initial, arr) => {
  if (arr.length === 0) return initial;
  const [first, ...rest] = arr;
  return callback(first, reduce(callback, initial, rest));
};

const sumOfSquares = (arr) => {
  // Изменять можно только здесь
};

const result = sumOfSquares([1, 2, 3]);

console.log(result);
// Ожидаемый результат: 14
```