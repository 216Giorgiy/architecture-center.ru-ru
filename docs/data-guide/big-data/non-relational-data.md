---
title: Нереляционные данные и базы данных NoSQL
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 2c284099bd7e8bbb85f1252a24d699d9fca09eae
ms.sourcegitcommit: 51f49026ec46af0860de55f6c082490e46792794
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="non-relational-data-and-nosql"></a>Нереляционные данные и базы данных NoSQL

*Нереляционная база данных* — это база данных, в которой в отличие от большинства традиционных систем баз данных не используется табличная схема строк и столбцов. В этих базах данных применяется модель хранения, оптимизированная под конкретные требования типа хранимых данных. Например, данные могут храниться как простые пары "ключ — значение", документы JSON или граф, состоящий из ребер и вершин. 

Все эти хранилища данных не используют [реляционную модель](../relational-data/index.md). Кроме того, они, как правило, поддерживают определенные типы данных. Процесс запроса данных также специфический. Например, хранилища данных временных рядов рассчитаны на запросы к последовательностям данных, упорядоченных по времени, а хранилища данных графов — на анализ взвешенных связей между сущностями. Ни один из форматов не подходит в полней мере при выполнении задач управления данными о транзакциях. 

Термин *NoSQL* применяется к хранилищам данных, которые не используют язык запросов SQL, а запрашивают данные с помощью других языков и конструкций. На практике NoSQL означает "нереляционная база данных", даже несмотря на то, что многие из этих баз данных не поддерживают совместимые с SQL запросы. Однако базовая стратегия выполнения запросов SQL обычно значительно отличается от применяемой в системе управления реляционной базой данных (реляционная СУБД).

В разделах ниже описаны основные категории нереляционных баз данных или баз данных NoSQL.

## <a name="document-data-stores"></a>Хранилища данных документов
Хранилище данных документов управляет набором значений именованных строковых полей и данных объекта в сущности, которая называется *документом*. Обычно данные в этих хранилищах содержатся в виде документов JSON. Каждое значение поля может представлять собой скалярный элемент, например число, или сложный объект, например список или коллекция типа "родитель — потомок". Данные в полях документа можно закодировать разными способами, например в формате XML, YAML, JSON, BSON, или хранить в виде обычного текста. Поля документов доступны системе управления хранилищем, что позволяет приложению выполнять запросы и применять фильтры, основанные на значениях этих полей.  

Как правило, документ содержит все данные одной сущности. Состав элементов для каждой сущности зависит от конкретного приложения. Например, сущность может содержать сведения о клиенте, заказе или и те, и другие. Один документ может содержать сведения, которые в реляционной СУБД обычно распределяются по нескольким реляционным таблицам. Хранилище документов не обязывает использовать одинаковую структуру для всех документов. Поддержка свободной формы обеспечивает большую гибкость. Например, приложения могут хранить в документах разные данные в соответствии с текущими требованиями компании.  

![Пример хранилища данных документов](./images/document.png)  

Приложение может получать документы по ключу документа. Это уникальный идентификатор документа. Часто к нему применяется хэширование для равномерного распределения данных. Некоторые базы данных документов создают ключ документа автоматически. Другие позволяют указать, какой атрибут документа следует использовать в качестве ключа. Также приложение может запрашивать документы по значениям одного или нескольких полей. Некоторые базы данных документов поддерживают индексирование, что ускоряет поиск документов по одному или нескольким индексированным полям.  

Многие базы данных документов поддерживают обновления "на месте", то есть позволяют приложению изменять значения отдельных полей без перезаписи всего документа. Операции чтения и записи в нескольких полях одного документа обычно являются атомарными.

Соответствующие службы Azure:  

