---
title: CAF. Средства управления затратами в Azure
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Средства управления затратами в Azure
author: BrianBlanchard
ms.openlocfilehash: 58dfa604863f704fd9b9fbb8d0693447cecdaf84
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58246605"
---
# <a name="cost-management-tools-in-azure"></a><span data-ttu-id="b49a2-103">Средства управления затратами в Azure</span><span class="sxs-lookup"><span data-stu-id="b49a2-103">Cost Management tools in Azure</span></span>

<span data-ttu-id="b49a2-104">[Управление затратами](overview.md) — это одна из [пяти дисциплин управления облаком](../governance-disciplines.md).</span><span class="sxs-lookup"><span data-stu-id="b49a2-104">[Cost Management](overview.md) is one of the [Five Disciplines of Cloud Governance](../governance-disciplines.md).</span></span> <span data-ttu-id="b49a2-105">Эта дисциплина сосредоточена на способах составления планов облачных расходов, распределения облачных бюджетов, мониторинга и реализации облачных бюджетов, выявления дорогостоящих аномалий и корректировки плана управления облаком, когда фактические расходы не совпадают.</span><span class="sxs-lookup"><span data-stu-id="b49a2-105">This discipline focuses on ways of establishing cloud spending plans, allocating cloud budgets, monitoring and enforcement of cloud budgets, detecting costly anomalies, and adjusting the cloud governance plan when actual spending is misaligned.</span></span>

<span data-ttu-id="b49a2-106">Ниже приведен список собственных инструментов Azure, которые могут помочь в совершенствовании политик и процессов, поддерживающих эту дисциплину управления.</span><span class="sxs-lookup"><span data-stu-id="b49a2-106">The following is a list of Azure native tools that can help mature the policies and processes that support this governance discipline.</span></span>

