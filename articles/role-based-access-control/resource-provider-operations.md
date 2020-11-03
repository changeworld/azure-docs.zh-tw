---
title: Azure 資源提供者作業
description: 列出 Azure 資源提供者的作業。
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 11/03/2020
ms.custom: generated
ms.openlocfilehash: 3f192493dfd6bfc2fb3d5802b1dd94cd4ca3a9a9
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285225"
---
# <a name="azure-resource-provider-operations"></a>Azure 資源提供者作業

本節列出用於內建角色的 Azure 資源提供者的作業。 您可以在自己的 [azure 自訂角色](custom-roles.md) 中使用這些作業，以對 Azure 中的資源提供細微的存取控制。 資源提供者作業會不斷發展。 若要取得最新的作業，請使用 [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) 或 [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list)。

按一下下表中的資源提供者名稱，以查看作業的清單。

## <a name="all"></a>全部

| 一般 |
| --- |
| [Microsoft.Addons](#microsoftaddons) |
| [Microsoft.Marketplace](#microsoftmarketplace) |
| [Microsoft.MarketplaceApps](#microsoftmarketplaceapps) |
| [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering) |
| [Microsoft.ResourceHealth](#microsoftresourcehealth) |
| [Microsoft 支援](#microsoftsupport) |
| **計算** |
| [Microsoft.ClassicCompute](#microsoftclassiccompute) |
| [Microsoft.Compute](#microsoftcompute) |
| [Microsoft.ServiceFabric](#microsoftservicefabric) |
| **網路功能** |
| [Microsoft.Cdn](#microsoftcdn) |
| [Microsoft.ClassicNetwork](#microsoftclassicnetwork) |
| [Microsoft.Network](#microsoftnetwork) |
| **儲存體** |
| [Microsoft.ClassicStorage](#microsoftclassicstorage) |
| [Microsoft.DataBox](#microsoftdatabox) |
| [Microsoft.ImportExport](#microsoftimportexport) |
| [Microsoft.NetApp](#microsoftnetapp) |
| [Microsoft.Storage](#microsoftstorage) |
| [microsoft.storagesync](#microsoftstoragesync) |
| [Microsoft.StorSimple](#microsoftstorsimple) |
| **Web** |
| [Microsoft.CertificateRegistration](#microsoftcertificateregistration) |
| [Microsoft.DomainRegistration](#microsoftdomainregistration) |
| [Microsoft.Maps](#microsoftmaps) |
| [Microsoft.Media](#microsoftmedia) |
| [Microsoft.Search](#microsoftsearch) |
| [Microsoft.SignalRService](#microsoftsignalrservice) |
| [microsoft web](#microsoftweb) |
| **容器** |
| [Microsoft.ContainerInstance](#microsoftcontainerinstance) |
| [Microsoft.ContainerRegistry](#microsoftcontainerregistry) |
| [Microsoft.ContainerService](#microsoftcontainerservice) |
| [Microsoft.DevSpaces](#microsoftdevspaces) |
| **資料庫** |
| [Microsoft.Cache](#microsoftcache) |
| [Microsoft.DataFactory](#microsoftdatafactory) |
| [Microsoft.DataMigration](#microsoftdatamigration) |
| [Microsoft.DBforMariaDB](#microsoftdbformariadb) |
| [Microsoft.DBforMySQL](#microsoftdbformysql) |
| [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql) |
| [Microsoft.DocumentDB](#microsoftdocumentdb) |
| [Microsoft.Sql](#microsoftsql) |
| [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine) |
| **分析** |
| [Microsoft.AnalysisServices](#microsoftanalysisservices) |
| [Microsoft.Databricks](#microsoftdatabricks) |
| [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics) |
| [Microsoft.DataLakeStore](#microsoftdatalakestore) |
| [Microsoft.EventHub](#microsofteventhub) |
| [Microsoft.HDInsight](#microsofthdinsight) |
| [Microsoft.Kusto](#microsoftkusto) |
| [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated) |
| [Microsoft.StreamAnalytics](#microsoftstreamanalytics) |
| **區塊鏈** |
| [Microsoft.Blockchain](#microsoftblockchain) |
| **AI + 機器學習** |
| [Microsoft.BotService](#microsoftbotservice) |
| [Microsoft.CognitiveServices](#microsoftcognitiveservices) |
| [Microsoft.MachineLearning](#microsoftmachinelearning) |
| [Microsoft.MachineLearningServices](#microsoftmachinelearningservices) |
| **物聯網** |
| [Microsoft.Devices](#microsoftdevices) |
| [Microsoft.IoTCentral](#microsoftiotcentral) |
| [Microsoft.NotificationHubs](#microsoftnotificationhubs) |
| [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights) |
| **混合實境** |
| [Microsoft.IoTSpaces](#microsoftiotspaces) |
| [Microsoft.MixedReality](#microsoftmixedreality) |
| **整合** |
| [Microsoft.ApiManagement](#microsoftapimanagement) |
| [Microsoft.AppConfiguration](#microsoftappconfiguration) |
| [Microsoft.AzureStack](#microsoftazurestack) |
| [Microsoft.DataBoxEdge](#microsoftdataboxedge) |
| [Microsoft.DataCatalog](#microsoftdatacatalog) |
| [Microsoft.EventGrid](#microsofteventgrid) |
| [Microsoft.Logic](#microsoftlogic) |
| [Microsoft.Relay](#microsoftrelay) |
| [Microsoft.ServiceBus](#microsoftservicebus) |
| **身分識別** |
| [Microsoft.AAD](#microsoftaad) |
| [microsoft.aadiam](#microsoftaadiam) |
| [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice) |
| [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory) |
| [Microsoft.ManagedIdentity](#microsoftmanagedidentity) |
| **安全性** |
| [Microsoft.KeyVault](#microsoftkeyvault) |
| [Microsoft.Security](#microsoftsecurity) |
| [Microsoft.SecurityGraph](#microsoftsecuritygraph) |
| [Microsoft.SecurityInsights](#microsoftsecurityinsights) |
| **DevOps** |
| [Microsoft.DevTestLab](#microsoftdevtestlab) |
| [Microsoft.LabServices](#microsoftlabservices) |
| [VisualStudio](#microsoftvisualstudio) |
| **移轉** |
| [Microsoft.Migrate](#microsoftmigrate) |
| [Microsoft.OffAzure](#microsoftoffazure) |
| **監視** |
| [Microsoft.AlertsManagement](#microsoftalertsmanagement) |
| [Microsoft.Insights](#microsoftinsights) |
| [Microsoft.OperationalInsights](#microsoftoperationalinsights) |
| [Microsoft.OperationsManagement](#microsoftoperationsmanagement) |
| [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor) |
| **管理和治理** |
| [Microsoft.Advisor](#microsoftadvisor) |
| [Microsoft.Authorization](#microsoftauthorization) |
| [Microsoft.Automation](#microsoftautomation) |
| [Microsoft.Batch](#microsoftbatch) |
| [Microsoft.Billing](#microsoftbilling) |
| [Microsoft.Blueprint](#microsoftblueprint) |
| [Microsoft.Capacity](#microsoftcapacity) |
| [Microsoft.Commerce](#microsoftcommerce) |
| [Microsoft.Consumption](#microsoftconsumption) |
| [Microsoft.CostManagement](#microsoftcostmanagement) |
| [Microsoft.Features](#microsoftfeatures) |
| [Microsoft.GuestConfiguration](#microsoftguestconfiguration) |
| [Microsoft.HybridCompute](#microsofthybridcompute) |
| [Microsoft.Kubernetes](#microsoftkubernetes) |
| [Microsoft.ManagedServices](#microsoftmanagedservices) |
| [Microsoft.Management](#microsoftmanagement) |
| [Microsoft.PolicyInsights](#microsoftpolicyinsights) |
| [Microsoft.Portal](#microsoftportal) |
| [Microsoft.RecoveryServices](#microsoftrecoveryservices) |
| [Microsoft.Resources](#microsoftresources) |
| [Microsoft.Scheduler](#microsoftscheduler) |
| [Microsoft.Solutions](#microsoftsolutions) |
| [Microsoft.Subscription](#microsoftsubscription) |
| **Intune** |
| [Microsoft.Intune](#microsoftintune) |
| **其他** |
| [Microsoft.BingMaps](#microsoftbingmaps) |
| [Microsoft.DigitalTwins](#microsoftdigitaltwins) |


## <a name="general"></a>一般

### <a name="microsoftaddons"></a>Microsoft.Addons

Azure 服務：核心

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Addons/register/action | 向 Microsoft.Addons 註冊指定的訂用帳戶 |
> | Microsoft.Addons/operations/read | 取得支援的 RP 作業。 |
> | Microsoft.Addons/supportProviders/listsupportplaninfo/action | 列出指定訂用帳戶目前的支援方案資訊。 |
> | Microsoft.Addons/supportProviders/supportPlanTypes/read | 取得指定的 Canonical 支援方案狀態。 |
> | Microsoft.Addons/supportProviders/supportPlanTypes/write | 新增指定的 Canonical 支援方案類型。 |
> | Microsoft.Addons/supportProviders/supportPlanTypes/delete | 移除指定的 Canonical 支援方案 |

### <a name="microsoftmarketplace"></a>Microsoft.Marketplace

Azure 服務：核心

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Marketplace/register/action | 在訂用帳戶中註冊 Microsoft.Marketplace 資源提供者。 |
> | Microsoft. Marketplace/privateStores/action | 更新 Privatestore 專案。 |
> | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | 傳回協議。 |
> | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | 接受已簽署的協議。 |
> | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | 傳回組態。 |
> | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | 儲存組態。 |
> | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | 將映像匯入至終端使用者的 ACR。 |
> | Microsoft. Marketplace/privateStores/write | 建立 Privatestore 專案。 |
> | Microsoft. Marketplace/privateStores/刪除 | 刪除 Privatestore 專案。 |
> | Microsoft Marketplace/privateStores/優惠/動作 | Privatestore 專案中的更新供應專案。 |
> | Microsoft. Marketplace/privateStores/read | 讀取 PrivateStores。 |
> | Microsoft Marketplace/privateStores/優惠/撰寫 | 在 Privatestore 專案中建立供應專案。 |
> | Microsoft. Marketplace/privateStores/優惠/刪除 | 從 Privatestore 專案刪除供應專案。 |
> | Microsoft. Marketplace/privateStores/優惠/讀取 | 讀取 Privatestore 專案優惠。 |

### <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

Azure 服務：核心

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.MarketplaceApps/ClassicDevServices/read | 在傳統開發服務上進行 GET 作業。 |
> | Microsoft.MarketplaceApps/ClassicDevServices/delete | 在傳統開發服務資源上進行 DELETE 作業。 |
> | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | 取得傳統開發服務的單一登入 URL。 |
> | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | 取得傳統開發服務資源管理金鑰。 |
> | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | 產生傳統開發服務資源管理金鑰。 |
> | Microsoft.MarketplaceApps/Operations/read | 讀取所有資源類型的作業。 |

### <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

Azure 服務：核心

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.MarketplaceOrdering/agreements/read | 傳回指定訂用帳戶下的所有合約 |
> | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | 傳回給定 Marketplace 項目的合約 |
> | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | 簽署給定 Marketplace 項目的合約 |
> | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | 取消給定 Marketplace 項目的合約 |
> | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | 取得指定 Marketplace 虛擬機器項目的合約 |
> | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | 簽署或取消指定 Marketplace 虛擬機器項目的合約 |
> | MarketplaceOrdering/operations/read | 列出 API 中所有可能的作業 |

### <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

Azure 服務： [Azure 服務健康狀態](../service-health/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.ResourceHealth/register/action | 為 Microsoft ResourceHealth 註冊訂用帳戶 |
> | Microsoft.ResourceHealth/unregister/action | 取消註冊 Microsoft ResourceHealth 的訂用帳戶 |
> | Microsoft.Resourcehealth/healthevent/action | 代表指定資源健康狀態的變更 |
> | Microsoft.ResourceHealth/AvailabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | 取得指定資源的可用性狀態 |
> | ResourceHealth/emergingissues/read | 取得 Azure 服務的新問題 |
> | Microsoft.ResourceHealth/events/read | 取得指定訂用帳戶的服務健康狀態事件 |
> | Microsoft.Resourcehealth/healthevent/Activated/action | 代表指定資源健康狀態的變更 |
> | Microsoft.Resourcehealth/healthevent/Updated/action | 代表指定資源健康狀態的變更 |
> | Microsoft.Resourcehealth/healthevent/Resolved/action | 代表指定資源健康狀態的變更 |
> | Microsoft.Resourcehealth/healthevent/InProgress/action | 代表指定資源健康狀態的變更 |
> | Microsoft.Resourcehealth/healthevent/Pending/action | 代表指定資源健康狀態的變更 |
> | Microsoft.ResourceHealth/impactedResources/read | 取得指定訂用帳戶的受影響資源 |
> | ResourceHealth/中繼資料/讀取 | 取得中繼資料 |
> | ResourceHealth/通知/讀取 | 接收 Azure Resource Manager 通知 |
> | Microsoft.ResourceHealth/Operations/read | 取得 Microsoft ResourceHealth 可用的作業 |

### <a name="microsoftsupport"></a>Microsoft.Support

Azure 服務：核心

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Support/register/action | 註冊支援資源提供者 |
> | Microsoft. Support/checkNameAvailability/action | 檢查名稱是否有效，且不會用於資源類型 |
> | Microsoft. 支援/operationresults/讀取 | 取得非同步作業的結果 |
> | Microsoft. 支援/作業/讀取 | 列出可在 Microsoft 上使用的所有作業。支援資源提供者 |
> | Microsoft. 支援/operationsstatus/讀取 | 取得非同步作業的狀態 |
> | Microsoft. 支援/服務/讀取 | 列出一或所有可支援的 Azure 服務 |
> | Microsoft。支援/服務/problemClassifications/讀取 | 列出一或多個 Azure 服務的問題分類 |
> | Microsoft.Support/supportTickets/read | 列出一或所有支援票證 |
> | Microsoft.Support/supportTickets/write | 允許建立和更新支援票證 |
> | Microsoft. 支援/>microsoft.office365.supporttickets/通訊/讀取 | 列出一或所有支援票證通訊 |
> | Microsoft. 支援/>microsoft.office365.supporttickets/通訊/寫入 | 將新的通訊新增至支援票證 |

## <a name="compute"></a>計算

### <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

Azure 服務：傳統部署模型虛擬機器

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.ClassicCompute/register/action | 傳統計算的暫存器 |
> | Microsoft.ClassicCompute/checkDomainNameAvailability/action | 檢查給定網域名稱的可用性。 |
> | Microsoft.ClassicCompute/moveSubscriptionResources/action | 將所有傳統資源移動到不同訂用帳戶。 |
> | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | 驗證訂用帳戶是否可進行傳統移動作業。 |
> | Microsoft.ClassicCompute/capabilities/read | 顯示功能 |
> | Microsoft.ClassicCompute/checkDomainNameAvailability/read | 取得指定網域名稱的可用性。 |
> | Microsoft.ClassicCompute/domainNames/read | 傳回資源的網域名稱。 |
> | Microsoft.ClassicCompute/domainNames/write | 新增或修改資源的網域名稱。 |
> | Microsoft.ClassicCompute/domainNames/delete | 移除資源的網域名稱。 |
> | Microsoft.ClassicCompute/domainNames/swap/action | 將預備位置切換到生產位置。 |
> | Microsoft.ClassicCompute/domainNames/active/write | 設定作用中的網域名稱。 |
> | Microsoft.ClassicCompute/domainNames/availabilitySets/read | 顯示資源的可用性設定組。 |
> | Microsoft.ClassicCompute/domainNames/capabilities/read | 顯示網域名稱功能 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/read | 顯示部署位置。 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/write | 建立或更新部署。 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | 取得網域名稱之部署位置上的角色 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | 取得網域名稱之部署位置上的角色執行個體 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | 取得部署位置狀態。 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | 新增部署位置狀態。 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | 取得網域名稱上之部署位置的升級網域 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | 更新網域名稱上之部署位置的升級網域 |
> | Microsoft.ClassicCompute/domainNames/extensions/read | 傳回網域名稱副檔名。 |
> | Microsoft.ClassicCompute/domainNames/extensions/write | 新增網域名稱副檔名。 |
> | Microsoft.ClassicCompute/domainNames/extensions/delete | 移除網域名稱副檔名。 |
> | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | 讀取網域名稱副檔名的作業狀態。 |
> | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | 取得內部負載平衡器。 |
> | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | 建立新的內部負載平衡。 |
> | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | 移除新的內部負載平衡。 |
> | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | 讀取網域名稱內部負載平衡器的作業狀態。 |
> | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | 取得已負載平衡的端點集。 |
> | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | 新增已負載平衡的端點集。 |
> | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | 讀取網域名稱之已負載平衡端點集的作業狀態。 |
> | Microsoft.ClassicCompute/domainNames/operationstatuses/read | 取得網域名稱的作業狀態。 |
> | Microsoft.ClassicCompute/domainNames/operationStatuses/read | 讀取網域名稱副檔名的作業狀態。 |
> | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | 傳回所使用的服務憑證。 |
> | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | 新增或修改所使用的服務憑證。 |
> | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | 刪除所使用的服務憑證。 |
> | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | 讀取網域名稱服務憑證的作業狀態。 |
> | Microsoft.ClassicCompute/domainNames/slots/read | 顯示部署位置。 |
> | Microsoft.ClassicCompute/domainNames/slots/write | 建立或更新部署。 |
> | Microsoft.ClassicCompute/domainNames/slots/delete | 刪除給定的部署位置。 |
> | Microsoft.ClassicCompute/domainNames/slots/start/action | 啟動部署位置。 |
> | Microsoft.ClassicCompute/domainNames/slots/stop/action | 暫止部署位置。 |
> | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | 驗證部署位置的移轉。 |
> | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | 準備部署位置的移轉。 |
> | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | 認可部署位置的移轉。 |
> | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | 中止部署位置的移轉。 |
> | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | 讀取網域名稱位置的作業狀態。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/read | 取得部署位置的角色。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/write | 新增部署位置的角色。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | 傳回部署位置角色的擴充功能參考。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | 新增或修改部署位置角色的擴充功能參考。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | 移除部署位置角色的擴充功能參考。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | 讀取網域名稱位置角色擴充功能參考的作業狀態。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | 取得網域名稱的角色計量定義。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | 取得網域名稱的角色計量。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | 取得網域名稱位置角色的作業狀態。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | 取得診斷設定。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | 取得計量定義。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | 下載網域名稱位置角色上角色執行個體的遠端桌面連線檔案。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | 取得角色執行個體。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | 重新啟動角色執行個體。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | 針對角色執行個體重新安裝映像。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | 重建角色執行個體。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | 取得網域名稱位置角色上角色執行個體的作業狀態。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | 取得部署位置的角色 SKU。 |
> | Microsoft.ClassicCompute/domainNames/slots/state/start/write | 將部署位置狀態變更為停止。 |
> | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | 將部署位置狀態變更為啟動。 |
> | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | 逐步升級網域。 |
> | Microsoft.ClassicCompute/operatingSystemFamilies/read | 列出可在 Microsoft Azure 使用的客體作業系統系列，同時也會列出各系列適用的作業系統版本。 |
> | Microsoft.ClassicCompute/operatingSystems/read | 列出 Microsoft Azure 中目前可用的客體作業系統版本。 |
> | Microsoft.ClassicCompute/operations/read | 取得作業的清單。 |
> | Microsoft.ClassicCompute/operationStatuses/read | 讀取資源的作業狀態。 |
> | Microsoft.ClassicCompute/quotas/read | 取得訂用帳戶配額。 |
> | Microsoft.ClassicCompute/resourceTypes/skus/read | 取得受支援之資源類型的 SKU 清單。 |
> | Microsoft.ClassicCompute/virtualMachines/read | 擷取虛擬機器清單。 |
> | Microsoft.ClassicCompute/virtualMachines/write | 新增或修改虛擬機器。 |
> | Microsoft.ClassicCompute/virtualMachines/delete | 移除虛擬機器。 |
> | Microsoft.ClassicCompute/virtualMachines/capture/action | 擷取虛擬機器。 |
> | Microsoft.ClassicCompute/virtualMachines/start/action | 啟動虛擬機器。 |
> | Microsoft.ClassicCompute/virtualMachines/redeploy/action | 重新部署虛擬機器。 |
> | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | 執行虛擬機器的維護。 |
> | Microsoft.ClassicCompute/virtualMachines/restart/action | 重新啟動虛擬機器。 |
> | Microsoft.ClassicCompute/virtualMachines/stop/action | 停止虛擬機器。 |
> | Microsoft.ClassicCompute/virtualMachines/shutdown/action | 關閉虛擬機器。 |
> | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | 將資料磁碟連結至虛擬機器。 |
> | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | 中斷資料磁碟對虛擬機器的連結。 |
> | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | 下載虛擬機器的 RDP 檔案。 |
> | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | 取得與虛擬機器相關聯的網路安全性群組。 |
> | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | 新增與虛擬機器相關聯的網路安全性群組。 |
> | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | 刪除與虛擬機器相關聯的網路安全性群組。 |
> | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | 讀取與網路安全性群組相關聯之虛擬機器的作業狀態。 |
> | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | 取得可能的非同步作業 |
> | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | 取得虛擬機器診斷設定。 |
> | Microsoft.ClassicCompute/virtualMachines/disks/read | 擷取資料磁碟清單 |
> | Microsoft.ClassicCompute/virtualMachines/extensions/read | 取得虛擬機器擴充功能。 |
> | Microsoft.ClassicCompute/virtualMachines/extensions/write | 放置虛擬機器擴充功能。 |
> | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | 讀取虛擬機器擴充功能的作業狀態。 |
> | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | 取得虛擬機器計量定義。 |
> | Microsoft.ClassicCompute/virtualMachines/metrics/read | 取得計量。 |
> | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | 取得與網路介面相關聯的網路安全性群組。 |
> | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | 新增與網路介面相關聯的網路安全性群組。 |
> | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | 刪除與網路介面相關聯的網路安全性群組。 |
> | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | 讀取與網路安全性群組相關聯之虛擬機器的作業狀態。 |
> | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | 讀取虛擬機器的作業狀態。 |
> | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | 取得診斷設定。 |
> | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | 取得計量定義。 |

### <a name="microsoftcompute"></a>Microsoft.Compute

Azure 服務： [虛擬機器](../virtual-machines/index.yml)、 [虛擬機器擴展集](../virtual-machine-scale-sets/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Compute/register/action | 向 Microsoft.Compute 資源提供者註冊訂用帳戶 |
> | Microsoft. 計算/取消註冊/動作 | 使用 Microsoft 註冊訂用帳戶。計算資源提供者 |
> | Microsoft.Compute/availabilitySets/read | 取得可用性設定組的屬性 |
> | Microsoft.Compute/availabilitySets/write | 建立新的可用性設定組，或更新現有的可用性設定組 |
> | Microsoft.Compute/availabilitySets/delete | 刪除可用性設定組 |
> | Microsoft.Compute/availabilitySets/vmSizes/read | 列出在可用性設定組中建立或更新虛擬機器時可以使用的大小 |
> | Microsoft. Compute/cloudServices/read | 取得 CloudService 的屬性。 |
> | Microsoft. Compute/cloudServices/write | 建立新的 CloudService 或更新現有的。 |
> | Microsoft. Compute/cloudServices/delete | 刪除 CloudService。 |
> | Microsoft. 計算/cloudServices/關機/動作 | 關閉 CloudService 的電源。 |
> | Microsoft. Compute/cloudServices/start/action | 啟動 CloudService。 |
> | Microsoft. 計算/cloudServices/重新開機/動作 | 重新開機 CloudService 中的一個或多個角色實例。 |
> | Microsoft. 計算/cloudServices/重新安裝映射/動作 | 重建 CloudService 中角色實例的所有磁片。 |
> | Microsoft. 計算/cloudServices/重建/動作 | 重新安裝 CloudService 中所有角色實例的映射。 |
> | Microsoft. 計算/cloudServices/刪除/動作 | 刪除 CloudService 中的角色實例。 |
> | Microsoft. Compute/cloudServices/instanceView/read | 取得 CloudService 的狀態。 |
> | Microsoft. Compute/cloudServices/roleInstances/delete | 從 CloudService 刪除 RoleInstance。 |
> | Microsoft. Compute/cloudServices/roleInstances/read | 從 CloudService 取得 RoleInstance。 |
> | Microsoft. Compute/cloudServices/roleInstances/restart/action | 重新開機 CloudService 的角色實例 |
> | Microsoft. Compute/cloudServices/roleInstances/重新安裝映射/動作 | 重新安裝 CloudService 角色實例的映射。 |
> | Microsoft. Compute/cloudServices/roleInstances/rebuild/action | 重建 CloudService 中的所有磁片。 |
> | Microsoft. Compute/cloudServices/roleInstances/instanceView/read | 從 CloudService 取得角色實例的狀態。 |
> | Microsoft. 計算/cloudServices/角色/讀取 | 從 CloudService 取得角色。 |
> | Microsoft. 計算/cloudServices/角色/寫入 | 調整角色中的實例 |
> | Microsoft. Compute/cloudServices/updateDomains/read | 取得 CloudService 中所有更新網域的清單。 |
> | Microsoft. Compute/diskAccesses/read | 取得 DiskAccess 資源的屬性 |
> | Microsoft. Compute/diskAccesses/write | 建立新的 DiskAccess 資源或更新現有資源 |
> | Microsoft. Compute/diskAccesses/delete | 刪除 DiskAccess 資源 |
> | Microsoft. Compute/diskAccesses/privateEndpointConnectionsApproval/action | 核准私人端點連線 |
> | Microsoft. Compute/diskAccesses/privateEndpointConnectionProxies/read | 取得私人端點連線 proxy 的屬性 |
> | Microsoft. Compute/diskAccesses/privateEndpointConnectionProxies/write | 建立新的私人端點連線 Proxy |
> | Microsoft. Compute/diskAccesses/privateEndpointConnectionProxies/delete | 刪除私人端點連線 Proxy |
> | Microsoft. Compute/diskAccesses/privateEndpointConnectionProxies/validate/action | 驗證私人端點連線 Proxy 物件 |
> | Microsoft. Compute/diskAccesses/privateEndpointConnections/delete | 刪除私人端點連線 |
> | Microsoft. Compute/diskEncryptionSets/read | 取得磁片加密集的屬性 |
> | Microsoft. Compute/diskEncryptionSets/write | 建立新的磁片加密集或更新現有的磁片加密集 |
> | Microsoft. Compute/diskEncryptionSets/delete | 刪除磁片加密集 |
> | Microsoft.Compute/disks/read | 取得磁碟的屬性 |
> | Microsoft.Compute/disks/write | 建立新的磁碟，或更新現有磁碟 |
> | Microsoft.Compute/disks/delete | 刪除磁碟 |
> | Microsoft.Compute/disks/beginGetAccess/action | 取得磁碟用於 Blob 存取的 SAS URI |
> | Microsoft.Compute/disks/endGetAccess/action | 撤銷磁碟的 SAS URI |
> | Microsoft.Compute/galleries/read | 取得資源庫的屬性 |
> | Microsoft.Compute/galleries/write | 建立新的資源庫或更新現有的資源庫 |
> | Microsoft.Compute/galleries/delete | 刪除資源庫 |
> | Microsoft. 計算/資源庫/共用/動作 | 共用資源庫至不同的範圍 |
> | Microsoft。計算/資源庫/應用程式/讀取 | 取得資源庫應用程式的屬性 |
> | Microsoft. 計算/資源庫/應用程式/寫入 | 建立新的資源庫應用程式，或更新現有的資源庫應用程式 |
> | Microsoft. 計算/資源庫/應用程式/刪除 | 刪除資源庫應用程式 |
> | Microsoft. 計算/資源庫/應用程式/版本/讀取 | 取得資源庫應用程式版本的屬性 |
> | Microsoft. 計算/資源庫/應用程式/版本/寫入 | 建立新的資源庫應用程式版本，或更新現有的應用程式版本 |
> | Microsoft。計算/資源庫/應用程式/版本/刪除 | 刪除資源庫應用程式版本 |
> | Microsoft.Compute/galleries/images/read | 取得資源庫映像的屬性 |
> | Microsoft.Compute/galleries/images/write | 建立新的資源庫映像或更新現有的資源庫映像 |
> | Microsoft.Compute/galleries/images/delete | 刪除資源庫映像 |
> | Microsoft.Compute/galleries/images/versions/read | 取得資源庫映像版本的屬性 |
> | Microsoft.Compute/galleries/images/versions/write | 建立新的資源庫映像版本或更新現有的資源庫映像版本 |
> | Microsoft.Compute/galleries/images/versions/delete | 刪除資源庫映像版本 |
> | Microsoft. Compute/hostGroups/read | 取得主機群組的屬性 |
> | Microsoft. Compute/hostGroups/write | 建立新的主機群組，或更新現有的主機群組 |
> | Microsoft. Compute/hostGroups/delete | 刪除主機群組 |
> | Microsoft. Compute/hostGroups/hosts/read | 取得主機的屬性 |
> | Microsoft. Compute/hostGroups/hosts/write | 建立新的主機或更新現有的主機 |
> | Microsoft. Compute/hostGroups/hosts/delete | 刪除主機 |
> | Microsoft.Compute/images/read | 取得映像的屬性 |
> | Microsoft.Compute/images/write | 建立新的映像，或更新現有映像 |
> | Microsoft.Compute/images/delete | 刪除映像 |
> | Microsoft.Compute/locations/capsOperations/read | 取得非同步 Caps 作業的狀態 |
> | Microsoft.Compute/locations/diskOperations/read | 取得非同步磁碟作業的狀態 |
> | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | 建立記錄以依時間間隔顯示要求總數以協助進行節流診斷。 |
> | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | 建立記錄以依 ResourceName、OperationName 或已套用的節流原則顯示已節流要求彙總。 |
> | Microsoft.Compute/locations/operations/read | 取得非同步作業的狀態 |
> | Microsoft. 計算/位置/privateEndpointConnectionProxyAzureAsyncOperation/讀取 | 取得非同步私人端點連接 Proxy 作業的狀態 |
> | Microsoft. 計算/位置/privateEndpointConnectionProxyOperationResults/讀取 | 取得私人端點連線 Proxy 操作的結果 |
> | Microsoft.Compute/locations/publishers/read | 取得發行者的屬性 |
> | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | 取得平台映像提供的屬性 |
> | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | 取得平台映像 SKU 的屬性 |
> | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | 取得平台映像版本的屬性 |
> | Microsoft.Compute/locations/publishers/artifacttypes/types/read | 取得 VMExtension 類型的屬性 |
> | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | 取得 VMExtension 版本的屬性 |
> | Microsoft.Compute/locations/runCommands/read | 列出位置中可用的執行命令 |
> | Microsoft.Compute/locations/usages/read | 取得訂用帳戶在某個位置之計算資源的服務限制和目前的使用量 |
> | Microsoft.Compute/locations/vmSizes/read | 列出某個位置可用的虛擬機器大小 |
> | Microsoft. 計算/位置/vsmOperations/讀取 | 使用虛擬機器執行時間服務延伸模組，取得虛擬機器擴展集的非同步作業狀態 |
> | Microsoft.Compute/operations/read | 列出 Microsoft.Compute 資源提供者的可用作業 |
> | Microsoft. Compute/proximityPlacementGroups/read | 取得鄰近位置群組的屬性 |
> | Microsoft. Compute/proximityPlacementGroups/write | 建立新的鄰近位置群組或更新現有的鄰近位置群組 |
> | Microsoft. Compute/proximityPlacementGroups/delete | 刪除鄰近位置群組 |
> | Microsoft.Compute/restorePointCollections/read | 取得還原點集合的屬性 |
> | Microsoft.Compute/restorePointCollections/write | 建立新的還原點集合，或更新現有還原點集合 |
> | Microsoft.Compute/restorePointCollections/delete | 刪除還原點集合和內含的還原點 |
> | Microsoft.Compute/restorePointCollections/restorePoints/read | 取得還原點的屬性 |
> | Microsoft.Compute/restorePointCollections/restorePoints/write | 建立新的還原點 |
> | Microsoft.Compute/restorePointCollections/restorePoints/delete | 刪除還原點 |
> | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | 取得還原點屬性以及 Blob SAS URI |
> | Microsoft. Compute/sharedVMExtensions/read | 取得共用 VM 延伸模組的屬性 |
> | Microsoft. Compute/sharedVMExtensions/write | 建立新的共用 VM 延伸模組，或更新現有的共用 VM 延伸模組 |
> | Microsoft. Compute/sharedVMExtensions/delete | 刪除共用 VM 延伸模組 |
> | Microsoft. Compute/sharedVMExtensions/版本/讀取 | 取得共用 VM 延伸模組版本的屬性 |
> | Microsoft. Compute/sharedVMExtensions/版本/寫入 | 建立新的共用 VM 延伸模組版本，或更新現有的版本 |
> | Microsoft. Compute/sharedVMExtensions/版本/刪除 | 刪除共用 VM 擴充功能版本 |
> | Microsoft.Compute/sharedVMImages/read | 取得 SharedVMImage 的屬性 |
> | Microsoft.Compute/sharedVMImages/write | 建立新的 SharedVMImage，或更新現有的 SharedVMImage |
> | Microsoft.Compute/sharedVMImages/delete | 刪除 SharedVMImage |
> | Microsoft.Compute/sharedVMImages/versions/read | 取得 SharedVMImageVersion 的屬性 |
> | Microsoft.Compute/sharedVMImages/versions/write | 建立新的 SharedVMImageVersion，或更新現有的 SharedVMImageVersion |
> | Microsoft.Compute/sharedVMImages/versions/delete | 刪除 SharedVMImageVersion |
> | Microsoft.Compute/sharedVMImages/versions/replicate/action | 將 SharedVMImageVersion 複寫至目標區域 |
> | Microsoft.Compute/skus/read | 取得可供您的訂用帳戶使用的 Microsoft.Compute SKU 清單 |
> | Microsoft.Compute/snapshots/read | 取得快照集的屬性 |
> | Microsoft.Compute/snapshots/write | 建立新的快照集，或更新現有快照集 |
> | Microsoft.Compute/snapshots/delete | 刪除快照集 |
> | Microsoft.Compute/snapshots/beginGetAccess/action | 取得快照集的 SAS URI 以用於 Blob 存取 |
> | Microsoft.Compute/snapshots/endGetAccess/action | 撤銷快照集的 SAS URI |
> | Microsoft. Compute/sshPublicKeys/read | 取得 SSH 公開金鑰的屬性 |
> | Microsoft. Compute/sshPublicKeys/write | 建立新的 SSH 公開金鑰，或更新現有的 SSH 公用金鑰 |
> | Microsoft. Compute/sshPublicKeys/delete | 刪除 SSH 公開金鑰 |
> | Microsoft. Compute/sshPublicKeys/generateKeyPair/action | 產生新的 SSH 公開/私密金鑰組 |
> | Microsoft.Compute/virtualMachines/read | 取得虛擬機器的屬性 |
> | Microsoft.Compute/virtualMachines/write | 建立新的虛擬機器，或更新現有虛擬機器 |
> | Microsoft.Compute/virtualMachines/delete | 刪除虛擬機器 |
> | Microsoft.Compute/virtualMachines/start/action | 啟動虛擬機器 |
> | Microsoft.Compute/virtualMachines/powerOff/action | 關閉虛擬機器的電源。 請注意，您會繼續收到虛擬機器的帳單。 |
> | Microsoft. 計算/virtualMachines/重新套用/動作 | 重新套用虛擬機器的目前模型 |
> | Microsoft.Compute/virtualMachines/redeploy/action | 重新部署虛擬機器 |
> | Microsoft.Compute/virtualMachines/restart/action | 重新啟動虛擬機器 |
> | Microsoft. Compute/virtualMachines/retrieveBootDiagnosticsData/action | 抓取開機診斷記錄 blob Uri |
> | Microsoft.Compute/virtualMachines/deallocate/action | 關閉虛擬機器的電源，並將計算資源釋出 |
> | Microsoft.Compute/virtualMachines/generalize/action | 將虛擬機器的狀態設定為「已一般化」，並準備虛擬機器以供擷取 |
> | Microsoft.Compute/virtualMachines/capture/action | 藉由複製虛擬硬碟來擷取虛擬機器，並產生可用來建立類似虛擬機器的範本 |
> | Microsoft.Compute/virtualMachines/runCommand/action | 在虛擬機器上執行預先定義的指令碼 |
> | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | 將虛擬機器的 Blob 型磁碟轉換為受控磁碟 |
> | Microsoft.Compute/virtualMachines/performMaintenance/action | 在 VM 上執行維護作業。 |
> | Microsoft.Compute/virtualMachines/reimage/action | 為正在使用差異磁碟的虛擬機器重新安裝映像。 |
> | Microsoft. Compute/virtualMachines/installPatches/action | 根據使用者提供的參數，在虛擬機器上安裝可用的 OS 更新修補程式。 包含可用修補程式清單的評量結果也會在此過程中重新整理。 |
> | Microsoft. Compute/virtualMachines/assessPatches/action | 評估虛擬機器，並尋找其可用 OS 更新修補程式的清單。 |
> | Microsoft. Compute/virtualMachines/cancelPatchInstallation/action | 取消虛擬機器上進行中的安裝作業系統更新修補作業。 |
> | Microsoft. Compute/virtualMachines/simulateEviction/action | 模擬找出現成的虛擬機器 |
> | Microsoft.Compute/virtualMachines/extensions/read | 取得虛擬機器擴充功能的屬性 |
> | Microsoft.Compute/virtualMachines/extensions/write | 建立新的虛擬機器擴充功能，或更新現有的虛擬機器擴充功能 |
> | Microsoft.Compute/virtualMachines/extensions/delete | 刪除虛擬機器擴充功能 |
> | Microsoft.Compute/virtualMachines/instanceView/read | 取得虛擬機器和其資源的詳細執行階段狀態 |
> | Microsoft.Compute/virtualMachines/vmSizes/read | 列出虛擬機器所能更新成的大小 |
> | Microsoft.Compute/virtualMachineScaleSets/read | 取得虛擬機器擴展集的屬性 |
> | Microsoft.Compute/virtualMachineScaleSets/write | 建立新的虛擬機器擴展集或更新現有的 |
> | Microsoft.Compute/virtualMachineScaleSets/delete | 刪除虛擬機器擴展集 |
> | Microsoft.Compute/virtualMachineScaleSets/delete/action | 刪除虛擬機器擴展集的執行個體 |
> | Microsoft.Compute/virtualMachineScaleSets/start/action | 啟動虛擬機器擴展集的執行個體 |
> | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | 關閉虛擬機器擴展集執行個體的電源 |
> | Microsoft.Compute/virtualMachineScaleSets/restart/action | 重新啟動虛擬機器擴展集的執行個體 |
> | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | 關閉虛擬機器擴展集之執行個體的電源，並將其計算資源釋出  |
> | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | 將虛擬機器擴展集的執行個體手動更新至最新模型 |
> | Microsoft.Compute/virtualMachineScaleSets/reimage/action | 重新安裝虛擬機器擴展集執行個體的映像 |
> | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | 為虛擬機器擴展集之執行個體的所有磁碟重新安裝映像 (OS 磁碟和資料磁碟)  |
> | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | 重新部署虛擬機器擴展集的執行個體 |
> | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | 在虛擬機器擴展集的執行個體上執行規劃的維護 |
> | Microsoft.Compute/virtualMachineScaleSets/scale/action | 驗證現有的虛擬機器擴展集是否可以相應縮小/相應放大到指定的執行個體計數 |
> | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | 手動徹查 Service Fabric 虛擬機器擴展集的平台更新網域，以完成停滯的暫止更新 |
> | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | 開始輪流升級，以將所有虛擬機器擴展集執行個體移至最新可用的平台映像作業系統版本。 |
> | Microsoft. Compute/virtualMachineScaleSets/setOrchestrationServiceState/action | 根據作業輸入中提供的動作，設定協調流程服務的狀態。 |
> | Microsoft. Compute/virtualMachineScaleSets/rollingUpgrades/action | 取消虛擬機器擴展集的輪流升級 |
> | Microsoft.Compute/virtualMachineScaleSets/extensions/read | 取得虛擬機器擴展集延伸模組的屬性 |
> | Microsoft.Compute/virtualMachineScaleSets/extensions/write | 建立新的虛擬機器擴展集延伸模組或更新現有的 |
> | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | 刪除虛擬機器擴展集延伸模組 |
> | Microsoft. 計算/virtualMachineScaleSets/擴充功能/角色/讀取 | 使用虛擬機器執行時間服務延伸模組，取得虛擬機器擴展集中角色的屬性 |
> | Microsoft. 計算/virtualMachineScaleSets/擴充/角色/寫入 | 使用虛擬機器執行時間服務延伸模組，更新虛擬機器擴展集中現有角色的屬性 |
> | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | 取得虛擬機器擴展集的執行個體檢視 |
> | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | 取得虛擬機器擴展集之所有網路介面的屬性 |
> | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | 取得虛擬機器擴展集的 OS 升級歷程記錄 |
> | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | 取得虛擬機器擴展集之所有公用 IP 位址的屬性 |
> | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | 取得虛擬機器擴展集最新的輪流升級狀態 |
> | Microsoft.Compute/virtualMachineScaleSets/skus/read | 列出現有虛擬機器擴展集的有效 SKU |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | 擷取 VM 擴展集內的虛擬機器屬性 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | 更新 VM 擴展集中虛擬機器的屬性 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | 刪除 VM 擴展集內的特定虛擬機器。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | 啟動 VM 擴展集內的虛擬機器執行個體。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | 將 VM 擴展集內的虛擬機器執行個體關閉電源。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | 重新啟動 VM 擴展集內的虛擬機器執行個體。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | 關閉 VM 擴展集之虛擬機器的電源，並將其計算資源釋出。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | 重新安裝虛擬機器擴展集中的虛擬機器執行個體映像。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | 為虛擬機器擴展集中虛擬機器執行個體的所有磁碟 (OS 磁碟和資料磁碟) 重新安裝映像。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | 重新部署虛擬機器擴展集中的虛擬機器執行個體 |
> | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/retrieveBootDiagnosticsData/action | 抓取虛擬機器擴展集中虛擬機器實例的開機診斷記錄 blob Uri |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | 在虛擬機器擴展集的虛擬機器執行個體上執行規劃的維護 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | 在虛擬機器擴展集中的虛擬機器執行個體上執行預先定義的指令碼。 |
> | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/simulateEviction/action | 模擬虛擬機器擴展集中的點虛擬機器收回 |
> | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/extensions/read | 取得虛擬機器擴展集中虛擬機器的延伸模組屬性 |
> | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/extensions/write | 為虛擬機器擴展集中的虛擬機器建立新的擴充功能，或更新現有的延伸模組 |
> | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/extensions/delete | 刪除虛擬機器擴展集中虛擬機器的延伸模組 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | 擷取 VM 擴展集內的虛擬機器執行個體檢視。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | 取得使用虛擬機器擴展集建立的虛擬機器其中一個或所有網路介面屬性 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | 取得使用虛擬機器擴展集建立的網路介面其中一個或所有 IP 設定屬性。 IP 設定代表私人 IP |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | 取得使用虛擬機器擴展集建立的公用 IP 位址屬性。 虛擬機器擴展集最多可以在每個 ipconfiguration (私人 IP) 建立一個公用 IP |
> | Microsoft. Compute/virtualMachineScaleSets/Vmsize/read | 列出在虛擬機器擴展集中建立或更新虛擬機器的可用大小 |
> | **DataAction** | **說明** |
> | Microsoft.Compute/virtualMachines/login/action | 以一般使用者身分登入虛擬機器 |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | 以 Windows 系統管理員或 Linux 根使用者權限登入虛擬機器 |

### <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

Azure 服務： [Service Fabric](../service-fabric/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.ServiceFabric/register/action | 註冊任何動作 |
> | Microsoft.ServiceFabric/clusters/read | 讀取任何叢集 |
> | Microsoft.ServiceFabric/clusters/write | 建立或更新任何叢集 |
> | Microsoft.ServiceFabric/clusters/delete | 刪除任何叢集 |
> | Microsoft.ServiceFabric/clusters/applications/read | 讀取任何應用程式 |
> | Microsoft.ServiceFabric/clusters/applications/write | 建立或更新任何應用程式 |
> | Microsoft.ServiceFabric/clusters/applications/delete | 刪除任何應用程式 |
> | Microsoft.ServiceFabric/clusters/applications/services/read | 讀取任何服務 |
> | Microsoft.ServiceFabric/clusters/applications/services/write | 建立或更新任何服務 |
> | Microsoft.ServiceFabric/clusters/applications/services/delete | 刪除任何服務 |
> | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | 讀取任何分割區 |
> | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | 讀取任何複本 |
> | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | 讀取任何服務狀態 |
> | Microsoft.ServiceFabric/clusters/applicationTypes/read | 讀取任何應用程式類型 |
> | Microsoft.ServiceFabric/clusters/applicationTypes/write | 建立或更新任何應用程式類型 |
> | Microsoft.ServiceFabric/clusters/applicationTypes/delete | 刪除任何應用程式類型 |
> | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | 讀取任何應用程式類型版本 |
> | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | 建立或更新任何應用程式類型版本 |
> | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | 刪除任何應用程式類型版本 |
> | Microsoft.ServiceFabric/clusters/nodes/read | 讀取任何節點 |
> | Microsoft.ServiceFabric/clusters/statuses/read | 讀取任何叢集狀態 |
> | Microsoft.ServiceFabric/locations/clusterVersions/read | 讀取任何叢集版本 |
> | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | 讀取特定環境的任何叢集版本 |
> | Microsoft.ServiceFabric/locations/operationresults/read | 讀取任何作業結果 |
> | Microsoft.ServiceFabric/locations/operations/read | 依位置讀取任何作業 |
> | Microsoft.ServiceFabric/operations/read | 讀取任何可用的作業 |

## <a name="networking"></a>網路功能

### <a name="microsoftcdn"></a>Microsoft.Cdn

Azure 服務： [內容傳遞網路](../cdn/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Cdn/register/action | 為 CDN 資源提供者註冊訂用帳戶，並讓您能夠建立 CDN 設定檔。 |
> | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Microsoft.Cdn/ValidateProbe/action |  |
> | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Microsoft Cdn/cdnwebapplicationfirewallmanagedrulesets/read |  |
> | Microsoft. Cdn/cdnwebapplicationfirewallmanagedrulesets/write |  |
> | Microsoft Cdn/cdnwebapplicationfirewallmanagedrulesets/delete |  |
> | Microsoft Cdn/cdnwebapplicationfirewallpolicies/read |  |
> | Microsoft. Cdn/cdnwebapplicationfirewallpolicies/write |  |
> | Microsoft Cdn/cdnwebapplicationfirewallpolicies/delete |  |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/edgenodes/write |  |
> | Microsoft.Cdn/edgenodes/delete |  |
> | Microsoft.Cdn/operationresults/read |  |
> | Microsoft.Cdn/operationresults/write |  |
> | Microsoft.Cdn/operationresults/delete |  |
> | Microsoft. Cdn/operationresults/cdnwebapplicationfirewallpolicyresults/read |  |
> | Microsoft. Cdn/operationresults/cdnwebapplicationfirewallpolicyresults/write |  |
> | Microsoft. Cdn/operationresults/cdnwebapplicationfirewallpolicyresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Operationresults/profileresults/endpointresults/origingroupresults/read |  |
> | Operationresults/profileresults/endpointresults/origingroupresults/write |  |
> | Microsoft. Cdn/operationresults/profileresults/endpointresults/origingroupresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Microsoft.Cdn/operations/read |  |
> | Microsoft.Cdn/profiles/read |  |
> | Microsoft.Cdn/profiles/write |  |
> | Microsoft.Cdn/profiles/delete |  |
> | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Microsoft.Cdn/profiles/endpoints/read |  |
> | Microsoft.Cdn/profiles/endpoints/write |  |
> | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Microsoft. Cdn/設定檔/端點/origingroups/讀取 |  |
> | Microsoft. Cdn/設定檔/端點/origingroups/寫入 |  |
> | Microsoft. Cdn/設定檔/端點/origingroups/刪除 |  |
> | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Microsoft.Cdn/profiles/endpoints/origins/delete |  |

### <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

Azure 服務：傳統部署模型虛擬網路

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.ClassicNetwork/register/action | 傳統網路的暫存器 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/read | 取得 Express Route 交叉連線。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/write | 新增 Express Route 交叉連線。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | 取得 Express Route 交叉連線作業狀態。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | 取得 Express Route 交叉連線對等互連。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | 新增 Express Route 交叉連線對等互連。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | 刪除 Express Route 交叉連線對等互連。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | 取得 Express Route 交叉連線對等互連作業狀態。 |
> | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | 擷取支援裝置清單。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/read | 取得網路安全性群組。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/write | 新增網路安全性群組。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/delete | 刪除網路安全性群組。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | 讀取網路安全性群組的作業狀態。 |
> | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | 取得網路安全性群組診斷設定 |
> | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新網路安全性群組診斷設定，此作業會由 Insights 資源提供者來補充。 |
> | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | 取得網路安全性群組的事件 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | 取得安全性規則。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | 新增或更新安全性規則。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | 刪除安全性規則。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | 讀取網路安全性群組安全性規則的作業狀態。 |
> | Microsoft.ClassicNetwork/operations/read | 取得傳統網路作業。 |
> | Microsoft.ClassicNetwork/quotas/read | 取得訂用帳戶配額。 |
> | Microsoft.ClassicNetwork/reservedIps/read | 取得保留的 IP |
> | Microsoft.ClassicNetwork/reservedIps/write | 新增保留的 IP |
> | Microsoft.ClassicNetwork/reservedIps/delete | 刪除保留的 IP。 |
> | Microsoft.ClassicNetwork/reservedIps/link/action | 連結保留的 IP |
> | Microsoft.ClassicNetwork/reservedIps/join/action | 加入保留的 IP |
> | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | 讀取保留之 IP 的作業狀態。 |
> | Microsoft.ClassicNetwork/virtualNetworks/read | 取得虛擬網路。 |
> | Microsoft.ClassicNetwork/virtualNetworks/write | 新增虛擬網路。 |
> | Microsoft.ClassicNetwork/virtualNetworks/delete | 刪除虛擬網路。 |
> | Microsoft.ClassicNetwork/virtualNetworks/peer/action | 讓某個虛擬網路與另一個虛擬網路對等互連。 |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | 加入虛擬網路。 |
> | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | 檢查虛擬網路中給定 IP 位址的可用性。 |
> | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | 驗證虛擬網路移轉 |
> | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | 準備虛擬網路移轉 |
> | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | 認可虛擬網路移轉 |
> | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | 中止虛擬網路移轉 |
> | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | 顯示功能 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | 取得虛擬網路閘道。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | 新增虛擬網路閘道。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | 刪除虛擬網路閘道。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | 啟動虛擬網路閘道的診斷。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | 停止虛擬網路閘道的診斷。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | 下載閘道診斷。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | 擷取迴路服務金鑰。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | 下載裝置組態指令碼。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | 列出虛擬網路閘道套件。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | 讀取已撤銷的用戶端憑證。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | 撤銷用戶端憑證。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | 取消撤銷用戶端憑證。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | 尋找用戶端根憑證。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | 上傳新的用戶端根憑證。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | 刪除虛擬網路閘道用戶端憑證。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | 依指紋下載憑證。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | 列出虛擬網路閘道憑證套件。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | 擷取連線清單。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | 建立網站對網站閘道連線。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | 中斷網站對網站閘道連線。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | 測試網站對網站閘道連線。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | 讀取虛擬網路閘道的作業狀態。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | 取得虛擬網路閘道套件。 |
> | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | 讀取虛擬網路的作業狀態。 |
> | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | 取得遠端虛擬網路對等互連 Proxy。 |
> | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | 新增或更新遠端虛擬網路對等互連 Proxy。 |
> | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | 刪除遠端虛擬網路對等互連 Proxy。 |
> | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | 取得與子網路相關聯的網路安全性群組。 |
> | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | 新增與子網路相關聯的網路安全性群組。 |
> | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | 刪除與子網路相關聯的網路安全性群組。 |
> | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | 讀取與網路安全性群組相關聯之虛擬網路子網路的作業狀態。 |
> | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | 取得虛擬網路對等互連。 |

### <a name="microsoftnetwork"></a>Microsoft.Network

Azure 服務： [應用程式閘道](../application-gateway/index.yml)、 [azure](../bastion/index.yml)防禦、 [Azure DDoS 保護](../virtual-network/ddos-protection-overview.md)、 [Azure DNS](../dns/index.yml)、 [azure ExpressRoute](../expressroute/index.yml)、 [azure 防火牆](../firewall/index.yml)、 [Azure Front Door service](../frontdoor/index.yml)、 [Azure Private Link](../private-link/index.yml)、 [Load Balancer](../load-balancer/index.yml)、 [網路](../network-watcher/index.yml)監看員、 [流量管理員](../traffic-manager/index.yml)、 [虛擬網路](../virtual-network/index.yml)、 [虛擬 WAN](../virtual-wan/index.yml)、 [VPN 閘道](../vpn-gateway/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Network/register/action | 註冊訂用帳戶 |
> | Microsoft.Network/unregister/action | 取消註冊訂用帳戶 |
> | Microsoft.Network/checkTrafficManagerNameAvailability/action | 檢查流量管理員相對 DNS 名稱的可用性。 |
> | Microsoft.Network/checkFrontDoorNameAvailability/action | 檢查 Front Door 名稱是否可用 |
> | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | 取得應用程式閘道可用的要求標頭 |
> | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | 取得應用程式閘道可用的回應標頭 |
> | Microsoft.Network/applicationGatewayAvailableServerVariables/read | 取得應用程式閘道可用的伺服器變數 |
> | Microsoft.Network/applicationGatewayAvailableSslOptions/read | 應用程式閘道可用的 SSL 選項 |
> | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | 應用程式閘道 SSL 預先定義的原則 |
> | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | 取得應用程式閘道可用的 WAF 規則集 |
> | Microsoft.Network/applicationGateways/read | 取得應用程式閘道 |
> | Microsoft.Network/applicationGateways/write | 建立或更新應用程式閘道 |
> | Microsoft.Network/applicationGateways/delete | 刪除應用程式閘道 |
> | Microsoft.Network/applicationGateways/backendhealth/action | 取得應用程式閘道後端的健康狀態 |
> | Microsoft. Network/applicationGateways/getBackendHealthOnDemand/action | 依需求取得給定 HTTP 設定和後端集區的應用程式閘道後端健全狀況 |
> | Microsoft.Network/applicationGateways/start/action | 啟動應用程式閘道 |
> | Microsoft.Network/applicationGateways/stop/action | 停止應用程式閘道 |
> | Microsoft. Network/applicationGateways/restart/action | 重新開機應用程式閘道 |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | 加入應用程式閘道後端位址集區。 不可警示。 |
> | Microsoft. Network/applicationGateways/privateEndpointConnections/read | 取得應用程式閘道 PrivateEndpoint 連接 |
> | Microsoft. Network/applicationGateways/privateEndpointConnections/write | 更新應用程式閘道 PrivateEndpoint 連接 |
> | Microsoft. Network/applicationGateways/privateEndpointConnections/delete | 刪除應用程式閘道 PrivateEndpoint 連接 |
> | Microsoft. Network/applicationGateways/privateLinkConfigurations/read | 取得應用程式閘道 Private Link 設定 |
> | Microsoft. Network/applicationGateways/privateLinkResources/read | 取得 ApplicationGateway PrivateLink 資源 |
> | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/read | 取得應用程式閘道 WAF 原則 |
> | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/write | 建立應用程式閘道 WAF 原則或更新應用程式閘道 WAF 原則 |
> | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | 刪除應用程式閘道 WAF 原則 |
> | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | 將 IP 設定加入至應用程式安全性群組。 不可警示。 |
> | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | 將安全性規則加入至應用程式安全性群組。 不可警示。 |
> | Microsoft.Network/applicationSecurityGroups/read | 取得應用程式安全性群組識別碼。 |
> | Microsoft.Network/applicationSecurityGroups/write | 建立應用程式安全性群組，或更新現有的應用程式安全性群組。 |
> | Microsoft.Network/applicationSecurityGroups/delete | 刪除應用程式安全性群組 |
> | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | 在 ApplicationSecurityGroup 中列出 IP 組態 |
> | Microsoft.Network/azureFirewallFqdnTags/read | 取得 Azure 防火牆 FQDN 標記 |
> | Microsoft.Network/azurefirewalls/read | 取得 Azure 防火牆 |
> | Microsoft.Network/azurefirewalls/write | 建立或更新 Azure 防火牆 |
> | Microsoft.Network/azurefirewalls/delete | 刪除 Azure 防火牆 |
> | Microsoft. Network/azureFirewalls/applicationRuleCollections/read | 取得 Azure 防火牆 ApplicationRuleCollection |
> | Microsoft. Network/azureFirewalls/applicationRuleCollections/write | CreatesOrUpdates Azure 防火牆 ApplicationRuleCollection |
> | Microsoft. Network/azureFirewalls/applicationRuleCollections/delete | 刪除 Azure 防火牆 ApplicationRuleCollection |
> | Microsoft. Network/azureFirewalls/natRuleCollections/read | 取得 Azure 防火牆 NatRuleCollection |
> | Microsoft. Network/azureFirewalls/natRuleCollections/write | CreatesOrUpdates Azure 防火牆 NatRuleCollection |
> | Microsoft. Network/azureFirewalls/natRuleCollections/delete | 刪除 Azure 防火牆 NatRuleCollection |
> | Microsoft. Network/azureFirewalls/networkRuleCollections/read | 取得 Azure 防火牆 NetworkRuleCollection |
> | Microsoft. Network/azureFirewalls/networkRuleCollections/write | CreatesOrUpdates Azure 防火牆 NetworkRuleCollection |
> | Microsoft. Network/azureFirewalls/networkRuleCollections/delete | 刪除 Azure 防火牆 NetworkRuleCollection |
> | Microsoft.Network/bastionHosts/read | 取得堡壘主機 |
> | Microsoft.Network/bastionHosts/write | 建立或更新堡壘主機 |
> | Microsoft.Network/bastionHosts/delete | 刪除堡壘主機 |
> | Microsoft. Network/bastionHosts/getactivesessions/action | 取得堡壘主機中的使用中會話 |
> | Microsoft. Network/bastionHosts/disconnectactivesessions/action | 中斷指定防禦主機中的作用中會話 |
> | Microsoft. Network/bastionHosts/getShareableLinks/action | 傳回防禦子網中指定 Vm 的可共用 url，並提供其 url |
> | Microsoft. Network/bastionHosts/createShareableLinks/action | 為防禦下的 Vm 建立可共用的 url，並傳回 url |
> | Microsoft. Network/bastionHosts/deleteShareableLinks/action | 在防禦下刪除所提供 Vm 的可共用 url |
> | Microsoft. Network/bastionHosts/deleteShareableLinksByToken/action | 針對防禦下提供的權杖刪除可共用的 url |
> | Microsoft.Network/bgpServiceCommunities/read | 取得 BGP 服務社群 |
> | Microsoft.Network/connections/read | 取得 VirtualNetworkGatewayConnection |
> | Microsoft.Network/connections/write | 建立或更新現有的 VirtualNetworkGatewayConnection |
> | Microsoft.Network/connections/delete | 刪除 VirtualNetworkGatewayConnection |
> | Microsoft.Network/connections/sharedkey/action | 取得 VirtualNetworkGatewayConnection SharedKey |
> | Microsoft.Network/connections/vpndeviceconfigurationscript/action | 取得 VirtualNetworkGatewayConnection 的 VPN 裝置設定 |
> | Microsoft.Network/connections/revoke/action | 將 Express Route 連線狀態標示為已撤銷 |
> | Microsoft. Network/connections/startpacketcapture/action | 啟動虛擬網路閘道連接封包捕獲。 |
> | Microsoft. Network/connections/stoppacketcapture/action | 停止虛擬網路閘道連接封包捕獲。 |
> | Microsoft.Network/connections/sharedKey/read | 取得 VirtualNetworkGatewayConnection SharedKey |
> | Microsoft.Network/connections/sharedKey/write | 建立或更新現有的 VirtualNetworkGatewayConnection SharedKey |
> | Microsoft. Network/customIpPrefixes/read | 取得自訂 Ip 首碼定義 |
> | Microsoft. Network/customIpPrefixes/write | 建立自訂 Ip 首碼，或更新現有的自訂 Ip 首碼 |
> | Microsoft. Network/customIpPrefixes/delete | 刪除自訂 Ip 首碼 |
> | Microsoft.Network/ddosCustomPolicies/read | 取得 DDoS 自訂原則定義 |
> | Microsoft.Network/ddosCustomPolicies/write | 建立 DDoS 自訂原則或更新現有的 DDoS 自訂原則 |
> | Microsoft.Network/ddosCustomPolicies/delete | 刪除 DDoS 自訂原則 |
> | Microsoft.Network/ddosProtectionPlans/read | 取得 DDoS 保護計劃 |
> | Microsoft.Network/ddosProtectionPlans/write | 建立 DDoS 保護計劃，或更新 DDoS 保護計劃  |
> | Microsoft.Network/ddosProtectionPlans/delete | 刪除 DDoS 保護計劃 |
> | Microsoft.Network/ddosProtectionPlans/join/action | 加入 DDoS 保護計劃。 不可警示。 |
> | Microsoft.Network/dnsoperationresults/read | 取得 DNS 作業的結果 |
> | Microsoft.Network/dnsoperationstatuses/read | 取得 DNS 作業的狀態  |
> | Microsoft.Network/dnszones/read | 取得 JSON 格式的 DNS 區域。 區域屬性包含標記、etag、numberOfRecordSets 和 maxNumberOfRecordSets。 請注意，此命令不會擷取區域內所包含的記錄集。 |
> | Microsoft.Network/dnszones/write | 在資源群組內建立或更新 DNS 區域。  用來更新 DNS 區域資源上的標記。 請注意，此命令無法用來在區域內建立或更新記錄集。 |
> | Microsoft.Network/dnszones/delete | 刪除 JSON 格式的 DNS 區域。 區域屬性包含標記、etag、numberOfRecordSets 和 maxNumberOfRecordSets。 |
> | Microsoft.Network/dnszones/A/read | 以 JSON 格式取得 ' A ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Microsoft.Network/dnszones/A/write | 在 DNS 區域內建立或更新 ' A ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Microsoft.Network/dnszones/A/delete | 從 DNS 區域中移除指定名稱的記錄集，並輸入 ' A '。 |
> | Microsoft.Network/dnszones/AAAA/read | 以 JSON 格式取得 ' AAAA ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Microsoft.Network/dnszones/AAAA/write | 在 DNS 區域內建立或更新 ' AAAA ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Microsoft.Network/dnszones/AAAA/delete | 從 DNS 區域中移除指定名稱的記錄集，並輸入 ' AAAA '。 |
> | Microsoft.Network/dnszones/all/read | 取得跨類型的 DNS 記錄集 |
> | Microsoft.Network/dnszones/CAA/read | 以 JSON 格式取得類型 ' CAA ' 的記錄集。 記錄集包含 TTL、標記和 etag。 |
> | Microsoft.Network/dnszones/CAA/write | 在 DNS 區域內建立或更新 ' CAA ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Microsoft.Network/dnszones/CAA/delete | 從 DNS 區域中移除指定名稱的記錄集，並輸入 ' CAA '。 |
> | Microsoft.Network/dnszones/CNAME/read | 以 JSON 格式取得 ' CNAME ' 類型的記錄集。 記錄集包含 TTL、標記和 etag。 |
> | Microsoft.Network/dnszones/CNAME/write | 在 DNS 區域內建立或更新 ' CNAME ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Microsoft.Network/dnszones/CNAME/delete | 從 DNS 區域中移除指定名稱的記錄集，然後輸入 ' CNAME '。 |
> | Microsoft.Network/dnszones/MX/read | 以 JSON 格式取得 ' MX ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Microsoft.Network/dnszones/MX/write | 在 DNS 區域內建立或更新 ' MX ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Microsoft.Network/dnszones/MX/delete | 從 DNS 區域中移除指定名稱的記錄集，並輸入 ' MX '。 |
> | Microsoft.Network/dnszones/NS/read | 取得 NS 類型的 DNS 記錄集 |
> | Microsoft.Network/dnszones/NS/write | 建立或更新 NS 類型的 DNS 記錄集 |
> | Microsoft.Network/dnszones/NS/delete | 刪除 NS 類型的 DNS 記錄集 |
> | Microsoft.Network/dnszones/PTR/read | 以 JSON 格式取得 ' PTR ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Microsoft.Network/dnszones/PTR/write | 在 DNS 區域內建立或更新 ' PTR ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Microsoft.Network/dnszones/PTR/delete | 從 DNS 區域中移除指定名稱的記錄集，並輸入 ' PTR '。 |
> | Microsoft.Network/dnszones/recordsets/read | 取得跨類型的 DNS 記錄集 |
> | Microsoft.Network/dnszones/SOA/read | 取得 SOA 類型的 DNS 記錄集 |
> | Microsoft.Network/dnszones/SOA/write | 建立或更新 SOA 類型的 DNS 記錄集 |
> | Microsoft.Network/dnszones/SRV/read | 取得 JSON 格式的 ' SRV ' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Microsoft.Network/dnszones/SRV/write | 建立或更新 SRV 類型的記錄集 |
> | Microsoft.Network/dnszones/SRV/delete | 從 DNS 區域中移除指定名稱的記錄集，並輸入 ' SRV '。 |
> | Microsoft.Network/dnszones/TXT/read | 以 JSON 格式取得 ' TXT ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Microsoft.Network/dnszones/TXT/write | 在 DNS 區域內建立或更新 ' TXT ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Microsoft.Network/dnszones/TXT/delete | 從 DNS 區域中移除指定名稱的記錄集，並輸入 ' TXT '。 |
> | Microsoft. Network/dscpConfiguration/write | 放置 DSCP 設定的操作 |
> | Microsoft. Network/dscpConfiguration/read | 放置 DSCP 設定的操作 |
> | Microsoft. Network/dscpConfiguration/join/action | 加入 DSCP 設定 |
> | Microsoft.Network/expressRouteCircuits/read | 取得 ExpressRouteCircuit |
> | Microsoft.Network/expressRouteCircuits/write | 建立或更新現有的 ExpressRouteCircuit |
> | Microsoft.Network/expressRouteCircuits/join/action | 加入 Express Route 線路。 不可警示。 |
> | Microsoft.Network/expressRouteCircuits/delete | 刪除 ExpressRouteCircuit |
> | Microsoft.Network/expressRouteCircuits/authorizations/read | 取得 ExpressRouteCircuit 授權 |
> | Microsoft.Network/expressRouteCircuits/authorizations/write | 建立或更新現有的 ExpressRouteCircuit 授權 |
> | Microsoft.Network/expressRouteCircuits/authorizations/delete | 刪除 ExpressRouteCircuit 授權 |
> | Microsoft.Network/expressRouteCircuits/peerings/read | 取得 ExpressRouteCircuit 對等互連 |
> | Microsoft.Network/expressRouteCircuits/peerings/write | 建立或更新現有的 ExpressRouteCircuit 對等互連 |
> | Microsoft.Network/expressRouteCircuits/peerings/delete | 刪除 ExpressRouteCircuit 對等互連 |
> | Microsoft. Network/expressRouteCircuits/對等互連/arpTables/read | 取得 ExpressRouteCircuit 對等互連 ArpTable |
> | Microsoft.Network/expressRouteCircuits/peerings/connections/read | 取得 ExpressRouteCircuit 連線 |
> | Microsoft.Network/expressRouteCircuits/peerings/connections/write | 建立或更新現有的 ExpressRouteCircuit 連線資源 |
> | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | 刪除 ExpressRouteCircuit 連線 |
> | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | 取得對等 Express Route 線路連線 |
> | Microsoft. Network/expressRouteCircuits/對等互連/routeTables/read | 取得 ExpressRouteCircuit 對等互連 RouteTable |
> | Microsoft. Network/expressRouteCircuits/對等互連/routeTablesSummary/read | 取得 ExpressRouteCircuit 對等互連 RouteTable 摘要 |
> | Microsoft.Network/expressRouteCircuits/peerings/stats/read | 取得 ExpressRouteCircuit 對等互連統計資料 |
> | Microsoft.Network/expressRouteCircuits/stats/read | 取得 ExpressRouteCircuit 統計資料 |
> | Microsoft.Network/expressRouteCrossConnections/read | 取得快速路由交叉連線 |
> | Microsoft.Network/expressRouteCrossConnections/join/action | 加入 Express Route 交叉連接。 不可警示。 |
> | Microsoft.Network/expressRouteCrossConnections/peerings/read | 取得快速路由交叉連線對等互連 |
> | Microsoft.Network/expressRouteCrossConnections/peerings/write | 建立快速路由交叉連線對等互連，或更新現有的快速路由交叉連線對等互連 |
> | Microsoft.Network/expressRouteCrossConnections/peerings/delete | 刪除快速路由交叉連線對等互連 |
> | Microsoft. Network/expressRouteCrossConnections/對等互連/arpTables/read | 取得快速路由交叉連線對等互連 ARP 資料表 |
> | Microsoft. Network/expressRouteCrossConnections/對等互連/routeTables/read | 取得快速路由交叉連線對等互連路由表 |
> | Microsoft. Network/expressRouteCrossConnections/對等互連/routeTableSummary/read | 取得快速路由交叉連線對等互連路由表摘要 |
> | Microsoft.Network/expressRouteGateways/read | 取得 ExpressRoute 閘道 |
> | Microsoft.Network/expressRouteGateways/join/action | 加入 Express Route 閘道。 不可警示。 |
> | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | 取得 ExpressRoute 連線 |
> | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | 建立 ExpressRoute 連線，或更新現有的 ExpressRoute 連線 |
> | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | 刪除 ExpressRoute 連線 |
> | Microsoft.Network/expressRoutePorts/read | 取得 ExpressRoutePorts |
> | Microsoft.Network/expressRoutePorts/write | 建立或更新 ExpressRoutePorts |
> | Microsoft.Network/expressRoutePorts/join/action | 加入 Express Route 埠。 不可警示。 |
> | Microsoft.Network/expressRoutePorts/delete | 刪除 ExpressRoutePorts |
> | Microsoft. Network/expressRoutePorts/generateloa/action | 產生 ExpressRoutePorts 的 LOA |
> | Microsoft.Network/expressRoutePorts/links/read | 取得 ExpressRouteLink |
> | Microsoft.Network/expressRoutePortsLocations/read | 取得 Express Route 連接埠位置 |
> | Microsoft.Network/expressRouteServiceProviders/read | 取得 ExpressRoute 服務提供者 |
> | Microsoft. Network/firewallPolicies/read | 取得防火牆原則 |
> | Microsoft. Network/firewallPolicies/write | 建立防火牆原則或更新現有的防火牆原則 |
> | Microsoft. Network/firewallPolicies/join/action | 加入防火牆原則。 不可警示。 |
> | Microsoft. Network/firewallPolicies/certificate/action | 產生防火牆原則憑證 |
> | Microsoft. Network/firewallPolicies/delete | 刪除防火牆原則 |
> | Microsoft. Network/firewallPolicies/ruleCollectionGroups/read | 取得防火牆原則規則集合群組 |
> | Microsoft. Network/firewallPolicies/ruleCollectionGroups/write | 建立防火牆原則規則集合群組或更新現有的防火牆原則規則集合群組 |
> | Microsoft. Network/firewallPolicies/ruleCollectionGroups/delete | 刪除防火牆原則規則集合群組 |
> | Microsoft. Network/firewallPolicies/ruleGroups/read | 取得防火牆原則規則群組 |
> | Microsoft. Network/firewallPolicies/ruleGroups/write | 建立防火牆原則規則群組，或更新現有的防火牆原則規則群組 |
> | Microsoft. Network/firewallPolicies/ruleGroups/delete | 刪除防火牆原則規則群組 |
> | Microsoft.Network/frontDoors/read | 取得 Front Door |
> | Microsoft.Network/frontDoors/write | 建立或更新 Front Door |
> | Microsoft.Network/frontDoors/delete | 刪除 Front Door |
> | Microsoft.Network/frontDoors/purge/action | 從前端清除快取的內容 |
> | Microsoft.Network/frontDoors/validateCustomDomain/action | 驗證 Front Door 的前端端點 |
> | Microsoft.Network/frontDoors/backendPools/read | 取得後端集區 |
> | Microsoft.Network/frontDoors/backendPools/write | 建立或更新後端集區 |
> | Microsoft.Network/frontDoors/backendPools/delete | 刪除後端集區 |
> | Microsoft.Network/frontDoors/frontendEndpoints/read | 取得前端端點 |
> | Microsoft.Network/frontDoors/frontendEndpoints/write | 建立或更新前端端點 |
> | Microsoft.Network/frontDoors/frontendEndpoints/delete | 刪除前端端點 |
> | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | 啟用前端端點上的 HTTPS |
> | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | 停用前端端點上的 HTTPS |
> | Microsoft.Network/frontDoors/healthProbeSettings/read | 取得健康情況探查設定 |
> | Microsoft.Network/frontDoors/healthProbeSettings/write | 建立或更新健康情況探查設定 |
> | Microsoft.Network/frontDoors/healthProbeSettings/delete | 刪除健康情況探查設定 |
> | Microsoft.Network/frontDoors/loadBalancingSettings/read | 取得負載平衡設定 |
> | Microsoft.Network/frontDoors/loadBalancingSettings/write | 建立或更新負載平衡設定 |
> | Microsoft.Network/frontDoors/loadBalancingSettings/delete | 建立或更新負載平衡設定 |
> | Microsoft.Network/frontDoors/routingRules/read | 取得路由規則 |
> | Microsoft.Network/frontDoors/routingRules/write | 建立或更新路由規則 |
> | Microsoft.Network/frontDoors/routingRules/delete | 刪除路由規則 |
> | Microsoft. Network/frontDoors/rulesEngines/read | 取得規則引擎 |
> | Microsoft. Network/frontDoors/rulesEngines/write | 建立或更新規則引擎 |
> | Microsoft. Network/frontDoors/rulesEngines/delete | 刪除規則引擎 |
> | Microsoft. Network/frontDoorWebApplicationFirewallManagedRuleSets/read | 取得 Web 應用程式防火牆受控規則集 |
> | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | 取得 Web 應用程式防火牆原則 |
> | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | 建立或更新 Web 應用程式防火牆原則 |
> | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | 刪除 Web 應用程式防火牆原則 |
> | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/join/action | 加入 Web 應用程式防火牆原則。 不可警示。 |
> | Microsoft. Network/ipAllocations/read | 取得 IpAllocation |
> | Microsoft. Network/ipAllocations/write | 建立 IpAllocation 或更新現有的 IpAllocation |
> | Microsoft. Network/ipAllocations/delete | 刪除 IpAllocation |
> | Microsoft. Network/ipGroups/read | 取得 IpGroup |
> | Microsoft. Network/ipGroups/write | 建立 IpGroup 或更新現有的 IpGroup |
> | Microsoft. Network/ipGroups/validate/action | 驗證 IpGroup |
> | Microsoft. Network/ipGroups/updateReferences/action | 更新 IpGroup 中的參考 |
> | Microsoft. Network/ipGroups/join/action | 加入 IpGroup。 不可警示。 |
> | Microsoft. Network/ipGroups/delete | 刪除 IpGroup |
> | Microsoft.Network/loadBalancers/read | 取得負載平衡器定義 |
> | Microsoft.Network/loadBalancers/write | 建立負載平衡器，或更新現有的負載平衡器 |
> | Microsoft.Network/loadBalancers/delete | 刪除負載平衡器 |
> | Microsoft.Network/loadBalancers/backendAddressPools/read | 取得負載平衡器的後端位址集區定義 |
> | Microsoft. Network/loadBalancers/Loadbalancer.backendaddresspools/write | 建立負載平衡器後端位址集區，或更新現有的負載平衡器後端位址集區 |
> | Microsoft. Network/loadBalancers/Loadbalancer.backendaddresspools/delete | 刪除負載平衡器後端位址集區 |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | 加入負載平衡器後端位址集區。 不可警示。 |
> | Microsoft. Network/loadBalancers/Loadbalancer.backendaddresspools/backendPoolAddresses/read | 列出 Load Balancer 後端位址集區的後端位址 |
> | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | 取得負載平衡器的前端 IP 組態定義 |
> | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | 加入 Load Balancer Frontend IP 設定。 不可警示。 |
> | Microsoft.Network/loadBalancers/inboundNatPools/read | 取得負載平衡器的輸入 NAT 集區定義 |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | 加入負載平衡器輸入 NAT 集區。 不可警示。 |
> | Microsoft.Network/loadBalancers/inboundNatRules/read | 取得負載平衡器的輸入 NAT 規則定義 |
> | Microsoft.Network/loadBalancers/inboundNatRules/write | 建立負載平衡器的輸入 NAT 規則，或更新現有的負載平衡器輸入 NAT 規則 |
> | Microsoft.Network/loadBalancers/inboundNatRules/delete | 刪除負載平衡器的輸入 NAT 規則 |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | 加入負載平衡器輸入 nat 規則。 不可警示。 |
> | Microsoft.Network/loadBalancers/loadBalancingRules/read | 取得負載平衡器的負載平衡規則定義 |
> | Microsoft.Network/loadBalancers/networkInterfaces/read | 取得負載平衡器下所有網路介面的參考 |
> | Microsoft.Network/loadBalancers/outboundRules/read | 取得負載平衡器的輸出規則定義 |
> | Microsoft.Network/loadBalancers/probes/read | 取得負載平衡器探查 |
> | Microsoft.Network/loadBalancers/probes/join/action | 允許使用負載平衡器的探查。 例如，使用此權限，VM 擴展集的 healthProbe 屬性就可以參考探查。 不可警示。 |
> | Microsoft.Network/loadBalancers/virtualMachines/read | 取得負載平衡器下所有虛擬機器的參考 |
> | Microsoft.Network/localnetworkgateways/read | 取得 LocalNetworkGateway |
> | Microsoft.Network/localnetworkgateways/write | 建立或更新現有的 LocalNetworkGateway |
> | Microsoft.Network/localnetworkgateways/delete | 刪除 LocalNetworkGateway |
> | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | 檢查加速網路支援 |
> | Microsoft. Network/位置/checkPrivateLinkServiceVisibility/action | 檢查 Private Link 服務可見度 |
> | Microsoft.Network/locations/bareMetalTenants/action | 配置或驗證裸機租用戶 |
> | Microsoft. Network/位置/setLoadBalancerFrontendPublicIpAddresses/action | SetLoadBalancerFrontendPublicIpAddresses 會以2個負載平衡器的前端 IP 設定為目標。 要求的主體中會提供 IP 設定的 Azure Resource Manager 識別碼。 |
> | Microsoft. Network/位置/autoApprovedPrivateLinkServices/read | 取得自動核准的 Private Link 服務 |
> | Microsoft.Network/locations/availableDelegations/read | 取得可用的委派 |
> | Microsoft. Network/位置/availablePrivateEndpointTypes/read | 取得可用的私人端點資源 |
> | Microsoft. Network/位置/availableServiceAliases/read | 取得可用的服務別名 |
> | Microsoft.Network/locations/checkDnsNameAvailability/read | 檢查指定的位置是否有 DNS 標籤 |
> | Microsoft.Network/locations/operationResults/read | 取得非同步 POST 或 DELETE 作業的作業結果 |
> | Microsoft.Network/locations/operations/read | 取得代表非同步作業狀態的作業資源 |
> | Microsoft. Network/位置/serviceTags/read | 取得服務標記 |
> | Microsoft.Network/locations/supportedVirtualMachineSizes/read | 取得支援的虛擬機器大小 |
> | Microsoft.Network/locations/usages/read | 取得資源使用量計量 |
> | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | 取得可用虛擬網路端點服務的清單 |
> | Microsoft. Network/masterCustomIpPrefixes/read | 取得主要自訂 Ip 首碼定義 |
> | Microsoft. Network/masterCustomIpPrefixes/write | 建立主要自訂 Ip 首碼，或更新現有的主要自訂 Ip 首碼 |
> | Microsoft. Network/masterCustomIpPrefixes/delete | 刪除主要自訂 Ip 首碼 |
> | Microsoft. Network/natGateways/join/action | 加入 NAT 閘道 |
> | Microsoft. Network/networkExperimentProfiles/read | 取得 Internet Analyzer 設定檔 |
> | Microsoft. Network/networkExperimentProfiles/write | 建立或更新 Internet Analyzer 設定檔 |
> | Microsoft. Network/networkExperimentProfiles/delete | 刪除 Internet Analyzer 設定檔 |
> | Microsoft. Network/networkExperimentProfiles/實驗/讀取 | 取得 Internet Analyzer 測試 |
> | Microsoft. Network/networkExperimentProfiles/實驗/寫入 | 建立或更新 Internet Analyzer 測試 |
> | Microsoft. Network/networkExperimentProfiles/實驗/刪除 | 刪除 Internet Analyzer 測試 |
> | Microsoft. Network/networkExperimentProfiles/實驗/時間序列/action | 取得 Internet Analyzer 測試的時間序列 |
> | Microsoft. Network/networkExperimentProfiles/實驗/latencyScorecard/action | 取得 Internet Analyzer 測試的延遲計分卡 |
> | Microsoft. Network/networkExperimentProfiles/preconfiguredEndpoints/read | 取得 Internet Analyzer 設定檔的預先設定端點 |
> | Microsoft.Network/networkIntentPolicies/read | 取得網路意圖原則描述 |
> | Microsoft.Network/networkIntentPolicies/write | 建立網路意圖原則或更新現有的網路意圖原則 |
> | Microsoft.Network/networkIntentPolicies/delete | 刪除網路意圖原則 |
> | Microsoft.Network/networkInterfaces/read | 取得網路介面定義。  |
> | Microsoft.Network/networkInterfaces/write | 建立網路介面，或更新現有的網路介面。  |
> | Microsoft.Network/networkInterfaces/join/action | 將虛擬機器加入網路介面。 不可警示。 |
> | Microsoft.Network/networkInterfaces/delete | 刪除網路介面 |
> | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | 取得 VM 網路介面上所設定的路由表 |
> | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | 取得 VM 網路介面上所設定的網路安全性群組 |
> | Microsoft. Network/networkInterfaces/UpdateParentNicAttachmentOnElasticNic/action | 更新與彈性 NIC 相關聯的父 NIC |
> | Microsoft.Network/networkInterfaces/ipconfigurations/read | 取得網路介面 IP 組態定義。  |
> | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | 加入網路介面 IP 設定。 不可警示。 |
> | Microsoft.Network/networkInterfaces/loadBalancers/read | 取得網路介面所屬的所有負載平衡器 |
> | Microsoft.Network/networkInterfaces/tapConfigurations/read | 取得網路介面 Tap 設定。 |
> | Microsoft.Network/networkInterfaces/tapConfigurations/write | 建立網路介面 Tap 設定，或更新現有的網路介面 Tap 設定。 |
> | Microsoft.Network/networkInterfaces/tapConfigurations/delete | 刪除網路介面 Tap 設定。 |
> | Microsoft.Network/networkProfiles/read | 取得網路設定檔 |
> | Microsoft.Network/networkProfiles/write | 建立或更新網路設定檔 |
> | Microsoft.Network/networkProfiles/delete | 刪除網路設定檔 |
> | Microsoft.Network/networkProfiles/setContainers/action | 設定容器 |
> | Microsoft.Network/networkProfiles/removeContainers/action | 移除容器 |
> | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | 設定容器網路介面 |
> | Microsoft.Network/networkSecurityGroups/read | 取得網路安全性群組定義 |
> | Microsoft.Network/networkSecurityGroups/write | 建立網路安全性群組，或更新現有的網路安全性群組 |
> | Microsoft.Network/networkSecurityGroups/delete | 刪除網路安全性群組 |
> | Microsoft.Network/networkSecurityGroups/join/action | 加入網路安全性群組。 不可警示。 |
> | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | 取得預設的安全性規則定義 |
> | Microsoft.Network/networkSecurityGroups/securityRules/read | 取得安全性規則定義 |
> | Microsoft.Network/networkSecurityGroups/securityRules/write | 建立安全性規則，或更新現有的安全性規則 |
> | Microsoft.Network/networkSecurityGroups/securityRules/delete | 刪除安全性規則 |
> | Microsoft.Network/networkWatchers/read | 取得網路監看員定義 |
> | Microsoft.Network/networkWatchers/write | 建立網路監看員，或更新現有的網路監看員 |
> | Microsoft.Network/networkWatchers/delete | 刪除網路監看員 |
> | Microsoft.Network/networkWatchers/configureFlowLog/action | 設定目標資源的流程記錄。 |
> | Microsoft.Network/networkWatchers/ipFlowVerify/action | 傳回是要允許還是拒絕特定目的地的往返封包。 |
> | Microsoft.Network/networkWatchers/nextHop/action | 針對指定的目標和目的地 IP 位址，傳回下一個躍點的類型和 IP 位址。 |
> | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | 取得資源的流程記錄狀態。 |
> | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | 取得上一次執行的疑難排解結果或目前執行的疑難排解作業。 |
> | Microsoft.Network/networkWatchers/securityGroupView/action | 檢視對 VM 套用之已設定且生效的網路安全性群組規則。 |
> | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | 網路組態的診斷。 |
> | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | 批次查詢監視指定端點之間的連線能力 |
> | Microsoft.Network/networkWatchers/topology/action | 取得資源的網路層級檢視和資源在資源群組中的關聯性。 |
> | Microsoft.Network/networkWatchers/troubleshoot/action | 開始針對 Azure 中的網路資源進行疑難排解。 |
> | Microsoft.Network/networkWatchers/connectivityCheck/action | 確認是否可以建立從虛擬機器到包含另一台 VM 或任意遠端伺服器之指定端點的直接 TCP 連線。 |
> | Microsoft.Network/networkWatchers/azureReachabilityReport/action | 傳回從指定位置到 Azure 區域之網際網路服務提供者的相對延遲分數。 |
> | Microsoft.Network/networkWatchers/availableProvidersList/action | 針對指定的 Azure 區域傳回所有可用的網際網路服務提供者。 |
> | Microsoft.Network/networkWatchers/connectionMonitors/start/action | 開始監視指定端點之間的連線能力 |
> | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | 停止/暫停監視指定端點之間的連線能力 |
> | Microsoft.Network/networkWatchers/connectionMonitors/query/action | 查詢監視指定端點之間的連線能力 |
> | Microsoft.Network/networkWatchers/connectionMonitors/read | 取得連線監視詳細資料 |
> | Microsoft.Network/networkWatchers/connectionMonitors/write | 建立連線監視 |
> | Microsoft.Network/networkWatchers/connectionMonitors/delete | 刪除連線監視 |
> | Microsoft. Network/networkWatchers/flowLogs/read | 取得流程記錄詳細資料 |
> | Microsoft. Network/networkWatchers/flowLogs/write | 建立流量記錄 |
> | Microsoft. Network/networkWatchers/flowLogs/delete | 刪除流量記錄 |
> | Microsoft.Network/networkWatchers/lenses/start/action | 開始監視指定端點上的網路流量 |
> | Microsoft.Network/networkWatchers/lenses/stop/action | 停止/暫停監視指定端點上的網路流量 |
> | Microsoft.Network/networkWatchers/lenses/query/action | 查詢監視指定端點上的網路流量 |
> | Microsoft.Network/networkWatchers/lenses/read | 取得功能濾鏡詳細資料 |
> | Microsoft.Network/networkWatchers/lenses/write | 建立功能濾鏡 |
> | Microsoft.Network/networkWatchers/lenses/delete | 刪除功能濾鏡 |
> | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | 取得屬性的相關資訊和封包擷取資源的狀態。 |
> | Microsoft.Network/networkWatchers/packetCaptures/stop/action | 停止正在執行的封包擷取工作階段。 |
> | Microsoft.Network/networkWatchers/packetCaptures/read | 取得封包擷取定義 |
> | Microsoft.Network/networkWatchers/packetCaptures/write | 建立封包擷取 |
> | Microsoft.Network/networkWatchers/packetCaptures/delete | 刪除封包擷取 |
> | Microsoft.Network/networkWatchers/pingMeshes/start/action | 在指定的 VM 之間啟動 PingMesh |
> | Microsoft.Network/networkWatchers/pingMeshes/stop/action | 在指定的 VM 之間停止 PingMesh |
> | Microsoft.Network/networkWatchers/pingMeshes/read | 取得 PingMesh 詳細資料 |
> | Microsoft.Network/networkWatchers/pingMeshes/write | 建立 PingMesh |
> | Microsoft.Network/networkWatchers/pingMeshes/delete | 刪除 PingMesh |
> | Microsoft.Network/operations/read | 取得可用的作業 |
> | Microsoft.Network/p2sVpnGateways/read | 取得 P2SVpnGateway。 |
> | Microsoft.Network/p2sVpnGateways/write | 放置 P2SVpnGateway。 |
> | Microsoft.Network/p2sVpnGateways/delete | 刪除 P2SVpnGateway。 |
> | microsoft. network/p2sVpnGateways/reset/action | 重設 P2SVpnGateway |
> | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | 產生 P2SVpnGateway 的 Vpn 設定檔 |
> | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | 取得 P2SVpnGateway 的 P2S Vpn 連線健康情況 |
> | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/action | 取得詳細 P2SVpnGateway 的 P2S Vpn 連線健康情況 |
> | Microsoft. Network/p2sVpnGateways/disconnectp2svpnconnections/action | 中斷 p2s vpn 連線 |
> | Microsoft. Network/privateDnsOperationResults/read | 取得私人 DNS 操作的結果 |
> | Microsoft. Network/privateDnsOperationStatuses/read | 取得私人 DNS 操作的狀態 |
> | Microsoft. Network/privateDnsZones/read | 以 JSON 格式取得私人 DNS 區域屬性。 請注意，此命令不會抓取私人 DNS 區域所連結的虛擬網路，或區域內包含的記錄集。 |
> | Microsoft. Network/privateDnsZones/write | 在資源群組中建立或更新私人 DNS 區域。 請注意，此命令不能用來建立或更新區域內的虛擬網路連結或記錄集。 |
> | Microsoft. Network/privateDnsZones/delete | 刪除私人 DNS 區域。 |
> | Microsoft. Network/privateDnsZones/join/action | 加入私人 DNS 區域 |
> | Microsoft. Network/privateDnsZones/A/read | 以 JSON 格式取得私人 DNS 區域內 ' A ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Microsoft. Network/privateDnsZones/A/write | 在私人 DNS 區域內建立或更新 ' A ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Microsoft. Network/privateDnsZones/A/delete | 從私人 DNS 區域中移除指定名稱的記錄集，並輸入 ' A '。 |
> | Microsoft. Network/privateDnsZones/AAAA/read | 以 JSON 格式取得私人 DNS 區域內 ' AAAA ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Microsoft. Network/privateDnsZones/AAAA/write | 在私人 DNS 區域內建立或更新 ' AAAA ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Microsoft. Network/privateDnsZones/AAAA/delete | 從私人 DNS 區域中移除指定名稱的記錄集，並輸入 ' AAAA '。 |
> | Microsoft. Network/privateDnsZones/ALL/read | 取得跨類型的私用 DNS 記錄集 |
> | Microsoft. Network/privateDnsZones/CNAME/read | 以 JSON 格式取得私人 DNS 區域內類型 ' CNAME ' 的記錄集。 |
> | Microsoft. Network/privateDnsZones/CNAME/write | 在私人 DNS 區域內建立或更新 ' CNAME ' 類型的記錄集。 |
> | Microsoft. Network/privateDnsZones/CNAME/delete | 從私人 DNS 區域中移除指定名稱的記錄集，然後輸入 ' CNAME '。 |
> | Microsoft. Network/privateDnsZones/MX/read | 以 JSON 格式取得私人 DNS 區域內 ' MX ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Microsoft. Network/privateDnsZones/MX/write | 在私人 DNS 區域內建立或更新 ' MX ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Microsoft. Network/privateDnsZones/MX/delete | 從私人 DNS 區域移除指定名稱和類型 ' MX ' 的記錄集。 |
> | Microsoft. Network/privateDnsZones/PTR/read | 以 JSON 格式取得私人 DNS 區域內 ' PTR ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Microsoft. Network/privateDnsZones/PTR/write | 在私人 DNS 區域內建立或更新 ' PTR ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Microsoft. Network/privateDnsZones/PTR/delete | 從私人 DNS 區域中移除指定名稱的記錄集，並輸入 ' PTR '。 |
> | Microsoft. Network/privateDnsZones/recordset/read | 取得跨類型的私用 DNS 記錄集 |
> | Microsoft. Network/privateDnsZones/SOA/read | 以 JSON 格式取得私人 DNS 區域內類型 ' SOA ' 的記錄集。 |
> | Microsoft. Network/privateDnsZones/SOA/write | 在私人 DNS 區域內更新 ' SOA ' 類型的記錄集。 |
> | Microsoft. Network/privateDnsZones/SRV/read | 以 JSON 格式取得私人 DNS 區域內類型 ' SRV ' 的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Microsoft. Network/privateDnsZones/SRV/write | 在私人 DNS 區域內建立或更新 ' SRV ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Microsoft. Network/privateDnsZones/SRV/delete | 從私人 DNS 區域中移除指定名稱的記錄集，並輸入 ' SRV '。 |
> | Microsoft. Network/privateDnsZones/TXT/read | 以 JSON 格式取得私人 DNS 區域內 ' TXT ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Microsoft. Network/privateDnsZones/TXT/write | 在私人 DNS 區域內建立或更新 ' TXT ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Microsoft. Network/privateDnsZones/TXT/delete | 從私人 DNS 區域中移除指定名稱的記錄集，並輸入 ' TXT '。 |
> | Microsoft. Network/privateDnsZones/Registrationenabled/read | 取得私人 DNS 區域連結至虛擬網路屬性（JSON 格式）。 |
> | Microsoft. Network/privateDnsZones/Registrationenabled/write | 建立或更新私人 DNS 區域連結至虛擬網路。 |
> | Microsoft. Network/privateDnsZones/Registrationenabled/delete | 刪除虛擬網路的私人 DNS 區域連結。 |
> | Microsoft. Network/privateEndpointRedirectMaps/read | 取得私人端點 RedirectMap |
> | Microsoft. Network/privateEndpointRedirectMaps/write | 建立私人端點 RedirectMap 或更新現有的私人端點 RedirectMap |
> | Microsoft. Network/privateEndpoints/read | 取得私人端點資源。 |
> | Microsoft. Network/privateEndpoints/write | 建立新的私人端點，或更新現有的私人端點。 |
> | Microsoft. Network/privateEndpoints/delete | 刪除私人端點資源。 |
> | Microsoft. Network/privateEndpoints/privateDnsZoneGroups/read | 取得私人 DNS 區域群組 |
> | Microsoft. Network/privateEndpoints/privateDnsZoneGroups/write | 放置私人 DNS 區域群組 |
> | Microsoft.Network/privateLinkServices/read | 取得私人連結服務資源。 |
> | Microsoft.Network/privateLinkServices/write | 建立新的私人連結服務或更新現有的私人連結服務。 |
> | Microsoft.Network/privateLinkServices/delete | 刪除私人連結服務資源。 |
> | Microsoft. Network/privateLinkServices/privateEndpointConnections/read | 取得私人端點連接定義。 |
> | Microsoft. Network/privateLinkServices/privateEndpointConnections/write | 建立新的私人端點連線，或更新現有的私人端點連接。 |
> | Microsoft. Network/privateLinkServices/privateEndpointConnections/delete | 刪除私人端點連接。 |
> | Microsoft.Network/publicIPAddresses/read | 取得公用 IP 位址定義。 |
> | Microsoft.Network/publicIPAddresses/write | 建立公用 IP 位址，或更新現有的公用 IP 位址。  |
> | Microsoft.Network/publicIPAddresses/delete | 刪除公用 IP 位址。 |
> | Microsoft.Network/publicIPAddresses/join/action | 加入公用 IP 位址。 不可警示。 |
> | Microsoft.Network/publicIPPrefixes/read | 取得公用 IP 首碼定義 |
> | Microsoft.Network/publicIPPrefixes/write | 建立公用 IP 首碼，或更新現有的公用 IP 首碼 |
> | Microsoft.Network/publicIPPrefixes/delete | 刪除公用 IP 首碼 |
> | Microsoft.Network/publicIPPrefixes/join/action | 加入 PublicIPPrefix。 不可警示。 |
> | Microsoft.Network/routeFilters/read | 取得路由篩選的定義 |
> | Microsoft.Network/routeFilters/join/action | 加入路由篩選。 不可警示。 |
> | Microsoft.Network/routeFilters/delete | 刪除路由篩選的定義 |
> | Microsoft.Network/routeFilters/write | 建立路由篩選條件，或更新現有的路由篩選條件 |
> | Microsoft.Network/routeFilters/routeFilterRules/read | 取得路由篩選規則的定義 |
> | Microsoft.Network/routeFilters/routeFilterRules/write | 建立路由篩選規則，或更新現有的路由篩選規則 |
> | Microsoft.Network/routeFilters/routeFilterRules/delete | 刪除路由篩選規則的定義 |
> | Microsoft.Network/routeTables/read | 取得路由表定義 |
> | Microsoft.Network/routeTables/write | 建立路由表或更新現有的路由表 |
> | Microsoft.Network/routeTables/delete | 刪除路由表定義 |
> | Microsoft.Network/routeTables/join/action | 加入路由表。 不可警示。 |
> | Microsoft.Network/routeTables/routes/read | 取得路由定義 |
> | Microsoft.Network/routeTables/routes/write | 建立路由，或更新現有路由 |
> | Microsoft.Network/routeTables/routes/delete | 刪除路由定義 |
> | Microsoft. Network/securityPartnerProviders/read | 取得 SecurityPartnerProvider |
> | Microsoft. Network/securityPartnerProviders/write | 建立 SecurityPartnerProvider 或更新現有的 SecurityPartnerProvider |
> | Microsoft. Network/securityPartnerProviders/validate/action | 驗證 SecurityPartnerProvider |
> | Microsoft. Network/securityPartnerProviders/updateReferences/action | 更新 SecurityPartnerProvider 中的參考 |
> | Microsoft. Network/securityPartnerProviders/join/action | 加入 SecurityPartnerProvider。 不可警示。 |
> | Microsoft. Network/securityPartnerProviders/delete | 刪除 SecurityPartnerProvider |
> | Microsoft.Network/serviceEndpointPolicies/read | 取得服務端點原則描述 |
> | Microsoft.Network/serviceEndpointPolicies/write | 建立服務端點原則，或更新現有的服務端點原則 |
> | Microsoft.Network/serviceEndpointPolicies/delete | 刪除服務端點原則 |
> | Microsoft.Network/serviceEndpointPolicies/join/action | 加入服務端點原則。 不可警示。 |
> | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | 將子網加入至服務端點原則。 不可警示。 |
> | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | 取得服務端點原則定義描述 |
> | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | 建立服務端點原則定義，或更新現有的服務端點原則定義 |
> | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | 刪除服務端點原則定義 |
> | Microsoft.Network/trafficManagerGeographicHierarchies/read | 取得流量管理員的地理階層，其所含的區域可用在地理流量路由方法中 |
> | Microsoft.Network/trafficManagerProfiles/read | 取得流量管理員設定檔組態。 這包括 DNS 設定、流量路由設定、端點監視設定，以及此流量管理員設定檔所路由之端點的清單。 |
> | Microsoft.Network/trafficManagerProfiles/write | 建立流量管理員設定檔，或修改現有流量管理員設定檔的組態。<br>這包括啟用或停用設定檔以及修改 DNS 設定、流量路由設定或端點監視設定。<br>您可以新增、移除、啟用或停用由流量管理員設定檔所路由傳送的端點。 |
> | Microsoft.Network/trafficManagerProfiles/delete | 刪除流量管理員設定檔。 與流量管理員設定檔相關聯的所有設定都會遺失，而且您無法再使用設定檔來路由傳送流量。 |
> | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | 取得屬於流量管理員設定檔的 Azure 端點，其中包含該 Azure 端點的所有屬性。 |
> | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | 在現有流量管理員設定檔中新增 Azure 端點，或在該流量管理員設定檔中更新現有 Azure 端點的屬性。 |
> | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | 從現有的流量管理員設定檔刪除 Azure 端點。 流量管理員將會停止將流量路由傳送到已刪除的 Azure 端點。 |
> | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | 取得屬於流量管理員設定檔的外部端點，其中包含該外部端點的所有屬性。 |
> | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | 在現有流量管理員設定檔中新增外部端點，或在該流量管理員設定檔中更新現有外部端點的屬性。 |
> | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | 從現有的流量管理員設定檔刪除外部端點。 流量管理員將會停止將流量路由傳送到已刪除的外部端點。 |
> | Microsoft.Network/trafficManagerProfiles/heatMaps/read | 取得指定流量管理員設定檔的流量管理員熱度圖，其中包含依位置和來源 IP 的查詢計數和延遲資料。 |
> | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | 取得屬於流量管理員設定檔的巢狀端點，其中包含該巢狀端點的所有屬性。 |
> | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | 在現有流量管理員設定檔中新增巢狀端點，或在該流量管理員設定檔中更新現有巢狀端點的屬性。 |
> | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | 從現有的流量管理員設定檔刪除巢狀端點。 流量管理員將會停止將流量路由傳送到已刪除的巢狀端點。 |
> | Microsoft.Network/trafficManagerUserMetricsKeys/read | 取得針對即時使用者計量集合使用的訂用帳戶層級金鑰。 |
> | Microsoft.Network/trafficManagerUserMetricsKeys/write | 建立要針對即時使用者計量集合使用的新訂用帳戶層級金鑰。 |
> | Microsoft.Network/trafficManagerUserMetricsKeys/delete | 刪除針對即時使用者計量集合使用的訂用帳戶層級金鑰。 |
> | Microsoft.Network/virtualHubs/delete | 刪除虛擬中樞 |
> | Microsoft.Network/virtualHubs/read | 取得虛擬中樞 |
> | Microsoft.Network/virtualHubs/write | 建立或更新虛擬中樞 |
> | Microsoft. Network/virtualHubs/effectiveRoutes/action | 取得在虛擬中樞上設定的有效路由 |
> | Microsoft. Network/virtualHubs/bgpConnections/read | 取得虛擬中樞的中樞 Bgp 連接子資源 |
> | Microsoft. Network/virtualHubs/bgpConnections/write | 建立或更新虛擬中樞的中樞 Bgp 連接子資源 |
> | Microsoft. Network/virtualHubs/bgpConnections/delete | 刪除虛擬中樞的中樞 Bgp 連接子資源 |
> | Microsoft. Network/virtualHubs/hubRouteTables/read | 取得虛擬中樞的路由表子資源 |
> | Microsoft. Network/virtualHubs/hubRouteTables/write | 建立或更新虛擬中樞的路由表子資源 |
> | Microsoft. Network/virtualHubs/hubRouteTables/delete | 刪除虛擬中樞的路由表子資源 |
> | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | 取得 HubVirtualNetworkConnection |
> | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | 建立或更新 HubVirtualNetworkConnection |
> | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | 刪除 HubVirtualNetworkConnection |
> | Microsoft. Network/virtualHubs/Ipconfiguration/read | 取得虛擬中樞的中樞 IpConfiguration 子資源 |
> | Microsoft. Network/virtualHubs/Ipconfiguration/write | 建立或更新虛擬中樞的中樞 IpConfiguration 子資源 |
> | Microsoft. Network/virtualHubs/Ipconfiguration/delete | 刪除虛擬中樞的中樞 IpConfiguration 子資源 |
> | Microsoft. Network/virtualHubs/routeTables/read | 取得 VirtualHubRouteTableV2 |
> | Microsoft. Network/virtualHubs/routeTables/write | 建立或更新 VirtualHubRouteTableV2 |
> | Microsoft. Network/virtualHubs/routeTables/delete | 刪除 VirtualHubRouteTableV2 |
> | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | 列出支援的 VPN 裝置 |
> | Microsoft.Network/virtualNetworkGateways/read | 取得 VirtualNetworkGateway |
> | Microsoft.Network/virtualNetworkGateways/write | 建立或更新 VirtualNetworkGateway |
> | Microsoft.Network/virtualNetworkGateways/delete | 刪除 virtualNetworkGateway |
> | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | 針對 virtualNetworkGateway 產生 VpnClient 套件 |
> | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | 針對 VirtualNetworkGateway 產生 VpnProfile 套件 |
> | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | 針對 VirtualNetworkGateway 取得每個 VPN 用戶端連線健康情況 |
> | microsoft. network/virtualnetworkgateways/disconnectvirtualnetworkgatewayvpnconnections/action | 中斷連接虛擬網路閘道 vpn 連線 |
> | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | 取得預先產生之 VPN 用戶端設定檔套件的 URL |
> | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | 設定適用於 VirtualNetworkGateway P2S 用戶端的 Vpnclient Ipsec 參數。 |
> | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | 取得適用於 VirtualNetworkGateway P2S 用戶端的 Vpnclient Ipsec 參數。 |
> | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | 重設適用於 VirtualNetworkGateway P2S 用戶端的 Vpnclient 共用金鑰。 |
> | microsoft.network/virtualnetworkgateways/reset/action | 重設 virtualNetworkGateway |
> | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | 取得 virtualNetworkGateway 通告路由 |
> | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | 取得 virtualNetworkGateway bgp 對等狀態 |
> | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | 取得 virtualnetworkgateway 學習到的路由 |
> | microsoft. network/virtualnetworkgateways/startpacketcapture/action | 啟動虛擬網路閘道封包捕獲。 |
> | microsoft. network/virtualnetworkgateways/stoppacketcapture/action | 停止虛擬網路閘道封包捕獲。 |
> | microsoft.network/virtualnetworkgateways/connections/read | 取得 VirtualNetworkGatewayConnection |
> | Microsoft.Network/virtualNetworks/read | 取得虛擬網路定義 |
> | Microsoft.Network/virtualNetworks/write | 建立虛擬網路，或更新現有的虛擬網路 |
> | Microsoft.Network/virtualNetworks/delete | 刪除虛擬網路 |
> | Microsoft. Network/virtualNetworks/joinLoadBalancer/action | 將負載平衡器加入虛擬網路 |
> | Microsoft.Network/virtualNetworks/peer/action | 讓某個虛擬網路與另一個虛擬網路對等互連 |
> | Microsoft. Network/virtualNetworks/join/action | 加入虛擬網路。 不可警示。 |
> | Microsoft.Network/virtualNetworks/BastionHosts/action | 取得虛擬網路中的堡壘主機參考。 |
> | Microsoft. Network/virtualNetworks/bastionHosts/default/action | 取得虛擬網路中的堡壘主機參考。 |
> | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | 檢查指定的虛擬網路中是否有 IP 位址 |
> | Microsoft. Network/virtualNetworks/privateDnsZoneLinks/read | 取得私人 DNS 區域連結至虛擬網路屬性（JSON 格式）。 |
> | Microsoft.Network/virtualNetworks/subnets/read | 取得虛擬網路子網路定義 |
> | Microsoft.Network/virtualNetworks/subnets/write | 建立虛擬網路子網路，或更新現有的虛擬網路子網路 |
> | Microsoft.Network/virtualNetworks/subnets/delete | 刪除虛擬網路子網路 |
> | Microsoft.Network/virtualNetworks/subnets/join/action | 加入虛擬網路。 不可警示。 |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | 將資源 (例如，儲存體帳戶或 SQL Database) 加入至子網路。 不可警示。 |
> | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | 藉由套用必要的網路原則來準備子網路 |
> | Microsoft. Network/virtualNetworks/subnet/unprepareNetworkPolicies/action | 移除套用的網路原則以 Unprepare 子網 |
> | Microsoft. Network/virtualNetworks/subnet/coNtextualServiceEndpointPolicies/read | 取得內容相關的服務端點原則 |
> | Microsoft. Network/virtualNetworks/subnet/coNtextualServiceEndpointPolicies/write | 建立內容相關的服務端點原則，或更新現有的內容服務端點原則 |
> | Microsoft. Network/virtualNetworks/subnet/coNtextualServiceEndpointPolicies/delete | 刪除內容相關的服務端點原則 |
> | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | 取得虛擬網路子網路中所有虛擬機器的參考 |
> | Microsoft.Network/virtualNetworks/usages/read | 取得虛擬網路中每個子網路的 IP 使用方式 |
> | Microsoft.Network/virtualNetworks/virtualMachines/read | 取得虛擬網路中所有虛擬機器的參考 |
> | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | 取得虛擬網路對等互連定義 |
> | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | 建立虛擬網路對等互連，或更新現有的虛擬網路對等互連 |
> | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | 刪除虛擬網路對等互連 |
> | Microsoft.Network/virtualNetworkTaps/read | 取得 Virtual Network Tap |
> | Microsoft.Network/virtualNetworkTaps/join/action | 加入虛擬網路的點擊。 不可警示。 |
> | Microsoft.Network/virtualNetworkTaps/delete | 刪除Virtual Network Tap |
> | Microsoft.Network/virtualNetworkTaps/write | 建立或更新 Virtual Network Tap |
> | Microsoft. Network/virtualRouters/read | 取得 VirtualRouter |
> | Microsoft. Network/virtualRouters/write | 建立 VirtualRouter 或更新現有的 VirtualRouter |
> | Microsoft. Network/virtualRouters/delete | 刪除 VirtualRouter |
> | Microsoft. Network/virtualRouters/join/action | 加入 VirtualRouter。 不可警示。 |
> | Microsoft. Network/virtualRouters/對等互連/read | 取得 VirtualRouterPeering |
> | Microsoft. Network/virtualRouters/對等互連/write | 建立 VirtualRouterPeering 或更新現有的 VirtualRouterPeering |
> | Microsoft. Network/virtualRouters/對等互連/delete | 刪除 VirtualRouterPeering |
> | Microsoft.Network/virtualWans/delete | 刪除虛擬 WAN |
> | Microsoft.Network/virtualWans/read | 取得虛擬 WAN |
> | Microsoft.Network/virtualWans/write | 建立或更新虛擬 WAN |
> | Microsoft.Network/virtualwans/vpnconfiguration/action | 取得 VPN 設定 |
> | Microsoft. Network/virtualwans/vpnServerConfigurations/action | 取得 VirtualWanVpnServerConfigurations |
> | Microsoft. Network/virtualwans/generateVpnProfile/action | 產生 VirtualWanVpnServerConfiguration VpnProfile |
> | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | 取得虛擬 Wan P2SVpnServerConfiguration |
> | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | 建立虛擬 Wan P2SVpnServerConfiguration 或更新現有的虛擬 Wan P2SVpnServerConfiguration |
> | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | 刪除虛擬 Wan P2SVpnServerConfiguration |
> | Microsoft.Network/virtualwans/supportedSecurityProviders/read | 取得支援的 VirtualWan 安全性提供者。 |
> | Microsoft.Network/virtualWans/virtualHubs/read | 取得所有參考虛擬 WAN 的虛擬中樞。 |
> | Microsoft.Network/virtualWans/vpnSites/read | 取得所有參考虛擬 WAN 的 VPN 網站。 |
> | Microsoft.Network/vpnGateways/read | 取得 VpnGateway。 |
> | Microsoft.Network/vpnGateways/write | 放置 VpnGateway。 |
> | Microsoft.Network/vpnGateways/delete | 刪除 VpnGateway。 |
> | microsoft.network/vpngateways/reset/action | 重設 VpnGateway |
> | microsoft. network/vpngateways/startpacketcapture/action | 使用根據資源啟動 Vpn 閘道封包捕獲 |
> | microsoft. network/vpngateways/stoppacketcapture/action | 使用 sasURL 停止 Vpn 閘道封包捕獲 |
> | microsoft.network/vpngateways/listvpnconnectionshealth/action | 取得 VpnGateway 上所有或部分連線的連線健康情況 |
> | microsoft.network/vpnGateways/vpnConnections/read | 取得 VpnConnection。 |
> | microsoft.network/vpnGateways/vpnConnections/write | 放置 VpnConnection。 |
> | microsoft.network/vpnGateways/vpnConnections/delete | 刪除 VpnConnection。 |
> | microsoft. network/vpnGateways/vpnConnections/startpacketcapture/action | 針對在 vpn 連線中選取的連結啟動封包捕獲 |
> | microsoft. network/vpnGateways/vpnConnections/stoppacketcapture/action | 在 vpn 連線中停止所選連結的封包捕獲 |
> | microsoft. network/vpnGateways/vpnConnections/vpnLinkConnections/read | 取得 Vpn 連結連接 |
> | Microsoft. Network/vpnServerConfigurations/read | 取得 VpnServerConfiguration |
> | Microsoft. Network/vpnServerConfigurations/write | 建立或更新 VpnServerConfiguration |
> | Microsoft. Network/vpnServerConfigurations/delete | 刪除 VpnServerConfiguration |
> | Microsoft.Network/vpnsites/read | 取得 VPN 網站資源。 |
> | Microsoft.Network/vpnsites/write | 建立或更新 VPN 網站資源。 |
> | Microsoft.Network/vpnsites/delete | 刪除 VPN 網站資源。 |
> | microsoft. network/Vpnsite/vpnSiteLinks/read | 取得 Vpn 站台連結 |

## <a name="storage"></a>儲存體

### <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

Azure 服務：傳統部署模型儲存體

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.ClassicStorage/register/action | 傳統儲存體的暫存器 |
> | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | 檢查儲存體帳戶的可用性。 |
> | Microsoft.ClassicStorage/capabilities/read | 顯示功能 |
> | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | 取得儲存體帳戶的可用性。 |
> | Microsoft.ClassicStorage/disks/read | 傳回儲存體帳戶磁碟。 |
> | Microsoft.ClassicStorage/images/read | 傳回映像。 |
> | Microsoft.ClassicStorage/images/operationstatuses/read | 取得映像作業狀態。 |
> | Microsoft.ClassicStorage/operations/read | 取得傳統儲存體作業 |
> | Microsoft.ClassicStorage/osImages/read | 傳回作業系統映像。 |
> | Microsoft.ClassicStorage/osPlatformImages/read | 取得作業系統平台映像。 |
> | Microsoft.ClassicStorage/publicImages/read | 取得公用虛擬機器映像。 |
> | Microsoft.ClassicStorage/quotas/read | 取得訂用帳戶配額。 |
> | Microsoft.ClassicStorage/storageAccounts/read | 傳回具有給定帳戶的儲存體帳戶。 |
> | Microsoft.ClassicStorage/storageAccounts/write | 新增儲存體帳戶。 |
> | Microsoft.ClassicStorage/storageAccounts/delete | 刪除儲存體帳戶。 |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | 列出儲存體帳戶的存取金鑰。 |
> | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | 重新產生儲存體帳戶的現有存取金鑰。 |
> | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | 驗證儲存體帳戶的移轉。 |
> | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | 準備儲存體帳戶的移轉。 |
> | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | 認可儲存體帳戶的移轉。 |
> | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | 中止儲存體帳戶的移轉。 |
> | Microsoft.classicstorage/storageAccounts/blobServices/providers/Microsoft. Insights/diagnosticSettings/read | 取得診斷設定。 |
> | Microsoft.classicstorage/storageAccounts/blobServices/providers/Microsoft. Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> | Microsoft.classicstorage/storageAccounts/blobServices/providers/Microsoft. Insights/metricDefinitions/read | 取得計量定義。 |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | 傳回儲存體帳戶磁碟。 |
> | Microsoft.ClassicStorage/storageAccounts/disks/write | 新增儲存體帳戶磁碟。 |
> | Microsoft.ClassicStorage/storageAccounts/disks/delete | 刪除給定的儲存體帳戶磁碟。 |
> | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | 讀取資源的作業狀態。 |
> | Microsoft.classicstorage/storageAccounts/fileServices/providers/Microsoft. Insights/diagnosticSettings/read | 取得診斷設定。 |
> | Microsoft.classicstorage/storageAccounts/fileServices/providers/Microsoft. Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> | Microsoft.classicstorage/storageAccounts/fileServices/providers/Microsoft. Insights/metricDefinitions/read | 取得計量定義。 |
> | Microsoft.ClassicStorage/storageAccounts/images/read | 傳回儲存體帳戶映像。 (已淘汰。 使用 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Microsoft.ClassicStorage/storageAccounts/images/delete | 刪除給定的儲存體帳戶映像。 (已淘汰。 使用 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | 傳回儲存體帳戶映像作業狀態。 |
> | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | 讀取資源的作業狀態。 |
> | Microsoft.ClassicStorage/storageAccounts/osImages/read | 傳回儲存體帳戶的作業系統映像。 |
> | Microsoft.ClassicStorage/storageAccounts/osImages/write | 新增給定儲存體帳戶作業系統映像。 |
> | Microsoft.ClassicStorage/storageAccounts/osImages/delete | 刪除給定之儲存體帳戶的作業系統映像。 |
> | Microsoft.classicstorage/storageAccounts/providers/Microsoft. Insights/diagnosticSettings/read | 取得診斷設定。 |
> | Microsoft.classicstorage/storageAccounts/providers/Microsoft. Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> | Microsoft.classicstorage/storageAccounts/providers/Microsoft. Insights/metricDefinitions/read | 取得計量定義。 |
> | Microsoft.classicstorage/storageAccounts/queueServices/providers/Microsoft. Insights/diagnosticSettings/read | 取得診斷設定。 |
> | Microsoft.classicstorage/storageAccounts/queueServices/providers/Microsoft. Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> | Microsoft.classicstorage/storageAccounts/queueServices/providers/Microsoft. Insights/metricDefinitions/read | 取得計量定義。 |
> | Microsoft.ClassicStorage/storageAccounts/services/read | 取得可用服務。 |
> | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | 取得診斷設定。 |
> | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | 新增或修改診斷設定。 |
> | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | 取得計量定義。 |
> | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | 取得計量。 |
> | Microsoft.classicstorage/storageAccounts/tableServices/providers/Microsoft. Insights/diagnosticSettings/read | 取得診斷設定。 |
> | Microsoft.classicstorage/storageAccounts/tableServices/providers/Microsoft. Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> | Microsoft.classicstorage/storageAccounts/tableServices/providers/Microsoft. Insights/metricDefinitions/read | 取得計量定義。 |
> | Microsoft.ClassicStorage/storageAccounts/vmImages/read | 傳回虛擬機器映像。 |
> | Microsoft.ClassicStorage/storageAccounts/vmImages/write | 新增指定的虛擬機器映像。 |
> | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | 刪除指定的虛擬機器映像。 |
> | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | 取得指定的虛擬機器映像作業狀態。 |
> | Microsoft.ClassicStorage/vmImages/read | 列出虛擬機器映像。 |

### <a name="microsoftdatabox"></a>Microsoft.DataBox

Azure 服務： [Azure 資料箱](../databox/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.DataBox/jobs/read | 列出或取得訂單 |
> | Microsoft.DataBox/jobs/delete | 刪除訂單 |
> | Microsoft.DataBox/jobs/write | 建立或更新訂單 |
> | 資料箱/位置/availableSkus/讀取 | 列出或取得可用的 Sku |
> | Microsoft.DataBox/locations/operationResults/read | 列出或取得作業結果 |
> | 資料箱/operations/read | 列出或取得作業 |
> | **DataAction** | **說明** |
> | Microsoft.DataBox/register/action | 註冊提供者 Microsoft.Databox |
> | 資料箱/取消註冊/動作 | Un-Register 提供者資料箱 |
> | Microsoft.DataBox/jobs/cancel/action | 取消進行中的訂單。 |
> | Microsoft.DataBox/jobs/bookShipmentPickUp/action | 允許預約退貨商品的取貨。 |
> | Microsoft.DataBox/jobs/listCredentials/action | 列出與訂單相關的未加密認證。 |
> | 資料箱/位置/validateInputs/動作 | 此方法會執行所有類型的驗證。 |
> | Microsoft.DataBox/locations/validateAddress/action | 驗證出貨地址，並提供備用的地址 (若有的話)。 |
> | Microsoft.DataBox/locations/availableSkus/action | 此方法會傳回可用的 SKU 清單。 |
> | 資料箱/位置/regionConfiguration/動作 | 此方法會傳回區域的設定。 |
> | 資料箱/訂閱/resourceGroups/moveResources/action | 這個方法會執行資源移動。 |
> | 資料箱/訂閱/resourceGroups/validateMoveResources/action | 這個方法會驗證是否允許移動資源。 |

### <a name="microsoftimportexport"></a>Microsoft.ImportExport

Azure 服務： [azure 匯入/匯出](../storage/common/storage-import-export-service.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.ImportExport/register/action | 針對匯入/匯出資源提供者註冊訂用帳戶，並讓您能夠建立匯入/匯出作業。 |
> | Microsoft.ImportExport/jobs/write | 使用指定參數建立作業，或更新指定作業的屬性或標記。 |
> | Microsoft.ImportExport/jobs/read | 取得指定作業的屬性，或傳回作業清單。 |
> | Microsoft.ImportExport/jobs/listBitLockerKeys/action | 取得指定作業的 BitLocker 金鑰。 |
> | Microsoft.ImportExport/jobs/delete | 刪除現有作業。 |
> | Microsoft.ImportExport/locations/read | 取得指定位置的屬性，或傳回位置清單。 |
> | Microsoft.importexport/operations/read | 取得資源提供者所支援的作業。 |

### <a name="microsoftnetapp"></a>Microsoft.NetApp

Azure 服務： [Azure NetApp Files](../azure-netapp-files/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft NetApp/register/action | 訂用帳戶註冊動作 |
> | Microsoft. NetApp/取消註冊/動作 | 使用 Microsoft. NetApp 資源提供者取消註冊訂用帳戶 |
> | Microsoft.NetApp/locations/read | 讀取可用性檢查資源。 |
> | Microsoft NetApp/位置/checknameavailability/動作 | 檢查資源名稱是否可用 |
> | Microsoft NetApp/位置/checkfilepathavailability/動作 | 檢查檔案路徑是否可用 |
> | Microsoft.NetApp/locations/operationresults/read | 讀取作業結果資源。 |
> | Microsoft.NetApp/netAppAccounts/read | 讀取帳戶資源。 |
> | Microsoft.NetApp/netAppAccounts/write | 寫入帳戶資源。 |
> | Microsoft.NetApp/netAppAccounts/delete | 刪除帳戶資源。 |
> | Microsoft NetApp/netAppAccounts/accountBackups/read | 讀取帳戶備份資源。 |
> | Microsoft NetApp/netAppAccounts/accountBackups/write | 寫入帳戶備份資源。 |
> | Microsoft NetApp/netAppAccounts/accountBackups/delete | 刪除帳戶備份資源。 |
> | Microsoft NetApp/netAppAccounts/backupPolicies/read | 讀取備份原則資源。 |
> | Microsoft NetApp/netAppAccounts/backupPolicies/write | 寫入備份原則資源。 |
> | Microsoft NetApp/netAppAccounts/backupPolicies/delete | 刪除備份原則資源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/read | 讀取集區資源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/write | 寫入集區資源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/delete | 刪除集區資源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | 讀取磁碟區資源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | 寫入磁碟區資源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | 刪除磁碟區資源。 |
> | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/還原/動作 | 將磁片區還原至特定快照集 |
> | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/BreakReplication/動作 | 中斷磁片區複寫關聯 |
> | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/ReplicationStatus/動作 | 讀取磁片區複寫的狀態。 |
> | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/ReInitializeReplication/動作 | 嘗試重新初始化未初始化的複寫 |
> | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/AuthorizeReplication/動作 | 授權來源磁片區複寫 |
> | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/ResyncReplication/動作 | 重新同步處理目的地磁片區上的複寫 |
> | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/DeleteReplication/動作 | 刪除目的地磁片區上的複寫 |
> | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/備份/讀取 | 讀取備份資源。 |
> | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/備份/寫入 | 寫入備份資源。 |
> | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/備份/刪除 | 刪除備份資源。 |
> | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/MountTargets/read | 讀取掛接目標資源。 |
> | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/ReplicationStatus/read | 讀取磁片區複寫的狀態。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | 讀取快照集資源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | 寫入快照集資源。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | 刪除快照集資源。 |
> | Microsoft NetApp/netAppAccounts/snapshotPolicies/read | 讀取快照集原則資源。 |
> | Microsoft NetApp/netAppAccounts/snapshotPolicies/write | 寫入快照集原則資源。 |
> | Microsoft NetApp/netAppAccounts/snapshotPolicies/delete | 刪除快照集原則資源。 |
> | Microsoft. NetApp/netAppAccounts/snapshotPolicies/ListVolumes/action | 列出連接到快照集原則的磁片區 |
> | Microsoft NetApp/netAppAccounts/vault/read | 讀取保存庫資源。 |
> | Microsoft.NetApp/Operations/read | 讀取作業資源。 |

### <a name="microsoftstorage"></a>Microsoft.Storage

Azure 服務： [儲存體](../storage/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Storage/register/action | 針對儲存體資源提供者註冊訂用帳戶，並讓您能夠建立儲存體帳戶。 |
> | Microsoft.Storage/checknameavailability/read | 確認帳戶名稱有效，且並非使用中。 |
> | Microsoft. Storage/deletedAccounts/read |  |
> | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | 讓 Microsoft.Storage 知道虛擬網路或子網路即將刪除 |
> | Microsoft. 儲存體/位置/checknameavailability/讀取 | 確認帳戶名稱有效，且並非使用中。 |
> | Microsoft.Storage/locations/usages/read | 傳回指定訂用帳戶資源的限制和目前的使用量計數 |
> | Microsoft.Storage/operations/read | 輪詢非同步作業的狀態。 |
> | Microsoft.Storage/skus/read | 列出 Microsoft.Storage 所支援的 SKU。 |
> | Microsoft. Storage/storageAccounts/updateInternalProperties/action |  |
> | Microsoft. Storage/storageAccounts/hnsonmigration/action | 客戶可以中止儲存體帳戶上進行中的 Hns 遷移 |
> | Microsoft. Storage/storageAccounts/hnsonmigration/action | 客戶可以遷移至 hns 帳戶類型 |
> | Microsoft. Storage/storageAccounts/restoreBlobRanges/action | 將 blob 範圍還原至指定時間的狀態 |
> | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/action | 核准私人端點連線 |
> | Microsoft. 儲存體/storageAccounts/容錯移轉/動作 | 客戶能夠在發生可用性問題時控制容錯移轉 |
> | Microsoft.Storage/storageAccounts/listkeys/action | 傳回指定儲存體帳戶的存取金鑰。 |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | 重新產生指定儲存體帳戶的存取金鑰。 |
> | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | 撤銷所指定儲存體帳戶的所有使用者委派金鑰。 |
> | Microsoft.Storage/storageAccounts/delete | 刪除現有的儲存體帳戶。 |
> | Microsoft.Storage/storageAccounts/read | 傳回儲存體帳戶清單，或取得指定儲存體帳戶的屬性。 |
> | Microsoft.Storage/storageAccounts/listAccountSas/action | 傳回指定儲存體帳戶的帳戶 SAS 權杖。 |
> | Microsoft.Storage/storageAccounts/listServiceSas/action | 傳回指定儲存體帳戶的服務 SAS 權杖。 |
> | Microsoft.Storage/storageAccounts/write | 使用指定參數來建立儲存體帳戶、更新指定儲存體帳戶的屬性或標記，或新增指定儲存體帳戶的自訂網域。 |
> | Microsoft. Storage/storageAccounts/accountLocks/read |  |
> | Microsoft. Storage/storageAccounts/accountLocks/write |  |
> | Microsoft. Storage/storageAccounts/accountLocks/delete |  |
> | Microsoft.Storage/storageAccounts/blobServices/read | 列出 blob 服務 |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | 傳回 Blob 服務的使用者委派金鑰 |
> | Microsoft.Storage/storageAccounts/blobServices/write | 傳回放置 Blob 服務屬性的結果 |
> | Microsoft.Storage/storageAccounts/blobServices/read | 傳回 Blob 服務屬性或統計資料 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | 傳回修補 Blob 容器的結果 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | 傳回刪除容器的結果 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | 傳回容器 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | 傳回容器的清單 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | 傳回租用 Blob 容器的結果 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | 傳回放置 Blob 容器的結果 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | 清除 Blob 容器法務保存措施 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | 設定 Blob 容器法務保存措施 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | 擴充 Blob 容器不變性原則 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | 刪除 Blob 容器不變性原則 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | 放置 Blob 容器不變性原則 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | 鎖定 Blob 容器不變性原則 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | 取得 Blob 容器不變性原則 |
> | Microsoft. Storage/storageAccounts/dataSharePolicies/delete |  |
> | Microsoft. Storage/storageAccounts/dataSharePolicies/read |  |
> | Microsoft. Storage/storageAccounts/dataSharePolicies/read |  |
> | Microsoft. Storage/storageAccounts/dataSharePolicies/write |  |
> | Microsoft. Storage/storageAccounts/encryptionScopes/read |  |
> | Microsoft. Storage/storageAccounts/encryptionScopes/read |  |
> | Microsoft. Storage/storageAccounts/encryptionScopes/write |  |
> | Microsoft. Storage/storageAccounts/encryptionScopes/write |  |
> | Microsoft. Storage/storageAccounts/fileServices/共用/動作 | 還原檔案共用 |
> | Microsoft. Storage/storageAccounts/fileServices/read | 列出檔案服務 |
> | Microsoft. Storage/storageAccounts/fileServices/write | Put file service 屬性 |
> | Microsoft. Storage/storageAccounts/fileServices/read | 取得檔案服務屬性 |
> | Microsoft. Storage/storageAccounts/fileServices/共用/刪除 | 刪除檔案共用 |
> | Microsoft. Storage/storageAccounts/fileServices/共用/讀取 | 取得檔案共用 |
> | Microsoft. Storage/storageAccounts/fileServices/共用/讀取 | 列出檔案共用 |
> | Microsoft. Storage/storageAccounts/fileServices/共用/寫入 | 建立或更新檔案共用 |
> | Microsoft. Storage/storageAccounts/inventoryPolicies/delete |  |
> | Microsoft. Storage/storageAccounts/inventoryPolicies/read |  |
> | Microsoft. Storage/storageAccounts/inventoryPolicies/read |  |
> | Microsoft. Storage/storageAccounts/inventoryPolicies/write |  |
> | Microsoft. Storage/storageAccounts/localUsers/delete | 刪除本機使用者 |
> | Microsoft. Storage/storageAccounts/localusers/listKeys/action | 列出本機使用者金鑰 |
> | Microsoft. Storage/storageAccounts/localusers/read | 列出本機使用者 |
> | Microsoft. Storage/storageAccounts/localusers/read | 取得本機使用者 |
> | Microsoft. Storage/storageAccounts/localusers/write | 建立或更新本機使用者 |
> | Microsoft. Storage/storageAccounts/managementPolicies/delete | 刪除儲存體帳戶管理原則 |
> | Microsoft. Storage/storageAccounts/managementPolicies/read | 取得儲存體管理帳戶原則 |
> | Microsoft. Storage/storageAccounts/managementPolicies/write | 放置儲存體帳戶管理原則 |
> | Microsoft. Storage/storageAccounts/objectReplicationPolicies/delete | 刪除物件複寫原則 |
> | Microsoft. Storage/storageAccounts/objectReplicationPolicies/read | 取得物件複寫原則 |
> | Microsoft. Storage/storageAccounts/objectReplicationPolicies/read | 列出物件複寫原則 |
> | Microsoft. Storage/storageAccounts/objectReplicationPolicies/write | 建立或更新物件複寫原則 |
> | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/read | 取得私人端點連線 Proxy |
> | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/delete | 刪除私人端點連線 proxy |
> | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/write | 放置私人端點連線 proxy |
> | Microsoft. Storage/storageAccounts/privateEndpointConnections/read | 列出私人端點連接 |
> | Microsoft. Storage/storageAccounts/privateEndpointConnections/delete | 刪除私人端點連線 |
> | Microsoft. Storage/storageAccounts/privateEndpointConnections/read | 取得私人端點連線 |
> | Microsoft. Storage/storageAccounts/privateEndpointConnections/write | 放置私人端點連線 |
> | Microsoft. Storage/storageAccounts/privateLinkResources/read | 取得 StorageAccount groupids |
> | Microsoft.Storage/storageAccounts/queueServices/read |  |
> | Microsoft.Storage/storageAccounts/queueServices/write |  |
> | Microsoft.Storage/storageAccounts/queueServices/read | 取得佇列服務屬性 |
> | Microsoft.Storage/storageAccounts/queueServices/read | 傳回佇列服務屬性或統計資料。 |
> | Microsoft.Storage/storageAccounts/queueServices/write | 傳回設定佇列服務屬性的結果 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | 傳回佇列或佇列清單。 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | 傳回寫入佇列的結果 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | 傳回刪除佇列的結果 |
> | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | 建立/更新儲存體帳戶的診斷設定。 |
> | Microsoft. Storage/storageAccounts/tableServices/read |  |
> | Microsoft. Storage/storageAccounts/tableServices/read | 取得表格服務屬性 |
> | Microsoft. Storage/storageAccounts/tableServices/write |  |
> | Microsoft. 儲存體/storageAccounts/tableServices/資料表/刪除 |  |
> | Microsoft. Storage/storageAccounts/tableServices/tables/read |  |
> | Microsoft. Storage/storageAccounts/tableServices/tables/read |  |
> | Microsoft. Storage/storageAccounts/tableServices/tables/write |  |
> | Microsoft.Storage/usages/read | 傳回指定訂用帳戶資源的限制和目前的使用量計數 |
> | **DataAction** | **說明** |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | 傳回 Blob 或 Blob 清單 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | 傳回寫入 Blob 的結果 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | 傳回刪除 Blob 的結果 |
> | Microsoft. Storage/storageAccounts/blobServices/container/blob/deleteBlobVersion/action | 傳回刪除 blob 版本的結果 |
> | Microsoft. Storage/storageAccounts/blobServices/container/blob/permanentDelete/action |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | 傳回新增 Blob 內容的結果 |
> | Microsoft. 儲存體/storageAccounts/blobServices/容器/blob/篩選/動作 | 傳回具有相符標記篩選之帳戶下的 blob 清單 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action | 將 blob 從一個路徑移到另一個路徑 |
> | Microsoft. Storage/storageAccounts/blobServices/container/blob/manageOwnership/action | 變更 blob 的擁有權 |
> | Microsoft. Storage/storageAccounts/blobServices/container/blob/modifyPermissions/action | 修改 blob 的許可權 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | 傳回 blob 命令的結果 |
> | Microsoft. Storage/storageAccounts/blobServices/container/blob/tag/read | 傳回讀取 blob 標記的結果 |
> | Microsoft. Storage/storageAccounts/blobServices/container/blob/tag/write | 傳回寫入 blob 標記的結果 |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | 傳回檔案/資料夾或檔案/資料夾的清單 |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | 傳回寫入檔案或建立資料夾的結果 |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | 傳回刪除檔案/資料夾的結果 |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | 傳回在檔案/資料夾上修改許可權的結果 |
> | Microsoft. Storage/storageAccounts/fileServices/檔案共用/files/actassuperuser/action | 取得檔案系統管理員許可權 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | 傳回訊息 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | 傳回寫入訊息的結果 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | 傳回刪除訊息的結果 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | 傳回新增訊息的結果 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | 傳回處理訊息的結果 |

### <a name="microsoftstoragesync"></a>microsoft.storagesync

Azure 服務： [儲存體](../storage/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | microsoft.storagesync/註冊/動作 | 為儲存體同步處理提供者註冊訂用帳戶 |
> | microsoft.storagesync/取消註冊/動作 | 取消註冊儲存體同步提供者的訂用帳戶 |
> | microsoft.storagesync/locations/checkNameAvailability/action | 確認儲存體同步服務名稱有效，且並非使用中。 |
> | microsoft.storagesync/位置/operationresults/讀取 | 取得非同步作業的結果 |
> | microsoft.storagesync/位置/作業/讀取 | 取得 azure 非同步作業的狀態 |
> | microsoft.storagesync/locations/workflows/operations/read | 取得非同步作業的狀態 |
> | microsoft.storagesync/operations/read | 取得支援的作業清單 |
> | microsoft.storagesync/storageSyncServices/read | 讀取任何儲存體同步服務 |
> | microsoft.storagesync/storageSyncServices/write | 建立或更新任何儲存體同步服務 |
> | microsoft.storagesync/storageSyncServices/delete | 刪除任何儲存體同步服務 |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnectionProxies/validate/action | 驗證任何私人端點 ConnectionProxies |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnectionProxies/read | 讀取任何私人端點 ConnectionProxies |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnectionProxies/write | 建立或更新任何私人端點 ConnectionProxies |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnectionProxies/delete | 刪除任何私人端點 ConnectionProxies |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnections/read | 讀取任何私人端點連接 |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnections/write | 建立或更新任何私人端點連接 |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnections/delete | 刪除任何私人端點連接 |
> | microsoft.storagesync/storageSyncServices/privateLinkResources/read | 讀取任何 Private Link 資源 |
> | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | 取得儲存體同步服務的可用計量 |
> | microsoft.storagesync/storageSyncServices/registeredServers/read | 讀取任何已註冊的伺服器 |
> | microsoft.storagesync/storageSyncServices/registeredServers/write | 建立或更新任何已註冊的伺服器 |
> | microsoft.storagesync/storageSyncServices/registeredServers/delete | 刪除任何已註冊的伺服器 |
> | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | 取得已註冊的伺服器可用的計量 |
> | microsoft.storagesync/storageSyncServices/syncGroups/read | 讀取任何同步群組 |
> | microsoft.storagesync/storageSyncServices/syncGroups/write | 建立或更新任何同步群組 |
> | microsoft.storagesync/storageSyncServices/syncGroups/delete | 刪除任何同步群組 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | 讀取任何雲端端點 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | 建立或更新任何雲端端點 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | 刪除任何雲端端點 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | 在備份之前呼叫此動作 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | 在備份之後呼叫此動作 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | 在還原之前呼叫此動作 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | 在還原之後呼叫此動作 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | 還原活動訊號 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/triggerChangeDetection/action | 呼叫此動作以觸發雲端端點檔案共用上的變更偵測 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | 取得非同步備份/還原作業的狀態 |
> | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | 取得同步群組的可用計量 |
> | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | 讀取任何伺服器端點 |
> | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | 建立或更新任何伺服器端點 |
> | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | 刪除任何伺服器端點 |
> | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | 呼叫此動作以將檔案回復到伺服器 |
> | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | 取得伺服器端點的可用計量 |
> | microsoft.storagesync/storageSyncServices/workflows/read | 讀取工作流程 |
> | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | 取得非同步作業的狀態 |
> | microsoft.storagesync/storageSyncServices/workflows/operations/read | 取得非同步作業的狀態 |

### <a name="microsoftstorsimple"></a>Microsoft.StorSimple

Azure 服務： [StorSimple](../storsimple/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.StorSimple/register/action | 註冊提供者 Microsoft.StorSimple |
> | Microsoft.StorSimple/managers/clearAlerts/action | 清除與裝置管理員相關聯的所有警示。 |
> | Microsoft.StorSimple/managers/getEncryptionKey/action | 取得裝置管理員的加密金鑰。 |
> | Microsoft.StorSimple/managers/read | 列出或取得裝置管理員 |
> | Microsoft.StorSimple/managers/delete | 刪除裝置管理員 |
> | Microsoft.StorSimple/managers/write | 建立或更新裝置管理員 |
> | Microsoft.StorSimple/managers/configureDevice/action | 設定裝置 |
> | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | 從傳統移轉至 Resource Manager |
> | Microsoft.StorSimple/managers/listActivationKey/action | 取得 StorSimple 裝置管理員的啟用金鑰。 |
> | Microsoft.StorSimple/managers/regenerateActivationKey/action | 重新產生現有 StorSimple 裝置管理員的啟用金鑰。 |
> | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | 列出 StorSimple 裝置管理員的公用加密金鑰。 |
> | Microsoft.StorSimple/managers/provisionCloudAppliance/action | 建立新的雲端應用裝置。 |
> | Microsoft.StorSimple/Managers/write | 「建立保存庫」作業會建立 'vault' 類型的 Azure 資源 |
> | Microsoft.StorSimple/Managers/read | 「取得保存庫」作業會取得物件，此物件代表 'vault' 類型的 Azure 資源 |
> | Microsoft.StorSimple/Managers/delete | 「刪除保存庫」作業會刪除 'vault' 類型的指定 Azure 資源 |
> | Microsoft.StorSimple/managers/accessControlRecords/read | 列出或取得存取控制記錄 |
> | Microsoft.StorSimple/managers/accessControlRecords/write | 建立或更新存取控制記錄 |
> | Microsoft.StorSimple/managers/accessControlRecords/delete | 刪除存取控制記錄 |
> | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | 列出或取得作業結果 |
> | Microsoft.StorSimple/managers/alerts/read | 列出或取得警示 |
> | Microsoft.StorSimple/managers/backups/read | 列出或取得備份組 |
> | Microsoft.StorSimple/managers/bandwidthSettings/read | 列出頻寬設定 (僅限 8000 系列) |
> | Microsoft.StorSimple/managers/bandwidthSettings/write | 新建或更新頻寬設定 (僅限 8000 系列) |
> | Microsoft.StorSimple/managers/bandwidthSettings/delete | 刪除現有頻寬設定 (僅限 8000 系列) |
> | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | 列出作業結果 |
> | Microsoft.StorSimple/managers/certificates/write | 建立或更新憑證 |
> | Microsoft.StorSimple/Managers/certificates/write | 「更新資源憑證」作業會更新資源/保存庫的認證憑證。 |
> | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | 列出雲端應用裝置所支援的組態 |
> | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | 傳送測試警示電子郵件給所設定的電子郵件收件者。 |
> | Microsoft.StorSimple/managers/devices/scanForUpdates/action | 掃描裝置的更新。 |
> | Microsoft.StorSimple/managers/devices/download/action | 下載裝置的更新。 |
> | Microsoft.StorSimple/managers/devices/install/action | 在裝置上安裝更新。 |
> | Microsoft.StorSimple/managers/devices/read | 列出或取得裝置 |
> | Microsoft.StorSimple/managers/devices/write | 建立或更新裝置 |
> | Microsoft.StorSimple/managers/devices/delete | 刪除裝置 |
> | Microsoft.StorSimple/managers/devices/deactivate/action | 停用裝置。 |
> | Microsoft.StorSimple/managers/devices/failover/action | 裝置的容錯移轉。 |
> | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | 發佈現有裝置的支援套件。 StorSimple 支援封裝是一種簡便的機制，可收集所有相關的記錄以協助 Microsoft 支援服務針對任何 StorSimple 裝置問題進行疑難排解。 |
> | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | 授權裝置的服務加密金鑰變換 |
> | Microsoft.StorSimple/managers/devices/installUpdates/action | 在裝置 (僅限 8000 系列) 上安裝更新。 |
> | Microsoft.StorSimple/managers/devices/listFailoverSets/action | 列出現有裝置 (僅限 8000 系列) 的容錯移轉集。 |
> | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | 列出裝置 (僅限 8000 系列) 的容錯移轉目標。 |
> | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | 列出裝置管理員的公用加密金鑰 |
> | Microsoft.StorSimple/managers/devices/alertSettings/read | 列出或取得警示設定 |
> | Microsoft.StorSimple/managers/devices/alertSettings/write | 建立或更新警示設定 |
> | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | 列出或取得作業結果 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/write | 新建或更新備份原則 (僅限 8000 系列) |
> | Microsoft.StorSimple/managers/devices/backupPolicies/read | 列出備份原則 (僅限 8000 系列) |
> | Microsoft.StorSimple/managers/devices/backupPolicies/delete | 刪除現有的備份原則 (僅限 8000 系列) |
> | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | 製作手動備份，以針對原則所保護的所有磁碟區建立隨選備份。 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | 列出作業結果 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | 新建或更新排程 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | 列出排程 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | 刪除現有排程 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | 列出作業結果 |
> | Microsoft.StorSimple/managers/devices/backups/read | 列出或取得備份組 |
> | Microsoft.StorSimple/managers/devices/backups/delete | 刪除備份組 |
> | Microsoft.StorSimple/managers/devices/backups/restore/action | 從備份組還原所有磁碟區。 |
> | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | 使用備份元素來複製共用或磁碟區。 |
> | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | 列出或取得作業結果 |
> | Microsoft.StorSimple/managers/devices/backups/operationResults/read | 列出或取得作業結果 |
> | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | 列出或取得備份排程群組 |
> | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | 建立或更新備份排程群組 |
> | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | 刪除備份排程群組 |
> | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | 列出或取得作業結果 |
> | Microsoft.StorSimple/managers/devices/chapSettings/write | 建立或更新 Chap 設定 |
> | Microsoft.StorSimple/managers/devices/chapSettings/read | 列出或取得 Chap 設定 |
> | Microsoft.StorSimple/managers/devices/chapSettings/delete | 刪除 Chap 設定 |
> | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | 列出或取得作業結果 |
> | Microsoft.StorSimple/managers/devices/disks/read | 列出或取得磁碟 |
> | Microsoft.StorSimple/managers/devices/failover/operationResults/read | 列出或取得作業結果 |
> | Microsoft.StorSimple/managers/devices/failoverTargets/read | 列出或取得裝置的容錯移轉目標 |
> | Microsoft.StorSimple/managers/devices/fileservers/read | 列出或取得檔案伺服器 |
> | Microsoft.StorSimple/managers/devices/fileservers/write | 建立或更新檔案伺服器 |
> | Microsoft.StorSimple/managers/devices/fileservers/delete | 刪除檔案伺服器 |
> | Microsoft.StorSimple/managers/devices/fileservers/backup/action | 製作檔案伺服器的備份。 |
> | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | 列出或取得計量 |
> | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | 列出或取得計量定義 |
> | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | 列出或取得作業結果 |
> | Microsoft.StorSimple/managers/devices/fileservers/shares/write | 建立或更新共用 |
> | Microsoft.StorSimple/managers/devices/fileservers/shares/read | 列出或取得共用 |
> | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | 刪除共用 |
> | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | 列出或取得計量 |
> | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | 列出或取得計量定義 |
> | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | 列出或取得作業結果 |
> | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | 列出硬體元件群組 |
> | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | 變更硬體元件群組的控制器電源狀態 |
> | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | 列出作業結果 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/read | 列出或取得 iSCSI 伺服器 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/write | 建立或更新 iSCSI 伺服器 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/delete | 刪除 iSCSI 伺服器 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | 製作 iSCSI 伺服器的備份。 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | 列出或取得磁碟 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | 建立或更新磁碟 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | 刪除磁碟 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | 列出或取得計量 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | 列出或取得計量定義 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | 列出或取得作業結果 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | 列出或取得計量 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | 列出或取得計量定義 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | 列出或取得作業結果 |
> | Microsoft.StorSimple/managers/devices/jobs/read | 列出或取得作業 |
> | Microsoft.StorSimple/managers/devices/jobs/cancel/action | 取消正在執行的作業 |
> | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | 列出作業結果 |
> | Microsoft.StorSimple/managers/devices/metrics/read | 列出或取得計量 |
> | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | 列出或取得計量定義 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | 匯入用於移轉的來源組態 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | 啟動作業來預估移轉程序的持續時間。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | 使用來源組態來開始移轉 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | 確認移轉成功，並加以認可。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | 提取移轉估計作業的狀態。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | 提取移轉狀態。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | 提取移轉的確認狀態。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | 列出確認移轉狀態 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | 列出移轉估計 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | 列出移轉狀態 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | 列出作業結果 |
> | Microsoft.StorSimple/managers/devices/networkSettings/read | 列出或取得網路設定 |
> | Microsoft.StorSimple/managers/devices/networkSettings/write | 新建或更新網路設定 |
> | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | 列出作業結果 |
> | Microsoft.StorSimple/managers/devices/operationResults/read | 列出或取得作業結果 |
> | Microsoft.StorSimple/managers/devices/securitySettings/update/action | 更新安全性設定。 |
> | Microsoft.StorSimple/managers/devices/securitySettings/read | 列出安全性設定 |
> | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | 同步處理裝置的遠端管理憑證。 |
> | Microsoft.StorSimple/managers/devices/securitySettings/write | 新建或更新安全性設定 |
> | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | 列出或取得作業結果 |
> | Microsoft.StorSimple/managers/devices/shares/read | 列出或取得共用 |
> | Microsoft.StorSimple/managers/devices/timeSettings/read | 列出或取得時間設定 |
> | Microsoft.StorSimple/managers/devices/timeSettings/write | 新建或更新時間設定 |
> | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | 列出作業結果 |
> | Microsoft.StorSimple/managers/devices/updates/operationResults/read | 列出或取得作業結果 |
> | Microsoft.StorSimple/managers/devices/updateSummary/read | 列出或取得更新摘要 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/write | 新建或更新磁碟區容器 (僅限 8000 系列) |
> | Microsoft.StorSimple/managers/devices/volumeContainers/read | 列出磁碟區容器 (僅限 8000 系列) |
> | Microsoft.StorSimple/managers/devices/volumeContainers/delete | 刪除現有的磁碟區容器 (僅限 8000 系列) |
> | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | 列出計量 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | 列出計量定義 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | 列出作業結果 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | 列出磁碟區 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | 新建或更新磁碟區 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | 刪除現有磁碟區 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | 列出計量 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | 列出計量定義 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | 列出作業結果 |
> | Microsoft.StorSimple/managers/devices/volumes/read | 列出磁碟區 |
> | Microsoft.StorSimple/managers/encryptionSettings/read | 列出或取得加密設定 |
> | Microsoft.StorSimple/managers/extendedInformation/read | 列出或取得延伸的保存庫資訊 |
> | Microsoft.StorSimple/managers/extendedInformation/write | 建立或更新延伸的保存庫資訊 |
> | Microsoft.StorSimple/managers/extendedInformation/delete | 刪除擴充的保存庫資訊 |
> | Microsoft.StorSimple/Managers/extendedInformation/read | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | Microsoft.StorSimple/Managers/extendedInformation/write | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | Microsoft.StorSimple/Managers/extendedInformation/delete | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | Microsoft.StorSimple/managers/features/read | 列出功能 |
> | Microsoft.StorSimple/managers/fileservers/read | 列出或取得檔案伺服器 |
> | Microsoft.StorSimple/managers/iscsiservers/read | 列出或取得 iSCSI 伺服器 |
> | Microsoft.StorSimple/managers/jobs/read | 列出或取得作業 |
> | Microsoft.StorSimple/managers/metrics/read | 列出或取得計量 |
> | Microsoft.StorSimple/managers/metricsDefinitions/read | 列出或取得計量定義 |
> | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | 列出移轉來源設定 (僅限 8000 系列) |
> | Microsoft.StorSimple/managers/operationResults/read | 列出或取得作業結果 |
> | Microsoft.StorSimple/managers/storageAccountCredentials/write | 建立或更新儲存體帳戶認證 |
> | Microsoft.StorSimple/managers/storageAccountCredentials/read | 列出或取得儲存體帳戶認證 |
> | Microsoft.StorSimple/managers/storageAccountCredentials/delete | 刪除儲存體帳戶認證 |
> | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | 列出或取得作業結果 |
> | Microsoft.StorSimple/managers/storageDomains/read | 列出或取得儲存體網域 |
> | Microsoft.StorSimple/managers/storageDomains/write | 建立或更新儲存體網域 |
> | Microsoft.StorSimple/managers/storageDomains/delete | 刪除儲存體網域 |
> | Microsoft.StorSimple/managers/storageDomains/operationResults/read | 列出或取得作業結果 |
> | Microsoft.StorSimple/operations/read | 列出或取得作業 |

## <a name="web"></a>Web

### <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

Azure 服務： [App Service 憑證](../app-service/configure-ssl-certificate.md#import-an-app-service-certificate)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | 為服務應用程式主體佈建服務主體 |
> | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | 驗證憑證購買物件，但不將其提交出去 |
> | Microsoft.CertificateRegistration/register/action | 針對訂用帳戶註冊 Microsoft 憑證資源提供者 |
> | Microsoft.CertificateRegistration/certificateOrders/Write | 新增 certificateOrder 或更新現有 certificateOrder |
> | Microsoft.CertificateRegistration/certificateOrders/Delete | 刪除現有的 AppServiceCertificate |
> | Microsoft.CertificateRegistration/certificateOrders/Read | 取得 CertificateOrders 的清單 |
> | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | 重新發行現有的 certificateorder |
> | Microsoft.CertificateRegistration/certificateOrders/renew/Action | 更新現有的 certificateorder |
> | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | 擷取憑證動作的清單 |
> | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | 擷取憑證電子郵件的歷程記錄 |
> | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | 重新傳送憑證電子郵件 |
> | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | 確認網域擁有權 |
> | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | 將要求電子郵件重新傳送至其他電子郵件地址 |
> | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | 擷取已發行之 App Service 憑證的網站密封 |
> | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | 新增憑證或更新現有憑證 |
> | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | 刪除現有憑證 |
> | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | 取得憑證清單 |
> | Microsoft.CertificateRegistration/operations/Read | 列出 App Service 憑證註冊的所有作業 |

### <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

Azure 服務： [App Service](../app-service/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.DomainRegistration/generateSsoRequest/Action | 產生要求以便登入網域控制中心。 |
> | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | 驗證網域購買物件，但不將其提交出去 |
> | Microsoft.DomainRegistration/checkDomainAvailability/Action | 檢查網域是否可供購買 |
> | Microsoft.DomainRegistration/listDomainRecommendations/Action | 根據關鍵字來擷取清單網域建議 |
> | Microsoft.DomainRegistration/register/action | 針對訂用帳戶註冊 Microsoft 網域資源提供者 |
> | Microsoft.DomainRegistration/domains/Read | 取得網域清單 |
> | Microsoft.DomainRegistration/domains/Read | 取得網域 |
> | Microsoft.DomainRegistration/domains/Write | 新增網域或更新現有網域 |
> | Microsoft.DomainRegistration/domains/Delete | 刪除現有網域。 |
> | Microsoft.DomainRegistration/domains/renew/Action | 更新現有網域。 |
> | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 列出擁有權識別碼 |
> | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 取得擁有權識別碼 |
> | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | 建立或更新識別碼 |
> | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | 刪除擁有權識別碼 |
> | Microsoft.DomainRegistration/domains/operationresults/Read | 取得網域作業 |
> | Microsoft.DomainRegistration/operations/Read | 列出 App Service 網域註冊的所有作業 |
> | Microsoft.DomainRegistration/topLevelDomains/Read | 取得最上層網域 |
> | Microsoft.DomainRegistration/topLevelDomains/Read | 取得最上層網域 |
> | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | 列出合約動作 |

### <a name="microsoftmaps"></a>Microsoft.Maps

Azure 服務： [Azure 地圖服務](../azure-maps/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Maps/register/action | 註冊提供者 |
> | Microsoft.Maps/accounts/write | 建立或更新地圖服務帳戶。 |
> | Microsoft.Maps/accounts/read | 取得地圖服務帳戶。 |
> | Microsoft.Maps/accounts/delete | 刪除地圖服務帳戶。 |
> | Microsoft.Maps/accounts/listKeys/action | 列出 Maps 帳戶金鑰。 |
> | Microsoft.Maps/accounts/regenerateKey/action | 產生新的 Maps 帳戶主要或次要金鑰。 |
> | Microsoft.Maps/accounts/eventGridFilters/delete | 刪除事件方格篩選。 |
> | Microsoft.Maps/accounts/eventGridFilters/read | 取得事件格線篩選 |
> | Microsoft.Maps/accounts/eventGridFilters/write | 建立或更新事件方格篩選。 |
> | Microsoft. Maps/accounts/privateAtlases/write | 建立或更新私人的塔。 |
> | Microsoft. Maps/accounts/privateAtlases/read | 取得私人的塔。 |
> | Microsoft. Maps/accounts/privateAtlases/delete | 刪除私人的塔。 |
> | Microsoft. Maps/operations/read | 讀取提供者作業 |
> | **DataAction** | **說明** |
> | Microsoft. Maps/accounts/services/data/read | 允許讀取資料上傳服務和私用的資料。 |
> | Microsoft. Maps/accounts/services/data/delete | 允許刪除資料上傳服務和私用的資料 |
> | Microsoft. Maps/accounts/services/data/write | 允許寫入或更新資料上傳服務和私用的資料。 |
> | Microsoft. Maps/accounts/services/提高許可權/讀取 | 允許讀取提高許可權服務的資料。 |
> | Microsoft. Maps/accounts/services/地理位置/讀取 | 允許讀取地理位置服務的資料。 |
> | Microsoft. Maps/accounts/services/行動/讀取 | 允許讀取行動服務的資料。 |
> | Microsoft. Maps/accounts/services/render/read | 允許讀取轉譯服務的資料。 |
> | Microsoft. Maps/accounts/services/route/read | 允許讀取路由服務的資料。 |
> | Microsoft. Maps/accounts/services/search/read | 允許讀取搜尋服務的資料。 |
> | Microsoft. Maps/accounts/services/空間/讀取 | 允許讀取空間服務的資料。 |
> | Microsoft. Maps/accounts/services/空間/寫入 | 允許寫入空間服務的資料，例如事件發行。 |
> | Microsoft. Maps/accounts/services/時區/讀取 | 允許讀取時區服務的資料。 |
> | Microsoft. Maps/accounts/services/流量/讀取 | 允許讀取流量服務的資料。 |
> | Microsoft. Maps/accounts/services/turnbyturn/read | 允許讀取 TurnByTurn 服務的資料。 |
> | Microsoft. Maps/accounts/services/氣象/read | 允許讀取天氣服務的資料。 |

### <a name="microsoftmedia"></a>Microsoft.Media

Azure 服務： [媒體服務](../media-services/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Media/register/action | 為媒體服務資源提供者註冊訂用帳戶，並讓您能夠建立媒體服務帳戶 |
> | Microsoft.Media/unregister/action | 為媒體服務資源提供者取消註冊訂用帳戶 |
> | Microsoft.Media/checknameavailability/action | 檢查媒體服務帳戶名稱是否可供使用 |
> | Microsoft.Media/locations/checkNameAvailability/action | 檢查媒體服務帳戶名稱是否可供使用 |
> | Microsoft.Media/mediaservices/read | 讀取任何媒體服務帳戶 |
> | Microsoft.Media/mediaservices/write | 建立或更新任何媒體服務帳戶 |
> | Microsoft.Media/mediaservices/delete | 刪除任何媒體服務帳戶 |
> | Microsoft. Media/windowsazure.mediaservices/regenerateKey/action | 重新產生媒體服務 ACS 金鑰 |
> | Microsoft. Media/windowsazure.mediaservices/listKeys/action | 列出媒體服務帳戶的 ACS 金鑰 |
> | Microsoft.Media/mediaservices/syncStorageKeys/action | 同步處理已連結之 Azure 儲存體帳戶的儲存體金鑰 |
> | Microsoft. Media/windowsazure.mediaservices/listEdgePolicies/action | 列出 edge 裝置的原則。 |
> | Microsoft.Media/mediaservices/accountfilters/read | 讀取任何帳戶篩選條件 |
> | Microsoft.Media/mediaservices/accountfilters/write | 建立或更新任何帳戶篩選條件 |
> | Microsoft.Media/mediaservices/accountfilters/delete | 刪除任何帳戶篩選條件 |
> | Microsoft.Media/mediaservices/assets/read | 讀取任何資產 |
> | Microsoft.Media/mediaservices/assets/write | 建立或更新任何資產 |
> | Microsoft.Media/mediaservices/assets/delete | 刪除任何資產 |
> | Microsoft.Media/mediaservices/assets/listContainerSas/action | 列出資產容器 SAS URL |
> | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | 取得資產加密金鑰 |
> | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | 列出資產的串流定位器 |
> | Microsoft.Media/mediaservices/assets/assetfilters/read | 讀取任何資產篩選條件 |
> | Microsoft.Media/mediaservices/assets/assetfilters/write | 建立或更新任何資產篩選條件 |
> | Microsoft.Media/mediaservices/assets/assetfilters/delete | 刪除任何資產篩選條件 |
> | Microsoft.Media/mediaservices/contentKeyPolicies/read | 讀取任何內容金鑰原則 |
> | Microsoft.Media/mediaservices/contentKeyPolicies/write | 建立或更新任何內容金鑰原則 |
> | Microsoft.Media/mediaservices/contentKeyPolicies/delete | 刪除任何內容金鑰原則 |
> | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | 取得含有祕密的原則屬性 |
> | Microsoft.Media/mediaservices/eventGridFilters/read | 讀取任何事件方格篩選 |
> | Microsoft.Media/mediaservices/eventGridFilters/write | 建立或更新任何事件方格篩選 |
> | Microsoft.Media/mediaservices/eventGridFilters/delete | 刪除任何事件方格篩選 |
> | Microsoft.Media/mediaservices/liveEventOperations/read | 讀取任何即時事件作業 |
> | Microsoft.Media/mediaservices/liveEvents/read | 讀取任何即時事件 |
> | Microsoft.Media/mediaservices/liveEvents/write | 建立或更新任何即時事件 |
> | Microsoft.Media/mediaservices/liveEvents/delete | 刪除任何即時事件 |
> | Microsoft.Media/mediaservices/liveEvents/start/action | 啟動任何即時事件作業 |
> | Microsoft.Media/mediaservices/liveEvents/stop/action | 停止任何即時事件作業 |
> | Microsoft.Media/mediaservices/liveEvents/reset/action | 重設任何即時事件作業 |
> | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | 讀取任何即時輸出 |
> | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | 建立或更新任何即時輸出 |
> | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | 刪除任何即時輸出 |
> | Microsoft. Media/windowsazure.mediaservices/Liveevent/privateEndpointConnectionProxies/read | 讀取任何即時事件私人端點連接 Proxy |
> | Microsoft. Media/windowsazure.mediaservices/Liveevent/privateEndpointConnectionProxies/write | 建立即時活動私人端點連接 Proxy |
> | Microsoft. Media/windowsazure.mediaservices/Liveevent/privateEndpointConnectionProxies/delete | 刪除實況活動私人端點連接 Proxy |
> | Microsoft. Media/windowsazure.mediaservices/Liveevent/privateEndpointConnectionProxies/validate/action | 驗證實況活動私人端點連接 Proxy |
> | Microsoft. Media/windowsazure.mediaservices/Liveevent/privateEndpointConnections/read | 讀取任何即時事件私人端點連接 |
> | Microsoft. Media/windowsazure.mediaservices/Liveevent/privateEndpointConnections/write | 建立即時活動私人端點連接 |
> | Microsoft. Media/windowsazure.mediaservices/Liveevent/privateEndpointConnections/delete | 刪除實況活動私人端點連接 |
> | Microsoft. Media/windowsazure.mediaservices/Liveevent/privateLinkResources/read | 讀取任何即時事件 Private Link 資源 |
> | Microsoft.Media/mediaservices/liveOutputOperations/read | 讀取任何即時輸出作業 |
> | Microsoft. Media/windowsazure.mediaservices/mediaGraphs/read | 讀取任何 Media Graph |
> | Microsoft. Media/windowsazure.mediaservices/mediaGraphs/write | 建立或更新任何 Media Graph |
> | Microsoft. Media/windowsazure.mediaservices/mediaGraphs/delete | 刪除任何 Media Graph |
> | Microsoft. Media/windowsazure.mediaservices/mediaGraphs/start/action | 啟動任何 Media Graph 作業 |
> | Microsoft. Media/windowsazure.mediaservices/mediaGraphs/stop/action | 停止任何 Media Graph 操作 |
> | Microsoft. Media/windowsazure.mediaservices/privateEndpointConnectionOperations/read | 讀取任何私人端點連接作業 |
> | Microsoft. Media/windowsazure.mediaservices/privateEndpointConnectionProxies/read | 讀取任何私人端點連接 Proxy |
> | Microsoft. Media/windowsazure.mediaservices/privateEndpointConnectionProxies/write | 建立私人端點連接 Proxy |
> | Microsoft. Media/windowsazure.mediaservices/privateEndpointConnectionProxies/delete | 刪除私人端點連線 Proxy |
> | Microsoft. Media/windowsazure.mediaservices/privateEndpointConnectionProxies/validate/action | 驗證私人端點連線 Proxy |
> | Microsoft. Media/windowsazure.mediaservices/privateEndpointConnections/read | 讀取任何私人端點連接 |
> | Microsoft. Media/windowsazure.mediaservices/privateEndpointConnections/write | 建立私人端點連接 |
> | Microsoft. Media/windowsazure.mediaservices/privateEndpointConnections/delete | 刪除私人端點連線 |
> | Microsoft. Media/windowsazure.mediaservices/privateLinkResources/read | 讀取任何 Private Link 資源 |
> | Microsoft.Media/mediaservices/streamingEndpointOperations/read | 讀取任何串流端點作業 |
> | Microsoft.Media/mediaservices/streamingEndpoints/read | 讀取任何串流端點 |
> | Microsoft.Media/mediaservices/streamingEndpoints/write | 建立或更新任何串流端點 |
> | Microsoft.Media/mediaservices/streamingEndpoints/delete | 刪除任何串流端點 |
> | Microsoft.Media/mediaservices/streamingEndpoints/start/action | 啟動任何串流端點作業 |
> | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | 停止任何串流端點作業 |
> | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | 調整任何串流端點作業 |
> | Microsoft. Media/windowsazure.mediaservices/Streamingendpoint/privateEndpointConnectionProxies/read | 讀取任何串流端點私人端點連接 Proxy |
> | Microsoft. Media/windowsazure.mediaservices/Streamingendpoint/privateEndpointConnectionProxies/write | 建立串流端點私人端點連接 Proxy |
> | Microsoft. Media/windowsazure.mediaservices/Streamingendpoint/privateEndpointConnectionProxies/delete | 刪除串流端點私人端點連接 Proxy |
> | Microsoft. Media/windowsazure.mediaservices/Streamingendpoint/privateEndpointConnectionProxies/validate/action | 驗證串流端點私人端點連接 Proxy |
> | Microsoft. Media/windowsazure.mediaservices/Streamingendpoint/privateEndpointConnections/read | 讀取任何串流端點私人端點連接 |
> | Microsoft. Media/windowsazure.mediaservices/Streamingendpoint/privateEndpointConnections/write | 建立串流端點私人端點連接 |
> | Microsoft. Media/windowsazure.mediaservices/Streamingendpoint/privateEndpointConnections/delete | 刪除串流端點私人端點連接 |
> | Microsoft. Media/windowsazure.mediaservices/Streamingendpoint/privateLinkResources/read | 讀取 Private Link 資源的任何串流端點 |
> | Microsoft.Media/mediaservices/streamingLocators/read | 讀取任何串流定位器 |
> | Microsoft.Media/mediaservices/streamingLocators/write | 建立或更新任何串流定位器 |
> | Microsoft.Media/mediaservices/streamingLocators/delete | 刪除任何串流定位器 |
> | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | 列出內容金鑰 |
> | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | 列出路徑 |
> | Microsoft.Media/mediaservices/streamingPolicies/read | 讀取任何串流原則 |
> | Microsoft.Media/mediaservices/streamingPolicies/write | 建立或更新任何串流原則 |
> | Microsoft.Media/mediaservices/streamingPolicies/delete | 刪除任何串流原則 |
> | Microsoft. Media/windowsazure.mediaservices/streamingPrivateEndpointConnectionOperations/read | 讀取任何串流私人端點連接作業 |
> | Microsoft. Media/windowsazure.mediaservices/streamingPrivateEndpointConnectionProxyOperations/read | 讀取任何串流私人端點連接 Proxy 作業 |
> | Microsoft.Media/mediaservices/transforms/read | 讀取任何轉換 |
> | Microsoft.Media/mediaservices/transforms/write | 建立或更新任何轉換 |
> | Microsoft.Media/mediaservices/transforms/delete | 刪除任何轉換 |
> | Microsoft.Media/mediaservices/transforms/jobs/read | 讀取任何作業 |
> | Microsoft.Media/mediaservices/transforms/jobs/write | 建立或更新任何作業 |
> | Microsoft.Media/mediaservices/transforms/jobs/delete | 刪除任何作業 |
> | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | 取消作業 |
> | Microsoft.Media/operations/read | 取得可用的作業 |

### <a name="microsoftsearch"></a>Microsoft.Search

Azure 服務： [Azure 搜尋](../search/index.yml)服務

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Search/register/action | 針對搜尋資源提供者註冊訂用帳戶，並讓您能夠建立搜尋服務。 |
> | Microsoft.Search/checkNameAvailability/action | 檢查服務名稱的可用性。 |
> | Microsoft.Search/operations/read | 列出 Microsoft.Search 提供者的所有可用作業。 |
> | Microsoft.Search/searchServices/write | 建立或更新搜尋服務。 |
> | Microsoft.Search/searchServices/read | 讀取搜尋服務。 |
> | Microsoft.Search/searchServices/delete | 刪除搜尋服務。 |
> | Microsoft.Search/searchServices/start/action | 啟動搜尋服務。 |
> | Microsoft.Search/searchServices/stop/action | 停止搜尋服務。 |
> | Microsoft.Search/searchServices/listAdminKeys/action | 讀取系統管理金鑰。 |
> | Microsoft.Search/searchServices/regenerateAdminKey/action | 重新產生系統管理金鑰。 |
> | Microsoft. Search/searchServices/listQueryKeys/action | 傳回指定 Azure 搜尋服務的查詢 API 金鑰清單。 |
> | Microsoft.Search/searchServices/createQueryKey/action | 建立查詢金鑰。 |
> | Microsoft.Search/searchServices/deleteQueryKey/delete | 刪除查詢金鑰。 |
> | Microsoft. Search/searchServices/privateEndpointConnectionProxies/validate/action | 驗證私人端點連接從 NRP 端建立呼叫 |
> | Microsoft. Search/searchServices/privateEndpointConnectionProxies/write | 使用指定的參數建立私人端點連線 proxy，或更新指定私人端點連接 proxy 的屬性或標記 |
> | Microsoft. Search/searchServices/privateEndpointConnectionProxies/read | 傳回私人端點連接 proxy 的清單，或取得指定私人端點連線 proxy 的屬性。 |
> | Microsoft. Search/searchServices/privateEndpointConnectionProxies/delete | 刪除現有的私人端點連線 proxy |
> | Microsoft. Search/searchServices/sharedPrivateLinkResources/write | 使用指定的參數建立新的共用私人連結資源，或更新指定之共用私人連結資源的屬性 |
> | Microsoft. Search/searchServices/sharedPrivateLinkResources/read | 傳回共用的私人連結資源清單，或取得指定之共用 private link 資源的屬性。 |
> | Microsoft. Search/searchServices/sharedPrivateLinkResources/delete | 刪除現有的共用私人連結資源 |
> | Microsoft. Search/searchServices/sharedPrivateLinkResources/operationStatuses/read | 取得長時間執行的共用私用連結資源作業的詳細資料 |

### <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

Azure 服務： [Azure SignalR Service](../azure-signalr/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.SignalRService/register/action | 向訂用帳戶註冊 'Microsoft.SignalRService' 資源提供者 |
> | Microsoft.SignalRService/unregister/action | 向訂用帳戶取消註冊 'Microsoft.SignalRService' 資源提供者 |
> | Microsoft.SignalRService/locations/checknameavailability/action | 檢查名稱是否可用於新的 SignalR 服務 |
> | Microsoft.SignalRService/locations/operationresults/signalr/read | 查詢以位置為基礎的非同步作業結果 |
> | Microsoft.signalrservice/位置/operationStatuses/operationId/read | 查詢以位置為基礎之非同步作業的狀態 |
> | Microsoft.SignalRService/locations/usages/read | 取得 Azure SignalR Service 的配額使用量 |
> | Microsoft.SignalRService/operationresults/read | 查詢提供者層級非同步作業的結果 |
> | Microsoft.signalrservice/operations/read | 列出 Azure SignalR 服務的作業。 |
> | Microsoft.signalrservice/>operationstatus/read | 查詢提供者層級非同步作業的狀態 |
> | Microsoft.SignalRService/SignalR/read | 在管理入口網站中或透過 API 檢視 SignalR 的設定和組態 |
> | Microsoft.SignalRService/SignalR/write | 在管理入口網站中或透過 API 修改 SignalR 的設定和組態 |
> | Microsoft.SignalRService/SignalR/delete | 刪除整個 SignalR Service |
> | Microsoft.SignalRService/SignalR/listkeys/action | 在管理入口網站中或透過 API 檢視 SignalR 存取金鑰 |
> | Microsoft.SignalRService/SignalR/regeneratekey/action | 在管理入口網站中或透過 API 變更 SignalR 存取金鑰 |
> | Microsoft.SignalRService/SignalR/restart/action | 在管理入口網站中或透過 API 重新啟動 Azure SignalR Service。 會有一些停機時間。 |
> | Microsoft.signalrservice/SignalR/eventGridFilters/read | 取得指定之事件方格篩選準則的屬性，或列出指定 SignalR 的所有事件方格篩選。 |
> | Microsoft.signalrservice/SignalR/eventGridFilters/write | 使用指定的參數建立或更新 SignalR 的事件方格篩選。 |
> | Microsoft.signalrservice/SignalR/eventGridFilters/delete | 從 SignalR 中刪除事件方格篩選。 |
> | Microsoft.signalrservice/SignalR/privateEndpointConnectionProxies/validate/action | 驗證私人端點連線 Proxy |
> | Microsoft.signalrservice/SignalR/privateEndpointConnectionProxies/write | 建立私人端點連接 Proxy |
> | Microsoft.signalrservice/SignalR/privateEndpointConnectionProxies/read | 讀取私人端點連接 Proxy |
> | Microsoft.signalrservice/SignalR/privateEndpointConnectionProxies/delete | 刪除私人端點連線 Proxy |
> | Microsoft.signalrservice/SignalR/privateEndpointConnections/write | 核准或拒絕私人端點連線 |
> | Microsoft.signalrservice/SignalR/privateEndpointConnections/read | 讀取私人端點連線 |
> | Microsoft.signalrservice/SignalR/privateLinkResources/read | 列出所有 SignalR Private Link 資源 |
> | **DataAction** | **說明** |
> | Microsoft.signalrservice/SignalR/auth/accessKey/action | 產生暫時 AccessKey 來簽署 ClientTokens。 |
> | Microsoft.signalrservice/SignalR/auth/clientToken/action | 產生用來啟動用戶端連接的 ClientToken。 |
> | Microsoft.signalrservice/SignalR/clientConnection/send/action | 將訊息直接傳送至用戶端連接。 |
> | Microsoft.signalrservice/SignalR/clientConnection/read | 檢查用戶端連接是否存在。 |
> | Microsoft.signalrservice/SignalR/clientConnection/write | 關閉用戶端連接。 |
> | Microsoft.signalrservice/SignalR/group/send/action | 將訊息廣播到群組。 |
> | Microsoft.signalrservice/SignalR/group/read | 檢查群組是否存在或使用者存在於群組中。 |
> | Microsoft.signalrservice/SignalR/group/write | 加入/離開群組。 |
> | Microsoft.signalrservice/SignalR/hub/send/action | 將訊息廣播至中樞內的所有用戶端連接。 |
> | Microsoft.signalrservice/SignalR/serverConnection/write | 啟動伺服器連接。 |
> | Microsoft.signalrservice/SignalR/user/send/action | 傳送訊息給使用者，其中可能包含多個用戶端連接。 |
> | Microsoft.signalrservice/SignalR/user/read | 檢查使用者是否存在。 |
> | Microsoft.signalrservice/SignalR/user/write |  |

### <a name="microsoftweb"></a>microsoft.web

Azure 服務： [App Service](../app-service/index.yml)、 [Azure Functions](../azure-functions/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | microsoft.web/unregister/action | 針對訂用帳戶取消註冊 Microsoft.Web 資源提供者。 |
> | microsoft.web/validate/action | 驗證。 |
> | microsoft.web/register/action | 針對訂用帳戶註冊 Microsoft.Web 資源提供者。 |
> | microsoft.web/verifyhostingenvironmentvnet/action | 確認裝載環境 Vnet。 |
> | microsoft.web/apimanagementaccounts/apiacls/read | 取得 API 管理帳戶 Apiacl。 |
> | microsoft.web/apimanagementaccounts/apis/read | 取得 API 管理帳戶 API。 |
> | microsoft.web/apimanagementaccounts/apis/delete | 刪除 API 管理帳戶 API。 |
> | microsoft.web/apimanagementaccounts/apis/write | 更新 API 管理帳戶 API。 |
> | microsoft.web/apimanagementaccounts/apis/apiacls/delete | 刪除 API 管理帳戶 API Apiacl。 |
> | microsoft.web/apimanagementaccounts/apis/apiacls/read | 讀取 API 管理帳戶 API Apiacl。 |
> | microsoft.web/apimanagementaccounts/apis/apiacls/write | 更新 API 管理帳戶 API Apiacl。 |
> | microsoft.web/apimanagementaccounts/apis/connectionacls/read | 取得 API 管理帳戶 API Connectionacl。 |
> | microsoft.web/apimanagementaccounts/apis/connections/read | 取得 API 管理帳戶 API 連線。 |
> | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | 確認同意碼 API 管理帳戶 API 連線。 |
> | microsoft.web/apimanagementaccounts/apis/connections/delete | 刪除 API 管理帳戶 API 連線。 |
> | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | 取得 API 管理帳戶 API 連線的同意連結。 |
> | microsoft.web/apimanagementaccounts/apis/connections/write | 更新 API 管理帳戶 API 連線。 |
> | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | 列出 API 管理帳戶 API 連線的連線金鑰。 |
> | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | 列出 API 管理帳戶 API 連線的祕密。 |
> | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | 刪除 API 管理帳戶 API 連線 Connectionacl。 |
> | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | 取得 API 管理帳戶 API 連線 Connectionacl。 |
> | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | 更新 API 管理帳戶 API 連線 Connectionacl。 |
> | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | 刪除 API 管理帳戶 API 已當地語系化的定義。 |
> | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | 取得 API 管理帳戶 API 已當地語系化的定義。 |
> | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | 更新 API 管理帳戶 API 已當地語系化的定義。 |
> | microsoft.web/apimanagementaccounts/connectionacls/read | 取得 API 管理帳戶 Connectionacl。 |
> | microsoft.web/availablestacks/read | 取得可用堆疊。 |
> | Microsoft.Web/certificates/Read | 取得憑證清單。 |
> | Microsoft.Web/certificates/Write | 新增憑證或更新現有憑證。 |
> | Microsoft.Web/certificates/Delete | 刪除現有憑證。 |
> | microsoft.web/checknameavailability/read | 檢查資源名稱是否可供使用。 |
> | microsoft.web/classicmobileservices/read | 取得傳統的行動服務。 |
> | Microsoft.Web/connectionGateways/Read | 取得連線閘道器的清單。 |
> | Microsoft.Web/connectionGateways/Write | 建立或更新連線閘道器。 |
> | Microsoft.Web/connectionGateways/Delete | 刪除連接閘道器。 |
> | Microsoft.Web/connectionGateways/Move/Action | 移動連線閘道。 |
> | Microsoft.Web/connectionGateways/Join/Action | 加入連線閘道器。 |
> | Microsoft Web/connectionGateways/關聯/動作 | 與連線閘道建立關聯。 |
> | Microsoft.Web/connectionGateways/ListStatus/Action | 列出連線閘道的狀態。 |
> | Microsoft.Web/connections/Read | 取得連線清單。 |
> | Microsoft.Web/connections/Write | 建立或更新連線。 |
> | Microsoft.Web/connections/Delete | 刪除連線。 |
> | Microsoft.Web/connections/Move/Action | 移動連線。 |
> | Microsoft.Web/connections/Join/Action | 加入連線。 |
> | microsoft.web/connections/confirmconsentcode/action | 確認連線同意程式碼。 |
> | microsoft.web/connections/listconsentlinks/action | 列出連線的同意連結。 |
> | Microsoft.Web/customApis/Read | 取得自訂 API 的清單。 |
> | Microsoft.Web/customApis/Write | 建立或更新自訂 API。 |
> | Microsoft.Web/customApis/Delete | 刪除自訂 API。 |
> | Microsoft.Web/customApis/Move/Action | 移動自訂 API。 |
> | Microsoft.Web/customApis/Join/Action | 加入自訂 API。 |
> | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | 從 WSDL 中擷取 API 定義。 |
> | Microsoft.Web/customApis/listWsdlInterfaces/Action | 列出自訂 API 的 WSDL 介面。 |
> | Microsoft Web/>customhostnames/Read | 取得指派給訂用帳戶的自訂主機名稱。 |
> | Microsoft Web/>customhostnames/sites/Read | 取得指派給自訂主機名稱的網站名稱。 |
> | Microsoft.Web/deletedSites/Read | 取得所刪除 Web 應用程式的屬性 |
> | microsoft.web/deploymentlocations/read | 取得部署位置。 |
> | Microsoft.Web/geoRegions/Read | 取得地理區域清單。 |
> | Microsoft.Web/hostingEnvironments/Read | 取得 App Service 環境的屬性 |
> | Microsoft.Web/hostingEnvironments/Write | 建立新的 App Service 環境，或更新現有的 App Service 環境 |
> | Microsoft.Web/hostingEnvironments/Delete | 刪除 App Service 環境 |
> | Microsoft.Web/hostingEnvironments/Join/Action | 加入 App Service 環境 |
> | Microsoft.Web/hostingEnvironments/reboot/Action | 將 App Service 環境中的所有機器重新開機 |
> | Microsoft Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | 核准私人端點連線 |
> | microsoft.web/hostingenvironments/resume/action | 繼續裝載環境。 |
> | microsoft.web/hostingenvironments/suspend/action | 暫止裝載環境。 |
> | microsoft.web/hostingenvironments/capacities/read | 取得裝載環境容量。 |
> | microsoft.web/hostingenvironments/detectors/read | 取得裝載環境偵測器。 |
> | microsoft.web/hostingenvironments/diagnostics/read | 取得裝載環境診斷。 |
> | Microsoft Web/hostingEnvironments/eventGridFilters/delete | 刪除主機環境上的事件方格篩選。 |
> | Microsoft Web/hostingEnvironments/eventGridFilters/read | 取得裝載環境的事件方格篩選。 |
> | Microsoft Web/hostingEnvironments/eventGridFilters/write | 在主控環境中放置事件方格篩選。 |
> | microsoft web/hostingenvironments/health/read | 取得 App Service 環境的健全狀況詳細資料。 |
> | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | 取得所有輸入相依性的網路端點。 |
> | microsoft.web/hostingenvironments/metricdefinitions/read | 取得裝載環境的計量定義。 |
> | Microsoft.Web/hostingEnvironments/multiRolePools/Read | 取得 App Service 環境中前端集區的屬性 |
> | Microsoft.Web/hostingEnvironments/multiRolePools/Write | 在 App Service 環境中建立新的前端集區，或更新 App Service 環境中現有的前端集區 |
> | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | 取得裝載環境 MultiRole 集區計量定義。 |
> | microsoft.web/hostingenvironments/multirolepools/metrics/read | 取得裝載環境 MultiRole 集區計量。 |
> | microsoft.web/hostingenvironments/multirolepools/skus/read | 取得裝載環境 MultiRole 集區 SKU。 |
> | microsoft.web/hostingenvironments/multirolepools/usages/read | 取得裝載環境 MultiRole 集區使用量。 |
> | microsoft.web/hostingenvironments/operations/read | 取得裝載環境作業。 |
> | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | 取得所有輸出相依性的網路端點。 |
> | microsoft.web/hostingenvironments/serverfarms/read | 取得裝載環境 App Service 方案。 |
> | microsoft.web/hostingenvironments/sites/read | 取得裝載環境 Web Apps。 |
> | microsoft.web/hostingenvironments/usages/read | 取得裝載環境使用量。 |
> | Microsoft.Web/hostingEnvironments/workerPools/Read | 取得 App Service 環境中背景工作集區的屬性 |
> | Microsoft.Web/hostingEnvironments/workerPools/Write | 在 App Service 環境中建立新的背景工作集區，或更新 App Service 環境中現有的背景工作集區 |
> | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | 取得裝載環境 Workerpools 計量定義。 |
> | microsoft.web/hostingenvironments/workerpools/metrics/read | 取得裝載環境 Workerpools 計量。 |
> | microsoft.web/hostingenvironments/workerpools/skus/read | 取得裝載環境 Workerpools SKU。 |
> | microsoft.web/hostingenvironments/workerpools/usages/read | 取得裝載環境 Workerpools 使用量。 |
> | microsoft.web/ishostingenvironmentnameavailable/read | 取得裝載環境名稱是否可供使用。 |
> | microsoft.web/ishostnameavailable/read | 檢查主機名稱是否可供使用。 |
> | microsoft.web/isusernameavailable/read | 檢查使用者名稱是否可供使用。 |
> | Microsoft.Web/listSitesAssignedToHostName/Read | 取得指派給主機名稱之網站的名稱。 |
> | microsoft.web/locations/extractapidefinitionfromwsdl/action | 針對位置從 WSDL 中擷取 API 定義。 |
> | microsoft.web/locations/listwsdlinterfaces/action | 列出適用於位置的 WSDL 介面。 |
> | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | 位置的虛擬網路或子網路刪除通知。 |
> | Microsoft Web/位置/previewstaticsiteworkflowfile/動作 | 預覽靜態網站工作流程檔案 |
> | microsoft.web/locations/apioperations/read | 取得位置 API 作業。 |
> | microsoft.web/locations/connectiongatewayinstallations/read | 取得位置連線閘道器安裝。 |
> | microsoft.web/locations/managedapis/read | 取得位置管理的 API。 |
> | Microsoft.Web/locations/managedapis/Join/Action | 加入受控 API。 |
> | microsoft.web/locations/managedapis/apioperations/read | 取得位置受控 API 作業。 |
> | microsoft web/位置/operationResults/讀取 | 取得作業。 |
> | microsoft web/位置/作業/讀取 | 取得作業。 |
> | microsoft.web/operations/read | 取得作業。 |
> | microsoft.web/publishingusers/read | 取得發佈使用者。 |
> | microsoft.web/publishingusers/write | 更新發佈使用者。 |
> | Microsoft.Web/recommendations/Read | 取得訂用帳戶的建議清單。 |
> | microsoft.web/resourcehealthmetadata/read | 取得資源健康狀態中繼資料。 |
> | Microsoft.Web/serverfarms/Read | 取得 App Service 方案的屬性 |
> | Microsoft.Web/serverfarms/Write | 建立新的 App Service 方案，或更新現有的 App Service 方案 |
> | Microsoft.Web/serverfarms/Delete | 刪除現有的 App Service 方案 |
> | Microsoft Web/serverfarms/Join/Action | 加入 App Service 計畫 |
> | Microsoft.Web/serverfarms/restartSites/Action | 重新啟動 App Service 方案中的所有 Web Apps |
> | microsoft.web/serverfarms/capabilities/read | 取得 App Service 方案的容量。 |
> | Microsoft Web/serverfarms/eventGridFilters/delete | 刪除伺服器陣列上的事件方格篩選。 |
> | Microsoft Web/serverfarms/eventGridFilters/read | 取得伺服器陣列上的事件方格篩選。 |
> | Microsoft Web/serverfarms/eventGridFilters/write | 將事件方格篩選放在伺服器陣列上。 |
> | microsoft.web/serverfarms/firstpartyapps/settings/delete | 刪除 App Service 方案的第一方應用程式設定。 |
> | microsoft.web/serverfarms/firstpartyapps/settings/read | 取得 App Service 方案的第一方應用程式設定。 |
> | microsoft.web/serverfarms/firstpartyapps/settings/write | 更新 App Service 方案的第一方應用程式設定。 |
> | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | 取得 App Service 方案的混合式連線命名空間轉送。 |
> | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | 刪除 App Service 方案的混合式連線命名空間轉送。 |
> | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | 取得 App Service 方案的混合式連線命名空間轉送 Web Apps。 |
> | microsoft.web/serverfarms/hybridconnectionplanlimits/read | 取得 App Service 方案的混合式連線方案限制。 |
> | microsoft.web/serverfarms/hybridconnectionrelays/read | 取得 App Service 方案的混合式連線轉送。 |
> | microsoft.web/serverfarms/metricdefinitions/read | 取得 App Service 方案的計量定義。 |
> | microsoft.web/serverfarms/metrics/read | 取得 App Service 方案的計量。 |
> | microsoft.web/serverfarms/operationresults/read | 取得 App Service 方案的作業結果。 |
> | microsoft.web/serverfarms/sites/read | 取得 App Service 方案的 Web Apps。 |
> | microsoft.web/serverfarms/skus/read | 取得 App Service 方案 SKU。 |
> | microsoft.web/serverfarms/usages/read | 取得 App Service 方案的使用量。 |
> | microsoft.web/serverfarms/virtualnetworkconnections/read | 取得 App Service 方案的虛擬網路連線。 |
> | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | 更新 App Service 方案的虛擬網路連線閘道器。 |
> | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | 刪除 App Service 方案的虛擬網路連線路由。 |
> | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | 取得 App Service 方案的虛擬網路連線路由。 |
> | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | 更新 App Service 方案的虛擬網路連線路由。 |
> | microsoft.web/serverfarms/workers/reboot/action | 重新啟動 App Service 方案的背景工作角色。 |
> | Microsoft.Web/sites/Read | 取得 Web 應用程式的屬性 |
> | Microsoft.Web/sites/Write | 建立新的 Web 應用程式，或更新現有 Web 應用程式 |
> | Microsoft.Web/sites/Delete | 刪除現有的 Web 應用程式 |
> | Microsoft.Web/sites/backup/Action | 建立新的 Web 應用程式備份 |
> | Microsoft.Web/sites/publishxml/Action | 取得 Web 應用程式的發行設定檔 xml |
> | Microsoft.Web/sites/publish/Action | 發佈 Web 應用程式 |
> | Microsoft.Web/sites/restart/Action | 重新啟動 Web 應用程式 |
> | Microsoft.Web/sites/start/Action | 啟動 Web 應用程式 |
> | Microsoft.Web/sites/stop/Action | 停止 Web 應用程式 |
> | Microsoft.Web/sites/slotsswap/Action | 交換 Web 應用程式部署位置 |
> | Microsoft.Web/sites/slotsdiffs/Action | 取得 Web 應用程式和位置之間的組態差異 |
> | Microsoft.Web/sites/applySlotConfig/Action | 將目標位置的 Web 應用程式位置組態套用到目前的 Web 應用程式 |
> | Microsoft.Web/sites/resetSlotConfig/Action | 重設 Web 應用程式組態 |
> | Microsoft Web/sites/PrivateEndpointConnectionsApproval/action | 核准私人端點連線 |
> | microsoft.web/sites/functions/action | 函式 Web Apps。 |
> | microsoft.web/sites/listsyncfunctiontriggerstatus/action | 列出同步處理函數觸發程式狀態。 |
> | microsoft.web/sites/networktrace/action | 網路追蹤 Web Apps。 |
> | microsoft.web/sites/newpassword/action | Newpassword Web Apps。 |
> | microsoft.web/sites/sync/action | 同步處理 Web Apps。 |
> | microsoft.web/sites/migratemysql/action | 移轉 MySql Web Apps。 |
> | microsoft.web/sites/recover/action | 復原 Web Apps。 |
> | microsoft.web/sites/restoresnapshot/action | 還原 Web Apps 快照集。 |
> | microsoft web/sites/restorefromdeletedapp/action | 從已刪除的應用程式還原 Web Apps。 |
> | microsoft.web/sites/syncfunctiontriggers/action | 同步函式觸發程式。 |
> | microsoft.web/sites/backups/action | 探索可從 Azure 儲存體中 Blob 還原的現有應用程式備份。 |
> | microsoft.web/sites/containerlogs/action | 取得 Web 應用程式的壓縮容器記錄。 |
> | microsoft.web/sites/restorefrombackupblob/action | 從備份 Blob 還原 Web 應用程式。 |
> | microsoft.web/sites/analyzecustomhostname/read | 分析自訂主機名稱。 |
> | microsoft.web/sites/backup/read | 取得 Web Apps 備份。 |
> | microsoft.web/sites/backup/write | 更新 Web Apps 備份。 |
> | Microsoft.Web/sites/backups/Read | 取得 Web 應用程式備份的屬性 |
> | microsoft.web/sites/backups/list/action | 列出 Web Apps 備份。 |
> | microsoft.web/sites/backups/restore/action | 還原 Web Apps 備份。 |
> | microsoft.web/sites/backups/delete | 刪除 Web Apps 備份。 |
> | microsoft.web/sites/backups/write | 更新 Web Apps 備份。 |
> | Microsoft Web/sites/basicPublishingCredentialsPolicies/Read | 列出可供 Web 應用程式使用的發佈方法 |
> | Microsoft Web/sites/basicPublishingCredentialsPolicies/ftp/Read | 取得 Web 應用程式是否允許 FTP 發行認證 |
> | Microsoft Web/sites/basicPublishingCredentialsPolicies/ftp/Write | 更新 Web 應用程式是否允許 FTP 發行認證 |
> | Microsoft Web/sites/basicPublishingCredentialsPolicies/scm/Read | 取得 Web 應用程式是否允許 SCM 發佈認證 |
> | Microsoft Web/sites/basicPublishingCredentialsPolicies/scm/Write | 更新 Web 應用程式是否允許 SCM 發佈認證 |
> | Microsoft.Web/sites/config/Read | 取得 Web 應用程式的組態設定 |
> | Microsoft.Web/sites/config/list/Action | 列出 Web 應用程式的安全性機密設定，例如發佈認證、應用程式設定和連接字串 |
> | Microsoft.Web/sites/config/Write | 更新 Web 應用程式的組態設定 |
> | microsoft.web/sites/config/delete | 刪除 Web Apps 設定。 |
> | microsoft.web/sites/config/snapshots/read | 取得 Web Apps 組態快照集。 |
> | microsoft.web/sites/containerlogs/download/action | 下載 Web Apps 容器記錄。 |
> | microsoft.web/sites/continuouswebjobs/delete | 刪除 Web Apps 的連續 Web 作業。 |
> | microsoft.web/sites/continuouswebjobs/read | 取得 Web Apps 的連續 Web 作業。 |
> | microsoft.web/sites/continuouswebjobs/start/action | 啟動 Web Apps 的連續 Web 作業。 |
> | microsoft.web/sites/continuouswebjobs/stop/action | 停止 Web Apps 的連續 Web 作業。 |
> | microsoft.web/sites/deployments/delete | 刪除 Web Apps 部署。 |
> | microsoft.web/sites/deployments/read | 取得 Web Apps 部署。 |
> | microsoft.web/sites/deployments/write | 更新 Web Apps 部署。 |
> | microsoft.web/sites/deployments/log/read | 取得 Web Apps 部署記錄。 |
> | microsoft.web/sites/detectors/read | 取得 Web Apps 偵測器。 |
> | microsoft.web/sites/diagnostics/read | 取得 Web Apps 診斷類別。 |
> | microsoft.web/sites/diagnostics/analyses/read | 取得 Web Apps 診斷分析。 |
> | microsoft.web/sites/diagnostics/analyses/execute/Action | 執行 Web Apps 診斷分析。 |
> | microsoft.web/sites/diagnostics/aspnetcore/read | 取得 ASP.NET Core 應用程式的 Web Apps 診斷。 |
> | microsoft.web/sites/diagnostics/autoheal/read | 取得 Web Apps 診斷 Autoheal。 |
> | microsoft.web/sites/diagnostics/deployment/read | 取得 Web Apps 診斷部署。 |
> | microsoft.web/sites/diagnostics/deployments/read | 取得 Web Apps 診斷部署。 |
> | microsoft.web/sites/diagnostics/detectors/read | 取得 Web Apps 診斷偵測器。 |
> | microsoft.web/sites/diagnostics/detectors/execute/Action | 執行 Web Apps 診斷偵測器。 |
> | microsoft.web/sites/diagnostics/failedrequestsperuri/read | 取得 Web Apps 診斷對於每個 URI 的失敗要求。 |
> | microsoft.web/sites/diagnostics/frebanalysis/read | 取得 Web Apps 診斷的 FREB 分析。 |
> | microsoft.web/sites/diagnostics/loganalyzer/read | 取得 Web Apps 診斷的記錄分析器。 |
> | microsoft.web/sites/diagnostics/runtimeavailability/read | 取得 Web Apps 診斷的執行階段可用性。 |
> | microsoft.web/sites/diagnostics/servicehealth/read | 取得 Web Apps 診斷的服務健康狀態。 |
> | microsoft.web/sites/diagnostics/sitecpuanalysis/read | 取得 Web Apps 診斷的網站 CPU 分析。 |
> | microsoft.web/sites/diagnostics/sitecrashes/read | 取得 Web Apps 診斷的網站損毀。 |
> | microsoft.web/sites/diagnostics/sitelatency/read | 取得 Web Apps 診斷的網站延遲。 |
> | microsoft.web/sites/diagnostics/sitememoryanalysis/read | 取得 Web Apps 診斷的網站記憶體分析。 |
> | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | 取得 Web Apps 診斷的網站重新啟動設定更新。 |
> | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | 取得 Web Apps 診斷中使用者初始的網站重新啟動。 |
> | microsoft.web/sites/diagnostics/siteswap/read | 取得 Web Apps 診斷的網站交換。 |
> | microsoft.web/sites/diagnostics/threadcount/read | 取得 Web Apps 診斷的執行緒計數。 |
> | microsoft.web/sites/diagnostics/workeravailability/read | 取得 Web Apps 診斷的 Workeravailability。 |
> | microsoft.web/sites/diagnostics/workerprocessrecycle/read | 取得 Web Apps 診斷的背景工作處理序回收。 |
> | microsoft.web/sites/domainownershipidentifiers/read | 取得 Web Apps 網域擁有權識別碼。 |
> | microsoft.web/sites/domainownershipidentifiers/write | 更新 Web Apps 網域擁有權識別碼。 |
> | Microsoft Web/sites/eventGridFilters/delete | 刪除 web 應用程式上的事件方格篩選。 |
> | Microsoft Web/sites/eventGridFilters/read | 取得 web 應用程式的事件方格篩選。 |
> | Microsoft Web/sites/eventGridFilters/write | 在 web 應用程式上放置事件方格篩選。 |
> | microsoft web/sites/extensions/delete | 刪除 Web Apps 的網站擴充功能。 |
> | microsoft web/sites/extensions/read | 取得 Web Apps 的網站擴充功能。 |
> | microsoft web/sites/extensions/write | 更新 Web Apps 的網站擴充功能。 |
> | microsoft.web/sites/functions/delete | 刪除 Web Apps 函式。 |
> | microsoft.web/sites/functions/listsecrets/action | 列出函式秘密。 |
> | microsoft web/sites/函數/listkeys/action | 列出功能索引鍵。 |
> | microsoft.web/sites/functions/read | 取得 Web Apps 函式。 |
> | microsoft.web/sites/functions/write | 更新 Web Apps 函式。 |
> | microsoft web/sites/函數/金鑰/寫入 | 更新函式金鑰。 |
> | microsoft web/sites/函數/金鑰/刪除 | 刪除函式金鑰。 |
> | microsoft.web/sites/functions/masterkey/read | 取得 Web Apps 函式的主要金鑰。 |
> | microsoft.web/sites/functions/token/read | 取得 Web Apps 函式的權杖。 |
> | microsoft web/sites/host/listkeys/action | 列出函數主機金鑰。 |
> | microsoft web/sites/主機/同步處理/動作 | 同步函式觸發程式。 |
> | microsoft web/sites/host/listsyncstatus/action | 列出同步處理函式觸發程式狀態。 |
> | microsoft web/sites/host/functionkeys/write | 更新函式主機函數金鑰。 |
> | microsoft web/sites/主機/functionkeys/刪除 | Delete 函式裝載函式金鑰。 |
> | microsoft web/sites/host/systemkeys/write | 更新函數主機系統金鑰。 |
> | microsoft web/sites/主機/systemkeys/刪除 | 刪除函數主機系統金鑰。 |
> | microsoft.web/sites/hostnamebindings/delete | 刪除 Web Apps 的主機名稱繫結。 |
> | microsoft.web/sites/hostnamebindings/read | 取得 Web Apps 的主機名稱繫結。 |
> | microsoft.web/sites/hostnamebindings/write | 更新 Web Apps 的主機名稱繫結。 |
> | Microsoft.Web/sites/hostruntime/host/action | 執行函式應用程式的執行階段動作，例如同步觸發程序、新增函式、叫用函式、刪除函式等動作。 |
> | microsoft.web/sites/hostruntime/functions/keys/read | 取得 Web Apps Hostruntime 函式金鑰。 |
> | microsoft.web/sites/hostruntime/host/read | 取得 Web Apps Hostruntime 主機。 |
> | Microsoft.Web/sites/hostruntime/host/_master/read | 取得系統管理作業的函式應用程式主要金鑰 |
> | microsoft.web/sites/hybridconnection/delete | 刪除 Web Apps 的混合式連線。 |
> | microsoft.web/sites/hybridconnection/read | 取得 Web Apps 的混合式連線。 |
> | microsoft.web/sites/hybridconnection/write | 更新 Web Apps 的混合式連線。 |
> | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | 刪除 Web Apps 的混合式連線命名空間轉送。 |
> | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | 列出 Web Apps 的混合式連線命名空間轉送。 |
> | microsoft.web/sites/hybridconnectionnamespaces/relays/write | 更新 Web Apps 的混合式連線命名空間轉送。 |
> | microsoft.web/sites/hybridconnectionnamespaces/relays/read | 取得 Web Apps 的混合式連線命名空間轉送。 |
> | microsoft.web/sites/hybridconnectionrelays/read | 取得 Web Apps 的混合式連線轉送。 |
> | microsoft.web/sites/instances/read | 取得 Web Apps 執行個體。 |
> | microsoft.web/sites/instances/deployments/read | 取得 Web Apps 的執行個體部署。 |
> | microsoft.web/sites/instances/deployments/delete | 刪除 Web Apps 的執行個體部署。 |
> | microsoft.web/sites/instances/extensions/read | 取得 Web Apps 的執行個體擴充功能。 |
> | microsoft.web/sites/instances/extensions/log/read | 取得 Web Apps 的執行個體擴充功能記錄。 |
> | microsoft.web/sites/instances/extensions/processes/read | 取得 Web Apps 執行個體擴充功能程序。 |
> | microsoft.web/sites/instances/processes/delete | 刪除 Web Apps 的執行個體處理序。 |
> | microsoft.web/sites/instances/processes/read | 取得 Web Apps 的執行個體處理序。 |
> | microsoft.web/sites/instances/processes/modules/read | 取得 Web Apps 執行個體處理程序模組。 |
> | microsoft.web/sites/instances/processes/threads/read | 取得 Web Apps 執行個體處理程序的執行緒。 |
> | microsoft.web/sites/metricdefinitions/read | 取得 Web Apps 計量定義。 |
> | microsoft.web/sites/metrics/read | 取得 Web Apps 計量。 |
> | microsoft.web/sites/metricsdefinitions/read | 取得 Web Apps 計量定義。 |
> | microsoft.web/sites/migratemysql/read | 取得 Web Apps 的移轉 MySql。 |
> | microsoft web/sites/Networkconfig.netcfg/read | 取得 App Service 的網路設定。 |
> | microsoft web/sites/Networkconfig.netcfg/write | 更新 App Service 網路設定。 |
> | microsoft web/sites/Networkconfig.netcfg/delete | 刪除 App Service 的網路設定。 |
> | microsoft web/sites/networktraces/operationresults/read | 取得 Web Apps 的網路追蹤操作結果。 |
> | microsoft.web/sites/operationresults/read | 取得 Web Apps 的作業結果。 |
> | microsoft.web/sites/operations/read | 取得 Web Apps 作業。 |
> | microsoft.web/sites/perfcounters/read | 取得 Web Apps 的效能計數器。 |
> | microsoft.web/sites/premieraddons/delete | 刪除 Web Apps 的頂級附加元件。 |
> | microsoft.web/sites/premieraddons/read | 取得 Web Apps 的頂級附加元件。 |
> | microsoft.web/sites/premieraddons/write | 更新 Web Apps 的頂級附加元件。 |
> | microsoft.web/sites/privateaccess/read | 取得私人網站存取啟用和已授權虛擬網路 (可存取該網站) 的資料。 |
> | Microsoft Web/sites/privateEndpointConnections/Write | 核准或拒絕私人端點連接。 |
> | Microsoft Web/sites/privateEndpointConnections/Read | 取得私人端點連線或私人端點連接清單。 |
> | Microsoft Web/sites/privateEndpointConnections/Delete | 刪除私人端點連接。 |
> | Microsoft Web/sites/privateLinkResources/Read | 取得 Private Link 資源。 |
> | microsoft.web/sites/processes/read | 取得 Web Apps 處理序。 |
> | microsoft web/sites/進程/模組/讀取 | 取得 Web Apps 進程模組。 |
> | microsoft web/sites/進程/執行緒/讀取 | 取得 Web Apps 處理執行緒。 |
> | microsoft.web/sites/publiccertificates/delete | 刪除 Web Apps 的公開憑證。 |
> | microsoft.web/sites/publiccertificates/read | 取得 Web Apps 的公開憑證。 |
> | microsoft.web/sites/publiccertificates/write | 更新 Web Apps 的公開憑證。 |
> | microsoft.web/sites/publishxml/read | 取得 Web Apps 的發佈 XML。 |
> | microsoft.web/sites/recommendationhistory/read | 取得 Web Apps 的建議歷程記錄。 |
> | Microsoft.Web/sites/recommendations/Read | 取得 Web 應用程式的建議清單。 |
> | microsoft.web/sites/recommendations/disable/action | 停用 Web Apps 建議。 |
> | microsoft.web/sites/resourcehealthmetadata/read | 取得 Web Apps 的資源健康狀態中繼資料。 |
> | microsoft.web/sites/restore/read | 取得 Web Apps 還原。 |
> | microsoft.web/sites/restore/write | 還原 Web Apps。 |
> | microsoft.web/sites/siteextensions/delete | 刪除 Web Apps 的網站擴充功能。 |
> | microsoft.web/sites/siteextensions/read | 取得 Web Apps 的網站擴充功能。 |
> | microsoft.web/sites/siteextensions/write | 更新 Web Apps 的網站擴充功能。 |
> | Microsoft.Web/sites/slots/Write | 建立新的 Web 應用程式位置，或更新現有的 Web 應用程式位置 |
> | Microsoft.Web/sites/slots/Delete | 刪除現有的 Web 應用程式位置 |
> | Microsoft.Web/sites/slots/backup/Action | 建立新的 Web 應用程式位置備份。 |
> | Microsoft.Web/sites/slots/publishxml/Action | 取得 Web 應用程式位置的發行設定檔 xml |
> | Microsoft.Web/sites/slots/publish/Action | 發佈 Web 應用程式位置 |
> | Microsoft.Web/sites/slots/restart/Action | 重新啟動 Web 應用程式位置 |
> | Microsoft.Web/sites/slots/start/Action | 啟動 Web 應用程式位置 |
> | Microsoft.Web/sites/slots/stop/Action | 停止 Web 應用程式位置 |
> | Microsoft.Web/sites/slots/slotsswap/Action | 交換 Web 應用程式部署位置 |
> | Microsoft.Web/sites/slots/slotsdiffs/Action | 取得 Web 應用程式和位置之間的組態差異 |
> | Microsoft.Web/sites/slots/applySlotConfig/Action | 將目標位置的 Web 應用程式位置組態套用到目前的位置。 |
> | Microsoft.Web/sites/slots/resetSlotConfig/Action | 重設 Web 應用程式位置組態 |
> | Microsoft.Web/sites/slots/Read | 取得 Web 應用程式部署位置的屬性 |
> | microsoft.web/sites/slots/newpassword/action | Newpassword Web Apps 位置。 |
> | microsoft.web/sites/slots/sync/action | 同步處理 Web Apps 的位置。 |
> | microsoft.web/sites/slots/networktrace/action | 網路追蹤 Web Apps 位置。 |
> | microsoft.web/sites/slots/recover/action | 復原 Web Apps 位置。 |
> | microsoft.web/sites/slots/restoresnapshot/action | 還原 Web Apps 位置快照集。 |
> | microsoft web/sites/插槽/restorefromdeletedapp/action | 從已刪除的應用程式還原 Web 應用程式位置。 |
> | microsoft.web/sites/slots/backups/action | 探索 Web Apps 位置備份。 |
> | microsoft.web/sites/slots/containerlogs/action | 取得 Web 應用程式位置的壓縮容器記錄。 |
> | microsoft.web/sites/slots/restorefrombackupblob/action | 從備份 Blob 還原 Web 應用程式位置。 |
> | microsoft.web/sites/slots/analyzecustomhostname/read | 取得 Web Apps 位置的分析自訂主機名稱。 |
> | microsoft.web/sites/slots/backup/write | 更新 Web Apps 位置的備份。 |
> | microsoft.web/sites/slots/backup/read | 取得 Web Apps 位置備份。 |
> | Microsoft.Web/sites/slots/backups/Read | 取得 Web 應用程式位置備份的屬性 |
> | microsoft.web/sites/slots/backups/list/action | 列出 Web Apps 位置的備份。 |
> | microsoft.web/sites/slots/backups/restore/action | 還原 Web Apps 位置的備份。 |
> | microsoft.web/sites/slots/backups/delete | 刪除 Web Apps 位置備份。 |
> | Microsoft Web/sites/插槽/basicPublishingCredentialsPolicies/Read | 列出可供 Web 應用程式位置使用的發佈認證 |
> | Microsoft Web/sites/插槽/basicPublishingCredentialsPolicies/ftp/Read | 取得 Web 應用程式位置是否允許 FTP 發行認證 |
> | Microsoft Web/sites/插槽/basicPublishingCredentialsPolicies/ftp/Write | 更新 Web 應用程式位置是否允許 FTP 發行認證 |
> | Microsoft Web/sites/插槽/basicPublishingCredentialsPolicies/scm/Read | 取得 Web 應用程式位置是否允許 SCM 發佈認證 |
> | Microsoft Web/sites/插槽/basicPublishingCredentialsPolicies/scm/Write | 更新 Web 應用程式位置是否允許 SCM 發佈認證 |
> | Microsoft.Web/sites/slots/config/Read | 取得 Web 應用程式位置的組態設定 |
> | Microsoft.Web/sites/slots/config/list/Action | 列出 Web 應用程式位置的安全性機密設定，例如發佈認證、應用程式設定和連接字串 |
> | Microsoft.Web/sites/slots/config/Write | 更新 Web 應用程式位置的組態設定 |
> | microsoft.web/sites/slots/config/delete | 刪除 Web Apps 的位置設定。 |
> | microsoft.web/sites/slots/containerlogs/download/action | 下載 Web Apps 位置容器記錄。 |
> | microsoft.web/sites/slots/continuouswebjobs/delete | 刪除 Web Apps 位置的連續 Web 作業。 |
> | microsoft.web/sites/slots/continuouswebjobs/read | 取得 Web Apps 位置的連續 Web 作業。 |
> | microsoft.web/sites/slots/continuouswebjobs/start/action | 啟動 Web Apps 位置的連續 Web 作業。 |
> | microsoft.web/sites/slots/continuouswebjobs/stop/action | 停止 Web Apps 位置的連續 Web 作業。 |
> | microsoft.web/sites/slots/deployments/delete | 刪除 Web Apps 的位置部署。 |
> | microsoft.web/sites/slots/deployments/read | 取得 Web Apps 的位置部署。 |
> | microsoft.web/sites/slots/deployments/write | 更新 Web Apps 的位置部署。 |
> | microsoft.web/sites/slots/deployments/log/read | 取得 Web Apps 的位置部署記錄。 |
> | microsoft.web/sites/slots/detectors/read | 取得 Web Apps 位置偵測器。 |
> | microsoft.web/sites/slots/diagnostics/read | 取得 Web Apps 位置診斷。 |
> | microsoft.web/sites/slots/diagnostics/analyses/read | 取得 Web Apps 位置診斷的分析。 |
> | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | 執行 Web Apps 位置診斷的分析。 |
> | microsoft.web/sites/slots/diagnostics/aspnetcore/read | 取得 ASP.NET Core 應用程式的 Web Apps 位置診斷。 |
> | microsoft.web/sites/slots/diagnostics/autoheal/read | 取得 Web Apps 位置診斷的 Autoheal。 |
> | microsoft.web/sites/slots/diagnostics/deployment/read | 取得 Web Apps 位置診斷的部署。 |
> | microsoft.web/sites/slots/diagnostics/deployments/read | 取得 Web Apps 位置診斷的部署。 |
> | microsoft.web/sites/slots/diagnostics/detectors/read | 取得 Web Apps 位置診斷的偵測器。 |
> | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | 執行 Web Apps 位置診斷的偵測器。 |
> | microsoft.web/sites/slots/diagnostics/frebanalysis/read | 取得 Web Apps 位置診斷的 FREB 分析。 |
> | microsoft.web/sites/slots/diagnostics/loganalyzer/read | 取得 Web Apps 位置診斷的記錄分析器。 |
> | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | 取得 Web Apps 位置診斷的執行階段可用性。 |
> | microsoft.web/sites/slots/diagnostics/servicehealth/read | 取得 Web Apps 位置診斷的服務健康狀態。 |
> | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | 取得 Web Apps 位置診斷的網站 CPU 分析。 |
> | microsoft.web/sites/slots/diagnostics/sitecrashes/read | 取得 Web Apps 位置診斷的網站損毀。 |
> | microsoft.web/sites/slots/diagnostics/sitelatency/read | 取得 Web Apps 位置診斷的網站延遲。 |
> | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | 取得 Web Apps 位置診斷的網站記憶體分析。 |
> | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | 取得 Web Apps 位置診斷的網站重新啟動設定更新。 |
> | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | 取得 Web Apps 位置診斷中使用者初始的網站重新啟動。 |
> | microsoft.web/sites/slots/diagnostics/siteswap/read | 取得 Web Apps 位置診斷的網站交換。 |
> | microsoft.web/sites/slots/diagnostics/threadcount/read | 取得 Web Apps 位置診斷的執行緒計數。 |
> | microsoft.web/sites/slots/diagnostics/workeravailability/read | 取得 Web Apps 位置診斷的 Workeravailability。 |
> | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | 取得 Web Apps 位置診斷的背景工作處理序回收。 |
> | microsoft.web/sites/slots/domainownershipidentifiers/read | 取得 Web Apps 位置的網域擁有權識別碼。 |
> | microsoft web/sites/插槽/擴充功能/讀取 | 取得 Web Apps 插槽延伸模組。 |
> | microsoft web/sites/插槽/擴充/寫入 | 更新 Web Apps 插槽延伸模組。 |
> | microsoft web/sites/插槽/功能/listkeys/action | 列出功能索引鍵。 |
> | microsoft.web/sites/slots/functions/read | 取得 Web Apps 位置函式。 |
> | microsoft.web/sites/slots/functions/listsecrets/action | 列出祕密 Web Apps 位置函式。 |
> | microsoft web/sites/插槽/函式/金鑰/寫入 | 更新函式金鑰。 |
> | microsoft web/sites/插槽/函式/金鑰/刪除 | 刪除函式金鑰。 |
> | microsoft web/sites/插槽/主機/listkeys/動作 | 列出函數主機金鑰。 |
> | microsoft web/sites/插槽/主機/同步/動作 | 同步函式觸發程式。 |
> | microsoft web/sites/插槽/主機/functionkeys/write | 更新函式主機函數金鑰。 |
> | microsoft web/sites/插槽/主機/functionkeys/刪除 | Delete 函式裝載函式金鑰。 |
> | microsoft web/sites/插槽/主機/systemkeys/write | 更新函數主機系統金鑰。 |
> | microsoft web/sites/插槽/主機/systemkeys/刪除 | 刪除函數主機系統金鑰。 |
> | microsoft.web/sites/slots/hostnamebindings/delete | 刪除 Web Apps 位置的主機名稱繫結。 |
> | microsoft.web/sites/slots/hostnamebindings/read | 取得 Web Apps 位置的主機名稱繫結。 |
> | microsoft.web/sites/slots/hostnamebindings/write | 更新 Web Apps 位置的主機名稱繫結。 |
> | microsoft.web/sites/slots/hybridconnection/delete | 刪除 Web Apps 位置的混合式連線。 |
> | microsoft.web/sites/slots/hybridconnection/read | 取得 Web Apps 位置的混合式連線。 |
> | microsoft.web/sites/slots/hybridconnection/write | 更新 Web Apps 位置的混合式連線。 |
> | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | 刪除 Web Apps 位置的混合式連線命名空間轉送。 |
> | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | 更新 Web Apps 位置的混合式連線命名空間轉送。 |
> | microsoft.web/sites/slots/hybridconnectionrelays/read | 取得 Web Apps 位置的混合式連線轉送。 |
> | microsoft.web/sites/slots/instances/read | 取得 Web Apps 位置的執行個體。 |
> | microsoft.web/sites/slots/instances/deployments/read | 取得 Web Apps 位置的執行個體部署。 |
> | microsoft.web/sites/slots/instances/processes/read | 取得 Web Apps 位置的執行個體處理序。 |
> | microsoft.web/sites/slots/instances/processes/delete | 刪除 Web Apps 位置的執行個體處理序。 |
> | microsoft.web/sites/slots/metricdefinitions/read | 取得 Web Apps 位置的計量定義。 |
> | microsoft.web/sites/slots/metrics/read | 取得 Web Apps 位置的計量。 |
> | microsoft.web/sites/slots/migratemysql/read | 取得 Web Apps 位置的移轉 MySql。 |
> | microsoft web/sites/插槽/networktraces/operationresults/read | 取得 Web Apps 位置的網路追蹤操作結果。 |
> | microsoft.web/sites/slots/operationresults/read | 取得 Web Apps 位置的作業結果。 |
> | microsoft.web/sites/slots/operations/read | 取得 Web Apps 位置的作業。 |
> | microsoft.web/sites/slots/perfcounters/read | 取得 Web Apps 位置的效能計數器。 |
> | microsoft.web/sites/slots/phplogging/read | 取得 Web Apps 位置的 Phplogging。 |
> | microsoft.web/sites/slots/premieraddons/delete | 刪除 Web Apps 位置的頂級附加元件。 |
> | microsoft.web/sites/slots/premieraddons/read | 取得 Web Apps 位置的頂級附加元件。 |
> | microsoft.web/sites/slots/premieraddons/write | 更新 Web Apps 位置的頂級附加元件。 |
> | microsoft.web/sites/slots/processes/read | 取得 Web Apps 位置的程序。 |
> | microsoft.web/sites/slots/publiccertificates/read | 取得 Web Apps 位置的公開憑證。 |
> | microsoft.web/sites/slots/publiccertificates/write | 建立或更新 Web Apps 位置的公開憑證。 |
> | microsoft.web/sites/slots/publiccertificates/delete | 刪除 Web Apps 位置的公開憑證。 |
> | microsoft.web/sites/slots/resourcehealthmetadata/read | 取得 Web Apps 位置的資源健康狀態中繼資料。 |
> | microsoft.web/sites/slots/restore/read | 取得 Web Apps 位置的還原。 |
> | microsoft.web/sites/slots/restore/write | 還原 Web Apps 位置。 |
> | microsoft.web/sites/slots/siteextensions/delete | 刪除 Web Apps 位置的網站擴充功能。 |
> | microsoft.web/sites/slots/siteextensions/read | 取得 Web Apps 位置的網站擴充功能。 |
> | microsoft.web/sites/slots/siteextensions/write | 更新 Web Apps 位置的網站擴充功能。 |
> | microsoft.web/sites/slots/snapshots/read | 取得 Web Apps 位置的快照集。 |
> | Microsoft.Web/sites/slots/sourcecontrols/Read | 取得 Web 應用程式位置的原始檔控制組態設定 |
> | Microsoft.Web/sites/slots/sourcecontrols/Write | 更新 Web 應用程式位置的原始檔控制組態設定 |
> | Microsoft.Web/sites/slots/sourcecontrols/Delete | 刪除 Web 應用程式位置的原始檔控制組態設定 |
> | microsoft.web/sites/slots/triggeredwebjobs/delete | 刪除 Web Apps 位置所觸發的 WebJobs。 |
> | microsoft.web/sites/slots/triggeredwebjobs/read | 取得 Web Apps 位置所觸發的 WebJobs。 |
> | microsoft.web/sites/slots/triggeredwebjobs/run/action | 執行 Web Apps 位置所觸發的 WebJobs。 |
> | microsoft.web/sites/slots/usages/read | 取得 Web Apps 位置的使用量。 |
> | microsoft.web/sites/slots/virtualnetworkconnections/delete | 刪除 Web Apps 位置的虛擬網路連線。 |
> | microsoft.web/sites/slots/virtualnetworkconnections/read | 取得 Web Apps 位置的虛擬網路連線。 |
> | microsoft.web/sites/slots/virtualnetworkconnections/write | 更新 Web Apps 位置的虛擬網路連線。 |
> | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | 更新 Web Apps 位置的虛擬網路連線閘道器。 |
> | microsoft.web/sites/slots/webjobs/read | 取得 Web Apps 位置的 WebJobs。 |
> | microsoft.web/sites/snapshots/read | 取得 Web Apps 快照集。 |
> | Microsoft.Web/sites/sourcecontrols/Read | 取得 Web 應用程式的原始檔控制組態設定 |
> | Microsoft.Web/sites/sourcecontrols/Write | 更新 Web 應用程式的原始檔控制組態設定 |
> | Microsoft.Web/sites/sourcecontrols/Delete | 刪除 Web 應用程式的原始檔控制組態設定 |
> | microsoft.web/sites/triggeredwebjobs/delete | 刪除 Web Apps 所觸發的 WebJobs。 |
> | microsoft.web/sites/triggeredwebjobs/read | 取得 Web Apps 所觸發的 WebJobs。 |
> | microsoft.web/sites/triggeredwebjobs/run/action | 執行 Web Apps 所觸發的 WebJobs。 |
> | microsoft.web/sites/triggeredwebjobs/history/read | 取得 Web Apps 所觸發的 WebJobs 歷程記錄。 |
> | microsoft.web/sites/usages/read | 取得 Web Apps 使用量。 |
> | microsoft.web/sites/virtualnetworkconnections/delete | 刪除 Web Apps 的虛擬網路連線。 |
> | microsoft.web/sites/virtualnetworkconnections/read | 取得 Web Apps 的虛擬網路連線。 |
> | microsoft.web/sites/virtualnetworkconnections/write | 更新 Web Apps 的虛擬網路連線。 |
> | microsoft.web/sites/virtualnetworkconnections/gateways/read | 取得 Web Apps 的虛擬網路連線閘道器。 |
> | microsoft.web/sites/virtualnetworkconnections/gateways/write | 更新 Web Apps 的虛擬網路連線閘道器。 |
> | microsoft.web/sites/webjobs/read | 取得 Web Apps WebJobs。 |
> | microsoft.web/skus/read | 取得 SKU。 |
> | microsoft.web/sourcecontrols/read | 取得原始檔控制。 |
> | microsoft.web/sourcecontrols/write | 更新原始檔控制。 |
> | Microsoft Web/staticSites/Read | 取得靜態網站的屬性 |
> | Microsoft Web/staticSites/Write | 建立新的靜態網站或更新現有的網站 |
> | Microsoft. Web/staticSites/Delete | 刪除現有的靜態網站 |
> | Microsoft Web/staticSites/createinvitation/action | 為靜態網站使用者建立一組角色的邀請連結 |
> | Microsoft Web/staticSites/listConfiguredRoles/action | 列出針對靜態網站設定的角色。 |
> | Microsoft Web/staticSites/listfunctionappsettings/Action | 列出靜態網站的函式應用程式設定 |
> | Microsoft Web/staticSites/listappsettings/Action | 列出靜態網站的應用程式設定 |
> | Microsoft Web/staticSites/卸離/動作 | 從目前連結的儲存機制卸離靜態網站 |
> | Microsoft Web/staticSites/getuser/Action | 取得使用者的靜態網站資訊 |
> | Microsoft Web/staticSites/listsecrets/action | 列出靜態網站的秘密 |
> | Microsoft Web/staticSites/resetapikey/Action | 重設靜態網站的 api 金鑰 |
> | Microsoft Web/staticSites/authproviders/listusers/Action | 列出靜態網站的使用者 |
> | Microsoft Web/staticSites/authproviders/users/Delete | 刪除靜態網站的使用者 |
> | Microsoft Web/staticSites/authproviders/users/Write | 更新靜態網站的使用者 |
> | Microsoft Web/staticSites/build/Read | 取得靜態網站的組建 |
> | Microsoft Web/staticSites/build/Delete | 刪除靜態網站的組建 |
> | Microsoft Web/staticSites/build/listfunctionappsettings/Action | 列出靜態網站組建的函式應用程式設定 |
> | Microsoft Web/staticSites/build/listappsettings/Action | 列出靜態網站組建的應用程式設定 |
> | Microsoft Web/staticSites/build/config/Write | 建立或更新靜態網站組建的應用程式設定 |
> | Microsoft Web/staticSites/config/Write | 建立或更新靜態網站的應用程式設定 |
> | Microsoft Web/staticSites/customdomains/Write | 建立靜態網站的自訂網域 |
> | Microsoft Web/staticSites/customdomains/Delete | 刪除靜態網站的自訂網域 |
> | Microsoft Web/staticSites/customdomains/Read | 列出靜態網站的自訂網域 |
> | Microsoft. Web/staticSites/customdomains/validate/Action | 驗證自訂網域可以新增至靜態網站 |
> | Microsoft Web/staticSites/函數/Read | 列出靜態網站的功能 |

## <a name="containers"></a>容器

### <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

Azure 服務： [容器實例](../container-instances/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.ContainerInstance/register/action | 為容器執行個體資源提供者註冊訂用帳戶，並允許建立容器群組。 |
> | Microsoft.ContainerInstance/containerGroups/read | 取得所有容器群組。 |
> | Microsoft.ContainerInstance/containerGroups/write | 建立或更新特定容器群組。 |
> | Microsoft.ContainerInstance/containerGroups/delete | 刪除特定容器群組。 |
> | Microsoft.ContainerInstance/containerGroups/restart/action | 重新啟動特定容器群組。 |
> | Microsoft.ContainerInstance/containerGroups/stop/action | 停止特定容器群組。 計算資源將會取消配置，計費也會停止。 |
> | Microsoft.ContainerInstance/containerGroups/start/action | 啟動特定容器群組。 |
> | Microsoft.ContainerInstance/containerGroups/containers/exec/action | 執行至特定容器內。 |
> | Microsoft.containerinstance/containerGroups/container/attach/action | 附加至容器的輸出資料流程。 |
> | Microsoft.containerinstance/containerGroups/container/buildlogs/read | 取得特定容器的組建記錄檔。 |
> | Microsoft.ContainerInstance/containerGroups/containers/logs/read | 取得特定容器的記錄。 |
> | Microsoft.containerinstance/containerGroups/偵測器/read | 列出容器群組偵測器 |
> | Microsoft.containerinstance/containerGroups/operationResults/read | 取得非同步作業結果 |
> | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | 取得容器群組的診斷設定。 |
> | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新容器群組的診斷設定。 |
> | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | 取得容器群組的可用計量。 |
> | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | 通知 Microsoft.ContainerInstance 正在刪除虛擬網路或子網路。 |
> | Microsoft.ContainerInstance/locations/cachedImages/read | 取得區域中訂用帳戶的快取映像。 |
> | Microsoft.ContainerInstance/locations/capabilities/read | 取得區域的功能。 |
> | Microsoft.containerinstance/位置/operationResults/讀取 | 取得非同步作業結果 |
> | Microsoft.containerinstance/位置/作業/讀取 | 列出 Azure 容器實例服務的作業。 |
> | Microsoft.ContainerInstance/locations/usages/read | 取得特定區域的使用量。 |
> | Microsoft.containerinstance/operations/read | 列出 Azure 容器實例服務的作業。 |
> | Microsoft.containerinstance/serviceassociationlinks/delete | 刪除子網上的 azure 容器實例資源提供者所建立的服務關聯連結。 |

### <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

Azure 服務： [Container Registry](../container-registry/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.ContainerRegistry/register/action | 針對容器登錄資源提供者註冊訂用帳戶，並讓您能夠建立容器登錄。 |
> | Microsoft.ContainerRegistry/checkNameAvailability/read | 檢查容器登錄名稱是否可供使用。 |
> | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | 讓 Microsoft.ContainerRegistry 知道虛擬網路或子網路即將刪除 |
> | Microsoft.ContainerRegistry/locations/operationResults/read | 取得非同步作業結果 |
> | Microsoft.ContainerRegistry/operations/read | 列出所有可用的 Azure Container Registry REST API 作業 |
> | Microsoft.ContainerRegistry/registries/read | 取得指定容器登錄的屬性，或列出指定資源群組或訂用帳戶下的所有容器登錄。 |
> | Microsoft.ContainerRegistry/registries/write | 使用指定參數，建立或更新容器登錄。 |
> | Microsoft.ContainerRegistry/registries/delete | 刪除容器登錄。 |
> | Microsoft.ContainerRegistry/registries/listCredentials/action | 列出指定容器登錄的登入認證。 |
> | Microsoft.ContainerRegistry/registries/regenerateCredential/action | 針對指定容器登錄重新產生一個登入認證。 |
> | >microsoft.containerregistry/registry/generateCredentials/action | 針對指定的容器登錄產生權杖的金鑰。 |
> | Microsoft.ContainerRegistry/registries/importImage/action | 使用指定參數將映像匯入到容器登錄。 |
> | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | 取得上傳位置，讓使用者可上傳來源。 |
> | Microsoft.ContainerRegistry/registries/queueBuild/action | 根據要求參數建立新的組建，並將它新增至組建佇列。 |
> | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | 取得容器登錄的來源上傳 URL 位置。 |
> | Microsoft.ContainerRegistry/registries/scheduleRun/action | 排程對容器登錄的流程執行。 |
> | >microsoft.containerregistry/registry/agentpools/listQueueStatus/action | 列出容器登錄的所有佇列狀態（agentpool）。 |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | 刪除容器登錄中的成品。 |
> | Microsoft.ContainerRegistry/registries/builds/read | 取得指定組建的屬性，或列出指定容器登錄的所有組建。 |
> | Microsoft.ContainerRegistry/registries/builds/write | 使用指定參數更新容器登錄的組建。 |
> | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | 取得下載組建記錄的連結。 |
> | Microsoft.ContainerRegistry/registries/builds/cancel/action | 取消現有組建。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/read | 取得指定組建工作的屬性，或列出指定容器登錄的所有組建工作。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/write | 使用指定參數建立或更新容器登錄的組建工作。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/delete | 刪除容器登錄中的組建工作。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | 列出組建工作的原始檔控制屬性。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | 取得指定組建步驟的屬性，或列出指定組建工作的所有組建步驟。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | 使用指定參數建立或更新組建工作的組建步驟。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | 從組建工作中刪除組建步驟。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | 列出組建步驟的組建引數，包括祕密引數。 |
> | Microsoft.ContainerRegistry/registries/eventGridFilters/read | 取得指定事件格線篩選的屬性，或列出指定容器登錄的所有事件格線篩選。 |
> | Microsoft.ContainerRegistry/registries/eventGridFilters/write | 使用指定參數，建立或更新容器登錄的事件格線篩選。 |
> | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | 從容器登錄中刪除事件格線篩選。 |
> | Microsoft.ContainerRegistry/registries/listPolicies/read | 列出指定容器登錄的原則 |
> | Microsoft.ContainerRegistry/registries/listUsages/read | 列出指定容器登錄的配額使用量。 |
> | >microsoft.containerregistry/登錄/中繼資料/讀取 | 取得容器登錄之特定存放庫的中繼資料 |
> | >microsoft.containerregistry/登錄/中繼資料/寫入 | 更新容器登錄庫的中繼資料 |
> | Microsoft.ContainerRegistry/registries/operationStatuses/read | 取得登錄非同步作業狀態 |
> | >microsoft.containerregistry/registry/privateEndpointConnectionProxies/validate/action | 僅驗證私人端點連線 Proxy (NRP)  |
> | >microsoft.containerregistry/registry/privateEndpointConnectionProxies/read | 僅 (NRP 取得私人端點連線 Proxy)  |
> | >microsoft.containerregistry/registry/privateEndpointConnectionProxies/write | 建立私人端點連線 Proxy (僅限 NRP)  |
> | >microsoft.containerregistry/registry/privateEndpointConnectionProxies/delete | 只刪除私人端點連線 Proxy (NRP)  |
> | >microsoft.containerregistry/registry/privateEndpointConnectionProxies/operationStatuses/read | 取得私人端點連線 Proxy 的非同步作業狀態 |
> | Microsoft.ContainerRegistry/registries/pull/read | 從容器登錄中提取或取得映像。 |
> | Microsoft.ContainerRegistry/registries/push/write | 將映像推送或寫入至容器登錄。 |
> | Microsoft.ContainerRegistry/registries/quarantine/read | 從容器登錄中提取或取得隔離的映像 |
> | Microsoft.ContainerRegistry/registries/quarantine/write | 寫入/修改已隔離映像的隔離狀態 |
> | Microsoft.ContainerRegistry/registries/replications/read | 取得指定複寫的屬性，或列出指定容器登錄的所有複寫。 |
> | Microsoft.ContainerRegistry/registries/replications/write | 使用指定參數，建立或更新容器登錄的複寫。 |
> | Microsoft.ContainerRegistry/registries/replications/delete | 刪除容器登錄中的複寫。 |
> | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | 取得複寫非同步作業狀態 |
> | Microsoft.ContainerRegistry/registries/runs/read | 對容器登錄或清單流程執行取得流程執行的屬性。 |
> | Microsoft.ContainerRegistry/registries/runs/write | 更新流程執行。 |
> | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | 取得流程執行的記錄檔 SAS URL。 |
> | Microsoft.ContainerRegistry/registries/runs/cancel/action | 取消現有的流程執行。 |
> | >microsoft.containerregistry/registry/scopeMaps/read | 取得指定之範圍對應的屬性，或列出指定容器登錄的所有範圍對應。 |
> | >microsoft.containerregistry/registry/scopeMaps/write | 使用指定的參數，建立或更新容器登錄的範圍對應。 |
> | >microsoft.containerregistry/registry/scopeMaps/delete | 從 container registry 刪除範圍對應。 |
> | >microsoft.containerregistry/registry/scopeMaps/operationStatuses/read | 取得範圍對應的非同步作業狀態。 |
> | Microsoft.ContainerRegistry/registries/sign/write | 推送/提取容器登錄的內容信任中繼資料。 |
> | >microsoft.containerregistry/registry/taskruns/listDetails/action | 列出容器登錄/tr 的所有詳細資料。 |
> | Microsoft.ContainerRegistry/registries/tasks/read | 取得容器登錄的工作或列出所有工作。 |
> | Microsoft.ContainerRegistry/registries/tasks/write | 建立或更新容器登錄的工作。 |
> | Microsoft.ContainerRegistry/registries/tasks/delete | 刪除容器登錄的工作。 |
> | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | 列出容器登錄工作的所有詳細資料。 |
> | >microsoft.containerregistry/登錄/權杖/讀取 | 取得指定之權杖的屬性，或列出指定容器登錄的所有權杖。 |
> | >microsoft.containerregistry/登錄/權杖/寫入 | 使用指定的參數，建立或更新容器登錄的權杖。 |
> | >microsoft.containerregistry/登錄/權杖/刪除 | 從容器登錄中刪除權杖。 |
> | >microsoft.containerregistry/registry/權杖/operationStatuses/read | 取得權杖非同步作業狀態。 |
> | Microsoft.ContainerRegistry/registries/updatePolicies/write | 更新指定容器登錄的原則 |
> | Microsoft.ContainerRegistry/registries/webhooks/read | 取得指定 Webhook 的屬性，或列出指定容器登錄的所有 Webhook。 |
> | Microsoft.ContainerRegistry/registries/webhooks/write | 使用指定參數，建立或更新容器登錄的 Webhook。 |
> | Microsoft.ContainerRegistry/registries/webhooks/delete | 刪除容器登錄中的 Webhook。 |
> | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | 針對 Webhook 取得服務 URI 的設定和自訂標頭。 |
> | Microsoft.ContainerRegistry/registries/webhooks/ping/action | 觸發要傳送到 Webhook 的 Ping 事件。 |
> | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | 針對指定的 Webhook 列出最近的事件。 |
> | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | 取得 Webhook 非同步作業狀態 |

### <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

Azure 服務： [Azure Kubernetes Service (AKS) ](../aks/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.ContainerService/register/action | 向 Microsoft.ContainerService 資源提供者註冊訂用帳戶 |
> | Microsoft.ContainerService/unregister/action | 向 Microsoft.ContainerService 資源提供者取消註冊訂用帳戶 |
> | Microsoft.ContainerService/containerServices/read | 取得容器服務 |
> | Microsoft.ContainerService/containerServices/write | 建立新的容器服務，或更新現有的容器服務 |
> | Microsoft.ContainerService/containerServices/delete | 刪除容器服務 |
> | Microsoft.ContainerService/locations/operationresults/read | 取得非同步作業結果的狀態 |
> | Microsoft.ContainerService/locations/operations/read | 取得非同步作業的狀態 |
> | Microsoft.ContainerService/locations/orchestrators/read | 列出支援的協調器 |
> | Microsoft.ContainerService/managedClusters/read | 取得受控叢集 |
> | Microsoft.ContainerService/managedClusters/write | 建立新的受控叢集，或更新現有的受控叢集 |
> | Microsoft.ContainerService/managedClusters/delete | 刪除受控叢集 |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | 列出受控叢集的 clusterAdmin 認證 |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | 列出受控叢集的 clusterUser 認證 |
> | >microsoft.containerservice/managedClusters/listClusterMonitoringUserCredential/action | 列出受控叢集的 clusterMonitoringUser 認證 |
> | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | 重設受控叢集的服務主體 |
> | Microsoft.ContainerService/managedClusters/resetAADProfile/action | 重設受控叢集的 AAD 設定檔 |
> | >microsoft.containerservice/managedClusters/rotateClusterCertificates/action | 輪替受控叢集的憑證 |
> | >microsoft.containerservice/managedClusters/privateEndpointConnectionsApproval/action | 決定是否允許使用者核准私人端點連接 |
> | Microsoft.ContainerService/managedClusters/accessProfiles/read | 依角色名稱取得受控叢集存取設定檔 |
> | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | 使用清單認證依角色名稱取得受控叢集存取設定檔 |
> | >microsoft.containerservice/managedClusters/agentPools/read | 取得代理程式組件區 |
> | >microsoft.containerservice/managedClusters/agentPools/write | 建立新的代理程式組件區或更新現有的代理程式組件區 |
> | >microsoft.containerservice/managedClusters/agentPools/delete | 刪除代理程式組件區 |
> | >microsoft.containerservice/managedClusters/agentPools/upgradeNodeImageVersion/write | 升級代理程式組件區的節點映射版本 |
> | >microsoft.containerservice/managedClusters/agentPools/upgradeProfiles/read | 取得代理程式組件區的升級設定檔 |
> | >microsoft.containerservice/managedClusters/availableAgentPoolVersions/read | 取得叢集中可用的代理程式組件區版本 |
> | >microsoft.containerservice/managedClusters/偵測器/read | 取得受控叢集偵測器 |
> | >microsoft.containerservice/managedClusters/diagnosticsState/read | 取得叢集的診斷狀態 |
> | >microsoft.containerservice/managedClusters/privateEndpointConnections/read | 取得私人端點連線 |
> | >microsoft.containerservice/managedClusters/privateEndpointConnections/write | 核准或拒絕私人端點連線 |
> | >microsoft.containerservice/managedClusters/privateEndpointConnections/delete | 刪除私人端點連線 |
> | >microsoft.containerservice/managedClusters/upgradeProfiles/read | 取得叢集的升級設定檔 |
> | Microsoft.ContainerService/openShiftClusters/read | 取得開啟的 Shift 叢集 |
> | Microsoft.ContainerService/openShiftClusters/write | 建立新的或更新現有的 Open Shift 叢集 |
> | Microsoft.ContainerService/openShiftClusters/delete | 刪除開啟的 Shift 叢集 |
> | Microsoft.ContainerService/openShiftManagedClusters/read | 取得開放式 Shift 受控叢集 |
> | Microsoft.ContainerService/openShiftManagedClusters/write | 建立新的或更新現有的 Open Shift 受控叢集 |
> | Microsoft.ContainerService/openShiftManagedClusters/delete | 刪除 Open Shift 受控叢集 |
> | Microsoft.ContainerService/operations/read | 列出 Microsoft.ContainerService 資源提供者的可用作業 |
> | **DataAction** | **說明** |
> | >microsoft.containerservice/managedClusters/admissionregistration. k8s. io/initializerconfigurations/read | 讀取 initializerconfigurations |
> | >microsoft.containerservice/managedClusters/admissionregistration. k8s. io/initializerconfigurations/write | 寫入 initializerconfigurations |
> | >microsoft.containerservice/managedClusters/admissionregistration. k8s. io/initializerconfigurations/delete | 刪除/DeletesCollection initializerconfigurations 資源 |
> | >microsoft.containerservice/managedClusters/admissionregistration. k8s. io/mutatingwebhookconfigurations/read | 讀取 mutatingwebhookconfigurations |
> | >microsoft.containerservice/managedClusters/admissionregistration. k8s. io/mutatingwebhookconfigurations/write | 寫入 mutatingwebhookconfigurations |
> | >microsoft.containerservice/managedClusters/admissionregistration. k8s. io/mutatingwebhookconfigurations/delete | 刪除 mutatingwebhookconfigurations |
> | >microsoft.containerservice/managedClusters/admissionregistration. k8s. io/validatingwebhookconfigurations/read | 讀取 validatingwebhookconfigurations |
> | >microsoft.containerservice/managedClusters/admissionregistration. k8s. io/validatingwebhookconfigurations/write | 寫入 validatingwebhookconfigurations |
> | >microsoft.containerservice/managedClusters/admissionregistration. k8s. io/validatingwebhookconfigurations/delete | 刪除 validatingwebhookconfigurations |
> | >microsoft.containerservice/managedClusters/api/read | 讀取 api |
> | >microsoft.containerservice/managedClusters/api/v1/read | 讀取 api/v1 |
> | >microsoft.containerservice/managedClusters/apiextensions. k8s. io/customresourcedefinitions/read | 讀取 customresourcedefinitions |
> | >microsoft.containerservice/managedClusters/apiextensions. k8s. io/customresourcedefinitions/write | 寫入 customresourcedefinitions |
> | >microsoft.containerservice/managedClusters/apiextensions. k8s. io/customresourcedefinitions/delete | 刪除 customresourcedefinitions |
> | >microsoft.containerservice/managedClusters/apiregistration. k8s. io/apiservices/read | 讀取 apiservices |
> | >microsoft.containerservice/managedClusters/apiregistration. k8s. io/apiservices/write | 寫入 apiservices |
> | >microsoft.containerservice/managedClusters/apiregistration. k8s. io/apiservices/delete | 刪除 apiservices |
> | >microsoft.containerservice/managedClusters/api/read | 讀取 api |
> | >microsoft.containerservice/managedClusters/api/admissionregistration. k8s. io/read | 讀取 admissionregistration.k8s.io |
> | >microsoft.containerservice/managedClusters/api/admissionregistration. k8s. io/v1/read | 讀取 admissionregistration.k8s.io/v1 |
> | >microsoft.containerservice/managedClusters/api/admissionregistration. k8s. io/v1Beta1/read | 讀取 admissionregistration.k8s.io/v1Beta1 |
> | >microsoft.containerservice/managedClusters/api/apiextensions. k8s. io/read | 讀取 apiextensions.k8s.io |
> | >microsoft.containerservice/managedClusters/api/apiextensions. k8s. io/v1/read | 讀取 apiextensions.k8s.io/v1 |
> | >microsoft.containerservice/managedClusters/api/apiextensions. k8s. io/v1Beta1/read | 讀取 apiextensions.k8s.io/v1Beta1 |
> | >microsoft.containerservice/managedClusters/api/apiregistration. k8s. io/read | 讀取 apiregistration.k8s.io |
> | >microsoft.containerservice/managedClusters/api/apiregistration. k8s. io/v1/read | 讀取 apiregistration.k8s.io/v1 |
> | >microsoft.containerservice/managedClusters/api/apiregistration. k8s. io/v1Beta1/read | 讀取 apiregistration.k8s.io/v1Beta1 |
> | >microsoft.containerservice/managedClusters/api/apps/read | 讀取應用程式 |
> | >microsoft.containerservice/managedClusters/api/apps/v1/read | 讀取應用程式/v1 |
> | >microsoft.containerservice/managedClusters/api/apps/v1Beta1/read | 讀取應用程式/v1Beta1 |
> | >microsoft.containerservice/managedClusters/api/apps/v1Beta2/read | 讀取應用程式/v1Beta2 |
> | >microsoft.containerservice/managedClusters/api/authentication. k8s. io/read | 讀取 authentication.k8s.io |
> | >microsoft.containerservice/managedClusters/api/authentication. k8s. io/v1/read | 讀取 authentication.k8s.io/v1 |
> | >microsoft.containerservice/managedClusters/api/authentication. k8s. io/v1Beta1/read | 讀取 authentication.k8s.io/v1Beta1 |
> | >microsoft.containerservice/managedClusters/api/authorization. k8s. io/read | 讀取 authorization.k8s.io |
> | >microsoft.containerservice/managedClusters/api/authorization. k8s. io/v1/read | 讀取 authorization.k8s.io/v1 |
> | >microsoft.containerservice/managedClusters/api/authorization. k8s. io/v1Beta1/read | 讀取 authorization.k8s.io/v1Beta1 |
> | >microsoft.containerservice/managedClusters/api/自動調整/讀取 | 讀取自動調整 |
> | >microsoft.containerservice/managedClusters/api/自動調整/v1/read | 讀取自動調整/v1 |
> | >microsoft.containerservice/managedClusters/api/自動調整/v2Beta1/read | 讀取自動調整/v2Beta1 |
> | >microsoft.containerservice/managedClusters/api/自動調整/v2Beta2/read | 讀取自動調整/v2Beta2 |
> | >microsoft.containerservice/managedClusters/api/batch/read | 讀取批次 |
> | >microsoft.containerservice/managedClusters/api/batch/v1/read | 讀取批次/v1 |
> | >microsoft.containerservice/managedClusters/api/batch/v1Beta1/read | 讀取批次/v1Beta1 |
> | >microsoft.containerservice/managedClusters/api/certificate. k8s. io/read | 讀取 certificates.k8s.io |
> | >microsoft.containerservice/managedClusters/api/certificate. k8s. io/v1Beta1/read | 讀取 certificates.k8s.io/v1Beta1 |
> | >microsoft.containerservice/managedClusters/api/協調. k8s. io/read | 讀取 coordination.k8s.io |
> | >microsoft.containerservice/managedClusters/api/協調. k8s. io/v1/read | 讀取協調/v1 |
> | >microsoft.containerservice/managedClusters/api/協調. k8s. io/v1Beta1/read | 讀取 coordination.k8s.io/v1Beta1 |
> | >microsoft.containerservice/managedClusters/api/events. k8s. io/read | 讀取 events.k8s.io |
> | >microsoft.containerservice/managedClusters/api/events. k8s. io/v1Beta1/read | 讀取 events.k8s.io/v1Beta1 |
> | >microsoft.containerservice/managedClusters/api/extensions/read | 讀取延伸模組 |
> | >microsoft.containerservice/managedClusters/api/extensions/v1Beta1/read | 讀取延伸模組/v1Beta1 |
> | >microsoft.containerservice/managedClusters/api/k8s. io/read | 讀取 metrics.k8s.io |
> | >microsoft.containerservice/managedClusters/api/計量. k8s. io/v1Beta1/read | 讀取 metrics.k8s.io/v1Beta1 |
> | >microsoft.containerservice/managedClusters/api/網路. k8s. io/read | 讀取 networking.k8s.io |
> | >microsoft.containerservice/managedClusters/api/網路。 k8s. io/v1/read | 讀取網路/v1 |
> | >microsoft.containerservice/managedClusters/api/網路。 k8s. io/v1Beta1/read | 讀取 networking.k8s.io/v1Beta1 |
> | >microsoft.containerservice/managedClusters/api/k8s. io/read | 讀取 node.k8s.io |
> | >microsoft.containerservice/managedClusters/api/node. k8s. io/v1Beta1/read | 讀取 node.k8s.io/v1Beta1 |
> | >microsoft.containerservice/managedClusters/api/policy/read | 讀取原則 |
> | >microsoft.containerservice/managedClusters/api/policy/v1Beta1/read | 讀取原則/v1Beta1 |
> | >microsoft.containerservice/managedClusters/api/rbac. k8s. io/read | 讀取 rbac.authorization.k8s.io |
> | >microsoft.containerservice/managedClusters/api/rbac. k8s.. io/v1/read | 讀取 rbac. 授權/v1 |
> | >microsoft.containerservice/managedClusters/api/rbac. k8s.. io/v1Beta1/read | 讀取 rbac.authorization.k8s.io/v1Beta1 |
> | >microsoft.containerservice/managedClusters/api/k8s. io/read | 讀取 scheduling.k8s.io |
> | >microsoft.containerservice/managedClusters/api/k8s. io/v1/read | 讀取排程/v1 |
> | >microsoft.containerservice/managedClusters/api/k8s. io/v1Beta1/read | 讀取 scheduling.k8s.io/v1Beta1 |
> | >microsoft.containerservice/managedClusters/api/k8s. io/read | 讀取 storage.k8s.io |
> | >microsoft.containerservice/managedClusters/api/儲存體. k8s. io/v1/read | 讀取儲存體/v1 |
> | >microsoft.containerservice/managedClusters/api/儲存體. k8s. io/v1Beta1/read | 讀取 storage.k8s.io/v1Beta1 |
> | >microsoft.containerservice/managedClusters/apps/controllerrevisions/read | 讀取 controllerrevisions |
> | >microsoft.containerservice/managedClusters/apps/controllerrevisions/write | 寫入 controllerrevisions |
> | >microsoft.containerservice/managedClusters/apps/controllerrevisions/delete | 刪除 controllerrevisions |
> | >microsoft.containerservice/managedClusters/apps/daemonset/read | 讀取 daemonset |
> | >microsoft.containerservice/managedClusters/apps/daemonset/write | 寫入 daemonset |
> | >microsoft.containerservice/managedClusters/apps/daemonset/delete | 刪除 daemonset |
> | >microsoft.containerservice/managedClusters/apps/部署/讀取 | 讀取部署 |
> | >microsoft.containerservice/managedClusters/apps/部署/寫入 | 寫入部署 |
> | >microsoft.containerservice/managedClusters/apps/部署/刪除 | 刪除部署 |
> | >microsoft.containerservice/managedClusters/apps/replicaset/read | 讀取 replicaset |
> | >microsoft.containerservice/managedClusters/apps/replicaset/write | 寫入 replicaset |
> | >microsoft.containerservice/managedClusters/apps/replicaset/delete | 刪除 replicaset |
> | >microsoft.containerservice/managedClusters/apps/statefulset/read | 讀取 statefulset |
> | >microsoft.containerservice/managedClusters/apps/statefulset/write | 寫入 statefulset |
> | >microsoft.containerservice/managedClusters/apps/statefulset/delete | 刪除 statefulset |
> | >microsoft.containerservice/managedClusters/authentication. k8s. io/tokenreviews/write | 寫入 tokenreviews |
> | >microsoft.containerservice/managedClusters/authentication. k8s. io/userextras/模擬/action | 模擬 userextras |
> | >microsoft.containerservice/managedClusters/authorization. k8s. io/localsubjectaccessreviews/write | 寫入 localsubjectaccessreviews |
> | >microsoft.containerservice/managedClusters/authorization. k8s. io/selfsubjectaccessreviews/write | 寫入 selfsubjectaccessreviews |
> | >microsoft.containerservice/managedClusters/authorization. k8s. io/selfsubjectrulesreviews/write | 寫入 selfsubjectrulesreviews |
> | >microsoft.containerservice/managedClusters/authorization. k8s. io/subjectaccessreviews/write | 寫入 subjectaccessreviews |
> | >microsoft.containerservice/managedClusters/自動調整/horizontalpodautoscalers/read | 讀取 horizontalpodautoscalers |
> | >microsoft.containerservice/managedClusters/自動調整/horizontalpodautoscalers/write | 寫入 horizontalpodautoscalers |
> | >microsoft.containerservice/managedClusters/自動調整/horizontalpodautoscalers/刪除 | 刪除 horizontalpodautoscalers |
> | >microsoft.containerservice/managedClusters/batch/cronjobs/read | 讀取 cronjobs |
> | >microsoft.containerservice/managedClusters/batch/cronjobs/write | 寫入 cronjobs |
> | >microsoft.containerservice/managedClusters/batch/cronjobs/delete | 刪除 cronjobs |
> | >microsoft.containerservice/managedClusters/batch/jobs/read | 讀取作業 |
> | >microsoft.containerservice/managedClusters/batch/jobs/write | 寫入作業 |
> | >microsoft.containerservice/managedClusters/batch/jobs/delete | 刪除作業 |
> | >microsoft.containerservice/managedClusters/bindings/write | 寫入系結 |
> | >microsoft.containerservice/managedClusters/certificate. k8s. io/certificatesigningrequests/read | 讀取 certificatesigningrequests |
> | >microsoft.containerservice/managedClusters/certificate. k8s. io/certificatesigningrequests/write | 寫入 certificatesigningrequests |
> | >microsoft.containerservice/managedClusters/certificate. k8s. io/certificatesigningrequests/delete | 刪除 certificatesigningrequests |
> | >microsoft.containerservice/managedClusters/componentstatuses/read | 讀取 componentstatuses |
> | >microsoft.containerservice/managedClusters/componentstatuses/write | 寫入 componentstatuses |
> | >microsoft.containerservice/managedClusters/componentstatuses/delete | 刪除 componentstatuses |
> | >microsoft.containerservice/managedClusters/configmaps/read | 讀取 configmaps |
> | >microsoft.containerservice/managedClusters/configmaps/write | 寫入 configmaps |
> | >microsoft.containerservice/managedClusters/configmaps/delete | 刪除 configmaps |
> | >microsoft.containerservice/managedClusters/k8s. io/租借/read | 讀取租用 |
> | >microsoft.containerservice/managedClusters/k8s. io/租借/write | 寫入租用 |
> | >microsoft.containerservice/managedClusters/k8s. io/租借/delete | 刪除租用 |
> | >microsoft.containerservice/managedClusters/端點/讀取 | 讀取端點 |
> | >microsoft.containerservice/managedClusters/端點/寫入 | 寫入端點 |
> | >microsoft.containerservice/managedClusters/端點/刪除 | 刪除端點 |
> | >microsoft.containerservice/managedClusters/events/read | 讀取事件 |
> | >microsoft.containerservice/managedClusters/events/write | 寫入事件 |
> | >microsoft.containerservice/managedClusters/events/delete | 刪除事件 |
> | >microsoft.containerservice/managedClusters/events. k8s. io/events/read | 讀取事件 |
> | >microsoft.containerservice/managedClusters/events. k8s. io/events/write | 寫入事件 |
> | >microsoft.containerservice/managedClusters/events. k8s. io/events/delete | 刪除事件 |
> | >microsoft.containerservice/managedClusters/extensions/daemonset/read | 讀取 daemonset |
> | >microsoft.containerservice/managedClusters/extensions/daemonset/write | 寫入 daemonset |
> | >microsoft.containerservice/managedClusters/extensions/daemonset/delete | 刪除 daemonset |
> | >microsoft.containerservice/managedClusters/extensions/部署/讀取 | 讀取部署 |
> | >microsoft.containerservice/managedClusters/extensions/部署/寫入 | 寫入部署 |
> | >microsoft.containerservice/managedClusters/extensions/部署/刪除 | 刪除部署 |
> | >microsoft.containerservice/managedClusters/extensions/ingresses/read | 讀取 ingresses |
> | >microsoft.containerservice/managedClusters/extensions/ingresses/write | 寫入 ingresses |
> | >microsoft.containerservice/managedClusters/extensions/ingresses/delete | 刪除 ingresses |
> | >microsoft.containerservice/managedClusters/extensions/networkpolicies/read | 讀取 networkpolicies |
> | >microsoft.containerservice/managedClusters/extensions/networkpolicies/write | 寫入 networkpolicies |
> | >microsoft.containerservice/managedClusters/extensions/networkpolicies/delete | 刪除 networkpolicies |
> | >microsoft.containerservice/managedClusters/extensions/podsecuritypolicies/read | 讀取 podsecuritypolicies |
> | >microsoft.containerservice/managedClusters/extensions/podsecuritypolicies/write | 寫入 podsecuritypolicies |
> | >microsoft.containerservice/managedClusters/extensions/podsecuritypolicies/delete | 刪除 podsecuritypolicies |
> | >microsoft.containerservice/managedClusters/extensions/replicaset/read | 讀取 replicaset |
> | >microsoft.containerservice/managedClusters/extensions/replicaset/write | 寫入 replicaset |
> | >microsoft.containerservice/managedClusters/extensions/replicaset/delete | 刪除 replicaset |
> | >microsoft.containerservice/managedClusters/groups/模擬/動作 | 模擬群組 |
> | >microsoft.containerservice/managedClusters/healthz/read | 讀取 healthz |
> | >microsoft.containerservice/managedClusters/healthz/autoregister-完成/讀取 | 讀取 autoregister-完成 |
> | >microsoft.containerservice/managedClusters/healthz/etcd/read | 讀取 etcd |
> | >microsoft.containerservice/managedClusters/healthz/log/read | 讀取記錄檔 |
> | >microsoft.containerservice/managedClusters/healthz/ping/read | 讀取 ping |
> | >microsoft.containerservice/managedClusters/healthz/poststarthook/apiservice-openapi-controller/read | 讀取 apiservice-openapi-控制器 |
> | >microsoft.containerservice/managedClusters/healthz/poststarthook/apiservice-註冊-控制器/讀取 | 讀取 apiservice-註冊-控制器 |
> | >microsoft.containerservice/managedClusters/healthz/poststarthook/apiservice-狀態-可用-控制器/讀取 | 讀取 apiservice-狀態-可用-控制器 |
> | >microsoft.containerservice/managedClusters/healthz/poststarthook/啟動程式-控制器/讀取 | 讀取啟動程式-控制器 |
> | >microsoft.containerservice/managedClusters/healthz/poststarthook/ca-註冊/讀取 | 讀取 ca-註冊 |
> | >microsoft.containerservice/managedClusters/healthz/poststarthook/.crd-informer-已同步/讀取 | 讀取 .crd-informer-已同步 |
> | >microsoft.containerservice/managedClusters/healthz/poststarthook/generic-apiserver-start-洩密者/read | 讀取泛型-apiserver-start-洩密者 |
> | >microsoft.containerservice/managedClusters/healthz/poststarthook/kube-apiserver-自動註冊/read | 讀取 kube-apiserver-自動註冊 |
> | >microsoft.containerservice/managedClusters/healthz/poststarthook/rbac/啟動程式-角色/讀取 | 讀取啟動程式-角色 |
> | >microsoft.containerservice/managedClusters/healthz/poststarthook/排程/啟動程式-系統優先順序-類別/讀取 | 讀取啟動程式-系統優先順序-類別 |
> | >microsoft.containerservice/managedClusters/healthz/poststarthook/開始-apiextensions-控制器/讀取 | 讀取開始-apiextensions-控制器 |
> | >microsoft.containerservice/managedClusters/healthz/poststarthook/start-apiextensions-洩密者/read | 讀取開始-apiextensions-洩密者 |
> | >microsoft.containerservice/managedClusters/healthz/poststarthook/start-kube-匯總工具-洩密者/read | 讀取開始-kube-匯總工具-洩密者 |
> | >microsoft.containerservice/managedClusters/healthz/poststarthook/開始-kube-apiserver-許可-初始化運算式/讀取 | 讀取開始-kube-apiserver-許可-初始化運算式 |
> | >microsoft.containerservice/managedClusters/limitranges/read | 讀取 limitranges |
> | >microsoft.containerservice/managedClusters/limitranges/write | 寫入 limitranges |
> | >microsoft.containerservice/managedClusters/limitranges/delete | 刪除 limitranges |
> | >microsoft.containerservice/managedClusters/livez/read | 讀取 livez |
> | >microsoft.containerservice/managedClusters/livez/autoregister-完成/讀取 | 讀取 autoregister-完成 |
> | >microsoft.containerservice/managedClusters/livez/etcd/read | 讀取 etcd |
> | >microsoft.containerservice/managedClusters/livez/log/read | 讀取記錄檔 |
> | >microsoft.containerservice/managedClusters/livez/ping/read | 讀取 ping |
> | >microsoft.containerservice/managedClusters/livez/poststarthook/apiservice-openapi-controller/read | 讀取 apiservice-openapi-控制器 |
> | >microsoft.containerservice/managedClusters/livez/poststarthook/apiservice-註冊-控制器/讀取 | 讀取 apiservice-註冊-控制器 |
> | >microsoft.containerservice/managedClusters/livez/poststarthook/apiservice-狀態-可用-控制器/讀取 | 讀取 apiservice-狀態-可用-控制器 |
> | >microsoft.containerservice/managedClusters/livez/poststarthook/啟動程式-控制器/讀取 | 讀取啟動程式-控制器 |
> | >microsoft.containerservice/managedClusters/livez/poststarthook/ca-註冊/讀取 | 讀取 ca-註冊 |
> | >microsoft.containerservice/managedClusters/livez/poststarthook/.crd-informer-已同步/讀取 | 讀取 .crd-informer-已同步 |
> | >microsoft.containerservice/managedClusters/livez/poststarthook/generic-apiserver-start-洩密者/read | 讀取泛型-apiserver-start-洩密者 |
> | >microsoft.containerservice/managedClusters/livez/poststarthook/kube-apiserver-自動註冊/read | 讀取 kube-apiserver-自動註冊 |
> | >microsoft.containerservice/managedClusters/livez/poststarthook/rbac/啟動程式-角色/讀取 | 讀取啟動程式-角色 |
> | >microsoft.containerservice/managedClusters/livez/poststarthook/排程/啟動程式-系統優先順序-類別/讀取 | 讀取啟動程式-系統優先順序-類別 |
> | >microsoft.containerservice/managedClusters/livez/poststarthook/開始-apiextensions-控制器/讀取 | 讀取開始-apiextensions-控制器 |
> | >microsoft.containerservice/managedClusters/livez/poststarthook/start-apiextensions-洩密者/read | 讀取開始-apiextensions-洩密者 |
> | >microsoft.containerservice/managedClusters/livez/poststarthook/start-kube-匯總工具-洩密者/read | 讀取開始-kube-匯總工具-洩密者 |
> | >microsoft.containerservice/managedClusters/livez/poststarthook/開始-kube-apiserver-許可-初始化運算式/讀取 | 讀取開始-kube-apiserver-許可-初始化運算式 |
> | >microsoft.containerservice/managedClusters/logs/read | 讀取記錄 |
> | >microsoft.containerservice/managedClusters/計量/讀取 | 讀取計量 |
> | >microsoft.containerservice/managedClusters/k8s。 io/節點/read | 讀取節點 |
> | >microsoft.containerservice/managedClusters/k8s. io/pod/read | 讀取 pod |
> | >microsoft.containerservice/managedClusters/命名空間/讀取 | 讀取命名空間 |
> | >microsoft.containerservice/managedClusters/命名空間/寫入 | 寫入命名空間 |
> | >microsoft.containerservice/managedClusters/命名空間/刪除 | 刪除命名空間 |
> | >microsoft.containerservice/managedClusters/網路. k8s. io/ingresses/read | 讀取 ingresses |
> | >microsoft.containerservice/managedClusters/網路. k8s. io/ingresses/write | 寫入 ingresses |
> | >microsoft.containerservice/managedClusters/網路. k8s. io/ingresses/delete | 刪除 ingresses |
> | >microsoft.containerservice/managedClusters/網路. k8s. io/networkpolicies/read | 讀取 networkpolicies |
> | >microsoft.containerservice/managedClusters/網路. k8s. io/networkpolicies/write | 寫入 networkpolicies |
> | >microsoft.containerservice/managedClusters/網路. k8s. io/networkpolicies/delete | 刪除 networkpolicies |
> | >microsoft.containerservice/managedClusters/k8s. io/runtimeclasses/read | 讀取 runtimeclasses |
> | >microsoft.containerservice/managedClusters/k8s. io/runtimeclasses/write | 寫入 runtimeclasses |
> | >microsoft.containerservice/managedClusters/k8s. io/runtimeclasses/delete | 刪除 runtimeclasses |
> | >microsoft.containerservice/managedClusters/節點/讀取 | 讀取節點 |
> | >microsoft.containerservice/managedClusters/節點/寫入 | 寫入節點 |
> | >microsoft.containerservice/managedClusters/節點/刪除 | 刪除節點 |
> | >microsoft.containerservice/managedClusters/openapi/v2/read | 讀取 v2 |
> | >microsoft.containerservice/managedClusters/persistentvolumeclaims/read | 讀取 persistentvolumeclaims |
> | >microsoft.containerservice/managedClusters/persistentvolumeclaims/write | 寫入 persistentvolumeclaims |
> | >microsoft.containerservice/managedClusters/persistentvolumeclaims/delete | 刪除 persistentvolumeclaims |
> | >microsoft.containerservice/managedClusters/persistentvolumes/read | 讀取 persistentvolumes |
> | >microsoft.containerservice/managedClusters/persistentvolumes/write | 寫入 persistentvolumes |
> | >microsoft.containerservice/managedClusters/persistentvolumes/delete | 刪除 persistentvolumes |
> | >microsoft.containerservice/managedClusters/pod/read | 讀取 pod |
> | >microsoft.containerservice/managedClusters/pod/write | 寫入 pod |
> | >microsoft.containerservice/managedClusters/pod/delete | 刪除 pod |
> | >microsoft.containerservice/managedClusters/podtemplates/read | 讀取 podtemplates |
> | >microsoft.containerservice/managedClusters/podtemplates/write | 寫入 podtemplates |
> | >microsoft.containerservice/managedClusters/podtemplates/delete | 刪除 podtemplates |
> | >microsoft.containerservice/managedClusters/policy/poddisruptionbudgets/read | 讀取 poddisruptionbudgets |
> | >microsoft.containerservice/managedClusters/policy/poddisruptionbudgets/write | 寫入 poddisruptionbudgets |
> | >microsoft.containerservice/managedClusters/policy/poddisruptionbudgets/delete | 刪除 poddisruptionbudgets |
> | >microsoft.containerservice/managedClusters/policy/podsecuritypolicies/read | 讀取 podsecuritypolicies |
> | >microsoft.containerservice/managedClusters/policy/podsecuritypolicies/write | 寫入 podsecuritypolicies |
> | >microsoft.containerservice/managedClusters/policy/podsecuritypolicies/delete | 刪除 podsecuritypolicies |
> | >microsoft.containerservice/managedClusters/policy/podsecuritypolicies/use/action | Podsecuritypolicies 上的使用動作 |
> | >microsoft.containerservice/managedClusters/rbac. k8s. io/clusterrolebindings/read | 讀取 clusterrolebindings |
> | >microsoft.containerservice/managedClusters/rbac. k8s. io/clusterrolebindings/write | 寫入 clusterrolebindings |
> | >microsoft.containerservice/managedClusters/rbac. k8s. io/clusterrolebindings/delete | 刪除 clusterrolebindings |
> | >microsoft.containerservice/managedClusters/rbac. k8s. io/clusterroles/read | 讀取 clusterroles |
> | >microsoft.containerservice/managedClusters/rbac. k8s. io/clusterroles/write | 寫入 clusterroles |
> | >microsoft.containerservice/managedClusters/rbac. k8s. io/clusterroles/delete | 刪除 clusterroles |
> | >microsoft.containerservice/managedClusters/rbac. k8s. io/clusterroles/bind/action | 系結 clusterroles |
> | >microsoft.containerservice/managedClusters/rbac. k8s. io/clusterroles/呈報/action | 升級 |
> | >microsoft.containerservice/managedClusters/rbac. k8s. io/rolebindings/read | 讀取 rolebindings |
> | >microsoft.containerservice/managedClusters/rbac. k8s. io/rolebindings/write | 寫入 rolebindings |
> | >microsoft.containerservice/managedClusters/rbac. k8s. io/rolebindings/delete | 刪除 rolebindings |
> | >microsoft.containerservice/managedClusters/rbac. k8s. io/role/read | 讀取角色 |
> | >microsoft.containerservice/managedClusters/rbac. k8s. io/role/write | 寫入角色 |
> | >microsoft.containerservice/managedClusters/rbac. k8s. io/role/delete | 刪除角色 |
> | >microsoft.containerservice/managedClusters/rbac. k8s. io/role/bind/action | 系結角色 |
> | >microsoft.containerservice/managedClusters/rbac. k8s. io/roles/呈報/action | 升級角色 |
> | >microsoft.containerservice/managedClusters/readyz/read | 讀取 readyz |
> | >microsoft.containerservice/managedClusters/readyz/autoregister-完成/讀取 | 讀取 autoregister-完成 |
> | >microsoft.containerservice/managedClusters/readyz/etcd/read | 讀取 etcd |
> | >microsoft.containerservice/managedClusters/readyz/log/read | 讀取記錄檔 |
> | >microsoft.containerservice/managedClusters/readyz/ping/read | 讀取 ping |
> | >microsoft.containerservice/managedClusters/readyz/poststarthook/apiservice-openapi-controller/read | 讀取 apiservice-openapi-控制器 |
> | >microsoft.containerservice/managedClusters/readyz/poststarthook/apiservice-註冊-控制器/讀取 | 讀取 apiservice-註冊-控制器 |
> | >microsoft.containerservice/managedClusters/readyz/poststarthook/apiservice-狀態-可用-控制器/讀取 | 讀取 apiservice-狀態-可用-控制器 |
> | >microsoft.containerservice/managedClusters/readyz/poststarthook/啟動程式-控制器/讀取 | 讀取啟動程式-控制器 |
> | >microsoft.containerservice/managedClusters/readyz/poststarthook/ca-註冊/讀取 | 讀取 ca-註冊 |
> | >microsoft.containerservice/managedClusters/readyz/poststarthook/.crd-informer-已同步/讀取 | 讀取 .crd-informer-已同步 |
> | >microsoft.containerservice/managedClusters/readyz/poststarthook/generic-apiserver-start-洩密者/read | 讀取泛型-apiserver-start-洩密者 |
> | >microsoft.containerservice/managedClusters/readyz/poststarthook/kube-apiserver-自動註冊/read | 讀取 kube-apiserver-自動註冊 |
> | >microsoft.containerservice/managedClusters/readyz/poststarthook/rbac/啟動程式-角色/讀取 | 讀取啟動程式-角色 |
> | >microsoft.containerservice/managedClusters/readyz/poststarthook/排程/啟動程式-系統優先順序-類別/讀取 | 讀取啟動程式-系統優先順序-類別 |
> | >microsoft.containerservice/managedClusters/readyz/poststarthook/開始-apiextensions-控制器/讀取 | 讀取開始-apiextensions-控制器 |
> | >microsoft.containerservice/managedClusters/readyz/poststarthook/start-apiextensions-洩密者/read | 讀取開始-apiextensions-洩密者 |
> | >microsoft.containerservice/managedClusters/readyz/poststarthook/start-kube-匯總工具-洩密者/read | 讀取開始-kube-匯總工具-洩密者 |
> | >microsoft.containerservice/managedClusters/readyz/poststarthook/開始-kube-apiserver-許可-初始化運算式/讀取 | 讀取開始-kube-apiserver-許可-初始化運算式 |
> | >microsoft.containerservice/managedClusters/readyz/shutdown/read | 讀取關機 |
> | >microsoft.containerservice/managedClusters/replicationcontrollers/read | 讀取 replicationcontrollers |
> | >microsoft.containerservice/managedClusters/replicationcontrollers/write | 寫入 replicationcontrollers |
> | >microsoft.containerservice/managedClusters/replicationcontrollers/delete | 刪除 replicationcontrollers |
> | >microsoft.containerservice/managedClusters/resetMetrics/read | 讀取 resetMetrics |
> | >microsoft.containerservice/managedClusters/resourcequotas/read | 讀取 resourcequotas |
> | >microsoft.containerservice/managedClusters/resourcequotas/write | 寫入 resourcequotas |
> | >microsoft.containerservice/managedClusters/resourcequotas/delete | 刪除 resourcequotas |
> | >microsoft.containerservice/managedClusters/k8s. io/priorityclasses/read | 讀取 priorityclasses |
> | >microsoft.containerservice/managedClusters/k8s. io/priorityclasses/write | 寫入 priorityclasses |
> | >microsoft.containerservice/managedClusters/k8s. io/priorityclasses/delete | 刪除 priorityclasses |
> | >microsoft.containerservice/managedClusters/秘密/讀取 | 讀取秘密 |
> | >microsoft.containerservice/managedClusters/秘密/寫入 | 寫入秘密 |
> | >microsoft.containerservice/managedClusters/秘密/刪除 | 刪除秘密 |
> | >microsoft.containerservice/managedClusters/serviceaccounts/read | 讀取 serviceaccounts |
> | >microsoft.containerservice/managedClusters/serviceaccounts/write | 寫入 serviceaccounts |
> | >microsoft.containerservice/managedClusters/serviceaccounts/delete | 刪除 serviceaccounts |
> | >microsoft.containerservice/managedClusters/serviceaccounts/模擬/動作 | 模擬 serviceaccounts |
> | >microsoft.containerservice/managedClusters/services/read | 讀取服務 |
> | >microsoft.containerservice/managedClusters/services/write | 寫入服務 |
> | >microsoft.containerservice/managedClusters/services/delete | 刪除服務 |
> | >microsoft.containerservice/managedClusters/k8s. io/csidrivers/read | 讀取 csidrivers |
> | >microsoft.containerservice/managedClusters/k8s. io/csidrivers/write | 寫入 csidrivers |
> | >microsoft.containerservice/managedClusters/k8s. io/csidrivers/delete | 刪除 csidrivers |
> | >microsoft.containerservice/managedClusters/k8s. io/csinodes/read | 讀取 csinodes |
> | >microsoft.containerservice/managedClusters/k8s. io/csinodes/write | 寫入 csinodes |
> | >microsoft.containerservice/managedClusters/k8s. io/csinodes/delete | 刪除 csinodes |
> | >microsoft.containerservice/managedClusters/k8s. io/storageclass/read | 讀取 storageclass |
> | >microsoft.containerservice/managedClusters/k8s. io/storageclass/write | 寫入 storageclass |
> | >microsoft.containerservice/managedClusters/k8s. io/storageclass/delete | 刪除 storageclass |
> | >microsoft.containerservice/managedClusters/k8s. io/volumeattachments/read | 讀取 volumeattachments |
> | >microsoft.containerservice/managedClusters/k8s. io/volumeattachments/write | 寫入 volumeattachments |
> | >microsoft.containerservice/managedClusters/k8s. io/volumeattachments/delete | 刪除 volumeattachments |
> | Microsoft. >microsoft.containerservice/managedClusters/swagger-api/read | 讀取 swagger-api |
> | Microsoft. >microsoft.containerservice/managedClusters/swagger-ui/read | 讀取 swagger ui |
> | Microsoft. >microsoft.containerservice/managedClusters/ui/read | 讀取 ui |
> | >microsoft.containerservice/managedClusters/users/模擬/動作 | 模擬使用者 |
> | >microsoft.containerservice/managedClusters/version/read | 讀取版本 |

### <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

Azure 服務： [Azure Dev Spaces](../dev-spaces/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.DevSpaces/register/action | 向訂用帳戶註冊 Microsoft Dev Spaces 資源提供者 |
> | Microsoft.DevSpaces/controllers/read | 讀取 Azure Dev Spaces 控制器屬性 |
> | Microsoft.DevSpaces/controllers/write | 建立或更新 Azure Dev Spaces 控制器屬性 |
> | Microsoft.DevSpaces/controllers/delete | 刪除 Azure Dev Spaces 控制器與資料平面服務 |
> | Microsoft.DevSpaces/controllers/listConnectionDetails/action | 列出 Azure Dev Spaces 控制器基礎結構的連接詳細資料 |
> | Microsoft.devspaces/位置/checkContainerHostMapping/動作 | 檢查容器主機的現有控制器對應 |
> | Microsoft.devspaces/位置/checkContainerHostMapping/讀取 | 檢查容器主機的現有控制器對應 |
> | Microsoft.devspaces/位置/operationresults/讀取 | 讀取非同步作業的結果 |
> | Microsoft.devspaces/operations/read | 列出所有支援的 Microsoft Dev Spaces 資源提供者作業 |

## <a name="databases"></a>資料庫

### <a name="microsoftcache"></a>Microsoft.Cache

Azure 服務： [Azure Cache for Redis](../azure-cache-for-redis/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Cache/checknameavailability/action | 檢查名稱是否可用於新的 Redis 快取 |
> | Microsoft.Cache/register/action | 向訂用帳戶註冊 'Microsoft.Cache' 資源提供者 |
> | Microsoft.Cache/unregister/action | 向訂用帳戶取消註冊 'Microsoft.Cache' 資源提供者 |
> | Microsoft. Cache/位置/asyncOperations/read | 列出 'Microsoft.Cache' 提供者支援的作業。 |
> | Microsoft.Cache/locations/operationresults/read | 取得先前已將 'Location' 標頭傳回用戶端之長時間執行作業的結果 |
> | Microsoft. Cache/位置/operationsStatus/read | 取得長時間執行之作業的結果，其 ' AzureAsync ' 標頭先前會傳回給用戶端 |
> | Microsoft.Cache/operations/read | 列出 'Microsoft.Cache' 提供者支援的作業。 |
> | Microsoft.Cache/redis/write | 在管理入口網站中修改 Redis 快取的設定和組態 |
> | Microsoft.Cache/redis/read | 在管理入口網站中檢視 Redis 快取的設定和組態 |
> | Microsoft.Cache/redis/delete | 刪除整個 Redis 快取 |
> | Microsoft.Cache/redis/listKeys/action | 在管理入口網站中檢視 Redis 快取存取金鑰的值 |
> | Microsoft.Cache/redis/regenerateKey/action | 在管理入口網站中變更 Redis 快取存取金鑰的值 |
> | Microsoft.Cache/redis/import/action | 從多個 Blob 將指定格式的資料匯入 Redis |
> | Microsoft.Cache/redis/export/action | 以指定格式將 Redis 資料匯出至有前置詞的儲存體 Blob |
> | Microsoft.Cache/redis/forceReboot/action | 強制重新啟動快取執行個體，但可能會造成資料遺失。 |
> | Microsoft.Cache/redis/stop/action | 停止快取執行個體。 |
> | Microsoft.Cache/redis/start/action | 啟動快取執行個體。 |
> | Microsoft. Cache/redis/eventGridFilters/read | 取得 Redis 快取事件方格篩選 |
> | Redis/eventGridFilters/write | 更新 Redis 快取事件方格篩選 |
> | Microsoft. Cache/redis/eventGridFilters/delete | 刪除 Redis 快取事件方格篩選 |
> | Microsoft.Cache/redis/firewallRules/read | 取得 Redis 快取的 IP 防火牆規則 |
> | Microsoft.Cache/redis/firewallRules/write | 編輯 Redis 快取的 IP 防火牆規則 |
> | Microsoft.Cache/redis/firewallRules/delete | 刪除 Redis 快取的 IP 防火牆規則 |
> | Microsoft.Cache/redis/linkedservers/read | 取得與 Redis 快取相關聯的連結伺服器。 |
> | Microsoft.Cache/redis/linkedservers/write | 對 Redis 快取新增連結伺服器 |
> | Microsoft.Cache/redis/linkedservers/delete | 從 Redis 快取中刪除連結伺服器 |
> | Microsoft.Cache/redis/metricDefinitions/read | 取得 Redis 快取可用的計量 |
> | Microsoft.Cache/redis/patchSchedules/read | 取得 Redis 快取的修補排程 |
> | Microsoft.Cache/redis/patchSchedules/write | 修改 Redis 快取的修補排程 |
> | Microsoft.Cache/redis/patchSchedules/delete | 刪除 Redis 快取的修補排程 |
> | Microsoft. Cache/redis/privateEndpointConnectionProxies/validate/action | 驗證私人端點連線 proxy |
> | Microsoft. Cache/redis/privateEndpointConnectionProxies/read | 取得私人端點連線 proxy |
> | Redis/privateEndpointConnectionProxies/write | 建立私人端點連接 proxy |
> | Microsoft. Cache/redis/privateEndpointConnectionProxies/delete | 刪除私人端點連線 proxy |
> | Microsoft. Cache/redis/privateEndpointConnections/read | 讀取私人端點連線 |
> | Redis/privateEndpointConnections/write | 撰寫私人端點連接 |
> | Microsoft. Cache/redis/privateEndpointConnections/delete | 刪除私人端點連線 |
> | Microsoft. Cache/redis/privateLinkResources/read | 讀取私用連結可以連線的 redis subresource ' groupId ' |
> | Microsoft. Cache/redisEnterprise/privateEndpointConnectionProxies/validate/action | 驗證私人端點連線 proxy |
> | Microsoft. Cache/redisEnterprise/privateEndpointConnectionProxies/read | 取得私人端點連線 proxy |
> | RedisEnterprise/privateEndpointConnectionProxies/write | 建立私人端點連接 proxy |
> | Microsoft. Cache/redisEnterprise/privateEndpointConnectionProxies/delete | 刪除私人端點連線 proxy |
> | RedisEnterprise/privateEndpointConnectionProxies/operationresults/read | 刪除私人端點連線 proxy |
> | Microsoft. Cache/redisEnterprise/provider/Microsoft. Insights/metricDefinitions/read | 取得 Redis Enterprise Cache 的可用計量 |

### <a name="microsoftdatafactory"></a>Microsoft.DataFactory

Azure 服務： [Data Factory](../data-factory/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.DataFactory/register/action | 為 Data Factory 資源提供者註冊訂用帳戶。 |
> | Microsoft.DataFactory/unregister/action | 為 Data Factory 資源提供者取消註冊訂用帳戶。 |
> | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | 檢查 Data Factory 名稱是否可供使用。 |
> | Microsoft.DataFactory/datafactories/read | 讀取 Data Factory。 |
> | Microsoft.DataFactory/datafactories/write | 建立或更新 Data Factory。 |
> | Microsoft.DataFactory/datafactories/delete | 刪除 Data Factory。 |
> | Microsoft.DataFactory/datafactories/activitywindows/read | 使用指定參數讀取 Data Factory 中的活動視窗。 |
> | Microsoft.DataFactory/datafactories/datapipelines/read | 讀取任何管線。 |
> | Microsoft.DataFactory/datafactories/datapipelines/delete | 刪除任何管線。 |
> | Microsoft.DataFactory/datafactories/datapipelines/pause/action | 暫停任何管線。 |
> | Microsoft.DataFactory/datafactories/datapipelines/resume/action | 繼續任何管線。 |
> | Microsoft.DataFactory/datafactories/datapipelines/update/action | 更新任何管線。 |
> | Microsoft.DataFactory/datafactories/datapipelines/write | 建立或更新任何管線。 |
> | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | 使用指定參數讀取管線活動的活動視窗。 |
> | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | 使用指定參數讀取管線的活動視窗。 |
> | Microsoft.DataFactory/datafactories/datasets/read | 讀取任何資料集。 |
> | Microsoft.DataFactory/datafactories/datasets/delete | 刪除任何資料集。 |
> | Microsoft.DataFactory/datafactories/datasets/write | 建立或更新任何資料集。 |
> | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | 使用指定參數讀取資料集的活動視窗。 |
> | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | 針對具有指定開始時間的指定資料集讀取資料配量執行。 |
> | Microsoft.DataFactory/datafactories/datasets/slices/read | 取得指定期間內的資料配量。 |
> | Microsoft.DataFactory/datafactories/datasets/slices/write | 更新資料配量的狀態。 |
> | Microsoft.DataFactory/datafactories/gateways/read | 讀取任何閘道。 |
> | Microsoft.DataFactory/datafactories/gateways/write | 建立或更新任何閘道。 |
> | Microsoft.DataFactory/datafactories/gateways/delete | 刪除任何閘道。 |
> | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | 讀取任何閘道的連線資訊。 |
> | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | 列出任何閘道的驗證金鑰。 |
> | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | 重新產生任何閘道的驗證金鑰。 |
> | Microsoft.DataFactory/datafactories/linkedServices/read | 讀取任何連結服務。 |
> | Microsoft.DataFactory/datafactories/linkedServices/delete | 刪除任何連結服務。 |
> | Microsoft.DataFactory/datafactories/linkedServices/write | 建立或更新任何連結服務。 |
> | Microsoft.DataFactory/datafactories/runs/loginfo/read | 讀取包含記錄的 Blob 容器 SAS URI。 |
> | Microsoft.DataFactory/datafactories/tables/read | 讀取任何資料集。 |
> | Microsoft.DataFactory/datafactories/tables/delete | 刪除任何資料集。 |
> | Microsoft.DataFactory/datafactories/tables/write | 建立或更新任何資料集。 |
> | Microsoft.DataFactory/factories/read | 讀取 Data Factory。 |
> | Microsoft.DataFactory/factories/write | 建立或更新 Data Factory |
> | Microsoft.DataFactory/factories/delete | 刪除 Data Factory。 |
> | DataFactory/工廠/createdataflowdebugsession/action | 建立資料流程的 debug 會話。 |
> | DataFactory/工廠/startdataflowdebugsession/action | 啟動資料流程的 debug 會話。 |
> | DataFactory/工廠/addDataFlowToDebugSession/action | 將資料流程新增至 debug 會話進行預覽。 |
> | DataFactory/工廠/executeDataFlowDebugCommand/action | 執行資料流程調試命令。 |
> | DataFactory/工廠/deletedataflowdebugsession/action | 刪除資料流程的調試會話。 |
> | DataFactory/工廠/querydataflowdebugsessions/action | 查詢資料流程的 debug 會話。 |
> | Microsoft.DataFactory/factories/cancelpipelinerun/action | 取消執行識別碼所指定的管線執行。 |
> | DataFactory/工廠/cancelSandboxPipelineRun/action | 取消管線的偵錯工具執行。 |
> | DataFactory/工廠/sandboxpipelineruns/action | 查詢偵錯管線執行。 |
> | DataFactory/工廠/querytriggers/action | 查詢觸發程式。 |
> | DataFactory/工廠/getFeatureValue/action | 取得特定位置的暴露風險控制特徵值。 |
> | DataFactory/工廠/queryFeaturesValue/action | 取得功能清單的公開控制功能值 |
> | DataFactory/工廠/getDataPlaneAccess/action | 取得 ADF DataPlane 服務的存取權。 |
> | Microsoft.DataFactory/factories/getGitHubAccessToken/action | 取得 GitHub 存取權杖。 |
> | Microsoft.DataFactory/factories/querytriggerruns/action | 查詢觸發程序執行。 |
> | Microsoft.DataFactory/factories/querypipelineruns/action | 查詢管線執行。 |
> | Microsoft.DataFactory/factories/querydebugpipelineruns/action | 查詢偵錯管線執行。 |
> | DataFactory/factory/資料流程/read | 讀取資料流程。 |
> | DataFactory/factory/資料流程/delete | 刪除資料流程。 |
> | DataFactory/factory/資料流程/write | 建立或更新資料流程 |
> | Microsoft.DataFactory/factories/datasets/read | 讀取任何資料集。 |
> | Microsoft.DataFactory/factories/datasets/delete | 刪除任何資料集。 |
> | Microsoft.DataFactory/factories/datasets/write | 建立或更新任何資料集。 |
> | DataFactory/factory/debugpipelineruns/cancel/action | 取消管線的偵錯工具執行。 |
> | DataFactory/factory/getDataPlaneAccess/read | 讀取 ADF DataPlane 服務的存取權。 |
> | DataFactory/factory/getFeatureValue/read | 讀取特定位置的暴露風險控制特徵值。 |
> | Microsoft.DataFactory/factories/integrationruntimes/read | 讀取任何 Integration Runtime。 |
> | Microsoft.DataFactory/factories/integrationruntimes/write | 建立或更新任何 Integration Runtime。 |
> | Microsoft.DataFactory/factories/integrationruntimes/delete | 刪除任何 Integration Runtime。 |
> | Microsoft.DataFactory/factories/integrationruntimes/start/action | 取動任何 Integration Runtime。 |
> | Microsoft.DataFactory/factories/integrationruntimes/stop/action | 停止任何 Integration Runtime。 |
> | DataFactory/factory/integrationruntimes/getconnectioninfo/action | 讀取 Integration Runtime 連線資訊。 |
> | DataFactory/factory/integrationruntimes/listauthkeys/action | 列出任何 Integration Runtime 的驗證金鑰。 |
> | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | 同步處理所指定 Integration Runtime 的認證。 |
> | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | 將指定的 Integration Runtime 升級。 |
> | DataFactory/factory/integrationruntimes/createexpressshirinstalllink/action | 建立自我裝載 Integration Runtime 的快速安裝連結。 |
> | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | 重新產生所指定 Integration Runtime 的驗證金鑰。 |
> | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | 從指定的 Integration Runtime 中移除連結的 Integration Runtime 參考。 |
> | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | 在指定的共用 Integration Runtime 上建立連結的 Integration Runtime 參考。 |
> | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | 取得所指定 Integration Runtime 的 SSIS Integration Runtime 中繼資料。 |
> | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | 重新整理所指定 Integration Runtime 的 SSIS Integration Runtime 中繼資料。 |
> | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | 讀取 Integration Runtime 狀態。 |
> | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | 取得任何 Integration Runtime 的監視資料。 |
> | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | 讀取所指定 Integration Runtime 的節點。 |
> | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | 刪除所指定 Integration Runtime 的節點。 |
> | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | 更新自我裝載的 Integration Runtime 節點。 |
> | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | 傳回 Integration Runtime 所指定節點的 IP 位址。 |
> | Microsoft.DataFactory/factories/linkedServices/read | 讀取連結服務。 |
> | Microsoft.DataFactory/factories/linkedServices/delete | 刪除連結服務。 |
> | Microsoft.DataFactory/factories/linkedServices/write | 建立或更新連結服務 |
> | DataFactory/factory/managedVirtualNetworks/read | 讀取受管理的虛擬網路。 |
> | DataFactory/factory/managedVirtualNetworks/write | 建立或更新受管理的虛擬網路。 |
> | DataFactory/factory/managedVirtualNetworks/managedPrivateEndpoints/read | 讀取受控私人端點。 |
> | DataFactory/factory/managedVirtualNetworks/managedPrivateEndpoints/write | 建立或更新受控私人端點。 |
> | DataFactory/factory/managedVirtualNetworks/managedPrivateEndpoints/delete | 刪除受控私人端點。 |
> | DataFactory/factory/operationResults/read | 取得作業結果。 |
> | Microsoft.DataFactory/factories/pipelineruns/read | 讀取管線執行。 |
> | Microsoft.DataFactory/factories/pipelineruns/cancel/action | 取消執行識別碼所指定的管線執行。 |
> | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | 查詢所指定管線執行識別碼的活動執行。 |
> | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | 讀取所指定管線執行識別碼的活動執行。 |
> | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | 讀取所指定管線執行識別碼的查詢活動執行結果。 |
> | Microsoft.DataFactory/factories/pipelines/read | 讀取管線。 |
> | Microsoft.DataFactory/factories/pipelines/delete | 刪除管線。 |
> | Microsoft.DataFactory/factories/pipelines/write | 建立或更新管線 |
> | Microsoft.DataFactory/factories/pipelines/createrun/action | 建立管線的執行。 |
> | DataFactory/工廠/管線/沙箱/動作 | 建立管線的偵錯工具執行環境。 |
> | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | 讀取管線執行。 |
> | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | 取得活動執行的進度。 |
> | DataFactory/factory/管線/沙箱/建立/動作 | 建立管線的偵錯工具執行環境。 |
> | DataFactory/factory/管線/沙箱/執行/動作 | 建立管線的偵錯工具執行。 |
> | DataFactory/factory/privateEndpointConnectionProxies/read | 讀取私人端點連線 Proxy。 |
> | DataFactory/factory/privateEndpointConnectionProxies/write | 建立或更新私人端點連線 Proxy。 |
> | DataFactory/factory/privateEndpointConnectionProxies/delete | 刪除私人端點連線 Proxy。 |
> | DataFactory/factory/privateEndpointConnectionProxies/驗證/動作 | 驗證私人端點連線 Proxy。 |
> | DataFactory/factory/privateEndpointConnectionProxies/operationresults/read | 閱讀建立私人端點連接 Proxy 的結果。 |
> | DataFactory/factory/privateEndpointConnectionProxies/operationstatuses/read | 讀取建立私人端點連線 Proxy 的狀態。 |
> | Microsoft.DataFactory/factories/querypipelineruns/read | 讀取查詢管線執行的結果。 |
> | Microsoft.DataFactory/factories/querytriggerruns/read | 讀取觸發程序執行的結果。 |
> | DataFactory/factory/sandboxpipelineruns/read | 取得管線的偵錯工具執行資訊。 |
> | DataFactory/factory/sandboxpipelineruns/sandboxActivityRuns/read | 取得活動的偵錯工具執行資訊。 |
> | Microsoft.DataFactory/factories/triggerruns/read | 讀取觸發程序執行。 |
> | Microsoft.DataFactory/factories/triggers/read | 讀取任何觸發程序。 |
> | Microsoft.DataFactory/factories/triggers/write | 建立或更新任何觸發程序。 |
> | Microsoft.DataFactory/factories/triggers/delete | 刪除任何觸發程序。 |
> | DataFactory/factory/觸發程式/subscribetoevents/動作 | 訂閱事件。 |
> | DataFactory/factory/觸發程式/geteventsubscriptionstatus/動作 | 事件訂閱狀態。 |
> | DataFactory/factory/觸發程式/unsubscribefromevents/動作 | 取消訂閱事件。 |
> | Microsoft.DataFactory/factories/triggers/start/action | 啟動任何觸發程序。 |
> | Microsoft.DataFactory/factories/triggers/stop/action | 停止任何觸發程序。 |
> | Microsoft.DataFactory/factories/triggers/triggerruns/read | 讀取觸發程序執行。 |
> | DataFactory/factory/觸發程式/triggerruns/取消/動作 | 使用指定的觸發程式執行識別碼取消觸發程式執行。 |
> | DataFactory/factory/觸發程式/triggerruns/重新執行/動作 | 使用指定的觸發程式執行識別碼重新執行觸發程式。 |
> | Microsoft.DataFactory/locations/configureFactoryRepo/action | 設定中心的存放庫。 |
> | Microsoft.DataFactory/locations/getFeatureValue/action | 取得特定位置的暴露風險控制特徵值。 |
> | Microsoft.DataFactory/locations/getFeatureValue/read | 讀取特定位置的暴露風險控制特徵值。 |
> | Microsoft.DataFactory/operations/read | 讀取 Microsoft Data Factory 提供者中的所有作業。 |

### <a name="microsoftdatamigration"></a>Microsoft.DataMigration

Azure 服務： [Azure 資料庫移轉服務](../dms/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.DataMigration/register/action | 向「Azure 資料庫移轉服務」提供者註冊訂用帳戶 |
> | Microsoft.DataMigration/locations/operationResults/read | 取得與「202 已接受」回應相關且長時間執行之作業的狀態 |
> | Microsoft.DataMigration/locations/operationStatuses/read | 取得與「202 已接受」回應相關且長時間執行之作業的狀態 |
> | Microsoft.DataMigration/services/read | 閱讀資源的相關資訊 |
> | Microsoft.DataMigration/services/write | 建立或更新資源及其屬性 |
> | Microsoft.DataMigration/services/delete | 刪除資源及其所有子系 |
> | Microsoft.DataMigration/services/stop/action | 停止 DMS 服務以將其成本降到最低 |
> | Microsoft.DataMigration/services/start/action | 啟動 DMS 服務以讓它再次處理移轉 |
> | Microsoft.DataMigration/services/checkStatus/action | 檢查服務是否已部署且在執行中 |
> | Microsoft.datamigration/services/configureWorker/action | 將 DMS 背景工作角色設定為服務的可用背景工作 |
> | Microsoft.datamigration/services/addWorker/action | 將 DMS 背景工作角色新增至服務的可用背景工作 |
> | Microsoft.datamigration/services/removeWorker/action | 將 DMS 背景工作角色移除至服務的可用背景工作 |
> | Microsoft.datamigration/services/updateAgentConfig/action | 以提供的值更新 DMS 代理程式設定。 |
> | Microsoft.datamigration/services/getHybridDownloadLink/action | 從 RP Blob 儲存體取得 DMS 背景工作套件的下載連結。 |
> | Microsoft.DataMigration/services/projects/read | 閱讀資源的相關資訊 |
> | Microsoft.DataMigration/services/projects/write | 執行工作「Azure 資料庫移轉服務」工作 |
> | Microsoft.DataMigration/services/projects/delete | 刪除資源及其所有子系 |
> | Microsoft.DataMigration/services/projects/accessArtifacts/action | 產生可用來對專案成品進行 GET 或 PUT 的 URL |
> | Microsoft.DataMigration/services/projects/tasks/read | 閱讀資源的相關資訊 |
> | Microsoft.DataMigration/services/projects/tasks/write | 執行工作「Azure 資料庫移轉服務」工作 |
> | Microsoft.DataMigration/services/projects/tasks/delete | 刪除資源及其所有子系 |
> | Microsoft.DataMigration/services/projects/tasks/cancel/action | 取消目前正在執行的工作 |
> | Microsoft.datamigration/services/serviceTasks/read | 閱讀資源的相關資訊 |
> | Microsoft.datamigration/services/serviceTasks/write | 執行工作「Azure 資料庫移轉服務」工作 |
> | Microsoft.datamigration/services/serviceTasks/delete | 刪除資源及其所有子系 |
> | Microsoft. Microsoft.datamigration/services/serviceTasks/cancel/action | 取消目前正在執行的工作 |
> | Microsoft.DataMigration/services/slots/read | 閱讀資源的相關資訊 |
> | Microsoft.DataMigration/services/slots/write | 建立或更新資源及其屬性 |
> | Microsoft.DataMigration/services/slots/delete | 刪除資源及其所有子系 |
> | Microsoft.DataMigration/skus/read | 取得 DMS 資源所支援的 SKU 清單。 |

### <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

Azure 服務： [適用於 MariaDB 的 Azure 資料庫](../mariadb/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | DBforMariaDB/privateEndpointConnectionsApproval/action | 決定是否允許使用者核准私人端點連接 |
> | DBforMariaDB/註冊/動作 | 註冊適用于 mariadb 資源提供者 |
> | DBforMariaDB/checkNameAvailability/action | 確認指定的伺服器名稱是否可用來針對指定的訂用帳戶進行全球佈建。 |
> | DBforMariaDB/位置/administratorAzureAsyncOperation/讀取 | 取得適用于 mariadb 伺服器系統管理員的進行中作業 |
> | DBforMariaDB/位置/administratorOperationResults/讀取 | 傳回適用于 mariadb 伺服器系統管理員操作結果 |
> | DBforMariaDB/位置/azureAsyncOperation/讀取 | 傳回適用于 mariadb 伺服器操作結果 |
> | DBforMariaDB/位置/operationResults/讀取 | 傳回以 ResourceGroup 為基礎的適用于 mariadb 伺服器操作結果 |
> | DBforMariaDB/位置/operationResults/讀取 | 傳回適用于 mariadb 伺服器操作結果 |
> | Microsoft.DBforMariaDB/locations/performanceTiers/read | 傳回可用的效能層級清單。 |
> | DBforMariaDB/位置/privateEndpointConnectionAzureAsyncOperation/讀取 | 取得私人端點連接作業的結果 |
> | DBforMariaDB/位置/privateEndpointConnectionOperationResults/讀取 | 取得私人端點連接作業的結果 |
> | DBforMariaDB/位置/privateEndpointConnectionProxyAzureAsyncOperation/讀取 | 取得私人端點連接 proxy 作業的結果 |
> | DBforMariaDB/位置/privateEndpointConnectionProxyOperationResults/讀取 | 取得私人端點連接 proxy 作業的結果 |
> | DBforMariaDB/位置/securityAlertPoliciesAzureAsyncOperation/讀取 | 傳回伺服器威脅偵測操作結果的清單。 |
> | DBforMariaDB/位置/securityAlertPoliciesOperationResults/讀取 | 傳回伺服器威脅偵測操作結果的清單。 |
> | DBforMariaDB/位置/serverKeyAzureAsyncOperation/讀取 | 取得資料加密伺服器金鑰的進行中作業 |
> | DBforMariaDB/位置/serverKeyOperationResults/讀取 | 取得透明資料加密伺服器金鑰的進行中作業 |
> | DBforMariaDB/operations/read | 傳回適用于 mariadb 作業的清單。 |
> | Microsoft.DBforMariaDB/performanceTiers/read | 傳回可用的效能層級清單。 |
> | DBforMariaDB/伺服器/開始/動作 | 啟動特定的伺服器。 |
> | DBforMariaDB/servers/stop/action | 停止特定的伺服器。 |
> | DBforMariaDB/servers/resetQueryPerformanceInsightData/action | 重設查詢效能深入解析資料 |
> | DBforMariaDB/servers/queryTexts/action | 傳回查詢清單的文字 |
> | DBforMariaDB/servers/queryTexts/action | 傳回查詢的文字 |
> | DBforMariaDB/servers/privateEndpointConnectionsApproval/action | 決定是否允許使用者核准私人端點連接 |
> | Microsoft.DBforMariaDB/servers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | Microsoft.DBforMariaDB/servers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | Microsoft.DBforMariaDB/servers/delete | 刪除現有伺服器。 |
> | DBforMariaDB/servers/restart/action | 重新開機特定的伺服器。 |
> | Microsoft.DBforMariaDB/servers/updateConfigurations/action | 更新指定伺服器的組態 |
> | DBforMariaDB/servers/administrator/read | 取得適用于 mariadb 伺服器系統管理員的清單。 |
> | DBforMariaDB/servers/administrator/write | 使用指定的參數建立或更新適用于 mariadb 伺服器系統管理員。 |
> | DBforMariaDB/servers/administrator/delete | 刪除適用于 mariadb 伺服器的現有系統管理員。 |
> | DBforMariaDB/servers/顧問/read | 傳回建議程式清單 |
> | DBforMariaDB/servers/顧問/read | 返回 advisor |
> | DBforMariaDB/servers/顧問/createRecommendedActionSession/action | 建立新的建議動作會話 |
> | DBforMariaDB/servers/顧問/recommendedActions/read | 傳回建議的動作清單 |
> | DBforMariaDB/servers/顧問/recommendedActions/read | 傳回建議的動作清單 |
> | DBforMariaDB/servers/顧問/recommendedActions/read | 返回建議的動作 |
> | Microsoft.DBforMariaDB/servers/configurations/read | 傳回伺服器的組態清單，或取得指定組態的屬性。 |
> | Microsoft.DBforMariaDB/servers/configurations/write | 更新指定組態的值 |
> | DBforMariaDB/servers/資料庫/讀取 | 傳回適用于 mariadb 資料庫的清單，或取得指定資料庫的屬性。 |
> | DBforMariaDB/servers/資料庫/寫入 | 使用指定的參數建立適用于 mariadb 資料庫，或更新指定之資料庫的屬性。 |
> | DBforMariaDB/servers/資料庫/刪除 | 刪除現有的適用于 mariadb 資料庫。 |
> | Microsoft.DBforMariaDB/servers/firewallRules/read | 傳回伺服器的防火牆規則清單，或取得指定防火牆規則的屬性。 |
> | Microsoft.DBforMariaDB/servers/firewallRules/write | 使用指定參數建立防火牆規則，或更新現有的規則。 |
> | Microsoft.DBforMariaDB/servers/firewallRules/delete | 刪除現有防火牆規則。 |
> | DBforMariaDB/servers/keys/read | 傳回伺服器金鑰的清單，或取得指定伺服器金鑰的屬性。 |
> | DBforMariaDB/servers/keys/write | 使用指定參數建立金鑰，或更新指定伺服器金鑰的屬性或標記。 |
> | DBforMariaDB/servers/keys/delete | 刪除現有的伺服器金鑰。 |
> | DBforMariaDB/servers/記錄檔/讀取 | 傳回適用于 mariadb 記錄檔的清單。 |
> | DBforMariaDB/servers/privateEndpointConnectionProxies/validate/action | 驗證私人端點連接從 NRP 端建立呼叫 |
> | DBforMariaDB/servers/privateEndpointConnectionProxies/read | 傳回私人端點連線 proxy 的清單，或取得指定私人端點連接 proxy 的屬性。 |
> | DBforMariaDB/servers/privateEndpointConnectionProxies/write | 使用指定的參數建立私人端點連線 proxy，或更新指定私人端點連接 proxy 的屬性或標記。 |
> | DBforMariaDB/servers/privateEndpointConnectionProxies/delete | 刪除現有的私人端點連線 proxy |
> | DBforMariaDB/servers/privateEndpointConnections/read | 傳回私人端點連接的清單，或取得指定私人端點連接的屬性。 |
> | DBforMariaDB/servers/privateEndpointConnections/delete | 刪除現有的私人端點連接 |
> | DBforMariaDB/servers/privateEndpointConnections/write | 核准或拒絕現有的私人端點連線 |
> | DBforMariaDB/servers/privateLinkResources/read | 取得對應適用于 mariadb 伺服器的私人連結資源 |
> | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | 取得 MariaDB 伺服器的可用記錄 |
> | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> | Microsoft.DBforMariaDB/servers/recoverableServers/read | 傳回可復原的 MariaDB 伺服器資訊 |
> | DBforMariaDB/servers/複本/read | 取得適用于 mariadb 伺服器的讀取複本 |
> | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | 擷取給定伺服器上所設定的伺服器威脅偵測原則詳細資料 |
> | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | 變更指定伺服器的伺服器威脅偵測原則 |
> | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | 取得針對指定伺服器所設定的伺服器威脅偵測原則清單 |
> | DBforMariaDB/servers/topQueryStatistics/read | 傳回最高排名查詢的查詢統計資料清單。 |
> | DBforMariaDB/servers/topQueryStatistics/read | 傳回查詢統計資料 |
> | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | 傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。 |
> | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | 使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。 |
> | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | 刪除現有虛擬網路規則 |
> | DBforMariaDB/servers/waitStatistics/read | 傳回執行個體的等候統計資料 |
> | DBforMariaDB/servers/waitStatistics/read | 傳回等候統計資料 |

### <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

Azure 服務： [適用於 MySQL 的 Azure 資料庫](../mysql/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.dbformysql/privateEndpointConnectionsApproval/action | 決定是否允許使用者核准私人端點連接 |
> | Microsoft.dbformysql/註冊/動作 | 註冊 MySQL 資源提供者 |
> | Microsoft.dbformysql/checkNameAvailability/action | 確認指定的伺服器名稱是否可用來針對指定的訂用帳戶進行全球佈建。 |
> | Microsoft.dbformysql/flexibleServers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | Microsoft.dbformysql/flexibleServers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | Microsoft.dbformysql/flexibleServers/delete | 刪除現有伺服器。 |
> | Microsoft.dbformysql/flexibleServers/providers/Microsoft. Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | Microsoft.dbformysql/flexibleServers/providers/Microsoft. Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Microsoft.dbformysql/flexibleServers/providers/Microsoft. Insights/logDefinitions/read | 取得 MySQL 伺服器的可用記錄 |
> | Microsoft.dbformysql/flexibleServers/providers/Microsoft. Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> | Microsoft.dbformysql/位置/administratorAzureAsyncOperation/讀取 | 取得 MySQL 伺服器管理員的進行中作業 |
> | Microsoft.dbformysql/位置/administratorOperationResults/讀取 | 傳回 MySQL 伺服器管理員操作結果 |
> | Microsoft.dbformysql/位置/azureAsyncOperation/讀取 | 傳回 MySQL 伺服器操作結果 |
> | Microsoft.dbformysql/位置/operationResults/讀取 | 傳回以 ResourceGroup 為基礎的 MySQL 伺服器操作結果 |
> | Microsoft.dbformysql/位置/operationResults/讀取 | 傳回 MySQL 伺服器操作結果 |
> | Microsoft.DBforMySQL/locations/performanceTiers/read | 傳回可用的效能層級清單。 |
> | Microsoft.dbformysql/位置/privateEndpointConnectionAzureAsyncOperation/讀取 | 取得私人端點連接作業的結果 |
> | Microsoft.dbformysql/位置/privateEndpointConnectionOperationResults/讀取 | 取得私人端點連接作業的結果 |
> | Microsoft.dbformysql/位置/privateEndpointConnectionProxyAzureAsyncOperation/讀取 | 取得私人端點連接 proxy 作業的結果 |
> | Microsoft.dbformysql/位置/privateEndpointConnectionProxyOperationResults/讀取 | 取得私人端點連接 proxy 作業的結果 |
> | Microsoft.dbformysql/位置/securityAlertPoliciesAzureAsyncOperation/讀取 | 傳回伺服器威脅偵測操作結果的清單。 |
> | Microsoft.dbformysql/位置/securityAlertPoliciesOperationResults/讀取 | 傳回伺服器威脅偵測操作結果的清單。 |
> | Microsoft.dbformysql/位置/serverKeyAzureAsyncOperation/讀取 | 取得透明資料加密伺服器金鑰的進行中作業 |
> | Microsoft.dbformysql/位置/serverKeyOperationResults/讀取 | 取得資料加密伺服器金鑰的進行中作業 |
> | Microsoft.dbformysql/operations/read | 傳回 MySQL 作業的清單。 |
> | Microsoft.DBforMySQL/performanceTiers/read | 傳回可用的效能層級清單。 |
> | Microsoft.dbformysql/servers/upgrade/action |  |
> | Microsoft.dbformysql/伺服器/開始/動作 | 啟動特定的伺服器。 |
> | Microsoft.dbformysql/servers/stop/action | 停止特定的伺服器。 |
> | Microsoft.dbformysql/servers/privateEndpointConnectionsApproval/action | 決定是否允許使用者核准私人端點連接 |
> | Microsoft.dbformysql/servers/resetQueryPerformanceInsightData/action | 重設查詢效能深入解析資料 |
> | Microsoft.dbformysql/servers/queryTexts/action | 傳回查詢清單的文字 |
> | Microsoft.dbformysql/servers/queryTexts/action | 傳回查詢的文字 |
> | Microsoft.DBforMySQL/servers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | Microsoft.DBforMySQL/servers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | Microsoft.DBforMySQL/servers/delete | 刪除現有伺服器。 |
> | Microsoft.dbformysql/servers/restart/action | 重新開機特定的伺服器。 |
> | Microsoft.DBforMySQL/servers/updateConfigurations/action | 更新指定伺服器的組態 |
> | Microsoft.dbformysql/servers/administrator/read | 取得 MySQL 伺服器管理員的清單。 |
> | Microsoft.dbformysql/servers/administrator/write | 使用指定的參數建立或更新 MySQL server 系統管理員。 |
> | Microsoft.dbformysql/servers/administrator/delete | 刪除 MySQL 伺服器的現有系統管理員。 |
> | Microsoft.dbformysql/servers/顧問/read | 傳回建議程式清單 |
> | Microsoft.dbformysql/servers/顧問/read | 返回 advisor |
> | Microsoft.dbformysql/servers/顧問/createRecommendedActionSession/action | 建立新的建議動作會話 |
> | Microsoft.dbformysql/servers/顧問/recommendedActions/read | 傳回建議的動作清單 |
> | Microsoft.dbformysql/servers/顧問/recommendedActions/read | 傳回建議的動作清單 |
> | Microsoft.dbformysql/servers/顧問/recommendedActions/read | 返回建議的動作 |
> | Microsoft.DBforMySQL/servers/configurations/read | 傳回伺服器的組態清單，或取得指定組態的屬性。 |
> | Microsoft.DBforMySQL/servers/configurations/write | 更新指定組態的值 |
> | Microsoft.dbformysql/servers/資料庫/讀取 | 傳回 MySQL 資料庫的清單，或取得指定資料庫的屬性。 |
> | Microsoft.dbformysql/servers/資料庫/寫入 | 使用指定的參數建立 MySQL 資料庫，或更新指定之資料庫的屬性。 |
> | Microsoft.dbformysql/servers/資料庫/刪除 | 刪除現有的 MySQL 資料庫。 |
> | Microsoft.dbformysql/servers/匯出/寫入 |  |
> | Microsoft.dbformysql/servers/匯出/讀取 |  |
> | Microsoft.dbformysql/servers/匯出/讀取 |  |
> | Microsoft.DBforMySQL/servers/firewallRules/read | 傳回伺服器的防火牆規則清單，或取得指定防火牆規則的屬性。 |
> | Microsoft.DBforMySQL/servers/firewallRules/write | 使用指定參數建立防火牆規則，或更新現有的規則。 |
> | Microsoft.DBforMySQL/servers/firewallRules/delete | 刪除現有防火牆規則。 |
> | Microsoft.dbformysql/servers/keys/read | 傳回伺服器金鑰的清單，或取得指定伺服器金鑰的屬性。 |
> | Microsoft.dbformysql/servers/keys/write | 使用指定參數建立金鑰，或更新指定伺服器金鑰的屬性或標記。 |
> | Microsoft.dbformysql/servers/keys/delete | 刪除現有的伺服器金鑰。 |
> | Microsoft.dbformysql/servers/記錄檔/讀取 | 傳回 MySQL 記錄檔清單。 |
> | Microsoft.dbformysql/servers/privateEndpointConnectionProxies/validate/action | 驗證私人端點連接從 NRP 端建立呼叫 |
> | Microsoft.dbformysql/servers/privateEndpointConnectionProxies/read | 傳回私人端點連線 proxy 的清單，或取得指定私人端點連接 proxy 的屬性。 |
> | Microsoft.dbformysql/servers/privateEndpointConnectionProxies/write | 使用指定的參數建立私人端點連線 proxy，或更新指定私人端點連接 proxy 的屬性或標記。 |
> | Microsoft.dbformysql/servers/privateEndpointConnectionProxies/delete | 刪除現有的私人端點連線 proxy |
> | Microsoft.dbformysql/servers/privateEndpointConnections/read | 傳回私人端點連接的清單，或取得指定私人端點連接的屬性。 |
> | Microsoft.dbformysql/servers/privateEndpointConnections/delete | 刪除現有的私人端點連接 |
> | Microsoft.dbformysql/servers/privateEndpointConnections/write | 核准或拒絕現有的私人端點連線 |
> | Microsoft.dbformysql/servers/privateLinkResources/read | 取得對應 MySQL 伺服器的私人連結資源 |
> | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | 取得 MySQL 伺服器的可用記錄 |
> | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> | Microsoft.DBforMySQL/servers/recoverableServers/read | 傳回可復原的 MySQL 伺服器資訊 |
> | Microsoft.dbformysql/servers/複本/read | 取得 MySQL 伺服器的讀取複本 |
> | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | 擷取給定伺服器上所設定的伺服器威脅偵測原則詳細資料 |
> | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | 變更指定伺服器的伺服器威脅偵測原則 |
> | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | 取得針對指定伺服器所設定的伺服器威脅偵測原則清單 |
> | Microsoft.dbformysql/servers/topQueryStatistics/read | 傳回最高排名查詢的查詢統計資料清單。 |
> | Microsoft.dbformysql/servers/topQueryStatistics/read | 傳回查詢統計資料 |
> | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | 傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。 |
> | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | 使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。 |
> | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | 刪除現有虛擬網路規則 |
> | Microsoft.dbformysql/servers/waitStatistics/read | 傳回執行個體的等候統計資料 |
> | Microsoft.dbformysql/servers/waitStatistics/read | 傳回等候統計資料 |

### <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

Azure 服務： [適用於 PostgreSQL 的 Azure 資料庫](../postgresql/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | DBforPostgreSQL/privateEndpointConnectionsApproval/action | 決定是否允許使用者核准私人端點連接 |
> | DBforPostgreSQL/註冊/動作 | 註冊于 postgresql 資源提供者 |
> | DBforPostgreSQL/checkNameAvailability/action | 確認指定的伺服器名稱是否可用來針對指定的訂用帳戶進行全球佈建。 |
> | DBforPostgreSQL/flexibleServers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | DBforPostgreSQL/flexibleServers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | DBforPostgreSQL/flexibleServers/delete | 刪除現有伺服器。 |
> | DBforPostgreSQL/flexibleServers/providers/Microsoft. Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | DBforPostgreSQL/flexibleServers/providers/Microsoft. Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | DBforPostgreSQL/flexibleServers/providers/Microsoft. Insights/logDefinitions/read | 取得于 postgresql 伺服器的可用記錄 |
> | DBforPostgreSQL/flexibleServers/providers/Microsoft. Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> | DBforPostgreSQL/位置/administratorAzureAsyncOperation/讀取 | 取得于 postgresql 伺服器系統管理員的進行中作業 |
> | DBforPostgreSQL/位置/administratorOperationResults/讀取 | 傳回于 postgresql 伺服器系統管理員操作結果 |
> | DBforPostgreSQL/位置/azureAsyncOperation/讀取 | 傳回于 postgresql 伺服器操作結果 |
> | DBforPostgreSQL/位置/operationResults/讀取 | 傳回以 ResourceGroup 為基礎的于 postgresql 伺服器操作結果 |
> | DBforPostgreSQL/位置/operationResults/讀取 | 傳回于 postgresql 伺服器操作結果 |
> | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | 傳回可用的效能層級清單。 |
> | DBforPostgreSQL/位置/privateEndpointConnectionAzureAsyncOperation/讀取 | 取得私人端點連接作業的結果 |
> | DBforPostgreSQL/位置/privateEndpointConnectionOperationResults/讀取 | 取得私人端點連接作業的結果 |
> | DBforPostgreSQL/位置/privateEndpointConnectionProxyAzureAsyncOperation/讀取 | 取得私人端點連接 proxy 作業的結果 |
> | DBforPostgreSQL/位置/privateEndpointConnectionProxyOperationResults/讀取 | 取得私人端點連接 proxy 作業的結果 |
> | DBforPostgreSQL/位置/securityAlertPoliciesAzureAsyncOperation/讀取 | 傳回伺服器威脅偵測操作結果的清單。 |
> | DBforPostgreSQL/位置/securityAlertPoliciesOperationResults/讀取 | 傳回伺服器威脅偵測操作結果的清單。 |
> | DBforPostgreSQL/位置/serverKeyAzureAsyncOperation/讀取 | 取得資料加密伺服器金鑰的進行中作業 |
> | DBforPostgreSQL/位置/serverKeyOperationResults/讀取 | 取得資料加密伺服器金鑰的進行中作業 |
> | DBforPostgreSQL/operations/read | 傳回于 postgresql 作業的清單。 |
> | Microsoft.DBforPostgreSQL/performanceTiers/read | 傳回可用的效能層級清單。 |
> | Microsoft.DBforPostgreSQL/servers/queryTexts/action | 傳回查詢的文字 |
> | DBforPostgreSQL/servers/resetQueryPerformanceInsightData/action | 重設查詢效能深入解析資料 |
> | DBforPostgreSQL/servers/privateEndpointConnectionsApproval/action | 決定是否允許使用者核准私人端點連接 |
> | Microsoft.DBforPostgreSQL/servers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | Microsoft.DBforPostgreSQL/servers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | Microsoft.DBforPostgreSQL/servers/delete | 刪除現有伺服器。 |
> | DBforPostgreSQL/servers/restart/action | 重新開機特定的伺服器。 |
> | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | 更新指定伺服器的組態 |
> | DBforPostgreSQL/servers/administrator/read | 取得于 postgresql 伺服器系統管理員的清單。 |
> | DBforPostgreSQL/servers/administrator/write | 使用指定的參數建立或更新于 postgresql 伺服器系統管理員。 |
> | DBforPostgreSQL/servers/administrator/delete | 刪除于 postgresql 伺服器的現有系統管理員。 |
> | Microsoft.DBforPostgreSQL/servers/advisors/read | 傳回建議程式清單 |
> | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | 提出建議 |
> | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | 傳回建議的動作清單 |
> | Microsoft.DBforPostgreSQL/servers/configurations/read | 傳回伺服器的組態清單，或取得指定組態的屬性。 |
> | Microsoft.DBforPostgreSQL/servers/configurations/write | 更新指定組態的值 |
> | DBforPostgreSQL/servers/資料庫/讀取 | 傳回于 postgresql 資料庫的清單，或取得指定資料庫的屬性。 |
> | DBforPostgreSQL/servers/資料庫/寫入 | 使用指定的參數建立于 postgresql 資料庫，或更新指定之資料庫的屬性。 |
> | DBforPostgreSQL/servers/資料庫/刪除 | 刪除現有的于 postgresql 資料庫。 |
> | Microsoft.DBforPostgreSQL/servers/firewallRules/read | 傳回伺服器的防火牆規則清單，或取得指定防火牆規則的屬性。 |
> | Microsoft.DBforPostgreSQL/servers/firewallRules/write | 使用指定參數建立防火牆規則，或更新現有的規則。 |
> | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | 刪除現有防火牆規則。 |
> | DBforPostgreSQL/servers/keys/read | 傳回伺服器金鑰的清單，或取得指定伺服器金鑰的屬性。 |
> | DBforPostgreSQL/servers/keys/write | 使用指定參數建立金鑰，或更新指定伺服器金鑰的屬性或標記。 |
> | DBforPostgreSQL/servers/keys/delete | 刪除現有的伺服器金鑰。 |
> | DBforPostgreSQL/servers/記錄檔/讀取 | 傳回于 postgresql 記錄檔的清單。 |
> | DBforPostgreSQL/servers/privateEndpointConnectionProxies/validate/action | 驗證私人端點連接從 NRP 端建立呼叫 |
> | DBforPostgreSQL/servers/privateEndpointConnectionProxies/read | 傳回私人端點連線 proxy 的清單，或取得指定私人端點連接 proxy 的屬性。 |
> | DBforPostgreSQL/servers/privateEndpointConnectionProxies/write | 使用指定的參數建立私人端點連線 proxy，或更新指定私人端點連接 proxy 的屬性或標記。 |
> | DBforPostgreSQL/servers/privateEndpointConnectionProxies/delete | 刪除現有的私人端點連線 proxy |
> | DBforPostgreSQL/servers/privateEndpointConnections/read | 傳回私人端點連接的清單，或取得指定私人端點連接的屬性。 |
> | DBforPostgreSQL/servers/privateEndpointConnections/delete | 刪除現有的私人端點連接 |
> | DBforPostgreSQL/servers/privateEndpointConnections/write | 核准或拒絕現有的私人端點連線 |
> | DBforPostgreSQL/servers/privateLinkResources/read | 取得對應于 postgresql 伺服器的私人連結資源 |
> | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | 取得于 postgresql 伺服器的可用記錄 |
> | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> | DBforPostgreSQL/servers/queryTexts/read | 傳回查詢清單的文字 |
> | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | 傳回可復原的 PostgreSQL 伺服器資訊 |
> | DBforPostgreSQL/servers/複本/read | 取得于 postgresql 伺服器的讀取複本 |
> | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | 擷取給定伺服器上所設定的伺服器威脅偵測原則詳細資料 |
> | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | 變更指定伺服器的伺服器威脅偵測原則 |
> | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | 傳回最高排名查詢的查詢統計資料清單。 |
> | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | 傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。 |
> | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | 使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。 |
> | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | 刪除現有虛擬網路規則 |
> | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | 傳回執行個體的等候統計資料 |
> | DBforPostgreSQL/serversv2/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | DBforPostgreSQL/serversv2/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | DBforPostgreSQL/serversv2/delete | 刪除現有伺服器。 |
> | DBforPostgreSQL/serversv2/updateConfigurations/action | 更新指定伺服器的組態 |
> | DBforPostgreSQL/serversv2/設定/讀取 | 傳回伺服器的組態清單，或取得指定組態的屬性。 |
> | DBforPostgreSQL/serversv2/設定/寫入 | 更新指定組態的值 |
> | DBforPostgreSQL/serversv2/firewallRules/read | 傳回伺服器的防火牆規則清單，或取得指定防火牆規則的屬性。 |
> | DBforPostgreSQL/serversv2/firewallRules/write | 使用指定參數建立防火牆規則，或更新現有的規則。 |
> | DBforPostgreSQL/serversv2/firewallRules/delete | 刪除現有防火牆規則。 |
> | DBforPostgreSQL/serversv2/providers/Microsoft. Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | DBforPostgreSQL/serversv2/providers/Microsoft. Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | DBforPostgreSQL/serversv2/providers/Microsoft. Insights/logDefinitions/read | 取得于 postgresql 伺服器的可用記錄 |
> | DBforPostgreSQL/serversv2/providers/Microsoft. Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> | DBforPostgreSQL/singleservers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | DBforPostgreSQL/singleservers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | DBforPostgreSQL/singleservers/delete | 刪除現有伺服器。 |
> | DBforPostgreSQL/singleservers/providers/Microsoft. Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | DBforPostgreSQL/singleservers/providers/Microsoft. Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | DBforPostgreSQL/singleservers/providers/Microsoft. Insights/logDefinitions/read | 取得于 postgresql 伺服器的可用記錄 |
> | DBforPostgreSQL/singleservers/providers/Microsoft. Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |

### <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

Azure 服務： [Azure Cosmos DB](../cosmos-db/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.DocumentDB/register/action |  為訂用帳戶註冊 Microsoft DocumentDB 資源提供者 |
> | Microsoft.DocumentDB/databaseAccountNames/read | 檢查名稱可用性。 |
> | Microsoft.DocumentDB/databaseAccounts/read | 讀取資料庫帳戶。 |
> | Microsoft.DocumentDB/databaseAccounts/write | 更新資料庫帳戶。 |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/action | 列出資料庫帳戶的金鑰 |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | 讀取資料庫帳戶的唯讀金鑰。 |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | 輪替資料庫帳戶的金鑰 |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | 取得資料庫帳戶的連接字串 |
> | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | 變更資料庫帳戶的資源群組 |
> | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | 變更資料庫帳戶區域的容錯移轉優先順序。 這會用來執行手動容錯移轉作業 |
> | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | 讓資料庫帳戶的某個區域離線。  |
> | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | 讓資料庫帳戶的某個區域上線。 |
> | Microsoft.DocumentDB/databaseAccounts/delete | 刪除資料庫帳戶。 |
> | Microsoft.DocumentDB/databaseAccounts/getBackupPolicy/action | 取得資料庫帳戶的備份原則 |
> | Microsoft.DocumentDB/databaseAccounts/PrivateEndpointConnectionsApproval/action | 管理資料庫帳戶的私人端點連接 |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | 提交還原要求 |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | 提交要求以設定備份 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/寫入 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 建立資料庫。 僅適用于 API 類型： ' sql '、' mongodb '、' gremlin'。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/讀取 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取資料庫或列出所有資料庫。 僅適用于 API 類型： ' sql '、' mongodb '、' gremlin'。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/刪除 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 刪除資料庫。 僅適用于 API 類型： ' sql '、' mongodb '、' gremlin'。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/集合/寫入 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 建立或更新集合。 僅適用于 API 類型： ' mongodb '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/集合/讀取 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取集合或列出所有集合。 僅適用于 API 類型： ' mongodb '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/集合/刪除 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 刪除集合。 僅適用于 API 類型： ' mongodb '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/集合/operationResults/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取非同步作業的狀態。 僅適用于 API 類型： ' mongodb '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/集合/設定/寫入 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 更新集合輸送量。 僅適用于 API 類型： ' mongodb '。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/集合/設定/讀取 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取集合輸送量。 僅適用于 API 類型： ' mongodb '。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/集合/設定/operationResults/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取非同步作業的狀態。 僅適用于 API 類型： ' mongodb '。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/容器/寫入 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 建立或更新容器。 僅適用于 API 類型： ' sql '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/容器/讀取 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取容器或列出所有容器。 僅適用于 API 類型： ' sql '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/容器/刪除 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 刪除容器。 僅適用于 API 類型： ' sql '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/容器/operationResults/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取非同步作業的狀態。 僅適用于 API 類型： ' sql '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/容器/設定/寫入 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 更新容器輸送量。 僅適用于 API 類型： ' sql '。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/容器/設定/讀取 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取容器輸送量。 僅適用于 API 類型： ' sql '。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/容器/settings/operationResults/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取非同步作業的狀態。 僅適用于 API 類型： ' sql '。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/圖形/寫入 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 建立或更新圖形。 僅適用于 API 類型： ' gremlin'。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/圖表/讀取 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取圖形或列出所有圖形。 僅適用于 API 類型： ' gremlin'。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/圖形/刪除 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 刪除圖形。 僅適用于 API 類型： ' gremlin'。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/圖形/operationResults/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取非同步作業的狀態。 僅適用于 API 類型： ' gremlin'。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/圖形/設定/寫入 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 更新圖形輸送量。 僅適用于 API 類型： ' gremlin'。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/圖形/設定/讀取 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取圖形輸送量。 僅適用于 API 類型： ' gremlin'。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/圖形/設定/operationResults/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取非同步作業的狀態。 僅適用于 API 類型： ' gremlin'。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/operationResults/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取非同步作業的狀態。 僅適用于 API 類型： ' sql '、' mongodb '、' gremlin'。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/設定/寫入 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 更新資料庫輸送量。 僅適用于 API 類型： ' sql '、' mongodb '、' gremlin'。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/設定/讀取 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取資料庫輸送量。 僅適用于 API 類型： ' sql '、' mongodb '、' gremlin'。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料庫/設定/operationResults/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取非同步作業的狀態。 僅適用于 API 類型： ' sql '、' mongodb '、' gremlin'。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/keyspace/write | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 建立 keyspace。 僅適用于 API 類型： ' cassandra '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/keyspace/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取 keyspace 或列出所有 keyspace。 僅適用于 API 類型： ' cassandra '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/keyspace/delete | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 刪除 keyspace。 僅適用于 API 類型： ' cassandra '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/keyspace/operationResults/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取非同步作業的狀態。 僅適用于 API 類型： ' cassandra '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/keyspace/settings/write | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 更新 keyspace 的輸送量。 僅適用于 API 類型： ' cassandra '。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/keyspace/settings/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取 keyspace 的輸送量。 僅適用于 API 類型： ' cassandra '。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/keyspace/settings/operationResults/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取非同步作業的狀態。 僅適用于 API 類型： ' cassandra '。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/keyspace/資料表/寫入 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 建立或更新資料表。 僅適用于 API 類型： ' cassandra '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/keyspace/tables/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取資料表或列出所有資料表。 僅適用于 API 類型： ' cassandra '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/keyspace/資料表/刪除 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 刪除資料表。 僅適用于 API 類型： ' cassandra '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/keyspace/tables/operationResults/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取非同步作業的狀態。 僅適用于 API 類型： ' cassandra '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/keyspace/tables/settings/write | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 更新資料表輸送量。 僅適用于 API 類型： ' cassandra '。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/keyspace/tables/settings/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取資料表輸送量。 僅適用于 API 類型： ' cassandra '。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/keyspace/tables/settings/operationResults/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取非同步作業的狀態。 僅適用于 API 類型： ' cassandra '。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料表/寫入 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 建立或更新資料表。 僅適用于 API 類型： ' table '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/tables/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取資料表或列出所有資料表。 僅適用于 API 類型： ' table '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/資料表/刪除 | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 刪除資料表。 僅適用于 API 類型： ' table '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/tables/operationResults/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取非同步作業的狀態。 僅適用于 API 類型： ' table '。 |
> | Microsoft.DocumentDB/databaseAccounts/api/tables/settings/write | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 更新資料表輸送量。 僅適用于 API 類型： ' table '。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/tables/settings/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取資料表輸送量。 僅適用于 API 類型： ' table '。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/api/tables/settings/operationResults/read | (已淘汰。 請使用不含 '/apis/' 區段的資源路徑) 讀取非同步作業的狀態。 僅適用于 API 類型： ' table '。 僅適用于設定類型：「輸送量」。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/write | 建立 Cassandra keyspace。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/read | 閱讀 Cassandra keyspace 或列出所有的 Cassandra keyspace。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/delete | 刪除 Cassandra keyspace。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/write | 建立或更新 Cassandra 資料表。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/read | 讀取 Cassandra 資料表或列出所有 Cassandra 資料表。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/delete | 刪除 Cassandra 資料表。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/write | 更新 Cassandra 資料表的輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/read | 讀取 Cassandra 資料表的輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/migrateToAutoscale/action | 將 Cassandra 資料表供應專案遷移至自動調整。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/migrateToManualThroughput/action | 將 Cassandra 資料表供應專案遷移至手動輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/migrateToAutoscale/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/migrateToManualThroughput/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/write | 更新 Cassandra keyspace 輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/read | 閱讀 Cassandra keyspace 的輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/migrateToAutoscale/action | 將 Cassandra keyspace 供應專案遷移至自動調整。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/migrateToManualThroughput/action | 將 Cassandra keyspace 供應專案遷移至手動輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/migrateToAutoscale/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/migrateToManualThroughput/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | 讀取集合計量定義。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | 讀取集合計量。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | 讀取資料庫帳戶分割區索引鍵層級計量 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | 讀取集合中的資料庫帳戶分割區 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | 讀取資料庫帳戶分割區層級計量 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | 讀取資料庫帳戶分割區層級使用量 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | 讀取集合使用量。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | 讀取資料庫計量定義 |
> | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | 讀取資料庫計量。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | 讀取資料庫使用量。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/write | 建立 Gremlin 資料庫。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/read | 讀取 Gremlin 資料庫或列出所有 Gremlin 資料庫。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/delete | 刪除 Gremlin 資料庫。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/圖形/寫入 | 建立或更新 Gremlin 圖形。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/圖形/讀取 | 讀取 Gremlin 圖或列出所有 Gremlin 圖形。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/圖形/刪除 | 刪除 Gremlin 圖形。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/圖形/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/圖形/throughputSettings/write | 更新 Gremlin 圖輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/圖形/throughputSettings/read | 讀取 Gremlin 圖輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/圖形/throughputSettings/migrateToAutoscale/action | 將 Gremlin graph 供應專案遷移至自動調整。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/圖形/throughputSettings/migrateToManualThroughput/action | 將 Gremlin graph 供應專案遷移至手動輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/圖形/throughputSettings/migrateToAutoscale/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/圖形/throughputSettings/migrateToManualThroughput/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/圖形/throughputSettings/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/write | 更新 Gremlin 資料庫輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/read | 讀取 Gremlin 資料庫輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/migrateToAutoscale/action | 將 Gremlin 資料庫供應專案遷移至自動調整。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/migrateToManualThroughput/action | 將 Gremlin 資料庫供應專案遷移至手動輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/migrateToAutoscale/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/migrateToManualThroughput/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | 讀取資料庫帳戶的計量定義。 |
> | Microsoft.DocumentDB/databaseAccounts/metrics/read | 讀取資料庫帳戶的計量。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/write | 建立 MongoDB 資料庫。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/read | 讀取 MongoDB 資料庫或列出所有的 MongoDB 資料庫。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/delete | 刪除 MongoDB 資料庫。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collection/write | 建立或更新 MongoDB 集合。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collection/read | 讀取 MongoDB 集合或列出所有 MongoDB 集合。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collection/delete | 刪除 MongoDB 集合。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collection/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collection/throughputSettings/write | 更新 MongoDB 集合輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collection/throughputSettings/read | 讀取 MongoDB 集合輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collection/throughputSettings/migrateToAutoscale/action | 將 MongoDB 集合供應專案遷移至自動調整。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collection/throughputSettings/migrateToManualThroughput/action | 將 MongoDB 收集供應專案遷移至手動輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collection/throughputSettings/migrateToAutoscale/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collection/throughputSettings/migrateToManualThroughput/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collection/throughputSettings/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/write | 更新 MongoDB 資料庫的輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/read | 讀取 MongoDB 資料庫的輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/migrateToAutoscale/action | 將 MongoDB 資料庫供應專案遷移至自動調整。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/migrateToManualThroughput/action | 將 MongoDB 資料庫供應專案遷移至手動輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/migrateToAutoscale/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/migrateToManualThroughput/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/write | 建立或更新筆記本工作區 |
> | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/read | 讀取筆記本工作區 |
> | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/delete | 刪除筆記本工作區 |
> | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/listConnectionInfo/action | 列出筆記本工作區的連接資訊 |
> | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/operationResults/read | 讀取筆記本工作區上非同步作業的狀態 |
> | Microsoft.DocumentDB/databaseAccounts/operationResults/read | 讀取非同步作業的狀態 |
> | Microsoft.DocumentDB/databaseAccounts/percentile/read | 讀取複寫延遲的百分位數 |
> | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | 讀取延遲計量 |
> | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | 讀取指定來源和目標區域的延遲計量 |
> | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | 讀取指定目標區域的延遲計量 |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | 讀取資料庫帳戶的私人端點連接 proxy |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | 建立或更新資料庫帳戶的私人端點連接 proxy |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | 刪除資料庫帳戶的私人端點連接 proxy |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | 驗證資料庫帳戶的私人端點連接 proxy |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/operationResults/read | 讀取私人端點連線 proxy 非同步作業的狀態 |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnections/read | 讀取私人端點連接，或列出資料庫帳戶的所有私人端點連接 |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnections/write | 建立或更新資料庫帳戶的私人端點連接 |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnections/delete | 刪除資料庫帳戶的私人端點連接 |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnections/operationResults/read | 讀取 privateEndpointConnenctions 非同步作業的狀態 |
> | Microsoft.DocumentDB/databaseAccounts/privateLinkResources/read | 讀取私人連結資源或列出資料庫帳戶的所有私用連結資源 |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | 讀取資料庫帳戶的唯讀金鑰。 |
> | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | 讀取區域集合計量。 |
> | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | 讀取區域資料庫帳戶分割區索引鍵層級計量 |
> | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | 讀取集合中的區域資料庫帳戶分割區 |
> | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | 讀取區域資料庫帳戶分割區層級計量 |
> | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | 讀取區域和資料庫帳戶計量。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/write | 建立 SQL 資料庫。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/read | 讀取 SQL 資料庫或列出所有 SQL 資料庫。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/delete | 刪除 SQL 資料庫。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/write | 建立或更新 SQL 容器。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/read | 讀取 SQL 容器或列出所有 SQL 容器。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/容器/刪除 | 刪除 SQL 容器。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/storedProcedures/write | 建立或更新 SQL 預存程式。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/storedProcedures/read | 讀取 SQL 預存程式或列出所有 SQL 預存程式。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/storedProcedures/delete | 刪除 SQL 預存程式。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/storedProcedures/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/throughputSettings/write | 更新 SQL 容器的輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/throughputSettings/read | 讀取 SQL 容器的輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/throughputSettings/migrateToAutoscale/action | 將 SQL 容器供應專案遷移至自動調整。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/throughputSettings/migrateToManualThroughput/action | 將 SQL database 輸送量供應專案遷移至手動輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/throughputSettings/migrateToAutoscale/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/throughputSettings/migrateToManualThroughput/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/throughputSettings/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/容器/觸發程式/寫入 | 建立或更新 SQL 觸發程式。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/容器/觸發程式/讀取 | 讀取 SQL 觸發程式或列出所有 SQL 觸發程式。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/容器/觸發程式/刪除 | 刪除 SQL 觸發程式。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/trigger/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/>src\actions\userdefinedfunctions\availability.js/write | 建立或更新 SQL 使用者定義函數。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/>src\actions\userdefinedfunctions\availability.js/read | 讀取 SQL 使用者定義函數，或列出所有 SQL 使用者定義函數。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/>src\actions\userdefinedfunctions\availability.js/delete | 刪除 SQL 使用者定義函數。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/container/>src\actions\userdefinedfunctions\availability.js/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/write | 更新 SQL 資料庫的輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/read | 讀取 SQL 資料庫的輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/migrateToAutoscale/action | 將 SQL database 供應專案遷移至自動調整。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/migrateToManualThroughput/action | 將 SQL database 輸送量供應專案遷移至手動輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/migrateToAutoscale/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/migrateToManualThroughput/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/write | 建立或更新資料表。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/read | 讀取資料表或列出所有資料表。 |
> | Microsoft.DocumentDB/databaseAccounts/資料表/刪除 | 刪除資料表。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/write | 更新資料表輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/read | 讀取資料表輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/migrateToAutoscale/action | 將資料表供應專案遷移至自動調整。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/migrateToManualThroughput/action | 將資料表供應專案遷移至手動輸送量。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/migrateToAutoscale/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/migrateToManualThroughput/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/operationResults/read | 讀取非同步作業的狀態。 |
> | Microsoft.DocumentDB/databaseAccounts/usages/read | 讀取資料庫帳戶的使用量。 |
> | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | 通知 Microsoft.DocumentDB 正在刪除虛擬網路或子網路 |
> | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | 讀取 deleteVirtualNetworkOrSubnets 非同步作業的狀態 |
> | Microsoft.DocumentDB/位置/operationsStatus/讀取 | 讀取非同步作業的狀態 |
> | Microsoft.DocumentDB/位置/restorableDatabaseAccounts/讀取 | 讀取可還原的資料庫帳戶，或列出所有可還原的資料庫帳戶 |
> | Microsoft.DocumentDB/位置/restorableDatabaseAccounts/還原/動作 | 提交還原要求 |
> | Microsoft.DocumentDB/operationResults/read | 讀取非同步作業的狀態 |
> | Microsoft.DocumentDB/operations/read | 讀取可供 Microsoft DocumentDB 使用的作業  |

### <a name="microsoftsql"></a>Microsoft.Sql

Azure 服務： [Azure SQL Database](../azure-sql/database/index.yml)、 [azure SQL 受控執行個體](../azure-sql/managed-instance/index.yml) [Azure Synapse Analytics (先前的 SQL 資料倉儲) ](../sql-data-warehouse/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Sql/checkNameAvailability/action | 確認指定的伺服器名稱是否可用來針對指定的訂用帳戶進行全球佈建。 |
> | Microsoft.Sql/register/action | 為 Microsoft SQL Database 資源提供者註冊訂用帳戶，並讓您能夠建立 Microsoft SQL Database。 |
> | Microsoft.Sql/unregister/action | 為 Microsoft SQL Database 資源提供者取消註冊訂用帳戶，並讓您能夠建立 Microsoft SQL Database。 |
> | Microsoft .Sql/privateEndpointConnectionsApproval/action | 決定是否允許使用者核准私人端點連接 |
> | Microsoft.Sql/instancePools/read | 取得執行個體集區 |
> | Microsoft.Sql/instancePools/write | 建立或更新執行個體集區 |
> | Microsoft.Sql/instancePools/delete | 刪除執行個體集區 |
> | Microsoft .Sql/instancePools/使用方式/讀取 | 取得實例集區的使用量資訊 |
> | Microsoft.Sql/locations/read | 取得指定訂用帳戶的可用位置 |
> | Microsoft .Sql/位置/administratorAzureAsyncOperation/讀取 | 取得受控實例的 azure async 系統管理員作業結果。 |
> | Microsoft .Sql/位置/administratorOperationResults/讀取 | 取得受控實例系統管理員作業結果。 |
> | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | 擷取擴充伺服器的 Blob 稽核原則設定作業結果 |
> | Microsoft.Sql/locations/auditingSettingsOperationResults/read | 擷取伺服器的「Blob 稽核原則設定」作業結果 |
> | Microsoft.Sql/locations/capabilities/read | 在指定位置取得此訂用帳戶的功能 |
> | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | 取得資料庫作業的狀態。 |
> | Microsoft.Sql/locations/databaseOperationResults/read | 取得資料庫作業的狀態。 |
> | Microsoft.Sql/locations/deletedServerAsyncOperation/read | 取得已刪除伺服器上進行中的作業 |
> | Microsoft.Sql/locations/deletedServerOperationResults/read | 取得已刪除伺服器上進行中的作業 |
> | Microsoft.Sql/locations/deletedServers/read | 傳回已刪除伺服器的清單，或取得指定已刪除伺服器的屬性。 |
> | Microsoft.Sql/locations/deletedServers/recover/action | 復原已刪除的伺服器 |
> | Microsoft .Sql/位置/devOpsAuditingSettingsAzureAsyncOperation/讀取 | 取得伺服器 DevOps 稽核原則設定作業的結果 |
> | Microsoft .Sql/位置/devOpsAuditingSettingsOperationResults/讀取 | 取得伺服器 DevOps 稽核原則設定作業的結果 |
> | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | 取得彈性集區非同步作業的 Aure 非同步作業 |
> | Microsoft.Sql/locations/elasticPoolOperationResults/read | 取得彈性集區作業的結果。 |
> | Microsoft .Sql/位置/encryptionProtectorAzureAsyncOperation/讀取 | 取得透明資料加密加密保護裝置上的進行中作業 |
> | Microsoft .Sql/位置/encryptionProtectorOperationResults/讀取 | 取得透明資料加密加密保護裝置上的進行中作業 |
> | Microsoft .Sql/位置/exportManagedDatabaseOperationResults/讀取 | 取得進行中的受控資料庫匯出作業 |
> | Microsoft .Sql/位置/exportManagedInstanceOperationResults/讀取 | 取得進行中的匯出作業 |
> | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | 擷取擴充伺服器的 Blob 稽核原則設定作業結果 |
> | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | 擷取擴充伺服器的 Blob 稽核原則設定作業結果 |
> | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | 取得防火牆規則作業的狀態。 |
> | Microsoft.Sql/locations/firewallRulesOperationResults/read | 取得防火牆規則作業的狀態。 |
> | Microsoft .Sql/位置/hybridLinkAzureAsyncOperation/讀取 | 取得 Azure Sql 受控執行個體上長期混合連結非同步作業的狀態。 |
> | Microsoft .Sql/位置/hybridLinkOperationResults/讀取 | 取得長期混合式連結非同步作業的狀態。 |
> | Microsoft.Sql/locations/instanceFailoverGroups/read | 傳回執行個體容錯移轉群組的清單，或取得指定執行個體容錯移轉群組的屬性。 |
> | Microsoft.Sql/locations/instanceFailoverGroups/write | 使用指定的參數建立實例容錯移轉群組，或更新指定實例容錯移轉群組的屬性或標記。 |
> | Microsoft.Sql/locations/instanceFailoverGroups/delete | 刪除現有的執行個體容錯移轉群組。 |
> | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | 在現有的執行個體容錯移轉群組中執行規劃的容錯移轉。 |
> | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | 在現有的執行個體容錯移轉群組中執行強制容錯移轉。 |
> | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | 取得實例集區作業的狀態 |
> | Microsoft.Sql/locations/instancePoolOperationResults/read | 取得實例集區作業的結果 |
> | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | 傳回特定介面端點 Azure 非同步作業的詳細資料 |
> | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | 傳回特定介面端點 設定檔作業的詳細資料 |
> | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | 取得工作代理程式作業的狀態。 |
> | Microsoft.Sql/locations/jobAgentOperationResults/read | 取得工作代理程式作業的結果。 |
> | Microsoft.Sql/locations/longTermRetentionBackups/read | 列出位置中每個伺服器上每個資料庫的長期保留備份 |
> | Microsoft .Sql/位置/longTermRetentionManagedInstanceBackups/讀取 | 傳回特定位置的受控實例 LTR 備份清單  |
> | Microsoft .Sql/位置/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read | 傳回受控實例資料庫的 LTR 備份清單 |
> | Microsoft .Sql/位置/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete | 刪除受控實例資料庫的 LTR 備份 |
> | Microsoft .Sql/位置/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read | 傳回特定受控實例的受控實例 LTR 備份清單 |
> | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | 列出伺服器上每個資料庫的長期保留備份 |
> | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | 列出資料庫的長期保留備份 |
> | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | 刪除長期保留備份 |
> | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | 完成受控資料庫還原作業 |
> | Microsoft .Sql/位置/managedInstanceEncryptionProtectorAzureAsyncOperation/讀取 | 取得透明資料加密受控實例加密保護裝置上的進行中作業 |
> | Microsoft .Sql/位置/managedInstanceEncryptionProtectorOperationResults/讀取 | 取得透明資料加密受控實例加密保護裝置上的進行中作業 |
> | Microsoft .Sql/位置/managedInstanceKeyAzureAsyncOperation/讀取 | 取得透明資料加密受控實例金鑰的進行中作業 |
> | Microsoft .Sql/位置/managedInstanceKeyOperationResults/讀取 | 取得透明資料加密受控實例金鑰的進行中作業 |
> | Microsoft .Sql/位置/managedInstanceLongTermRetentionPolicyAzureAsyncOperation/讀取 | 取得受控資料庫的長期保留原則操作的狀態 |
> | Microsoft .Sql/位置/managedInstanceLongTermRetentionPolicyOperationResults/讀取 | 取得受控資料庫的長期保留原則操作的狀態 |
> | Microsoft .Sql/位置/managedInstancePrivateEndpointConnectionAzureAsyncOperation/讀取 | 取得私人端點連接作業的結果 |
> | Microsoft .Sql/位置/managedInstancePrivateEndpointConnectionOperationResults/讀取 | 取得私人端點連接作業的結果 |
> | Microsoft .Sql/位置/managedInstancePrivateEndpointConnectionProxyAzureAsyncOperation/讀取 | 取得私人端點連接 proxy 作業的結果 |
> | Microsoft .Sql/位置/managedInstancePrivateEndpointConnectionProxyOperationResults/讀取 | 取得私人端點連接 proxy 作業的結果 |
> | Microsoft .Sql/位置/managedShortTermRetentionPolicyOperationResults/讀取 | 取得短期保留原則作業的狀態 |
> | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | 取得有關受控資料庫之透明資料加密的進行中作業 |
> | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | 取得有關受控資料庫之透明資料加密的進行中作業 |
> | Microsoft .Sql/位置/operationsHealth/讀取 | 取得位置中服務作業的健全狀況狀態 |
> | Microsoft .Sql/位置/privateEndpointConnectionAzureAsyncOperation/讀取 | 取得私人端點連接作業的結果 |
> | Microsoft .Sql/位置/privateEndpointConnectionOperationResults/讀取 | 取得私人端點連接作業的結果 |
> | Microsoft .Sql/位置/privateEndpointConnectionProxyAzureAsyncOperation/讀取 | 取得私人端點連接 proxy 作業的結果 |
> | Microsoft .Sql/位置/privateEndpointConnectionProxyOperationResults/讀取 | 取得私人端點連接 proxy 作業的結果 |
> | Microsoft .Sql/位置/serverAdministratorAzureAsyncOperation/讀取 | 伺服器 Azure Active Directory 系統管理員非同步作業結果 |
> | Microsoft .Sql/位置/serverAdministratorOperationResults/讀取 | 伺服器 Azure Active Directory 系統管理員操作結果 |
> | Microsoft .Sql/位置/serverKeyAzureAsyncOperation/讀取 | 取得透明資料加密伺服器金鑰的進行中作業 |
> | Microsoft .Sql/位置/serverKeyOperationResults/讀取 | 取得透明資料加密伺服器金鑰的進行中作業 |
> | Microsoft .Sql/位置/serverTrustGroups/write | 使用指定的參數建立伺服器信任群組 |
> | Microsoft .Sql/位置/serverTrustGroups/刪除 | 刪除現有的 SQL Server Trust 群組 |
> | Microsoft .Sql/位置/serverTrustGroups/讀取 | 傳回現有的 SQL Server 信任群組 |
> | Microsoft .Sql/位置/shortTermRetentionPolicyOperationResults/讀取 | 取得短期保留原則作業的狀態 |
> | Microsoft.Sql/locations/syncAgentOperationResults/read | 擷取同步代理程式資源作業的結果 |
> | Microsoft.Sql/locations/syncDatabaseIds/read | 擷取特定區域和訂用帳戶的同步資料庫識別碼 |
> | Microsoft.Sql/locations/syncGroupOperationResults/read | 擷取同步群組資源作業的結果 |
> | Microsoft.Sql/locations/syncMemberOperationResults/read | 擷取同步成員資源作業的結果 |
> | Microsoft .Sql/位置/時區/讀取 | 依位置傳回受控實例時區的清單。 |
> | Microsoft .Sql/位置/transparentDataEncryptionAzureAsyncOperation/讀取 | 取得邏輯資料庫透明資料加密的進行中作業 |
> | Microsoft .Sql/位置/transparentDataEncryptionOperationResults/讀取 | 取得邏輯資料庫透明資料加密的進行中作業 |
> | Microsoft.Sql/locations/usages/read | 在位置中取得此訂用帳戶的使用計量集合 |
> | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | 傳回指定虛擬網路規則 Aure 非同步作業的詳細資料  |
> | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | 傳回指定虛擬網路規則作業的詳細資料  |
> | Microsoft.Sql/managedInstances/tdeCertificates/action | 建立/更新 TDE 憑證 |
> | Microsoft .Sql/managedInstances/joinServerTrustGroup/action | 判斷是否允許使用者加入伺服器信任群組 |
> | Microsoft .Sql/managedInstances/export/action | 匯出系統資料庫和 SMK 金鑰 |
> | Microsoft.Sql/managedInstances/read | 傳回受控執行個體的清單，或取得指定受控執行個體的屬性。 |
> | Microsoft.Sql/managedInstances/write | 使用指定參數建立受控執行個體，或更新指定受控執行個體的屬性或標記。 |
> | Microsoft.Sql/managedInstances/delete | 刪除現有的受控執行個體。 |
> | Microsoft .Sql/managedInstances/容錯移轉/動作 | 客戶起始的受控實例容錯移轉。 |
> | Microsoft.Sql/managedInstances/administrators/read | 取得受控執行個體系統管理員的清單。 |
> | Microsoft.Sql/managedInstances/administrators/write | 使用指定參數，建立或更新受控執行個體的系統管理員。 |
> | Microsoft.Sql/managedInstances/administrators/delete | 刪除受控執行個體的現有系統管理員。 |
> | Microsoft .Sql/managedInstances/azureADOnlyAuthentications/read | 只 Azure Active Directory authentication 物件讀取特定的受管理伺服器 |
> | Microsoft .Sql/managedInstances/azureADOnlyAuthentications/write | 新增或更新特定的受管理伺服器 Azure Active Directory 僅限驗證物件 |
> | Microsoft .Sql/managedInstances/azureADOnlyAuthentications/delete | 只 Azure Active Directory authentication 物件刪除特定的受管理伺服器 |
> | Microsoft.Sql/managedInstances/databases/read | 取得現有的受控資料庫 |
> | Microsoft.Sql/managedInstances/databases/delete | 刪除現有的受控資料庫 |
> | Microsoft.Sql/managedInstances/databases/write | 建立新的資料庫或更新現有資料庫。 |
> | Microsoft .Sql/managedInstances/資料庫/匯出/動作 | 匯出使用者資料庫。 |
> | Microsoft .Sql/managedInstances/資料庫/completeRestore/action | 完成受控資料庫還原作業 |
> | Microsoft .Sql/managedInstances/資料庫/backupLongTermRetentionPolicies/write | 更新受控資料庫的長期保留原則 |
> | Microsoft .Sql/managedInstances/database/backupLongTermRetentionPolicies/read | 取得受控資料庫的長期保留原則 |
> | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | 取得受控資料庫中的短期保留原則 |
> | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | 更新受控資料庫中的短期保留原則 |
> | Microsoft .Sql/managedInstances/資料庫/資料行/讀取 | 傳回受管理資料庫的資料行清單 |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | Microsoft .Sql/managedInstances/資料庫/currentSensitivityLabels/write | 批次更新敏感度標籤 |
> | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | 取得受控執行個體資料庫的可用記錄 |
> | Microsoft .Sql/managedInstances/資料庫/查詢/讀取 | 依查詢識別碼取得查詢文字 |
> | Microsoft .Sql/managedInstances/資料庫/查詢/統計資料/讀取 | 依查詢識別碼取得查詢執行統計資料 |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | Microsoft .Sql/managedInstances/資料庫/recommendedSensitivityLabels/write | 批次更新建議的敏感度標籤 |
> | Microsoft .Sql/managedInstances/database/restoreDetails/read | 在還原進行時傳回受管理的資料庫還原詳細資料。 |
> | Microsoft .Sql/managedInstances/資料庫/架構/讀取 | 取得受控資料庫架構。 |
> | Microsoft .Sql/managedInstances/資料庫/架構/資料表/讀取 | 取得受控資料庫資料表 |
> | Microsoft .Sql/managedInstances/資料庫/架構/資料表/資料行/讀取 | 取得受控資料庫資料行 |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | 取得指定資料行的敏感度標籤 |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | 建立或更新指定資料行的敏感度標籤 |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | 刪除指定資料行的敏感度標籤 |
> | Microsoft .Sql/managedInstances/資料庫/架構/資料表/資料行/sensitivityLabels/停用/動作 | 停用指定資料行的敏感度建議 |
> | Microsoft .Sql/managedInstances/資料庫/架構/資料表/資料行/sensitivityLabels/啟用/動作 | 針對指定的資料行啟用敏感度建議 |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | 變更指定受控資料庫的資料庫威脅偵測原則 |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | 取出針對指定伺服器所設定的受控資料庫威脅偵測原則清單 |
> | Microsoft.Sql/managedInstances/databases/securityEvents/read | 擷取受控資料庫的安全性事件 |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | 擷取指定受控資料庫上資料庫透明資料加密的詳細資料 |
> | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | 變更指定受控資料庫的資料庫透明資料加密 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | 變更指定資料庫的弱點評量 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | 移除指定資料庫的弱點評量 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | 擷取指定資料庫上的弱點評量原則 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | 移除指定資料庫的弱點評量規則基準 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | 變更指定資料庫的弱點評量規則基準 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | 取得指定資料庫的弱點評量規則基準 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | 執行弱點評估資料庫掃描。 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | 將現有的掃描結果轉換成人類可讀取的格式。 如果已存在，則不會執行任何動作 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | 傳回資料庫弱點評量掃描記錄的清單，或取得指定掃描識別碼的掃描記錄。 |
> | Microsoft .Sql/managedInstances/encryptionProtector/重新驗證/動作 | 更新指定伺服器加密保護裝置的屬性。 |
> | Microsoft.Sql/managedInstances/encryptionProtector/read | 傳回伺服器加密保護裝置的清單，或取得指定伺服器加密保護裝置的屬性。 |
> | Microsoft.Sql/managedInstances/encryptionProtector/write | 更新指定伺服器加密保護裝置的屬性。 |
> | Microsoft .Sql/managedInstances/hybridLink/read | 傳回混合連結的清單，或取得指定之分散式可用性群組的屬性。 |
> | Microsoft .Sql/managedInstances/hybridLink/write | 使用指定的參數建立或更新混合式連結。 |
> | Microsoft .Sql/managedInstances/hybridLink/delete | 刪除具有指定之分散式可用性群組的混合式連結。 |
> | Microsoft .Sql/managedInstances/inaccessibleManagedDatabases/read | 取得受控實例中無法存取的受控資料庫清單 |
> | Microsoft.Sql/managedInstances/keys/read | 傳回受控執行個體金鑰的清單，或取得指定受控執行個體金鑰的屬性。 |
> | Microsoft.Sql/managedInstances/keys/write | 使用指定參數建立金鑰，或更新指定受控執行個體金鑰的屬性或標記。 |
> | Microsoft.Sql/managedInstances/keys/delete | 刪除現有的 Azure SQL 受控執行個體金鑰。 |
> | Microsoft.Sql/managedInstances/metricDefinitions/read | 取得受控執行個體的計量定義 |
> | Microsoft.Sql/managedInstances/metrics/read | 取得受控執行個體計量 |
> | Microsoft .Sql/managedInstances/operations/read | 取得受控實例作業 |
> | Microsoft .Sql/managedInstances/operations/cancel/action | 取消尚未完成的 Azure SQL 受控執行個體暫止的非同步作業。 |
> | Microsoft .Sql/managedInstances/privateEndpointConnectionProxies/read | 傳回私人端點連線 proxy 的清單，或取得指定私人端點連接 proxy 的屬性。 |
> | Microsoft .Sql/managedInstances/privateEndpointConnectionProxies/write | 使用指定的參數建立私人端點連線 proxy，或更新指定私人端點連接 proxy 的屬性或標記。 |
> | Microsoft .Sql/managedInstances/privateEndpointConnectionProxies/delete | 刪除現有的私人端點連線 proxy |
> | Microsoft .Sql/managedInstances/privateEndpointConnectionProxies/validate/action | 驗證私人端點連接從 NRP 端建立呼叫 |
> | Microsoft .Sql/managedInstances/privateEndpointConnections/read | 傳回私人端點連接的清單，或取得指定私人端點連接的屬性。 |
> | Microsoft .Sql/managedInstances/privateEndpointConnections/delete | 刪除現有的私人端點連接 |
> | Microsoft .Sql/managedInstances/privateEndpointConnections/write | 核准或拒絕現有的私人端點連線 |
> | Microsoft .Sql/managedInstances/privateLinkResources/read | 取得對應 sql server 的 private link 資源 |
> | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | 取得受控執行個體的可用記錄 |
> | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供受控執行個體使用的計量類型 |
> | Microsoft.Sql/managedInstances/recoverableDatabases/read | 傳回可還原的受控資料庫清單 |
> | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | 傳回可還原的已卸除受控資料庫清單。 |
> | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | 取得已卸除之受控資料庫中的短期保留原則 |
> | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | 更新已卸除之受控資料庫中的短期保留原則 |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/write | 變更指定受控伺服器的受控伺服器威脅偵測原則 |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/read | 取出針對指定伺服器所設定的受管理伺服器威脅偵測原則清單 |
> | Microsoft .Sql/managedInstances/serverTrustGroups/read | 依受控執行個體名稱傳回現有的 SQL Server Trust 群組 |
> | Microsoft .Sql/managedInstances/topqueries/read | 取得受控實例的最高資源取用查詢 |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | 變更指定受控執行個體的弱點評量 |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | 移除指定受控執行個體的弱點評量 |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | 擷取指定受控執行個體上的弱點評量原則 |
> | Microsoft.Sql/operations/read | 取得可用的 REST 作業 |
> | Microsoft.Sql/servers/tdeCertificates/action | 建立/更新 TDE 憑證 |
> | Microsoft.Sql/servers/import/action | 匯入新的 Azure SQL Database |
> | Microsoft.Sql/servers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | Microsoft.Sql/servers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | Microsoft.Sql/servers/delete | 刪除現有伺服器。 |
> | Microsoft .Sql/servers/privateEndpointConnectionsApproval/action | 決定是否允許使用者核准私人端點連接 |
> | Microsoft.Sql/servers/administratorOperationResults/read | 取得關於伺服器系統管理員的進行中作業 |
> | Microsoft.Sql/servers/administrators/read | 取得特定的 Azure Active Directory 系統管理員物件 |
> | Microsoft.Sql/servers/administrators/write | 新增或更新特定 Azure Active Directory 系統管理員物件 |
> | Microsoft.Sql/servers/administrators/delete | 刪除特定的 Azure Active Directory 系統管理員物件 |
> | Microsoft.Sql/servers/advisors/read | 傳回伺服器可用 Advisor 的清單 |
> | Microsoft.Sql/servers/advisors/write | 更新伺服器層級上 Advisor 的自動執行狀態。 |
> | Microsoft.Sql/servers/advisors/recommendedActions/read | 傳回伺服器之指定 Advisor 的建議動作清單 |
> | Microsoft.Sql/servers/advisors/recommendedActions/write | 對伺服器套用建議動作 |
> | Microsoft.Sql/servers/auditingSettings/read | 擷取給定伺服器上所設定之伺服器 Blob 稽核原則的詳細資料 |
> | Microsoft.Sql/servers/auditingSettings/write | 變更給定伺服器的伺服器 Blob 稽核 |
> | Microsoft.Sql/servers/auditingSettings/operationResults/read | 擷取伺服器的「Blob 稽核原則設定」作業結果 |
> | Microsoft.Sql/servers/automaticTuning/read | 傳回伺服器的自動調整設定 |
> | Microsoft.Sql/servers/automaticTuning/write | 更新伺服器的自動調整設定，並傳回更新的設定 |
> | Microsoft .Sql/servers/azureADOnlyAuthentications/read | 只 Azure Active Directory authentication 物件讀取特定伺服器 |
> | Microsoft .Sql/servers/azureADOnlyAuthentications/write | 將特定伺服器新增或更新 Azure Active Directory 只有驗證物件 |
> | Microsoft .Sql/servers/azureADOnlyAuthentications/delete | 只 Azure Active Directory authentication 物件刪除特定伺服器 |
> | Microsoft.Sql/servers/communicationLinks/read | 傳回指定伺服器的通訊連結清單。 |
> | Microsoft.Sql/servers/communicationLinks/write | 建立或更新伺服器通訊連結。 |
> | Microsoft.Sql/servers/communicationLinks/delete | 刪除現有的伺服器通訊連結。 |
> | Microsoft.Sql/servers/databases/read | 傳回資料庫清單，或取得指定資料庫的屬性。 |
> | Microsoft.Sql/servers/databases/write | 使用指定參數建立資料庫，或更新指定資料庫的屬性或標記。 |
> | Microsoft.Sql/servers/databases/delete | 刪除現有的資料庫。 |
> | Microsoft.Sql/servers/databases/pause/action | 暫停 Azure SQL 資料倉儲資料庫 |
> | Microsoft.Sql/servers/databases/resume/action | 繼續 Azure SQL 資料倉儲資料庫 |
> | Microsoft.Sql/servers/databases/export/action | 匯出 Azure SQL Database |
> | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | 將 Azure SQL 資料倉儲資料庫升級 |
> | Microsoft.Sql/servers/databases/move/action | 變更現有資料庫的名稱。 |
> | Microsoft.Sql/servers/databases/restorePoints/action | 建立新的還原點 |
> | Microsoft .Sql/servers/資料庫/匯入/動作 | 匯入 Azure SQL Database |
> | Microsoft .Sql/servers/資料庫/容錯移轉/動作 | 客戶起始的資料庫容錯移轉。 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | 執行弱點評估資料庫掃描。 |
> | Microsoft.Sql/servers/databases/advisors/read | 傳回資料庫可用 Advisor 的清單 |
> | Microsoft.Sql/servers/databases/advisors/write | 更新資料庫層級上 Advisor 的自動執行狀態。 |
> | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | 傳回資料庫之指定 Advisor 的建議動作清單 |
> | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | 對資料庫套用建議動作 |
> | Microsoft.Sql/servers/databases/auditingSettings/read | 擷取給定資料庫上所設定之 Blob 稽核原則的詳細資料 |
> | Microsoft.Sql/servers/databases/auditingSettings/write | 變更給定資料庫的 Blob 稽核原則 |
> | Microsoft.Sql/servers/databases/auditRecords/read | 擷取資料庫 Blob 稽核記錄 |
> | Microsoft.Sql/servers/databases/automaticTuning/read | 傳回資料庫的自動調整設定 |
> | Microsoft.Sql/servers/databases/automaticTuning/write | 更新資料庫的自動調整設定，並傳回更新的設定 |
> | Microsoft.Sql/servers/databases/azureAsyncOperation/read | 取得資料庫作業的狀態。 |
> | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | 建立或更新資料庫備份封存原則。 |
> | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | 傳回指定資料庫的備份封存原則清單。 |
> | Microsoft .Sql/servers/資料庫/backupShortTermRetentionPolicies/read | 取得資料庫的短期保留原則 |
> | Microsoft .Sql/servers/資料庫/backupShortTermRetentionPolicies/write | 更新資料庫的短期保留原則 |
> | Microsoft .Sql/servers/資料庫/資料行/讀取 | 傳回資料庫的資料行清單 |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | Microsoft .Sql/servers/資料庫/currentSensitivityLabels/write | 批次更新敏感度標籤 |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | 傳回資料庫資料遮罩原則的清單。 |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | 變更給定伺服器的資料遮罩原則 |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | 擷取給定資料庫上所設定之資料遮罩原則規則的詳細資料 |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | 變更給定伺服器的資料遮罩原則規則 |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | 刪除指定資料庫的資料遮罩原則規則 |
> | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | 傳回所選查詢識別碼的資料倉儲散發查詢資訊 |
> | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | 傳回所選步驟識別碼之資料倉儲查詢的分散式查詢步驟資訊 |
> | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | 擷取 SQL 資料倉儲執行個體的使用者活動，其中包含執行中和暫止的查詢 |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | 擷取指定資料庫上所設定之擴充 Blob 稽核原則的詳細資料 |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | 變更指定資料庫的擴充 Blob 稽核原則 |
> | Microsoft.Sql/servers/databases/extensions/read | 取得資料庫的擴充功能集合。 |
> | Microsoft.Sql/servers/databases/extensions/write | 變更指定資料庫的擴充功能 |
> | Microsoft .Sql/servers/資料庫/extensions/importExtensionOperationResults/read | 取得進行中的匯入作業 |
> | Microsoft.Sql/servers/databases/geoBackupPolicies/read | 擷取指定資料庫的異地備份原則 |
> | Microsoft.Sql/servers/databases/geoBackupPolicies/write | 建立或更新資料庫異地備份原則 |
> | Microsoft .Sql/servers/資料庫/importExportAzureAsyncOperation/read | 取得進行中的匯入/匯出作業 |
> | Microsoft.Sql/servers/databases/importExportOperationResults/read | 取得進行中的匯入/匯出作業 |
> | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | 取得所選資料庫的可用維護時間範圍清單。 |
> | Microsoft.Sql/servers/databases/maintenanceWindows/read | 取得所選資料庫的維護時間範圍設定。 |
> | Microsoft.Sql/servers/databases/maintenanceWindows/write | 設定所選資料庫的維護時間範圍設定。 |
> | Microsoft.Sql/servers/databases/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> | Microsoft.Sql/servers/databases/metrics/read | 傳回資料庫的計量 |
> | Microsoft.Sql/servers/databases/operationResults/read | 取得資料庫作業的狀態。 |
> | Microsoft.Sql/servers/databases/operations/cancel/action | 取消尚未完成的 Azure SQL Database 暫止非同步作業。 |
> | Microsoft.Sql/servers/databases/operations/read | 傳回在資料庫上執行的作業清單 |
> | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | 取得資料庫的可用記錄 |
> | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> | Microsoft.Sql/servers/databases/queryStore/read | 傳回資料庫之查詢存放區設定目前的值。 |
> | Microsoft.Sql/servers/databases/queryStore/write | 更新資料庫的查詢存放區設定 |
> | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | 傳回對應至指定參數的查詢文字集合。 |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | Microsoft .Sql/servers/資料庫/recommendedSensitivityLabels/write | 批次更新建議的敏感度標籤 |
> | Microsoft.Sql/servers/databases/replicationLinks/read | 傳回複寫連結的清單，或取得指定複寫連結的屬性。 |
> | Microsoft.Sql/servers/databases/replicationLinks/delete | 強制終止複寫關係，但可能遺失資料 |
> | Microsoft.Sql/servers/databases/replicationLinks/failover/action | 從主要資料庫同步處理所有變更後進行容錯移轉，讓此資料庫變成複寫關係的主要資料庫，並讓遠端的主要資料庫變成次要資料庫 |
> | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | 立即進行容錯移轉 (但可能遺失資料)，讓此資料庫變成複寫關係的主要資料庫，並讓遠端的主要資料庫變成次要資料庫 |
> | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | 將連結的複寫模式更新為同步或非同步模式 |
> | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | 強制終止複寫關係，或在與合作夥伴進行同步處理之後終止 |
> | Microsoft.Sql/servers/databases/restorePoints/read | 傳回資料庫的還原點。 |
> | Microsoft.Sql/servers/databases/restorePoints/delete | 刪除資料庫的還原點。 |
> | Microsoft.Sql/servers/databases/schemas/read | 取得資料庫結構描述。 |
> | Microsoft.Sql/servers/databases/schemas/tables/read | 取得資料庫資料表。 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | 取得資料庫資料行。 |
> | Microsoft .Sql/servers/資料庫/架構/資料表/資料行/sensitivityLabels/啟用/動作 | 針對指定的資料行啟用敏感度建議 |
> | Microsoft .Sql/servers/資料庫/架構/資料表/資料行/sensitivityLabels/停用/動作 | 停用指定資料行的敏感度建議 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | 取得指定資料行的敏感度標籤 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | 建立或更新指定資料行的敏感度標籤 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | 刪除指定資料行的敏感度標籤 |
> | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | 擷取資料庫上的索引建議清單 |
> | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | 套用索引建議 |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/write | 變更指定資料庫的資料庫威脅偵測原則 |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/read | 取得針對指定伺服器所設定的資料庫威脅偵測原則清單 |
> | Microsoft.Sql/servers/databases/securityMetrics/read | 取得資料庫安全性計量的集合 |
> | Microsoft.Sql/servers/databases/sensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | 傳回相關建議，以讓您根據查詢執行統計資料來相應增加或減少資料庫，以提升效能或降低成本 |
> | Microsoft.Sql/servers/databases/skus/read | 取得可供資料庫使用的 SKU 集合 |
> | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | 重新整理同步中樞資料庫結構描述 |
> | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | 取消同步群組同步處理 |
> | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | 觸發同步群組同步處理 |
> | Microsoft.Sql/servers/databases/syncGroups/read | 傳回同步群組的清單，或取得指定同步群組的屬性。 |
> | Microsoft.Sql/servers/databases/syncGroups/write | 使用指定參數建立同步群組，或更新指定同步群組的屬性。 |
> | Microsoft.Sql/servers/databases/syncGroups/delete | 刪除現有的同步群組。 |
> | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | 傳回同步中樞資料庫結構描述的清單 |
> | Microsoft.Sql/servers/databases/syncGroups/logs/read | 傳回同步群組記錄的清單 |
> | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | 擷取同步中樞結構描述重新整理作業的結果 |
> | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | 傳回同步成員的清單，或取得指定同步成員的屬性。 |
> | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | 使用指定參數建立同步成員，或更新指定同步成員的屬性。 |
> | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | 刪除現有的同步成員。 |
> | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | 重新整理同步成員結構描述 |
> | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | 擷取同步成員結構描述重新整理作業的結果 |
> | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | 傳回同步成員資料庫結構描述的清單 |
> | Microsoft.Sql/servers/databases/topQueries/queryText/action | 傳回所選查詢識別碼的 Transact-SQL 文字 |
> | Microsoft.Sql/servers/databases/topQueries/read | 傳回所選查詢在所選時段內彙總的執行階段統計資料 |
> | Microsoft.Sql/servers/databases/topQueries/statistics/read | 傳回所選查詢在所選時段內彙總的執行階段統計資料 |
> | Microsoft.Sql/servers/databases/transparentDataEncryption/read | 取得指定受控資料庫上的邏輯資料庫透明資料加密詳細資料 |
> | Microsoft.Sql/servers/databases/transparentDataEncryption/write | 變更給定邏輯資料庫的資料庫透明資料加密 |
> | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | 取得有關透明資料加密的進行中作業 |
> | Microsoft.Sql/servers/databases/usages/read | 取得 Azure SQL Database 的使用方式資訊 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | 變更指定資料庫的弱點評量 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | 移除指定資料庫的弱點評量 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | 擷取指定資料庫上的弱點評量原則 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | 移除指定資料庫的弱點評量規則基準 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | 變更指定資料庫的弱點評量規則基準 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | 取得指定資料庫的弱點評量規則基準 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | 執行弱點評估資料庫掃描。 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | 傳回資料庫弱點評量掃描記錄的清單，或取得指定掃描識別碼的掃描記錄。 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | 將現有的掃描結果轉換成人類可讀取的格式。 如果已存在，則不會執行任何動作 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | 擷取資料庫弱點評量掃描執行作業的結果 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | 擷取指定資料庫上所設定之弱點評量的詳細資料 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | 變更指定資料庫的弱點評量 |
> | Microsoft .Sql/servers/資料庫/workloadGroups/read | 列出所選資料庫的工作負載群組。 |
> | Microsoft .Sql/servers/資料庫/workloadGroups/write | 設定特定工作負載群組的屬性。 |
> | Microsoft .Sql/servers/資料庫/workloadGroups/delete | 卸載特定的工作負載群組。 |
> | Microsoft .Sql/servers/資料庫/workloadGroups/workloadClassifiers/read | 列出所選資料庫的工作負載分類器。 |
> | Microsoft .Sql/servers/資料庫/workloadGroups/workloadClassifiers/write | 設定特定工作負載分類器的屬性。 |
> | Microsoft .Sql/servers/database/workloadGroups/workloadClassifiers/delete | 卸載特定的工作負載分類器。 |
> | Microsoft .Sql/servers/devOpsAuditingSettings/read | 取得指定伺服器上所設定之伺服器 DevOps 稽核原則的詳細資料 |
> | Microsoft .Sql/servers/devOpsAuditingSettings/write | 變更給定伺服器的伺服器 DevOps 稽核原則 |
> | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | 取得包括此伺服器在內的災害復原設定集合 |
> | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | 變更伺服器的災害復原設定 |
> | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | 刪除指定伺服器的現有災害復原設定 |
> | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | 進行 DisasterRecoveryConfiguration 的容錯移轉 |
> | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | 強制進行 DisasterRecoveryConfiguration 的容錯移轉 |
> | Microsoft.Sql/servers/elasticPoolEstimates/read | 傳回已針對此伺服器建立之彈性集區估計值的清單 |
> | Microsoft.Sql/servers/elasticPoolEstimates/write | 針對所提供的資料庫清單建立新的彈性集區估計值 |
> | Microsoft.Sql/servers/elasticPools/read | 擷取指定伺服器上彈性集區的詳細資料 |
> | Microsoft.Sql/servers/elasticPools/write | 建立新的彈性集區，或變更現有彈性集區的屬性 |
> | Microsoft.Sql/servers/elasticPools/delete | 刪除現有彈性集區 |
> | Microsoft .Sql/servers/elasticPools/容錯移轉/動作 | 客戶起始的彈性集區容錯移轉。 |
> | Microsoft.Sql/servers/elasticPools/advisors/read | 傳回彈性集區可用 Advisor 的清單 |
> | Microsoft.Sql/servers/elasticPools/advisors/write | 更新彈性集區層級上 Advisor 的自動執行狀態。 |
> | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | 傳回彈性集區之指定 Advisor 的建議動作清單 |
> | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | 對彈性集區套用建議動作 |
> | Microsoft.Sql/servers/elasticPools/databases/read | 取得彈性集區的資料庫清單 |
> | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | 擷取給定彈性資料庫集區的活動和詳細資料 |
> | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | 擷取屬於彈性資料庫集區之給定資料庫的活動和詳細資料 |
> | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | 傳回可供彈性資料庫集區使用之計量的類型 |
> | Microsoft.Sql/servers/elasticPools/metrics/read | 傳回彈性資料庫集區的計量 |
> | Microsoft.Sql/servers/elasticPools/operations/cancel/action | 取消尚未完成的 Azure SQL 彈性集區暫止非同步作業。 |
> | Microsoft.Sql/servers/elasticPools/operations/read | 傳回在彈性集區上執行的作業清單 |
> | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供彈性資料庫集區使用之計量的類型 |
> | Microsoft.Sql/servers/elasticPools/skus/read | 取得可供彈性集區使用的 SKU 集合 |
> | Microsoft .Sql/servers/encryptionProtector/重新驗證/動作 | 更新指定伺服器加密保護裝置的屬性。 |
> | Microsoft.Sql/servers/encryptionProtector/read | 傳回伺服器加密保護裝置的清單，或取得指定伺服器加密保護裝置的屬性。 |
> | Microsoft.Sql/servers/encryptionProtector/write | 更新指定伺服器加密保護裝置的屬性。 |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | 擷取指定伺服器上所設定之擴充伺服器 Blob 稽核原則的詳細資料 |
> | Microsoft.Sql/servers/extendedAuditingSettings/write | 變更指定伺服器的擴充伺服器 Blob 稽核 |
> | Microsoft.Sql/servers/failoverGroups/read | 傳回容錯移轉群組的清單，或取得指定容錯移轉群組的屬性。 |
> | Microsoft.Sql/servers/failoverGroups/write | 使用指定參數建立容錯移轉群組，或更新指定容錯移轉的屬性或標記。 |
> | Microsoft.Sql/servers/failoverGroups/delete | 刪除現有的容錯移轉群組。 |
> | Microsoft.Sql/servers/failoverGroups/failover/action | 在現有的容錯移轉群組中執行規劃的容錯移轉。 |
> | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | 在現有的容錯移轉群組中執行強制容錯移轉。 |
> | Microsoft.Sql/servers/firewallRules/write | 使用指定參數建立伺服器防火牆規則、更新指定規則的屬性，或使用新的伺服器防火牆規則覆寫所有現有的規則。 |
> | Microsoft.Sql/servers/firewallRules/read | 傳回伺服器防火牆規則的清單，或取得指定伺服器防火牆規則的屬性。 |
> | Microsoft.Sql/servers/firewallRules/delete | 刪除現有的伺服器防火牆規則。 |
> | Microsoft.Sql/servers/importExportOperationResults/read | 取得進行中的匯入/匯出作業 |
> | Microsoft .Sql/servers/inaccessibleDatabases/read | 傳回邏輯伺服器中無法存取的資料庫 () 清單。 |
> | Microsoft.Sql/servers/interfaceEndpointProfiles/write | 使用指定參數建立介面端點設定檔，或更新指定介面端點的屬性或標記 |
> | Microsoft.Sql/servers/interfaceEndpointProfiles/read | 傳回指定介面端點設定檔的屬性 |
> | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | 刪除指定的介面端點設定檔 |
> | Microsoft.Sql/servers/jobAgents/read | 取得 Azure SQL DB 工作代理程式 |
> | Microsoft.Sql/servers/jobAgents/write | 建立或更新 Azure SQL DB 工作代理程式 |
> | Microsoft.Sql/servers/jobAgents/delete | 刪除 Azure SQL DB 工作代理程式 |
> | Microsoft.Sql/servers/keys/read | 傳回伺服器金鑰的清單，或取得指定伺服器金鑰的屬性。 |
> | Microsoft.Sql/servers/keys/write | 使用指定參數建立金鑰，或更新指定伺服器金鑰的屬性或標記。 |
> | Microsoft.Sql/servers/keys/delete | 刪除現有的伺服器金鑰。 |
> | Microsoft.Sql/servers/operationResults/read | 取得進行中的伺服器作業 |
> | Microsoft .Sql/servers/operations/read | 傳回在伺服器上執行的作業清單 |
> | Microsoft .Sql/servers/privateEndpointConnectionProxies/updatePrivateEndpointProperties/action | 由 NRP 用來將屬性回填至私人端點連接 |
> | Microsoft .Sql/servers/privateEndpointConnectionProxies/validate/action | 驗證私人端點連接從 NRP 端建立呼叫 |
> | Microsoft .Sql/servers/privateEndpointConnectionProxies/read | 傳回私人端點連線 proxy 的清單，或取得指定私人端點連接 proxy 的屬性。 |
> | Microsoft .Sql/servers/privateEndpointConnectionProxies/write | 使用指定的參數建立私人端點連線 proxy，或更新指定私人端點連接 proxy 的屬性或標記。 |
> | Microsoft .Sql/servers/privateEndpointConnectionProxies/delete | 刪除現有的私人端點連線 proxy |
> | Microsoft .Sql/servers/privateEndpointConnections/read | 傳回私人端點連接的清單，或取得指定私人端點連接的屬性。 |
> | Microsoft .Sql/servers/privateEndpointConnections/delete | 刪除現有的私人端點連接 |
> | Microsoft .Sql/servers/privateEndpointConnections/write | 核准或拒絕現有的私人端點連線 |
> | Microsoft .Sql/servers/privateLinkResources/read | 取得對應 sql server 的 private link 資源 |
> | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供伺服器使用的計量類型 |
> | Microsoft.Sql/servers/recommendedElasticPools/read | 擷取彈性資料庫集區的建議，以根據資源的歷史使用量來降低成本或改善效能 |
> | Microsoft.Sql/servers/recommendedElasticPools/databases/read | 擷取給定伺服器之建議彈性資料庫集區的計量 |
> | Microsoft.Sql/servers/recoverableDatabases/read | 這項作業可用於即時資料庫的災害復原，以將資料庫還原至最後一個已知良好的備份點。 它會傳回最後一個良好備份的相關資訊，但它實際上並不會還原資料庫。 |
> | Microsoft.Sql/servers/replicationLinks/read | 傳回複寫連結的清單，或取得指定複寫連結的屬性。 |
> | Microsoft.Sql/servers/restorableDroppedDatabases/read | 取得指定伺服器上雖已捨棄但仍留在保留原則內的資料庫清單。 |
> | Microsoft.Sql/servers/securityAlertPolicies/write | 變更指定伺服器的伺服器威脅偵測原則 |
> | Microsoft.Sql/servers/securityAlertPolicies/read | 取得針對指定伺服器所設定的伺服器威脅偵測原則清單 |
> | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | 擷取伺服器威脅偵測原則寫入作業的結果 |
> | Microsoft.Sql/servers/serviceObjectives/read | 擷取給定伺服器上可用的服務等級目標 (也稱為效能層級) 清單 |
> | Microsoft.Sql/servers/syncAgents/read | 傳回同步代理程式的清單，或取得指定同步代理程式的屬性。 |
> | Microsoft.Sql/servers/syncAgents/write | 使用指定參數建立同步代理程式，或更新指定同步代理程式的屬性。 |
> | Microsoft.Sql/servers/syncAgents/delete | 刪除現有的同步代理程式。 |
> | Microsoft.Sql/servers/syncAgents/generateKey/action | 產生同步代理程式登錄金鑰 |
> | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | 傳回已連結同步代理程式的資料庫清單 |
> | Microsoft.Sql/servers/usages/read | 傳回伺服器中所有資料庫的伺服器 DTU 配額和目前 DTU 耗用量 |
> | Microsoft.Sql/servers/virtualNetworkRules/read | 傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。 |
> | Microsoft.Sql/servers/virtualNetworkRules/write | 使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。 |
> | Microsoft.Sql/servers/virtualNetworkRules/delete | 刪除現有虛擬網路規則 |
> | Microsoft.Sql/servers/vulnerabilityAssessments/write | 變更指定伺服器的弱點評量 |
> | Microsoft.Sql/servers/vulnerabilityAssessments/delete | 移除指定伺服器的弱點評量 |
> | Microsoft.Sql/servers/vulnerabilityAssessments/read | 擷取指定伺服器上的弱點評量原則 |
> | Microsoft .Sql/virtualClusters/updateManagedInstanceDnsServers/action | 使用虛擬叢集所在的 Azure 虛擬網路設定，同步處理 Azure SQL 虛擬叢集上的 DNS 伺服器設定。 |
> | Microsoft.Sql/virtualClusters/read | 傳回虛擬叢集清單，或取得指定虛擬叢集的屬性。 |
> | Microsoft.Sql/virtualClusters/write | 更新虛擬叢集標記。 |
> | Microsoft .Sql/virtualClusters/delete | 刪除現有的虛擬叢集。 |

### <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

Azure 服務： [Azure 虛擬機器上的 SQL Server](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.sqlvirtualmachine/註冊/動作 | 向 Microsoft.sqlvirtualmachine 資源提供者註冊訂用帳戶 |
> | Microsoft.sqlvirtualmachine/取消註冊/動作 | 使用 Microsoft.sqlvirtualmachine 資源提供者取消註冊訂用帳戶 |
> | Microsoft.sqlvirtualmachine/位置/registerSqlVmCandidate/動作 | 註冊 SQL Vm 候選版 |
> | Microsoft.sqlvirtualmachine/位置/availabilityGroupListenerOperationResults/讀取 | 取得可用性群組接聽程式作業的結果 |
> | Microsoft.sqlvirtualmachine/位置/sqlVirtualMachineGroupOperationResults/讀取 | 取得 SQL 虛擬機器群組操作的結果 |
> | Microsoft.sqlvirtualmachine/位置/sqlVirtualMachineOperationResults/讀取 | 取得 SQL 虛擬機器操作的結果 |
> | Microsoft.sqlvirtualmachine/operations/read |  |
> | Microsoft.sqlvirtualmachine/sqlVirtualMachineGroups/read | 深入瞭解 SQL 虛擬機器群組的詳細資料 |
> | Microsoft.sqlvirtualmachine/sqlVirtualMachineGroups/write | 建立新的或變更現有 SQL 虛擬機器群組的屬性 |
> | Microsoft.sqlvirtualmachine/sqlVirtualMachineGroups/delete | 刪除現有的 SQL 虛擬機器群組 |
> | Microsoft.sqlvirtualmachine/sqlVirtualMachineGroups/availabilityGroupListeners/read | 取得指定 SQL 虛擬機器群組上 SQL 可用性群組接聽程式的詳細資料 |
> | Microsoft.sqlvirtualmachine/sqlVirtualMachineGroups/availabilityGroupListeners/write | 建立現有 SQL 可用性群組接聽程式的新或變更屬性 |
> | Microsoft.sqlvirtualmachine/sqlVirtualMachineGroups/availabilityGroupListeners/delete | 刪除現有的可用性群組接聽程式 |
> | Microsoft.sqlvirtualmachine/sqlVirtualMachineGroups/sqlVirtualMachines/read | 依特定的 sql 虛擬機器群組列出 Sql 虛擬機器 |
> | Microsoft.sqlvirtualmachine/sqlVirtualMachines/read | 取得 SQL 虛擬機器的詳細資料 |
> | Microsoft.sqlvirtualmachine/sqlVirtualMachines/write | 建立新的或變更現有 SQL 虛擬機器的屬性 |
> | Microsoft.sqlvirtualmachine/sqlVirtualMachines/delete | 刪除現有的 SQL 虛擬機器 |

## <a name="analytics"></a>分析

### <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

Azure 服務： [azure Analysis Services](../analysis-services/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.AnalysisServices/register/action | 註冊 Analysis Services 資源提供者。 |
> | Microsoft.AnalysisServices/locations/checkNameAvailability/action | 確認給定的 Analysis Server 名稱有效，且並非使用中。 |
> | Microsoft.AnalysisServices/locations/operationresults/read | 擷取指定作業結果的資訊。 |
> | Microsoft.AnalysisServices/locations/operationstatuses/read | 擷取指定作業狀態的資訊。 |
> | Microsoft.AnalysisServices/operations/read | 擷取作業的資訊 |
> | Microsoft.AnalysisServices/servers/read | 擷取所指定 Analysis Server 的資訊。 |
> | Microsoft.AnalysisServices/servers/write | 建立或更新所指定的 Analysis Server。 |
> | Microsoft.AnalysisServices/servers/delete | 刪除 Analysis Server。 |
> | Microsoft.AnalysisServices/servers/suspend/action | 暫止 Analysis Server。 |
> | Microsoft.AnalysisServices/servers/resume/action | 繼續 Analysis Server。 |
> | Microsoft.AnalysisServices/servers/listGatewayStatus/action | 列出與伺服器相關聯的閘道狀態。 |
> | Microsoft.AnalysisServices/servers/skus/read | 擷取伺服器的可用 SKU 資訊 |
> | Microsoft.AnalysisServices/skus/read | 擷取 SKU 的資訊 |

### <a name="microsoftdatabricks"></a>Microsoft.Databricks

Azure 服務： [Azure Databricks](https://docs.microsoft.com/azure/databricks/)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Databricks/register/action | 註冊 Databricks。 |
> | Databricks/位置/getNetworkPolicies/動作 | 根據 NRP 所使用的位置取得子網的網路意圖原則 |
> | Databricks/位置/operationstatuses/讀取 | 讀取資源的作業狀態。 |
> | Databricks/operations/read | 取得作業的清單。 |
> | Microsoft.Databricks/workspaces/read | 擷取 Databricks 工作區的清單。 |
> | Microsoft.Databricks/workspaces/write | 建立 Databricks 工作區。 |
> | Microsoft.Databricks/workspaces/delete | 移除 Databricks 工作區。 |
> | Databricks/workspace/refreshPermissions/action | 重新整理工作區的許可權 |
> | Databricks/workspace/updateDenyAssignment/action | 為工作區的受控資源群組更新拒絕指派的動作 |
> | Databricks/workspace/refreshWorkspaces/action | 以新的詳細資料（例如 URL）重新整理工作區 |
> | Databricks/workspace/dbWorkspaces/write |  (內部) 初始化 Databricks 工作區 |
> | Databricks/workspace/privateEndpointConnectionProxies/read | 取得私人端點連線 Proxy |
> | Databricks/workspace/privateEndpointConnectionProxies/validate/action | 驗證私人端點連線 proxy |
> | Databricks/workspace/privateEndpointConnectionProxies/write | 放置私人端點連線 proxy |
> | Databricks/workspace/privateEndpointConnectionProxies/delete | 刪除私人端點連線 proxy |
> | Databricks/workspace/privateEndpointConnections/read | 列出私人端點連接 |
> | Databricks/workspace/privateEndpointConnections/write | 核准私人端點連線 |
> | Databricks/workspace/privateEndpointConnections/delete | 移除私人端點連線 |
> | Databricks/workspace/privateLinkResources/read | 列出 Private Link 資源 |
> | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | 設定 Databricks 工作區的可用診斷設定 |
> | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | 取得 Databricks 工作區的可用記錄定義 |
> | Databricks/workspace/virtualNetworkPeerings/read | 取得虛擬網路對等互連。 |
> | Databricks/workspace/virtualNetworkPeerings/write | 新增或修改虛擬網路對等互連 |
> | Databricks/workspace/virtualNetworkPeerings/delete | 刪除虛擬網路對等互連 |

### <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

Azure 服務： [Data Lake Analytics](../data-lake-analytics/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.DataLakeAnalytics/register/action | 向 DataLakeAnalytics 註冊訂用帳戶。 |
> | Microsoft.DataLakeAnalytics/accounts/read | 取得現有 DataLakeAnalytics 帳戶的相關資訊。 |
> | Microsoft.DataLakeAnalytics/accounts/write | 建立或更新 DataLakeAnalytics 帳戶。 |
> | Microsoft.DataLakeAnalytics/accounts/delete | 刪除 DataLakeAnalytics 帳戶。 |
> | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | 在 DataLakeAnalytics 帳戶之間轉送 SystemMaxAnalyticsUnits。 |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | 授與權限以取消其他使用者所提交的作業。 |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | 取得計算原則的相關資訊。 |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | 建立或更新計算原則。 |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | 刪除計算原則。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | 取得 DataLakeAnalytics 帳戶所連結之 DataLakeStore 帳戶的相關資訊。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | 建立或更新 DataLakeAnalytics 帳戶所連結的 DataLakeStore 帳戶。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | 取消 DataLakeStore 帳戶與 DataLakeAnalytics 帳戶的連結。 |
> | DataLakeAnalytics/accounts/dataLakeStoreGen2Accounts/read | 取得 DataLakeAnalytics 帳戶之連結 DataLakeStoreGen2 帳戶的相關資訊。 |
> | DataLakeAnalytics/accounts/dataLakeStoreGen2Accounts/write | 建立或更新 DataLakeAnalytics 帳戶的連結 DataLakeStoreGen2 帳戶。 |
> | DataLakeAnalytics/accounts/dataLakeStoreGen2Accounts/delete | 將 DataLakeStoreGen2 帳戶與 DataLakeAnalytics 帳戶取消連結。 |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | 取得防火牆規則的相關資訊。 |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | 建立或更新防火牆規則。 |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | 刪除防火牆規則。 |
> | Microsoft.DataLakeAnalytics/accounts/operationResults/read | 取得 DataLakeAnalytics 帳戶作業的結果。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | 取得 DataLakeAnalytics 帳戶所連結之儲存體帳戶的相關資訊。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | 建立或更新 DataLakeAnalytics 帳戶所連結的儲存體帳戶。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | 取消儲存體帳戶與 DataLakeAnalytics 帳戶的連結。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | 取得 DataLakeAnalytics 帳戶所連結之儲存體帳戶的容器。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | 針對 DataLakeAnalytics 帳戶所連結之儲存體帳戶的儲存體容器列出 SAS 權杖。 |
> | DataLakeAnalytics/accounts/virtualNetworkRules/read | 取得虛擬網路規則的相關資訊。 |
> | DataLakeAnalytics/accounts/virtualNetworkRules/write | 建立或更新虛擬網路規則。 |
> | DataLakeAnalytics/accounts/virtualNetworkRules/delete | 刪除虛擬網路規則。 |
> | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | 檢查 DataLakeAnalytics 帳戶名稱的可用性。 |
> | Microsoft.DataLakeAnalytics/locations/capability/read | 取得訂用帳戶中關於使用 DataLakeAnalytics 的功能資訊。 |
> | Microsoft.DataLakeAnalytics/locations/operationResults/read | 取得 DataLakeAnalytics 帳戶作業的結果。 |
> | Microsoft.DataLakeAnalytics/locations/usages/read | 取得訂用帳戶中關於使用 DataLakeAnalytics 的配額使用量資訊。 |
> | Microsoft.DataLakeAnalytics/operations/read | 取得 DataLakeAnalytics 的可用作業。 |

### <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

Azure 服務： [Azure Data Lake 存放區](../storage/blobs/data-lake-storage-introduction.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.DataLakeStore/register/action | 向 DataLakeStore 註冊訂用帳戶。 |
> | Microsoft.DataLakeStore/accounts/read | 取得現有 DataLakeStore 帳戶的相關資訊。 |
> | Microsoft.DataLakeStore/accounts/write | 建立或更新 DataLakeStore 帳戶。 |
> | Microsoft.DataLakeStore/accounts/delete | 刪除 DataLakeStore 帳戶。 |
> | Microsoft.DataLakeStore/accounts/enableKeyVault/action | 針對 DataLakeStore 帳戶啟用 KeyVault。 |
> | Microsoft.DataLakeStore/accounts/Superuser/action | 使用 Microsoft.Authorization/roleAssignments/write 授與時，授與 Data Lake Store 的超級使用者。 |
> | Microsoft.DataLakeStore/accounts/eventGridFilters/read | 取得 EventGrid 篩選。 |
> | Microsoft.DataLakeStore/accounts/eventGridFilters/write | 建立或更新 EventGrid 篩選。 |
> | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | 刪除 EventGrid 篩選。 |
> | Microsoft.DataLakeStore/accounts/firewallRules/read | 取得防火牆規則的相關資訊。 |
> | Microsoft.DataLakeStore/accounts/firewallRules/write | 建立或更新防火牆規則。 |
> | Microsoft.DataLakeStore/accounts/firewallRules/delete | 刪除防火牆規則。 |
> | Microsoft.DataLakeStore/accounts/operationResults/read | 取得 DataLakeStore 帳戶作業的結果。 |
> | DataLakeStore/帳戶/共用/讀取 | 取得共用的相關資訊。 |
> | DataLakeStore/帳戶/共用/寫入 | 建立或更新共用。 |
> | DataLakeStore/帳戶/共用/刪除 | 刪除共用。 |
> | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | 取得所信任識別提供者的相關資訊。 |
> | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | 建立或更新所信任的識別提供者。 |
> | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | 刪除所信任的識別提供者。 |
> | DataLakeStore/accounts/virtualNetworkRules/read | 取得虛擬網路規則的相關資訊。 |
> | DataLakeStore/accounts/virtualNetworkRules/write | 建立或更新虛擬網路規則。 |
> | DataLakeStore/accounts/virtualNetworkRules/delete | 刪除虛擬網路規則。 |
> | Microsoft.DataLakeStore/locations/checkNameAvailability/action | 檢查 DataLakeStore 帳戶名稱的可用性。 |
> | Microsoft.DataLakeStore/locations/capability/read | 取得訂用帳戶中關於使用 DataLakeStore 的功能資訊。 |
> | Microsoft.DataLakeStore/locations/operationResults/read | 取得 DataLakeStore 帳戶作業的結果。 |
> | Microsoft.DataLakeStore/locations/usages/read | 取得訂用帳戶中關於使用 DataLakeStore 的配額使用量資訊。 |
> | Microsoft.DataLakeStore/operations/read | 取得 DataLakeStore 的可用作業。 |

### <a name="microsofteventhub"></a>Microsoft.EventHub

Azure 服務： [事件中樞](../event-hubs/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.EventHub/checkNamespaceAvailability/action | 檢查給定訂用帳戶下的命名空間是否可用。 此 API 即將淘汰。請改用 CheckNameAvailabiltiy。 |
> | Microsoft.EventHub/checkNameAvailability/action | 檢查給定訂用帳戶下的命名空間是否可用。 |
> | Microsoft.EventHub/register/action | 針對 EventHub 資源提供者註冊訂用帳戶，並讓您能夠建立 EventHub 資源 |
> | Microsoft.EventHub/unregister/action | 註冊 EventHub 資源提供者 |
> | Microsoft. EventHub/availableClusterRegions/read | 讀取作業，以列出 Azure 區域可用的預先布建叢集。 |
> | Microsoft.EventHub/clusters/read | 取得叢集資源描述 |
> | Microsoft.EventHub/clusters/write | 建立或修改現有的叢集資源。 |
> | Microsoft EventHub/叢集/刪除 | 刪除現有的叢集資源。 |
> | Microsoft EventHub/叢集/命名空間/讀取 | 列出叢集中命名空間 Azure Resource Manager 識別碼的命名空間。 |
> | Microsoft EventHub/叢集/operationresults/read | 取得非同步叢集操作的狀態。 |
> | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | 取得叢集計量資源描述的清單 |
> | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | 針對指定的 VNet，刪除 EventHub 資源提供者中的 VNet 規則 |
> | Microsoft.EventHub/namespaces/write | 建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。 |
> | Microsoft.EventHub/namespaces/read | 取得命名空間資源描述的清單 |
> | Microsoft.EventHub/namespaces/Delete | 刪除命名空間資源 |
> | Microsoft.EventHub/namespaces/authorizationRules/action | 更新命名空間授權規則。 此 API 即將淘汰。 請改用 PUT 呼叫來更新命名空間授權規則。 API 版本 2017-04-01 不支援此作業。 |
> | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | 移除 ACS 命名空間 |
> | Microsoft EventHub/命名空間/privateEndpointConnectionsApproval/action | 核准私人端點連線 |
> | Microsoft.EventHub/namespaces/authorizationRules/read | 取得命名空間授權規則描述的清單。 |
> | Microsoft.EventHub/namespaces/authorizationRules/write | 建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | Microsoft.EventHub/namespaces/authorizationRules/delete | 刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。  |
> | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | 取得命名空間的連接字串 |
> | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | 對資源重新產生主要或次要金鑰 |
> | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 檢查指定訂用帳戶下命名空間別名的可用性。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | 建立或更新與命名空間相關聯的災害復原設定。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | 取得與命名空間相關聯的災害復原設定。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | 刪除與命名空間相關聯的災害復原設定。 此作業只能透過主要命名空間叫用。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | 停用災害復原，並停止將變更從主要命名空間複寫至次要命名空間。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | 叫用異地災害復原容錯移轉，並將命名空間別名重新設定為指向次要命名空間。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | 取得災害復原主要命名空間的授權規則 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 取得災害復原主要命名空間的授權規則金鑰 |
> | Microsoft.EventHub/namespaces/eventhubs/write | 建立或更新 EventHub 屬性。 |
> | Microsoft.EventHub/namespaces/eventhubs/read | 取得 EventHub 資源描述的清單 |
> | Microsoft.EventHub/namespaces/eventhubs/Delete | 用來刪除 EventHub 資源的作業 |
> | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | 更新 EventHub 的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  取得 EventHub 授權規則的清單 |
> | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | 建立 EventHub 授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | 用來刪除 EventHub 授權規則的作業 |
> | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | 取得 EventHub 的連接字串 |
> | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | 對資源重新產生主要或次要金鑰 |
> | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | 建立或更新 ConsumerGroup 屬性。 |
> | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | 取得 ConsumerGroup 資源描述的清單 |
> | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | 用來刪除 ConsumerGroup 資源的作業 |
> | Microsoft.EventHub/namespaces/ipFilterRules/read | 取得 IP 篩選資源 |
> | Microsoft.EventHub/namespaces/ipFilterRules/write | 建立 IP 篩選資源 |
> | Microsoft.EventHub/namespaces/ipFilterRules/delete | 刪除 IP 篩選資源 |
> | Microsoft.EventHub/namespaces/messagingPlan/read | 取得命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> | Microsoft.EventHub/namespaces/messagingPlan/write | 更新命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> | Microsoft EventHub/命名空間/networkruleset/read | 取得 NetworkRuleSet 資源 |
> | Microsoft EventHub/命名空間/networkruleset/write | 建立 VNET 規則資源 |
> | Microsoft EventHub/命名空間/networkruleset/delete | 刪除 VNET 規則資源 |
> | Microsoft EventHub/命名空間/networkrulesets/read | 取得 NetworkRuleSet 資源 |
> | Microsoft EventHub/命名空間/networkrulesets/write | 建立 VNET 規則資源 |
> | Microsoft EventHub/命名空間/networkrulesets/delete | 刪除 VNET 規則資源 |
> | Microsoft.EventHub/namespaces/operationresults/read | 取得命名空間作業的狀態 |
> | Microsoft EventHub/命名空間/privateEndpointConnectionProxies/驗證/動作 | 驗證私人端點連線 Proxy |
> | Microsoft EventHub/命名空間/privateEndpointConnectionProxies/read | 取得私人端點連線 Proxy |
> | Microsoft EventHub/命名空間/privateEndpointConnectionProxies/write | 建立私人端點連接 Proxy |
> | Microsoft EventHub/命名空間/privateEndpointConnectionProxies/delete | 刪除私人端點連線 Proxy |
> | Microsoft. EventHub/命名空間/privateEndpointConnectionProxies/>operationstatus/read | 取得非同步私人端點操作的狀態 |
> | Microsoft EventHub/命名空間/privateEndpointConnections/read | 取得私人端點連線 |
> | Microsoft EventHub/命名空間/privateEndpointConnections/write | 建立或更新私人端點連線 |
> | Microsoft EventHub/命名空間/privateEndpointConnections/delete | 移除私人端點連線 |
> | Microsoft. EventHub/命名空間/privateEndpointConnections/>operationstatus/read | 取得非同步私人端點操作的狀態 |
> | Microsoft EventHub/命名空間/privateLinkResources/read | 取得支援私人端點連接的資源類型 |
> | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 取得命名空間診斷設定資源描述的清單 |
> | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 取得命名空間診斷設定資源描述的清單 |
> | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | 取得命名空間記錄資源描述的清單 |
> | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 取得命名空間計量資源描述的清單 |
> | Microsoft EventHub/命名空間/schemagroups/write | 建立或更新 SchemaGroup 屬性。 |
> | Microsoft EventHub/命名空間/schemagroups/read | 取得 SchemaGroup 資源描述的清單 |
> | Microsoft EventHub/命名空間/schemagroups/delete | 用來刪除 SchemaGroup 資源的作業 |
> | Microsoft.EventHub/namespaces/virtualNetworkRules/read | 取得 VNET 規則資源 |
> | Microsoft.EventHub/namespaces/virtualNetworkRules/write | 建立 VNET 規則資源 |
> | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | 刪除 VNET 規則資源 |
> | Microsoft.EventHub/operations/read | 取得作業 |
> | Microsoft.EventHub/sku/read | 取得 SKU 資源描述的清單 |
> | Microsoft.EventHub/sku/regions/read | 取得 SkuRegions 資源描述的清單 |
> | **DataAction** | **說明** |
> | Microsoft EventHub/命名空間/訊息/傳送/動作 | 傳送訊息 |
> | Microsoft EventHub/命名空間/訊息/接收/動作 | 接收訊息 |
> | Microsoft EventHub/命名空間/架構/讀取 | 取出架構 |
> | Microsoft EventHub/命名空間/架構/寫入 | 寫入架構 |
> | Microsoft EventHub/命名空間/架構/刪除 | 刪除結構描述 |

### <a name="microsofthdinsight"></a>Microsoft.HDInsight

Azure 服務： [HDInsight](../hdinsight/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft HDInsight/註冊/動作 | 為訂用帳戶註冊 HDInsight 資源提供者 |
> | Microsoft HDInsight/取消註冊/動作 | 取消註冊訂用帳戶的 HDInsight 資源提供者 |
> | Microsoft.HDInsight/clusters/write | 建立或更新 HDInsight 叢集 |
> | Microsoft.HDInsight/clusters/read | 取得 HDInsight 叢集的相關詳細資料 |
> | Microsoft.HDInsight/clusters/delete | 刪除 HDInsight 叢集 |
> | Microsoft.HDInsight/clusters/changerdpsetting/action | 變更 HDInsight 叢集的 RDP 設定 |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | 取得 HDInsight 叢集的閘道設定 |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | 更新 HDInsight 叢集的閘道設定 |
> | Microsoft.HDInsight/clusters/configurations/action | 取得 HDInsight 叢集組態 |
> | Microsoft.HDInsight/clusters/applications/read | 取得 HDInsight 叢集的應用程式 |
> | Microsoft.HDInsight/clusters/applications/write | 建立或更新 HDInsight 叢集的應用程式 |
> | Microsoft.HDInsight/clusters/applications/delete | 刪除 HDInsight 叢集的應用程式 |
> | Microsoft.HDInsight/clusters/configurations/read | 取得 HDInsight 叢集組態 |
> | Microsoft HDInsight/叢集/擴充/寫入 | 建立適用于 HDInsight 叢集的叢集擴充功能 |
> | Microsoft HDInsight/叢集/擴充功能/讀取 | 取得 HDInsight 叢集的叢集擴充功能 |
> | Microsoft HDInsight/叢集/擴充功能/刪除 | 刪除 HDInsight 叢集的叢集擴充功能 |
> | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源 HDInsight 叢集的診斷設定 |
> | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源 HDInsight 叢集的診斷設定 |
> | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | 取得 HDInsight 叢集的可用計量 |
> | Microsoft.HDInsight/clusters/roles/resize/action | 調整 HDInsight 叢集的大小 |
> | Microsoft.HDInsight/locations/capabilities/read | 取得訂用帳戶功能 |
> | Microsoft.HDInsight/locations/checkNameAvailability/read | 檢查名稱可用性 |

### <a name="microsoftkusto"></a>Microsoft.Kusto

Azure 服務： [azure 資料總管](/azure/data-explorer/)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Kusto/註冊/動作 | 訂用帳戶註冊動作 |
> | Kusto/註冊/動作 | 向 Kusto 資源提供者註冊訂用帳戶。 |
> | Kusto/取消註冊/動作 | 取消註冊 Kusto 資源提供者的訂用帳戶。 |
> | Microsoft.Kusto/Clusters/read | 讀取叢集資源。 |
> | Microsoft.Kusto/Clusters/write | 寫入叢集資源。 |
> | Microsoft.Kusto/Clusters/delete | 刪除叢集資源。 |
> | Kusto/叢集/開始/動作 | 啟動叢集。 |
> | Kusto/叢集/停止/動作 | 停止叢集。 |
> | Kusto/叢集/啟用/動作 | 啟動叢集。 |
> | Kusto/叢集/停用/動作 | 停止叢集。 |
> | Kusto/叢集/CheckNameAvailability/action | 檢查叢集名稱可用性。 |
> | Kusto/叢集/DetachFollowerDatabases/action | 卸離的資料庫。 |
> | Kusto/叢集/ListFollowerDatabases/action | 列出您的資料庫。 |
> | Kusto/叢集/DiagnoseVirtualNetwork/action | 針對服務相依的外部資源，診斷其網路線上狀態。 |
> | Kusto/叢集/ListLanguageExtensions/action | 列出語言擴充功能。 |
> | Kusto/叢集/AddLanguageExtensions/action | 新增語言擴充功能。 |
> | Kusto/叢集/RemoveLanguageExtensions/action | 移除語言擴充功能。 |
> | Kusto/叢集/AttachedDatabaseConfigurations/read | 讀取附加的資料庫設定資源。 |
> | Kusto/叢集/AttachedDatabaseConfigurations/write | 寫入附加的資料庫設定資源。 |
> | Kusto/叢集/AttachedDatabaseConfigurations/delete | 刪除附加的資料庫設定資源。 |
> | Microsoft.Kusto/Clusters/Databases/read | 讀取資料庫資源。 |
> | Microsoft.Kusto/Clusters/Databases/write | 寫入資料庫資源。 |
> | Microsoft.Kusto/Clusters/Databases/delete | 刪除資料庫資源。 |
> | Kusto/叢集/資料庫/ListPrincipals/動作 | 列出資料庫主體。 |
> | Kusto/叢集/資料庫/AddPrincipals/動作 | 新增資料庫主體。 |
> | Kusto/叢集/資料庫/RemovePrincipals/動作 | 移除資料庫主體。 |
> | Kusto/叢集/資料庫/DataConnectionValidation/動作 | 驗證資料庫資料連線。 |
> | Kusto/叢集/資料庫/CheckNameAvailability/動作 | 檢查給定類型的名稱可用性。 |
> | Kusto/叢集/資料庫/EventHubConnectionValidation/動作 | 驗證資料庫事件中樞連接。 |
> | Microsoft.Kusto/Clusters/Databases/DataConnections/read | 讀取資料連線資源。 |
> | Microsoft.Kusto/Clusters/Databases/DataConnections/write | 寫入資料連線資源。 |
> | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | 刪除資料連線資源。 |
> | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | 讀取事件中樞連線資源。 |
> | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | 寫入事件中樞連線資源。 |
> | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | 刪除事件中樞連線資源。 |
> | Kusto/叢集/資料庫/PrincipalAssignments/read | 讀取資料庫主體指派資源。 |
> | Kusto/叢集/資料庫/PrincipalAssignments/write | 寫入資料庫主體指派資源。 |
> | Kusto/叢集/資料庫/PrincipalAssignments/delete | 刪除資料庫主體指派資源。 |
> | Kusto/叢集/Dataconnection/read | 讀取叢集的資料連線資源。 |
> | Kusto/叢集/Dataconnection/write | 寫入叢集的資料連線資源。 |
> | Kusto/叢集/Dataconnection/delete | 刪除叢集的資料連線資源。 |
> | Kusto/叢集/PrincipalAssignments/read | 讀取叢集主體指派資源。 |
> | Kusto/叢集/PrincipalAssignments/write | 寫入叢集主體指派資源。 |
> | Kusto/叢集/PrincipalAssignments/delete | 刪除叢集主體指派資源。 |
> | Kusto/叢集/Sku/讀取 | 讀取叢集 SKU 資源。 |
> | Kusto/位置/CheckNameAvailability/動作 | 檢查資源名稱的可用性。 |
> | Kusto/位置/GetNetworkPolicies/動作 | 取得網路意圖原則 |
> | Microsoft.Kusto/locations/operationresults/read | 讀取作業資源 |
> | Microsoft.Kusto/Operations/read | 讀取作業資源 |
> | Kusto/Sku/read | 讀取 SKU 資源。 |

### <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

Azure 服務： [Power BI Embedded](https://docs.microsoft.com/azure/power-bi-embedded/)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.PowerBIDedicated/register/action | 註冊 Power BI 專用資源提供者。 |
> | Microsoft.PowerBIDedicated/capacities/read | 擷取指定 Power BI 專用容量的資訊。 |
> | Microsoft.PowerBIDedicated/capacities/write | 建立或更新指定的 Power BI 專用容量。 |
> | Microsoft.PowerBIDedicated/capacities/delete | 刪除 Power BI 專用容量。 |
> | Microsoft.PowerBIDedicated/capacities/suspend/action | 暫止容量。 |
> | Microsoft.PowerBIDedicated/capacities/resume/action | 繼續容量。 |
> | Microsoft.PowerBIDedicated/capacities/skus/read | 擷取容量的可用 SKU 資訊 |
> | PowerBIDedicated/位置/checkNameAvailability/動作 | 檢查指定的 Power BI 專用容量名稱有效且未在使用中。 |
> | Microsoft.PowerBIDedicated/locations/operationresults/read | 擷取指定作業結果的資訊。 |
> | Microsoft.PowerBIDedicated/locations/operationstatuses/read | 擷取指定作業狀態的資訊。 |
> | Microsoft.PowerBIDedicated/operations/read | 擷取作業的資訊 |
> | Microsoft.PowerBIDedicated/skus/read | 擷取 SKU 的資訊 |

### <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

Azure 服務： [串流分析](../stream-analytics/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.StreamAnalytics/Register/action | 向串流分析資源提供者註冊訂用帳戶 |
> | Microsoft.StreamAnalytics/locations/quotas/Read | 讀取串流分析訂用帳戶配額 |
> | Microsoft.StreamAnalytics/operations/Read | 讀取串流分析作業 |
> | Microsoft.StreamAnalytics/streamingjobs/Delete | 刪除串流分析作業 |
> | >mslearn-streamanalytics/streamingjobs/PublishEdgePackage/action | 針對串流分析作業發佈 edge 套件 |
> | Microsoft.StreamAnalytics/streamingjobs/Read | 讀取串流分析作業 |
> | >mslearn-streamanalytics/streamingjobs/Scale/action | 調整串流分析作業 |
> | Microsoft.StreamAnalytics/streamingjobs/Start/action | 啟動串流分析作業 |
> | Microsoft.StreamAnalytics/streamingjobs/Stop/action | 停止串流分析作業 |
> | Microsoft.StreamAnalytics/streamingjobs/Write | 寫入串流分析作業 |
> | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | 刪除串流分析作業函式 |
> | Microsoft.StreamAnalytics/streamingjobs/functions/Read | 讀取串流分析作業函式 |
> | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | 擷取串流分析作業函式的預設定義 |
> | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | 測試串流分析作業函式 |
> | Microsoft.StreamAnalytics/streamingjobs/functions/Write | 寫入串流分析作業函式 |
> | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | 讀取串流分析作業函式的作業結果 |
> | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | 刪除串流分析作業輸入 |
> | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | 讀取串流分析作業輸入 |
> | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | 進行串流分析作業輸入的取樣 |
> | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | 測試串流分析作業輸入 |
> | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | 寫入串流分析作業輸入 |
> | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | 讀取串流分析作業輸入的作業結果 |
> | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | 讀取計量定義 |
> | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | 讀取串流分析作業的作業結果 |
> | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | 刪除串流分析作業輸出 |
> | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | 讀取串流分析作業輸出 |
> | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | 測試串流分析作業輸出 |
> | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | 寫入串流分析作業輸出 |
> | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | 讀取串流分析作業輸出的作業結果 |
> | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | 讀取診斷設定。 |
> | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | 寫入診斷設定。 |
> | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | 取得 streamingjobs 的可用記錄 |
> | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | 取得 streamingjobs 的可用計量 |
> | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | 刪除串流分析作業轉換 |
> | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | 讀取串流分析作業轉換 |
> | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | 寫入串流分析作業轉換 |

## <a name="blockchain"></a>區塊鏈

### <a name="microsoftblockchain"></a>Microsoft.Blockchain

Azure 服務： [Azure 區塊鏈服務](../blockchain/workbench/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 區塊鏈/註冊/動作 | 註冊區塊鏈資源提供者的訂用帳戶。 |
> | 區塊鏈/blockchainMembers/read | 取得或列出現有的區塊鏈成員 (s) 。 |
> | 區塊鏈/blockchainMembers/write | 建立或更新區塊鏈成員。 |
> | 區塊鏈/blockchainMembers/delete | 刪除現有的區塊鏈成員。 |
> | 區塊鏈/blockchainMembers/listApiKeys/action | 取得或列出現有的區塊鏈成員 API 金鑰。 |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | 取得或列出現有的區塊鏈成員交易節點。 |
> | 區塊鏈/blockchainMembers/transactionNodes/write | 建立或更新區塊鏈成員交易節點。 |
> | 區塊鏈/blockchainMembers/transactionNodes/delete | 刪除現有的區塊鏈成員交易節點。 |
> | 區塊鏈/blockchainMembers/transactionNodes/listApiKeys/action | 取得或列出現有的區塊鏈成員交易節點 API 金鑰。 |
> | 區塊鏈/cordaMembers/read | 取得或列出現有的區塊鏈 Corda 成員 (s) 。 |
> | 區塊鏈/cordaMembers/write | 建立或更新區塊鏈 Corda 成員。 |
> | 區塊鏈/cordaMembers/delete | 刪除現有的區塊鏈 Corda 成員。 |
> | 區塊鏈/位置/checkNameAvailability/動作 | 檢查資源名稱是否有效且不在使用中。 |
> | 區塊鏈/位置/blockchainMemberOperationResults/讀取 | 取得區塊鏈成員的作業結果。 |
> | 區塊鏈/operations/read | 列出 Microsoft 區塊鏈資源提供者中的所有作業。 |
> | **DataAction** | **說明** |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | 連線至區塊鏈成員交易節點。 |

## <a name="ai--machine-learning"></a>AI + 機器學習

### <a name="microsoftbotservice"></a>Microsoft.BotService

Azure 服務： [Azure Bot service](https://docs.microsoft.com/azure/bot-service/)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | BotService/checknameavailability/action | 檢查 Bot 的名稱可用性 |
> | BotService/listauthserviceproviders/action | 列出驗證服務提供者 |
> | Microsoft.BotService/botServices/read | 讀取 Bot 服務 |
> | Microsoft.BotService/botServices/write | 寫入 Bot 服務 |
> | Microsoft.BotService/botServices/delete | 刪除 Bot 服務 |
> | Microsoft.BotService/botServices/channels/read | 讀取 Bot Service 通道 |
> | Microsoft.BotService/botServices/channels/write | 撰寫 Bot Service 通道 |
> | Microsoft.BotService/botServices/channels/delete | 刪除 Bot Service 通道 |
> | BotService/>botservices.cs/channel/listchannelwithkeys/action | 列出具有秘密的 Botservice 通道 |
> | BotService/>botservices.cs/channel/providers/Microsoft. Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | BotService/>botservices.cs/channel/providers/Microsoft. Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | BotService/>botservices.cs/channel/providers/Microsoft. Insights/logDefinitions/read | 取得資源名稱的可用記錄 &lt;&gt; |
> | BotService/>botservices.cs/channel/providers/Microsoft. Insights/metricDefinitions/read | 建立或更新資源的診斷設定 |
> | Microsoft.BotService/botServices/connections/read | 讀取 Bot Service 連接 |
> | Microsoft.BotService/botServices/connections/write | 撰寫 Bot Service 連接 |
> | Microsoft.BotService/botServices/connections/delete | 刪除 Bot Service 連接 |
> | BotService/>botservices.cs/connections/listwithsecrets/write | 撰寫 Bot Service 連接清單  |
> | BotService/>botservices.cs/connections/providers/Microsoft. Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | BotService/>botservices.cs/connections/providers/Microsoft. Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | BotService/>botservices.cs/connections/providers/Microsoft. Insights/logDefinitions/read | 取得資源名稱的可用記錄 &lt;&gt; |
> | BotService/>botservices.cs/connections/providers/Microsoft. Insights/metricDefinitions/read | 建立或更新資源的診斷設定 |
> | BotService/>botservices.cs/providers/Microsoft. Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | BotService/>botservices.cs/providers/Microsoft. Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | BotService/>botservices.cs/providers/Microsoft. Insights/logDefinitions/read | 取得資源名稱的可用記錄 &lt;&gt; |
> | BotService/>botservices.cs/providers/Microsoft. Insights/metricDefinitions/read | 建立或更新資源的診斷設定 |
> | BotService/checknameavailability/providers/Microsoft. Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | BotService/checknameavailability/providers/Microsoft. Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | BotService/checknameavailability/providers/Microsoft. Insights/logDefinitions/read | 取得資源名稱的可用記錄 &lt;&gt; |
> | BotService/checknameavailability/providers/Microsoft. Insights/metricDefinitions/read | 建立或更新資源的診斷設定 |
> | BotService/listauthserviceproviders/providers/Microsoft. Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | BotService/listauthserviceproviders/providers/Microsoft. Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | BotService/listauthserviceproviders/providers/Microsoft. Insights/logDefinitions/read | 取得資源名稱的可用記錄 &lt;&gt; |
> | BotService/listauthserviceproviders/providers/Microsoft. Insights/metricDefinitions/read | 建立或更新資源的診斷設定 |
> | Microsoft.BotService/locations/operationresults/read | 讀取非同步作業的狀態 |
> | Microsoft.BotService/Operations/read | 讀取所有資源類型的作業 |

### <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

Azure 服務： [認知服務](../cognitive-services/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.CognitiveServices/register/action | 訂用帳戶註冊動作 |
> | Microsoft.CognitiveServices/register/action | 為認知服務註冊訂用帳戶 |
> | CognitiveServices/checkDomainAvailability/action | 讀取訂用帳戶的可用 Sku。 |
> | Microsoft.CognitiveServices/register/action | 為認知服務註冊訂用帳戶 |
> | Microsoft.CognitiveServices/register/action | 為認知服務註冊訂用帳戶 |
> | Microsoft.CognitiveServices/accounts/read | 讀取 API 帳戶。 |
> | Microsoft.CognitiveServices/accounts/write | 寫入 API 帳戶。 |
> | Microsoft.CognitiveServices/accounts/delete | 刪除 API 帳戶 |
> | Microsoft.CognitiveServices/accounts/listKeys/action | 列出金鑰 |
> | Microsoft.CognitiveServices/accounts/regenerateKey/action | 重新產生金鑰 |
> | CognitiveServices/accounts/privateEndpointConnectionProxies/read | 讀取私人端點連接。 |
> | CognitiveServices/accounts/privateEndpointConnectionProxies/write | 寫入私人端點連接。 |
> | CognitiveServices/accounts/privateEndpointConnectionProxies/delete | 刪除私人端點連接。 |
> | CognitiveServices/accounts/privateEndpointConnectionProxies/validate/action | 驗證私人端點連接。 |
> | CognitiveServices/accounts/privateEndpointConnections/read | 讀取私人端點連接。 |
> | CognitiveServices/accounts/privateEndpointConnections/write | 寫入私人端點連接。 |
> | CognitiveServices/accounts/privateEndpointConnections/delete | 刪除私人端點連接。 |
> | CognitiveServices/accounts/privateLinkResources/read | 讀取帳戶的私人連結資源。 |
> | Microsoft.CognitiveServices/accounts/skus/read | 讀取現有資源的可用 SKU。 |
> | Microsoft.CognitiveServices/accounts/usages/read | 取得現有資源的配額使用情況。 |
> | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 讀取訂用帳戶的可用 Sku。 |
> | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 讀取訂用帳戶的可用 Sku。 |
> | CognitiveServices/位置/deleteVirtualNetworkOrSubnets/動作 | 正在刪除 VirtualNetworks 或子網的 Microsoft 通知。 |
> | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 讀取訂用帳戶的可用 Sku。 |
> | CognitiveServices/位置/deleteVirtualNetworkOrSubnets/動作 | 正在刪除 VirtualNetworks 或子網的 Microsoft 通知。 |
> | CognitiveServices/位置/operationresults/讀取 | 讀取非同步作業的狀態。 |
> | Microsoft.CognitiveServices/Operations/read | 列出所有可用的作業 |
> | **DataAction** | **說明** |
> | CognitiveServices/accounts/AnomalyDetector/時間序列/整個/偵測/動作 | 這項作業會使用整個數列產生模型，且每個點都會使用相同的模型來偵測。<br>使用這個方法時，會使用特定點前後的點來判斷它是否為異常。<br>整個偵測可為使用者提供時間序列的整體狀態。 |
> | CognitiveServices/accounts/AnomalyDetector/時間序列/last/偵測/action | 這項作業會使用最新的點來產生模型。 使用這個方法時，只有歷程記錄點會用來判斷目標點是否為異常。 偵測到的最新點會符合即時監視商務計量的案例。 |
> | CognitiveServices/accounts/自動建議/search/action | 這種作業會針對給定的查詢或部分查詢提供建議。 |
> | CognitiveServices/accounts/ComputerVision/分析/動作 | 這項作業會根據影像內容來解壓縮一組豐富的視覺功能。  |
> | CognitiveServices/accounts/ComputerVision/說明/動作 | 這項作業會以具有完整句子的人類可讀語言產生影像的描述。<br> 描述是以內容標記的集合為基礎，這些標記也會由作業傳回。<br>每個映射可以產生一個以上的描述。<br> 描述依其信賴分數排序。<br>所有描述都是英文版。 |
> | CognitiveServices/accounts/ComputerVision/generatethumbnail/action | 此操作會產生具有使用者指定之寬度和高度的縮圖影像。<br> 根據預設，此服務會分析影像、識別相關區域 (ROI)，並根據 ROI 產生智慧裁剪的座標。<br> 當您指定與輸入影像不同的外觀比例時，智慧裁剪會有所説明 |
> | CognitiveServices/accounts/ComputerVision/ocr/action | 光學字元辨識 (OCR) 偵測影像中的文字，並將可辨識的字元解壓縮至可供電腦使用的字元資料流。    |
> | CognitiveServices/accounts/ComputerVision/recognizetext/action | 使用這個介面可取得辨識文字作業的結果。 當您使用辨識文字介面時，回應會包含名為 "Operation-Location" 的欄位。 [作業-位置] 欄位包含您必須用於「取得辨識文字」作業結果作業的 URL。 |
> | CognitiveServices/accounts/ComputerVision/tag/action | 這項作業會產生與提供之影像內容相關的單字或標記清單。<br>電腦視覺 API 可以根據在影像中找到的物件、客廳、景象或動作傳回標記。<br>不同于類別，標記不會根據階層式分類系統來組織，而是對應至影像內容。<br>標記可能包含可避免不明確或提供內容的提示，例如，"cello" 標記可能伴隨提示 "音樂檢測"。<br>所有標記都是英文版。 |
> | CognitiveServices/accounts/ComputerVision/areaofinterest/action | 這項作業會傳回影像最重要區域周圍的周框方塊。 |
> | CognitiveServices/accounts/ComputerVision/偵測/動作 | 此作業會在指定的映射上執行物件偵測。  |
> | CognitiveServices/accounts/ComputerVision/模型/讀取 | 這項作業會傳回電腦視覺 API 所支援的特定領域模型清單。  目前，API 支援下列特定領域模型：名人辨識器、地標辨識器。 |
> | CognitiveServices/accounts/ComputerVision/模型/分析/動作 | 這項作業會套用特定領域的模型，以辨識影像中的內容。<br> 您可以使用/models GET 要求來抓取電腦視覺 API 所支援的特定領域模型清單。<br> 目前，API 提供下列特定領域模型：名人、地標。 |
> | CognitiveServices/accounts/ComputerVision/read/分析/動作 | 您可以使用此介面來執行讀取作業，採用最先進的光學字元辨識 (OCR) 演算法針對大量文字檔進行優化。<br>它可以處理手寫、列印或混合檔。<br>當您使用讀取介面時，回應會包含名為「作業-位置」的標頭。<br>「作業-位置」標頭包含您必須用於「取得讀取」結果作業以存取 OCR 結果的 URL。 |
> | CognitiveServices/accounts/ComputerVision/read/analyzeresults/read | 您可以使用此介面來取得讀取作業的狀態和 OCR 結果。  讀取作業的 ' Operation-Location ' 回應標頭中會傳回包含 ' operationId ' 的 URL。 |
> | CognitiveServices/accounts/ComputerVision/read/core/asyncbatchanalyze/action | 使用此介面來取得批次讀取檔案作業的結果，並採用最先進的光學字元 |
> | CognitiveServices/accounts/ComputerVision/read/operations/read | 這個介面是用來取得讀取作業的 OCR 結果。 您應從批次讀取檔介面所傳回的 <b>[作業-位置]</b> 欄位中，取出此介面的 URL。 |
> | CognitiveServices/accounts/ComputerVision/textoperations/read | 這個介面是用來取得辨識文字作業結果。 您應從辨識文字介面所傳回的 <b>[作業-位置]</b> 欄位中，抓取此介面的 URL。 |
> | CognitiveServices/accounts/ContentModerator/imagelists/action | 建立影像清單。 |
> | CognitiveServices/accounts/ContentModerator/termlists/action | 建立字詞清單。 |
> | CognitiveServices/accounts/ContentModerator/imagelists/read | 影像清單-取得詳細資料-影像清單-取得全部 |
> | CognitiveServices/accounts/ContentModerator/imagelists/delete | 影像清單-刪除 |
> | CognitiveServices/accounts/ContentModerator/imagelists/refreshindex/action | 影像清單-重新整理搜尋索引 |
> | CognitiveServices/accounts/ContentModerator/imagelists/write | 影像清單-更新詳細資料 |
> | CognitiveServices/accounts/ContentModerator/imagelists/images/write | 將影像新增至您的影像清單。 使用影像/比對 API 時，影像清單可以用來對其他影像進行模糊比對。 |
> | CognitiveServices/accounts/ContentModerator/imagelists/images/delete | 刪除影像清單中的影像。 使用影像/比對 API 時，影像清單可以用來對其他影像進行模糊比對。 從清單中刪除所有影像。 使用影像/比對 API 時，影像清單可以用來對其他影像進行模糊比對。 * |
> | CognitiveServices/accounts/ContentModerator/imagelists/images/read | 影像-取得所有影像識別碼 |
> | CognitiveServices/accounts/ContentModerator/processimage/評估/動作 | 傳回包含猥褻或成人內容的影像機率。 |
> | CognitiveServices/accounts/ContentModerator/processimage/findfaces/action | 尋找影像中的臉部。 |
> | CognitiveServices/accounts/ContentModerator/processimage/match/action | 模糊會比對影像與您的其中一個自訂影像清單。 您可以使用此 API 來建立和管理自訂影像清單。  |
> | CognitiveServices/accounts/ContentModerator/processimage/ocr/action | 傳回在影像中找到的任何指定語言文字。 如果輸入中未指定任何語言，則偵測會預設為英文。 |
> | CognitiveServices/accounts/ContentModerator/processtext/detectlanguage/action | 這項作業會偵測到指定輸入內容的語言。 傳回包含所提交文字之主流語言的 ISO 639-3 程式碼。 支援110以上的語言。 |
> | CognitiveServices/accounts/ContentModerator/processtext/screen/action | 作業會偵測超過100種語言的不雅內容，並符合自訂和共用的 blocklists。 |
> | CognitiveServices/accounts/ContentModerator/團隊/工作/動作 | 將會針對這個端點上張貼的影像內容傳回作業識別碼。  |
> | CognitiveServices/accounts/ContentModerator/團隊/評論/動作 | 所建立的評論會顯示在您小組的審核者上。 當審核者完成審查時，評論的結果會張貼 (也就是 HTTP POST) 指定的 >callbackendpoint。 |
> | CognitiveServices/accounts/ContentModerator/團隊/jobs/read | 取得作業識別碼的作業詳細資料。 |
> | CognitiveServices/accounts/ContentModerator/團隊/評論/閱讀 | 傳回通過審核識別碼的評論詳細資料。 |
> | CognitiveServices/accounts/ContentModerator/團隊/評論/發佈/動作 | 影片評論最初是在未發行的狀態中建立，這表示您的小組審核者無法進行評論。 |
> | CognitiveServices/accounts/ContentModerator/團隊/評論/成績單/action | 此 API 會將影片中所說的所有單字文字 (文字版本的文字記錄檔，) 至影片評論。 檔案應為有效的 WebVTT 格式。 |
> | CognitiveServices/accounts/ContentModerator/團隊/評論/transcriptmoderationresult/action | 此 API 會新增影片評論的文字記錄畫面文字結果檔。 文字記錄畫面文字結果檔案是螢幕文字 API 的結果。 若要產生文字記錄畫面文字結果檔，必須使用螢幕文字 API 將文字記錄檔篩選為不雅內容。 |
> | CognitiveServices/accounts/ContentModerator/團隊/評論/accesskey/read | 取得小組的評論內容存取金鑰。 |
> | CognitiveServices/accounts/ContentModerator/團隊/評論/框架/寫 | 使用這個方法來新增影片評論的畫面格。 |
> | CognitiveServices/accounts/ContentModerator/團隊/評論/框架/讀取 | *NotDefined* |
> | CognitiveServices/accounts/ContentModerator/團隊/settings/templates/write | 建立或更新指定的範本 |
> | CognitiveServices/accounts/ContentModerator/小組/設定/範本/刪除 | 刪除小組中的範本 |
> | CognitiveServices/accounts/ContentModerator/團隊/settings/templates/read | 傳回這個小組所布建之審核範本的陣列。 |
> | CognitiveServices/accounts/ContentModerator/團隊/工作流程/撰寫 | 建立新的工作流程，或更新現有的工作流程。 |
> | CognitiveServices/accounts/ContentModerator/團隊/工作流程/讀取 | 取得小組中特定工作流程的詳細資料，以取得適用于您小組的所有工作流程 * |
> | CognitiveServices/accounts/ContentModerator/termlists/bulkupdate/action | 字詞清單-大量更新 |
> | CognitiveServices/accounts/ContentModerator/termlists/delete | 字詞清單-刪除 |
> | CognitiveServices/accounts/ContentModerator/termlists/read | 字詞清單-取得所有詞彙清單-取得詳細資料 |
> | CognitiveServices/accounts/ContentModerator/termlists/refreshindex/action | 字詞清單-重新整理搜尋索引 |
> | CognitiveServices/accounts/ContentModerator/termlists/write | 詞彙清單-更新詳細資料 |
> | CognitiveServices/accounts/ContentModerator/termlists/詞彙/write | 字詞-新增字詞 |
> | CognitiveServices/accounts/ContentModerator/termlists/詞彙/刪除 | 字詞-刪除詞彙-刪除所有詞彙 |
> | CognitiveServices/accounts/ContentModerator/termlists/詞彙/read | 字詞-取得所有字詞 |
> | CognitiveServices/accounts/CustomVision/projects/action | 建立專案。 |
> | CognitiveServices/accounts/CustomVision/user/action | *NotDefined* |
> | CognitiveServices/accounts/CustomVision/quota/action | *NotDefined* |
> | CognitiveServices/accounts/CustomVision. 預測/專案/動作 | 建立專案。 |
> | CognitiveServices/accounts/CustomVision. 預測/使用者/動作 | *NotDefined* |
> | CognitiveServices/accounts/CustomVision. 預測/配額/動作 | *NotDefined* |
> | CognitiveServices/accounts/CustomVision. 預測/分類/反復專案/影像/動作 | 分類影像並儲存結果。 |
> | CognitiveServices/accounts/CustomVision. 預測/分類/反復專案/url/動作 | 將影像 url 分類並儲存結果。 |
> | CognitiveServices/accounts/CustomVision. 預測/分類/反復專案/影像/nostore/動作 | 在不儲存結果的情況下分類影像。 |
> | CognitiveServices/accounts/CustomVision. 預測/分類/反復專案/url/nostore/action | 將影像 url 分類，而不儲存結果。 |
> | CognitiveServices/accounts/CustomVision. 預測/偵測/反復專案/影像/動作 | 偵測影像中的物件，並儲存結果。 |
> | CognitiveServices/accounts/CustomVision. 預測/偵測/反復專案/url/動作 | 偵測影像 url 中的物件，並儲存結果。 |
> | CognitiveServices/accounts/CustomVision. 預測/偵測/反復專案/影像/nostore/動作 | 偵測影像中的物件，而不儲存結果。 |
> | CognitiveServices/accounts/CustomVision. 預測/偵測/反復專案/url/nostore/action | 偵測影像 url 中的物件，而不儲存結果。 |
> | CognitiveServices/accounts/CustomVision. 預測/網域/讀取 | 取得特定網域的相關資訊。 取得可用網域的清單。 * |
> | CognitiveServices/accounts/CustomVision. 預測/labelproposals/設定/動作 | 設定標籤提案的集區大小。 |
> | CognitiveServices/accounts/CustomVision. 預測/labelproposals/設定/讀取 | 取得此專案之標籤提案的集區大小。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/遷移/動作 | *NotDefined* |
> | CognitiveServices/accounts/CustomVision. 預測/專案/影像/動作 | 此 API 會以多部分/表單資料和應用程式/八位資料流程的形式接受主體內容。 使用多部分時 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/標記/動作 | 建立專案的標記。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/刪除 | 刪除特定的專案。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/讀取 | 取得特定的專案。 取得您的專案。 * |
> | CognitiveServices/accounts/CustomVision. 預測/專案/訓練/動作 | 用於定型的佇列專案。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/寫入 | 更新特定的專案。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/匯入/動作 | 匯入專案。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/匯出/讀取 | 匯出專案。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/影像/區域/動作 | 此 API 接受一批映射區域和選擇性標記，以使用區域資訊來更新現有的映射。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/影像/檔案/動作 | 此 API 會接受批次的檔案和選擇性的標記，以建立影像。 其限制為 64 個影像和 20 個標記。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/影像/預測/動作 | 此 API 會從指定的預測影像建立一批映射。 其限制為 64 個影像和 20 個標記。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/影像/url/動作 | 此 API 會接受批次的 url 和選擇性標記，以建立影像。 其限制為 64 個影像和 20 個標記。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/影像/標記/動作 | 將一組影像與一組標記建立關聯。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/影像/刪除 | 從定型影像集中刪除影像。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/影像/regionproposals/動作 | 此 API 會取得映射的區域提案，以及區域的 confidences。 如果找不到任何提案，則會傳回空陣列。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/影像/建議/動作 | 此 API 將會提取依建議標記識別項篩選的未標記映射。 如果找不到影像，則會傳回空陣列。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/影像/識別碼/讀取 | 此 API 會傳回指定標記的一組影像，並選擇性地進行反復專案。 如果未指定任何反復專案， |
> | CognitiveServices/accounts/CustomVision. 預測/專案/影像/區域/刪除 | 刪除一組映射區域。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/影像/建議/計數/動作 | 此 API 會採用 Tagid 來取得每個建議標記的未標記影像計數，以獲得指定的閾值。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/影像/標記/讀取 | 此 API 支援批次處理和範圍選取。 依預設，它只會傳回符合影像的前50個影像。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/影像/標記/計數/讀取 | 篩選是在 and/or 關聯性上。 例如，如果提供的標記識別項適用于 "Dog" 和 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/影像/標記/刪除 | 從一組影像中移除一組標記。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/影像/未標記/讀取 | 此 API 支援批次處理和範圍選取。 依預設，它只會傳回符合影像的前50個影像。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/影像/未標記/計數/讀取 | 此 API 會傳回沒有指定專案之標記的影像，並選擇性地傳回反復專案。 如果未指定任何反復專案， |
> | CognitiveServices/accounts/CustomVision. 預測/專案/反復專案/刪除 | 刪除專案的特定反復專案。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/反復專案/匯出/動作 | 匯出已定型的反復專案。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/反復專案/讀取 | 取得特定的反復專案。 取得專案的反復專案。 * |
> | CognitiveServices/accounts/CustomVision. 預測/專案/反復專案/發行/動作 | 發行特定的反復專案。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/反復專案/寫入 | 更新特定的反復專案。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/反復專案/匯出/讀取 | 取得特定反復專案的匯出清單。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/反復專案/效能/讀取 | 取得有關反復專案的詳細效能資訊。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/反復專案/效能/影像/讀取 | 此 API 支援批次處理和範圍選取。 依預設，它只會傳回符合影像的前50個影像。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/反復專案/效能/影像/計數/讀取 | 篩選是在 and/or 關聯性上。 例如，如果提供的標記識別項適用于 "Dog" 和 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/反復專案/發行/刪除 | 取消發行特定的反復專案。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/預測/刪除 | 刪除一組預測的影像及其相關聯的預測結果。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/預測/查詢/動作 | 取得已傳送至您預測端點的影像。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/quicktest/影像/動作 | 快速測試映射。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/quicktest/url/action | 快速測試影像 url。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/標記/刪除 | 從專案中刪除標記。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/標記/讀取 | 取得特定標記的相關資訊。 取得指定專案和反復專案的標記。 * |
> | CognitiveServices/accounts/CustomVision. 預測/專案/標記/寫入 | 更新標記。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/tagsandregions/建議/動作 | 此 API 會針對未標記影像的陣列/批次，以及標記的 confidences 取得建議的標記和區域。 如果找不到標記，則會傳回空陣列。 |
> | CognitiveServices/accounts/CustomVision. 預測/專案/訓練/advanced/action | 使用 PipelineConfiguration 和定型類型進行定型的佇列專案。 |
> | CognitiveServices/accounts/CustomVision. 預測/配額/刪除 | *NotDefined* |
> | CognitiveServices/accounts/CustomVision. 預測/配額/重新整理/寫入 | *NotDefined* |
> | CognitiveServices/accounts/CustomVision. 預測/使用量/預測/使用者/讀取 | 取得 Oxford 使用者的預測資源使用量 |
> | CognitiveServices/accounts/CustomVision. 預測/使用量/訓練/資源/層/讀取 | 取得適用于 Azure 使用者的訓練資源使用量 |
> | CognitiveServices/accounts/CustomVision. 預測/使用量/訓練/使用者/讀取 | 取得 Oxford 使用者訓練資源的使用方式 |
> | CognitiveServices/accounts/CustomVision. 預測/使用者/刪除 | *NotDefined* |
> | CognitiveServices/accounts/CustomVision. 預測/使用者/狀態/寫入 | 更新使用者狀態 |
> | CognitiveServices/accounts/CustomVision. 預測/使用者/階層/寫入 | *NotDefined* |
> | CognitiveServices/accounts/CustomVision. 預測/使用者/讀取 | *NotDefined* |
> | CognitiveServices/accounts/CustomVision. 預測/白名單/刪除 | 刪除具有特定功能的 allowlisted 使用者 |
> | CognitiveServices/accounts/CustomVision. 預測/白名單/讀取 | 取得具有特定功能的 allowlisted 使用者清單 |
> | CognitiveServices/accounts/CustomVision. 預測/白名單/寫入 | 在具有特定功能的允許清單中更新或建立使用者 |
> | CognitiveServices/accounts/CustomVision/分類/反復專案/影像/動作 | 分類影像並儲存結果。 |
> | CognitiveServices/accounts/CustomVision/分類/反復專案/url/動作 | 將影像 url 分類並儲存結果。 |
> | CognitiveServices/accounts/CustomVision/分類/反復專案/影像/nostore/動作 | 在不儲存結果的情況下分類影像。 |
> | CognitiveServices/accounts/CustomVision/分類/反復專案/url/nostore/action | 將影像 url 分類，而不儲存結果。 |
> | CognitiveServices/accounts/CustomVision/偵測/反復專案/影像/動作 | 偵測影像中的物件，並儲存結果。 |
> | CognitiveServices/accounts/CustomVision/偵測/反復專案/url/action | 偵測影像 url 中的物件，並儲存結果。 |
> | CognitiveServices/accounts/CustomVision/偵測/反復專案/影像/nostore/動作 | 偵測影像中的物件，而不儲存結果。 |
> | CognitiveServices/accounts/CustomVision/偵測/反復專案/url/nostore/action | 偵測影像 url 中的物件，而不儲存結果。 |
> | CognitiveServices/accounts/CustomVision/網域/讀取 | 取得特定網域的相關資訊。 取得可用網域的清單。 * |
> | CognitiveServices/accounts/CustomVision/labelproposals/設定/動作 | 設定標籤提案的集區大小。 |
> | CognitiveServices/accounts/CustomVision/labelproposals/設定/讀取 | 取得此專案之標籤提案的集區大小。 |
> | CognitiveServices/accounts/CustomVision/project/遷移/動作 | *NotDefined* |
> | CognitiveServices/accounts/CustomVision/projects/images/action | 此 API 會以多部分/表單資料和應用程式/八位資料流程的形式接受主體內容。 使用多部分時 |
> | CognitiveServices/accounts/CustomVision/projects/tag/action | 建立專案的標記。 |
> | CognitiveServices/accounts/CustomVision/projects/delete | 刪除特定的專案。 |
> | CognitiveServices/accounts/CustomVision/projects/read | 取得特定的專案。 取得您的專案。 * |
> | CognitiveServices/accounts/CustomVision/projects/訓練/動作 | 用於定型的佇列專案。 |
> | CognitiveServices/accounts/CustomVision/projects/write | 更新特定的專案。 |
> | CognitiveServices/accounts/CustomVision/projects/import/action | 匯入專案。 |
> | CognitiveServices/accounts/CustomVision/projects/構件/read | 根據 blob 中的成品相對路徑，從 blob 儲存體取得成品內容。 |
> | CognitiveServices/accounts/CustomVision/projects/export/read | 匯出專案。 |
> | CognitiveServices/accounts/CustomVision/專案/影像/區域/動作 | 此 API 接受一批映射區域和選擇性標記，以使用區域資訊來更新現有的映射。 |
> | CognitiveServices/accounts/CustomVision/projects/images/files/action | 此 API 會接受批次的檔案和選擇性的標記，以建立影像。 其限制為 64 個影像和 20 個標記。 |
> | CognitiveServices/accounts/CustomVision/projects/images/預測/動作 | 此 API 會從指定的預測影像建立一批映射。 其限制為 64 個影像和 20 個標記。 |
> | CognitiveServices/accounts/CustomVision/projects/images/url/action | 此 API 會接受批次的 url 和選擇性標記，以建立影像。 其限制為 64 個影像和 20 個標記。 |
> | CognitiveServices/accounts/CustomVision/projects/images/tag/action | 將一組影像與一組標記建立關聯。 |
> | CognitiveServices/accounts/CustomVision/projects/images/delete | 從定型影像集中刪除影像。 |
> | CognitiveServices/accounts/CustomVision/projects/images/regionproposals/action | 此 API 會取得映射的區域提案，以及區域的 confidences。 如果找不到任何提案，則會傳回空陣列。 |
> | CognitiveServices/accounts/CustomVision/projects/images/建議/動作 | 此 API 將會提取依建議標記識別項篩選的未標記映射。 如果找不到影像，則會傳回空陣列。 |
> | CognitiveServices/accounts/CustomVision/projects/images/read | 此 API 支援批次處理和範圍選取。 依預設，它只會傳回符合影像的前50個影像。 |
> | CognitiveServices/accounts/CustomVision/projects/images/metadata/action | 此 API 接受一批映射識別碼和中繼資料來更新映射。 最大值為64的影像。 |
> | CognitiveServices/accounts/CustomVision/projects/images/count/read | 篩選是在 and/or 關聯性上。 例如，如果提供的標記識別項適用于 "Dog" 和 |
> | CognitiveServices/accounts/CustomVision/projects/images/id/read | 此 API 會傳回指定標記的一組影像，並選擇性地進行反復專案。 如果未指定任何反復專案， |
> | CognitiveServices/accounts/CustomVision/專案/影像/區域/刪除 | 刪除一組映射區域。 |
> | CognitiveServices/accounts/CustomVision/projects/images/建議/計數/動作 | 此 API 會採用 Tagid 來取得每個建議標記的未標記影像計數，以獲得指定的閾值。 |
> | CognitiveServices/accounts/CustomVision/projects/images/tag/read | 此 API 支援批次處理和範圍選取。 依預設，它只會傳回符合影像的前50個影像。 |
> | CognitiveServices/accounts/CustomVision/projects/images/tag/count/read | 篩選是在 and/or 關聯性上。 例如，如果提供的標記識別項適用于 "Dog" 和 |
> | CognitiveServices/accounts/CustomVision/projects/images/tag/delete | 從一組影像中移除一組標記。 |
> | CognitiveServices/accounts/CustomVision/projects/images/未標記/讀取 | 此 API 支援批次處理和範圍選取。 依預設，它只會傳回符合影像的前50個影像。 |
> | CognitiveServices/accounts/CustomVision/projects/images/未標記/計數/讀取 | 此 API 會傳回沒有指定專案之標記的影像，並選擇性地傳回反復專案。 如果未指定任何反復專案， |
> | CognitiveServices/accounts/CustomVision/projects/反復專案/刪除 | 刪除專案的特定反復專案。 |
> | CognitiveServices/accounts/CustomVision/projects/反復專案/匯出/動作 | 匯出已定型的反復專案。 |
> | CognitiveServices/accounts/CustomVision/projects/反復專案/讀取 | 取得特定的反復專案。 取得專案的反復專案。 * |
> | CognitiveServices/accounts/CustomVision/projects/反復專案/發行/動作 | 發行特定的反復專案。 |
> | CognitiveServices/accounts/CustomVision/projects/反復專案/寫入 | 更新特定的反復專案。 |
> | CognitiveServices/accounts/CustomVision/projects/反覆運算/匯出/讀取 | 取得特定反復專案的匯出清單。 |
> | CognitiveServices/accounts/CustomVision/projects/反復專案/效能/讀取 | 取得有關反復專案的詳細效能資訊。 |
> | CognitiveServices/accounts/CustomVision/projects/反復專案/效能/影像/讀取 | 此 API 支援批次處理和範圍選取。 依預設，它只會傳回符合影像的前50個影像。 |
> | CognitiveServices/accounts/CustomVision/projects/反覆運算/效能/影像/計數/讀取 | 篩選是在 and/or 關聯性上。 例如，如果提供的標記識別項適用于 "Dog" 和 |
> | CognitiveServices/accounts/CustomVision/projects/反復專案/發行/刪除 | 取消發行特定的反復專案。 |
> | CognitiveServices/accounts/CustomVision/projects/預測/刪除 | 刪除一組預測的影像及其相關聯的預測結果。 |
> | CognitiveServices/accounts/CustomVision/projects/預測/查詢/動作 | 取得已傳送至您預測端點的影像。 |
> | CognitiveServices/accounts/CustomVision/projects/quicktest/image/action | 快速測試映射。 |
> | CognitiveServices/accounts/CustomVision/projects/quicktest/url/action | 快速測試影像 url。 |
> | CognitiveServices/accounts/CustomVision/projects/tag/delete | 從專案中刪除標記。 |
> | CognitiveServices/accounts/CustomVision/projects/tag/read | 取得特定標記的相關資訊。 取得指定專案和反復專案的標記。 * |
> | CognitiveServices/accounts/CustomVision/projects/tag/write | 更新標記。 |
> | CognitiveServices/accounts/CustomVision/projects/tagsandregions/建議/動作 | 此 API 會針對未標記影像的陣列/批次，以及標記的 confidences 取得建議的標記和區域。 如果找不到標記，則會傳回空陣列。 |
> | CognitiveServices/accounts/CustomVision/projects/訓練/advanced/action | 使用 PipelineConfiguration 和定型類型進行定型的佇列專案。 |
> | CognitiveServices/accounts/CustomVision/quota/delete | *NotDefined* |
> | CognitiveServices/accounts/CustomVision/quota/refresh/write | *NotDefined* |
> | CognitiveServices/accounts/CustomVision/usage/預測/使用者/讀取 | 取得 Oxford 使用者的預測資源使用量 |
> | CognitiveServices/accounts/CustomVision/使用量/訓練/資源/層/讀取 | 取得適用于 Azure 使用者的訓練資源使用量 |
> | CognitiveServices/accounts/CustomVision/usage/訓練/使用者/讀取 | 取得 Oxford 使用者訓練資源的使用方式 |
> | CognitiveServices/accounts/CustomVision/user/delete | *NotDefined* |
> | CognitiveServices/accounts/CustomVision/user/state/write | 更新使用者狀態 |
> | CognitiveServices/accounts/CustomVision/user/分層/write | *NotDefined* |
> | CognitiveServices/accounts/CustomVision/users/read | *NotDefined* |
> | CognitiveServices/accounts/CustomVision/白名單/刪除 | 刪除具有特定功能的 allowlisted 使用者 |
> | CognitiveServices/accounts/CustomVision/白名單/read | 取得具有特定功能的 allowlisted 使用者清單 |
> | CognitiveServices/accounts/CustomVision/列入允許清單/寫入 | 在具有特定功能的允許清單中更新或建立使用者 |
> | CognitiveServices/accounts/EntitySearch/search/action | 取得指定查詢的實體並放置結果。 |
> | CognitiveServices/帳戶/臉部/偵測/動作 | 偵測影像中的人臉、退回臉部矩形，以及選擇性地使用 faceIds、地標和屬性。 |
> | CognitiveServices/accounts/臉部/findsimilars/action | 指定查詢臉部的 faceId，從 faceId 陣列、臉部清單或大型臉部清單中搜尋類似外觀的臉部。 faceId |
> | CognitiveServices/帳戶/臉部/群組/動作 | 根據臉部相似性將候選臉部分割成群組。 |
> | CognitiveServices/帳戶/臉部/識別/動作 | 一對多識別，以找出人員群組或大型人員群組中特定查詢人員臉部的最相符專案。 |
> | CognitiveServices/帳戶/臉部/驗證/動作 | 確認兩個臉部屬於同一個人，還是一個臉部屬於某個人。 |
> | CognitiveServices/accounts/臉部/facelist/write | 使用使用者指定的 faceListId、名稱和選擇性的 userData 來建立空白的臉部清單。 最多64個臉部清單可以更新臉部清單的資訊，包括名稱和 userData。 |
> | CognitiveServices/accounts/臉部/facelist/delete | 刪除指定的臉部清單。 臉部清單中的相關臉部影像也會一併刪除。 |
> | CognitiveServices/accounts/臉部/facelist/read | 取出臉部清單中的 faceListId、姓名、userData 和臉部。 列出臉部清單的 faceListId、名稱和 userData。 |
> | CognitiveServices/accounts/臉部/facelist/persistedfaces/write | 將臉部新增至指定的臉部清單，最多1000個臉部。 |
> | CognitiveServices/accounts/臉部/facelist/persistedfaces/delete | 藉由指定的 faceListId 和 persisitedFaceId，從臉部清單中刪除臉部。 也會刪除相關的臉部影像。 |
> | CognitiveServices/accounts/臉部/largefacelists/write | 使用使用者指定的 largeFaceListId、名稱和選擇性的 userData 來建立空白的大型臉部清單。 更新大型臉部清單的資訊，包括名稱和 userData。 |
> | CognitiveServices/accounts/臉部/largefacelists/delete | 刪除指定的大型臉部清單。 大型臉部清單中的相關臉部影像也會一併刪除。 |
> | CognitiveServices/accounts/臉部/largefacelists/read | 取出大型臉部清單的 largeFaceListId、名稱、userData。 列出 largeFaceListId、name 和 userData 等大型臉部清單的資訊。 |
> | CognitiveServices/accounts/臉部/largefacelists/訓練/動作 | 提交大型臉部清單定型工作。 定型是一項重要的步驟，只有經過訓練的大型臉部清單可以使用。 |
> | CognitiveServices/accounts/臉部/largefacelists/persistedfaces/write | 將臉部新增至指定的大型臉部清單，最多1000000個臉部。 依 persistedFaceId，在大型臉部清單中更新指定臉部的 userData 欄位。 |
> | CognitiveServices/accounts/臉部/largefacelists/persistedfaces/delete | 藉由指定的 largeFaceListId 和 persisitedFaceId，從大型臉部清單中刪除臉部。 也會刪除相關的臉部影像。 |
> | CognitiveServices/accounts/臉部/largefacelists/persistedfaces/read | 依 largeFaceListId 和 persistedFaceId 取出大型臉部清單中的保存臉部。 在指定的大型臉部清單中列出臉部的 persistedFaceId 和 userData。 |
> | CognitiveServices/accounts/臉部/largefacelists/訓練/閱讀 | 以檢查大型臉部清單定型狀態已完成或仍在進行中。 LargeFaceList 訓練是非同步作業 |
> | CognitiveServices/accounts/臉部/largepersongroups/write | 使用使用者指定的 largePersonGroupId、名稱和選擇性的 userData 來建立新的大型人員群組。 更新現有的大型人員群組名稱和 userData。 如果屬性不在要求主體中，這些屬性會保持不變。 |
> | CognitiveServices/accounts/臉部/largepersongroups/delete | 使用指定的 personGroupId 來刪除現有的大型人員群組。 將會刪除此大型人員群組中的保存資料。 |
> | CognitiveServices/accounts/臉部/largepersongroups/read | 取出大型人員群組的資訊，包括其名稱和 userData。 此 API 會傳回大型人員群組資訊清單，列出所有現有的大型人員群組的 largePesonGroupId、名稱和 userData。 |
> | CognitiveServices/accounts/臉部/largepersongroups/訓練/動作 | 提交大型人員群組定型工作。 定型是只有經過訓練的大型人員群組可以使用的重要步驟。 |
> | CognitiveServices/accounts/臉部/largepersongroups/人員/動作 | 在指定的大型人員群組中建立新的人員。 若要將臉部新增至此人，請致電 |
> | CognitiveServices/accounts/臉部/largepersongroups/人員/刪除 | 從大型人員群組中刪除現有的人員。 所有儲存的人員資料和 person 專案中的臉部影像都將被刪除。 |
> | CognitiveServices/accounts/臉部/largepersongroups/person/read | 取出人員的名稱和 userData，以及代表註冊的人臉影像的保存 faceIds。 列出指定之大型人員群組中的所有人員資訊，包括 personId、name、userData 和 persistedFaceIds。 |
> | CognitiveServices/accounts/臉部/largepersongroups/人員/撰寫 | 更新人員的名稱或 userData。 |
> | CognitiveServices/accounts/臉部/largepersongroups/person/persistedfaces/write | 將臉部影像新增至個人的大型人員群組，以進行臉部識別或驗證。 若要處理更新已保存臉部之人為 userData 欄位的影像。 |
> | CognitiveServices/accounts/臉部/largepersongroups/人員/persistedfaces/刪除 | 從大型人員群組中的人員刪除臉部。 也會一併刪除與這個臉部專案相關的臉部資料和影像。 |
> | CognitiveServices/accounts/臉部/largepersongroups/person/persistedfaces/read | 取出人員臉部資訊。 持續性人員臉部是由其 largePersonGroupId、personId 和 persistedFaceId 所指定。 |
> | CognitiveServices/accounts/臉部/largepersongroups/訓練/閱讀 | 檢查大型人員群組定型狀態已完成或仍在進行中。 LargePersonGroup 訓練是非同步作業 |
> | CognitiveServices/accounts/臉部/persongroup/write | 使用指定的 personGroupId、名稱和使用者提供的 userData 來建立新的人員群組。 更新現有人員群組的名稱和 userData。 如果屬性不在要求主體中，這些屬性會保持不變。 |
> | CognitiveServices/accounts/臉部/persongroup/delete | 刪除具有指定 personGroupId 的現有人員群組。 將會刪除此 person 群組中的保存資料。 |
> | CognitiveServices/accounts/臉部/persongroup/read | 取出 person 組名和 userData。 若要取得此 personGroup 下的人員資訊，請使用列出人員群組的 pesonGroupId、名稱和 userData。 |
> | CognitiveServices/accounts/臉部/persongroup/訓練/動作 | 提交人員群組定型工作。 定型是只有已定型的人員群組可以使用的重要步驟。 |
> | CognitiveServices/accounts/臉部/persongroup/人員/動作 | 在指定的人員群組中建立新的人員。 若要將臉部新增至此人，請致電 |
> | CognitiveServices/accounts/臉部/persongroup/人員/刪除 | 從 person 群組刪除現有的人員。 所有儲存的人員資料和 person 專案中的臉部影像都將被刪除。 |
> | CognitiveServices/accounts/臉部/persongroup/person/read | 取出人員的名稱和 userData，以及代表註冊的人臉影像的保存 faceIds。 列出指定人員群組中的所有人員資訊，包括 personId、name、userData 和已註冊的 persistedFaceIds。 |
> | CognitiveServices/accounts/臉部/persongroup/人員/撰寫 | 更新人員的名稱或 userData。 |
> | CognitiveServices/accounts/臉部/persongroup/person/persistedfaces/write | 將臉部影像新增至人員群組，以進行臉部辨識或驗證。 若要處理多個更新的影像，人員已保存臉部的 userData 欄位。 |
> | CognitiveServices/accounts/臉部/persongroup/人員/persistedfaces/刪除 | 從 person 群組中的人員刪除臉部。 也會一併刪除與這個臉部專案相關的臉部資料和影像。 |
> | CognitiveServices/accounts/臉部/persongroup/person/persistedfaces/read | 取出人員臉部資訊。 持續性人員臉部是由其 personGroupId、personId 和 persistedFaceId 所指定。 |
> | CognitiveServices/accounts/臉部/persongroup/訓練/閱讀 | 檢查人員群組定型狀態已完成或仍在進行中。 PersonGroup 訓練是觸發的非同步作業 |
> | CognitiveServices/帳戶/臉部/快照/採取/動作 | 取得物件的快照集。 |
> | CognitiveServices/帳戶/臉部/快照/讀取 | 取得快照集作業的狀態。 |
> | CognitiveServices/帳戶/臉部/快照/申請/動作 | 套用快照集，並提供使用者指定的物件識別碼。 |
> | CognitiveServices/帳戶/臉部/快照/刪除 | 刪除快照集。 |
> | CognitiveServices/帳戶/臉部/快照/寫入 | 更新快照集的屬性。 |
> | CognitiveServices/帳戶/臉部/快照/讀取 | 列出所有使用者可存取的快照集，並提供資訊。 * |
> | CognitiveServices/accounts/FormRecognizer/自訂/訓練/動作 | 建立和定型自訂模型。<br>定型要求必須包含可外部存取 Azure 儲存體 blob 容器 Uri 的來源參數 (最好是共用存取簽章 Uri) 或本機掛接之磁片磁碟機中的資料檔案夾的有效路徑。<br>指定本機路徑時，必須遵循 Linux/Unix 路徑格式，而且必須是根目錄為輸入裝載設定的絕對路徑。 |
> | CognitiveServices/accounts/FormRecognizer/自訂/模型/動作 | 建立和定型自訂模型。<br>要求必須包含可從外部存取的 Azure 儲存體 blob 容器 Uri 的來源參數， (最好是共用存取簽章 Uri) 或本機掛接之磁片磁碟機中的資料檔案夾的有效路徑。<br>指定本機路徑時，它們必須遵循 Linux/Unix 路徑格式，而且必須是根目錄為輸入裝載設定值的絕對路徑，例如，如果 ' {mount： Input} ' 設定值為 '/input '，則有效的來源路徑會是 '/input/contosodataset '。<br>所有要定型的資料都應該位於其下的來源資料夾或子資料夾下。<br>模型會使用下列內容類型的檔進行定型： ' application/pdf '、' image/jpeg '、' image/png '、' image/tiff '。<br>其他類型的內容則會被忽略。 |
> | CognitiveServices/accounts/FormRecognizer/自訂/模型/分析/動作 | 從指定的檔中，將索引鍵/值組解壓縮。 輸入檔必須是其中一個支援的內容類型-' application/pdf '、' image/jpeg ' 或 ' image/png '。 成功的回應會以 JSON 的形式傳回。 |
> | CognitiveServices/accounts/FormRecognizer/自訂/模型/刪除 | 刪除模型成品。 |
> | CognitiveServices/accounts/FormRecognizer/自訂/模型/讀取 | 取得模型的相關資訊。 |
> | CognitiveServices/accounts/FormRecognizer/自訂/模型/copyauthorization/action | 產生授權承載，以複製目標表單辨識器資源的模型。 |
> | CognitiveServices/accounts/FormRecognizer/自訂/模型/複製/動作 | 將自訂表單辨識器模型從一個訂用帳戶複製到另一個。<br>藉由 `modelId` 使用此 API 搭配查詢字串，從目標端點取得權杖來開始處理常式 `source=false` 。<br>然後， `modelId` 在要求主體中傳遞參考以及其他目標資源資訊。 |
> | CognitiveServices/accounts/FormRecognizer/自訂/模型/分析/動作 | 從指定的檔中，將索引鍵/值組、資料表和語義值解壓縮。<br>輸入檔必須是其中一個支援的內容類型-' application/pdf '、' image/jpeg '、' image/png ' 或 ' image/tiff '。<br>或者，使用 ' application/json ' 類型來指定要分析之檔的 Url 位置。 |
> | CognitiveServices/accounts/FormRecognizer/自訂/模型/複製/動作 | 將自訂表單辨識器模型複製到目標表單辨識器資源。 叫用此操作之前，您必須先取得要複製到其中的授權  |
> | CognitiveServices/accounts/FormRecognizer/自訂/模型/刪除 | 標記要刪除的模型。 模型成品將在48小時內永久移除。 |
> | CognitiveServices/accounts/FormRecognizer/自訂/模型/copyauthorization/action | 產生模型複製作業的授權承載。 針對目標表單辨識器資源端點呼叫這項作業  |
> | CognitiveServices/accounts/FormRecognizer/自訂/模型/讀取 | 取得自訂模型的詳細資訊。 取得所有自訂模型的相關資訊 |
> | CognitiveServices/accounts/FormRecognizer/自訂/模型/analyzeresults/read | 取得 [分析表單] 作業的目前狀態和結果。 |
> | CognitiveServices/accounts/FormRecognizer/自訂/模型/analyzeresults/read | 取得 [分析表單] 作業的目前狀態和結果。 |
> | CognitiveServices/accounts/FormRecognizer/自訂/模型/copyresults/read | 取得自訂表單模型複製作業的目前狀態和結果。 |
> | CognitiveServices/accounts/FormRecognizer/自訂/模型/copyresults/read | 取得自訂表單模型複製作業的目前狀態和結果。 |
> | CognitiveServices/accounts/FormRecognizer/自訂/模型/金鑰/讀取 | 取得模型的索引鍵。 |
> | CognitiveServices/accounts/FormRecognizer/layout/分析/動作 | 從指定的檔中解壓縮文字和版面配置資訊。<br>輸入檔必須是其中一個支援的內容類型-' application/pdf '、' image/jpeg '、' image/png ' 或 ' image/tiff '。<br>或者，使用 ' application/json ' 類型來指定要分析之檔的 Url 位置。 |
> | CognitiveServices/accounts/FormRecognizer/layout/analyzeresults/read | 追蹤進度並取得分析版面配置作業的結果 |
> | CognitiveServices/accounts/FormRecognizer/預建/名片/分析/動作 | 從指定的名片檔中，將欄位文字和語義值解壓縮。  |
> | CognitiveServices/accounts/FormRecognizer/預建/名片/analyzeresults/read | 查詢狀態，並取得「分析商務智慧卡」作業的結果。 您可以從「分析名片」回應中的「操作位置」標頭取得此介面的 URL。 |
> | CognitiveServices/accounts/FormRecognizer/預建/收據/asyncbatchanalyze/action | 從指定的收據檔中，將欄位文字和語義值解壓縮。 輸入檔必須是其中一個支援的 |
> | CognitiveServices/accounts/FormRecognizer/預建/收據/analyzeresults/read | 查詢狀態，並取得分析收據作業的結果。 您可以從「分析收據」回應中的「操作位置」標頭取得此介面的 URL。 |
> | CognitiveServices/accounts/FormRecognizer/預建/接收/作業/讀取 | 查詢狀態，並取得分析收據作業的結果。 您可以從「分析收據」回應中的「操作位置」標頭取得此介面的 URL。 |
> | CognitiveServices/accounts/ImageSearch/details/action | 傳回影像的相關見解，例如包含影像的網頁。 |
> | CognitiveServices/accounts/ImageSearch/search/action | 取得指定查詢的相關影像。 |
> | CognitiveServices/accounts/ImageSearch/趨勢/動作 | 取得目前的趨勢影像。 |
> | CognitiveServices/accounts/ImmersiveReader/getcontentmodelforreader/action | 建立沈浸式閱讀程式會話 |
> | CognitiveServices/accounts/InkRecognizer/辨識/動作 | 假設有一組筆觸資料會分析內容，並產生可辨識的實體清單，包括已辨識的文字。 |
> | CognitiveServices/accounts/LUIS/predict/action | 取得指定查詢的已發行端點預測。 |
> | CognitiveServices/accounts/MetricsAdvisor/alert/異常/設定/寫入 | 建立或更新異常警示設定 |
> | CognitiveServices/accounts/MetricsAdvisor/alert/異常/設定/刪除 | 刪除異常警示設定 |
> | CognitiveServices/accounts/MetricsAdvisor/alert/異常/設定/讀取 | 查詢單一異常警示設定 |
> | CognitiveServices/accounts/MetricsAdvisor/alert/異常/設定/alerts/query/action | 異常警示設定下的查詢警示 |
> | CognitiveServices/accounts/MetricsAdvisor/alert/異常/設定/警示/異常/讀取 | 在特定警示下查詢異常 |
> | CognitiveServices/accounts/MetricsAdvisor/alert/異常/設定/警示/事件/讀取 | 在特定警示下查詢事件 |
> | CognitiveServices/accounts/MetricsAdvisor/datafeeds/write | 建立或更新資料摘要。 |
> | CognitiveServices/accounts/MetricsAdvisor/datafeeds/delete | 刪除資料摘要 |
> | CognitiveServices/accounts/MetricsAdvisor/datafeeds/read | 依識別碼取得資料摘要，或列出所有資料摘要 |
> | CognitiveServices/accounts/MetricsAdvisor/datafeeds/ingestionprogress/read | 取得資料上一次成功的內嵌工作時間戳（依資料摘要） |
> | CognitiveServices/accounts/MetricsAdvisor/datafeeds/ingestionprogress/reset/action | 將資料摘要的資料內嵌狀態重設為回填資料 |
> | CognitiveServices/accounts/MetricsAdvisor/datafeeds/ingestionstatus/query/action | 依資料摘要取得資料內嵌狀態 |
> | CognitiveServices/accounts/MetricsAdvisor/擴充/anomalydetection/configuration/write | 建立或更新異常偵測設定 |
> | CognitiveServices/accounts/MetricsAdvisor/擴充/anomalydetection/設定/刪除 | 刪除異常偵測設定 |
> | CognitiveServices/accounts/MetricsAdvisor/擴充/anomalydetection/configuration/read | 查詢單一異常偵測設定 |
> | CognitiveServices/accounts/MetricsAdvisor/擴充/anomalydetection/configuration/alert/異常/設定/讀取 | 查詢特定異常偵測設定的所有異常警示設定 |
> | CognitiveServices/accounts/MetricsAdvisor/擴充/anomalydetection/configuration/異常/查詢/動作 | 異常偵測設定下的查詢異常 |
> | CognitiveServices/accounts/MetricsAdvisor/擴充/anomalydetection/configuration/異常/dimension/query/action | 異常的查詢維度值 |
> | CognitiveServices/accounts/MetricsAdvisor/擴充/anomalydetection/configuration/事件/查詢/動作 | 在異常偵測設定下查詢事件 |
> | CognitiveServices/accounts/MetricsAdvisor/擴充/anomalydetection/configuration/事件/rootcause/read | 查詢事件的根本原因 |
> | CognitiveServices/accounts/MetricsAdvisor/擴充/anomalydetection/configuration/series/query/action | 異常偵測所擴充的查詢系列 |
> | CognitiveServices/accounts/MetricsAdvisor/回饋/公制/write | 建立新的度量意見反應 |
> | CognitiveServices/accounts/MetricsAdvisor/意見反應/度量/讀取 | 依識別碼取得度量意見反應 |
> | CognitiveServices/accounts/MetricsAdvisor/意見反應/度量/查詢/動作 | 列出指定度量的意見反應 |
> | CognitiveServices/accounts/MetricsAdvisor/勾點/寫入 | 建立或更新勾點 |
> | CognitiveServices/accounts/MetricsAdvisor/勾點/刪除 | 刪除勾點 |
> | CognitiveServices/accounts/MetricsAdvisor/勾點/讀取 | 依識別碼或列出所有勾點來取得攔截 |
> | CognitiveServices/accounts/MetricsAdvisor/計量/資料/查詢/動作 | 取得度量的時間序列資料 |
> | CognitiveServices/accounts/MetricsAdvisor/計量/維度/查詢/動作 | 從特定度量列出維度 |
> | CognitiveServices/accounts/MetricsAdvisor/計量/擴充/anomalydetection/設定/讀取 | 查詢特定度量的所有異常偵測設定 |
> | CognitiveServices/accounts/MetricsAdvisor/計量/系列/查詢/動作 | 從計量)  (維度組合的清單系列 |
> | CognitiveServices/accounts/MetricsAdvisor/計量/status/擴充/anomalydetection/query/action | 查詢異常偵測狀態 |
> | CognitiveServices/accounts/MetricsAdvisor/stats/最新/讀取 | 取得最新的使用統計資料 |
> | CognitiveServices/accounts/NewsSearch/categorysearch/action | 傳回所提供分類的新聞。 |
> | CognitiveServices/accounts/NewsSearch/search/action | 取得與指定查詢相關的新聞文章。 |
> | CognitiveServices/accounts/NewsSearch/trendingtopics/action | 取得 Bing 所識別的趨勢主題。 這些是位於 Bing 首頁底部橫幅中的相同主題。 |
> | CognitiveServices/accounts/QnAMaker/root/action | QnA Maker |
> | CognitiveServices/accounts/QnAMaker. v2/root/action | QnA Maker |
> | CognitiveServices/accounts/QnAMaker。 v2/變更/讀取 | 從執行時間下載變更。 |
> | CognitiveServices/accounts/QnAMaker v2/變更/寫入 | 取代變更資料。 |
> | CognitiveServices/accounts/QnAMaker. v2/endpointkeys/read | 取得端點的端點金鑰 |
> | CognitiveServices/accounts/QnAMaker. v2/endpointkeys/refreshkeys/action | 重新產生端點金鑰。 |
> | CognitiveServices/accounts/QnAMaker. v2/endpointsettings/read | 取得端點的端點設定 |
> | CognitiveServices/accounts/QnAMaker. v2/endpointsettings/write | 更新端點的端點 seettings。 |
> | CognitiveServices/accounts/QnAMaker. v2/知識庫/publish/action | 將知識庫的測試索引中的所有變更發佈至其生產索引。 |
> | CognitiveServices/accounts/QnAMaker. v2/知識庫/delete | 刪除知識庫及其所有資料。 |
> | CognitiveServices/accounts/QnAMaker. v2/知識庫/read | 取得特定 knowledgebaser 的知識庫清單或詳細資料。 |
> | CognitiveServices/accounts/QnAMaker. v2/知識庫/write | 用來修改知識庫或取代知識庫內容的非同步作業。 |
> | CognitiveServices/accounts/QnAMaker. v2/知識庫/generateanswer/action | GenerateAnswer 呼叫以查詢知識庫。 |
> | CognitiveServices/accounts/QnAMaker. v2/知識庫/訓練/action | 將呼叫定型以將建議新增至知識庫。 |
> | CognitiveServices/accounts/QnAMaker. v2/知識庫/create/write | 建立新知識庫的非同步作業。 |
> | CognitiveServices/accounts/QnAMaker. v2/知識庫/下載/讀取 | 下載知識庫。 |
> | CognitiveServices/accounts/QnAMaker v2/operations/read | 取得特定長時間執行作業的詳細資料。 |
> | CognitiveServices/accounts/QnAMaker/變更/讀取 | 從執行時間下載變更。 |
> | CognitiveServices/accounts/QnAMaker/變更/寫入 | 取代變更資料。 |
> | CognitiveServices/accounts/QnAMaker/endpointkeys/read | 取得端點的端點金鑰 |
> | CognitiveServices/accounts/QnAMaker/endpointkeys/refreshkeys/action | 重新產生端點金鑰。 |
> | CognitiveServices/accounts/QnAMaker/endpointsettings/read | 取得端點的端點設定 |
> | CognitiveServices/accounts/QnAMaker/endpointsettings/write | 更新端點的端點 seettings。 |
> | CognitiveServices/accounts/QnAMaker/知識庫/publish/action | 將知識庫的測試索引中的所有變更發佈至其生產索引。 |
> | CognitiveServices/accounts/QnAMaker/知識庫/delete | 刪除知識庫及其所有資料。 |
> | CognitiveServices/accounts/QnAMaker/知識庫/read | 取得特定 knowledgebaser 的知識庫清單或詳細資料。 |
> | CognitiveServices/accounts/QnAMaker/知識庫/write | 用來修改知識庫或取代知識庫內容的非同步作業。 |
> | CognitiveServices/accounts/QnAMaker/知識庫/generateanswer/action | GenerateAnswer 呼叫以查詢知識庫。 |
> | CognitiveServices/accounts/QnAMaker/知識庫/訓練/action | 將呼叫定型以將建議新增至知識庫。 |
> | CognitiveServices/accounts/QnAMaker/知識庫/create/write | 建立新知識庫的非同步作業。 |
> | CognitiveServices/accounts/QnAMaker/知識庫/下載/讀取 | 下載知識庫。 |
> | CognitiveServices/accounts/QnAMaker/operations/read | 取得特定長時間執行作業的詳細資料。 |
> | CognitiveServices/accounts/拼字檢查/拼字檢查/action | 透過 GET 或 POST 取得拼寫檢查查詢的結果。 |
> | CognitiveServices/accounts/>microsoft.azure.cognitiveservices.language.textanalytics/語言/動作 | API 會傳回偵測到的語言，以及介於0和1之間的數值分數。 接近 1 的分數表示 100% 確定已識別的語言為真實。 總共支援 120 種語言。 |
> | CognitiveServices/accounts/>microsoft.azure.cognitiveservices.language.textanalytics/entities/action | API 會在指定的檔中，傳回已知實體和一般命名實體 (\" 人員 \" 、 \" 位置 \" 、 \" 組織 \" 等) 的清單。 |
> | CognitiveServices/accounts/>microsoft.azure.cognitiveservices.language.textanalytics/keyphrases/action | API 會傳回輸入文字中代表說話重點的字串清單。 |
> | CognitiveServices/accounts/>microsoft.azure.cognitiveservices.language.textanalytics/情感/action | 此 API 會傳回 0 到 1 之間的數值分數。<br>接近 1 的分數表示正面的情感，而接近 0 的分數則表示負面的情感。<br>分數0.5 表示缺少情感 (例如<br>) 的模擬陳述。 |
> | CognitiveServices/accounts/TextTranslation/偵測/動作 | 識別一段文字的語言。 |
> | CognitiveServices/accounts/TextTranslation/breaksentence/action | 識別句子界限在一段文字裡的位置。 |
> | CognitiveServices/accounts/TextTranslation/翻譯/動作 | 翻譯文字。 |
> | CognitiveServices/accounts/TextTranslation/直譯/action | 將某種語言的文字從某個指令碼轉換成另一個指令碼。 |
> | CognitiveServices/accounts/TextTranslation/dictionary/範例/動作 | 提供範例以說明字典中的字詞在內容中的使用方式。 此作業會與字典查閱搭配使用。 |
> | CognitiveServices/accounts/TextTranslation/dictionary/lookup/action | 提供字組和少數慣用語的替代翻譯。<br>每個翻譯都有一個詞性和一份反向翻譯清單。<br>反向翻譯可讓使用者了解內容的翻譯。<br>字典範例作業允許進一步向下鑽研，以查看每組翻譯配對的範例用法。 |
> | CognitiveServices/accounts/TextTranslation/語言/讀取 | 取得翻譯工具文字 API 其他作業目前支援的語言集合。 |
> | CognitiveServices/accounts/VideoSearch/趨勢/動作 | 取得目前的影片。 |
> | CognitiveServices/accounts/VideoSearch/details/action | 取得影片的相關見解，例如相關影片。 |
> | CognitiveServices/accounts/VideoSearch/search/action | 取得與指定查詢相關的影片。 |
> | CognitiveServices/accounts/VisualSearch/search/action | 傳回與所提供影像相關的標記清單 |
> | CognitiveServices/accounts/WebSearch/search/action | 取得指定查詢的 web、影像、新聞、& 影片結果。 |

### <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

Azure 服務： [Machine Learning Studio (傳統) ](../machine-learning/classic/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.MachineLearning/register/action | 針對 Machine Learning Web 服務資源提供者註冊訂用帳戶，並讓您能夠建立 Web 服務。 |
> | Microsoft.MachineLearning/webServices/action | 建立所支援區域的區域性 Web 服務屬性 |
> | Microsoft.MachineLearning/commitmentPlans/read | 讀取任何 Machine Learning 承諾用量方案 |
> | Microsoft.MachineLearning/commitmentPlans/write | 建立或更新任何 Machine Learning 承諾用量方案 |
> | Microsoft.MachineLearning/commitmentPlans/delete | 刪除任何 Machine Learning 承諾用量方案 |
> | Microsoft.MachineLearning/commitmentPlans/join/action | 加入任何 Machine Learning 承諾用量方案 |
> | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | 讀取任何 Machine Learning 承諾用量方案關聯 |
> | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | 移動任何 Machine Learning 承諾用量方案關聯 |
> | Microsoft.MachineLearning/locations/operationresults/read | 取得 Machine Learning 作業的結果 |
> | Microsoft.MachineLearning/locations/operationsstatus/read | 取得執行中 Machine Learning 作業的狀態 |
> | Microsoft.MachineLearning/operations/read | 取得 Machine Learning 作業 |
> | Microsoft.MachineLearning/skus/read | 取得 Machine Learning 承諾用量方案 SKU |
> | Microsoft.MachineLearning/webServices/read | 讀取任何 Machine Learning Web 服務 |
> | Microsoft.MachineLearning/webServices/write | 建立或更新任何 Machine Learning Web 服務 |
> | Microsoft.MachineLearning/webServices/delete | 刪除任何 Machine Learning Web 服務 |
> | Microsoft.MachineLearning/webServices/listkeys/read | 取得 Machine Learning Web 服務的金鑰 |
> | Microsoft.MachineLearning/Workspaces/read | 讀取任何 Machine Learning 工作區 |
> | Microsoft.MachineLearning/Workspaces/write | 建立或更新任何 Machine Learning 工作區 |
> | Microsoft.MachineLearning/Workspaces/delete | 刪除任何 Machine Learning 工作區 |
> | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | 列出 Machine Learning 工作區的金鑰 |
> | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | 重新同步處理針對 Machine Learning 工作區所設定之儲存體帳戶的金鑰 |

### <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

Azure 服務： [Machine Learning 服務](../machine-learning/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.MachineLearningServices/register/action | 為機器學習服務資源提供者註冊訂用帳戶 |
> | MachineLearningServices/位置/updateQuotas/動作 | 更新訂用帳戶或工作區層級上每個 VM 系列的配額。 |
> | MachineLearningServices/位置/computeoperationsstatus/讀取 | 取得特定計算作業的狀態 |
> | MachineLearningServices/位置/配額/讀取 | 取得目前根據 VMFamily 指派的工作區配額。 |
> | Microsoft.MachineLearningServices/locations/usages/read | 訂用帳戶中的 aml 計算資源的使用量報告 |
> | Microsoft.MachineLearningServices/locations/vmsizes/read | 取得支援的 VM 大小 |
> | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | 取得特定工作區作業的狀態 |
> | MachineLearningServices/operations/read | 取得 Machine Learning Services 資源提供者的所有作業 |
> | Microsoft.MachineLearningServices/workspaces/read | 取得機器學習服務工作區 |
> | Microsoft.MachineLearningServices/workspaces/write | 建立或更新機器學習服務工作區 |
> | Microsoft.MachineLearningServices/workspaces/delete | 刪除機器學習服務工作區 |
> | Microsoft.MachineLearningServices/workspaces/listKeys/action | 列出機器學習服務工作區的祕密 |
> | MachineLearningServices/workspace/resynckeys/action | 重新同步處理 Machine Learning Services 工作區的秘密 |
> | MachineLearningServices/workspace/batchEndpoints/read | 取得 Machine Learning Services 工作區 (s 中的批次推斷端點)  |
> | MachineLearningServices/workspace/batchEndpoints/write | 在 Machine Learning Services 工作區 (s 中建立或更新批次推斷端點)  |
> | MachineLearningServices/workspace/batchEndpoints/delete | 在 Machine Learning Services 工作區 (s 中刪除批次推斷端點)  |
> | MachineLearningServices/workspace/batchEndpoints/listKeys/action | 列出 Machine Learning Services 工作區 (s 中批次推斷端點的索引鍵)  |
> | MachineLearningServices/workspace/batchEndpoints/checkNameAvailability/read | 在 Machine Learning Services 工作區 (s 中檢查批次推斷端點的名稱)  |
> | MachineLearningServices/workspace/batchEndpoints/部署/讀取 | 在 Machine Learning Services 工作區 (s 中取得批次推斷端點的部署)  |
> | MachineLearningServices/workspace/batchEndpoints/部署/寫入 | 在 Machine Learning Services 工作區 (s 中，于批次推斷端點建立或更新部署)  |
> | MachineLearningServices/workspace/batchEndpoints/部署/刪除 | 刪除 Machine Learning Services 工作區 (s 中批次推斷端點的部署)  |
> | MachineLearningServices/workspace/batchEndpoints/部署/checkNameAvailability/read | 在 Machine Learning Services 工作區 (s 中，檢查批次推斷端點部署的名稱)  |
> | Microsoft.MachineLearningServices/workspaces/computes/read | 取得機器學習服務工作區中的計算資源 |
> | Microsoft.MachineLearningServices/workspaces/computes/write | 在機器學習服務工作區中建立或更新計算資源 |
> | Microsoft.MachineLearningServices/workspaces/computes/delete | 刪除機器學習服務工作區中的計算資源 |
> | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | 列出機器學習服務工作區中的計算資源祕密 |
> | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | 列出機器學習服務工作區中的計算資源節點 |
> | MachineLearningServices/workspace/計算/開始/動作 | 在 Machine Learning Services 工作區中啟動計算資源 |
> | MachineLearningServices/workspace/計算/停止/動作 | 停止 Machine Learning 服務工作區中的計算資源 |
> | MachineLearningServices/workspace/計算/重新開機/動作 | 在 Machine Learning Services 工作區中重新開機計算資源 |
> | MachineLearningServices/workspace/計算/applicationaccess/action | 存取 Machine Learning Services 工作區中的計算資源 |
> | MachineLearningServices/workspace/connections/read | 取得 Machine Learning Services 工作區連接 (s)  |
> | MachineLearningServices/workspace/connections/write | 建立或更新 Machine Learning Services 連接 (s)  |
> | MachineLearningServices/workspace/connections/delete | 刪除 Machine Learning 服務連接 (s)  |
> | MachineLearningServices/workspace/datadriftdetectors/read | 取得 Machine Learning Services 工作區 (s 中的資料漂移偵測器)  |
> | MachineLearningServices/workspace/datadriftdetectors/write | 在 Machine Learning Services 工作區 (s 中建立或更新資料漂移偵測器)  |
> | MachineLearningServices/workspace/datadriftdetectors/delete | 刪除 Machine Learning Services 工作區 (s 中的資料漂移偵測器)  |
> | MachineLearningServices/工作區/資料集/已註冊/讀取 | 在 Machine Learning Services 工作區 (s 中取得已註冊的資料集)  |
> | MachineLearningServices/工作區/資料集/已註冊/寫入 | 在 Machine Learning Services 工作區 (s 中建立或更新已註冊的資料集)  |
> | MachineLearningServices/工作區/資料集/已註冊/刪除 | 在 Machine Learning Services 工作區 (s 中刪除已註冊的資料集)  |
> | MachineLearningServices/工作區/資料集/已註冊/預覽/讀取 | 在 Machine Learning Services 工作區 (s 中，取得已註冊資料集的資料集預覽)  |
> | MachineLearningServices/工作區/資料集/已註冊/設定檔/讀取 | 在 Machine Learning Services 工作區 (s 中，取得已註冊資料集的資料集設定檔)  |
> | MachineLearningServices/工作區/資料集/已註冊/設定檔/寫入 | 在 Machine Learning Services 工作區 (s 中，建立或更新已註冊資料集的資料集設定檔)  |
> | MachineLearningServices/工作區/資料集/已註冊/架構/讀取 | 取得 Machine Learning Services 工作區 (s 中已註冊資料集的資料集架構)  |
> | MachineLearningServices/工作區/資料集/已註冊/讀取 | 取得 Machine Learning Services 工作區 (s 中未註冊的資料集)  |
> | MachineLearningServices/工作區/資料集/未註冊/寫入 | 在 Machine Learning Services 工作區 (s 中建立或更新未註冊的資料集)  |
> | MachineLearningServices/工作區/資料集/取消註冊/刪除 | 在 Machine Learning Services 工作區 (s 中刪除未註冊的資料集)  |
> | MachineLearningServices/工作區/資料集/未註冊/預覽/讀取 | 取得 Machine Learning Services 工作區 (s 中未註冊資料集的資料集預覽)  |
> | MachineLearningServices/工作區/資料集/未註冊/設定檔/讀取 | 取得 Machine Learning Services 工作區 (s 中未註冊資料集的資料集設定檔)  |
> | MachineLearningServices/工作區/資料集/未註冊/設定檔/寫入 | 在 Machine Learning Services 工作區 (s 中，建立或更新未註冊資料集的資料集設定檔)  |
> | MachineLearningServices/工作區/資料集/未註冊/架構/讀取 | 取得 Machine Learning Services 工作區 (s 中未註冊資料集的資料集架構)  |
> | MachineLearningServices/workspace/資料存放區/read | 在 Machine Learning Services 工作區 (s 中取得資料存放區)  |
> | MachineLearningServices/workspace/資料存放區/write | 在 Machine Learning Services 工作區 (s 中建立或更新資料存放區)  |
> | MachineLearningServices/workspace/資料存放區/delete |  (s Machine Learning 服務工作區中刪除資料存放區)  |
> | MachineLearningServices/工作區/端點/管線/讀取 | 取得 Machine Learning Services 工作區 (s 中的已發佈管線和管線端點)  |
> | MachineLearningServices/workspace/端點/管線/寫入 | 在 Machine Learning Services 工作區 (s 中建立或更新已發佈的管線和管線端點)  |
> | MachineLearningServices/工作區/環境/讀取 | 取得 Machine Learning Services 工作區中的環境 (s)  |
> | MachineLearningServices/workspace/環境/readSecrets/action | 取得在 Machine Learning Services 工作區 (s 中具有秘密的環境)  |
> | MachineLearningServices/工作區/環境/寫入 | 在 Machine Learning Services 工作區 (s 中建立或更新環境)  |
> | MachineLearningServices/工作區/環境/組建/動作 | 在 Machine Learning Services 工作區中建立環境 (s)  |
> | MachineLearningServices/workspace/eventGridFilters/read | 取得特定工作區的事件方格篩選器 |
> | MachineLearningServices/工作區/實驗/讀取 | 取得 Machine Learning Services 工作區 (s 中的實驗)  |
> | MachineLearningServices/工作區/實驗/寫入 | 在 Machine Learning Services 工作區 (s 中建立或更新實驗)  |
> | MachineLearningServices/workspace/實驗/刪除 | 在 Machine Learning Services 工作區 (s 中刪除實驗)  |
> | MachineLearningServices/workspace/實驗/執行/提交/動作 | 在 Machine Learning Services 工作區 (s 中建立或更新腳本執行)  |
> | MachineLearningServices/工作區/實驗/執行/讀取 | 會在 Machine Learning Services 工作區 (s 中執行)  |
> | MachineLearningServices/工作區/實驗/執行/寫入 | 建立或更新會在 Machine Learning Services 工作區 (s 中執行)  |
> | MachineLearningServices/workspace/features/read | 取得 Machine Learning 服務工作區的所有已啟用功能 |
> | MachineLearningServices/workspace/inferenceEndpoints/read | 取得 Machine Learning Services 工作區 (s 中的推斷推斷端點)  |
> | MachineLearningServices/workspace/inferenceEndpoints/write | 在 Machine Learning Services 工作區 (s 中建立或更新推斷推斷端點)  |
> | MachineLearningServices/workspace/inferenceEndpoints/delete | 刪除 Machine Learning Services 工作區 (s 中的推斷推斷端點)  |
> | MachineLearningServices/workspace/inferenceEndpoints/checkNameAvailability/read | 在 Machine Learning Services 工作區 (s 中檢查推斷推斷端點的名稱)  |
> | MachineLearningServices/workspace/inferenceEndpoints/部署/讀取 | 在 Machine Learning Services 工作區 (s 中取得推斷推斷端點的部署)  |
> | MachineLearningServices/workspace/inferenceEndpoints/部署/寫入 | 在 Machine Learning Services 工作區 (s 中的推斷推斷端點建立或更新部署)  |
> | MachineLearningServices/workspace/inferenceEndpoints/部署/刪除 | 在 Machine Learning Services 工作區 (s 中，刪除推斷推斷端點中的部署)  |
> | MachineLearningServices/workspace/inferenceEndpoints/部署/checkNameAvailability/read | 在 Machine Learning Services 工作區 (s 中，檢查推斷推斷端點部署的名稱)  |
> | MachineLearningServices/workspace/inferenceEndpoints/部署/sku/read | 針對 Machine Learning Services 工作區 (s 中的推斷推斷端點中的部署，取得擴展 sku 設定)  |
> | MachineLearningServices/工作區/標籤/匯出/動作 | 在 Machine Learning Services 工作區中匯出標記專案的標籤 (s)  |
> | MachineLearningServices/工作區/標籤/標籤/讀取 | 取得 Machine Learning Services 工作區中標記專案的標籤 (s)  |
> | MachineLearningServices/工作區/標籤/標籤/寫入 | 在 Machine Learning Services 工作區 (s 中建立標籤專案的標籤)  |
> | MachineLearningServices/工作區/標籤/標籤/拒絕/動作 | 在 Machine Learning Services 工作區 (s 中拒絕標記專案的標籤)  |
> | MachineLearningServices/工作區/標籤/專案/讀取 | 取得 Machine Learning Services 工作區 (s 中的標籤專案)  |
> | MachineLearningServices/工作區/標籤/專案/寫入 | 在 Machine Learning Services 工作區 (s 中建立或更新標籤專案)  |
> | MachineLearningServices/工作區/標籤/專案/刪除 | 刪除 Machine Learning Services 工作區 (s 中的標籤專案)  |
> | MachineLearningServices/工作區/標籤/專案/摘要/讀取 | 取得 Machine Learning Services 工作區 (s 中的標籤專案摘要)  |
> | MachineLearningServices/workspace/metadata/構件/read | 取得 Machine Learning Services 工作區中的構件 (s)  |
> | MachineLearningServices/workspace/metadata/構件/write | 在 Machine Learning Services 工作區 (s 中建立或更新構件)  |
> | MachineLearningServices/workspace/metadata/構件/delete | 刪除 Machine Learning Services 工作區 (s 中的成品)  |
> | MachineLearningServices/workspace/metadata/秘密/read | 取得 Machine Learning Services 工作區 (s 中的密碼)  |
> | MachineLearningServices/工作區/中繼資料/秘密/寫入 | 在 Machine Learning Services 工作區 (s 中建立或更新秘密)  |
> | MachineLearningServices/workspace/metadata/秘密/刪除 | 在 Machine Learning Services 工作區 (s 中刪除秘密)  |
> | MachineLearningServices/工作區/中繼資料/快照集/讀取 | 取得 Machine Learning Services 工作區 (s 中的快照集)  |
> | MachineLearningServices/workspace/metadata/快照/寫入 | 在 Machine Learning Services 工作區 (s 中建立或更新快照集)  |
> | MachineLearningServices/工作區/中繼資料/快照/刪除 | 刪除 Machine Learning Services 工作區 (s 中的快照集)  |
> | MachineLearningServices/工作區/計量/資源/寫入 |  (s Machine Learning 服務工作區中建立資源計量)  |
> | MachineLearningServices/工作區/模型/讀取 | 取得 Machine Learning Services 工作區中的模型 (s)  |
> | MachineLearningServices/工作區/模型/寫入 | 在 Machine Learning Services 工作區 (s 中建立或更新模型)  |
> | MachineLearningServices/工作區/模型/刪除 | 刪除 Machine Learning Services 工作區 (s 中的模型)  |
> | MachineLearningServices/工作區/模型/套件/動作 | Machine Learning Services 工作區中的套件模型 (s)  |
> | MachineLearningServices/工作區/模組/讀取 | 取得 Machine Learning 服務工作區中的模組 (s)  |
> | MachineLearningServices/workspace/模組/寫入 | 在 Machine Learning Services 工作區 (s 中建立或更新模組)  |
> | MachineLearningServices/workspace/筆記本/samples/read | 取得範例筆記本 |
> | MachineLearningServices/工作區/筆記本/儲存體/讀取 | 取得工作區的筆記本檔案 |
> | MachineLearningServices/工作區/筆記本/儲存體/寫入 | 將檔案寫入工作區儲存體 |
> | MachineLearningServices/工作區/筆記本/儲存體/刪除 | 從工作區儲存體刪除檔案 |
> | MachineLearningServices/workspace/筆記本/vm/read | 取得特定工作區的筆記本 Vm |
> | MachineLearningServices/workspace/筆記本/vm/write | 變更筆記本 VM 的狀態 |
> | MachineLearningServices/workspace/筆記本/vm/delete | 刪除筆記本 VM |
> | MachineLearningServices/workspace/onlineEndpoints/read | 取得 Machine Learning Services 工作區 (s 中的線上推斷端點)  |
> | MachineLearningServices/workspace/onlineEndpoints/write | 在 Machine Learning Services 工作區 (s 中建立或更新線上推斷端點)  |
> | MachineLearningServices/workspace/onlineEndpoints/delete | 在 Machine Learning Services 工作區 (s 中刪除線上推斷端點)  |
> | MachineLearningServices/workspace/onlineEndpoints/checkNameAvailability/read | 在 Machine Learning Services 工作區 (s 中檢查線上推斷端點的名稱)  |
> | MachineLearningServices/workspace/onlineEndpoints/部署/讀取 | 取得 Machine Learning Services 工作區 (s 中線上推斷端點的部署)  |
> | MachineLearningServices/workspace/onlineEndpoints/部署/寫入 | 在 Machine Learning Services 工作區 (s 中，于線上推斷端點建立或更新部署)  |
> | MachineLearningServices/workspace/onlineEndpoints/部署/刪除 | 在 Machine Learning Services 工作區 (s 中，刪除線上推斷端點中的部署)  |
> | MachineLearningServices/workspace/onlineEndpoints/部署/checkNameAvailability/read | 在 Machine Learning Services 工作區 (s 中，檢查線上推斷端點部署的名稱)  |
> | MachineLearningServices/workspace/onlineEndpoints/部署/sku/read | 針對 Machine Learning Services 工作區 (s 中的線上推斷端點中的部署，取得擴展 sku 設定)  |
> | MachineLearningServices/workspace/pipelinedrafts/read | 取得 Machine Learning Services 工作區 (s 中的管線草稿)  |
> | MachineLearningServices/workspace/pipelinedrafts/write | 在 Machine Learning Services 工作區 (s 中建立或更新管線草稿)  |
> | MachineLearningServices/workspace/pipelinedrafts/delete | 刪除 Machine Learning Services 工作區 (s 中的管線草稿)  |
> | MachineLearningServices/workspace/reports/read | 在 Machine Learning Services 工作區 (s 中取得自訂報表)  |
> | MachineLearningServices/workspace/reports/write | 在 Machine Learning Services 工作區 (s 中建立或更新自訂報表)  |
> | MachineLearningServices/workspace/reports/delete | 在 Machine Learning Services 工作區 (s 中刪除自訂報表)  |
> | Microsoft. MachineLearningServices/workspace/services/read | 取得 Machine Learning Services 工作區 (s 中的服務)  |
> | MachineLearningServices/workspace/services/aci/write | 在 Machine Learning Services 工作區 (s 中建立或更新 ACI 服務)  |
> | MachineLearningServices/workspace/services/aci/listkeys/action | 列出 Machine Learning Services 工作區中 ACI 服務的金鑰 (s)  |
> | MachineLearningServices/workspace/services/aci/delete | 在 Machine Learning Services 工作區 (s 中刪除 ACI 服務)  |
> | MachineLearningServices/workspace/services/aks/write | 在 Machine Learning Services 工作區 (s 中建立或更新 AKS 服務)  |
> | MachineLearningServices/workspace/services/aks/listkeys/action | 列出 Machine Learning Services 工作區中 AKS 服務的金鑰 (s)  |
> | MachineLearningServices/workspace/services/aks/delete | 在 Machine Learning Services 工作區 (s 中刪除 AKS 服務)  |
> | MachineLearningServices/workspace/services/aks/計分/動作 | Machine Learning Services 工作區中的分數 AKS 服務 (s)  |

## <a name="internet-of-things"></a>Internet of things

### <a name="microsoftdevices"></a>Microsoft.Devices

Azure 服務： [iot](../iot-hub/index.yml)中樞、 [iot 中樞裝置](../iot-dps/index.yml)布建服務

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Devices/register/action | 針對 IotHub 資源提供者註冊訂用帳戶，並讓您能夠建立 IotHub 資源 |
> | Microsoft.Devices/checkNameAvailability/Action | 檢查 IotHub 名稱是否可供使用 |
> | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | 確認佈建服務名稱是否可用 |
> | Microsoft.Devices/register/action | 針對 IotHub 資源提供者註冊訂用帳戶，並讓您能夠建立 IotHub 資源 |
> | Microsoft.Devices/checkNameAvailability/Action | 檢查 IotHub 名稱是否可供使用 |
> | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | 確認佈建服務名稱是否可用 |
> | Microsoft.Devices/register/action | 針對 IotHub 資源提供者註冊訂用帳戶，並讓您能夠建立 IotHub 資源 |
> | Microsoft. 裝置/帳戶/diagnosticSettings/讀取 | 取得資源的診斷設定 |
> | Microsoft. 裝置/帳戶/diagnosticSettings/寫入 | 建立或更新資源的診斷設定 |
> | Microsoft. 裝置/帳戶/logDefinitions/讀取 | 取得 IotHub 服務的可用記錄定義 |
> | Microsoft. 裝置/帳戶/metricDefinitions/讀取 | 取得 IotHub 服務的可用計量 |
> | Microsoft. Devices/digitalTwins/Read | 取得與訂用帳戶相關聯的數位 Twins 帳戶清單 |
> | Microsoft. Devices/digitalTwins/Write | 建立新的 Digitial Twins 帳戶 |
> | Microsoft. Devices/digitalTwins/Delete | 刪除現有的數位 Twins 帳戶及其所有子系 |
> | Microsoft. Devices/digitalTwins/operationresults/Read | 針對數位 Twins 帳戶取得作業的狀態 |
> | Microsoft. Devices/digitalTwins/sku/Read | 取得數位 Twins 帳戶的有效 Sku 清單 |
> | Microsoft.Devices/ElasticPools/diagnosticSettings/read | 取得資源的診斷設定 |
> | Microsoft.Devices/ElasticPools/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Microsoft. Devices/elasticPools/eventGridFilters/Write | 建立新的或更新現有的彈性集區事件方格篩選 |
> | Microsoft. Devices/elasticPools/eventGridFilters/Read | 取得彈性集區事件方格篩選 |
> | Microsoft. Devices/elasticPools/eventGridFilters/Delete | 刪除彈性集區事件方格篩選 |
> | Microsoft.Devices/elasticPools/iotHubTenants/Write | 建立或更新 IotHub 租用戶資源 |
> | Microsoft.Devices/elasticPools/iotHubTenants/Read | 取得 IotHub 租用戶資源 |
> | Microsoft.Devices/elasticPools/iotHubTenants/Delete | 刪除 IotHub 租用戶資源 |
> | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | 取得 IotHub 租用戶金鑰 |
> | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | 匯出裝置 |
> | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | 匯入裝置 |
> | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | 取得憑證 |
> | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | 建立或更新憑證 |
> | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | 刪除憑證 |
> | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | 產生驗證碼 |
> | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | 驗證憑證資源 |
> | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | 取得資源的診斷設定 |
> | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | 建立 EventHub 取用者群組 |
> | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | 取得 EventHub 取用者群組 |
> | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | 刪除 EventHub 取用者群組 |
> | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | 取得 IotHub 租用戶統計資料資源 |
> | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | 取得 IotHub 租用戶金鑰 |
> | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | 取得在給定的 IotHub 上所提交的作業詳細資料 |
> | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | 取得 IotHub 服務的可用記錄定義 |
> | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | 取得 IotHub 服務的可用計量 |
> | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | 取得配額計量 |
> | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | 針對所有現有路由來測試訊息 |
> | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | 針對所提供的測試路由來測試訊息 |
> | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | 取得 IotHub 之所有路由端點的健康狀態 |
> | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/Write | 更新 Iot 租使用者中樞上的 Azure 資訊安全中心設定 |
> | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/Read | 取得 Iot 租使用者中樞上的 Azure 資訊安全中心設定 |
> | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/operationResults/Read | 取得 Iot 租使用者中樞的非同步 Put 作業結果 SecuritySettings |
> | Microsoft.Devices/ElasticPools/metricDefinitions/read | 取得 IotHub 服務的可用計量 |
> | Microsoft.Devices/iotHubs/Read | 取得 IotHub 資源 |
> | Microsoft.Devices/iotHubs/Write | 建立或更新 IotHub 資源 |
> | Microsoft.Devices/iotHubs/Delete | 刪除 IotHub 資源 |
> | Microsoft.Devices/iotHubs/listkeys/Action | 取得所有 IotHub 金鑰 |
> | Microsoft.Devices/iotHubs/exportDevices/Action | 匯出裝置 |
> | Microsoft.Devices/iotHubs/importDevices/Action | 匯入裝置 |
> | Microsoft. Devices/iotHubs/privateEndpointConnectionsApproval/Action | 核准或拒絕私人端點連線 |
> | Microsoft.Devices/iotHubs/certificates/Read | 取得憑證 |
> | Microsoft.Devices/iotHubs/certificates/Write | 建立或更新憑證 |
> | Microsoft.Devices/iotHubs/certificates/Delete | 刪除憑證 |
> | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | 產生驗證碼 |
> | Microsoft.Devices/iotHubs/certificates/verify/Action | 驗證憑證資源 |
> | Microsoft.Devices/iotHubs/certificates/Read | 取得憑證 |
> | Microsoft.Devices/iotHubs/certificates/Write | 建立或更新憑證 |
> | Microsoft.Devices/iotHubs/certificates/Delete | 刪除憑證 |
> | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | 產生驗證碼 |
> | Microsoft.Devices/iotHubs/certificates/verify/Action | 驗證憑證資源 |
> | Microsoft.Devices/IotHubs/diagnosticSettings/read | 取得資源的診斷設定 |
> | Microsoft.Devices/IotHubs/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Microsoft. Devices/iotHubs/digitalTwinsLinks/Write | IotHub 與數位 Twins 服務的連結 |
> | Microsoft. Devices/iotHubs/digitalTwinsLinks/Read | 取得目前連結的數位 Twins 服務的相關資訊 |
> | Microsoft. Devices/iotHubs/digitalTwinsLinks/Delete | 刪除數位 Twins 服務的連結 |
> | Microsoft.Devices/iotHubs/eventGridFilters/Write | 建立新的或更新現有的事件格線篩選 |
> | Microsoft.Devices/iotHubs/eventGridFilters/Read | 取得事件格線篩選 |
> | Microsoft.Devices/iotHubs/eventGridFilters/Delete | 刪除事件格線篩選 |
> | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | 建立 EventHub 取用者群組 |
> | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | 取得 EventHub 取用者群組 |
> | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | 刪除 EventHub 取用者群組 |
> | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | 取得給定名稱的 IotHub 金鑰 |
> | Microsoft.Devices/iotHubs/iotHubStats/Read | 取得 IotHub 統計資料 |
> | Microsoft.Devices/iotHubs/jobs/Read | 取得在給定的 IotHub 上所提交的作業詳細資料 |
> | Microsoft.Devices/IotHubs/logDefinitions/read | 取得 IotHub 服務的可用記錄定義 |
> | Microsoft.Devices/IotHubs/metricDefinitions/read | 取得 IotHub 服務的可用計量 |
> | Microsoft.Devices/iotHubs/operationresults/Read | 取得作業結果 (過時的 API) |
> | Microsoft. Devices/iotHubs/privateEndpointConnectionProxies/validate/Action | 在建立期間驗證私人端點連接 proxy 輸入 |
> | Microsoft. Devices/iotHubs/privateEndpointConnectionProxies/Read | 取得指定私人端點連接 proxy 的屬性 |
> | Microsoft. Devices/iotHubs/privateEndpointConnectionProxies/Write | 建立或更新私人端點連線 proxy |
> | Microsoft. Devices/iotHubs/privateEndpointConnectionProxies/Delete | 刪除現有的私人端點連線 proxy |
> | Microsoft. Devices/iotHubs/privateEndpointConnectionProxies/operationResults/Read | 取得私人端點連線 proxy 上非同步作業的結果 |
> | Microsoft. Devices/iotHubs/privateEndpointConnections/Read | 取得指定之 iot 中樞的所有私人端點連線 |
> | Microsoft. Devices/iotHubs/privateEndpointConnections/Delete | 刪除現有的私人端點連接 |
> | Microsoft. Devices/iotHubs/privateEndpointConnections/Write | 建立或更新私人端點連接 |
> | Microsoft. Devices/iotHubs/privateEndpointConnections/operationResults/Read | 取得私人端點連接上非同步作業的結果 |
> | Microsoft. Devices/iotHubs/privateLinkResources/Read | 取得 IotHub 的私人連結資源 |
> | Microsoft.Devices/iotHubs/quotaMetrics/Read | 取得配額計量 |
> | Microsoft.Devices/iotHubs/routing/$testall/Action | 針對所有現有路由來測試訊息 |
> | Microsoft.Devices/iotHubs/routing/$testnew/Action | 針對所提供的測試路由來測試訊息 |
> | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | 取得 IotHub 之所有路由端點的健康狀態 |
> | Microsoft. Devices/iotHubs/securitySettings/Write | 更新 Iot 中樞上的 Azure 資訊安全中心設定 |
> | Microsoft. Devices/iotHubs/securitySettings/Read | 取得 Iot 中樞上的 Azure 資訊安全中心設定 |
> | Microsoft. Devices/iotHubs/securitySettings/operationResults/Read | 取得 Iot 中樞的非同步 Put 作業結果 SecuritySettings |
> | Microsoft.Devices/iotHubs/skus/Read | 取得有效的 IotHub SKU |
> | Microsoft.Devices/locations/operationresults/Read | 取得以位置為基礎的作業結果 |
> | Microsoft.Devices/locations/operationresults/Read | 取得以位置為基礎的作業結果 |
> | Microsoft.Devices/operationresults/Read | 取得作業結果 |
> | Microsoft.Devices/operationresults/Read | 取得作業結果 |
> | Microsoft.Devices/operations/Read | 取得所有 ResourceProvider 作業 |
> | Microsoft.Devices/operations/Read | 取得所有 ResourceProvider 作業 |
> | Microsoft.Devices/provisioningServices/Read | 取得 IotDps 資源 |
> | Microsoft.Devices/provisioningServices/Write | 建立 IotDps 資源 |
> | Microsoft.Devices/provisioningServices/Delete | 刪除 IotDps 資源 |
> | Microsoft.Devices/provisioningServices/listkeys/Action | 取得所有 IotDps 金鑰 |
> | Microsoft. Devices/provisioningServices/privateEndpointConnectionsApproval/Action | 核准或拒絕私人端點連線 |
> | Microsoft.Devices/provisioningServices/certificates/Read | 取得憑證 |
> | Microsoft.Devices/provisioningServices/certificates/Write | 建立或更新憑證 |
> | Microsoft.Devices/provisioningServices/certificates/Delete | 刪除憑證 |
> | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | 產生驗證碼 |
> | Microsoft.Devices/provisioningServices/certificates/verify/Action | 驗證憑證資源 |
> | Microsoft.Devices/provisioningServices/diagnosticSettings/read | 取得資源的診斷設定 |
> | Microsoft.Devices/provisioningServices/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Microsoft.Devices/provisioningServices/keys/listkeys/Action | 取得金鑰名稱的 IotDps 金鑰 |
> | Microsoft.Devices/provisioningServices/logDefinitions/read | 取得佈建服務的可用記錄定義 |
> | Microsoft.Devices/provisioningServices/metricDefinitions/read | 取得佈建服務的可用計量 |
> | Microsoft.Devices/provisioningServices/operationresults/Read | 取得 DPS 作業結果 |
> | Microsoft. Devices/provisioningServices/privateEndpointConnectionProxies/validate/Action | 在建立期間驗證私人端點連接 proxy 輸入 |
> | Microsoft. Devices/provisioningServices/privateEndpointConnectionProxies/Read | 取得指定私人端點連接 proxy 的屬性 |
> | Microsoft. Devices/provisioningServices/privateEndpointConnectionProxies/Write | 建立或更新私人端點連線 proxy |
> | Microsoft. Devices/provisioningServices/privateEndpointConnectionProxies/Delete | 刪除現有的私人端點連線 proxy |
> | Microsoft. Devices/provisioningServices/privateEndpointConnectionProxies/operationResults/Read | 取得私人端點連線 proxy 上非同步作業的結果 |
> | Microsoft. Devices/provisioningServices/privateEndpointConnections/Read | 取得指定之 iot 中樞的所有私人端點連線 |
> | Microsoft. Devices/provisioningServices/privateEndpointConnections/Delete | 刪除現有的私人端點連接 |
> | Microsoft. Devices/provisioningServices/privateEndpointConnections/Write | 建立或更新私人端點連接 |
> | Microsoft. Devices/provisioningServices/privateEndpointConnections/operationResults/Read | 取得私人端點連接上非同步作業的結果 |
> | Microsoft. Devices/provisioningServices/privateLinkResources/Read | 取得 IotHub 的私人連結資源 |
> | Microsoft.Devices/provisioningServices/skus/Read | 取得有效的 IotDps SKU |
> | Microsoft.Devices/usages/Read | 取得此提供者的訂用帳戶使用量詳細資料。 |
> | Microsoft.Devices/usages/Read | 取得此提供者的訂用帳戶使用量詳細資料。 |

### <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

Azure 服務： [IoT Central](../iot-central/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.IoTCentral/checkNameAvailability/action | 檢查 IoT Central 應用程式名稱是否可用 |
> | Microsoft.IoTCentral/checkSubdomainAvailability/action | 檢查 IoT Central 應用程式子網域是否可用 |
> | Microsoft.iotcentral/appTemplates/action | 取得 Azure IoT Central 上所有可用的應用程式範本 |
> | Microsoft.IoTCentral/register/action | 針對 Azure IoT Central 資源提供者註冊訂用帳戶 |
> | Microsoft.IoTCentral/IoTApps/read | 取得單一 IoT Central 應用程式 |
> | Microsoft.IoTCentral/IoTApps/write | 建立或更新 IoT Central 應用程式 |
> | Microsoft.IoTCentral/IoTApps/delete | 刪除 IoT Central 應用程式 |
> | Microsoft.IoTCentral/operations/read | 取得 IoT Central 應用程式上所有可用的作業 |

### <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

Azure 服務： [通知中樞](../notification-hubs/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.NotificationHubs/register/action | 針對 NotifciationHubs 資源提供者註冊訂用帳戶，並讓您能夠建立命名空間和 NotificationHubs |
> | Microsoft.NotificationHubs/unregister/action | 針對 NotifciationHubs 資源提供者取消註冊訂用帳戶，並讓您能夠建立命名空間和 NotificationHubs |
> | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | 檢查在 NotificationHub 服務內，給定的命名空間資源名稱是否可供使用。 |
> | Microsoft.NotificationHubs/Namespaces/write | 建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。 |
> | Microsoft.NotificationHubs/Namespaces/read | 取得命名空間資源描述的清單 |
> | Microsoft.NotificationHubs/Namespaces/Delete | 刪除命名空間資源 |
> | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | 取得命名空間授權規則描述的清單。 |
> | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | 檢查在命名空間內，給定的 NotificationHub 名稱是否可供使用。 |
> | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | 建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | 取得命名空間授權規則描述的清單。 |
> | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | 刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。  |
> | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | 取得命名空間的連接字串 |
> | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | 命名空間授權規則會重新產生主要/次要金鑰，指定需要重新產生的金鑰 |
> | NotificationHubs/命名空間/diagnosticSettings/read | 取得命名空間診斷設定資源描述的清單 |
> | NotificationHubs/命名空間/diagnosticSettings/write | 取得命名空間診斷設定資源描述的清單 |
> | NotificationHubs/命名空間/logDefinitions/read | 取得命名空間記錄資源描述的清單 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | 建立通知中樞，並更新其屬性。 其屬性主要包括 PNS 認證。 授權規則與 TTL |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | 取得通知中樞資源描述的清單 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | 刪除通知中樞資源 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | 取得通知中樞授權規則的清單 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | 取得所有通知中樞的 PNS 認證。 這包括 WNS、MPNS、APNS、GCM 和百度認證 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | 傳送測試推播通知。 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | 建立通知中樞授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | 取得通知中樞授權規則的清單 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | 刪除通知中樞的授權規則 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | 取得通知中樞的連接字串 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | 通知中樞授權規則會重新產生主要/次要金鑰，指定需要重新產生的金鑰 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | 取得命名空間計量資源描述的清單 |
> | Microsoft.NotificationHubs/operationResults/read | 傳回通知中樞提供者的作業結果 |
> | Microsoft.NotificationHubs/operations/read | 傳回通知中樞提供者支援的作業清單 |

### <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

Azure 服務：[時間序列深入](../time-series-insights/index.yml)解析

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.TimeSeriesInsights/register/action | 為時間序列深入解析資源提供者註冊訂用帳戶，並讓您能夠建立時間序列深入解析環境。 |
> | Microsoft.TimeSeriesInsights/environments/read | 取得環境的屬性。 |
> | Microsoft.TimeSeriesInsights/environments/write | 建立新的環境，或更新現有的環境。 |
> | Microsoft.TimeSeriesInsights/environments/delete | 刪除環境。 |
> | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | 取得存取原則的屬性。 |
> | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | 針對環境建立新的存取原則，或更新現有的存取原則。 |
> | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | 刪除存取原則。 |
> | Microsoft.TimeSeriesInsights/environments/eventsources/read | 取得事件來源的屬性。 |
> | Microsoft.TimeSeriesInsights/environments/eventsources/write | 針對環境建立新的事件來源，或更新現有的事件來源。 |
> | Microsoft.TimeSeriesInsights/environments/eventsources/delete | 刪除事件來源。 |
> | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | 取得參考資料集的屬性。 |
> | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | 針對環境建立新的參考資料集，或更新現有的參考資料集。 |
> | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | 刪除參考資料集。 |
> | Microsoft.TimeSeriesInsights/environments/status/read | 取得環境的狀態，其相關聯作業 (例如輸入) 的狀態。 |

## <a name="mixed-reality"></a>混合實境

### <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

Azure 服務： [Azure 數位 Twins](../digital-twins/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.IoTSpaces/register/action | 為 Microsoft.IoTSpaces Graph 資源提供者註冊訂用帳戶以啟用資源的建立作業 |
> | Microsoft.IoTSpaces/Graph/write | 建立 Microsoft.IoTSpaces Graph 資源 |
> | Microsoft.IoTSpaces/Graph/read | 取得 Microsoft.IoTSpaces Graph 資源 |
> | Microsoft.IoTSpaces/Graph/delete | 刪除 Microsoft.IoTSpaces Graph 資源 |

### <a name="microsoftmixedreality"></a>Microsoft.MixedReality

Azure 服務： [Azure 空間錨點](../spatial-anchors/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | MixedReality/註冊/動作 | 註冊混合現實資源提供者的訂用帳戶。 |
> | MixedReality/remoteRenderingAccounts/providers/Microsoft. Insights/metricDefinitions/read | 取得 MixedReality/remoteRenderingAccounts 的可用計量 |
> | MixedReality/spatialAnchorsAccounts/providers/Microsoft. Insights/diagnosticSettings/read | 取得 MixedReality/spatialAnchorsAccounts 的診斷設定 |
> | MixedReality/spatialAnchorsAccounts/providers/Microsoft. Insights/diagnosticSettings/write | 建立或更新 MixedReality/spatialAnchorsAccounts 的診斷設定 |
> | MixedReality/spatialAnchorsAccounts/providers/Microsoft. Insights/metricDefinitions/read | 取得 MixedReality/spatialAnchorsAccounts 的可用計量 |
> | **DataAction** | **說明** |
> | MixedReality/ObjectUnderstandingAccounts/內嵌/動作 | 建立模型內嵌作業 |
> | MixedReality/ObjectUnderstandingAccounts/內嵌/讀取 | 取得模型內嵌作業狀態 |
> | MixedReality/RemoteRenderingAccounts/convert/action | 開始資產轉換 |
> | MixedReality/RemoteRenderingAccounts/managesessions/action | 開始會話 |
> | MixedReality/RemoteRenderingAccounts/convert/read | 取得資產轉換屬性 |
> | MixedReality/RemoteRenderingAccounts/convert/delete | 停止資產轉換 |
> | MixedReality/RemoteRenderingAccounts/診斷/讀取 | 連接至遠端轉譯檢查 |
> | MixedReality/RemoteRenderingAccounts/managesessions/read | 取得會話屬性 |
> | MixedReality/RemoteRenderingAccounts/managesessions/delete | 停止會話 |
> | MixedReality/RemoteRenderingAccounts/render/read | 連接至會話 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | 建立空間錨點 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | 刪除空間錨點 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | 更新空間錨點屬性 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | 探索附近的空間錨點 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | 取得空間錨點的屬性 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | 找出空間錨點 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | 提交診斷資料，以協助改善 Azure 空間錨點服務的品質 |

## <a name="integration"></a>整合

### <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

Azure 服務： [API 管理](../api-management/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.ApiManagement/register/action | 針對 Microsoft.ApiManagement 資源提供者註冊訂用帳戶 |
> | Microsoft.ApiManagement/unregister/action | 針對 Microsoft.ApiManagement 資源提供者取消註冊訂用帳戶 |
> | Microsoft.ApiManagement/checkNameAvailability/read | 檢查所提供的服務名稱是否可用 |
> | ApiManagement/deletedservices/read | 取得可在虛刪除期間內還原的已刪除 API 管理服務 |
> | ApiManagement/deletedservices/delete | 刪除 API 管理服務，但不含還原的選項 |
> | Microsoft.ApiManagement/operations/read | 讀取可供 Microsoft.ApiManagement 資源使用的所有 API 作業 |
> | Microsoft.ApiManagement/reports/read | 取得依時間週期、地理區域、開發人員、產品、API、作業、訂用帳戶和依需求彙總的報告。 |
> | Microsoft.ApiManagement/service/write | 建立或更新 API 管理服務執行個體 |
> | Microsoft.ApiManagement/service/read | 讀取 API 管理服務執行個體的中繼資料 |
> | Microsoft.ApiManagement/service/delete | 刪除 API 管理服務執行個體 |
> | Microsoft.ApiManagement/service/updatehostname/action | 設定、更新或移除 API 管理服務的自訂網域名稱 |
> | Microsoft.ApiManagement/service/updatecertificate/action | 上傳 API 管理服務的 TLS/SSL 憑證 |
> | Microsoft.ApiManagement/service/backup/action | 將 API 管理服務備份到使用者所提供之儲存體帳戶中的指定容器 |
> | Microsoft.ApiManagement/service/restore/action | 從使用者所提供之儲存體帳戶中的指定容器來還原 API 管理服務 |
> | Microsoft.ApiManagement/service/managedeployments/action | 變更 SKU/單位、新增/移除 API 管理服務的區域部署 |
> | Microsoft.ApiManagement/service/getssotoken/action | 取得 SSO 權杖，以供用來登入 API 管理服務舊版入口網站 (登入身分為系統管理員) |
> | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | 更新虛擬網路中執行的 Microsoft.ApiManagement 資源，以挑選更新後的網路設定。 |
> | Microsoft.ApiManagement/service/users/action | 註冊新的使用者 |
> | Microsoft.ApiManagement/service/notifications/action | 將通知傳送給指定的使用者 |
> | ApiManagement/服務/閘道/動作 | 抓取閘道設定。 或更新閘道的信號。 |
> | Microsoft.ApiManagement/service/apis/read | 列出 API 管理服務實例的所有 Api。 或取得其識別碼所指定之 API 的詳細資料。 |
> | Microsoft.ApiManagement/service/apis/write | 建立新的或更新 API 管理服務實例的現有指定 API。 或更新 API 管理服務實例的指定 API。 |
> | Microsoft.ApiManagement/service/apis/delete | 刪除 API 管理服務實例的指定 API。 |
> | Microsoft.ApiManagement/service/apis/diagnostics/read | 列出 API 的所有診斷。 或取得其識別碼所指定之 API 的診斷詳細資料。 |
> | Microsoft.ApiManagement/service/apis/diagnostics/write | 為 API 建立新的診斷，或更新現有的診斷。 或更新識別碼所指定之 API 的診斷詳細資料。 |
> | Microsoft.ApiManagement/service/apis/diagnostics/delete | 從 API 中刪除指定的診斷。 |
> | Microsoft.ApiManagement/service/apis/issues/read | 列出與指定的 API 相關聯的所有問題。 或取得其識別碼所指定之 API 問題的詳細資料。 |
> | Microsoft.ApiManagement/service/apis/issues/write | 為 API 建立新的問題，或更新現有的問題。 或更新 API 的現有問題。 |
> | Microsoft.ApiManagement/service/apis/issues/delete | 從 API 中刪除指定的問題。 |
> | Microsoft.ApiManagement/service/apis/issues/attachments/read | 列出與指定的 API 相關聯之問題的所有附件。 或取得其識別碼所指定之 API 的問題附件詳細資料。 |
> | Microsoft.ApiManagement/service/apis/issues/attachments/write | 針對 API 中的問題建立新的附件，或更新現有的附件。 |
> | Microsoft.ApiManagement/service/apis/issues/attachments/delete | 從問題中刪除指定的批註。 |
> | Microsoft.ApiManagement/service/apis/issues/comments/read | 列出與指定的 API 相關聯之問題的所有批註。 或取得其識別碼所指定之 API 問題批註的詳細資料。 |
> | Microsoft.ApiManagement/service/apis/issues/comments/write | 在 API 中建立問題的新批註，或更新現有的批註。 |
> | Microsoft.ApiManagement/service/apis/issues/comments/delete | 從問題中刪除指定的批註。 |
> | Microsoft.ApiManagement/service/apis/operations/read | 列出指定之 API 的作業集合。 或取得其識別碼所指定之 API 作業的詳細資料。 |
> | Microsoft.ApiManagement/service/apis/operations/write | 在 API 中建立新的作業，或更新現有的作業。 或更新識別碼所指定之 API 中的作業詳細資料。 |
> | Microsoft.ApiManagement/service/apis/operations/delete | 刪除 API 中指定的作業。 |
> | Microsoft.ApiManagement/service/apis/operations/policies/read | 取得 API 作業層級的原則設定清單。 或取得 API 作業層級的原則設定。 |
> | Microsoft.ApiManagement/service/apis/operations/policies/write | 建立或更新 API 作業層級的原則設定。 |
> | Microsoft.ApiManagement/service/apis/operations/policies/delete | 刪除 Api 作業的原則設定。 |
> | Microsoft.ApiManagement/service/apis/operations/policy/read | 取得作業層級的原則設定 |
> | Microsoft.ApiManagement/service/apis/operations/policy/write | 在作業層級建立原則設定 |
> | Microsoft.ApiManagement/service/apis/operations/policy/delete | 刪除作業層級的原則設定 |
> | Microsoft.ApiManagement/service/apis/operations/tags/read | 列出與作業相關聯的所有標記。 或取得與作業相關聯的標記。 |
> | Microsoft.ApiManagement/service/apis/operations/tags/write | 將標記指派給作業。 |
> | Microsoft.ApiManagement/service/apis/operations/tags/delete | 從作業卸離標記。 |
> | Microsoft.ApiManagement/service/apis/operationsByTags/read | 列出與標記相關聯的作業集合。 |
> | Microsoft.ApiManagement/service/apis/policies/read | 取得 API 層級的原則設定。 或取得 API 層級的原則設定。 |
> | Microsoft.ApiManagement/service/apis/policies/write | 建立或更新 API 的原則設定。 |
> | Microsoft.ApiManagement/service/apis/policies/delete | 刪除 Api 上的原則設定。 |
> | Microsoft.ApiManagement/service/apis/policy/read | 取得 API 層級的原則設定 |
> | Microsoft.ApiManagement/service/apis/policy/write | 在 API 層級建立原則設定 |
> | Microsoft.ApiManagement/service/apis/policy/delete | 刪除 API 層級的原則設定 |
> | Microsoft.ApiManagement/service/apis/products/read | 列出 API 所屬的所有產品。 |
> | Microsoft.ApiManagement/service/apis/releases/read | 列出 API 的所有版本。<br>Api 版本會在建立 API 修訂時建立。<br>版本也會用來回複至先前的修訂。<br>結果會進行分頁，且可以受 $top 和 $skip 參數的限制。<br>或傳回 API 版本的詳細資料。 |
> | Microsoft.ApiManagement/service/apis/releases/delete | 移除 API 的所有版本，或刪除 API 中指定的版本。 |
> | Microsoft.ApiManagement/service/apis/releases/write | 為 API 建立新的版本。 或更新識別碼所指定之 API 版本的詳細資料。 |
> | Microsoft.ApiManagement/service/apis/revisions/read | 列出 API 的所有修訂。 |
> | Microsoft.ApiManagement/service/apis/revisions/delete | 移除 API 的所有修訂 |
> | Microsoft.ApiManagement/service/apis/schemas/read | 取得 API 層級的架構設定。 或取得 API 層級的架構設定。 |
> | Microsoft.ApiManagement/service/apis/schemas/write | 建立或更新 API 的架構設定。 |
> | Microsoft.ApiManagement/service/apis/schemas/delete | 刪除 Api 上的架構設定。 |
> | Microsoft.ApiManagement/service/apis/tagDescriptions/read | 列出 API 範圍中的所有標記描述。 類似于 swagger tagDescription 的模型是在 API 層級上定義，但標記可能會指派給作業，或在 API 範圍中取得標記描述 |
> | Microsoft.ApiManagement/service/apis/tagDescriptions/write | 在 Api 的範圍中建立/更新標記描述。 |
> | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | 刪除 Api 的標記描述。 |
> | Microsoft.ApiManagement/service/apis/tags/read | 列出與 API 相關聯的所有標記。 或取得與 API 相關聯的標記。 |
> | Microsoft.ApiManagement/service/apis/tags/write | 將標記指派給 Api。 |
> | Microsoft.ApiManagement/service/apis/tags/delete | 從 Api 卸離標記。 |
> | Microsoft.ApiManagement/service/apisByTags/read | 列出與標記相關聯的 api 集合。 |
> | Microsoft.ApiManagement/service/apiVersionSets/read | 列出指定服務實例中的 API 版本集集合。 或取得其識別碼指定的 Api 版本設定詳細資料。 |
> | Microsoft.ApiManagement/service/apiVersionSets/write | 建立或更新 Api 版本集。 或更新識別碼所指定之 Api VersionSet 的詳細資料。 |
> | Microsoft.ApiManagement/service/apiVersionSets/delete | 刪除特定的 Api 版本設定。 |
> | Microsoft.ApiManagement/service/apiVersionSets/versions/read | 取得版本實體的清單 |
> | Microsoft.ApiManagement/service/authorizationServers/read | 列出服務實例內定義的授權伺服器集合。 或取得無秘密授權伺服器的詳細資料。 |
> | Microsoft.ApiManagement/service/authorizationServers/write | 建立新的授權伺服器，或更新現有的授權伺服器。 或更新識別碼所指定之授權伺服器的詳細資料。 |
> | Microsoft.ApiManagement/service/authorizationServers/delete | 刪除特定授權伺服器實例。 |
> | ApiManagement/service/authorizationServers/listSecrets/action | 取得授權伺服器的密碼。 |
> | Microsoft.ApiManagement/service/backends/read | 列出指定之服務實例中的後端集合。 或取得其識別碼所指定後端的詳細資料。 |
> | Microsoft.ApiManagement/service/backends/write | 建立或更新後端。 或更新現有的後端。 |
> | Microsoft.ApiManagement/service/backends/delete | 刪除指定的後端。 |
> | Microsoft.ApiManagement/service/backends/reconnect/action | 通知 APIM proxy 在指定的超時時間之後，建立與後端的新連接。 如果未指定任何超時，則會使用2分鐘的超時時間。 |
> | ApiManagement/service/cache/read | 列出指定服務實例中所有外部快取的集合。 或取得其識別碼所指定之快取的詳細資料。 |
> | ApiManagement/service/cache/write | 建立或更新要在 Api 管理實例中使用的外部快取。 或更新識別碼所指定之快取的詳細資料。 |
> | ApiManagement/service/cache/delete | 刪除特定的快取。 |
> | Microsoft.ApiManagement/service/certificates/read | 列出指定服務實例中所有憑證的集合。 或取得識別碼所指定之憑證的詳細資料。 |
> | Microsoft.ApiManagement/service/certificates/write | 建立或更新用來向後端進行驗證的憑證。 |
> | Microsoft.ApiManagement/service/certificates/delete | 刪除特定的憑證。 |
> | Microsoft.ApiManagement/service/contentTypes/read | 傳回內容類型清單 |
> | Microsoft.ApiManagement/service/contentTypes/contentItems/read | 傳回內容項目清單或傳回內容項目詳細資料 |
> | Microsoft.ApiManagement/service/contentTypes/contentItems/write | 建立新的內容項目或更新指定的內容項目 |
> | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | 移除指定的內容項目。 |
> | Microsoft.ApiManagement/service/diagnostics/read | 列出 API 管理服務實例的所有診斷。 或取得識別碼所指定之診斷的詳細資料。 |
> | Microsoft.ApiManagement/service/diagnostics/write | 建立新的診斷或更新現有的診斷。 或更新識別碼所指定之診斷的詳細資料。 |
> | Microsoft.ApiManagement/service/diagnostics/delete | 刪除指定的診斷。 |
> | ApiManagement/服務/閘道/讀取 | 列出已向服務實例註冊的閘道集合。 或取得其識別碼所指定之閘道的詳細資料。 |
> | ApiManagement/服務/閘道/寫入 | 建立或更新要在 Api 管理實例中使用的閘道。 或更新識別碼所指定之閘道的詳細資料。 |
> | ApiManagement/服務/閘道/刪除 | 刪除特定的閘道。 |
> | ApiManagement/服務/閘道/listKeys/動作 | 抓取閘道金鑰。 |
> | ApiManagement/服務/閘道/regenerateKey/動作 | 重新產生指定的閘道金鑰 invalidationg 使用它建立的任何權杖。 |
> | ApiManagement/服務/閘道/generateToken/動作 | 取得閘道的共用存取授權權杖。 |
> | ApiManagement/服務/閘道/api/讀取 | 列出與閘道相關聯的 Api 集合。 |
> | ApiManagement/服務/閘道/api/寫入 | 將 API 新增至指定的閘道。 |
> | ApiManagement/服務/閘道/api/刪除 | 從指定的閘道刪除指定的 API。 |
> | ApiManagement/服務/閘道/hostnameConfigurations/read | 列出指定閘道的主機名稱設定集合。 |
> | Microsoft.ApiManagement/service/groups/read | 列出服務實例內定義的群組集合。 或取得其識別碼所指定之群組的詳細資料。 |
> | Microsoft.ApiManagement/service/groups/write | 建立或更新群組。 或更新識別碼所指定之群組的詳細資料。 |
> | Microsoft.ApiManagement/service/groups/delete | 刪除 API 管理服務實例的特定群組。 |
> | Microsoft.ApiManagement/service/groups/users/read | 列出與群組相關聯的使用者實體集合。 |
> | Microsoft.ApiManagement/service/groups/users/write | 將現有使用者新增至現有群組 |
> | Microsoft.ApiManagement/service/groups/users/delete | 從現有的群組中移除現有的使用者。 |
> | Microsoft.ApiManagement/service/identityProviders/read | 列出指定的服務實例中設定的身分識別提供者集合。 或取得沒有秘密之身分識別提供者的設定詳細資料。 |
> | Microsoft.ApiManagement/service/identityProviders/write | 建立或更新 IdentityProvider 設定。 或更新現有的 IdentityProvider 設定。 |
> | Microsoft.ApiManagement/service/identityProviders/delete | 刪除指定的身分識別提供者設定。 |
> | ApiManagement/service/identityProviders/listSecrets/action | 取得身分識別提供者秘密。 |
> | ApiManagement/服務/問題/閱讀 | 列出指定服務實例中的問題集合。 或取得 API 管理問題詳細資料 |
> | Microsoft.ApiManagement/service/locations/networkstatus/read | 取得位置中服務相依資源的網路存取狀態。 |
> | Microsoft.ApiManagement/service/loggers/read | 列出指定服務實例中的記錄器集合。 或取得識別碼所指定之記錄器的詳細資料。 |
> | Microsoft.ApiManagement/service/loggers/write | 建立或更新記錄器。 或更新現有的記錄器。 |
> | Microsoft.ApiManagement/service/loggers/delete | 刪除指定的記錄器。 |
> | ApiManagement/service/namedValues/read | 列出服務實例內所定義的命名值集合。 或取得其識別碼所指定之指定值的詳細資料。 |
> | ApiManagement/service/namedValues/write | 建立或更新名為的值。 或更新特定的指定值。 |
> | ApiManagement/service/namedValues/delete | 從 API 管理服務實例刪除特定的指定值。 |
> | ApiManagement/service/namedValues/listValue/action | 取得識別碼所指定之指定值的秘密。 |
> | Microsoft.ApiManagement/service/networkstatus/read | 取得服務相依資源的網路存取狀態。 |
> | Microsoft.ApiManagement/service/notifications/read | 列出服務實例內定義的屬性集合。 或取得識別碼所指定之通知的詳細資料。 |
> | Microsoft.ApiManagement/service/notifications/write | 建立或更新 API 管理發行者通知。 |
> | Microsoft.ApiManagement/service/notifications/recipientEmails/read | 取得訂閱通知的通知收件者電子郵件清單。 |
> | Microsoft.ApiManagement/service/notifications/recipientEmails/write | 將電子郵件地址加入通知的收件者清單中。 |
> | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | 從通知清單中移除電子郵件。 |
> | Microsoft.ApiManagement/service/notifications/recipientUsers/read | 取得通知收件者使用者訂閱通知的清單。 |
> | Microsoft.ApiManagement/service/notifications/recipientUsers/write | 將 API 管理使用者新增至通知的收件者清單。 |
> | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | 從通知清單中移除 API 管理使用者。 |
> | Microsoft.ApiManagement/service/openidConnectProviders/read | 所有 OpenId Connect 提供者的清單。 或取得不具秘密的特定 OpenID Connect 提供者。 |
> | Microsoft.ApiManagement/service/openidConnectProviders/write | 建立或更新 OpenID Connect 提供者。 或更新特定 OpenID Connect 提供者。 |
> | Microsoft.ApiManagement/service/openidConnectProviders/delete | 刪除 API 管理服務實例的特定 OpenID Connect 提供者。 |
> | ApiManagement/service/openidConnectProviders/listSecrets/action | 取得特定 OpenID Connect 提供者秘密。 |
> | Microsoft.ApiManagement/service/operationresults/read | 取得長時間執行之作業的目前狀態 |
> | Microsoft.ApiManagement/service/policies/read | 列出 Api 管理服務的所有全域原則定義。 或取得 Api 管理服務的全域原則定義。 |
> | Microsoft.ApiManagement/service/policies/write | 建立或更新 Api 管理服務的全域原則設定。 |
> | Microsoft.ApiManagement/service/policies/delete | 刪除 Api 管理服務的全域原則設定。 |
> | Microsoft. ApiManagement/service/policy/read | 取得租使用者層級的原則設定 |
> | ApiManagement/service/policy/write | 在租使用者層級建立原則設定 |
> | ApiManagement/服務/原則/刪除 | 刪除租使用者層級上的原則設定 |
> | ApiManagement/service/policyDescriptions/read | 列出所有原則描述。 |
> | Microsoft.ApiManagement/service/policySnippets/read | 列出所有原則程式碼片段。 |
> | ApiManagement/service/portalSettings/read | 列出入口網站設定的集合。 或取得入口網站的登入設定，或取得入口網站的註冊設定，或取得入口網站的委派設定。 |
> | ApiManagement/service/portalSettings/write | 更新 Sign-In 設定。 或建立或更新 Sign-In 設定。 或更新註冊設定或更新註冊設定或更新委派設定。 或建立或更新委派設定。 |
> | ApiManagement/service/portalSettings/listSecrets/action | 取得入口網站委派設定的驗證金鑰。 或取得媒體內容 blob 容器 uri。 |
> | Microsoft.ApiManagement/service/products/read | 列出指定服務實例中的產品集合。 或取得其識別碼所指定之產品的詳細資料。 |
> | Microsoft.ApiManagement/service/products/write | 建立或更新產品。 或更新現有的產品詳細資料。 |
> | Microsoft.ApiManagement/service/products/delete | 刪除產品。 |
> | Microsoft.ApiManagement/service/products/apis/read | 列出與產品相關聯的 Api 集合。 |
> | Microsoft.ApiManagement/service/products/apis/write | 將 API 新增至指定的產品。 |
> | Microsoft.ApiManagement/service/products/apis/delete | 從指定的產品中刪除指定的 API。 |
> | Microsoft.ApiManagement/service/products/groups/read | 列出與指定產品相關聯的開發人員群組集合。 |
> | Microsoft.ApiManagement/service/products/groups/write | 加入指定之開發人員群組與指定產品之間的關聯。 |
> | Microsoft.ApiManagement/service/products/groups/delete | 刪除指定群組和產品之間的關聯。 |
> | Microsoft.ApiManagement/service/products/policies/read | 取得產品層級的原則設定。 或取得產品層級的原則設定。 |
> | Microsoft.ApiManagement/service/products/policies/write | 建立或更新產品的原則設定。 |
> | Microsoft.ApiManagement/service/products/policies/delete | 刪除產品的原則設定。 |
> | Microsoft.ApiManagement/service/products/policy/read | 取得產品層級的原則設定 |
> | Microsoft.ApiManagement/service/products/policy/write | 在產品層級建立原則設定 |
> | Microsoft.ApiManagement/service/products/policy/delete | 刪除產品層級的原則設定 |
> | Microsoft.ApiManagement/service/products/subscriptions/read | 列出指定產品的訂閱集合。 |
> | Microsoft.ApiManagement/service/products/tags/read | 列出與產品相關聯的所有標記。 或取得與產品相關聯的標記。 |
> | Microsoft.ApiManagement/service/products/tags/write | 將標記指派給產品。 |
> | Microsoft.ApiManagement/service/products/tags/delete | 從產品卸離標記。 |
> | Microsoft.ApiManagement/service/productsByTags/read | 列出與標記相關聯的產品集合。 |
> | Microsoft.ApiManagement/service/properties/read | 列出服務實例內定義的屬性集合。 或取得其識別碼所指定之屬性的詳細資料。 |
> | Microsoft.ApiManagement/service/properties/write | 建立或更新屬性。 或更新特定屬性。 |
> | Microsoft.ApiManagement/service/properties/delete | 刪除 API 管理服務實例中的特定屬性。 |
> | ApiManagement/service/properties/listSecrets/action | 取得其識別碼所指定之屬性的密碼。 |
> | Microsoft.ApiManagement/service/quotas/read | 取得配額的值 |
> | Microsoft.ApiManagement/service/quotas/write | 設定配額計數器目前的值 |
> | Microsoft.ApiManagement/service/quotas/periods/read | 取得期間的配額計數器值 |
> | Microsoft.ApiManagement/service/quotas/periods/write | 設定配額計數器目前的值 |
> | ApiManagement/服務/區域/讀取 | 列出服務所在的所有 azure 區域。 |
> | Microsoft.ApiManagement/service/reports/read | 取得依時間週期彙總的報告、取得依地理區域彙總的報告，或取得依開發人員彙總的報告。<br>或者，取得依產品彙總的報告。<br>或者，取得依 API 彙總的報告、取得依作業彙總的報告，或取得依訂用帳戶彙總的報告。<br>或者，取得報告資料的要求 |
> | Microsoft. ApiManagement/service/settings/read | 列出租使用者設定的集合。 一律為空白。 改為使用/settings/public |
> | Microsoft.ApiManagement/service/subscriptions/read | 列出 API 管理服務實例的所有訂用帳戶。 或取得指定的訂閱實體 (沒有索引鍵) 。 |
> | Microsoft.ApiManagement/service/subscriptions/write | 建立或更新指定之產品的指定使用者訂用帳戶。 或更新識別碼所指定之訂用帳戶的詳細資料。 |
> | Microsoft.ApiManagement/service/subscriptions/delete | 刪除指定的訂閱。 |
> | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | 重新產生 API 管理服務實例現有訂用帳戶的主要金鑰。 |
> | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | 重新產生 API 管理服務實例現有訂用帳戶的次要金鑰。 |
> | ApiManagement/service/訂用帳戶/listSecrets/動作 | 取得指定的訂用帳戶金鑰。 |
> | Microsoft.ApiManagement/service/tagResources/read | 列出與標記相關聯的資源集合。 |
> | Microsoft.ApiManagement/service/tags/read | 列出服務實例內定義的標記集合。 或取得識別碼所指定之標記的詳細資料。 |
> | Microsoft.ApiManagement/service/tags/write | 建立標記。 或更新識別碼所指定之標記的詳細資料。 |
> | Microsoft.ApiManagement/service/tags/delete | 刪除 API 管理服務實例的特定標記。 |
> | Microsoft.ApiManagement/service/templates/read | 取得所有電子郵件範本，或取得 API 管理電子郵件範本詳細資料 |
> | Microsoft.ApiManagement/service/templates/write | 建立或更新 API 管理電子郵件範本，或更新 API 管理電子郵件範本 |
> | Microsoft.ApiManagement/service/templates/delete | 重設預設的 API 管理電子郵件範本 |
> | Microsoft.ApiManagement/service/tenant/read | 列出租使用者存取設定的集合。 或取得 Api 管理服務的全域原則定義。 或取得租使用者存取訊號詳細資料 |
> | Microsoft.ApiManagement/service/tenant/write | 設定租使用者的原則設定或更新租使用者存取訊號詳細資料，或更新租使用者存取訊號詳細資料 |
> | Microsoft.ApiManagement/service/tenant/delete | 移除租用戶的原則組態 |
> | ApiManagement/service/tenant/listSecrets/action | 取得租用戶存取資訊的詳細資料 |
> | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | 重新產生主要存取金鑰 |
> | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | 重新產生次要存取金鑰 |
> | Microsoft.ApiManagement/service/tenant/deploy/action | 執行部署工作，以將所指定 git 分支的變更套用至資料庫中的組態。 |
> | Microsoft.ApiManagement/service/tenant/save/action | 在存放庫的指定分支中建立具有組態快照集的認可 |
> | Microsoft.ApiManagement/service/tenant/validate/action | 驗證所指定 git 分支的變更 |
> | Microsoft.ApiManagement/service/tenant/operationResults/read | 取得作業結果的清單，或取得特定作業的結果 |
> | Microsoft.ApiManagement/service/tenant/syncState/read | 取得上次 git 同步處理的狀態 |
> | Microsoft.ApiManagement/service/users/read | 列出指定的服務實例中已註冊的使用者集合。 或取得使用者的識別碼所指定之使用者的詳細資料。 |
> | Microsoft.ApiManagement/service/users/write | 建立或更新使用者。 或更新使用者的識別碼所指定之使用者的詳細資料。 |
> | Microsoft.ApiManagement/service/users/delete | 刪除特定使用者。 |
> | Microsoft.ApiManagement/service/users/generateSsoUrl/action | 抓取包含驗證權杖的重新導向 URL，以將指定的使用者簽入開發人員入口網站。 |
> | Microsoft.ApiManagement/service/users/token/action | 取得使用者的共用存取授權權杖。 |
> | Microsoft.ApiManagement/service/users/confirmations/send/action | 傳送確認 |
> | Microsoft.ApiManagement/service/users/groups/read | 列出所有使用者群組。 |
> | ApiManagement/服務/使用者/身分識別/讀取 | 所有使用者身分識別的清單。 |
> | Microsoft.ApiManagement/service/users/keys/read | 取得與使用者相關聯的金鑰 |
> | Microsoft.ApiManagement/service/users/subscriptions/read | 列出指定之使用者的訂閱集合。 |

### <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

Azure 服務：核心

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | AppConfiguration/註冊/動作 | 註冊訂用帳戶以使用 Microsoft 應用程式設定。 |
> | AppConfiguration/取消註冊/動作 | 使用 Microsoft 應用程式設定取消註冊訂用帳戶。 |
> | AppConfiguration/checkNameAvailability/read | 檢查資源名稱是否可供使用。 |
> | AppConfiguration/configurationStores/read | 取得指定之設定存放區的屬性，或列出指定資源群組或訂用帳戶下的所有設定存放區。 |
> | AppConfiguration/configurationStores/write | 使用指定的參數建立或更新設定存放區。 |
> | AppConfiguration/configurationStores/delete | 刪除設定存放區。 |
> | AppConfiguration/configurationStores/ListKeys/action | 列出指定之設定存放區的 API 金鑰。 |
> | AppConfiguration/configurationStores/RegenerateKey/action | 重新產生指定之設定存放區的 API 金鑰。 |
> | AppConfiguration/configurationStores/ListKeyValue/action | 列出指定之設定存放區的索引鍵/值。 |
> | AppConfiguration/configurationStores/PrivateEndpointConnectionsApproval/action | 自動核准指定的設定存放區下的私人端點連線。 |
> | AppConfiguration/configurationStores/eventGridFilters/read | 取得指定之設定存放區事件方格篩選的屬性，或列出指定設定存放區下的所有設定存放區事件方格篩選。 |
> | AppConfiguration/configurationStores/eventGridFilters/write | 使用指定的參數建立或更新設定存放區事件方格篩選。 |
> | AppConfiguration/configurationStores/eventGridFilters/delete | 刪除設定存放區事件方格篩選。 |
> | AppConfiguration/configurationStores/privateEndpointConnectionProxies/validate/action | 驗證指定的設定存放區下的私人端點連線 proxy。 |
> | AppConfiguration/configurationStores/privateEndpointConnectionProxies/read | 取得指定設定存放區下的私人端點連線 proxy。 |
> | AppConfiguration/configurationStores/privateEndpointConnectionProxies/write | 在指定的設定存放區下建立或更新私人端點連線 proxy。 |
> | AppConfiguration/configurationStores/privateEndpointConnectionProxies/delete | 刪除指定設定存放區下的私人端點連線 proxy。 |
> | AppConfiguration/configurationStores/privateEndpointConnections/read | 取得私人端點連線，或列出指定設定存放區下的私人端點連接。 |
> | AppConfiguration/configurationStores/privateEndpointConnections/write | 核准或拒絕指定設定存放區下的私人端點連線。 |
> | AppConfiguration/configurationStores/privateEndpointConnections/delete | 刪除指定設定存放區下的私人端點連線。 |
> | AppConfiguration/configurationStores/privateLinkResources/read | 列出指定設定存放區下的所有私人連結資源。 |
> | AppConfiguration/configurationStores/providers/Microsoft. Insights/diagnosticSettings/read | 讀取設定存放區的所有診斷設定值。 |
> | AppConfiguration/configurationStores/providers/Microsoft. Insights/diagnosticSettings/write | 適用于 Microsoft 應用程式設定的寫入/覆寫診斷設定。 |
> | AppConfiguration/configurationStores/providers/Microsoft. Insights/metricDefinitions/read | 取得 Microsoft 應用程式設定的所有度量定義。 |
> | AppConfiguration/位置/operationsStatus/讀取 | 取得作業的狀態。 |
> | AppConfiguration/operations/read | 列出 Microsoft 應用程式設定所支援的所有作業。 |
> | **DataAction** | **說明** |
> | AppConfiguration/configurationStores/keyValues/read | 從設定存放區讀取機碼值。 |
> | AppConfiguration/configurationStores/keyValues/write | 建立或更新設定存放區中的索引鍵/值。 |
> | AppConfiguration/configurationStores/keyValues/delete | 從設定存放區刪除現有的索引鍵/值。 |

### <a name="microsoftazurestack"></a>Microsoft.AzureStack

Azure 服務：核心

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.AzureStack/register/action | 訂用帳戶註冊動作 |
> | Microsoft.AzureStack/register/action | 向 Microsoft.AzureStack 資源提供者註冊訂用帳戶 |
> | AzureStack/cloudManifestFiles/read | 取得雲端資訊清單檔 |
> | AzureStack/edgeSubscriptions/read | 取得 Azure Stack Edge 訂用帳戶的屬性 |
> | AzureStack/edgeSubscriptions/write | 建立或更新 edge 訂用帳戶 |
> | AzureStack/edgeSubscriptions/delete | 刪除 Edge 訂用帳戶 |
> | AzureStack/edgeSubscriptions/edgeResourceGroups/action | 讀取或寫入邊緣資源群組下的投射邊緣資源 |
> | AzureStack/edgeSubscriptions/edgeProviders/action | 讀取或寫入指定 edge 資源提供者命名空間下的投射邊緣資源 |
> | AzureStack/edgeSubscriptions/operations/action | 取得或列出預估邊緣資源上的非同步作業狀態 |
> | AzureStack/linkedSubscriptions/read | 取得 Azure Stack 連結訂用帳戶的屬性 |
> | AzureStack/linkedSubscriptions/write | 建立或更新連結的訂用帳戶 |
> | AzureStack/linkedSubscriptions/delete | 刪除連結的訂用帳戶 |
> | AzureStack/linkedSubscriptions/linkedResourceGroups/action | 在連結的資源群組下讀取或寫入投射的連結資源 |
> | AzureStack/linkedSubscriptions/linkedProviders/action | 在指定的連結資源提供者命名空間下讀取或寫入投射的連結資源 |
> | AzureStack/linkedSubscriptions/operations/action | 取得或列出所投射連結資源上的非同步作業狀態 |
> | Microsoft.AzureStack/Operations/read | 取得資源提供者作業的屬性 |
> | Microsoft.AzureStack/registrations/read | 取得 Azure Stack 註冊的屬性 |
> | Microsoft.AzureStack/registrations/write | 建立或更新 Azure Stack 註冊 |
> | Microsoft.AzureStack/registrations/delete | 刪除 Azure Stack 註冊 |
> | Microsoft.AzureStack/registrations/getActivationKey/action | 取得最新的 Azure Stack 啟用金鑰 |
> | AzureStack/註冊/enableRemoteManagement/動作 | 針對 Azure Stack 註冊啟用 RemoteManagement |
> | Microsoft.AzureStack/registrations/customerSubscriptions/read | 取得 Azure Stack 客戶訂用帳戶的屬性 |
> | Microsoft.AzureStack/registrations/customerSubscriptions/write | 建立或更新 Azure Stack 客戶訂用帳戶 |
> | Microsoft.AzureStack/registrations/customerSubscriptions/delete | 刪除 Azure Stack 客戶訂用帳戶 |
> | Microsoft.AzureStack/registrations/products/read | 取得 Azure Stack Marketplace 產品的屬性 |
> | Microsoft.AzureStack/registrations/products/listDetails/action | 擷取 Azure Stack Marketplace 產品的延伸詳細資料 |
> | AzureStack/註冊/產品/getProducts/動作 | 抓取 Azure Stack Marketplace 產品的清單 |
> | AzureStack/註冊/產品/getProduct/動作 | 抓取 Azure Stack Marketplace 產品 |
> | AzureStack/註冊/產品/uploadProductLog/動作 | 記錄 Azure Stack Marketplace 產品操作狀態和時間戳記 |

### <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

Azure 服務： [Azure Stack Edge](../databox-online/azure-stack-edge-overview.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.databoxedge/availableSkus/read | 列出或取得可用的 sku |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | 建立或更新 Data Box Edge 裝置 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | 列出或取得 Data Box Edge 裝置 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | 刪除 Data Box Edge 裝置 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | 列出或取得 Data Box Edge 裝置 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | 列出或取得 Data Box Edge 裝置 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | 建立或更新 Data Box Edge 裝置 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/getExtendedInformation/action | 擷取資源延伸資訊 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/updateExtendedInformation/action | ArmApiDesc_action_updateExtendedInformation_dataBoxEdgeDevices |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | 掃描更新 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | 在裝置中下載更新 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | 在裝置上安裝更新 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | 上傳裝置註冊的憑證 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/generateCertificate/action | ArmApiDesc_action_generateCertificate_dataBoxEdgeDevices |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | 列出或取得警示 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | 列出或取得警示 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | 列出或取得頻寬排程 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | 列出或取得頻寬排程 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | 建立或更新頻寬排程 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | 刪除頻寬排程 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | 列出或取得作業結果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | 列出或取得作業 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | 列出或取得裝置網路設定 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/節點/讀取 | 列出或取得節點 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/operationResults/read | 列出或取得作業結果 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/operationsStatus/read | 列出或取得作業狀態 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/orders/read | 列出或取得訂單 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/orders/read | 列出或取得訂單 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/orders/write | 建立或更新訂單 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/orders/delete | 刪除訂單 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/orders/listDCAccessCode/action | ArmApiDesc_action_listDCAccessCode_orders |
> | Microsoft.databoxedge/dataBoxEdgeDevices/orders/operationResults/read | 列出或取得作業結果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | 列出或取得角色 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | 列出或取得角色 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | 建立或更新角色 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | 刪除角色 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/roles/monitoringConfig/write | 建立或更新監視設定 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/roles/monitoringConfig/delete | 刪除監視設定 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/roles/monitoringConfig/read | 列出或取得監視設定 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/roles/monitoringConfig/read | 列出或取得監視設定 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/roles/monitoringConfig/operationResults/read | 列出或取得作業結果 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/roles/operationResults/read | 列出或取得作業結果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | 更新安全性設定 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/securitySettings/operationResults/read | 列出或取得作業結果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | 列出或取得共用 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | 列出或取得共用 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | 建立或更新共用 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | 使用來自雲端的資料重新整理共用中繼資料 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | 刪除共用 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/共用/operationResults/read | 列出或取得作業結果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | 建立或更新儲存體帳戶認證 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | 列出或取得儲存體帳戶認證 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | 列出或取得儲存體帳戶認證 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | 刪除儲存體帳戶認證 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | 列出或取得作業結果 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/storageAccounts/read | 列出或取得儲存體帳戶 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/storageAccounts/read | 列出或取得儲存體帳戶 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/storageAccounts/write | 建立或更新儲存體帳戶 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/storageAccounts/delete | 刪除儲存體帳戶 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/storageAccounts/container/read | 列出或取得容器 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/storageAccounts/container/read | 列出或取得容器 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/storageAccounts/container/write | 建立或更新容器 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/storageAccounts/容器/刪除 | 刪除容器 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/storageAccounts/容器/refresh/action | 使用來自雲端的資料重新整理容器中繼資料 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/storageAccounts/container/operationResults/read | 列出或取得作業結果 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/storageAccounts/operationResults/read | 列出或取得作業結果 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/trigger/read | 列出或取得觸發程式 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/trigger/read | 列出或取得觸發程式 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/trigger/write | 建立或更新觸發程式 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/trigger/delete | 刪除觸發程式 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/trigger/operationResults/read | 列出或取得作業結果 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | 列出或取得更新摘要 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | 列出或取得共用使用者 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | 列出或取得共用使用者 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | 建立或更新共用使用者 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | 刪除共用使用者 |
> | Microsoft.databoxedge/dataBoxEdgeDevices/users/operationResults/read | 列出或取得作業結果 |
> | Microsoft.databoxedge/sku/read | 列出或取得 Sku |

### <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

Azure 服務： [資料目錄](../data-catalog/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.DataCatalog/checkNameAvailability/action | 檢查租用戶的目錄名稱可用性。 |
> | Microsoft.DataCatalog/register/action | 向 Microsoft.DataCatalog 資源提供者註冊訂用帳戶。 |
> | Microsoft.DataCatalog/unregister/action | 從 Microsoft.DataCatalog 資源提供者取消註冊訂用帳戶。 |
> | Microsoft.DataCatalog/catalogs/read | 取得訂用帳戶或資源群組下一或多個目錄的屬性。 |
> | Microsoft.DataCatalog/catalogs/write | 建立目錄，或更新目錄的標記和屬性。 |
> | Microsoft.DataCatalog/catalogs/delete | 刪除目錄。 |
> | Microsoft.DataCatalog/operations/read | 列出可以對 Microsoft.DataCatalog 資源提供者執行的作業。 |

### <a name="microsofteventgrid"></a>Microsoft.EventGrid

Azure 服務： [事件方格](../event-grid/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.EventGrid/register/action | 為 EventGrid 資源提供者註冊訂用帳戶。 |
> | Microsoft.EventGrid/unregister/action | 為 EventGrid 資源提供者取消註冊訂用帳戶。 |
> | Microsoft.EventGrid/domains/write | 建立或更新網域 |
> | Microsoft.EventGrid/domains/read | 讀取網域 |
> | Microsoft.EventGrid/domains/delete | 刪除網域 |
> | Microsoft.EventGrid/domains/listKeys/action | 列出網域的金鑰 |
> | Microsoft.EventGrid/domains/regenerateKey/action | 重新產生網域的金鑰 |
> | EventGrid/網域/privateEndpointConnectionProxies/驗證/動作 | 驗證網域的 PrivateEndpointConnectionProxies |
> | EventGrid/網域/privateEndpointConnectionProxies/讀取 | 讀取網域的 PrivateEndpointConnectionProxies |
> | EventGrid/網域/privateEndpointConnectionProxies/寫入 | 寫入網域的 PrivateEndpointConnectionProxies |
> | EventGrid/網域/privateEndpointConnectionProxies/刪除 | 刪除網域的 PrivateEndpointConnectionProxies |
> | EventGrid/網域/privateEndpointConnections/讀取 | 讀取網域的 PrivateEndpointConnections |
> | EventGrid/網域/privateEndpointConnections/寫入 | 寫入網域的 PrivateEndpointConnections |
> | EventGrid/網域/privateEndpointConnections/刪除 | 刪除網域的 PrivateEndpointConnections |
> | EventGrid/網域/privateLinkResources/讀取 | 取得或列出網域的 PrivateLinkResources |
> | EventGrid/網域/提供者/Microsoft. Insights/logDefinitions/read | 允許存取診斷記錄 |
> | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | 取得網域的可用計量 |
> | Microsoft.EventGrid/domains/topics/read | 讀取網域主題 |
> | EventGrid/網域/主題/撰寫 | 建立或更新網域主題 |
> | EventGrid/網域/主題/刪除 | 刪除網域主題 |
> | Microsoft.EventGrid/eventSubscriptions/write | 建立或更新 eventSubscription |
> | Microsoft.EventGrid/eventSubscriptions/read | 讀取 eventSubscription |
> | Microsoft.EventGrid/eventSubscriptions/delete | 刪除 eventSubscription |
> | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | 取得事件訂閱的完整 URL |
> | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | 取得事件訂閱的診斷設定 |
> | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新事件訂閱的診斷設定 |
> | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | 取得 eventSubscriptions 的可用計量 |
> | Microsoft.EventGrid/extensionTopics/read | 度取 extensionTopic。 |
> | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | 取得主題的診斷設定 |
> | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新主題的診斷設定 |
> | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | 取得主題的可用計量 |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | 列出區域事件訂用帳戶 |
> | Microsoft.EventGrid/locations/operationResults/read | 讀取區域作業的結果 |
> | Microsoft.EventGrid/locations/operationsStatus/read | 讀取區域作業的狀態 |
> | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | 依主題類型列出區域事件訂用帳戶 |
> | Microsoft.EventGrid/operationResults/read | 讀取作業的結果 |
> | Microsoft.EventGrid/operations/read | 列出 EventGrid 作業。 |
> | Microsoft.EventGrid/operationsStatus/read | 讀取作業的狀態 |
> | EventGrid/partnerNamespaces/providers/Microsoft. Insights/diagnosticSettings/read | 取得夥伴命名空間的診斷設定 |
> | EventGrid/partnerNamespaces/providers/Microsoft. Insights/diagnosticSettings/write | 建立或更新夥伴命名空間的診斷設定 |
> | EventGrid/partnerNamespaces/providers/Microsoft. Insights/logDefinitions/read | 允許存取診斷記錄 |
> | EventGrid/partnerNamespaces/providers/Microsoft. Insights/metricDefinitions/read | 取得夥伴命名空間的可用計量 |
> | EventGrid/partnerTopics/providers/Microsoft. Insights/diagnosticSettings/read | 取得夥伴主題的診斷設定 |
> | EventGrid/partnerTopics/providers/Microsoft. Insights/diagnosticSettings/write | 建立或更新合作夥伴主題的診斷設定 |
> | EventGrid/partnerTopics/providers/Microsoft. Insights/logDefinitions/read | 允許存取診斷記錄 |
> | EventGrid/partnerTopics/providers/Microsoft. Insights/metricDefinitions/read | 取得合作夥伴主題的可用計量 |
> | EventGrid/sku/read | 讀取事件方格資源的可用 Sku 定義 |
> | EventGrid/systemTopics/providers/Microsoft. Insights/diagnosticSettings/read | 取得系統主題的診斷設定 |
> | EventGrid/systemTopics/providers/Microsoft. Insights/diagnosticSettings/write | 建立或更新系統主題的診斷設定 |
> | EventGrid/systemTopics/providers/Microsoft. Insights/logDefinitions/read | 允許存取診斷記錄 |
> | EventGrid/systemTopics/providers/Microsoft. Insights/metricDefinitions/read | 取得系統主題的可用計量 |
> | Microsoft.EventGrid/topics/write | 建立或更新主題 |
> | Microsoft.EventGrid/topics/read | 讀取主題 |
> | Microsoft.EventGrid/topics/delete | 刪除主題 |
> | Microsoft.EventGrid/topics/listKeys/action | 列出主題的金鑰 |
> | Microsoft.EventGrid/topics/regenerateKey/action | 重新產生主題的金鑰 |
> | EventGrid/主題/privateEndpointConnectionProxies/validate/action | 驗證主題的 PrivateEndpointConnectionProxies |
> | EventGrid/主題/privateEndpointConnectionProxies/read | 閱讀主題的 PrivateEndpointConnectionProxies |
> | EventGrid/主題/privateEndpointConnectionProxies/write | 撰寫主題的 PrivateEndpointConnectionProxies |
> | EventGrid/主題/privateEndpointConnectionProxies/delete | 刪除主題的 PrivateEndpointConnectionProxies |
> | EventGrid/主題/privateEndpointConnections/read | 閱讀主題的 PrivateEndpointConnections |
> | EventGrid/主題/privateEndpointConnections/write | 撰寫主題的 PrivateEndpointConnections |
> | EventGrid/主題/privateEndpointConnections/delete | 刪除主題的 PrivateEndpointConnections |
> | EventGrid/主題/privateLinkResources/read | 閱讀主題的 PrivateLinkResources |
> | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | 取得主題的診斷設定 |
> | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新主題的診斷設定 |
> | EventGrid/主題/提供者/Microsoft. Insights/logDefinitions/read | 允許存取診斷記錄 |
> | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | 取得主題的可用計量 |
> | Microsoft.EventGrid/topictypes/read | 讀取 topictype |
> | Microsoft.EventGrid/topictypes/eventSubscriptions/read | 依主題類型列出全域事件訂用帳戶 |
> | Microsoft.EventGrid/topictypes/eventtypes/read | 讀取 topictype 支援的 eventtypes |

### <a name="microsoftlogic"></a>Microsoft.Logic

Azure 服務： [Logic Apps](../logic-apps/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Logic/register/action | 為指定的訂用帳戶註冊 Microsoft.Logic 資源提供者。 |
> | Microsoft.Logic/integrationAccounts/read | 讀取整合帳戶。 |
> | Microsoft.Logic/integrationAccounts/write | 建立或更新整合帳戶。 |
> | Microsoft.Logic/integrationAccounts/delete | 刪除整合帳戶。 |
> | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | 重新產生存取金鑰祕密。 |
> | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | 取得整合帳戶的回呼 URL。 |
> | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | 取得金鑰保存庫中的金鑰。 |
> | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | 記錄整合帳戶中的追蹤事件。 |
> | Microsoft.Logic/integrationAccounts/join/action | 加入整合帳戶。 |
> | Microsoft.Logic/integrationAccounts/agreements/read | 讀取整合帳戶中的合約。 |
> | Microsoft.Logic/integrationAccounts/agreements/write | 建立或更新整合帳戶中的合約。 |
> | Microsoft.Logic/integrationAccounts/agreements/delete | 刪除整合帳戶中的合約。 |
> | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | 取得整合帳戶中合約內容的回呼 URL。 |
> | Microsoft.Logic/integrationAccounts/assemblies/read | 讀取整合帳戶中的組件。 |
> | Microsoft.Logic/integrationAccounts/assemblies/write | 建立或更新整合帳戶中的組件。 |
> | Microsoft.Logic/integrationAccounts/assemblies/delete | 刪除整合帳戶中的組件。 |
> | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | 取得整合帳戶中組件內容的回呼 URL。 |
> | Microsoft.Logic/integrationAccounts/batchConfigurations/read | 讀取整合帳戶中的批次設定。 |
> | Microsoft.Logic/integrationAccounts/batchConfigurations/write | 建立或更新整合帳戶中的批次設定。 |
> | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | 刪除整合帳戶中的批次設定。 |
> | Microsoft.Logic/integrationAccounts/certificates/read | 讀取整合帳戶中的憑證。 |
> | Microsoft.Logic/integrationAccounts/certificates/write | 建立或更新整合帳戶中的憑證。 |
> | Microsoft.Logic/integrationAccounts/certificates/delete | 刪除整合帳戶中的憑證。 |
> | Microsoft. 邏輯/integrationAccounts/groups/read | 讀取整合帳戶中的群組。 |
> | Microsoft. 邏輯/integrationAccounts/groups/write | 建立或更新整合帳戶中的群組。 |
> | Microsoft. 邏輯/integrationAccounts/groups/delete | 刪除整合帳戶中的群組。 |
> | Microsoft.Logic/integrationAccounts/maps/read | 讀取整合帳戶中的對應。 |
> | Microsoft.Logic/integrationAccounts/maps/write | 建立或更新整合帳戶中的對應。 |
> | Microsoft.Logic/integrationAccounts/maps/delete | 刪除整合帳戶中的對應。 |
> | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | 取得整合帳戶中對應內容的回呼 URL。 |
> | Microsoft.Logic/integrationAccounts/partners/read | 讀取整合帳戶中的合作夥伴。 |
> | Microsoft.Logic/integrationAccounts/partners/write | 建立或更新整合帳戶中的合作夥伴。 |
> | Microsoft.Logic/integrationAccounts/partners/delete | 刪除整合帳戶中的合作夥伴。 |
> | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | 取得整合帳戶中合作夥伴內容的回呼 URL。 |
> | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | 讀取整合帳戶記錄定義。 |
> | Microsoft. 邏輯/integrationAccounts/rosettaNetProcessConfigurations/read | 讀取整合帳戶中的 RosettaNet 程式設定。 |
> | Microsoft. 邏輯/integrationAccounts/rosettaNetProcessConfigurations/write | 建立或更新整合帳戶中的 RosettaNet 程式設定。 |
> | Microsoft. 邏輯/integrationAccounts/rosettaNetProcessConfigurations/delete | 刪除整合帳戶中的 RosettaNet 程式設定。 |
> | Microsoft. 邏輯/integrationAccounts/排程/讀取 | 讀取整合帳戶中的排程。 |
> | Microsoft. 邏輯/integrationAccounts/排程/寫入 | 建立或更新整合帳戶中的排程。 |
> | Microsoft. 邏輯/integrationAccounts/排程/刪除 | 刪除整合帳戶中的排程。 |
> | Microsoft.Logic/integrationAccounts/schemas/read | 讀取整合帳戶中的結構描述。 |
> | Microsoft.Logic/integrationAccounts/schemas/write | 建立或更新整合帳戶中的結構描述。 |
> | Microsoft.Logic/integrationAccounts/schemas/delete | 刪除整合帳戶中的結構描述。 |
> | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | 取得整合帳戶中結構描述內容的回呼 URL。 |
> | Microsoft.Logic/integrationAccounts/sessions/read | 讀取整合帳戶中的會話。 |
> | Microsoft.Logic/integrationAccounts/sessions/write | 建立或更新整合帳戶中的工作階段。 |
> | Microsoft.Logic/integrationAccounts/sessions/delete | 刪除整合帳戶中的工作階段。 |
> | Microsoft. 邏輯/integrationAccounts/usageConfigurations/read | 讀取整合帳戶中的使用方式設定。 |
> | Microsoft. 邏輯/integrationAccounts/usageConfigurations/write | 建立或更新整合帳戶中的使用方式設定。 |
> | Microsoft. 邏輯/integrationAccounts/usageConfigurations/delete | 刪除整合帳戶中的使用方式設定。 |
> | IntegrationAccounts/usageConfigurations/listCallbackUrl/action | 取得整合帳戶中使用方式設定的回呼 URL。 |
> | Microsoft.Logic/integrationServiceEnvironments/read | 讀取整合服務環境。 |
> | Microsoft.Logic/integrationServiceEnvironments/write | 建立或更新整合服務環境。 |
> | Microsoft.Logic/integrationServiceEnvironments/delete | 刪除整合服務環境。 |
> | Microsoft.Logic/integrationServiceEnvironments/join/action | 聯結整合服務環境。 |
> | Microsoft. 邏輯/integrationServiceEnvironments/availableManagedApis/read | 讀取整合服務環境可用的受控 Api。 |
> | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | 讀取整合服務環境受控 API。 |
> | Microsoft. 邏輯/integrationServiceEnvironments/managedApis/write | 建立或更新整合服務環境受控 API。 |
> | Microsoft. 邏輯/integrationServiceEnvironments/managedApis/join/action | 加入整合服務環境受控 API。 |
> | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | 讀取整合服務環境受控 API 作業。 |
> | IntegrationServiceEnvironments/managedApis/operationStatuses/read | 讀取整合服務環境受控 API 作業狀態。 |
> | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | 讀取整合服務環境作業狀態。 |
> | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | 讀取整合服務環境計量定義。 |
> | Microsoft.Logic/locations/workflows/validate/action | 驗證工作流程。 |
> | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | 取得工作流程建議的作業群組。 |
> | Microsoft.Logic/operations/read | 取得作業。 |
> | Microsoft.Logic/workflows/read | 讀取工作流程。 |
> | Microsoft.Logic/workflows/write | 建立或更新工作流程。 |
> | Microsoft.Logic/workflows/delete | 刪除工作流程。 |
> | Microsoft.Logic/workflows/run/action | 啟動工作流程的執行。 |
> | Microsoft.Logic/workflows/disable/action | 停用工作流程。 |
> | Microsoft.Logic/workflows/enable/action | 啟用工作流程。 |
> | Microsoft.Logic/workflows/suspend/action | 暫止工作流程。 |
> | Microsoft.Logic/workflows/validate/action | 驗證工作流程。 |
> | Microsoft.Logic/workflows/move/action | 將工作流程從其現有的訂用帳戶識別碼、資源群組和/或名稱改為不同的訂用帳戶識別碼、資源群組和/或名稱。 |
> | Microsoft.Logic/workflows/listSwagger/action | 取得工作流程 Swagger 定義。 |
> | Microsoft.Logic/workflows/regenerateAccessKey/action | 重新產生存取金鑰祕密。 |
> | Microsoft.Logic/workflows/listCallbackUrl/action | 取得工作流程的回呼 URL。 |
> | Microsoft.Logic/workflows/accessKeys/read | 讀取存取金鑰。 |
> | Microsoft.Logic/workflows/accessKeys/write | 建立或更新存取金鑰。 |
> | Microsoft.Logic/workflows/accessKeys/delete | 刪除存取金鑰。 |
> | Microsoft.Logic/workflows/accessKeys/list/action | 列出存取金鑰祕密。 |
> | Microsoft.Logic/workflows/accessKeys/regenerate/action | 重新產生存取金鑰祕密。 |
> | Microsoft. 邏輯/工作流程/偵測器/讀取 | 讀取工作流程偵測器。 |
> | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | 讀取工作流程診斷設定。 |
> | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新工作流程診斷設定。 |
> | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | 讀取工作流程記錄定義。 |
> | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | 讀取工作流程計量定義。 |
> | Microsoft.Logic/workflows/runs/read | 讀取工作流程的執行。 |
> | Microsoft. 邏輯/工作流程/執行/刪除 | 刪除工作流程的執行。 |
> | Microsoft.Logic/workflows/runs/cancel/action | 取消工作流程的執行。 |
> | Microsoft.Logic/workflows/runs/actions/read | 讀取工作流程的執行動作。 |
> | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | 取得工作流程執行動作運算式追蹤。 |
> | Microsoft.Logic/workflows/runs/actions/repetitions/read | 讀取工作流程執行動作重複作業。 |
> | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | 取得工作流程執行動作重複作業運算式追蹤。 |
> | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | 讀取工作流程執行重複動作要求歷程記錄。 |
> | Microsoft.Logic/workflows/runs/actions/requestHistories/read | 讀取工作流程執行動作要求歷程記錄。 |
> | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | 讀取工作流程執行動作範圍重複作業。 |
> | Microsoft.Logic/workflows/runs/operations/read | 讀取工作流程的執行作業狀態。 |
> | Microsoft.Logic/workflows/triggers/read | 讀取觸發程序。 |
> | Microsoft.Logic/workflows/triggers/run/action | 執行觸發程序。 |
> | Microsoft.Logic/workflows/triggers/reset/action | 重設觸發程序。 |
> | Microsoft.Logic/workflows/triggers/setState/action | 設定觸發程序狀態。 |
> | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | 取得觸發程序的回呼 URL。 |
> | Microsoft.Logic/workflows/triggers/histories/read | 讀取觸發程序歷程記錄。 |
> | Microsoft.Logic/workflows/triggers/histories/resubmit/action | 重新提交工作流程的觸發程序。 |
> | Microsoft.Logic/workflows/versions/read | 讀取工作流程版本。 |
> | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | 取得觸發程序的回呼 URL。 |

### <a name="microsoftrelay"></a>Microsoft.Relay

Azure 服務： [Azure 轉送](../service-bus-relay/relay-what-is-it.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Relay/checkNamespaceAvailability/action | 檢查給定訂用帳戶下的命名空間是否可用。 此 API 即將淘汰。請改用 CheckNameAvailabiltiy。 |
> | Microsoft.Relay/checkNameAvailability/action | 檢查給定訂用帳戶下的命名空間是否可用。 |
> | Microsoft.Relay/register/action | 針對轉送資源提供者註冊訂用帳戶，並讓您能夠建立轉送資源 |
> | Microsoft.Relay/unregister/action | 針對轉送資源提供者註冊訂用帳戶，並讓您能夠建立轉送資源 |
> | Microsoft.Relay/namespaces/write | 建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。 |
> | Microsoft.Relay/namespaces/read | 取得命名空間資源描述的清單 |
> | Microsoft.Relay/namespaces/Delete | 刪除命名空間資源 |
> | Microsoft.Relay/namespaces/authorizationRules/action | 更新命名空間授權規則。 此 API 即將淘汰。 請改用 PUT 呼叫來更新命名空間授權規則。 API 版本 2017-04-01 不支援此作業。 |
> | Microsoft.Relay/namespaces/removeAcsNamepsace/action | 移除 ACS 命名空間 |
> | Microsoft. 轉送/命名空間/privateEndpointConnectionsApproval/動作 | 核准私人端點連線 |
> | Microsoft.Relay/namespaces/authorizationRules/read | 取得命名空間授權規則描述的清單。 |
> | Microsoft.Relay/namespaces/authorizationRules/write | 建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | Microsoft.Relay/namespaces/authorizationRules/delete | 刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。  |
> | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | 取得命名空間的連接字串 |
> | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | 對資源重新產生主要或次要金鑰 |
> | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 檢查指定訂用帳戶下命名空間別名的可用性。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | 建立或更新與命名空間相關聯的災害復原設定。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | 取得與命名空間相關聯的災害復原設定。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | 刪除與命名空間相關聯的災害復原設定。 此作業只能透過主要命名空間叫用。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | 停用災害復原，並停止將變更從主要命名空間複寫至次要命名空間。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | 叫用異地災害復原容錯移轉，並將命名空間別名重新設定為指向次要命名空間。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | 取得災害復原主要命名空間的授權規則 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 取得災害復原主要命名空間的授權規則金鑰 |
> | Microsoft.Relay/namespaces/HybridConnections/write | 建立或更新 HybridConnection 屬性。 |
> | Microsoft.Relay/namespaces/HybridConnections/read | 取得 HybridConnection 資源描述的清單 |
> | Microsoft.Relay/namespaces/HybridConnections/Delete | 用來刪除 HybridConnection 資源的作業 |
> | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | 更新 HybridConnection 的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  取得 HybridConnection 授權規則的清單 |
> | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | 建立 HybridConnection 授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | 用來刪除 HybridConnection 授權規則的作業 |
> | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | 取得 HybridConnection 的連接字串 |
> | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | 對資源重新產生主要或次要金鑰 |
> | Microsoft.Relay/namespaces/messagingPlan/read | 取得命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> | Microsoft.Relay/namespaces/messagingPlan/write | 更新命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> | Microsoft. 轉送/命名空間/networkrulesets/read | 取得 NetworkRuleSet 資源 |
> | Microsoft. 轉送/命名空間/networkrulesets/write | 建立 VNET 規則資源 |
> | Microsoft. 轉送/命名空間/networkrulesets/刪除 | 刪除 VNET 規則資源 |
> | Microsoft.Relay/namespaces/operationresults/read | 取得命名空間作業的狀態 |
> | Microsoft. 轉送/命名空間/privateEndpointConnectionProxies/驗證/動作 | 驗證私人端點連線 Proxy |
> | Microsoft. 轉送/命名空間/privateEndpointConnectionProxies/read | 取得私人端點連線 Proxy |
> | Microsoft. 轉送/命名空間/privateEndpointConnectionProxies/write | 建立私人端點連接 Proxy |
> | Microsoft. 轉送/命名空間/privateEndpointConnectionProxies/刪除 | 刪除私人端點連線 Proxy |
> | Microsoft. 轉送/命名空間/privateEndpointConnectionProxies/>operationstatus/read | 取得非同步私人端點操作的狀態 |
> | Microsoft. 轉送/命名空間/privateEndpointConnections/read | 取得私人端點連線 |
> | Microsoft. 轉送/命名空間/privateEndpointConnections/write | 建立或更新私人端點連線 |
> | Microsoft. 轉送/命名空間/privateEndpointConnections/刪除 | 移除私人端點連線 |
> | Microsoft. 轉送/命名空間/privateEndpointConnections/>operationstatus/read | 取得非同步私人端點操作的狀態 |
> | Microsoft. 轉送/命名空間/privateLinkResources/read | 取得支援私人端點連接的資源類型 |
> | Microsoft. 轉送/命名空間/提供者/Microsoft. Insights/diagnosticSettings/read | 取得命名空間診斷設定資源描述的清單 |
> | Microsoft. 轉送/命名空間/提供者/Microsoft. Insights/diagnosticSettings/write | 取得命名空間診斷設定資源描述的清單 |
> | Microsoft. 轉送/命名空間/提供者/Microsoft. Insights/logDefinitions/read | 取得命名空間記錄資源描述的清單 |
> | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 取得命名空間計量資源描述的清單 |
> | Microsoft.Relay/namespaces/WcfRelays/write | 建立或更新 WcfRelay 屬性。 |
> | Microsoft.Relay/namespaces/WcfRelays/read | 取得 WcfRelay 資源描述的清單 |
> | Microsoft.Relay/namespaces/WcfRelays/Delete | 用來刪除 WcfRelay 資源的作業 |
> | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | 更新 WcfRelay 的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  取得 WcfRelay 授權規則的清單 |
> | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | 建立 WcfRelay 授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | 用來刪除 WcfRelay 授權規則的作業 |
> | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | 取得 WcfRelay 的連接字串 |
> | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | 對資源重新產生主要或次要金鑰 |
> | Microsoft.Relay/operations/read | 取得作業 |

### <a name="microsoftservicebus"></a>Microsoft.ServiceBus

Azure 服務： [服務匯流排](../service-bus/index.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.ServiceBus/checkNamespaceAvailability/action | 檢查給定訂用帳戶下的命名空間是否可用。 此 API 即將淘汰。請改用 CheckNameAvailabiltiy。 |
> | Microsoft.ServiceBus/checkNameAvailability/action | 檢查給定訂用帳戶下的命名空間是否可用。 |
> | Microsoft.ServiceBus/register/action | 針對 ServiceBus 資源提供者註冊訂用帳戶，並讓您能夠建立 ServiceBus 資源 |
> | Microsoft.ServiceBus/unregister/action | 針對 ServiceBus 資源提供者註冊訂用帳戶，並讓您能夠建立 ServiceBus 資源 |
> | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | 針對指定的 VNet，刪除 ServiceBus 資源提供者中的 VNet 規則 |
> | Microsoft.ServiceBus/namespaces/write | 建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。 |
> | Microsoft.ServiceBus/namespaces/read | 取得命名空間資源描述的清單 |
> | Microsoft.ServiceBus/namespaces/Delete | 刪除命名空間資源 |
> | Microsoft.ServiceBus/namespaces/authorizationRules/action | 更新命名空間授權規則。 此 API 即將淘汰。 請改用 PUT 呼叫來更新命名空間授權規則。 API 版本 2017-04-01 不支援此作業。 |
> | Microsoft.ServiceBus/namespaces/migrate/action | 移轉命名空間作業 |
> | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | 移除 ACS 命名空間 |
> | Microsoft. 執行空間/命名空間/privateEndpointConnectionsApproval/action | 核准私人端點連線 |
> | Microsoft.ServiceBus/namespaces/authorizationRules/write | 建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | Microsoft.ServiceBus/namespaces/authorizationRules/read | 取得命名空間授權規則描述的清單。 |
> | Microsoft.ServiceBus/namespaces/authorizationRules/delete | 刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。  |
> | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | 取得命名空間的連接字串 |
> | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | 對資源重新產生主要或次要金鑰 |
> | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 檢查指定訂用帳戶下命名空間別名的可用性。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | 建立或更新與命名空間相關聯的災害復原設定。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | 取得與命名空間相關聯的災害復原設定。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | 刪除與命名空間相關聯的災害復原設定。 此作業只能透過主要命名空間叫用。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | 停用災害復原，並停止將變更從主要命名空間複寫至次要命名空間。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | 叫用異地災害復原容錯移轉，並將命名空間別名重新設定為指向次要命名空間。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | 取得災害復原主要命名空間的授權規則 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 取得災害復原主要命名空間的授權規則金鑰 |
> | Microsoft.ServiceBus/namespaces/eventGridFilters/write | 建立或更新與命名空間相關聯的事件格線篩選。 |
> | Microsoft.ServiceBus/namespaces/eventGridFilters/read | 取得與命名空間相關聯的事件格線篩選。 |
> | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | 刪除與命名空間相關聯的事件格線篩選。 |
> | Microsoft.ServiceBus/namespaces/eventhubs/read | 取得 EventHub 資源描述的清單 |
> | Microsoft.ServiceBus/namespaces/ipFilterRules/read | 取得 IP 篩選資源 |
> | Microsoft.ServiceBus/namespaces/ipFilterRules/write | 建立 IP 篩選資源 |
> | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | 刪除 IP 篩選資源 |
> | Microsoft.ServiceBus/namespaces/messagingPlan/read | 取得命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> | Microsoft.ServiceBus/namespaces/messagingPlan/write | 更新命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | 建立或更新移轉設定。 這會開始將資源從標準命名空間同步至進階命名空間 |
> | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | 取得指出移轉和暫止複寫作業狀態的移轉設定 |
> | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | 刪除移轉設定。 |
> | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | 還原從標準到進階命名空間的移轉 |
> | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | 將與標準命名空間建立關聯的 DNS 指派給進階命名空間，這會完成移轉，並停止將資源從標準命名空間同步至進階命名空間 |
> | Microsoft.：//命名空間/networkruleset/read | 取得 NetworkRuleSet 資源 |
> | Microsoft.：//命名空間/networkruleset/write | 建立 VNET 規則資源 |
> | Microsoft.：//命名空間/networkruleset/刪除 | 刪除 VNET 規則資源 |
> | Microsoft.：//命名空間/networkrulesets/read | 取得 NetworkRuleSet 資源 |
> | Microsoft.：//命名空間/networkrulesets/write | 建立 VNET 規則資源 |
> | Microsoft.：//命名空間/networkrulesets/刪除 | 刪除 VNET 規則資源 |
> | Microsoft.ServiceBus/namespaces/operationresults/read | 取得命名空間作業的狀態 |
> | Microsoft 驗證/命名空間/privateEndpointConnectionProxies/驗證/動作 | 驗證私人端點連線 Proxy |
> | Microsoft.：//命名空間/privateEndpointConnectionProxies/read | 取得私人端點連線 Proxy |
> | Microsoft.：//命名空間/privateEndpointConnectionProxies/write | 建立私人端點連接 Proxy |
> | Microsoft.：//命名空間/privateEndpointConnectionProxies/刪除 | 刪除私人端點連線 Proxy |
> | Microsoft.：//命名空間/privateEndpointConnectionProxies/>operationstatus/read | 取得非同步私人端點操作的狀態 |
> | Microsoft.：//命名空間/privateEndpointConnections/read | 取得私人端點連線 |
> | Microsoft.：//命名空間/privateEndpointConnections/write | 建立或更新私人端點連線 |
> | Microsoft.：//命名空間/privateEndpointConnections/刪除 | 移除私人端點連線 |
> | Microsoft.：//命名空間/privateEndpointConnections/>operationstatus/read | 取得非同步私人端點操作的狀態 |
> | Microsoft.：//命名空間/privateLinkResources/read | 取得支援私人端點連接的資源類型 |
> | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 取得命名空間診斷設定資源描述的清單 |
> | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 取得命名空間診斷設定資源描述的清單 |
> | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | 取得命名空間記錄資源描述的清單 |
> | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 取得命名空間計量資源描述的清單 |
> | Microsoft.ServiceBus/namespaces/queues/write | 建立或更新佇列屬性。 |
> | Microsoft.ServiceBus/namespaces/queues/read | 取得佇列資源描述的清單 |
> | Microsoft.ServiceBus/namespaces/queues/Delete | 用來刪除佇列資源的作業 |
> | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | 更新佇列的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | 建立佇列授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  取得佇列授權規則的清單 |
> | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | 用來刪除佇列授權規則的作業 |
> | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | 取得佇列的連接字串 |
> | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | 對資源重新產生主要或次要金鑰 |
> | Microsoft.//命名空間/sku/讀取 | 列出命名空間支援的 Sku |
> | Microsoft.ServiceBus/namespaces/topics/write | 建立或更新主題屬性。 |
> | Microsoft.ServiceBus/namespaces/topics/read | 取得主題資源描述的清單 |
> | Microsoft.ServiceBus/namespaces/topics/Delete | 用來刪除主題資源的作業 |
> | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | 更新主題的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | 建立主題授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  取得主題授權規則的清單 |
> | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | 用來刪除主題授權規則的作業 |
> | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | 取得主題的連接字串 |
> | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | 對資源重新產生主要或次要金鑰 |
> | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | 建立或更新 TopicSubscription 屬性。 |
> | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | 取得 TopicSubscription 資源描述的清單 |
> | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | 用來刪除 TopicSubscription 資源的作業 |
> | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | 建立或更新規則屬性。 |
> | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | 取得規則資源描述的清單 |
> | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | 用來刪除規則資源的作業 |
> | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | 取得 VNET 規則資源 |
> | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | 建立 VNET 規則資源 |
> | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | 刪除 VNET 規則資源 |
> | Microsoft.ServiceBus/operations/read | 取得作業 |
> | Microsoft.ServiceBus/sku/read | 取得 SKU 資源描述的清單 |
> | Microsoft.ServiceBus/sku/regions/read | 取得 SkuRegions 資源描述的清單 |
> | **DataAction** | **說明** |
> | Microsoft. 執行空間/命名空間/訊息/傳送/動作 | 傳送訊息 |
> | Microsoft. 執行空間/命名空間/訊息/接收/動作 | 接收訊息 |

## <a name="identity"></a>身分識別

### <a name="microsoftaad"></a>Microsoft.AAD

Azure 服務： [Azure Active Directory Domain Services](../active-directory-domain-services/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.AAD/unregister/action | 取消註冊網域服務 |
> | Microsoft.AAD/register/action | 註冊網域服務 |
> | Microsoft.AAD/domainServices/read | 讀取網域服務 |
> | Microsoft.AAD/domainServices/write | 寫入網域服務 |
> | Microsoft.AAD/domainServices/delete | 刪除網域服務 |
> | Microsoft.AAD/domainServices/oucontainer/read | 讀取 Ou 容器 |
> | Microsoft.AAD/domainServices/oucontainer/write | 寫入 Ou 容器 |
> | Microsoft.AAD/domainServices/oucontainer/delete | 刪除 Ou 容器 |
> | Microsoft.AAD/locations/operationresults/read |  |
> | Microsoft.AAD/Operations/read |  |

### <a name="microsoftaadiam"></a>microsoft.aadiam

Azure 服務： Azure Active Directory

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | aadiam/azureADMetrics/read | 讀取 Azure AD 計量定義 |
> | aadiam/azureADMetrics/write | 建立和更新 Azure AD 計量定義 |
> | aadiam/azureADMetrics/delete | 刪除 Azure AD 計量定義 |
> | microsoft.aadiam/diagnosticsettings/write | 正在寫入診斷設定 |
> | microsoft.aadiam/diagnosticsettings/read | 正在讀取診斷設定 |
> | microsoft.aadiam/diagnosticsettings/delete | 正在刪除診斷設定 |
> | microsoft.aadiam/diagnosticsettingscategories/read | 正在讀取診斷設定類別 |
> | aadiam/metricDefinitions/read | 讀取 Tenant-Level 度量定義 |
> | aadiam/計量/讀取 | 讀取 Tenant-Level 計量 |
> | aadiam/privateLinkForAzureAD/read | 讀取 Private Link 原則定義 |
> | aadiam/privateLinkForAzureAD/write | 建立和更新 Private Link 原則定義 |
> | aadiam/privateLinkForAzureAD/delete | 刪除 Private Link 原則定義 |
> | aadiam/privateLinkForAzureAD/privateEndpointConnectionProxies/read | 讀取 Private Link proxy |
> | aadiam/privateLinkForAzureAD/privateEndpointConnectionProxies/delete | 刪除 Private Link proxy |
> | aadiam/privateLinkForAzureAD/privateEndpointConnectionProxies/validate/action | 驗證 Private Link proxy |
> | aadiam/privateLinkForAzureAD/privateEndpointConnections/read | 讀取 PrivateEndpointConnections |
> | aadiam/privateLinkForAzureAD/privateEndpointConnections/write | 建立和更新 PrivateEndpointConnections |
> | aadiam/privateLinkForAzureAD/privateEndpointConnections/delete | 刪除 PrivateEndpointConnections |
> | aadiam/privateLinkForAzureAD/privateLinkResources/read | 讀取 PrivateLinkResources |
> | aadiam/privateLinkForAzureAD/privateLinkResources/write | 建立和更新 PrivateLinkResources |
> | aadiam/privateLinkForAzureAD/privateLinkResources/delete | 刪除 PrivateLinkResources |

### <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

Azure 服務： [Azure Active Directory](../active-directory/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.ADHybridHealthService/configuration/action | 更新租用戶組態。 |
> | Microsoft.ADHybridHealthService/services/action | 更新租用戶中的服務執行個體。 |
> | Microsoft.ADHybridHealthService/addsservices/action | 為租用戶建立新的樹系。 |
> | Microsoft.ADHybridHealthService/register/action | 註冊 ADHybrid 健康情況服務資源提供者，並允許建立 ADHybrid 健康情況服務資源。 |
> | Microsoft.ADHybridHealthService/unregister/action | 將 ADHybrid 健康情況服務資源提供者的訂用帳戶取消註冊。 |
> | Microsoft.ADHybridHealthService/addsservices/write | 建立或更新租用戶的 ADDomainService 執行個體。 |
> | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | 對服務新增伺服器執行個體。 |
> | Microsoft.ADHybridHealthService/addsservices/read | 取得指定服務名稱的服務詳細資料。 |
> | Microsoft.ADHybridHealthService/addsservices/delete | 刪除服務及其伺服器以及健康情況資料。 |
> | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | 取得指定服務名稱的所有伺服器。 |
> | Microsoft.ADHybridHealthService/addsservices/alerts/read | 取得樹系的警示詳細資料，例如 alertid、警示引發日期、上次偵測到的警示、警示說明、上次更新日期、警示層級、警示狀態、警示疑難排解連結等等。 |
> | Microsoft.ADHybridHealthService/addsservices/configuration/read | 取得樹系的服務組態。 範例 - 樹系名稱、功能等級、網域命名主機 FSMO 角色、架構主機 FSMO 角色等等。 |
> | Microsoft.ADHybridHealthService/addsservices/dimensions/read | 取得樹系的網域和網站詳細資料。 範例 - 健康情況狀態、作用中警示、已解決的警示、屬性 (例如，網域功能等級、樹系、基礎結構主機、PDC、RID 主機等等)。  |
> | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | 取得樹系的使用者喜好設定。<br>範例 - MetricCounterName，例如 ldapsuccessfulbinds、ntlmauthentications、kerberosauthentications、addsinsightsagentprivatebytes、ldapsearches。<br>UI 圖表等項目的設定。 |
> | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | 取得指定樹系的樹系摘要，例如樹系名稱、此樹系下的網域數目、網站數目和網站詳細資料等。 |
> | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | 取得指定服務的支援計量清單。<br>例如，ADFS 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomainService 的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>ADSync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | 在指定了服務時，此 API 會取得計量資訊。<br>例如，此 API 可用來取得與下列項目相關的資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | 此 API 會取得進階租用戶所有已上架 ADDomainServices 的清單。 |
> | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | 取得指定服務名稱的所有伺服器複寫詳細資料。 |
> | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | 取得網域控制站數目和其存在的任何複寫錯誤。 |
> | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | 取得完整的網域控制站清單，以及指定樹系的複寫詳細資料。 |
> | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | 刪除指定服務和租用戶的伺服器。 |
> | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | 在 ADDomainService 的伺服器註冊期間，會呼叫此 API 來取得用於將新伺服器上架的認證。 |
> | Microsoft.ADHybridHealthService/configuration/write | 建立租用戶組態。 |
> | Microsoft.ADHybridHealthService/configuration/read | 讀取租用戶組態。 |
> | Microsoft.ADHybridHealthService/logs/read | 取得租用戶的代理程式安裝和註冊記錄。 |
> | Microsoft.ADHybridHealthService/logs/contents/read | 取得 Blob 中所儲存的代理程式安裝和註冊記錄內容。 |
> | Microsoft.ADHybridHealthService/operations/read | 取得系統所支援作業的清單。 |
> | Microsoft.ADHybridHealthService/reports/availabledeployments/read | 取得可用區域清單，以供 DevOps 用來支援客戶事件。 |
> | Microsoft.ADHybridHealthService/reports/badpassword/read | 取得 Active Directory Federation Service 中所有使用者的錯誤密碼嘗試清單。 |
> | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | 取得 Blob SAS URI，其包含新近排入佇列的報告作業狀態和最終結果，執行該作業是為了了解指定租用戶每個 UserId、每個 IPAddress、每天發生的錯誤使用者名稱/密碼嘗試頻率。 |
> | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | 取得 DevOps 同意的租用戶清單。 通常用於客戶支援。 |
> | Microsoft.ADHybridHealthService/reports/isdevops/read | 取得可指出租用戶是否已獲得 DevOps 同意的值。 |
> | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | 更新所選 Dev Ops 租用戶的 userid (objectid)。 |
> | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | 取得指定租用戶所選取的部署。 |
> | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | 在指定了租用戶識別碼時，會取得租用戶儲存位置。 |
> | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | 取得會存取資料的地理位置。 |
> | Microsoft.ADHybridHealthService/services/write | 建立租用戶中的服務執行個體。 |
> | Microsoft.ADHybridHealthService/services/read | 讀取租用戶中的服務執行個體。 |
> | Microsoft.ADHybridHealthService/services/delete | 刪除租用戶中的服務執行個體。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/action | 建立或更新服務中的伺服器實例。 |
> | Microsoft.ADHybridHealthService/services/alerts/read | 讀取服務的警示。 |
> | Microsoft.ADHybridHealthService/services/alerts/read | 讀取服務的警示。 |
> | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | 在指定了功能名稱時，會確認服務是否已備妥一切所需而可使用該功能。 |
> | Microsoft.ADHybridHealthService/services/exporterrors/read | 取得指定 Sync 服務的匯出錯誤。 |
> | Microsoft.ADHybridHealthService/services/exportstatus/read | 取得指定服務的匯出狀態。 |
> | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | 取得指定服務和伺服器的警示意見反應。 |
> | ADHybridHealthService/services/ipAddressAggregates/read | 讀取嘗試存取服務的錯誤 Ip。 |
> | ADHybridHealthService/services/ipAddressAggregateSettings/read | 讀取錯誤 Ip 的警示閾值。 |
> | ADHybridHealthService/services/ipAddressAggregateSettings/write | 寫入錯誤 Ip 的警示閾值。 |
> | Microsoft.ADHybridHealthService/services/metricmetadata/read | 取得指定服務的支援計量清單。<br>例如，ADFS 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomainService 的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>ADSync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> | Microsoft.ADHybridHealthService/services/metrics/groups/read | 在指定了服務時，此 API 會取得計量資訊。<br>例如，此 API 可用來取得與下列項目相關的資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | 在指定了服務時，此 API 會取得指定服務的計量平均值。<br>例如，此 API 可用來取得與下列項目相關的資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | 在指定了服務時，此 API 會取得指定服務計量的彙總檢視。<br>例如，此 API 可用來取得與下列項目相關的資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | 新增或更新服務的監視組態。 |
> | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | 取得指定服務的監視組態。 |
> | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | 新增或更新服務的監視組態。 |
> | Microsoft.ADHybridHealthService/services/premiumcheck/read | 此 API 會取得進階租用戶所有已上架服務的清單。 |
> | ADHybridHealthService/services/reports/generateBlobUri/action | 產生具風險的 IP 報告，並傳回指向它的 URI。 |
> | ADHybridHealthService/services/reports/blobUris/read | 取得過去7天的所有具風險 IP 報表 Uri。 |
> | Microsoft.ADHybridHealthService/services/reports/details/read | 取得過去 7 天排行前 50 名有不正確密碼錯誤的使用者報告 |
> | Microsoft.ADHybridHealthService/services/servicemembers/read | 讀取服務中的伺服器執行個體。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/delete | 刪除服務中的伺服器執行個體。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | 讀取伺服器的警示。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | 在伺服器註冊期間，會呼叫此 API 來取得用於將新伺服器上架的認證。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | 針對指定的伺服器，此 API 會取得伺服器所要上傳的資料類型清單，以及每個上傳的最新時間。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | 取得指定 Sync 服務的 Sync 匯出錯誤。 |
> | ADHybridHealthService/services/servicemembers/計量/讀取 | 取得連接器的清單，並執行指定服務和服務成員的設定檔名稱。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | 在指定了服務時，此 API 會取得計量資訊。<br>例如，此 API 可用來取得與下列項目相關的資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | 取得指定租用戶的服務組態。 |
> | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | 取得指定租使用者的功能 allowlisting 狀態。 |

### <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

Azure 服務： [Azure Active Directory B2C](../active-directory-b2c/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.AzureActiveDirectory/register/action | 為 Microsoft.AzureActiveDirectory 資源提供者註冊訂用帳戶 |
> | Microsoft.AzureActiveDirectory/b2cDirectories/write | 建立或更新 B2C 目錄資源 |
> | Microsoft.AzureActiveDirectory/b2cDirectories/read | 檢視 B2C 目錄資源 |
> | Microsoft.AzureActiveDirectory/b2cDirectories/delete | 刪除 B2C 目錄資源 |
> | AzureActiveDirectory/b2ctenants/read | 列出使用者為其成員的所有 B2C 租使用者 |
> | AzureActiveDirectory/guestUsages/write | 建立或更新來賓使用方式資源 |
> | AzureActiveDirectory/guestUsages/read | 查看來賓使用方式資源 |
> | AzureActiveDirectory/guestUsages/delete | 刪除來賓使用方式資源 |
> | Microsoft.AzureActiveDirectory/operations/read | 讀取可供 Microsoft.AzureActiveDirectory 資源提供者使用的所有 API 作業 |

### <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

Azure 服務：[適用于 azure 資源的受控](../active-directory/managed-identities-azure-resources/index.yml)識別

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.ManagedIdentity/register/action | 為受控識別資源提供者註冊訂用帳戶 |
> | ManagedIdentity/身分識別/讀取 | 取得現有系統指派的身分識別 |
> | ManagedIdentity/operations/read | 列出 ManagedIdentity 資源提供者的可用作業 |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | 取得現有已指派使用者的身分識別 |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | 建立新的已指派使用者的身分識別，或更新與現有已指派使用者之身分識別相關聯的標記 |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | 刪除現有已指派使用者的身分識別 |
> | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | 用來將現有已指派使用者的身分識別指派給資源的 RBAC 動作 |

## <a name="security"></a>安全性

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Azure 服務： [Key Vault](../key-vault/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.KeyVault/register/action | 註冊訂用帳戶 |
> | Microsoft.KeyVault/unregister/action | 取消註冊訂用帳戶 |
> | Microsoft.KeyVault/checkNameAvailability/read | 確認 Key Vault 名稱有效，且並非使用中 |
> | Microsoft.KeyVault/deletedVaults/read | 檢視虛刪除之 Key Vault 的屬性 |
> | Microsoft.KeyVault/hsmPools/read | 檢視 HSM 集區的屬性 |
> | Microsoft.KeyVault/hsmPools/write | 建立新的 HSM 集區，或更新現有 HSM 集區的屬性 |
> | Microsoft.KeyVault/hsmPools/delete | 刪除 HSM 集區 |
> | Microsoft.KeyVault/hsmPools/joinVault/action | 將金鑰保存庫加入至 HSM 集區 |
> | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | 通知 Microsoft.KeyVault 正在刪除虛擬網路或子網路 |
> | Microsoft.KeyVault/locations/deletedVaults/read | 檢視虛刪除之 Key Vault 的屬性 |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | 清除虛刪除的 Key Vault |
> | Microsoft.KeyVault/locations/operationResults/read | 檢查長時間執行之作業的結果 |
> | KeyVault/managedHSMs/read | 查看受控 HSM 的屬性 |
> | KeyVault/managedHSMs/write | 建立新的受控 HSM 或更新現有受控 HSM 的屬性 |
> | KeyVault/managedHSMs/delete | 刪除受控 HSM |
> | Microsoft.KeyVault/operations/read | 列出可以對 Microsoft.KeyVault 資源提供者執行的作業 |
> | Microsoft.KeyVault/vaults/read | 檢視 Key Vault 的屬性 |
> | Microsoft.KeyVault/vaults/write | 建立新的 Key Vault，或更新現有 Key Vault 的屬性 |
> | Microsoft.KeyVault/vaults/delete | 刪除 Key Vault |
> | Microsoft.KeyVault/vaults/deploy/action | 在部署 Azure 資源時啟用 Key Vault 中祕密的存取權 |
> | Microsoft.KeyVault/vaults/accessPolicies/write | 藉由合併或取代來更新現有存取原則，或在保存庫中新增存取原則。 |
> | KeyVault/保存庫/eventGridFilters/read | 通知 KeyVault，正在查看 Key Vault 的 EventGrid 訂用帳戶 |
> | KeyVault/保存庫/eventGridFilters/寫入 | 通知 Microsoft KeyVault 正在建立 Key Vault 的新 EventGrid 訂用帳戶 |
> | KeyVault/保存庫/eventGridFilters/刪除 | 通知 KeyVault，正在刪除 Key Vault 的 EventGrid 訂用帳戶 |
> | KeyVault/保存庫/金鑰/讀取 | 列出指定之保存庫中的金鑰，或讀取指定之金鑰的目前版本。 |
> | KeyVault/保存庫/金鑰/寫入 | 建立新的金鑰，或更新現有的金鑰。 |
> | KeyVault/保存庫/金鑰/版本/讀取 | 列出指定之金鑰的版本，或讀取指定的金鑰版本。 |
> | Microsoft.KeyVault/vaults/secrets/read | 查看秘密的屬性，而非其值。 |
> | Microsoft.KeyVault/vaults/secrets/write | 建立新的秘密，或更新現有秘密的值。 |
> | **DataAction** | **說明** |
> | KeyVault/保存庫/certificatecas/刪除 | 刪除憑證簽發者 |
> | KeyVault/保存庫/certificatecas/read | 讀取憑證簽發者 |
> | KeyVault/保存庫/certificatecas/寫入 | 寫入憑證簽發者 |
> | KeyVault/保存庫/certificatecontacts/寫入 | 管理憑證連絡人 |
> | KeyVault/保存庫/憑證/刪除 | 刪除憑證。 |
> | KeyVault/保存庫/憑證/讀取 | 列出指定之金鑰保存庫中的憑證，或取得憑證的相關資訊。 |
> | KeyVault/保存庫/憑證/備份/動作 | 建立憑證的備份檔案。 檔案可以用來還原同一個訂用帳戶 Key Vault 中的憑證。 可能適用限制。 |
> | KeyVault/保存庫/憑證/清除/動作 | 清除憑證，使其無法復原。 |
> | KeyVault/保存庫/憑證/更新/動作 | 更新與指定憑證相關聯的指定屬性。 |
> | KeyVault/保存庫/憑證/建立/動作 | 建立新的憑證。 |
> | KeyVault/保存庫/憑證/匯入/動作 | 將包含私密金鑰的現有有效憑證匯入 Azure Key Vault。 要匯入的憑證可以是 PFX 或 PEM 格式。 |
> | KeyVault/保存庫/憑證/復原/動作 | 復原已刪除的憑證。 作業會執行刪除作業的反轉。 作業適用于啟用虛刪除的保存庫，而且必須在保留間隔期間發出。 |
> | KeyVault/保存庫/憑證/還原/動作 | 將備份的憑證及其所有版本還原至保存庫。 |
> | KeyVault/保存庫/金鑰/讀取 | 列出指定保存庫中的金鑰，或讀取索引鍵的屬性和公開內容。<br>若為非對稱金鑰，這項作業會公開公開金鑰，並包含執行公開金鑰演算法的能力，例如加密和驗證簽章。<br>私用金鑰和對稱金鑰永遠不會公開。 |
> | KeyVault/保存庫/金鑰/更新/動作 | 更新與指定索引鍵相關聯的指定屬性。 |
> | KeyVault/保存庫/金鑰/建立/動作 | 建立新的金鑰。 |
> | KeyVault/保存庫/金鑰/匯入/動作 | 匯出外部建立的金鑰、加以儲存，然後將金鑰參數和屬性傳回至用戶端。 |
> | KeyVault/保存庫/金鑰/復原/動作 | 復原已刪除的金鑰。 作業會執行刪除作業的反轉。 作業適用于啟用虛刪除的保存庫，而且必須在保留間隔期間發出。 |
> | KeyVault/保存庫/金鑰/還原/動作 | 將備份的金鑰及其所有版本還原至保存庫。 |
> | KeyVault/保存庫/金鑰/刪除 | 刪除金鑰。 |
> | KeyVault/保存庫/金鑰/備份/動作 | 建立金鑰的備份檔案。 檔案可以用來還原相同訂用帳戶 Key Vault 中的金鑰。 可能適用限制。 |
> | KeyVault/保存庫/金鑰/清除/動作 | 清除金鑰，使其無法復原。 |
> | KeyVault/保存庫/金鑰/加密/動作 | 使用金鑰加密純文字。 請注意，如果金鑰為非對稱金鑰，則可透過具有讀取存取權的主體來執行此作業。 |
> | KeyVault/保存庫/金鑰/解密/動作 | 使用金鑰來解密加密文字。 |
> | KeyVault/保存庫/金鑰/包裝/動作 | 使用 Key Vault 金鑰包裝對稱金鑰。 請注意，如果 Key Vault 的金鑰是非對稱的，則可以使用讀取存取來執行此作業。 |
> | KeyVault/保存庫/金鑰/解除包裝/動作 | 使用 Key Vault 金鑰解除包裝對稱金鑰。 |
> | KeyVault/保存庫/金鑰/符號/動作 | 使用金鑰簽署雜湊。 |
> | KeyVault/保存庫/金鑰/驗證/動作 | 確認雜湊。 請注意，如果金鑰為非對稱金鑰，則可透過具有讀取存取權的主體來執行此作業。 |
> | KeyVault/保存庫/秘密/刪除 | 刪除秘密。 |
> | KeyVault/保存庫/秘密/備份/動作 | 建立秘密的備份檔案。 檔案可以用來還原同一個訂用帳戶 Key Vault 中的密碼。 可能適用限制。 |
> | KeyVault/保存庫/秘密/清除/動作 | 清除秘密，使其無法復原。 |
> | KeyVault/保存庫/秘密/更新/動作 | 更新與指定的密碼相關聯的指定屬性。 |
> | KeyVault/保存庫/秘密/復原/動作 | 復原已刪除的秘密。 作業會執行刪除作業的反轉。 作業適用于啟用虛刪除的保存庫，而且必須在保留間隔期間發出。 |
> | KeyVault/保存庫/秘密/還原/動作 | 將備份的密碼及其所有版本還原至保存庫。 |
> | KeyVault/保存庫/秘密/readMetadata/動作 | 列出或查看秘密的屬性，而不是其值。 |
> | KeyVault/保存庫/秘密/>client.getsecret/動作 | 取得秘密的值。 |
> | KeyVault/保存庫/秘密/>client.setsecret/動作 | 建立新的秘密。 |
> | KeyVault/保存庫/storageaccounts/read | 讀取受控儲存體帳戶和 SAS 的定義。 |
> | KeyVault/保存庫/storageaccounts/set/action | 建立或更新受控儲存體帳戶的定義。 |
> | KeyVault/保存庫/storageaccounts/刪除 | 刪除受控儲存體帳戶的定義。 |
> | KeyVault/保存庫/storageaccounts/備份/動作 | 建立受控儲存體帳戶的定義及其 SAS (共用存取簽章) 的備份。 |
> | KeyVault/保存庫/storageaccounts/清除/動作 | 清除已虛刪除的受控儲存體帳戶或 SAS (共用存取簽章) 的定義。 |
> | KeyVault/保存庫/storageaccounts/regeneratekey/action | 重新產生受控儲存體帳戶的存取金鑰。 |
> | KeyVault/保存庫/storageaccounts/復原/動作 | 復原已虛刪除的受控儲存體帳戶或 SAS (共用存取簽章) 的定義。 |
> | KeyVault/保存庫/storageaccounts/還原/動作 | 將受控儲存體帳戶及其 SAS 的定義還原)  (共用存取簽章。 |
> | KeyVault/保存庫/storageaccounts/sas/設定/動作 | 建立或更新受管理儲存體帳戶) 定義的 SAS (共用存取簽章。 |
> | KeyVault/保存庫/storageaccounts/sas/刪除 | 刪除受控儲存體帳戶) 定義的 SAS (共用存取簽章。 |

### <a name="microsoftsecurity"></a>Microsoft.Security

Azure 服務： [安全性中心](../security-center/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Security/register/action | 為 Azure 資訊安全中心註冊訂用帳戶 |
> | Microsoft.Security/unregister/action | 從 Azure 資訊安全中心取消註冊訂用帳戶 |
> | Microsoft. Security/adaptiveNetworkHardenings/read | 取得 Azure 受保護資源的彈性網路強化建議 |
> | Microsoft. Security/adaptiveNetworkHardenings/強制/動作 | 在指定的網路安全性群組 (s 上建立相符的安全性規則，以強制執行指定的流量強化規則)  |
> | Microsoft.Security/advancedThreatProtectionSettings/read | 取得資源的進階威脅防護設定 |
> | Microsoft.Security/advancedThreatProtectionSettings/write | 更新資源的進階威脅防護設定 |
> | Microsoft.Security/alerts/read | 取得所有可用的安全性警訊 |
> | Microsoft.Security/applicationWhitelistings/read | 取得應用程式 allowlistings |
> | Microsoft.Security/applicationWhitelistings/write | 建立新的應用程式 allowlisting 或更新現有的應用程式 |
> | Microsoft. Security/assessmentMetadata/read | 取得訂用帳戶的可用安全性評定中繼資料 |
> | Microsoft. Security/assessmentMetadata/write | 建立或更新安全性評定中繼資料 |
> | Microsoft. 安全性/評量/讀取 | 取得訂用帳戶的安全性評量 |
> | Microsoft.Security/assessments/write | 在您的訂用帳戶上建立或更新安全性評量 |
> | Microsoft. Security/自動化/read | 取得範圍的自動化 |
> | Microsoft. Security/自動化/write | 建立或更新範圍的自動化 |
> | Microsoft. Security/自動化/delete | 刪除範圍的自動化 |
> | Microsoft. Security/自動化/validate/action | 驗證範圍的 automation 模型 |
> | Microsoft. Security/autoProvisioningSettings/read | 取得訂用帳戶的安全性自動布建設定 |
> | Microsoft. Security/autoProvisioningSettings/write | 建立或更新訂用帳戶的安全性自動布建設定 |
> | Microsoft.Security/complianceResults/read | 取得資源的合規性結果 |
> | Microsoft. Security/deviceSecurityGroups/write | 建立或更新 IoT 裝置安全性群組 |
> | Microsoft. Security/deviceSecurityGroups/delete | 刪除 IoT 裝置安全性群組 |
> | Microsoft. Security/deviceSecurityGroups/read | 取得 IoT 裝置安全性群組 |
> | Microsoft.Security/informationProtectionPolicies/read | 取得資源的資訊保護原則 |
> | Microsoft.Security/informationProtectionPolicies/write | 更新資源的資訊保護原則 |
> | Microsoft. Security/iotSecuritySolutions/write | 建立或更新 IoT 安全性解決方案 |
> | Microsoft. Security/iotSecuritySolutions/delete | 刪除 IoT 安全性解決方案 |
> | Microsoft. Security/iotSecuritySolutions/read | 取得 IoT 安全性解決方案 |
> | Microsoft. Security/iotSecuritySolutions/analyticsModels/read | 取得 IoT 安全性分析模型 |
> | Microsoft. Security/iotSecuritySolutions/analyticsModels/read | 取得 IoT 警示類型 |
> | Microsoft. Security/iotSecuritySolutions/analyticsModels/read | 取得 IoT 警示 |
> | Microsoft. Security/iotSecuritySolutions/analyticsModels/read | 取得 IoT 建議類型 |
> | Microsoft. Security/iotSecuritySolutions/analyticsModels/read | 取得 IoT 建議 |
> | Microsoft. Security/iotSecuritySolutions/analyticsModels/aggregatedAlerts/read | 取得 IoT 匯總警示 |
> | Microsoft. Security/iotSecuritySolutions/analyticsModels/aggregatedAlerts/解除/action | 關閉 IoT 匯總警示 |
> | Microsoft. Security/iotSecuritySolutions/analyticsModels/aggregatedRecommendations/read | 取得 IoT 匯總建議 |
> | Microsoft.Security/locations/read | 取得安全性資料位置 |
> | Microsoft.Security/locations/alerts/read | 取得所有可用的安全性警訊 |
> | Microsoft.Security/locations/alerts/dismiss/action | 關閉安全性警示 |
> | Microsoft.Security/locations/alerts/activate/action | 啟動安全性警訊 |
> | Microsoft.Security/locations/jitNetworkAccessPolicies/read | 取得 Just-in-Time 網路存取原則 |
> | Microsoft.Security/locations/jitNetworkAccessPolicies/write | 建立新的 Just-in-Time 網路存取原則，或更新現有的 Just-in-Time 網路存取原則 |
> | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | 刪除 Just-in-Time 網路存取原則 |
> | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | 起始 Just-in-Time 網路存取原則要求 |
> | Microsoft.Security/locations/tasks/read | 取得所有可用的安全性建議 |
> | Microsoft.Security/locations/tasks/start/action | 啟動安全性建議 |
> | Microsoft.Security/locations/tasks/resolve/action | 解析安全性建議 |
> | Microsoft.Security/locations/tasks/activate/action | 啟用安全性建議 |
> | Microsoft.Security/locations/tasks/dismiss/action | 關閉安全性建議 |
> | Microsoft.Security/policies/read | 取得安全性原則 |
> | Microsoft.Security/policies/write | 更新安全性原則 |
> | Microsoft.Security/pricings/read | 取得該範圍的定價設定 |
> | Microsoft.Security/pricings/write | 更新該範圍的定價設定 |
> | Microsoft.Security/pricings/delete | 刪除該範圍的定價設定 |
> | Microsoft.Security/securityContacts/read | 取得安全性連絡人 |
> | Microsoft.Security/securityContacts/write | 更新安全性連絡人 |
> | Microsoft.Security/securityContacts/delete | 刪除安全性連絡人 |
> | Microsoft.Security/securitySolutions/read | 取得安全性解決方案 |
> | Microsoft.Security/securitySolutions/write | 建立新的安全性解決方案，或更新現有的安全性解決方案 |
> | Microsoft.Security/securitySolutions/delete | 刪除安全性解決方案 |
> | Microsoft.Security/securitySolutionsReferenceData/read | 取得安全性解決方案參考資料 |
> | Microsoft.Security/securityStatuses/read | 取得 Azure 資源的安全性健康狀態 |
> | Microsoft.Security/securityStatusesSummaries/read | 取得該範圍的安全性狀態摘要 |
> | Microsoft.Security/settings/read | 取得範圍的設定 |
> | Microsoft.Security/settings/write | 更新範圍的設定 |
> | Microsoft.Security/tasks/read | 取得所有可用的安全性建議 |
> | Microsoft.Security/webApplicationFirewalls/read | 取得 Web 應用程式防火牆 |
> | Microsoft.Security/webApplicationFirewalls/write | 建立新的 Web 應用程式防火牆，或更新現有的 Web 應用程式防火牆 |
> | Microsoft.Security/webApplicationFirewalls/delete | 刪除 Web 應用程式防火牆 |
> | Microsoft.Security/workspaceSettings/read | 取得工作區設定 |
> | Microsoft.Security/workspaceSettings/write | 更新工作區設定 |
> | Microsoft.Security/workspaceSettings/delete | 刪除工作區設定 |
> | Microsoft.Security/workspaceSettings/connect/action | 變更工作區設定的重新連線設定 |

### <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

Azure 服務： Microsoft Monitoring Insights

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.SecurityGraph/diagnosticsettings/write | 正在寫入診斷設定 |
> | Microsoft.SecurityGraph/diagnosticsettings/read | 正在讀取診斷設定 |
> | Microsoft.SecurityGraph/diagnosticsettings/delete | 正在刪除診斷設定 |
> | Microsoft.SecurityGraph/diagnosticsettingscategories/read | 正在讀取診斷設定類別 |

### <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

Azure 服務： [Azure Sentinel](../sentinel/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | SecurityInsights/註冊/動作 | 註冊訂用帳戶以 Azure Sentinel |
> | SecurityInsights/取消註冊/動作 | 從 Azure Sentinel 取消註冊訂用帳戶 |
> | Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action | 檢查使用者授權和使用權 |
> | SecurityInsights/匯總/讀取 | 取得匯總資訊 |
> | SecurityInsights/alertRules/read | 取得警示規則 |
> | SecurityInsights/alertRules/write | 更新警示規則 |
> | SecurityInsights/alertRules/delete | 刪除警示規則 |
> | SecurityInsights/alertRules/actions/read | 取得警示規則的回應動作 |
> | SecurityInsights/alertRules/actions/write | 更新警示規則的回應動作 |
> | SecurityInsights/alertRules/actions/delete | 刪除警示規則的回應動作 |
> | SecurityInsights/書簽/讀取 | 取得書簽 |
> | SecurityInsights/書簽/寫入 | 更新書簽 |
> | SecurityInsights/書簽/刪除 | 刪除書簽 |
> | SecurityInsights/書簽/展開/動作 | 依特定的展開取得實體的相關實體 |
> | SecurityInsights/書簽/關係/讀取 | 取得書簽關聯 |
> | SecurityInsights/書簽/關係/寫入 | 更新書簽關聯 |
> | SecurityInsights/書簽/關係/刪除 | 刪除書簽關聯 |
> | SecurityInsights/案例/讀取 | 取得案例 |
> | SecurityInsights/案例/寫入 | 更新案例 |
> | SecurityInsights/案例/刪除 | 刪除案例 |
> | SecurityInsights/案例/留言/讀取 | 取得案例批註 |
> | SecurityInsights/案例/批註/寫入 | 建立案例批註 |
> | SecurityInsights/案例/調查/讀取 | 取得個案調查 |
> | SecurityInsights/案例/調查/撰寫 | 更新案例的中繼資料 |
> | SecurityInsights/dataConnectors/read | 取得資料連線器 |
> | SecurityInsights/dataConnectors/write | 更新資料連線器 |
> | SecurityInsights/dataConnectors/delete | 刪除資料連線器 |
> | SecurityInsights/實體/讀取 | 取得 sentinel 實體圖形 |
> | SecurityInsights/實體/時程表/動作 | 取得特定範圍的實體時間軸 |
> | SecurityInsights/實體/關係/讀取 | 取得實體與相關資源之間的關聯性 |
> | SecurityInsights/實體/關係/寫入 | 更新實體與相關資源之間的關聯性 |
> | SecurityInsights/實體/關係/刪除 | 刪除實體與相關資源之間的關聯性 |
> | SecurityInsights/entityQueries/read | 取得實體的調查擴充 |
> | SecurityInsights/事件/讀取 | 取得事件 |
> | SecurityInsights/事件/寫入 | 更新事件 |
> | SecurityInsights/事件/刪除 | 刪除事件 |
> | SecurityInsights/事件/留言/讀取 | 取得事件批註 |
> | SecurityInsights/事件/批註/寫入 | 建立事件的批註 |
> | SecurityInsights/事件/批註/刪除 | 刪除事件上的批註 |
> | SecurityInsights/事件/關係/讀取 | 取得事件和相關資源之間的關聯性 |
> | SecurityInsights/事件/關係/寫入 | 更新事件和相關資源之間的關聯性 |
> | SecurityInsights/事件/關係/刪除 | 刪除事件與相關資源之間的關聯性 |
> | SecurityInsights/officeConsents/read | 從 Microsoft Office 取得同意 |
> | SecurityInsights/officeConsents/delete | 從 Microsoft Office 中刪除同意 |
> | SecurityInsights/operations/read | 取得作業 |
> | Microsoft. SecurityInsights/settings/read | 取得設定 |
> | Microsoft. SecurityInsights/settings/write | 更新設定 |
> | Microsoft. SecurityInsights/settings/delete | 刪除設定 |
> | SecurityInsights/threatintelligence/read | 取得威脅情報 |
> | SecurityInsights/threatintelligence/write | 更新威脅情報 |
> | SecurityInsights/threatintelligence/delete | 刪除威脅情報 |
> | SecurityInsights/threatintelligence/query/action | 查詢威脅情報 |
> | SecurityInsights/threatintelligence/計量/動作 | 收集威脅情報計量 |
> | SecurityInsights/threatintelligence/bulkDelete/action | 大量刪除威脅情報 |
> | SecurityInsights/threatintelligence/bulkTag/action | 大量標記威脅情報 |
> | SecurityInsights/threatintelligence/createIndicator/action | 建立威脅情報指標 |
> | SecurityInsights/threatintelligence/queryIndicators/action | 查詢威脅情報指標 |
> | SecurityInsights/threatintelligence/指標/write | 更新威脅情報指標 |
> | SecurityInsights/threatintelligence/指標/刪除 | 刪除威脅情報指標 |
> | SecurityInsights/threatintelligence/指標/查詢/動作 | 查詢威脅情報指標 |
> | SecurityInsights/threatintelligence/指標/計量/動作 | 取得威脅情報指標計量 |
> | SecurityInsights/threatintelligence/指標/bulkDelete/action | 大量刪除威脅情報指標 |
> | SecurityInsights/threatintelligence/指標/bulkTag/action | 大量標記威脅情報指標 |
> | SecurityInsights/threatintelligence/指標/read | 取得威脅情報指標 |
> | SecurityInsights/threatintelligence/指標/appendTags/action | 將標記附加至威脅情報指標 |
> | SecurityInsights/threatintelligence/指標/replaceTags/action | 取代威脅情報指標的標記 |
> | SecurityInsights/threatintelligence/計量/讀取 | 收集威脅情報計量 |
> | SecurityInsights/Watchlists/read | 取得 Watchlists |
> | SecurityInsights/Watchlists/write | 建立 Watchlists |
> | SecurityInsights/Watchlists/delete | 刪除 Watchlists |

## <a name="devops"></a>DevOps

### <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

Azure 服務： [Azure 實驗室服務](../lab-services/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.DevTestLab/register/action | 註冊訂用帳戶 |
> | Microsoft.DevTestLab/labCenters/delete | 刪除實驗室中心。 |
> | Microsoft.DevTestLab/labCenters/read | 讀取實驗室中心。 |
> | Microsoft.DevTestLab/labCenters/write | 新增或修改實驗室中心。 |
> | Microsoft.DevTestLab/labs/delete | 刪除實驗室。 |
> | Microsoft.DevTestLab/labs/read | 讀取實驗室。 |
> | Microsoft.DevTestLab/labs/write | 新增或修改實驗室。 |
> | Microsoft.DevTestLab/labs/ListVhds/action | 列出可供建立自訂映像的磁碟映像。 |
> | Microsoft.DevTestLab/labs/GenerateUploadUri/action | 產生 URI 以用來將自訂磁碟映像上傳到實驗室。 |
> | Microsoft.DevTestLab/labs/CreateEnvironment/action | 在實驗室中建立虛擬機器。 |
> | Microsoft.DevTestLab/labs/ClaimAnyVm/action | 在實驗室中宣告隨機的可宣告虛擬機器。 |
> | Microsoft.DevTestLab/labs/ExportResourceUsage/action | 將實驗室資源使用狀況匯出到儲存體帳戶 |
> | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | 將虛擬機器匯入不同的實驗室。 |
> | Microsoft.devtestlab/labs/EnsureCurrentUserProfile/action | 請確認目前的使用者在實驗室中具備有效的設定檔。 |
> | Microsoft.DevTestLab/labs/artifactSources/delete | 刪除構件來源。 |
> | Microsoft.DevTestLab/labs/artifactSources/read | 讀取構件來源。 |
> | Microsoft.DevTestLab/labs/artifactSources/write | 新增或修改構件來源。 |
> | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | 讀取 Azure Resource Manager 範本。 |
> | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | 讀取構件。 |
> | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | 為指定的成品產生 Azure Resource Manager 範本、將必要的檔案上傳至儲存體帳戶，並驗證所產生的成品。 |
> | Microsoft.DevTestLab/labs/costs/read | 讀取成本。 |
> | Microsoft.DevTestLab/labs/costs/write | 新增或修改成本。 |
> | Microsoft.DevTestLab/labs/customImages/delete | 刪除自訂映像。 |
> | Microsoft.DevTestLab/labs/customImages/read | 讀取自訂映像。 |
> | Microsoft.DevTestLab/labs/customImages/write | 新增或修改自訂映像。 |
> | Microsoft.DevTestLab/labs/formulas/delete | 刪除公式。 |
> | Microsoft.DevTestLab/labs/formulas/read | 讀取公式。 |
> | Microsoft.DevTestLab/labs/formulas/write | 新增或修改公式。 |
> | Microsoft.DevTestLab/labs/galleryImages/read | 讀取資源庫映像。 |
> | Microsoft.DevTestLab/labs/notificationChannels/delete | 刪除通知通道。 |
> | Microsoft.DevTestLab/labs/notificationChannels/read | 讀取通知通道。 |
> | Microsoft.DevTestLab/labs/notificationChannels/write | 新增或修改通知通道。 |
> | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | 對所提供的通道傳送通知。 |
> | Microsoft.DevTestLab/labs/policySets/read | 讀取原則集合。 |
> | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | 評估實驗室原則。 |
> | Microsoft.DevTestLab/labs/policySets/policies/delete | 刪除原則。 |
> | Microsoft.DevTestLab/labs/policySets/policies/read | 讀取原則。 |
> | Microsoft.DevTestLab/labs/policySets/policies/write | 新增或修改原則。 |
> | Microsoft.DevTestLab/labs/schedules/delete | 刪除排程。 |
> | Microsoft.DevTestLab/labs/schedules/read | 讀取排程。 |
> | Microsoft.DevTestLab/labs/schedules/write | 新增或修改排程。 |
> | Microsoft.DevTestLab/labs/schedules/Execute/action | 執行排程。 |
> | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | 列出所有適用排程 |
> | Microsoft.DevTestLab/labs/serviceRunners/delete | 刪除服務執行器。 |
> | Microsoft.DevTestLab/labs/serviceRunners/read | 讀取服務執行器。 |
> | Microsoft.DevTestLab/labs/serviceRunners/write | 新增或修改服務執行器。 |
> | Microsoft.devtestlab/labs/共用資源庫/delete | 刪除共用資源庫。 |
> | Microsoft.devtestlab/labs/共用資源庫/read | 讀取共用資源庫。 |
> | Microsoft.devtestlab/labs/共用資源庫/write | 新增或修改共用資源庫。 |
> | Microsoft.devtestlab/labs/共用資源庫/共用影像/delete | 刪除共用映射。 |
> | Microsoft.devtestlab/labs/共用資源庫/共用影像/read | 讀取共用映射。 |
> | Microsoft.devtestlab/labs/共用資源庫/共用影像/write | 新增或修改共用映射。 |
> | Microsoft.DevTestLab/labs/users/delete | 刪除使用者設定檔。 |
> | Microsoft.DevTestLab/labs/users/read | 讀取使用者設定檔。 |
> | Microsoft.DevTestLab/labs/users/write | 新增或修改使用者設定檔。 |
> | Microsoft.DevTestLab/labs/users/disks/delete | 刪除磁碟。 |
> | Microsoft.DevTestLab/labs/users/disks/read | 讀取磁碟。 |
> | Microsoft.DevTestLab/labs/users/disks/write | 新增或修改磁碟。 |
> | Microsoft.DevTestLab/labs/users/disks/Attach/action | 將磁碟連結至虛擬機器並建立磁碟租用。 |
> | Microsoft.DevTestLab/labs/users/disks/Detach/action | 將連結至虛擬機器的磁碟中斷連結並中斷磁碟租用。 |
> | Microsoft.DevTestLab/labs/users/environments/delete | 刪除環境。 |
> | Microsoft.DevTestLab/labs/users/environments/read | 讀取環境。 |
> | Microsoft.DevTestLab/labs/users/environments/write | 新增或修改環境。 |
> | Microsoft.DevTestLab/labs/users/secrets/delete | 刪除祕密。 |
> | Microsoft.DevTestLab/labs/users/secrets/read | 讀取祕密。 |
> | Microsoft.DevTestLab/labs/users/secrets/write | 新增或修改祕密。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | 刪除 Service Fabric。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/read | 讀取 Service Fabric。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/write | 新增或修改 Service Fabric。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | 啟動 Service Fabric。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | 停止 Service Fabric |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | 列出適用的啟動/停止排程 (若有的話)。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | 刪除排程。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | 讀取排程。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | 新增或修改排程。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | 執行排程。 |
> | Microsoft.DevTestLab/labs/virtualMachines/delete | 刪除虛擬機器。 |
> | Microsoft.DevTestLab/labs/virtualMachines/read | 讀取虛擬機器。 |
> | Microsoft.DevTestLab/labs/virtualMachines/write | 新增或修改虛擬機器。 |
> | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | 將新的或現有的資料磁碟連結至虛擬機器。 |
> | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | 將構件套用至虛擬機器。 |
> | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | 取得現有虛擬機器的擁有權 |
> | Microsoft.devtestlab/labs/virtualMachines/ClearArtifactResults/action | 清除虛擬機器的成品結果。 |
> | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | 中斷指定磁碟與虛擬機器的連結。 |
> | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | 取得代表虛擬機器 RDP 檔案內容的字串 |
> | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | 列出適用的啟動/停止排程 (若有的話)。 |
> | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | 重新部署虛擬機器 |
> | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | 調整虛擬機器的大小。 |
> | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | 重新啟動虛擬機器。 |
> | Microsoft.DevTestLab/labs/virtualMachines/Start/action | 啟動虛擬機器。 |
> | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | 停止虛擬機器 |
> | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | 傳送所有連結至虛擬機器的資料磁碟，由目前的使用者擁有。 |
> | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | 釋放現有虛擬機器的擁有權 |
> | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | 刪除排程。 |
> | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | 讀取排程。 |
> | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | 新增或修改排程。 |
> | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | 執行排程。 |
> | Microsoft.DevTestLab/labs/virtualNetworks/delete | 刪除虛擬網路。 |
> | Microsoft.DevTestLab/labs/virtualNetworks/read | 讀取虛擬網路。 |
> | Microsoft.DevTestLab/labs/virtualNetworks/write | 新增或修改虛擬網路。 |
> | Microsoft.devtestlab/labs/virtualNetworks/bastionHosts/delete | 刪除 bastionhosts。 |
> | Microsoft.devtestlab/labs/virtualNetworks/bastionHosts/read | 讀取 bastionhosts。 |
> | Microsoft.devtestlab/labs/virtualNetworks/bastionHosts/write | 新增或修改 bastionhosts。 |
> | Microsoft.DevTestLab/labs/vmPools/delete | 刪除虛擬機器集區。 |
> | Microsoft.DevTestLab/labs/vmPools/read | 讀取虛擬機器集區。 |
> | Microsoft.DevTestLab/labs/vmPools/write | 新增或修改虛擬機器集區。 |
> | Microsoft.DevTestLab/locations/operations/read | 讀取作業。 |
> | Microsoft.DevTestLab/schedules/delete | 刪除排程。 |
> | Microsoft.DevTestLab/schedules/read | 讀取排程。 |
> | Microsoft.DevTestLab/schedules/write | 新增或修改排程。 |
> | Microsoft.DevTestLab/schedules/Execute/action | 執行排程。 |
> | Microsoft.DevTestLab/schedules/Retarget/action | 更新排程的目標資源識別碼。 |

### <a name="microsoftlabservices"></a>Microsoft.LabServices

Azure 服務： [Azure 實驗室服務](../lab-services/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.LabServices/register/action | 註冊訂用帳戶 |
> | Microsoft.LabServices/labAccounts/delete | 刪除實驗室帳戶。 |
> | Microsoft.LabServices/labAccounts/read | 讀取實驗室帳戶。 |
> | Microsoft.LabServices/labAccounts/write | 新增或修改實驗室帳戶。 |
> | Microsoft.LabServices/labAccounts/CreateLab/action | 在實驗室帳戶中建立實驗室。 |
> | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | 取得在實驗室帳戶下設定的每個大小類別的區域可用性資訊 |
> | LabServices/labAccounts/GetPricingAndAvailability/action | 依大小、地理位置和作業系統的各種組合，取得實驗室帳戶的價格和可用性。 |
> | LabServices/labAccounts/GetRestrictionsAndUsage/action | 取得此訂用帳戶的核心限制及使用量 |
> | Microsoft.LabServices/labAccounts/galleryImages/delete | 刪除資源庫映像。 |
> | Microsoft.LabServices/labAccounts/galleryImages/read | 讀取資源庫映像。 |
> | Microsoft.LabServices/labAccounts/galleryImages/write | 新增或修改資源庫映像。 |
> | Microsoft.LabServices/labAccounts/labs/delete | 刪除實驗室。 |
> | Microsoft.LabServices/labAccounts/labs/read | 讀取實驗室。 |
> | Microsoft.LabServices/labAccounts/labs/write | 新增或修改實驗室。 |
> | Microsoft.LabServices/labAccounts/labs/AddUsers/action | 將使用者新增至實驗室 |
> | Microsoft.LabServices/labAccounts/labs/SendEmail/action | 傳送附有註冊連結的電子郵件給實驗室 |
> | LabServices/labAccounts/labs/GetLabPricingAndAvailability/action | 取得此實驗室的每個實驗室單位的定價，以及指出此實驗室是否可擴大的可用性。 |
> | LabServices/labAccounts/labs/SyncUserList/action | 將 AAD 群組的變更同步處理到 userlist |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | 刪除環境設定。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | 讀取環境設定。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | 新增或修改環境設定。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | 根據實驗室/環境設定的目前狀態，佈建/取消佈建環境設定所需的資源。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | 啟動範本內的所有資源以啟動範本。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | 透過停止範本中的所有資源來停止範本。 |
> | LabServices/labAccounts/labs/>cloudtask.environmentsettings/SaveImage/action | 將目前的範本映射儲存至實驗室帳戶中的共用資源庫 |
> | LabServices/labAccounts/labs/>cloudtask.environmentsettings/ResetPassword/action | 重設範本虛擬機器上的密碼。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | 刪除環境。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | 讀取環境。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | 啟動環境內的所有資源以啟動環境。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | 停止環境內的所有資源以停止環境 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | 重設環境的使用者密碼 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | 刪除排程。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | 讀取排程。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | 新增或修改排程。 |
> | Microsoft.LabServices/labAccounts/labs/users/delete | 刪除使用者。 |
> | Microsoft.LabServices/labAccounts/labs/users/read | 讀取使用者。 |
> | Microsoft.LabServices/labAccounts/labs/users/write | 新增或修改使用者。 |
> | Microsoft.LabServices/labAccounts/sharedGalleries/delete | 刪除共用資源庫。 |
> | Microsoft.LabServices/labAccounts/sharedGalleries/read | 讀取共用資源庫。 |
> | Microsoft.LabServices/labAccounts/sharedGalleries/write | 新增或修改共用資源庫。 |
> | Microsoft.LabServices/labAccounts/sharedImages/delete | 刪除共用影像。 |
> | Microsoft.LabServices/labAccounts/sharedImages/read | 讀取共用影像。 |
> | Microsoft.LabServices/labAccounts/sharedImages/write | 新增或修改共用影像。 |
> | Microsoft.LabServices/locations/operations/read | 讀取作業。 |
> | Microsoft.LabServices/users/Register/action | 向受控實驗室註冊使用者 |
> | LabServices/users/ListAllEnvironments/action | 列出使用者的所有環境 |
> | Microsoft.LabServices/users/StartEnvironment/action | 啟動環境內的所有資源以啟動環境。 |
> | Microsoft.LabServices/users/StopEnvironment/action | 停止環境內的所有資源以停止環境 |
> | Microsoft.LabServices/users/ResetPassword/action | 重設環境的使用者密碼 |
> | LabServices/users/u/action | 更新並傳回個人使用者設定。 |

### <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

Azure 服務： [Azure DevOps](https://docs.microsoft.com/azure/devops/)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.VisualStudio/Register/Action | 向 Microsoft.VisualStudio 提供者註冊 Azure 訂用帳戶 |
> | Microsoft.VisualStudio/Account/Write | 設定帳戶 |
> | Microsoft.VisualStudio/Account/Delete | 刪除帳戶 |
> | Microsoft.VisualStudio/Account/Read | 讀取帳戶 |
> | Microsoft.VisualStudio/Account/Extension/Read | 讀取帳戶/擴充功能 |
> | Microsoft.VisualStudio/Account/Project/Read | 讀取帳戶/專案 |
> | Microsoft.VisualStudio/Account/Project/Write | 讀取帳戶/專案 |
> | Microsoft.VisualStudio/Extension/Write | 設定擴充功能 |
> | Microsoft.VisualStudio/Extension/Delete | 刪除擴充功能 |
> | Microsoft.VisualStudio/Extension/Read | 讀取擴充功能 |
> | Microsoft.VisualStudio/Project/Write | 設定專案 |
> | Microsoft.VisualStudio/Project/Delete | 刪除專案 |
> | Microsoft.VisualStudio/Project/Read | 讀取專案 |

## <a name="migrate"></a>遷移

### <a name="microsoftmigrate"></a>Microsoft.Migrate

Azure 服務： [Azure Migrate](../migrate/migrate-services-overview.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Migrate/register/action | 向 Microsoft.Migrate 資源提供者註冊訂用帳戶 |
> | Microsoft. 遷移/assessmentprojects/讀取 | 取得評量專案的屬性 |
> | Microsoft. 遷移/assessmentprojects/write | 建立新的評定專案或更新現有的評定專案 |
> | Microsoft. 遷移/assessmentprojects/刪除 | 刪除評量專案 |
> | Microsoft. 遷移/assessmentprojects/assessmentOptions/read | 取得可在指定位置使用的評定選項 |
> | Microsoft. 遷移/assessmentprojects/評量/讀取 | 列出專案內的評定 |
> | Microsoft. 遷移/assessmentprojects/assessmentsSummary/read | 取得指定位置中可用的評量摘要 |
> | Microsoft. 遷移/assessmentprojects/avsAssessmentOptions/read | 取得指定位置中可用的 AVS 評定選項 |
> | Microsoft. 遷移/assessmentprojects/群組/讀取 | 取得群組的屬性 |
> | Microsoft. 遷移/assessmentprojects/群組/寫入 | 建立新的群組或更新現有的群組 |
> | Microsoft. 遷移/assessmentprojects/群組/刪除 | 刪除群組 |
> | Microsoft. 遷移/assessmentprojects/groups/updateMachines/action | 藉由新增或移除機器來更新群組 |
> | Microsoft. 遷移/assessmentprojects/群組/評量/讀取 | 取得評定的屬性 |
> | Microsoft. 遷移/assessmentprojects/群組/評量/寫入 | 建立新的評定或更新現有的評定 |
> | Microsoft. 遷移/assessmentprojects/群組/評量/刪除 | 刪除評定 |
> | Microsoft. 遷移/assessmentprojects/群組/評量/downloadurl/動作 | 下載評定報告的 URL |
> | Microsoft. 遷移/assessmentprojects/群組/評量/assessedmachines/讀取 | 取得已評定電腦的屬性 |
> | Microsoft. 遷移/assessmentprojects/groups/avsAssessments/read | 取得 AVS 評量的屬性 |
> | Microsoft. 遷移/assessmentprojects/groups/avsAssessments/write | 建立新的 AVS 評量，或更新現有的 AVS 評量 |
> | Microsoft. 遷移/assessmentprojects/groups/avsAssessments/delete | 刪除 AVS 評量 |
> | Microsoft. 遷移/assessmentprojects/groups/avsAssessments/downloadurl/action | 下載 AVS 評定報告的 URL |
> | Microsoft. 遷移/assessmentprojects/groups/avsAssessments/avsassessedmachines/read | 取得 AVS 評估機器的屬性 |
> | Microsoft. 遷移/assessmentprojects/hypervcollectors/read | 取得 HyperV 收集器的屬性 |
> | Microsoft. 遷移/assessmentprojects/hypervcollectors/write | 建立新的 HyperV 收集器，或更新現有的 HyperV 收集器 |
> | Microsoft. 遷移/assessmentprojects/hypervcollectors/delete | 刪除 HyperV 收集器 |
> | Microsoft. 遷移/assessmentprojects/importcollectors/read | 取得匯入收集器的屬性 |
> | Microsoft. 遷移/assessmentprojects/importcollectors/write | 建立新的匯入收集器，或更新現有的匯入收集器 |
> | Microsoft. 遷移/assessmentprojects/importcollectors/delete | 刪除匯入收集器 |
> | Microsoft. 遷移/assessmentprojects/機器/讀取 | 取得電腦的屬性 |
> | Microsoft. 遷移/assessmentprojects/privateEndpointConnectionProxies/read | 取得私人端點連線 Proxy |
> | Microsoft. 遷移/assessmentprojects/privateEndpointConnectionProxies/驗證/動作 | 驗證私人端點連線 Proxy |
> | Microsoft. 遷移/assessmentprojects/privateEndpointConnectionProxies/write | 建立或更新私人端點連線 Proxy |
> | Microsoft. 遷移/assessmentprojects/privateEndpointConnectionProxies/delete | 刪除私人端點連線 Proxy |
> | Microsoft. 遷移/assessmentprojects/privateEndpointConnections/read | 取得私人端點連線 |
> | Microsoft. 遷移/assessmentprojects/privateEndpointConnections/write | 更新私人端點連線 |
> | Microsoft. 遷移/assessmentprojects/privateEndpointConnections/delete | 刪除私人端點連線 |
> | Microsoft. 遷移/assessmentprojects/privateLinkResources/read | 取得 Private Link 資源 |
> | Microsoft. 遷移/assessmentprojects/servercollectors/read | 取得伺服器收集器的屬性 |
> | Microsoft. 遷移/assessmentprojects/servercollectors/write | 建立新的伺服器收集器，或更新現有的伺服器收集器 |
> | Microsoft. 遷移/assessmentprojects/vmwarecollectors/read | 取得 VMware 收集器的屬性 |
> | Microsoft. 遷移/assessmentprojects/vmwarecollectors/write | 建立新的 VMware 收集器，或更新現有的 VMware 收集器 |
> | Microsoft. 遷移/assessmentprojects/vmwarecollectors/delete | 刪除 VMware 收集器 |
> | Microsoft.Migrate/locations/checknameavailability/action | 檢查指定位置中，指定訂用帳戶的資源名稱可用性 |
> | Microsoft.Migrate/locations/assessmentOptions/read | 取得可在指定位置使用的評定選項 |
> | Microsoft.Migrate/migrateprojects/read | 取得遷移專案的屬性 |
> | Microsoft. 遷移/migrateprojects/write | 建立新的遷移專案或更新現有的遷移專案 |
> | Microsoft. 遷移/migrateprojects/刪除 | 刪除遷移專案 |
> | Microsoft. 遷移/migrateprojects/registerTool/action | 將工具註冊至遷移專案 |
> | Microsoft. 遷移/migrateprojects/RefreshSummary/action | 重新整理遷移專案摘要 |
> | Microsoft. 遷移/migrateprojects/registrationDetails/action | 提供工具註冊詳細資料 |
> | Microsoft. 遷移/migrateprojects/DatabaseInstances/read | 取得資料庫實例的屬性 |
> | Microsoft. 遷移/migrateprojects/資料庫/讀取 | 取得資料庫的屬性 |
> | Microsoft. 遷移/migrateprojects/機器/讀取 | 取得電腦的屬性 |
> | Microsoft. 遷移/migrateprojects/MigrateEvents/read | 取得遷移事件的屬性。 |
> | Microsoft. 遷移/migrateprojects/MigrateEvents/Delete | 刪除遷移事件 |
> | Microsoft. 遷移/migrateprojects/privateEndpointConnectionProxies/read | 取得私人端點連線 Proxy |
> | Microsoft. 遷移/migrateprojects/privateEndpointConnectionProxies/驗證/動作 | 驗證私人端點連線 Proxy |
> | Microsoft. 遷移/migrateprojects/privateEndpointConnectionProxies/write | 建立或更新私人端點連線 Proxy |
> | Microsoft. 遷移/migrateprojects/privateEndpointConnectionProxies/delete | 刪除私人端點連線 Proxy |
> | Microsoft. 遷移/migrateprojects/privateEndpointConnections/read | 取得私人端點連線 |
> | Microsoft. 遷移/migrateprojects/privateEndpointConnections/write | 更新私人端點連線 |
> | Microsoft. 遷移/migrateprojects/privateEndpointConnections/delete | 刪除私人端點連線 |
> | Microsoft. 遷移/migrateprojects/privateLinkResources/read | 取得 Private Link 資源 |
> | Microsoft.Migrate/migrateprojects/solutions/read | 取得遷移專案方案的屬性 |
> | Microsoft. 遷移/migrateprojects/解決方案/撰寫 | 建立新的遷移專案方案，或更新現有的遷移專案方案 |
> | Microsoft. 遷移/migrateprojects/解決方案/刪除 | 刪除遷移專案方案 |
> | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | 取得遷移專案解決方案組態 |
> | Microsoft. 遷移/migrateprojects/解決方案/cleanupData/動作 | 清除遷移專案解決方案資料 |
> | Microsoft. 遷移/migrateprojects/VirtualDesktopUsers/read | 取得虛擬桌面使用者的屬性 |
> | Microsoft. 遷移/migrateprojects/WebServers/read | 取得 web 伺服器的屬性 |
> | Microsoft. 遷移/migrateprojects/網站/讀取 | 取得網站的屬性 |
> | Microsoft.Migrate/Operations/read | 列出可對 Microsoft.Migrate 資源提供者進行的作業 |
> | Microsoft.Migrate/projects/read | 取得專案的屬性 |
> | Microsoft.Migrate/projects/write | 建立新的專案或更新現有的專案 |
> | Microsoft.Migrate/projects/delete | 刪除專案 |
> | Microsoft.Migrate/projects/keys/action | 取得專案的共用金鑰 |
> | Microsoft.Migrate/projects/assessments/read | 列出專案內的評定 |
> | Microsoft.Migrate/projects/groups/read | 取得群組的屬性 |
> | Microsoft.Migrate/projects/groups/write | 建立新的群組或更新現有的群組 |
> | Microsoft.Migrate/projects/groups/delete | 刪除群組 |
> | Microsoft.Migrate/projects/groups/assessments/read | 取得評定的屬性 |
> | Microsoft.Migrate/projects/groups/assessments/write | 建立新的評定或更新現有的評定 |
> | Microsoft.Migrate/projects/groups/assessments/delete | 刪除評定 |
> | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | 下載評定報告的 URL |
> | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | 取得已評定電腦的屬性 |
> | Microsoft.Migrate/projects/machines/read | 取得電腦的屬性 |

### <a name="microsoftoffazure"></a>Microsoft.OffAzure

Azure 服務： [Azure Migrate](../migrate/migrate-services-overview.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.OffAzure/register/action | 向 Microsoft.OffAzure 資源提供者註冊訂用帳戶 |
> | Microsoft.OffAzure/register/action | 向 Microsoft.OffAzure 資源提供者註冊訂用帳戶 |
> | Microsoft.OffAzure/register/action | 向 Microsoft.OffAzure 資源提供者註冊訂用帳戶 |
> | Microsoft.OffAzure/register/action | 向 Microsoft.OffAzure 資源提供者註冊訂用帳戶 |
> | Microsoft.OffAzure/register/action | 向 Microsoft.OffAzure 資源提供者註冊訂用帳戶 |
> | Microsoft.OffAzure/HyperVSites/read | 取得 Hyper-V 站台的屬性 |
> | Microsoft.OffAzure/HyperVSites/write | 建立或更新 Hyper-V 站台 |
> | Microsoft.OffAzure/HyperVSites/delete | 刪除 Hyper-V 站台 |
> | Microsoft.OffAzure/HyperVSites/refresh/action | 重新整理 Hyper-V 站台內的物件 |
> | Microsoft.offazure/HyperVSites/updateProperties/action | 更新網站中電腦的屬性 |
> | Microsoft.OffAzure/HyperVSites/read | 取得 Hyper-V 站台的屬性 |
> | Microsoft.OffAzure/HyperVSites/write | 建立或更新 Hyper-V 站台 |
> | Microsoft.OffAzure/HyperVSites/delete | 刪除 Hyper-V 站台 |
> | Microsoft.OffAzure/HyperVSites/refresh/action | 重新整理 Hyper-V 站台內的物件 |
> | Microsoft.offazure/HyperVSites/updateProperties/action | 更新網站中電腦的屬性 |
> | Microsoft.OffAzure/HyperVSites/read | 取得 Hyper-V 站台的屬性 |
> | Microsoft.OffAzure/HyperVSites/write | 建立或更新 Hyper-V 站台 |
> | Microsoft.OffAzure/HyperVSites/delete | 刪除 Hyper-V 站台 |
> | Microsoft.OffAzure/HyperVSites/refresh/action | 重新整理 Hyper-V 站台內的物件 |
> | Microsoft.offazure/HyperVSites/updateProperties/action | 更新網站中電腦的屬性 |
> | Microsoft.OffAzure/HyperVSites/read | 取得 Hyper-V 站台的屬性 |
> | Microsoft.OffAzure/HyperVSites/write | 建立或更新 Hyper-V 站台 |
> | Microsoft.OffAzure/HyperVSites/delete | 刪除 Hyper-V 站台 |
> | Microsoft.OffAzure/HyperVSites/refresh/action | 重新整理 Hyper-V 站台內的物件 |
> | Microsoft.offazure/HyperVSites/updateProperties/action | 更新網站中電腦的屬性 |
> | Microsoft.OffAzure/HyperVSites/read | 取得 Hyper-V 站台的屬性 |
> | Microsoft.OffAzure/HyperVSites/write | 建立或更新 Hyper-V 站台 |
> | Microsoft.OffAzure/HyperVSites/delete | 刪除 Hyper-V 站台 |
> | Microsoft.OffAzure/HyperVSites/refresh/action | 重新整理 Hyper-V 站台內的物件 |
> | Microsoft.offazure/HyperVSites/updateProperties/action | 更新網站中電腦的屬性 |
> | Microsoft.OffAzure/HyperVSites/clusters/read | 取得 Hyper-V 叢集的屬性 |
> | Microsoft.OffAzure/HyperVSites/clusters/write | 建立或更新 Hyper-V 叢集 |
> | Microsoft.OffAzure/HyperVSites/clusters/read | 取得 Hyper-V 叢集的屬性 |
> | Microsoft.OffAzure/HyperVSites/clusters/write | 建立或更新 Hyper-V 叢集 |
> | Microsoft.OffAzure/HyperVSites/clusters/read | 取得 Hyper-V 叢集的屬性 |
> | Microsoft.OffAzure/HyperVSites/clusters/write | 建立或更新 Hyper-V 叢集 |
> | Microsoft.OffAzure/HyperVSites/clusters/read | 取得 Hyper-V 叢集的屬性 |
> | Microsoft.OffAzure/HyperVSites/clusters/write | 建立或更新 Hyper-V 叢集 |
> | Microsoft.OffAzure/HyperVSites/clusters/read | 取得 Hyper-V 叢集的屬性 |
> | Microsoft.OffAzure/HyperVSites/clusters/write | 建立或更新 Hyper-V 叢集 |
> | Microsoft.offazure/HyperVSites/healthsummary/read | 取得 Hyper-v 資源的健全狀況摘要 |
> | Microsoft.offazure/HyperVSites/healthsummary/read | 取得 Hyper-v 資源的健全狀況摘要 |
> | Microsoft.offazure/HyperVSites/healthsummary/read | 取得 Hyper-v 資源的健全狀況摘要 |
> | Microsoft.offazure/HyperVSites/healthsummary/read | 取得 Hyper-v 資源的健全狀況摘要 |
> | Microsoft.offazure/HyperVSites/healthsummary/read | 取得 Hyper-v 資源的健全狀況摘要 |
> | Microsoft.OffAzure/HyperVSites/hosts/read | 取得 Hyper-V 主機的屬性 |
> | Microsoft.OffAzure/HyperVSites/hosts/write | 建立或更新 Hyper-V 主機 |
> | Microsoft.OffAzure/HyperVSites/hosts/read | 取得 Hyper-V 主機的屬性 |
> | Microsoft.OffAzure/HyperVSites/hosts/write | 建立或更新 Hyper-V 主機 |
> | Microsoft.OffAzure/HyperVSites/hosts/read | 取得 Hyper-V 主機的屬性 |
> | Microsoft.OffAzure/HyperVSites/hosts/write | 建立或更新 Hyper-V 主機 |
> | Microsoft.OffAzure/HyperVSites/hosts/read | 取得 Hyper-V 主機的屬性 |
> | Microsoft.OffAzure/HyperVSites/hosts/write | 建立或更新 Hyper-V 主機 |
> | Microsoft.OffAzure/HyperVSites/hosts/read | 取得 Hyper-V 主機的屬性 |
> | Microsoft.OffAzure/HyperVSites/hosts/write | 建立或更新 Hyper-V 主機 |
> | Microsoft.OffAzure/HyperVSites/jobs/read | 取得 Hyper-V 作業的屬性 |
> | Microsoft.OffAzure/HyperVSites/jobs/read | 取得 Hyper-V 作業的屬性 |
> | Microsoft.OffAzure/HyperVSites/jobs/read | 取得 Hyper-V 作業的屬性 |
> | Microsoft.OffAzure/HyperVSites/jobs/read | 取得 Hyper-V 作業的屬性 |
> | Microsoft.OffAzure/HyperVSites/jobs/read | 取得 Hyper-V 作業的屬性 |
> | Microsoft.OffAzure/HyperVSites/machines/read | 取得 Hyper-V 機器的屬性 |
> | Microsoft.OffAzure/HyperVSites/machines/read | 取得 Hyper-V 機器的屬性 |
> | Microsoft.OffAzure/HyperVSites/machines/read | 取得 Hyper-V 機器的屬性 |
> | Microsoft.OffAzure/HyperVSites/machines/read | 取得 Hyper-V 機器的屬性 |
> | Microsoft.OffAzure/HyperVSites/machines/read | 取得 Hyper-V 機器的屬性 |
> | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 取得 Hyper-V 作業狀態的屬性 |
> | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 取得 Hyper-V 作業狀態的屬性 |
> | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 取得 Hyper-V 作業狀態的屬性 |
> | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 取得 Hyper-V 作業狀態的屬性 |
> | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 取得 Hyper-V 作業狀態的屬性 |
> | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 取得 Hyper-V 執行身分帳戶的屬性 |
> | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 取得 Hyper-V 執行身分帳戶的屬性 |
> | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 取得 Hyper-V 執行身分帳戶的屬性 |
> | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 取得 Hyper-V 執行身分帳戶的屬性 |
> | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 取得 Hyper-V 執行身分帳戶的屬性 |
> | Microsoft.offazure/HyperVSites/summary/read | 取得 Hyper-v 網站的摘要 |
> | Microsoft.offazure/HyperVSites/summary/read | 取得 Hyper-v 網站的摘要 |
> | Microsoft.offazure/HyperVSites/summary/read | 取得 Hyper-v 網站的摘要 |
> | Microsoft.offazure/HyperVSites/summary/read | 取得 Hyper-v 網站的摘要 |
> | Microsoft.offazure/HyperVSites/summary/read | 取得 Hyper-v 網站的摘要 |
> | Microsoft.OffAzure/HyperVSites/usage/read | 取得 Hyper-V 站台的使用情形 |
> | Microsoft.OffAzure/HyperVSites/usage/read | 取得 Hyper-V 站台的使用情形 |
> | Microsoft.OffAzure/HyperVSites/usage/read | 取得 Hyper-V 站台的使用情形 |
> | Microsoft.OffAzure/HyperVSites/usage/read | 取得 Hyper-V 站台的使用情形 |
> | Microsoft.OffAzure/HyperVSites/usage/read | 取得 Hyper-V 站台的使用情形 |
> | Microsoft.offazure/ImportSites/read | 取得匯入網站的屬性 |
> | Microsoft.offazure/ImportSites/write | 建立或更新匯入網站 |
> | Microsoft.offazure/ImportSites/delete | 刪除匯入網站 |
> | Microsoft.offazure/ImportSites/importuri/action | 取得用來匯入機器 CSV 檔案的 SAS uri。 |
> | Microsoft.offazure/ImportSites/exporturi/action | 取得用來匯出機器 CSV 檔案的 SAS uri。 |
> | Microsoft.offazure/ImportSites/read | 取得匯入網站的屬性 |
> | Microsoft.offazure/ImportSites/write | 建立或更新匯入網站 |
> | Microsoft.offazure/ImportSites/delete | 刪除匯入網站 |
> | Microsoft.offazure/ImportSites/importuri/action | 取得用來匯入機器 CSV 檔案的 SAS uri。 |
> | Microsoft.offazure/ImportSites/exporturi/action | 取得用來匯出機器 CSV 檔案的 SAS uri。 |
> | Microsoft.offazure/ImportSites/read | 取得匯入網站的屬性 |
> | Microsoft.offazure/ImportSites/write | 建立或更新匯入網站 |
> | Microsoft.offazure/ImportSites/delete | 刪除匯入網站 |
> | Microsoft.offazure/ImportSites/importuri/action | 取得用來匯入機器 CSV 檔案的 SAS uri。 |
> | Microsoft.offazure/ImportSites/exporturi/action | 取得用來匯出機器 CSV 檔案的 SAS uri。 |
> | Microsoft.offazure/ImportSites/read | 取得匯入網站的屬性 |
> | Microsoft.offazure/ImportSites/write | 建立或更新匯入網站 |
> | Microsoft.offazure/ImportSites/delete | 刪除匯入網站 |
> | Microsoft.offazure/ImportSites/importuri/action | 取得用來匯入機器 CSV 檔案的 SAS uri。 |
> | Microsoft.offazure/ImportSites/exporturi/action | 取得用來匯出機器 CSV 檔案的 SAS uri。 |
> | Microsoft.offazure/ImportSites/read | 取得匯入網站的屬性 |
> | Microsoft.offazure/ImportSites/write | 建立或更新匯入網站 |
> | Microsoft.offazure/ImportSites/delete | 刪除匯入網站 |
> | Microsoft.offazure/ImportSites/importuri/action | 取得用來匯入機器 CSV 檔案的 SAS uri。 |
> | Microsoft.offazure/ImportSites/exporturi/action | 取得用來匯出機器 CSV 檔案的 SAS uri。 |
> | Microsoft.offazure/ImportSites/jobs/read | 取得匯入作業的屬性 |
> | Microsoft.offazure/ImportSites/jobs/read | 取得匯入作業的屬性 |
> | Microsoft.offazure/ImportSites/jobs/read | 取得匯入作業的屬性 |
> | Microsoft.offazure/ImportSites/jobs/read | 取得匯入作業的屬性 |
> | Microsoft.offazure/ImportSites/jobs/read | 取得匯入作業的屬性 |
> | Microsoft.offazure/ImportSites/機器/讀取 | 取得匯入電腦的屬性 |
> | Microsoft.offazure/ImportSites/機器/刪除 | 刪除匯入電腦 |
> | Microsoft.offazure/ImportSites/機器/讀取 | 取得匯入電腦的屬性 |
> | Microsoft.offazure/ImportSites/機器/刪除 | 刪除匯入電腦 |
> | Microsoft.offazure/ImportSites/機器/讀取 | 取得匯入電腦的屬性 |
> | Microsoft.offazure/ImportSites/機器/刪除 | 刪除匯入電腦 |
> | Microsoft.offazure/ImportSites/機器/讀取 | 取得匯入電腦的屬性 |
> | Microsoft.offazure/ImportSites/機器/刪除 | 刪除匯入電腦 |
> | Microsoft.offazure/ImportSites/機器/讀取 | 取得匯入電腦的屬性 |
> | Microsoft.offazure/ImportSites/機器/刪除 | 刪除匯入電腦 |
> | Microsoft.offazure/MasterSites/operationsstatus/read | 取得主要網站操作狀態的屬性 |
> | Microsoft.offazure/MasterSites/operationsstatus/read | 取得主要網站操作狀態的屬性 |
> | Microsoft.offazure/MasterSites/operationsstatus/read | 取得主要網站操作狀態的屬性 |
> | Microsoft.offazure/MasterSites/operationsstatus/read | 取得主要網站操作狀態的屬性 |
> | Microsoft.offazure/MasterSites/operationsstatus/read | 取得主要網站操作狀態的屬性 |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/read | 取得私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/validate/action | 驗證私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/write | 建立或更新私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/delete | 刪除私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/read | 取得私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/validate/action | 驗證私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/write | 建立或更新私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/delete | 刪除私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/read | 取得私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/validate/action | 驗證私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/write | 建立或更新私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/delete | 刪除私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/read | 取得私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/validate/action | 驗證私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/write | 建立或更新私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/delete | 刪除私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/read | 取得私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/validate/action | 驗證私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/write | 建立或更新私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnectionProxies/delete | 刪除私人端點連線 Proxy |
> | Microsoft.offazure/MasterSites/privateEndpointConnections/read | 取得私人端點連線 |
> | Microsoft.offazure/MasterSites/privateEndpointConnections/write | 更新私人端點連線 |
> | Microsoft.offazure/MasterSites/privateEndpointConnections/delete | 刪除私人端點連線 |
> | Microsoft.offazure/MasterSites/privateEndpointConnections/read | 取得私人端點連線 |
> | Microsoft.offazure/MasterSites/privateEndpointConnections/write | 更新私人端點連線 |
> | Microsoft.offazure/MasterSites/privateEndpointConnections/delete | 刪除私人端點連線 |
> | Microsoft.offazure/MasterSites/privateEndpointConnections/read | 取得私人端點連線 |
> | Microsoft.offazure/MasterSites/privateEndpointConnections/write | 更新私人端點連線 |
> | Microsoft.offazure/MasterSites/privateEndpointConnections/delete | 刪除私人端點連線 |
> | Microsoft.offazure/MasterSites/privateEndpointConnections/read | 取得私人端點連線 |
> | Microsoft.offazure/MasterSites/privateEndpointConnections/write | 更新私人端點連線 |
> | Microsoft.offazure/MasterSites/privateEndpointConnections/delete | 刪除私人端點連線 |
> | Microsoft.offazure/MasterSites/privateEndpointConnections/read | 取得私人端點連線 |
> | Microsoft.offazure/MasterSites/privateEndpointConnections/write | 更新私人端點連線 |
> | Microsoft.offazure/MasterSites/privateEndpointConnections/delete | 刪除私人端點連線 |
> | Microsoft.offazure/MasterSites/privateLinkResources/read | 取得 Private Link 資源 |
> | Microsoft.offazure/MasterSites/privateLinkResources/read | 取得 Private Link 資源 |
> | Microsoft.offazure/MasterSites/privateLinkResources/read | 取得 Private Link 資源 |
> | Microsoft.offazure/MasterSites/privateLinkResources/read | 取得 Private Link 資源 |
> | Microsoft.offazure/MasterSites/privateLinkResources/read | 取得 Private Link 資源 |
> | Microsoft.OffAzure/Operations/read | 讀取公開的作業 |
> | Microsoft.offazure/ServerSites/read | 取得伺服器網站的屬性 |
> | Microsoft.offazure/ServerSites/write | 建立或補救伺服器網站 |
> | Microsoft.offazure/ServerSites/delete | 刪除伺服器網站 |
> | Microsoft.offazure/ServerSites/refresh/action | 重新整理伺服器網站中的物件 |
> | Microsoft.offazure/ServerSites/updateProperties/action | 更新網站中電腦的屬性 |
> | Microsoft.offazure/ServerSites/read | 取得伺服器網站的屬性 |
> | Microsoft.offazure/ServerSites/write | 建立或補救伺服器網站 |
> | Microsoft.offazure/ServerSites/delete | 刪除伺服器網站 |
> | Microsoft.offazure/ServerSites/refresh/action | 重新整理伺服器網站中的物件 |
> | Microsoft.offazure/ServerSites/updateProperties/action | 更新網站中電腦的屬性 |
> | Microsoft.offazure/ServerSites/read | 取得伺服器網站的屬性 |
> | Microsoft.offazure/ServerSites/write | 建立或補救伺服器網站 |
> | Microsoft.offazure/ServerSites/delete | 刪除伺服器網站 |
> | Microsoft.offazure/ServerSites/refresh/action | 重新整理伺服器網站中的物件 |
> | Microsoft.offazure/ServerSites/updateProperties/action | 更新網站中電腦的屬性 |
> | Microsoft.offazure/ServerSites/read | 取得伺服器網站的屬性 |
> | Microsoft.offazure/ServerSites/write | 建立或補救伺服器網站 |
> | Microsoft.offazure/ServerSites/delete | 刪除伺服器網站 |
> | Microsoft.offazure/ServerSites/refresh/action | 重新整理伺服器網站中的物件 |
> | Microsoft.offazure/ServerSites/updateProperties/action | 更新網站中電腦的屬性 |
> | Microsoft.offazure/ServerSites/read | 取得伺服器網站的屬性 |
> | Microsoft.offazure/ServerSites/write | 建立或補救伺服器網站 |
> | Microsoft.offazure/ServerSites/delete | 刪除伺服器網站 |
> | Microsoft.offazure/ServerSites/refresh/action | 重新整理伺服器網站中的物件 |
> | Microsoft.offazure/ServerSites/updateProperties/action | 更新網站中電腦的屬性 |
> | Microsoft.offazure/ServerSites/jobs/read | 取得伺服器作業的屬性 |
> | Microsoft.offazure/ServerSites/jobs/read | 取得伺服器作業的屬性 |
> | Microsoft.offazure/ServerSites/jobs/read | 取得伺服器作業的屬性 |
> | Microsoft.offazure/ServerSites/jobs/read | 取得伺服器作業的屬性 |
> | Microsoft.offazure/ServerSites/jobs/read | 取得伺服器作業的屬性 |
> | Microsoft.offazure/ServerSites/機器/讀取 | 取得伺服器電腦的屬性 |
> | Microsoft.offazure/ServerSites/機器/寫入 | 撰寫伺服器電腦的屬性 |
> | Microsoft.offazure/ServerSites/機器/讀取 | 取得伺服器電腦的屬性 |
> | Microsoft.offazure/ServerSites/機器/寫入 | 撰寫伺服器電腦的屬性 |
> | Microsoft.offazure/ServerSites/機器/讀取 | 取得伺服器電腦的屬性 |
> | Microsoft.offazure/ServerSites/機器/寫入 | 撰寫伺服器電腦的屬性 |
> | Microsoft.offazure/ServerSites/機器/讀取 | 取得伺服器電腦的屬性 |
> | Microsoft.offazure/ServerSites/機器/寫入 | 撰寫伺服器電腦的屬性 |
> | Microsoft.offazure/ServerSites/機器/讀取 | 取得伺服器電腦的屬性 |
> | Microsoft.offazure/ServerSites/機器/寫入 | 撰寫伺服器電腦的屬性 |
> | Microsoft.offazure/ServerSites/operationsstatus/read | 取得伺服器操作狀態的屬性 |
> | Microsoft.offazure/ServerSites/operationsstatus/read | 取得伺服器操作狀態的屬性 |
> | Microsoft.offazure/ServerSites/operationsstatus/read | 取得伺服器操作狀態的屬性 |
> | Microsoft.offazure/ServerSites/operationsstatus/read | 取得伺服器操作狀態的屬性 |
> | Microsoft.offazure/ServerSites/operationsstatus/read | 取得伺服器操作狀態的屬性 |
> | Microsoft.offazure/ServerSites/runasaccounts/read | 取得伺服器執行身份帳戶的屬性 |
> | Microsoft.offazure/ServerSites/runasaccounts/read | 取得伺服器執行身份帳戶的屬性 |
> | Microsoft.offazure/ServerSites/runasaccounts/read | 取得伺服器執行身份帳戶的屬性 |
> | Microsoft.offazure/ServerSites/runasaccounts/read | 取得伺服器執行身份帳戶的屬性 |
> | Microsoft.offazure/ServerSites/runasaccounts/read | 取得伺服器執行身份帳戶的屬性 |
> | Microsoft.offazure/ServerSites/summary/read | 取得伺服器網站的摘要 |
> | Microsoft.offazure/ServerSites/summary/read | 取得伺服器網站的摘要 |
> | Microsoft.offazure/ServerSites/summary/read | 取得伺服器網站的摘要 |
> | Microsoft.offazure/ServerSites/summary/read | 取得伺服器網站的摘要 |
> | Microsoft.offazure/ServerSites/summary/read | 取得伺服器網站的摘要 |
> | Microsoft.offazure/ServerSites/usage/read | 取得伺服器網站的使用方式 |
> | Microsoft.offazure/ServerSites/usage/read | 取得伺服器網站的使用方式 |
> | Microsoft.offazure/ServerSites/usage/read | 取得伺服器網站的使用方式 |
> | Microsoft.offazure/ServerSites/usage/read | 取得伺服器網站的使用方式 |
> | Microsoft.offazure/ServerSites/usage/read | 取得伺服器網站的使用方式 |
> | Microsoft.OffAzure/VMwareSites/read | 取得 VMware 站台的屬性 |
> | Microsoft.OffAzure/VMwareSites/write | 建立或更新 VMware 站台 |
> | Microsoft.OffAzure/VMwareSites/delete | 刪除 VMware 站台 |
> | Microsoft.OffAzure/VMwareSites/refresh/action | 重新整理 VMware 站台內的物件 |
> | Microsoft.offazure/VMwareSites/exportapplications/action | 將 VMware 應用程式和角色資料匯出到 xls |
> | Microsoft.offazure/VMwareSites/updateProperties/action | 更新網站中電腦的屬性 |
> | Microsoft.offazure/VMwareSites/generateCoarseMap/action | 產生電腦清單的粗略地圖 |
> | Microsoft.offazure/VMwareSites/generateDetailedMap/action | 產生詳細的 VMware 粗略地圖 |
> | Microsoft.offazure/VMwareSites/clientGroupMembers/action | 列出所選用戶端群組的用戶端群組成員。 |
> | Microsoft.offazure/VMwareSites/serverGroupMembers/action | 列出所選伺服器群組的伺服器群組成員。 |
> | Microsoft.offazure/VMwareSites/getApplications/action | 取得所選電腦的清單應用程式資訊 |
> | Microsoft.offazure/VMwareSites/exportDependencies/action | 匯出所選機器的相依性資訊 |
> | Microsoft.OffAzure/VMwareSites/read | 取得 VMware 站台的屬性 |
> | Microsoft.OffAzure/VMwareSites/write | 建立或更新 VMware 站台 |
> | Microsoft.OffAzure/VMwareSites/delete | 刪除 VMware 站台 |
> | Microsoft.OffAzure/VMwareSites/refresh/action | 重新整理 VMware 站台內的物件 |
> | Microsoft.offazure/VMwareSites/exportapplications/action | 將 VMware 應用程式和角色資料匯出到 xls |
> | Microsoft.offazure/VMwareSites/updateProperties/action | 更新網站中電腦的屬性 |
> | Microsoft.offazure/VMwareSites/generateCoarseMap/action | 產生電腦清單的粗略地圖 |
> | Microsoft.offazure/VMwareSites/generateDetailedMap/action | 產生詳細的 VMware 粗略地圖 |
> | Microsoft.offazure/VMwareSites/clientGroupMembers/action | 列出所選用戶端群組的用戶端群組成員。 |
> | Microsoft.offazure/VMwareSites/serverGroupMembers/action | 列出所選伺服器群組的伺服器群組成員。 |
> | Microsoft.offazure/VMwareSites/getApplications/action | 取得所選電腦的清單應用程式資訊 |
> | Microsoft.offazure/VMwareSites/exportDependencies/action | 匯出所選機器的相依性資訊 |
> | Microsoft.OffAzure/VMwareSites/read | 取得 VMware 站台的屬性 |
> | Microsoft.OffAzure/VMwareSites/write | 建立或更新 VMware 站台 |
> | Microsoft.OffAzure/VMwareSites/delete | 刪除 VMware 站台 |
> | Microsoft.OffAzure/VMwareSites/refresh/action | 重新整理 VMware 站台內的物件 |
> | Microsoft.offazure/VMwareSites/exportapplications/action | 將 VMware 應用程式和角色資料匯出到 xls |
> | Microsoft.offazure/VMwareSites/updateProperties/action | 更新網站中電腦的屬性 |
> | Microsoft.offazure/VMwareSites/generateCoarseMap/action | 產生電腦清單的粗略地圖 |
> | Microsoft.offazure/VMwareSites/generateDetailedMap/action | 產生詳細的 VMware 粗略地圖 |
> | Microsoft.offazure/VMwareSites/clientGroupMembers/action | 列出所選用戶端群組的用戶端群組成員。 |
> | Microsoft.offazure/VMwareSites/serverGroupMembers/action | 列出所選伺服器群組的伺服器群組成員。 |
> | Microsoft.offazure/VMwareSites/getApplications/action | 取得所選電腦的清單應用程式資訊 |
> | Microsoft.offazure/VMwareSites/exportDependencies/action | 匯出所選機器的相依性資訊 |
> | Microsoft.OffAzure/VMwareSites/read | 取得 VMware 站台的屬性 |
> | Microsoft.OffAzure/VMwareSites/write | 建立或更新 VMware 站台 |
> | Microsoft.OffAzure/VMwareSites/delete | 刪除 VMware 站台 |
> | Microsoft.OffAzure/VMwareSites/refresh/action | 重新整理 VMware 站台內的物件 |
> | Microsoft.offazure/VMwareSites/exportapplications/action | 將 VMware 應用程式和角色資料匯出到 xls |
> | Microsoft.offazure/VMwareSites/updateProperties/action | 更新網站中電腦的屬性 |
> | Microsoft.offazure/VMwareSites/generateCoarseMap/action | 產生電腦清單的粗略地圖 |
> | Microsoft.offazure/VMwareSites/generateDetailedMap/action | 產生詳細的 VMware 粗略地圖 |
> | Microsoft.offazure/VMwareSites/clientGroupMembers/action | 列出所選用戶端群組的用戶端群組成員。 |
> | Microsoft.offazure/VMwareSites/serverGroupMembers/action | 列出所選伺服器群組的伺服器群組成員。 |
> | Microsoft.offazure/VMwareSites/getApplications/action | 取得所選電腦的清單應用程式資訊 |
> | Microsoft.offazure/VMwareSites/exportDependencies/action | 匯出所選機器的相依性資訊 |
> | Microsoft.OffAzure/VMwareSites/read | 取得 VMware 站台的屬性 |
> | Microsoft.OffAzure/VMwareSites/write | 建立或更新 VMware 站台 |
> | Microsoft.OffAzure/VMwareSites/delete | 刪除 VMware 站台 |
> | Microsoft.OffAzure/VMwareSites/refresh/action | 重新整理 VMware 站台內的物件 |
> | Microsoft.offazure/VMwareSites/exportapplications/action | 將 VMware 應用程式和角色資料匯出到 xls |
> | Microsoft.offazure/VMwareSites/updateProperties/action | 更新網站中電腦的屬性 |
> | Microsoft.offazure/VMwareSites/generateCoarseMap/action | 產生電腦清單的粗略地圖 |
> | Microsoft.offazure/VMwareSites/generateDetailedMap/action | 產生詳細的 VMware 粗略地圖 |
> | Microsoft.offazure/VMwareSites/clientGroupMembers/action | 列出所選用戶端群組的用戶端群組成員。 |
> | Microsoft.offazure/VMwareSites/serverGroupMembers/action | 列出所選伺服器群組的伺服器群組成員。 |
> | Microsoft.offazure/VMwareSites/getApplications/action | 取得所選電腦的清單應用程式資訊 |
> | Microsoft.offazure/VMwareSites/exportDependencies/action | 匯出所選機器的相依性資訊 |
> | Microsoft.offazure/VMwareSites/healthsummary/read | 取得 VMware 資源的健全狀況摘要 |
> | Microsoft.offazure/VMwareSites/healthsummary/read | 取得 VMware 資源的健全狀況摘要 |
> | Microsoft.offazure/VMwareSites/healthsummary/read | 取得 VMware 資源的健全狀況摘要 |
> | Microsoft.offazure/VMwareSites/healthsummary/read | 取得 VMware 資源的健全狀況摘要 |
> | Microsoft.offazure/VMwareSites/healthsummary/read | 取得 VMware 資源的健全狀況摘要 |
> | Microsoft.offazure/VMwareSites/hosts/read | 取得 VMware 主機的屬性 |
> | Microsoft.offazure/VMwareSites/hosts/read | 取得 VMware 主機的屬性 |
> | Microsoft.offazure/VMwareSites/hosts/read | 取得 VMware 主機的屬性 |
> | Microsoft.offazure/VMwareSites/hosts/read | 取得 VMware 主機的屬性 |
> | Microsoft.offazure/VMwareSites/hosts/read | 取得 VMware 主機的屬性 |
> | Microsoft.OffAzure/VMwareSites/jobs/read | 取得 VMware 作業的屬性 |
> | Microsoft.OffAzure/VMwareSites/jobs/read | 取得 VMware 作業的屬性 |
> | Microsoft.OffAzure/VMwareSites/jobs/read | 取得 VMware 作業的屬性 |
> | Microsoft.OffAzure/VMwareSites/jobs/read | 取得 VMware 作業的屬性 |
> | Microsoft.OffAzure/VMwareSites/jobs/read | 取得 VMware 作業的屬性 |
> | Microsoft.OffAzure/VMwareSites/machines/read | 取得 VMware 機器的屬性 |
> | Microsoft.OffAzure/VMwareSites/machines/stop/action | 停止 VMware 機器 |
> | Microsoft.OffAzure/VMwareSites/machines/start/action | 啟動 VMware 機器 |
> | Microsoft.OffAzure/VMwareSites/machines/read | 取得 VMware 機器的屬性 |
> | Microsoft.OffAzure/VMwareSites/machines/stop/action | 停止 VMware 機器 |
> | Microsoft.OffAzure/VMwareSites/machines/start/action | 啟動 VMware 機器 |
> | Microsoft.OffAzure/VMwareSites/machines/read | 取得 VMware 機器的屬性 |
> | Microsoft.OffAzure/VMwareSites/machines/stop/action | 停止 VMware 機器 |
> | Microsoft.OffAzure/VMwareSites/machines/start/action | 啟動 VMware 機器 |
> | Microsoft.OffAzure/VMwareSites/machines/read | 取得 VMware 機器的屬性 |
> | Microsoft.OffAzure/VMwareSites/machines/stop/action | 停止 VMware 機器 |
> | Microsoft.OffAzure/VMwareSites/machines/start/action | 啟動 VMware 機器 |
> | Microsoft.OffAzure/VMwareSites/machines/read | 取得 VMware 機器的屬性 |
> | Microsoft.OffAzure/VMwareSites/machines/stop/action | 停止 VMware 機器 |
> | Microsoft.OffAzure/VMwareSites/machines/start/action | 啟動 VMware 機器 |
> | Microsoft.offazure/VMwareSites/機器/應用程式/讀取 | 取得 VMware 機器應用程式的屬性 |
> | Microsoft.offazure/VMwareSites/機器/應用程式/讀取 | 取得 VMware 機器應用程式的屬性 |
> | Microsoft.offazure/VMwareSites/機器/應用程式/讀取 | 取得 VMware 機器應用程式的屬性 |
> | Microsoft.offazure/VMwareSites/機器/應用程式/讀取 | 取得 VMware 機器應用程式的屬性 |
> | Microsoft.offazure/VMwareSites/機器/應用程式/讀取 | 取得 VMware 機器應用程式的屬性 |
> | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 取得 VMware 作業狀態的屬性 |
> | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 取得 VMware 作業狀態的屬性 |
> | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 取得 VMware 作業狀態的屬性 |
> | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 取得 VMware 作業狀態的屬性 |
> | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 取得 VMware 作業狀態的屬性 |
> | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 取得 VMware 執行身分帳戶的屬性 |
> | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 取得 VMware 執行身分帳戶的屬性 |
> | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 取得 VMware 執行身分帳戶的屬性 |
> | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 取得 VMware 執行身分帳戶的屬性 |
> | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 取得 VMware 執行身分帳戶的屬性 |
> | Microsoft.offazure/VMwareSites/summary/read | 取得 VMware 網站的摘要 |
> | Microsoft.offazure/VMwareSites/summary/read | 取得 VMware 網站的摘要 |
> | Microsoft.offazure/VMwareSites/summary/read | 取得 VMware 網站的摘要 |
> | Microsoft.offazure/VMwareSites/summary/read | 取得 VMware 網站的摘要 |
> | Microsoft.offazure/VMwareSites/summary/read | 取得 VMware 網站的摘要 |
> | Microsoft.OffAzure/VMwareSites/usage/read | 取得 VMware 站台的使用情形 |
> | Microsoft.OffAzure/VMwareSites/usage/read | 取得 VMware 站台的使用情形 |
> | Microsoft.OffAzure/VMwareSites/usage/read | 取得 VMware 站台的使用情形 |
> | Microsoft.OffAzure/VMwareSites/usage/read | 取得 VMware 站台的使用情形 |
> | Microsoft.OffAzure/VMwareSites/usage/read | 取得 VMware 站台的使用情形 |
> | Microsoft.OffAzure/VMwareSites/vcenters/read | 取得 VMware vCenter 的屬性 |
> | Microsoft.OffAzure/VMwareSites/vcenters/write | 建立或更新 VMware vCenter |
> | Microsoft.OffAzure/VMwareSites/vcenters/read | 取得 VMware vCenter 的屬性 |
> | Microsoft.OffAzure/VMwareSites/vcenters/write | 建立或更新 VMware vCenter |
> | Microsoft.OffAzure/VMwareSites/vcenters/read | 取得 VMware vCenter 的屬性 |
> | Microsoft.OffAzure/VMwareSites/vcenters/write | 建立或更新 VMware vCenter |
> | Microsoft.OffAzure/VMwareSites/vcenters/read | 取得 VMware vCenter 的屬性 |
> | Microsoft.OffAzure/VMwareSites/vcenters/write | 建立或更新 VMware vCenter |
> | Microsoft.OffAzure/VMwareSites/vcenters/read | 取得 VMware vCenter 的屬性 |
> | Microsoft.OffAzure/VMwareSites/vcenters/write | 建立或更新 VMware vCenter |

## <a name="monitor"></a>監視

### <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

Azure 服務： [Azure 監視器](../azure-monitor/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.AlertsManagement/register/action | 訂用帳戶註冊動作 |
> | Microsoft.AlertsManagement/register/action | 向 Microsoft 警示管理註冊訂用帳戶 |
> | Microsoft.AlertsManagement/actionRules/read | 取得輸入篩選的所有動作規則。 |
> | Microsoft.AlertsManagement/actionRules/write | 在指定的訂用帳戶中建立或更新動作規則 |
> | Microsoft.AlertsManagement/actionRules/delete | 在指定的訂用帳戶中刪除動作規則。 |
> | Microsoft.AlertsManagement/alerts/read | 取得輸入篩選的所有警示。 |
> | Microsoft.AlertsManagement/alerts/changestate/action | 變更警示的狀態。 |
> | Microsoft.AlertsManagement/alerts/diagnostics/read | 取得警示的所有診斷 |
> | Microsoft.AlertsManagement/alerts/history/read | 取得警示的歷程記錄 |
> | Microsoft.AlertsManagement/alertsList/read | 取得訂用帳戶之間輸入篩選條件的所有警示 |
> | Microsoft.alertsmanagement/alertsMetaData/read | 取得輸入參數的警示中繼資料。 |
> | Microsoft.AlertsManagement/alertsSummary/read | 取得警示的摘要 |
> | Microsoft.AlertsManagement/alertsSummaryList/read | 取得訂用帳戶之間警示的摘要 |
> | Microsoft.AlertsManagement/Operations/read | 讀取已提供的作業 |
> | Microsoft.alertsmanagement/smartDetectorAlertRules/write | 在指定的訂用帳戶中建立或更新智慧型偵測器警示規則 |
> | Microsoft.alertsmanagement/smartDetectorAlertRules/read | 取得輸入篩選器的所有智慧型偵測器警示規則 |
> | Microsoft.alertsmanagement/smartDetectorAlertRules/delete | 刪除指定訂用帳戶中的智慧型偵測器警示規則 |
> | Microsoft.AlertsManagement/smartGroups/read | 取得輸入篩選的所有智慧群組 |
> | Microsoft.AlertsManagement/smartGroups/changestate/action | 變更智慧群組的狀態 |
> | Microsoft.AlertsManagement/smartGroups/history/read | 取得智慧群組的歷程記錄 |

### <a name="microsoftinsights"></a>Microsoft.Insights

Azure 服務： [Azure 監視器](../azure-monitor/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Insights/Metrics/Action | 計量動作 |
> | Microsoft.Insights/Register/Action | 註冊 Microsoft Insights 提供者 |
> | Microsoft.Insights/Unregister/Action | 註冊 Microsoft Insights 提供者 |
> | Microsoft.Insights/ListMigrationDate/Action | 取回訂用帳戶移轉日期 |
> | Microsoft.Insights/MigrateToNewpricingModel/Action | 將訂用帳戶移轉至新的計價模式 |
> | Microsoft.Insights/RollbackToLegacyPricingModel/Action | 將訂用帳戶復原至舊版計價模式 |
> | Microsoft.Insights/ActionGroups/Write | 建立或更新動作群組 |
> | Microsoft.Insights/ActionGroups/Delete | 刪除動作群組 |
> | Microsoft.Insights/ActionGroups/Read | 讀取動作群組 |
> | Microsoft.Insights/ActivityLogAlerts/Write | 建立或更新活動記錄警示 |
> | Microsoft.Insights/ActivityLogAlerts/Delete | 刪除活動記錄警示 |
> | Microsoft.Insights/ActivityLogAlerts/Read | 讀取活動記錄警示 |
> | Microsoft.Insights/ActivityLogAlerts/Activated/Action | 活動記錄警示已啟用 |
> | Microsoft.Insights/AlertRules/Write | 建立或更新傳統計量警示 |
> | Microsoft.Insights/AlertRules/Delete | 刪除傳統計量警示 |
> | Microsoft.Insights/AlertRules/Read | 讀取傳統計量警示 |
> | Microsoft.Insights/AlertRules/Activated/Action | 傳統計量警示已啟用 |
> | Microsoft.Insights/AlertRules/Resolved/Action | 傳統計量警示已解決 |
> | Microsoft.Insights/AlertRules/Throttled/Action | 傳統計量警示規則已節流 |
> | Microsoft.Insights/AlertRules/Incidents/Read | 讀取傳統計量警示事件 |
> | Microsoft.Insights/AutoscaleSettings/Write | 建立或更新自動調整設定 |
> | Microsoft.Insights/AutoscaleSettings/Delete | 刪除自動調整設定 |
> | Microsoft.Insights/AutoscaleSettings/Read | 讀取自動調整設定 |
> | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | 自動調整相應增加已起始 |
> | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | 自動調整相應減少已起始 |
> | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | 自動調整相應增加已完成 |
> | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | 自動調整相應減少已完成 |
> | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | 讀取資源診斷設定 |
> | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | 建立或更新資源診斷設定 |
> | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | 讀取記錄定義 |
> | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | 讀取計量定義 |
> | Microsoft Insights/基準/讀取 | 讀取計量基準 (預覽)  |
> | Microsoft Insights/CalculateBaseline/Read | 計算度量值的基準 (預覽)  |
> | Microsoft.Insights/Components/AnalyticsTables/Action | Application Insights 分析資料表動作 |
> | Microsoft.Insights/Components/ApiKeys/Action | 產生 Application Insights API 金鑰 |
> | Microsoft.Insights/Components/Purge/Action | 從 Application Insights 清除資料 |
> | Microsoft Insights/元件/DailyCapReached/Action | 已達到 Application Insights 元件的每日上限 |
> | Microsoft Insights/元件/DailyCapWarningThresholdReached/Action | 已達到 Application Insights 元件的每日上限警告閾值 |
> | Microsoft.Insights/Components/Write | 寫入 Application Insights 元件設定 |
> | Microsoft.Insights/Components/Delete | 刪除 Application Insights 元件設定 |
> | Microsoft.Insights/Components/Read | 讀取 Application Insights 元件設定 |
> | Microsoft.Insights/Components/ExportConfiguration/Action | Application Insights 匯出設定動作 |
> | Microsoft.Insights/Components/Move/Action | 將 Application Insights 元件移到另一個資源群組或訂用帳戶 |
> | Microsoft.Insights/Components/AnalyticsItems/Delete | 刪除 Application Insights 分析項目 |
> | Microsoft.Insights/Components/AnalyticsItems/Read | 讀取 Application Insights 分析項目 |
> | Microsoft.Insights/Components/AnalyticsItems/Write | 寫入 Application Insights 分析項目 |
> | Microsoft.Insights/Components/AnalyticsTables/Delete | 刪除 Application Insights 分析資料表結構描述 |
> | Microsoft.Insights/Components/AnalyticsTables/Read | 讀取 Application Insights 分析資料表結構描述 |
> | Microsoft.Insights/Components/AnalyticsTables/Write | 寫入 Application Insights 分析資料表結構描述 |
> | Microsoft.Insights/Components/Annotations/Delete | 刪除 Application Insights 註釋 |
> | Microsoft.Insights/Components/Annotations/Read | 讀取 Application Insights 註釋 |
> | Microsoft.Insights/Components/Annotations/Write | 寫入 Application Insights 註釋 |
> | Microsoft.Insights/Components/Api/Read | 讀取 Application Insights 元件資料 API |
> | Microsoft.Insights/Components/ApiKeys/Delete | 刪除 Application Insights API 金鑰 |
> | Microsoft.Insights/Components/ApiKeys/Read | 讀取 Application Insights API 金鑰 |
> | Microsoft.Insights/Components/BillingPlanForComponent/Read | 讀取 Application Insights 元件的計費方案 |
> | Microsoft.Insights/Components/CurrentBillingFeatures/Read | 在讀取 Application Insights 元件目前的計費功能 |
> | Microsoft.Insights/Components/CurrentBillingFeatures/Write | 寫入 Application Insights 元件目前的計費功能 |
> | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | 讀取 Application Insights 預設的 ALM 整合設定 |
> | Microsoft.Insights/Components/Events/Read | 使用 OData 查詢格式從 Application Insights 取得記錄 |
> | Microsoft.Insights/Components/ExportConfiguration/Delete | 刪除 Application Insights 匯出設定 |
> | Microsoft.Insights/Components/ExportConfiguration/Read | 讀取 Application Insights 匯出設定 |
> | Microsoft.Insights/Components/ExportConfiguration/Write | 寫入 Application Insights 匯出設定 |
> | Microsoft.Insights/Components/ExtendQueries/Read | 讀取 Application Insights 元件擴充的查詢結果 |
> | Microsoft.Insights/Components/Favorites/Delete | 刪除 Application Insights 我的最愛 |
> | Microsoft.Insights/Components/Favorites/Read | 讀取 Application Insights 我的最愛 |
> | Microsoft.Insights/Components/Favorites/Write | 寫入 Application Insights 我的最愛 |
> | Microsoft.Insights/Components/FeatureCapabilities/Read | 讀取 Application Insights 元件功能的能力 |
> | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | 讀取 Application Insights 元件可用的計費功能 |
> | Microsoft.Insights/Components/GetToken/Read | 讀取 Application Insights 元件權杖 |
> | Microsoft.Insights/Components/MetricDefinitions/Read | 讀取 Application Insights 元件計量定義 |
> | Microsoft.Insights/Components/Metrics/Read | 讀取 Application Insights 元件計量 |
> | Microsoft.Insights/Components/MyAnalyticsItems/Delete | 刪除 Application Insights 個人分析項目 |
> | Microsoft.Insights/Components/MyAnalyticsItems/Write | 寫入 Application Insights 個人分析項目 |
> | Microsoft.Insights/Components/MyAnalyticsItems/Read | 讀取 Application Insights 個人分析項目 |
> | Microsoft.Insights/Components/MyFavorites/Read | 讀取 Application Insights 個人我的最愛 |
> | Microsoft.Insights/Components/Operations/Read | 取得 Application Insights 中長時間執行之作業的狀態 |
> | Microsoft.Insights/Components/PricingPlans/Read | 讀取 Application Insights 元件定價方案 |
> | Microsoft.Insights/Components/PricingPlans/Write | 寫入 Application Insights 元件定價方案 |
> | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | 讀取 Application Insights 主動式偵測設定 |
> | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | 寫入 Application Insights 主動式偵測設定 |
> | Microsoft Insights/Components/providers/Microsoft. Insights/diagnosticSettings/Read | 讀取資源診斷設定 |
> | Microsoft Insights/Components/providers/Microsoft. Insights/diagnosticSettings/Write | 建立或更新資源診斷設定 |
> | Microsoft Insights/Components/providers/Microsoft. Insights/logDefinitions/Read | 讀取記錄定義 |
> | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | 讀取計量定義 |
> | Microsoft.Insights/Components/Query/Read | 針對 Application Insights 記錄執行查詢 |
> | Microsoft.Insights/Components/QuotaStatus/Read | 讀取 Application Insights 元件配額狀態 |
> | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | 讀取 Application Insights webtest 位置 |
> | Microsoft.Insights/Components/Webtests/Read | 讀取 Webtest 設定 |
> | Microsoft.Insights/Components/WorkItemConfigs/Delete | 刪除 Application Insights ALM 整合設定 |
> | Microsoft.Insights/Components/WorkItemConfigs/Read | 讀取 Application Insights ALM 整合設定 |
> | Microsoft.Insights/Components/WorkItemConfigs/Write | 寫入 Application Insights ALM 整合設定 |
> | Microsoft Insights/DataCollectionRuleAssociations/Read | 讀取資源與資料集合規則的關聯 |
> | Microsoft Insights/DataCollectionRuleAssociations/Write | 建立或更新資源與資料集合規則的關聯 |
> | Microsoft Insights/DataCollectionRuleAssociations/Delete | 刪除資源與資料集合規則的關聯 |
> | Microsoft Insights/DataCollectionRules/Read | 讀取資料收集規則 |
> | Microsoft Insights/DataCollectionRules/Write | 建立或更新資料收集規則 |
> | Microsoft Insights/DataCollectionRules/Delete | 刪除資料收集規則 |
> | Microsoft.Insights/DiagnosticSettings/Write | 建立或更新資源診斷設定 |
> | Microsoft.Insights/DiagnosticSettings/Delete | 刪除資源診斷設定 |
> | Microsoft.Insights/DiagnosticSettings/Read | 讀取資源診斷設定 |
> | Microsoft Insights/DiagnosticSettingsCategories/Read | 讀取診斷設定類別 |
> | Microsoft.Insights/EventCategories/Read | 讀取可用的活動記錄事件類別 |
> | Microsoft.Insights/eventtypes/digestevents/Read | 讀取管理事件類型摘要 |
> | Microsoft.Insights/eventtypes/values/Read | 讀取活動記錄事件 |
> | Microsoft.Insights/ExtendedDiagnosticSettings/Write | 建立或更新網路流量記錄診斷設定 |
> | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | 刪除網路流量記錄診斷設定 |
> | Microsoft.Insights/ExtendedDiagnosticSettings/Read | 讀取網路流量記錄診斷設定 |
> | Microsoft.Insights/ListMigrationDate/Read | 取回訂用帳戶移轉日期 |
> | Microsoft.Insights/LogDefinitions/Read | 讀取記錄定義 |
> | Microsoft.Insights/LogProfiles/Write | 建立或更新活動記錄的記錄設定檔 |
> | Microsoft.Insights/LogProfiles/Delete | 刪除活動記錄的記錄設定檔 |
> | Microsoft.Insights/LogProfiles/Read | 讀取活動記錄的記錄設定檔 |
> | Microsoft.Insights/Logs/Read | 從您的所有記錄讀取資料 |
> | Microsoft Insights/Logs/AADDomainServicesAccountLogon/Read | 從 AADDomainServicesAccountLogon 資料表讀取資料 |
> | Microsoft Insights/Logs/AADDomainServicesAccountManagement/Read | 從 AADDomainServicesAccountManagement 資料表讀取資料 |
> | Microsoft Insights/Logs/AADDomainServicesDirectoryServiceAccess/Read | 從 AADDomainServicesDirectoryServiceAccess 資料表讀取資料 |
> | Microsoft Insights/Logs/AADDomainServicesLogonLogoff/Read | 從 AADDomainServicesLogonLogoff 資料表讀取資料 |
> | Microsoft Insights/Logs/AADDomainServicesPolicyChange/Read | 從 AADDomainServicesPolicyChange 資料表讀取資料 |
> | Microsoft Insights/Logs/AADDomainServicesPrivilegeUse/Read | 從 AADDomainServicesPrivilegeUse 資料表讀取資料 |
> | Microsoft Insights/Logs/AADDomainServicesSystemSecurity/Read | 從 AADDomainServicesSystemSecurity 資料表讀取資料 |
> | Microsoft Insights/Logs/AADManagedIdentitySignInLogs/Read | 從 AADManagedIdentitySignInLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/AADNonInteractiveUserSignInLogs/Read | 從 AADNonInteractiveUserSignInLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/AADServicePrincipalSignInLogs/Read | 從 AADServicePrincipalSignInLogs 資料表讀取資料 |
> | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | 從 ADAssessmentRecommendation 資料表讀取資料 |
> | Microsoft Insights/Logs/AddonAzureBackupAlerts/Read | 從 AddonAzureBackupAlerts 資料表讀取資料 |
> | Microsoft Insights/Logs/AddonAzureBackupJobs/Read | 從 AddonAzureBackupJobs 資料表讀取資料 |
> | Microsoft Insights/Logs/AddonAzureBackupPolicy/Read | 從 AddonAzureBackupPolicy 資料表讀取資料 |
> | Microsoft Insights/Logs/AddonAzureBackupProtectedInstance/Read | 從 AddonAzureBackupProtectedInstance 資料表讀取資料 |
> | Microsoft Insights/Logs/AddonAzureBackupStorage/Read | 從 AddonAzureBackupStorage 資料表讀取資料 |
> | Microsoft Insights/Logs/ADFActivityRun/Read | 從 ADFActivityRun 資料表讀取資料 |
> | Microsoft Insights/Logs/ADFPipelineRun/Read | 從 ADFPipelineRun 資料表讀取資料 |
> | Microsoft Insights/Logs/ADFSSISIntegrationRuntimeLogs/Read | 從 ADFSSISIntegrationRuntimeLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/ADFSSISPackageEventMessageCoNtext/Read | 從 ADFSSISPackageEventMessageCoNtext 資料表讀取資料 |
> | Microsoft Insights/Logs/ADFSSISPackageEventMessages/Read | 從 ADFSSISPackageEventMessages 資料表讀取資料 |
> | Microsoft Insights/Logs/ADFSSISPackageExecutableStatistics/Read | 從 ADFSSISPackageExecutableStatistics 資料表讀取資料 |
> | Microsoft Insights/Logs/ADFSSISPackageExecutionComponentPhases/Read | 從 ADFSSISPackageExecutionComponentPhases 資料表讀取資料 |
> | Microsoft Insights/Logs/ADFSSISPackageExecutionDataStatistics/Read | 從 ADFSSISPackageExecutionDataStatistics 資料表讀取資料 |
> | Microsoft Insights/Logs/ADFTriggerRun/Read | 從 ADFTriggerRun 資料表讀取資料 |
> | Microsoft.Insights/Logs/ADReplicationResult/Read | 從 ADReplicationResult 資料表讀取資料 |
> | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | 從 ADSecurityAssessmentRecommendation 資料表讀取資料 |
> | Microsoft Insights/Logs/ADTDigitalTwinsOperation/Read | 從 ADTDigitalTwinsOperation 資料表讀取資料 |
> | Microsoft Insights/Logs/ADTEventRoutesOperation/Read | 從 ADTEventRoutesOperation 資料表讀取資料 |
> | Microsoft Insights/Logs/ADTModelsOperation/Read | 從 ADTModelsOperation 資料表讀取資料 |
> | Microsoft Insights/Logs/ADTQueryOperation/Read | 從 ADTQueryOperation 資料表讀取資料 |
> | Microsoft Insights/Logs/AegDeliveryFailureLogs/Read | 從 AegDeliveryFailureLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/AegPublishFailureLogs/Read | 從 AegPublishFailureLogs 資料表讀取資料 |
> | Microsoft.Insights/Logs/Alert/Read | 從 Alert 資料表讀取資料 |
> | Microsoft.Insights/Logs/AlertHistory/Read | 從 AlertHistory 資料表讀取資料 |
> | Microsoft Insights/Logs/AmlComputeClusterEvent/Read | 從 AmlComputeClusterEvent 資料表讀取資料 |
> | Microsoft Insights/Logs/AmlComputeClusterNodeEvent/Read | 從 AmlComputeClusterNodeEvent 資料表讀取資料 |
> | Microsoft Insights/Logs/AmlComputeCpuGpuUtilization/Read | 從 AmlComputeCpuGpuUtilization 資料表讀取資料 |
> | Microsoft Insights/Logs/AmlComputeJobEvent/Read | 從 AmlComputeJobEvent 資料表讀取資料 |
> | Microsoft Insights/Logs/AmlRunStatusChangedEvent/Read | 從 AmlRunStatusChangedEvent 資料表讀取資料 |
> | Microsoft Insights/Logs/ApiManagementGatewayLogs/Read | 從 ApiManagementGatewayLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/AppAvailabilityResults/Read | 從 AppAvailabilityResults 資料表讀取資料 |
> | Microsoft Insights/Logs/AppBrowserTimings/Read | 從 AppBrowserTimings 資料表讀取資料 |
> | Microsoft Insights/Logs/AppCenterError/Read | 從 AppCenterError 資料表讀取資料 |
> | Microsoft Insights/Logs/AppDependencies/Read | 從 AppDependencies 資料表讀取資料 |
> | Microsoft Insights/Logs/AppEvents/Read | 從 AppEvents 資料表讀取資料 |
> | Microsoft Insights/Logs/AppExceptions/Read | 從 AppExceptions 資料表讀取資料 |
> | Microsoft.Insights/Logs/ApplicationInsights/Read | 從 ApplicationInsights 資料表讀取資料 |
> | Microsoft Insights/Logs/AppMetrics/Read | 從 AppMetrics 資料表讀取資料 |
> | Microsoft Insights/Logs/AppPageViews/Read | 從 AppPageViews 資料表讀取資料 |
> | Microsoft Insights/Logs/AppPerformanceCounters/Read | 從 AppPerformanceCounters 資料表讀取資料 |
> | Microsoft Insights/Logs/AppPlatformLogsforSpring/Read | 從 AppPlatformLogsforSpring 資料表讀取資料 |
> | Microsoft Insights/Logs/AppPlatformSystemLogs/Read | 從 AppPlatformSystemLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/AppRequests/Read | 從 AppRequests 資料表讀取資料 |
> | Microsoft Insights/Logs/AppServiceAntivirusScanLogs/Read | 從 AppServiceAntivirusScanLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/AppServiceAppLogs/Read | 從 AppServiceAppLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/AppServiceAuditLogs/Read | 從 AppServiceAuditLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/AppServiceConsoleLogs/Read | 從 AppServiceConsoleLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/AppServiceEnvironmentPlatformLogs/Read | 從 AppServiceEnvironmentPlatformLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/AppServiceFileAuditLogs/Read | 從 AppServiceFileAuditLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/AppServiceHTTPLogs/Read | 從 AppServiceHTTPLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/AppServicePlatformLogs/Read | 從 AppServicePlatformLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/AppSystemEvents/Read | 從 AppSystemEvents 資料表讀取資料 |
> | Microsoft Insights/Logs/AppTraces/Read | 從 AppTraces 資料表讀取資料 |
> | Microsoft Insights/Logs/AuditLogs/Read | 從 AuditLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/AutoscaleEvaluationsLog/Read | 從 AutoscaleEvaluationsLog 資料表讀取資料 |
> | Microsoft Insights/Logs/AutoscaleScaleActionsLog/Read | 從 AutoscaleScaleActionsLog 資料表讀取資料 |
> | Microsoft Insights/Logs/AWSCloudTrail/Read | 從 AWSCloudTrail 資料表讀取資料 |
> | Microsoft.Insights/Logs/AzureActivity/Read | 從 AzureActivity 資料表讀取資料 |
> | Microsoft Insights/Logs/AzureAssessmentRecommendation/Read | 從 AzureAssessmentRecommendation 資料表讀取資料 |
> | Microsoft Insights/Logs/AzureDevOpsAuditing/Read | 從 AzureDevOpsAuditing 資料表讀取資料 |
> | Microsoft Insights/Logs/AzureDiagnostics/Read | 從 AzureDiagnostics 資料表讀取資料 |
> | Microsoft.Insights/Logs/AzureMetrics/Read | 從 AzureMetrics 資料表讀取資料 |
> | Microsoft Insights/Logs/BaiClusterEvent/Read | 從 BaiClusterEvent 資料表讀取資料 |
> | Microsoft Insights/Logs/BaiClusterNodeEvent/Read | 從 BaiClusterNodeEvent 資料表讀取資料 |
> | Microsoft Insights/Logs/BaiJobEvent/Read | 從 BaiJobEvent 資料表讀取資料 |
> | Microsoft Insights/Logs/BehaviorAnalytics/Read | 從 BehaviorAnalytics 資料表讀取資料 |
> | Microsoft Insights/Logs/BlockchainApplicationLog/Read | 從 BlockchainApplicationLog 資料表讀取資料 |
> | Microsoft Insights/Logs/BlockchainProxyLog/Read | 從 BlockchainProxyLog 資料表讀取資料 |
> | Microsoft.Insights/Logs/BoundPort/Read | 從 BoundPort 資料表讀取資料 |
> | Microsoft.Insights/Logs/CommonSecurityLog/Read | 從 CommonSecurityLog 資料表讀取資料 |
> | Microsoft.Insights/Logs/ComputerGroup/Read | 從 ComputerGroup 資料表讀取資料 |
> | Microsoft.Insights/Logs/ConfigurationChange/Read | 從 ConfigurationChange 資料表讀取資料 |
> | Microsoft.Insights/Logs/ConfigurationData/Read | 從 ConfigurationData 資料表讀取資料 |
> | Microsoft.Insights/Logs/ContainerImageInventory/Read | 從 ContainerImageInventory 資料表讀取資料 |
> | Microsoft.Insights/Logs/ContainerInventory/Read | 從 ContainerInventory 資料表讀取資料 |
> | Microsoft.Insights/Logs/ContainerLog/Read | 從 ContainerLog 資料表讀取資料 |
> | Microsoft Insights/Logs/ContainerNodeInventory/Read | 從 ContainerNodeInventory 資料表讀取資料 |
> | Microsoft Insights/Logs/ContainerRegistryLoginEvents/Read | 從 ContainerRegistryLoginEvents 資料表讀取資料 |
> | Microsoft Insights/Logs/ContainerRegistryRepositoryEvents/Read | 從 ContainerRegistryRepositoryEvents 資料表讀取資料 |
> | Microsoft.Insights/Logs/ContainerServiceLog/Read | 從 ContainerServiceLog 資料表讀取資料 |
> | Microsoft Insights/Logs/CoreAzureBackup/Read | 從 CoreAzureBackup 資料表讀取資料 |
> | Microsoft Insights/Logs/DatabricksAccounts/Read | 從 DatabricksAccounts 資料表讀取資料 |
> | Microsoft Insights/Logs/DatabricksClusters/Read | 從 DatabricksClusters 資料表讀取資料 |
> | Microsoft Insights/Logs/DatabricksDBFS/Read | 從 DatabricksDBFS 資料表讀取資料 |
> | Microsoft Insights/Logs/DatabricksInstancePools/Read | 從 DatabricksInstancePools 資料表讀取資料 |
> | Microsoft Insights/Logs/DatabricksJobs/Read | 從 DatabricksJobs 資料表讀取資料 |
> | Microsoft Insights/Logs/DatabricksNotebook/Read | 從 DatabricksNotebook 資料表讀取資料 |
> | Microsoft Insights/Logs/DatabricksSecrets/Read | 從 DatabricksSecrets 資料表讀取資料 |
> | Microsoft Insights/Logs/DatabricksSQLPermissions/Read | 從 DatabricksSQLPermissions 資料表讀取資料 |
> | Microsoft Insights/Logs/DatabricksSSH/Read | 從 DatabricksSSH 資料表讀取資料 |
> | Microsoft Insights/Logs/DatabricksTables/Read | 從 DatabricksTables 資料表讀取資料 |
> | Microsoft Insights/Logs/DatabricksWorkspace/Read | 從 DatabricksWorkspace 資料表讀取資料 |
> | Microsoft.Insights/Logs/DeviceAppCrash/Read | 從 DeviceAppCrash 資料表讀取資料 |
> | Microsoft.Insights/Logs/DeviceAppLaunch/Read | 從 DeviceAppLaunch 資料表讀取資料 |
> | Microsoft.Insights/Logs/DeviceCalendar/Read | 從 DeviceCalendar 資料表讀取資料 |
> | Microsoft.Insights/Logs/DeviceCleanup/Read | 從 DeviceCleanup 資料表讀取資料 |
> | Microsoft.Insights/Logs/DeviceConnectSession/Read | 從 DeviceConnectSession 資料表讀取資料 |
> | Microsoft.Insights/Logs/DeviceEtw/Read | 從 DeviceEtw 資料表讀取資料 |
> | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | 從 DeviceHardwareHealth 資料表讀取資料 |
> | Microsoft.Insights/Logs/DeviceHealth/Read | 從 DeviceHealth 資料表讀取資料 |
> | Microsoft.Insights/Logs/DeviceHeartbeat/Read | 從 DeviceHeartbeat 資料表讀取資料 |
> | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | 從 DeviceSkypeHeartbeat 資料表讀取資料 |
> | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | 從 DeviceSkypeSignIn 資料表讀取資料 |
> | Microsoft.Insights/Logs/DeviceSleepState/Read | 從 DeviceSleepState 資料表讀取資料 |
> | Microsoft.Insights/Logs/DHAppFailure/Read | 從 DHAppFailure 資料表讀取資料 |
> | Microsoft.Insights/Logs/DHAppReliability/Read | 從 DHAppReliability 資料表讀取資料 |
> | Microsoft Insights/Logs/DHCPActivity/Read | 從 DHCPActivity 資料表讀取資料 |
> | Microsoft.Insights/Logs/DHDriverReliability/Read | 從 DHDriverReliability 資料表讀取資料 |
> | Microsoft.Insights/Logs/DHLogonFailures/Read | 從 DHLogonFailures 資料表讀取資料 |
> | Microsoft.Insights/Logs/DHLogonMetrics/Read | 從 DHLogonMetrics 資料表讀取資料 |
> | Microsoft.Insights/Logs/DHOSCrashData/Read | 從 DHOSCrashData 資料表讀取資料 |
> | Microsoft.Insights/Logs/DHOSReliability/Read | 從 DHOSReliability 資料表讀取資料 |
> | Microsoft.Insights/Logs/DHWipAppLearning/Read | 從 DHWipAppLearning 資料表讀取資料 |
> | Microsoft.Insights/Logs/DnsEvents/Read | 從 DnsEvents 資料表讀取資料 |
> | Microsoft.Insights/Logs/DnsInventory/Read | 從 DnsInventory 資料表讀取資料 |
> | Microsoft Insights/Logs/Dynamics365Activity/Read | 從 Dynamics365Activity 資料表讀取資料 |
> | Microsoft.Insights/Logs/ETWEvent/Read | 從 ETWEvent 資料表讀取資料 |
> | Microsoft.Insights/Logs/Event/Read | 從 Event 資料表讀取資料 |
> | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | 從 ExchangeAssessmentRecommendation 資料表讀取資料 |
> | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | 從 ExchangeOnlineAssessmentRecommendation 資料表讀取資料 |
> | Microsoft Insights/Logs/FailedIngestion/Read | 從 FailedIngestion 資料表讀取資料 |
> | Microsoft Insights/Logs/FunctionAppLogs/Read | 從 FunctionAppLogs 資料表讀取資料 |
> | Microsoft.Insights/Logs/Heartbeat/Read | 從 Heartbeat 資料表讀取資料 |
> | Microsoft Insights/Logs/HuntingBookmark/Read | 從 HuntingBookmark 資料表讀取資料 |
> | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | 從 IISAssessmentRecommendation 資料表讀取資料 |
> | Microsoft.Insights/Logs/InboundConnection/Read | 從 InboundConnection 資料表讀取資料 |
> | Microsoft Insights/Logs/InsightsMetrics/Read | 從 InsightsMetrics 資料表讀取資料 |
> | Microsoft Insights/Logs/IntuneAuditLogs/Read | 從 IntuneAuditLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/IntuneDeviceComplianceOrg/Read | 從 IntuneDeviceComplianceOrg 資料表讀取資料 |
> | Microsoft Insights/Logs/IntuneOperationalLogs/Read | 從 IntuneOperationalLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/IoTHubDistributedTracing/Read | 從 IoTHubDistributedTracing 資料表讀取資料 |
> | Microsoft Insights/Logs/KubeEvents/Read | 從 KubeEvents 資料表讀取資料 |
> | Microsoft Insights/Logs/KubeHealth/Read | 從 KubeHealth 資料表讀取資料 |
> | Microsoft Insights/Logs/KubeMonAgentEvents/Read | 從 KubeMonAgentEvents 資料表讀取資料 |
> | Microsoft.Insights/Logs/KubeNodeInventory/Read | 從 KubeNodeInventory 資料表讀取資料 |
> | Microsoft.Insights/Logs/KubePodInventory/Read | 從 KubePodInventory 資料表讀取資料 |
> | Microsoft Insights/Logs/KubeServices/Read | 從 KubeServices 資料表讀取資料 |
> | Microsoft.Insights/Logs/LinuxAuditLog/Read | 從 LinuxAuditLog 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAApplication/Read | 從 MAApplication 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAApplicationHealth/Read | 從 MAApplicationHealth 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | 從 MAApplicationHealthAlternativeVersions 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | 從 MAApplicationHealthIssues 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAApplicationInstance/Read | 從 MAApplicationInstance 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | 從 MAApplicationInstanceReadiness 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAApplicationReadiness/Read | 從 MAApplicationReadiness 資料表讀取資料 |
> | Microsoft.Insights/Logs/MADeploymentPlan/Read | 從 MADeploymentPlan 資料表讀取資料 |
> | Microsoft.Insights/Logs/MADevice/Read | 從 MADevice 資料表讀取資料 |
> | Microsoft Insights/Logs/MADeviceNotEnrolled/Read | 從 MADeviceNotEnrolled 資料表讀取資料 |
> | Microsoft Insights/Logs/MADeviceNRT/Read | 從 MADeviceNRT 資料表讀取資料 |
> | Microsoft.Insights/Logs/MADevicePnPHealth/Read | 從 MADevicePnPHealth 資料表讀取資料 |
> | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | 從 MADevicePnPHealthAlternativeVersions 資料表讀取資料 |
> | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | 從 MADevicePnPHealthIssues 資料表讀取資料 |
> | Microsoft.Insights/Logs/MADeviceReadiness/Read | 從 MADeviceReadiness 資料表讀取資料 |
> | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | 從 MADriverInstanceReadiness 資料表讀取資料 |
> | Microsoft.Insights/Logs/MADriverReadiness/Read | 從 MADriverReadiness 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeAddin/Read | 從 MAOfficeAddin 資料表讀取資料 |
> | Microsoft Insights/Logs/MAOfficeAddinEntityHealth/Read | 從 MAOfficeAddinEntityHealth 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | 從 MAOfficeAddinHealth 資料表讀取資料 |
> | Microsoft Insights/Logs/MAOfficeAddinHealthEventNRT/Read | 從 MAOfficeAddinHealthEventNRT 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | 從 MAOfficeAddinHealthIssues 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | 從 MAOfficeAddinInstance 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | 從 MAOfficeAddinInstanceReadiness 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | 從 MAOfficeAddinReadiness 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeApp/Read | 從 MAOfficeApp 資料表讀取資料 |
> | Microsoft Insights/Logs/MAOfficeAppCrashesNRT/Read | 從 MAOfficeAppCrashesNRT 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | 從 MAOfficeAppHealth 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | 從 MAOfficeAppInstance 資料表讀取資料 |
> | Microsoft Insights/Logs/MAOfficeAppInstanceHealth/Read | 從 MAOfficeAppInstanceHealth 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | 從 MAOfficeAppReadiness 資料表讀取資料 |
> | Microsoft Insights/Logs/MAOfficeAppSessionsNRT/Read | 從 MAOfficeAppSessionsNRT 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | 從 MAOfficeBuildInfo 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | 從 MAOfficeCurrencyAssessment 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | 從 MAOfficeCurrencyAssessmentDailyCounts 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | 從 MAOfficeDeploymentStatus 資料表讀取資料 |
> | Microsoft Insights/Logs/MAOfficeDeploymentStatusNRT/Read | 從 MAOfficeDeploymentStatusNRT 資料表讀取資料 |
> | Microsoft Insights/Logs/MAOfficeMacroErrorNRT/Read | 從 MAOfficeMacroErrorNRT 資料表讀取資料 |
> | Microsoft Insights/Logs/MAOfficeMacroGlobalHealth/Read | 從 MAOfficeMacroGlobalHealth 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | 從 MAOfficeMacroHealth 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | 從 MAOfficeMacroHealthIssues 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | 從 MAOfficeMacroIssueInstanceReadiness 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | 從 MAOfficeMacroIssueReadiness 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | 從 MAOfficeMacroSummary 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeSuite/Read | 從 MAOfficeSuite 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | 從 MAOfficeSuiteInstance 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | 從 MAProposedPilotDevices 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | 從 MAWindowsBuildInfo 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | 從 MAWindowsCurrencyAssessment 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | 從 MAWindowsCurrencyAssessmentDailyCounts 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | 從 MAWindowsDeploymentStatus 資料表讀取資料 |
> | Microsoft Insights/Logs/MAWindowsDeploymentStatusNRT/Read | 從 MAWindowsDeploymentStatusNRT 資料表讀取資料 |
> | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | 從 MAWindowsSysReqInstanceReadiness 資料表讀取資料 |
> | Microsoft Insights/Logs/McasShadowItReporting/Read | 從 McasShadowItReporting 資料表讀取資料 |
> | Microsoft Insights/Logs/MicrosoftAzureBastionAuditLogs/Read | 從 MicrosoftAzureBastionAuditLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/MicrosoftDataShareReceivedSnapshotLog/Read | 從 MicrosoftDataShareReceivedSnapshotLog 資料表讀取資料 |
> | Microsoft Insights/Logs/MicrosoftDataShareSentSnapshotLog/Read | 從 MicrosoftDataShareSentSnapshotLog 資料表讀取資料 |
> | Microsoft Insights/Logs/MicrosoftDataShareShareLog/Read | 從 MicrosoftDataShareShareLog 資料表讀取資料 |
> | Microsoft Insights/Logs/MicrosoftDynamicsTelemetryPerformanceLogs/Read | 從 MicrosoftDynamicsTelemetryPerformanceLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/MicrosoftDynamicsTelemetrySystemMetricsLogs/Read | 從 MicrosoftDynamicsTelemetrySystemMetricsLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/MicrosoftHealthcareApisAuditLogs/Read | 從 MicrosoftHealthcareApisAuditLogs 資料表讀取資料 |
> | Microsoft.Insights/Logs/NetworkMonitoring/Read | 從 NetworkMonitoring 資料表讀取資料 |
> | Microsoft.Insights/Logs/OfficeActivity/Read | 從 OfficeActivity 資料表讀取資料 |
> | Microsoft.Insights/Logs/Operation/Read | 從 Operation 資料表讀取資料 |
> | Microsoft.Insights/Logs/OutboundConnection/Read | 從 OutboundConnection 資料表讀取資料 |
> | Microsoft.Insights/Logs/Perf/Read | 從 Perf 資料表讀取資料 |
> | Microsoft.Insights/Logs/ProtectionStatus/Read | 從 ProtectionStatus 資料表讀取資料 |
> | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | 從 ReservedAzureCommonFields 資料表讀取資料 |
> | Microsoft.Insights/Logs/ReservedCommonFields/Read | 從 ReservedCommonFields 資料表讀取資料 |
> | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | 從 SCCMAssessmentRecommendation 資料表讀取資料 |
> | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | 從 SCOMAssessmentRecommendation 資料表讀取資料 |
> | Microsoft.Insights/Logs/SecurityAlert/Read | 從 SecurityAlert 資料表讀取資料 |
> | Microsoft.Insights/Logs/SecurityBaseline/Read | 從 SecurityBaseline 資料表讀取資料 |
> | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | 從 SecurityBaselineSummary 資料表讀取資料 |
> | Microsoft.Insights/Logs/SecurityDetection/Read | 從 SecurityDetection 資料表讀取資料 |
> | Microsoft.Insights/Logs/SecurityEvent/Read | 從 SecurityEvent 資料表讀取資料 |
> | Microsoft Insights/Logs/SecurityIncident/Read | 從 SecurityIncident 資料表讀取資料 |
> | Microsoft Insights/Logs/SecurityIoTRawEvent/Read | 從 SecurityIoTRawEvent 資料表讀取資料 |
> | Microsoft Insights/Logs/SecurityNestedRecommendation/Read | 從 SecurityNestedRecommendation 資料表讀取資料 |
> | Microsoft Insights/Logs/SecurityRecommendation/Read | 從 SecurityRecommendation 資料表讀取資料 |
> | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | 從 ServiceFabricOperationalEvent 資料表讀取資料 |
> | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | 從 ServiceFabricReliableActorEvent 資料表讀取資料 |
> | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | 從 ServiceFabricReliableServiceEvent 資料表讀取資料 |
> | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | 從 SfBAssessmentRecommendation 資料表讀取資料 |
> | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | 從 SfBOnlineAssessmentRecommendation 資料表讀取資料 |
> | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | 從 SharePointOnlineAssessmentRecommendation 資料表讀取資料 |
> | Microsoft Insights/Logs/SignalRServiceDiagnosticLogs/Read | 從 SignalRServiceDiagnosticLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/SigninLogs/Read | 從 SigninLogs 資料表讀取資料 |
> | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | 從 SPAssessmentRecommendation 資料表讀取資料 |
> | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | 從 SQLAssessmentRecommendation 資料表讀取資料 |
> | Microsoft Insights/Logs/SqlDataClassification/Read | 從 SqlDataClassification 資料表讀取資料 |
> | Microsoft.Insights/Logs/SQLQueryPerformance/Read | 從 SQLQueryPerformance 資料表讀取資料 |
> | Microsoft Insights/Logs/SqlVulnerabilityAssessmentResult/Read | 從 SqlVulnerabilityAssessmentResult 資料表讀取資料 |
> | Microsoft Insights/Logs/StorageBlobLogs/Read | 從 StorageBlobLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/StorageFileLogs/Read | 從 StorageFileLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/StorageQueueLogs/Read | 從 StorageQueueLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/StorageTableLogs/Read | 從 StorageTableLogs 資料表讀取資料 |
> | Microsoft Insights/Logs/SucceededIngestion/Read | 從 SucceededIngestion 資料表讀取資料 |
> | Microsoft.Insights/Logs/Syslog/Read | 從 Syslog 資料表讀取資料 |
> | Microsoft.Insights/Logs/SysmonEvent/Read | 從 SysmonEvent 資料表讀取資料 |
> | Microsoft Insights/Logs/資料表。自訂/讀取 | 從任何自訂記錄檔讀取資料 |
> | Microsoft Insights/Logs/ThreatIntelligenceIndicator/Read | 從 ThreatIntelligenceIndicator 資料表讀取資料 |
> | Microsoft Insights/Logs/TSIIngress/Read | 從 TSIIngress 資料表讀取資料 |
> | Microsoft.Insights/Logs/UAApp/Read | 從 UAApp 資料表讀取資料 |
> | Microsoft.Insights/Logs/UAComputer/Read | 從 UAComputer 資料表讀取資料 |
> | Microsoft.Insights/Logs/UAComputerRank/Read | 從 UAComputerRank 資料表讀取資料 |
> | Microsoft.Insights/Logs/UADriver/Read | 從 UADriver 資料表讀取資料 |
> | Microsoft.Insights/Logs/UADriverProblemCodes/Read | 從 UADriverProblemCodes 資料表讀取資料 |
> | Microsoft.Insights/Logs/UAFeedback/Read | 從 UAFeedback 資料表讀取資料 |
> | Microsoft.Insights/Logs/UAHardwareSecurity/Read | 從 UAHardwareSecurity 資料表讀取資料 |
> | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | 從 UAIESiteDiscovery 資料表讀取資料 |
> | Microsoft.Insights/Logs/UAOfficeAddIn/Read | 從 UAOfficeAddIn 資料表讀取資料 |
> | Microsoft.Insights/Logs/UAProposedActionPlan/Read | 從 UAProposedActionPlan 資料表讀取資料 |
> | Microsoft.Insights/Logs/UASysReqIssue/Read | 從 UASysReqIssue 資料表讀取資料 |
> | Microsoft.Insights/Logs/UAUpgradedComputer/Read | 從 UAUpgradedComputer 資料表讀取資料 |
> | Microsoft.Insights/Logs/Update/Read | 從 Update 資料表讀取資料 |
> | Microsoft.Insights/Logs/UpdateRunProgress/Read | 從 UpdateRunProgress 資料表讀取資料 |
> | Microsoft.Insights/Logs/UpdateSummary/Read | 從 UpdateSummary 資料表讀取資料 |
> | Microsoft.Insights/Logs/Usage/Read | 從 Usage 資料表讀取資料 |
> | Microsoft Insights/Logs/UserAccessAnalytics/Read | 從 UserAccessAnalytics 資料表讀取資料 |
> | Microsoft Insights/Logs/UserPeerAnalytics/Read | 從 UserPeerAnalytics 資料表讀取資料 |
> | Microsoft Insights/Logs/VMBoundPort/Read | 從 VMBoundPort 資料表讀取資料 |
> | Microsoft Insights/Logs/VMComputer/Read | 從 VMComputer 資料表讀取資料 |
> | Microsoft Insights/Logs/VMConnection/Read | 從 VMConnection 資料表讀取資料 |
> | Microsoft Insights/Logs/VMProcess/Read | 從 VMProcess 資料表讀取資料 |
> | Microsoft.Insights/Logs/W3CIISLog/Read | 從 W3CIISLog 資料表讀取資料 |
> | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | 從 WaaSDeploymentStatus 資料表讀取資料 |
> | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | 從 WaaSInsiderStatus 資料表讀取資料 |
> | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | 從 WaaSUpdateStatus 資料表讀取資料 |
> | Microsoft.Insights/Logs/WDAVStatus/Read | 從 WDAVStatus 資料表讀取資料 |
> | Microsoft.Insights/Logs/WDAVThreat/Read | 從 WDAVThreat 資料表讀取資料 |
> | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | 從 WindowsClientAssessmentRecommendation 資料表讀取資料 |
> | Microsoft Insights/Logs/Windowsevent 進行篩選/Read | 從 WindowsEvent 資料表讀取資料 |
> | Microsoft.Insights/Logs/WindowsFirewall/Read | 從 WindowsFirewall 資料表讀取資料 |
> | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | 從 WindowsServerAssessmentRecommendation 資料表讀取資料 |
> | Microsoft.Insights/Logs/WireData/Read | 從 WireData 資料表讀取資料 |
> | Microsoft Insights/Logs/WorkloadMonitoringPerf/Read | 從 WorkloadMonitoringPerf 資料表中讀取資料 |
> | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | 從 WUDOAggregatedStatus 資料表讀取資料 |
> | Microsoft.Insights/Logs/WUDOStatus/Read | 從 WUDOStatus 資料表讀取資料 |
> | Microsoft Insights/Logs/WVDCheckpoints/Read | 從 WVDCheckpoints 資料表讀取資料 |
> | Microsoft Insights/Logs/WVDConnections/Read | 從 WVDConnections 資料表讀取資料 |
> | Microsoft Insights/Logs/WVDErrors/Read | 從 WVDErrors 資料表讀取資料 |
> | Microsoft Insights/Logs/WVDFeeds/Read | 從 WVDFeeds 資料表讀取資料 |
> | Microsoft Insights/Logs/WVDHostRegistrations/Read | 從 WVDHostRegistrations 資料表讀取資料 |
> | Microsoft Insights/Logs/WVDManagement/Read | 從 WVDManagement 資料表讀取資料 |
> | Microsoft.Insights/MetricAlerts/Write | 建立或更新計量警示 |
> | Microsoft.Insights/MetricAlerts/Delete | 刪除計量警示 |
> | Microsoft.Insights/MetricAlerts/Read | 讀取計量警示 |
> | Microsoft.Insights/MetricAlerts/Status/Read | 讀取計量警示狀態 |
> | Microsoft Insights/MetricBaselines/Read | 讀取計量基準 |
> | Microsoft.Insights/MetricDefinitions/Read | 讀取計量定義 |
> | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | 讀取計量定義 |
> | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | 讀取計量定義 |
> | Microsoft Insights/Metricnamespaces/Read | 讀取計量命名空間 |
> | Microsoft.Insights/Metrics/Read | 讀取計量 |
> | Microsoft Insights/計量/Microsoft. Insights/Read | 讀取計量 |
> | Microsoft.Insights/Metrics/providers/Metrics/Read | 讀取計量 |
> | Microsoft Insights/myWorkbooks/Write | 建立或更新私人活頁簿 |
> | Microsoft Insights/myWorkbooks/Read | 讀取私用活頁簿 |
> | Microsoft Insights/MyWorkbooks/Read | 讀取私用活頁簿 |
> | Microsoft Insights/MyWorkbooks/Write | 建立或更新私人活頁簿 |
> | Microsoft Insights/MyWorkbooks/Delete | 刪除私人活頁簿 |
> | Microsoft.Insights/Operations/Read | 讀取作業 |
> | Microsoft Insights/PrivateLinkScopeOperationStatuses/Read | 讀取私用連結範圍的作業狀態 |
> | Microsoft Insights/PrivateLinkScopes/Read | 讀取私人連結範圍 |
> | Microsoft Insights/PrivateLinkScopes/Write | 建立或更新私人連結範圍 |
> | Microsoft Insights/PrivateLinkScopes/Delete | 刪除私人連結範圍 |
> | Microsoft Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Read | 讀取私人端點連線 proxy |
> | Microsoft Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Write | 建立或更新私人端點連線 proxy |
> | Microsoft Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Delete | 刪除私人端點連線 proxy |
> | Microsoft Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Validate/Action | 驗證私人端點連線 proxy |
> | Microsoft Insights/PrivateLinkScopes/PrivateEndpointConnections/Read | 讀取私人端點連線 |
> | Microsoft Insights/PrivateLinkScopes/PrivateEndpointConnections/Write | 建立或更新私人端點連線 |
> | Microsoft Insights/PrivateLinkScopes/PrivateEndpointConnections/Delete | 刪除私人端點連線 |
> | Microsoft Insights/PrivateLinkScopes/PrivateLinkResources/Read | 讀取私人連結資源 |
> | Microsoft Insights/PrivateLinkScopes/ScopedResources/Read | 讀取私用連結範圍的資源 |
> | Microsoft Insights/PrivateLinkScopes/ScopedResources/Write | 建立或更新私人連結範圍的資源 |
> | Microsoft Insights/PrivateLinkScopes/ScopedResources/Delete | 刪除私人連結範圍的資源 |
> | Microsoft.Insights/ScheduledQueryRules/Write | 寫入排程的查詢規則 |
> | Microsoft.Insights/ScheduledQueryRules/Read | 讀取排程的查詢規則 |
> | Microsoft.Insights/ScheduledQueryRules/Delete | 刪除排程的查詢規則 |
> | Microsoft.Insights/Tenants/Register/Action | 將 Microsoft Insights 提供者初始化 |
> | Microsoft.Insights/Webtests/Write | 寫入至 Webtest 設定 |
> | Microsoft.Insights/Webtests/Delete | 刪除 Webtest 設定 |
> | Microsoft.Insights/Webtests/Read | 讀取 Webtest 設定 |
> | Microsoft.Insights/Webtests/GetToken/Read | 讀取 Webtest 權杖 |
> | Microsoft.Insights/Webtests/MetricDefinitions/Read | 讀取 Webtest 計量定義 |
> | Microsoft.Insights/Webtests/Metrics/Read | 讀取 Webtest 計量 |
> | Microsoft Insights/活頁簿/撰寫 | 建立或更新活頁簿 |
> | Microsoft Insights/活頁簿/刪除 | 刪除活頁簿 |
> | Microsoft Insights/活頁簿/讀取 | 讀取活頁簿 |
> | **DataAction** | **說明** |
> | Microsoft Insights/DataCollectionRules/資料/寫入 | 將資料傳送至資料收集規則 |
> | Microsoft.Insights/Metrics/Write | 寫入計量 |

### <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

Azure 服務： [Azure 監視器](../azure-monitor/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.OperationalInsights/register/action | 向資源提供者註冊訂用帳戶。 |
> | operationalinsights/註冊/動作 | Rergisters 訂用帳戶。 |
> | operationalinsights/取消註冊/動作 | 取消註冊訂用帳戶。 |
> | operationalinsights/availableservicetiers/read | 取得可用的服務層級。 |
> | OperationalInsights/叢集/讀取 | 取得叢集 |
> | OperationalInsights/叢集/寫入 | 建立或更新叢集 |
> | OperationalInsights/叢集/刪除 | 刪除叢集 |
> | OperationalInsights/deletedWorkspaces/read | 列出已虛刪除期間的工作區。 |
> | Microsoft.OperationalInsights/linkTargets/read | 列出已虛刪除期間的工作區。 |
> | operationalinsights/位置/operationStatuses/讀取 | 取得 Log Analytics Azure 非同步作業狀態。 |
> | microsoft.operationalinsights/operations/read | 列出所有可用的 OperationalInsights Rest API 作業。 |
> | Microsoft.OperationalInsights/workspaces/write | 建立新的工作區，或藉由提供來自現有工作區的客戶識別碼來連結至現有工作區。 |
> | Microsoft.OperationalInsights/workspaces/read | 取得現有工作區 |
> | Microsoft.OperationalInsights/workspaces/delete | 刪除工作區。 如果工作區在建立時連結到現有工作區，則系統不會刪除其所連結的工作區。 |
> | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | 為工作區產生註冊憑證。 此憑證可用來將 Microsoft System Center Operation Manager 連線到工作區。 |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | 擷取工作區的共用金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。 |
> | Microsoft.OperationalInsights/workspaces/listKeys/action | 擷取工作區的清單金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。 |
> | Microsoft.OperationalInsights/workspaces/search/action | 執行搜尋查詢 |
> | Microsoft.OperationalInsights/workspaces/purge/action | 從工作區刪除指定的資料 |
> | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | 重新產生指定的工作區共用金鑰 |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | 使用新的引擎進行搜尋。 |
> | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | 取得搜尋結構描述 V2。 |
> | Microsoft.OperationalInsights/workspaces/api/query/action | 使用新的引擎進行搜尋。 |
> | Microsoft.OperationalInsights/workspaces/api/query/schema/read | 取得搜尋結構描述 V2。 |
> | OperationalInsights/workspace/availableservicetiers/read | 適用于工作區之所有可用服務層級的清單。 |
> | Microsoft.OperationalInsights/workspaces/configurationScopes/read | 取得組態範圍 |
> | Microsoft.OperationalInsights/workspaces/configurationScopes/write | 設定組態範圍 |
> | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | 刪除組態範圍 |
> | operationalinsights/workspace/dataExports/read | 取得特定的資料匯出。 |
> | operationalinsights/workspace/dataExports/write | 建立或更新資料匯出。 |
> | operationalinsights/workspace/dataExports/delete | 刪除特定的資料匯出。 |
> | Microsoft.OperationalInsights/workspaces/datasources/read | 取得工作區下的資料來源。 |
> | Microsoft.OperationalInsights/workspaces/datasources/write | 在工作區下建立/更新資料來源。 |
> | Microsoft.OperationalInsights/workspaces/datasources/delete | 刪除工作區下的資料來源。 |
> | Microsoft.OperationalInsights/workspaces/gateways/delete | 移除針對工作區設定的閘道。 |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | 列出給定工作區可見的所有智慧套件，並且也會列出該工作區會啟用還是停用套件。 |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | 為給定工作區啟用智慧套件。 |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | 為給定工作區停用智慧套件。 |
> | Microsoft.OperationalInsights/workspaces/linkedServices/read | 取得指定工作區下已連結的服務。 |
> | Microsoft.OperationalInsights/workspaces/linkedServices/write | 建立/更新指定工作區下已連結的服務。 |
> | Microsoft.OperationalInsights/workspaces/linkedServices/delete | 刪除指定工作區下已連結的服務。 |
> | Microsoft.OperationalInsights/workspaces/listKeys/read | 擷取工作區的清單金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。 |
> | Microsoft.OperationalInsights/workspaces/managementGroups/read | 取得與此工作區連線之 System Center Operations Manager 管理群組的名稱和中繼資料。 |
> | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | 取得工作區下的計量定義 |
> | Microsoft.OperationalInsights/workspaces/notificationSettings/read | 針對工作區取得使用者的通知設定。 |
> | Microsoft.OperationalInsights/workspaces/notificationSettings/write | 針對工作區設定使用者的通知設定。 |
> | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | 針對工作區刪除使用者的通知設定。 |
> | operationalinsights/工作區/作業/讀取 | 取得 OperationalInsights 工作區操作的狀態。 |
> | Microsoft.OperationalInsights/workspaces/query/read | 針對工作區中的資料執行查詢 |
> | OperationalInsights/workspace/query/AADDomainServicesAccountLogon/read | 從 AADDomainServicesAccountLogon 資料表讀取資料 |
> | OperationalInsights/workspace/query/AADDomainServicesAccountManagement/read | 從 AADDomainServicesAccountManagement 資料表讀取資料 |
> | OperationalInsights/workspace/query/AADDomainServicesDirectoryServiceAccess/read | 從 AADDomainServicesDirectoryServiceAccess 資料表讀取資料 |
> | OperationalInsights/workspace/query/AADDomainServicesLogonLogoff/read | 從 AADDomainServicesLogonLogoff 資料表讀取資料 |
> | OperationalInsights/workspace/query/AADDomainServicesPolicyChange/read | 從 AADDomainServicesPolicyChange 資料表讀取資料 |
> | OperationalInsights/workspace/query/AADDomainServicesPrivilegeUse/read | 從 AADDomainServicesPrivilegeUse 資料表讀取資料 |
> | OperationalInsights/workspace/query/AADDomainServicesSystemSecurity/read | 從 AADDomainServicesSystemSecurity 資料表讀取資料 |
> | OperationalInsights/workspace/query/AADManagedIdentitySignInLogs/read | 從 AADManagedIdentitySignInLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AADNonInteractiveUserSignInLogs/read | 從 AADNonInteractiveUserSignInLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AADProvisioningLogs/read | 從 AADProvisioningLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AADServicePrincipalSignInLogs/read | 從 AADServicePrincipalSignInLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/ABSBotRequests/read | 從 ABSBotRequests 資料表讀取資料 |
> | OperationalInsights/workspace/query/ABSChannelToBotRequests/read | 從 ABSChannelToBotRequests 資料表讀取資料 |
> | OperationalInsights/workspace/query/ABSDependenciesRequests/read | 從 ABSDependenciesRequests 資料表讀取資料 |
> | OperationalInsights/workspace/query/ACSBillingUsage/read | 從 ACSBillingUsage 資料表讀取資料 |
> | OperationalInsights/workspace/query/ACSChatIncomingOperations/read | 從 ACSChatIncomingOperations 資料表讀取資料 |
> | OperationalInsights/workspace/query/ACSSMSIncomingOperations/read | 從 ACSSMSIncomingOperations 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | 從 ADAssessmentRecommendation 資料表讀取資料 |
> | OperationalInsights/workspace/query/AddonAzureBackupAlerts/read | 從 AddonAzureBackupAlerts 資料表讀取資料 |
> | OperationalInsights/workspace/query/AddonAzureBackupJobs/read | 從 AddonAzureBackupJobs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AddonAzureBackupPolicy/read | 從 AddonAzureBackupPolicy 資料表讀取資料 |
> | OperationalInsights/workspace/query/AddonAzureBackupProtectedInstance/read | 從 AddonAzureBackupProtectedInstance 資料表讀取資料 |
> | OperationalInsights/workspace/query/AddonAzureBackupStorage/read | 從 AddonAzureBackupStorage 資料表讀取資料 |
> | OperationalInsights/workspace/query/ADFActivityRun/read | 從 ADFActivityRun 資料表讀取資料 |
> | OperationalInsights/workspace/query/ADFPipelineRun/read | 從 ADFPipelineRun 資料表讀取資料 |
> | OperationalInsights/workspace/query/ADFSSISIntegrationRuntimeLogs/read | 從 ADFSSISIntegrationRuntimeLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/ADFSSISPackageEventMessageCoNtext/read | 從 ADFSSISPackageEventMessageCoNtext 資料表讀取資料 |
> | OperationalInsights/workspace/query/ADFSSISPackageEventMessages/read | 從 ADFSSISPackageEventMessages 資料表讀取資料 |
> | OperationalInsights/workspace/query/ADFSSISPackageExecutableStatistics/read | 從 ADFSSISPackageExecutableStatistics 資料表讀取資料 |
> | OperationalInsights/workspace/query/ADFSSISPackageExecutionComponentPhases/read | 從 ADFSSISPackageExecutionComponentPhases 資料表讀取資料 |
> | OperationalInsights/workspace/query/ADFSSISPackageExecutionDataStatistics/read | 從 ADFSSISPackageExecutionDataStatistics 資料表讀取資料 |
> | OperationalInsights/workspace/query/ADFTriggerRun/read | 從 ADFTriggerRun 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | 從 ADReplicationResult 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | 從 ADSecurityAssessmentRecommendation 資料表讀取資料 |
> | OperationalInsights/workspace/query/ADTDigitalTwinsOperation/read | 從 ADTDigitalTwinsOperation 資料表讀取資料 |
> | OperationalInsights/workspace/query/ADTEventRoutesOperation/read | 從 ADTEventRoutesOperation 資料表讀取資料 |
> | OperationalInsights/workspace/query/ADTModelsOperation/read | 從 ADTModelsOperation 資料表讀取資料 |
> | OperationalInsights/workspace/query/ADTQueryOperation/read | 從 ADTQueryOperation 資料表讀取資料 |
> | OperationalInsights/workspace/query/ADXCommand/read | 從 ADXCommand 資料表讀取資料 |
> | OperationalInsights/workspace/query/ADXIngestionBatching/read | 從 ADXIngestionBatching 資料表讀取資料 |
> | OperationalInsights/workspace/query/ADXQuery/read | 從 ADXQuery 資料表讀取資料 |
> | OperationalInsights/workspace/query/ADXTableDetails/read | 從 ADXTableDetails 資料表讀取資料 |
> | OperationalInsights/workspace/query/ADXTableUsageStatistics/read | 從 ADXTableUsageStatistics 資料表讀取資料 |
> | OperationalInsights/workspace/query/AegDeliveryFailureLogs/read | 從 AegDeliveryFailureLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AegPublishFailureLogs/read | 從 AegPublishFailureLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AEWAuditLogs/read | 從 AEWAuditLogs 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/Alert/read | 從 Alert 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | 從 AlertHistory 資料表讀取資料 |
> | OperationalInsights/workspace/query/AmlComputeClusterEvent/read | 從 AmlComputeClusterEvent 資料表讀取資料 |
> | OperationalInsights/workspace/query/AmlComputeClusterNodeEvent/read | 從 AmlComputeClusterNodeEvent 資料表讀取資料 |
> | OperationalInsights/workspace/query/AmlComputeCpuGpuUtilization/read | 從 AmlComputeCpuGpuUtilization 資料表讀取資料 |
> | OperationalInsights/workspace/query/AmlComputeJobEvent/read | 從 AmlComputeJobEvent 資料表讀取資料 |
> | OperationalInsights/workspace/query/AmlRunStatusChangedEvent/read | 從 AmlRunStatusChangedEvent 資料表讀取資料 |
> | OperationalInsights/workspace/query/ApiManagementGatewayLogs/read | 從 ApiManagementGatewayLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppAvailabilityResults/read | 從 AppAvailabilityResults 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppBrowserTimings/read | 從 AppBrowserTimings 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | 從 AppCenterError 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppDependencies/read | 從 AppDependencies 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppEvents/read | 從 AppEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppExceptions/read | 從 AppExceptions 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | 從 ApplicationInsights 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppMetrics/read | 從 AppMetrics 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppPageViews/read | 從 AppPageViews 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppPerformanceCounters/read | 從 AppPerformanceCounters 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppPlatformLogsforSpring/read | 從 AppPlatformLogsforSpring 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppPlatformSystemLogs/read | 從 AppPlatformSystemLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppRequests/read | 從 AppRequests 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppServiceAntivirusScanAuditLogs/read | 從 AppServiceAntivirusScanAuditLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppServiceAntivirusScanLogs/read | 從 AppServiceAntivirusScanLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppServiceAppLogs/read | 從 AppServiceAppLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppServiceAuditLogs/read | 從 AppServiceAuditLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppServiceConsoleLogs/read | 從 AppServiceConsoleLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppServiceEnvironmentPlatformLogs/read | 從 AppServiceEnvironmentPlatformLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppServiceFileAuditLogs/read | 從 AppServiceFileAuditLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppServiceHTTPLogs/read | 從 AppServiceHTTPLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppServiceIPSecAuditLogs/read | 從 AppServiceIPSecAuditLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppServicePlatformLogs/read | 從 AppServicePlatformLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppSystemEvents/read | 從 AppSystemEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/AppTraces/read | 從 AppTraces 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | 從 AuditLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/AutoscaleEvaluationsLog/read | 從 AutoscaleEvaluationsLog 資料表讀取資料 |
> | OperationalInsights/workspace/query/AutoscaleScaleActionsLog/read | 從 AutoscaleScaleActionsLog 資料表讀取資料 |
> | OperationalInsights/workspace/query/AWSCloudTrail/read | 從 AWSCloudTrail 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | 從 AzureActivity 資料表讀取資料 |
> | OperationalInsights/workspace/query/AzureAssessmentRecommendation/read | 從 AzureAssessmentRecommendation 資料表讀取資料 |
> | OperationalInsights/workspace/query/AzureDevOpsAuditing/read | 從 AzureDevOpsAuditing 資料表讀取資料 |
> | OperationalInsights/workspace/query/AzureDiagnostics/read | 從 AzureDiagnostics 資料表讀取資料 |
> | OperationalInsights/workspace/query/AzureDiagnostics/read | 從 AzureDiagnostics 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | 從 AzureMetrics 資料表讀取資料 |
> | OperationalInsights/workspace/query/BaiClusterEvent/read | 從 BaiClusterEvent 資料表讀取資料 |
> | OperationalInsights/workspace/query/BaiClusterNodeEvent/read | 從 BaiClusterNodeEvent 資料表讀取資料 |
> | OperationalInsights/workspace/query/BaiJobEvent/read | 從 BaiJobEvent 資料表讀取資料 |
> | OperationalInsights/workspace/query/BehaviorAnalytics/read | 從 BehaviorAnalytics 資料表讀取資料 |
> | OperationalInsights/workspace/query/BlockchainApplicationLog/read | 從 BlockchainApplicationLog 資料表讀取資料 |
> | OperationalInsights/workspace/query/BlockchainProxyLog/read | 從 BlockchainProxyLog 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | 從 BoundPort 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | 從 CommonSecurityLog 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | 從 ComputerGroup 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | 從 ConfigurationChange 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | 從 ConfigurationData 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | 從 ContainerImageInventory 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | 從 ContainerInventory 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | 從 ContainerLog 資料表讀取資料 |
> | OperationalInsights/workspace/query/ContainerNodeInventory/read | 從 ContainerNodeInventory 資料表讀取資料 |
> | OperationalInsights/workspace/query/ContainerRegistryLoginEvents/read | 從 ContainerRegistryLoginEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/ContainerRegistryRepositoryEvents/read | 從 ContainerRegistryRepositoryEvents 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | 從 ContainerServiceLog 資料表讀取資料 |
> | OperationalInsights/workspace/query/CoreAzureBackup/read | 從 CoreAzureBackup 資料表讀取資料 |
> | OperationalInsights/workspace/query/DatabricksAccounts/read | 從 DatabricksAccounts 資料表讀取資料 |
> | OperationalInsights/workspace/query/DatabricksClusters/read | 從 DatabricksClusters 資料表讀取資料 |
> | OperationalInsights/workspace/query/DatabricksDBFS/read | 從 DatabricksDBFS 資料表讀取資料 |
> | OperationalInsights/workspace/query/DatabricksInstancePools/read | 從 DatabricksInstancePools 資料表讀取資料 |
> | OperationalInsights/workspace/query/DatabricksJobs/read | 從 DatabricksJobs 資料表讀取資料 |
> | OperationalInsights/workspace/query/DatabricksNotebook/read | 從 DatabricksNotebook 資料表讀取資料 |
> | OperationalInsights/workspace/query/DatabricksSecrets/read | 從 DatabricksSecrets 資料表讀取資料 |
> | OperationalInsights/workspace/query/DatabricksSQLPermissions/read | 從 DatabricksSQLPermissions 資料表讀取資料 |
> | OperationalInsights/workspace/query/DatabricksSSH/read | 從 DatabricksSSH 資料表讀取資料 |
> | OperationalInsights/workspace/query/DatabricksTables/read | 從 DatabricksTables 資料表讀取資料 |
> | OperationalInsights/workspace/query/DatabricksWorkspace/read | 從 DatabricksWorkspace 資料表讀取資料 |
> | OperationalInsights/workspace/query/相依性/讀取 | 從相依性資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | 從 DeviceAppCrash 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | 從 DeviceAppLaunch 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | 從 DeviceCalendar 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | 從 DeviceCleanup 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | 從 DeviceConnectSession 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | 從 DeviceEtw 資料表讀取資料 |
> | OperationalInsights/workspace/query/DeviceEvents/read | 從 DeviceEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/DeviceFileEvents/read | 從 DeviceFileEvents 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | 從 DeviceHardwareHealth 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | 從 DeviceHealth 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | 從 DeviceHeartbeat 資料表讀取資料 |
> | OperationalInsights/workspace/query/DeviceImageLoadEvents/read | 從 DeviceImageLoadEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/DeviceInfo/read | 從 DeviceInfo 資料表讀取資料 |
> | OperationalInsights/workspace/query/DeviceLogonEvents/read | 從 DeviceLogonEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/DeviceNetworkEvents/read | 從 DeviceNetworkEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/DeviceNetworkInfo/read | 從 DeviceNetworkInfo 資料表讀取資料 |
> | OperationalInsights/workspace/query/DeviceProcessEvents/read | 從 DeviceProcessEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/DeviceRegistryEvents/read | 從 DeviceRegistryEvents 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | 從 DeviceSkypeHeartbeat 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | 從 DeviceSkypeSignIn 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | 從 DeviceSleepState 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | 從 DHAppFailure 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | 從 DHAppReliability 資料表讀取資料 |
> | OperationalInsights/workspace/query/DHCPActivity/read | 從 DHCPActivity 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | 從 DHDriverReliability 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | 從 DHLogonFailures 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | 從 DHLogonMetrics 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | 從 DHOSCrashData 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | 從 DHOSReliability 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | 從 DHWipAppLearning 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | 從 DnsEvents 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | 從 DnsInventory 資料表讀取資料 |
> | OperationalInsights/workspace/query/Dynamics365Activity/read | 從 Dynamics365Activity 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | 從 ETWEvent 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/Event/read | 從 Event 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | 從 ExchangeAssessmentRecommendation 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | 從 ExchangeOnlineAssessmentRecommendation 資料表讀取資料 |
> | OperationalInsights/workspace/query/FailedIngestion/read | 從 FailedIngestion 資料表讀取資料 |
> | OperationalInsights/workspace/query/FunctionAppLogs/read | 從 FunctionAppLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightAmbariClusterAlerts/read | 從 HDInsightAmbariClusterAlerts 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightAmbariSystemMetrics/read | 從 HDInsightAmbariSystemMetrics 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightGatewayAuditLogs/read | 從 HDInsightGatewayAuditLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightHadoopAndYarnLogs/read | 從 HDInsightHadoopAndYarnLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightHadoopAndYarnMetrics/read | 從 HDInsightHadoopAndYarnMetrics 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightHBaseLogs/read | 從 HDInsightHBaseLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightHBaseMetrics/read | 從 HDInsightHBaseMetrics 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightHiveAndLLAPLogs/read | 從 HDInsightHiveAndLLAPLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightHiveAndLLAPMetrics/read | 從 HDInsightHiveAndLLAPMetrics 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightJupyterNotebookEvents/read | 從 HDInsightJupyterNotebookEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightKafkaLogs/read | 從 HDInsightKafkaLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightKafkaMetrics/read | 從 HDInsightKafkaMetrics 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightOozieLogs/read | 從 HDInsightOozieLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightRangerAuditLogs/read | 從 HDInsightRangerAuditLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightSecurityLogs/read | 從 HDInsightSecurityLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightSparkApplicationEvents/read | 從 HDInsightSparkApplicationEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightSparkBlockManagerEvents/read | 從 HDInsightSparkBlockManagerEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightSparkEnvironmentEvents/read | 從 HDInsightSparkEnvironmentEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightSparkExecutorEvents/read | 從 HDInsightSparkExecutorEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightSparkExtraEvents/read | 從 HDInsightSparkExtraEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightSparkJobEvents/read | 從 HDInsightSparkJobEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightSparkLogs/read | 從 HDInsightSparkLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightSparkSQLExecutionEvents/read | 從 HDInsightSparkSQLExecutionEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightSparkStageEvents/read | 從 HDInsightSparkStageEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightSparkStageTaskAccumulables/read | 從 HDInsightSparkStageTaskAccumulables 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightSparkTaskEvents/read | 從 HDInsightSparkTaskEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightStormLogs/read | 從 HDInsightStormLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightStormMetrics/read | 從 HDInsightStormMetrics 資料表讀取資料 |
> | OperationalInsights/workspace/query/HDInsightStormTopologyMetrics/read | 從 HDInsightStormTopologyMetrics 資料表讀取資料 |
> | OperationalInsights/workspace/query/HealthStateChangeEvent/read | 從 HealthStateChangeEvent 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | 從 Heartbeat 資料表讀取資料 |
> | OperationalInsights/workspace/query/HuntingBookmark/read | 從 HuntingBookmark 資料表讀取資料 |
> | OperationalInsights/workspace/query/IdentityInfo/read | 從 IdentityInfo 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | 從 IISAssessmentRecommendation 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | 從 InboundConnection 資料表讀取資料 |
> | OperationalInsights/workspace/query/InsightsMetrics/read | 從 InsightsMetrics 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | 從 IntuneAuditLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/IntuneDeviceComplianceOrg/read | 從 IntuneDeviceComplianceOrg 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | 從 IntuneOperationalLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/IoTHubDistributedTracing/read | 從 IoTHubDistributedTracing 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | 從 KubeEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/KubeHealth/read | 從 KubeHealth 資料表讀取資料 |
> | OperationalInsights/workspace/query/KubeMonAgentEvents/read | 從 KubeMonAgentEvents 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | 從 KubeNodeInventory 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | 從 KubePodInventory 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | 從 KubeServices 資料表讀取資料 |
> | OperationalInsights/workspace/query/LAQueryLogs/read | 從 LAQueryLogs 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | 從 LinuxAuditLog 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | 從 MAApplication 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | 從 MAApplicationHealth 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | 從 MAApplicationHealthAlternativeVersions 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | 從 MAApplicationHealthIssues 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | 從 MAApplicationInstance 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | 從 MAApplicationInstanceReadiness 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | 從 MAApplicationReadiness 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | 從 MADeploymentPlan 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MADevice/read | 從 MADevice 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | 從 MADeviceNotEnrolled 資料表讀取資料 |
> | OperationalInsights/workspace/query/MADeviceNRT/read | 從 MADeviceNRT 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | 從 MADevicePnPHealth 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | 從 MADevicePnPHealthAlternativeVersions 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | 從 MADevicePnPHealthIssues 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | 從 MADeviceReadiness 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | 從 MADriverInstanceReadiness 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | 從 MADriverReadiness 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | 從 MAOfficeAddin 資料表讀取資料 |
> | OperationalInsights/workspace/query/MAOfficeAddinEntityHealth/read | 從 MAOfficeAddinEntityHealth 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | 從 MAOfficeAddinHealth 資料表讀取資料 |
> | OperationalInsights/workspace/query/MAOfficeAddinHealthEventNRT/read | 從 MAOfficeAddinHealthEventNRT 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | 從 MAOfficeAddinHealthIssues 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | 從 MAOfficeAddinInstance 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | 從 MAOfficeAddinInstanceReadiness 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | 從 MAOfficeAddinReadiness 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | 從 MAOfficeApp 資料表讀取資料 |
> | OperationalInsights/workspace/query/MAOfficeAppCrashesNRT/read | 從 MAOfficeAppCrashesNRT 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | 從 MAOfficeAppHealth 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | 從 MAOfficeAppInstance 資料表讀取資料 |
> | OperationalInsights/workspace/query/MAOfficeAppInstanceHealth/read | 從 MAOfficeAppInstanceHealth 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | 從 MAOfficeAppReadiness 資料表讀取資料 |
> | OperationalInsights/workspace/query/MAOfficeAppSessionsNRT/read | 從 MAOfficeAppSessionsNRT 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | 從 MAOfficeBuildInfo 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | 從 MAOfficeCurrencyAssessment 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | 從 MAOfficeCurrencyAssessmentDailyCounts 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | 從 MAOfficeDeploymentStatus 資料表讀取資料 |
> | OperationalInsights/workspace/query/MAOfficeDeploymentStatusNRT/read | 從 MAOfficeDeploymentStatusNRT 資料表讀取資料 |
> | OperationalInsights/workspace/query/MAOfficeMacroErrorNRT/read | 從 MAOfficeMacroErrorNRT 資料表讀取資料 |
> | OperationalInsights/workspace/query/MAOfficeMacroGlobalHealth/read | 從 MAOfficeMacroGlobalHealth 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | 從 MAOfficeMacroHealth 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | 從 MAOfficeMacroHealthIssues 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | 從 MAOfficeMacroIssueInstanceReadiness 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | 從 MAOfficeMacroIssueReadiness 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | 從 MAOfficeMacroSummary 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | 從 MAOfficeSuite 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | 從 MAOfficeSuiteInstance 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | 從 MAProposedPilotDevices 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | 從 MAWindowsBuildInfo 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | 從 MAWindowsCurrencyAssessment 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | 從 MAWindowsCurrencyAssessmentDailyCounts 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | 從 MAWindowsDeploymentStatus 資料表讀取資料 |
> | OperationalInsights/workspace/query/MAWindowsDeploymentStatusNRT/read | 從 MAWindowsDeploymentStatusNRT 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | 從 MAWindowsSysReqInstanceReadiness 資料表讀取資料 |
> | OperationalInsights/workspace/query/McasShadowItReporting/read | 從 McasShadowItReporting 資料表讀取資料 |
> | OperationalInsights/workspace/query/MicrosoftAzureBastionAuditLogs/read | 從 MicrosoftAzureBastionAuditLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/MicrosoftDataShareReceivedSnapshotLog/read | 從 MicrosoftDataShareReceivedSnapshotLog 資料表讀取資料 |
> | OperationalInsights/workspace/query/MicrosoftDataShareSentSnapshotLog/read | 從 MicrosoftDataShareSentSnapshotLog 資料表讀取資料 |
> | OperationalInsights/workspace/query/MicrosoftDataShareShareLog/read | 從 MicrosoftDataShareShareLog 資料表讀取資料 |
> | OperationalInsights/workspace/query/MicrosoftDynamicsTelemetryPerformanceLogs/read | 從 MicrosoftDynamicsTelemetryPerformanceLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/MicrosoftDynamicsTelemetrySystemMetricsLogs/read | 從 MicrosoftDynamicsTelemetrySystemMetricsLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/MicrosoftHealthcareApisAuditLogs/read | 從 MicrosoftHealthcareApisAuditLogs 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | 從 NetworkMonitoring 資料表讀取資料 |
> | OperationalInsights/workspace/query/NWConnectionMonitorDestinationListenerResult/read | 從 NWConnectionMonitorDestinationListenerResult 資料表讀取資料 |
> | OperationalInsights/workspace/query/NWConnectionMonitorDNSResult/read | 從 NWConnectionMonitorDNSResult 資料表讀取資料 |
> | OperationalInsights/workspace/query/NWConnectionMonitorPathResult/read | 從 NWConnectionMonitorPathResult 資料表讀取資料 |
> | OperationalInsights/workspace/query/NWConnectionMonitorTestResult/read | 從 NWConnectionMonitorTestResult 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | 從 OfficeActivity 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/Operation/read | 從 Operation 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | 從 OutboundConnection 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/Perf/read | 從 Perf 資料表讀取資料 |
> | OperationalInsights/workspace/query/PowerBIDatasetsTenantPreview/read | 從 PowerBIDatasetsTenantPreview 資料表讀取資料 |
> | OperationalInsights/workspace/query/PowerBIDatasetsWorkspacePreview/read | 從 PowerBIDatasetsWorkspacePreview 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | 從 ProtectionStatus 資料表讀取資料 |
> | OperationalInsights/workspace/query/requests/read | 從要求資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | 從 SCCMAssessmentRecommendation 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | 從 SCOMAssessmentRecommendation 資料表讀取資料 |
> | OperationalInsights/workspace/query/SecureScoreControls/read | 從 SecureScoreControls 資料表讀取資料 |
> | OperationalInsights/workspace/query/SecureScores/read | 從 SecureScores 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | 從 SecurityAlert 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | 從 SecurityBaseline 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | 從 SecurityBaselineSummary 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | 從 SecurityDetection 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | 從 SecurityEvent 資料表讀取資料 |
> | OperationalInsights/workspace/query/SecurityIncident/read | 從 SecurityIncident 資料表讀取資料 |
> | OperationalInsights/workspace/query/SecurityIoTRawEvent/read | 從 SecurityIoTRawEvent 資料表讀取資料 |
> | OperationalInsights/workspace/query/SecurityNestedRecommendation/read | 從 SecurityNestedRecommendation 資料表讀取資料 |
> | OperationalInsights/workspace/query/SecurityRecommendation/read | 從 SecurityRecommendation 資料表讀取資料 |
> | OperationalInsights/workspace/query/SecurityRegulatoryCompliance/read | 從 SecurityRegulatoryCompliance 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | 從 ServiceFabricOperationalEvent 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | 從 ServiceFabricReliableActorEvent 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | 從 ServiceFabricReliableServiceEvent 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | 從 SfBAssessmentRecommendation 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | 從 SfBOnlineAssessmentRecommendation 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | 從 SharePointOnlineAssessmentRecommendation 資料表讀取資料 |
> | OperationalInsights/workspace/query/SignalRServiceDiagnosticLogs/read | 從 SignalRServiceDiagnosticLogs 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | 從 SigninLogs 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | 從 SPAssessmentRecommendation 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | 從 SQLAssessmentRecommendation 資料表讀取資料 |
> | OperationalInsights/workspace/query/SqlDataClassification/read | 從 SqlDataClassification 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | 從 SQLQueryPerformance 資料表讀取資料 |
> | OperationalInsights/workspace/query/SqlVulnerabilityAssessmentResult/read | 從 SqlVulnerabilityAssessmentResult 資料表讀取資料 |
> | OperationalInsights/workspace/query/SqlVulnerabilityAssessmentScanStatus/read | 從 SqlVulnerabilityAssessmentScanStatus 資料表讀取資料 |
> | OperationalInsights/workspace/query/StorageBlobLogs/read | 從 StorageBlobLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/StorageFileLogs/read | 從 StorageFileLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/StorageQueueLogs/read | 從 StorageQueueLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/StorageTableLogs/read | 從 StorageTableLogs 資料表讀取資料 |
> | OperationalInsights/workspace/query/SucceededIngestion/read | 從 SucceededIngestion 資料表讀取資料 |
> | OperationalInsights/workspace/query/SynapseGatewayEvents/read | 從 SynapseGatewayEvents 資料表讀取資料 |
> | OperationalInsights/workspace/query/SynapseRBACEvents/read | 從 SynapseRBACEvents 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/Syslog/read | 從 Syslog 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | 從 SysmonEvent 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | 從任何自訂記錄檔讀取資料 |
> | OperationalInsights/workspace/query/ThreatIntelligenceIndicator/read | 從 ThreatIntelligenceIndicator 資料表讀取資料 |
> | OperationalInsights/workspace/query/TSIIngress/read | 從 TSIIngress 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/UAApp/read | 從 UAApp 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | 從 UAComputer 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | 從 UAComputerRank 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/UADriver/read | 從 UADriver 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | 從 UADriverProblemCodes 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | 從 UAFeedback 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | 從 UAHardwareSecurity 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | 從 UAIESiteDiscovery 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | 從 UAOfficeAddIn 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | 從 UAProposedActionPlan 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | 從 UASysReqIssue 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | 從 UAUpgradedComputer 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/Update/read | 從 Update 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | 從 UpdateRunProgress 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | 從 UpdateSummary 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/Usage/read | 從 Usage 資料表讀取資料 |
> | OperationalInsights/workspace/query/UserAccessAnalytics/read | 從 UserAccessAnalytics 資料表讀取資料 |
> | OperationalInsights/workspace/query/UserPeerAnalytics/read | 從 UserPeerAnalytics 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | 從 VMBoundPort 資料表讀取資料 |
> | OperationalInsights/workspace/query/VMComputer/read | 從 VMComputer 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | 從 VMConnection 資料表讀取資料 |
> | OperationalInsights/workspace/query/VMProcess/read | 從 VMProcess 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | 從 W3CIISLog 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | 從 WaaSDeploymentStatus 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | 從 WaaSInsiderStatus 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | 從 WaaSUpdateStatus 資料表讀取資料 |
> | OperationalInsights/workspace/query/關注清單/read | 從關注清單資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | 從 WDAVStatus 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | 從 WDAVThreat 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | 從 WindowsClientAssessmentRecommendation 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | 從 WindowsEvent 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | 從 WindowsFirewall 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | 從 WindowsServerAssessmentRecommendation 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/WireData/read | 從 WireData 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | 從 WorkloadMonitoringPerf 資料表中讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | 從 WUDOAggregatedStatus 資料表讀取資料 |
> | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | 從 WUDOStatus 資料表讀取資料 |
> | OperationalInsights/workspace/query/WVDAgentHealthStatus/read | 從 WVDAgentHealthStatus 資料表讀取資料 |
> | OperationalInsights/workspace/query/WVDCheckpoints/read | 從 WVDCheckpoints 資料表讀取資料 |
> | OperationalInsights/workspace/query/WVDConnections/read | 從 WVDConnections 資料表讀取資料 |
> | OperationalInsights/workspace/query/WVDErrors/read | 從 WVDErrors 資料表讀取資料 |
> | OperationalInsights/workspace/query/WVDFeeds/read | 從 WVDFeeds 資料表讀取資料 |
> | OperationalInsights/workspace/query/WVDHostRegistrations/read | 從 WVDHostRegistrations 資料表讀取資料 |
> | OperationalInsights/workspace/query/WVDManagement/read | 從 WVDManagement 資料表讀取資料 |
> | microsoft.operationalinsights/workspaces/rules/read | 取得所有警示規則。 |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | 取得已儲存的搜尋查詢 |
> | Microsoft.OperationalInsights/workspaces/savedSearches/write | 建立已儲存的搜尋查詢 |
> | Microsoft.OperationalInsights/workspaces/savedSearches/delete | 刪除已儲存的搜尋查詢 |
> | microsoft.operationalinsights/workspaces/savedsearches/results/read | 取得已儲存的搜尋結果。 已被取代 |
> | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | 取得已排程的搜尋。 |
> | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | 刪除已排程的搜尋。 |
> | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | 建立或更新已排程的搜尋。 |
> | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | 取得已排程的搜尋動作。 |
> | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | 刪除已排程的搜尋動作。 |
> | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | 建立或更新已排程的搜尋動作。 |
> | Microsoft.OperationalInsights/workspaces/schema/read | 取得工作區的搜尋結構描述。  搜尋結構描述包括公開的欄位和其類型。 |
> | operationalinsights/workspace/scopedPrivateLinkProxies/read | 取得範圍 Private Link Proxy。 |
> | operationalinsights/workspace/scopedPrivateLinkProxies/write | 將範圍設定 Private Link Proxy。 |
> | operationalinsights/workspace/scopedPrivateLinkProxies/delete | 刪除範圍 Private Link Proxy。 |
> | microsoft.operationalinsights/workspaces/search/read | 取得搜尋結果。 已被取代。 |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | 擷取工作區的共用金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。 |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | 建立新的儲存體組態。 這些組態可用來提取現有儲存體帳戶中來自某個位置的資料。 |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | 取得儲存體組態。 |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | 刪除儲存體組態。 這會讓 Microsoft Operational Insights 停止從儲存體帳戶讀取資料。 |
> | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | 取得工作區的搜尋升級翻譯失敗記錄 |
> | Microsoft.OperationalInsights/workspaces/usages/read | 取得工作區的使用量資料，包括工作區所讀取的資料量。 |

### <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

Azure 服務： [Azure 監視器](../azure-monitor/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.OperationsManagement/register/action | 向資源提供者註冊訂用帳戶。 |
> | Microsoft.OperationsManagement/managementAssociations/write | 建立新的管理關聯 |
> | Microsoft.OperationsManagement/managementAssociations/read | 取得現有的管理關聯 |
> | Microsoft.OperationsManagement/managementAssociations/delete | 刪除現有的管理關聯 |
> | Microsoft.OperationsManagement/managementConfigurations/write | 建立新的管理設定 |
> | Microsoft.OperationsManagement/managementConfigurations/read | 取得現有的管理設定 |
> | Microsoft.OperationsManagement/managementConfigurations/delete | 刪除現有的管理組態 |
> | Microsoft.OperationsManagement/solutions/write | 建立新的 OMS 解決方案 |
> | Microsoft.OperationsManagement/solutions/read | 取得現有的 OMS 解決方案 |
> | Microsoft.OperationsManagement/solutions/delete | 刪除現有的 OMS 解決方案 |

### <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

Azure 服務： [Azure 監視器](../azure-monitor/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | WorkloadMonitor/註冊/動作 | 向 WorkloadMonitor 資源提供者註冊訂用帳戶 |
> | WorkloadMonitor/取消註冊/動作 | 使用 Microsoft WorkloadMonitor 資源提供者取消註冊訂用帳戶 |
> | Microsoft.WorkloadMonitor/monitors/read | 取得監視器和其目前的健康狀態 |
> | WorkloadMonitor/monitor/history/read | 取得特定監視的健康狀態變更歷程記錄 |
> | Microsoft.WorkloadMonitor/operations/read | 取得支援的作業清單 |

## <a name="management--governance"></a>管理和治理

### <a name="microsoftadvisor"></a>Microsoft.Advisor

Azure 服務： [Azure Advisor](../advisor/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Advisor/generateRecommendations/action | 取得產生建議狀態 |
> | Microsoft.Advisor/register/action | 針對 Microsoft Advisor 註冊訂用帳戶 |
> | Microsoft.Advisor/unregister/action | 為 Microsoft Advisor 取消註冊訂用帳戶 |
> | Microsoft.Advisor/configurations/read | 取得組態 |
> | Microsoft.Advisor/configurations/write | 建立/更新設定 |
> | Microsoft.Advisor/generateRecommendations/read | 取得產生建議狀態 |
> | Microsoft Advisor/中繼資料/讀取 | 取得中繼資料 |
> | Microsoft.Advisor/operations/read | 取得 Microsoft Advisor 的作業 |
> | Microsoft.Advisor/recommendations/read | 讀取建議 |
> | Microsoft.Advisor/recommendations/available/action | Microsoft Advisor 提供新的建議 |
> | Microsoft.Advisor/recommendations/suppressions/read | 取得隱藏項目 |
> | Microsoft.Advisor/recommendations/suppressions/write | 建立/更新隱藏項目 |
> | Microsoft.Advisor/recommendations/suppressions/delete | 刪除隱藏項目 |
> | Microsoft.Advisor/suppressions/read | 取得隱藏項目 |
> | Microsoft.Advisor/suppressions/write | 建立/更新隱藏項目 |
> | Microsoft.Advisor/suppressions/delete | 刪除隱藏項目 |

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Azure 服務： [Azure 原則](../governance/policy/overview.md)、 [azure RBAC](overview.md)、 [Azure Resource Manager](../azure-resource-manager/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Authorization/elevateAccess/action | 授與呼叫者租用戶範圍的使用者存取管理員存取 |
> | Microsoft.Authorization/classicAdministrators/read | 讀取訂用帳戶的系統管理員。 在自訂角色中當做 NotAction 使用時，不會有作用。 |
> | Microsoft.Authorization/classicAdministrators/write | 對訂用帳戶新增或修改系統管理員。 |
> | Microsoft.Authorization/classicAdministrators/delete | 從訂用帳戶中移除系統管理員。 |
> | Microsoft.Authorization/classicAdministrators/operationstatuses/read | 取得訂用帳戶的系統管理員作業狀態。 |
> | Microsoft.Authorization/denyAssignments/read | 取得拒絕指派的資訊。 |
> | Microsoft.Authorization/denyAssignments/write | 在指定範圍建立拒絕指派。 |
> | Microsoft.Authorization/denyAssignments/delete | 在指定範圍刪除拒絕指派。 |
> | Microsoft.Authorization/locks/read | 取得指定範圍的鎖定。 |
> | Microsoft.Authorization/locks/write | 新增指定範圍的鎖定。 |
> | Microsoft.Authorization/locks/delete | 刪除指定範圍的鎖定。 |
> | Microsoft.Authorization/operations/read | 取得作業的清單 |
> | Microsoft.Authorization/permissions/read | 列出呼叫者在給定範圍內所具有的所有權限。 |
> | Microsoft. 授權/原則/審核/動作 | 以「audit」效果評估 Azure 原則的結果所採取的動作 |
> | Microsoft. 授權/原則/auditIfNotExists/動作 | 因為 ' auditIfNotExists ' 效果的 Azure 原則評估結果而採取的動作 |
> | Microsoft. 授權/原則/拒絕/動作 | 由於「拒絕」效果的 Azure 原則評估而採取的動作 |
> | Microsoft. 授權/原則/deployIfNotExists/動作 | 因為 ' deployIfNotExists ' 效果的 Azure 原則評估結果而採取的動作 |
> | Microsoft.Authorization/policyAssignments/read | 取得關於原則指派的資訊。 |
> | Microsoft.Authorization/policyAssignments/write | 建立指定範圍的原則指派。 |
> | Microsoft.Authorization/policyAssignments/delete | 刪除指定範圍的原則指派。 |
> | Microsoft. 授權/policyAssignments/豁免/動作 | 豁免指定範圍的原則指派。 |
> | Microsoft. Authorization/policyAssignments/privateLinkAssociations/read | 取得私人連結關聯的相關資訊。 |
> | Microsoft. Authorization/policyAssignments/privateLinkAssociations/write | 建立或更新私人連結關聯。 |
> | Microsoft. Authorization/policyAssignments/privateLinkAssociations/delete | 刪除私人連結關聯。 |
> | Microsoft. Authorization/policyAssignments/resourceManagementPrivateLinks/read | 取得資源管理私用連結的相關資訊。 |
> | Microsoft. Authorization/policyAssignments/resourceManagementPrivateLinks/write | 建立或更新資源管理私用連結。 |
> | Microsoft. Authorization/policyAssignments/resourceManagementPrivateLinks/delete | 刪除資源管理私用連結。 |
> | Microsoft. Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnectionProxies/read | 取得私人端點連線 proxy 的相關資訊。 |
> | Microsoft. Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnectionProxies/write | 建立或更新私人端點連線 proxy。 |
> | Microsoft. Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnectionProxies/delete | 刪除私人端點連線 proxy。 |
> | Microsoft. Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnectionProxies/validate/action | 驗證私人端點連線 proxy。 |
> | Microsoft. Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnections/read | 取得私人端點連接的相關資訊。 |
> | Microsoft. Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnections/write | 建立或更新私人端點連接。 |
> | Microsoft. Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnections/delete | 刪除私人端點連接。 |
> | Microsoft.Authorization/policyDefinitions/read | 取得關於原則定義的資訊。 |
> | Microsoft.Authorization/policyDefinitions/write | 建立自訂的原則定義。 |
> | Microsoft.Authorization/policyDefinitions/delete | 刪除原則定義。 |
> | Microsoft. 授權/policyExemptions/讀取 | 取得原則豁免的相關資訊。 |
> | Microsoft. Authorization/policyExemptions/write | 在指定的範圍內建立原則豁免。 |
> | Microsoft. 授權/policyExemptions/刪除 | 刪除指定範圍的原則豁免。 |
> | Microsoft.Authorization/policySetDefinitions/read | 取得原則集合定義的相關資訊。 |
> | Microsoft.Authorization/policySetDefinitions/write | 建立自訂原則集合定義。 |
> | Microsoft.Authorization/policySetDefinitions/delete | 刪除原則集合定義。 |
> | Microsoft.Authorization/providerOperations/read | 針對所有資源提供者取得可在角色定義中使用的作業。 |
> | Microsoft.Authorization/roleAssignments/read | 取得關於角色指派的資訊。 |
> | Microsoft.Authorization/roleAssignments/write | 建立指定範圍的角色指派。 |
> | Microsoft.Authorization/roleAssignments/delete | 刪除指定範圍內的角色指派。 |
> | Microsoft.Authorization/roleDefinitions/read | 取得關於角色定義的資訊。 |
> | Microsoft.Authorization/roleDefinitions/write | 以指定權限和可指派的範圍來建立或更新自訂角色定義。 |
> | Microsoft.Authorization/roleDefinitions/delete | 刪除指定的自訂角色定義。 |

### <a name="microsoftautomation"></a>Microsoft.Automation

Azure 服務： [自動化](../automation/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Automation/register/action | 對 Azure 自動化註冊訂用帳戶 |
> | Microsoft.Automation/automationAccounts/webhooks/action | 產生 Azure 自動化 Webhook 的 URI |
> | Microsoft.Automation/automationAccounts/read | 取得 Azure 自動化帳戶 |
> | Microsoft.Automation/automationAccounts/write | 建立或更新 Azure 自動化帳戶 |
> | Microsoft.Automation/automationAccounts/listKeys/action | 讀取自動化帳戶的金鑰 |
> | Microsoft.Automation/automationAccounts/delete | 刪除 Azure 自動化帳戶 |
> | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | 讀取 Azure Automation DSC 的註冊資訊 |
> | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | 寫入要求以重新產生 Azure Automation DSC 金鑰 |
> | Microsoft.Automation/automationAccounts/certificates/getCount/action | 讀取憑證的計數 |
> | Microsoft.Automation/automationAccounts/certificates/read | 取得 Azure 自動化憑證資產 |
> | Microsoft.Automation/automationAccounts/certificates/write | 建立或更新 Azure 自動化憑證資產 |
> | Microsoft.Automation/automationAccounts/certificates/delete | 刪除 Azure 自動化憑證資產 |
> | Microsoft.Automation/automationAccounts/compilationjobs/write | 寫入 Azure Automation DSC 的編譯 |
> | Microsoft.Automation/automationAccounts/compilationjobs/read | 讀取 Azure Automation DSC 的編譯 |
> | Microsoft.Automation/automationAccounts/compilationjobs/write | 寫入 Azure Automation DSC 的編譯 |
> | Microsoft.Automation/automationAccounts/compilationjobs/read | 讀取 Azure Automation DSC 的編譯 |
> | Microsoft.Automation/automationAccounts/configurations/read | 取得 Azure Automation DSC 的內容 |
> | Microsoft.Automation/automationAccounts/configurations/getCount/action | 讀取 Azure Automation DSC 內容的計數 |
> | Microsoft.Automation/automationAccounts/configurations/write | 寫入 Azure Automation DSC 的內容 |
> | Microsoft.Automation/automationAccounts/configurations/delete | 刪除 Azure Automation DSC 的內容 |
> | Microsoft.Automation/automationAccounts/configurations/content/read | 讀取組態媒體內容 |
> | Microsoft.Automation/automationAccounts/connections/read | 取得 Azure 自動化連線資產 |
> | Microsoft.Automation/automationAccounts/connections/getCount/action | 讀取連線的計數 |
> | Microsoft.Automation/automationAccounts/connections/write | 建立或更新 Azure 自動化連線資產 |
> | Microsoft.Automation/automationAccounts/connections/delete | 刪除 Azure 自動化連線資產 |
> | Microsoft.Automation/automationAccounts/connectionTypes/read | 取得 Azure 自動化連線類型資產 |
> | Microsoft.Automation/automationAccounts/connectionTypes/write | 建立 Azure 自動化連線類型資產 |
> | Microsoft.Automation/automationAccounts/connectionTypes/delete | 刪除 Azure 自動化連線類型資產 |
> | Microsoft.Automation/automationAccounts/credentials/read | 取得 Azure 自動化認證資產 |
> | Microsoft.Automation/automationAccounts/credentials/getCount/action | 讀取認證的計數 |
> | Microsoft.Automation/automationAccounts/credentials/write | 建立或更新 Azure 自動化認證資產 |
> | Microsoft.Automation/automationAccounts/credentials/delete | 刪除 Azure 自動化認證資產 |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | 讀取混合式 Runbook 背景工作角色資源 |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | 刪除混合式 Runbook 背景工作角色資源 |
> | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | 在作業執行時取得 Azure 自動化 Runbook 的內容 |
> | Microsoft.Automation/automationAccounts/jobs/read | 取得 Azure 自動化作業 |
> | Microsoft.Automation/automationAccounts/jobs/write | 建立 Azure 自動化作業 |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | 停止 Azure 自動化作業 |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | 暫止 Azure 自動化作業 |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | 繼續 Azure 自動化作業 |
> | Microsoft.Automation/automationAccounts/jobs/output/read | 取得作業的輸出 |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | 取得 Azure 自動化作業串流 |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | 取得 Azure 自動化作業串流 |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | 取得 Azure 自動化作業排程 |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | 建立 Azure 自動化作業排程 |
> | Microsoft.Automation/automationAccounts/jobSchedules/delete | 刪除 Azure 自動化作業排程 |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | 取得連結至自動化帳戶的工作區 |
> | Microsoft.Automation/automationAccounts/modules/read | 取得 Azure 自動化 Powershell 模組 |
> | Microsoft.Automation/automationAccounts/modules/getCount/action | 取得自動化帳戶內的 Powershell 模組計數 |
> | Microsoft.Automation/automationAccounts/modules/write | 建立或更新 Azure 自動化 Powershell 模組 |
> | Microsoft.Automation/automationAccounts/modules/delete | 刪除 Azure 自動化 Powershell 模組 |
> | Microsoft.Automation/automationAccounts/modules/activities/read | 取得 Azure 自動化活動 |
> | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | 讀取 Azure Automation DSC 的節點設定內容 |
> | Microsoft.Automation/automationAccounts/nodeConfigurations/read | 讀取 Azure Automation DSC 的節點設定 |
> | Microsoft.Automation/automationAccounts/nodeConfigurations/write | 寫入 Azure Automation DSC 的節點設定 |
> | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | 刪除 Azure Automation DSC 的節點設定 |
> | Microsoft.Automation/automationAccounts/nodecounts/read | 讀取指定類型的節點計數摘要 |
> | Microsoft.Automation/automationAccounts/nodes/read | 讀取 Azure Automation DSC 節點 |
> | Microsoft.Automation/automationAccounts/nodes/write | 建立或更新 Azure Automation DSC 節點 |
> | Microsoft.Automation/automationAccounts/nodes/delete | 刪除 Azure Automation DSC 節點 |
> | Microsoft.Automation/automationAccounts/nodes/reports/read | 讀取 Azure Automation DSC 報告 |
> | Microsoft.Automation/automationAccounts/nodes/reports/content/read | 讀取 Azure Automation DSC 報告內容 |
> | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | 取得 Azure 自動化 TypeField |
> | Microsoft. Automation/automationAccounts/privateEndpointConnectionProxies/read | 讀取 Azure 自動化私人端點連接 Proxy |
> | Microsoft. Automation/automationAccounts/privateEndpointConnectionProxies/write | 建立 Azure 自動化私人端點連接 Proxy |
> | Microsoft. Automation/automationAccounts/privateEndpointConnectionProxies/validate/action | 驗證私人端點連接要求 (groupId 驗證)  |
> | Microsoft. Automation/automationAccounts/privateEndpointConnectionProxies/delete | 刪除 Azure 自動化私人端點連接 Proxy |
> | Microsoft. Automation/automationAccounts/privateEndpointConnectionProxies/operationResults/read | 取得 Azure 自動化私人端點 proxy 操作結果。 |
> | Microsoft. Automation/automationAccounts/privateEndpointConnections/read | 取得 Azure 自動化私人端點連接狀態 |
> | Microsoft. Automation/automationAccounts/privateEndpointConnections/write | 核准或拒絕 Azure 自動化私人端點連接 |
> | Microsoft. Automation/automationAccounts/privateLinkResources/read | 讀取私人端點的群組資訊 |
> | Microsoft.Automation/automationAccounts/python2Packages/read | 取得 Azure 自動化 Python 2 套件 |
> | Microsoft.Automation/automationAccounts/python2Packages/write | 建立或更新 Azure 自動化 Python 2 套件 |
> | Microsoft.Automation/automationAccounts/python2Packages/delete | 刪除 Azure 自動化 Python 2 套件 |
> | Microsoft.Automation/automationAccounts/python3Packages/read | 取得 Azure 自動化 Python 3 套件 |
> | Microsoft.Automation/automationAccounts/python3Packages/write | 建立或更新 Azure 自動化 Python 3 套件 |
> | Microsoft.Automation/automationAccounts/python3Packages/delete | 刪除 Azure 自動化 Python 3 套件 |
> | Microsoft.Automation/automationAccounts/runbooks/read | 取得 Azure 自動化 Runbook |
> | Microsoft.Automation/automationAccounts/runbooks/getCount/action | 取得 Azure 自動化 Runbook 的計數 |
> | Microsoft.Automation/automationAccounts/runbooks/write | 建立或更新 Azure 自動化 Runbook |
> | Microsoft.Automation/automationAccounts/runbooks/delete | 刪除 Azure 自動化 Runbook |
> | Microsoft.Automation/automationAccounts/runbooks/publish/action | 發佈 Azure 自動化 Runbook 草稿 |
> | Microsoft.Automation/automationAccounts/runbooks/content/read | 取得 Azure 自動化 Runbook 的內容 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/read | 取得 Azure 自動化 Runbook 草稿 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | 復原對 Azure 自動化 Runbook 草稿所做的編輯 |
> | Microsoft. Automation/automationAccounts/runbook/draft/write | 建立 runbook 草稿 Azure 自動化 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | 建立 Azure 自動化 Runbook 草稿的內容 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | 取得 Azure 自動化 Runbook 草稿作業結果 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | 取得 Azure 自動化 Runbook 草稿測試作業 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | 建立 Azure 自動化 Runbook 草稿測試作業 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | 停止 Azure 自動化 Runbook 草稿測試作業 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | 暫止 Azure 自動化 Runbook 草稿測試作業 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | 繼續 Azure 自動化 Runbook 草稿測試作業 |
> | Microsoft.Automation/automationAccounts/schedules/read | 取得 Azure 自動化排程資產 |
> | Microsoft.Automation/automationAccounts/schedules/getCount/action | 取得 Azure 自動化排程的計數 |
> | Microsoft.Automation/automationAccounts/schedules/write | 建立或更新 Azure 自動化排程資產 |
> | Microsoft.Automation/automationAccounts/schedules/delete | 刪除 Azure 自動化排程資產 |
> | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/read | 取得 Azure 自動化的軟體更新設定電腦執行 |
> | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationRuns/read | 取得 Azure 自動化的軟體更新設定執行 |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | 建立或更新 Azure 自動化軟體更新設定 |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | 取得 Azure 自動化軟體更新設定 |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | 刪除 Azure 自動化軟體更新設定 |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | 建立或更新 Azure 自動化軟體更新設定 |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | 取得 Azure 自動化軟體更新設定 |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | 刪除 Azure 自動化軟體更新設定 |
> | Microsoft.Automation/automationAccounts/statistics/read | 取得 Azure 自動化統計資料 |
> | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | 取得 Azure 自動化更新部署機器 |
> | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | 取得 Azure 自動化更新管理修補作業 |
> | Microsoft.Automation/automationAccounts/usages/read | 取得 Azure 自動化使用方式 |
> | Microsoft.Automation/automationAccounts/variables/read | 讀取 Azure 自動化變數資產 |
> | Microsoft.Automation/automationAccounts/variables/write | 建立或更新 Azure 自動化變數資產 |
> | Microsoft.Automation/automationAccounts/variables/delete | 刪除 Azure 自動化變數資產 |
> | Microsoft.Automation/automationAccounts/watchers/write | 建立 Azure 自動化監看員作業 |
> | Microsoft.Automation/automationAccounts/watchers/read | 取得 Azure 自動化監看員作業 |
> | Microsoft.Automation/automationAccounts/watchers/delete | 刪除 Azure 自動化監看員作業 |
> | Microsoft.Automation/automationAccounts/watchers/start/action | 啟動 Azure 自動化監看員作業 |
> | Microsoft.Automation/automationAccounts/watchers/stop/action | 停止 Azure 自動化監看員作業 |
> | Microsoft.Automation/automationAccounts/watchers/streams/read | 取得 Azure 自動化監看員作業串流 |
> | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | 建立 Azure 自動化監看員作業動作 |
> | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | 取得 Azure 自動化監看員作業動作 |
> | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | 刪除 Azure 自動化監看員作業動作 |
> | Microsoft.Automation/automationAccounts/webhooks/read | 讀取 Azure 自動化 Webhook |
> | Microsoft.Automation/automationAccounts/webhooks/write | 建立或更新 Azure 自動化 Webhook |
> | Microsoft.Automation/automationAccounts/webhooks/delete | 刪除 Azure 自動化 Webhook  |
> | Microsoft.Automation/operations/read | 取得 Azure 自動化資源的可用作業 |

### <a name="microsoftbatch"></a>Microsoft.Batch

Azure 服務： [批次](../batch/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Batch/register/action | 針對 Batch 資源提供者註冊訂用帳戶，並讓您能夠建立 Batch 帳戶 |
> | Microsoft.Batch/unregister/action | 為 Batch 資源提供者取消註冊訂用帳戶，以防止建立 Batch 帳戶 |
> | Microsoft.Batch/batchAccounts/read | 列出 Batch 帳戶，或取得 Batch 帳戶的屬性 |
> | Microsoft.Batch/batchAccounts/write | 建立新的 Batch 帳戶，或更新現有的 Batch 帳戶 |
> | Microsoft.Batch/batchAccounts/delete | 刪除 Batch 帳戶 |
> | Microsoft.Batch/batchAccounts/listkeys/action | 列出 Batch 帳戶的存取金鑰 |
> | Microsoft.Batch/batchAccounts/regeneratekeys/action | 重新產生 Batch 帳戶的存取金鑰 |
> | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | 針對為 Batch 帳戶所設定的自動儲存體帳戶同步處理存取金鑰 |
> | Microsoft.Batch/batchAccounts/applications/read | 列出應用程式，或取得應用程式的屬性 |
> | Microsoft.Batch/batchAccounts/applications/write | 建立新的應用程式，或更新現有應用程式 |
> | Microsoft.Batch/batchAccounts/applications/delete | 刪除應用程式 |
> | Microsoft.Batch/batchAccounts/applications/versions/read | 取得應用程式套件的屬性 |
> | Microsoft.Batch/batchAccounts/applications/versions/write | 建立新的應用程式套件，或更新現有的應用程式套件 |
> | Microsoft.Batch/batchAccounts/applications/versions/delete | 刪除應用程式套件 |
> | Microsoft.Batch/batchAccounts/applications/versions/activate/action | 啟動應用程式套件 |
> | Microsoft.Batch/batchAccounts/certificateOperationResults/read | 取得 Batch 帳戶上長時間執行憑證作業的結果 |
> | Microsoft.Batch/batchAccounts/certificates/read | 列出批 Batch 帳戶上的憑證，或取得憑證的屬性 |
> | Microsoft.Batch/batchAccounts/certificates/write | 在 Batch 帳戶上建立新的憑證，或更新現有憑證 |
> | Microsoft.Batch/batchAccounts/certificates/delete | 從 Batch 帳戶刪除憑證 |
> | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | 取消 Batch 帳戶上無法刪除憑證的作業 |
> | Microsoft.Batch/batchAccounts/operationResults/read | 取得長時間執行 Batch 帳戶作業的結果 |
> | Microsoft.Batch/batchAccounts/poolOperationResults/read | 取得 Batch 帳戶上長時間執行集區作業的結果 |
> | Microsoft.Batch/batchAccounts/pools/read | 列出 Batch 帳戶上的集區，或取得集區的屬性 |
> | Microsoft.Batch/batchAccounts/pools/write | 在 Batch 帳戶上建立新的集區，或更新現有的集區 |
> | Microsoft.Batch/batchAccounts/pools/delete | 從 Batch 帳戶刪除集區 |
> | Microsoft.Batch/batchAccounts/pools/stopResize/action | 停止 Batch 帳戶集區上正在進行的調整大小作業 |
> | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | 停用 Batch 帳戶集區的自動縮放比例 |
> | Microsoft.Batch/batchAccounts/privateEndpointConnectionResults/read | 取得長時間執行的 Batch 帳戶私人端點連接作業的結果 |
> | Microsoft.Batch/batchAccounts/privateEndpointConnections/write | 更新 Batch 帳戶上現有的私人端點連線 |
> | Microsoft.Batch/batchAccounts/privateEndpointConnections/read | 取得私人端點連線或列出 Batch 帳戶上的私人端點連接 |
> | Microsoft.Batch/batchAccounts/privateLinkResources/read | 取得私人連結資源的屬性，或列出 Batch 帳戶上的 Private link 資源 |
> | Microsoft.Batch/locations/checkNameAvailability/action | 檢查帳戶名稱有效，且並非使用中。 |
> | Microsoft.Batch/locations/accountOperationResults/read | 取得長時間執行 Batch 帳戶作業的結果 |
> | Microsoft.Batch/locations/quotas/read | 取得指定訂用帳戶在指定 Azure 區域內的 Batch 配額 |
> | Microsoft.Batch/operations/read | 列出可對 Microsoft.Batch 資源提供者進行的作業 |
> | **DataAction** | **說明** |
> | Microsoft.Batch/batchAccounts/jobs/read | 列出 Batch 帳戶的作業或取得作業的屬性 |
> | Microsoft.Batch/batchAccounts/jobs/write | 在 Batch 帳戶上建立新的作業，或更新現有的作業 |
> | Microsoft.Batch/batchAccounts/jobs/delete | 從 Batch 帳戶刪除作業 |
> | Microsoft.Batch/batchAccounts/jobSchedules/read | 列出 Batch 帳戶上的作業排程，或取得作業排程的屬性 |
> | Microsoft.Batch/batchAccounts/jobSchedules/write | 在 Batch 帳戶上建立新的作業排程，或更新現有的作業排程 |
> | Microsoft.Batch/batchAccounts/jobSchedules/delete | 從 Batch 帳戶刪除作業排程 |

### <a name="microsoftbilling"></a>Microsoft.Billing

Azure 服務： [成本管理 + 計費](../cost-management-billing/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft 帳單/validateAddress/action |  |
> | Microsoft.Billing/register/action |  |
> | Microsoft.Billing/billingAccounts/read |  |
> | Microsoft. 帳單/billingAccounts/listInvoiceSectionsWithCreateSubscriptionPermission/action |  |
> | Microsoft. 帳單/billingAccounts/write |  |
> | Microsoft. 帳單/billingAccounts/合約/讀取 |  |
> | Microsoft. 帳單/billingAccounts/billingPermissions/read |  |
> | Microsoft. 帳單/billingAccounts/billingProfiles/read |  |
> | Microsoft. 帳單/billingAccounts/billingProfiles/write |  |
> | Microsoft. 帳單/billingAccounts/billingProfiles/billingPermissions/read |  |
> | Microsoft. 帳單/billingAccounts/billingProfiles/customers/read |  |
> | Microsoft. 帳單/billingAccounts/billingProfiles/invoiceSections/read |  |
> | Microsoft. 計費/billingAccounts/billingProfiles/invoiceSections/write |  |
> | Microsoft. 帳單/billingAccounts/billingProfiles/invoiceSections/billingPermissions/read |  |
> | Microsoft. 帳單/billingAccounts/billingProfiles/pricesheet/下載/動作 |  |
> | Microsoft. 帳單/billingAccounts/billingRoleAssignments/write |  |
> | Microsoft. 帳單/billingAccounts/billingSubscriptions/read |  |
> | Microsoft. 計費/billingAccounts/billingSubscriptions/移動/動作 |  |
> | Microsoft. 計費/billingAccounts/billingSubscriptions/validateMoveEligibility/action |  |
> | Microsoft. 帳單/billingAccounts/customers/read |  |
> | Microsoft. 帳單/billingAccounts/customers/billingPermissions/read |  |
> | Microsoft.Billing/billingAccounts/departments/read |  |
> | Microsoft. 帳單/billingAccounts/部門/billingPermissions/讀取 |  |
> | Microsoft. 計費/billingAccounts/部門/billingRoleAssignments/寫入 |  |
> | Microsoft.Billing/billingAccounts/enrollmentAccounts/read |  |
> | Microsoft. 帳單/billingAccounts/enrollmentAccounts/billingPermissions/read |  |
> | Microsoft. 計費/billingAccounts/enrollmentAccounts/billingRoleAssignments/write |  |
> | Microsoft. 帳單/billingAccounts/發票/pricesheet/下載/動作 |  |
> | Microsoft. 帳單/billingAccounts/產品/讀取 |  |
> | Microsoft 帳單/billingAccounts/products/move/action |  |
> | Microsoft. 帳單/billingAccounts/products/validateMoveEligibility/action |  |
> | Microsoft.Billing/departments/read |  |
> | Microsoft. 帳單/發票/下載/動作 | 使用清單中的下載連結來下載發票 |
> | Microsoft. 帳單/發票/下載/動作 | 使用清單中的下載連結來下載發票 |
> | Microsoft. 計費/作業/讀取 |  |

### <a name="microsoftblueprint"></a>Microsoft.Blueprint

Azure 服務： [Azure 藍圖](../governance/blueprints/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Blueprint/register/action | 註冊 Azure 藍圖資源提供者 |
> | Microsoft.Blueprint/blueprintAssignments/read | 讀取任何藍圖成品 |
> | Microsoft.Blueprint/blueprintAssignments/write | 建立或更新任何藍圖成品 |
> | Microsoft.Blueprint/blueprintAssignments/delete | 刪除任何藍圖成品 |
> | Microsoft. 藍圖/blueprintAssignments/Whoisblueprint/action | 取得 Azure 藍圖服務主體物件識別碼。 |
> | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | 讀取任何藍圖成品 |
> | Microsoft.Blueprint/blueprints/read | 讀取任何藍圖 |
> | Microsoft.Blueprint/blueprints/write | 建立或更新任何藍圖 |
> | Microsoft.Blueprint/blueprints/delete | 刪除任何藍圖 |
> | Microsoft.Blueprint/blueprints/artifacts/read | 讀取任何藍圖成品 |
> | Microsoft.Blueprint/blueprints/artifacts/write | 建立或更新任何藍圖成品 |
> | Microsoft.Blueprint/blueprints/artifacts/delete | 刪除任何藍圖成品 |
> | Microsoft.Blueprint/blueprints/versions/read | 讀取任何藍圖 |
> | Microsoft.Blueprint/blueprints/versions/write | 建立或更新任何藍圖 |
> | Microsoft.Blueprint/blueprints/versions/delete | 刪除任何藍圖 |
> | Microsoft.Blueprint/blueprints/versions/artifacts/read | 讀取任何藍圖成品 |

### <a name="microsoftcapacity"></a>Microsoft.Capacity

Azure 服務：核心

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Capacity/calculateprice/action | 計算任何保留價格 |
> | Microsoft.Capacity/checkoffers/action | 檢查任何訂用帳戶供應項目 |
> | Microsoft.Capacity/checkscopes/action | 檢查任何訂用帳戶 |
> | Microsoft.Capacity/validatereservationorder/action | 驗證任何保留項目 |
> | Microsoft.Capacity/reservationorders/action | 更新任何保留項目 |
> | Microsoft.Capacity/register/action | 註冊容量資源提供者，並讓您能夠建立容量資源。 |
> | Microsoft.Capacity/unregister/action | 取消註冊任何租用戶 |
> | Microsoft. 容量/calculateexchange/action | 計算新購買的交換金額和價格，並傳回原則錯誤。 |
> | Microsoft. 容量/exchange/動作 | 交換任何保留 |
> | Microsoft.Capacity/appliedreservations/read | 讀取所有保留項目 |
> | Microsoft.Capacity/catalogs/read | 讀取保留目錄 |
> | Microsoft.Capacity/commercialreservationorders/read | 取得在任何租用戶中建立的保留順序 |
> | Microsoft.Capacity/operations/read | 讀取任何作業 |
> | Microsoft.Capacity/reservationorders/availablescopes/action | 尋找任何可用的範圍 |
> | Microsoft.Capacity/reservationorders/read | 讀取所有保留項目 |
> | Microsoft.Capacity/reservationorders/write | 建立任何保留項目 |
> | Microsoft.Capacity/reservationorders/delete | 刪除任何保留項目 |
> | Microsoft.Capacity/reservationorders/reservations/action | 更新任何保留項目 |
> | Microsoft.Capacity/reservationorders/return/action | 傳回任何保留項目 |
> | Microsoft.Capacity/reservationorders/swap/action | 交換任何保留項目 |
> | Microsoft.Capacity/reservationorders/split/action | 分割任何保留項目 |
> | Microsoft.Capacity/reservationorders/merge/action | 合併任何保留項目 |
> | Microsoft. 容量/reservationorders/calculaterefund/action | 計算新購買的退款金額和價格，並傳回原則錯誤。 |
> | Microsoft. 容量/reservationorders/mergeoperationresults/read | 輪詢任何合併作業 |
> | Microsoft. 容量/reservationorders/保留/availablescopes/動作 | 尋找任何可用的範圍 |
> | Microsoft.Capacity/reservationorders/reservations/read | 讀取所有保留項目 |
> | Microsoft.Capacity/reservationorders/reservations/write | 建立任何保留項目 |
> | Microsoft.Capacity/reservationorders/reservations/delete | 刪除任何保留項目 |
> | Microsoft. 容量/reservationorders/保留/封存/動作 | 封存處於終端機狀態的保留，例如已過期、已分割等等。 |
> | Microsoft. 容量/reservationorders/保留/取消封存/動作 | 取消封存先前封存的保留 |
> | Microsoft.Capacity/reservationorders/reservations/revisions/read | 讀取所有保留項目 |
> | Microsoft. 容量/reservationorders/splitoperationresults/read | 輪詢任何分割作業 |
> | Microsoft.Capacity/tenants/register/action | 註冊任何租用戶 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

Azure 服務：核心

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft Commerce/報名/動作 | 註冊 Microsoft Commerce UsageAggregate 訂用帳戶 |
> | Microsoft Commerce/取消註冊/動作 | 取消註冊 Microsoft Commerce UsageAggregate 的訂用帳戶 |
> | Microsoft.Commerce/RateCard/read | 傳回給定訂用帳戶的供應項目資料、資源/計量中繼資料和速率。 |
> | Microsoft.Commerce/UsageAggregates/read | 抓取訂用帳戶 Microsoft Azure 的耗用量。 結果會包含特定時間範圍內的彙總使用量資料、訂用帳戶和資源相關資訊。 |

### <a name="microsoftconsumption"></a>Microsoft.Consumption

Azure 服務： [成本管理](../cost-management-billing/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Consumption/register/action | 向 Consumption RP 註冊 |
> | Microsoft. 耗用量/aggregatedcost/讀取 | 列出管理群組的 AggregatedCost。 |
> | Microsoft.Consumption/balances/read | 針對管理群組列出計費期的使用率摘要。 |
> | Microsoft.Consumption/budgets/read | 依訂用帳戶或管理群組列出預算。 |
> | Microsoft.Consumption/budgets/write | 依訂用帳戶或管理群組建立和更新預算。 |
> | Microsoft.Consumption/budgets/delete | 依訂用帳戶或管理群組刪除預算。 |
> | Microsoft.Consumption/charges/read | 列出費用 |
> | Microsoft.Consumption/credits/read | 列出信用額度 |
> | Microsoft.Consumption/events/read | 列出事件 |
> | Microsoft. 耗用量/externalBillingAccounts/標記/讀取 | 列出 EA 和訂用帳戶的標記。 |
> | Microsoft. 耗用量/externalSubscriptions/標記/讀取 | 列出 EA 和訂用帳戶的標記。 |
> | Microsoft.Consumption/forecasts/read | 列出預測 |
> | Microsoft.Consumption/lots/read | 列出位置 |
> | Microsoft.Consumption/marketplaces/read | 針對適用於 EA 與 WebDirect 訂用帳戶的範圍列出 Marketplace 資源使用狀況詳細資料。 |
> | Microsoft.Consumption/operationresults/read | 列出作業結果 |
> | Microsoft.Consumption/operations/read | 列出 Microsoft.Consumption 資源提供者支援的所有作業。 |
> | Microsoft.Consumption/operationstatus/read | 列出作業狀態 |
> | Microsoft.Consumption/pricesheets/read | 列出適用於訂用帳戶或管理群組的價位表資料。 |
> | Microsoft.Consumption/reservationDetails/read | 依保留順序或管理群組列出保留執行個體的使用率詳細資料。 詳細資料屬於每日層級的每個執行個體。 |
> | Microsoft.Consumption/reservationRecommendations/read | 列出訂用帳戶保留執行個體的單一或共用建議。 |
> | Microsoft.Consumption/reservationSummaries/read | 依保留順序或管理群組列出保留執行個體的使用率摘要。 摘要資料位於每月或每日層級。 |
> | Microsoft.Consumption/reservationTransactions/read | 依管理群組列出保留執行個體的交易記錄。 |
> | Microsoft.Consumption/tags/read | 列出 EA 和訂用帳戶的標記。 |
> | Microsoft.Consumption/tenants/register/action | 依某個租用戶註冊 Microsoft.Consumption 範圍的動作。 |
> | Microsoft. 耗用量/租使用者/讀取 | 列出租使用者 |
> | Microsoft.Consumption/terms/read | 列出適用於訂用帳戶或管理群組的條款。 |
> | Microsoft.Consumption/usageDetails/read | 針對適用於 EA 與 WebDirect 訂用帳戶的範圍列出使用狀況詳細資料。 |

### <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

Azure 服務： [成本管理](../cost-management-billing/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.CostManagement/query/action | 依範圍查使用狀況資料。 |
> | Microsoft.CostManagement/reports/action | 依範圍排定使用狀況資料報告。 |
> | Microsoft.CostManagement/exports/action | 執行指定的匯出。 |
> | CostManagement/註冊/動作 | 依訂用帳戶註冊 CostManagement 範圍的動作。 |
> | CostManagement/views/action | 建立 view。 |
> | CostManagement/預測/動作 | 依範圍預測使用量資料。 |
> | CostManagement/alerts/write | 更新警示。 |
> | CostManagement/alerts/read | 列出警示。 |
> | CostManagement/預算/讀取 | 依訂用帳戶或管理群組列出預算。 |
> | CostManagement/cloudConnectors/read | 列出已驗證使用者的 cloudConnectors。 |
> | CostManagement/cloudConnectors/write | 建立或更新指定的 cloudConnector。 |
> | CostManagement/cloudConnectors/delete | 刪除指定的 cloudConnector。 |
> | Microsoft.CostManagement/dimensions/read | 依範圍列出所有支援的維度。 |
> | Microsoft.CostManagement/exports/read | 依範圍列出匯出。 |
> | Microsoft.CostManagement/exports/write | 建立或更新指定的匯出。 |
> | Microsoft.CostManagement/exports/delete | 刪除指定的匯出。 |
> | CostManagement/匯出/執行/動作 | 執行匯出。 |
> | CostManagement/externalBillingAccounts/read | 列出已驗證使用者的 externalBillingAccounts。 |
> | CostManagement/externalBillingAccounts/query/action | 查詢外部 BillingAccounts 的使用方式資料。 |
> | CostManagement/externalBillingAccounts/預報/動作 | 預測外部 BillingAccounts 的使用量資料。 |
> | CostManagement/externalBillingAccounts/dimension/read | 列出所有支援的外部 BillingAccounts 維度。 |
> | CostManagement/externalBillingAccounts/externalSubscriptions/read | 列出已驗證使用者之 externalBillingAccount 內的 externalSubscriptions。 |
> | CostManagement/externalBillingAccounts/預測/讀取 | 預測外部 BillingAccounts 的使用量資料。 |
> | CostManagement/externalBillingAccounts/query/read | 查詢外部 BillingAccounts 的使用方式資料。 |
> | CostManagement/externalSubscriptions/read | 列出已驗證使用者的 externalSubscriptions。 |
> | CostManagement/externalSubscriptions/write | 更新 externalSubscription 的關聯管理群組 |
> | CostManagement/externalSubscriptions/query/action | 查詢外部訂用帳戶的使用量資料。 |
> | CostManagement/externalSubscriptions/預報/動作 | 預測外部 BillingAccounts 的使用量資料。 |
> | CostManagement/externalSubscriptions/dimension/read | 列出所有支援的外部訂用帳戶維度。 |
> | CostManagement/externalSubscriptions/預測/讀取 | 預測外部 BillingAccounts 的使用量資料。 |
> | CostManagement/externalSubscriptions/query/read | 查詢外部訂用帳戶的使用量資料。 |
> | CostManagement/預測/讀取 | 依範圍預測使用量資料。 |
> | CostManagement/operations/read | 列出 CostManagement 資源提供者所支援的所有作業。 |
> | Microsoft.CostManagement/query/read | 依範圍查使用狀況資料。 |
> | Microsoft.CostManagement/reports/read | 依範圍排定使用狀況資料報告。 |
> | CostManagement/tenant/register/action | 註冊由租使用者 CostManagement 的 Microsoft 範圍動作。 |
> | CostManagement/views/read | 列出所有已儲存的視圖。 |
> | CostManagement/views/delete | 刪除已儲存的視圖。 |
> | CostManagement/views/write | 更新視圖。 |

### <a name="microsoftfeatures"></a>Microsoft.Features

Azure 服務： [Azure Resource Manager](../azure-resource-manager/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Features/register/action | 註冊訂用帳戶的功能。 |
> | Microsoft.Features/features/read | 取得訂用帳戶的功能。 |
> | Microsoft.Features/operations/read | 取得作業的清單。 |
> | Microsoft.Features/providers/features/read | 取得給定資源提供者中某個訂用帳戶的功能。 |
> | Microsoft.Features/providers/features/register/action | 註冊給定資源提供者中某個訂用帳戶的功能。 |
> | Microsoft.Features/providers/features/unregister/action | 在指定的資源提供者內取消註冊訂用帳戶的功能。 |

### <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

Azure 服務： [Azure 原則](../governance/policy/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | >microsoft.guestconfiguration/註冊/動作 | 註冊 >microsoft.guestconfiguration 資源提供者的訂用帳戶。 |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | 取得新的來賓組態指派。 |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | 取得來賓組態指派。 |
> | >microsoft.guestconfiguration/guestConfigurationAssignments/delete | 刪除來賓設定指派。 |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | 取得來賓組態指派報告。 |
> | >microsoft.guestconfiguration/operations/read | 取得 >microsoft.guestconfiguration 資源提供者的作業 |

### <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

Azure 服務： [Azure Arc](../azure-arc/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | HybridCompute/註冊/動作 | 註冊 HybridCompute 資源提供者的訂用帳戶 |
> | HybridCompute/取消註冊/動作 | 取消註冊 HybridCompute 資源提供者的訂用帳戶 |
> | HybridCompute/位置/operationresults/讀取 | 讀取 HybridCompute 資源提供者上的作業狀態 |
> | HybridCompute/位置/>operationstatus/讀取 | 讀取 HybridCompute 資源提供者上的作業狀態 |
> | Microsoft.HybridCompute/machines/read | 讀取任何 Azure Arc 機器 |
> | Microsoft.HybridCompute/machines/write | 寫入 Azure Arc 機器 |
> | Microsoft.HybridCompute/machines/delete | 刪除 Azure Arc 機器 |
> | HybridCompute/電腦/擴充功能/讀取 | 讀取任何 Azure Arc 擴充功能 |
> | Microsoft.HybridCompute/machines/extensions/write | 安裝或更新 Azure Arc 擴充 |
> | HybridCompute/電腦/擴充/刪除 | 刪除 Azure Arc 擴充功能 |
> | HybridCompute/operations/read | 讀取伺服器 Azure Arc 的所有作業 |

### <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

Azure 服務： [Azure Arc 啟用 Kubernetes](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Kubernetes/connectedClusters/Read | 讀取 connectedClusters |
> | Kubernetes/connectedClusters/Write | 寫入 connectedClusters |
> | Kubernetes/connectedClusters/Delete | 刪除 connectedClusters |
> | Kubernetes/connectedClusters/listClusterUserCredentials/action | 列出 clusterUser 認證 |
> | Kubernetes/RegisteredSubscriptions/read | 讀取已註冊的訂閱 |
> | **DataAction** | **說明** |
> | Kubernetes/connectedClusters/admissionregistration. k8s. io/initializerconfigurations/read | 讀取 initializerconfigurations |
> | Kubernetes/connectedClusters/admissionregistration. k8s. io/initializerconfigurations/write | 寫入 initializerconfigurations |
> | Kubernetes/connectedClusters/admissionregistration. k8s. io/initializerconfigurations/delete | 刪除 initializerconfigurations |
> | Kubernetes/connectedClusters/admissionregistration. k8s. io/mutatingwebhookconfigurations/read | 讀取 mutatingwebhookconfigurations |
> | Kubernetes/connectedClusters/admissionregistration. k8s. io/mutatingwebhookconfigurations/write | 寫入 mutatingwebhookconfigurations |
> | Kubernetes/connectedClusters/admissionregistration. k8s. io/mutatingwebhookconfigurations/delete | 刪除 mutatingwebhookconfigurations |
> | Kubernetes/connectedClusters/admissionregistration. k8s. io/validatingwebhookconfigurations/read | 讀取 validatingwebhookconfigurations |
> | Kubernetes/connectedClusters/admissionregistration. k8s. io/validatingwebhookconfigurations/write | 寫入 validatingwebhookconfigurations |
> | Kubernetes/connectedClusters/admissionregistration. k8s. io/validatingwebhookconfigurations/delete | 刪除 validatingwebhookconfigurations |
> | Kubernetes/connectedClusters/api/read | 讀取 api |
> | Kubernetes/connectedClusters/api/v1/read | 讀取 api/v1 |
> | Kubernetes/connectedClusters/apiextensions. k8s. io/customresourcedefinitions/read | 讀取 customresourcedefinitions |
> | Kubernetes/connectedClusters/apiextensions. k8s. io/customresourcedefinitions/write | 寫入 customresourcedefinitions |
> | Kubernetes/connectedClusters/apiextensions. k8s. io/customresourcedefinitions/delete | 刪除 customresourcedefinitions |
> | Kubernetes/connectedClusters/apiregistration. k8s. io/apiservices/read | 讀取 apiservices |
> | Kubernetes/connectedClusters/apiregistration. k8s. io/apiservices/write | 寫入 apiservices |
> | Kubernetes/connectedClusters/apiregistration. k8s. io/apiservices/delete | 刪除 apiservices |
> | Kubernetes/connectedClusters/api/read | 讀取 api |
> | Kubernetes/connectedClusters/api/admissionregistration. k8s. io/read | 讀取 admissionregistration.k8s.io |
> | Kubernetes/connectedClusters/api/admissionregistration. k8s. io/v1/read | 讀取 admissionregistration.k8s.io/v1 |
> | Kubernetes/connectedClusters/api/admissionregistration. k8s. io/v1Beta1/read | 讀取 admissionregistration.k8s.io/v1Beta1 |
> | Kubernetes/connectedClusters/api/apiextensions. k8s. io/read | 讀取 apiextensions.k8s.io |
> | Kubernetes/connectedClusters/api/apiextensions. k8s. io/v1/read | 讀取 apiextensions.k8s.io/v1 |
> | Kubernetes/connectedClusters/api/apiextensions. k8s. io/v1Beta1/read | 讀取 apiextensions.k8s.io/v1Beta1 |
> | Kubernetes/connectedClusters/api/apiregistration. k8s. io/read | 讀取 apiregistration.k8s.io |
> | Kubernetes/connectedClusters/api/apiregistration. k8s. io/v1/read | 讀取 apiregistration.k8s.io/v1 |
> | Kubernetes/connectedClusters/api/apiregistration. k8s. io/v1Beta1/read | 讀取 apiregistration.k8s.io/v1Beta1 |
> | Kubernetes/connectedClusters/api/apps/read | 讀取應用程式 |
> | Kubernetes/connectedClusters/api/apps/v1Beta1/read | 讀取應用程式/v1Beta1 |
> | Kubernetes/connectedClusters/api/apps/v1Beta2/read | 讀取 v1Beta2 |
> | Kubernetes/connectedClusters/api/authentication. k8s. io/read | 讀取 authentication.k8s.io |
> | Kubernetes/connectedClusters/api/authentication. k8s. io/v1/read | 讀取 authentication.k8s.io/v1 |
> | Kubernetes/connectedClusters/api/authentication. k8s. io/v1Beta1/read | 讀取 authentication.k8s.io/v1Beta1 |
> | Kubernetes/connectedClusters/api/authorization. k8s. io/read | 讀取 authorization.k8s.io |
> | Kubernetes/connectedClusters/api/authorization. k8s. io/v1/read | 讀取 authorization.k8s.io/v1 |
> | Kubernetes/connectedClusters/api/authorization. k8s. io/v1Beta1/read | 讀取 authorization.k8s.io/v1Beta1 |
> | Kubernetes/connectedClusters/api/自動調整/讀取 | 讀取自動調整 |
> | Kubernetes/connectedClusters/api/自動調整/v1/read | 讀取自動調整/v1 |
> | Kubernetes/connectedClusters/api/自動調整/v2Beta1/read | 讀取自動調整/v2Beta1 |
> | Kubernetes/connectedClusters/api/自動調整/v2Beta2/read | 讀取自動調整/v2Beta2 |
> | Kubernetes/connectedClusters/api/batch/read | 讀取批次 |
> | Kubernetes/connectedClusters/api/batch/v1/read | 讀取批次/v1 |
> | Kubernetes/connectedClusters/api/batch/v1Beta1/read | 讀取批次/v1Beta1 |
> | Kubernetes/connectedClusters/api/certificate. k8s. io/read | 讀取 certificates.k8s.io |
> | Kubernetes/connectedClusters/api/certificate. k8s. io/v1Beta1/read | 讀取 certificates.k8s.io/v1Beta1 |
> | Kubernetes/connectedClusters/api/協調. k8s. io/read | 讀取 coordination.k8s.io |
> | Kubernetes/connectedClusters/api/協調. k8s. io/v1/read | 讀取協調/v1 |
> | Kubernetes/connectedClusters/api/協調. k8s. io/v1Beta1/read | 讀取 coordination.k8s.io/v1Beta1 |
> | Kubernetes/connectedClusters/api/events. k8s. io/read | 讀取 events.k8s.io |
> | Kubernetes/connectedClusters/api/events. k8s. io/v1Beta1/read | 讀取 events.k8s.io/v1Beta1 |
> | Kubernetes/connectedClusters/api/extensions/read | 讀取延伸模組 |
> | Kubernetes/connectedClusters/api/extensions/v1Beta1/read | 讀取延伸模組/v1Beta1 |
> | Kubernetes/connectedClusters/api/k8s. io/read | 讀取 metrics.k8s.io |
> | Kubernetes/connectedClusters/api/計量. k8s. io/v1Beta1/read | 讀取 metrics.k8s.io/v1Beta1 |
> | Kubernetes/connectedClusters/api/網路. k8s. io/read | 讀取 networking.k8s.io |
> | Kubernetes/connectedClusters/api/網路。 k8s. io/v1/read | 讀取網路/v1 |
> | Kubernetes/connectedClusters/api/網路。 k8s. io/v1Beta1/read | 讀取 networking.k8s.io/v1Beta1 |
> | Kubernetes/connectedClusters/api/k8s. io/read | 讀取 node.k8s.io |
> | Kubernetes/connectedClusters/api/node. k8s. io/v1Beta1/read | 讀取 node.k8s.io/v1Beta1 |
> | Kubernetes/connectedClusters/api/policy/read | 讀取原則 |
> | Kubernetes/connectedClusters/api/policy/v1Beta1/read | 讀取原則/v1Beta1 |
> | Kubernetes/connectedClusters/api/rbac. k8s. io/read | 讀取 rbac.authorization.k8s.io |
> | Kubernetes/connectedClusters/api/rbac. k8s.. io/v1/read | 讀取 rbac. 授權/v1 |
> | Kubernetes/connectedClusters/api/rbac. k8s.. io/v1Beta1/read | 讀取 rbac.authorization.k8s.io/v1Beta1 |
> | Kubernetes/connectedClusters/api/k8s. io/read | 讀取 scheduling.k8s.io |
> | Kubernetes/connectedClusters/api/k8s. io/v1/read | 讀取排程/v1 |
> | Kubernetes/connectedClusters/api/k8s. io/v1Beta1/read | 讀取 scheduling.k8s.io/v1Beta1 |
> | Kubernetes/connectedClusters/api/k8s. io/read | 讀取 storage.k8s.io |
> | Kubernetes/connectedClusters/api/儲存體. k8s. io/v1/read | 讀取儲存體/v1 |
> | Kubernetes/connectedClusters/api/儲存體. k8s. io/v1Beta1/read | 讀取 storage.k8s.io/v1Beta1 |
> | Kubernetes/connectedClusters/apps/controllerrevisions/read | 讀取 controllerrevisions |
> | Kubernetes/connectedClusters/apps/controllerrevisions/write | 寫入 controllerrevisions |
> | Kubernetes/connectedClusters/apps/controllerrevisions/delete | 刪除 controllerrevisions |
> | Kubernetes/connectedClusters/apps/daemonset/read | 讀取 daemonset |
> | Kubernetes/connectedClusters/apps/daemonset/write | 寫入 daemonset |
> | Kubernetes/connectedClusters/apps/daemonset/delete | 刪除 daemonset |
> | Kubernetes/connectedClusters/apps/部署/讀取 | 讀取部署 |
> | Kubernetes/connectedClusters/apps/部署/寫入 | 寫入部署 |
> | Kubernetes/connectedClusters/apps/部署/刪除 | 刪除部署 |
> | Kubernetes/connectedClusters/apps/replicaset/read | 讀取 replicaset |
> | Kubernetes/connectedClusters/apps/replicaset/write | 寫入 replicaset |
> | Kubernetes/connectedClusters/apps/replicaset/delete | 刪除 replicaset |
> | Kubernetes/connectedClusters/apps/statefulset/read | 讀取 statefulset |
> | Kubernetes/connectedClusters/apps/statefulset/write | 寫入 statefulset |
> | Kubernetes/connectedClusters/apps/statefulset/delete | 刪除 statefulset |
> | Kubernetes/connectedClusters/authentication. k8s. io/tokenreviews/write | 寫入 tokenreviews |
> | Kubernetes/connectedClusters/authentication. k8s. io/userextras/模擬/action | 模擬 userextras |
> | Kubernetes/connectedClusters/authorization. k8s. io/localsubjectaccessreviews/write | 寫入 localsubjectaccessreviews |
> | Kubernetes/connectedClusters/authorization. k8s. io/selfsubjectaccessreviews/write | 寫入 selfsubjectaccessreviews |
> | Kubernetes/connectedClusters/authorization. k8s. io/selfsubjectrulesreviews/write | 寫入 selfsubjectrulesreviews |
> | Kubernetes/connectedClusters/authorization. k8s. io/subjectaccessreviews/write | 寫入 subjectaccessreviews |
> | Kubernetes/connectedClusters/自動調整/horizontalpodautoscalers/read | 讀取 horizontalpodautoscalers |
> | Kubernetes/connectedClusters/自動調整/horizontalpodautoscalers/write | 寫入 horizontalpodautoscalers |
> | Kubernetes/connectedClusters/自動調整/horizontalpodautoscalers/刪除 | 刪除 horizontalpodautoscalers |
> | Kubernetes/connectedClusters/batch/cronjobs/read | 讀取 cronjobs |
> | Kubernetes/connectedClusters/batch/cronjobs/write | 寫入 cronjobs |
> | Kubernetes/connectedClusters/batch/cronjobs/delete | 刪除 cronjobs |
> | Kubernetes/connectedClusters/batch/jobs/read | 讀取作業 |
> | Kubernetes/connectedClusters/batch/jobs/write | 寫入作業 |
> | Kubernetes/connectedClusters/batch/jobs/delete | 刪除作業 |
> | Kubernetes/connectedClusters/bindings/write | 寫入系結 |
> | Kubernetes/connectedClusters/certificate. k8s. io/certificatesigningrequests/read | 讀取 certificatesigningrequests |
> | Kubernetes/connectedClusters/certificate. k8s. io/certificatesigningrequests/write | 寫入 certificatesigningrequests |
> | Kubernetes/connectedClusters/certificate. k8s. io/certificatesigningrequests/delete | 刪除 certificatesigningrequests |
> | Kubernetes/connectedClusters/>clusterconfig.json。 azure .com/azureclusteridentityrequests/read | 讀取 azureclusteridentityrequests |
> | Kubernetes/connectedClusters/>clusterconfig.json。 azure .com/azureclusteridentityrequests/write | 寫入 azureclusteridentityrequests |
> | Kubernetes/connectedClusters/>clusterconfig.json。 azure .com/azureclusteridentityrequests/delete | 刪除 azureclusteridentityrequests |
> | Kubernetes/connectedClusters/componentstatuses/read | 讀取 componentstatuses |
> | Kubernetes/connectedClusters/componentstatuses/write | 寫入 componentstatuses |
> | Kubernetes/connectedClusters/componentstatuses/delete | 刪除 componentstatuses |
> | Kubernetes/connectedClusters/configmaps/read | 讀取 configmaps |
> | Kubernetes/connectedClusters/configmaps/write | 寫入 configmaps |
> | Kubernetes/connectedClusters/configmaps/delete | 刪除 configmaps |
> | Kubernetes/connectedClusters/k8s. io/租借/read | 讀取租用 |
> | Kubernetes/connectedClusters/k8s. io/租借/write | 寫入租用 |
> | Kubernetes/connectedClusters/k8s. io/租借/delete | 刪除租用 |
> | Kubernetes/connectedClusters/端點/讀取 | 讀取端點 |
> | Kubernetes/connectedClusters/端點/寫入 | 寫入端點 |
> | Kubernetes/connectedClusters/端點/刪除 | 刪除端點 |
> | Kubernetes/connectedClusters/events/read | 讀取事件 |
> | Kubernetes/connectedClusters/events/write | 寫入事件 |
> | Kubernetes/connectedClusters/events/delete | 刪除事件 |
> | Kubernetes/connectedClusters/events. k8s. io/events/read | 讀取事件 |
> | Kubernetes/connectedClusters/events. k8s. io/events/write | 寫入事件 |
> | Kubernetes/connectedClusters/events. k8s. io/events/delete | 刪除事件 |
> | Kubernetes/connectedClusters/extensions/daemonset/read | 讀取 daemonset |
> | Kubernetes/connectedClusters/extensions/daemonset/write | 寫入 daemonset |
> | Kubernetes/connectedClusters/extensions/daemonset/delete | 刪除 daemonset |
> | Kubernetes/connectedClusters/extensions/部署/讀取 | 讀取部署 |
> | Kubernetes/connectedClusters/extensions/部署/寫入 | 寫入部署 |
> | Kubernetes/connectedClusters/extensions/部署/刪除 | 刪除部署 |
> | Kubernetes/connectedClusters/extensions/ingresses/read | 讀取 ingresses |
> | Kubernetes/connectedClusters/extensions/ingresses/write | 寫入 ingresses |
> | Kubernetes/connectedClusters/extensions/ingresses/delete | 刪除 ingresses |
> | Kubernetes/connectedClusters/extensions/networkpolicies/read | 讀取 networkpolicies |
> | Kubernetes/connectedClusters/extensions/networkpolicies/write | 寫入 networkpolicies |
> | Kubernetes/connectedClusters/extensions/networkpolicies/delete | 刪除 networkpolicies |
> | Kubernetes/connectedClusters/extensions/podsecuritypolicies/read | 讀取 podsecuritypolicies |
> | Kubernetes/connectedClusters/extensions/podsecuritypolicies/write | 寫入 podsecuritypolicies |
> | Kubernetes/connectedClusters/extensions/podsecuritypolicies/delete | 刪除 podsecuritypolicies |
> | Kubernetes/connectedClusters/extensions/replicaset/read | 讀取 replicaset |
> | Kubernetes/connectedClusters/extensions/replicaset/write | 寫入 replicaset |
> | Kubernetes/connectedClusters/extensions/replicaset/delete | 刪除 replicaset |
> | Kubernetes/connectedClusters/groups/模擬/動作 | 模擬群組 |
> | Kubernetes/connectedClusters/healthz/read | 讀取 healthz |
> | Kubernetes/connectedClusters/healthz/autoregister-完成/讀取 | 讀取 autoregister-完成 |
> | Kubernetes/connectedClusters/healthz/etcd/read | 讀取 etcd |
> | Kubernetes/connectedClusters/healthz/log/read | 讀取記錄檔 |
> | Kubernetes/connectedClusters/healthz/ping/read | 讀取 ping |
> | Kubernetes/connectedClusters/healthz/poststarthook/apiservice-openapi-controller/read | 讀取 apiservice-openapi-控制器 |
> | Kubernetes/connectedClusters/healthz/poststarthook/apiservice-註冊-控制器/讀取 | 讀取 apiservice-註冊-控制器 |
> | Kubernetes/connectedClusters/healthz/poststarthook/apiservice-狀態-可用-控制器/讀取 | 讀取 apiservice-狀態-可用-控制器 |
> | Kubernetes/connectedClusters/healthz/poststarthook/啟動程式-控制器/讀取 | 讀取啟動程式-控制器 |
> | Kubernetes/connectedClusters/healthz/poststarthook/ca-註冊/讀取 | 讀取 ca-註冊 |
> | Kubernetes/connectedClusters/healthz/poststarthook/.crd-informer-已同步/讀取 | 讀取 .crd-informer-已同步 |
> | Kubernetes/connectedClusters/healthz/poststarthook/generic-apiserver-start-洩密者/read | 讀取泛型-apiserver-start-洩密者 |
> | Kubernetes/connectedClusters/healthz/poststarthook/kube-apiserver-自動註冊/read | 讀取 kube-apiserver-自動註冊 |
> | Kubernetes/connectedClusters/healthz/poststarthook/rbac/啟動程式-角色/讀取 | 讀取啟動程式-角色 |
> | Kubernetes/connectedClusters/healthz/poststarthook/排程/啟動程式-系統優先順序-類別/讀取 | 讀取啟動程式-系統優先順序-類別 |
> | Kubernetes/connectedClusters/healthz/poststarthook/開始-apiextensions-控制器/讀取 | 讀取開始-apiextensions-控制器 |
> | Kubernetes/connectedClusters/healthz/poststarthook/start-apiextensions-洩密者/read | 讀取開始-apiextensions-洩密者 |
> | Kubernetes/connectedClusters/healthz/poststarthook/start-kube-匯總工具-洩密者/read | 讀取開始-kube-匯總工具-洩密者 |
> | Kubernetes/connectedClusters/healthz/poststarthook/開始-kube-apiserver-許可-初始化運算式/讀取 | 讀取開始-kube-apiserver-許可-初始化運算式 |
> | Kubernetes/connectedClusters/limitranges/read | 讀取 limitranges |
> | Kubernetes/connectedClusters/limitranges/write | 寫入 limitranges |
> | Kubernetes/connectedClusters/limitranges/delete | 刪除 limitranges |
> | Kubernetes/connectedClusters/livez/read | 讀取 livez |
> | Kubernetes/connectedClusters/livez/autoregister-完成/讀取 | 讀取 autoregister-完成 |
> | Kubernetes/connectedClusters/livez/etcd/read | 讀取 etcd |
> | Kubernetes/connectedClusters/livez/log/read | 讀取記錄檔 |
> | Kubernetes/connectedClusters/livez/ping/read | 讀取 ping |
> | Kubernetes/connectedClusters/livez/poststarthook/apiservice-openapi-controller/read | 讀取 apiservice-openapi-控制器 |
> | Kubernetes/connectedClusters/livez/poststarthook/apiservice-註冊-控制器/讀取 | 讀取 apiservice-註冊-控制器 |
> | Kubernetes/connectedClusters/livez/poststarthook/apiservice-狀態-可用-控制器/讀取 | 讀取 apiservice-狀態-可用-控制器 |
> | Kubernetes/connectedClusters/livez/poststarthook/啟動程式-控制器/讀取 | 讀取啟動程式-控制器 |
> | Kubernetes/connectedClusters/livez/poststarthook/ca-註冊/讀取 | 讀取 ca-註冊 |
> | Kubernetes/connectedClusters/livez/poststarthook/.crd-informer-已同步/讀取 | 讀取 .crd-informer-已同步 |
> | Kubernetes/connectedClusters/livez/poststarthook/generic-apiserver-start-洩密者/read | 讀取泛型-apiserver-start-洩密者 |
> | Kubernetes/connectedClusters/livez/poststarthook/kube-apiserver-自動註冊/read | 讀取 kube-apiserver-自動註冊 |
> | Kubernetes/connectedClusters/livez/poststarthook/rbac/啟動程式-角色/讀取 | 讀取啟動程式-角色 |
> | Kubernetes/connectedClusters/livez/poststarthook/排程/啟動程式-系統優先順序-類別/讀取 | 讀取啟動程式-系統優先順序-類別 |
> | Kubernetes/connectedClusters/livez/poststarthook/開始-apiextensions-控制器/讀取 | 讀取開始-apiextensions-控制器 |
> | Kubernetes/connectedClusters/livez/poststarthook/start-apiextensions-洩密者/read | 讀取開始-apiextensions-洩密者 |
> | Kubernetes/connectedClusters/livez/poststarthook/start-kube-匯總工具-洩密者/read | 讀取開始-kube-匯總工具-洩密者 |
> | Kubernetes/connectedClusters/livez/poststarthook/開始-kube-apiserver-許可-初始化運算式/讀取 | 讀取開始-kube-apiserver-許可-初始化運算式 |
> | Kubernetes/connectedClusters/logs/read | 讀取記錄 |
> | Kubernetes/connectedClusters/計量/讀取 | 讀取計量 |
> | Kubernetes/connectedClusters/k8s。 io/節點/read | 讀取節點 |
> | Kubernetes/connectedClusters/k8s. io/pod/read | 讀取 pod |
> | Kubernetes/connectedClusters/命名空間/讀取 | 讀取命名空間 |
> | Kubernetes/connectedClusters/命名空間/寫入 | 寫入命名空間 |
> | Kubernetes/connectedClusters/命名空間/刪除 | 刪除命名空間 |
> | Kubernetes/connectedClusters/網路. k8s. io/ingresses/read | 讀取 ingresses |
> | Kubernetes/connectedClusters/網路. k8s. io/ingresses/write | 寫入 ingresses |
> | Kubernetes/connectedClusters/網路. k8s. io/ingresses/delete | 刪除 ingresses |
> | Kubernetes/connectedClusters/網路. k8s. io/networkpolicies/read | 讀取 networkpolicies |
> | Kubernetes/connectedClusters/網路. k8s. io/networkpolicies/write | 寫入 networkpolicies |
> | Kubernetes/connectedClusters/網路. k8s. io/networkpolicies/delete | 刪除 networkpolicies |
> | Kubernetes/connectedClusters/k8s. io/runtimeclasses/read | 讀取 runtimeclasses |
> | Kubernetes/connectedClusters/k8s. io/runtimeclasses/write | 寫入 runtimeclasses |
> | Kubernetes/connectedClusters/k8s. io/runtimeclasses/delete | 刪除 runtimeclasses |
> | Kubernetes/connectedClusters/節點/讀取 | 讀取節點 |
> | Kubernetes/connectedClusters/節點/寫入 | 寫入節點 |
> | Kubernetes/connectedClusters/節點/刪除 | 刪除節點 |
> | Kubernetes/connectedClusters/openapi/v2/read | 讀取 v2 |
> | Kubernetes/connectedClusters/persistentvolumeclaims/read | 讀取 persistentvolumeclaims |
> | Kubernetes/connectedClusters/persistentvolumeclaims/write | 寫入 persistentvolumeclaims |
> | Kubernetes/connectedClusters/persistentvolumeclaims/delete | 刪除 persistentvolumeclaims |
> | Kubernetes/connectedClusters/persistentvolumes/read | 讀取 persistentvolumes |
> | Kubernetes/connectedClusters/persistentvolumes/write | 寫入 persistentvolumes |
> | Kubernetes/connectedClusters/persistentvolumes/delete | 刪除 persistentvolumes |
> | Kubernetes/connectedClusters/pod/read | 讀取 pod |
> | Kubernetes/connectedClusters/pod/write | 寫入 pod |
> | Kubernetes/connectedClusters/pod/delete | 刪除 pod |
> | Kubernetes/connectedClusters/podtemplates/read | 讀取 podtemplates |
> | Kubernetes/connectedClusters/podtemplates/write | 寫入 podtemplates |
> | Kubernetes/connectedClusters/podtemplates/delete | 刪除 podtemplates |
> | Kubernetes/connectedClusters/policy/poddisruptionbudgets/read | 讀取 poddisruptionbudgets |
> | Kubernetes/connectedClusters/policy/poddisruptionbudgets/write | 寫入 poddisruptionbudgets |
> | Kubernetes/connectedClusters/policy/poddisruptionbudgets/delete | 刪除 poddisruptionbudgets |
> | Kubernetes/connectedClusters/policy/podsecuritypolicies/read | 讀取 podsecuritypolicies |
> | Kubernetes/connectedClusters/policy/podsecuritypolicies/write | 寫入 podsecuritypolicies |
> | Kubernetes/connectedClusters/policy/podsecuritypolicies/delete | 刪除 podsecuritypolicies |
> | Kubernetes/connectedClusters/policy/podsecuritypolicies/use/action | Podsecuritypolicies 上的使用動作 |
> | Kubernetes/connectedClusters/rbac. k8s. io/clusterrolebindings/read | 讀取 clusterrolebindings |
> | Kubernetes/connectedClusters/rbac. k8s. io/clusterrolebindings/write | 寫入 clusterrolebindings |
> | Kubernetes/connectedClusters/rbac. k8s. io/clusterrolebindings/delete | 刪除 clusterrolebindings |
> | Kubernetes/connectedClusters/rbac. k8s. io/clusterroles/read | 讀取 clusterroles |
> | Kubernetes/connectedClusters/rbac. k8s. io/clusterroles/write | 寫入 clusterroles |
> | Kubernetes/connectedClusters/rbac. k8s. io/clusterroles/delete | 刪除 clusterroles |
> | Kubernetes/connectedClusters/rbac. k8s. io/clusterroles/bind/action | 系結 clusterroles |
> | Kubernetes/connectedClusters/rbac. k8s. io/clusterroles/呈報/action | 升級 |
> | Kubernetes/connectedClusters/rbac. k8s. io/rolebindings/read | 讀取 rolebindings |
> | Kubernetes/connectedClusters/rbac. k8s. io/rolebindings/write | 寫入 rolebindings |
> | Kubernetes/connectedClusters/rbac. k8s. io/rolebindings/delete | 刪除 rolebindings |
> | Kubernetes/connectedClusters/rbac. k8s. io/role/read | 讀取角色 |
> | Kubernetes/connectedClusters/rbac. k8s. io/role/write | 寫入角色 |
> | Kubernetes/connectedClusters/rbac. k8s. io/role/delete | 刪除角色 |
> | Kubernetes/connectedClusters/rbac. k8s. io/role/bind/action | 系結角色 |
> | Kubernetes/connectedClusters/rbac. k8s. io/roles/呈報/action | 升級角色 |
> | Kubernetes/connectedClusters/readyz/read | 讀取 readyz |
> | Kubernetes/connectedClusters/readyz/autoregister-完成/讀取 | 讀取 autoregister-完成 |
> | Kubernetes/connectedClusters/readyz/etcd/read | 讀取 etcd |
> | Kubernetes/connectedClusters/readyz/log/read | 讀取記錄檔 |
> | Kubernetes/connectedClusters/readyz/ping/read | 讀取 ping |
> | Kubernetes/connectedClusters/readyz/poststarthook/apiservice-openapi-controller/read | 讀取 apiservice-openapi-控制器 |
> | Kubernetes/connectedClusters/readyz/poststarthook/apiservice-註冊-控制器/讀取 | 讀取 apiservice-註冊-控制器 |
> | Kubernetes/connectedClusters/readyz/poststarthook/apiservice-狀態-可用-控制器/讀取 | 讀取 apiservice-狀態-可用-控制器 |
> | Kubernetes/connectedClusters/readyz/poststarthook/啟動程式-控制器/讀取 | 讀取啟動程式-控制器 |
> | Kubernetes/connectedClusters/readyz/poststarthook/ca-註冊/讀取 | 讀取 ca-註冊 |
> | Kubernetes/connectedClusters/readyz/poststarthook/.crd-informer-已同步/讀取 | 讀取 .crd-informer-已同步 |
> | Kubernetes/connectedClusters/readyz/poststarthook/generic-apiserver-start-洩密者/read | 讀取泛型-apiserver-start-洩密者 |
> | Kubernetes/connectedClusters/readyz/poststarthook/kube-apiserver-自動註冊/read | 讀取 kube-apiserver-自動註冊 |
> | Kubernetes/connectedClusters/readyz/poststarthook/rbac/啟動程式-角色/讀取 | 讀取啟動程式-角色 |
> | Kubernetes/connectedClusters/readyz/poststarthook/排程/啟動程式-系統優先順序-類別/讀取 | 讀取啟動程式-系統優先順序-類別 |
> | Kubernetes/connectedClusters/readyz/poststarthook/開始-apiextensions-控制器/讀取 | 讀取開始-apiextensions-控制器 |
> | Kubernetes/connectedClusters/readyz/poststarthook/start-apiextensions-洩密者/read | 讀取開始-apiextensions-洩密者 |
> | Kubernetes/connectedClusters/readyz/poststarthook/start-kube-匯總工具-洩密者/read | 讀取開始-kube-匯總工具-洩密者 |
> | Kubernetes/connectedClusters/readyz/poststarthook/開始-kube-apiserver-許可-初始化運算式/讀取 | 讀取開始-kube-apiserver-許可-初始化運算式 |
> | Kubernetes/connectedClusters/readyz/shutdown/read | 讀取關機 |
> | Kubernetes/connectedClusters/replicationcontrollers/read | 讀取 replicationcontrollers |
> | Kubernetes/connectedClusters/replicationcontrollers/write | 寫入 replicationcontrollers |
> | Kubernetes/connectedClusters/replicationcontrollers/delete | 刪除 replicationcontrollers |
> | Kubernetes/connectedClusters/resetMetrics/read | 讀取 resetMetrics |
> | Kubernetes/connectedClusters/resourcequotas/read | 讀取 resourcequotas |
> | Kubernetes/connectedClusters/resourcequotas/write | 寫入 resourcequotas |
> | Kubernetes/connectedClusters/resourcequotas/delete | 刪除 resourcequotas |
> | Kubernetes/connectedClusters/k8s. io/priorityclasses/read | 讀取 priorityclasses |
> | Kubernetes/connectedClusters/k8s. io/priorityclasses/write | 寫入 priorityclasses |
> | Kubernetes/connectedClusters/k8s. io/priorityclasses/delete | 刪除 priorityclasses |
> | Kubernetes/connectedClusters/秘密/讀取 | 讀取秘密 |
> | Kubernetes/connectedClusters/秘密/寫入 | 寫入秘密 |
> | Kubernetes/connectedClusters/秘密/刪除 | 刪除秘密 |
> | Kubernetes/connectedClusters/serviceaccounts/read | 讀取 serviceaccounts |
> | Kubernetes/connectedClusters/serviceaccounts/write | 寫入 serviceaccounts |
> | Kubernetes/connectedClusters/serviceaccounts/delete | 刪除 serviceaccounts |
> | Kubernetes/connectedClusters/serviceaccounts/模擬/動作 | 模擬 serviceaccounts |
> | Kubernetes/connectedClusters/services/read | 讀取服務 |
> | Kubernetes/connectedClusters/services/write | 寫入服務 |
> | Kubernetes/connectedClusters/services/delete | 刪除服務 |
> | Kubernetes/connectedClusters/k8s. io/csidrivers/read | 讀取 csidrivers |
> | Kubernetes/connectedClusters/k8s. io/csidrivers/write | 寫入 csidrivers |
> | Kubernetes/connectedClusters/k8s. io/csidrivers/delete | 刪除 csidrivers |
> | Kubernetes/connectedClusters/k8s. io/csinodes/read | 讀取 csinodes |
> | Kubernetes/connectedClusters/k8s. io/csinodes/write | 寫入 csinodes |
> | Kubernetes/connectedClusters/k8s. io/csinodes/delete | 刪除 csinodes |
> | Kubernetes/connectedClusters/k8s. io/storageclass/read | 讀取 storageclass |
> | Kubernetes/connectedClusters/k8s. io/storageclass/write | 寫入 storageclass |
> | Kubernetes/connectedClusters/k8s. io/storageclass/delete | 刪除 storageclass |
> | Kubernetes/connectedClusters/k8s. io/volumeattachments/read | 讀取 volumeattachments |
> | Kubernetes/connectedClusters/k8s. io/volumeattachments/write | 寫入 volumeattachments |
> | Kubernetes/connectedClusters/k8s. io/volumeattachments/delete | 刪除 volumeattachments |
> | Microsoft. Kubernetes/connectedClusters/swagger-api/read | 讀取 swagger-api |
> | Microsoft. Kubernetes/connectedClusters/swagger-ui/read | 讀取 swagger ui |
> | Microsoft. Kubernetes/connectedClusters/ui/read | 讀取 ui |
> | Kubernetes/connectedClusters/users/模擬/動作 | 模擬使用者 |
> | Kubernetes/connectedClusters/version/read | 讀取版本 |

### <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

Azure 服務： [Azure Lighthouse](../lighthouse/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | >microsoft.managedservices/註冊/動作 | 註冊至受管理的服務。 |
> | >microsoft.managedservices/取消註冊/動作 | 從受管理的服務取消註冊。 |
> | >microsoft.managedservices/marketplaceRegistrationDefinitions/read | 抓取受控服務註冊定義的清單。 |
> | >microsoft.managedservices/operations/read | 抓取受控服務作業的清單。 |
> | Microsoft.ManagedServices/operationStatuses/read | 讀取資源的作業狀態。 |
> | Microsoft.ManagedServices/registrationAssignments/read | 取出受控服務註冊指派的清單。 |
> | >microsoft.managedservices/registrationAssignments/write | 新增或修改受控服務註冊指派。 |
> | Microsoft.ManagedServices/registrationAssignments/delete | 移除受控服務註冊指派。 |
> | >microsoft.managedservices/registrationDefinitions/read | 抓取受控服務註冊定義的清單。 |
> | >microsoft.managedservices/registrationDefinitions/write | 新增或修改受控服務註冊定義。 |
> | >microsoft.managedservices/registrationDefinitions/delete | 移除受控服務註冊定義。 |

### <a name="microsoftmanagement"></a>Microsoft.Management

Azure 服務： [管理群組](../governance/management-groups/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Management/checkNameAvailability/action | 檢查指定的管理群組名稱是否有效且唯一。 |
> | Microsoft.Management/getEntities/action | 列出已驗證之使用者的所有實體 (管理群組、訂用帳戶等)。 |
> | Microsoft.Management/register/action | 向 Microsoft.Management 註冊指定的訂用帳戶 |
> | Microsoft.Management/managementGroups/read | 列出已驗證之使用者的管理群組。 |
> | Microsoft.Management/managementGroups/write | 建立或更新管理群組。 |
> | Microsoft.Management/managementGroups/delete | 刪除管理群組。 |
> | Microsoft. Management/managementGroups/子代/read | 取得管理群組 (管理群組、訂閱) 的所有下階。 |
> | Microsoft. Management/managementGroups/settings/read | 列出現有的管理群組階層設定。 |
> | Microsoft.Management/managementGroups/settings/write | 建立或更新管理群組階層設定。 |
> | Microsoft.Management/managementGroups/settings/delete | 刪除管理群組階層設定。 |
> | Microsoft. 管理/managementGroups/訂閱/讀取 | 列出指定管理群組底下的訂用帳戶。 |
> | Microsoft.Management/managementGroups/subscriptions/write | 將現有的訂用帳戶關聯至管理群組。 |
> | Microsoft.Management/managementGroups/subscriptions/delete | 從管理群組中取消訂用帳戶的關聯。 |

### <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

Azure 服務： [Azure 原則](../governance/policy/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.PolicyInsights/register/action | 註冊 Microsoft Policy Insights 資源提供者，並對其啟用動作。 |
> | Microsoft.policyinsights/取消註冊/動作 | 取消註冊 Microsoft Policy Insights 資源提供者。 |
> | Microsoft.PolicyInsights/asyncOperationResults/read | 取得非同步作業結果。 |
> | Microsoft.policyinsights/證明/read | 取得合規性狀態證明。 |
> | Microsoft.policyinsights/證明/write | 建立或更新合規性狀態證明。 |
> | Microsoft.policyinsights/證明/delete | 刪除合規性狀態證明。 |
> | Microsoft.policyinsights/checkPolicyRestrictions/read | 取得原則將在資源上強制執行之限制的詳細資料。 |
> | Microsoft.policyinsights/eventGridFilters/read | 取得事件方格篩選器，用來追蹤要發佈狀態變更通知的範圍。 |
> | Microsoft.policyinsights/eventGridFilters/write | 建立或更新事件方格篩選。 |
> | Microsoft.policyinsights/eventGridFilters/delete | 刪除事件方格篩選。 |
> | Microsoft.policyinsights/operations/read | 取得 Microsoft.policyinsights 命名空間上支援的作業 |
> | Microsoft.PolicyInsights/policyEvents/queryResults/action | 查詢原則事件的相關資訊。 |
> | Microsoft.PolicyInsights/policyEvents/queryResults/read | 查詢原則事件的相關資訊。 |
> | Microsoft.policyinsights/policyMetadata/read | 取得原則中繼資料資源。 |
> | Microsoft.PolicyInsights/policyStates/queryResults/action | 查詢原則狀態的相關資訊。 |
> | Microsoft.PolicyInsights/policyStates/summarize/action | 查詢原則最新狀態的摘要資訊。 |
> | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | 對選取的範圍觸發新的合規性評估。 |
> | Microsoft.PolicyInsights/policyStates/queryResults/read | 查詢原則狀態的相關資訊。 |
> | Microsoft.PolicyInsights/policyStates/summarize/read | 查詢原則最新狀態的摘要資訊。 |
> | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | 查詢 DeployIfNotExists 原則所需之資源的相關資訊。 |
> | Microsoft.PolicyInsights/remediations/read | 取得原則補救。 |
> | Microsoft.PolicyInsights/remediations/write | 建立或更新 Microsoft 原則補救。 |
> | Microsoft.PolicyInsights/remediations/delete | 刪除原則補救。 |
> | Microsoft.PolicyInsights/remediations/cancel/action | 取消進行中的 Microsoft 原則補救。 |
> | Microsoft.PolicyInsights/remediations/listDeployments/read | 列出原則補救所需的部署。 |
> | **DataAction** | **說明** |
> | Microsoft.PolicyInsights/checkDataPolicyCompliance/action | 根據資料原則檢查給定元件的合規性狀態。 |
> | Microsoft.PolicyInsights/policyEvents/logDataEvents/action | 記錄資源元件原則事件。 |

### <a name="microsoftportal"></a>Microsoft.Portal

Azure 服務： [Azure 入口網站](../azure-portal/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Portal/register/action | 註冊入口網站 |
> | Microsoft.Portal/consoles/delete | 移除 Cloud Shell 實例。 |
> | Microsoft.Portal/consoles/write | 建立或更新 Cloud Shell 實例。 |
> | Microsoft 入口網站/主控台/讀取 | 讀取 Cloud Shell 實例。 |
> | Microsoft.Portal/dashboards/read | 讀取訂用帳戶的儀表板。 |
> | Microsoft.Portal/dashboards/write | 新增或修改訂用帳戶的儀表板。 |
> | Microsoft.Portal/dashboards/delete | 從訂用帳戶移除儀表板。 |
> | Microsoft Portal/tenantConfigurations/read | 讀取租使用者設定 |
> | Microsoft 入口網站/tenantConfigurations/write | 新增或更新租使用者設定。 使用者必須是此作業的租使用者系統管理員。 |
> | Microsoft 入口網站/tenantConfigurations/刪除 | 移除租使用者設定。 使用者必須是此作業的租使用者系統管理員。 |
> | Microsoft.Portal/usersettings/delete | 移除 Cloud Shell 的使用者設定。 |
> | Microsoft.Portal/usersettings/write | 建立或更新 Cloud Shell 使用者設定。 |
> | Microsoft.Portal/usersettings/read | 讀取 Cloud Shell 的使用者設定。 |

### <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

Azure 服務： [Site Recovery](../site-recovery/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.RecoveryServices/register/action | 為指定的資源提供者註冊訂用帳戶 |
> | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/Locations/backupStatus/action | 檢查復原服務保存庫的備份狀態 |
> | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | 驗證功能 |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp 是服務所使用的內部作業 |
> | Microsoft.RecoveryServices/locations/checkNameAvailability/action | 檢查資源名稱可用性是 API，以檢查是否有可用的資源名稱 |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp 是服務所使用的內部作業 |
> | Az.recoveryservices/位置/backupProtectedItem/寫入 | 建立備用的受保護項目 |
> | Az.recoveryservices/位置/backupProtectedItems/讀取 | 傳回所有受保護項目的清單。 |
> | Microsoft.RecoveryServices/locations/operationStatus/read | 取得給定作業的作業狀態 |
> | Microsoft.RecoveryServices/operations/read | 作業會傳回資源提供者的作業清單 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | 匯出作業 |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | 傳回復原服務保存庫的安全性 PIN 碼資訊。 |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | 驗證受保護項目上的作業 |
> | Microsoft.RecoveryServices/Vaults/write | 「建立保存庫」作業會建立 'vault' 類型的 Azure 資源 |
> | Microsoft.RecoveryServices/Vaults/read | 「取得保存庫」作業會取得物件，此物件代表 'vault' 類型的 Azure 資源 |
> | Microsoft.RecoveryServices/Vaults/delete | 「刪除保存庫」作業會刪除 'vault' 類型的指定 Azure 資源 |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | 傳回復原服務保存庫的組態。 |
> | Microsoft.RecoveryServices/Vaults/backupconfig/write | 更新復原服務保存庫的組態。 |
> | Az.recoveryservices/保存庫/backupEncryptionConfigs/read | 取得備份資源加密設定。 |
> | Az.recoveryservices/保存庫/backupEncryptionConfigs/寫入 | 更新備份資源加密設定 |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | 傳回已向保存庫註冊的所有備份管理伺服器。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | 重新整理容器清單 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | 刪除備份保護用途 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | 取得備份保護用途 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | 建立備份保護用途 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 傳回作業的狀態 |
> | Az.recoveryservices/保存庫/backupFabrics/operationsStatus/read | 傳回作業的狀態 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | 取得所有可保護的容器 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | 刪除已註冊的容器 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | 執行容器內工作負載的查詢 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | 傳回所有已註冊的容器 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | 建立已註冊的容器 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | 取得容器中的所有項目 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | 取得對保護容器執行之作業的結果。 |
> | Az.recoveryservices/保存庫/backupFabrics/protectionContainers/operationsStatus/read | 取得保護容器上所執行之作業的狀態。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 對受保護的項目執行備份。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | 刪除受保護的項目 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 傳回受保護項目的物件詳細資料 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | 建立備用的受保護項目 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 取得對受保護項目執行之作業的結果。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 傳回對受保護項目執行之作業的狀態。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 為受保護的項目佈建即時項目復原 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 取得受保護項目的復原點。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 還原受保護項目的復原點。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 為受保護的項目撤銷即時項目復原 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | 取消作業 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | 傳回所有作業物件 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | 傳回作業的作業結果。 |
> | Az.recoveryservices/保存庫/>backupjobs/operationsStatus/read | 傳回工作作業的狀態。 |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | 傳回復原服務保存庫的備份作業結果。 |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | 傳回復原服務保存庫的備份作業狀態。 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | 刪除保護原則 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | 傳回所有保護原則 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | 建立保護原則 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | 取得原則作業的結果。 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | 取得原則作業的狀態。 |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | 傳回所有可保護項目的清單。 |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | 傳回所有受保護項目的清單。 |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | 傳回屬於訂用帳戶的所有容器 |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | 列出所有的備份保護用途 |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | 傳回復原服務保存庫的儲存體組態。 |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | 更新復原服務保存庫的儲存體組態。 |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | 傳回復原服務之受保護項目和受保護伺服器的摘要。 |
> | Microsoft.RecoveryServices/Vaults/certificates/write | 「更新資源憑證」作業會更新資源/保存庫的認證憑證。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | 取得復原服務保存庫的警示。 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | 解決警示。 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | 取得復原服務保存庫通知組態。 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | 設定復原服務保存庫的電子郵件通知。 |
> | Az.recoveryservices/保存庫/privateEndpointConnectionProxies/刪除 | 等候幾分鐘的時間，然後再次嘗試操作。 如果問題持續發生， 請連絡 Microsoft 支援服務。 |
> | Az.recoveryservices/保存庫/privateEndpointConnectionProxies/read | 取得所有可保護的容器 |
> | Az.recoveryservices/保存庫/privateEndpointConnectionProxies/驗證/動作 | 取得所有可保護的容器 |
> | Az.recoveryservices/保存庫/privateEndpointConnectionProxies/寫入 | 取得所有可保護的容器 |
> | Az.recoveryservices/保存庫/privateEndpointConnectionProxies/operationsStatus/read | 取得所有可保護的容器 |
> | Az.recoveryservices/保存庫/privateEndpointConnections/刪除 | 刪除私人端點要求。 此呼叫是由備份管理員進行。 |
> | Az.recoveryservices/保存庫/privateEndpointConnections/寫入 | 核准或拒絕私人端點要求。 此呼叫是由備份管理員進行。 |
> | Az.recoveryservices/保存庫/privateEndpointConnections/operationsStatus/read | 傳回私人端點連接的作業狀態。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | 「註冊服務容器」作業可用來向復原服務註冊容器。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | 「取得容器」作業可用來取得為資源註冊的容器。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | 「取消註冊容器」作業可用來取消註冊容器。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | 「取得作業結果」作業可用來取得以非同步方式提交之作業的作業狀態和結果 |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | 讀取任何警示設定 |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | 建立或更新任何警示設定 |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | 讀取任何事件 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | 讀取任何網狀架構 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/write | 建立或更新任何網狀架構 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | 移除網狀架構 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | 檢查網狀架構的一致性 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | 刪除任何網狀架構 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | 更新網狀架構的憑證 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | 部署處理序伺服器的映像 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | 重新關聯閘道 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | 將網狀架構遷移至 AAD |
> | Az.recoveryservices/保存庫/replicationFabrics/operationresults/read | 追蹤資源網狀架構上非同步作業的結果 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | 讀取任何邏輯網路 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | 讀取任何網路 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 讀取任何網路對應 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | 建立或更新任何網路對應 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | 刪除任何網路對應 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | 讀取任何保護容器 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | 探索可保護的項目 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | 建立或更新任何保護容器 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | 移除保護容器 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | 切換保護容器 |
> | Az.recoveryservices/保存庫/replicationFabrics/replicationProtectionContainers/operationresults/read | 追蹤資源保護容器上非同步作業的結果 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | 讀取任何移轉項目 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | 建立或更新任何移轉項目 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | 刪除任何移轉項目 |
> | Az.recoveryservices/保存庫/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/resync/action | 重新同步處理 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | 遷移資料 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | 測試遷移 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | 測試遷移清除 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | 讀取任何移轉復原點 |
> | Az.recoveryservices/vault/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/read | 追蹤資源遷移專案上非同步作業的結果 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 讀取任何可保護的項目 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 讀取任何受保護的項目 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | 建立或更新任何受保護的項目 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | 刪除任何受保護的項目 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | 移除受保護的項目 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 計劃性容錯移轉 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | 容錯移轉 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Test Failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | 測試容錯移轉清理 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | 容錯移轉認可 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | 重新保護受保護的項目 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | 更新行動服務 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | 修復複寫 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 套用復原點 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | 提交意見 |
> | Az.recoveryservices/vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | 新增磁碟 |
> | Az.recoveryservices/vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | 移除磁片 |
> | Az.recoveryservices/vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Az.recoveryservices/vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/read | 追蹤資源受保護專案上非同步作業的結果 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 讀取任何複寫復原點 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | 讀取任何目標計算大小 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 讀取任何保護容器對應 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | 建立或更新任何保護容器對應 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | 移除保護容器對應 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | 刪除任何保護容器對應 |
> | Az.recoveryservices/vault/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/read | 追蹤資源保護容器對應上非同步作業的結果 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | 讀取任何復原服務提供者 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | 建立或更新任何復原服務提供者 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | 移除復原服務提供者 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | 刪除任何復原服務提供者 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | 重新整理提供者 |
> | Az.recoveryservices/保存庫/replicationFabrics/replicationRecoveryServicesProviders/operationresults/read | 追蹤資源復原服務提供者上非同步作業的結果 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | 讀取任何存放裝置分類 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 讀取任何存放裝置分類對應 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | 建立或更新任何存放裝置分類對應 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | 刪除任何存放裝置分類對應 |
> | Az.recoveryservices/vault/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/read | 追蹤資源儲存體分類對應上非同步作業的結果 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | 讀取任何 vCenter |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | 建立或更新任何 vCenter |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | 刪除任何 vCenter |
> | Az.recoveryservices/保存庫/replicationFabrics/replicationvCenters/operationresults/read | 追蹤資源 vCenters 上非同步作業的結果 |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | 讀取任何作業 |
> | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | 取消作業 |
> | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | 重新啟動作業 |
> | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | 繼續作業 |
> | Az.recoveryservices/保存庫/replicationJobs/operationresults/read | 追蹤資源作業上非同步作業的結果 |
> | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | 讀取任何移轉項目 |
> | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | 讀取任何網路對應 |
> | Microsoft.RecoveryServices/vaults/replicationNetworks/read | 讀取任何網路 |
> | Microsoft.RecoveryServices/vaults/replicationOperationStatus/read | 讀取任何保存庫複寫作業狀態 |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | 讀取任何原則 |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/write | 建立或更新任何原則 |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | 刪除任何原則 |
> | Az.recoveryservices/保存庫/replicationPolicies/operationresults/read | 追蹤資源原則上非同步作業的結果 |
> | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | 讀取任何受保護的項目 |
> | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | 讀取任何保護容器對應 |
> | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | 讀取任何保護容器 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 讀取任何復原方案 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | 建立或更新任何復原方案 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | 刪除任何復原方案 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | 計劃性容錯移轉復原方案 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | 容錯移轉復原方案 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | 測試容錯移轉復原方案 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | 測試容錯移轉清理復原方案 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | 容錯移轉認可復原方案 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | 重新保護復原方案 |
> | Az.recoveryservices/保存庫/replicationRecoveryPlans/operationresults/read | 追蹤資源復原方案上非同步作業的結果 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | 讀取任何復原服務提供者 |
> | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | 讀取任何存放裝置分類對應 |
> | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | 讀取任何存放裝置分類 |
> | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | 讀取任何  |
> | Microsoft.RecoveryServices/vaults/replicationUsages/read | 讀取任何保存庫複寫使用量 |
> | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | 讀取任何保存庫複寫健康情況 |
> | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | 重新整理保存庫健康情況 |
> | Az.recoveryservices/保存庫/replicationVaultHealth/operationresults/read | 追蹤資源保存庫複寫健全狀況上非同步作業的結果 |
> | Az.recoveryservices/保存庫/replicationVaultSettings/read | 讀取任何  |
> | Az.recoveryservices/保存庫/replicationVaultSettings/寫入 | 建立或更新任何  |
> | Microsoft.RecoveryServices/vaults/replicationvCenters/read | 讀取任何 vCenter |
> | Microsoft.RecoveryServices/Vaults/usages/read | 傳回復原服務保存庫的使用量詳細資料。 |
> | Microsoft.RecoveryServices/vaults/usages/read | 讀取任何保存庫使用量 |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | 「保存庫權杖」作業可用來取得保存庫層級後端作業的保存庫權杖。 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure 服務： [Azure Resource Manager](../azure-resource-manager/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Resources/checkResourceName/action | 檢查資源名稱是否有效。 |
> | Microsoft .Resources/calculateTemplateHash/action | 計算所提供範本的雜湊。 |
> | Microsoft .Resources/checkPolicyCompliance/read | 檢查指定的資源是否符合資源原則，以了解合規性狀態。 |
> | Microsoft.Resources/deployments/read | 取得或列出部署。 |
> | Microsoft.Resources/deployments/write | 建立或更新部署。 |
> | Microsoft.Resources/deployments/delete | 刪除部署。 |
> | Microsoft.Resources/deployments/cancel/action | 取消部署。 |
> | Microsoft.Resources/deployments/validate/action | 驗證部署。 |
> | Microsoft .Resources/部署/whatIf/action | 預測範本部署變更。 |
> | Microsoft .Resources/部署/exportTemplate/action | 匯出部署的範本 |
> | Microsoft.Resources/deployments/operations/read | 取得或列出部署作業。 |
> | Microsoft .Resources/部署/operationstatuses/read | 取得或列出部署作業狀態。 |
> | Microsoft .Resources/deploymentScripts/read | 取得或列出部署腳本 |
> | Microsoft .Resources/deploymentScripts/write | 建立或更新部署腳本 |
> | Microsoft .Resources/deploymentScripts/delete | 刪除部署腳本 |
> | Microsoft .Resources/deploymentScripts/logs/read | 取得或列出部署腳本記錄 |
> | Microsoft.Resources/links/read | 取得或列出資源連結。 |
> | Microsoft.Resources/links/write | 建立或更新資源連結。 |
> | Microsoft.Resources/links/delete | 刪除資源連結。 |
> | Microsoft.Resources/marketplace/purchase/action | 從 Marketplace 購買資源。 |
> | Microsoft.Resources/providers/read | 取得提供者清單。 |
> | Microsoft.Resources/resources/read | 根據篩選來取得資源清單。 |
> | Microsoft.Resources/subscriptions/read | 取得訂用帳戶清單。 |
> | Microsoft.Resources/subscriptions/locations/read | 取得所支援位置的清單。 |
> | Microsoft.Resources/subscriptions/operationresults/read | 取得訂用帳戶作業結果。 |
> | Microsoft.Resources/subscriptions/providers/read | 取得或列出資源提供者。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | Microsoft.Resources/subscriptions/resourceGroups/write | 建立或更新資源群組。 |
> | Microsoft.Resources/subscriptions/resourceGroups/delete | 刪除資源群組和其所有資源。 |
> | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | 將資源從某個資源群組移到另一個資源群組。 |
> | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | 驗證資源從某個資源群組移到另一個資源群組的動作。 |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | 取得或列出部署。 |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | 建立或更新部署。 |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | 取得或列出部署作業。 |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | 取得或列出部署作業狀態。 |
> | Microsoft.Resources/subscriptions/resourcegroups/resources/read | 取得資源群組的資源。 |
> | Microsoft.Resources/subscriptions/resources/read | 取得訂用帳戶的資源。 |
> | Microsoft.Resources/subscriptions/tagNames/read | 取得或列出訂用帳戶標記。 |
> | Microsoft.Resources/subscriptions/tagNames/write | 新增訂用帳戶標記。 |
> | Microsoft.Resources/subscriptions/tagNames/delete | 刪除訂用帳戶標記。 |
> | Microsoft.Resources/subscriptions/tagNames/tagValues/read | 取得或列出訂用帳戶標記值。 |
> | Microsoft.Resources/subscriptions/tagNames/tagValues/write | 新增訂用帳戶標記值。 |
> | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | 刪除訂用帳戶標記值。 |
> | Microsoft .Resources/tag/read | 取得資源上的所有標記。 |
> | Microsoft .Resources/tag/write | 藉由使用一組新的標記來取代或合併現有的標記，或移除現有的標記，以更新資源上的標記。 |
> | Microsoft .Resources/標記/刪除 | 移除資源上的所有標記。 |
> | Microsoft .Resources/templateSpecs/read | 取得或列出範本規格 |
> | Microsoft .Resources/templateSpecs/write | 建立或更新範本規格 |
> | Microsoft .Resources/templateSpecs/delete | 刪除範本規格 |
> | Microsoft .Resources/templateSpecs/版本/讀取 | 取得或列出範本規格 |
> | Microsoft .Resources/templateSpecs/版本/寫入 | 建立或更新範本規格版本 |
> | Microsoft .Resources/templateSpecs/版本/刪除 | 刪除範本規格版本 |
> | Microsoft.Resources/tenants/read | 取得租用戶清單。 |

### <a name="microsoftscheduler"></a>Microsoft.Scheduler

Azure[服務：排程](../scheduler/index.yml)器

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Scheduler/jobcollections/read | 取得作業集合 |
> | Microsoft.Scheduler/jobcollections/write | 建立或更新作業集合。 |
> | Microsoft.Scheduler/jobcollections/delete | 刪除作業集合。 |
> | Microsoft.Scheduler/jobcollections/enable/action | 啟用作業集合。 |
> | Microsoft.Scheduler/jobcollections/disable/action | 停用作業集合。 |
> | Microsoft.Scheduler/jobcollections/jobs/read | 取得作業。 |
> | Microsoft.Scheduler/jobcollections/jobs/write | 建立或更新作業。 |
> | Microsoft.Scheduler/jobcollections/jobs/delete | 刪除作業。 |
> | Microsoft.Scheduler/jobcollections/jobs/run/action | 執行作業。 |
> | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | 根據排程器作業來產生邏輯應用程式定義。 |
> | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | 取得工作歷程記錄。 |

### <a name="microsoftsolutions"></a>Microsoft.Solutions

Azure 服務： [Azure 受控應用程式](../azure-resource-manager/managed-applications/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Solutions/register/action | 向 Solutions 註冊。 |
> | Microsoft. 解決方案/取消註冊/動作 | 從解決方案取消註冊。 |
> | Microsoft.Solutions/applicationDefinitions/read | 擷取應用程式定義清單。 |
> | Microsoft.Solutions/applicationDefinitions/write | 新增或修改應用程式定義。 |
> | Microsoft.Solutions/applicationDefinitions/delete | 移除應用程式定義。 |
> | Microsoft. 解決方案/applicationDefinitions/applicationArtifacts/read | 列出應用程式定義的應用程式成品。 |
> | Microsoft.Solutions/applications/read | 擷取應用程式清單。 |
> | Microsoft.Solutions/applications/write | 建立應用程式。 |
> | Microsoft.Solutions/applications/delete | 移除應用程式。 |
> | Microsoft. 解決方案/應用程式/refreshPermissions/action | 重新整理應用程式許可權 (s) 。 |
> | Microsoft. 解決方案/應用程式/updateAccess/action | 更新應用程式存取。 |
> | Microsoft. 解決方案/應用程式/applicationArtifacts/read | 列出應用程式成品。 |
> | Microsoft.Solutions/jitRequests/read | 擷取 JitRequests 清單 |
> | Microsoft.Solutions/jitRequests/write | 建立 JitRequest |
> | Microsoft.Solutions/jitRequests/delete | 移除 JitRequest |
> | Microsoft.Solutions/locations/operationStatuses/read | 讀取資源的作業狀態。 |
> | Microsoft。解決方案/作業/讀取 | 取得作業的清單。 |

### <a name="microsoftsubscription"></a>Microsoft.Subscription

Azure 服務：核心

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft. 訂用帳戶/取消/動作 | 取消訂用帳戶 |
> | Microsoft. 訂用帳戶/重新命名/動作 | 重新命名訂用帳戶 |
> | Microsoft. 訂用帳戶/啟用/動作 | 重新開機訂用帳戶 |
> | Microsoft.Subscription/CreateSubscription/action | 建立 Azure 訂用帳戶 |
> | Microsoft.Subscription/register/action | 向 Microsoft.Subscription 資源提供者註冊訂用帳戶 |
> | Microsoft. 訂用帳戶/updateTenant/動作 | 更新訂用帳戶的租使用者 |
> | Microsoft. 訂用帳戶/別名/寫入 | 建立訂用帳號別名 |
> | Microsoft. 訂用帳戶/別名/讀取 | 取得訂用帳號別名 |
> | Microsoft. 訂用帳戶/別名/刪除 | 刪除訂用帳號別名 |
> | Microsoft. 訂用帳戶/訂閱/寫入 | 建立 Azure 訂用帳戶 |

## <a name="intune"></a>Intune

### <a name="microsoftintune"></a>Microsoft.Intune

Azure 服務： Microsoft Monitoring Insights

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | Microsoft.Intune/diagnosticsettings/write | 正在寫入診斷設定 |
> | Microsoft.Intune/diagnosticsettings/read | 正在讀取診斷設定 |
> | Microsoft.Intune/diagnosticsettings/delete | 正在刪除診斷設定 |
> | Microsoft.Intune/diagnosticsettingscategories/read | 正在讀取診斷設定類別 |

## <a name="other"></a>其他

### <a name="microsoftbingmaps"></a>Microsoft.BingMaps

Azure 服務： [Bing 地圖](https://docs.microsoft.com/BingMaps/)服務

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | BingMaps/updateCommunicationPreference/action | 更新 BingMaps 擁有者的通訊喜好設定 |
> | BingMaps/listCommunicationPreference/action | 取得 BingMaps 擁有者的通訊喜好設定。 |
> | Microsoft.BingMaps/mapApis/Read | 取得 BingMaps/mapApis 的資源 |
> | Microsoft.BingMaps/mapApis/Write | 更新 BingMaps/mapApis 的資源 |
> | Microsoft.BingMaps/mapApis/Delete | 刪除 BingMaps/mapApis 的資源 |
> | Microsoft.BingMaps/mapApis/regenerateKey/action | 重新產生 BingMaps/mapApis 的金鑰 (s)  |
> | Microsoft.BingMaps/mapApis/listSecrets/action | 列出 BingMaps/mapApis 的秘密 |
> | BingMaps/mapApis/listUsageMetrics/action | 列出 BingMaps/mapApis 的計量 |
> | Microsoft.BingMaps/Operations/read | 列出 BingMaps 的作業。 |

### <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

Azure 服務： [Azure 數位 Twins](../digital-twins/index.yml)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | DigitalTwins/註冊/動作 | 為數字 Twins 資源提供者註冊訂用帳戶，並讓您能夠建立數位 Twins 實例。 |
> | DigitalTwins/取消註冊/動作 | 為數字 Twins 資源提供者取消註冊訂用帳戶 |
> | DigitalTwins/digitalTwinsInstances/read | 讀取任何 DigitalTwins/digitalTwinsInstances 資源 |
> | DigitalTwins/digitalTwinsInstances/write | 建立或更新任何 DigitalTwins/digitalTwinsInstances 資源 |
> | DigitalTwins/digitalTwinsInstances/delete | 刪除 DigitalTwins/digitalTwinsInstances 資源 |
> | DigitalTwins/digitalTwinsInstances/diagnosticSettings/read | 取得資源的診斷設定 |
> | DigitalTwins/digitalTwinsInstances/diagnosticSettings/write | 設定資源的診斷設定 |
> | DigitalTwins/digitalTwinsInstances/端點/刪除 | 刪除數位 Twins 資源的任何端點 |
> | DigitalTwins/digitalTwinsInstances/端點/讀取 | 讀取數位 Twins 資源的任何端點 |
> | DigitalTwins/digitalTwinsInstances/端點/寫入 | 建立或更新數位 Twins 資源的任何端點 |
> | DigitalTwins/digitalTwinsInstances/logDefinitions/read | 取得資源 Azure 監視器的記錄檔設定 |
> | DigitalTwins/digitalTwinsInstances/metricDefinitions/read | 取得資源 Azure 監視器的度量設定 |
> | DigitalTwins/digitalTwinsInstances/operationsResults/read | 讀取任何作業結果 |
> | DigitalTwins/位置/checkNameAvailability/動作 | 檢查數位 Twins 資源提供者中的資源名稱可用性 |
> | DigitalTwins/位置/operationsResults/讀取 | 讀取任何作業結果 |
> | DigitalTwins/operations/read | 讀取所有作業 |
> | **DataAction** | **說明** |
> | DigitalTwins/query/action | 查詢任何數位 Twins 圖形 |
> | DigitalTwins/DigitalTwins/read | 讀取任何數位對應項 |
> | DigitalTwins/DigitalTwins/write | 建立或更新任何數位對應項 |
> | DigitalTwins/DigitalTwins/delete | 刪除任何數位對應項 |
> | DigitalTwins/DigitalTwins/命令/action | 在數位對應項上叫用任何命令 |
> | DigitalTwins/DigitalTwins/關係/read | 讀取任何數位對應項關聯性 |
> | DigitalTwins/DigitalTwins/關係/write | 建立或更新任何數位對應項關聯性 |
> | DigitalTwins/DigitalTwins/關聯性/刪除 | 刪除任何數位對應項關聯性 |
> | DigitalTwins/eventroutes/read | 讀取任何事件路由 |
> | DigitalTwins/eventroutes/delete | 刪除任何事件路由 |
> | DigitalTwins/eventroutes/write | 建立或更新任何事件路由 |
> | DigitalTwins/模型/讀取 | 讀取任何模型 |
> | DigitalTwins/模型/寫入 | 建立或更新任何模型 |
> | DigitalTwins/模型/刪除 | 刪除任何模型 |

## <a name="next-steps"></a>後續步驟

- [將資源提供者與服務配對](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Azure 內建角色](built-in-roles.md)
- [雲端採用架構：Azure 中的資源存取管理](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
