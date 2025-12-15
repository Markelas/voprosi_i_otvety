## 🎨 Паттерны React

Продвинутые паттерны React, которые помогают создавать более структурированный, переиспользуемый и поддерживаемый код. Эти паттерны — важная часть архитектуры компонентов, и они дают большую гибкость при создании интерфейсов.

---

## 1. Составные компоненты (Compound Components)

**Составные компоненты** позволяют создавать компоненты с гибкой структурой, где дочерние элементы определяются в пределах одного компонента, используя композицию. Это подходит для компонентов, которые имеют несколько вложенных элементов (например, формы, меню, карточки).

### 💡 Пример: Карточка с составными компонентами

```jsx
import React from 'react';

// Главный компонент Card, отвечающий за общую структуру
function Card({ children }) {
  return <div className="card">{children}</div>;
}

// Вложенный компонент для заголовка карточки
Card.Heading = function CardHeading({ children }) {
  return <div className="card-heading">{children}</div>;
};

// Вложенный компонент для тела карточки
Card.Body = function CardBody({ children }) {
  return <div className="card-body">{children}</div>;
};

// Вложенный компонент для кнопок карточки
Card.Button = function CardButton({ children, onClick }) {
  return (
    <button className="card-button" onClick={onClick}>
      {children}
    </button>
  );
};

// Пример использования составных компонентов
export default function App() {
  return (
    <Card>
      <Card.Heading>Заголовок карточки</Card.Heading>
      <Card.Body>
        <p>Это текст внутри карточки.</p>
      </Card.Body>
      <Card.Button onClick={() => alert('Нажали кнопку!')}>
        Клик
      </Card.Button>
    </Card>
  );
}
```

### 🎯 Преимущества:

- ✅ **Гибкость** — можно комбинировать компоненты в любом порядке
- ✅ **Переиспользование** — каждый подкомпонент можно использовать отдельно
- ✅ **Читаемость** — структура компонента понятна из JSX
- ✅ **Инкапсуляция** — логика и стили связаны в одном месте

### 📝 Пояснение:

- Компонент `Card` управляет общей структурой, а его вложенные элементы (`Card.Heading`, `Card.Body`, `Card.Button`) предоставляют специализированные части
- Этот подход позволяет гибко управлять содержимым компонента, не изменяя его внутреннюю реализацию

### 🔄 Пример с Context для обмена состоянием

```jsx
import React, { createContext, useContext, useState } from 'react';

const TabsContext = createContext();

function Tabs({ children, defaultTab }) {
  const [activeTab, setActiveTab] = useState(defaultTab);
  
  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      <div className="tabs">{children}</div>
    </TabsContext.Provider>
  );
}

Tabs.List = function TabsList({ children }) {
  return <div className="tabs-list">{children}</div>;
};

Tabs.Tab = function Tab({ id, children }) {
  const { activeTab, setActiveTab } = useContext(TabsContext);
  
  return (
    <button
      className={activeTab === id ? 'active' : ''}
      onClick={() => setActiveTab(id)}
    >
      {children}
    </button>
  );
};

Tabs.Panel = function TabPanel({ id, children }) {
  const { activeTab } = useContext(TabsContext);
  
  if (activeTab !== id) return null;
  return <div className="tab-panel">{children}</div>;
};

// Использование
function App() {
  return (
    <Tabs defaultTab="tab1">
      <Tabs.List>
        <Tabs.Tab id="tab1">Вкладка 1</Tabs.Tab>
        <Tabs.Tab id="tab2">Вкладка 2</Tabs.Tab>
      </Tabs.List>
      <Tabs.Panel id="tab1">Содержимое вкладки 1</Tabs.Panel>
      <Tabs.Panel id="tab2">Содержимое вкладки 2</Tabs.Panel>
    </Tabs>
  );
}
```

---

## 2. Render Props

**Render Props** — это паттерн, который позволяет компоненту динамически изменять его содержимое с помощью функции, передаваемой через пропсы. Это полезно, когда нужно делегировать рендеринг данных внешним компонентам.

### 💡 Пример: Компонент с Render Props для отображения состояния

```jsx
import React, { useState } from 'react';

function Toggle({ render }) {
  const [isOn, setIsOn] = useState(false);
  const toggle = () => setIsOn(!isOn);

  return render({ isOn, toggle });
}

export default function App() {
  return (
    <Toggle
      render={({ isOn, toggle }) => (
        <div>
          <p>Состояние: {isOn ? 'Включено' : 'Выключено'}</p>
          <button onClick={toggle}>Переключить</button>
        </div>
      )}
    />
  );
}
```

