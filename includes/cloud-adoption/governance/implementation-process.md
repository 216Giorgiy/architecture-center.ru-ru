<!-- TEMPLATE FILE - DO NOT ADD METADATA -->

## <a name="dependent-decisions"></a>Зависимые решения

Следующие решения поступают от команд, не входящих в группу управления облаком. Реализация каждого из них выполняется теми же командами. Но при этом команда управления облаком отвечает за внедрение решений, позволяющих проверить единообразие этих реализаций.

### <a name="identity-baseline"></a>Основные способы идентификации

Основные способы идентификации служат фундаментальной отправной точкой для всех систем управления. Прежде чем применять систему управления, необходимо создать систему идентификации. Созданная стратегия идентификации будет применяться во всех решениях для управления.
На пути развития систем управления команда управления удостоверениями реализует шаблон **синхронизации службы каталогов**.

- RBAC предоставляется службой Azure Active Directory (Azure AD) на основе синхронизации службы каталогов или функции "Такой же вход", которая была реализована при переходе компании на использование Office 365. Руководство по реализации см. в [описании эталонной архитектуры для интеграции с Azure AD](/azure/architecture/reference-architectures/identity/azure-ad).
- Арендатор Azure AD будет управлять аутентификацией и доступом к ресурсам, развернутым в Azure.

На этапе MVP системы управления группа управления применяет для реплицированного клиента инструментарий управления подписками, который обсуждается [далее в этой статье](#subscription-model). В дальнейшем группа управления может применить еще и широкий набор инструментов в Azure AD, чтобы расширить возможности.

### <a name="security-baseline-networking"></a>Основные способы защиты Сеть

Программно-конфигурируемая сеть является важным начальным аспектом для основных способов защиты. Создание MVP управления зависит от решений команды управления безопасностью на раннем этапе, позволяющих безопасно настраивать сети.

При отсутствии требований команда ИТ-безопасности выбрала самый надежный вариант — шаблон **облачной сети периметра**. Это означает, что управление развертываниями Azure будет минимальным.

- Подписки Azure можно подключить к существующему центру обработки данных через VPN, но для них необходимо соблюдать все существующие в локальной системе ИТ-политики в отношении подключения демилитаризованной зоны к защищенным ресурсам. Руководство по реализации VPN-подключений можно найти в описании [эталонной архитектуры VPN](/azure/architecture/reference-architectures/hybrid-networking/vpn).
- Решения по поводу подсети, брандмауэра и маршрутизации в настоящее время доверяются руководителям, ответственным за конкретное приложение или рабочую нагрузку.
- Перед предоставлением доступа к любым защищенным данным или критически важным рабочим нагрузкам требуется дополнительный анализ.

В таких случаях облачные сети могут подключаться к локальным ресурсам только через предварительно выделенную виртуальную частную сеть, которая совместима с Azure. Трафик, поступающий через это подключение, будет рассматриваться как обычный трафик из демилитаризованной зоны. Возможно, на пограничном устройстве локальной сети потребуются дополнительные настройки для безопасной обработки трафика из Azure.

Команда управления облаком заранее приглашает членов команд управления сетью и ИТ-безопасностью на регулярные собрания, чтобы своевременно реагировать на требования и риски, имеющие отношения к сети.

### <a name="security-baseline-encryption"></a>Основные способы защиты Шифрование

Шифрование — это еще один фундаментальный аспект, требующий решений в дисциплине основных способов защиты. Так как сейчас компания не хранит в облаке защищенные данные, команда безопасности решила выбрать менее строгий алгоритм шифрования.
На этом этапе для шифрования предлагается шаблон **полностью облачного решения**, но он не является обязательным для групп разработчиков.

- Требования системы управления в отношении использования шифрования пока отсутствуют, так как действующая корпоративная политика запрещает размещение критически важных или защищенных данных в облаке.
- Перед предоставлением доступа к любым защищенным данным или критически важным рабочим нагрузкам потребуется дополнительный анализ.

## <a name="policy-enforcement"></a>Принудительное применение политики

Для ускорения развертывания первым делом необходимо выбрать шаблон применения. В нашем примере команда управления решила реализовать шаблон **автоматического применения**.

- Специалистам по безопасности и идентификации будет предоставлен доступ к Центру безопасности Azure для мониторинга угроз безопасности. Также обе команды, скорее всего, будут использовать Центр безопасности для выявления новых рисков и развития корпоративной политики.
- Механизм RBAC является обязательным для всех подписок, чтобы управлять применением аутентификации.
- Служба "Политика Azure" будет опубликована для каждой группы управления и применяться ко всем подпискам. Но при этом уровень применяемых политик в начальной версии MVP управления будет крайне ограниченным.
- Несмотря на применение групп управления Azure иерархия будет относительно простой.
- Azure Blueprints будет использоваться для развертывания и обновления подписок с применением требований RBAC, шаблонов Resource Manager и Политики Azure в группах управления.

## <a name="applying-the-dependent-patterns"></a>Применение зависимые шаблонов

Следующие решения определяют, какие шаблоны будут применяться в описанной выше стратегии применения политики.

**Основные способы идентификации**. Azure Blueprints будет определять требования RBAC на уровне подписки, чтобы удостоверения были единообразно настроены для всех подписок.

**Основные способы защиты: сеть**. Команда управления облаком поддерживает шаблон Resource Manager для создания VPN-шлюза между Azure и локальным VPN-устройством. Когда команде разработки приложения потребуется VPN-подключение, команда управления облаком применит шаблон Resource Manager для шлюза с помощью Azure Blueprints.

**Основные способы защиты: шифрование**. На этом этапе применение политики в этой области не требуется. К этому вопросу мы вернемся на следующих этапах развития.
