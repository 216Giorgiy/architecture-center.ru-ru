---
title: "Анализ режима сбоя"
description: "Эта статья содержит сведения о выполнении анализа режима сбоя для облачных решений на платформе Azure."
author: MikeWasson
ms.date: 03/24/2017
ms.custom: resiliency
pnp.series.title: Design for Resiliency
ms.openlocfilehash: 09d09468eebe5c6fe1c9cdab14e142ff46cf0b25
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2017
---
# <a name="failure-mode-analysis"></a>Анализ режима сбоя
[!INCLUDE [header](../_includes/header.md)]

Анализ режимов сбоя (FMA) — это процесс реализации устойчивости в системе за счет определения возможных точек сбоя в системе. FMA должна быть частью архитектуры и выполняться на этапе разработки, чтобы вы могли включить функцию восстановления после сбоев в систему с самого начала.

Ниже описана общая процедура для выполнения FMA:

1. Определите все компоненты в системе. Включите внешние зависимости, например поставщики удостоверений, сторонние службы и т. д.   
2. Для каждого компонента определите потенциальные сбои, которые могут произойти. Один компонент может иметь несколько режимов сбоя. Например, следует рассматривать ошибки чтения и записи отдельно, так как их влияние и способы решения будут отличаться.
3. Оценивайте каждый режим сбоя в соответствии с его общим уровнем риска. Учитывайте следующие факторы:  

   * Какова вероятность возникновения сбоя? Возникает ли он очень часто или крайне редко? Вам не нужны точные показатели, главное — это назначить приоритет.
   * Каково влияние на работу приложения с точки зрения доступности, потери данных, денежных расходов и нарушения работы организации?
4. Для каждого режима сбоя определите, как приложение будет реагировать на эти сбои и восстанавливаться после них. Оцените компромиссы в цене и сложности приложения.   

В качестве отправной точки для процесса FMA эта статья содержит каталог потенциальных режимов сбоя и способов их устранения. Каталог упорядочен по технологии или службе Azure, а также общей категории для проектирования уровня приложения. Каталог не является исчерпывающим, но охватывает множество основных служб Azure.

## <a name="app-service"></a>Служба приложений
### <a name="app-service-app-shuts-down"></a>Приложение службы приложений завершает работу.
**Обнаружение.** Возможные причины:

* Ожидаемое завершение работы

  * Оператор завершает работу приложения, например, с помощью портала Azure.
  * Приложение выгружено, так как оно бездействовало. (Только если параметр `Always On` отключен.)
* Непредвиденное завершение работы

  * Приложение аварийно завершает работу.
  * Экземпляр виртуальной машины службы приложений становится недоступным.

Журнал метода Application_End зарегистрирует завершение работы домена приложения (несерьезный сбой процесса). Это единственный способ регистрации завершения работы доменов приложения.

**Восстановление**

* Если завершение работы ожидалось, корректно завершите работу с помощь соответствующего события. Например, в ASP.NET используйте метод `Application_End`.
* Если приложение было выгружено из-за бездействия, оно будет автоматически перезагружено при следующем запросе. Тем не менее с вас будет взиматься плата за "холодный запуск".
* Чтобы избежать выгрузки приложения, когда оно бездействует, включите параметр `Always On` в веб-приложении. Ознакомьтесь со статьей [Настройка веб-приложений в службе приложений Azure][app-service-configure].
* Чтобы оператор не завершал работу приложения, установите блокировку ресурсов с уровнем `ReadOnly`. Ознакомьтесь со статьей [Блокировка ресурсов для предотвращения непредвиденных изменений][rm-locks].
* Если происходит сбой приложения или виртуальная машина службы приложений становится недоступной, служба приложений автоматически перезагружает приложение.

**Диагностика.** Журналы приложений и веб-сервера. Ознакомьтесь со статьей [Включение ведения журнала диагностики для веб-приложений в службе приложений Azure][app-service-logging].

### <a name="a-particular-user-repeatedly-makes-bad-requests-or-overloads-the-system"></a>Конкретный пользователь многократно выполняет неправильные запросы или перегружает систему.
**Обнаружение.** Выполните проверку подлинности пользователей и включите идентификаторы пользователей в журналы приложений.

**Восстановление**

* Выполните регулирование запросов от пользователя с помощью [службы управления API Azure][api-management]. Ознакомьтесь со статьей [Расширенное регулирование запросов с помощью API Management][api-management-throttling].
* Заблокируйте пользователя.

**Диагностика.** Регистрируйте все запросы проверки подлинности.

### <a name="a-bad-update-was-deployed"></a>Развернуто неправильное обновление.
**Обнаружение.** Выполните мониторинг работоспособности приложения с помощью портала Azure (ознакомьтесь со статьей [Мониторинг производительности веб-приложения Azure][app-insights-web-apps]) или реализуйте [шаблон мониторинга конечной точки работоспособности][health-endpoint-monitoring-pattern].