### 🔄 Альтернативный синтаксис с children

```jsx
function Toggle({ children }) {
  const [isOn, setIsOn] = useState(false);
  const toggle = () => setIsOn(!isOn);

  return children({ isOn, toggle });
}

// Использование
<Toggle>
  {({ isOn, toggle }) => (
    <div>
      <p>Состояние: {isOn ? 'Включено' : 'Выключено'}</p>
      <button onClick={toggle}>Переключить</button>
    </div>
  )}
</Toggle>
```

### 💡 Пример: Mouse Tracker с Render Props

```jsx
import React, { useState, useEffect } from 'react';

function MouseTracker({ render }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handleMouseMove = (e) => {
      setPosition({ x: e.clientX, y: e.clientY });
    };
    
    window.addEventListener('mousemove', handleMouseMove);
    return () => window.removeEventListener('mousemove', handleMouseMove);
  }, []);

  return render(position);
}

// Использование
function App() {
  return (
    <MouseTracker
      render={({ x, y }) => (
        <div>
          <p>Позиция мыши: {x}, {y}</p>
        </div>
      )}
    />
  );
}
```

### 🎯 Преимущества:

- ✅ **Гибкость** — полный контроль над рендерингом
- ✅ **Переиспользование логики** — логика отделена от представления
- ✅ **Композиция** — можно комбинировать несколько render props

### ⚠️ Недостатки:

- ❌ **Вложенность** — может привести к "callback hell"
- ❌ **Производительность** — функция создается при каждом рендере (нужна мемоизация)

### 📝 Пояснение:

- Вместо того, чтобы определять статическую структуру, компонент `Toggle` принимает `render`-функцию и передает текущее состояние `isOn` и метод `toggle` в качестве аргументов
- Это позволяет внешним компонентам управлять тем, как они будут отображать состояние `Toggle`

### 🔄 Современная альтернатива: Custom Hooks

```jsx
// Вместо Render Props можно использовать кастомный хук
function useToggle(initialValue = false) {
  const [isOn, setIsOn] = useState(initialValue);
  const toggle = () => setIsOn(!isOn);
  return [isOn, toggle];
}

// Использование
function App() {
  const [isOn, toggle] = useToggle();
  
  return (
    <div>
      <p>Состояние: {isOn ? 'Включено' : 'Выключено'}</p>
      <button onClick={toggle}>Переключить</button>
    </div>
  );
}
```

---

## 3. Инкапсуляция

**Инкапсуляция** — это принцип скрытия внутренней логики и управления состоянием внутри компонента. Инкапсуляция предотвращает нежелательное вмешательство в работу компонента извне и защищает внутреннюю логику.

### 💡 Пример: Инкапсуляция состояния компонента

```jsx
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  
  // Внутренние методы для управления состоянием
  const increment = () => setCount(prev => prev + 1);
  const decrement = () => setCount(prev => prev - 1);
  const reset = () => setCount(0);

  return (
    <div>
      <p>Счетчик: {count}</p>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={reset}>Сброс</button>
    </div>
  );
}

export default Counter;
```

### 💡 Пример: Инкапсуляция с закрытыми методами и валидацией

```jsx
import React, { useState } from 'react';

function FormInput({ label, type = 'text' }) {
  const [value, setValue] = useState('');
  const [error, setError] = useState('');

  // Внутренняя логика валидации
  const validate = (inputValue) => {
    if (type === 'email' && !inputValue.includes('@')) {
      return 'Некорректный email';
    }
    if (inputValue.length < 3) {
      return 'Минимум 3 символа';
    }
    return '';
  };

  const handleChange = (e) => {
    const newValue = e.target.value;
    setValue(newValue);
    setError(validate(newValue));
  };

  return (
    <div>
      <label>{label}</label>
      <input 
        type={type} 
        value={value} 
        onChange={handleChange}
      />
      {error && <span className="error">{error}</span>}
    </div>
  );
}
```

### 🎯 Преимущества:

- ✅ **Безопасность** — внутренняя логика защищена от внешнего вмешательства
- ✅ **Простота использования** — компонент предоставляет простой API
- ✅ **Поддерживаемость** — изменения внутренней логики не влияют на использование
- ✅ **Тестируемость** — можно тестировать компонент изолированно

