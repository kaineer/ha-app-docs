Timer
=====

Обёртка над `setTimeout`, первый параметр при создании - опции для настройки
таймера. Например:

```javascript
var autosaveTimer = Timer({
  // Функция-обработчик. Вызывается, когда пауза заканчивается
  handler: function() {
    app.emit('timer.autosave');
  },

  // Время после вызова метода start, через которое будет вызван
  //   обработчик
  timeout: 3000, // 3 seconds

  // Таймер подпишется на событие `timer.autosave.restart`
  //   и вызовет `reset` по приходу события
  'timer.autosave.restart': function() { this.reset(); }
})
```

Методы
------

 * `start` - запустить таймер
 * `reset` - остановить, если запущен и заново запустить таймер
 * `stop` - остановить таймер
 * `enable` - установить флаг `enabled`
 * `disable` - сбросить флаг `enabled`

Обработчик не будет запущен, если флаг `enabled` сброшен (`== false`).
