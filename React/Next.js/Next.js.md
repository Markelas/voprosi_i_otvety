# Next.js

## Краткий ответ для собеседования

**Next.js** — фреймворк для React с встроенным SSR, SSG, ISR, роутингом, API routes / Route Handlers, Server Actions. Главные отличия от обычного React: серверный рендеринг из коробки, файловый роутинг, автоматический code splitting, оптимизация изображений и шрифтов. Среды: **клиент (браузер)** и **сервер**; на сервере есть runtime **Node.js** и **Edge**. Кэширование многоуровневое: Request Memoization, Data Cache, Full Route Cache, Router Cache. SSR нужен для первой загрузки (SEO, FCP), дальше часто работает как SPA.

---

## Что такое Next.js

**Next.js** — React-фреймворк от Vercel для создания **Production-ready** приложений с:
- **SSR, SSG, ISR** — разные стратегии рендеринга
- **Роутинг** — файловый роутинг (file-based routing)
- **API Routes / Route Handlers / Server Actions** — серверная часть рядом с фронтом
- **Оптимизации** — code splitting, изображения, шрифты, streaming, кэш
- **TypeScript** — поддержка из коробки
- **Runtime** — Client, Node.js, Edge

---

## Отличия от обычного React (CRA, Vite)

| Параметр | React (CRA/Vite) | Next.js |
|----------|------------------|---------|
| **Рендеринг** | Только CSR | SSR, SSG, ISR, CSR |
| **Роутинг** | Нужна библиотека (React Router) | Встроенный файловый роутинг |
| **SEO** | Плохое (пустой HTML) | Отличное (готовый HTML) |
| **Code Splitting** | Вручную (`React.lazy`) | Автоматически (по роутам) |
| **API** | Нужен отдельный бэкенд | API Routes (бэкенд в Next.js) |
| **Оптимизация** | Вручную | Автоматически (изображения, шрифты) |
| **Конфигурация** | Больше настроек | Convention over configuration |

---

## Среды и runtime в Next.js

На собеседовании здесь часто путают два разных деления.

### 1. Клиент vs сервер

| Среда | Где выполняется | Что там живёт |
|-------|------------------|---------------|
| **Клиент (браузер)** | У пользователя | Client Components, обработчики событий, browser API |
| **Сервер** | На бэкенде Next.js | Server Components, Route Handlers, Server Actions, SSR/SSG |

Это про **где крутится React-код**.

### 2. Серверные runtime: Node.js и Edge

Внутри серверной части Next.js есть два runtime:

| Runtime | Что это | Когда использовать |
|---------|---------|--------------------|
| **Node.js** | Полноценный Node-сервер | БД, тяжёлая логика, много Node API, большинство страниц и API |
| **Edge** | Облегчённый runtime ближе к пользователю | Middleware, быстрые лёгкие ответы, geo/A-B, auth-checks на входе |

#### Node.js runtime

- полный доступ к экосистеме Node
- удобен для работы с БД, файлами, тяжёлыми библиотеками
- обычно используется по умолчанию для Server Components, Route Handlers, Server Actions
- мощнее, но cold start может быть тяжелее, чем у Edge

#### Edge runtime

- быстрее поднять и выполнить лёгкий код
- работает ближе к пользователю
- ограниченнее: не весь Node API доступен
- идеален для **Middleware** и простых проверок на входе запроса
- плохой выбор для тяжёлой бизнес-логики и сложных драйверов БД

### Как запомнить

```text
Next.js
 ├── Client runtime  → браузер
 └── Server
      ├── Node.js runtime  → основной серверный код
      └── Edge runtime     → быстрый лёгкий слой на краю сети
```

**На собеседовании:**  
«В Next.js есть клиент и сервер. На сервере — два runtime: Node.js для основной логики и Edge для быстрых лёгких задач вроде middleware.»

Код, написанный для сервера, **не должен попадать** в клиентский бандл с секретами и Node-only API.

