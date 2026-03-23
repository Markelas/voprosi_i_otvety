```js
// Написать функцию, которая возврщает все ноды в порядке следования
// соответсвующие переданному типу
// Глубина любая

function getNodes(tree, needType) {
    const { type, value, children } = tree;
    const result = [];

    if (type === needType) {
        result.push({ type, value })
    }

    if (children && children.length) {
        for (let item of children) {
            result.push(...getNodes(item, needType));
        }
    }

    return result;
}

const tree = {
    type: 'nested',
    children: [
        { type: 'added', value: 42 },
        {
            type: 'nested',
            children: [
                { type: 'added', value: 43 },
                { type: 'added', value: 44 },

            ],
        },
        { type: 'added', value: 45 },
    ],
};


console.log(getNodes(tree, 'added')) 
/* [
    { type: 'added', value: 42 },
    { type: 'added', value: 43 },
    { type: 'added', value: 44 },
    { type: 'added', value: 45 },
    ...
] */
```

Через стек
```js
function getNodes(tree, needType) {
    const stack = [tree];
    const result = [];

    while (stack.length) {
        const { type, value, children } = stack.pop();

        if (type === needType) {
            result.push({type, value})
        }

        if (children && children.length) {
            for (let i = children.length - 1; i >= 0; i--) {
                stack.push(children[i])
            }
        }
        
    }

    return result;
}
```