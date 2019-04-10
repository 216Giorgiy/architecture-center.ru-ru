---
title: Эталонная реализация микрослужб для Службы Azure Kubernetes
description: Эта эталонная реализация включает советы и рекомендации по использованию архитектуры микрослужб
author: MikeWasson
ms.date: 02/26/2019
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: microservices
ms.openlocfilehash: 17e275e5b5f45233f7467192402cb28fce35c57b
ms.sourcegitcommit: 0a8a60d782facc294f7f78ec0e9033e3ee16bf4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068911"
---
# <a name="designing-a-microservices-architecture"></a><span data-ttu-id="33395-103">Проектирование архитектуры микрослужб</span><span class="sxs-lookup"><span data-stu-id="33395-103">Designing a microservices architecture</span></span>

<span data-ttu-id="33395-104">Архитектура микрослужб широко используется для создания устойчивых, высокомасштабируемых, независимо развертываемых и быстро развивающихся облачных приложений.</span><span class="sxs-lookup"><span data-stu-id="33395-104">Microservices have become a popular architectural style for building cloud applications that are resilient, highly scalable, independently deployable, and able to evolve quickly.</span></span> <span data-ttu-id="33395-105">Но это не просто специальный термин. Применение микрослужб требует другого подхода к разработке и созданию приложений.</span><span class="sxs-lookup"><span data-stu-id="33395-105">To be more than just a buzzword, however, microservices require a different approach to designing and building applications.</span></span>

<span data-ttu-id="33395-106">В этой серии статей мы рассмотрим, как создать и запустить архитектуру микрослужб в Azure.</span><span class="sxs-lookup"><span data-stu-id="33395-106">In this set of articles, we explore how to build and run a microservices architecture on Azure.</span></span> <span data-ttu-id="33395-107">Будут рассмотрены следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="33395-107">Topics include:</span></span>

- [<span data-ttu-id="33395-108">Обмен данными между службами</span><span class="sxs-lookup"><span data-stu-id="33395-108">Interservice communication</span></span>](./interservice-communication.md)
- [<span data-ttu-id="33395-109">Проектирование API</span><span class="sxs-lookup"><span data-stu-id="33395-109">API design</span></span>](./api-design.md)
- [<span data-ttu-id="33395-110">Шлюзы API</span><span class="sxs-lookup"><span data-stu-id="33395-110">API gateways</span></span>](./gateway.md)
- [<span data-ttu-id="33395-111">Рекомендации по работе с данными</span><span class="sxs-lookup"><span data-stu-id="33395-111">Data considerations</span></span>](./data-considerations.md)
- [<span data-ttu-id="33395-112">Шаблоны проектирования</span><span class="sxs-lookup"><span data-stu-id="33395-112">Design patterns</span></span>](./patterns.md)

## <a name="prerequisites"></a><span data-ttu-id="33395-113">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="33395-113">Prerequisites</span></span>

<span data-ttu-id="33395-114">Перед прочтением этих статей вы можете ознакомиться со следующими материалами:</span><span class="sxs-lookup"><span data-stu-id="33395-114">Before reading these articles, you might start with the following:</span></span>

- <span data-ttu-id="33395-115">[Введение в архитектуру микрослужб](../introduction.md).</span><span class="sxs-lookup"><span data-stu-id="33395-115">[Introduction to microservices architectures](../introduction.md).</span></span> <span data-ttu-id="33395-116">Описание преимуществ и недостатков микрослужб, а также сценариев использования этого стиля архитектуры.</span><span class="sxs-lookup"><span data-stu-id="33395-116">Understand the benefits and challenges of microservices, and when to use this style of architecture.</span></span>
- <span data-ttu-id="33395-117">[Анализ предметной области для моделирования микрослужб](../model/domain-analysis.md).</span><span class="sxs-lookup"><span data-stu-id="33395-117">[Using domain analysis to model microservices](../model/domain-analysis.md).</span></span> <span data-ttu-id="33395-118">Моделирование микрослужб с использованием предметно-ориентированного подхода.</span><span class="sxs-lookup"><span data-stu-id="33395-118">Learn a domain-driven approach to modeling microservices.</span></span>

## <a name="reference-implementation"></a><span data-ttu-id="33395-119">Эталонная реализация</span><span class="sxs-lookup"><span data-stu-id="33395-119">Reference implementation</span></span>

<span data-ttu-id="33395-120">Чтобы предоставить некоторые рекомендации по использованию архитектуры микрослужб, мы создали пример реализации, который назвали приложением для доставки с помощью дронов.</span><span class="sxs-lookup"><span data-stu-id="33395-120">To illustrate best practices for a microservices architecture, we created a reference implementation that we call the Drone Delivery application.</span></span> <span data-ttu-id="33395-121">В примере реализации используется Kubernetes со Службой Azure Kubernetes (AKS).</span><span class="sxs-lookup"><span data-stu-id="33395-121">This implementation runs on Kubernetes using Azure Kubernetes Service (AKS).</span></span> <span data-ttu-id="33395-122">Этот пример реализации можно найти на портале [GitHub][drone-ri].</span><span class="sxs-lookup"><span data-stu-id="33395-122">You can find the reference implementation on [GitHub][drone-ri].</span></span>

