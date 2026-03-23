```js
/**
 * Необходимо написать функцию, которая на вход принимает объект
 * и убирает из него все falsy значения
 * falsy значение - это такое значение value, для которого Boolean(value) === false
 * считаем, что obj - результат выполнения JSON.parse, то есть plain object
 */

function filterFalsy(obj) {
    // Примитивы возвращаем как есть
    if (typeof obj !== 'object' || obj === null) {
        return obj;
    }

    // Работаем с массивами
    if (Array.isArray(obj)) {
        const result = [];
        for (const item of obj) {
            const filtered = filterFalsy(item);
            if (filtered) {  // Проверяем truthy
                result.push(filtered);
            }
        }
        return result;
    }

    // Работаем с объектами
    const result = {};
    for (const key in obj) {
        const filtered = filterFalsy(obj[key]);
        if (filtered) {  // Проверяем truthy
            result[key] = filtered;
        }
    }
    return result;
}

console.log(filterFalsy([null, 0, false, 1])); // => [1]
console.log(filterFalsy({ 'a': null, 'b': [false, 1] })); // => { 'b': [1] }
console.log(filterFalsy([null, 0, 5, [0], [false, 16]])); // => [5, [], [16]]
```