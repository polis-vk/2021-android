---
layout: default
title: Service, IntentService, JobIntentService
highlight: true
---

В этом уроке мы узнаем, что такое Service, IntentService и JobIntentService.

## Service

Класс [Service](https://developer.android.com/reference/android/app/Service.html) является одним из основных компонентов Android приложения и, в общем случае, предназначен для выполнения операций в фоне (`background`), например: проигрывание музыки, синхронизация данных приложения с сервером, длительные сетевые запросы и т.д.
Более того, сервис непосредственно не связан с пользовательским интерфейсом приложения (`UI`) и его жизненным циклом.

Как и у [Activity](https://developer.android.com/reference/android/app/Activity) жизненный цикл сервиса 'привязан' к главному потоку процесса. Это означает, что для выполнения своих задач, сервис должен испльзовать фоновые (`worker`) потоки в том или ином виде.

В качестве `обратной связи`, т.е. публикации результатов работы сервиса можно использовать различные механизмы, например: [BroadcastReceiver](https://developer.android.com/reference/android/content/BroadcastReceiver), [ResultReceiver](https://developer.android.com/reference/android/os/ResultReceiver) и т.д.

## IntentService

Класс [IntentService](https://developer.android.com/reference/android/app/IntentService.html) является сервисом, который обрабатывает `входящие` задачи последовательно (см. [AsyncTask]()), в порядке их поступления и использует для этого [HandlerThread](https://developer.android.com/reference/android/os/HandlerThread).

```java
public abstract class IntentService extends Service {
    ...

    private final class ServiceHandler extends Handler {
        public ServiceHandler(Looper looper) {
            super(looper);
        }

        @Override
        public void handleMessage(Message msg) {
            onHandleIntent((Intent)msg.obj);
            ...
        }
    }

    ...

    @Override
    public void onCreate() {
        // TODO: It would be nice to have an option to hold a partial wakelock
        // during processing, and to have a static startService(Context, Intent)
        // method that would launch the service & hand off a wakelock.

        super.onCreate();
        HandlerThread thread = new HandlerThread("IntentService[" + mName + "]");
        thread.start();

        mServiceLooper = thread.getLooper();
        mServiceHandler = new ServiceHandler(mServiceLooper);
    }

    @Override
    public void onStart(Intent intent, int startId) {
        Message msg = mServiceHandler.obtainMessage();
        msg.arg1 = startId;
        msg.obj = intent;
        mServiceHandler.sendMessage(msg);
    }

    ...
}
```

## JobIntentService

Класс [JobIntentService](https://developer.android.com/reference/android/support/v4/app/JobIntentService.html), фактически, является тем же самым `IntentService`-ом, но контролируется (управляется) планировщиком задач - [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler).

Для выполнения задач используется [AsyncTask](https://developer.android.com/reference/android/os/AsyncTask).

Поскольку работу `JobIntentService` контролирует планировщик задач, то это позволяет несколько снизить расход батареи устройства и свести к минимуму ситуации, когда устройство не может перейти в `режим сна - Doze mode` (что опять же влияет на экономию заряда батареи).

## Что почитать

- [Optimize for Doze and App Standby](https://developer.android.com/training/monitoring-device-state/doze-standby)
