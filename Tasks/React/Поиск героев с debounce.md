## 🔍 Поиск героев с debounce

### Задача

Создать приложение для поиска по списку героев:
- Поле ввода для поискового запроса
- Запрос по мере ввода
- После получения отобразить имена
- Индикация загрузки
- Обработка ошибки от API

---

### Решение:

```jsx
import React, { useEffect, useState } from "react";

// --- debounce ---
function debounce(fn, delay) {
  let timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
}

// --- API-запрос ---
async function fetchCharacters(name) {
  const url = `https://rickandmortyapi.com/api/character?name=${encodeURIComponent(name)}`;
  const res = await fetch(url);

  if (res.ok) {
    const data = await res.json();
    return { results: data.results, notFound: data.results.length === 0 };
  }

  if (res.status === 404) {
    return { results: [], notFound: true };
  }

  let message = `Ошибка ${res.status}`;
  try {
    const err = await res.json();
    if (err.error) message = err.error;
  } catch {}

  throw new Error(message);
}

export default function App() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [notFound, setNotFound] = useState(false);

  // оборачиваем API вызов через debounce
  const debouncedSearch = debounce(async (value) => {
    setLoading(true);
    setError(null);
    setNotFound(false);

    try {
      const { results, notFound } = await fetchCharacters(value);
      setResults(results);
      setNotFound(notFound);
    } catch (e) {
      setError(e.message || "Unknown error");
      setResults([]);
      setNotFound(false);
    } finally {
      setLoading(false);
    }
  }, 400);

  // отслеживаем query
  useEffect(() => {
    if (query.trim()) {
      debouncedSearch(query);
    } else {
      setResults([]);
      setLoading(false);
      setError(null);
      setNotFound(false);
    }
  }, [query]);

  return (
    <div>
      <label htmlFor="search">Персонаж</label>
      <input
        id="search"
        type="text"
        placeholder="Начните вводить имя (например, rick)…"
        value={query}
        onChange={(e) => setQuery(e.target.value)}
      />
      <div>
        {loading && <div>Загрузка…</div>}
        {error && <div>Ошибка: {error}</div>}
        {notFound && <div>Ничего не найдено</div>}
        {results.length > 0 && (
          <ul>
            {results.map((hero) => (
              <li key={hero.id}>{hero.name}</li>
            ))}
          </ul>
        )}
      </div>
    </div>
  );
}
```

---

### Особенности:

- Использует `debounce` для уменьшения количества запросов
- Обрабатывает состояния загрузки, ошибок и "не найдено"
- Очищает результаты при пустом запросе
