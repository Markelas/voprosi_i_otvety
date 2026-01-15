## ⚛️ Типизация React в TypeScript

TypeScript помогает создавать типобезопасные React-компоненты, события и хуки, предотвращая ошибки на этапе разработки.

---

## 📦 Типизация компонентов

### **Функциональный компонент:**

```tsx
interface ButtonProps {
  label: string;
  onClick: () => void;
  disabled?: boolean;
}

function Button({ label, onClick, disabled }: ButtonProps) {
  return (
    <button onClick={onClick} disabled={disabled}>
      {label}
    </button>
  );
}
```

### **С children:**

```tsx
interface CardProps {
  title: string;
  children: React.ReactNode; // или JSX.Element | string | null
}

function Card({ title, children }: CardProps) {
  return (
    <div>
      <h2>{title}</h2>
      {children}
    </div>
  );
}
```

### **С типизированным children:**

```tsx
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
}

function List<T>({ items, renderItem }: ListProps<T>) {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{renderItem(item)}</li>
      ))}
    </ul>
  );
}
```

---

## 🎯 Типизация Props

### **Интерфейс vs Type:**

```tsx
// Интерфейс (рекомендуется для объектов)
interface UserProps {
  name: string;
  age: number;
}

// Type (для union, intersection)
type Status = "active" | "inactive";
type UserStatus = UserProps & { status: Status };
```

### **Опциональные props:**

```tsx
interface UserProps {
  name: string;
  age?: number; // Опциональный
  email: string;
}

function User({ name, age = 0, email }: UserProps) {
  return <div>{name} - {age}</div>;
}
```

### **Props с defaultProps:**

```tsx
interface ButtonProps {
  variant?: "primary" | "secondary";
  size?: "small" | "large";
}

function Button({ variant = "primary", size = "small" }: ButtonProps) {
  return <button className={`btn-${variant} btn-${size}`}>Click</button>;
}
```

---

## 🖱️ Типизация событий

### **События формы:**

```tsx
function Form() {
  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    // ...
  };

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    console.log(e.target.value);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input onChange={handleChange} />
    </form>
  );
}
```

### **События клика:**

```tsx
function Button() {
  const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => {
    e.preventDefault();
    console.log("Clicked");
  };

  return <button onClick={handleClick}>Click</button>;
}
```

### **События клавиатуры:**

```tsx
function Input() {
  const handleKeyDown = (e: React.KeyboardEvent<HTMLInputElement>) => {
    if (e.key === "Enter") {
      // ...
    }
  };

  return <input onKeyDown={handleKeyDown} />;
}
```

### **Универсальный тип события:**

```tsx
// Для любого HTML элемента
type InputChangeEvent = React.ChangeEvent<HTMLInputElement>;
type ButtonClickEvent = React.MouseEvent<HTMLButtonElement>;
type FormSubmitEvent = React.FormEvent<HTMLFormElement>;
```

---

## 🎣 Типизация хуков

### **useState:**

```tsx
// Примитивы
const [count, setCount] = useState<number>(0);
const [name, setName] = useState<string>("");

// Объекты
interface User {
  name: string;
  age: number;
}
const [user, setUser] = useState<User | null>(null);

// Массивы
const [items, setItems] = useState<string[]>([]);
```

### **useEffect:**

```tsx
useEffect(() => {
  // Эффект
}, []); // Зависимости

// С возвращаемой функцией очистки
useEffect(() => {
  const timer = setInterval(() => {}, 1000);
  return () => clearInterval(timer);
}, []);
```

### **useRef:**

```tsx
// Для DOM элементов
const inputRef = useRef<HTMLInputElement>(null);

// Для значений
const countRef = useRef<number>(0);
```

### **useCallback:**

```tsx
const handleClick = useCallback((id: number) => {
  console.log(id);
}, []); // Зависимости

// Типизация параметров
const handleClick = useCallback<(id: number) => void>((id) => {
  console.log(id);
}, []);
```

### **useMemo:**

```tsx
const expensiveValue = useMemo<number>(() => {
  return computeExpensiveValue(a, b);
}, [a, b]);
```

### **useContext:**

```tsx
interface ThemeContextType {
  theme: "light" | "dark";
  toggleTheme: () => void;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error("useTheme must be used within ThemeProvider");
  }
  return context;
}
```

---

## 🎯 Типизация кастомных хуков

