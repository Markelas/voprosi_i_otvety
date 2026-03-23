```js
function oddSort(arr) {
    // 1. Собираем все нечетные числа и сортируем их
    const odds = arr.filter(num => num % 2 !== 0).sort((a, b) => a - b);
    
    // 2. Создаем новый массив
    const result = [];
    let oddIndex = 0;
    
    // 3. Проходим по исходному массиву
    for (let i = 0; i < arr.length; i++) {
        if (arr[i] % 2 !== 0) {
            // Если число нечетное - берем из отсортированных
            result.push(odds[oddIndex]);
            oddIndex++;
        } else {
            // Если четное - оставляем на месте
            result.push(arr[i]);
        }
    }
    
    return result;
}
```

```js
function oddSort(arr) {
    const odds = arr.filter(n => n % 2).sort((a, b) => a - b);
    return arr.map(n => n % 2 ? odds.shift() : n);
}
```