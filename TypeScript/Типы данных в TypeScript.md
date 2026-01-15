## 📋 Типы данных в TypeScript

**TypeScript** — это статически типизированный язык, который расширяет JavaScript, добавляя строгую типизацию.

---

## 📊 Все типы TypeScript

| Категория | Типы | Описание |
|-----------|------|----------|
| **Примитивные** | `number`, `string`, `boolean`, `null`, `undefined`, `symbol`, `bigint` | Базовые типы данных |
| **Сложные** | `object`, `Array<T>`, `Tuple` | Объекты, массивы, кортежи |
| **Специальные** | `any`, `unknown`, `void`, `never` | Специальные случаи |
| **Объединение** | `Union` (`\|`), `Intersection` (`&`) | Комбинации типов |
| **Литеральные** | `"literal"`, `123`, `true` | Точные значения |
| **Перечисления** | `enum` | Набор констант |
| **Функции** | `(param: type) => returnType` | Типизация функций |
| **Объекты** | `interface`, `type` | Структуры данных |
| **Generic** | `<T>`, `<T extends U>` | Переиспользуемые типы |
| **Утилитарные** | `Partial`, `Required`, `Readonly`, `Pick`, `Omit`, `Record` | Встроенные утилиты |

---

## 🔢 Примитивные типы

### **`number`** — числа

```ts
const age: number = 25;
const pi: number = 3.14;
```

### **`string`** — строки

```ts
const name: string = "John";
const fullName: string = `${name} Doe`;
```

### **`boolean`** — логический тип

```ts
const isLoggedIn: boolean = true;
```

### **`null` и `undefined`**

```ts
let value: string | null | undefined;
```

**Важно:** В `strictNullChecks` `null` и `undefined` не могут быть присвоены другим типам без явного указания.

### **`symbol`** — уникальные идентификаторы

```ts
const key: symbol = Symbol("key");
```

### **`bigint`** — большие целые числа

```ts
const big: bigint = 9007199254740991n;
```

---

## 📦 Типы сложных данных

### **`object`** — объекты

```ts
// Лучше использовать interface или type
interface User {
  name: string;
  age: number;
}

const user: User = { name: "Alice", age: 30 };
```

### **`Array<T>`** — массивы

```ts
const numbers: number[] = [1, 2, 3];
const strings: Array<string> = ["a", "b"];
```

### **`Tuple`** — кортежи

```ts
const tuple: [string, number] = ["Alice", 30];
const [name, age] = tuple;
```

---

## 🎯 Специальные типы

### **`any`** — отключает проверку типов

```ts
let data: any = 42;
data = "Hello"; // Допустимо, но небезопасно
```

**⚠️ Избегайте `any`** — теряются преимущества TypeScript.

### **`unknown`** — безопасная альтернатива `any`

```ts
let value: unknown = getData();
// value.foo(); // ❌ Ошибка

if (typeof value === "string") {
  value.toUpperCase(); // ✅ OK после проверки
}
```

### **`void`** — отсутствие возвращаемого значения

```ts
function log(message: string): void {
  console.log(message);
}
```

### **`never`** — функция никогда не возвращается

```ts
function throwError(): never {
  throw new Error("Error");
}
```

---

## 📝 Литеральные типы

```ts
type Direction = "up" | "down" | "left" | "right";
type DiceRoll = 1 | 2 | 3 | 4 | 5 | 6;
type StatusCode = 200 | 404 | 500;
```

---

## 🔢 Enum

```ts
enum Status {
  Pending,    // 0
  Success,    // 1
  Error       // 2
}

enum Direction {
  Up = "UP",
  Down = "DOWN"
}
```

---

## 🎭 Типизация функций

```ts
// Обычная функция
function add(a: number, b: number): number {
  return a + b;
}

// Стрелочная функция
const multiply = (a: number, b: number): number => a * b;

// Опциональные параметры
function greet(name: string, age?: number): string {
  return age ? `Hello, ${name}, ${age}` : `Hello, ${name}`;
}

// Rest параметры
function sum(...numbers: number[]): number {
  return numbers.reduce((acc, n) => acc + n, 0);
}

// Тип функции
type MathOp = (a: number, b: number) => number;
const divide: MathOp = (a, b) => a / b;
```

---

## 🎯 Интерфейсы и типы

### **`interface`**

```ts
interface User {
  name: string;
  age: number;
  email?: string;        // Опциональное
  readonly id: number;   // Только для чтения
}
```

### **`type`**

```ts
type Point = {
  x: number;
  y: number;
};

type ID = string | number;
```

### **Индексные сигнатуры**

```ts
interface Dictionary {
  [key: string]: string;
}
```

---

## 🎯 Generic типы

```ts
// Простой generic
function identity<T>(arg: T): T {
  return arg;
}

// С ограничениями
function logLength<T extends { length: number }>(arg: T): T {
  console.log(arg.length);
  return arg;
}

// Generic интерфейс
interface Box<T> {
  value: T;
}

const box: Box<number> = { value: 42 };
```

---

## 🎯 Утилитарные типы

```ts
interface User {
  name: string;
  age: number;
}

type PartialUser = Partial<User>;        // { name?: string; age?: number; }
type RequiredUser = Required<PartialUser>; // { name: string; age: number; }
type ReadonlyUser = Readonly<User>;      // Все readonly
type UserName = Pick<User, "name">;      // { name: string; }
type UserWithoutAge = Omit<User, "age">;  // { name: string; }
type UserMap = Record<string, User>;     // { [key: string]: User }
```

---

## 🎯 Вывод типов

TypeScript автоматически выводит типы:

```ts
const age = 25;        // number
const name = "Alice";  // string
const mixed = ["hello", 42]; // (string | number)[]
```

---

## 🎯 Краткие ответы для собеседования

**Примитивные типы:** `number`, `string`, `boolean`, `null`, `undefined`, `symbol`, `bigint`

**`any` vs `unknown`:** `any` отключает проверку типов, `unknown` требует проверки перед использованием

**Union vs Intersection:** `Union (|)` — один из типов, `Intersection (&)` — все типы вместе

**`void` vs `never`:** `void` — функция ничего не возвращает, `never` — функция никогда не завершается

**Generic:** Переиспользуемые типы с параметрами `<T>`

**Утилитарные типы:** `Partial`, `Required`, `Readonly`, `Pick`, `Omit`, `Record` — для работы с типами