### 📝 Пояснение:

- Методы `increment`, `decrement` и `reset` скрыты внутри компонента и не могут быть вызваны извне
- Этот подход уменьшает сложность и предотвращает неожиданные изменения состояния компонента
- Внутренняя логика валидации инкапсулирована и не доступна извне

---

## 4. Декомпозиция и Абстракция

**Декомпозиция** — это процесс разбиения больших компонентов на более мелкие и специализированные компоненты. **Абстракция** предполагает создание компонентов, которые инкапсулируют общую логику и могут использоваться повторно.

### 💡 Пример: Декомпозиция формы на отдельные компоненты

```jsx
import React from 'react';

function Form({ children, onSubmit }) {
  const handleSubmit = (e) => {
    e.preventDefault();
    onSubmit(e);
  };

  return <form onSubmit={handleSubmit}>{children}</form>;
}

Form.Input = function FormInput({ label, type = 'text', ...props }) {
  return (
    <div className="form-group">
      <label>{label}</label>
      <input type={type} {...props} />
    </div>
  );
};

Form.Textarea = function FormTextarea({ label, ...props }) {
  return (
    <div className="form-group">
      <label>{label}</label>
      <textarea {...props} />
    </div>
  );
};

Form.Button = function FormButton({ children, type = 'submit' }) {
  return <button type={type}>{children}</button>;
};

// Использование декомпозированных компонентов
export default function App() {
  const handleSubmit = (e) => {
    const formData = new FormData(e.target);
    console.log(Object.fromEntries(formData));
  };

  return (
    <Form onSubmit={handleSubmit}>
      <Form.Input label="Имя" name="name" type="text" />
      <Form.Input label="Электронная почта" name="email" type="email" />
      <Form.Textarea label="Сообщение" name="message" />
      <Form.Button>Отправить</Form.Button>
    </Form>
  );
}
```

### 💡 Пример: Абстракция для работы с API

```jsx
import React, { useState, useEffect } from 'react';

// Абстракция для загрузки данных
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch(url)
      .then(response => response.json())
      .then(data => {
        setData(data);
        setLoading(false);
      })
      .catch(error => {
        setError(error);
        setLoading(false);
      });
  }, [url]);

  return { data, loading, error };
}

// Декомпозиция компонента пользователя
function UserAvatar({ src, alt }) {
  return <img src={src} alt={alt} className="avatar" />;
}

function UserName({ name }) {
  return <h3>{name}</h3>;
}

function UserEmail({ email }) {
  return <p>{email}</p>;
}

function UserCard({ user }) {
  return (
    <div className="user-card">
      <UserAvatar src={user.avatar} alt={user.name} />
      <UserName name={user.name} />
      <UserEmail email={user.email} />
    </div>
  );
}

// Использование
function App() {
  const { data: users, loading, error } = useFetch('/api/users');

  if (loading) return <div>Загрузка...</div>;
  if (error) return <div>Ошибка: {error.message}</div>;

  return (
    <div>
      {users.map(user => (
        <UserCard key={user.id} user={user} />
      ))}
    </div>
  );
}
```

### 🎯 Преимущества:

- ✅ **Модульность** — каждый компонент выполняет одну задачу
- ✅ **Переиспользование** — компоненты можно использовать в разных местах
- ✅ **Читаемость** — код легче понимать и поддерживать
- ✅ **Тестируемость** — маленькие компоненты проще тестировать

### 📝 Пояснение:

- Каждый компонент (`Form.Input`, `Form.Button`) выполняет свою маленькую задачу
- Это упрощает код и делает его более модульным
- Абстракция через кастомные хуки позволяет переиспользовать логику

---

## 5. Композиция компонентов

**Композиция** — это процесс объединения нескольких простых компонентов для создания более сложного интерфейса. В React композиция часто используется вместо наследования для объединения компонентов.

### 💡 Пример: Композиция компонентов с помощью children

```jsx
import React from 'react';

function Layout({ header, content, footer }) {
  return (
    <div className="layout">
      <header>{header}</header>
      <main>{content}</main>
      <footer>{footer}</footer>
    </div>
  );
}

export default function App() {
  return (
    <Layout
      header={<h1>Заголовок сайта</h1>}
      content={<p>Это основной контент страницы.</p>}
      footer={<p>Подвал сайта</p>}
    />
  );
}
```

