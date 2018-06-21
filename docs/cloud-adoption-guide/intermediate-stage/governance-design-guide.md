---
title: Руководство по проектированию системы управления. Новая разработка в Azure для нескольких команд
description: Руководство по настройке элементов управления Azure для нескольких команд, нескольких рабочих нагрузок и нескольких сред
author: petertay
ms.openlocfilehash: 05f1f9bb24af4f4da55b15c1aca2c71bc0b65411
ms.sourcegitcommit: b3d74d8a89b2224fc796ce0e89cea447af43a0d4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35291310"
---
# <a name="governance-design-guide-new-development-in-azure-for-multiple-teams"></a>Руководство по проектированию системы управления. Новая разработка в Azure для нескольких команд

Цель этого руководства — помочь изучить процесс разработки модели управления ресурсами в Azure.  Мы рассмотрим набор гипотетических требований к управлению, а затем рассмотрим несколько примеров реализации, которые отвечают этим требованиям. 

Для этого необходимы следующие компоненты.
* Управление удостоверениями для нескольких команд с требованиями доступа к разным ресурсам в Azure. Система управления удостоверениями хранит идентификаторы следующих пользователей.
  1. Ответственного за владение **подписками**.
  2. Ответственного за **ресурсы общей инфраструктуры**, которая используется для подключения локальной сети организации к виртуальной сети Azure. 
  3. Двое ответственных за управление **рабочей нагрузкой** организации. 
* Поддержка нескольких **сред**. Как было показано ранее, среда представляет собой логическую группировку ресурсов с аналогичными требованиями к управлению и безопасности. Нам необходимы три среды.
  1. **Среда общей инфраструктуры**, которая включает общие ресурсы, разделяемые рабочими нагрузками в других средах. Например, виртуальная сеть с подсетью шлюза, обеспечивающая подключение к локальной сети.
  2. **Рабочая среда** с наиболее строгими политиками безопасности. Может включать внутренние или внешние рабочие нагрузки.
  3. **Среда разработки** для подтверждения концепции и тестирования. Эта среда имеет политики безопасности, соответствий и затрат, которые отличаются от политик производственной среды.
* **Модель разрешений с наименьшими привилегиями**, при которой пользователи не имеют разрешений по умолчанию. Модель должна поддерживать следующее.
  * Единственный доверенный пользователь в области подписки с разрешением назначать права доступа к ресурсам.
  * Каждому владельцу рабочей нагрузки по умолчанию запрещен доступ к ресурсам. Единственный доверенный пользователь в области подписки предоставляет права доступа к ресурсам явным образом.
  * Доступ к управлению для ресурсов общей инфраструктуры ограничен владельцем общей инфраструктуры.  
  * Доступ к управлению для каждой рабочей нагрузки ограничен владельцем рабочей нагрузки.
  * Использование только [встроенных ролей RBAC][rbac-built-in-roles]. Отсутствие пользовательских ролей RBAC.
* Отслеживание затрат по имени владельца рабочей среды, по среде или по обоим. 

## <a name="identity-management"></a>Управление удостоверениями

Прежде чем разрабатывать управление удостоверениями для нашей модели системы управления, важно понять четыре основные области, которые она охватывает.

* Администрирование: процессы и средства для создания, редактирования и удаления идентификатора пользователя.
* Аутентификация: проверка подлинности пользователя путем проверки учетных данных, таких как имя пользователя и пароль.
* Авторизация: определение того, к каким ресурсам разрешен доступ аутентифицированному пользователю или на выполнение каких операций у него есть разрешение.
* Аудит: периодический просмотр журналов и другой информации для обнаружения проблем безопасности, связанных с идентификацией пользователя. Он включает проверку подозрительных шаблонов использования, периодический просмотр разрешений пользователей для проверки их точности и другие функции.

Существует только одна доверенная служба от Azure для идентификации, и это Azure Active Directory (AD). Мы будем добавлять пользователей в Microsoft Azure Active Directory и использовать их для всех перечисленных выше функций. Но прежде чем посмотреть на настройку Microsoft Azure Active Directory, важно понять суть привилегированных учетных записей, которые используются для управления доступом к этим службам.

