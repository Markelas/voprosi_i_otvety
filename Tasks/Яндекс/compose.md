```js
/**
 * Реализовать функцию compose, которая принимает
 * переменное количество функций и возвращает новую функцию.
 * Результат работы каждой функции передается в следующую.
 */

const compose = (...fns) => {
    return (...args) => {
        return fns.reduceRight((acc, fn) => {
            // Если acc - массив, разворачиваем его как аргументы
            // Если нет - передаем как один аргумент
            return Array.isArray(acc) ? fn(...acc) : fn(acc);
        }, args);
    }
}



const square = (x) => x * x;
const times2 = (x) => x * 2;
const sum = (a, b) => a + b;
const array = (c) => [c / 2, c / 3];

console.clear();
// console.log(compose(square, times2)(2));
console.log(compose(square, times2, sum)(3, 4) === square(times2(sum(3, 4))));
```