### 💡 Пример: Композиция с несколькими children

```jsx
import React from 'react';

function Page({ children }) {
  return <div className="page">{children}</div>;
}

function Sidebar({ children }) {
  return <aside className="sidebar">{children}</aside>;
}

function Main({ children }) {
  return <main className="main">{children}</main>;
}

// Использование композиции
function App() {
  return (
    <Page>
      <Sidebar>
        <nav>Навигация</nav>
      </Sidebar>
      <Main>
        <h1>Заголовок</h1>
        <p>Контент</p>
      </Main>
    </Page>
  );
}
```

### 💡 Пример: Композиция с функциями-компонентами

```jsx
import React from 'react';

function Modal({ isOpen, onClose, children }) {
  if (!isOpen) return null;

  return (
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal-content" onClick={(e) => e.stopPropagation()}>
        {children}
      </div>
    </div>
  );
}

Modal.Header = function ModalHeader({ children }) {
  return <div className="modal-header">{children}</div>;
};

Modal.Body = function ModalBody({ children }) {
  return <div className="modal-body">{children}</div>;
};

Modal.Footer = function ModalFooter({ children }) {
  return <div className="modal-footer">{children}</div>;
};

// Использование
function App() {
  const [isOpen, setIsOpen] = React.useState(false);

  return (
    <>
      <button onClick={() => setIsOpen(true)}>Открыть модал</button>
      <Modal isOpen={isOpen} onClose={() => setIsOpen(false)}>
        <Modal.Header>
          <h2>Заголовок модального окна</h2>
        </Modal.Header>
        <Modal.Body>
          <p>Содержимое модального окна</p>
        </Modal.Body>
        <Modal.Footer>
          <button onClick={() => setIsOpen(false)}>Закрыть</button>
        </Modal.Footer>
      </Modal>
    </>
  );
}
```

### 🎯 Преимущества:

- ✅ **Гибкость** — можно комбинировать компоненты разными способами
- ✅ **Переиспользование** — компоненты можно использовать в разных контекстах
- ✅ **Читаемость** — структура понятна из JSX
- ✅ **Расширяемость** — легко добавлять новые компоненты

### 📝 Пояснение:

- Компонент `Layout` использует `header`, `content` и `footer` для динамического построения своей структуры
- Мы можем передавать в эти пропсы любые React-компоненты, что делает `Layout` гибким и повторно используемым
- Композиция позволяет создавать сложные интерфейсы из простых компонентов

---

## 6. Контейнер и Презентационные компоненты

**Контейнерные компоненты** содержат логику работы с данными и управляют состоянием. **Презентационные компоненты** отвечают за отображение UI и не содержат бизнес-логики.

### 💡 Пример: Разделение контейнера и презентационного компонента

```jsx
import React, { useState, useEffect } from 'react';

// Контейнерный компонент
function UserContainer() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    fetch('https://jsonplaceholder.typicode.com/users')
      .then(response => response.json())
      .then(data => {
        setUsers(data);
        setLoading(false);
      })
      .catch(error => {
        setError(error);
        setLoading(false);
      });
  }, []);
  
  if (loading) return <div>Загрузка...</div>;
  if (error) return <div>Ошибка: {error.message}</div>;
  
  return <UserList users={users} />;
}

// Презентационный компонент
function UserList({ users }) {
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}

export default UserContainer;
```

### 💡 Пример: Более сложный пример с фильтрацией

```jsx
import React, { useState, useEffect } from 'react';

// Контейнерный компонент
function UserSearchContainer() {
  const [users, setUsers] = useState([]);
  const [searchTerm, setSearchTerm] = useState('');
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch('https://jsonplaceholder.typicode.com/users')
      .then(response => response.json())
      .then(data => {
        setUsers(data);
        setLoading(false);
      });
  }, []);

  const filteredUsers = users.filter(user =>
    user.name.toLowerCase().includes(searchTerm.toLowerCase())
  );

  return (
    <UserSearch
      users={filteredUsers}
      searchTerm={searchTerm}
      onSearchChange={setSearchTerm}
      loading={loading}
    />
  );
}

// Презентационный компонент
function UserSearch({ users, searchTerm, onSearchChange, loading }) {
  if (loading) return <div>Загрузка...</div>;

  return (
    <div>
      <input
        type="text"
        value={searchTerm}
        onChange={(e) => onSearchChange(e.target.value)}
        placeholder="Поиск пользователей..."
      />
      <ul>
        {users.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}

export default UserSearchContainer;
```

