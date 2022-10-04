# logParsing

Розв'язання цієї задачі може виглядати ось так:

```js
export const logParsing = (string = "") => {
  // 1. Виділити кожний запис (міститься між фігурними дужками)
  const records = string.match(/{([\s\S]*?)}/gm);
  // 2. Для кожного запису: розділити запис на елементи
  const completeRecords = records.filter(
    // 3. Перевірити що елементів, що їх рівно 6, тобто запис є повним
    (record) => record.split("\n").length === 6
  );

  const result = [];
  // 4. Для кожного повного запису
  completeRecords.forEach((record) => {
    const rows = record.split("\n");
    const elementsPosition = {
      timestamp: 0,
      "server-id": 1,
      message: 2,
    };
    const finalRecord = [];
    rows.forEach((row) => {
      const isRequiredRow = /timestamp|server-id|message/.test(row);
      if (isRequiredRow) {
        const key = row.slice(2, 17).replace("mulog/", "").trim();
        // витягнути значення необхідних ідентифікаторів
        const value = row.slice(17).trim().replace(/[",}]/g, "");
        finalRecord[elementsPosition[key]] = value;
        // (час конвертувати в формат ISO)
        if (key === "timestamp") {
          finalRecord[0] = new Date(+finalRecord[0]).toISOString().slice(0, 10);
        }
      }
    });
    // і додати до вихідної строки
    result.push(finalRecord.join(";"));
  });
  // 5. Повернути закінчену вихідну строку
  return result.join("\n");
};
```

Цю задачу можна вирішити за допомогою тільки циклів `for` та вбудованих методів строки. Але код можна зробити більш коротким і зрозумілим, якщо розглядати строку як масив (перетворивши її на масив, наприклад, як було показано в задачі [arrToString](js-track/basic/arrToString)) і застосувати вбудовані методи масиву для ітерування.

Розберемо вирішення цього завдання, а також трохи переробимо його.

Перший пункт звучить так: "Виділити кожний запис (міститься між фігурними дужками)". Тобто нам необхідно ітерувати задану строку і записувати усе в змінні. Почнемо з використання циклу `for` та оператора умови `if`.

```js
let currentIndex = 0; // Тут зберігаємо індекс найденого запису
let result = []; // Записи будемо зберігати у масиві
// Ітеруємо (тобто перебираємо) кожний символ вхідної строки
let start; // Маркер початку запису
for (let i = 0; i <= string.length; i++) {
  if (string[i] === "{") { // Якщо ми зустріли початок запису,
    start = i; // то поставити тут маркер
  }
  if (string[i] === "}")  { // Якщо це кінець запису
    // Зберігаємо найдений запис
    result[currentIndex] = string.substring(start + 1, i);
    // І збільшуємо індекс записів на один для наступного запису
    currentIndex++;
  }
}
console.log(result); // <- Тут записаний результат
```

Але це занадто багатослівно, бо у JavaScript існує спосіб кращий за цей - можна використати [регулярні вирази](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions/Cheatsheet).

Щоб отримати все, що міститься між фігурних дужок, нам необхідно зробити групування за допомогою круглих дужок і вказати, що ми приймаємо будь-яку кількість (тобто `*`) будь-яких символів (тобто `.`):

```js
const records = string.match(/{(.*)}/gm);
```

Але в цьому випадку ми не враховуємо символ переносу строки, тому перепишемо наступним чином:

```js
const records = string.match(/{([\s\S]*?)}/gm);
```

Як бачите, більше десяти строк початкового коду ми замінили однією, тому надалі будемо використовувати вбудовані методи, коли вони доступні.

Наступні два пункти алгоритму вимагають, щоб ми відфільтрували отриманий масив, залишивши тільки ті записи, що містять повну кількість елементів, тобто рівно 6. Оскільки ми знаємо, що один запис відбитий від іншого символом `\n`, то ми можемо використати метод `split` і порівняти довжину отриманого масиву:

```js
const completeRecords = records.filter(
  (record) => record.split("\n").length === 6
);
```

В наступному, четвертому пункті алгоритму, нам необхідно для кожного запису вичленити необхідні значення і зберегти їх в тимчасову змінну. Оскільки послідовність цих елементів є довільною, то для пошуку, нам необхідно перевірити кожний елемент запису, порівнюючи з бажаним значенням. Це можна зробити за допомогою оператора `if`, але використаємо метод `test`, бо в такому випадку, ми можемо задати усі пошукові терміни, розділивши їх символом `|`.

**Увага:** Ми не можемо використовувати регулярний вираз в якості строки для пошуку, використовуючи метод строки `includes`, бо він викине `TypeError`. Саме тому, для цього випадку зручніше використати метод регулярних виразів `test`.

```js
const isRequiredRow = /timestamp|server-id|message/.test(row);
```

Оскільки ми знаємо, що вхідні дані завжди форматовані відступами, тобто значення знаходиться на фіксованій позиції відносно ключа,

```text
 :mulog/timestamp  1664789750262,
 :server-id        "S01"
 :message          "Some text here1"}
```

