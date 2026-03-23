```js
/* Напишите функцию memo(в Java — статический метод memoize),
 которая принимает другую функцию и возвращает её мемоизированную версию.
 Мемоизация — это техника оптимизации, при которой результаты вызовов функции кэшируются,
 чтобы при повторном вызове с теми же аргументами не выполнять вычисления заново.
 */


const map = new Map();
const memo = (fn) => {
    return function memoized(...args) {
        const agrsString = args.join()
        if (map.has(agrsString)) {
            return `${map.get(agrsString)} (из кэша)`;
        }

        const result = fn(...args);
        map.set(agrsString, result);

        return result;
    }
};

const memoizedPow = memo((a, b) => a ** b);

console.log(memoizedPow(2, 3)) // 8
console.log(memoizedPow(2, 3)) // 8 (из кэша)
console.log(memoizedPow(2, 4)) // 16
console.log(map);
```