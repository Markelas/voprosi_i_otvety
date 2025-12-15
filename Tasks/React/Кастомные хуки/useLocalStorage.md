## 💾 useLocalStorage

### Что делает:

**useLocalStorage** — хук для синхронизации состояния компонента с `localStorage`. Значение сохраняется в браузере и восстанавливается при перезагрузке страницы.

---

### Реализация:

```js
import { useState, useEffect } from 'react';

function useLocalStorage(key, defData) {
	const [data, setData] = useState(() => {
		//При первой загрузке
		const localData = localStorage.getItem(key);
		return localData || defData;
});

useEffect(() => {
	localStorage.setItem(key, data);
}, [data]);

return [data, setData];
}
```

---
### Усложненная реализация:
  

```js

import { useState, useEffect } from 'react';

function useLocalStorage(key, initialValue) {
	// Инициализация состояния из localStorage или initialValue
		const [storedValue, setStoredValue] = useState(() => {
		try {
			const item = window.localStorage.getItem(key);
			return item ? JSON.parse(item) : initialValue;
		} catch (error) {
			return initialValue;
		}
	});
	
	  
	
	// Функция для обновления значения
	
	const setValue = (value) => {
		try {
			// Поддержка функции как значения (как в useState)
			const valueToStore = value instanceof Function ? value(storedValue) : value;
			setStoredValue(valueToStore);
			window.localStorage.setItem(key, JSON.stringify(valueToStore));
		} catch (error) {
			console.error(error);
		}
	};
	return [storedValue, setValue];
}

```
---

### Пример использования:

```jsx
function App() {
  const [name, setName] = useLocalStorage('name', '');

  return (
    <div>
      <input
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Введите имя"
      />
      <p>Привет, {name}!</p>
    </div>
  );
}
```

---

### Объяснение:

1. **Инициализация из localStorage:**
   - При первом рендере пытаемся прочитать значение из `localStorage`
   - Если значение есть — парсим JSON и используем его
   - Если нет — используем `initialValue`

2. **Ленивая инициализация:**
   - Используем функцию в `useState(() => ...)` чтобы чтение из `localStorage` происходило только один раз

3. **Обновление значения:**
   - `setValue` обновляет состояние и сохраняет в `localStorage`
   - Поддерживает функцию как значение (как в `useState`): `setValue(prev => prev + 1)`

4. **Обработка ошибок:**
   - `try/catch` защищает от ошибок парсинга или переполнения `localStorage`

---

### Важные моменты:

- **Только JSON-совместимые значения:** объекты и массивы сериализуются автоматически
- **Синхронизация:** значение сохраняется при каждом изменении
- **Восстановление:** при перезагрузке страницы значение восстанавливается из `localStorage`

