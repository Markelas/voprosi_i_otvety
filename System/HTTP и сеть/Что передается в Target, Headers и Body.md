## 📦 Что передаётся в Target, Headers и Body в запросах

---

### 🎯 Target

**Путь и query-параметры** (например, `/users?id=5`)

```http
GET /users?id=5&status=active HTTP/1.1
```

---

### 📋 Headers

В виде **ключ-значение**. Передают служебную информацию:

#### Request Headers:

| Заголовок | Описание |
|-----------|----------|
| **`Host`** | Домен сервера |
| **`User-Agent`** | Информация о клиенте (браузер, ОС) |
| **`Authorization`** | Токены/пароли |
| **`Content-Type`** | Формат данных в body (`application/json`, `multipart/form-data`) |
| **`Accept`** | Что клиент ожидает в ответе (`application/json`, `text/html`) |
| **`Cookie`** | Данные сессии |

#### Response Headers:

| Заголовок | Описание |
|-----------|----------|
| **`Content-Type`** | Формат данных (`application/json`, `text/html`) |
| **`Set-Cookie`** | Устанавливает cookie у клиента |
| **`Cache-Control`** | Как и сколько кэшировать |
| **`Access-Control-Allow-Origin`** | CORS разрешения |

---

### 📄 Body

**Данные запроса/ответа:**

- JSON
- Файлы (multipart/form-data)
- Текст (text/plain)
- XML

**Пример:**

```json
{
  "name": "John",
  "email": "john@example.com"
}
```

---

### 💡 Полный пример запроса:

```http
POST /api/users HTTP/1.1
Host: api.example.com
Content-Type: application/json
Authorization: Bearer token123

{
  "name": "John",
  "email": "john@example.com"
}
```
