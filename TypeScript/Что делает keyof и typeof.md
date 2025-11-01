## 🔑 Что делает `keyof` и `typeof`

---

### 🔹 `keyof`

Получает все ключи типа как union.

```ts
type User = { name: string; age: number; };

type UserKeys = keyof User;  // Результат: "name" | "age"

const key1: UserKeys = "name"; // ✅
const key2: UserKeys = "age";  // ✅
const key3: UserKeys = "email"; // ❌ Ошибка
```

**Применение:**

```ts
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user = { name: "Alice", age: 25 };
const name = getProperty(user, "name"); // ✅
```

---

### 🔹 `typeof`

Получает тип переменной. Извлекает тип значения переменной, полезен для создания типов на основе существующих данных.

```ts
const user = { name: "Alice", age: 25 };

type UserType = typeof user;
/* Результат: type UserType = { name: string; age: number; } */
```

**Применение:**

```ts
const colors = {
  red: '#ff0000',
  blue: '#0000ff',
  green: '#00ff00'
} as const;

type Color = typeof colors; // { red: '#ff0000', blue: '#0000ff', green: '#00ff00' }
type ColorKey = keyof typeof colors; // "red" | "blue" | "green"
```

---

### 💡 Комбинация:

```ts
const config = { apiUrl: "https://api.com", timeout: 5000 };

type Config = typeof config; // { apiUrl: string; timeout: number }
type ConfigKeys = keyof typeof config; // "apiUrl" | "timeout"
```