При регистрации для организации учетной записи Azure был назначен как минимум один **владелец учетной записи** Azure и был создан **клиент** Microsoft Azure Active Directory в случае, если ранее не было клиента Microsoft Azure Active Directory, связанного с использованием организацией других служб Microsoft, таких как Office 365. Microsoft Azure Active Directory**Глобальный администратор** с полными правами при создании был связан с клиентом Microsoft Azure Active Directory. 

Идентификаторы пользователей, как владельцев учетных записей Azure, так и глобальных администраторов Microsoft Azure Active Directory, хранятся в надежной системе идентификации, управляемой корпорацией Майкрософт. Владелец учетной записи Azure имеет право создавать, обновлять и удалять подписки. Глобальный администратор Microsoft Azure Active Directory уполномочен выполнять множество действий в Microsoft Azure Active Directory, но в этом руководстве по проектированию рассматривается только создание и удаление идентификатора пользователя. 

> [!NOTE]
> Если имеется существующая лицензия Office 365 или Intune, связанная с учетной записью организации, то возможно уже есть существующий клиент Microsoft Azure Active Directory.

У владельца учетной записи Azure есть разрешение на создание, обновление и удаление подписок. 

![Учетная запись Azure с менеджером учетной записи и глобальным администратором Microsoft Azure Active Directory](../_images/governance-3-0.png)
*Рис. 1. Учетная запись Azure с менеджером учетной записи и глобальным администратором Microsoft Azure Active Directory.*

**Глобальный администратор** Microsoft Azure Active Directory имеет разрешение на создание учетных записей пользователей.  

![Учетная запись Azure с менеджером учетной записи и глобальным администратором Microsoft Azure Active Directory](../_images/governance-3-0a.png)
*Рис. 2. Глобальный администратор Microsoft Azure Active Directory создает необходимые учетные записи в клиенте.*

Первые две учетные записи, **владельца рабочей нагрузки App1** и **владельца рабочей нагрузки App2**, связанные с определенными лицами организации, ответственными за управление рабочей нагрузкой. Учетная запись **пользователя сетевых операций** принадлежит лицу, ответственному за ресурсы общей инфраструктуры. Наконец, учетная запись **владельца подписок** связана с лицом, ответственным за право собственности на подписки.

## <a name="resource-access-permissions-model-of-least-privilege"></a>Модель разрешений доступа с наименьшими привилегиями

Теперь, когда у нас создана система управления удостоверениями и учетные записи пользователей, необходимо решить, как применять роли на основе ролей управления доступом (RBAC) к каждой учетной записи для поддержки модели разрешений с наименьшими привилегиями.

Другое требование состоит в том, чтобы ресурсы, связанные с каждой рабочей нагрузкой, были изолированы друг от друга, так чтобы ни один владелец рабочей нагрузки не имел управления доступом к любой другой рабочей нагрузке, которой он не владеет. У нас есть требование реализовать эту модель, используя только [встроенные роли Azure RBAC][rbac-built-in-roles].

Каждая роль RBAC применяется в одной из трех областей в Azure: **подписка**, **группа ресурсов** и отдельный **ресурс**. Роли наследуются в более низких областях. Например, если пользователю назначена [встроенная роль владельца][rbac-built-in-owner] на уровне подписки, то эта роль, если только она не переопределена, также назначается этому пользователю в группе ресурсов и на уровне отдельного ресурса.

Поэтому, чтобы создать модель доступа с наименьшими привилегиями, необходимо разрешить действия, которые может предпринимать конкретный тип пользователя в каждой из этих трех областей. Например, наше требование состоит в том, чтобы владелец рабочей нагрузки имел разрешение управлять доступом только к ресурсам, связанным с его рабочей нагрузкой, и никаким другим. Если назначить [встроенную роль владельца][rbac-built-in-owner] в области подписки, то каждый владелец рабочей нагрузки будет иметь доступ к управлению всеми рабочими нагрузками.

Давайте рассмотрим два примера моделей разрешений, чтобы лучше понять эту концепцию. В первом примере наша модель позволяет только администратору служб создавать группы ресурсов. Во втором примере наша модель назначает встроенную роль владельца каждому владельцу рабочей нагрузки в области подписки. 