---

## Как работает Next.js

### 1. Первая загрузка (SSR)

1. **Пользователь заходит на `/about`**
2. **Next.js рендерит страницу на сервере** (SSR или SSG)
3. **Отправляет HTML** — пользователь видит контент сразу (FCP)
4. **Загружается JS** — браузер загружает React-код
5. **Гидрация** — React "оживляет" HTML, добавляет обработчики

**Результат:** быстрая первая загрузка + SEO.

### 2. Последующая навигация (CSR)

После гидрации Next.js работает как **SPA**:
- Переходы между страницами **без перезагрузки**
- Данные подгружаются через API (fetch)
- Плавная навигация, как в обычном React

**Вывод:**  
SSR нужен только для **первой загрузки**, дальше — обычная SPA.

---

## Роутинг в Next.js

### Файловый роутинг (File-based Routing)

**Структура папок = структура URL:**

```
app/
  page.tsx         → /
  about/
    page.tsx       → /about
  blog/
    [id]/
      page.tsx     → /blog/123 (динамический)
```

**Преимущества:**
- Не нужен React Router
- Автоматический code splitting по роутам
- Понятная структура

### Динамические роуты

```tsx
// app/blog/[id]/page.tsx
export default function BlogPost({ params }: { params: { id: string } }) {
  return <div>Post ID: {params.id}</div>;
}
```

URL `/blog/123` → `params.id = "123"`.

---

## Уровни кэширования в Next.js

Next.js кэширует **на нескольких уровнях**. Это один из самых частых вопросов по App Router.

### 4 основных уровня

| Уровень | Где | Что кэширует | Живёт |
|---------|-----|--------------|-------|
| **1. Request Memoization** | Сервер | Одинаковые `fetch` внутри **одного** рендера запроса | Только на время текущего request |
| **2. Data Cache** | Сервер | Результаты `fetch` / данных между запросами | Между запросами пользователей |
| **3. Full Route Cache** | Сервер | Готовый HTML/RSC payload страницы (static) | Между запросами |
| **4. Router Cache** | Клиент | Посещённые/префетчённые сегменты роутов в памяти браузера | В сессии пользователя |

### Как запомнить

```text
Запрос страницы
   │
   ├─ Request Memoization   → не ходи в API дважды в одном рендере
   ├─ Data Cache            → можно ли взять уже сохранённые данные
   ├─ Full Route Cache      → можно ли отдать уже готовую страницу
   └─ Router Cache          → на клиенте уже есть этот роут?
```

---

### 1. Request Memoization

**Зачем:** если в одном серверном рендере несколько компонентов делают одинаковый `fetch`, Next.js выполнит его **один раз**.

Это не долговременный кэш между пользователями.  
Это дедупликация **внутри одного request**.

---

### 2. Data Cache

**Зачем:** сохранить результат data fetching между запросами.

По умолчанию в App Router `fetch` часто кэшируется агрессивно.

Важные режимы:

- `force-cache` — кэшировать
- `no-store` — не кэшировать, всегда свежие данные
- `revalidate: N` — ISR-подобное обновление через N секунд
- `tags` — пометить данные тегом для точечной инвалидации

---

### 3. Full Route Cache

**Зачем:** кэшировать уже отрендеренный результат роута.

- статическая страница может отдаваться из кэша целиком
- динамическая страница (cookies, headers, no-store и т.д.) обычно не попадает в Full Route Cache

На собеседовании:

> Full Route Cache — это кэш целой страницы/сегмента на сервере. Data Cache — кэш данных, из которых страница собирается.

---

### 4. Router Cache

**Зачем:** ускорить клиентскую навигацию.

После гидрации Next.js работает как SPA и может держать в памяти уже загруженные RSC payload / сегменты роутов.

Поэтому повторный переход может быть очень быстрым, даже без нового полного document request.

---

### Инвалидация кэшей

