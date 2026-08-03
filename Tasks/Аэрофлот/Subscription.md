```js
/**
 * Реализовать класс Subscription.
 * Должны поддерживаться методы subscribe, unsubscribe и next.
 * Все методы должны быть строго типизированы.
 */
class Subscription<T> {
    // Изменять можно только здесь
}

const sub = new Subscription<string>();

const handler1 = (value: string) => {
    console.log('Handler1 received:', value);
};

const handler2 = (value: string) => {
    console.log('Handler2 received:', value);
};

const unsubscribe1 = sub.subscribe(handler1);
const unsubscribe2 = sub.subscribe(handler2);

sub.next('First');
// Вывод:
// Handler1 received: First
// Handler2 received: First

unsubscribe1();

sub.next('Second');
// Вывод:
// Handler2 received: Second
```