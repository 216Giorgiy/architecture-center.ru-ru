---
title: CAF. Метрики, индикаторы и допустимость риска в базовой системе безопасности
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Метрики, индикаторы и допустимость риска в базовой системе безопасности
author: BrianBlanchard
ms.openlocfilehash: 30deafca59b2e09c78432ad3b59d328fb27a1e2c
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241555"
---
# <a name="security-baseline-metrics-indicators-and-risk-tolerance"></a><span data-ttu-id="ac543-103">Метрики, индикаторы и допустимость риска в базовой системе безопасности</span><span class="sxs-lookup"><span data-stu-id="ac543-103">Security Baseline metrics, indicators, and risk tolerance</span></span>

<span data-ttu-id="ac543-104">В этой статье описывается, как оценить допустимость бизнес-рисков в отношении базовой системы безопасности.</span><span class="sxs-lookup"><span data-stu-id="ac543-104">This article is intended to help you quantify business risk tolerance as it relates to Security Baseline.</span></span> <span data-ttu-id="ac543-105">Определение метрик и показателей позволит создать экономическое обоснование для принятия решений об инвестициях в развитие дисциплины базовой системы безопасности.</span><span class="sxs-lookup"><span data-stu-id="ac543-105">Defining metrics and indicators helps you create a business case for making an investment in maturing the Security Baseline discipline.</span></span>

## <a name="metrics"></a><span data-ttu-id="ac543-106">Метрики</span><span class="sxs-lookup"><span data-stu-id="ac543-106">Metrics</span></span>

<span data-ttu-id="ac543-107">Базовая система безопасности обычно нацелена на выявление потенциальных уязвимостей в облачных развертываниях.</span><span class="sxs-lookup"><span data-stu-id="ac543-107">Security Baseline generally focuses on identifying potential vulnerabilities in your cloud deployments.</span></span> <span data-ttu-id="ac543-108">В процессе анализа рисков вам нужно собрать данные, связанные со средой безопасности, чтобы определить степень имеющегося риска и важность инвестиций в базовую систему управления безопасностью для запланированных облачных развертываний.</span><span class="sxs-lookup"><span data-stu-id="ac543-108">As part of your risk analysis you'll want to gather data related to your security environment to determine how much risk you face, and how important investment in Security Baseline governance is to your planned cloud deployments.</span></span>

<span data-ttu-id="ac543-109">У каждой организации есть уникальные характеристики и требования к безопасности, а также разные возможные источники данных о безопасности.</span><span class="sxs-lookup"><span data-stu-id="ac543-109">Every organization has different security environments and requirements and different potential sources of security data.</span></span> <span data-ttu-id="ac543-110">Ниже приведены примеры полезных метрик, которые вы можете собрать для оценки риска по дисциплине базовой системы безопасности.</span><span class="sxs-lookup"><span data-stu-id="ac543-110">The following are examples of useful metrics that you should gather to help evaluate risk tolerance within the Security Baseline discipline:</span></span>

