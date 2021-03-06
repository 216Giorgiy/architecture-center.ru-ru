---
title: Шаблон очереди с приоритетом
titleSuffix: Cloud Design Patterns
description: Запросы, отправляемые в службу, получают разные приоритеты. При этом запросы с высоким приоритетом принимаются и обрабатываются быстрее, чем запросы с низким приоритетом.
keywords: Конструктивный шаблон
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: b3cde9c59e7b4cd023369366ae69977b153eb805
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58244415"
---
# <a name="priority-queue-pattern"></a>Шаблон очереди с приоритетом

[!INCLUDE [header](../_includes/header.md)]

Назначайте приоритет запросам, отправляемым в службу, чтобы получать и обрабатывать запросы с высоким приоритетом быстрее, чем запросы с низким приоритетом. Этот шаблон полезен в приложениях, предлагающих отдельным клиентам различные гарантии на уровне обслуживания.

## <a name="context-and-problem"></a>Контекст и проблема

Приложения могут делегировать задачи другим службам, например для выполнения фоновой обработки или для интеграции с другими приложениями или службами. В облаке очередь сообщений обычно используется для делегирования задач фоновой обработки. Во многих случаях порядок запросов, полученных службой, не имеет значения. В некоторых случаях он важен для определения приоритета конкретных запросов. Эти запросы должны обрабатываться перед низкоприоритетными запросами, которые были отправлены приложением ранее.

## <a name="solution"></a>Решение

Очередь обычно является структурой, функционирующей по принципу FIFO ("первым поступил — первым обслужен"), а клиенты получают сообщения в том же порядке, в котором они были переданы в очередь. Тем не менее некоторые очереди сообщений поддерживают приоритет обмена сообщениями. Приложение, отправляющее сообщение, может присваивать приоритет, а сообщения в очереди будут автоматически переупорядочиваться, поэтому сообщения с более высоким приоритетом будут получены раньше, чем сообщения с более низким приоритетом. На рисунке показана очередь с приоритетом обмена сообщениями.

![Рисунок 1. Использование механизма организации очередей, который поддерживает определение приоритета сообщений](./_images/priority-queue-pattern.png)

> Большинство реализаций очереди сообщений поддерживает отправку сообщений нескольким клиентам (согласно [шаблону конкурирующих потребителей](./competing-consumers.md)), а число клиентских процессов масштабируется по требованию.

В системах, которые не поддерживают очереди сообщений на основе приоритета, альтернативным решением является создание отдельной очереди для каждого уровня приоритета. Приложение отвечает за отправку сообщений в соответствующую очередь. Каждая очередь может иметь отдельный пул потребителей. В очередях с более высоким приоритетом пул потребителей, работающих на более быстром оборудовании, может быть больше, чем в очереди с более низким приоритетом. На следующем рисунке показано использование отдельных очередей сообщений для каждого уровня приоритета.

![Рисунок 2. Использование отдельный очередей сообщений для каждого уровня приоритета](./_images/priority-queue-separate.png)

Вариация этой стратегии — один пул потребителей, который сначала проверяет сообщения в приоритетных очередях и только затем начинает извлекать сообщения из очередей с более низким приоритетом. Есть некоторые семантические различия между решением, которое использует один пул процессов потребителя (либо с одной очередью, которая поддерживает сообщения с разными приоритетами, либо с несколькими очередями, каждая из которых обрабатывает сообщения с одним приоритетом), и решением, которое использует несколько очередей с отдельным пулом для каждой очереди.

При использовании подхода с одним пулом сообщения с более высоким приоритетом всегда поступают и обрабатываются перед сообщениями с низким приоритетом. В теории сообщения с очень низким приоритетом могут постоянно замещаться и никогда не будут обработаны. При использовании нескольких пулов низкоприоритетные сообщения будут всегда обрабатываться, просто не так быстро, как сообщения с более высоким приоритетом (в зависимости от относительных размеров пулов и ресурсов, которые им доступны).

