## 🛡️ Type Guards

---

### **Задача 1: Type Guard для определения типа фигуры**

**Условие:**
Напишите и типизируйте функцию `isCircle` для определения типа фигуры, используя type predicate (`is`).

**Начальный код:**
```ts
type Rectangle = {
  width: number;
  height: number;
};

type Circle = {
  radius: number;
};

type AvailableFigure = Rectangle | Circle;

function isCircle(figure) {
  // TODO
}

function getCircleArea(figure: Circle): number {
  return Math.pow(figure.radius, 2) * Math.PI;
}

function getRectangleArea(figure: Rectangle): number {
  return figure.width * figure.height;
}

function getArea(figure: AvailableFigure): number {
  return isCircle(figure)
    ? getCircleArea(figure)
    : getRectangleArea(figure);
}
```

**Решение:**
```ts
type Rectangle = {
  width: number;
  height: number;
};

type Circle = {
  radius: number;
};

type AvailableFigure = Rectangle | Circle;

function isCircle(figure: AvailableFigure): figure is Circle {
  return 'radius' in figure;
}

function getCircleArea(figure: Circle): number {
  return Math.pow(figure.radius, 2) * Math.PI;
}

function getRectangleArea(figure: Rectangle): number {
  return figure.width * figure.height;
}

function getArea(figure: AvailableFigure): number {
  return isCircle(figure)
    ? getCircleArea(figure)      // ✅ TypeScript знает, что это Circle
    : getRectangleArea(figure);  // ✅ TypeScript знает, что это Rectangle
}
```

**Альтернативное решение:**
```ts
function isCircle(figure: AvailableFigure): figure is Circle {
  return figure.hasOwnProperty('radius');
}

// Или через проверку отсутствия свойства
function isRectangle(figure: AvailableFigure): figure is Rectangle {
  return 'width' in figure && 'height' in figure;
}
```

**Объяснение:**
- `figure is Circle` — type predicate, говорит TypeScript, что если функция вернула `true`, то `figure` это `Circle`
- `'radius' in figure` — проверяем наличие свойства `radius`
- После проверки TypeScript автоматически сужает тип

---

### **Задача 2: Type Guard для проверки строки**

**Условие:**
Создайте type guard для проверки, является ли значение строкой.

**Начальный код:**
```ts
function processValue(value: unknown) {
  // TODO: Проверить, что value - строка
  return value.toUpperCase(); // Ошибка: value имеет тип unknown
}
```

**Решение:**
```ts
function isString(value: unknown): value is string {
  return typeof value === 'string';
}

function processValue(value: unknown) {
  if (isString(value)) {
    return value.toUpperCase(); // ✅ TypeScript знает, что value - string
  }
  throw new Error('Value is not a string');
}
```

---

### **Задача 3: Type Guard для проверки объекта с определенными свойствами**

**Условие:**
Создайте type guard для проверки, является ли значение объектом с определенными свойствами.

**Начальный код:**
```ts
interface User {
  id: number;
  name: string;
  email: string;
}

function processData(data: unknown) {
  // TODO: Проверить, что data - User
  console.log(data.name); // Ошибка
}
```

**Решение:**
```ts
interface User {
  id: number;
  name: string;
  email: string;
}

function isUser(data: unknown): data is User {
  return (
    typeof data === 'object' &&
    data !== null &&
    'id' in data &&
    'name' in data &&
    'email' in data &&
    typeof (data as any).id === 'number' &&
    typeof (data as any).name === 'string' &&
    typeof (data as any).email === 'string'
  );
}

function processData(data: unknown) {
  if (isUser(data)) {
    console.log(data.name); // ✅ TypeScript знает, что data - User
    console.log(data.email); // ✅
  }
}
```

**Более строгая версия:**
```ts
function isUser(data: unknown): data is User {
  if (typeof data !== 'object' || data === null) {
    return false;
  }
  
  const obj = data as Record<string, unknown>;
  
  return (
    typeof obj.id === 'number' &&
    typeof obj.name === 'string' &&
    typeof obj.email === 'string'
  );
}
```

---

### **Задача 4: Type Guard для проверки массива определенного типа**

**Условие:**
Создайте type guard для проверки, является ли значение массивом строк.

**Начальный код:**
```ts
function processArray(data: unknown) {
  // TODO: Проверить, что data - string[]
  data.forEach(item => console.log(item.toUpperCase())); // Ошибка
}
```

**Решение:**
```ts
function isStringArray(data: unknown): data is string[] {
  return (
    Array.isArray(data) &&
    data.every(item => typeof item === 'string')
  );
}

function processArray(data: unknown) {
  if (isStringArray(data)) {
    data.forEach(item => console.log(item.toUpperCase())); // ✅
  }
}
```

---

### **Задача 5: Комбинированный Type Guard**

**Условие:**
Создайте type guard, который проверяет, является ли значение либо строкой, либо числом.

**Начальный код:**
```ts
function processValue(value: unknown) {
  // TODO: Проверить, что value - string | number
  if (typeof value === 'string') {
    return value.length;
  }
  return value.toFixed(2); // Ошибка
}
```

**Решение:**
```ts
function isStringOrNumber(value: unknown): value is string | number {
  return typeof value === 'string' || typeof value === 'number';
}

function processValue(value: unknown) {
  if (isStringOrNumber(value)) {
    if (typeof value === 'string') {
      return value.length; // ✅
    }
    return value.toFixed(2); // ✅
  }
  throw new Error('Value must be string or number');
}
```


