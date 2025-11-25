## 🔄 Наследование: `extends`, `super` и mixins

---

### 🔗 `extends` — наследование классов

**`extends`** позволяет создать класс на основе другого класса, наследуя его свойства и методы.

```js
class Animal {
  constructor(name) {
    this.name = name;
  }
  
  speak() {
    return `${this.name} makes a sound.`;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);      // вызываем конструктор родителя
    this.breed = breed;
  }
  
  speak() {
    return `${this.name} barks.`; // переопределение метода
  }
}

const dog = new Dog('Rex', 'Shepherd');
console.log(dog.speak());        // "Rex barks."
console.log(dog instanceof Animal); // true
```

**Что происходит:**
- `Dog.prototype.__proto__ === Animal.prototype` (прототипная связь)
- `Dog` наследует все методы и свойства `Animal`
- Можно переопределять методы родителя

---

### 🎯 `super` — вызов родительского класса

**`super`** используется для:
1. Вызова конструктора родителя (`super(...)`)
2. Вызова методов родителя (`super.method()`)

#### 1. `super()` в конструкторе

**Обязателен**, если в наследнике есть `constructor`:

```js
class Animal {
  constructor(name) {
    this.name = name;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);      // ОБЯЗАТЕЛЬНО вызвать перед this
    this.breed = breed;
  }
}
```

**Правило:** `super()` должен быть вызван **до использования `this`**.

#### 2. `super.method()` для вызова методов родителя

```js
class Animal {
  speak() {
    return 'makes a sound';
  }
}

class Dog extends Animal {
  speak() {
    return `${super.speak()} and barks`; // вызываем метод родителя
  }
}

const dog = new Dog();
console.log(dog.speak()); // "makes a sound and barks"
```

---

### 🔄 Mixins — множественное наследование

**Mixins** — способ добавить функциональность из нескольких источников, так как JavaScript поддерживает только одиночное наследование.

#### Способ 1: Функции-миксины

```js
// Миксин для логирования
const Logger = {
  log(message) {
    console.log(`[${this.constructor.name}] ${message}`);
  }
};

// Миксин для валидации
const Validator = {
  validate() {
    return this.name && this.name.length > 0;
  }
};

// Применение миксинов
class User {
  constructor(name) {
    this.name = name;
  }
}

// Копируем методы из миксинов
Object.assign(User.prototype, Logger, Validator);

const user = new User('Иван');
user.log('Создан пользователь'); // [User] Создан пользователь
console.log(user.validate());     // true
```

#### Способ 2: Функция-миксин

```js
// Функция для создания миксина
function mixin(target, ...sources) {
  Object.assign(target.prototype, ...sources);
}

// Миксины
const Logger = {
  log(message) {
    console.log(`[LOG] ${message}`);
  }
};

const Timestamp = {
  getTimestamp() {
    return new Date().toISOString();
  }
};

// Применение
class Event {
  constructor(name) {
    this.name = name;
  }
}

mixin(Event, Logger, Timestamp);

const event = new Event('Click');
event.log('Event created');     // [LOG] Event created
console.log(event.getTimestamp()); // 2024-01-01T12:00:00.000Z
```

#### Способ 3: Миксин через класс (современный подход)

```js
// Базовый класс
class Base {
  constructor(name) {
    this.name = name;
  }
}

// Миксины как функции
const LoggerMixin = (BaseClass) => class extends BaseClass {
  log(message) {
    console.log(`[${this.constructor.name}] ${message}`);
  }
};

const TimestampMixin = (BaseClass) => class extends BaseClass {
  getTimestamp() {
    return new Date().toISOString();
  }
};

// Применение миксинов
class Event extends TimestampMixin(LoggerMixin(Base)) {
  constructor(name) {
    super(name);
  }
}

const event = new Event('Click');
event.log('Created');           // [Event] Created
console.log(event.getTimestamp()); // 2024-01-01T12:00:00.000Z
```

---

### 📊 Сравнение наследования и миксинов:

| Параметр | `extends` | Mixins |
|----------|-----------|--------|
| **Количество** | Один родитель | Несколько источников |
| **Связь** | Прототипная цепочка | Копирование методов |
| **Использование** | Для иерархии классов | Для переиспользования функциональности |

---

### 🎯 Итог:

- **`extends`** — наследование от одного класса
- **`super()`** — вызов конструктора родителя (обязателен в `constructor`)
- **`super.method()`** — вызов метода родителя
- **Mixins** — способ добавить функциональность из нескольких источников
- Mixins полезны, когда нужна функциональность из разных классов без создания глубокой иерархии







