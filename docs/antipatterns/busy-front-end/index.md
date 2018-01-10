---
title: "Антишаблон загруженности внешнего интерфейса"
description: "Выполнение асинхронных операций в большом количестве фоновых потоков может замедлить выполнение других задач переднего плана ресурсов."
author: dragon119
ms.date: 06/05/2017
ms.openlocfilehash: cedb80ddac5ceb1eb901455df3165993fd28a138
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2017
---
# <a name="busy-front-end-antipattern"></a>Антишаблон загруженности внешнего интерфейса

Выполнение асинхронных операций в большом количестве фоновых потоков может замедлить выполнение других задач переднего плана ресурсов, что приводит к уменьшению времени отклика до недопустимого уровня.

## <a name="problem-description"></a>Описание проблемы

Ресурсоемкие задачи могут увеличить время отклика на запросы пользователей и привести к высокой задержке. Один из способов уменьшения времени отклика заключается в разгрузке ресурсоемких задач в отдельный поток. Этот подход позволяет приложению реагировать во время обработки в фоновом режиме. Тем не менее задачи, выполняемые в фоновом потоке, по-прежнему потребляют ресурсы. Большое количество таких задач может замедлить выполнение потоков, обрабатывающих запросы.

> [!NOTE]
> Термин *ресурс* может охватывать множество понятий, таких как использование ЦП, заполнение памяти и операции ввода-вывода сети или диска.

Эта проблема обычно возникает, когда приложение разработано как монолитный фрагмент кода со всеми бизнес-логиками, объединенными на одном уровне с уровнем представления данных.

Ниже приведен пример кода ASP.NET, который демонстрирует эту проблему. Полный пример см. [здесь][code-sample].

```csharp
public class WorkInFrontEndController : ApiController
{
    [HttpPost]
    [Route("api/workinfrontend")]
    public HttpResponseMessage Post()
    {
        new Thread(() =>
        {
            //Simulate processing
            Thread.SpinWait(Int32.MaxValue / 100);
        }).Start();

        return Request.CreateResponse(HttpStatusCode.Accepted);
    }
}

public class UserProfileController : ApiController
{
    [HttpGet]
    [Route("api/userprofile/{id}")]
    public UserProfile Get(int id)
    {
        //Simulate processing
        return new UserProfile() { FirstName = "Alton", LastName = "Hudgens" };
    }
}
```

- Метод `Post` в контроллере `WorkInFrontEnd` реализует операцию HTTP POST. Эта операция моделирует продолжительную задачу с интенсивной нагрузкой ЦП. Задача выполняется в отдельном потоке. Это позволяет выполнить операцию POST быстрее.

- Метод `Get` в контроллере `UserProfile` реализует операцию HTTP GET. Этот метод требует меньше ресурсов ЦП.

Особое внимание следует уделить требованиям к ресурсам метода `Post`. Несмотря на то что задачи выполняются в фоновом потоке, они по-прежнему могут потреблять значительный объем ресурсов ЦП. Эти ресурсы используются и другими операциями, выполняемыми другими параллельно работающими пользователями. Если умеренное количество пользователей одновременно отправит этот запрос, это с большей вероятностью отрицательно повлияет на общую производительность, что приведет к снижению скорости выполнения всех операций. У пользователей, например, могут возникнуть значительные задержки в методе `Get`.

## <a name="how-to-fix-the-problem"></a>Как устранить проблему

Переместите процессы, требующие значительные ресурсы, в отдельную серверную часть. 

При этом подходе внешний интерфейс помещает ресурсоемкие задачи в очередь сообщений. Серверная часть выбирает эти задачи и асинхронно обрабатывает их. Очередь также позволяет выровнять нагрузку, выполняя буферизацию запросов серверной части. Если очередь становится слишком длинной, вы можете настроить автоматическое масштабирование серверной части.

Ниже приведена исправленная версия предыдущего примера кода. В этой версии метод `Post` помещает сообщение в очередь служебной шины. 

