# findMaxMin

Розв'язання цієї задачі може виглядати ось так:

```js
const findMaxMin = (prices = [], sortBy = "") => {
  // 1. Якщо довжина масиву дорівнює нулю, то повернути `[-1, -1]`
  if (prices.length === 0) {
    return [-1, -1];
  }
  let minMaxPrice;
  let index;
  // 2. Інакше, задати початкові значення min-max згідно `sortBy`
  if (sortBy === "max") {
    minMaxPrice = Number.MIN_SAFE_INTEGER;
  } else {
    minMaxPrice = Number.MAX_SAFE_INTEGER;
  }
  // 3. Для кожного елементу масиву
  for (let i = 0; i < prices.length; i++) {
    // порівняти його зі знайденим min-max значенням
    if (sortBy === "max" && prices[i] > minMaxPrice) {
      // 4. При виконанні умов пошуку -
      minMaxPrice = prices[i]; // зберігти нове значення
      index = i; // і його індекс
    }
    if (sortBy === "min" && prices[i] < minMaxPrice) {
      minMaxPrice = prices[i];
      index = i;
    }
  }
  // 5. Повернути отриманий результат
  return [minMaxPrice, index];
};
```

Давайте покращимо це рішення. Можемо замінити перший крок алгоритму і, замість того, щоб одразу повертати результат, робити це потім.

```js
export const findMaxMin = (prices = [], sortBy = "") => {
  let minMaxPrice = -1;
  let index = -1;

  if (prices.length) {
    // ...
  }

  return [minMaxPrice, index];
};
```

Ми також можемо переробити умову `if-else` для створення початкового значення пошуку. Для цього ми використаємо тернарний оператор, а також те, що В JavaScript існує [два способи](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Property_Accessors) звернутись до елементу обʼєкту: через dot нотацію та через bracket нотацію. При цьому ми розглядаємо `Number` як обʼєкт і звертаємось до його поля:

```js
Number.MAX_SAFE_INTEGER
// 9007199254740991
Number["MAX_SAFE_INTEGER"]
// 9007199254740991
```

Таким чином ми можемо зробити наступне:

```js
Number["max".toUpperCase() + "_SAFE_INTEGER"]
// 9007199254740991
```

тобто ми можемо скористатися аргументом `sortBy`, аби одразу отримати необхідне значення. Зверніть увагу, якщо ми шукаємо максимальне значення (`sortBy = "max"`), то нам необхідно присвоїти початкове значення мінімальне допустиме і навпаки.

```js
minMaxPrice = Number[(sortBy === "max" ? "MIN" : "MAX") + "_SAFE_INTEGER"];
```

Але ми можемо зробити ще простіше і прибрати цей код зовсім, якщо для пошуку значення будемо використовувати не свій алгоритм, а вбудований. Для цього використаємо вбудований глобальний обʼєкт [Math](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math) і його методи `min` та `max`. Для цього нам необхідно передати в якості аргументів ціни.

**Зверніть увагу:** ці методи приймають в якості аргументів тільки примітиви, тому ми не можемо передати масив - його необхідно розділити на окремі елементи, що можна зробити за допомогою [spread operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)

Таким чином отримаємо:

```js
if (sortBy === "max") {
  minMaxPrice = Math.max(...prices);
} else {
  minMaxPrice = Math.min(...prices);
}
```

Для цього коду ми можемо застосувати таку саму оптимізацію з тернарним оператором, як і раніше:

```js
minMaxPrice = Math[sortBy](...prices);
```

Тепер залишилось тільки віднайти індекс цього елементу, що легко зробити за допомогою вбудованого методу [indexOf](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf).

Таким чином ми отримали красиве і коротке рішення:

```js
const findMaxMin = (prices = [], sortBy = "") => {
  let minMaxPrice = -1;
  let index = -1;

  if (prices.length) {
    minMaxPrice = Math[sortBy](...prices);
    index = prices.indexOf(minMaxPrice);
  }

  return [minMaxPrice, index];
};
```

Таке рішення вже достатньо оптимізовано і підходить для "продакшн релізу". Воно є читабільне і швидке. Але ми можемо погратись у [гольф кодом](https://en.wikipedia.org/wiki/Code_golf) і зробити його ще коротшим 🤓

```js
export const findMaxMin = (prices = [], sortBy = "", m) =>
  prices.length
    ? [(m = Math[sortBy](...prices)), prices.indexOf(m)]
    : [-1, -1];
```
