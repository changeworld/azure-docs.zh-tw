---
title: 延伸模組資源類型
description: 列出 Azure 資源類型可用來擴充其他資源類型的功能。
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 8b80c63d361f3ad8199fd669178f7bf88dabe02e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969737"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>擴充其他資源功能的資源類型

延伸模組資源是新增至另一個資源功能的資源。 例如，資源鎖定是延伸模組資源。 您將資源鎖定套用至另一個資源，以防止其遭到刪除或修改。 自行建立資源鎖定並不合理。 延伸模組資源一律會套用至另一個資源。

## <a name="extension-resource-types"></a>延伸模組資源類型

- Microsoft Advisor/設定
- Microsoft Advisor/建議
- Microsoft. Advisor/隱藏
- Microsoft.alertsmanagement/警示
- Microsoft.alertsmanagement/alertsSummary
- Microsoft. 授權/accessReviewScheduleDefinitions
- Microsoft. 授權/accessReviewScheduleSettings
- Microsoft. 授權/checkAccess
- Microsoft. 授權/denyAssignments
- Microsoft. 授權/findOrphanRoleAssignments
- Microsoft.Authorization/locks
- Microsoft. 授權/許可權
- Microsoft.Authorization/policyAssignments
- Microsoft. 授權/policyDefinitions
- Microsoft. 授權/policyExemptions
- Microsoft. 授權/policySetDefinitions
- Microsoft. 授權/privateLinkAssociations
- Microsoft.Authorization/roleAssignments
- Microsoft. 授權/roleAssignmentsUsageMetrics
- Microsoft. 授權/roleDefinitions
- Automanage/configurationProfileAssignments
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
- Microsoft 耗用量/預算
- Microsoft. 耗用量/費用
- Microsoft. 耗用量/CostTags
- Microsoft 的取用/點數
- Microsoft. 耗用量/事件
- Microsoft. 耗用量/預測
- Microsoft 耗用量/大量
- Microsoft 耗用量/marketplace
- Microsoft. 耗用量/OperationResults
- Microsoft. 耗用量/>operationstatus
- Microsoft. 耗用量/Pricesheets
- Microsoft 取用/產品
- Microsoft. 耗用量/ReservationDetails
- Microsoft. 耗用量/ReservationRecommendationDetails
- Microsoft. 耗用量/ReservationRecommendations
- Microsoft. 耗用量/ReservationSummaries
- Microsoft. 耗用量/ReservationTransactions
- Microsoft. 耗用量/標記
- Microsoft 取用/租使用者
- Microsoft 取用/詞彙
- Microsoft. 耗用量/UsageDetails
- Microsoft.containerinstance/serviceAssociationLinks
- CostManagement/警示
- CostManagement/預算
- CostManagement/costAllocationRules
- CostManagement/維度
- CostManagement/匯出
- CostManagement/ExternalSubscriptions
- CostManagement/預測
- CostManagement/見解
- CostManagement/Query
- CostManagement/Reportconfigs
- CostManagement/報表
- CostManagement/showbackRules
- CostManagement/Views
- Microsoft.CustomProviders/associations
- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics
- >microsoft.guestconfiguration/configurationProfileAssignments
- >microsoft.guestconfiguration/guestConfigurationAssignments
- >microsoft.guestconfiguration/軟體
- >microsoft.guestconfiguration/softwareUpdateProfile
- >microsoft.guestconfiguration/softwareUpdates
- microsoft insights/基準
- microsoft insights/calculatebaseline
- microsoft insights/dataCollectionRuleAssociations
- microsoft insights/diagnosticSettings
- microsoft insights/diagnosticSettingsCategories
- microsoft insights/eventtypes
- microsoft insights/extendedDiagnosticSettings
- microsoft insights/generateLiveToken
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
- microsoft insights/vmInsightsOnboardingStatuses
- KubernetesConfiguration/擴充功能
- KubernetesConfiguration/sourceControlConfigurations
- Microsoft. 維護/applyUpdates
- Microsoft. 維護/configurationAssignments
- Microsoft. 維護/更新
- ManagedIdentity/身分識別
- >microsoft.managedservices/registrationAssignments
- >microsoft.managedservices/registrationDefinitions
- OperationalInsights/storageInsightConfigs
- Microsoft.operationsmanagement/managementassociations
- Microsoft.policyinsights/證明
- Microsoft.policyinsights/policyEvents
- Microsoft.policyinsights/policyStates
- Microsoft.policyinsights/policyTrackedResources
- Microsoft.PolicyInsights/remediations
- Az.recoveryservices/backupProtectedItems
- Az.recoveryservices/replicationEligibilityResults
- ResourceHealth/availabilityStatuses
- ResourceHealth/childAvailabilityStatuses
- ResourceHealth/childResources
- ResourceHealth/events
- ResourceHealth/impactedResources
- ResourceHealth/通知
- Microsoft .Resources/links
- Microsoft .Resources/標記
- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. 安全性/評量
- Microsoft. Security/complianceResults
- Microsoft. Security/規範
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/iotSensors
- Microsoft. Security/jitPolicies
- Microsoft. Security/serverVulnerabilityAssessments
- Microsoft. Security/sqlVulnerabilityAssessments
- SecurityInsights/匯總
- SecurityInsights/alertRules
- SecurityInsights/alertRuleTemplates
- SecurityInsights/automationRules
- SecurityInsights/書簽
- SecurityInsights/案例
- SecurityInsights/dataConnectors
- SecurityInsights/dataConnectorsCheckRequirements
- SecurityInsights/實體
- SecurityInsights/entityQueries
- SecurityInsights/事件
- SecurityInsights/officeConsents
- Microsoft. SecurityInsights/settings
- SecurityInsights/threatIntelligence
- SecurityInsights/watchlists
- SoftwarePlan/hybridUseBenefits
- Microsoft. 訂用帳戶/CreateSubscription
- microsoft. 支援/>microsoft.office365.supporttickets
- WorkloadMonitor/元件
- WorkloadMonitor/monitorInstances
- WorkloadMonitor/監視器
- WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>下一步

- 若要取得 Azure Resource Manager 範本中擴充功能資源的資源識別碼，請使用 [extensionResourceId](../templates/template-functions-resource.md#extensionresourceid)。
- 如需在範本中建立延伸模組資源的範例，請參閱 [Event Grid 事件訂閱](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions)。