|  | [<span data-ttu-id="b49a2-107">портал Azure</span><span class="sxs-lookup"><span data-stu-id="b49a2-107">Azure portal</span></span>](https://azure.microsoft.com/features/azure-portal/)  | [<span data-ttu-id="b49a2-108">Управление затратами Azure</span><span class="sxs-lookup"><span data-stu-id="b49a2-108">Azure Cost Management</span></span>](/azure/cost-management/overview-cost-mgt)  | [<span data-ttu-id="b49a2-109">Пакет содержимого Azure EA</span><span class="sxs-lookup"><span data-stu-id="b49a2-109">Azure EA Content Pack</span></span>](/power-bi/service-connect-to-azure-enterprise)  | [<span data-ttu-id="b49a2-110">Политика Azure</span><span class="sxs-lookup"><span data-stu-id="b49a2-110">Azure Policy</span></span>](/azure/governance/policy/overview) |
|---------|---------|---------|---------|---------|
|<span data-ttu-id="b49a2-111">Требуется ли соглашение Enterprise?</span><span class="sxs-lookup"><span data-stu-id="b49a2-111">Enterprise agreement required?</span></span>     | <span data-ttu-id="b49a2-112">Нет </span><span class="sxs-lookup"><span data-stu-id="b49a2-112">No</span></span>         | <span data-ttu-id="b49a2-113">Да (не обязательно для [Cloudyn](/azure/cost-management/overview))</span><span class="sxs-lookup"><span data-stu-id="b49a2-113">Yes (not required with [Cloudyn](/azure/cost-management/overview))</span></span>         | <span data-ttu-id="b49a2-114">Yes</span><span class="sxs-lookup"><span data-stu-id="b49a2-114">Yes</span></span>         | <span data-ttu-id="b49a2-115">Нет </span><span class="sxs-lookup"><span data-stu-id="b49a2-115">No</span></span>         |
|<span data-ttu-id="b49a2-116">Управление бюджетом</span><span class="sxs-lookup"><span data-stu-id="b49a2-116">Budget control</span></span>     | <span data-ttu-id="b49a2-117">Нет </span><span class="sxs-lookup"><span data-stu-id="b49a2-117">No</span></span>         | <span data-ttu-id="b49a2-118">Yes</span><span class="sxs-lookup"><span data-stu-id="b49a2-118">Yes</span></span>         | <span data-ttu-id="b49a2-119">Нет </span><span class="sxs-lookup"><span data-stu-id="b49a2-119">No</span></span>         | <span data-ttu-id="b49a2-120">Yes</span><span class="sxs-lookup"><span data-stu-id="b49a2-120">Yes</span></span>         |
|<span data-ttu-id="b49a2-121">Отслеживание затрат на отдельном ресурсе</span><span class="sxs-lookup"><span data-stu-id="b49a2-121">Monitor spending on single resource</span></span>    | <span data-ttu-id="b49a2-122">Yes</span><span class="sxs-lookup"><span data-stu-id="b49a2-122">Yes</span></span>         | <span data-ttu-id="b49a2-123">Да</span><span class="sxs-lookup"><span data-stu-id="b49a2-123">Yes</span></span>         | <span data-ttu-id="b49a2-124">Да</span><span class="sxs-lookup"><span data-stu-id="b49a2-124">Yes</span></span>         | <span data-ttu-id="b49a2-125">Нет </span><span class="sxs-lookup"><span data-stu-id="b49a2-125">No</span></span>         |
|<span data-ttu-id="b49a2-126">Отслеживание затрат на нескольких ресурсах</span><span class="sxs-lookup"><span data-stu-id="b49a2-126">Monitor spending across multiple resources</span></span>    | <span data-ttu-id="b49a2-127">Нет </span><span class="sxs-lookup"><span data-stu-id="b49a2-127">No</span></span>         | <span data-ttu-id="b49a2-128">Yes</span><span class="sxs-lookup"><span data-stu-id="b49a2-128">Yes</span></span>        | <span data-ttu-id="b49a2-129">Да</span><span class="sxs-lookup"><span data-stu-id="b49a2-129">Yes</span></span>         | <span data-ttu-id="b49a2-130">Нет </span><span class="sxs-lookup"><span data-stu-id="b49a2-130">No</span></span>         |
|<span data-ttu-id="b49a2-131">Контроль затрат на отдельном ресурсе</span><span class="sxs-lookup"><span data-stu-id="b49a2-131">Control spending on single resource</span></span>     | <span data-ttu-id="b49a2-132">Да (изменение размера вручную)</span><span class="sxs-lookup"><span data-stu-id="b49a2-132">Yes - manual sizing</span></span>         | <span data-ttu-id="b49a2-133">Yes</span><span class="sxs-lookup"><span data-stu-id="b49a2-133">Yes</span></span>         | <span data-ttu-id="b49a2-134">Нет </span><span class="sxs-lookup"><span data-stu-id="b49a2-134">No</span></span>         | <span data-ttu-id="b49a2-135">Yes</span><span class="sxs-lookup"><span data-stu-id="b49a2-135">Yes</span></span>         |
|<span data-ttu-id="b49a2-136">Применение затрат для нескольких ресурсов</span><span class="sxs-lookup"><span data-stu-id="b49a2-136">Enforce spending across multiple resources</span></span>    | <span data-ttu-id="b49a2-137">Нет </span><span class="sxs-lookup"><span data-stu-id="b49a2-137">No</span></span>         | <span data-ttu-id="b49a2-138">Yes</span><span class="sxs-lookup"><span data-stu-id="b49a2-138">Yes</span></span>         | <span data-ttu-id="b49a2-139">Нет </span><span class="sxs-lookup"><span data-stu-id="b49a2-139">No</span></span>         | <span data-ttu-id="b49a2-140">Yes</span><span class="sxs-lookup"><span data-stu-id="b49a2-140">Yes</span></span>         |
|<span data-ttu-id="b49a2-141">Отслеживание и выявление тенденций</span><span class="sxs-lookup"><span data-stu-id="b49a2-141">Monitor and detect trends</span></span>     | <span data-ttu-id="b49a2-142">Да (ограничено)</span><span class="sxs-lookup"><span data-stu-id="b49a2-142">Yes - limited</span></span>         | <span data-ttu-id="b49a2-143">Yes</span><span class="sxs-lookup"><span data-stu-id="b49a2-143">Yes</span></span>        | <span data-ttu-id="b49a2-144">Да</span><span class="sxs-lookup"><span data-stu-id="b49a2-144">Yes</span></span>         | <span data-ttu-id="b49a2-145">Нет </span><span class="sxs-lookup"><span data-stu-id="b49a2-145">No</span></span>         |
|<span data-ttu-id="b49a2-146">Обнаружение аномалий в затратах</span><span class="sxs-lookup"><span data-stu-id="b49a2-146">Detect spending anomalies</span></span>     | <span data-ttu-id="b49a2-147">Нет </span><span class="sxs-lookup"><span data-stu-id="b49a2-147">No</span></span>         | <span data-ttu-id="b49a2-148">Yes</span><span class="sxs-lookup"><span data-stu-id="b49a2-148">Yes</span></span>        | <span data-ttu-id="b49a2-149">Да</span><span class="sxs-lookup"><span data-stu-id="b49a2-149">Yes</span></span>         | <span data-ttu-id="b49a2-150">Нет </span><span class="sxs-lookup"><span data-stu-id="b49a2-150">No</span></span>        |
|<span data-ttu-id="b49a2-151">Социализация отклонений</span><span class="sxs-lookup"><span data-stu-id="b49a2-151">Socialize deviations</span></span>     | <span data-ttu-id="b49a2-152">Нет </span><span class="sxs-lookup"><span data-stu-id="b49a2-152">No</span></span>        | <span data-ttu-id="b49a2-153">Yes</span><span class="sxs-lookup"><span data-stu-id="b49a2-153">Yes</span></span>        | <span data-ttu-id="b49a2-154">Да</span><span class="sxs-lookup"><span data-stu-id="b49a2-154">Yes</span></span>        | <span data-ttu-id="b49a2-155">Нет </span><span class="sxs-lookup"><span data-stu-id="b49a2-155">No</span></span>        |