### 🎯 Преимущества:

- ✅ **Разделение ответственности** — логика отделена от представления
- ✅ **Переиспользование** — презентационные компоненты можно использовать в разных контекстах
- ✅ **Тестируемость** — презентационные компоненты легко тестировать
- ✅ **Читаемость** — код проще понимать

### 📝 Пояснение:

- `UserContainer` управляет логикой получения данных, состоянием и обработкой ошибок
- `UserList` — чистый компонент, который только отображает переданные ему данные, не изменяя их
- Презентационные компоненты получают данные через props и вызывают колбэки для взаимодействия

### 🔄 Современный подход с кастомными хуками

```jsx
// Кастомный хук для логики
function useUsers() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch('https://jsonplaceholder.typicode.com/users')
      .then(response => response.json())
      .then(data => {
        setUsers(data);
        setLoading(false);
      })
      .catch(error => {
        setError(error);
        setLoading(false);
      });
  }, []);

  return { users, loading, error };
}

// Презентационный компонент
function UserList({ users, loading, error }) {
  if (loading) return <div>Загрузка...</div>;
  if (error) return <div>Ошибка: {error.message}</div>;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}

// Использование
function App() {
  const { users, loading, error } = useUsers();
  return <UserList users={users} loading={loading} error={error} />;
}
```

---

## 7. Контекстный API для управления состоянием

**Context API** позволяет передавать данные через дерево компонентов без необходимости передавать props на каждом уровне. Это полезно для глобального состояния, темы, авторизации и других данных, которые нужны многим компонентам.

### 💡 Пример: Создание контекста для темы

```jsx
import React, { createContext, useContext, useState } from 'react';

// Создание контекста
const ThemeContext = createContext();

// Провайдер темы
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

// Кастомный хук для использования темы
function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within ThemeProvider');
  }
  return context;
}

// Компонент, использующий тему
function ThemedButton() {
  const { theme, toggleTheme } = useTheme();

  return (
    <button
      onClick={toggleTheme}
      style={{
        backgroundColor: theme === 'light' ? '#fff' : '#333',
        color: theme === 'light' ? '#333' : '#fff',
      }}
    >
      Текущая тема: {theme}
    </button>
  );
}

// Использование
function App() {
  return (
    <ThemeProvider>
      <ThemedButton />
    </ThemeProvider>
  );
}
```

### 💡 Пример: Контекст для авторизации

```jsx
import React, { createContext, useContext, useState } from 'react';

const AuthContext = createContext();

function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(false);

  const login = async (email, password) => {
    setLoading(true);
    // Симуляция API запроса
    setTimeout(() => {
      setUser({ email, name: 'John Doe' });
      setLoading(false);
    }, 1000);
  };

  const logout = () => {
    setUser(null);
  };

  return (
    <AuthContext.Provider value={{ user, login, logout, loading }}>
      {children}
    </AuthContext.Provider>
  );
}

function useAuth() {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider');
  }
  return context;
}

// Компоненты
function LoginButton() {
  const { login, loading } = useAuth();

  return (
    <button onClick={() => login('user@example.com', 'password')} disabled={loading}>
      {loading ? 'Вход...' : 'Войти'}
    </button>
  );
}

function UserProfile() {
  const { user, logout } = useAuth();

  if (!user) return null;

  return (
    <div>
      <p>Привет, {user.name}!</p>
      <button onClick={logout}>Выйти</button>
    </div>
  );
}

function App() {
  return (
    <AuthProvider>
      <LoginButton />
      <UserProfile />
    </AuthProvider>
  );
}
```

### 💡 Пример: Оптимизация с мемоизацией

```jsx
import React, { createContext, useContext, useState, useMemo } from 'react';

const AppContext = createContext();

function AppProvider({ children }) {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('');

  // Мемоизация значения контекста
  const value = useMemo(
    () => ({
      count,
      setCount,
      name,
      setName,
    }),
    [count, name]
  );

  return (
    <AppContext.Provider value={value}>
      {children}
    </AppContext.Provider>
  );
}

function useApp() {
  const context = useContext(AppContext);
  if (!context) {
    throw new Error('useApp must be used within AppProvider');
  }
  return context;
}
```

### 🎯 Преимущества:

