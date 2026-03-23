
```js
/**
 * @param {Function} cb (error, data) => {}
 */
const asyncAuth = (cb) => { cb(true) };

/**
 * Функция `asyncAuth(callback)` принимает callback, в который может
 * быть передана ошибка (первым аргументом) и данные
 * с бэкенда (вторым аргументом).
 * asyncAuth((error, data) => {});
 *
 * Вам нужно реализовать функцию `auth()`,
 * которая вызывает `asyncAuth()`, но возвращает Promise.
 *
 * @returns {Promise}
 */
function auth() {
    return new Promise((resolve, reject) => {
        asyncAuth((error, data) => {
            if (error) {
                reject(error)
            } else {
                resolve(data);
            }
        })
    })
}

/**
 * Функция `tryAuth()` использует `auth()` и, в случае ошибки,
 * совершает N дополнительных попыток.
 * в случае, если все попытки провалились - вернуть последнюю ошибку
 *
 * @returns {Promise}
 */
async function tryAuth(n) {
    let lastError = '';
    let retries = n;

    for (let i = 0; i <= n; i++) {
        try {
            return await auth();
        } catch (e) {
            if (retries === 0) {
                throw lastError;
            }
            retries--;
            lastError = e;
        }   
    }
}
tryAuth(10).then(console.log).catch(console.log)
```