# Паттерны React

## Краткий ответ для собеседования

Основные паттерны: **Render Props** (передача функции в props для переиспользования логики), **HOC** (Higher-Order Component — обёртка компонента), **Compound Components** (составные компоненты с общим состоянием), **Provider Pattern** (Context API), **Container/Presentational** (разделение логики и UI), **Custom Hooks** (переиспользование логики через хуки). Render Props и HOC устарели с появлением хуков — теперь кастомные хуки предпочтительнее.

---

## 1. Render Props

### Что это

**Передача функции через prop**, которая возвращает React-элемент. Компонент вызывает эту функцию и передаёт ей данные.

**Цель:**  
Переиспользование логики между компонентами.

### Пример

```jsx
class MouseTracker extends React.Component {
  state = { x: 0, y: 0 };

  handleMouseMove = (e) => {
    this.setState({ x: e.clientX, y: e.clientY });
  };

  render() {
    return (
      <div onMouseMove={this.handleMouseMove}>
        {this.props.render(this.state)} {/* вызов render prop */}
      </div>
    );
  }
}

// Использование
<MouseTracker render={({ x, y }) => (
  <h1>Позиция: {x}, {y}</h1>
)} />
```

### Когда использовать

Раньше — для переиспользования логики; **сейчас устарел** (заменён кастомными хуками).

### Минусы

- Callback hell (вложенные render props)
- Сложнее читать

---

## 2. Higher-Order Component (HOC)

### Что это

**Функция, которая принимает компонент и возвращает новый компонент** с дополнительной логикой.

**Цель:**  
Переиспользование логики, добавление функциональности.

### Пример

```jsx
function withLogging(Component) {
  return function WrappedComponent(props) {
    console.log('Rendering:', Component.name);
    return <Component {...props} />;
  };
}

const MyComponent = ({ name }) => <div>Hello, {name}</div>;

const MyComponentWithLogging = withLogging(MyComponent);
```

### Типичные примеры

- `withRouter` (React Router)
- `connect` (Redux)

### Когда использовать

Раньше — для переиспользования логики; **сейчас устарел** (заменён кастомными хуками).

### Минусы

- Wrapper hell (множество HOC оборачивают компонент)
- Конфликты props
- Сложнее отлаживать

---

## 3. Custom Hooks (Кастомные хуки)

### Что это

**Функция, начинающаяся с `use`**, которая переиспользует логику состояния через хуки.

**Цель:**  
Переиспользование логики без HOC и Render Props.

### Пример

```jsx
function useMousePosition() {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handleMove = (e) => setPosition({ x: e.clientX, y: e.clientY });
    window.addEventListener('mousemove', handleMove);
    return () => window.removeEventListener('mousemove', handleMove);
  }, []);

  return position;
}

// Использование
function App() {
  const { x, y } = useMousePosition();
  return <h1>Позиция: {x}, {y}</h1>;
}
```

### Когда использовать

**Всегда**, когда нужно переиспользовать логику состояния.

### Преимущества

- Проще, чем HOC и Render Props
- Нет wrapper hell
- Легко комбинировать несколько хуков

---

## 4. Compound Components (Составные компоненты)

### Что это

**Группа компонентов, которые работают вместе** через общее состояние (обычно через Context).

**Цель:**  
Гибкий API для сложных компонентов.

### Пример

```jsx
const TabsContext = React.createContext();

function Tabs({ children }) {
  const [activeTab, setActiveTab] = useState(0);

  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      {children}
    </TabsContext.Provider>
  );
}

function TabList({ children }) {
  return <div>{children}</div>;
}

function Tab({ index, children }) {
  const { activeTab, setActiveTab } = useContext(TabsContext);
  return (
    <button onClick={() => setActiveTab(index)} disabled={activeTab === index}>
      {children}
    </button>
  );
}

function TabPanel({ index, children }) {
  const { activeTab } = useContext(TabsContext);
  return activeTab === index ? <div>{children}</div> : null;
}

// Использование
<Tabs>
  <TabList>
    <Tab index={0}>Tab 1</Tab>
    <Tab index={1}>Tab 2</Tab>
  </TabList>
  <TabPanel index={0}>Content 1</TabPanel>
  <TabPanel index={1}>Content 2</TabPanel>
</Tabs>
```

