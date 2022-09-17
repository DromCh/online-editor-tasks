# textCutter

Розв'язання цієї задачі може виглядати ось так:

```js
export const textCutter = (string = "", n = 0) => {
  if (string.length <= n) {
    return string;
  }

  let acc = 0;
  const targetLength = n - 3;
  const words = string.split(" ");
  const textCut = [];
  words.forEach((w) => {
    if (acc + w.length <= targetLength) {
      textCut.push(w);
      acc += w.length;
    }
  }, 0);

  let res = textCut.join(" ");
  if (res.slice(-1) === "." || res.slice(-1) === ",") {
    res = res.slice(0, -1);
  }

  return res + "...";
};
```

## Алгоритм дій:

1. Якщо задана максимальна довжина фрагменту не менше тексту, то повернути весь текст цілком
1. Інакше додавати по одному слову у фрагмент до тих пір, поки не досягнуто цільової довжини
1. Перевірити, що у отриманому фрагменті немає висячих знаків пунктуації
1. Повернути фрагмент, додавши три крапки в кінець фрагменту

Розглянемо реалізацію алгоритму в коді. Для виконання першого пункту, нам достатньо наступного коду:

```js
export const textCutter = (string = "", n = 0) => {
  // Якщо задана максимальна довжина фрагменту не менше тексту
  if (string.length <= n) {
    // То повернути весь текст цілком
    return string;
  }
}
```

Тепер розглянемо наступний пункт алгоритму. Для ітерування слів у вихідному тексті можна використати цикл `for` і шукати символ пробілу " ", або ж скористуватися методом строки `split`, з якого отримати масив слів і працювати з ним (наприклад, за допомогою методу `forEach`). Після того, як фрагмент набере необхідну кількість слів, знову обʼєднаємо його у строку за допомогою метода `join`.

```js
export const textCutter = (string = "", n = 0) => {
  if (string.length <= n) {
    return string;
  }

  // Інакше обробити текст наступним чином
  let acc = 0; // Тут зберігаємо довжину фрагменту
  const targetLength = n - 3; // Це наша цільова довжина: мінус крапки
  const words = string.split(" "); // Працювати будемо з масивом слів
  const textCut = []; // Тут зберігаємо отриманий фрагмент
  words.forEach((w) => { // Для кожного слова з тексту
    // Якщо довжина отриманого фрагменту не перевищує цільову,
    if (acc + w.length <= targetLength) {
      textCut.push(w); // додати це слово у фрагмент і
      acc += w.length; // врахувати в загальну довжину фрагменту
    }
  }, 0);
  // Обʼєднати отриманий фрагмент в строку
  let res = textCut.join(" ");
};
```

Останні два кроки алгоритму в тому, що перевірити "висячі" знаки пунктуації. Для цього нам необхідно перевірити останній символ фрагменту, чи там є "." або "," із завдання. Якщо є, то прибрати її. Перевірити можна за допомогою методу строки `charAt` або, знайомим по минулим задачам, методом `slice`. Далі нем необхідно додати до отриманого фрагменту три крапки "..." за допомогою методу `concat` або ж за допомогою оператора "+", який для нашого випадку робить те саме.

```js
export const textCutter = (string = "", n = 0) => {
  if (string.length <= n) {
    return string;
  }
  
  let acc = 0;
  const targetLength = n - 3;
  const words = string.split(" ");
  const textCut = [];
  words.forEach((w) => {
    if (acc + w.length <= targetLength) {
      textCut.push(w);
      acc += w.length;
    }
  }, 0);
  let res = textCut.join(" ");

  // Перевірити, що немає висячих знаків пунктуації
  if (res.slice(-1) === "." || res.slice(-1) === ",") {
    // і якщо є, то відрізати останній символ
    res = res.slice(0, -1);
  }
  // Повернути результат з трьома крапками наприкінці
  return res + "...";
};
```

Наше рішення працює, але його можна трохи оптимізувати. Якщо використати метод `reduce` для ітерування, ланцюжки оброки та регулярні вирази, то рішення стане коротшим та більш елегантним 🤗

**Увага:** Недоліком цього підходу буде значно більше використання памʼяті, бо метод `reduce` буде робити копію масиву на кожній ітерації.

```js
export const textCutter = (string = "", n = 0) => {
  // На початку, перевіримо чи взагалі треба щось робити
  if (string.length <= n) {
    return string;
  }
  // Якщо треба, то тоді
  return (
    string
      // Розділимо на слова
      .split(" ")
      // Для кожного слова з тексту
      .reduce(
        (acc, w) => {
          // Якщо довжина отриманого фрагменту не перевищує цільову,
          if (acc[1] + w.length <= n - 3) {
            // записати те слово і збільшити лічильник довжини фрагменту,
            return [[...acc[0], w], (acc[1] += w.length)];
          }
          // інакше запамʼятати цей фрагмент
          return acc;
        },
        [[], 0]
      )[0]
      // Обʼєднати отриманий фрагмент в строку
      .join(" ")
      // Додати до результату три крапки наприкінці
      .concat("...")
      // Перевірити, що немає висячих знаків пунктуації і повернути
      .replace(/,\.\.\.|\.\.\.\./, "...")
  );
};
```

## Корисні посилання

[String.prototype.slice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/slice)

[String.prototype.split](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split)

[Array.prototype.join](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/join)

[Array.prototype.forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)

[String.prototype.concat](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/concat)

[for...of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of#iterating_over_a_string)
