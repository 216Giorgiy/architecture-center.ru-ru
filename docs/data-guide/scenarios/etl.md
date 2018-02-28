---
title: "Извлечение, преобразование и загрузка (ETL)"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: a980c1f8aef99fc263083e5e496b1340204f7dac
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2018
---
# <a name="extract-transform-and-load-etl"></a><span data-ttu-id="a4cb2-102">Извлечение, преобразование и загрузка (ETL)</span><span class="sxs-lookup"><span data-stu-id="a4cb2-102">Extract, transform, and load (ETL)</span></span>

<span data-ttu-id="a4cb2-103">Распространенной проблемой, с которой сталкиваются организации, является сбор данных из нескольких источников в нескольких форматах и ​​перенос этих данных в одно или несколько хранилищ данных.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-103">A common problem that organizations face is how to gathering data from multiple sources, in multiple formats, and move it to one or more data stores.</span></span> <span data-ttu-id="a4cb2-104">Тип хранилища данных в целевой и исходной средах может отличаться, также как и формат. Иногда данные необходимо форматировать или очистить до загрузки в конечное расположение.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-104">The destination may not be the same type of data store as the source, and often the format is different, or the data needs to be shaped or cleaned before loading it into its final destination.</span></span>

<span data-ttu-id="a4cb2-105">За несколько лет для решения этих проблем было разработано много средств, служб и процессов.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-105">Various tools, services, and processes have been developed over the years to help address these challenges.</span></span> <span data-ttu-id="a4cb2-106">Независимо от используемого процесса, существует общая потребность в координации работы и преобразовании данных в конвейере.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-106">No matter the process used, there is a common need to coordinate the work and apply some level of data transformation within the data pipeline.</span></span> <span data-ttu-id="a4cb2-107">В следующих разделах описываются распространенные методы, используемые для выполнения этих задач.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-107">The following sections highlight the common methods used to perform these tasks.</span></span>

## <a name="extract-transform-and-load-etl"></a><span data-ttu-id="a4cb2-108">Извлечение, преобразование и загрузка (ETL)</span><span class="sxs-lookup"><span data-stu-id="a4cb2-108">Extract, transform, and load (ETL)</span></span>

<span data-ttu-id="a4cb2-109">Извлечение, преобразование и загрузка (ETL) представляет собой конвейер данных, в рамках которого данные собираются из различных источников, преобразовываются в соответствии с бизнес-правилами и загружаются в целевое хранилище данных.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-109">Extract, transform, and load (ETL) is a data pipeline used to collect data from various sources, transform the data according to business rules, and load it into a destination data store.</span></span> <span data-ttu-id="a4cb2-110">Процесс преобразования в конвейере ETL выполняется в специальной подсистеме. Зачастую для временного хранения данных во время их преобразования и до загрузки в пункт назначения используются промежуточные таблицы.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-110">The transformation work in ETL takes place in a specialized engine, and often involves using staging tables to temporarily hold data as it is being transformed and ultimately loaded to its destination.</span></span>

<span data-ttu-id="a4cb2-111">Обычно в процессе преобразования данных применяются различные операции (например, фильтрация, сортировка, агрегирование, объединение, очистка, дедупликация и проверка данных).</span><span class="sxs-lookup"><span data-stu-id="a4cb2-111">The data transformation that takes place usually involves various operations, such as filtering, sorting, aggregating, joining data, cleaning data, deduplicating, and validating data.</span></span>

![Процесс извлечения, преобразования и загрузки](./images/etl.png)

<span data-ttu-id="a4cb2-113">Часто три этапа ETL выполняются параллельно, чтобы сэкономить время.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-113">Often, the three ETL phases are run in parallel to save time.</span></span> <span data-ttu-id="a4cb2-114">Например, при извлечении данных процесс преобразования может уже обрабатывать полученные данные и подготавливать их для загрузки, а процесс загрузки может начать обрабатывать подготовленные данные, не дожидаясь полного завершения извлечения.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-114">For example, while data is being extracted, a transformation process could be working on data already received and prepare it for loading, and a loading process can begin working on the prepared data, rather than waiting for the entire extraction process to complete.</span></span>