- ✅ **Избежание prop drilling** — не нужно передавать props через множество уровней
- ✅ **Глобальное состояние** — удобно для данных, нужных многим компонентам
- ✅ **Простота использования** — легко добавить и использовать

### ⚠️ Недостатки и ограничения:

- ❌ **Производительность** — изменения контекста вызывают ре-рендер всех потребителей
- ❌ **Не подходит для часто меняющихся данных** — лучше использовать state management библиотеки
- ❌ **Сложность тестирования** — нужно оборачивать компоненты в провайдеры

### 📝 Пояснение:

- Context API полезен для данных, которые редко меняются (тема, язык, авторизация)
- Для часто меняющихся данных лучше использовать Redux, Zustand или другие state management решения
- Всегда мемоизируйте значение контекста, если оно содержит объекты или функции

---

## 8. Кастомные хуки (Custom Hooks)

**Кастомные хуки** — это функции, которые начинаются с `use` и могут вызывать другие хуки. Они позволяют переиспользовать логику состояния между компонентами.

### 💡 Пример: Кастомный хук для загрузки данных

```jsx
import { useState, useEffect } from 'react';

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    setLoading(true);
    setError(null);

    fetch(url)
      .then(response => {
        if (!response.ok) {
          throw new Error('Ошибка загрузки данных');
        }
        return response.json();
      })
      .then(data => {
        setData(data);
        setLoading(false);
      })
      .catch(error => {
        setError(error);
        setLoading(false);
      });
  }, [url]);

  return { data, loading, error };
}

// Использование
function UserList() {
  const { data: users, loading, error } = useFetch('https://jsonplaceholder.typicode.com/users');

  if (loading) return <div>Загрузка...</div>;
  if (error) return <div>Ошибка: {error.message}</div>;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

### 💡 Пример: Кастомный хук для локального хранилища

```jsx
import { useState, useEffect } from 'react';

function useLocalStorage(key, initialValue) {
  // Инициализация из localStorage
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      return initialValue;
    }
  });

  // Функция для обновления значения
  const setValue = (value) => {
    try {
      // Поддержка функции как значения
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error(error);
    }
  };

  return [storedValue, setValue];
}

// Использование
function App() {
  const [name, setName] = useLocalStorage('name', '');

  return (
    <div>
      <input
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Введите имя"
      />
      <p>Привет, {name}!</p>
    </div>
  );
}
```

### 💡 Пример: Кастомный хук для debounce

```jsx
import { useState, useEffect } from 'react';

function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);

  return debouncedValue;
}

// Использование
function SearchInput() {
  const [searchTerm, setSearchTerm] = useState('');
  const debouncedSearchTerm = useDebounce(searchTerm, 500);

  useEffect(() => {
    if (debouncedSearchTerm) {
      // Выполнить поиск
      console.log('Поиск:', debouncedSearchTerm);
    }
  }, [debouncedSearchTerm]);

  return (
    <input
      type="text"
      value={searchTerm}
      onChange={(e) => setSearchTerm(e.target.value)}
      placeholder="Поиск..."
    />
  );
}
```

### 🎯 Преимущества:

- ✅ **Переиспользование логики** — одна логика в разных компонентах
- ✅ **Чистота компонентов** — компоненты остаются простыми
- ✅ **Тестируемость** — хуки можно тестировать отдельно
- ✅ **Композиция** — можно комбинировать несколько хуков

### 📝 Правила кастомных хуков:

1. Имя должно начинаться с `use`
2. Могут вызывать другие хуки
3. Не должны вызываться условно
4. Должны быть чистыми функциями

---

## 9. Паттерн Provider

**Паттерн Provider** — это комбинация Context API и компонента-провайдера, который оборачивает приложение и предоставляет данные всем дочерним компонентам.

### 💡 Пример: Provider для управления состоянием

```jsx
import React, { createContext, useContext, useReducer } from 'react';

// Начальное состояние
const initialState = {
  count: 0,
  user: null,
};

// Редьюсер
function appReducer(state, action) {
  switch (action.type) {
    case 'INCREMENT':
      return { ...state, count: state.count + 1 };
    case 'DECREMENT':
      return { ...state, count: state.count - 1 };
    case 'SET_USER':
      return { ...state, user: action.payload };
    default:
      return state;
  }
}

// Создание контекста
const AppContext = createContext();

