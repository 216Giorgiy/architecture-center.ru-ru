---
title: CAF. Примеры правил политики управления затратами
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 1/4/2019
description: Примеры правил политики управления затратами
author: BrianBlanchard
ms.openlocfilehash: 1c8b94ae5285fa26cdf9760892beaced2487af8b
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55902520"
---
# <a name="cost-management-sample-policy-statements"></a>Примеры правил политики управления затратами

Отдельные правила облачной политики служат рекомендациями по устранению конкретных рисков, выявленных в процессе оценки рисков. В этих правилах должна предоставляться краткая сводка по рискам и планам их устранения. Каждое определение правила должно включать следующие сведения:

- Бизнес-риск. Сводная информация о риске, которому посвящена эта политика.
- Правила политики. Четкое описание требований политики.
- Варианты архитектуры. Практические рекомендации, спецификации или другие руководства, которые позволят ИТ-специалистам и разработчикам реализовать эту политику.

Следующие примеры правил политики нацелены на несколько распространенных бизнес-рисков, связанных с затратами. Вы можете использовать их как основу для разработки реальных правил политики по конкретным потребностям организации. Не следует рассматривать эти примеры как строгие рекомендации, ведь почти для каждого выявленного риска существует несколько возможных вариантов политик. Лучшие решения для политик по конкретным рискам следует искать в тесном сотрудничестве с подразделениями организации и ИТ-службами.  

## <a name="future-proofing"></a>Перспективность

**Бизнес-риск**. Текущие условия не гарантируют внимания команды управления к дисциплине управления затратами. Но вы полагаете, что в будущем такое внимание появится.

**Правило политики**. Все развернутые в облаке ресурсы следует согласовать с отделом выставления счетов, а также сопоставить с приложениями или рабочими нагрузками и проверить соблюдение стандартов именования. Эта политика гарантирует эффективность будущих усилий по управлению затратами.

**Варианты архитектуры.** Сведения о создании перспективной основы можно получить в разделе обсуждений по созданию MVP управления, которые включены в инструкции по CAF в виде [практических руководств по разработке](../journeys/overview.md).

## <a name="budget-overruns"></a>Превышения бюджета

**Бизнес-риск**. Самостоятельное развертывание влечет за собой риск перерасхода.

**Правило политики**. Учет всех облачных развертываний должен быть закреплен за отделом выставления счетов, имеющим утвержденный бюджет и механизм применения бюджетных ограничений.

**Варианты архитектуры.** В Azure управление бюджетом можно реализовать с помощью службы [Управление затратами Azure](/azure/cost-management/manage-budgets).

## <a name="underutilization"></a>Недостаточно эффективное использование

**Бизнес-риск**. Компания заранее оплачивает облачные службы или выделила определенную сумму на год. Есть риск, что согласованный объем ресурсов не будет использован полностью, что означает потерю инвестиций.

**Правило политики**. Каждый отдел выставления счетов с выделенным бюджетом на облачные службы будет проводить совещания ежегодно для определения бюджетов, ежеквартально для корректировки бюджетов и ежемесячно для планирования действий по сравнению бюджетных и фактических расходов. Ежемесячно обсуждайте любые отклонения свыше 20 % с руководителем отдела выставления счетов. В целях учета передавайте данные о всех ресурсах отделу выставления счетов.

**Варианты архитектуры.**

- В Azure управление плановыми и фактическими расходами можно осуществлять с помощью [Управления затратами Azure](/azure/cost-management/quick-acm-cost-analysis).
- Существует несколько вариантов группировки ресурсов отделом выставления счетов. Совместно с командой управления следует выбрать для Azure [модель согласованности ресурсов](../../decision-guides/resource-consistency/overview.md) и применить ее ко всем ресурсам.

## <a name="overprovisioned-assets"></a>Избыточная подготовка ресурсов

**Бизнес-риск**. В традиционных локальных центрах обработки данных часто применяется практика избыточного развертывания ресурсов с расчетом на расширение в отдаленном будущем. Облако масштабируется намного быстрее, чем обычное оборудование. Кроме того, ресурсы в облаке оплачиваются пропорционально их технической мощности. Существует риск того, что прежний подход, характерный для локальной инфраструктуры, искусственно раздует затраты на облако.

**Правило политики**. Любой развернутый в облаке ресурс должен быть зарегистрирован в программе, позволяющей отслеживать использование и информировать о всех мощностях, использование которых превышает 50 %. Любому развернутому в облаке ресурсу следует назначить группу или тег с логичным значением, чтобы команда управления всегда могла связаться с владельцем рабочей нагрузки для обсуждения оптимизации при избыточной подготовке ресурсов.

**Варианты архитектуры.**

- В Azure рекомендации по оптимизации можно получать от [Помощника по Azure](/azure/advisor/advisor-cost-recommendations).
- Существует несколько вариантов группировки ресурсов отделом выставления счетов. Совместно с командой управления следует выбрать для Azure [модель согласованности ресурсов](../../decision-guides/resource-consistency/overview.md) и применить ее ко всем ресурсам.

## <a name="overoptimization"></a>Избыточная оптимизация

**Бизнес-риск**. Эффективное управление затратами иногда создает новые риски. Оптимизация расходов напрямую противоречит производительности системы. Снижение затрат может приводить к ухудшению взаимодействий с пользователями.

**Правило политики**. Следует выделить с помощью групп или тегов все ресурсы, которые непосредственно влияют на взаимодействие с клиентом. Прежде чем выполнять оптимизацию для любого ресурса, который влияет на взаимодействие с клиентом, команде управления облаком следует принять во внимание тенденции его использования не менее чем за 90 дней. Документируйте любые пики нагрузки, связанные с сезонными изменениями или конкретными событиями, которые нужно учитывать при оптимизации.

**Варианты архитектуры.**

- В Azure есть [аналитические функции Azure Monitor](/azure/azure-monitor/insights/vminsights-performance), которые помогут в анализе использования системы.
- Также существует несколько методов группировки и маркировки ресурсов с учетом их ролей. Совместно с группой управления следует выбрать для Azure [модель согласованности ресурсов](../../decision-guides/resource-consistency/overview.md) и применить ее ко всем ресурсам.

## <a name="next-steps"></a>Дополнительная информация

Используйте примеры, приведенные в этой статье, в качестве отправной точки для разработки политик по конкретным бизнес-рискам, соответствующих вашим планам внедрения облачных систем.

Чтобы начать создание собственных правил пользовательской политики, имеющих отношение к управлению затратами, скачайте [шаблон управления затратами](template.md).

Чтобы ускорить внедрение этой дисциплины, выберите [практические рекомендации по управлению](../journeys/overview.md), которые больше всего подходят вашей среде. Затем измените архитектуру с учетом принятых решений по корпоративной политике.

> [!div class="nextstepaction"]
> [Стратегии действенного управления](../journeys/overview.md)