Использование механизма приоритета очередей предоставляет следующие преимущества:

- Он обеспечивает приложениям соответствие бизнес-требованиям, по которым нужны приоритеты в доступности или производительности, например за счет различных уровней обслуживания для определенных групп клиентов.

- Это может помочь сократить эксплуатационные расходы. При использовании одной очереди можно выполнить обратное масштабирование числа потребителей, если это необходимо. Сообщения с высоким приоритетом будут по-прежнему обрабатываться в первую очередь (хотя, возможно, и медленнее), а обработка низкоприоритетных сообщений может быть отложена на еще больший срок. Если реализован подход с несколькими очередями сообщений с отдельными пулами потребителей для каждой очереди, можно уменьшить пул потребителей для очередей с более низким приоритетом или даже приостановить обработку для очередей с очень низким приоритетом, остановив всех потребителей, которые ожидают передачу данных сообщений в этих очередях.

- Подход с несколькими очередями сообщений позволяет максимизировать производительность и масштабируемость приложений путем разделения сообщений по требованиям к обработке. Например, критически важные задачи могут иметь приоритет при обработке получателями, которые запускаются немедленно, в то время как менее важные фоновые задачи могут обрабатываться получателями, которые планируется запустить в менее загруженные периоды.

## <a name="issues-and-considerations"></a>Проблемы и рекомендации

При принятии решения о реализации этого шаблона необходимо учитывать следующие моменты.

Определяйте приоритеты в контексте решения. Например, высокий приоритет может означать, что сообщения могут обрабатываться в течение 10 секунд. Идентифицируйте требования для обработки элементов с высоким приоритетом и ресурсы, которые следует выделить на выполнение этих требований.

Решите, должны ли все высокоприоритетные элементы обрабатываться перед любыми низкоприоритетными элементами. Если сообщения обрабатываются в одном пуле потребителей, нужно предоставить механизм выгрузки и приостановки задачи, которая обрабатывает сообщение с низким приоритетом, если сообщение с более высоким приоритетом становится доступным.

В подходе с несколькими очередями при использовании одного пула потребительских процессов, который прослушивается во всех очередях, а не в выделенном пуле потребителей для каждой очереди, потребитель должен применять алгоритм, который гарантирует, что сообщения из очередей с более высоким приоритетом всегда обслуживаются перед сообщениями из низкоприоритетных очередей.

Отслеживайте скорость обработки в очередях с высоким и низким приоритетами, чтобы гарантировать, что сообщения в этих очередях обрабатывались с ожидаемой скоростью.

Если нужно обеспечить обработку низкоприоритетных сообщений, необходимо реализовать подход, в котором предусмотрено несколько очередей сообщений с несколькими пулами потребителей. Кроме того, в очереди, которая поддерживает определение приоритетов сообщений, есть возможность динамически увеличивать приоритет сообщения в очереди по мере истечения его срока годности. Однако такой подход зависит от очереди сообщений, предоставляющих эту функцию.

Использование отдельной очереди для каждого приоритета сообщений лучше всего подходит для систем с небольшим количеством четко определенных приоритетов.

Система может определять приоритеты сообщений логически. Например, вместо явного отображения сообщений с высоким и низким приоритетом можно было бы назначить уровень "платный клиент" или "бесплатный клиент". В зависимости от бизнес-модели система может выделить больше ресурсов для обработки сообщений от платных клиентов, чем от бесплатных.

При проверке сообщений в очереди могут возникнуть финансовые затраты и затраты на обработку (в некоторых коммерческих системах обмена сообщениями при отправке или извлечении сообщения или запроса сообщений в очереди взимается небольшая плата). Плата увеличивается при проверке нескольких очередей.

