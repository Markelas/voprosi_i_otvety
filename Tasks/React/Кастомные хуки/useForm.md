## 📝 useForm

### Что делает:

**useForm** — хук для управления полями формы. Хранит значения, даёт функцию изменения и обработчик отправки. Минимальная обёртка над состоянием для форм.

---

### Реализация:

```js
import { useState } from 'react';

function useForm(initialValues = {}, onSubmit) {
  const [values, setValues] = useState(initialValues);

  const handleChange = (e) => {
    const { name, value } = e.target;
    setValues((prev) => ({ ...prev, [name]: value }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    onSubmit?.(values);
  };

  const reset = () => setValues(initialValues);

  return { values, handleChange, handleSubmit, reset };
}
```

---

### Пример использования:

```jsx
function LoginForm() {
  const { values, handleChange, handleSubmit } = useForm(
    { email: '', password: '' },
    (data) => console.log('Отправка:', data)
  );

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="email"
        value={values.email}
        onChange={handleChange}
        placeholder="Email"
      />
      <input
        name="password"
        type="password"
        value={values.password}
        onChange={handleChange}
        placeholder="Пароль"
      />
      <button type="submit">Войти</button>
    </form>
  );
}
```

---

### Объяснение:

1. **values:** объект со всеми полями формы; у инпутов должен быть атрибут `name`.
2. **handleChange:** по `name` обновляет соответствующее поле в `values`.
3. **handleSubmit:** вызывает `preventDefault` и передаёт текущие `values` в callback.
4. **reset:** возвращает форму к `initialValues`.

---

### Важные моменты:

- У каждого инпута обязательно указывать `name`.
- Подходит для простых форм; для сложной валидации и полей можно расширить (например, добавить объект ошибок).