**Восстановление**. Используйте несколько [слотов развертывания][app-service-slots] и выполните откат до последнего известного корректного развертывания. Дополнительные сведения см. в статье [Basic web application][ra-web-apps-basic] (Базовое веб-приложение).

## <a name="azure-active-directory"></a>Azure Active Directory
### <a name="openid-connect-oidc-authentication-fails"></a>Проверка подлинности OpenID Connect (OIDC) завершается сбоем.
**Обнаружение.** К возможным режимам сбоев относится следующее:

1. Azure AD недоступен, или не удается получить к нему доступ из-за сбоя сети. Перенаправление на конечную точку проверки подлинности завершается сбоем, и ПО промежуточного слоя OIDC выдает исключение.
2. Клиент Azure AD не существует. При перенаправлении на конечную точку проверки подлинности возвращается код ошибки HTTP и ПО промежуточного слоя OIDC выдает исключение.
3. Пользователь не может выполнить проверку подлинности. Стратегия обнаружения не нужна. Azure AD обрабатывает ошибки входа.

**Восстановление**

1. Перехват необработанных исключений из ПО промежуточного слоя.
2. Обработка событий `AuthenticationFailed`.
3. Перенаправление пользователя на страницу ошибки.
4. Выполнение повторного подключения пользователем.

## <a name="azure-search"></a>поиск Azure;
### <a name="writing-data-to-azure-search-fails"></a>Запись данных в службу поиска Azure завершается сбоем.
**Обнаружение.** Перехватите ошибки `Microsoft.Rest.Azure.CloudException`.

**Восстановление**

[Пакет SDK для .NET службы поиска][search-sdk] автоматически осуществляет новую попытку после временного сбоя. Исключения, выдаваемые клиентским пакетом SDK, должны рассматриваться как повторяющиеся ошибки.

Политика повтора по умолчанию использует стратегию экспоненциальной отсрочки. Чтобы использовать другую политику повтора, вызовите `SetRetryPolicy` в классе `SearchIndexClient` или `SearchServiceClient`. Дополнительные сведения см. в документации по [автоматическим повторам][auto-rest-client-retry].

**Диагностика.** Воспользуйтесь [аналитикой поискового трафика][search-analytics].

### <a name="reading-data-from-azure-search-fails"></a>Считывание данных из службы поиска Azure завершается сбоем.
**Обнаружение.** Перехватите ошибки `Microsoft.Rest.Azure.CloudException`.

**Восстановление**

[Пакет SDK для .NET службы поиска][search-sdk] автоматически осуществляет новую попытку после временного сбоя. Исключения, выдаваемые клиентским пакетом SDK, должны рассматриваться как повторяющиеся ошибки.

Политика повтора по умолчанию использует стратегию экспоненциальной отсрочки. Чтобы использовать другую политику повтора, вызовите `SetRetryPolicy` в классе `SearchIndexClient` или `SearchServiceClient`. Дополнительные сведения см. в документации по [автоматическим повторам][auto-rest-client-retry].

**Диагностика.** Воспользуйтесь [аналитикой поискового трафика][search-analytics].

