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

 * [Task](./models/task.md)
 * [JavascriptTask](./models/javascript-task.md)
 * [JavascriptChallengeTask](./models/javascript-challenge-task.md)
 * [Goal](./models/goal.md)
 * [JavascriptGoal](./models/javascript-goal.md)
 * [JavascriptChallengeGoal](./models/javascript-challenge-goal.md)
 * [Code](./models/code.md)

 * [AstAsserts](./models/ast-asserts.md)
 * [ConsoleAsserts](./models/console-asserts.md)

**Отображения** (view) - объекты, плотно связанные с DOM, реагируют
на события, что-нибудь меняют в DOM и отправляют события, реагируя
на DOM-события.

 * [TaskView](./views/task-view.md) - отображение задания. На самом деле, могло бы быть обёрткой, т.к. по большей части создаёт другие отображения, обёртки и модели
 * [GoalsView](./views/goals-view.md) - показывает, насколько выполнены задания
 * [JavascriptConsoleView](./views/javascript-console-view.md) - отображает сообщения от консоли
 * [JavascriptControlsView](./views/javascript-controls-view.md) - кнопки управления заданием (проверить на сервере, сохранить код и т.п.)
 * [JavascriptRunView](./views/javascript-run-view.md) - кнопка "выполнить код", чекбокс "автовыполнение"
 * [NextTaskView](./views/next-task-view.md) - кнопка "следующее задание"
 * [ShowAnswersView](./views/show-answers-view.md) - кнопка "показать ответ"
 * [TaskCompletedView](./views/task-completed-view.md) - сообщение "задание выполнено"

**Обёртки** (wrapper) - объекты, содержащие в себе всю остальную логику,
не попавшую в модели или отображения.

 * [Answers](./wrappers/answers.md)
 * [Console](./wrappers/console.md)
 * [ConsoleSlowdown](./wrappers/console-slowdown.md)
 * [Editor](./wrappers/editor.md)
 * [CssEditor](./wrappers/css-editor.md)
 * [HtmlEditor](./wrappers/editors/html-editor.md)
 * [JsEditor](./wrappers/editors/js-editor.md)
 * [Editors](./wrappers/editors.md)
 * [JavascriptRunner](./wrappers/javascript-runner.md)
 * [Storage](./wrappers/storage.md)
 * [Timer](./wrappers/timer.md)
 * [Timers](./wrappers/timers.md)
 * [XHR](./wrappers/xhr.md)