| Способ | Что делает |
|--------|------------|
| `revalidatePath('/blog')` | Сбрасывает кэш конкретного пути |
| `revalidateTag('posts')` | Сбрасывает данные с тегом |
| `revalidate = 60` | Time-based обновление |
| `cache: 'no-store'` | Вообще не класть в Data Cache |
| Пересборка / очистка `.next` | Жёсткий полный сброс |

### Практика

**В разработке:**

- перезапуск dev-сервера
- очистка `.next`

**В проде:**

- `revalidatePath` / `revalidateTag` после мутаций
- правильные стратегии cache/revalidate для данных

### Что сказать на собеседовании

> В Next.js четыре уровня кэша: Request Memoization внутри одного рендера, Data Cache для данных, Full Route Cache для готовых статических страниц и Router Cache на клиенте для быстрой навигации. Инвалидация — через revalidatePath, revalidateTag и настройки fetch.

---

## SSR в Next.js: зачем и как работает

### Зачем нужен SSR

1. **SEO** — поисковики получают готовый HTML с контентом
2. **Быстрая первая загрузка (FCP)** — пользователь видит контент сразу
3. **Работа без JS** — контент виден, даже если JS не загрузился

### Как работает после первой загрузки

**SSR только для первой страницы**, дальше Next.js работает как **SPA**:
- Переходы между страницами — через client-side routing (без перезагрузки)
- Данные подгружаются через fetch/API
- React управляет DOM

**Пример:**
1. Пользователь заходит на `/` — **SSR**, получает HTML
2. Кликает на `/about` — **CSR**, данные загружаются через fetch, переход мгновенный

---

## App Router vs Pages Router

Next.js 13+ добавил **App Router** — новый способ организации приложения. Старый способ называется **Pages Router**. Оба до сих пор работают, и на собеседованиях часто спрашивают, чем они отличаются и когда какой использовать.

### Краткий ответ для собеседования

**Pages Router** — старая модель: папка `pages/`, все компоненты по сути клиентские, данные через `getServerSideProps` / `getStaticProps` / `getInitialProps`, layouts делаются вручную.  
**App Router** — новая модель: папка `app/`, по умолчанию **Server Components**, встроенные `layout.tsx`, `loading.tsx`, `error.tsx`, data fetching прямо в `async`-компонентах, поддержка Streaming и React Server Components.  
Новые проекты обычно делают на **App Router**, но в легаси и больших миграциях Pages Router всё ещё встречается.

---

### Структура папок

#### Pages Router

```text
pages/
  index.tsx          → /
  about.tsx          → /about
  blog/
    [id].tsx         → /blog/123
  api/
    users.ts         → /api/users
  _app.tsx           → обёртка всего приложения
  _document.tsx      → HTML-документ
```

#### App Router

```text
app/
  layout.tsx         → корневой layout
  page.tsx           → /
  about/
    page.tsx         → /about
  blog/
    [id]/
      page.tsx       → /blog/123
  api/
    users/
      route.ts       → /api/users
```

**Разница:**

- в Pages Router файл почти сразу становится роутом
- в App Router роут создаёт папка, а внутри неё специальные файлы: `page`, `layout`, `loading`, `error`, `not-found`, `route`

---

### Сравнительная таблица

| Параметр | Pages Router | App Router |
|----------|--------------|------------|
| **Папка** | `pages/` | `app/` |
| **Компоненты по умолчанию** | Клиентские | Серверные (RSC) |
| **Layouts** | Через `_app.tsx` / HOC / вручную | Встроенные `layout.tsx`, вложенные |
| **Loading UI** | Вручную | `loading.tsx` + Suspense |
| **Error UI** | Error Boundary вручную | `error.tsx` |
| **Data Fetching** | `getServerSideProps`, `getStaticProps`, `getInitialProps` | `async` Server Components, `fetch`, Server Actions |
| **API** | `pages/api/*.ts` | `app/api/*/route.ts` (Route Handlers) |
| **Streaming** | Нет из коробки | Да |
| **React Server Components** | Нет | Да |
| **Metadata / SEO** | `next/head`, `Head` | `metadata` / `generateMetadata` |
| **Рекомендация** | Легаси, миграция | Новые проекты |