- <span data-ttu-id="ac543-111">**Классификация данных**.</span><span class="sxs-lookup"><span data-stu-id="ac543-111">**Data classification**.</span></span> <span data-ttu-id="ac543-112">Количество данных и служб, размещенных в облаке и не имеющих классификации по корпоративным стандартам конфиденциальности, соответствия требованиям или бизнеса.</span><span class="sxs-lookup"><span data-stu-id="ac543-112">Number of cloud-stored data and services that are unclassified according to on your organization's privacy, compliance, or business impact standards.</span></span>
- <span data-ttu-id="ac543-113">**Число хранилищ конфиденциальных данных**. Количество конечных точек хранилища и (или) баз данных, которые содержат конфиденциальные данные и должны быть защищены.</span><span class="sxs-lookup"><span data-stu-id="ac543-113">**Number of sensitive data stores** - Number of storage end points or databases that contain sensitive data and should be protected.</span></span>
- <span data-ttu-id="ac543-114">**Число хранилищ незашифрованных данных**. Число хранилищ конфиденциальных и незашифрованных данных.</span><span class="sxs-lookup"><span data-stu-id="ac543-114">**Number of unencrypted data stores** - Number of sensitive data stores that are not encrypted.</span></span>
- <span data-ttu-id="ac543-115">**Поверхность атаки**.</span><span class="sxs-lookup"><span data-stu-id="ac543-115">**Attack surface**.</span></span> <span data-ttu-id="ac543-116">Общее количество источников данных, служб и приложений, которые будут размещены в облаке.</span><span class="sxs-lookup"><span data-stu-id="ac543-116">How many total data sources, services, and applications will be cloud-hosted.</span></span> <span data-ttu-id="ac543-117">Какой процент этих источников классифицируются как конфиденциальные?</span><span class="sxs-lookup"><span data-stu-id="ac543-117">What percentage of these data sources are classified as sensitive?</span></span> <span data-ttu-id="ac543-118">Какой процент этих приложений и служб считаются критически важными?</span><span class="sxs-lookup"><span data-stu-id="ac543-118">What percentage of these applications and services are mission-critical?</span></span>
- <span data-ttu-id="ac543-119">**Охваченные стандарты**.</span><span class="sxs-lookup"><span data-stu-id="ac543-119">**Covered Standards**.</span></span> <span data-ttu-id="ac543-120">Количество стандартов безопасности, определенных группой безопасности.</span><span class="sxs-lookup"><span data-stu-id="ac543-120">Number of security standards defined by the Security team.</span></span>
- <span data-ttu-id="ac543-121">**Охваченные ресурсы**.</span><span class="sxs-lookup"><span data-stu-id="ac543-121">**Covered Resources**.</span></span> <span data-ttu-id="ac543-122">Развернутые ресурсы, охваченные стандартами безопасности.</span><span class="sxs-lookup"><span data-stu-id="ac543-122">Deployed assets that are covered by security standards.</span></span>
- <span data-ttu-id="ac543-123">**Общее соответствие стандартам**.</span><span class="sxs-lookup"><span data-stu-id="ac543-123">**Overall Standards Compliance**.</span></span> <span data-ttu-id="ac543-124">Процент соблюдения стандартов безопасности.</span><span class="sxs-lookup"><span data-stu-id="ac543-124">Ratio of compliance adherence to security standards.</span></span>
- <span data-ttu-id="ac543-125">**Атаки по уровням серьезности**.</span><span class="sxs-lookup"><span data-stu-id="ac543-125">**Attacks by Severity**.</span></span> <span data-ttu-id="ac543-126">Сколько скоординированных попыток нарушить работу ваших облачных служб, например, посредством атак распределенного отказа в обслуживании (DDoS), испытывает ваша инфраструктура?</span><span class="sxs-lookup"><span data-stu-id="ac543-126">How many coordinated attempts to disrupt your cloud-hosted services, such as through Distributed Denial of Service (DDoS) attacks, does your infrastructure experience?</span></span> <span data-ttu-id="ac543-127">Какой масштаб и уровень серьезности у этих атак?</span><span class="sxs-lookup"><span data-stu-id="ac543-127">What is the size and severity of these attacks?</span></span>
- <span data-ttu-id="ac543-128">**Защита от вредоносных программ**.</span><span class="sxs-lookup"><span data-stu-id="ac543-128">**Malware protection**.</span></span> <span data-ttu-id="ac543-129">Процент развернутых виртуальных машин, на которых установлены все необходимые программные средства защиты от вредоносных программ, брандмауэры и т. п.</span><span class="sxs-lookup"><span data-stu-id="ac543-129">Percentage of deployed virtual machines (VMs) that have all required anti-malware, firewall, or other security software installed.</span></span>
- <span data-ttu-id="ac543-130">**Задержка исправлений**.</span><span class="sxs-lookup"><span data-stu-id="ac543-130">**Patch latency**.</span></span> <span data-ttu-id="ac543-131">Сколько времени прошло с момента применения исправлений ОС и программного обеспечения на виртуальных машинах?</span><span class="sxs-lookup"><span data-stu-id="ac543-131">How long has it been since VMs have had OS and software patches applied.</span></span>
- <span data-ttu-id="ac543-132">**Рекомендации по обеспечению безопасности**.</span><span class="sxs-lookup"><span data-stu-id="ac543-132">**Security health recommendations**.</span></span> <span data-ttu-id="ac543-133">Количество рекомендаций по безопасности для программного обеспечения, которые необходимо применить для устранения несоответствий стандартам безопасности для развернутых ресурсов, с разбивкой по степени серьезности.</span><span class="sxs-lookup"><span data-stu-id="ac543-133">Number of security software recommendations for resolving health standards for deployed resources, organized by severity.</span></span>

