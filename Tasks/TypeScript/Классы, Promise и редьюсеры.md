## 🏗️ Классы, Promise и редьюсеры

---

### **Задача 1: Классы и модификаторы доступа**

**Условие:**
Создайте класс `Bird`, который наследует `Animal` и добавляет метод `fly(distance: number)`. Поле `name` должно быть доступно только внутри класса и его наследников.

**Начальный код:**
```ts
class Animal {
  constructor(public name: string) {}
  move(distance: number = 0) {
    console.log(`${this.name} moved ${distance}m.`);
  }
}

// TODO: Создать класс Bird
```

**Решение:**
```ts
class Animal {
  protected name: string; // protected - доступно в классе и наследниках
  
  constructor(name: string) {
    this.name = name;
  }
  
  move(distance: number = 0) {
    console.log(`${this.name} moved ${distance}m.`);
  }
}

class Bird extends Animal {
  fly(distance: number) {
    console.log(`${this.name} flew ${distance}m.`);
  }
}

const bird = new Bird("Eagle");
bird.fly(100); // ✅ "Eagle flew 100m."
// bird.name; // ❌ Ошибка - name недоступен снаружи
```

**Объяснение:**
- `protected` — доступно в классе и его наследниках, но не снаружи
- `private` — доступно только внутри класса
- `public` — доступно везде (по умолчанию)

---

### **Задача 2: Работа с Promise и async/await**

**Условие:**
Напишите асинхронную функцию `loadData`, которая вызывает `fetchData` и выводит результат в консоль.

**Начальный код:**
```ts
function fetchData(): Promise<string> {
  return new Promise((resolve) => {
    setTimeout(() => resolve("Data loaded"), 1000);
  });
}

// TODO: Написать loadData
```

**Решение:**
```ts
function fetchData(): Promise<string> {
  return new Promise((resolve) => {
    setTimeout(() => resolve("Data loaded"), 1000);
  });
}

async function loadData(): Promise<void> {
  try {
    const data = await fetchData();
    console.log(data); // "Data loaded"
  } catch (error) {
    console.error("Error:", error);
  }
}

// Или с явной типизацией ошибки
async function loadData(): Promise<void> {
  try {
    const data = await fetchData();
    console.log(data);
  } catch (error) {
    if (error instanceof Error) {
      console.error("Error:", error.message);
    }
  }
}
```

---

### **Задача 3: Типизация редьюсера**

**Условие:**
Реализуйте функцию-редьюсер, которая обрабатывает действия `increment` и `decrement`, изменяя состояние.

**Начальный код:**
```ts
type Action = 
  | { type: "increment"; payload: number }
  | { type: "decrement"; payload: number };

function reducer(state: number, action: Action): number {
  // TODO: Реализовать редьюсер
}
```

**Решение:**
```ts
type Action = 
  | { type: "increment"; payload: number }
  | { type: "decrement"; payload: number };

function reducer(state: number, action: Action): number {
  switch (action.type) {
    case "increment":
      return state + action.payload;
    case "decrement":
      return state - action.payload;
    default:
      // Exhaustive check
      const _exhaustive: never = action;
      return state;
  }
}

// Использование
const state = reducer(10, { type: "increment", payload: 5 }); // 15
const newState = reducer(state, { type: "decrement", payload: 3 }); // 12
```

**С exhaustive check:**
```ts
function reducer(state: number, action: Action): number {
  switch (action.type) {
    case "increment":
      return state + action.payload;
    case "decrement":
      return state - action.payload;
    default:
      // Если добавим новый action, TypeScript выдаст ошибку
      const _exhaustive: never = action;
      throw new Error(`Unknown action: ${(_exhaustive as any).type}`);
  }
}
```

---

### **Задача 4: Типизация сложного редьюсера**

**Условие:**
Создайте типизированный редьюсер для управления состоянием пользователя.

**Начальный код:**
```ts
// TODO: Создать типы и редьюсер
```

**Решение:**
```ts
interface UserState {
  user: { id: number; name: string; email: string } | null;
  loading: boolean;
  error: string | null;
}

type UserAction =
  | { type: "FETCH_START" }
  | { type: "FETCH_SUCCESS"; payload: { id: number; name: string; email: string } }
  | { type: "FETCH_ERROR"; payload: string }
  | { type: "LOGOUT" };

function userReducer(state: UserState, action: UserAction): UserState {
  switch (action.type) {
    case "FETCH_START":
      return { ...state, loading: true, error: null };
    case "FETCH_SUCCESS":
      return { user: action.payload, loading: false, error: null };
    case "FETCH_ERROR":
      return { ...state, loading: false, error: action.payload };
    case "LOGOUT":
      return { user: null, loading: false, error: null };
    default:
      const _exhaustive: never = action;
      return state;
  }
}

// Использование
const initialState: UserState = {
  user: null,
  loading: false,
  error: null
};

const newState = userReducer(initialState, { 
  type: "FETCH_SUCCESS", 
  payload: { id: 1, name: "John", email: "john@example.com" } 
});
```

---

### **Задача 5: Типизация async функции с обработкой ошибок**

**Условие:**
Создайте типизированную функцию для загрузки данных с обработкой ошибок.

**Начальный код:**
```ts
// TODO: Типизировать функцию
async function fetchUser(id) {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}
```

**Решение:**
```ts
interface User {
  id: number;
  name: string;
  email: string;
}

async function fetchUser(id: number): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  
  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
  }
  
  const data: User = await response.json();
  return data;
}

// С обработкой ошибок
async function getUser(id: number): Promise<User | null> {
  try {
    return await fetchUser(id);
  } catch (error) {
    if (error instanceof Error) {
      console.error("Error fetching user:", error.message);
    }
    return null;
  }
}
```

---

### **Задача 6: Типизация класса с generic**

**Условие:**
Создайте generic класс для работы с коллекцией элементов.

**Начальный код:**
```ts
// TODO: Создать generic класс Collection
```

**Решение:**
```ts
class Collection<T> {
  private items: T[] = [];

  add(item: T): void {
    this.items.push(item);
  }

  get(index: number): T | undefined {
    return this.items[index];
  }

  getAll(): T[] {
    return [...this.items];
  }

  remove(index: number): void {
    this.items.splice(index, 1);
  }

  size(): number {
    return this.items.length;
  }
}

// Использование
const stringCollection = new Collection<string>();
stringCollection.add("hello");
stringCollection.add("world");

const numberCollection = new Collection<number>();
numberCollection.add(1);
numberCollection.add(2);
```