---

### Главные концептуальные отличия

#### 1. Где выполняется код

- **Pages Router:** страница почти всегда клиентский React-компонент. Серверная работа делается через специальные функции data fetching.
- **App Router:** компоненты по умолчанию **Server Components**. Они выполняются на сервере и не попадают в клиентский бандл, пока явно не поставить `'use client'`.

Это самое важное отличие на собеседовании.

#### 2. Data Fetching

**Pages Router:**

- `getServerSideProps` — SSR на каждый запрос
- `getStaticProps` — SSG при сборке
- `getStaticPaths` — для динамических SSG-роутов
- `getInitialProps` — старый универсальный способ

**App Router:**

- data fetching прямо в `async` Server Component
- кэширование и revalidate через `fetch` / `revalidatePath` / `revalidateTag`
- мутации через **Server Actions**
- отдельные функции data fetching больше не нужны как основной API

#### 3. Layouts

**Pages Router:**

- общий layout обычно через `_app.tsx`
- вложенные layouts делать неудобно: HOC, обёртки, условная логика

**App Router:**

- `layout.tsx` на любом уровне вложенности
- layouts сохраняются между переходами
- это одна из самых сильных причин переходить на App Router

#### 4. Loading и Error состояния

**Pages Router:**

- loading и error обычно делают вручную внутри компонентов

**App Router:**

- `loading.tsx` — UI загрузки для сегмента роута
- `error.tsx` — error boundary для сегмента
- `not-found.tsx` — 404 для сегмента

То есть роутинг и UI-состояния связаны с файловой структурой сильнее.

#### 5. API Routes vs Route Handlers

**Pages Router:**

- `pages/api/users.ts`

**App Router:**

- `app/api/users/route.ts`
- экспортируются HTTP-методы: `GET`, `POST`, `PUT`, `DELETE`

Идея та же — backend внутри Next.js, но API немного другой.

---

### Как запомнить разницу

```text
Pages Router
  pages/ + getServerSideProps/getStaticProps
  всё ближе к классическому React SPA + SSR-хуки

App Router
  app/ + Server Components + layouts + streaming
  серверная модель React "из коробки"
```

Или ещё короче:

- **Pages Router** = старый Next.js
- **App Router** = Next.js на React Server Components

---

### Можно ли использовать оба сразу?

**Да.** В одном проекте могут сосуществовать `pages/` и `app/`.

Это удобно для миграции:

1. оставляем старые страницы в Pages Router
2. новые фичи пишем в App Router
3. постепенно переносим роуты

Но важно помнить:

- приоритет роутов и конфликты URL нужно контролировать
- не стоит бесконечно жить в гибридном состоянии без плана миграции

---

### Когда что использовать

#### App Router

- новый проект
- нужны Server Components
- нужны вложенные layouts
- важны streaming, loading/error на уровне роутов
- команда готова к новой модели React

#### Pages Router

- большой легаси-проект
- уже много кода на `getServerSideProps` / `getStaticProps`
- нет времени на миграцию
- команда пока не готова к RSC-модели

---

### Что чаще спрашивают на собеседовании

| Вопрос | Краткий ответ |
|--------|----------------|
| Чем App Router отличается от Pages Router? | App Router использует `app/`, Server Components, встроенные layouts/loading/error и новый data fetching. Pages Router — `pages/` и `getServerSideProps` / `getStaticProps`. |
| Что по умолчанию в App Router? | Server Components. |
| Как получать данные в App Router? | Через `async` Server Components и `fetch`, а не через `getServerSideProps`. |
| Зачем переходить на App Router? | Лучшие layouts, меньше клиентского JS, streaming, современная RSC-модель. |
| Можно ли жить на Pages Router? | Да, особенно в легаси, но для новых проектов рекомендуют App Router. |
| Можно ли смешивать оба? | Да, для постепенной миграции. |