```csharp
public class WorkInBackgroundController : ApiController
{
    private static readonly QueueClient QueueClient;
    private static readonly string QueueName;
    private static readonly ServiceBusQueueHandler ServiceBusQueueHandler;

    public WorkInBackgroundController()
    {
        var serviceBusConnectionString = ...;
        QueueName = ...;
        ServiceBusQueueHandler = new ServiceBusQueueHandler(serviceBusConnectionString);
        QueueClient = ServiceBusQueueHandler.GetQueueClientAsync(QueueName).Result;
    }

    [HttpPost]
    [Route("api/workinbackground")]
    public async Task<long> Post()
    {
        return await ServiceBusQueuehandler.AddWorkLoadToQueueAsync(QueueClient, QueueName, 0);
    }
}
```

Серверная часть извлекает эти сообщения и обрабатывает их.

```csharp
public async Task RunAsync(CancellationToken cancellationToken)
{
    this._queueClient.OnMessageAsync(
        // This lambda is invoked for each message received.
        async (receivedMessage) =>
        {
            try
            {
                // Simulate processing of message
                Thread.SpinWait(Int32.Maxvalue / 1000);

                await receivedMessage.CompleteAsync();
            }
            catch
            {
                receivedMessage.Abandon();
            }
        });
}
```

## <a name="considerations"></a>Рекомендации

- Этот подход усложняет архитектуру приложения. При обработке помещения сообщений в очередь и удаления их из нее следует соблюдать осторожность, чтобы не потерять запросы в случае сбоя.
- Приложение зависит от дополнительной службы обработки очереди сообщений.
- Среда обработки должна обладать достаточной масштабируемостью, чтобы обработать ожидаемую рабочую нагрузку и обеспечить соответствие целевым показателям пропускной способности.
- Хотя такой подход должен повысить общую скорость реагирования, выполнение задач, перемещаемых в серверную часть, может занять больше времени. 

## <a name="how-to-detect-the-problem"></a>Как определить проблему

Если внешний интерфейс загружен, при выполнении ресурсоемких задач возникают большие задержки. Пользователи могут сообщить об увеличении времени отклика или ошибках, вызванных завершением времени ожидания служб. Эти сбои также могут возвращать ошибки HTTP 500 (внутренняя ошибка сервера) или HTTP 503 (служба недоступна). Изучите журналы событий веб-сервера, которые, скорее всего, содержат более подробные сведения о причинах и обстоятельствах возникновения ошибок.

Чтобы определить эту проблему, сделайте следующее:

1. Выполните мониторинг рабочей системы, чтобы определить точки замедления времени отклика.
2. Проверьте данные телеметрии, собранные в этих точках, чтобы определить, выполняемые операции и используемые ресурсы. 
3. Определите связь между увеличением времени отклика и количеством выполняемых в это время операций, а также их сочетанием.
4. Выполните нагрузочное тестирование каждой подозрительной операции, чтобы определить, какие из них потребляют ресурсы и замедляют выполнение других операций. 
5. Просмотрите исходный код этих операций, чтобы определить причину избыточного потребления ресурсов.

## <a name="example-diagnosis"></a>Пример диагностики 

В следующих разделах эти шаги применяются к примеру приложения, описанному ранее.

### <a name="identify-points-of-slowdown"></a>Определение точек замедления

Выполните инструментирование каждого метода, чтобы отследить продолжительность каждого запроса и потребляемые ресурсы. Затем выполните мониторинг приложения в рабочей среде. Это может обеспечить полное представление того, как запросы конкурируют друг с другом. Во время стрессовых периодов медленно выполняемые, нуждающиеся в ресурсах запросы, скорее всего, повлияют на другие операции. Это поведение, а также соответствующие проблемы с производительностью можно предотвратить, выполняя мониторинг системы.

