# Reflow и Repaint

## Краткий ответ для собеседования

**Reflow (Layout)** — пересчёт позиций и размеров элементов; дорогая операция, вызывается при изменении геометрии (width, height, margin). **Repaint** — перерисовка пикселей элемента без изменения layout; дешевле, вызывается при изменении цвета, фона, visibility. Как избежать: батчить DOM-изменения, использовать `transform`/`opacity` (GPU), `documentFragment`, виртуализацию списков, избегать чтения computed styles в цикле, использовать [[requestAnimationFrame]] для привязки обновлений к кадру и батчинга изменений.

---

## Что такое Reflow и Repaint

### Reflow (Layout, Пересчёт макета)

**Reflow** — процесс пересчёта **позиций и размеров** элементов на странице.

**Когда происходит:**
- Изменение размеров элемента (width, height, padding, margin, border)
- Изменение позиционирования (position, top, left)
- Добавление/удаление элементов DOM
- Изменение контента (текст, изображения)
- Изменение шрифта
- Изменение размера окна (resize)
- Чтение некоторых свойств (offsetWidth, clientHeight — принудительный reflow)

**Цена:**  
**Дорогая операция** — браузер пересчитывает layout всего поддерева или всей страницы.

---

### Repaint (Перерисовка)

**Repaint** — процесс **перерисовки пикселей** элемента без изменения его размеров и позиций.

**Когда происходит:**
- Изменение цвета (`color`, `background-color`)
- Изменение видимости (`visibility`)
- Изменение границ, теней (`box-shadow`, `outline`)

**Цена:**  
**Дешевле Reflow** — не требует пересчёта layout, только перерисовку.

---

## Разница между Reflow и Repaint

| Параметр | Reflow | Repaint |
|----------|--------|---------|
| **Что делает** | Пересчитывает позиции и размеры | Перерисовывает пиксели |
| **Стоимость** | Дорого | Дешевле |
| **Триггеры** | Изменение геометрии (width, height, margin) | Изменение визуальных свойств (color, background) |
| **Влияние** | Может затронуть всю страницу | Только текущий элемент |

**Важно:**  
Reflow **всегда вызывает** Repaint (после пересчёта layout нужно перерисовать), но Repaint **не всегда вызывает** Reflow.

---

## Что вызывает Reflow

### 1. Изменение геометрии элемента

```js
element.style.width = '200px';  // reflow + repaint
element.style.height = '100px'; // reflow + repaint
element.style.padding = '10px'; // reflow + repaint
element.style.margin = '20px';  // reflow + repaint
```

### 2. Добавление/удаление элементов

```js
document.body.appendChild(newElement); // reflow + repaint
document.body.removeChild(element);    // reflow + repaint
```

### 3. Изменение контента

```js
element.textContent = 'New text'; // reflow + repaint (если размер изменился)
element.innerHTML = '<div>...</div>'; // reflow + repaint
```

### 4. Изменение классов с геометрией

```js
element.classList.add('bigger'); // если .bigger меняет width/height → reflow
```

### 5. Чтение computed styles (принудительный reflow)

```js
const width = element.offsetWidth;  // принудительный reflow
const height = element.clientHeight; // принудительный reflow
const rect = element.getBoundingClientRect(); // принудительный reflow
```

**Почему:**  
Браузер откладывает reflow для оптимизации (батчинг), но при чтении этих свойств **вынужден** пересчитать layout немедленно, чтобы вернуть актуальное значение.

### 6. Изменение размера окна, прокрутка

```js
window.addEventListener('resize', () => {
  // reflow на каждый resize
});
```

---

## Что вызывает только Repaint (без Reflow)

```js
element.style.color = 'red';             // только repaint
element.style.backgroundColor = 'blue';  // только repaint
element.style.visibility = 'hidden';     // только repaint
element.style.boxShadow = '0 0 5px #000'; // только repaint
```

---

## Свойства, которые НЕ вызывают Reflow/Repaint

**GPU-ускоренные свойства** (Compositing):

```js
element.style.transform = 'translateX(100px)'; // только compositing
element.style.opacity = '0.5';                  // только compositing
```

**Почему:**  
Эти свойства обрабатываются на **отдельном слое** (compositor layer), GPU работает без пересчёта layout и перерисовки.

---

## Как избежать/минимизировать Reflow и Repaint

### 1. Батчить изменения DOM

**❌ Плохо — каждое изменение вызывает reflow:**

```js
element.style.width = '100px';  // reflow 1
element.style.height = '100px'; // reflow 2
element.style.margin = '10px';  // reflow 3
```

**✅ Хорошо — изменить через класс (один reflow):**

```css
.box {
  width: 100px;
  height: 100px;
  margin: 10px;
}
```

```js
element.classList.add('box'); // reflow 1
```

**Или через `cssText`:**

```js
element.style.cssText = 'width: 100px; height: 100px; margin: 10px;'; // reflow 1
```

---

### 2. Использовать `documentFragment` для массовых изменений

**❌ Плохо:**

```js
for (let i = 0; i < 1000; i++) {
  const div = document.createElement('div');
  document.body.appendChild(div); // reflow на каждую итерацию (1000 reflow)
}
```

**✅ Хорошо:**

```js
const fragment = document.createDocumentFragment();

for (let i = 0; i < 1000; i++) {
  const div = document.createElement('div');
  fragment.appendChild(div); // без reflow
}

document.body.appendChild(fragment); // reflow 1
```

---

### 3. Скрывать элемент перед изменениями

