---
title: Решения Интернета вещей и аналитики данных для строительной отрасли
titleSuffix: Azure Example Scenarios
description: Использование решений Интернета вещей и анализа данных для комплексного администрирования и реализации строительных проектов.
author: alexbuckgit
ms.date: 08/29/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: IoT, data-analytics
social_image_url: /azure/architecture/example-scenario/data/media/architecture-big-data-with-iot.png
ms.openlocfilehash: bf963c0467acd4ecbb4bdca6272385d7532167f2
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58244775"
---
# <a name="iot-and-data-analytics-in-the-construction-industry"></a>Решения Интернета вещей и аналитики данных для строительной отрасли

Этот пример сценария имеет отношение к организациям, которые создают решения, интегрирующие данные со многих устройств Интернета вещей в комплексную архитектуру анализа данных для улучшения и автоматизации принятия решений. Потенциальные приложения включают в себя конструирование, интеллектуальный анализ, производство или другие отраслевые решения, связанные с большими объемами данных из многих входных данных на основе Интернета вещей.

В этом случае производитель строительного оборудования выполняет сборку транспортных средств, единиц измерения и беспилотных летательных аппаратов, используя технологии Интернета вещей и GPS для передачи данных телеметрии. Компания хочет модернизировать свою архитектуру данных, чтобы лучше отслеживать рабочие условия и работоспособность оборудования. Замена устаревшего решения компании с использованием локальной инфраструктуры будет как временной, так и трудоемкой, и ее будет невозможно масштабировать для обработки ожидаемого объема данных.

Компания хочет компилировать облачное решение смарт-конструирования. Должен быть собран полный набор данных для конструирования сайта и автоматизации операции и обслуживания его различных элементов. Компания поставила перед собой следующие цели.

- Интеграция и анализ всего оборудования и данных конструирования сайта для минимизации простоев оборудования и сокращения краж.
- Удаленное и автоматическое управление строительным оборудованием для смягчения последствий нехватки рабочей силы, что в конечном итоге требует меньше работников и позволяет работникам с низким уровнем квалификации добиваться успеха.
- Сведение к минимуму эксплуатационных расходов и требований работоспособности поддерживающей инфраструктуры с одновременным повышением производительности и безопасности.
- Легкая масштабируемость инфраструктуры для поддержки увеличения данных телеметрии.
- Соблюдение всех соответствующих юридических требований путем подготовки ресурсов в своей стране без ущерба доступности системы.
- Использование программного обеспечения с открытым исходным кодом для увеличения вложений в раскрытие имеющихся навыков сотрудников.

Использование управляемых служб Azure (например, Центр Интернета вещей и HDInsight) позволяет клиентам быстро создавать и развертывать комплексное решение с более низкой ценой работы. Если у вас есть дополнительная аналитика данных, изучите список доступных [полностью управляемых служб аналитики данных в Azure][product-category].

## <a name="relevant-use-cases"></a>Варианты соответствующего использования

Другие варианты использования:

- Построение, интеллектуальный анализ или сценарий производства оборудования
- Крупномасштабная коллекция данных устройства для хранения и анализа
- Прием и анализ больших наборов данных

## <a name="architecture"></a>Архитектура

![Архитектура Интернета вещей и аналитики данных для строительной отрасли][architecture]

Поток данных проходит через решение следующим образом.

1. Оборудование конструирования собирает данные датчиков и отправляет данные результатов конструкции с регулярными интервалами для загрузки с балансировкой веб-служб, размещенных в кластере виртуальных машин Azure.
2. Пользовательские веб-службы принимают данные результатов конструирования и сохраняют их в кластере Apache Cassandra, запущенном на виртуальных машинах Azure.
3. Другой набор данных собирается с датчиков Интернета вещей на различные конструкции оборудования и отправляются в Центр Интернета вещей.
4. Собранные необработанные данные отправляются непосредственно из Центра Интернета вещей в хранилище BLOB-объектов Azure и в то же время становятся доступными для просмотра и анализа.
5. Данные, собранные с помощью Интернета вещей, обрабатываются практически в реальном времени путем задания Azure Stream Analytics и хранятся в базе данных Azure SQL.
6. Веб-приложение Smart Construction Cloud доступно аналитикам и пользователям для просмотра и анализа данных датчиков и изображений.
7. Пакетные задания инициируются пользователями веб-приложений по запросу. Задание выполняется в Apache Spark в HDInsight и анализирует новые хранимые данные в кластере Cassandra.

### <a name="components"></a>Компоненты