В обоих примерах у нас есть администратор служб подписки, которому назначена [встроенная роль владельца][rbac-built-in-owner] в области подписки. Напомним, что [встроенная роль владельца][rbac-built-in-owner] предоставляет все разрешения, включая управление доступом к ресурсам.
![администратор служб подписки с ролью владельца](../_images/governance-2-1.png)
*Рис. 3. Подписка с администратором служб назначила встроенную роль владельца.* 

1. В первом примере имеем **владельца рабочей нагрузки A** без разрешений в области подписки, по умолчанию он не имеет прав управления доступом к ресурсам. Этот пользователь хочет развернуть ресурсы для своей рабочей нагрузки и управлять ими. Ему необходимо обратиться к **администратору служб** с запросом о создании группы ресурсов.
![запрос владельца рабочей нагрузки о создании группы ресурсов A](../_images/governance-2-2.png)  

2. **Администратор служб** рассматривает его запрос и создает **группу ресурсов A**. На этом этапе **владелец рабочей нагрузки A** по-прежнему не имеет никаких разрешений.
![администратор служб создает группу ресурсов А](../_images/governance-2-3.png)

3. **Администратор служб** добавляет **владельца рабочей нагрузки A** к **группе ресурсов A** и назначает [встроенную роль участника](/azure/role-based-access-control/built-in-roles#contributor). Роль участника предоставляет все разрешения в **группе ресурсов A**, за исключением разрешения на доступ к управлению.
![администратор служб добавляет владельца рабочей нагрузки к группе ресурсов А](../_images/governance-2-4.png)

4. Предположим, что **владельцу рабочей нагрузки A** необходима пара членов команды для просмотра данных мониторинга ЦП и сетевого трафика в рамках планирования мощности для рабочей нагрузки. Поскольку **владельцу рабочей нагрузки A** назначена роль участника, у него нет разрешения на добавление пользователей к **группе ресурсов A**. Необходимо отправить этот запрос **администратору служб**.
![владелец рабочей нагрузки запрашивает добавление участников рабочей нагрузки к группе ресурсов](../_images/governance-2-5.png)

5. **Администратор служб** рассматривает запрос и добавляет двух пользователей, **участников рабочей нагрузки**, к **группе ресурсов А**. Ни одному из этих двух пользователей не требуется разрешение для управления ресурсами, поэтому им назначаются [встроенные роли читателей](/azure/role-based-access-control/built-in-roles#contributor). 
![администратор служб добавляет участников рабочей нагрузки к группе ресурсов А](../_images/governance-2-6.png)

6. Далее, **владелец рабочей нагрузки Б** также требует группу ресурсов для ресурсов своей рабочей нагрузки. Как и в случае с **владельцем рабочей нагрузки A**, **владелец рабочей нагрузки Б** изначально не имеет разрешения принимать какие-либо действия в области подписки, поэтому ему необходимо отправить запрос **администратору служб** 
![владелец рабочей нагрузки Б запрашивает создание группы ресурсов Б](../_images/governance-2-7.png)

7. **Администратор служб** рассматривает его запрос и создает **группу ресурсов Б**. ![администратор служб создает группу ресурсов Б](../_images/governance-2-8.png)

8. **Администратор служб** добавляет **владельца рабочей нагрузки Б** к **группе ресурсов Б** и назначает [встроенную роль участника](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#contributor). 
![Администратор служб добавляет владельца рабочей нагрузки Б к группе ресурсов Б](../_images/governance-2-9.png)

На этом этапе каждый владелец рабочей нагрузки изолирован в своей собственной группе ресурсов. Ни один из владельцев рабочей нагрузки или члены их команд не имеют доступа к управлению ресурсами в любой другой группе ресурсов. 

![подписка с группами ресурсов A и Б](../_images/governance-2-10.png)
*Рис. 4. Подписка с двумя владельцами рабочей нагрузки, изолированными в собственных группах ресурсов.*

Эта модель является моделью с наименьшими привилегиями — каждому пользователю назначается соответствующее разрешение в соответствующей области управления ресурсами.

Однако учтите, что каждая задача в этом примере выполнялась **администратором служб**. Это простой пример, и здесь не возникают проблемы, возможно потому, что есть только два владельца рабочей нагрузки. Но легко представить себе типы проблем, которые могут возникнуть в большой организации. Например, **администратор служб** может стать узким местом с большим количеством невыполненных запросов, которые приводят к задержкам.  

Давайте рассмотрим второй пример, который уменьшает количество задач, выполняемых **администратором служб**. 

1. В этой модели **владельцу рабочей нагрузки A** присваивается [встроенная роль владельца][rbac-built-in-owner] в области подписки, что позволяет ему создавать свою собственную группу ресурсов: **группа ресурсов A**. ![Администратор служб добавляет владельца рабочей нагрузки А к подписке](../_images/governance-2-11.png)

2. Когда создается **группа ресурсов A**, по умолчанию добавляется **владелец рабочей нагрузки A** и наследует [встроенную роль владельца][rbac-built-in-owner] из области подписки.
![Владелец рабочей нагрузки А создает группу ресурсов А](../_images/governance-2-12.png)

3. [Роль встроенного владельца][rbac-built-in-owner] предоставляет **владельцу рабочей нагрузки A** разрешение на управление доступом к группе ресурсов. **Владелец рабочей нагрузки A** добавляет двух **участников рабочей нагрузки** и назначает каждому из них [встроенную роль читателя][rbac-built-in-owner]. 
![Владелец рабочей нагрузки A добавляет участников рабочей нагрузки](../_images/governance-2-13.png)

4. **Администратор служб** теперь добавляет **владельца рабочей нагрузки Б** к подписке с встроенной ролью владельца. 
![Администратор служб добавляет владельца рабочей нагрузки Б к подписке](../_images/governance-2-14.png)

5. **Владелец рабочей нагрузки Б** создает **группы ресурсов Б** и добавляется по умолчанию. Опять же, **владелец рабочей нагрузки Б** наследует встроенную роль владельца из области подписки.
![Владелец рабочей нагрузки Б создает группу ресурсов Б](../_images/governance-2-15.png)

Обратите внимание, что в этой модели **администратор служб** выполнял меньше действий, чем в первом примере, путем делегирования управления доступом каждому из отдельных владельцев рабочей нагрузки.

![подписка с группами ресурсов A и Б](../_images/governance-2-16.png)
*Рис. 5. Подписка с администратором службы и двумя владельцами рабочей нагрузки, всем назначены встроенные роли владельца.*

Тем не менее, поскольку **владельцу рабочей нагрузки A** и **владельцу рабочей нагрузки Б** присваивается встроенная роль владельца в области подписки, каждая из них унаследовала роль встроенного владельца групп ресурсов друг друга. Это означает, что они не только имеют полный доступ к ресурсам друг друга, но также могут делегировать доступ управления к группам ресурсов друг друга. Например, владелец **рабочей нагрузки Б** имеет разрешение добавлять любого другого пользователя в **группу ресурсов A** и может назначать ему какую-либо роль, включая встроенную роль владельца.

Если сравнивать примеры с нашими требованиями, видно, что оба примера поддерживают одного доверенного пользователя в области подписки с разрешением на предоставление прав доступа к ресурсам двум владельцам рабочей нагрузки. Каждый из двух владельцев рабочей нагрузки не имел доступа к управлению ресурсами по умолчанию и требовал, чтобы **администратор служб** явно назначил ему права. Однако только первый пример поддерживает требование, чтобы ресурсы, связанные с отдельной рабочей нагрузкой, были изолированы друг от друга таким образом, что владелец рабочей нагрузки не имеет доступа к ресурсам любой другой рабочей нагрузки.

## <a name="resource-management-model"></a>Модель управления ресурсами

Теперь, когда модель разрешений с наименьшими привилегиями разработана, давайте перейдем к рассмотрению некоторых практических приложений для этих моделей системы управления. Напомним, что нам необходимо поддерживать следующие три среды.
1. **Общая инфраструктура:** единая группа ресурсов, общая для всех рабочих нагрузок. Это такие ресурсы, как сетевые шлюзы, брандмауэры и службы безопасности.  
2. **Разработка:** несколько групп ресурсов, представляющих несколько нерабочих готовых рабочих нагрузок. Эти ресурсы используются для подтверждения концепции, тестирования и других действий разработчиков. Эти ресурсы могут иметь менее жесткую модель системы управления, позволяющую увеличить маневренность разработчика.
3. **Рабочая среда:** несколько групп ресурсов, представляющих несколько рабочих нагрузок. Эти ресурсы используются для размещения частных и публичных артефактов приложений. Эти ресурсы обычно имеют самые жесткие модели системы управления и безопасности для защиты ресурсов, кода приложений и данных от несанкционированного доступа.

Для каждой из этих трех сред есть требования по отслеживанию данных о расходах **владельца рабочей нагрузки**, **среды** или обоих. То есть необходимо знать текущие затраты **общей инфраструктуры**, затраты, вызванные отдельными лицами в среде **разработки** и **рабочей** среде, и, наконец, общую стоимость **разработки** и **производства**. 

Вы уже знаете, что ресурсы ограничиваются только двумя уровнями: **подпиской** и **группой ресурсов**. Поэтому первое наше решение — организовать среду с использованием **подписки**. Есть только две возможности: единая подписка или несколько подписок. 

Прежде чем перейти к примерам каждой из этих моделей, давайте рассмотрим структуру управления подписками в Azure. 

Следует напомнить, что в организации есть лицо, которое отвечает за подписки, и этот пользователь владеет учетной записью **владельца подписок** в клиенте Microsoft Azure Active Directory. Однако эта учетная запись не имеет разрешения на создание подписок. Только **владелец учетной записи Azure** имеет на это право. ![](../_images/governance-3-0b.png)
*Рис. 6. Владелец учетной записи Azure создает подписку.*

После создания подписки **владелец учетной записи Azure** может добавить учетную запись **владельца подписки** к подписке с ролью **владельца**.

![](../_images/governance-3-0c.png)
*Рис. 7. Владелец учетной записи Azure добавляет учетную запись пользователя **владельца подписки** к подписке с ролью **владельца**.*

**Владелец подписки** теперь может создать **группу ресурсов** и делегировать управление доступом к ресурсам.

Сначала давайте рассмотрим пример модели управления ресурсами, используя единую подписку. Наше первое решение касается выравнивания групп ресурсов в трех средах. Это можно сделать двумя способами.
1. Совместите каждую среду с одной группой ресурсов. Все ресурсы общей инфраструктуры развертываются в единую группу ресурсов **общей инфраструктуры**. Все ресурсы, связанные с рабочими нагрузками разработки, развертываются в единую группу ресурсов **разработки**. Все ресурсы, связанные с производственными рабочими нагрузками, развертываются в единую **производственную** группу ресурсов для **рабочей** среды. 
2. Выровняйте рабочие нагрузки в отдельной группе ресурсов, используя соглашение об именовании и теги для выравнивания групп ресурсов в каждой из трех сред.  

Начнем с оценки первого способа. Воспользуемся моделью разрешений, о которой шла речь в предыдущем разделе, с администратором служб единой подписки, который создает группы ресурсов и добавляет к ним пользователей с помощью встроенной роли **участника** или **читателя**. 

1. Первая развернутая группа ресурсов представляет собой среду **общей инфраструктуры**. **Владелец подписки** создает группу ресурсов для ресурсов общей инфраструктуры с именем **netops-shared-rg**. 
![](../_images/governance-3-0d.png)
2. **Владелец подписки** добавляет учетную запись **пользователя сетевых операций** для группы ресурсов и назначает роль **участника**. 
![](../_images/governance-3-0e.png)
3. **Пользователь сетевых операций** создает [VPN-шлюз](/azure/vpn-gateway/vpn-gateway-about-vpngateways) и настраивает его для подключения к VPN-устройству в локальной среде. Пользователь **сетевых операций** также применяет пару [тегов](/azure/azure-resource-manager/resource-group-using-tags) для каждого из ресурсов: *environment:shared* и *managedBy:netOps*. Когда **администратор служб подписки** экспортирует отчет о затратах, затраты будут выровнены по каждому из этих тегов. Это позволяет **администратору служб подписки** сводить затраты с помощью тега *environment* и тега *managedBy*. Обратите внимание на счетчик **границ ресурсов** в верхней правой части рисунка. Каждая подписка Azure имеет [границы обслуживания](/azure/azure-subscription-service-limits), и чтобы понять эффект этих ограничений, будем придерживаться границ виртуальной сети для каждой подписки. По умолчанию ограничение составляет 50 виртуальных сетей на подписку, а после развертывания первой виртуальной сети остаются доступными 49 виртуальных сетей.
![](../_images/governance-3-1.png)
4. Были развернуты еще две группы ресурсов, первая называется *prod-rg*. Эта группа ресурсов выровнена с **рабочей** средой. Вторая называется *dev-rg* и выровнена со средой **разработки**. Все ресурсы, связанные с производственными рабочими нагрузками, развертываются в **рабочей** среде, а все ресурсы, связанные с рабочими нагрузками разработки, развертываются в среде **разработки**. В этом примере показано, как развернуть только две рабочие нагрузки для каждой из этих двух сред, чтобы не превысить пределы абонентской подписки Azure. Однако важно учитывать, что каждая группа ресурсов имеет ограничение в 800 ресурсов. Поэтому, если добавлять рабочие нагрузки для каждой группы ресурсов, возможно, что этот предел будет достигнут. 
![](../_images/governance-3-2.png)
5. Первый **владелец рабочей нагрузки** отправляет запрос **администратору служб подписки** и добавляется к каждой из сред групп ресурсов (к среде **разработки** и к **рабочей** среде) с ролью **участника**. Как упоминалось ранее, роль **участника** позволяет пользователю выполнять любую операцию, отличную от назначения роли другому пользователю. Первый **владелец рабочей нагрузки** теперь может создавать ресурсы, связанные со своей рабочей нагрузкой.
![](../_images/governance-3-3.png)
6. Первый **владелец рабочей нагрузки** создает виртуальную сеть в каждой из двух групп ресурсов с парой виртуальных машин в каждой. Первый **владелец рабочей нагрузки** применяет теги *environment* и *managedBy* ко всем ресурсам. Обратите внимание, что счетчик границ обслуживания Azure теперь показывает оставшиеся 47 виртуальных сетей.
![](../_images/governance-3-4.png)
7. При создании каждой виртуальной сети нет возможности подключения к локальным сетям. В этом типе архитектуры каждая виртуальная сеть должна быть привязана к *hub-vnet* в среде **общей инфраструктуры**. Пиринг виртуальной сети создает соединение между двумя отдельными виртуальными сетями и позволяет сетевому трафику перемещаться между ними. Обратите внимание, что пиринг виртуальной сети не является по своей сути транзитивным. В каждой из двух подключенных виртуальных сетей должен быть указан пиринг, и если только в одной из виртуальных сетей указан пиринг, соединение является неполным. Чтобы проиллюстрировать этот эффект, пусть первый **владелец рабочей нагрузки** укажет пиринг между **prod-vnet** и **hub-vnet**. Создается первый пиринг, но нет потоков трафика, потому что еще не был указан взаимодополняющий пиринг из **hub-vnet** к **prod-vnet**. Первый **владелец рабочей нагрузки** связывается с пользователем **сетевых операций** и запрашивает это взаимодополняющее пиринговое подключение.
![](../_images/governance-3-5.png)
8. Пользователь **сетевых операций** просматривает запрос, утверждает его, а затем указывает пиринг в параметрах **hub-vnet**. Теперь пиринговое соединение полное и сетевой трафик проходит между двумя виртуальными сетями.
![](../_images/governance-3-6.png)
9. Теперь второй **владелец рабочей нагрузки** отправляет запрос **администратору служб подписки** и добавляется к каждой из существующих сред групп ресурсов (к среде **разработки** и к **рабочей** среде) с ролью **участника**. Второй **владелец рабочей нагрузки** имеет те же разрешения, что и первый **владелец рабочей нагрузки**, для всех ресурсов в каждой группе ресурсов. 
![](../_images/governance-3-7.png)
10. Второй **владелец рабочей нагрузки** создает подсеть в виртуальной сети **prod-vnet**, а затем добавляет две виртуальные машины. Второй **владелец рабочей нагрузки** применяет теги *environment* и *managedBy* для каждого ресурса.
![](../_images/governance-3-8.png) 

Этот пример модели управления ресурсами позволяет нам управлять ресурсами в трех требуемых средах. Наши ресурсы общей инфраструктуры защищены, поскольку в подписке есть только один пользователь с правом доступа к этим ресурсам. Каждый из владельцев рабочей нагрузки может использовать ресурсы общей инфраструктуры не имея каких-либо разрешений на действующие общие ресурсы. Эта модель управления не отвечает требованиям по изоляции рабочей нагрузки — каждый из двух **владельцев рабочей нагрузки** может получить доступ к ресурсам рабочей нагрузки другого пользователя. 

Есть еще одно важное замечание к этой модели, которое может быть не сразу очевидно. В примере был зарегистрирован **владелец рабочей нагрузки аpp1**, который запросил сетевое пиринговое соединение с **hub-vnet**, чтобы обеспечить возможность подключения к локальной сети. Пользователь **сетевых операций** оценил этот запрос на основании ресурсов, развертываемых с этой рабочей нагрузкой. Когда **владелец подписки** добавил **владельца рабочей нагрузки app2** с ролью **участника**, у этого пользователя были права доступа к управлению всеми ресурсами в группе ресурсов **prod-rg**. 

![](../_images/governance-3-10.png)

Это значит, что **владелец рабочей нагрузки app2** имел разрешение на развертывание своей собственной подсети с виртуальными машинами в виртуальной сети **prod-vnet**. По умолчанию эти виртуальные машины теперь имеют доступ к локальной сети. Пользователь **сетевых операций** не имеет сведений о компьютерах и не утвердил их подключение к локальной сети.

Затем давайте рассмотрим единую подписку с несколькими группами ресурсов для разных сред и рабочих нагрузок. Обратите внимание, что в предыдущем примере ресурсы для каждой среды были легко идентифицируемы, поскольку они находились в одной группе ресурсов. Теперь, когда больше нет этой группировки, для обеспечения этой функциональности придется полагаться на соглашение об именовании групп ресурсов. 

1. Ресурсы нашей **общей инфраструктуры** в этой модели все еще имеют отдельную группу ресурсов, поэтому она останется прежней. Для каждой рабочей нагрузки требуются две группы ресурсов — по одной для среды **разработки** и для **рабочей** среды. Для первой рабочей нагрузки **владелец подписки** создает две группы ресурсов. Первая называется **app1-prod-rg**, а вторая — **app1-dev-rg**. Как обсуждалось ранее, это соглашение об именовании идентифицирует ресурсы как связанные с первой рабочей нагрузкой **app1** и либо со средой **dev**, либо со средой **prod**. Опять же, владелец *подписки* добавляет **владельца рабочей нагрузки аpp1** в группу ресурсов с ролью **участника**.
![](../_images/governance-3-12.png)
2. Как и в первом примере **владелец рабочей нагрузки аpp1** развертывает виртуальную сеть с именем **app1-prod-vnet** в **рабочей** среде, а другой — виртуальную сеть **app1- dev-vnet** в среде **разработки**. Опять же, **владелец рабочей нагрузки аpp1** отправляет запрос пользователю **сетевых операций** для создания пирингового соединения. Обратите внимание, что **владелец рабочей нагрузки аpp1** добавляет те же теги, что и в первом примере, а счетчик границ был уменьшен до 47 виртуальных сетей, оставшихся в подписке.
![](../_images/governance-3-13.png)
3. **Владелец подписки** теперь создает две группы ресурсов для **владельца рабочей нагрузки app2**. Следуя тем же соглашениям, что и для **владельца рабочей нагрузки app1**, группы ресурсов называются **app2-prod-rg** и **app2-dev-rg**. **Владелец подписки** добавляет **владельца рабочей нагрузки app2** к каждой из групп ресурсов с ролью **участника**.
![](../_images/governance-3-14.png)
4. *Владелец рабочей нагрузки App2* развертывает виртуальные сети и виртуальные машины в группе ресурсов с теми же соглашениями об именовании. Добавляются теги, а счетчик границ уменьшается до 45 виртуальных сетей, остающихся в *подписке*.
![](../_images/governance-3-15.png)
5. *Владелец рабочей нагрузки App2* отправляет запрос пользователю *сетевых операций* для установки кэширующего узла *app2-prod-vnet* с *hub-vnet*. Пользователь *сетевых операций* создает пиринговое соединение.
![](../_images/governance-3-16.png)

Полученная модель управления похожа на первый пример с несколькими ключевыми отличиями.
* Каждая из двух рабочих нагрузок является изолированной рабочей нагрузкой и средой.
* Этой модели требуются две дополнительные виртуальные сети, по сравнению с первым примером модели. Теоретический предел числа рабочих нагрузок для этой модели равен 24, хотя это не является важным отличием для модели только с двумя рабочими нагрузками. 
* Ресурсы больше не группируются в одну группу ресурсов для каждой среды. Для группирования ресурсов требуется понимание соглашений об именовании, используемых для каждой среды. 
* Каждое из подключенных виртуальных сетевых соединений было рассмотрено и одобрено пользователем *сетевых операций*.

Теперь давайте рассмотрим модель управления ресурсами, использующую несколько подписок. В этой модели мы выровняем каждую из трех сред с отдельной подпиской: подписка **общих служб**, **рабочая** подписка и, наконец, подписка **разработки**. Соображения для этой модели аналогичны модели, использующей единую подписку, в которой необходимо решить, как уровнять группы ресурсов с рабочими нагрузками. Уже определено, что создание группы ресурсов для каждой рабочей нагрузки удовлетворяет требованию изоляции рабочей нагрузки, поэтому в примере будем придерживаться этой модели.

1. В этой модели существуют три *подписки*: *общая инфраструктура*, *рабочая* и *разработка*. Для каждой из этих трех подписок требуется *владелец подписки*, в нашем простом примере будем использовать ту же учетную запись для всех трех подписок. Ресурсы *общей инфраструктуры* управляются аналогично первым двум приведенным выше примерам, а первая рабочая нагрузка связана с *app1-rg* в *рабочей* среде и с так же называемой группой ресурсов в среде *разработки*. *Владелец рабочей нагрузки аpp1* добавляется в каждую группу ресурсов с ролью *участника*. 
![](../_images/governance-3-17.png)
2. Как и в предыдущих примерах, *владелец рабочей нагрузки аpp1* создает ресурсы и запрашивает пиринговое подключение с виртуальной сетью *общей инфраструктуры*. *Владелец рабочей нагрузки App1* добавляет только тег *managedBy*, так как тег *environment* больше не требуется. То есть, ресурсы для каждой среды теперь сгруппированы в одну и ту же *подписку*, а тэг *environment* является избыточным. Счетчик ограничений уменьшается до 49 виртуальных сетей.
![](../_images/governance-3-18.png)
3. Наконец *владелец подписки* повторяет процесс для второй рабочей нагрузки, добавляя группы ресурсов *владельцу рабочей нагрузки аpp2* в *роли участника. Счетчик ограничений для каждой подписки на среду уменьшается до 48 виртуальных сетей. 

Эта модель управления имеет преимущества по сравнению со вторым примером. Однако ключевое различие заключается в том, что границы являются менее значимыми так как они распределены по двум *подпискам*. Недостатком является то, что данные по затратам, отслеживаемые с помощью тегов, должны быть агрегированы по всем трем *подпискам*. 

Таким образом можно выбрать любой из этих двух примеров моделей управления ресурсами в зависимости от приоритета требований. Если предполагается, что ваша организация не достигнет ограничения для служб для одной подписки, можно использовать одну подписку с несколькими группами ресурсов. И наоборот — если ваша организация планирует использовать много рабочих нагрузок, несколько подписок для каждой среды будет лучшим решением.

<!-- ## Summary



## Next steps -->


<!-- links -->

[rbac-built-in-owner]: /azure/role-based-access-control/built-in-roles#owner
[rbac-built-in-roles]: /azure/role-based-access-control/built-in-roles