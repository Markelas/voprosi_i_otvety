```js
// Определение: Монотонный массив — это массив, элементы которого 
// либо только не убывают(каждый следующий ≥ предыдущего),
// либо только не возрастают(каждый следующий ≤ предыдущего).
// Равные соседние элементы допустимы в обоих случаях.

// Задача: Написать функцию`isMonotonic(arr)`, которая принимает массив чисел и
// возвращает`true`, если массив монотонный, и`false` в противном случае.


function isMonotonic(array) {
    let isSmallest = array.every((digit, index) => index === 0 || digit <= array[index - 1])
    let isBiggetst = array.every((digit, index) => index === 0 || digit >= array[index - 1])

    return isSmallest || isBiggetst
}




console.log(isMonotonic([1, 2, 3, 4]))     // → true  (возрастающий)
console.log(isMonotonic([4, 3, 2, 1]))     // → true  (убывающий)
console.log(isMonotonic([1, 1, 2, 3]))     // → true  (нестрого возрастающий)
console.log(isMonotonic([1, 1, 1, 1]))     // → true  (все равные)
console.log(isMonotonic([1, 3, 2]))        // → false
console.log(isMonotonic([1, 2, 2, 1]))     // → false (сначала растёт, потом убывает)

```

Второе решение, через флаги

```js
function isMonotonic(array) {
    let isBiggest = true;
    let isSmallest = true;

    for (let i = 1; i < array.length; i++) {
        if (array[i] > array[i - 1]) {
            isSmallest = false;
        }

        if (array[i] < array[i - 1]) {
            isBiggest = false;
        }

        if (!isBiggest && !isSmallest) return false
    }
}


```