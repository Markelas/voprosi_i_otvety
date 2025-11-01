## 🔍 Lower и Upper Bound

### Задача

Найти **наименьший элемент ≥ target** (lower bound) и **наибольший элемент ≤ target** (upper bound).

---

### Решение для Lower Bound:

```js
function lowerBound(arr, target) {
  let left = 0, right = arr.length - 1, result = -1;

  while (left <= right) {
    let mid = Math.floor((left + right) / 2);

    if (arr[mid] >= target) {
      result = mid;
      right = mid - 1;
    } else {
      left = mid + 1;
    }
  }

  return result;
}
```

---

### Решение для Upper Bound:

```js
function upperBound(arr, target) {
  let left = 0, right = arr.length - 1, result = -1;

  while (left <= right) {
    let mid = Math.floor((left + right) / 2);

    if (arr[mid] <= target) {
      result = mid;
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }

  return result;
}
```

---

### Объяснение:

- **Lower Bound:** ищем наименьший элемент, который ≥ target (двигаемся влево при совпадении)
- **Upper Bound:** ищем наибольший элемент, который ≤ target (двигаемся вправо при совпадении)