---

### Кратко запомнить

**Pages Router** — старый Next.js с `pages/` и специальными функциями data fetching.  
**App Router** — новый Next.js с `app/`, Server Components, layouts и streaming.  
Главная мысль: App Router — это не просто другая папка, а **другая модель мышления**: сервер по умолчанию, клиент — только там, где нужна интерактивность.

---

## Server Components и Client Components

### Server Components (по умолчанию)

**Где выполняются:** только на сервере.

**Преимущества:**
- Нет в клиентском бандле → меньше JS
- Доступ к серверным API (БД, файловая система)
- Безопасность (секреты не утекают)

**Ограничения:**
- Нельзя использовать `useState`, `useEffect`, обработчики событий

### Client Components (`'use client'`)

**Где выполняются:** на сервере (SSR) + клиенте (CSR).

```tsx
'use client'; // директива вверху файла

import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

**Когда использовать:**
- Интерактивность (`useState`, `onClick`)
- Браузерные API (`localStorage`, `window`)
- Хуки React (`useEffect`, `useContext`)

---

## Performance: какие оптимизации есть в Next.js

На собеседовании обычно ждут не один пункт, а набор.

### 1. Рендеринг и данные

- **SSR / SSG / ISR** — быстрый первый HTML там, где нужно
- **Server Components** — меньше JS на клиенте
- **Streaming + Suspense** — страница начинает отдаваться раньше
- **Partial Prerendering (PPR)** — идея смешивать статику и динамику на одной странице
- правильное **кэширование** и revalidation

### 2. JS и бандл

- **automatic code splitting** по роутам
- меньше `'use client'` — меньше клиентского JS
- динамический импорт тяжёлых клиентских кусков
- не тащить большие библиотеки на клиент без нужды

### 3. Картинки и медиа

`next/image`:

- ресайз под нужный размер
- lazy loading
- современные форматы (WebP/AVIF)
- приоритет для LCP-картинок (`priority`)

### 4. Шрифты

`next/font`:

- оптимизация загрузки шрифтов
- меньше layout shift
- меньше FOIT/FOUT

### 5. Навигация и загрузка

- **prefetch** ссылок
- **Router Cache** для быстрых переходов
- `loading.tsx` для мгновенного feedback
- не блокировать весь экран, если можно стримить куски

### 6. Сеть и edge

- статику отдавать с CDN
- **Edge Middleware** для быстрых проверок на входе
- не делать лишних waterfall-запросов на сервере

### 7. Практический чеклист на собеседовании

Если спрашивают «как ускорить Next.js приложение»:

1. Уменьшить клиентский JS
2. Правильно выбрать SSR/SSG/ISR
3. Настроить кэш и revalidate
4. Оптимизировать images/fonts
5. Использовать streaming / Suspense
6. Убрать лишние client waterfalls
7. Prefetch + хорошая навигация
8. Следить за LCP / CLS / INP

### Что сказать коротко

> Performance в Next.js — это комбинация: меньше клиентского JS через Server Components, правильная стратегия рендеринга и кэша, оптимизация images/fonts, streaming и быстрая клиентская навигация.

---

## Server Actions

### Что это

**Server Actions** — серверные функции, которые можно вызывать прямо из UI (например, из формы или кнопки), а выполняются они **на сервере**.

Это способ делать **мутации** без отдельного ручного API endpoint для каждой формы.

### Зачем нужны

- отправка форм
- создание / обновление / удаление данных
- login / logout
- любые действия, которые должны выполняться на сервере с секретами и доступом к БД

### Чем отличаются от обычного API

| | Route Handler / API Route | Server Action |
|--|---------------------------|---------------|
| Модель | HTTP endpoint | Серверная функция, привязанная к UI/действию |
| Удобство для форм | Нужно руками собирать fetch | Очень удобно для forms/mutations |
| Где живёт | Отдельный route | Рядом с серверным кодом приложения |
| Типичный use case | Публичное/общее API, webhooks | Мутации из интерфейса приложения |

### Важные свойства

- выполняются **на сервере**
- секреты и БД остаются на сервере
- хорошо стыкуются с `revalidatePath` / `revalidateTag`
- можно вызывать из Server Components и Client Components
- уменьшают количество «ручного» glue-кода вокруг fetch + API

### Когда использовать

- формы и мутации внутри Next-приложения
- когда действие логически принадлежит UI-сценарию
- когда хотите сразу после мутации обновить кэш/страницу

### Когда лучше обычный Route Handler

- нужно внешнее API для других клиентов
- webhooks
- сложный REST/JSON API контракт
- действие не привязано к конкретному UI

### Что сказать на собеседовании

> Server Actions — это серверные функции для мутаций, которые вызываются из UI и выполняются на сервере. Они удобны для форм и изменений данных, позволяют не писать отдельный API на каждый UI-сценарий и хорошо работают вместе с revalidation кэша.

---

## Вопросы на собеседовании

| Вопрос | Краткий ответ |
|--------|----------------|
| Что такое Next.js? | React-фреймворк с SSR, SSG, ISR, файловым роутингом, оптимизациями |
| В чём отличие от обычного React? | SSR из коробки, файловый роутинг, автоматический code splitting, API routes |
| Какие runtime есть в Next.js? | Клиент (браузер) и сервер; на сервере — Node.js и Edge |
| Чем Node runtime отличается от Edge? | Node — полный серверный runtime для основной логики; Edge — быстрый лёгкий runtime для middleware и простых проверок |
| Как работает SSR в Next.js? | Первая загрузка — SSR (HTML с сервера), дальше — SPA (CSR) |
| Зачем SSR, если дальше SPA? | Для SEO и быстрой первой загрузки (FCP) |
| Что такое гидрация? | "Оживление" HTML в браузере — React добавляет обработчики событий |
| Какие уровни кэша в Next.js? | Request Memoization, Data Cache, Full Route Cache, Router Cache |
| Чем Data Cache отличается от Full Route Cache? | Data Cache — данные; Full Route Cache — уже готовая страница/сегмент |
| Как инвалидировать кэш? | `revalidatePath`, `revalidateTag`, time-based revalidate, `no-store` |
| Что такое Server Components? | Компоненты, которые выполняются только на сервере, не попадают в бандл |
| Что такое Client Components? | Компоненты с `'use client'`, выполняются на сервере (SSR) + клиенте (CSR) |
| Что такое Server Actions? | Серверные функции для мутаций, вызываемые из UI; выполняются на сервере |
| Чем Server Actions отличаются от API Route? | Actions удобны для UI-мутаций/форм; API Route — для общего HTTP API и внешних клиентов |
| Как ускорить Next.js приложение? | Меньше client JS, правильный рендеринг/кэш, images/fonts, streaming, prefetch, меньше waterfalls |
| Чем App Router отличается от Pages Router? | App Router: `app/`, Server Components, layouts, streaming. Pages Router: `pages/`, `getServerSideProps` / `getStaticProps` |
| Что по умолчанию в App Router? | Server Components; клиент нужен только с `'use client'` |
| Можно ли смешивать App и Pages Router? | Да, для постепенной миграции |

---

## Кратко для ответа

Next.js — React-фреймворк с SSR, SSG, ISR, файловым роутингом, Route Handlers и Server Actions. Среды: клиент и сервер; на сервере runtime **Node.js** и **Edge**. Кэш: **Request Memoization**, **Data Cache**, **Full Route Cache**, **Router Cache**. Performance: меньше client JS, правильный рендеринг/кэш, `next/image`, `next/font`, streaming, prefetch. **Server Actions** — серверные мутации из UI. **Pages Router** — старая модель; **App Router** — Server Components, layouts, streaming.