На снимке экрана ниже показана панель мониторинга. (При тестировании мы использовали [AppDynamics].) Изначально в системе наблюдается небольшая нагрузка. Затем пользователи запрашивают метод GET `UserProfile`. Производительность оставалась довольно хорошей, пока другие пользователи не начали отправлять запросы к методу POST `WorkInFrontEnd`. На этом этапе значительно увеличилось время отклика (первая стрелка). Время отклика улучшилось только после снижения количества запросов к контроллеру `WorkInFrontEnd` (вторая стрелка).

![Панель бизнес-транзакций AppDynamics, на которой отображается время отклика всех запросов при использовании контроллера WorkInFrontEnd][AppDynamics-Transactions-Front-End-Requests]

### <a name="examine-telemetry-data-and-find-correlations"></a>Изучение данных телеметрии и поиск связи

На следующем снимке экрана показаны некоторые метрики, собранные для мониторинга использования ресурсов на притяжении того же периода времени. Сначала несколько пользователей вошли в систему. Когда к ней подключается больше пользователей, загрузка ЦП становится очень высокой (100 %). Кроме того, обратите внимание, что с увеличением использования ресурсов ЦП сначала скорость сетевых операций ввода-вывода поднимается. Но когда использование ЦП достигает пиковых значений, скорость сетевых операций ввода-вывода снижается. Это связано с тем, что система может обрабатывать только относительно небольшое число запросов, когда ЦП работает на полную мощность. Когда пользователи отключаются, нагрузка на ЦП уменьшается.

![Метрики использования ресурсов сети и ЦП в AppDynamics][AppDynamics-Metrics-Front-End-Requests]

На этом этапе, скорее всего, более тщательно следует проанализировать метод `Post` в контроллере `WorkInFrontEnd`. Чтобы подтвердить эту версию, в управляемой среде нужно провести дальнейшую работу.

### <a name="perform-load-testing"></a>Выполнение нагрузочного тестирования 

Следующий шаг — провести тестирования в управляемой среде. Например, можно выполнить ряд нагрузочных тестов, которые сначала включают каждый запрос, а затем исключают их, чтобы оценить влияние.

На графике ниже показаны результаты нагрузочного теста, выполненного в такой же облачной службе, что и в предыдущих тестах. При тестировании применялась постоянная нагрузка с 500 пользователями, выполняющими операцию `Get` в контроллере `UserProfile`, а также пошаговая нагрузка с пользователями, выполняющими операцию `Post` в контроллере `WorkInFrontEnd`. 

![Начальные результаты нагрузочного теста контроллера WorkInFrontEnd][Initial-Load-Test-Results-Front-End]

Изначально пошаговая нагрузка равна нулю, поэтому только активные пользователи выполняют запросы `UserProfile`. Система может отвечать примерно на 500 запросов в секунду. Через 60 секунд нагрузку увеличили на 100 дополнительных пользователей, которые начинают отправлять запросы POST к контроллеру `WorkInFrontEnd`. Почти мгновенно рабочая нагрузка, отправленная к контроллеру `UserProfile`, снижается до около 150 запросов в секунду. Это связано с функционированием средства выполнения нагрузочных тестов. Он ожидает ответ перед отправкой следующего запроса, поэтому чем больше время ответа, тем ниже частота запросов.

При увеличении количества пользователей, отправляющих запросы POST к контроллеру `WorkInFrontEnd`, скорость ответа контроллера `UserProfile` продолжает снижаться. Но обратите внимание, что количество запросов, обрабатываемых контроллером `WorkInFrontEnd`, остается относительно постоянным. Насыщенность системы становится очевидной, так как общее число обоих запросов становится постоянным, но имеет минимальный уровень.


### <a name="review-the-source-code"></a>Просмотр исходного кода

Последний шаг — просмотреть исходный код. Группе разработчиков стало известно, что выполнение метода `Post` может занять значительное время, поэтому при исходной реализации используется отдельный поток. Это позволило решить насущную проблему, так как метод `Post` не блокирует выполнение длительных задач.