- [Центр Интернета вещей](/azure/iot-hub/about-iot-hub) выступает в качестве центрального концентратора сообщений для безопасной двунаправленной связи с идентификатором каждого устройства между облачной платформой и строительным оборудованием и другими элементами сайта. Центр Интернета вещей быстро может собирать данные для каждого устройства для приема в конвейер аналитики данных.
- [Azure Stream Analytics](/azure/stream-analytics/stream-analytics-introduction) — это модуль обработки событий, который проверяет большие потоки данных с устройств и из других источников данных. Эта служба также поддерживает извлечение информации из потоков данных для определения шаблонов и связей. В этом случае Stream Analytics принимает и анализирует данные с устройств Интернета вещей и сохраняет результаты в Базе данных SQL Azure.
- [База данных SQL Azure](/azure/sql-database/sql-database-technical-overview) содержит результаты проанализированных данных с устройств Интернета вещей и показатели, которые с помощью веб-приложения на основе Azure могут просмотреть аналитики и пользователи.
- [Хранилище BLOB-объектов](/azure/storage/blobs/storage-blobs-introduction) хранит данные образа, собранные с устройств Центра Интернета вещей. Данные образа можно просмотреть с помощью веб-приложения.
- [Диспетчер трафика Azure](/azure/traffic-manager/traffic-manager-overview) управляет распределением пользовательского трафика между конечными точками службы в разных регионах Azure.
- [Подсистема балансировки нагрузки](/azure/load-balancer/load-balancer-overview) распределяет данные от устройств строительного оборудования через веб-службы на основе виртуальной машины, чтобы обеспечить высокую доступность.
- [Виртуальные машины Azure](/azure/virtual-machines) развертывают веб-службы, которые получают и принимают данные результатов конструирования в базе данных Apache Cassandra.
- [Apache Cassandra](https://cassandra.apache.org) — распределенная база данных NoSQL, которую используют для хранения данных конструирования для последующей обработки службой Apache Spark.
- [Веб-приложения](/azure/app-service/app-service-web-overview) размещают веб-приложение конечного пользователя, которое может использоваться для запроса и просмотра исходящих данных и изображений. Пользователи также могут инициировать пакетные задания в Apache Spark через приложение.
- [Apache Spark на HDInsight](/azure/hdinsight/spark/apache-spark-overview) поддерживает обработку в памяти для повышения производительности приложений больших аналитик данных. В этом случае, чтобы запустить сложные алгоритмы над хранимыми данными в Apache Cassandra, используется Spark.

### <a name="alternatives"></a>Альтернативные варианты

- [Cosmos DB](/azure/cosmos-db/introduction) представляет собой альтернативную технологию базы данных NoSQL. Cosmos DB обеспечивает [поддержку нескольких источников в глобальном масштабе](/azure/cosmos-db/multi-region-writers) с [несколькими четко определенными уровнями согласованности](/azure/cosmos-db/consistency-levels) в соответствии с требованиями различных клиентов. Она также поддерживает [API Cassandra](/azure/cosmos-db/cassandra-introduction).
- [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) — это высокопроизводительная платформа на основе Apache Spark, оптимизированная для Azure. Она интегрирована с Azure, чтобы обеспечить настройку одним щелчком мыши, упорядочить рабочие процессы и интерактивное рабочее пространство для совместной работы.
- [Data Lake Storage](/azure/storage/data-lake-storage) является альтернативой для хранилища BLOB-объектов. В этом случае Data Lake Storage недоступно в целевом регионе.
- [Веб-приложения](/azure/app-service) также могут использоваться для размещения веб-служб для приема данных результатов конструирования.
- Различные варианты технологий доступны в режиме реального времени для приема сообщений, хранения данных, обработки потоков данных, хранения аналитических данных, а также аналитики и отчетности. Обзор этих параметров, их возможностей и ключевых критериев выбора см. в разделе [Выбор технологии приема сообщений в реальном времени в Azure ](/azure/architecture/data-guide/technology-choices/real-time-ingestion) в [Руководстве по архитектуре данных Azure](/azure/architecture/data-guide).

## <a name="considerations"></a>Рекомендации

Широкая доступность регионов Azure является важным фактором для этого сценария. Наличие более чем одного региона в одной стране может обеспечить аварийное восстановление, а также соблюдение контрактных обязательств и требований правоохранительных органов. Высокоскоростная связь Azure между регионами — важный фактор в этом сценарии.

Поддержка Azure для технологий с открытым исходным кодом позволила клиенту воспользоваться преимуществами имеющихся навыков работы. Клиент также может ускорить внедрение новых технологий с меньшими затратами и рабочими нагрузками по сравнению с локальным решением.

## <a name="pricing"></a>Цены

Следующие соображения определят значительную часть затрат для этого решения.

- Стоимость виртуальной машины Azure увеличивается по мере создания дополнительных экземпляров. Оплата освобожденных виртуальных машин будет взиматься за хранение, а не за вычислительные операции. Когда спрос возрастет, эти освобожденные машины могут быть перераспределены.
- Расходы [Центра Интернета вещей](https://azure.microsoft.com/pricing/details/iot-hub) определяются количеством подготовленных единиц Центра Интернета вещей, а также выбранным уровнем обслуживания, который определяет количество сообщений в день на разрешенную единицу.
- Оплата за услуги [Stream Analytics](https://azure.microsoft.com/pricing/details/stream-analytics) взимается в зависимости от количества единиц потоковой передачи, необходимых для обработки данных в службе.

## <a name="related-resources"></a>Связанные ресурсы

Чтобы увидеть реализацию подобной архитектуры, прочитайте [пользовательскую историю Komatsu][customer-story].

Рекомендации по архитектуре обработки больших данных доступны в [Руководстве по архитектуре данных в Azure](/azure/architecture/data-guide).

<!-- links -->

[product-category]: https://azure.microsoft.com/product-categories/analytics/
[customer-site]: https://home.komatsu/en/
[customer-story]: https://customers.microsoft.com/story/komatsu-manufacturing-azure-iot-hub-japan
[architecture]: ./media/architecture-big-data-with-iot.png
