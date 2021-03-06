---
title: CAF. Метрики, индикаторы и допустимые риски ускорения развертывания
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Метрики, индикаторы и допустимые риски ускорения развертывания
author: alexbuckgit
ms.openlocfilehash: 87d6f9f67b98d5761aced560392c9d38fa682ee7
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241215"
---
# <a name="deployment-acceleration-metrics-indicators-and-risk-tolerance"></a>Метрики, индикаторы и допустимые риски ускорения развертывания

Эта статья поможет вам оценить допустимость бизнес-рисков в отношении укоренения развертывания. Определение метрик и показателей позволит создать экономическое обоснование для принятия решений об инвестициях в развитие дисциплины "Укоренение развертывания".

## <a name="metrics"></a>Метрики

Ускорение развертывания сосредоточено на развертывании, обновлении и обслуживании облачных ресурсов, настроенных для правильной работы систем. При внедрении этой дисциплины управления облаком полезны следующие сведения:

- **Целевое время восстановления (RTO).** Это максимально допустимое время, в течение которого приложение может быть недоступно после инцидента.
- **Целевая точка восстановления (RPO).** Это максимальная продолжительность потери данных, которая является приемлемой во время аварии. Например, если вы храните данные в одной базе данных без репликации в другие базы данных и ежечасно выполняете резервное копирование, вы можете потерять до часа данных.
- **Среднее время для восстановления (MTTR)**. Среднее время, необходимое для восстановления после сбоя.
- **Среднее время безотказной работы (MTBF)**. Продолжительность работы, которую можно ожидать от компонента между отключениями. Эта метрика позволяет рассчитать, как часто служба будет недоступна.
- **Соглашения об уровне обслуживания (SLA)**. Они могут включать в себя как обязательства корпорации Майкрософт по бесперебойной работе и подключению служб Azure, так и обязательства бизнеса перед внешними и внутренними клиентами.
- **Время развертывания**. Время, необходимое для развертывания обновлений в имеющейся системе.
- **Ресурсы, которые не соответствует политике доступа**. Количество или процент ресурсов, не соответствующих определенным политикам.

## <a name="risk-tolerance-indicators"></a>Индикаторы рискоустойчивости

Риски, связанные с ускорением развертывания, в значительной степени связаны с количеством и сложностью облачных систем, развернутых для вашего предприятия. По мере роста облачных ресурсов количество развернутых систем и частота обновления облачных ресурсов будет увеличиваться. Зависимости между ресурсами повышают важность обеспечения правильной конфигурации ресурсов и разработки систем для обеспечения отказоустойчивости в случае непредвиденного простоя одного или нескольких ресурсов.

<!-- "en-us" location is required for the URL below. -->

Рассмотрите возможность внедрения DevOps или организационной культуры [DevSecOps](https://www.microsoft.com/en-us/securityengineering/devsecops) на ранней стадии внедрения облака. В традиционных корпоративных ИТ-организациях отделы операций, безопасности и команды разработчиков часто изолированы и плохо сотрудничают или даже враждебны друг к другу. Признание этих проблем на раннем этапе и интеграция ключевых заинтересованных сторон из каждой команды может помочь обеспечить гибкость при внедрении облачной среды при высоком уровне безопасности и хорошей управляемости.

Совместно с командой DevSecOps и заинтересованными сторонами определите [бизнес-риски](business-risks.md), связанные с конфигурацией, а затем приемлемые базовые показатели для устойчивости к рискам конфигурации. Примеры в этом разделе руководства CAF дадут вам определенные ориентиры, но конкретный список рисков и требований для вашей компании и ваших развертываний может существенно отличаться.

Определив базовые показатели, установите минимальные характеристики недопустимого увеличения выявленных рисков. Эти характеристики будут информировать вас о необходимости предпринимать действия для устранения этих рисков. В примерах ниже показано, как описанные выше и другие метрики конфигурации могут обосновать повышение инвестиций в дисциплину "Ускорение развертывания".

- **Триггер соглашения об уровне обслуживания (SLA)**. Компания, которая не может выполнить свои Соглашения об уровне обслуживания для своих внешних клиентов или внутренних партнеров, должна инвестировать в дисциплину "Ускорение развертывания", чтобы сократить время простоя системы.
- **Триггеры времени восстановления**. Если компания превышает требуемые пороговые значения времени восстановления после сбоя системы, она должна инвестировать в улучшение дисциплины "Ускорение развертывания" и проектирование систем, чтобы уменьшить или устранить сбои или последствия простоя отдельных компонентов.
- **Триггеры смещения конфигурации**. Компания, которая испытывает непредвиденные изменения в конфигурации ключевых компонентов системы или сбои в развертывании или обновлениях своих систем, должна инвестировать в дисциплину "Ускорение развертывания", чтобы определить основные причины и шаги для исправления.  
- **Триггеры несоответствия** Если количество ресурсов, не соответствующих требованиям, превышает заданное пороговое значение (общее количество ресурсов или процент от общего объема ресурсов), компания должна инвестировать в улучшение дисциплины "Ускорение развертывания", чтобы обеспечить соответствие конфигурации каждого ресурса на протяжении всего его жизненного цикла.
- **Триггеры расписания проекта**. Если время развертывания ресурсов и приложений компании часто превышает пороговое значение, компания должна инвестировать в процессы ускорения развертывания, чтобы внедрить или улучшить автоматизированные развертывания для обеспечения согласованности и предсказуемости. Время развертывания, измеряемое в днях или даже неделях, обычно указывает на неоптимальную стратегию ускорения развертывания.

## <a name="next-steps"></a>Дополнительная информация

С помощью [шаблона облачного управления](./template.md) задокументируйте метрики и индикаторы рискоустойчивости, соответствующие текущему плану внедрения облака.

Основываясь на рисках и рискоустойчивости, создайте процесс управления и информирования о соблюдении политики Ускорения развертывания.

> [!div class="nextstepaction"]
> [Разработка процессов, обеспечивающих соблюдение политик](compliance-processes.md)
