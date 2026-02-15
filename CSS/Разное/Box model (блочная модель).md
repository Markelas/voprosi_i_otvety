# Box model (блочная модель)

## Краткий ответ для собеседования

**Блочная модель (box model)** в CSS описывает, из чего состоит прямоугольник элемента: изнутри наружу — **content** (содержимое), **padding** (внутренние отступы), **border** (рамка), **margin** (внешний отступ). По умолчанию заданные **width** и **height** относятся только к **content**; padding и border добавляются к ним, и итоговый размер блока = width + padding + border. При **box-sizing: border-box** width и height включают padding и border — итоговый размер блока равен заданному width/height. На собеседовании часто спрашивают: что входит в box model, чем content-box отличается от border-box, зачем border-box.

---

## Что входит в box model

Изнутри наружу:
- **Content** — область содержимого (текст, дочерние элементы). Размер задаётся **width** и **height** (в content-box — именно эта область).
- **Padding** — внутренний отступ между content и border.
- **Border** — рамка.
- **Margin** — внешний отступ между элементом и соседями (не входит в «коробку» элемента для фона и рамки, но влияет на общий размер занимаемого места в потоке).

---

## box-sizing: content-box и border-box

- **content-box** (значение по умолчанию в классической модели): **width** и **height** задают только размер **content**. Padding и border добавляются снаружи. Итоговая ширина элемента = width + padding-left + padding-right + border-left + border-right.
- **border-box**: **width** и **height** задают размер блока **вместе с padding и border**. Content сжимается. Итоговая ширина элемента = заданная width; удобно верстать, не пересчитывая размеры при добавлении padding.

На практике часто для всех элементов задают **box-sizing: border-box** (через универсальный селектор или normalize/reset), чтобы заданный width/height совпадал с визуальным размером блока.

---

## Вопросы на собеседовании

| Вопрос | Краткий ответ |
|--------|----------------|
| Что входит в box model? | Content, padding, border, margin (изнутри наружу). |
| Чем content-box отличается от border-box? | content-box: width/height только для content, padding и border добавляются снаружи. border-box: width/height включают padding и border. |
| Зачем используют border-box? | Чтобы заданная ширина/высота совпадала с итоговым размером блока; не нужно вычитать padding и border из width. |

---

## Кратко запомнить

**Box model** — content, padding, border, margin. **content-box** — width/height только для content. **border-box** — width/height включают padding и border; часто задают глобально для удобной вёрстки.
