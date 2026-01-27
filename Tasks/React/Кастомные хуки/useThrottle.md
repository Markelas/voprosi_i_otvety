## ⏱️ useThrottle

### Что делает:

**useThrottle** — хук для ограничения частоты обновления значения. Значение обновляется не чаще, чем раз в указанный интервал времени. Полезен для обработки скролла, изменения размера окна и других частых событий.

---

### Реализация:

```js
import { useState, useEffect, useRef } from 'react';

function useThrottle(value, delay) {
  const [throttledValue, setThrottledValue] = useState(value);
  const lastRan = useRef(Date.now());

  useEffect(() => {
    const handler = setTimeout(() => {
      if (Date.now() - lastRan.current >= delay) {
        setThrottledValue(value);
        lastRan.current = Date.now();
      }
    }, delay - (Date.now() - lastRan.current));

    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);

  return throttledValue;
}
```

---

### Пример использования:

```jsx
function ScrollComponent() {
  const [scrollY, setScrollY] = useState(0);
  const throttledScrollY = useThrottle(scrollY, 200);

  useEffect(() => {
    const handleScroll = () => {
      setScrollY(window.scrollY);
    };

    window.addEventListener('scroll', handleScroll);
    return () => window.removeEventListener('scroll', handleScroll);
  }, []);

  useEffect(() => {
    // Выполняется не чаще, чем раз в 200мс
    console.log('Throttled scroll position:', throttledScrollY);
  }, [throttledScrollY]);

  return (
    <div>
      <p>Scroll position: {throttledScrollY}px</p>
    </div>
  );
}
```

---

### Объяснение:

1. **Хранение троттленного значения:**
   - `throttledValue` — это значение, которое обновляется с ограничением частоты
   - Изначально равно переданному `value`

2. **Отслеживание времени последнего обновления:**
   - `lastRan` — ref, хранящий время последнего обновления
   - Используется для проверки, прошло ли достаточно времени

3. **Таймер для ограничения частоты:**
   - При изменении `value` проверяется, прошло ли достаточно времени с последнего обновления
   - Если прошло — обновляем значение сразу
   - Если нет — устанавливаем таймер на оставшееся время

4. **Результат:**
   - `throttledValue` обновляется не чаще, чем раз в `delay` миллисекунд

---

### Важные моменты:

- **Оптимизация производительности:** уменьшает количество обновлений при частых изменениях
- **Регулярность:** гарантирует, что значение обновится хотя бы раз в указанный интервал
- **Отличие от debounce:** throttle обновляет значение регулярно, debounce — только после паузы
- **Гибкость:** можно использовать для любого значения (строки, числа, объекты)

---

### Альтернативный вариант (для функций):

Если нужно троттлить функцию, а не значение:

```js
import { useRef, useCallback } from 'react';

function useThrottledCallback(callback, delay) {
  const lastRan = useRef(Date.now());
  const timerRef = useRef();

  const throttledCallback = useCallback((...args) => {
    if (Date.now() - lastRan.current >= delay) {
      callback(...args);
      lastRan.current = Date.now();
    } else {
      if (timerRef.current) {
        clearTimeout(timerRef.current);
      }
      timerRef.current = setTimeout(() => {
        callback(...args);
        lastRan.current = Date.now();
      }, delay - (Date.now() - lastRan.current));
    }
  }, [callback, delay]);

  return throttledCallback;
}
```
