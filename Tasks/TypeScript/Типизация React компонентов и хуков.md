## ⚛️ Типизация React компонентов и хуков

---

### **Задача: Полная типизация React приложения**

**Условие:**
Допишите типы для всех компонентов, хуков и функций в следующем коде.

**Начальный код:**
```tsx
import { useState, useEffect } from 'react';

// Задача 1: Допиши типы для User и Post
interface User {
  // TODO: id (string), name (string), email (string)
}

interface Post {
  // TODO: id (number), title (string), body (string), userId (number)
}

// Задача 2: Исправь useFetch (добавь дженерик и типы)
export const useFetch = (url) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  useEffect(() => {
    setLoading(true);
    fetch(url)
      .then((res) => res.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [url]);

  return { data, loading, error };
};

// Задача 3: Сделай List универсальным (дженерик + типизация пропсов)
const List = ({ items, renderItem }) => {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{renderItem(item)}</li>
      ))}
    </ul>
  );
};

// Задача 4: Типизируй пропсы компонентов
const UserProfile = ({ user }) => {
  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  );
};

const PostDetail = ({ post }) => {
  return (
    <div>
      <h2>{post.title}</h2>
      <p>{post.body}</p>
    </div>
  );
};

// Задача 5: Типизируй хук формы (дженерик + типы событий)
const useForm = (initialValues) => {
  const [values, setValues] = useState(initialValues);

  const handleChange = (e) => {
    const { name, value } = e.target;
    setValues({ ...values, [name]: value });
  };

  return { values, handleChange };
};

// Задача 6: Типизируй API-ответы
const fetchUsers = async (): Promise</* TODO */> => {
  const response = await fetch('https://jsonplaceholder.typicode.com/users');
  return await response.json();
};

const fetchPosts = async (): Promise</* TODO */> => {
  const response = await fetch('https://jsonplaceholder.typicode.com/posts');
  return await response.json();
};

export const App = () => {
  // Задача 7: Укажи типы для useFetch
  const { data: users, loading: usersLoading, error: usersError } = useFetch(
    'https://jsonplaceholder.typicode.com/users'
  );
  const { data: posts, loading: postsLoading, error: postsError } = useFetch(
    'https://jsonplaceholder.typicode.com/posts'
  );

  const { values, handleChange } = useForm({ search: '' });

  if (usersLoading || postsLoading) return <div>Loading...</div>;
  if (usersError || postsError) return <div>Error!</div>;

  const filteredUsers = users.filter((user) =>
    user.name.toLowerCase().includes(values.search.toLowerCase())
  );

  return (
    <div>
      <input
        type="text"
        name="search"
        value={values.search}
        onChange={handleChange}
        placeholder="Search users..."
      />

      <h2>Users</h2>
      <List
        items={filteredUsers}
        renderItem={(user) => <UserProfile user={user} />}
      />

      <h2>Posts</h2>
      <List
        items={posts}
        renderItem={(post) => <PostDetail post={post} />}
      />
    </div>
  );
};
```

**Решение:**

```tsx
import { useState, useEffect } from 'react';

// Задача 1: Типы для User и Post
interface User {
  id: number;
  name: string;
  email: string;
}

interface Post {
  id: number;
  title: string;
  body: string;
  userId: number;
}

// Задача 2: useFetch с дженериком
export const useFetch = <T,>(url: string) => {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState<boolean>(false);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    setLoading(true);
    fetch(url)
      .then((res) => res.json())
      .then((data: T) => setData(data))
      .catch((err: Error) => setError(err))
      .finally(() => setLoading(false));
  }, [url]);

  return { data, loading, error };
};

// Задача 3: Универсальный List
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
}

const List = <T,>({ items, renderItem }: ListProps<T>) => {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{renderItem(item)}</li>
      ))}
    </ul>
  );
};

// Задача 4: Типизация пропсов компонентов
interface UserProfileProps {
  user: User;
}

const UserProfile = ({ user }: UserProfileProps) => {
  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  );
};

interface PostDetailProps {
  post: Post;
}

const PostDetail = ({ post }: PostDetailProps) => {
  return (
    <div>
      <h2>{post.title}</h2>
      <p>{post.body}</p>
    </div>
  );
};

// Задача 5: Типизация хука формы
function useForm<T extends Record<string, string>>(initialValues: T) {
  const [values, setValues] = useState<T>(initialValues);

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setValues({ ...values, [name]: value });
  };

  return { values, handleChange };
}

// Задача 6: Типизация API-ответов
const fetchUsers = async (): Promise<User[]> => {
  const response = await fetch('https://jsonplaceholder.typicode.com/users');
  return await response.json();
};

const fetchPosts = async (): Promise<Post[]> => {
  const response = await fetch('https://jsonplaceholder.typicode.com/posts');
  return await response.json();
};

export const App = () => {
  // Задача 7: Типизация useFetch
  const { data: users, loading: usersLoading, error: usersError } = useFetch<User[]>(
    'https://jsonplaceholder.typicode.com/users'
  );
  const { data: posts, loading: postsLoading, error: postsError } = useFetch<Post[]>(
    'https://jsonplaceholder.typicode.com/posts'
  );

  const { values, handleChange } = useForm({ search: '' });

  if (usersLoading || postsLoading) return <div>Loading...</div>;
  if (usersError || postsError) return <div>Error!</div>;
  if (!users || !posts) return <div>No data</div>;

  const filteredUsers = users.filter((user) =>
    user.name.toLowerCase().includes(values.search.toLowerCase())
  );

  return (
    <div>
      <input
        type="text"
        name="search"
        value={values.search}
        onChange={handleChange}
        placeholder="Search users..."
      />

      <h2>Users</h2>
      <List
        items={filteredUsers}
        renderItem={(user) => <UserProfile user={user} />}
      />

      <h2>Posts</h2>
      <List
        items={posts}
        renderItem={(post) => <PostDetail post={post} />}
      />
    </div>
  );
};
```

**Ключевые моменты решения:**

1. **Дженерик в useFetch**: `<T,>` позволяет указать тип данных при вызове
2. **Типизация событий**: `React.ChangeEvent<HTMLInputElement>` для input
3. **Generic компонент List**: `<T,>` делает компонент универсальным
4. **Типизация пропсов**: отдельные интерфейсы для каждого компонента
5. **Проверка на null**: добавлены проверки перед использованием данных


