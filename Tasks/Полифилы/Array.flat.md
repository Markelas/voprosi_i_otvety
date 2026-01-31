## 🔄 Полифил Array.flat

### Реализация:

```js
Array.prototype.myFlat = function(depth = 1) {
  const result = [];

  function flatten(arr, currentDepth) {
    for (let i = 0; i < arr.length; i++) {
      // Если элемент - массив и не достигли максимальной глубины
      if (Array.isArray(arr[i]) && currentDepth < depth) {
        // Рекурсивно разглаживаем вложенный массив
        flatten(arr[i], currentDepth + 1);
      } else {
        // Иначе просто добавляем элемент
        result.push(arr[i]);
      }
    }
  }

  flatten(this, 0);
  return result;
};
```

---

### Пример использования:

```js
const nested = [1, [2, 3], [4, [5, 6]]];

console.log(nested.myFlat()); // [1, 2, 3, 4, [5, 6]] (depth = 1 по умолчанию)
console.log(nested.myFlat(1)); // [1, 2, 3, 4, [5, 6]]
console.log(nested.myFlat(2)); // [1, 2, 3, 4, 5, 6]
```

---

### Вариант с бесконечной глубиной:

```js
Array.prototype.myFlatInfinity = function() {
  const result = [];

  function flatten(arr) {
    for (let i = 0; i < arr.length; i++) {
      if (Array.isArray(arr[i])) {
        flatten(arr[i]);
      } else {
        result.push(arr[i]);
      }
    }
  }

  flatten(this);
  return result;
};

const deeplyNested = [1, [2, [3, [4, [5]]]]];
console.log(deeplyNested.myFlatInfinity()); // [1, 2, 3, 4, 5]
```

---

### Объяснение:

1. Создаём новый массив `result`
2. Используем рекурсивную функцию `flatten` для обхода вложенных массивов
3. Если элемент — массив и не достигли максимальной глубины, рекурсивно разглаживаем его
4. Иначе добавляем элемент в результат
5. Возвращаем разглаженный массив
