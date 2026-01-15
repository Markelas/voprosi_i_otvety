## 🔧 Условные типы и Mapped Types

---

### **Задача 1: Условный тип IsString**

**Условие:**
Создайте условный тип `IsString<T>`, который проверяет, является ли `T` строкой.

**Начальный код:**
```ts
type IsString<T> = // TODO

type A = IsString<string>;  // Должно быть: true
type B = IsString<number>;  // Должно быть: false
```

**Решение:**
```ts
type IsString<T> = T extends string ? true : false;

type A = IsString<string>;  // true ✅
type B = IsString<number>;  // false ✅
type C = IsString<"literal">; // true ✅
```

**Объяснение:**
- `T extends string` — проверяет, является ли `T` подтипом `string`
- Если да → `true`, иначе → `false`

---

### **Задача 2: Mapped Type для Readonly**

**Условие:**
Создайте тип `ReadonlyPerson`, в котором все поля `Person` будут `readonly`, используя mapped types.

**Начальный код:**
```ts
interface Person {
  name: string;
  age: number;
}

// TODO: Создать ReadonlyPerson
```

**Решение:**
```ts
interface Person {
  name: string;
  age: number;
}

type ReadonlyPerson = {
  readonly [K in keyof Person]: Person[K];
};
// { readonly name: string; readonly age: number }

// Универсальная версия
type MyReadonly<T> = {
  readonly [K in keyof T]: T[K];
};

type ReadonlyPerson2 = MyReadonly<Person>;
```

**Объяснение:**
- `[K in keyof Person]` — итерируемся по всем ключам `Person`
- `readonly` делает каждое свойство только для чтения

---

### **Задача 3: Условный тип для извлечения типа из массива**

**Условие:**
Создайте условный тип, который извлекает тип элемента из массива.

**Начальный код:**
```ts
// TODO: Реализовать ElementType
type ArrayElement = ElementType<number[]>; // Должно быть: number
type StringElement = ElementType<string[]>; // Должно быть: string
```

**Решение:**
```ts
type ElementType<T> = T extends (infer U)[] ? U : never;

type ArrayElement = ElementType<number[]>; // number ✅
type StringElement = ElementType<string[]>; // string ✅
type NotArray = ElementType<string>; // never ✅
```

**Объяснение:**
- `T extends (infer U)[]` — проверяем, является ли `T` массивом
- `infer U` — извлекаем тип элемента массива
- Если не массив → `never`

---

### **Задача 4: Условный тип для извлечения возвращаемого типа**

**Условие:**
Создайте условный тип `MyReturnType`, который извлекает возвращаемый тип функции.

**Начальный код:**
```ts
// TODO: Реализовать MyReturnType
function getValue(): string {
  return "Hello";
}

type ValueType = MyReturnType<typeof getValue>; // Должно быть: string
```

**Решение:**
```ts
type MyReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

function getValue(): string {
  return "Hello";
}

type ValueType = MyReturnType<typeof getValue>; // string ✅

function add(a: number, b: number): number {
  return a + b;
}

type AddReturn = MyReturnType<typeof add>; // number ✅
```

**Объяснение:**
- `T extends (...args: any[]) => infer R` — проверяем, является ли `T` функцией
- `infer R` — извлекаем тип возвращаемого значения
- Если не функция → `never`

---

### **Задача 5: Условный тип для исключения null и undefined**

**Условие:**
Создайте условный тип `MyNonNullable`, который исключает `null` и `undefined` из типа.

**Начальный код:**
```ts
// TODO: Реализовать MyNonNullable
type T1 = string | null | undefined;
type T2 = MyNonNullable<T1>; // Должно быть: string
```

**Решение:**
```ts
type MyNonNullable<T> = T extends null | undefined ? never : T;

type T1 = string | null | undefined;
type T2 = MyNonNullable<T1>; // string ✅

type T3 = number | null;
type T4 = MyNonNullable<T3>; // number ✅
```

**Объяснение:**
- `T extends null | undefined ? never : T` — если `T` это `null` или `undefined`, возвращаем `never`, иначе `T`
- В union типах `never` автоматически исключается

---

### **Задача 6: Mapped Type для преобразования типов**

**Условие:**
Создайте mapped type, который преобразует все свойства объекта в строки.

**Начальный код:**
```ts
interface User {
  id: number;
  name: string;
  age: number;
}

// TODO: Создать Stringify
type StringUser = Stringify<User>;
// Должно быть: { id: string; name: string; age: string }
```

**Решение:**
```ts
interface User {
  id: number;
  name: string;
  age: number;
}

type Stringify<T> = {
  [K in keyof T]: string;
};

type StringUser = Stringify<User>;
// { id: string; name: string; age: string } ✅
```

**Объяснение:**
- `[K in keyof T]` — итерируемся по всем ключам
- `: string` — все значения становятся строками


