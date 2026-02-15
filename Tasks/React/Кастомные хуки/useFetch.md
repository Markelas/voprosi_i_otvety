## 🌐 useFetch

### Что делает:

**useFetch** — хук для загрузки данных по URL. Возвращает данные, состояние загрузки и ошибку. Подходит для простых GET-запросов.

---

### Реализация:

```js
import { useState, useEffect } from 'react';

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    if (!url) return;

    setLoading(true);
    setError(null);

    fetch(url)
      .then((res) => {
        if (!res.ok) throw new Error(res.statusText);
        return res.json();
      })
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [url]);

  return { data, loading, error };
}
```

---

### Пример использования:

```jsx
function UserList() {
  const { data, loading, error } = useFetch('https://api.example.com/users');

  if (loading) return <p>Загрузка...</p>;
  if (error) return <p>Ошибка: {error.message}</p>;
  if (!data) return null;

  return (
    <ul>
      {data.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

---

### Объяснение:

1. **Состояния:** `data` — результат, `loading` — идёт запрос, `error` — ошибка.
2. **Зависимость от url:** при смене `url` эффект выполняется заново, запрос перезапускается.
3. **Сброс:** перед новым запросом очищаем ошибку и ставим `loading: true`.
4. **Очистка:** при размонтировании во время запроса `setState` вызовется на размонтированном компоненте — в продакшене можно добавить флаг отмены (AbortController).

---

### Важные моменты:

- **Простота:** достаточно для демо и собеседования.
- **Расширения:** можно добавить `refetch`, опции `fetch`, AbortController для отмены при размонтировании.
