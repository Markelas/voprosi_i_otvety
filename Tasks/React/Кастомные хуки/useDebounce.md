## ⏱️ useDebounce

### Что делает:

**useDebounce** — хук для откладывания обновления значения до тех пор, пока не пройдёт определённое время без новых изменений. Полезен для поиска, валидации форм и оптимизации запросов.

---

### Реализация:

```js
import { useState, useEffect } from 'react';

function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    // Устанавливаем таймер для обновления значения
    const timer = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    // Очищаем таймер при изменении value или размонтировании
    return () => {
      clearTimeout(timer);
    };
  }, [value, delay]);

  return debouncedValue;
}
```

---

### Пример использования:

```jsx
function SearchComponent() {
  const [query, setQuery] = useState('');
  const debouncedQuery = useDebounce(query, 500);

  useEffect(() => {
    if (debouncedQuery) {
      // Выполняем поиск только после задержки
      fetch(`/api/search?q=${debouncedQuery}`)
        .then(res => res.json())
        .then(data => console.log(data));
    }
  }, [debouncedQuery]);

  return (
    <input
      value={query}
      onChange={(e) => setQuery(e.target.value)}
      placeholder="Поиск..."
    />
  );
}
```

---

### Объяснение:

1. **Хранение задержанного значения:**
   - `debouncedValue` — это значение, которое обновляется с задержкой
   - Изначально равно переданному `value`

2. **Таймер для задержки:**
   - При изменении `value` устанавливается таймер на `delay` миллисекунд
   - Если `value` изменится снова до истечения таймера, предыдущий таймер очищается

3. **Очистка таймера:**
   - `useEffect` возвращает функцию очистки, которая вызывается:
     - При изменении `value` (перед установкой нового таймера)
     - При размонтировании компонента

4. **Результат:**
   - `debouncedValue` обновляется только через `delay` мс после последнего изменения `value`

---

### Важные моменты:

- **Оптимизация запросов:** уменьшает количество запросов к API при вводе текста
- **Производительность:** предотвращает лишние вычисления при частых изменениях
- **Гибкость:** можно использовать для любого значения (строки, числа, объекты)

---

### Альтернативный вариант (для функций):

Если нужно дебаунсить функцию, а не значение:

```js
import { useRef, useCallback } from 'react';

function useDebouncedCallback(callback, delay) {
  const timerRef = useRef();

  const debouncedCallback = useCallback((...args) => {
    if (timerRef.current) {
      clearTimeout(timerRef.current);
    }
    timerRef.current = setTimeout(() => {
      callback(...args);
    }, delay);
  }, [callback, delay]);

  return debouncedCallback;
}
```