<span data-ttu-id="a4cb2-115">Соответствующие службы Azure:</span><span class="sxs-lookup"><span data-stu-id="a4cb2-115">Relevant Azure service:</span></span>
- <span data-ttu-id="a4cb2-116">[Фабрика данных Azure версии 2](https://azure.microsoft.com/services/data-factory/).</span><span class="sxs-lookup"><span data-stu-id="a4cb2-116">[Azure Data Factory v2](https://azure.microsoft.com/services/data-factory/)</span></span>

<span data-ttu-id="a4cb2-117">Другие средства:</span><span class="sxs-lookup"><span data-stu-id="a4cb2-117">Other tools:</span></span>
- [<span data-ttu-id="a4cb2-118">SQL Server Integration Services (SSIS)</span><span class="sxs-lookup"><span data-stu-id="a4cb2-118">SQL Server Integration Services (SSIS)</span></span>](/sql/integration-services/sql-server-integration-services)

## <a name="extract-load-and-transform-elt"></a><span data-ttu-id="a4cb2-119">Извлечение, загрузка и преобразование (ELT)</span><span class="sxs-lookup"><span data-stu-id="a4cb2-119">Extract, load, and transform (ELT)</span></span>

<span data-ttu-id="a4cb2-120">Конвейер извлечения, загрузки и преобразования (ELT) отличается от ETL исключительно средой выполнения преобразования.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-120">Extract, load, and transform (ELT) differs from ETL solely in where the transformation takes place.</span></span> <span data-ttu-id="a4cb2-121">В конвейере ELT преобразование происходит в целевом хранилище данных.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-121">In the ELT pipeline, the transformation occurs in the target data store.</span></span> <span data-ttu-id="a4cb2-122">В этом случае для преобразования данных вместо специальной подсистемы используются средства обработки целевого хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-122">Instead of using a separate transformation engine, the processing capabilities of the target data store are used to transform data.</span></span> <span data-ttu-id="a4cb2-123">Это упрощает архитектуру за счет удаления механизма преобразования из конвейера.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-123">This simplifies the architecture by removing the transformation engine from the pipeline.</span></span> <span data-ttu-id="a4cb2-124">Еще одним преимуществом этого подхода является то, что масштабирование целевого хранилища данных также улучшает производительность конвейера ELT.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-124">Another benefit to this approach is that scaling the target data store also scales the ELT pipeline performance.</span></span> <span data-ttu-id="a4cb2-125">Тем не менее ELT работает надлежащим образом, только если целевая система имеет достаточную производительность для эффективного преобразования данных.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-125">However, ELT only works well when the target system is powerful enough to transform the data efficiently.</span></span>

![Процесс извлечения, загрузки и преобразования (ELT)](./images/elt.png)

<span data-ttu-id="a4cb2-127">Обычно конвейер ELT применяется для обработки больших объемов данных.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-127">Typical use cases for ELT fall within the big data realm.</span></span> <span data-ttu-id="a4cb2-128">К примеру, вы можете извлечь все исходные данные в неструктурированные файлы в масштабируемое хранилище (например, распределенную файловую систему Hadoop (HDFS) или Azure Data Lake Store).</span><span class="sxs-lookup"><span data-stu-id="a4cb2-128">For example, you might start by extracting all of the source data to flat files in scalable storage such as Hadoop distributed file system (HDFS) or Azure Data Lake Store.</span></span> <span data-ttu-id="a4cb2-129">Затем для выполнения запроса исходных данных можно использовать такие технологии, как Spark, Hive или PolyBase.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-129">Technologies such as Spark, Hive, or PolyBase can then be used to query the source data.</span></span> <span data-ttu-id="a4cb2-130">Ключевой особенностью ELT является то, что хранилище данных, используемое для выполнения преобразования, — это то же хранилище, в котором данные в конечном счете потребляются.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-130">The key point with ELT is that the data store used to perform the transformation is the same data store where the data is ultimately consumed.</span></span> <span data-ttu-id="a4cb2-131">Это хранилище данных считывает данные непосредственно из масштабируемого хранилища, вместо того чтобы загружать их в собственное защищаемое хранилище.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-131">This data store reads directly from the scalable storage, instead of loading the data into its own proprietary storage.</span></span> <span data-ttu-id="a4cb2-132">Этот подход пропускает этап копирования (присутствующий в ETL), который может занимать много времени при обработке больших наборов данных.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-132">This approach skips the data copy step present in ETL, which can be a time consuming operation for large data sets.</span></span>

<span data-ttu-id="a4cb2-133">Обычно целевым хранилищем является [хранилище данных](./data-warehousing.md), использующее кластер Hadoop (с помощью Hive или Spark) или хранилище данных SQL.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-133">In practice, the target data store is a [data warehouse](./data-warehousing.md) using either a Hadoop cluster (using Hive or Spark) or a SQL Data Warehouse.</span></span> <span data-ttu-id="a4cb2-134">Чаще всего схема накладывается на данные неструктурированных файлов во время выполнения запроса и сохраняется в виде таблиц, позволяя запрашивать данные таким же образом, как и любую другую таблицу в хранилище данных.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-134">In general, a schema is overlaid on the flat file data at query time and stored as a table, enabling the data to be queried like any other table in the data store.</span></span> <span data-ttu-id="a4cb2-135">Они называются внешними таблицами, так как данные находятся не в хранилище, управляемом самим хранилищем данных, а во внешнем масштабируемом хранилище.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-135">These are referred to as external tables because the data does not reside in storage managed by the data store itself, but on some external scalable storage.</span></span> 

<span data-ttu-id="a4cb2-136">Хранилище данных управляет только схемой данных и применяет ее при чтении.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-136">The data store only manages the schema of the data and applies the schema on read.</span></span> <span data-ttu-id="a4cb2-137">Например, кластер Hadoop, использующий Hive, описывает таблицу Hive, где источником данных является фактический путь к набору файлов в HDFS.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-137">For example, a Hadoop cluster using Hive would describe a Hive table where the data source is effectively a path to a set of files in HDFS.</span></span> <span data-ttu-id="a4cb2-138">В хранилище данных SQL технология PolyBase может достичь того же результата, создав таблицу с данными, хранящимися в самой базе данных.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-138">In SQL Data Warehouse, PolyBase can achieve the same result &mdash; creating a table against data stored externally to the database itself.</span></span> <span data-ttu-id="a4cb2-139">Когда исходные данные загружены, данные, имеющиеся во внешних таблицах, можно обрабатывать, используя возможности хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-139">Once the source data is loaded, the data present in the external tables can be processed using the capabilities of the data store.</span></span> <span data-ttu-id="a4cb2-140">В сценариях с большими данными это означает, что хранилище данных должно поддерживать массовую параллельную обработку (MPP), когда данные разбиваются на более мелкие фрагменты, а обработка этих фрагментов распределяется сразу между несколькими компьютерами в параллельном режиме.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-140">In big data scenarios, this means the data store must be capable of massively parallel processing (MPP), which breaks the data into smaller chunks and distributes processing of the chunks across multiple machines in parallel.</span></span>

<span data-ttu-id="a4cb2-141">Последний этап конвейера ELT обычно заключается в преобразовании исходных данных в окончательный формат, более эффективный для тех типов запросов, которые необходимо поддерживать.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-141">The final phase of the ELT pipeline is typically to transform the source data into a final format that is more efficient for the types of queries that need to be supported.</span></span> <span data-ttu-id="a4cb2-142">Например, данные могут быть секционированы.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-142">For example, the data may be partitioned.</span></span> <span data-ttu-id="a4cb2-143">Кроме того, ELT может использовать оптимизированные форматы хранения (например, Parquet), в которых построчные данные хранятся в виде столбцов и предоставляется оптимизированная индексация.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-143">Also, ELT might use optimized storage formats like Parquet, which stores row-oriented data in a columnar fashion and providess optimized indexing.</span></span> 

<span data-ttu-id="a4cb2-144">Соответствующие службы Azure:</span><span class="sxs-lookup"><span data-stu-id="a4cb2-144">Relevant Azure service:</span></span>

- [<span data-ttu-id="a4cb2-145">Хранилище данных Azure SQL</span><span class="sxs-lookup"><span data-stu-id="a4cb2-145">Azure SQL Data Warehouse</span></span>](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)
- <span data-ttu-id="a4cb2-146">[HDInsight с Hive](/azure/hdinsight/hadoop/hdinsight-use-hive);</span><span class="sxs-lookup"><span data-stu-id="a4cb2-146">[HDInsight with Hive](/azure/hdinsight/hadoop/hdinsight-use-hive)</span></span>
- <span data-ttu-id="a4cb2-147">[фабрика данных Azure версии 2](https://azure.microsoft.com/services/data-factory/);</span><span class="sxs-lookup"><span data-stu-id="a4cb2-147">[Azure Data Factory v2](https://azure.microsoft.com/services/data-factory/)</span></span>
- <span data-ttu-id="a4cb2-148">[Oozie в HDInsight](/azure/hdinsight/hdinsight-use-oozie-linux-mac).</span><span class="sxs-lookup"><span data-stu-id="a4cb2-148">[Oozie on HDInsight](/azure/hdinsight/hdinsight-use-oozie-linux-mac)</span></span>

<span data-ttu-id="a4cb2-149">Другие средства:</span><span class="sxs-lookup"><span data-stu-id="a4cb2-149">Other tools:</span></span>

- [<span data-ttu-id="a4cb2-150">SQL Server Integration Services (SSIS)</span><span class="sxs-lookup"><span data-stu-id="a4cb2-150">SQL Server Integration Services (SSIS)</span></span>](/sql/integration-services/sql-server-integration-services)

## <a name="data-flow-and-control-flow"></a><span data-ttu-id="a4cb2-151">Поток данных и поток управления</span><span class="sxs-lookup"><span data-stu-id="a4cb2-151">Data flow and control flow</span></span>

<span data-ttu-id="a4cb2-152">В контексте конвейеров данных поток управления обеспечивает обработку набора задач в правильном порядке.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-152">In the context of data pipelines, the control flow ensures orderly processing of a set of tasks.</span></span> <span data-ttu-id="a4cb2-153">Для этого используется управление очередностью.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-153">To enforce the correct processing order of these tasks, precedence constraints are used.</span></span> <span data-ttu-id="a4cb2-154">Эти ограничения можно рассматривать как соединители на схеме рабочего процесса, показанной ниже.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-154">You can think of these constraints as connectors in a workflow diagram, as shown in the image below.</span></span> <span data-ttu-id="a4cb2-155">Каждая задача имеет результат (успешное завершение, сбой или завершение).</span><span class="sxs-lookup"><span data-stu-id="a4cb2-155">Each task has an outcome, such as success, failure, or completion.</span></span> <span data-ttu-id="a4cb2-156">Все последующие задачи начинают обработку данных, только когда предыдущая задача завершена с одним из этих результатов.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-156">Any subsequent task does not initiate processing until its predecessor has completed with one of these outcomes.</span></span>

<span data-ttu-id="a4cb2-157">Потоки управления выполняют потоки данных в качестве задачи.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-157">Control flows execute data flows as a task.</span></span> <span data-ttu-id="a4cb2-158">В рамках задачи потока данных данные извлекаются из источника, преобразовываются и загружаются в хранилище данных.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-158">In a data flow task, data is extracted from a source, transformed, or loaded into a data store.</span></span> <span data-ttu-id="a4cb2-159">Выходные данные одной задачи потока данных могут использоваться в качестве входных данных для следующей задачи потока данных, а эти потоки могут выполняться одновременно.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-159">The output of one data flow task can be the input to the next data flow task, and data flowss can run in parallel.</span></span> <span data-ttu-id="a4cb2-160">В отличие от потоков управления, вы не можете добавить ограничения между задачами в потоке данных.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-160">Unlike control flows, you cannot add constraints between tasks in a data flow.</span></span> <span data-ttu-id="a4cb2-161">Однако вы можете добавить средство просмотра данных для наблюдения за данными по мере их обрабатывания каждой задачей.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-161">You can, however, add a data viewer to observe the data as it is processed by each task.</span></span>

![Поток данных, выполняемый в виде задачи в потоке управления](./images/control-flow-data-flow.png)

<span data-ttu-id="a4cb2-163">На приведенной выше схеме показано несколько задач в потоке управления, одна из которых является задачей потока данных.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-163">In the diagram above, there are several tasks within the control flow, one of which is a data flow task.</span></span> <span data-ttu-id="a4cb2-164">Одна из задач вложена в контейнер.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-164">One of the tasks is nested within a container.</span></span> <span data-ttu-id="a4cb2-165">Контейнеры можно использовать для обеспечения структуры задач, тем самым формируя единицу работы.</span><span class="sxs-lookup"><span data-stu-id="a4cb2-165">Containers can be used to provide structure to tasks, providing a unit of work.</span></span> <span data-ttu-id="a4cb2-166">Одним из примеров является повторение элементов в коллекции (например, файлы в папке или инструкции базы данных).</span><span class="sxs-lookup"><span data-stu-id="a4cb2-166">One such example is for repeating elements within a collection, such as files in a folder or database statements.</span></span>

<span data-ttu-id="a4cb2-167">Соответствующие службы Azure:</span><span class="sxs-lookup"><span data-stu-id="a4cb2-167">Relevant Azure service:</span></span>
- <span data-ttu-id="a4cb2-168">[Фабрика данных Azure версии 2](https://azure.microsoft.com/services/data-factory/).</span><span class="sxs-lookup"><span data-stu-id="a4cb2-168">[Azure Data Factory v2](https://azure.microsoft.com/services/data-factory/)</span></span>

<span data-ttu-id="a4cb2-169">Другие средства:</span><span class="sxs-lookup"><span data-stu-id="a4cb2-169">Other tools:</span></span>
- [<span data-ttu-id="a4cb2-170">SQL Server Integration Services (SSIS)</span><span class="sxs-lookup"><span data-stu-id="a4cb2-170">SQL Server Integration Services (SSIS)</span></span>](/sql/integration-services/sql-server-integration-services)

## <a name="technology-choices"></a><span data-ttu-id="a4cb2-171">Выбор технологий</span><span class="sxs-lookup"><span data-stu-id="a4cb2-171">Technology choices</span></span>

- [<span data-ttu-id="a4cb2-172">Хранилища данных оперативной обработки транзакций (OLTP)</span><span class="sxs-lookup"><span data-stu-id="a4cb2-172">Online Transaction Processing (OLTP) data stores</span></span>](../technology-choices/oltp-data-stores.md)
- [<span data-ttu-id="a4cb2-173">Хранилища данных оперативной аналитической обработки (OLAP)</span><span class="sxs-lookup"><span data-stu-id="a4cb2-173">Online Analytical Processing (OLAP) data stores</span></span>](../technology-choices/olap-data-stores.md)
- [<span data-ttu-id="a4cb2-174">Хранилища данных</span><span class="sxs-lookup"><span data-stu-id="a4cb2-174">Data warehouses</span></span>](../technology-choices/data-warehouses.md)
- [<span data-ttu-id="a4cb2-175">Оркестрация конвейеров</span><span class="sxs-lookup"><span data-stu-id="a4cb2-175">Pipeline orchestration</span></span>](../technology-choices/pipeline-orchestration-data-movement.md)