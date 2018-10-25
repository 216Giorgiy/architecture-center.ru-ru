---
title: Децентрализованное доверие между банками в Azure
description: Реализация доверенной среды для взаимодействия и обмена данными без обращения к централизованной базе данных.
author: vitoc
ms.date: 09/09/2018
ms.openlocfilehash: fe27f885635ce5ae4ce368992affa1a85d7af416
ms.sourcegitcommit: 62945777e519d650159f0f963a2489b6bb6ce094
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48876769"
---
# <a name="decentralized-trust-between-banks-on-azure"></a>Децентрализованное доверие между банками в Azure

Этот пример сценария полезен для банков или любых других учреждений, которые хотят реализовать доверенную среду для обмена данными без обращения к централизованной базе данных. В данном примере описывается сценарий в контексте обслуживания данных о кредитных баллах между банками, но архитектура может применяться к любому сценарию, в котором консорциум организаций хочет поделиться проверенными данными друг с другом без обращения к центральной системе, которой управляет одна сторона.

Традиционно банки в рамках финансовой системы полагаются на централизованные источники, такие как кредитные бюро для получения данных о кредитных баллах и истории. Централизованный подход представляет собой концентрацию операционного риска, а иногда и ненужную третью сторону.

С помощью DLT (технология распределенного реестра) консорциум банков может создать децентрализованную систему, которая может быть более эффективной, менее подверженной атакам и служить новой платформой, где инновационные структуры могут быть реализованы для решения традиционных задач обеспечения конфиденциальности, скорости и стоимости.

В этом примере показано, как можно быстро настроить службы Azure, такие как масштабируемый набор виртуальных машин, виртуальную сеть, Key Vault, службу хранилища, Load Balancer и Monitor для развертывания эффективного частного блокчейна Ethereum PoA, где банки-члены могут создавать собственные узлы.

## <a name="relevant-use-cases"></a>Варианты соответствующего использования

Следующие варианты использования имеют похожие шаблоны проектирования.

* Перемещение выделенных бюджетов между различными бизнес-единицами многонациональной корпорации
* Трансграничные платежи
* Сценарии торгового финансирования
* Системы лояльности с участием разных компаний
* Экосистемы цепочки поставок и др.

## <a name="architecture"></a>Архитектура

![Схема архитектуры децентрализованного доверия банка](./media/architecture-decentralized-trust.png)

В этом сценарии рассматриваются компоненты серверной части, необходимые для создания масштабируемой, защищенной и контролируемой частной сети корпоративного блокчейна в консорциуме из двух или более членов. Подробная информация о том, как эти компоненты предоставляются (то есть в разных подписях и группах ресурсов), а также требования к подключению (то есть VPN или ExpressRoute) оставляются для вашего рассмотрения на основе требований к политике вашей организации. Ниже приведен пример прохождения потока данных.

1. Банк А создает и обновляет кредитную запись отдельного лица, отправляя транзакции в сеть блокчейна с помощью JSON-RPC.
2. Данные передаются с сервера частных приложений банка А в балансировщик нагрузки Azure, а затем в проверяющий узел масштабируемого набора виртуальных машин.
3. Сеть Ethereum PoA создает блок в заданное время (2 секунды для этого сценария).
4. Транзакция вставляется в созданный блок и проверяется через сеть блокчейна.
5. Банк B может читать кредитную запись, созданную банком A, путем связи с собственным узлом аналогичным образом с помощью JSON-RPC.

### <a name="components"></a>Компоненты

* виртуальные машины в масштабируемых наборах виртуальных машин предоставляют средство вычислений по запросу для размещения процессов проверяющего элемента управления для блокчейна;
* Key Vault используется как безопасное хранилище для закрытых ключей каждого проверяющего элемента управления;
* Load Balancer распределяет запросы RPC, пиринга и децентрализованного приложения системы управления;
* служба хранилища размещает сведения о сети и координации аренды;
* Operations Management Suite (объединение нескольких служб Azure) предоставляет сведения о доступных узлах, транзакциях в минуту и членах консорциума.

### <a name="alternatives"></a>Альтернативные варианты

В этом примере выбран подход Ethereum PoA, поскольку он является хорошей точкой входа для консорциума организаций, которые хотят создать среду, в которой можно обмениваться данными и с легкостью совместно использовать их доверенным, децентрализованным и понятным способом. Доступные шаблоны решений Azure также обеспечивают быстрый и удобный способ запуска блокчейна Ethereum PoA не только для лидера консорциума, но и для организаций-членов консорциума, чтобы развернуть собственные ресурсы Azure в рамках своей группы ресурсов и подписки для присоединения к существующей сети.

Для других расширенных или разных сценариев может возникнуть такая проблема, как конфиденциальность транзакций. Например, в сценарии передачи ценных бумаг члены консорциума могут не хотеть, чтобы их транзакции отображались для других членов. Существуют и другие альтернативы Ethereum PoA, которые решают эти проблемы по-своему.

* Corda
* Кворум
* Hyperledger

## <a name="considerations"></a>Рекомендации

### <a name="availability"></a>Доступность

