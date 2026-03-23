```js
/*
В прототипе рекламной сети продажа рекламных мест устроена следующим образом: 
покупатели заранее называют свою цену, а на каждое рекламное место отвечают, 
готовы они его купить или нет.

Необходимо реализовать функцию, которая перед продажей рекламного места
будет ожидать согласия или отказа от покупателей с высокой ценой ставки,
а затем продаст рекламное место покупателю с самой высокой ценой
среди тех, кто согласился на покупку.

Вернуть ответ из функции нужно настолько быстро, насколько это возможно.
Нужно вернуть индекс покупателя.

type Buyer = {
    price: number,           // цена, которую предлагает покупатель
    accepts: () => Promise<boolean>, // функция, возвращающая Promise с ответом
}

async function bestBuyer(buyers: Buyer[]): Promise<number>

Примеры:

1) Покупатели предлагают цену 1, 5, 10
   - Покупатель с предложением 10 ответил отказом
   - Покупатель с предложением 1 ответил согласием // Всё ещё ждём, 
     поскольку может ответить покупатель с ценой 5
   - Покупатель с предложением 5 ответил согласием // Выбираем покупателя 
     с предложением 5 (индекс 2)

2) Покупатели предлагают цену 1, 5, 10
   - Покупатель с предложением 10 ответил отказом
   - Покупатель с предложением 5 ответил согласием // Не ждём ответа 
     от покупателя с предложением 1 - его точно не выберем

3) Покупатели предлагают цену 1, 2
   - Оба ответили отказом // Никого не выбираем, возвращаем -1

Требования:
- Опрашивать покупателей нужно в порядке убывания цены (сначала самых дорогих)
- Не ждать ответа от покупателей, которые заведомо не могут быть выбраны
- Возвращать индекс покупателя с максимальной ценой среди согласившихся
- Если никто не согласился, вернуть -1
*/

async function BestBuyer(buyers) {
	if (await buyer[0].accept()) {
		return 0;
	}
	return -1;
}
```

Решение:

```js
async function bestBuyer(buyers) {
    // Сортируем покупателей по убыванию цены, но сохраняем оригинальные индексы
    const sorted = buyers
        .map((buyer, index) => ({ ...buyer, originalIndex: index }))
        .sort((a, b) => b.price - a.price);
    
    // Опрашиваем покупателей по очереди (от самой высокой цены к низкой)
    for (const buyer of sorted) {
        const agreed = await buyer.accepts();
        
        if (agreed) {
            // Если согласился - это лучший вариант (он самый дорогой из оставшихся)
            return buyer.originalIndex;
        }
        // Если отказался - переходим к следующему по цене
    }
    
    // Никто не согласился
    return -1;
}
```

Тест кейсы

```js
console.clear();

// Вспомогательная функция для создания асинхронных ответов
const asyncResponse = (ms, response) => () =>
    new Promise(resolve => setTimeout(() => resolve(response), ms));

// Функция для запуска тестов
const testcase = (label, expectedResult, timeout, fn) => {
    const start = Date.now();
    fn().then(result => {
        const duration = Date.now() - start;
        const testResult = `[${duration}ms] ${label}: ${result}`;

        if (duration > timeout) {
            return console.error('❌ ' + testResult + ` (превышен таймаут ${timeout}ms)`);
        }
        
        if (result !== expectedResult) {
            return console.error('❌ ' + testResult + ` (ожидалось ${expectedResult})`);
        }

        console.log('✅ ' + testResult);
    });
};

// Тест 1: Цены [10, 1, 5] - побеждает цена 5 (индекс 2)
// 10 отвечает быстро отказом (0ms)
// 1 отвечает отказом (100ms)
// 5 отвечает согласием (300ms)
testcase('1 5 10 // Цена 5, индекс 2', 2, 350, async () => {
    const buyers = [
        { price: 10, accepts: asyncResponse(0, false) },
        { price: 1, accepts: asyncResponse(100, false) },
        { price: 5, accepts: asyncResponse(300, true) },
    ];

    return bestBuyer(buyers);
});

// Тест 2: Цены [10, 1, 5] - побеждает цена 5 (индекс 2), но с другим порядком ответов
// 10 отвечает отказом (200ms)
// 5 отвечает согласием (100ms) - не ждём ответа от 1
testcase('1 5 10 // Цена 5, индекс 2', 2, 250, async () => {
    const buyers = [
        { price: 10, accepts: asyncResponse(200, false) },
        { price: 1, accepts: asyncResponse(300, false) },
        { price: 5, accepts: asyncResponse(100, true) },
    ];

    return bestBuyer(buyers);
});

// Тест 3: Цены [10, 5, 1] - побеждает цена 5 (индекс 1)
// 10 отвечает отказом (0ms)
// 5 отвечает согласием (100ms) - не ждём ответа от 1
testcase('1 5 10 // Цена 5, индекс 1', 1, 150, async () => {
    const buyers = [
        { price: 10, accepts: asyncResponse(0, false) },
        { price: 5, accepts: asyncResponse(100, true) },
        { price: 1, accepts: asyncResponse(300, true) },
    ];

    return bestBuyer(buyers);
});

// Тест 4: Цены [1, 2] - никто не согласился, возвращаем -1
// Оба отвечают отказом
testcase('1 2 // Никого не выбираем, индекс -1', -1, 100, async () => {
    const buyers = [
        { price: 1, accepts: asyncResponse(0, false) },
        { price: 2, accepts: asyncResponse(50, false) },
    ];

    return bestBuyer(buyers);
});

// Дополнительный тест 5: Цены [100, 50, 25] - первый же соглашается
testcase('100 50 25 // Первый согласился, индекс 0', 0, 150, async () => {
    const buyers = [
        { price: 100, accepts: asyncResponse(100, true) },
        { price: 50, accepts: asyncResponse(200, true) },
        { price: 25, accepts: asyncResponse(300, true) },
    ];

    return bestBuyer(buyers);
});

// Дополнительный тест 6: Все отказываются, но с разными задержками
testcase('Все отказались, индекс -1', -1, 300, async () => {
    const buyers = [
        { price: 30, accepts: asyncResponse(100, false) },
        { price: 20, accepts: asyncResponse(150, false) },
        { price: 10, accepts: asyncResponse(200, false) },
    ];

    return bestBuyer(buyers);
});
```