```js
/**
 * Реализовать метод times для числового объекта.
 * Функция должна принимать callback и вызывать его
 * заданное количество раз с индексом текущей итерации.
 */

Number.prototype.times = function (callback) {
    for (let i = 0; i < this; i++) {
        callback(i);
    }
}

// Примеры
console.clear();
(3).times(console.log)
// 0
// 1
// 2

```