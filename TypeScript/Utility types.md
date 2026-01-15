## 🛠️ Utility types

**Utility types** — это встроенные типы TypeScript, которые помогают создавать новые типы на основе существующих, упрощая работу с интерфейсами и объектами.

---

### 📊 Таблица Utility types:

| Utility type        | Что делает                                                               | Пример использования                                                        |
| ------------------- | ------------------------------------------------------------------------ | --------------------------------------------------------------------------- |
| **`Partial<T>`**    | Делает все свойства типа `T` необязательными                             | `Partial<User>` — все свойства `User` становятся опциональными              |
| **`Required<T>`**   | Делает все свойства типа `T` обязательными                               | Обратное `Partial`                                                          |
| **`Pick<T, K>`**    | Выбирает подмножество свойств `K` из типа `T`                            | `Pick<User, "id" \| "name">` — выбираем только `id` и `name`                |
| **`Omit<T, K>`**    | Исключает свойства `K` из типа `T`                                       | `Omit<User, "password">` — все свойства, кроме `password`                   |
| **`Readonly<T>`**   | Делает все свойства типа `T` только для чтения                           | Защищает объект от изменений                                                |
| **`Record<K, T>`**  | Создаёт объект с ключами типа `K` и значениями типа `T`                  | `Record<string, number>` — объект с ключами строками и числовыми значениями |
| **`Exclude<T, U>`** | Исключает из типа `T` все составляющие, которые можно присвоить типу `U` | `Exclude<"a" \| "b" \| "c", "a">` — результат: `"b" \| "c"`                 |
| **`Extract<T, U>`** | Извлекает из типа `T` только те типы, которые можно присвоить типу `U`    | `Extract<"a" \| "b" \| "c", "a" \| "d">` — результат: `"a"`                 |
| **`NonNullable<T>`** | Исключает `null` и `undefined` из типа `T`                                | `NonNullable<string \| null \| undefined>` — результат: `string`           |
| **`Parameters<T>`** | Извлекает типы параметров функции `T` в виде кортежа                      | `Parameters<(a: number, b: string) => void>` — результат: `[number, string]`|
| **`ReturnType<T>`** | Извлекает возвращаемый тип функции `T`                                    | `ReturnType<() => string>` — результат: `string`                          |

---

### 💡 Примеры:

```ts
type User = {
  id: number;
  name: string;
  email: string;
  password: string;
};

// Pick — выбираем только id и name
const userSummary: Pick<User, "id" | "name"> = {
  id: 1,
  name: "Alice"
};

// Omit — исключаем password
type PublicUser = Omit<User, "password">;

// Partial — все свойства опциональны
type PartialUser = Partial<User>;

// Readonly — нельзя изменять
const readonlyUser: Readonly<User> = { ... };
// readonlyUser.name = "Bob"; // ❌ Ошибка

// Record — создаём объект с определёнными типами ключей и значений
type UserRoles = Record<string, "admin" | "user" | "guest">;

// Extract — извлекаем только определённые типы
type T1 = "a" | "b" | "c";
type T2 = Extract<T1, "a" | "d">; // "a"

// NonNullable — исключаем null и undefined
type StringOnly = NonNullable<string | null | undefined>; // string

// Parameters — извлекаем параметры функции
type FuncParams = Parameters<(a: number, b: string) => void>; // [number, string]

// ReturnType — извлекаем возвращаемый тип
type FuncReturn = ReturnType<() => string>; // string
```

---

### 🔧 Как работают под капотом

Utility types используют **mapped types** и **conditional types** для преобразования типов:

```ts
// Partial<T> - добавляет ? ко всем свойствам
type Partial<T> = {
  [P in keyof T]?: T[P];
};

// Required<T> - убирает ? (удаляет опциональность)
type Required<T> = {
  [P in keyof T]-?: T[P];
};

// Readonly<T> - добавляет readonly
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

// Pick<T, K> - выбирает свойства
type Pick<T, K extends keyof T> = {
  [P in K]: T[P];
};

// Omit<T, K> - исключает свойства
type Omit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>;

// Record<K, T> - создает объект
type Record<K extends keyof any, T> = {
  [P in K]: T;
};

// Exclude<T, U> - исключает из union
type Exclude<T, U> = T extends U ? never : T;

// Extract<T, U> - извлекает из union
type Extract<T, U> = T extends U ? T : never;

// NonNullable<T> - исключает null и undefined
type NonNullable<T> = T extends null | undefined ? never : T;

// Parameters<T> - извлекает параметры функции
type Parameters<T extends (...args: any) => any> = 
  T extends (...args: infer P) => any ? P : never;

// ReturnType<T> - извлекает возвращаемый тип
type ReturnType<T extends (...args: any) => any> = 
  T extends (...args: any) => infer R ? R : any;
```

---

### 💡 Практические примеры

#### **Обновление объекта (Partial):**

```ts
interface User {
  id: number;
  name: string;
  email: string;
}

function updateUser(id: number, updates: Partial<User>): User {
  // Обновляем только переданные поля
  return { ...getUser(id), ...updates };
}

updateUser(1, { name: "Bob" }); // ✅ Можно обновить только name
```

#### **Публичный API (Omit):**

```ts
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
  internalId: string;
}

// Исключаем приватные поля
type PublicUser = Omit<User, "password" | "internalId">;

function getPublicUser(id: number): PublicUser {
  const user = getUser(id);
  const { password, internalId, ...publicUser } = user;
  return publicUser;
}
```

#### **Словарь конфигурации (Record):**

```ts
type Theme = "light" | "dark";
type Config = Record<Theme, { bg: string; text: string }>;

const themeConfig: Config = {
  light: { bg: "#fff", text: "#000" },
  dark: { bg: "#000", text: "#fff" }
};
```

#### **Комбинация utility types:**

```ts
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
  createdAt: Date;
}

// Создаем тип для обновления (Partial + Omit)
type UserUpdate = Partial<Omit<User, "id" | "createdAt">>;
// { name?: string; email?: string; password?: string; }

// Создаем тип для создания (Omit + Required)
type CreateUser = Required<Omit<User, "id" | "createdAt">>;
// { name: string; email: string; password: string; }
```

---

### 🎯 Когда использовать:

- **`Pick`** / **`Omit`** — когда нужно выбрать или исключить свойства
- **`Partial`** — для опциональных обновлений (например, `updateUser`)
- **`Readonly`** — для иммутабельных данных
- **`Record`** — для словарей и маппингов
- **`Exclude`** / **`Extract`** — для работы с union типами
- **`NonNullable`** — когда нужно гарантировать отсутствие null/undefined
- **`Parameters`** / **`ReturnType`** — для работы с типами функций

---

### 🎯 Краткий ответ для собеседования

**Utility types** — встроенные типы TypeScript для преобразования других типов. `Partial` делает свойства опциональными, `Pick`/`Omit` выбирают/исключают свойства, `Record` создает объект с заданными ключами и значениями. Используют mapped types (`[K in keyof T]`) и conditional types для преобразования.

---


## 📚 Полезные практики- **Комбинируйте utility types** для создания сложных типов
- **Используйте `Partial`** для функций обновления
- **Используйте `Omit`** для исключения приватных полей
- **Используйте `Record`** для словарей и конфигураций
- **Используйте `Pick`** когда нужно мало свойств
- **Используйте `Parameters`/`ReturnType`** для работы с типами функций
- **Создавайте собственные utility types** для повторяющихся паттернов
