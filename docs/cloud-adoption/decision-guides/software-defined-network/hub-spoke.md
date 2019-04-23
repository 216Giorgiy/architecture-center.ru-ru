---
title: CAF. Программно-конфигурируемые сети для полностью облачных решений
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Обсуждение сетевых служб для полностью облачных решений
author: rotycenh
ms.openlocfilehash: e0ad6803f2ddc982ea0c42c59fdf2486e1710433
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241140"
---
# <a name="software-defined-networks-hub-and-spoke"></a><span data-ttu-id="7792b-103">Программно-определяемые сети. звездообразная модель</span><span class="sxs-lookup"><span data-stu-id="7792b-103">Software Defined Networks: Hub and Spoke</span></span>

<span data-ttu-id="7792b-104">Звездообразная модель сети организует сетевую облачную инфраструктуру Azure в несколько взаимосвязанных виртуальных сетей.</span><span class="sxs-lookup"><span data-stu-id="7792b-104">The hub and spoke networking model organizes your Azure-based cloud network infrastructure into multiple connected virtual networks.</span></span> <span data-ttu-id="7792b-105">Эта модель позволяет эффективнее управлять общими требованиями к связи или безопасности и работать с потенциальными ограничениями подписки.</span><span class="sxs-lookup"><span data-stu-id="7792b-105">This model allows you to more efficiently manage common communication or security requirements and deal with potential subscription limitations.</span></span>

<span data-ttu-id="7792b-106">В звездообразной модели *концентратор* является виртуальной сетью, которая действует в качестве центрального расположения по управлению внешними службами подключения и размещения, используемыми несколькими рабочими нагрузками.</span><span class="sxs-lookup"><span data-stu-id="7792b-106">In the hub and spoke model, the *hub* is a virtual network that acts as a central location for managing external connectivity and hosting services used by multiple workloads.</span></span> <span data-ttu-id="7792b-107">*Периферийные зоны* — это виртуальные сети, в которых размещены рабочие нагрузки, и которые подключаются к центральному концентратору через [пиринг виртуальной сети](/virtual-network/virtual-network-peering-overview).</span><span class="sxs-lookup"><span data-stu-id="7792b-107">The *spokes* are virtual networks that host workloads and connect to the central hub through [virtual network peering](/virtual-network/virtual-network-peering-overview).</span></span>

<span data-ttu-id="7792b-108">Весь трафик, проходящий через периферийные сети рабочих нагрузок, маршрутизируется в сеть концентратора, если его можно маршрутизировать, просматривать или иным образом управлять им с помощью централизованно управляемых ИТ-правил или процессов.</span><span class="sxs-lookup"><span data-stu-id="7792b-108">All traffic passing in or out of the workload spoke networks is routed through the hub network where it can be routed, inspected, or otherwise managed by centrally managed IT rules or processes.</span></span>

<span data-ttu-id="7792b-109">Эта модель помогает решить следующие проблемы:</span><span class="sxs-lookup"><span data-stu-id="7792b-109">This model aims to address the following issues:</span></span>

- <span data-ttu-id="7792b-110">Сокращение затрат и повышения эффективности управления.</span><span class="sxs-lookup"><span data-stu-id="7792b-110">Cost savings and management efficiency.</span></span> <span data-ttu-id="7792b-111">Централизация служб, которые могут совместно использоваться несколькими рабочими нагрузками, такими как сетевые виртуальные модули (NVA) и DNS-серверы в одном расположении, что позволяет ИТ-отделу минимизировать избыток ресурсов и усилия по управлению в нескольких рабочих нагрузках.</span><span class="sxs-lookup"><span data-stu-id="7792b-111">Centralizing services that can be shared by multiple workloads, such as network virtual appliances (NVAs) and DNS servers, in a single location allows IT to minimize redundant resources and management effort across multiple workloads.</span></span>
- <span data-ttu-id="7792b-112">Преодоление ограничения подписок.</span><span class="sxs-lookup"><span data-stu-id="7792b-112">Overcoming subscriptions limits.</span></span> <span data-ttu-id="7792b-113">Большим рабочим нагрузкам может потребоваться больше ресурсов, чем разрешено в рамках одной подписки Azure (см. статью об [ограничениях подписки](/azure/azure-subscription-service-limits)).</span><span class="sxs-lookup"><span data-stu-id="7792b-113">Large cloud-based workloads may require the use of more resources than are allowed within a single Azure subscription (see [subscription limits](/azure/azure-subscription-service-limits)).</span></span> <span data-ttu-id="7792b-114">Пиринг рабочей нагрузки виртуальных сетей из различных подписок в центральный концентратор может преодолеть эти ограничения.</span><span class="sxs-lookup"><span data-stu-id="7792b-114">Peering workload virtual networks from different subscriptions to a central hub can overcome these limits.</span></span>
- <span data-ttu-id="7792b-115">Разделение областей ответственности.</span><span class="sxs-lookup"><span data-stu-id="7792b-115">Separation of concerns.</span></span> <span data-ttu-id="7792b-116">Возможность развертывания отдельных рабочих нагрузок между центральными ИТ-командами и командами по выполнению рабочих нагрузок.</span><span class="sxs-lookup"><span data-stu-id="7792b-116">The ability to deploy individual workloads between central IT teams and workloads teams.</span></span>

<span data-ttu-id="7792b-117">На следующей схеме показан пример звездообразной архитектуры с централизованно управляемым гибридным подключением.</span><span class="sxs-lookup"><span data-stu-id="7792b-117">The following diagram shows an example hub and spoke architecture including centrally managed hybrid connectivity.</span></span>

