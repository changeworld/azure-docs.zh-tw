---
title: 延伸模組資源類型
description: 列出 Azure 資源類型是用來擴充其他資源類型的功能。
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 84de9b66f9001985b8c7b92882f03ff8c7cbf431
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374009"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>擴充其他資源功能的資源類型

延伸模組資源是新增至另一個資源功能的資源。 例如，資源鎖定是擴充功能資源。 您會將資源鎖定套用至另一個資源，以防止其遭到刪除或修改。 建立資源鎖定本身並不合理。 延伸模組資源一律會套用至另一個資源。

## <a name="extension-resource-types"></a>延伸模組資源類型

- Microsoft Advisor/設定
- Microsoft Advisor/建議
- Microsoft Advisor/隱藏
- Microsoft.alertsmanagement/警示
- Microsoft.alertsmanagement/alertsSummary
- Microsoft 授權/checkAccess
- Microsoft 授權/denyAssignments
- Microsoft 授權/findOrphanRoleAssignments
- Microsoft.Authorization/locks
- Microsoft。授權/許可權
- Microsoft.Authorization/policyAssignments
- Microsoft 授權/policyDefinitions
- Microsoft 授權/policyExemptions
- Microsoft 授權/policySetDefinitions
- Microsoft 授權/privateLinkAssociations
- Microsoft.Authorization/roleAssignments
- Microsoft 授權/roleAssignmentsUsageMetrics
- Microsoft 授權/roleDefinitions
- Microsoft 帳單/billingPeriods
- Microsoft 帳單/billingPermissions
- Microsoft 帳單/billingRoleAssignments
- Microsoft 帳單/billingRoleDefinitions
- Microsoft 帳單/createBillingRoleAssignment
- Microsoft 藍圖/blueprintAssignments
- Microsoft 藍圖/藍圖
- ChangeAnalysis/resourceChanges
- Microsoft. 耗用量/AggregatedCost
- Microsoft. 耗用量/餘額
- Microsoft. 耗用量/預算
- Microsoft。耗用量/費用
- Microsoft. 耗用量/CostTags
- Microsoft。耗用量/預測
- Microsoft。耗用量/市場
- Microsoft. 耗用量/OperationResults
- Microsoft. 耗用量/OperationStatus
- Microsoft. 耗用量/Pricesheets
- Microsoft. 耗用量/ReservationDetails
- Microsoft. 耗用量/ReservationRecommendationDetails
- Microsoft. 耗用量/ReservationRecommendations
- Microsoft. 耗用量/ReservationSummaries
- Microsoft. 耗用量/ReservationTransactions
- Microsoft. 耗用量/標記
- Microsoft。耗用量/條款
- Microsoft. 耗用量/UsageDetails
- Microsoft. 耗用量/點數
- Microsoft. 耗用量/事件
- Microsoft. 耗用量/大量
- Microsoft. 耗用量/產品
- Microsoft。耗用量/租使用者
- Microsoft.containerinstance/serviceAssociationLinks
- CostManagement/警示
- CostManagement/預算
- CostManagement/costAllocationRules
- CostManagement/維度
- CostManagement/匯出
- CostManagement/ExternalSubscriptions
- CostManagement/預測
- CostManagement/Query
- CostManagement/Reportconfigs
- CostManagement/Reports
- CostManagement/showbackRules
- CostManagement/Views
- Microsoft.CustomProviders/associations
- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics
- GuestConfiguration/configurationProfileAssignments
- GuestConfiguration/guestConfigurationAssignments
- GuestConfiguration/軟體
- GuestConfiguration/softwareUpdateProfile
- GuestConfiguration/softwareUpdates
- microsoft insights/基準
- microsoft insights/calculatebaseline
- microsoft insights/dataCollectionRuleAssociations
- microsoft insights/diagnosticSettings
- microsoft insights/diagnosticSettingsCategories
- microsoft insights/eventtypes
- microsoft insights/extendedDiagnosticSettings
- microsoft insights/不 guestdiagnosticsettingsassociation
- microsoft insights/logDefinitions
- microsoft insights/記錄
- microsoft insights/Metricdefinitions.listasync
- microsoft insights/metricNamespaces
- microsoft insights/metricbaselines
- microsoft insights/計量
- microsoft insights/myWorkbooks
- microsoft insights/拓撲
- microsoft 深入解析/交易
- microsoft insights/vmInsightsOnboardingStatuses
- KubernetesConfiguration/sourceControlConfigurations
- Microsoft. 維護/applyUpdates
- Microsoft. 維護/configurationAssignments
- Microsoft. 維護/更新
- Microsoft.managedidentity/身分識別
- ManagedServices/registrationAssignments
- ManagedServices/registrationDefinitions
- Microsoft.operationalinsights/storageInsightConfigs
- Microsoft.operationsmanagement/managementassociations
- Microsoft.policyinsights/證明
- Microsoft.policyinsights/policyEvents
- Microsoft.policyinsights/policyStates
- Microsoft.policyinsights/policyTrackedResources
- Microsoft.PolicyInsights/remediations
- Azurerm.recoveryservices/backupProtectedItems
- Azurerm.recoveryservices/replicationEligibilityResults
- ResourceHealth/availabilityStatuses
- ResourceHealth/childAvailabilityStatuses
- ResourceHealth/childResources
- ResourceHealth/events
- ResourceHealth/impactedResources
- ResourceHealth/通知
- Microsoft .Resources/links
- Microsoft .Resources/tags
- Microsoft. Security/規範
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. 安全性/評量
- Microsoft. Security/complianceResults
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/jitPolicies
- Microsoft. Security/serverVulnerabilityAssessments
- SecurityInsights/匯總
- SecurityInsights/alertRuleTemplates
- SecurityInsights/alertRules
- SecurityInsights/automationRules
- SecurityInsights/書簽
- SecurityInsights/案例
- SecurityInsights/dataConnectors
- SecurityInsights/dataConnectorsCheckRequirements
- SecurityInsights/實體
- SecurityInsights/entityQueries
- SecurityInsights/事件
- SecurityInsights/officeConsents
- SecurityInsights/settings
- SecurityInsights/threatIntelligence
- SoftwarePlan/hybridUseBenefits
- Microsoft 訂用帳戶/CreateSubscription
- microsoft 支援/supporttickets
- WorkloadMonitor/components
- WorkloadMonitor/monitorInstances
- WorkloadMonitor/監視器
- WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>後續步驟

- 若要取得 Azure Resource Manager 範本中擴充功能資源的資源識別碼，請使用[extensionResourceId](../templates/template-functions-resource.md#extensionresourceid)。
- 如需在範本中建立擴充功能資源的範例，請參閱[Event Grid 事件訂閱](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions)。