## <a name="risk-tolerance-indicators"></a><span data-ttu-id="ac543-134">Индикаторы допустимости риска</span><span class="sxs-lookup"><span data-stu-id="ac543-134">Risk tolerance indicators</span></span>

<span data-ttu-id="ac543-135">Облачные платформы предоставляют базовый набор возможностей, которые позволяют небольшим командам развертывания настроить простые параметры безопасности без долгого дополнительного планирования.</span><span class="sxs-lookup"><span data-stu-id="ac543-135">Cloud platforms provide a baseline set of features that enable small deployment teams to configure basic security settings without extensive additional planning.</span></span> <span data-ttu-id="ac543-136">Это означает, что для небольших рабочих нагрузок разработки, тестирования или экспериментов, которые не содержат конфиденциальных данных, уровень риска будет относительно низок и вряд ли попадет под действие официальной политики базовой системы безопасности.</span><span class="sxs-lookup"><span data-stu-id="ac543-136">As a result, small Dev/Test or experimental first workloads that do not include sensitive data represent a relatively low level of risk, and will likely not need much in the way of formal Security Baseline policy.</span></span> <span data-ttu-id="ac543-137">Но как только в облако будут перемещены важные данные или критически важные функции, риск безопасности увеличится, а допустимость этого риска резко снизится.</span><span class="sxs-lookup"><span data-stu-id="ac543-137">However, as soon as important data or mission-critical functionality is moved to the cloud, security risks increase, while tolerance for those risks diminishes rapidly.</span></span> <span data-ttu-id="ac543-138">По мере развертывания в облаке новых данных и функций постоянно повышается вероятность того, что потребуются дополнительные инвестиции по дисциплине базовой системы безопасности.</span><span class="sxs-lookup"><span data-stu-id="ac543-138">As more of your data and functionality is deployed to the cloud, the more likely you need an increased investment in the Security Baseline discipline.</span></span>

<span data-ttu-id="ac543-139">На ранних стадиях внедрения облака согласуйте с командой ИТ-безопасности и заинтересованными лицами список относящихся к безопасности [бизнес-рисков](business-risks.md) и определите приемлемые базовые показатели допустимости таких рисков.</span><span class="sxs-lookup"><span data-stu-id="ac543-139">In the early stages of cloud adoption, work with your IT security team and business stakeholders to identify [business risks](business-risks.md) related to security, then determine an acceptable baseline for security risk tolerance.</span></span> <span data-ttu-id="ac543-140">Примеры в этом разделе CAF дадут вам определенные ориентиры, но конкретный список рисков и требований для вашей компании и ваших развертываний может существенно отличаться.</span><span class="sxs-lookup"><span data-stu-id="ac543-140">This section of the CAF provides examples, but the detailed risks and baselines for your company or deployments may be different.</span></span>

<span data-ttu-id="ac543-141">Определив базовые показатели, установите минимальные характеристики недопустимого увеличения выявленных рисков.</span><span class="sxs-lookup"><span data-stu-id="ac543-141">Once you have a baseline, establish minimum benchmarks representing an unacceptable increase in your identified risks.</span></span> <span data-ttu-id="ac543-142">Эти характеристики будут выполнять роль триггеров, чтобы вы получали сведения о необходимости предпринять действия для устранения этих рисков.</span><span class="sxs-lookup"><span data-stu-id="ac543-142">These benchmarks act as triggers for when you need to take action to mitigate these risks.</span></span> <span data-ttu-id="ac543-143">В примерах ниже показано, как описанные выше и другие метрики безопасности могут обосновать повышение инвестиций по дисциплине базовой системы безопасности.</span><span class="sxs-lookup"><span data-stu-id="ac543-143">The following are a few examples of how security metrics, such as those discussed above, can justify an increased investment in the Security Baseline discipline.</span></span>