Однако операция, выполняемая этим методом, по-прежнему зависит от ЦП, памяти и других ресурсов. Если выполнить этот процесс асинхронно, это может фактически снизить производительность, так как пользователи смогут одновременно активировать большое количество этих операций без какого-либо контроля. Число потоков, которое можно запустить на сервере, ограничено. При превышении этого предела, когда приложение попытается создать поток, скорее всего, возникнет исключение.

> [!NOTE]
> Это не значит, что следует избегать асинхронных операций. В сетевых вызовах рекомендуется применять асинхронные методы с использованием оператора await (см. антишаблон [синхронных операций ввода-вывода][sync-io]). Проблема здесь в том, что нагружающая ЦП задача может возникнуть в другом потоке. 

### <a name="implement-the-solution-and-verify-the-result"></a>Реализация решения и проверка результатов

На снимке экрана ниже приведены показатели производительности после реализации решения. Нагрузка совпадает с приведенной выше, но теперь время ответа контроллера `UserProfile` значительно выше. За аналогичный период количество запросов выросло с 2759 до 23 565. 

![Панель бизнес-транзакций AppDynamics, на которой отображается время отклика всех запросов при использовании контроллера WorkInBackground][AppDynamics-Transactions-Background-Requests]

Обратите внимание, что контроллер `WorkInBackground` также обработал гораздо большее количество запросов. Однако прямое сравнение в этом случае невозможно сделать, так как операции, выполняемые в этом контроллере, сильно отличаются от исходного кода. Новая версия просто добавляет запрос в очередь, а не выполняет продолжительное вычисление. Самое главное, что этот метод больше не влияет на производительность всей системы под нагрузкой.

Показатели использования ресурсов ЦП и сети также улучшились. Показатель использования ЦП никогда не достигал 100 %, а количество обработанных сетевых запросов значительно выросло и не снижалось до падения рабочей нагрузки.

![Метрики использования ресурсов сети и ЦП контроллера WorkInBackground в AppDynamics][AppDynamics-Metrics-Background-Requests]

На графике ниже показаны результаты нагрузочного теста. Общее количество обработанных запросов значительно увеличилось по сравнению с предыдущими тестами.

![Результаты нагрузочного теста контроллера BackgroundImageProcessing][Load-Test-Results-Background]

## <a name="related-guidance"></a>Связанные руководства

- [Автомасштабирование][autoscaling]
- [Фоновые задания][background-jobs]
- [Шаблон балансировки нагрузки на основе очередей][load-leveling]
- [Web Queue Worker architecture style][web-queue-worker] (Стиль архитектуры с применением веб-интерфейса, рабочего потока и очереди)

[AppDyanamics]: https://www.appdynamics.com/
[autoscaling]: /azure/architecture/best-practices/auto-scaling
[background-jobs]: /azure/architecture/best-practices/background-jobs
[code-sample]: https://github.com/mspnp/performance-optimization/tree/master/BusyFrontEnd
[fullDemonstrationOfSolution]: https://github.com/mspnp/performance-optimization/tree/master/BusyFrontEnd
[load-leveling]: /azure/architecture/patterns/queue-based-load-leveling
[sync-io]: ../synchronous-io/index.md
[web-queue-worker]: /azure/architecture/guide/architecture-styles/web-queue-worker

[WebJobs]: http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx
[ComputePartitioning]: https://msdn.microsoft.com/library/dn589773.aspx
[ServiceBusQueues]: https://msdn.microsoft.com/library/azure/hh367516.aspx
[AppDynamics-Transactions-Front-End-Requests]: ./_images/AppDynamicsPerformanceStats.jpg
[AppDynamics-Metrics-Front-End-Requests]: ./_images/AppDynamicsFrontEndMetrics.jpg
[Initial-Load-Test-Results-Front-End]: ./_images/InitialLoadTestResultsFrontEnd.jpg
[AppDynamics-Transactions-Background-Requests]: ./_images/AppDynamicsBackgroundPerformanceStats.jpg
[AppDynamics-Metrics-Background-Requests]: ./_images/AppDynamicsBackgroundMetrics.jpg
[Load-Test-Results-Background]: ./_images/LoadTestResultsBackground.jpg

