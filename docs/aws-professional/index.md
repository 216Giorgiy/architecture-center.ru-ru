---
title: "Azure для специалистов AWS"
description: "Получите представление об учетных записях, платформе и службах Microsoft Azure. А также изучите основные общие черты и различия между AWS и платформами Azure. Используйте навыки работы с AWS в Azure."
keywords: AWS experts, Azure comparison, AWS comparison, difference between azure and aws, azure and aws
author: lbrader
ms.date: 03/24/2017
pnp.series.title: Azure for AWS Professionals
ms.openlocfilehash: 251489e7a6d78d82f3ed70ca2df6c88f8759f9a5
ms.sourcegitcommit: fbcf9a1c25db13b2627a8a58bbc985cd01ea668d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2017
---
# <a name="azure-for-aws-professionals"></a>Azure для специалистов AWS

Эта статья поможет специалистам по Amazon Web Services (AWS) получить представление об учетных записях, платформе и службах Microsoft Azure. Кроме того, в ней описаны основные общие черты и различия между AWS и платформами Azure.

Вы узнаете:

* как организованы учетные записи и ресурсы в Azure;
* как структурированы решения в Azure;
* чем основные службы Azure отличаются от служб AWS.

 В течение долгого времени Azure и AWS развивались независимо друг от друга. Поэтому между ними есть важные различия в реализации и разработке решений.

## <a name="overview"></a>Обзор

Как и AWS, платформа Microsoft Azure основана на базовом наборе служб вычислений, хранения, баз данных и сетевых служб. Во многих случаях обе платформы предоставляют эквивалентные продукты и службы. AWS и Azure позволяют создавать решения высокого уровня доступности на основе узлов Windows или Linux. Если вы привыкли выполнять разработку с помощью Linux и технологии OSS, подходят обе платформы.

Возможности обеих платформ схожи, но предоставляющие их ресурсы часто организованы по-разному. Поэтому взаимодействие между службами для разработки решений не всегда эквивалентно. Также встречаются случаи, когда конкретная служба может предлагаться на одной платформе, но недоступна на другой. См. [схемы сравнения служб Azure и AWS](services.md).

## <a name="accounts-and-subscriptions"></a>Учетные записи и подписки

