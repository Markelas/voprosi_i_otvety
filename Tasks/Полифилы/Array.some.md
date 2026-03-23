```js
Array.prototype.some = function(callback, thisArg) {
    for (let i = 0; i < this.length; i++) {
        if (callback.call(thisArg, this[i], i, this)) {
            return true;
        }
    }
    return false;
};

// Примеры:
console.log([1, 2].some(x => x > 1)); // true
console.log([10, 5].some(x => x < 5)); // false```