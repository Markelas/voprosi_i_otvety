## ⏱️ Debounce

### Что делает:

**Debounce** — откладывает выполнение функции до тех пор, пока не пройдёт определённое время без новых вызовов.

---

### Реализация:

```js
function debounce(fn, delay) {
  let timer;

  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
}
```

---

### Пример использования:

```js
const debouncedSearch = debounce((query) => {
  console.log('Поиск:', query);
}, 300);

// При вводе в поле поиска функция вызовется только через 300мс после последнего ввода
input.addEventListener('input', (e) => {
  debouncedSearch(e.target.value);
});
```

---

### Объяснение:

1. При каждом вызове очищаем предыдущий таймер
2. Устанавливаем новый таймер
3. Функция выполнится только если прошло `delay` времени без новых вызовов
