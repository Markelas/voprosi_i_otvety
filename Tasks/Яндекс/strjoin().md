
```js
/**
Необходимо написать функцию strjoin,
которая склеивает строки через разделитель.*/

function strjoin() {
    const separator = arguments[0];
    const strings = Array.from(arguments).slice(1);
    return strings.join(separator);
}

console.log(strjoin('.', 'a', 'b', 'c')) // a.b.c
console.log(strjoin('-', 'a', 'b', 'c', 'd', 'e', 'f')) // a-b-c-d-e-f

```

```js
function strjoin(separator, ...strings) {
    return strings.join(separator);
}

console.log(strjoin('.', 'a', 'b', 'c')) // a.b.c
console.log(strjoin('-', 'a', 'b', 'c', 'd', 'e', 'f')) // a-b-c-d-e-f
```