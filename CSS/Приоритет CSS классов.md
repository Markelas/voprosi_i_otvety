🎯 **Приоритет CSS селекторов**

🏆 <span class="yellow">**Иерархия приоритетов:**</span>

1. <span>`style=""`</span> - инлайн стили **(1000)**

`<p style="color: red;">Текст</p>`

2. <span>`#id`</span> - идентификатор **(100)**

`#alert { color: blue; }`

3. <span>`.class`</span> - классы **(10)**

`.error { color: orange; }`

4. <span>`tag`</span> - теги **(1)**

`p { color: green; }`

---
<span class="yellow">**💡 Запомни: `!important` > `inline` > `#id` > `.class` > `tag`**</span>