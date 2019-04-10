---
title: Мониторинг Azure Databricks с помощью Azure Monitor
description: Библиотека Scala для включения мониторинга Azure Databricks в Azure Log Analytics
author: petertaylor9999
ms.date: 03/26/2019
ms.openlocfilehash: 4544d3abc3264ec459a80ac1a61a912e6d30d6b2
ms.sourcegitcommit: 1a3cc91530d56731029ea091db1f15d41ac056af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887698"
---
# <a name="monitoring-azure-databricks"></a><span data-ttu-id="b5d44-103">Мониторинг Azure Databricks</span><span class="sxs-lookup"><span data-stu-id="b5d44-103">Monitoring Azure Databricks</span></span>

<span data-ttu-id="b5d44-104">[Azure Databricks](/azure/azure-databricks/) — это высокопроизводительная и многофункциональная служба аналитики на основе [Apache Spark](https://spark.apache.org/), которая позволяет без труда разрабатывать и развертывать решения аналитики больших данных и искусственного интеллекта (ИИ).</span><span class="sxs-lookup"><span data-stu-id="b5d44-104">[Azure Databricks](/azure/azure-databricks/) is a fast, powerful [Apache Spark](https://spark.apache.org/)–based analytics service that makes it easy to rapidly develop and deploy big data analytics and artificial intelligence (AI) solutions.</span></span> <span data-ttu-id="b5d44-105">Многие пользователи по достоинству оценили простоту записных книжек в решениях Azure Databricks.</span><span class="sxs-lookup"><span data-stu-id="b5d44-105">Many users take advantage of the simplicity of notebooks in their Azure Databricks solutions.</span></span> <span data-ttu-id="b5d44-106">Если же требуются более надежные вычислительные ресурсы, Azure Databricks поддерживает распределенное выполнение пользовательского кода приложений.</span><span class="sxs-lookup"><span data-stu-id="b5d44-106">For users that require more robust computing options, Azure Databricks supports the distributed execution of custom application code.</span></span>

<span data-ttu-id="b5d44-107">Мониторинг — это важнейшая часть любого решения уровня рабочей среды. Azure Databricks предоставляет надежные функции для мониторинга метрик пользовательских приложений, потоковой передачи событий запроса и отправки сообщений журнала приложений.</span><span class="sxs-lookup"><span data-stu-id="b5d44-107">Monitoring is a critical part of any production-level solution, and Azure Databricks offers robust functionality for monitoring custom application metrics, streaming query events, and application log messages.</span></span> <span data-ttu-id="b5d44-108">Данные мониторинга Azure Databricks можно отправлять в различные службы ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="b5d44-108">Azure Databricks can send this monitoring data to different logging services.</span></span>

<span data-ttu-id="b5d44-109">В перечисленных ниже статьях объясняется, как отправлять данные мониторинга из Azure Databricks в [Azure Monitor](/azure/azure-monitor/overview), платформу данных мониторинга для Azure.</span><span class="sxs-lookup"><span data-stu-id="b5d44-109">The following articles show how to send monitoring data from Azure Databricks to [Azure Monitor](/azure/azure-monitor/overview), the monitoring data platform for Azure.</span></span> <span data-ttu-id="b5d44-110">Изучайте эти статьи в указанном порядке.</span><span class="sxs-lookup"><span data-stu-id="b5d44-110">You should follow these articles in order.</span></span>

1. [<span data-ttu-id="b5d44-111">Настройка Azure Databricks для отправки метрик в Azure Monitor</span><span class="sxs-lookup"><span data-stu-id="b5d44-111">Configure Azure Databricks to send metrics to Azure Monitor</span></span>](./configure-cluster.md)
1. [<span data-ttu-id="b5d44-112">Отправка журналов приложения Azure Databricks в Azure Monitor</span><span class="sxs-lookup"><span data-stu-id="b5d44-112">Send Azure Databricks application logs to Azure Monitor</span></span>](./application-logs.md)
1. [<span data-ttu-id="b5d44-113">Визуализация метрик Azure Databricks с помощью панелей мониторинга</span><span class="sxs-lookup"><span data-stu-id="b5d44-113">Use dashboards to visualize Azure Databricks metrics</span></span>](./dashboards.md)

<span data-ttu-id="b5d44-114">Библиотека кода, сопровождающая эти статьи, расширяет основные возможности мониторинга Azure Databricks для отправки метрик, событий и данных журналов Spark в Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="b5d44-114">The code library that accompanies these articles extends the core monitoring functionality of Azure Databricks to send Spark metrics, events, and logging information to Azure Monitor.</span></span>

<span data-ttu-id="b5d44-115">Эти статьи и сопровождающая их библиотека кода предназначены для разработчиков решений Apache Spark и Azure Databricks.</span><span class="sxs-lookup"><span data-stu-id="b5d44-115">The audience for these articles and the accompanying code library are Apache Spark and Azure Databricks solution developers.</span></span> <span data-ttu-id="b5d44-116">Код нужно встроить в файлы архива Java (JAR), а затем развернуть в кластере Azure Databricks.</span><span class="sxs-lookup"><span data-stu-id="b5d44-116">The code must be built into Java Archive (JAR) files and then deployed to an Azure Databricks cluster.</span></span> <span data-ttu-id="b5d44-117">Код представляет собой сочетание [Scala](https://www.scala-lang.org/) и Java с соответствующим набором файлов объектной модели проекта (POM) [Maven](https://maven.apache.org) для создания выходных JAR-файлов.</span><span class="sxs-lookup"><span data-stu-id="b5d44-117">The code is a combination of [Scala](https://www.scala-lang.org/) and Java, with a corresponding set of [Maven](https://maven.apache.org) project object model (POM) files to build the output JAR files.</span></span> <span data-ttu-id="b5d44-118">Перед началом работы рекомендуем изучить общие сведения о Java, Scala и Maven.</span><span class="sxs-lookup"><span data-stu-id="b5d44-118">Understanding of Java, Scala, and Maven are recommended as prerequisistes.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b5d44-119">Дополнительная информация</span><span class="sxs-lookup"><span data-stu-id="b5d44-119">Next steps</span></span>

<span data-ttu-id="b5d44-120">Сначала создайте библиотеку кода и разверните ее в кластере Azure Databricks.</span><span class="sxs-lookup"><span data-stu-id="b5d44-120">Start by building the code library and deploying it to your Azure Databricks cluster.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="b5d44-121">Настройка Azure Databricks для отправки метрик в Azure Monitor</span><span class="sxs-lookup"><span data-stu-id="b5d44-121">Configure Azure Databricks to send metrics to Azure Monitor</span></span>](./configure-cluster.md)