// Provider компонент
function AppProvider({ children }) {
  const [state, dispatch] = useReducer(appReducer, initialState);

  return (
    <AppContext.Provider value={{ state, dispatch }}>
      {children}
    </AppContext.Provider>
  );
}

// Кастомный хук
function useApp() {
  const context = useContext(AppContext);
  if (!context) {
    throw new Error('useApp must be used within AppProvider');
  }
  return context;
}

// Компоненты
function Counter() {
  const { state, dispatch } = useApp();

  return (
    <div>
      <p>Счетчик: {state.count}</p>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>+</button>
      <button onClick={() => dispatch({ type: 'DECREMENT' })}>-</button>
    </div>
  );
}

function App() {
  return (
    <AppProvider>
      <Counter />
    </AppProvider>
  );
}
```

### 💡 Пример: Множественные провайдеры

```jsx
import React from 'react';

function ThemeProvider({ children }) {
  // ... логика темы
  return <ThemeContext.Provider value={themeValue}>{children}</ThemeContext.Provider>;
}

function AuthProvider({ children }) {
  // ... логика авторизации
  return <AuthContext.Provider value={authValue}>{children}</AuthContext.Provider>;
}

function App() {
  return (
    <ThemeProvider>
      <AuthProvider>
        <YourComponents />
      </AuthProvider>
    </ThemeProvider>
  );
}
```

---

## 10. Паттерн Controlled/Uncontrolled Components

**Controlled Components** — компоненты, состояние которых контролируется React через props. **Uncontrolled Components** — компоненты, которые хранят свое состояние в DOM.

### 💡 Пример: Controlled Component

```jsx
import React, { useState } from 'react';

function ControlledInput() {
  const [value, setValue] = useState('');

  return (
    <input
      type="text"
      value={value}
      onChange={(e) => setValue(e.target.value)}
    />
  );
}
```

### 💡 Пример: Uncontrolled Component

```jsx
import React, { useRef } from 'react';

function UncontrolledInput() {
  const inputRef = useRef(null);

  const handleSubmit = () => {
    console.log(inputRef.current.value);
  };

  return (
    <div>
      <input type="text" ref={inputRef} />
      <button onClick={handleSubmit}>Отправить</button>
    </div>
  );
}
```

### 🎯 Когда использовать:

**Controlled Components:**
- ✅ Нужна валидация в реальном времени
- ✅ Нужно синхронизировать состояние с другими компонентами
- ✅ Нужен полный контроль над значением

**Uncontrolled Components:**
- ✅ Простые формы
- ✅ Нужна производительность (меньше ре-рендеров)
- ✅ Интеграция с не-React кодом

---

## 🎯 Итоговая памятка

**Основные паттерны React:**

1. ✅ **Compound Components** — гибкая структура компонентов
2. ✅ **Render Props** — делегирование рендеринга через функции
3. ✅ **Инкапсуляция** — скрытие внутренней логики
4. ✅ **Декомпозиция** — разбиение на маленькие компоненты
5. ✅ **Композиция** — объединение компонентов
6. ✅ **Container/Presentational** — разделение логики и представления
7. ✅ **Context API** — глобальное состояние
8. ✅ **Custom Hooks** — переиспользование логики
9. ✅ **Provider Pattern** — обертка для контекста
10. ✅ **Controlled/Uncontrolled** — управление состоянием форм

**Выбор паттерна зависит от:**
- Сложности задачи
- Необходимости переиспользования
- Требований к производительности
- Структуры приложения

---

## 💡 Примеры для собеседования

**Вопрос: "В чем разница между Render Props и Custom Hooks?"**

**Ответ:**
- Render Props — паттерн, где компонент принимает функцию для рендеринга
- Custom Hooks — функции, которые инкапсулируют логику состояния
- Custom Hooks более современный подход и проще в использовании
- Render Props может привести к "callback hell"

**Вопрос: "Когда использовать Context API, а когда Redux?"**

**Ответ:**
- Context API — для редко меняющихся данных (тема, язык, авторизация)
- Redux — для сложного состояния приложения, часто меняющихся данных
- Context API проще, но менее производителен для больших приложений

**Вопрос: "Что такое Compound Components?"**

**Ответ:**
- Паттерн, где компонент состоит из нескольких связанных подкомпонентов
- Позволяет создавать гибкую структуру через композицию
- Пример: `<Card><Card.Header /><Card.Body /></Card>`
