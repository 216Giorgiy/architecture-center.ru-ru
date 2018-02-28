---
title: "Руководство по архитектуре данных в Azure"
description: 
author: zoinerTejada
ms:date: 02/12/2018
layout: LandingPage
ms.openlocfilehash: 848601f27faf56ea069852d8983e4d10fbad9d77
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2018
---
# <a name="azure-data-architecture-guide"></a>Руководство по архитектуре данных в Azure

В этом руководстве представлен структурированный подход к разработке решений для обработки данных в Microsoft Azure. Подход основан на проверенных методах, которые мы узнали от клиентов.

## <a name="introduction"></a>Введение

Облако изменяет структуру приложений, в том числе способ обработки и хранения данных. Вместо одной базы данных общего назначения, которая обрабатывает все данные решения, решения с поддержкой _Polyglot Persistence (сочетания нескольких технологий хранения данных)_ используют несколько специализированных хранилищ данных, каждое из которых оптимизировано для предоставления определенных возможностей. В результате меняется и представление о данных в решении. Больше не используется несколько слоев бизнес-логики для чтения и записи данных в пределах одного слоя. Вместо этого решения разрабатываются на основе *конвейера данных*, который описывает, как данные перемещаются в решении, где они обрабатываются и хранятся, а также как используются следующим компонентом в конвейере. 

## <a name="how-this-guide-is-structured"></a>Структура руководства

В этом руководстве рассматриваются базовые сведения о различии между *реляционными* и *нереляционными* данными. 

![](./images/guide-steps.svg)

Как правило, реляционные данные хранятся в традиционной реляционной СУБД или хранилище данных. Эти данные имеют предварительно определенную схему (схема при записи) с набором ограничений для обеспечения целостности данных. Для запросов большинство реляционных баз данных используют язык SQL. К решениям, которые используют реляционные базы данных, относятся OLTP (оперативная обработка транзакций) и OLAP (оперативная аналитическая обработка).

Нереляционные данные — это все данные, не использующие [реляционную модель](https://en.wikipedia.org/wiki/Relational_model), представленную в традиционных системах управления реляционной базой данных. К таким данным относятся данные типа "ключ — значение", данные JSON, данные графа, данные временных рядов и другие типы данных. Термин *NoSQL* относится к базам данных, которые предназначены для хранения нереляционных данных различных типов. Но этот термин не является точным, так как многие нереляционные хранилища данных поддерживают SQL-совместимые запросы. Нереляционные данные и базы данных NoSQL часто рассматриваются, когда речь заходит о решениях для *больших данных*. Архитектура для обработки больших данных позволяет принимать, обрабатывать и анализировать данные, которые являются слишком объемными или слишком сложными для традиционных систем баз данных. 

В каждой из двух основных категорий руководства по архитектуре данных содержатся следующие разделы:

- **Основные понятия.** Обзорные статьи, в которых объясняются основные понятия, необходимые при работе с этим типом данных.
- **Сценарии.** Набор примеров сценариев работы с данными, включая описание соответствующих служб Azure и подходящей архитектуры для сценария.
- **Возможность выбора технологий.** Подробное сравнение различных технологий обработки данных, доступных в Azure, включая варианты с открытым кодом. В каждой категории приводятся основные критерии выбора и матрица возможностей. Это позволит вам выбрать подходящую технологию для вашего сценария.

Это руководство не предназначено для изучения теории баз данных или обработки и анализа данных &mdash; этим темам посвящены целые книги. Цель этого руководства — помочь вам выбрать правильную архитектуру данных или конвейер данных для вашего сценария, а также службы и технологии Azure, которые лучше всего соответствуют вашим требованиям. Если вы уже знаете, какая архитектура вам нужна, можете сразу перейти к выбору технологии.

## <a name="traditional-rdbms"></a>Традиционные реляционные СУБД

### <a name="concepts"></a>Основные понятия

- [Реляционные данные](./concepts/relational-data.md) 
- [Данные о транзакциях](./concepts/transactional-data.md) 
- [Семантическое моделирование](./concepts/semantic-modeling.md) 

### <a name="scenarios"></a>Сценарии

- [Оперативная аналитическая обработка (OLAP)](./scenarios/online-analytical-processing.md)
- [Оперативная обработка транзакций (OLTP)](./scenarios/online-transaction-processing.md) 
- [Хранение данных и киоски данных](./scenarios/data-warehousing.md)
- [Извлечение, преобразование и загрузка](./scenarios/etl.md) 

## <a name="big-data-and-nosql"></a>Большие данные и NoSQL

### <a name="concepts"></a>Основные понятия

- [Нереляционные хранилища данных](./concepts/non-relational-data.md)
- [Работа с CSV- и JSON-файлами](./concepts/csv-and-json.md)
- [Варианты архитектуры для обработки больших данных](./concepts/big-data.md)
- [Расширенная аналитика](./concepts/advanced-analytics.md) 
- [Машинное обучение с поддержкой масштабирования](./concepts/machine-learning-at-scale.md)

### <a name="scenarios"></a>Сценарии

- [Пакетная обработка](./scenarios/batch-processing.md)
- [Обработка в режиме реального времени](./scenarios/real-time-processing.md)
- [Текстовый поиск со свободной формой записи](./scenarios/search.md)
- [Интерактивное исследование данных](./scenarios/interactive-data-exploration.md)
- [Обработка естественного языка](./scenarios/natural-language-processing.md)
- [Решения для временных рядов](./scenarios/time-series.md)

## <a name="cross-cutting-concerns"></a>Сквозные функции

- [Передача данных в службы Azure и обратно](./scenarios/data-transfer.md) 
- [Расширение локальных решений для работы с данными в облако](./scenarios/hybrid-on-premises-and-cloud.md) 
- [Защита решений для работы с данными](./scenarios/securing-data-solutions.md) 