## ⏱️ Throttle

### Что делает:

**Throttle** — ограничивает частоту вызовов функции, выполняя её не чаще, чем раз в определённый промежуток времени.

---

### Реализация:

```js
function throttle(callback, delay) {
  let shouldWait = false;

  return function (...args) {
    if (shouldWait) return;

    callback.apply(this, args);
    shouldWait = true;

    setTimeout(() => {
      shouldWait = false;
    }, delay);
  };
}
```

---

### Пример использования:

```js
const throttledScroll = throttle(() => {
  console.log('Scroll event');
}, 100);

window.addEventListener('scroll', throttledScroll);
```

---

### Разница с Debounce:

- **Debounce** — ждёт окончания активности (например, окончания ввода)
- **Throttle** — выполняет функцию с фиксированной частотой (например, каждые 100мс)

---

### Объяснение:

1. Флаг `shouldWait` блокирует вызовы
2. Если функция вызвана — выполняем и устанавливаем блокировку
3. Через `delay` снимаем блокировку
4. Пока блокировка активна — игнорируем вызовы