![Звездообразная архитектура сети](../../../reference-architectures/hybrid-networking/images/hub-spoke.png)

<span data-ttu-id="7792b-119">Звездообразная архитектура часто используется вместе с гибридной архитектурой сети, обеспечивая централизованное управляемое соединение с локальной средой, общей для нескольких рабочих нагрузок.</span><span class="sxs-lookup"><span data-stu-id="7792b-119">The hub and spoke architecture is often used alongside the hybrid networking architecture, providing a centrally managed connection to your on-premises environment shared between multiple workloads.</span></span> <span data-ttu-id="7792b-120">В этом случае весь трафик, передающийся между рабочими нагрузками и локальным расположением, проходит через концентратор, где он защищен и управляем.</span><span class="sxs-lookup"><span data-stu-id="7792b-120">In this scenario, all traffic traveling between the workloads and on-premises passes through the hub where it can be managed and secured.</span></span>

## <a name="hub-and-spoke-assumptions"></a><span data-ttu-id="7792b-121">Предварительные требования для звездообразной архитектуры</span><span class="sxs-lookup"><span data-stu-id="7792b-121">Hub and spoke assumptions</span></span>

<span data-ttu-id="7792b-122">Реализация звездообразной архитектуры виртуальной сети предполагает следующее:</span><span class="sxs-lookup"><span data-stu-id="7792b-122">Implementing a hub and spoke virtual networking architecture assumes the following:</span></span>

- <span data-ttu-id="7792b-123">Облачные развертывания включают рабочие нагрузки, размещенные в различных рабочих средах (среда разработки, тестирования и рабочая среда), которые полагаются на набор распространенных служб, таких как DNS или службы каталогов.</span><span class="sxs-lookup"><span data-stu-id="7792b-123">Your cloud deployments will involve workloads hosted in separate working environments, such as development, test, and production, that all rely on a set of common services such as DNS or directory services.</span></span>
- <span data-ttu-id="7792b-124">Рабочие нагрузки не обязательно должны взаимодействовать друг с другом, но могут иметь общие требования к внешним коммуникациям и общим службам.</span><span class="sxs-lookup"><span data-stu-id="7792b-124">Your workloads do not need to communicate with each other but have common external communications and shared services requirements.</span></span>
- <span data-ttu-id="7792b-125">Рабочие нагрузки требуют больше ресурсов, чем доступно в одной подписке Azure.</span><span class="sxs-lookup"><span data-stu-id="7792b-125">Your workloads require more resources than are available within a single Azure subscription.</span></span>
- <span data-ttu-id="7792b-126">Необходимо предоставить командам по выполнению рабочих нагрузок права делегированного управления собственными ресурсами, сохраняя при этом централизованное управление безопасностью внешних подключений.</span><span class="sxs-lookup"><span data-stu-id="7792b-126">You need to provide workload teams with delegated management rights over their own resources while maintaining central security control over external connectivity.</span></span>

## <a name="global-hub-and-spoke"></a><span data-ttu-id="7792b-127">Глобальная звездообразная архитектура</span><span class="sxs-lookup"><span data-stu-id="7792b-127">Global hub and spoke</span></span>

<span data-ttu-id="7792b-128">Звездообразные архитектуры обычно реализуются с помощью виртуальных сетей, развернутых в том же регионе Azure, чтобы свести к минимуму задержку между сетями.</span><span class="sxs-lookup"><span data-stu-id="7792b-128">Hub and spoke architectures are commonly implemented with virtual networks deployed to the same Azure Region to minimize latency between networks.</span></span> <span data-ttu-id="7792b-129">Однако большим организациям с глобальным охватом может потребоваться развернуть рабочие нагрузки в нескольких регионах в целях доступности, аварийного восстановления или нормативных требований.</span><span class="sxs-lookup"><span data-stu-id="7792b-129">However, large organizations with global reach may need to deploy workloads across multiple regions for availability, disaster recovery, or regulatory requirements.</span></span> <span data-ttu-id="7792b-130">За счет использования [глобального пиринга виртуальной сети](/azure/virtual-network/virtual-network-peering-overview) Azure звездообразная модель может расширить централизованное управление и общие службы по регионам, чтобы поддерживать рабочие нагрузки, распределенные по всему миру.</span><span class="sxs-lookup"><span data-stu-id="7792b-130">Through the use of Azure [global virtual network peering](/azure/virtual-network/virtual-network-peering-overview), the hub and spoke model can extend centralized management and shared services across regions to support workloads distributed across the world.</span></span>

## <a name="learn-more"></a><span data-ttu-id="7792b-131">Подробнее</span><span class="sxs-lookup"><span data-stu-id="7792b-131">Learn more</span></span>

<span data-ttu-id="7792b-132">Примеры реализации звездообразных сетей в Azure см. на сайте эталонных архитектур Azure:</span><span class="sxs-lookup"><span data-stu-id="7792b-132">For examples of how to implement hub and spoke networks on Azure, see the following examples on the Azure Reference Architectures site:</span></span>

- [<span data-ttu-id="7792b-133">Реализация звездообразной топологии сети в Azure</span><span class="sxs-lookup"><span data-stu-id="7792b-133">Implement a hub-spoke network topology in Azure</span></span>](../../../reference-architectures/hybrid-networking/hub-spoke.md)
- [<span data-ttu-id="7792b-134">Реализация звездообразной топологии сети с помощью общих служб в Azure</span><span class="sxs-lookup"><span data-stu-id="7792b-134">Implement a hub-spoke network topology with shared services in Azure</span></span>](../../../reference-architectures/hybrid-networking/shared-services.md)