Для служб Azure предусмотрено несколько вариантов тарификации. Можно подобрать подходящий тариф в соответствии с размером и потребностями организации. Дополнительные сведения см. на странице с [обзором цен](https://azure.microsoft.com/pricing/).

[Подписки Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-infrastructure-subscription-accounts-guidelines/) — это группы ресурсов с назначенным владельцем, который отвечает за управление счетами и разрешениями. В отличие от AWS, где все ресурсы, созданные для учетной записи, привязаны к ней, подписки существуют независимо от учетных записей их владельцев и при необходимости могут быть переназначены новым владельцам.

![Сравнение структуры учетных записей AWS и подписок Azure и особенностей владения ими](./images/azure-aws-account-compare.png "Comparison of structure and ownership of AWS accounts and Azure subscriptions")
<br/>*Сравнение структуры учетных записей AWS и подписок Azure и особенностей владения ими*
<br/><br/>

Подпискам назначаются три типа учетных записей администратора:

-   **Администратор учетной записи.** Владелец подписки и пользователь учетной записи, которому выставляются счета за использование ресурсов в подписке. Администратора учетной записи можно изменить, только передав права владения подпиской.

-   **Администратор служб.** Эта учетная запись предоставляет права на создание ресурсов в подписке и управление ими, но не охватывает выставление счетов. По умолчанию администратору учетной записи и администратору служб назначается одна и та же учетная запись. Администратор учетной записи может назначить отдельному пользователю учетную запись администратора, чтобы он мог управлять техническими и рабочими аспектами подписки. Для каждой подписки можно назначить только одного администратора служб.

-   **Соадминистратор.** Для каждой подписки можно назначить несколько учетных записей соадминистратора. Соадминистраторы не могут изменить администратора служб, но имеют полный контроль над ресурсами и пользователями подписки.

На более низком уровне роли и отдельные разрешения пользователей также можно назначить определенным ресурсам, аналогично предоставлению разрешений пользователям и группам IAM в AWS. В Azure все учетные записи пользователей связаны с учетной записью Майкрософт или учетной записью организации (учетная запись в Azure Active Directory).

Как и для учетных записей AWS, для подписок действуют квоты и ограничения службы по умолчанию. Полный список этих ограничений см. в статье [Подписка Azure, границы, квоты и ограничения службы](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/).
Эти ограничения можно максимально увеличить, [заполнив форму запроса на поддержку на портале управления](https://blogs.msdn.microsoft.com/girishp/2015/09/20/increasing-core-quota-limits-in-azure/).

### <a name="see-also"></a>См. также

-   [Добавление или изменение ролей администратора Azure](https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/).

-   [Как скачать счет на оплату и данные о ежедневном использовании ресурсов Azure](https://azure.microsoft.com/documentation/articles/billing-download-azure-invoice-daily-usage-date/)

## <a name="resource-management"></a>Управление ресурсами

Термин "ресурс" в Azure определяется так же, как и в AWS. То есть это любой вычислительной экземпляр, объект хранилища, сетевое устройство или другая сущность, которую можно создать или настроить на платформе.

Ресурсы Azure развертываются и управляются с помощью одной из двух моделей: [Azure Resource Manager или более ранней [классической модели развертывания](/azure/azure-resource-manager/resource-manager-deployment-model)Azure.
Все новые ресурсы создаются с помощью модели Resource Manager.

### <a name="resource-groups"></a>Группы ресурсов

В Azure и AWS есть сущности, которые называются группами ресурсов. В них упорядочиваются ресурсы, такие как виртуальные машины, хранилище и виртуальные сетевые устройства. Но [группа ресурсов Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/) не является полным аналогом группы ресурсов AWS.

В AWS ресурс можно отметить как принадлежащий нескольким группам ресурсов, а ресурс Azure всегда связан только с одной. Ресурс, созданный в одной группе, можно переместить в другую. Но он не может присутствовать в нескольких группах ресурсов одновременно. Группы ресурсов — это основные группы, используемые в Azure Resource Manager.

Кроме того, ресурсы можно упорядочить с помощью [тегов](https://azure.microsoft.com/documentation/articles/resource-group-using-tags/).
Теги — это пары "ключ — значение", которые позволяют группировать ресурсы в подписке независимо от членства в группе ресурсов.

### <a name="management-interfaces"></a>Интерфейсы управления

Azure предоставляет несколько способов управления ресурсами:

-   [Веб-интерфейс.](https://azure.microsoft.com/documentation/articles/resource-group-portal/)
    Как и панель мониторинга AWS, портал Azure предоставляет полнофункциональный интерфейс управления для ресурсов Azure.

-   [REST API.](https://azure.microsoft.com/documentation/articles/resource-manager-rest-api/)
    REST API Azure Resource Manager предоставляет программный доступ к большинству функций на портале Azure.

-   [Командная строка.](https://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/)
    Средство Azure CLI 2.0 предоставляет интерфейс командной строки для создания ресурсов Azure и управления ими. Интерфейс Azure CLI доступен для операционных систем [Windows, Linux и Mac OS](https://aka.ms/azurecli2).

-   [PowerShell.](https://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/)
    Модули Azure для PowerShell позволяют выполнять автоматизированные задачи управления с помощью скриптов. Модули PowerShell доступны для операционных систем [Windows, Linux и Mac OS](https://github.com/PowerShell/PowerShell).

-   [Шаблоны.](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/)
    Шаблоны Azure Resource Manager предоставляют те же возможности управления ресурсами на основе шаблонов JSON, что и служба AWS CloudFormation.

В каждом из этих интерфейсов группа ресурсов определяет, как создаются, развертываются или изменяются ресурсы Azure. Подобную роль играет стек при группировании ресурсов AWS во время развертывания CloudFormation.

Синтаксис и структура этих интерфейсов отличаются от их аналогов в AWS, но они обеспечивают схожие возможности. Кроме того, многие средства управления сторонних производителей, которые используются в AWS, такие как [Terraform Hashicorp](https://www.terraform.io/docs/providers/azurerm/) и [Netflix Spinnaker](http://www.spinnaker.io/), также доступны в Azure.

### <a name="see-also"></a>См. также

-   [Рекомендации по группам ресурсов Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

## <a name="regions-and-zones-high-availability"></a>Регионы и зоны (высокий уровень доступности)

В AWS доступность основывается на понятии зон доступности. В Azure при разработке решений с высоким уровнем доступности применяются домены сбоя и группы доступности. Пары регионов расширяют возможности аварийного восстановления.

### <a name="availability-zones-azure-fault-domains-and-availability-sets"></a>Зоны доступности, домены сбоя Azure и группы доступности

В AWS регион разделен на несколько зон доступности. Зона доступности соответствует физически изолированному центру обработки данных в географическом регионе.
При развертывании серверов приложений в отдельных зонах доступности сбой оборудования или подключения в одной зоне не влияет на серверы в других.

В Azure [домен сбоя](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-manage-availability/) — это группа виртуальных машин, которыми совместно используется один физический источник питания и сетевой коммутатор.
[Группы доступности](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-manage-availability/) используются для распределения виртуальных машин по разным доменам сбоя. Если экземпляры назначаются одной группе доступности, Azure равномерно распределяет их по нескольким доменам сбоя. Если в домене сбоя отключается питание или происходит сбой сети, по крайней мере некоторые виртуальные машины из группы находятся в другом домене сбоя и простой их не затрагивает.

![Сравнение зон доступности AWS с доменами сбоя и группами доступности Azure](./images/zone-fault-domains.png "AWS Availability Zones compared with Azure fault domains and availability sets")
<br/>*Сравнение зон доступности AWS с доменами сбоя и группами доступности Azure*
<br/><br/>

Группы доступности должны быть упорядочены по роли экземпляра в приложении, чтобы обеспечить работу одного экземпляра для каждой роли. Например, в стандартном трехуровневом веб-приложении нужно создать отдельные группы доступности для внешнего интерфейса, приложения и экземпляров данных.

![Группы доступности Azure для каждой роли приложения](./images/three-tier-example.png "Availability sets for each application role")
<br/>*Группы доступности Azure для каждой роли приложения*
<br/><br/>

Когда экземпляры виртуальной машины добавляются в группы доступности, им назначается [домен обновления](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-manage-availability/).
Домен обновления — это группа виртуальных машин для одновременных запланированных событий технического обслуживания. Распределение виртуальных машин по нескольким доменам обновления гарантирует, что запланированные события обновления и исправления в любое время влияют только на подгруппу этих виртуальных машин.

### <a name="paired-regions"></a>Пары регионов

В Azure [пары регионов](https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/) используются для поддержки избыточности между двумя заранее определенными географическими регионами. Это гарантирует, что, даже если сбой затрагивает весь регион Azure, ваше решение по-прежнему будет доступно.

В отличие от зон доступности AWS, которые являются физически разделенными центрами обработки данных, но могут находиться в расположенных относительно близко географических областях, пары регионов обычно находятся на расстоянии не менее 500 км. Благодаря этому в случае масштабных аварий затрагивается только один из регионов в паре. Соседним парам можно назначить базу данных синхронизации и данные службы хранилища. Вы можете настроить пары таким образом, чтобы обновления платформы распространялись только на один регион в паре за один раз.

Для [геоизбыточного хранилища Azure](https://azure.microsoft.com/documentation/articles/storage-redundancy/#geo-redundant-storage) автоматически выполняется резервное копирование в соответствующем регионе из пары. Для всех остальных ресурсов создание полностью избыточного решения с использованием пары регионов означает создание полной копии решения в обоих регионах.

### <a name="see-also"></a>См. также

-   [Регионы и доступность виртуальных машин в Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-regions-and-availability/).

-   [Обеспечение высокой доступности для приложений Azure](../resiliency/high-availability-azure-applications.md)

-   [Disaster recovery for Azure applications](../resiliency/disaster-recovery-azure-applications.md) (Аварийное восстановление для приложений Azure).

-   [Плановое обслуживание виртуальных машин Linux в Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-planned-maintenance/)

## <a name="services"></a>Службы

В [полной сравнительной таблице служб AWS и Azure](https://aka.ms/azure4aws-services) приведен список их общих черт и различий при работе с разными платформами.

Не все продукты и службы Azure доступны во всех регионах. Дополнительные сведения см. на странице [Доступность продуктов по регионам](https://azure.microsoft.com/regions/services/). Сведения о политике, касающейся гарантированного времени доступности и возмещения затрат в случае простоя для каждого продукта или службы Azure, см. на странице [Соглашения об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/).

В следующих разделах кратко описаны различия между часто используемыми функциями и службами платформ AWS и Azure.

### <a name="compute-services"></a>Службы вычислений

#### <a name="ec2-instances-and-azure-virtual-machines"></a>Экземпляры EC2 и виртуальные машины Azure

Типы экземпляров AWS и размеры виртуальных машин Azure классифицируются примерно одинаково, но между ними есть различия в возможностях ОЗУ, ЦП и хранилища.

-   [Типы экземпляров EC2 Amazon](https://aws.amazon.com/ec2/instance-types/).

-   [Размеры виртуальных машин в Azure (Windows)](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/).

-   [Размеры виртуальных машин в Azure (Linux)](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/).

В отличие от посекундной тарификации AWS, использование виртуальных машин Azure по требованию оценивается в минутах.

В Azure нет эквивалентов для точечных экземпляров EC2, зарезервированных экземпляров или выделенных узлов.

#### <a name="ebs-and-azure-storage-for-vm-disks"></a>EBS и служба хранилища Azure для дисков виртуальных машин

Надежное хранение данных для виртуальных машин Azure обеспечивается за счет [дисков данных](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-about-disks-vhds/) в хранилище BLOB-объектов. Для экземпляров EC2 тома дисков аналогичным образом хранятся в эластичном блочном хранилище (EBS). [Временное хранилище Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/) предоставляет для виртуальных машин временное пространство хранения с низкой задержкой и возможностью чтения и записи. Его аналог в AWS — хранилище экземпляров EC2 (также называется временным хранилищем).

[Хранилище Azure класса Premium](https://docs.microsoft.com/azure/storage/storage-premium-storage) поддерживает более высокую производительность операций ввода-вывода для дисков.
Такие же функции реализованы для хранилища с подготовленными операциями ввода-вывода, которое предоставляется в AWS.

#### <a name="lambda-azure-functions-azure-web-jobs-and-azure-logic-apps"></a>Lambda, решение "Функции Azure", служба "Веб-задания Azure" и Azure Logic Apps

Решение [Функции Azure](https://azure.microsoft.com/services/functions/) — это основной эквивалент AWS Lambda, который предоставляет бессерверный код по запросу.
Но функциональность Lambda также пересекается с другими службами Azure:

-   Служба [Веб-задания](https://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/) позволяет создавать запланированные или постоянно выполняющиеся задачи в фоновом режиме.

-   Служба [Logic Apps](https://azure.microsoft.com/services/logic-apps/) обеспечивает управление обменом данными, интеграцией и бизнес-правилами.

#### <a name="autoscaling-azure-vm-scaling-and-azure-app-service-autoscale"></a>Автоматическое масштабирование, масштабирование виртуальных машин Azure и автоматическое масштабирование службы приложений Azure

Автоматическое масштабирование в Azure поддерживается двумя службами:

-   Служба [масштабируемых наборов виртуальных машин](https://azure.microsoft.com/documentation/articles/virtual-machine-scale-sets-overview/) позволяет развернуть набор идентичных виртуальных машин и управлять ими. Число экземпляров может автоматически масштабироваться в соответствии с требованиями к производительности.

-   Служба [автомасштабирования службы приложений](https://azure.microsoft.com/documentation/articles/web-sites-scale/) позволяет автоматически масштабировать решения службы приложений Azure.


#### <a name="container-service"></a>Служба контейнеров
[Служба контейнеров Azure](https://docs.microsoft.com/azure/container-service/container-service-intro) поддерживает контейнеры Docker под управлением Docker Swarm, Kubernetes или DC/OS.

#### <a name="other-compute-services"></a>Другие службы вычислений 


Azure предоставляет несколько служб вычислений, которые не имеют полных аналогов в AWS:

-   [Пакетная служба Azure](https://azure.microsoft.com/documentation/articles/batch-technical-overview/) позволяет управлять ресурсоемкими вычислениями в масштабируемой коллекции виртуальных машин.

-   [Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-overview/) — платформа для разработки и размещения масштабируемых решений для [микрослужб](https://azure.microsoft.com/documentation/articles/service-fabric-overview-microservices/).

#### <a name="see-also"></a>См. также

-   [Создание виртуальной машины Linux в Azure с помощью портала](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-quick-create-portal/)

-   [Эталонная архитектура Azure: запуск виртуальной машины Linux в Azure](https://azure.microsoft.com/documentation/articles/guidance-compute-single-vm-linux/).

-   [Приступая к работе с веб-приложениями Node.js в службе приложений Azure](https://azure.microsoft.com/documentation/articles/app-service-web-nodejs-get-started/)

-   [Эталонная архитектура Azure: базовое веб-приложение](https://azure.microsoft.com/documentation/articles/guidance-web-apps-basic/).

-   [Создание первой функции Azure](https://azure.microsoft.com/documentation/articles/functions-create-first-azure-function/)

### <a name="storage"></a>Хранилище

#### <a name="s3ebsefs-and-azure-storage"></a>S3, EBS, EFS и служба хранилища Azure

На платформе AWS облачное хранилище разделено на три основные службы:

-   **Simple Storage Service (S3)** — базовое хранилище объектов. Обеспечивает доступ к данным через API-интерфейс, доступный в Интернете.

-   **Эластичное блочное хранилище (EBS)** — хранилище на уровне блоков, предназначенное для доступа с одной виртуальной машины.

-   **Эластичная файловая система (EFS)** — хранилище файлов, предназначенное для использования в качестве общего хранилища для большого (исчисляемого в тысячах) количества экземпляров EC2.

В службе хранилища Azure [учетные записи хранения](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/) с привязкой к подписке позволяют создавать следующие службы хранилища и управлять ими:

-   [Хранилище BLOB-объектов](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/) — здесь могут храниться текстовые или двоичные данные любого типа, например документы, файлы мультимедиа или установщики приложений. Можно задать для хранилища BLOB-объектов частный или общий доступ к содержимому в Интернете. Хранилище BLOB-объектов выполняет ту же функцию, что и S3 или EBS в AWS.

-   [Хранилище таблиц](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-table-storage/) — здесь хранятся структурированные наборы данных. Хранилище таблиц представляет собой хранилище данных NoSQL типа "ключ — атрибут", которое позволяет ускорить разработку и доступ к большим объемам данных. Аналогично службам SimpleDB и DynamoDB в AWS.

-   [Хранилище очередей](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-queues/) — обеспечивает обмен сообщениями, поддерживая рабочие процессы обработки и взаимодействие между компонентами облачных служб.

-   [Хранилище файлов](https://azure.microsoft.com/documentation/articles/storage-java-how-to-use-file-storage/) — предоставляет общее пространство хранения для приложений прежних версий с помощью стандартного протокола SMB. Хранилище файлов используется так же, как EFS на платформе AWS.

#### <a name="glacier-and-azure-storage"></a>Glacier и служба хранилища Azure

Служба хранилища Azure не является полным эквивалентом службы долгосрочного архивного хранения Glacier в AWS. Для данных, которые используются редко и долго хранятся, в Azure предусмотрен ["холодный" уровень хранилища BLOB-объектов](https://azure.microsoft.com/documentation/articles/storage-blob-storage-tiers/).
Затраты и производительность для "холодного" уровня хранилища BLOB-объектов ниже, чем для стандартного. Его можно сравнить с вариантом хранилища нечастого доступа S3 для AWS.

#### <a name="see-also"></a>См. также

-   [Производительность хранилища Microsoft Azure и контрольный список масштабируемости](https://azure.microsoft.com/documentation/articles/storage-performance-checklist/)

-   [Руководство по безопасности службы хранилища Azure](https://azure.microsoft.com/documentation/articles/storage-security-guide/)

-   [Шаблоны и рекомендации для сети доставки содержимого (CDN)](https://azure.microsoft.com/documentation/articles/best-practices-cdn/).

### <a name="networking"></a>Сеть

#### <a name="elastic-load-balancing-azure-load-balancer-and-azure-application-gateway"></a>Эластичная балансировка нагрузки, Azure Load Balancer и шлюз приложений Azure

В Azure эквивалентами для двух служб эластичной балансировки нагрузки являются:

-   [Load Balancer](https://azure.microsoft.com/documentation/articles/load-balancer-overview/) — предоставляет те же возможности, что и классическая подсистема балансировки нагрузки AWS. Позволяет распределять трафик между несколькими виртуальными машинами на уровне сети. Также предоставляет возможность отработки отказа.

-   [Шлюз приложений](https://azure.microsoft.com/documentation/articles/application-gateway-introduction/) — обеспечивает маршрутизацию на уровне приложений на основе правил. Аналог в AWS — подсистема балансировки нагрузки для приложений.

#### <a name="route-53-azure-dns-and-azure-traffic-manager"></a>Route 53, Azure DNS и диспетчер трафика Azure

В AWS Route 53 обеспечивает управление DNS-именами, маршрутизацию трафика на уровне DNS и службы отработки отказа. В Azure это реализуется посредством двух служб:

-   [Azure DNS](https://azure.microsoft.com/documentation/services/dns/) — обеспечивает управление доменами и DNS.

-   [Диспетчер трафика](https://azure.microsoft.com/documentation/articles/traffic-manager-overview/) — обеспечивает маршрутизацию трафика на уровне DNS, балансировку нагрузки и отработку отказа.

#### <a name="direct-connect-and-azure-expressroute"></a>Прямое соединение и Azure ExpressRoute

Azure предоставляет аналогичное выделенное подключение "сеть — сеть" при помощи службы [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). ExpressRoute позволяет подключить локальную сеть напрямую к ресурсам Azure с помощью выделенного подключения к частной сети. Также Azure предлагает более традиционные [VPN-подключения "сеть — сеть"](https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-site-to-site-resource-manager-portal/) по более низкой цене.

#### <a name="see-also"></a>См. также

-   [Создание виртуальной сети с помощью портала Azure](https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/)

-   [Планирование и проектирование виртуальных сетей Azure](https://azure.microsoft.com/documentation/articles/virtual-network-vnet-plan-design-arm/).

-   [Рекомендации по обеспечению безопасности в сети Azure](https://azure.microsoft.com/documentation/articles/azure-security-network-security-best-practices/)

### <a name="database-services"></a>Службы баз данных

#### <a name="rds-and-azure-sql-database-service"></a>RDS и служба "База данных SQL Azure"

В AWS и Azure используются различные подходы к реляционным базам данных в облаке. Служба реляционной базы данных (RDS) в AWS поддерживает создание экземпляров с помощью нескольких различных ядер СУБД, таких как Oracle и MySQL.

[База данных SQL](https://azure.microsoft.com/documentation/articles/sql-database-technical-overview/) — это служба базы данных Azure в облаке. Эта управляемая служба предоставляет реляционное хранилище данных с высоким уровнем масштабирования. В службе "База данных SQL" используется собственное ядро и не поддерживается создание баз данных других типов. Другие ядра СУБД, такие как [SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/), [Oracle](https://azure.microsoft.com/campaigns/oracle/) или [MySQL](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-classic-mysql-2008r2/), можно развернуть с помощью экземпляров виртуальных машин Azure.

Цены на RDS в AWS определяются объемом аппаратных ресурсов, которые использует экземпляр, таких как ЦП, память, хранилище и пропускная способности сети. В службе "База данных SQL" цена зависит от размера базы данных, параллельных подключений и уровней пропускной способности.

#### <a name="see-also"></a>См. также

-   [Руководства по службе "База данных SQL"](https://azure.microsoft.com/documentation/articles/sql-database-explore-tutorials/).

-   [Настройка георепликации для службы "База данных SQL" с помощью портала Azure](https://azure.microsoft.com/documentation/articles/sql-database-geo-replication-portal/).

-   [Знакомство с Cosmos DB: база данных NoSQL JSON](https://azure.microsoft.com/documentation/articles/documentdb-introduction/).

-   [Использование табличного хранилища Azure из Node.js](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-table-storage/)

### <a name="security-and-identity"></a>Безопасность и идентификация

#### <a name="directory-service-and-azure-active-directory"></a>Служба каталогов и Azure Active Directory

В Azure службы каталогов подразделяются на следующие предложения:

-   [Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-whatis/) — это облачный каталог и служба управления удостоверениями.

-   [Azure Active Directory B2B](https://azure.microsoft.com/documentation/articles/active-directory-b2b-collaboration-overview/) позволяет предоставлять доступ к корпоративным приложениям с помощью удостоверений, управляемых партнером.

-   [Azure Active Directory B2C](https://azure.microsoft.com/documentation/articles/active-directory-b2c-overview/) — служба с поддержкой единого входа и управления пользователями для приложений, ориентированных на конечного пользователя.

-   [Доменные службы Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-ds-overview/) — размещенная служба контроллера домена, которая обеспечивает совместимое с Active Directory присоединение к домену и функциональность управления пользователями.

#### <a name="web-application-firewall"></a>Брандмауэр веб-приложения

В дополнение к [брандмауэру веб-приложения для шлюза приложений](https://azure.microsoft.com/documentation/articles/application-gateway-webapplicationfirewall-overview/) вы можете [использовать брандмауэры веб-приложений](https://azure.microsoft.com/documentation/articles/application-gateway-webapplicationfirewall-overview/) сторонних производителей, таких как [Barracuda Networks](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf/).

#### <a name="see-also"></a>См. также

-   [Приступая к работе с безопасностью Microsoft Azure](https://azure.microsoft.com/documentation/articles/azure-security-getting-started/).

-   [Рекомендации по обеспечению безопасности за счет управления удостоверениями и контроля доступа Azure](https://azure.microsoft.com/documentation/articles/azure-security-identity-management-best-practices/)

### <a name="application-and-messaging-services"></a>Службы приложений и обмена сообщениями

#### <a name="simple-email-service"></a>Simple Email Service

AWS предоставляет службу Simple Email Service (SES) для отправки уведомлений, а также сообщений электронной почты, касающихся транзакций или маркетинга. В Azure услуги электронной почты предоставляются при помощи решений сторонних производителей, таких как [Sendgrid](https://sendgrid.com/partners/azure/).

#### <a name="simple-queueing-service"></a>Simple Queueing Service

Служба Simple Queueing Service (SQS) в AWS предоставляет систему обмена сообщениями для подключения приложений, служб и устройств в пределах платформы AWS. В Azure есть две службы, которые выполняют аналогичные функции:

-   [Хранилище очередей](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-queues/) — служба обмена сообщениями в облаке, которая обеспечивает обмен данными между компонентами приложения в пределах платформы Azure.

-   [Служебная шина](https://azure.microsoft.com/en-us/services/service-bus/) — более надежная система обмена сообщениями для подключения приложений, служб и устройств. С помощью соответствующего [ретранслятора служебной шины](https://docs.microsoft.com/en-us/azure/service-bus-relay/relay-what-is-it) она также может подключаться к удаленным приложениям и службам.

#### <a name="device-farm"></a>Device Farm

Device Farm в AWS предоставляет возможность тестирования нескольких устройств. В Azure [Xamarin Test Cloud](https://www.xamarin.com/test-cloud) выполняет аналогичную функцию тестирования внешнего интерфейса для нескольких мобильных устройств.

Помимо тестирования внешнего интерфейса, [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) предоставляет ресурсы для тестирования серверной части в средах Linux и Windows.

#### <a name="see-also"></a>См. также

-   [Использование хранилища очередей из Node.js](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-queues/)

-   [Как использовать очереди служебной шины](https://azure.microsoft.com/documentation/articles/service-bus-nodejs-how-to-use-queues/)

### <a name="analytics-and-big-data"></a>Аналитика и большие данные

[Cortana Intelligence Suite](https://azure.microsoft.com/suites/cortana-intelligence-suite/) — пакет продуктов и услуг Azure, предназначенный для отслеживания, упорядочивания, анализа и визуализации больших объемов данных. Набор Cortana состоит из следующих служб:

-   [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) — управляемый дистрибутив Apache, который включает Hadoop, Spark, Storm или HBase.

-   [Фабрика данных](https://azure.microsoft.com/documentation/services/data-factory/) обеспечивает функции оркестрации и конвейера данных.

-   [Хранилище данных SQL](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) — крупномасштабное реляционное хранилище данных.

-   [Data Lake Store](https://azure.microsoft.com/documentation/services/data-lake-store/) — крупномасштабное хранилище, оптимизированное для рабочих нагрузок анализа больших данных.

-   [Машинное обучение](https://azure.microsoft.com/documentation/services/machine-learning/) используется для создания и применения операций прогнозной аналитики данных.

-   [Stream Analytics](https://azure.microsoft.com/documentation/services/stream-analytics/) обеспечивает анализ данных в режиме реального времени.

-   [Data Lake Analytics](https://azure.microsoft.com/documentation/articles/data-lake-analytics-overview/) — крупномасштабная служба аналитики, оптимизированная для работы с Data Lake Store.

-   [Power BI](https://powerbi.microsoft.com/) обеспечивает визуализацию данных.

#### <a name="see-also"></a>См. также

-   [Коллекция Cortana Intelligence](https://gallery.cortanaintelligence.com/).

-   [Understanding Microsoft big data solutions](https://msdn.microsoft.com/library/dn749804.aspx) (Общие сведения о решениях Майкрософт для работы с большими данными).

-   [Блог Azure Data Lake и Azure HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/).

### <a name="internet-of-things"></a>Интернет вещей

#### <a name="see-also"></a>См. также

-   [Приступая к работе с Центром Интернета вещей Azure](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/).

-   [Сравнение Центра Интернета вещей и концентраторов событий](https://azure.microsoft.com/documentation/articles/iot-hub-compare-event-hubs/).

### <a name="mobile-services"></a>Мобильные службы

#### <a name="notifications"></a>Уведомления

Центры уведомлений не поддерживают отправку SMS-сообщений или сообщений электронной почты. Для этих типов доставки требуются службы сторонних производителей.

#### <a name="see-also"></a>См. также

-   [Создание приложения Android](https://azure.microsoft.com/documentation/articles/app-service-mobile-android-get-started/).

-   [Проверка подлинности и авторизация в мобильных приложениях Azure](https://azure.microsoft.com/documentation/articles/app-service-mobile-auth/)

-   [Отправка push-уведомлений с помощью центров уведомлений Azure](https://azure.microsoft.com/documentation/articles/notification-hubs-android-push-notification-google-fcm-get-started/).

### <a name="management-and-monitoring"></a>Управление и мониторинг

#### <a name="see-also"></a>См. также
-   [Руководство по мониторингу и диагностике](https://azure.microsoft.com/documentation/articles/best-practices-monitoring/)

-   [Рекомендации по созданию шаблонов Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-manager-template-best-practices/).

-   [Шаблоны Resource Manager для быстрого начала работы](https://azure.microsoft.com/documentation/templates/).


## <a name="next-steps"></a>Дальнейшие действия

-   [Полная сравнительная таблица для служб AWS и Azure](https://aka.ms/azure4aws-services).

-   [Комплексное интерактивное представление платформы Azure](http://azureplatform.azurewebsites.net/).

-   [Приступая к работе с Azure](https://azure.microsoft.com/get-started/)

-   [Архитектуры решений Azure](https://azure.microsoft.com/solutions/architecture/).

-   [Azure Reference Architectures](https://azure.microsoft.com/documentation/articles/guidance-architecture/) (Эталонная архитектура Azure).

-   [Руководство по шаблонам и методикам Azure](https://azure.microsoft.com/documentation/articles/guidance/).

-   [Бесплатный интернет-курс: Microsoft Azure для специалистов по AWS](http://aka.ms/azureforaws).