- <span data-ttu-id="ac543-144">**Триггер по критически важным рабочим нагрузкам**.</span><span class="sxs-lookup"><span data-stu-id="ac543-144">**Mission-critical workloads trigger**.</span></span> <span data-ttu-id="ac543-145">При развертывании критически важных рабочих нагрузок в облаке компании следует выделить ресурсы на дисциплину базовой системы безопасности, чтобы избежать возможных перебоев в работе службы или раскрытия конфиденциальных данных.</span><span class="sxs-lookup"><span data-stu-id="ac543-145">A company deploying mission-critical workloads to the cloud should invest in the Security Baseline discipline to prevent potential disruption of service or sensitive data exposure.</span></span>
- <span data-ttu-id="ac543-146">**Триггер защищенных данных**.</span><span class="sxs-lookup"><span data-stu-id="ac543-146">**Protected data trigger**.</span></span> <span data-ttu-id="ac543-147">Если компания размещает в облаке данные, которые можно классифицировать как конфиденциальные, частные или иным образом подпадающие под нормативные ограничения.</span><span class="sxs-lookup"><span data-stu-id="ac543-147">A company hosting data on the cloud that can be classified as confidential, private, or otherwise subject to regulatory concerns.</span></span> <span data-ttu-id="ac543-148">В этом случае дисциплина базовой системы безопасности нужна для того, чтобы предотвратить потерю, раскрытие или кражу таких данных.</span><span class="sxs-lookup"><span data-stu-id="ac543-148">They need a Security Baseline discipline to ensure that this data is not subject to loss, exposure, or theft.</span></span>
- <span data-ttu-id="ac543-149">**Триггер внешних атак**.</span><span class="sxs-lookup"><span data-stu-id="ac543-149">**External attacks trigger**.</span></span> <span data-ttu-id="ac543-150">Если компания испытывает серьезные атаки на сетевую инфраструктуру с частотой Х раз в месяц, ей будет полезно применить дисциплину базовой системы безопасности.</span><span class="sxs-lookup"><span data-stu-id="ac543-150">A company that experiences serious attacks against their network infrastructure X times per month could benefit from the Security Baseline discipline.</span></span>  
- <span data-ttu-id="ac543-151">**Триггер соответствия стандартам**.</span><span class="sxs-lookup"><span data-stu-id="ac543-151">**Standards compliance trigger**.</span></span> <span data-ttu-id="ac543-152">Если у компании более X % ресурсов не соответствуют требованиям стандартов, ей необходимо выделить ресурсы по дисциплине базовой системы безопасности, чтобы гарантировать согласованное применение стандартов ко всей ИТ-инфраструктуре.</span><span class="sxs-lookup"><span data-stu-id="ac543-152">A company with more than X% of resources out of security standards compliance should invest in the Security Baseline discipline to ensure standards are applied consistently across your IT infrastructure.</span></span>
- <span data-ttu-id="ac543-153">**Триггер размера облачной инфраструктуры**.</span><span class="sxs-lookup"><span data-stu-id="ac543-153">**Cloud estate size trigger**.</span></span> <span data-ttu-id="ac543-154">Компания размещает более X приложений, служб или источников данных.</span><span class="sxs-lookup"><span data-stu-id="ac543-154">A company hosting more than X number of applications, services, or data sources.</span></span> <span data-ttu-id="ac543-155">Крупные облачные развертывания можно улучшить путем инвестиций по дисциплине базовой системы безопасности, которая позволит защитить всю область, которая подвергается несанкционированному доступу и другим внешним угрозам.</span><span class="sxs-lookup"><span data-stu-id="ac543-155">Large cloud deployments can benefit from investment in the Security Baseline discipline to ensure that their overall attack surface is properly protected against unauthorized access or other external threats.</span></span>
- <span data-ttu-id="ac543-156">**Триггер наличия программного обеспечения для защиты**.</span><span class="sxs-lookup"><span data-stu-id="ac543-156">**Security software compliance trigger**.</span></span> <span data-ttu-id="ac543-157">В компании менее X % развернутых виртуальных машин имеют все необходимое программное обеспечение для защиты.</span><span class="sxs-lookup"><span data-stu-id="ac543-157">A company where less than X% of deployed virtual machines have all required security software installed.</span></span> <span data-ttu-id="ac543-158">Дисциплину базовой системы безопасности можно применить для стабильного применения программного обеспечения для защиты.</span><span class="sxs-lookup"><span data-stu-id="ac543-158">A Security Baseline discipline can be used to ensure software is installed consistently on all software.</span></span>
- <span data-ttu-id="ac543-159">**Триггер установки исправлений**.</span><span class="sxs-lookup"><span data-stu-id="ac543-159">**Patching trigger**.</span></span> <span data-ttu-id="ac543-160">В компании развернуты виртуальные машины или службы, к которым не применялись исправления операционной системы или программного обеспечения за последние X дней.</span><span class="sxs-lookup"><span data-stu-id="ac543-160">A company where deployed virtual machines or services where OS or software patches have not been applied in the last X number of days.</span></span> <span data-ttu-id="ac543-161">Дисциплина базовой системы безопасности позволит убедиться, что исправления применяются своевременно по заданному расписанию.</span><span class="sxs-lookup"><span data-stu-id="ac543-161">A Security Baseline discipline can be used to ensure patching is kept up-to-date within a required schedule.</span></span>
- <span data-ttu-id="ac543-162">**Система, ориентированная на безопасность**.</span><span class="sxs-lookup"><span data-stu-id="ac543-162">**Security focused**.</span></span> <span data-ttu-id="ac543-163">Некоторые компании применяют строгие требования по безопасности и конфиденциальности данных даже для тестовых и экспериментальных рабочих нагрузок.</span><span class="sxs-lookup"><span data-stu-id="ac543-163">Some companies will have strong security and data confidentiality requirements even for test and experimental workloads.</span></span> <span data-ttu-id="ac543-164">Таким компаниям потребуется выделить ресурсы по дисциплине базовой системы безопасности еще перед началом первого развертывания.</span><span class="sxs-lookup"><span data-stu-id="ac543-164">These companies will need to invest in the Security Baseline discipline before any deployments can begin.</span></span>