![Архитектура приложения для доставки с помощью дронов](../images/drone-delivery.png)

## <a name="scenario"></a><span data-ttu-id="33395-124">Сценарий</span><span class="sxs-lookup"><span data-stu-id="33395-124">Scenario</span></span>

<span data-ttu-id="33395-125">Компания Fabrikam запускает службу доставки беспилотными летательными аппаратами.</span><span class="sxs-lookup"><span data-stu-id="33395-125">Fabrikam, Inc. is starting a drone delivery service.</span></span> <span data-ttu-id="33395-126">Она управляет парком таких аппаратов.</span><span class="sxs-lookup"><span data-stu-id="33395-126">The company manages a fleet of drone aircraft.</span></span> <span data-ttu-id="33395-127">Организации регистрируются с помощью службы, и пользователи могут отправить заявку на использование беспилотного летательного аппарата, чтобы забрать посылки для доставки.</span><span class="sxs-lookup"><span data-stu-id="33395-127">Businesses register with the service, and users can request a drone to pick up goods for delivery.</span></span> <span data-ttu-id="33395-128">Когда клиент планирует отгрузку, серверная система назначает аппарат и сообщает пользователю предполагаемое время доставки.</span><span class="sxs-lookup"><span data-stu-id="33395-128">When a customer schedules a pickup, a backend system assigns a drone and notifies the user with an estimated delivery time.</span></span> <span data-ttu-id="33395-129">В ходе доставки клиент может отслеживать местоположение аппарата, получая актуальные сведения об ожидаемом времени доставки.</span><span class="sxs-lookup"><span data-stu-id="33395-129">While the delivery is in progress, the customer can track the location of the drone, with a continuously updated ETA.</span></span>

<span data-ttu-id="33395-130">Этот сценарий довольной сложный,</span><span class="sxs-lookup"><span data-stu-id="33395-130">This scenario involves a fairly complicated domain.</span></span> <span data-ttu-id="33395-131">так как его этапы включают планирование загруженности беспилотных летательных аппаратов, отслеживание посылок, управление учетными записями пользователей, а также хранение и анализ исторических данных.</span><span class="sxs-lookup"><span data-stu-id="33395-131">Some of the business concerns include scheduling drones, tracking packages, managing user accounts, and storing and analyzing historical data.</span></span> <span data-ttu-id="33395-132">Кроме того, компания Fabrikam хочет быстро выйти на рынок, а затем быстро повторить этот процесс, добавив новые функции и возможности.</span><span class="sxs-lookup"><span data-stu-id="33395-132">Moreover, Fabrikam wants to get to market quickly and then iterate quickly, adding new functionality and capabilities.</span></span> <span data-ttu-id="33395-133">Приложение должно работать в масштабе облака с высоким целевым уровнем обслуживания.</span><span class="sxs-lookup"><span data-stu-id="33395-133">The application needs to operate at cloud scale, with a high service level objective (SLO).</span></span> <span data-ttu-id="33395-134">Компания также ожидает, что в разных частях системы требования к хранению данных и выполнению запросов будут очень отличаться.</span><span class="sxs-lookup"><span data-stu-id="33395-134">Fabrikam also expects that different parts of the system will have very different requirements for data storage and querying.</span></span> <span data-ttu-id="33395-135">С учетом всех этих факторов Fabrikam выбрала архитектуру микрослужб для приложения доставки беспилотными летательными аппаратами.</span><span class="sxs-lookup"><span data-stu-id="33395-135">All of these considerations lead Fabrikam to choose a microservices architecture for the Drone Delivery application.</span></span>

> [!NOTE]
> <span data-ttu-id="33395-136">Если вам нужна помощь в выборе между микрослужбами и другими архитектурами, ознакомьтесь с [руководством по архитектуре приложений Azure](../../guide/index.md).</span><span class="sxs-lookup"><span data-stu-id="33395-136">For help in choosing between a microservices architecture and other architectural styles, see the [Azure Application Architecture Guide](../../guide/index.md).</span></span>

<span data-ttu-id="33395-137">В примере реализации используется Kubernetes со [Службой Azure Kubernetes](/azure/aks/) (AKS).</span><span class="sxs-lookup"><span data-stu-id="33395-137">Our reference implementation uses Kubernetes with [Azure Kubernetes Service](/azure/aks/) (AKS).</span></span> <span data-ttu-id="33395-138">Но большинство общих архитектурных решений и задач будут применимы к любому оркестратору контейнера, включая [Azure Service Fabric](/azure/service-fabric/).</span><span class="sxs-lookup"><span data-stu-id="33395-138">However, many of the high-level architectural decisions and challenges will apply to any container orchestrator, including [Azure Service Fabric](/azure/service-fabric/).</span></span>

<!-- links -->

[drone-ri]: https://github.com/mspnp/microservices-reference-implementation/tree/v0.1.0-orig

## <a name="next-steps"></a><span data-ttu-id="33395-139">Дополнительная информация</span><span class="sxs-lookup"><span data-stu-id="33395-139">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="33395-140">Выбор вычислительного ресурса</span><span class="sxs-lookup"><span data-stu-id="33395-140">Choose a compute option</span></span>](./compute-options.md)