---
layout: default
title: Обработка взаимодействия
highlight: true
---

В этом уроке мы познакомимся с основными событиями ввода в Android.

## Обзор

В системе Android предусмотрено несколько способов перехвата событий взаимодействия пользователя с вашим приложением. Например, когда пользователь касается отображаемого объекта (кнопки), вызывается метод `onTouchEvent()` этого объекта.

Основные методы взаимодействия с `View`:

- `onClick()` из `View.OnClickListener`. Этот метод вызывается, когда пользователь касается элемента;
- `onLongClick()` из `View.OnLongClickListener`. Этот метод вызывается, когда пользователь касается элемента и удерживает его;
- `onFocusChange()` из  `View.OnFocusChangeListener`. Этот метод вызывается, когда пользователь делает активным или не активным текущий элемент;
- `onKey()` из `View.OnKeyListener`. Этот метод вызывается, когда пользователь переносит фокус на элемент и нажимает или отпускает клавишу на устройстве;
- `onTouch()` из `View.OnTouchListener`. Этот метод вызывается, когда пользователь выполняет действие, считающееся событием касания, например, нажимает, отпускает или выполняет любой жест на экране (в пределах границ элемента).
- `onCreateContextMenu()` из `View.OnCreateContextMenuListener`. Этот метод вызывается, когда создается контекстное меню.

 Чтобы определить один из этих методов и обрабатывать события, необходимо реализовать интерфейс в вашем проекте или определите его как анонимный класс. 

 Обратимся к документации. Обратите внимание, что вызов `onClick()` не содержит возвращаемого значения, но некоторые методы должны возвращать значение. Нужно возвращать значение или нет, зависит от события. Например:
`onLongClick()` — этот метод возвращает значение, указывающее, что вы обработали это событие: `true` - вы обработали событие и его следует остановить, `false` -  если вы не обработали его и/или событие должно продолжаться.


## onTouch

С простейшим onClick мы разобрались в предыдущем уроке. Давайте теперь посмотрим, что такое onTouch и с какими событиями он может работать. Для этого создадим новое `View` и установим ему `onTouchListener`:

```kotlin
floatingActionButton.setOnTouchListener(View.OnTouchListener { _, event ->
    Log.e("!!!", event.toString())
    return@OnTouchListener false
})
```

Запустив приложение и коснувшись экрана, мы увидим одну строку:

```log
2021-03-09 13:17:04.916 2751-2751/ru.ok.technopolis.recyclerview.sample E/!!!: MotionEvent { action=ACTION_DOWN, actionButton=0, id[0]=0, x[0]=49.0, y[0]=69.0, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, classification=NONE, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=0, eventTime=75312792, downTime=75312792, deviceId=6, source=0x1002, displayId=0 }
```

В логах есть упоминание только о событии `ACTION_DOWN`, но если обратиться к документации, событий гораздо больше. Почему другие события не попали в логи? Дело в том, что мы вернули `false` в методе `onTouch`, следовательно, мы сами сообщили о том, что завершили обработку событий. Давайте теперь попробуем вернуть значение `true`:

```log
2021-03-09 13:17:59.838 4750-4750/ru.ok.technopolis.recyclerview.sample E/!!!: MotionEvent { action=ACTION_DOWN, actionButton=0, id[0]=0, x[0]=64.0, y[0]=75.0, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, classification=NONE, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=0, eventTime=75367698, downTime=75367698, deviceId=6, source=0x1002, displayId=0 }
2021-03-09 13:17:59.840 4750-4750/ru.ok.technopolis.recyclerview.sample E/!!!: MotionEvent { action=ACTION_MOVE, actionButton=0, id[0]=0, x[0]=64.0, y[0]=75.0, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, classification=NONE, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=1, eventTime=75367721, downTime=75367698, deviceId=6, source=0x1002, displayId=0 }
2021-03-09 13:17:59.883 4750-4750/ru.ok.technopolis.recyclerview.sample E/!!!: MotionEvent { action=ACTION_MOVE, actionButton=0, id[0]=0, x[0]=64.0, y[0]=75.0, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, classification=NONE, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=0, eventTime=75367750, downTime=75367698, deviceId=6, source=0x1002, displayId=0 }
2021-03-09 13:17:59.895 4750-4750/ru.ok.technopolis.recyclerview.sample E/!!!: MotionEvent { action=ACTION_MOVE, actionButton=0, id[0]=0, x[0]=64.0, y[0]=75.0, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, classification=NONE, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=0, eventTime=75367768, downTime=75367698, deviceId=6, source=0x1002, displayId=0 }
2021-03-09 13:17:59.896 4750-4750/ru.ok.technopolis.recyclerview.sample E/!!!: MotionEvent { action=ACTION_UP, actionButton=0, id[0]=0, x[0]=64.0, y[0]=75.0, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, classification=NONE, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=0, eventTime=75367776, downTime=75367698, deviceId=6, source=0x1002, displayId=0 }
```

Теперь в нашем логе появились новые события: `ACTION_MOVE` и `ACTION_UP`.


## Что почитать

- [https://developer.android.com/guide/topics/ui/ui-events](https://developer.android.com/guide/topics/ui/ui-events)
- [https://developer.android.com/training/gestures/viewgroup](https://developer.android.com/training/gestures/viewgroup)
- [https://guides.codepath.com/android/gestures-and-touch-events](https://guides.codepath.com/android/gestures-and-touch-events)