то ми можемо сміло відрізати частину строки, починаючи з 17-ї позиції і до кінця, для отримання значення. При цьому необхідно врахувати, що ми можемо отримати в результаті трохи пробілів, а також додаткові зайві символи (наприклад, кому `,` в кінці `mulog/timestamp`). Тому пробіли ми можемо видалити за допомогою метода строки `trim`, а зайві символи - методом `replace`. Щоб задати список поодиноких символів у регулярному виразі, які ми хочемо видалити, треба їх перелічити у квадратних дужках, тобто `[",}]`. Таким чином ми отримали наступне:

```js
const value = row.slice(17).trim().replace(/[",}]/g, "");
```

Залишається одне невирішене питання: яким чином розмістити елементи у заданому порядку, якщо на вході того порядку немає? То можна зробити декількома способами. Наприклад, перевіряти `if`-ом, який ми маємо зараз ключ, щоб зберігати його у відповідній тимчасовій змінній і потім склеїти їх:

```js
// На початку циклу
let timestamp, server, message;
  // У самому циклі
  const value = row.slice(17).trim().replace(/[",}]/g, "");
  if (row.includes('timestamp')) {
    timestamp = value;
  } else if // і так далі для всіх ключів
// І після циклу
const result = timestamp + ";" + server + ";" + value;
```

Але є більш красивий спосіб, можна використати таблицю відповідності ключів до позицій і порівнювати отриманий ключ з цією таблицею.

```js
// На початку циклу створити таблицю
const elementsPosition = {
  timestamp: 0,
  "server-id": 1,
  message: 2,
};
const finalRecord = []; // А тут збережемо результат
// У самому циклі, перевіряємо, що це один із очікуваних елементів
const isRequiredRow = /timestamp|server-id|message/.test(row);
if (isRequiredRow) {
  // Отримуємо його значення як і раніше
  const value = row.slice(17).trim().replace(/[",}]/g, "");
  // І отримуємо ключ (для timestamp треба відрізати префікс mulog)
  const key = row.slice(2, 17).replace("mulog/", "").trim();
  // Записуємо значення у відповідну комірку масиву
  finalRecord[elementsPosition[key]] = value;
// І після циклу
result.push(finalRecord.join(";"));
```

Єдине виключення, яке нам необхідно перевірити - час `timestamp`. Тільки для цього ключа необхідно перевести його значення у формат ISO.

```js
if (key === "timestamp") {
  const timestamp = +finalRecord[0]; // `+` конвертує строку у число
  const time = new Date(timestamp); // Створити обʼєкт дати з числа
  // Отримаємо щось таке: Tue Oct 04 2022 12:22:49 GMT+0200, тому
  const timeISO = time.toISOString(); // конвертуємо у 2022-10-04T10:22:38.917Z
  // Перезаписати дату, використовуючи тільки перші 10 символів
  finalRecord[0] = timeISO.slice(0, 10); // -> 2022-10-04
}
```

Залишилось тільки обʼєднати через `;` всі елементи у єдиний запис і потім склеїти усі записи за допомогою символу `\n`.

Тепер усе працює! 🚀

Цю задачу можна вирішити за допомогою методу `reduce` (як було показано у рішенні задачі [logConverting](js-track/objects/logConverting)). В цьому випадку ми можемо, використовуючи функціональний підхід, одразу використовувати результат виконання однієї функції як вхідні данні для наступної, а також обійтись без багатьох тимчасових змінних.

```js
export const logParsing = (string = "") => {
  const elementsPosition = {
    timestamp: 0,
    "server-id": 1,
    message: 2,
  };

  return string
    .match(/{([\s\S]*?)}/gm)
    .filter((record) => record.split("\n").length === 6)
    .reduce((result, record) => {
      result.push(
        record
          .split("\n")
          .reduce((final, row) => {
            if (/timestamp|server-id|message/.test(row)) {
              const key = row.slice(2, 17).replace("mulog/", "").trim();
              const value = row.slice(17).trim().replace(/[",}]/g, "");
              final[elementsPosition[key]] =
                key === "timestamp"
                  ? new Date(+value).toISOString().slice(0, 10)
                  : value;
            }
            return final;
          }, [])
          .join(";")
      );
      return result;
    }, [])
    .join("\n");
};
```

Недоліком цього рішення є то, що воно використовую більше памʼяті і може працювати повільніше попереднього, в залежності від величини вхідних даних. В такому випадку, можна поліпшити це рішення, використовуючи в обох циклах `reduce` пусту строку замість масиву и склеювати результат одразу. Спробуєте покращити? 🤓

## Корисні посилання

[Date.prototype.toISOString](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toISOString)

[Тут можна більше прочитати](https://stackoverflow.com/questions/5642315/regular-expression-to-get-a-string-between-two-strings-in-javascript), як можна виділяти фрагмент між двома елементами, використовуючи регулярні вирази

[String.prototype.match](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match)

[String.prototype.includes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/includes)

[RegExp.prototype.test](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/test)

[String.prototype.split](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split)

[String.prototype.trim](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/Trim)

[String.prototype.replace](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace)

[Array.prototype.filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)

[Array.prototype.forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)

[Array.prototype.reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)