<span data-ttu-id="ac543-165">Точные метрики и триггеры, которые вы примените для оценки допустимости риска и требуемого уровня вложений по дисциплине базовой системы безопасности, будут зависеть от вашей организации. Приведенные выше примеры станут для вас полезной основой для обсуждений внутри команды управления облаком.</span><span class="sxs-lookup"><span data-stu-id="ac543-165">The exact metrics and triggers you use to gauge risk tolerance and the level of investment in the Security Baseline discipline will be specific to your organization, but the examples above should serve as a useful base for discussion within your Cloud Governance team.</span></span>  

## <a name="next-steps"></a><span data-ttu-id="ac543-166">Дополнительная информация</span><span class="sxs-lookup"><span data-stu-id="ac543-166">Next steps</span></span>

<span data-ttu-id="ac543-167">С помощью [шаблона управления облачными ресурсами](./template.md) задокументируйте метрики и индикаторы допустимости, соответствующие текущему плану внедрения облака.</span><span class="sxs-lookup"><span data-stu-id="ac543-167">Using the [Cloud Management template](./template.md), document metrics and tolerance indicators that align to the current cloud adoption plan.</span></span>

<span data-ttu-id="ac543-168">Основываясь на рисках и допустимости рисков, создайте процесс управления и информирования о соблюдении политики базовой системы безопасности.</span><span class="sxs-lookup"><span data-stu-id="ac543-168">Building on risks and tolerance, establish a process for governing and communicating Security Baseline policy adherence.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="ac543-169">Разработка процессов, обеспечивающих соблюдение требованиям</span><span class="sxs-lookup"><span data-stu-id="ac543-169">Establish policy compliance processes</span></span>](compliance-processes.md)