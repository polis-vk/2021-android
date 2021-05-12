---
layout: default
title: MessageQueue, Looper, Handler
highlight: true
---

В этом уроке мы узнаем, что такое MessageQueue, Looper и Handler.

##  MessageQueue

В качестве реализации очереди сообщений Android предлагает использовать класс [MessageQueue] (https://developer.android.com/reference/android/os/MessageQueue.html). Это низкоуровневый `интерфейс`, который, в большинстве случаев, напрямую не используется в приложении.

`MessageQueue` оперирует экземплярами класса [Message] (https://developer.android.com/reference/android/os/Message.html).

К экземпляру класса `Message` можно добавить `payload`: данные любого типа или объект типа [Runnable] (https://developer.android.com/reference/java/lang/Runnable.html):

```java
    ...

    final Message message = Message.obtain();
    //Добавляем строку к сообщению
    message.obj = "Payload";

    ...
```

Поскольку для экземпляров класса `Message` Android автоматически создаёт `pool` (реализованный прямо в классе `Message`), то для их инстанцирования рекомендуется использовать группу статических методов [Message.obtain(...)] (https://developer.android.com/reference/android/os/Message.html#obtain()) вместо явного создания:

```java
    ...

    //Не рекомендуется
    final Message message = new Message();

    ...
```

##  Looper

Класс [Looper] (https://developer.android.com/reference/android/os/Looper.html) представляет собой бесконечный цикл выборки сообщений, выполняющийся в потоке ([Thread] (https://developer.android.com/reference/java/lang/Thread.html)), к которому он добавлен.

По умолчанию, поток не имеет цикла выборки сообщений.

В качестве хранилища сообщений `Looper` использует экземпляр класса `MessageQueue`.

`Looper` отвечает за доставкау (диспетчеризацию) сообщений из `MessageQueue` своим получателям.

Класс `Looper` имеет три основных метода:

- статический метод [Looper.prepare()] (https://developer.android.com/reference/android/os/Looper.html#prepare()), который создаёт цикл выборки сообщений и, собственно, добавлет его к потоку, в котором этот метод вызывается
- блокирующий метод [Looper.loop()] (https://developer.android.com/reference/android/os/Looper.html#loop()), который стартует цикл выборки сообщений (собственно в котором и осуществляется их диспетчеризация)
- и, наконец, метод [Looper.quit()] (https://developer.android.com/reference/android/os/Looper.html#quit()), который завершает (прерывает) выполнение метода [Looper.loop()] (https://developer.android.com/reference/android/os/Looper.html#loop()).

К потоку можно добавить не более одного цикла выборки сообщений (попытка добавить ещё один приведёт к возникновению исключительной ситуации).

`Looper`, в подавляющем большинстве случаев, не используется для прямого доступа к своей `MessageQueue`, хотя такая возможность есть.

Ещё один полезный метод, предоставляемый классом `Looper` - [Looper.getThread()](https://developer.android.com/reference/android/os/Looper.html#getThread()). Метод вернёт поток, к кторому `Looper` был добавлен или `null`.

## Handler

Класс [Handler] (https://developer.android.com/reference/android/os/Handler.html) предназначен для отправки сообщений (`Message`) и объектов типа `Runnable` для обработки в потоке, к которому добавлен `Looper`:

```java
    ...

    public Looper getHandlerLooper() {
        // return a looper that's has already been added to the thread
    }

    final Looper looper = getHandlerLooper();
    final Handler handler = new Handler(looper) {
        // Этот метод будет выполнен в потоке, к которому добавлен looper
        @Override
        public void handleMessage(Message msg) {
            // handle incoming message
        }
    };

    ...    
```

Или создаём `Handler`, кторый будет обрабатывать сообщения в главном (`UI`) потоке:

```java
    ...

    final Looper looper = Looper.getMainLooper();
    final Handler handler = new Handler(looper) {
        // Этот метод будет выполнен в главном потоке
        @Override
        public void handleMessage(Message msg) {
            // handle incoming message
        }
    };

    ...
```

Или создаём `Handler`, кторый будет обрабатывать сообщения в текущем потоке (т.е. в потоке, в котором создаётся `Handler`):

```java
    ...

    // Используется неявный вызов метода Looper.myLooper()
    final Handler handler = new Handler() {
        // Этот метод будет выполнен в потоке, в котором создан handler
        @Override
        public void handleMessage(Message msg) {
            // handle incoming message
        }
    };

    ...
```

Попытка создать Handler без цикла выборки сообщений недопустима.
Другими словами, при создании экземпляра класса `Handler` ему необходимо укзать `Looper` (явно или неявно), который был добавлен к потоку.

Отправляем сообщение:

```java
    ...

    final Message message = Message.obtain();
    // Указываем уникальный идентификатор сообщения
    message.what = 0;
    // Добавляем строку к сообщению
    message.obj = "Payload";

    // handler - получатель
    handler.sendMessage(message);

    ...    
```

Упрощаем `создание` и отправку сообщения:

```java
    ...

    // handler - получатель - он же target
    handler.obtainMessage(0, "Payload").sendToTarget();

    ...    
```

Отправляем `Runnable`:

```java
    ...

    // handler - получатель
    // Неявно создаётся Message, к которому добавляется указанный Runnable
    handler.post(
        new Runnable() {
            @Override
            public void run() {
                // do something
            }
        });

    ...    
```
