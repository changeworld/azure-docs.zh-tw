---
title: 延伸模組資源類型
description: 列出 Azure 資源類型可用來擴充其他資源類型的功能。
ms.topic: conceptual
ms.date: 11/14/2020
ms.openlocfilehash: 5561c480dd5a2849588ed2288eb5bcc35fc1446c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658446"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>擴充其他資源功能的資源類型

延伸模組資源是新增至另一個資源功能的資源。 例如，資源鎖定是延伸模組資源。 您將資源鎖定套用至另一個資源，以防止其遭到刪除或修改。 自行建立資源鎖定並不合理。 延伸模組資源一律會套用至另一個資源。

## <a name="microsoftadvisor"></a>Microsoft.Advisor

- Microsoft Advisor/設定
- Microsoft Advisor/建議
- Microsoft. Advisor/隱藏

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

- Microsoft.alertsmanagement/警示

## <a name="microsoftauthorization"></a>Microsoft.Authorization

- Microsoft. 授權/denyAssignments
- Microsoft.Authorization/locks
- Microsoft.Authorization/policyAssignments
- Microsoft. 授權/policyDefinitions
- Microsoft. 授權/policyExemptions
- Microsoft. 授權/policySetDefinitions
- Microsoft. 授權/privateLinkAssociations
- Microsoft.Authorization/roleAssignments
- Microsoft. 授權/roleDefinitions

## <a name="microsoftautomanage"></a>Automanage

- Automanage/configurationProfileAssignments

## <a name="microsoftbilling"></a>Microsoft.Billing

- Microsoft 帳單/billingPeriods
- Microsoft 帳單/billingPermissions
- Microsoft 帳單/billingRoleAssignments
- Microsoft 帳單/billingRoleDefinitions
- Microsoft 帳單/createBillingRoleAssignment

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

- Microsoft 藍圖/blueprintAssignments
- Microsoft 藍圖/藍圖

## <a name="microsoftconsumption"></a>Microsoft.Consumption

- Microsoft. 耗用量/AggregatedCost
- Microsoft. 耗用量/餘額
- Microsoft 耗用量/預算
- Microsoft. 耗用量/費用
- Microsoft. 耗用量/CostTags
- Microsoft 的取用/點數
- Microsoft. 耗用量/事件
- Microsoft. 耗用量/預測
- Microsoft 耗用量/大量
- Microsoft 耗用量/marketplace
- Microsoft. 耗用量/Pricesheets
- Microsoft 取用/產品
- Microsoft. 耗用量/ReservationDetails
- Microsoft. 耗用量/ReservationRecommendationDetails
- Microsoft. 耗用量/ReservationRecommendations
- Microsoft. 耗用量/ReservationSummaries
- Microsoft. 耗用量/ReservationTransactions

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

- Microsoft.containerinstance/serviceAssociationLinks

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

- CostManagement/警示
- CostManagement/預算
- CostManagement/維度
- CostManagement/匯出
- CostManagement/ExternalSubscriptions
- CostManagement/預測
- CostManagement/見解
- CostManagement/Query
- CostManagement/Reportconfigs
- CostManagement/報表
- CostManagement/Views

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

- Microsoft.CustomProviders/associations

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

- >microsoft.guestconfiguration/configurationProfileAssignments
- >microsoft.guestconfiguration/guestConfigurationAssignments
- >microsoft.guestconfiguration/軟體

## <a name="microsoftinsights"></a>microsoft.insights

- microsoft insights/基準
- microsoft insights/dataCollectionRuleAssociations
- microsoft insights/diagnosticSettings
- microsoft insights/diagnosticSettingsCategories
- microsoft insights/eventtypes
- microsoft insights/extendedDiagnosticSettings
- microsoft insights/不 guestdiagnosticsettingsassociation
- microsoft insights/logDefinitions
- microsoft insights/記錄
- microsoft insights/metricbaselines
- microsoft insights/metricDefinitions
- microsoft insights/metricNamespaces
- microsoft insights/計量
- microsoft insights/myWorkbooks
- microsoft insights/拓撲
- microsoft insights/交易

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

- KubernetesConfiguration/擴充功能
- KubernetesConfiguration/sourceControlConfigurations

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

- Microsoft. 維護/applyUpdates
- Microsoft. 維護/configurationAssignments
- Microsoft. 維護/更新

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

- ManagedIdentity/身分識別

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

- >microsoft.managedservices/registrationAssignments
- >microsoft.managedservices/registrationDefinitions

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

- OperationalInsights/storageInsightConfigs

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

- Microsoft.operationsmanagement/managementassociations

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

- Microsoft.policyinsights/證明
- Microsoft.policyinsights/policyEvents
- Microsoft.policyinsights/policyStates
- Microsoft.policyinsights/policyTrackedResources
- Microsoft.PolicyInsights/remediations

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

- Az.recoveryservices/backupProtectedItems
- Az.recoveryservices/replicationEligibilityResults

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

- ResourceHealth/childResources
- ResourceHealth/events
- ResourceHealth/impactedResources
- ResourceHealth/通知

## <a name="microsoftresources"></a>Microsoft.Resources

- Microsoft .Resources/links
- Microsoft .Resources/標記

## <a name="microsoftsecurity"></a>Microsoft.Security

- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. 安全性/評量
- Microsoft. Security/規範
- Microsoft. Security/dataCollectionAgents
- Microsoft. 安全性/裝置
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/iotSensors
- Microsoft. Security/jitPolicies
- Microsoft. Security/serverVulnerabilityAssessments
- Microsoft. Security/sqlVulnerabilityAssessments

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

- SecurityInsights/匯總
- SecurityInsights/alertRules
- SecurityInsights/alertRuleTemplates
- SecurityInsights/automationRules
- SecurityInsights/書簽
- SecurityInsights/案例
- SecurityInsights/dataConnectors
- SecurityInsights/dataConnectorsCheckRequirements
- SecurityInsights/實體
- SecurityInsights/事件
- Microsoft. SecurityInsights/settings
- SecurityInsights/threatIntelligence
- SecurityInsights/watchlists

## <a name="microsoftserialconsoleppe"></a>SerialConsole. PPE

- SerialConsole. PPE/Serialport

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

- SoftwarePlan/hybridUseBenefits

## <a name="microsoftsupport"></a>microsoft.support

- microsoft. 支援/>microsoft.office365.supporttickets

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

- WorkloadMonitor/元件
- WorkloadMonitor/monitorInstances
- WorkloadMonitor/監視器
- WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>後續步驟

- 若要取得 Azure Resource Manager 範本中擴充功能資源的資源識別碼，請使用 [extensionResourceId](../templates/template-functions-resource.md#extensionresourceid)。
- 如需在範本中建立延伸模組資源的範例，請參閱 [Event Grid 事件訂閱](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions)。
