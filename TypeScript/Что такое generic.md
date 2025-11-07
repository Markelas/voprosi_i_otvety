## 🔀 Что такое generic

**Generic в TypeScript** — это способ создавать компоненты (функции, классы, интерфейсы, типы), которые работают с разными типами данных, сохраняя при этом типовую безопасность.

---

### 🎯 Зачем нужны:

- ✅ Чтобы писать **переиспользуемый** и **типобезопасный** код
- ✅ Чтобы не жёстко привязываться к конкретному типу (`string`, `number` и т.д.)
- ✅ Чтобы сохранить информацию о типе между входными и выходными данными

---

### 💡 Пример:

Вместо `any`, который убирает проверку типов, generic сохраняет и передаёт тип, обеспечивая безопасность и переиспользуемость кода.

```ts
// ❌ Плохо — теряем информацию о типе
function identity(value: any): any {
  return value;
}

// ✅ Хорошо — сохраняем тип
function identity<T>(value: T): T {
  return value;
}

const str = identity("hello"); // str: string
const num = identity(42); // num: number
```

---

### 🔧 Примеры:

#### Интерфейс с generic:

```ts
interface KeyValue<K, V> {
  key: K;
  value: V;
}

const kv1: KeyValue<string, number> = { key: "age", value: 30 };
const kv2: KeyValue<number, boolean> = { key: 1, value: true };
```

#### Функция с generic:

```ts
function pair<T, U>(first: T, second: U): [T, U] {
  return [first, second];
}

const a = pair<number, string>(1, "hello");
// a: [number, string]
```

---

### 🎯 Ограничения (constraints):

```ts
function getLength<T extends { length: number }>(item: T): number {
  return item.length;
}

getLength("hello"); // ✅
getLength([1, 2, 3]); // ✅
getLength(42); // ❌ Ошибка
```

---

### 🔧 Дополнительные манипуляции с generic:

#### 1. **Default параметры:**
Если generic не указан явно, используется значение по умолчанию.

```ts
function identity<T = string>(value: T): T {
  return value;
}

identity('hello'); // T = string (по умолчанию)
identity<number>(42); // T = number (явно указан)
```

#### 2. **Mapped types:**
Преобразует все свойства одного типа в другой тип, итерируясь по ключам.

```ts
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

// Преобразует { name: string, age: number } 
// в { readonly name: string, readonly age: number }
```

#### 3. **Conditional types:**
Условные типы — если `T` расширяет `null | undefined`, возвращаем `never`, иначе `T`.

```ts
type NonNullable<T> = T extends null | undefined ? never : T;

// NonNullable<string | null> → string (null убирается)
// NonNullable<number> → number
```

#### 4. **Infer:**
Извлекает тип из другого типа. Здесь извлекаем тип возвращаемого значения функции.

```ts
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

// ReturnType<() => string> → string
// ReturnType<(x: number) => boolean> → boolean
```

---

### 🎯 Метод для двух конкретных типов:

Задача: функция принимает только `number` или `string`, и возвращаемый тип зависит от входного типа.

#### Вариант 1: Перегрузка функций (проще и понятнее)
Объявляем несколько сигнатур функции — TypeScript выберет правильную по типу аргумента.

```ts
// Сигнатуры (объявления)
function process(input: number): number;
function process(input: string): string;
// Реализация
function process(input: number | string): number | string {
  if (typeof input === 'number') {
    return input * 2; // number
  }
  return input.toUpperCase(); // string
}

const num = process(5);    // number (TypeScript знает из сигнатуры)
const str = process('hi');  // string (TypeScript знает из сигнатуры)
```

**Как работает:** TypeScript сопоставляет тип аргумента с сигнатурами и выбирает подходящую.

#### Вариант 2: Conditional types (более гибко)
Используем условный тип для определения результата на основе входного типа.

```ts
// Если T extends number → возвращаем number
// Если T extends string → возвращаем string
// Иначе → never (ошибка)
type ProcessResult<T> = T extends number ? number : T extends string ? string : never;

function process<T extends number | string>(input: T): ProcessResult<T> {
  if (typeof input === 'number') {
    return (input * 2) as ProcessResult<T>;
  }
  return (input.toUpperCase()) as ProcessResult<T>;
}

const num = process(5);    // number
const str = process('hi');  // string
// process(true);           // ❌ Ошибка (T не number и не string)
```

**Как работает:** Conditional type проверяет, какой тип передан, и возвращает соответствующий тип результата.