## <a name="cassandra"></a>Cassandra
### <a name="reading-or-writing-to-a-node-fails"></a>Чтение или запись в узле завершается сбоем.
**Обнаружение.** Перехватите исключение. Для клиентов .NET это, как правило, будет `System.Web.HttpException`. Другой клиент может иметь другие типы исключений.  Дополнительные сведения см. в записи блога [Cassandra error handling done right](http://www.datastax.com/dev/blog/cassandra-error-handling-done-right) (Правильная обработка ошибок Cassandra).

**Восстановление**

* Каждый [клиент Cassandra](https://wiki.apache.org/cassandra/ClientOptions) имеет собственные политики повтора и возможности. Дополнительные сведения см. в записи блога [Cassandra error handling done right][cassandra-error-handling] (Правильная обработка ошибок Cassandra).
* Используйте развертывание с поддержкой стоек с узлами данных, распределенными между доменами сбоя.
* Выполните развертывание в несколько регионов, используя согласованность локального кворума. При возникновении ошибки, которая не является временной, выполните отработку отказа в другой регион.

**Диагностика.** Журналы приложений

## <a name="cloud-service"></a>Облачная служба
### <a name="web-or-worker-roles-are-unexpectedlybeing-shut-down"></a>Веб-роли или рабочие роли неожиданно завершают работу.
**Обнаружение.** Появляется событие [RoleEnvironment.Stopping][RoleEnvironment.Stopping].

**Восстановление**. Переопределите метод [RoleEntryPoint.OnStop][RoleEntryPoint.OnStop], чтобы выполнить корректную очистку. Дополнительные сведения см. в записи блога [The Right Way to Handle Azure OnStop Events][onstop-events] (Правильный способ обработки событий Azure OnStop).

## <a name="cosmos-db"></a>База данных Cosmos 
### <a name="reading-data-fails"></a>Считывание данных завершается сбоем.
**Обнаружение.** Перехватите `System.Net.Http.HttpRequestException` или `Microsoft.Azure.Documents.DocumentClientException`.

**Восстановление**

* Пакет SDK автоматически осуществляет новую попытку после сбоя. Чтобы задать количество повторных попыток и максимальное время ожидания, настройте свойство `ConnectionPolicy.RetryOptions`. Исключения клиента находятся за пределами действия политики повтора или не являются временными ошибками.
* Если Cosmos DB выполняет для клиента регулирование, возвращается ошибка HTTP 429. Проверьте код состояния в `DocumentClientException`. Если вы постоянно получаете ошибку 429, попробуйте увеличить значение пропускной способности коллекции.
    * При использовании API MongoDB служба возвращает код ошибки 16500 при регулировании количества запросов.
* Реплицируйте базу данных Cosmos DB в двух или более регионах. Все реплики доступны для чтения. Укажите параметр `PreferredLocations` с помощью клиентских пакетов SDK. Это упорядоченный список регионов Azure. Все операции чтения будут отправляться в первый доступный регион из списка. Если запрос завершается сбоем, клиент попытается отправить операции в другие регионы в списке в указанном порядке. Дополнительные сведения см. в статье [Как настроить глобальное распределение Azure Cosmos DB с помощью API DocumentDB][docdb-multi-region].

**Диагностика.** Регистрируйте все ошибки на стороне клиента.

### <a name="writing-data-fails"></a>Запись данных завершается сбоем.
**Обнаружение.** Перехватите `System.Net.Http.HttpRequestException` или `Microsoft.Azure.Documents.DocumentClientException`.

**Восстановление**

* Пакет SDK автоматически осуществляет новую попытку после сбоя. Чтобы задать количество повторных попыток и максимальное время ожидания, настройте свойство `ConnectionPolicy.RetryOptions`. Исключения клиента находятся за пределами действия политики повтора или не являются временными ошибками.
* Если Cosmos DB выполняет для клиента регулирование, возвращается ошибка HTTP 429. Проверьте код состояния в `DocumentClientException`. Если вы постоянно получаете ошибку 429, попробуйте увеличить значение пропускной способности коллекции.
* Реплицируйте базу данных Cosmos DB в двух или более регионах. При сбое основного региона уровень другого региона будет повышен для записи. Вы также можете активировать отработку отказа вручную. Пакет SDK выполняет автоматическое обнаружение и маршрутизацию, поэтому код приложения продолжает работать после отработки отказа. Во время отработки отказа (обычно в минутах) операции записи будут выполняться дольше, так как пакет SDK ищет новый регион записи.
  Дополнительные сведения см. в статье [Как настроить глобальное распределение Azure Cosmos DB с помощью API DocumentDB][docdb-multi-region].
* В качестве резерва сохраните документ в резервной очереди и обработайте очередь позже.

**Диагностика.** Регистрируйте все ошибки на стороне клиента.

## <a name="elasticsearch"></a>Elasticsearch
### <a name="reading-data-from-elasticsearch-fails"></a>Считывание данных из Elasticsearch завершается сбоем.
**Обнаружение.** Перехватите соответствующее исключение для конкретного используемого [клиента Elasticsearch][elasticsearch-client].

**Восстановление**

* Используйте механизм повтора. Каждый клиент имеет собственные политики повтора.
* Разверните несколько узлов Elasticsearch и выполните репликацию для обеспечения высокого уровня доступности.

Дополнительные сведения см. в статье [Run Elasticsearch on Azure][elasticsearch-azure] (Запуск Elasticsearch в Azure).

**Диагностика.** Вы можете использовать средства мониторинга для Elasticsearch или регистрируйте все ошибки на стороне клиента с использованием полезных данных. Просмотрите раздел "Мониторинг" статьи [Running Elasticsearch on Azure][elasticsearch-azure] (Запуск Elasticsearch в Azure).

### <a name="writing-data-to-elasticsearch-fails"></a>Запись данных в Elasticsearch завершается сбоем.
**Обнаружение.** Перехватите соответствующее исключение для конкретного используемого [клиента Elasticsearch][elasticsearch-client].  

**Восстановление**

* Используйте механизм повтора. Каждый клиент имеет собственные политики повтора.
* Если пониженный уровень согласованности приложения допустим, рассмотрите возможность записи с помощью параметра `write_consistency` `quorum`.

Дополнительные сведения см. в статье [Run Elasticsearch on Azure][elasticsearch-azure] (Запуск Elasticsearch в Azure).

**Диагностика.** Вы можете использовать средства мониторинга для Elasticsearch или регистрируйте все ошибки на стороне клиента с использованием полезных данных. Просмотрите раздел "Мониторинг" статьи [Running Elasticsearch on Azure][elasticsearch-azure] (Запуск Elasticsearch в Azure).

## <a name="queue-storage"></a>Хранилище очередей
### <a name="writing-a-message-to-azure-queue-storage-fails-consistently"></a>Запись сообщения в хранилище очередей Azure постоянно завершается сбоем.
**Обнаружение.** После *N* повторных попыток операции записи по-прежнему завершаются сбоем.

**Восстановление**

* Сохраните данные в локальном кэше и выполните переадресацию записей в хранилище, когда служба станет доступной.
* Создайте дополнительную очередь и запишите в нее данные, если основная очередь недоступна.

**Диагностика.** Используйте [метрики хранения][storage-metrics].

### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>Приложение не может обработать конкретное сообщение из очереди.
**Обнаружение.** Зависит от приложения. Например, сообщение содержит недопустимые данные или по какой-то причине происходит сбой бизнес-логики.

**Восстановление**

Переместите сообщение в отдельную очередь. Запустите отдельный процесс для проверки сообщений в этой очереди.

Попробуйте использовать очереди сообщений служебной шины Azure, которые для этой цели предоставляют функции [очереди недоставленных сообщений][sb-dead-letter-queue].

> [!NOTE]
> Если вы используете очереди хранилища с веб-заданиями, пакет SDK для веб-заданий предоставляет встроенную обработку сообщений о сбое. Ознакомьтесь со статьей об [использовании пакета SDK веб-заданий для работы с хранилищем очередей Azure][sb-poison-message].

**Диагностика.** Воспользуйтесь данными журнала.

## <a name="redis-cache"></a>Кэш Redis
### <a name="reading-from-the-cache-fails"></a>Чтение из кэша завершается сбоем.
**Обнаружение.** Перехватите `StackExchange.Redis.RedisConnectionException`.

**Восстановление**

1. Повторите попытки в случае временного сбоя. Кэш Redis для Azure поддерживает встроенный механизм повторных попыток. Ознакомьтесь с разделом [Руководство по использованию механизма повторов для кэша Redis для Azure][redis-retry].
2. Обрабатывайте постоянные ошибки в качестве промахов кэша и переключитесь на исходный источник данных.

**Диагностика.** Выполните [диагностику кэша Redis][redis-monitor].

### <a name="writing-to-the-cache-fails"></a>Сбой при записи в кэш.
**Обнаружение.** Перехватите `StackExchange.Redis.RedisConnectionException`.

**Восстановление**

1. Повторите попытки в случае временного сбоя. Кэш Redis для Azure поддерживает встроенный механизм повторных попыток. Ознакомьтесь с разделом [Руководство по использованию механизма повторов для кэша Redis для Azure][redis-retry].
2. Если ошибка не временная, игнорируйте ее и позвольте другим транзакциям позже выполнить запись данных в кэш.

**Диагностика.** Выполните [диагностику кэша Redis][redis-monitor].

## <a name="sql-database"></a>База данных SQL
### <a name="cannot-connect-to-the-database-in-the-primary-region"></a>Не удается подключиться к базе данных в основном регионе.
**Обнаружение.** Сбой подключения.

**Восстановление**

Необходимое условие. База данных должна быть настроена для активной георепликации. Ознакомьтесь сто статьей [Обзор. Группы отработки отказа и активная георепликация][sql-db-replication].

* Для запросов выполните чтение из вторичной реплики.
* Для операций вставок и обновлений выполните отработку отказа вручную во вторичную реплику. Ознакомьтесь со статьей [Настройка активной георепликации для базы данных SQL Azure с помощью портала Azure и запуск отработки отказа][sql-db-failover].

Реплика использует другую строку подключения, поэтому необходимо будет обновить строку подключения в приложении.

### <a name="client-runs-out-of-connections-in-the-connection-pool"></a>Клиенту не хватает подключений в пуле подключений.
**Обнаружение.** Перехватите ошибки `System.InvalidOperationException`.

**Восстановление**

* Повторите операцию.
* В качестве плана по устранению рисков используйте следующий прием: изолируйте пулы подключений для каждого варианта использования, чтобы один из вариантов использования не мог влиять на все подключения.
* Увеличьте максимальное количество пулов подключений.

**Диагностика.** Журналы приложений.

### <a name="database-connection-limit-is-reached"></a>Достигнут предел для числа подключений к базе данных.
**Обнаружение.** База данных SQL Azure ограничивает количество одновременных рабочих ролей, входов в систему и сеансов. Ограничения зависят от уровня службы. Дополнительные сведения см. в статье [Ограничения ресурсов базы данных SQL Azure][sql-db-limits].

Чтобы обнаружить эти ошибки, перехватите `System.Data.SqlClient.SqlException` и проверьте значение `SqlException.Number` для кода ошибки SQL. Список соответствующих кодов ошибки см. в разделе [Ошибки управления ресурсами][sql-db-errors].

**Восстановление**. Эти ошибки считаются временными, поэтому повторная попытка может решить проблему. Если вы постоянно получаете эти ошибки, попробуйте выполнить масштабирование базы данных.

**Диагностика.** Запрос [sys.event_log][sys.event_log] возвращает сведения об успешных подключениях к базе данных, а также о сбоях подключения и взаимоблокировках.

* Создайте [правило генерации оповещений][azure-alerts] для неудачных подключений.
* Включите [аудит базы данных SQL][sql-db-audit] и проверьте неудачные попытки входа.

## <a name="service-bus-messaging"></a>Обмен сообщениями через служебную шину
### <a name="reading-a-message-from-a-service-bus-queue-fails"></a>Чтение сообщения из очереди служебной шины завершается сбоем.
**Обнаружение.** Перехватите исключения из пакета SDK клиента. Базовый класс для исключений служебной шины — это [MessagingException][sb-messagingexception-class]. Если это временная ошибка, свойство `IsTransient` имеет значение true.

Дополнительные сведения см. в статье [Исключения обмена сообщениями служебной шины][sb-messaging-exceptions].

**Восстановление**

1. Повторите попытки в случае временного сбоя. Ознакомьтесь с разделом [Руководство по использованию механизма повторов для служебной шины][sb-retry].
2. Сообщения, которые не удается доставить ни одному адресату, помещаются в *очередь недоставленных сообщений*. С помощью этой очереди можно просмотреть, какие сообщения невозможно получить. Автоматическая очистка очереди недоставленных сообщений не предусмотрена. Сообщения остаются там, пока вы явно не извлечете их. Ознакомьтесь со статьей [Обзор очередей недоставленных сообщений служебной шины][sb-dead-letter-queue].

### <a name="writing-a-message-to-a-service-bus-queue-fails"></a>Запись сообщения в очередь служебной шины завершается сбоем.
**Обнаружение.** Перехватите исключения из пакета SDK клиента. Базовый класс для исключений служебной шины — это [MessagingException][sb-messagingexception-class]. Если это временная ошибка, свойство `IsTransient` имеет значение true.

Дополнительные сведения см. в статье [Исключения обмена сообщениями служебной шины][sb-messaging-exceptions].

**Восстановление**

1. Клиент служебной шины автоматически осуществляет новую попытку после временных ошибок. По умолчанию он использует экспоненциальную задержку. По достижении максимального числа повторных попыток или завершении периода ожидания клиент создает исключение. Дополнительные сведения см. в разделе [Руководство по использованию механизма повторов для служебной шины][sb-retry].
2. В случае превышения квоты очереди клиент создает исключение [QuotaExceededException][QuotaExceededException]. Сообщение об исключении содержит более подробные сведения. Остановите запись некоторых сообщений из очереди, прежде чем повторить попытку, и попробуйте использовать шаблон автоматического выключения, чтобы избежать непрерывных попыток в случае превышения квоты. Кроме того, убедитесь, что свойству [BrokeredMessage.TimeToLive] не задано слишком большое значение.
3. В пределах региона можно повысить устойчивость с помощью [секционированных очередей и разделов][sb-partition]. Несекционированные очередь или раздел назначаются одному хранилищу сообщений. Если это хранилище сообщений недоступно, все операции с этой очередью или разделом завершатся с ошибкой. Секционированные очереди или разделы разделяются по нескольким хранилищам сообщений.
4. Для повышения устойчивости создайте два пространства имен служебной шины в разных регионах и реплицируйте сообщения. Вы можете использовать активную или пассивную репликацию.

   * Активная репликация. Клиент отправляет каждое сообщение в обе очереди. Получатель прослушивает обе очереди. Отметьте сообщения уникальным идентификатором, чтобы клиент мог удалять повторяющиеся сообщения.
   * Пассивная репликация. Клиент отправляет сообщение в одну очередь. Если возникает ошибка, клиент начинает использовать другую очередь. Получатель прослушивает обе очереди. При таком подходе уменьшается число отправляемых повторяющихся сообщений. Тем не менее получатель должен по-прежнему обрабатывать повторяющиеся сообщения.

     Дополнительные сведения см. в статье, посвященной [примеру GeoReplication][sb-georeplication-sample], и статье [Рекомендации по изолированию приложений от простоев и аварий служебной шины](/azure/service-bus-messaging/service-bus-outages-disasters/).

### <a name="duplicate-message"></a>Повторяющиеся сообщения.
**Обнаружение.** Изучите свойства `MessageId` и `DeliveryCount` сообщения.

**Восстановление**

* По возможности спроектируйте операции обработки сообщений идемпотентными. В противном случае сохраните идентификаторы уже обработанных сообщений и проверьте их перед обработкой сообщений.
* Включите поиск повторяющихся сообщений, создав очередь с параметром `RequiresDuplicateDetection`, которому присвоено значение true. С помощью этого параметра служебная шина автоматически удаляет любое сообщение, отправленное с тем же `MessageId`, что и предыдущее.  Обратите внимание на следующее.

  * Этот параметр предотвращает помещение в очередь повторяющихся сообщений. Он не предотвращает повторную обработку одного и того же сообщения пользователем.
  * Функция обнаружения дубликатов имеет временное окно. Если повторяющееся сообщение отправляется в это время, оно не будет обнаружено.

**Диагностика.** Регистрируйте повторяющиеся сообщения.

### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>Приложение не может обработать конкретное сообщение из очереди.
**Обнаружение.** Зависит от приложения. Например, сообщение содержит недопустимые данные или по какой-то причине происходит сбой бизнес-логики.

**Восстановление**

Существует два режима сбоя, которые следует учитывать.

* Получатель обнаруживает сбой. В этом случает переместите сообщение в очередь недоставленных сообщений. Позже запустите отдельный процесс для проверки сообщений в этой очереди.
* В процессе обработки сообщения получателем происходит сбой, например, из-за необработанного исключения. В этом случае используйте режим `PeekLock`. В этом режиме по истечении срока действия блокировки сообщение стает доступным для других получателей. Если максимальное число доставок или срок жизни сообщения превышают предел, сообщение автоматически перемещается в очередь недоставленных сообщений.

Дополнительные сведения см. в статье [Обзор очередей недоставленных сообщений служебной шины][sb-dead-letter-queue].

**Диагностика.** Каждый раз, когда приложение перемещает сообщение в очередь недоставленных сообщений, записывайте событие в журнал приложений.

## <a name="service-fabric"></a>Service Fabric
### <a name="a-request-to-a-service-fails"></a>Запрос к службе завершается ошибкой.
**Обнаружение.** Служба возвращает ошибку.

**Восстановление**

* Снова найдите прокси-сервер (`ServiceProxy` или `ActorProxy`) и повторно вызовите метод службы или субъекта.
* **Служба с отслеживанием состояния.** Поместите операции в надежных коллекциях в транзакцию. Если возникает ошибка, будет выполнен откат транзакции. Запрос (если он извлечен из очереди) будет обрабатываться повторно.
* **Служба без отслеживания состояния.** Если служба сохраняет данные во внешнее хранилище, все операции должны быть идемпотентными.

**Диагностика.** Журнал приложений

### <a name="service-fabric-node-is-shut-down"></a>Узел Service Fabric завершил работу.
**Обнаружение.** Токен отмены передается в метод `RunAsync` службы. Service Fabric отменяет задачу до завершения работы узла.

**Восстановление**. С помощью токена отмены можно обнаружить завершение работы. Когда Service Fabric запрашивает отмену задачи, как можно быстрее завершите все выполняющиеся операции и выйдите из  `RunAsync`.

**Диагностика.** Журналы приложений

## <a name="storage"></a>Хранилище
### <a name="writing-data-to-azure-storage-fails"></a>Запись данных в службу хранилища Azure завершается сбоем.
**Обнаружение.** Клиент получает ошибки при записи.

**Восстановление**

1. Повторите операцию, чтобы восстановить данные после временных сбоев. [Политика повтора][Storage.RetryPolicies] в клиентском пакете SDK обрабатывает это автоматически.
2. Реализуйте шаблон автоматического выключения, чтобы избежать перегрузки хранилища.
3. При сбое N повторных попыток выполните корректный переход. Например:

   * Сохраните данные в локальном кэше и выполните переадресацию записей в хранилище, когда служба станет доступной.
   * Если действие записи выполнялось в области транзакций, компенсируйте транзакции.

**Диагностика.** Используйте [метрики хранения][storage-metrics].

### <a name="reading-data-from-azure-storage-fails"></a>Считывание данных из службы хранилища Azure завершается сбоем.
**Обнаружение.** Клиент получает ошибки при считывании данных.

**Восстановление**

1. Повторите операцию, чтобы восстановить данные после временных сбоев. [Политика повтора][Storage.RetryPolicies] в клиентском пакете SDK обрабатывает это автоматически.
2. Для хранилища RA-GRS, если произошел сбой при чтении из основной конечной точки, переключитесь на чтение из вторичной конечной точки. Клиентский пакет SDK может обрабатывать это автоматически. Ознакомьтесь со статьей [Репликация службы хранилища Azure][storage-replication].
3. При сбое *N* повторных попыток выполните откат, чтобы корректно снизить производительность. Например, если изображение продукта нельзя извлечь из хранилища, используйте универсальное изображение-заполнитель.

**Диагностика.** Используйте [метрики хранения][storage-metrics].

## <a name="virtual-machine"></a>Виртуальная машина
### <a name="connection-to-a-backend-vm-fails"></a>Подключение к серверной виртуальной машине завершается сбоем.
**Обнаружение.** Ошибки сетевого подключения.

**Восстановление**

* Разверните по крайней мере две серверных виртуальных машины в группе доступности за подсистемой балансировки нагрузки.
* Если ошибка подключения временная, иногда протокол TCP будет успешно выполнять повторную отправку сообщения.
* Реализуйте политику повтора в приложении.
* Если ошибки постоянные или повторяющиеся, реализуйте шаблон [автоматического выключения][circuit-breaker].
* Если вызывающая виртуальная машина превышает предел возврата данных по сети, очередь исходящих сообщений будет заполнена. Если очередь исходящих сообщений постоянно заполнена, попробуйте выполнить масштабирование.

**Диагностика.** Регистрируйте события в границах служб.

### <a name="vm-instance-becomes-unavailable-or-unhealthy"></a>Экземпляр виртуальной машины становится недоступным или неработоспособным.
**Обнаружение.** Настройте [проверку работоспособности][lb-probe] подсистемы балансировки нагрузки, которая сообщает о том, работоспособен ли экземпляр виртуальной машины. При этом проверяется, правильно ли отвечают критически важные функции.

**Восстановление**. Для каждого уровня приложения поместите несколько экземпляров виртуальной машины в одну и ту же группу доступности и разместите подсистему балансировки нагрузки перед виртуальной машиной. Если проверка завершается сбоем, подсистема балансировки нагрузки Azure прекращает отправлять новое подключение неработоспособным экземплярам.

**Диагностика.** Используйте службу [Log Analytics][lb-monitor] для подсистемы балансировки нагрузки.

* Настройте систему мониторинга для отслеживания всех конечных точек мониторинга работоспособности.

### <a name="operator-accidentally-shuts-down-a-vm"></a>Оператор случайно завершает работу виртуальной машины.
**Обнаружение.** Недоступно

**Восстановление**. Установите блокировку ресурсов с уровнем `ReadOnly`. Ознакомьтесь со статьей [Блокировка ресурсов для предотвращения непредвиденных изменений][rm-locks].

**Диагностика.** Используйте [журналы действий Azure][azure-activity-logs].

## <a name="webjobs"></a>Веб-задания
### <a name="continuous-job-stops-running-when-the-scm-host-is-idle"></a>Выполнение непрерывного задания останавливается, когда узел диспетчера служб неактивен.
**Обнаружение.** Передайте токен отмены в функцию веб-задания. Дополнительные сведения см. в статье о [корректном завершении работы][web-jobs-shutdown].

**Восстановление**. Включите параметр `Always On` в веб-приложении. Дополнительные сведения см. в статье [Выполнение фоновых задач с помощью веб-заданий в службе приложений Azure][web-jobs].

## <a name="application-design"></a>Проектирование приложений
### <a name="application-cant-handle-a-spike-in-incoming-requests"></a>Приложение не может обработать всплеск входящих запросов.
**Обнаружение.** Зависит от приложения. Обычные симптомы:

* Веб-сайт начинает возвращать коды ошибок HTTP 5xx.
* Зависимые службы, например база данных или служба хранилища, начинают регулировать запросы. Найдите ошибки HTTP, такие как HTTP 429 (слишком много запросов), в зависимости от службы.
* Длина очереди HTTP увеличивается.

**Восстановление**

* Выполните развертывание, чтобы обработать более высокие нагрузки.
* Устраните ошибки, чтобы предотвратить каскадные сбои во всем приложении. Стратегии устранения рисков включают следующее:

  * Реализация [шаблона регулирования][throttling-pattern] во избежание перегрузки серверных систем.
  * Использование [выравнивания нагрузки на основе очередей][queue-based-load-leveling], чтобы выполнить буферизацию запросов и обрабатывать их с удобной для вас скоростью.
  * Назначение приоритетов нескольким клиентам. Например, если приложение имеет платные и бесплатные уровни, выполните регулирование клиентов бесплатного уровня, а не платного. Ознакомьтесь со статьей [Шаблон очереди с приоритетом][priority-queue-pattern].

**Диагностика.** Используйте [журнал ведения диагностики службы приложений][app-service-logging]. Воспользуйтесь службой, например [Azure Log Analytics][azure-log-analytics], [Application Insights][app-insights] или [New Relic][new-relic], чтобы разобраться в журналах диагностики.

### <a name="one-of-the-operations-in-a-workflow-or-distributed-transaction-fails"></a>Одна из операций в рабочем процессе или распределенная транзакция завершается сбоем.
**Обнаружение.** После *N* повторных попыток по-прежнему происходит сбой.

**Восстановление**

* В качестве плана устранения рисков реализуйте шаблон [Планировщик, агент, контролер][scheduler-agent-supervisor] для управления целым рабочим процессом.
* Не повторяйте попытку после истечения времени ожидания. Процент успешных операций для устранения этой ошибки очень мал.
* Если очередь работает, повторите попытку позже.

**Диагностика.** Регистрируйте все операции (успешные и неудачные), включая компенсирующие действия. Используйте идентификаторы корреляции, чтобы отслеживать все операции в одной и той же транзакции.

### <a name="a-call-to-a-remote-service-fails"></a>Вызов удаленной службы завершается сбоем.
**Обнаружение.** Код ошибки HTTP.

**Восстановление**

1. Повторите попытки в случае временного сбоя.
2. Если вызов завершается сбоем после *N* попыток, выполите откат. (Зависит от приложения.)
3. Реализуйте [шаблон автоматического выключения][circuit-breaker], чтобы избежать каскадных сбоев.

**Диагностика.** Регистрируйте все ошибки удаленных вызовов.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о процессе FMA см. в статье [Resilience by design for cloud services][resilience-by-design-pdf] (Намеренная устойчивость облачных служб) (скачиваемый PDF-файл).

<!-- links -->

[api-management]: https://azure.microsoft.com/documentation/services/api-management/
[api-management-throttling]: /azure/api-management/api-management-sample-flexible-throttling/
[app-insights]: /azure/application-insights/app-insights-overview/
[app-insights-web-apps]: /azure/application-insights/app-insights-azure-web-apps/
[app-service-configure]: /azure/app-service-web/web-sites-configure/
[app-service-logging]: /azure/app-service-web/web-sites-enable-diagnostic-log/
[app-service-slots]: /azure/app-service-web/web-sites-staged-publishing/
[auto-rest-client-retry]: https://github.com/Azure/autorest/tree/master/docs
[azure-activity-logs]: /azure/monitoring-and-diagnostics/monitoring-overview-activity-logs/
[azure-alerts]: /azure/monitoring-and-diagnostics/insights-alerts-portal/
[azure-log-analytics]: /azure/log-analytics/log-analytics-overview/
[BrokeredMessage.TimeToLive]: https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
[cassandra-error-handling]: http://www.datastax.com/dev/blog/cassandra-error-handling-done-right
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[docdb-multi-region]: /azure/documentdb/documentdb-developing-with-multiple-regions/
[elasticsearch-azure]: ../elasticsearch/index.md
[elasticsearch-client]: https://www.elastic.co/guide/en/elasticsearch/client/index.html
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[onstop-events]: https://azure.microsoft.com/blog/the-right-way-to-handle-azure-onstop-events/
[lb-monitor]: /azure/load-balancer/load-balancer-monitor-log/
[lb-probe]: /azure/load-balancer/load-balancer-custom-probe-overview/#learn-about-the-types-of-probes
[new-relic]: https://newrelic.com/
[priority-queue-pattern]: https://msdn.microsoft.com/library/dn589794.aspx
[queue-based-load-leveling]: https://msdn.microsoft.com/library/dn589783.aspx
[QuotaExceededException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx
[ra-web-apps-basic]: ../reference-architectures/app-service-web-app/basic-web-app.md
[redis-monitor]: /azure/redis-cache/cache-how-to-monitor/
[redis-retry]: ../best-practices/retry-service-specific.md#azure-redis-cache-retry-guidelines
[resilience-by-design-pdf]: http://download.microsoft.com/download/D/8/C/D8C599A4-4E8A-49BF-80EE-FE35F49B914D/Resilience_by_Design_for_Cloud_Services_White_Paper.pdf
[RoleEntryPoint.OnStop]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[RoleEnvironment.Stopping]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx
[rm-locks]: /azure/azure-resource-manager/resource-group-lock-resources/
[sb-dead-letter-queue]: /azure/service-bus-messaging/service-bus-dead-letter-queues/
[sb-georeplication-sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/GeoReplication
[sb-messagingexception-class]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
[sb-messaging-exceptions]: /azure/service-bus-messaging/service-bus-messaging-exceptions/
[sb-outages]: /azure/service-bus-messaging/service-bus-outages-disasters/#protecting-queues-and-topics-against-datacenter-outages-or-disasters
[sb-partition]: /azure/service-bus-messaging/service-bus-partitioning/
[sb-poison-message]: /azure/app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to/#poison
[sb-retry]: ../best-practices/retry-service-specific.md#service-bus-retry-guidelines
[search-sdk]: https://msdn.microsoft.com/library/dn951165.aspx
[scheduler-agent-supervisor]: https://msdn.microsoft.com/library/dn589780.aspx
[search-analytics]: /azure/search/search-traffic-analytics/
[sql-db-audit]: /azure/sql-database/sql-database-auditing-get-started/
[sql-db-errors]: /azure/sql-database/sql-database-develop-error-messages/#resource-governance-errors
[sql-db-failover]: /azure/sql-database/sql-database-geo-replication-failover-portal/
[sql-db-limits]: /azure/sql-database/sql-database-resource-limits/
[sql-db-replication]: /azure/sql-database/sql-database-geo-replication-overview/
[storage-metrics]: https://msdn.microsoft.com/library/dn782843.aspx
[storage-replication]: /azure/storage/storage-redundancy/
[Storage.RetryPolicies]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.aspx
[sys.event_log]: https://msdn.microsoft.com/library/dn270018.aspx
[throttling-pattern]: https://msdn.microsoft.com/library/dn589798.aspx
[web-jobs]: /azure/app-service-web/web-sites-create-web-jobs/
[web-jobs-shutdown]: /azure/app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to/#graceful