```tsx
interface UseCounterReturn {
  count: number;
  increment: () => void;
  decrement: () => void;
}

function useCounter(initialValue: number = 0): UseCounterReturn {
  const [count, setCount] = useState<number>(initialValue);

  const increment = useCallback(() => {
    setCount((prev) => prev + 1);
  }, []);

  const decrement = useCallback(() => {
    setCount((prev) => prev - 1);
  }, []);

  return { count, increment, decrement };
}
```

---

## 🎯 Краткий ответ для собеседования

**Типизация React в TypeScript:**
- **Компоненты**: типизируйте props через `interface` или `type`
- **События**: используйте `React.ChangeEvent<T>`, `React.MouseEvent<T>`, `React.FormEvent<T>`
- **Хуки**: указывайте generic типы для `useState<T>`, `useRef<T>`, `useCallback<T>`
- **Children**: используйте `React.ReactNode` для содержимого компонента

---

## ❓ Каверзные вопросы и ответы

### **1. Как типизировать ref для DOM элемента?**

**Ответ:**
Используйте `useRef` с generic типом элемента:

```tsx
const inputRef = useRef<HTMLInputElement>(null);

// При использовании
<input ref={inputRef} />
inputRef.current?.focus(); // Опциональная цепочка, т.к. может быть null
```

---

### **2. В чем разница между `React.ReactNode` и `JSX.Element`?**

**Ответ:**
- **`React.ReactNode`** — более широкий тип (строки, числа, элементы, массивы, null, undefined)
- **`JSX.Element`** — только React элементы

```tsx
// ReactNode - принимает всё
children: React.ReactNode; // ✅ "text", 123, <div>, null

// JSX.Element - только элементы
children: JSX.Element; // ✅ <div>, ❌ "text"
```

---

### **3. Как типизировать событие с правильным типом элемента?**

**Ответ:**
Указывайте generic тип элемента в событии:

```tsx
// Для input
const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  e.target.value; // ✅ TypeScript знает тип
};

// Для textarea
const handleChange = (e: React.ChangeEvent<HTMLTextAreaElement>) => {
  e.target.value; // ✅ TypeScript знает тип
};
```

---

### **4. Как типизировать компонент с generic props?**

**Ответ:**
Используйте generic для компонента:

```tsx
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
}

function List<T>({ items, renderItem }: ListProps<T>) {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{renderItem(item)}</li>
      ))}
    </ul>
  );
}

// Использование
<List<{ id: number; name: string }>
  items={users}
  renderItem={(user) => <div>{user.name}</div>}
/>
```

---

### **5. Как типизировать forwardRef?**

**Ответ:**
Используйте `React.forwardRef` с типами:

```tsx
interface ButtonProps {
  label: string;
}

const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ label }, ref) => {
    return <button ref={ref}>{label}</button>;
  }
);
```

---

### **6. Как типизировать компонент с render prop?**

**Ответ:**
Типизируйте функцию render:

```tsx
interface DataFetcherProps<T> {
  url: string;
  render: (data: T | null, loading: boolean) => React.ReactNode;
}

function DataFetcher<T>({ url, render }: DataFetcherProps<T>) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  
  // ...
  
  return <>{render(data, loading)}</>;
}
```

---

### **7. Как типизировать события в обработчиках?**

**Ответ:**
Используйте правильный тип события для элемента:

```tsx
// Клик по кнопке
const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => {};

// Изменение input
const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {};

// Отправка формы
const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {};

// Нажатие клавиши
const handleKeyDown = (e: React.KeyboardEvent<HTMLInputElement>) => {};
```

---

### **8. Как типизировать useState с функцией инициализации?**

**Ответ:**
TypeScript автоматически выводит тип из функции:

```tsx
// Автоматический вывод типа
const [user, setUser] = useState(() => {
  return { name: "John", age: 30 };
}); // user: { name: string; age: number }

// Явное указание типа
const [user, setUser] = useState<User | null>(() => {
  return null;
});
```

---

## 📚 Полезные практики

- **Используйте `interface`** для типизации props (можно расширять)
- **Используйте `React.ReactNode`** для children (более гибко)
- **Типизируйте события** с правильным типом элемента (`HTMLInputElement`, `HTMLButtonElement`)
- **Указывайте generic типы** для хуков (`useState<T>`, `useRef<T>`)
- **Создавайте типы для кастомных хуков** через интерфейсы возвращаемых значений
- **Используйте `as const`** для литеральных типов в props