### Когда использовать

Сложные UI-компоненты (вкладки, аккордеоны, меню) с гибким API.

### Преимущества

- Гибкий API
- Легко кастомизировать

---

## 5. Provider Pattern (Context API)

### Что это

**Передача данных через Context API** без prop drilling.

### Пример

```jsx
const ThemeContext = React.createContext();

function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

function App() {
  return (
    <ThemeProvider>
      <Component />
    </ThemeProvider>
  );
}

function Component() {
  const { theme } = useContext(ThemeContext);
  return <div>Тема: {theme}</div>;
}
```

### Когда использовать

Глобальное состояние (тема, язык, авторизация).

---

## 6. Container/Presentational Pattern

### Что это

**Разделение компонентов на:**
- **Container (Контейнер)** — логика, состояние, side-эффекты
- **Presentational (Презентационный)** — только UI, получает данные через props

### Пример

**Container:**

```jsx
function UserContainer() {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch('/api/user').then(r => r.json()).then(setUser);
  }, []);

  return <UserCard user={user} />; // передаёт данные
}
```

**Presentational:**

```jsx
function UserCard({ user }) {
  if (!user) return <div>Loading...</div>;
  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

### Когда использовать

Раньше — распространённый паттерн; **сейчас менее актуален** (хуки позволяют совмещать логику и UI).

---

## 7. Controlled vs Uncontrolled Components

### Controlled (Контролируемые)

**React управляет состоянием** input.

```jsx
const [value, setValue] = useState('');

<input value={value} onChange={(e) => setValue(e.target.value)} />
```

### Uncontrolled (Неконтролируемые)

**DOM управляет состоянием** input.

```jsx
const inputRef = useRef();

<input ref={inputRef} />

// Чтение: inputRef.current.value
```

### Когда использовать

- **Controlled** — почти всегда (React управляет всем)
- **Uncontrolled** — интеграция со сторонними библиотеками, файлы

---

## 8. State Reducer Pattern

### Что это

**Пользователь может переопределить логику изменения состояния** компонента через reducer.

### Пример

```jsx
function useToggle(initialValue, reducer) {
  const [state, dispatch] = useReducer(reducer || toggleReducer, {
    on: initialValue
  });

  const toggle = () => dispatch({ type: 'TOGGLE' });

  return [state, { toggle }];
}

// Кастомный reducer
const customReducer = (state, action) => {
  if (action.type === 'TOGGLE') {
    console.log('Custom logic');
  }
  return toggleReducer(state, action);
};

const [state, { toggle }] = useToggle(false, customReducer);
```

### Когда использовать

Библиотеки компонентов — дать пользователям контроль над логикой.

---

## Сравнение паттернов

| Паттерн | Сложность | Актуальность | Использование |
|---------|-----------|--------------|---------------|
| **Render Props** | Средняя | ❌ Устарел | Хуки лучше |
| **HOC** | Средняя | ❌ Устарел | Хуки лучше |
| **Custom Hooks** | Низкая | ✅ Актуален | Переиспользование логики |
| **Compound Components** | Средняя | ✅ Актуален | Сложные UI-компоненты |
| **Provider Pattern** | Низкая | ✅ Актуален | Глобальное состояние |
| **Container/Presentational** | Низкая | ⚠️ Менее актуален | Разделение логики и UI |

---

## Вопросы на собеседовании

| Вопрос | Краткий ответ |
|--------|----------------|
| Что такое Render Props? | Передача функции в props для переиспользования логики; устарел |
| Что такое HOC? | Функция, оборачивающая компонент для добавления логики; устарел |
| Зачем нужны кастомные хуки? | Переиспользование логики состояния; заменили Render Props и HOC |
| Что такое Compound Components? | Группа компонентов с общим состоянием через Context |
| Что такое Container/Presentational? | Разделение логики (Container) и UI (Presentational); менее актуален с хуками |

---

## Кратко

Паттерны: **Render Props** (функция в props), **HOC** (обёртка компонента), **Custom Hooks** (переиспользование логики, актуален), **Compound Components** (составные с Context), **Provider Pattern** (Context API), **Container/Presentational** (разделение логики и UI). Render Props и HOC устарели — Custom Hooks предпочтительнее.
