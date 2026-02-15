## 📄 usePaginatedQuery

### Что делает:

**usePaginatedQuery** — хук для постраничной загрузки данных. Принимает URL с поддержкой параметра страницы (например `page`), возвращает данные текущей страницы, номер страницы, функции «далее»/«назад» и состояние загрузки.

---

### Реализация:

```js
import { useState, useEffect } from 'react';

function usePaginatedQuery(baseUrl, initialPage = 1) {
  const [page, setPage] = useState(initialPage);
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const url = baseUrl.includes('?')
      ? `${baseUrl}&page=${page}`
      : `${baseUrl}?page=${page}`;

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
  }, [baseUrl, page]);

  const nextPage = () => setPage((p) => p + 1);
  const prevPage = () => setPage((p) => Math.max(1, p - 1));

  return { data, loading, error, page, nextPage, prevPage };
}
```

---

### Пример использования:

```jsx
function PostList() {
  const { data, loading, error, page, nextPage, prevPage } = usePaginatedQuery(
    'https://api.example.com/posts'
  );

  if (loading) return <p>Загрузка...</p>;
  if (error) return <p>Ошибка: {error.message}</p>;

  return (
    <div>
      <ul>
        {data?.items?.map((post) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
      <p>Страница {page}</p>
      <button onClick={prevPage} disabled={page <= 1}>
        Назад
      </button>
      <button onClick={nextPage}>Вперёд</button>
    </div>
  );
}
```

---

### Объяснение:

1. **page:** текущий номер страницы; при изменении эффект перезапрашивает данные.
2. **URL:** к `baseUrl` добавляется `?page=N` (или `&page=N`, если в URL уже есть `?`).
3. **nextPage / prevPage:** увеличивают или уменьшают страницу; ниже 1 не опускаем.
4. Структура ответа (`data.items` и т.д.) зависит от API — в примере условная.

---

### Важные моменты:

- Подразумевается, что API принимает параметр `page`; при другом формате (offset, cursor) логику можно изменить.
- Можно расширить: передавать `pageSize`, проверять `hasNext` из ответа и отключать кнопку «Вперёд».
