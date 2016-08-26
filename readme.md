Структура приложения
====================

(Файлы: `ha-app/app.js`, `ha-app/app/event-dispatcher.js`)

Чтобы не передавать в конструкторы каждого отображения (view) и каждой модели
ссылки на уже существующие объекты, введён-таки снова **god**-объект
"приложение" (`App`), который хранит в себе ссылки на объекты,
доступ к которым необходим из других объектов.

Пример:

```javascript
// ha-app/models/code.js
var app = require('../app')();

var Code = function() {
  var code = { ... };

  // --- skip ---

  app.register('model.code', code);

  return code;
}

// ha-app/wrappers/javascript-runner.js
var app = require('../app')();

var JavascriptRunner = function() {
  var runner = {
    // --- skip ---

    evaluate: function() {
      // Получаем ссылку на модель
      var code = app.get('model.code');
      // Получаем javascript-код
      var jsCode = code.js;
      // --- skip ---
    }
  };

  // --- skip ---

  return runner;
};


```

Кроме того, объект `App` обеспечивает передачу сообщений от любого отображения
или модели к любому объекту, подписавшемуся на это сообщение.

Пример:

```javascript
// Получаем ссылку на объект "Приложение"
var app = require('../app')();

var ShowAnswersView = function() {
  var view = {
    initialize: function() {
      // Если у задачи вызван метод addAnswers, отображаем кнопку
      app.on('task.has.answers', this.onTaskHasAnswers, this);

      // При нажатии кнопки "Показать ответы"
      document.querySelector('.js--show-answers').addEventListener('click',
        function() {
          // Отправляем сообщение объекту "Ответы"
          app.emit('answers.show');
        }
      );
    },

    // --- skip ---
  };

  view.initialize();

  return view;
}
```

Остальные объекты: модели, отображения и обёртки будут рассмотрены ниже.

**Модели** (model) - объекты, хранящие в себе данные. По изменению
данных могут отправлять события. Могут содержать логику, в частности,
для заданий определяют - выполнено ли задание или нет.

**Отображения** (view) - объекты, плотно связанные с DOM, реагируют
на события, что-нибудь меняют в DOM и отправляют события, реагируя
на DOM-события.

**Обёртки** (wrapper) - объекты, содержащие в себе всю остальную логику,
не попавшую в модели или отображения.
