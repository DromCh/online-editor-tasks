# logMonitoring

Розв'язання цієї задачі може виглядати ось так:

```js
export const logMonitoring = (string = "", dates = "") => {
  // 1. Використовуючи заданий список роздільників, визначити
  let separator = "";
  if (dates.includes("...")) {
    separator = "...";
  } else if (dates.includes(";")) {
    separator = ";";
  } else {
    separator = " - ";
  }
  // період дат, для яких треба зробити моніторинг
  const [startDateString, endDateString] = dates.split(separator);
  const startDate = new Date(startDateString);
  const endDate = new Date(endDateString + "T23:59:59.999");

  // 2. Для наданого лог-файлу отримати масив записів
  const logs = string.split("\n");

  // 3. Використовуючи список статусів,
  const CRITICAL = ["CRIT", "КРИТ", "严重"];
  const result = logs.filter((l) => {
    // виділити необхідні фрагменти
    const log = l.split(";");
    const date = new Date(log[0]); // з датою
    const status = log[1]; // та статусом

    // 4. Для кожного запису перевірити,
    if (
      // чи не є він критичним
      CRITICAL.includes(status) &&
      // і попадає в заданий період дат
      date.getTime() > startDate.getTime() &&
      date.getTime() < endDate.getTime()
    ) {
    // 5. При отриманні позитивного результату - записати його
      return true;
    } else {
      return false;
    }
  });
  // 6. Якщо кількість критичних статусів більше 0 - повернути `true`
  return result.length > 0;
}
```

Розберемо алгоритм дій і приведений варіант рішення, а також застосуємо деякі принципи програмування для спрощення нашого коду.

Перший пункт звучить так: "Використовуючи заданий список роздільників, визначити період дат, для яких треба зробити моніторинг".

Найпростішим способом розділення строки дат, є використання методу `split`, але цей метод приймає тільки одне значення, тому необхідно перевірити на кожний роздільник із заданого списку. Отримаємо масив із двох дат. Цей масив можна одразу присвоїти змінним, використовую [деструктуризаційне присвоєння](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment).

Після цього, нам треба перетворити отримані строки на дату типу [Date]((https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)). Зверніть увагу, що при виконанні коду

```js
new Date('2022-01-22');
// Sat Jan 22 2022 01:00:00 GMT+0100 (Central European Standard Time)
```

ми отримаємо час `00:00:00 GMT`, а нам потрібно, щоб друга дата враховувалась до кінця вказаного дня, для цього додамо до кінцевої дати час самостійно: `endDateString + "T23:59:59.999"`.

Давайте тут використаємо [принцип KISS](https://uk.wikipedia.org/wiki/%D0%9F%D1%80%D0%B8%D0%BD%D1%86%D0%B8%D0%BF_%C2%ABKISS%C2%BB) і спростимо ланцюжок `if`. Для цього створимо список сепараторів і перевіримо, який саме було використано для в якості роздільника:

```js
const separators = [";", "...", ' - '];
const separator = separators.filter(s => dates.includes(s))
```

Так код став трохи простішим. Але ми можемо його ще спростити, прибравши змінну `separators` і викликати метод напряму:

```js
const separator = [";", "...", " - "].filter((s) => dates.includes(s));
```

Імплементацію всіх наступних пунктів алгоритму представлено в рішенні вище. Як видно, у нас є багато повторювань (наприклад, метод `getTime` і конструктор `new Date` викликаються декілька разів), тому ми використаємо [принцип DRY](https://uk.wikipedia.org/wiki/Don%27t_repeat_yourself).

Створимо допоміжну функцію, для перетворення строки в тип Date і отримання часу.

```js
const time = (t) => new Date(t).getTime();
```

Таким чином, можна змінити код, де ми отримуємо початкову та кінцеві дати, бо нам тепер потрібна тільки `String`. Можемо використати метод `map` і обробити строку періоду дат так, щоб до другої дати добавити суфікс з часом, використовуючи індекс. Цей метод має наступну сигнатуру:

```js
map((element, [index, [array]]) => { /* … */ })
```

Тому наш код перетвориться на:

```js
const [startDate, endDate] = dates
    .split(separator)
    .map((d, i) => (!i ? d : d + "T23:59:59.999"));
```

Тут ми перевіряємо, чи індекс `i` є `truthy`, тобто не рівний нулю, якщо так, то просто повертаємо строку, інакше ми отримали другу (кінцеву) дату з порядковим індексом 1 і додаємо до тої дати ще й час. Чи є такий код більш зрозумілим, чи ні - в кожному випадку треба вирішувати окремо 🤗

Метод, який ми використали для ітерування записів `filter`, перебирає усі елементи масиву. Хоча нам відомо, що якщо хоч один з записів має статус "критичний", то одразу можна повертати `true` і більше не ітерувати далі. Тому тут краще використати метод [some](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some).

Метод `some()` перевіряє, чи принаймні один елемент у масиві проходить перевірку, реалізовану наданою функцією. Він повертає `true`, якщо в масиві знаходить елемент, для якого надана функція повертає `true`; інакше він повертає `false`. Це не змінює масив.

Результат виконання цього методу можна одразу повертати і не записувати в змінну. А на вхід можна подати вхідну змінну `string`, яку одразу перетворимо на масив методом `split`.

Кінцевий код буде мати вигляд:

```js
export const logMonitoring = (string = "", dates = "") => {
  const CRITICAL = ["CRIT", "КРИТ", "严重"];
  const time = (t) => new Date(t).getTime();

  const separator = [";", "...", " - "].filter((s) => dates.includes(s));
  const [startDate, endDate] = dates
    .split(separator)
    .map((d, i) => (!i ? d : d + "T23:59:59.999"));

  return string.split("\n").some((l) => {
    const [date, status] = l.split(";");
    return (
      CRITICAL.includes(status) &&
      time(date) > time(startDate) &&
      time(date) < time(endDate)
    );
  });
};
```

Принаймні, тепер наша програма коротша ніж у першому варіанті 😅

## Корисні посилання

[Array.prototype.filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)

[Array.prototype.some](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)

[Array.prototype.map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

[Date.prototype.getTime](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/getTime)