- [База данных Azure Cosmos](https://azure.microsoft.com/services/cosmos-db/)

## <a name="columnar-data-stores"></a>Столбчатые хранилища данных
Столбчатое хранилище данных или хранилище семейств столбцов упорядочивает данные по столбцам и строкам. Столбчатое хранилище данных в простейшей форме почти неотличимо от реляционной базы данных, по крайней мере организационно. Настоящее преимущество столбчатого хранилища данных заключается в способности денормализованно структурировать разреженные данные, что связано со столбцово-ориентированным методом хранения данных.  

Столбчатое хранилище данных можно представить как набор табличных данных со строками и столбцами, в которых столбцы разделяются на определенные группы или семейства столбцов. Каждое семейство столбцов включает набор логически связанных столбцов, которые обычно извлекаются или управляются как единое целое. Другие данные, которые используются в других процессах, хранятся отдельно в других семействах столбцов. В семейство столбцов можно динамически добавить новые столбцы, а строки могут быть разреженными (то есть строки не обязаны иметь значение для каждого столбца). 

На следующей диаграмме представлен пример таблицы с двумя семействами столбцов: `Identity` и `Contact Info`. Данные одной сущности имеют одинаковые ключи строк во всех семействах столбцов. Такая структура, в которой строки любого объекта в семействе столбцов могут динамически изменяться, определяет важное преимущество этой категории хранилищ. Семейства столбцов очень хорошо подходят для хранения данных с различными схемами.

![Пример данных семейств столбцов](../../guide/technology-choices/images/column-family.png)

В отличие от хранилища пар "ключ — значение" и баз данных документов, большинство столбчатых баз данных упорядочивают хранимые данные с помощью самих значений ключей, а не хэш-кодов от них. Ключ строки рассматривается как первичный индекс и обеспечивает доступ на основе определенного ключа или их диапазона. Некоторые реализации позволяют создавать вторичные индексы по определенным столбцам в семействе столбцов. Вторичные индексы позволяют получать данные по значениям столбцов, а не ключам строки.

Все столбцы одного семейства хранятся на диске в одном файле. Каждый файл содержит определенное число строк. При использовании больших наборов данных этот подход позволяет повысить производительность за счет снижения объема данных, которые необходимо считывать с диска, когда отправляется запрос на получение нескольких столбцов за раз. 

Чтение и запись одной строки из одного семейства столбцов обычно атомарные операции. Но некоторые реализации поддерживают атомарность всей строки, распределенной по нескольким семействам столбцов.

Соответствующие службы Azure:  

- [HBase в HDInsight](/azure/hdinsight/hdinsight-hbase-overview)

## <a name="keyvalue-data-stores"></a>Хранилище пар "ключ — значение"
Хранилище пар "ключ — значение" по сути представляет собой большую хэш-таблицу. Каждое значение сопоставляется с уникальным ключом, и хранилище ключей использует этот ключ для хранения данных, применяя к нему некоторую функцию хэширования. Выбор функции хэширования должен обеспечить равномерное распределение хэшированных ключей по хранилищу данных.

Большинство хранилищ пар "ключ — значение" поддерживают только самые простые операции запроса, вставки и удаления. Чтобы частично или полностью изменить значение, приложение всегда перезаписывает существующее значение целиком. В большинстве реализаций атомарной операцией считается чтение или запись одного значения. Запись больших значений занимает относительно долгое время.

Приложение может хранить в наборе значений произвольные данные, но некоторые хранилища пар "ключ — значение" накладывают ограничения на максимальный размер значений. Программное обеспечение хранилища ничего не знает о значениях, которые в нем хранятся. Все сведения о схеме поддерживаются и применяются на уровне приложения. Эти значения по существу являются большими двоичными объектами, которые хранилище извлекает и сохраняет по соответствующему ключу.

![Пример данных в хранилище пар "ключ — значение"](../../guide/technology-choices/images/key-value.png)

Хранилища пар "ключ — значение" рассчитаны на приложения, выполняющие простые операции поиска на основе значения ключа или диапазона ключей, но не очень подходят для систем, которым нужно запрашивать данные из нескольких таблиц хранилищ пар "ключ — значение", например присоединенные данные в нескольких таблицах. 

Кроме того, хранилища пар "ключ — значение" неудобны в сценариях, где могут выполняться запросы или фильтрация по значению, а не только по ключам. Например, в реляционной базе данных вы можете найти определенную запись с помощью предложения WHERE и отфильтровать ее по неключевым столбцам, но хранилища пар "ключ — значение" обычно не поддерживают такие возможности поиска значений или же этот процесс выполняется медленно.

Одно хранилище пар "ключ — значение" очень легко масштабируется, поскольку позволяет удобно распределить данные среди нескольких узлов на разных компьютерах.

Соответствующие службы Azure:  
- [API таблиц Azure Cosmos DB](/azure/cosmos-db/table-introduction)  
- [кэш Azure Redis](https://azure.microsoft.com/services/cache/)  
- [хранилище таблиц Azure](https://azure.microsoft.com/services/storage/tables/)

## <a name="graph-data-stores"></a>Хранилища данных графов
Хранилища данных графов управляют сведениями двух типов: узлами и ребрами. Узлы в этом случае представляют сущности, а ребра определяют связи между ними. Узлы и грани имеют свойства, которые предоставляют сведения о конкретном узле или грани, примерно как столбцы в реляционной таблице. Грани могут иметь направление, указывающее на характер связи.  

Хранилища данных графов позволяют приложениям эффективно выполнять запросы, которые проходят через сеть узлов и ребер, а также анализировать связи между сущностями. На схеме ниже представлены данные персонала организации, структурированные в виде графа. Сущностями здесь являются сотрудники и отделы, а грани определяют отношения подчинения и отдел, в котором работает каждый сотрудник. На этом графе грани имеют строгое направление, которое на схеме обозначено стрелками.

![Пример данных в хранилище данных графов](../../guide/technology-choices/images/graph.png)

Такая структура позволяет легко выполнять такие запросы, как "найти всех сотрудников, которые прямо или косвенно подчиняются Светлане" или "найти всех, кто работает в одном отделе с Дмитрием". Процессы сложного анализа выполняются очень быстро, даже на больших графах с большим количеством сущностей и связей. Многие базы данных графов предоставляют язык запросов, который можно использовать для эффективного обхода сети связей.  

Соответствующие службы Azure:  
- [API Graph в Azure Cosmos DB](/azure/cosmos-db/graph-introduction)  

## <a name="time-series-data-stores"></a>Хранилища данных временных рядов
Данными временных рядов называются наборы значений, которые упорядочены по времени. Соответственно хранилища данных временных рядов оптимизированы для хранения данных именно такого типа. Хранилища данных временных рядов должны поддерживать очень большое число операций записи, так как обычно в них в режиме реального времени собирается большой объем данных из большого количества источников. Эти хранилища также хорошо подходят для хранения данных телеметрии. Например, для сбора данных от датчиков Интернета вещей или счетчиков в приложениях или системах. Обновления в таких базах данных выполняются редко, а удаление чаще всего является массовой операцией.

![Пример данных временных рядов](./images/time-series.png)

Размер отдельных записей в базе данных временных рядов обычно невелик, но их очень много, а значит общий размер данных быстро увеличивается. Хранилища данных временных рядов также обрабатывают данные, полученные вне очереди или несвоевременно, автоматически индексируют точки данных и оптимизируют запросы, полученные в течение определенного промежутка времени. Эта последняя возможность позволяет быстро выполнять запросы к миллионам точек данных и нескольким потокам данных, что, в свою очередь, обеспечивает поддержку визуализации временных рядов (стандартный способ потребления данных временных рядов). 

Дополнительные сведения см. в статье [Решения для временных рядов](../scenarios/time-series.md).

Соответствующие службы Azure:  
- [Аналитика временных рядов Azure](https://azure.microsoft.com/services/time-series-insights/)  
- [OpenTSDB с HBase в HDInsight](/azure/hdinsight/hdinsight-hbase-overview)

## <a name="object-data-stores"></a>Хранилище данных объектов
Хранилища данных объектов оптимизированы для хранения и извлечения больших двоичных объектов, например изображений, текстовых файлов, видео- и аудиопотоков, объектов данных и документов приложений большого размера, образы дисков виртуальных машин. Объект состоит из сохраненных данных, метаданных и уникального идентификатора доступа к объекту. Хранилища объектов поддерживают отдельные большие файлы, а также позволяют управлять всеми файлами за счет внушительного общего объема хранилища.  

![Пример данных объектов](./images/object.png)

Некоторые хранилища данных объектов реплицируют определенный большой двоичный объект между несколькими узлами кластера, что обеспечивает быстрое параллельное чтение. Это, в свою очередь, позволяет реализовать масштабируемую архитектуру запроса данных, хранящихся в больших файлах, так как несколько процессов, обычно выполняющихся на разных серверах, могут одновременно запрашивать большие файлы данных.

Часто хранилища данных объектов используют как сетевые общие папки. Доступ к файлам, хранящимся в этих папках, можно получить через компьютерную сеть с использованием стандартных сетевых протоколов, например SMB. Если созданы необходимые механизмы поддержки безопасности и одновременного доступа, такое совместное использование данных позволяет распределенным службам с высокой степенью масштабируемости предоставлять доступ к данным для базовых низкоуровневых операций, то есть для простых запросов на чтение и запись.

Соответствующие службы Azure:   

- [Хранилище BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/)  
- [Хранилище озера данных Azure](https://azure.microsoft.com/services/data-lake-store/)  
- [Хранилище файлов Azure](https://azure.microsoft.com/services/storage/files/)  


## <a name="external-index-data-stores"></a>Хранилища данных внешних индексов

Хранилища данных внешних индексов позволяют искать информацию, содержащуюся в других хранилищах данных и службах. Внешний индекс выступает в роли вторичного индекса любого хранилища данных. Кроме того, с его помощью можно индексировать большие объемы данных и предоставлять доступ к этим индексам почти в реальном времени. 

Например, в файловой системе могут храниться текстовые файлы. По пути файл можно найти быстро, но поиск на основе содержимого выполняется медленно, так как сканируются все файлы. Внешний индекс позволяет создавать вторичные индексы, а затем быстро искать путь к файлам, соответствующим заданным условиям. Рассмотрим еще один пример использования внешнего индекса. Предположим, что хранилища пар "ключ — значение" поддерживают индексирование только по ключу. Вы можете создать вторичный индекс на основе значений данных и быстро найти ключ, однозначно определяющий каждый соответствующий элемент. 

![Пример данных поиска](./images/search.png)

Индексы создаются в процессе индексирования, который может выполняться по модели извлечения, то есть по требованию хранилища данных, или по модели передачи, то есть по команде из кода приложения. В некоторых системах поддерживаются многомерные индексы и полнотекстовый поиск по большим объемам текстовых данных. 

Часто хранилища данных внешних индексов используют для реализации полнотекстового поиска и поиска в Интернете. В этих случаях поддерживается точный или нечеткий поиск. Нечеткий поиск находит документы, которые соответствуют набору условий, и вычисляет для них коэффициент совпадения с этим набором. Некоторые внешние индексы также поддерживают лингвистический анализ, который возвращает соответствия с учетом синонимов, категорий (например, при поиске по запросу "собаки" соответствием считается "питомцы") и морфологии (например, при поиске по запросу "бег" соответствием считается "бегущий"). 

Соответствующие службы Azure:  

- [Поиск Azure;](https://azure.microsoft.com/services/search/)


## <a name="typical-requirements"></a>Стандартные требования
Часто архитектура нереляционных хранилищ данных отличается от архитектуры реляционных баз данных. В частности эти хранилища, как правило, не имеют фиксированной схемы, а также не поддерживают транзакции или ограничивают их область. Из соображений масштабируемости они обычно не включают вторичные индексы.

В таблице ниже приведено сравнение требований каждого нереляционного хранилища данных.

| Требование | Хранилище данных документов | Столбчатое хранилище данных | Хранилище данных пар "ключ — значение" | Хранилище данных графов | 
| --- | --- | --- | --- | --- | 
| Нормализация | Денормализированные данные | Денормализированные данные | Денормализированные данные | Нормализированные данные | 
| Схема | Схема при чтении | Семейства столбцов, определенные при записи, схема столбца при чтении | Схема при чтении | Схема при чтении | 
| Согласованность (между параллельными транзакциями) | Настраиваемый уровень согласованности, гарантии на уровне документа | Гарантии на уровне семейства столбцов | Гарантии на уровне ключей | Гарантии на уровне графа 
| Атомарность (область транзакции) | Коллекция | Таблица | Таблица | График | 
| Стратегия блокировки | Оптимистичная (без блокировки) | Пессимистичная (блокировка строк) | Оптимистичная (ETag) | 
| Шаблон доступа | Прямой доступ | Статистические выражения на основе данных большого формата | Прямой доступ | Прямой доступ |
| Индексация | Первичный и вторичные индексы | Первичный и вторичные индексы | Только первичный индекс | Первичный и вторичные индексы | 
| Форма представления данных | Документ | Таблица с семействами столбцов | Ключ и значение | Граф с ребрами и вершинами | 
| разреженные; | Yes | Yes | Yes | Нет  | 
| Масштабность (большое количество столбцов и атрибутов) | Yes | Yes | Нет  | Нет  |  
| Размер данных | От малого (КБ) до среднего (несколько МБ) | От среднего (МБ) до большого (несколько ГБ) | Небольшой (КБ) | Небольшой (КБ) | 
| Общий максимальный масштаб | Очень большой (ПБ) | Очень большой (ПБ) | Очень большой (ПБ) | Большой (ТБ) | 

| Требование | Данные временных рядов | Хранилище данных объектов | Хранилище данных внешних индексов |
| --- | --- | --- | --- |
| Нормализация | Нормализированные данные | Денормализированные данные | Денормализированные данные |
| Схема | Схема при чтении | Схема при чтении | Схема при записи | 
| Согласованность (между параллельными транзакциями) | Недоступно | Недоступно | Недоступно | 
| Атомарность (область транзакции) | Недоступно | Объект. | Недоступно |
| Стратегия блокировки | Недоступно | Пессимистичная (блокировка больших двоичных объектов) | Недоступно |
| Шаблон доступа | Прямой доступ и агрегирование | Последовательный доступ | Прямой доступ | 
| Индексация | Первичный и вторичные индексы | Только первичный индекс | Недоступно |
| Форма представления данных | Таблица | Большой двоичный объект и метаданные | Документ |
| разреженные; | Нет  | Недоступно | Нет  | 
| Масштабность (большое количество столбцов и атрибутов) |  Нет  | Yes | Yes |  
| Размер данных | Небольшой (КБ) | От большого (ГБ) до очень большого (ТБ) | Небольшой (КБ) |
| Общий максимальный масштаб | Большой (несколько ТБ)  | Очень большой (ПБ) | Большой (несколько ТБ) | 