[Azure Monitor][monitor] используется для непрерывного мониторинга сети блокчейна, чтобы обеспечить доступность. Ссылка на пользовательскую панель мониторинга на основе Azure Monitor будет отправлена вам после успешного развертывания шаблона решения блокчейна, используемого в этом сценарии. На панели мониторинга отображаются узлы, сообщающие о пакетах пульса за последние 30 минут, а также другая полезная статистика. 

Дополнительные сведения по другим вопросам доступности см. в статье [с контрольным списком для обеспечения доступности][availability] в Центре архитектуры Azure.

### <a name="scalability"></a>Масштабируемость

Популярной проблемой для блокчейна является количество транзакций, которые он может включать в течение заданного промежутка времени. Этот сценарий использует алгоритм Proof-of-Authority, где такая масштабируемость может быть лучше управляемой, чем Proof-of-Work. В сетях, основанных на алгоритме Proof-of-Authority, участники консенсуса известны и управляемы, что делает консенсус более подходящим для частного блокчейна консорциума организаций, которые известны друг другу. Параметры, такие как среднее время блока, транзакции в минуту и потребление ресурсов вычислений, можно легко контролировать через пользовательскую панель мониторинга. Затем ресурсы можно настроить в зависимости от требований к масштабированию.

Общие рекомендации по разработке масштабируемого сценария см. в [контрольном списке для обеспечения масштабируемости][scalability] в Центре архитектуры Azure.

### <a name="security"></a>Безопасность

[Azure Key Vault][vault] используется для простого хранения и управления закрытыми ключами проверяющего элемента управления. Развертывание по умолчанию в этом примере создает сеть блокчейна, доступную через Интернет. Для сценария производства, где требуется частная сеть, члены могут быть подключены друг к другу через подключение VPN-шлюза типа "виртуальная сеть — виртуальная сеть". Шаги по настройке VPN включены в разделе связанных ресурсов ниже.

Общие рекомендации по разработке безопасных решений см. в разделе [Azure Security Documentation][security].

### <a name="resiliency"></a>Устойчивость

Блокчейн Ethereum PoA сам по себе может обеспечить некоторую степень устойчивости, поскольку узлы проверяющего элемента управления могут быть развернуты в разных регионах. Azure имеет параметры для развертывания в более чем 54 регионах по всему миру. Блокчейн, как в этом сценарии, предоставляет уникальные и обновленные возможности сотрудничества для повышения устойчивости. Устойчивость сети обеспечивается не только одной централизованной стороной, но и всеми членами консорциума. Блокчейн, основанный на алгоритме Proof-of-Authority, позволяет повысить устойчивость сети и ее работоспособность.

Общее руководство по проектированию устойчивых решений см. в разделе [Проектирование устойчивых приложений для Azure][resiliency].

## <a name="pricing"></a>Цены

Чтобы изучить стоимость выполнения этого сценария, все услуги были предварительно сконфигурированы в калькуляторе стоимости. Чтобы узнать, как изменится цена для вашего конкретного варианта использования, измените соответствующие переменные в соответствии с ожидаемыми требованиями к производительности и доступности.

Мы предоставили три примера профилей затрат, основанных на количестве экземпляров масштабируемых наборов виртуальных машин для запуска приложений (экземпляры могут находиться в разных регионах).

* [Небольшой][small-pricing]. Этот пример тарификации предполагает наличие 2 виртуальных машин в месяц с отключенным мониторингом.
* [Средний][medium-pricing]. Этот пример тарификации предполагает наличие 7 виртуальных машин в месяц с включенным мониторингом.
* [Большой][large-pricing]. Этот пример тарификации предполагает наличие 15 виртуальных машин в месяц с включенным мониторингом.

Вышеуказанная цена предназначена для того, чтобы один участник консорциума запустил сеть блокчейна или подключился к ней. Как правило, в консорциуме, где задействовано несколько компаний или организаций, каждый участник получит свою собственную подписку Azure.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы увидеть пример этого сценария, разверните [демонстрационное приложение блокчейна Ethereum PoA][deploy] в Azure, а затем перейдите к [README исходного кода сценария][source].

## <a name="related-resources"></a>Связанные ресурсы

Дополнительные сведения об использовании шаблона решения сети Proof-of-Authority в Ethereum для Azure см. в [руководстве по использованию][guide].

<!-- links -->
[small-pricing]: https://azure.com/e/4e429d721eb54adc9a1558fae3e67990
[medium-pricing]: https://azure.com/e/bb42cd77437744be8ed7064403bfe2ef
[large-pricing]: https://azure.com/e/e205b443de3e4adfadf4e09ffee30c56
[guide]: /azure/blockchain-workbench/ethereum-poa-deployment
[deploy]: https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium
[source]: https://github.com/vitoc/creditscoreblockchain
[monitor]: /azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor
[availability]: /azure/architecture/checklist/availability
[scalability]: /azure/architecture/checklist/scalability
[resiliency]: ../../resiliency/index.md
[security]: /azure/security/
[vault]: https://azure.microsoft.com/services/key-vault/