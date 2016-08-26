JavascriptTask
==============

Надстройка над моделью `Task`, позволяет проверять цели по обычному javascript-заданию.

Определяет методы
-----------------

 * `createMatcher` - позволяет определить куски кода, с которыми можно матчить введенный код
 * `addGoal` - DSL метод, позволяет добавить к заданию цель, которую должен выполнить пользователь

 Например

```javascript
// window.HtmlacademyTask ссылается на экземпляр модели Task
HtmlacademyTask.createMatcher(function(matcher) {
  matcher.
    goal('seconds.per.hour').
      code('"#builtin.anything"; 60 * 60; "#day.seconds"').
      assign('day.seconds', 'builtin.anything').
    goal('seconds.per.day').
      takes('seconds.per.hour').
      assign('day.seconds', 'seconds.per.day.permutation').
    fragment('seconds.per.day.permutation').
      code('24 * 60 * 60', '60 * 60 * 24', '60 * 24 * 60');
});

HtmlacademyTask.addGoal(function(goal) {
  goal.console.assertResultEqualTo(3600);
  goal.ast.assertMatches('seconds.per.hour');
});

HtmlacademyTask.addGoal(function(goal) {
  goal.console.assertResultEqualTo(3600*24);
  goal.ast.assertMatches('seconds.per.day');
});
```

 * `sendCheckRequest` - отправляет на сервер запрос проверить цели, используя текущее состояние кода
 * `applyResponse` - приводит модель `Task` в соответствие с ответом от сервера

 * `runGoals` - запускает проверки на всех целях по порядку

Подписывается на события
------------------------

 * `code.is.dirty` - как только код изменён, запускает таймер перед выполнением javascript-кода
 * `code.changed.done` - когда все уже знают, что код изменился, запустить метод `runGoals`
 * `send.check-request` - когда нажата кнопка "Проверить на сервере", вызвать метод `sendCheckRequest`
