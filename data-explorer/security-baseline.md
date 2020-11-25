---
title: Базовый план безопасности Azure для Azure обозреватель данных
description: Базовый план безопасности Azure обозреватель данных предоставляет практические рекомендации и ресурсы для реализации рекомендаций по безопасности, указанных в статье о производительности системы безопасности Azure.
author: msmbaldwin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d641714607bd1843e46ac708a9302c8a1dad6782
ms.sourcegitcommit: cffc81de2b5c75a0ef5a3c71ff58d1ef52d4eb5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95872334"
---
# <a name="azure-security-baseline-for-azure-data-explorer"></a>Базовый план безопасности Azure для Azure обозреватель данных

Этот базовый план безопасности применяет рекомендации из [тестового показателя безопасности Azure версии 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview-v1) к Azure обозреватель данных. Azure Security Benchmark содержит рекомендации по обеспечению безопасности облачных решений в Azure.
Содержимое группируются по **элементам управления безопасностью** , определенным в средстве оценки безопасности Azure, и связанным рекомендациям, применимым к Azure обозреватель данных. **Элементы управления** , неприменимые к Azure обозреватель данных, были исключены.

 
Сведения о том, как Azure обозреватель данных полностью сопоставляется с тестовым уровнем безопасности Azure, см. в [полном файле сопоставления базовых показателей безопасности azure обозреватель данных](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Безопасность сети

*Дополнительные сведения см. в статье о [производительности системы безопасности Azure: безопасность сети](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: защита ресурсов Azure в виртуальных сетях

**Руководство**. Azure обозреватель данных поддерживает развертывание кластера в подсети в виртуальной сети. Эта возможность позволяет применять правила группы безопасности сети (NSG) к трафику кластера Azure обозреватель данных, подключать локальную сеть к подсети кластера Azure обозреватель данных и защищать источники подключения к данным (концентраторы событий и службы "Сетка событий") с конечными точками служб.

- [Создание кластера в виртуальной сети](https://docs.microsoft.com/azure/data-explorer/vnet-create-cluster-portal)

- [Развертывание кластера обозреватель данных Azure в виртуальной сети](https://docs.microsoft.com/azure/data-explorer/vnet-deployment)

- [Устранение неполадок при создании, подключении и работе кластера виртуальной сети](https://docs.microsoft.com/azure/data-explorer/vnet-deploy-troubleshoot?tabs=windows)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2. Мониторинг и запись конфигурации и трафика виртуальных сетей, подсетей и сетевых интерфейсов

**Руководство**. Включение журналов потоков для группы безопасности сети (NSG) и отправка журналов в учетную запись хранения для аудита трафика.

- [Как включить журналы потоков NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Общие сведения о безопасности сети, предоставляемой центром безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: запретите обмен данными с известными вредоносными IP-адресами

**Руководство**. Включите стандарт защиты Azure от атак DDoS для виртуальной сети, который защищает кластеры Azure обозреватель данных для защиты от атак от атак DDoS. Используйте интегрированную аналитику угроз Центра безопасности Azure, чтобы запретить обмен данными с известными вредоносными или неиспользуемыми IP-адресами Интернета.

- [Настройка защиты от атак DDoS](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Общие сведения об интегрированной аналитике угроз в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="15-record-network-packets"></a>1,5: запись сетевых пакетов

**Руководство**. Включение журналов потоков в группах безопасности сети (NSG), используемых для защиты кластера Azure обозреватель данных, и отправка журналов в учетную запись хранения для аудита трафика.

- [Как включить журналы потоков NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8. Уменьшение сложности и дополнительных затрат на администрирование в правилах безопасности сети

**Руководство**. Использование тегов службы виртуальной сети для определения элементов управления доступом к сети для групп безопасности сети или брандмауэров Azure, связанных с кластерами Azure обозреватель данных. Теги служб можно использовать вместо определенных IP-адресов при создании правил безопасности. Указав имя тега службы (например, ApiManagement) в соответствующем исходном поле или поле назначения правила, можно разрешить или запретить трафик для соответствующей службы. Корпорация Майкрософт управляет префиксами адресов, входящих в тег службы, и автоматически обновляет этот тег при изменении адресов.

- [Основные сведения о тегах служб и их использование](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Требования к конфигурации тегов служб для Azure обозреватель данных](https://docs.microsoft.com/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9. Поддержание стандартных конфигураций безопасности для сетевых устройств

**Руководство**. Клиент определяет и реализует стандартные конфигурации безопасности для сетевых ресурсов с помощью политики Azure.

Кроме того, клиенты могут использовать схемы Azure для упрощения крупномасштабных развертываний Azure с помощью ключевых артефактов среды пакетов, таких как шаблоны Azure Resource Manager, элементы управления RBAC в Azure и назначения политик Azure, в одном определении схемы. Простое применение схемы к новым подпискам и средам, а также управление точной настройкой и управление с помощью версионирования.

- [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Создание схемы Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10. Документация по правилам конфигурации трафика

**Руководство**. Использование тегов для групп безопасности сети (NSG) и других ресурсов, относящихся к сетевой безопасности и потоку трафика для кластеров Azure обозреватель данных. Для отдельных правил NSG используйте поле "Описание", чтобы указать бизнес-потребности и длительность (и т. д.) любых правил, которые разрешают трафик в сеть и из нее.

- [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Использование автоматизированных средств для мониторинга конфигураций сетевых ресурсов и обнаружения изменений

**Руководство**. Использование политики Azure для проверки (и/или исправьте) конфигурации сетевых ресурсов.

- [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

*Дополнительные сведения см. в статье [производительность системы безопасности Azure: ведение журнала и мониторинг](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="22-configure-central-security-log-management"></a>2.2. Настройка централизованного управления журналами безопасности

**Руководство**. Azure обозреватель данных использует журналы диагностики для анализа успешных и неудачных приемов приема. Вы можете экспортировать журналы операций в службу хранилища Azure, концентратор событий или Log Analytics для отслеживания состояния приема.

- [Мониторинг операций приема обозреватель данных Azure](https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs)

- [Прием и запрос данных мониторинга в Azure обозреватель данных](https://docs.microsoft.com/azure/data-explorer/ingest-data-no-code)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Включение журналов аудита для ресурсов Azure

**Руководство**. Включение параметров диагностики для Azure обозреватель данных для доступа и ведения журналов для операций и ведения журнала, связанных с обслуживанием. Журналы действий Azure в Azure Monitor, которые включают подробное ведение журнала по ресурсу, включены по умолчанию.

- [Мониторинг операций приема обозреватель данных Azure](https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs)

- [Как получить журналы и метрики платформы с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Общие сведения о журналах платформы Azure](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**. Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5. Настройка хранения журнала безопасности

**Руководство**. В Azure Monitor задайте период хранения для рабочей области Log Analytics согласно нормативным требованиям вашей организации. Используйте учетные записи хранения Azure для долгосрочного и архивного хранения.

- [Задание параметров хранения журналов для рабочих областей Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="26-monitor-and-review-logs"></a>2,6: мониторинг и просмотр журналов

**Руководство**. анализ и мониторинг журналов для аномальных поведений и регулярная проверка результатов. После включения параметров диагностики для обозреватель данных Azure используйте рабочую область Log Analytics Azure Monitor, чтобы просматривать журналы и выполнять запросы к данным журнала.

- [Основные сведения о рабочей области Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

- [Выполнение пользовательских запросов в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

## <a name="identity-and-access-control"></a>Идентификатор и управление доступом

*Дополнительные сведения см. в статье о [производительности системы безопасности Azure: идентификация и управление доступом](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Инвентаризация учетных записей администраторов

**Руководство**. в Azure обозреватель данных роли безопасности определяют, какие субъекты безопасности (пользователи и приложения) имеют разрешения на работу с защищенным ресурсом, таким как база данных или таблица, и какие операции разрешены. Вы можете использовать запрос Kusto для перечисления принципов в роли администратора для кластеров и баз данных Azure обозреватель данных.

- [Управление ролями безопасности в Azure обозреватель данных с помощью запроса Kusto](https://docs.microsoft.com/azure/kusto/management/security-roles)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2. Изменение паролей по умолчанию, где применимо

**Руководство**. Azure Active Directory (Azure AD) не имеет концепции паролей по умолчанию. Другие ресурсы Azure, которым требуется пароль, принудительно запрашивают пароль с требованиями к сложности и минимальной длиной пароля, которая зависит от службы. Вы несете ответственность за сторонние приложения и службы Marketplace, которые могут использовать пароли по умолчанию.

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3. Применение выделенных административных учетных записей

**Руководство**. Клиент для создания стандартных рабочих процедур, связанных с использованием выделенных административных учетных записей. Используйте учетную запись Центра безопасности Azure и управление доступом для мониторинга количества административных учетных записей.

Клиенты также могут включить JIT-доступ с помощью Azure Active Directory (Azure AD) управление привилегированными пользователями привилегированных ролей для служб Майкрософт и Azure ARM. 

- [Управление привилегированными пользователями](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4. Использование единого входа Azure Active Directory (SSO)

**Рекомендации**. когда это возможно, клиент должен использовать единый вход с Azure Active Directory (Azure AD), а не настраивать отдельные автономные учетные данные для каждой службы. Используйте рекомендации по управлению удостоверениями и доступом в центре безопасности Azure.

- [Общие сведения об использовании единого входа в Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: используйте многофакторную проверку подлинности для всех Azure Active Directory доступа

**Руководство**. Включение многофакторной идентификации (MFA) для Azure Active Directory (Azure AD) и следование рекомендациям по управлению удостоверениями и доступом в центре безопасности Azure.

- [Включение MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Мониторинг идентификации и доступа в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: используйте защищенные рабочие станции, управляемые Azure, для административных задач

**Руководство**. Использование лапы (рабочих станций привилегированного доступа) с многофакторной идентификацией (MFA), настроенной для входа и настройки ресурсов Azure.

- [Использование рабочих станций с привилегированным доступом](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Включение MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: журналы и оповещения о подозрительных действиях учетных записей администраторов

**Руководство**. Использование отчетов по безопасности Azure Active Directory (Azure AD) для создания журналов и оповещений при возникновении подозрительных или ненадежных действий в среде. Использование центра безопасности Azure для мониторинга активности удостоверений и доступа

- [Как определить пользователей Azure AD, помеченных для события риска](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Как отслеживать активность удостоверений и доступа пользователей в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8. Управление ресурсами Azure только из утвержденных расположений

**Руководство**. Клиент использует именованные расположения с условным доступом, чтобы разрешить доступ только из конкретных логических групп диапазонов IP-адресов или стран и регионов.

- [Настройка именованных расположений в Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="39-use-azure-active-directory"></a>3.9. Использование Azure Active Directory

**Рекомендации**: Azure Active Directory (Azure AD) является предпочтительным способом проверки подлинности в Azure обозреватель данных. Azure AD поддерживает несколько сценариев проверки подлинности:

- Аутентификация пользователей (интерактивный вход в систему). Используется для аутентификации субъектов, представляющих людей.

- Проверка подлинности приложений (неинтерактивный вход в систему). Используется для проверки подлинности служб и приложений, которые должны запускаться и (или) проходить проверку подлинности без участия пользователя.

Дополнительные сведения см. в следующих ресурсах.

- [Общие сведения об управлении доступом обозреватель данных Azure](https://docs.microsoft.com/azure/kusto/management/access-control)

- [Проверка подлинности с помощью Azure Active Directory](https://docs.microsoft.com/azure/kusto/management/access-control/aad)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10. Регулярная проверка и согласование доступа пользователей

**Руководство**. Azure Active Directory (Azure AD) предоставляет журналы для облегчения поиска устаревших учетных записей. Кроме того, используйте проверку доступа удостоверений Azure, чтобы эффективно управлять членством в группах, доступом к корпоративным приложениям и назначениями ролей. Доступ пользователя можно проверить регулярно, чтобы убедиться, что доступ к ним имеют только нужные пользователи. 

- [Как пройти проверку подлинности Azure AD для Azure обозреватель данных Access](https://docs.microsoft.com/azure/kusto/management/access-control/how-to-authenticate-with-aad)

- [Отчеты Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Использование проверок доступа для идентификации Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: монитор пытается получить доступ к отключенным учетным данным

**Руководство**. Вы можете использовать действия входа Azure Active Directory (Azure AD), события аудита и риска для мониторинга, которые позволяют интегрироваться с любым средством управления сведениями о безопасности и событиями (SIEM) и наблюдением.

 Этот процесс можно упростить, создав параметры диагностики для Azure Active Directory учетных записей пользователей, отправив журналы аудита и журналы входа в рабочую область Log Analytics. Клиент для настройки нужных оповещений в Log Analytics рабочей области.

- [Как интегрировать журналы действий Azure в Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: предупреждение об отклонении при входе в учетную запись

**Руководство**. Использование Azure Active Directory (Azure AD) обнаружения рисков и функции защиты идентификации для настройки автоматических ответов на обнаруженные подозрительные действия, связанные с удостоверениями пользователей. Кроме того, вы можете принимать данные в метку Azure для дальнейшего изучения.

- [Просмотр рискованных входов в Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Как настроить и включить политики рисков с помощью защиты идентификации](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13. Предоставление корпорации Майкрософт доступа к соответствующим данным клиентов в рамках сценариев поддержки

**Руководство**. в сценариях поддержки, в которых корпорации Майкрософт требуется доступ к данным клиентов, защищенное хранилище предоставляет пользователям интерфейс для просмотра и утверждения или отклонения запросов на доступ к данным клиентов.

- [Основные сведения о защищенное хранилище](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

## <a name="data-protection"></a>Защита данных

*Дополнительные сведения см. в статье о [производительности системы безопасности Azure: защита данных](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Инвентаризация конфиденциальных данных

**Руководство**. Используйте теги для пометки ресурсов Azure, в которых хранятся или обрабатываются конфиденциальные данные.

- [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2. Изолирование систем, хранящих или обрабатывающих конфиденциальные данные

**Руководство**. Реализуйте отдельные подписки и группы управления для разработки, тестирования и производства. Кластеры обозреватель данных Azure должны отделяться от других ресурсов по виртуальной сети или подсети, помечены соответствующим образом и защищены в рамках группы безопасности сети (NSG) или брандмауэра Azure. Кластеры Azure обозреватель данных, в которых хранятся или обрабатываются конфиденциальные данные, должны быть достаточно изолированы.

- [Создание дополнительных подписок Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Создание групп управления](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Развертывание кластера обозреватель данных Azure в виртуальной сети](https://docs.microsoft.com/azure/data-explorer/vnet-deployment)

- [Создание группы безопасности сети с конфигурацией безопасности](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4. Шифрование любой конфиденциальной информации при передаче

**Руководство**. кластер Azure обозреватель данных согласовывает TLS 1,2 по умолчанию. Убедитесь, что все клиенты, подключающиеся к ресурсам Azure, могут согласовать TLS 1,2 или более поздней версии.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Совмещаемая блокировка

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Использование средства активного обнаружения для распознавания конфиденциальных данных

**Руководство**. функции для идентификации данных, классификации и предотвращения потерь еще не доступны для Azure обозреватель данных. Установите сторонние решения, если это необходимо для обеспечения соответствия требованиям.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным и предпринимает все возможные усилия для защиты клиентов от потери данных и раскрытия информации. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала и поддерживает набор надежных элементов управления и возможностей защиты данных.

- [Общие сведения о защите данных клиентов в Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6. Использование управления доступом на основе ролей для управления доступом к ресурсам

**Руководство**. Azure обозреватель данных позволяет управлять доступом к базам данных и таблицам с помощью модели управления доступом на основе РОЛЕЙ (RBAC) Azure. По этой модели субъекты (пользователи, группы и приложения) сопоставляются с ролями. Субъекты могут получить доступ к ресурсам в соответствии со своими назначенными ролями.

- [Список ролей и разрешений, а затем инструкции по настройке RBAC для Azure обозреватель данных](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8. Шифрование конфиденциальной информации при хранении

**Руководство**. шифрование дисков Azure помогает защитить данные и обеспечить их защиту в соответствии с вашими требованиями к безопасности и соблюдению требований Организации. Он обеспечивает шифрование томов для дисков операционной системы и данных виртуальных машин кластера. Она также интегрируется с Azure Key Vault, которая позволяет управлять ключами и секретами шифрования диска и управлять ими, а затем обеспечить шифрование всех данных на дисках виртуальной машины в службе хранилища Azure.

- [Включение шифрования неактивных компонентов для кластеров Azure обозреватель данных](https://docs.microsoft.com/azure/data-explorer/manage-cluster-security)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Включение в журнал и создание оповещений по изменениям критических ресурсов Azure

**Руководство**. Использование Azure Monitor с журналом действий Azure для создания оповещений о том, что изменения на уровне ресурсов выполняются в кластерах Azure обозреватель данных.

- [Создание оповещений для событий журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

- [Создание оповещений для событий журнала действий Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

## <a name="vulnerability-management"></a>Управление уязвимостями

*Дополнительные сведения см. в статье о [производительности системы безопасности Azure: Управление уязвимостью](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Использование процесса оценки рисков для определения приоритета в устранении обнаруженных уязвимостей

**Руководство**. Используйте оценки рисков по умолчанию (Оценка безопасности), предоставляемые центром безопасности Azure.

- [Оценка безопасности центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

## <a name="inventory-and-asset-management"></a>Инвентаризация и управление ресурсами

*Дополнительные сведения см. в статье о [производительности системы безопасности Azure: Инвентаризация и управление активами](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1. Использование автоматизированного решения для обнаружения ресурсов

**Руководство**. Использование графа ресурсов Azure для запроса и обнаружения всех ресурсов в подписках. Убедитесь в том, что в вашем клиенте есть соответствующие разрешения (на чтение) и вы можете перечислить все подписки Azure, а также ресурсы в ваших подписках.

Хотя классические ресурсы Azure можно обнаружить через Resource Graph, настоятельно рекомендуется в дальнейшем создавать и использовать ресурсы Azure Resource Manager.

- [Как создавать запросы с помощью Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Как просматривать подписки Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Общие сведения об Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2. Ведение метаданных активов

**Руководство**. Применяйте к ресурсам Azure теги, чтобы логически классифицировать их на основе метаданных.

- [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Удаление неавторизованных ресурсов Azure

**Руководство**. для Организации и мониторинга ресурсов можно использовать соответствующие соглашения об именовании, теги, группы управления или отдельные подписки. Вы можете использовать граф ресурсов Azure для регулярной согласования инвентаризации и своевременного удаления неавторизованных ресурсов из подписки. 

- [Создание дополнительных подписок Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Создание групп управления](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Создание и использование тегов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Как создавать запросы с помощью Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: определение и обслуживание инвентаризации утвержденных ресурсов Azure

**Рекомендации**. вам потребуется создать инвентаризацию утвержденных ресурсов Azure и утвержденного программного обеспечения для ресурсов вычислений в соответствии с потребностями Организации.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5. Отслеживание неутвержденных ресурсов Azure

**Руководство**. Вы можете использовать политики Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в клиентских подписках, используя следующие встроенные определения политик:

- Недопустимые типы ресурсов

- Допустимые типы ресурсов

Вы сможете отслеживать созданные политикой события с помощью журналов действий, которые можно отслеживать с помощью Azure Monitor.

Кроме того, вы можете использовать граф ресурсов Azure для запроса или обнаружения ресурсов в подписках.

- [Создание политик и управление ими для обеспечения соответствия](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Краткое руководство. Выполнение запроса Resource Graph с помощью обозревателя Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Создание, просмотр оповещений журнала действий и управление ими с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9. Использование только утвержденных служб Azure

**Руководство**. Вы можете использовать политики Azure, чтобы ограничить тип ресурсов, которые могут быть созданы в подписках клиентов, используя следующие встроенные определения политик:

 - Недопустимые типы ресурсов

 - Допустимые типы ресурсов

Дополнительные сведения см. в следующих ресурсах.

- [Создание политик и управление ими для обеспечения соответствия](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Примеры для Политики Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: ограничьте возможность пользователей работать с Azure Resource Manager

**Рекомендации**. Используйте условный доступ Azure, чтобы ограничить возможность пользователей взаимодействовать с Azure Resource Manager путем настройки "Блокировать доступ" для приложения "Управление Microsoft Azure". Это предотвратит создание и изменение ресурсов в подписках Azure. 

- [Управление доступом к функциям управления Azure с помощью условного доступа](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

## <a name="secure-configuration"></a>Настройка безопасности

*Дополнительные сведения см. в статье о [производительности системы безопасности Azure: безопасная конфигурация](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Установка безопасных конфигураций для всех ресурсов Azure

**Руководство**. Использование псевдонимов политик Azure для создания настраиваемых политик для аудита или принудительного применения конфигурации ресурсов Azure. Вы также можете использовать встроенные определения политик Azure.

Кроме того, Azure Resource Manager может экспортировать шаблон в нотация объектов JavaScript (JSON), который следует проверить, чтобы убедиться, что конфигурации соответствуют требованиям безопасности Организации или превышают их.

Вы также можете использовать рекомендации из центра безопасности Azure в качестве защищенного шаблона базовой конфигурации для ресурсов Azure.

- [Просмотр доступных псевдонимов политик Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Руководство по Создание политик и управление ими для обеспечения соответствия требованиям](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Экспорт одного и нескольких ресурсов в шаблон в портал Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Рекомендации по безопасности — справочное руководство](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3. Сохранение безопасных конфигураций для ресурсов Azure

**Руководство**. Использование политики Azure [Deny] и [развертывание если не существует] для обеспечения безопасности параметров в ресурсах Azure.  Вы можете использовать такие решения, как Отслеживание изменений, панель мониторинга соответствия политике или пользовательское решение, чтобы легко обнаруживать изменения в системе безопасности в среде.

- [Общие сведения о влиянии политики Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Создание политик и управление ими для обеспечения соответствия](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Отслеживание изменений в среде с помощью решения для отслеживания изменений](https://docs.microsoft.com/azure/automation/change-tracking)

- [Получение данных о соответствии для ресурсов Azure](https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5. Безопасное хранение конфигурации ресурсов Azure

**Руководство**. Использование Azure Repos для безопасного хранения и управления кодом, например пользовательскими политиками Azure, Azure Resource Manager шаблонами, сценариями настройки требуемого состояния и т. д. Чтобы получить доступ к ресурсам, которыми вы управляете в Azure DevOps, вы можете предоставить или отклонить разрешения для определенных пользователей, встроенных групп безопасности или групп, определенных в Azure Active Directory (Azure AD), если они интегрированы с Azure DevOps, или Active Directory, если они интегрированы с TFS.

- [Как хранить код в Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [О разрешениях и группах в Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: развертывание средств управления конфигурацией для ресурсов Azure

**Руководство**. Определение и реализация стандартных конфигураций безопасности для ресурсов Azure с помощью политики Azure. Используйте псевдонимы политик Azure для создания настраиваемых политик для аудита или принудительного применения конфигурации сети ресурсов Azure. Вы также можете использовать встроенные определения политик, связанные с конкретными ресурсами.  Кроме того, вы можете использовать службу автоматизации Azure для развертывания изменений конфигурации.

- [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Использование псевдонимов](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9. Реализация автоматического мониторинга конфигурации для ресурсов Azure

**Руководство**. Использование псевдонимов политик Azure для создания настраиваемых политик для оповещения, аудита и принудительного применения конфигураций системы. Используйте политику Azure [аудит], [запретить] и [развернуть, если не существует] для автоматического применения конфигураций для ресурсов Azure.

- [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12. Безопасное и автоматическое управление удостоверениями

**Руководство**. Использование управляемых удостоверений для предоставления служб Azure с автоматически управляемым удостоверением в Azure Active Directory (Azure AD). Управляемое удостоверение можно использовать для проверки подлинности в любой службе, которая поддерживает проверку подлинности Azure AD, включая Key Vault, при этом не сохраняя каких-либо учетных данных в коде.

- [Настройка управляемых удостоверений](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

- [Настройка управляемых удостоверений для кластера Azure обозреватель данных](https://docs.microsoft.com/azure/data-explorer/managed-identities)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13. Устранение непреднамеренного раскрытия учетных данных

**Руководство**. Реализуйте сканер учетных данных для обнаружения учетных данных в коде. Сканер учетных данных также рекомендует перемещать обнаруженные учетные данные в более безопасные расположения, такие как Azure Key Vault. 

- [Как настроить сканер учетных данных](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

## <a name="malware-defense"></a>Защита от вредоносных программ

*Дополнительные сведения см. в статье о [производительности системы безопасности Azure: защита от вредоносных программ](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2. Предварительная проверка файлов для отправки в ресурсы Azure, не являющиеся вычислительными

**Руководство**. антивредоносное по Майкрософт включено на базовом узле, поддерживающем службы Azure (например, Azure обозреватель данных), но не выполняется в содержимом клиента.

Предварительное сканирование любого содержимого, отправляемого в Нерасчетные ресурсы Azure, например Azure обозреватель данных, Data Lake Storage, хранилище BLOB-объектов, база данных Azure для PostgreSQL и т. д. Корпорация Майкрософт не может получить доступ к данным в этих экземплярах.

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

## <a name="data-recovery"></a>Восстановление данных

*Дополнительные сведения см. в статье о [производительности системы безопасности Azure: восстановление данных](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Обеспечьте регулярное автоматическое резервное копирование

**Руководство**. данные в учетной записи хранения Microsoft Azure, используемой кластером Azure обозреватель данных, всегда реплицируются для обеспечения устойчивости и высокой доступности. Служба хранилища Azure копируют данные для защиты от запланированных и незапланированных событий, включая временные сбои оборудования, сети или энергоснабжения, масштабные стихийные бедствия и т. д. Вы можете реплицировать данные в пределах одного центра обработки данных, между зональными центрами обработки данных в одном регионе или между географически разделенными регионами.

- [Общие сведения о избыточности службы хранилища Azure и Service-Level соглашениях](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

- [Экспорт данных в хранилище](https://docs.microsoft.com/azure/kusto/management/data-export/export-data-to-storage)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: выполните полное резервное копирование системы и резервное копирование ключей, управляемых клиентом

**Руководство**. Azure обозреватель данных шифрует все данные в неактивных учетных записях хранения. По умолчанию данные шифруются с помощью ключей, управляемых корпорацией Майкрософт. Для дополнительного управления ключами шифрования можно предоставить ключи, управляемые клиентом, для шифрования данных. Ключи, управляемые клиентом, должны храниться в Azure Key Vault.

- [Настройка ключей, управляемых клиентом, с помощью портал Azure ](https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-portal)

- [Настройка ключей, управляемых клиентом, с помощью C # ](https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp)

- [Настройка ключей, управляемых клиентом, с помощью шаблона Azure Resource Manager](https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager)

- [Резервное копирование сертификатов Azure Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-4.8.0&amp;preserve-view=true)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Проверьте все резервные копии, включая управляемые клиентом ключи.

**Руководство**. периодическое тестирование данных для Azure Key Vault секретов.

- [Восстановление сертификатов Azure Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-4.8.0&amp;preserve-view=true)

- [Настройка ключей, управляемых клиентом, с помощью C # ](https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp)

- [Настройка ключей, управляемых клиентом, с помощью шаблона Azure Resource Manager](https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Обеспечьте защиту резервных копий и ключей, управляемых клиентом

**Руководство**. клиент включает Soft-Delete в Key Vault для защиты ключей от случайного или вредоносного удаления.  Вы также можете включить защиту от вирусов, чтобы в случае, если хранилище или объект в удаленном состоянии не удастся очистить, пока не будет пройден срок хранения.  

- [Включение Soft-Delete и очистка защиты в Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

- [Настройка ключей, управляемых клиентом, с помощью C # ](https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp)

- [Настройка ключей, управляемых клиентом, с помощью шаблона Azure Resource Manager](https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

## <a name="incident-response"></a>реагирование на инциденты.

*Дополнительные сведения см. в статье о [производительности системы безопасности Azure: реагирование на инциденты](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Создание руководства по реагированию на инциденты

**Руководство**. Создайте руководство по реагированию на инциденты для вашей организации. Убедитесь в том, что имеются письменные планы реагирования на инциденты, которые определяют все действия персонала, а также этапы обработки инцидентов и управления ими для проверки после инцидента.
    

- [ Руководство по созданию собственного процесса реагирования на инциденты безопасности](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

    

- [ Анатомия инцидента в центре Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

    

- [ Клиент также может воспользоваться руководством по обработке инцидентов безопасности компьютера NIST, чтобы помочь в создании собственного плана реагирования на инциденты.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Создание процедуры оценки инцидента и определения приоритетов

**Руководство**. Центр безопасности назначает каждому оповещению серьезность, которая поможет определить, какие предупреждения следует расследовать первыми. Серьезность основывается на том, насколько уверен Центр безопасности в исследовании или аналитике, используемой для оповещения, а также на уровне достоверности злонамеренности события, приведшего к оповещению.

Кроме того, четко помечайте подписки тегами (например, рабочие, нерабочие) и создайте систему именования, чтобы четко определить и классифицировать ресурсы Azure, особенно обрабатывающие конфиденциальные данные.  Вы несете ответственность за назначение приоритета оповещениям, требующим действий по исправлению, в зависимости от важности ресурсов Azure и среды, в которой произошел инцидент.

- [Оповещения безопасности в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [использование тегов для упорядочения ресурсов в Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="103-test-security-response-procedures"></a>10.3. Проверка процедур реагирования на угрозы

**Руководство**. Выполняйте упражнения, чтобы периодически тестировать возможности ваших систем реагировать на угрозы для защиты ресурсов Azure. Выявите слабые точки и пробелов и пересмотрите план по мере необходимости.
    

- [ Обратитесь к публикации NIST: руководство по тестированию, обучению и упражнениям для ИТ-планов и возможностей.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Предоставление контактных сведений и настройка уведомлений по инцидентам безопасности

**Руководство**. Корпорация Майкрософт будет использовать информацию об инциденте безопасности для связи с вами, если центр Microsoft Security Response Center (MSRC) обнаружит, что к вашим данным был получен незаконный или несанкционированный доступ. Проверьте инциденты после факта обращения, чтобы убедиться в том, что проблемы устранены.
    

- [Как задать контакт безопасности Центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Мониторинг Центра безопасности Azure**: Да

**Ответственность**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Включение оповещений системы безопасности в систему реагирования на инциденты

**Руководство**. Экспортируйте оповещения и рекомендации Центра безопасности Azure с помощью функции непрерывного экспорта с целью выявления рисков для ресурсов Azure. Непрерывный экспорт позволяет экспортировать предупреждения и рекомендации как вручную, так и в постоянном, непрерывном режиме. Вы можете использовать соединитель данных Центра безопасности Azure для потоковой передачи оповещений в Azure Sentinel.
    

- [ Настройка непрерывного экспорта](https://docs.microsoft.com/azure/security-center/continuous-export)

    

- [ Потоковая передача предупреждений в Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6. Автоматизация реагирования на оповещения системы безопасности

**Руководство**. Использование функции автоматизации рабочих процессов в центре безопасности Azure для автоматического запуска ответов с помощью "Logic Apps" в оповещениях системы безопасности и рекомендациях по защите ресурсов Azure.
    

- [ Настройка автоматизации рабочего процесса и Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Мониторинг Центра безопасности Azure**: в настоящее время недоступен.

**Ответственность**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Тесты на проникновение и попытки нарушения безопасности "красной командой"

*Дополнительные сведения см. в статье [тесты производительности системы безопасности Azure: испытания на проникновение и команды красных команд](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1. Проведите регулярное тестирование на проникновение ресурсов Azure и обеспечьте исправление всех критических результатов безопасности.

**Рекомендации**. Следуйте правилам тестирования уязвимости Microsoft Cloud, чтобы убедиться, что тесты на проникновение не нарушают политики Майкрософт. Используйте стратегию и исполнение Microsoft, а затем протестировать проникновение в реальном времени для управляемой корпорацией Майкрософт облачной инфраструктуры, служб и приложений. 

- [Правила тестирования уязвимости Задействований](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud красное объединение](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Мониторинг центра безопасности Azure**: неприменимо

**Ответственность**: Совмещаемая блокировка

## <a name="next-steps"></a>Дальнейшие действия

- См. [Обзор производительности системы безопасности Azure v2](/azure/security/benchmarks/overview)
- Дополнительные сведения о [базовой конфигурации безопасности Azure](/azure/security/benchmarks/security-baselines-overview).
