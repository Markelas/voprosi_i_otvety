```js
function simpleGroupBy(array, keyGetter) {
    const result = {};
    
    for (const item of array) {
        const key = keyGetter(item);
        
        if (!result[key]) {
            result[key] = [];
        }
        
        result[key].push(item);
    }
    
    return result;
}

// Использование:
const people = [
    { name: "Анна", age: 25 },
    { name: "Иван", age: 30 },
    { name: "Мария", age: 25 }
];

const byAge = simpleGroupBy(people, person => person.age);
console.log(byAge);
// {
//   25: [{ name: "Анна", age: 25 }, { name: "Мария", age: 25 }],
//   30: [{ name: "Иван", age: 30 }]
// }
```