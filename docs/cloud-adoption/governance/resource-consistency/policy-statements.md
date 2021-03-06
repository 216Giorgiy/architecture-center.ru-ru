---
title: CAF. Примеры правил политики согласованности ресурсов
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Примеры правил политики согласованности ресурсов
author: BrianBlanchard
ms.openlocfilehash: 9217ae73b0edf5b40bedac1cdc961b7a34da87d1
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901467"
---
# <a name="resource-consistency-sample-policy-statements"></a>Примеры правил политики согласованности ресурсов

Отдельные правила облачной политики служат рекомендациями по устранению конкретных рисков, выявленных в процессе оценки рисков. В этих правилах должна предоставляться краткая сводка по рискам и планам их устранения. Каждое определение правила должно включать следующие сведения:

- Технический риск. Сводная информация о риске, которому посвящена эта политика.
- Правила политики. Четкое описание требований политики.
- Варианты архитектуры. Практические рекомендации, спецификации или другие руководства, которые позволят ИТ-специалистам и разработчикам реализовать эту политику.

Следующие примеры правил политики нацелены на несколько распространенных бизнес-рисков, связанных с согласованностью ресурсов. Вы можете использовать их как основу для разработки правил политики по конкретным потребностям организации. Обратите внимание, что не следует рассматривать эти примеры как строгие рекомендации, ведь почти для любого определенного риска существует несколько возможных параметров политик. Лучшие решения для политик по уникальным наборам рисков следует искать в тесном сотрудничестве с подразделениями организации и ИТ-службами.

## <a name="tagging"></a>Добавление тегов

**Технический риск**. Без надлежащего добавления тегов на основе метаданных, связанных с развернутыми ресурсами, ИТ-отделы не могут определить, каким ресурсам отдавать предпочтение при поддержке или оптимизации на основе требуемого соглашения об уровне обслуживания, важности бизнес-операций или операционных расходов. Это может привести к неправильному распределению ИТ-ресурсов и потенциальным задержкам в разрешении инцидентов.

**Правило политики**. Будут реализованы следующие политики.

- Развернутые ресурсы должны быть помечены следующими значениями: стоимость, критичность, Соглашение об уровне обслуживания и среда.
- Средства управления должны проверять теги, относящиеся к классификации затрат, уровня важности, Соглашения об уровне обслуживания, приложений и среды. Все значения должны совпадать с предустановленными значениями, управляемыми командой управления.

**Возможные варианты архитектуры.** Большинство типов ресурсов в Azure поддерживают [стандартные теги метаданных типа "имя — значение"](/azure/azure-resource-manager/resource-group-using-tags). [Политика Azure](/azure/governance/policy/overview) используется для принудительного применения конкретных тегов в процессе создания ресурса.

## <a name="ungoverned-subscriptions"></a>Неуправляемые подписки

**Технический риск**. Произвольное создание подписок и групп управления может привести к изолированным разделам вашего облачного пространства, на которые не распространяются ваши политики управления.

**Правило политики**. Создание новых подписок или групп управления для любых критически важных приложений или защищенных данных потребует проверки со стороны команды управления облачными решениями. Утвержденные изменения будут включены в надлежащее назначение схемы.

**Возможные варианты архитектуры.** Предоставляйте административный доступ к [группам управления Azure](/azure/governance/management-groups/) в организации только утвержденным участникам команды управления, которые будут контролировать процесс создания подписки и управлять доступом.

## <a name="manage-updates-to-virtual-machines"></a>Управление обновлениями виртуальных машин

**Технический риск**. Виртуальные машины, на которых не установлены последние обновления и исправления программного обеспечения, являются уязвимыми к проблемам безопасности или производительности, которые могут привести к сбоям в обслуживании.

**Правило политики**. Средства управления должны обеспечивать установку автоматических обновлений на всех развернутых виртуальных машинах. Нарушения следует рассмотреть с привлечением команд операционного управления и устранить в соответствии с операционными политиками. Ресурсы, которые не обновляются автоматически, необходимо включить в процессы, контролируемые командой ИТ-операций.

**Возможные варианты архитектуры.** Для виртуальных машин, размещенных в Azure, вы можете обеспечить согласованное управление обновлениями, используя [решение по управлению обновлениями в службе автоматизации Azure](/azure/automation/automation-update-management).

## <a name="deployment-compliance"></a>Соответствие требованиям для развертывания

**Технический риск**. Сценарии развертывания и средства автоматизации, которые не полностью проверены командой управления облачными решениями, могут привести к развертыванию ресурсов, нарушающему политики.

**Правило политики**. Будут реализованы следующие политики.

- Средства развертывания должны быть утверждены командой управления облачными решениями для обеспечения непрерывного управления развернутыми ресурсами.
- Сценарии развертывания должны храниться в центральном репозитории, который доступен команде управления облачными решениями для периодической проверки и аудита.

**Возможные варианты архитектуры.** Последовательное использование [Azure Blueprints](/azure/governance/blueprints/) для управления автоматизированными развертываниями дает возможность согласованно развертывать ресурсы Azure, которые соответствуют стандартам и политикам управления вашей организации.

## <a name="monitoring"></a>Мониторинг

**Технический риск**. Неправильно реализованный или несогласованный мониторинг может предотвратить обнаружение проблем работоспособности рабочей нагрузки или других нарушений соответствия политике.

**Правило политики**. Будут реализованы следующие политики.

- Средства управления должны проверять, что все ресурсы, связанные с критически важными приложениями или защищенными данными, включены в мониторинг для оптимизации и наблюдения за истощением ресурсов.
- Средства управления должны проверять, что данные журнала соответствующего уровня собираются для всех критически важных приложений или защищенных данных.

**Возможные варианты архитектуры.** [Azure Monitor](/azure/azure-monitor/overview) является службой мониторинга по умолчанию на платформе Azure, а согласованный мониторинг можно применять, используя [Azure Blueprints](/azure/governance/blueprints/) при развертывании ресурсов.

## <a name="disaster-recovery"></a>Аварийное восстановление

**Технический риск**. Сбой ресурса, удаление или повреждение могут привести к сбою критически важных приложений или служб и потере конфиденциальных данных.

**Правило политики**. Во всех критически важных приложениях и защищенных данных должны быть реализованы решения для резервного копирования и восстановления, чтобы минимизировать влияние сбоев или ошибок системы на бизнес.

**Возможные варианты архитектуры.** Служба [Azure Site Recovery] предоставляет возможности резервного копирования, восстановления и репликации, предназначенные для минимизации времени простоя в сценариях непрерывности бизнеса и аварийного восстановления.

## <a name="next-steps"></a>Дополнительная информация

Используйте примеры, приведенные в этой статье, в качестве отправной точки для разработки политик по конкретным бизнес-рискам, соответствующих вашим планам внедрения облачных систем.

Чтобы начать разработку собственных пользовательских правил политики, связанных с согласованностью ресурсов, скачайте [шаблон согласованности ресурсов](template.md).

Чтобы ускорить внедрение этой дисциплины, выберите [практические рекомендации по управлению](../journeys/overview.md), которые больше всего подходят вашей среде. Затем измените архитектуру с учетом принятых решений по корпоративной политике.

> [!div class="nextstepaction"]
> [Стратегии действенного управления](../journeys/overview.md)