```js
element.style.display = 'none'; // reflow 1

// Множество изменений (без reflow)
element.style.width = '100px';
element.style.height = '100px';
element.textContent = 'Text';

element.style.display = 'block'; // reflow 2
```

---

### 4. Использовать `transform` и `opacity` вместо top/left

**❌ Плохо (reflow):**

```js
element.style.left = '100px'; // reflow + repaint
```

**✅ Хорошо (только compositing, GPU):**

```js
element.style.transform = 'translateX(100px)'; // только compositing
```

---

### 5. Избегать чтения computed styles в цикле

**❌ Плохо — принудительный reflow на каждой итерации:**

```js
for (let i = 0; i < elements.length; i++) {
  const width = elements[i].offsetWidth; // reflow на каждой итерации
  elements[i].style.width = width + 10 + 'px';
}
```

**✅ Хорошо — сначала читаем, потом пишем:**

```js
// Фаза чтения
const widths = elements.map(el => el.offsetWidth); // reflow 1

// Фаза записи
elements.forEach((el, i) => {
  el.style.width = widths[i] + 10 + 'px'; // без лишних reflow
});
```

---

### 6. Использовать виртуализацию для длинных списков

Рендерить только видимые элементы (react-window, react-virtualized) → меньше DOM-узлов → быстрее reflow.

---

### 7. Использовать `will-change` (с осторожностью)

```css
.animated {
  will-change: transform, opacity;
}
```

**Что делает:**  
Подсказывает браузеру создать отдельный слой для элемента → GPU-ускорение.

**Осторожно:**  
Не злоупотреблять — создание слоёв требует памяти.

---

### 8. Debounce для resize/scroll

```js
let timeout;
window.addEventListener('resize', () => {
  clearTimeout(timeout);
  timeout = setTimeout(() => {
    // Обработка resize (один раз вместо сотен)
  }, 300);
});
```

---

### 9. Использовать [[requestAnimationFrame]] (rAF)

**Что делает:**  
`requestAnimationFrame(callback)` вызывает callback **перед следующим кадром** (перед следующим paint). Браузер подстраивает вызов под частоту обновления экрана (обычно 60 Hz).

**Зачем для оптимизации reflow/repaint:**

- **Привязка к кадру** — обновления DOM/стилей попадают в один кадр и выполняются до отрисовки; меньше шансов, что reflow/repaint размажутся по нескольким кадрам и вызовут дёргания (jank).
- **Батчинг изменений** — вместо множества синхронных изменений (каждое может дать свой reflow) можно отложить все изменения в callback rAF; браузер пересчитает layout и перерисует **один раз за кадр**.
- **Разделение чтения и записи** — можно читать offsetWidth/getBoundingClientRect вне rAF, а записи (style.*) делать внутри callback rAF; так уменьшается layout thrashing и принудительные reflow.
- **Анимации** — для движения и изменения стилей лучше использовать rAF, а не setInterval/setTimeout: rAF вызывается ровно перед paint, обновления и repaint попадают в один кадр → плавная картинка.
- **Пауза в фоне** — когда вкладка скрыта, браузер обычно не вызывает callback rAF → меньше лишних перерасчётов и перерисовок.

**Итог:** rAF не отменяет reflow/repaint, но снижает их количество и привязывает к циклу отрисовки — это и есть оптимизация. Подробнее: [[requestAnimationFrame]].

---

## Инструменты для анализа

### Chrome DevTools — Performance

1. Открыть DevTools → Performance
2. Записать сессию (Record)
3. Посмотреть **Layout** (Reflow) и **Paint** (Repaint)
4. Найти длинные Layout/Paint — оптимизировать

### Rendering Panel

DevTools → More tools → Rendering → включить:
- **Paint flashing** — подсвечивает repaint
- **Layout Shift Regions** — показывает layout shifts

---

## Вопросы на собеседовании

| Вопрос | Краткий ответ |
|--------|----------------|
| Что такое Reflow? | Пересчёт позиций и размеров элементов; дорого |
| Что такое Repaint? | Перерисовка пикселей без изменения layout; дешевле |
| Что дороже — Reflow или Repaint? | Reflow (требует пересчёта layout) |
| Что вызывает Reflow? | Изменение геометрии (width, height), добавление/удаление элементов, чтение offsetWidth |
| Что вызывает только Repaint? | Изменение color, background, visibility |
| Почему чтение offsetWidth вызывает Reflow? | Браузер вынужден пересчитать layout, чтобы вернуть актуальное значение |
| Как избежать Reflow? | Батчить изменения, использовать `transform`/`opacity`, `documentFragment`, избегать чтения в цикле, [[requestAnimationFrame]] |
| Как rAF помогает при оптимизации reflow/repaint? | Привязывает обновления к кадру, батчит изменения в один кадр, разделяет чтение и запись, для анимаций плавнее setInterval/setTimeout. Подробнее: [[requestAnimationFrame]] |
| Какие свойства не вызывают Reflow/Repaint? | `transform`, `opacity` (GPU-ускорение, compositing) |

---

## Кратко для ответа

**Reflow** — пересчёт layout (позиции, размеры), дорого, вызывается при изменении геометрии (width, height, margin), добавлении элементов, чтении offsetWidth. **Repaint** — перерисовка пикселей, дешевле, вызывается при изменении color, background. Reflow всегда вызывает Repaint. Как избежать: батчить DOM-изменения (классы, cssText), использовать `transform`/`opacity` (GPU), `documentFragment`, избегать чтения computed styles в цикле, использовать [[requestAnimationFrame]] для привязки обновлений к кадру и батчинга изменений.