Пул потребителей можно динамически настроить по длине очереди, которую обслуживает пул. Дополнительные сведения см. в [руководстве по автомасштабированию](https://msdn.microsoft.com/library/dn589774.aspx).

## <a name="when-to-use-this-pattern"></a>Когда следует использовать этот шаблон

Этот шаблон полезен в следующих ситуациях:

- система должна обработать несколько задач с различными приоритетами;

- различные пользователи и клиенты должны обслуживаться с разными приоритетами.

## <a name="example"></a>Пример

Microsoft Azure не предоставляет механизм организации очереди, который поддерживает автоматическое определение приоритетов сообщений с помощью сортировки. Однако предусмотрены разделы и подписки служебной шины Azure, которые поддерживают механизм организации очереди, предоставляющий фильтрацию сообщений и множество гибких возможностей, которые идеально подходят для использования в большинстве реализаций очереди с приоритетом.

В решении Azure можно реализовать раздел служебной шины, в который приложение может отправлять сообщения, также как в очередь. Сообщения могут содержать метаданные в форме пользовательских свойств, определенных приложением. Подписки служебной шины можно связать с разделом. Эти подписки могут фильтровать сообщения на основе их свойств. Когда приложение отправляет сообщение в раздел, сообщение направляется в нужную подписку, где оно может быть прочитано. Потребитель обрабатывает сообщения, полученные из подписки, используя ту же семантику, что и очередь сообщений (подписка — это логическая очередь). На следующем рисунке показана реализация очереди с приоритетом с помощью разделов и подписок служебной шины.

![Рисунок 3. Реализация очереди с приоритетом при помощи разделов и подписок служебной шины](./_images/priority-queue-service-bus.png)

На приведенном выше рисунке приложение создает несколько сообщений и присваивает настраиваемое свойство, называемое `Priority` в каждом сообщении со значением `High` или `Low`. Приложение отправляет эти сообщения в раздел. Раздел содержит две связанные подписки, которые фильтруют сообщения с помощью проверки свойства `Priority`. Одна подписка принимает сообщения, где свойство `Priority` имеет значение `High`, а другая — где свойство `Priority` имеет значение `Low`. Пул потребителей считывает сообщения из каждой подписки. Подписка с высоким приоритетом имеет больший пул, поэтому потребители могут работать на более мощных компьютерах с большим количеством ресурсов, чем потребители в низкоприоритетном пуле.

Обратите внимание, что в этом примере нет ничего особенного в обозначении сообщений с высоким и низким приоритетом. Это просто метки, указанные в качестве свойств в каждом сообщении и используемые для направления сообщений в определенную подписку. Если необходимы дополнительные приоритеты, можно относительно просто создать дополнительные подписки и пулы потребительских процессов для обработки этих приоритетов.

Решение PriorityQueue, доступное на [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/priority-queue), содержит реализацию этого подхода. Это решение содержит проекты двух рабочих ролей с именем `PriorityQueue.High` и `PriorityQueue.Low`. Эти рабочие роли наследуются от класса `PriorityWorkerRole`, который содержит функции для подключения к указанной подписке в методе `OnStart`.

Рабочие роли `PriorityQueue.High` и `PriorityQueue.Low` подключаются к разным подпискам, которые определяются их параметрами конфигурации. Администратор может настраивать различное количество ролей, которые нужно выполнить. Обычно у вас будет больше экземпляров рабочей роли `PriorityQueue.High`, чем рабочей роли `PriorityQueue.Low`.

Метод `Run` в классе `PriorityWorkerRole` упорядочивает виртуальный метод `ProcessMessage` (также определенный в классе `PriorityWorkerRole`) для выполнения для каждого сообщения, полученного в очереди. В следующем коде приведены методы `Run` и `ProcessMessage`. Класс `QueueManager`, определенный в проекте PriorityQueue.Shared, предоставляет вспомогательные методы для использования очередей служебной шины Azure.

```csharp
public class PriorityWorkerRole : RoleEntryPoint
{
  private QueueManager queueManager;
  ...

  public override void Run()
  {
    // Start listening for messages on the subscription.
    var subscriptionName = CloudConfigurationManager.GetSetting("SubscriptionName");
    this.queueManager.ReceiveMessages(subscriptionName, this.ProcessMessage);
    ...;
  }
  ...

  protected virtual async Task ProcessMessage(BrokeredMessage message)
  {
    // Simulating processing.
    await Task.Delay(TimeSpan.FromSeconds(2));
  }
}
```

Рабочие роли `PriorityQueue.High` и `PriorityQueue.Low` перезаписывают функции по умолчанию метода `ProcessMessage`. В коде ниже показан метод `ProcessMessage` рабочей роли `PriorityQueue.High`.

```csharp
protected override async Task ProcessMessage(BrokeredMessage message)
{
  // Simulate message processing for High priority messages.
  await base.ProcessMessage(message);
  Trace.TraceInformation("High priority message processed by " +
    RoleEnvironment.CurrentRoleInstance.Id + " MessageId: " + message.MessageId);
}
```

Когда приложение отправляет сообщение в раздел, связанный с подписками, используемыми в рабочих ролях `PriorityQueue.High` и `PriorityQueue.Low`, оно указывает приоритет с помощью настраиваемого свойства `Priority`, как показано в следующем примере кода. Код (реализованный в классе `WorkerRole` в проекте PriorityQueue.Sender) использует вспомогательный метод `SendBatchAsync` класса `QueueManager` для отправки сообщений в раздел в пакетах.

```csharp
// Send a low priority batch.
var lowMessages = new List<BrokeredMessage>();

for (int i = 0; i < 10; i++)
{
  var message = new BrokeredMessage() { MessageId = Guid.NewGuid().ToString() };
  message.Properties["Priority"] = Priority.Low;
  lowMessages.Add(message);
}

this.queueManager.SendBatchAsync(lowMessages).Wait();
...

// Send a high priority batch.
var highMessages = new List<BrokeredMessage>();

for (int i = 0; i < 10; i++)
{
  var message = new BrokeredMessage() { MessageId = Guid.NewGuid().ToString() };
  message.Properties["Priority"] = Priority.High;
  highMessages.Add(message);
}

this.queueManager.SendBatchAsync(highMessages).Wait();
```

## <a name="related-patterns-and-guidance"></a>Связанные шаблоны и рекомендации

При реализации этого шаблона следует принять во внимание следующие шаблоны и рекомендации.

- Пример, демонстрирующий этот шаблон, можно найти на сайте [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/priority-queue).

- [Руководство по асинхронному обмену сообщениями](https://msdn.microsoft.com/library/dn589781.aspx). Службе потребителя, которая обрабатывает запрос, может потребоваться отправить ответ на экземпляр приложения, отправившего запрос. Позволяет получить сведения о стратегиях, которые можно использовать для обмена сообщениями (запрос — ответ).

- [Шаблон конкурирующих потребителей](./competing-consumers.md). Чтобы увеличить пропускную способность очередей, можно сделать так, чтобы несколько потребителей ожидали передачи данных в одной очереди и обрабатывали задачи параллельно. Эти потребители будут конкурировать за сообщения, но только один должен иметь возможность обрабатывать каждое сообщение. Позволяет получить дополнительные сведения о преимуществах и недостатках реализации этого метода.

- [Throttling pattern](./throttling.md) (Шаблон регулирования). Регулирование можно реализовать с помощью очередей. Приоритетные сообщения могут использоваться, чтобы запросы от критически важных приложений или приложений, выполняемых ценными клиентами, получали приоритет над запросами от менее важных приложений.

- [Autoscaling](https://msdn.microsoft.com/library/dn589774.aspx) (Автомасштабирование). В зависимости от длины очереди можно масштабировать размеры пула процессов пользователя, обрабатывающего очередь. Эта стратегия помогает повысить производительность, особенно для пулов, обрабатывающих сообщения с высоким приоритетом.

- [Enterprise Integration Patterns with Service Bus (Part 2)](https://abhishekrlal.com/2013/01/11/enterprise-integration-patterns-with-service-bus-part-2/) (Шаблоны интеграции Enterprise со служебной шиной) в блоге Абхишека Лала (Abhishek Lal).
