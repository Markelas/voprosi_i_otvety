```js
/*

Дана асинхронная функция fn и время t в миллисекундах, нужно вернуть новую версию этой функции,

выполнение которой ограничено заданным временем. Функция fn принимает аргументы, переданные в эту новую функцию.
Возвращаемая функция работает по следующим правилам:
- если fn выполнится за заданное время t, то функция резолвит полученные данные
- если fn не выполнится за заданное время t, то функция реджектит строку "Time limit exceeded"
*/

/**

* @param {Function} fn
* @param {number} t
* @return {Function}
*/

const timeLimited = function (fn, t) {
return function (...args) {
	return Promise.race(
	[fn(...args), 
	new Promise((resolve, reject) => setTimeout(() => reject('Time limit exceeded'), t))]
	)}
};


const asyncF = () => new Promise((resolve) => setTimeout(() => resolve(1), 2000))
const limitedF = timeLimited(asyncF, 1000);

console.log(limitedF().then(console.log).catch(console.log))
```