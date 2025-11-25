## 📦 Data-* атрибуты

**Data-* атрибуты** — это специальные пользовательские атрибуты, которые позволяют хранить дополнительную информацию внутри HTML-элементов.

Они удобны для передачи данных из разметки в JavaScript через свойство `dataset` без нарушения валидности HTML.

---

### 🎯 Для чего нужны:

- ✅ Хранение дополнительных данных в HTML-элементах
- ✅ Передача данных из HTML в JavaScript
- ✅ Валидный HTML (не нарушают стандарт)
- ✅ Удобный доступ через `dataset`

---

### 📝 Синтаксис:

```html
<div data-user-id="123" data-status="active" data-price="99.99">
  Пользователь
</div>
```

**Правила:**
- Начинаются с `data-`
- После дефиса может быть любое имя (в нижнем регистре, с дефисами)
- Значение — строка

---

### 🔧 Доступ через JavaScript

#### 1. Через `dataset` (рекомендуемый способ)

```js
const element = document.querySelector('div');

// Чтение
console.log(element.dataset.userId);    // "123" (camelCase)
console.log(element.dataset.status);    // "active"
console.log(element.dataset.price);     // "99.99"

// Запись
element.dataset.userId = '456';
element.dataset.newAttribute = 'value'; // автоматически создаст data-new-attribute
```

**Важно:** 
- Дефисы в HTML (`data-user-id`) преобразуются в camelCase в JavaScript (`userId`)
- `data-user-id` → `dataset.userId`
- `data-user-name` → `dataset.userName`

#### 2. Через `getAttribute()` / `setAttribute()`

```js
// Чтение
const userId = element.getAttribute('data-user-id');

// Запись
element.setAttribute('data-user-id', '456');
```

---

### 💡 Примеры использования

#### Пример 1: Хранение ID для обработки событий

```html
<ul>
  <li data-item-id="1">Товар 1</li>
  <li data-item-id="2">Товар 2</li>
  <li data-item-id="3">Товар 3</li>
</ul>

<script>
  document.querySelectorAll('li').forEach(item => {
    item.addEventListener('click', function() {
      const id = this.dataset.itemId;
      console.log('Выбран товар с ID:', id);
    });
  });
</script>
```

#### Пример 2: Хранение конфигурации

```html
<button data-action="delete" data-item-id="42" data-confirm="true">
  Удалить
</button>

<script>
  const button = document.querySelector('button');
  
  button.addEventListener('click', function() {
    const action = this.dataset.action;      // "delete"
    const itemId = this.dataset.itemId;     // "42"
    const needsConfirm = this.dataset.confirm === 'true'; // true
    
    if (needsConfirm && confirm('Удалить?')) {
      deleteItem(itemId);
    }
  });
</script>
```

#### Пример 3: Делегирование событий с data-атрибутами

```html
<div class="container" data-section="products">
  <button data-action="add">Добавить</button>
  <button data-action="edit">Редактировать</button>
  <button data-action="delete">Удалить</button>
</div>

<script>
  document.querySelector('.container').addEventListener('click', function(event) {
    const action = event.target.dataset.action;
    
    if (action) {
      const section = this.dataset.section;
      console.log(`Действие: ${action}, Секция: ${section}`);
      
      switch(action) {
        case 'add':
          addItem(section);
          break;
        case 'edit':
          editItem(section);
          break;
        case 'delete':
          deleteItem(section);
          break;
      }
    }
  });
</script>
```

---

### 🔄 Преобразование имен

| HTML атрибут | JavaScript (dataset) |
|--------------|---------------------|
| `data-user-id` | `dataset.userId` |
| `data-user-name` | `dataset.userName` |
| `data-is-active` | `dataset.isActive` |
| `data-price-usd` | `dataset.priceUsd` |

**Правило:** Дефисы удаляются, следующая буква становится заглавной (camelCase).

---

### ⚠️ Важные моменты

1. **Значения всегда строки:**
```js
element.dataset.count = 42;
console.log(typeof element.dataset.count); // "string", не "number"
```

2. **Преобразование типов:**
```js
// Если нужно число
const count = parseInt(element.dataset.count, 10);
const price = parseFloat(element.dataset.price);

// Если нужно boolean
const isActive = element.dataset.active === 'true';
```

3. **Удаление атрибута:**
```js
delete element.dataset.userId; // удалит data-user-id
// или
element.removeAttribute('data-user-id');
```

---

### 🎯 Когда использовать:

✅ **Подходит для:**
- Хранения ID элементов
- Конфигурации компонентов
- Метаданных для JavaScript
- Интеграции с фреймворками (React, Vue)

❌ **Не подходит для:**
- Визуального стиля (используй CSS)
- Доступности (используй ARIA-атрибуты)
- SEO (используй семантические атрибуты)

---

### 💡 Итог:

- **Data-* атрибуты** — валидный способ хранить данные в HTML
- **`dataset`** — удобный доступ к ним в JavaScript
- Дефисы в HTML преобразуются в camelCase в JavaScript
- Значения всегда строки — нужна конвертация типов при необходимости