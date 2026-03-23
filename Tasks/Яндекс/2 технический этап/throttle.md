```js
/**
 * throttle.
 *
 * Напишите функцию throttle(fn, delay, ctx) – «тормозилку», которая возвращает обёртку,
 * вызывающую fn не чаще, чем раз в delay миллисекунд.
 * В качестве контекста исполнения используется ctx.
 * Первый вызов fn всегда должен быть синхронным.
 * Если игнорируемый вызов оказался последним, то он должен выполниться.
 */

// пример для delay === 100
// . - вызовы throttledFn
// ! - вызовы fn
//..............
//!        !        !
//0ms      100ms    200ms
//..  .    .    .
//!        !        !
//0ms      100ms    200ms

function throttle(fn, delay, ctx) {
    // code here
}

function test() {
    const start = Date.now();

    function log(text) {
        const msPassed = Date.now() - start;
        console.log(`${msPassed}: ${this.name} logged ${text}`);
    }

    const throttled = throttle(log, 100, { name: 'me' });

    setTimeout(() => throttled('m'), 0);
    setTimeout(() => throttled('mo'), 22);
    setTimeout(() => throttled('moc'), 22);
}
```

Решения

```js
function throttle(fn, delay, ctx) {
    let lastCall = 0;
    let timeout = null;

    return function (...args) {
        const now = Date.now();

        if (now - lastCall >= delay) {
            lastCall = now;
            fn.apply(ctx, args);
        } else if (!timeout) {
            timeout = setTimeout(() => {
                timeout = null;
                lastCall = Date.now();
                fn.apply(ctx, args);
            }, delay);
        }
    };
}
```

Второе решение

```js
function throttle(fn, delay, ctx) {
    let isThrottled = false;
    let lastArgs = null;
    let lastThis = null;

    return function resultFn(...args) {
        if (isThrottled) {
            // Запоминаем последние аргументы для выполнения после задержки
            lastArgs = args;
            lastThis = this;
            return;
        }

        // Выполняем первый вызов сразу
        fn.apply(ctx || this, args);
        isThrottled = true;

        // Устанавливаем таймер на сброс состояния
        setTimeout(() => {
            isThrottled = false;

            // Если был последний вызов во время "торможения" - выполняем его
            if (lastArgs) {
                resultFn.apply(lastThis, lastArgs);
                lastArgs = null;
                lastThis = null;
            }
        }, delay);
    };
}
```