# Contribution guide

## Install

Для запуску необхідно встановити залежності, виконавши 
команду `npm install` в корні проекту.

Вимоги до версій "Nodejs" та "npm":

```json
{
  "node": ">=16.0.0",
  "npm": ">=8.0.0"
}
```

## Запуск тестів

Для тестування завдань використовується [`jasmine`](https://github.com/jasmine/jasmine).
Тести до завдань запустити можна двома способами:

* відкривши файл `index.spec.html` відповідного завдання в браузері
* запустивши одну з команд тест-ранера [`karma`](https://karma-runner.github.io/latest/index.html)

### Запуск тестів за допомогою Karma

Запуск тестів для всіх треків:

```npm
npm run test:karma
```

Запуск тестів для певного завдання:

```npm
npm run test:karma --grep <track-name>/<test-name>
```

Наприклад запустимо тести для задачі `sum` в треку `basic`:
`npm run test:karma -- --grep basic/sum`

Запуск тестів для всього треку:

```npm
npm run test:karma -- --grep <track-name>
```

Наприклад запустимо для всього треку `basic`:
`npm run test:karma -- --grep basic`

## Структура задачі:

До проекту задачі додаються шляхом пулл-реквесту в поточний репозиторій.

  ```
  .
  └── 📁 00-taskName
    ├── 📄 readme.md
    ├── 📄 index.js
    ├── 📄 index.spec.html
    ├── 📄 index.spec.js
    └── 📁 solution
        ├── 📄 index.js
        └── 📄 readme.md
  ```
  
  * `readme.md` - опис завдання
  * `index.js` - структура завдання без рішення
  * `index.spec.html` - файл для запуску тестів
  * `index.spec.js` - тести до завдання
  * `solution/index.js` - файл з розв'язанням завдання
  * `solution/readme.md` - опис алгоритму розв'язання завдання

## Структура треку завдань:

Трек (track) - це підбірка завдань/задач об'єднаних спільною ідеєю.

Можна об'єднувати задачі в трек за критерієм складності, наприклад:

* js-core-level-1
* js-core-level-2
* js-core-level-3
* ...

Так само можна піти шляхом об'єднання завдань відповідно до тем:

* array-methods
* data-conversion
* objects
* algorithms
* ...

Або навіть об'єднати ці два підходи: `array-methods-level-1`

Кожен track може містити до 99 задач, які іменуються за відповідним шаблоном:

```
<номер-завдання>-<назва-завдання>
```

**Приклад:**

  ```
  .
  └── 📁 trackName
    ├── 📄 readme.md
    ├── 📁 01-taskName
    ├── 📁 02-someTask
    ├── 📁 ... 
    ├── 📁 99-anotherTask
  ```

  * `readme.md` - опис треку
  * `00-taskName` - назва завдання
  
Самі завдання будуть видаватись користувачу для вирішення відповідно до номера в
назві завдання.
