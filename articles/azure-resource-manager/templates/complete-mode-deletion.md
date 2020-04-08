---
title: 完整模式刪除
description: 顯示資源類型如何處理 Azure Resource Manager 範本中的完整模式刪除。
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: e0250b289ce7a228d844023c3e1d1110438b3afc
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802564"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>完整模式部署的 Azure 資源刪除

此文章說明當以完整模式部署的範本中沒有資源類型時，資源類型會如何處理刪除。

當類型不在部署的完整模式的範本中時,標記為 **「是**」的資源類型將被刪除。

未在範本中標記為 **「否**」的資源類型不會自動刪除;因此,在範本中,不會自動刪除"否"的資源類型。但是,如果刪除父資源,它們將被刪除。 如需行為的完整描述，請參閱 [Azure Resource Manager 部署模式](deployment-modes.md)。

如果部署到[樣本中的多個資源組](cross-resource-group-deployment.md),則部署操作中指定的資源組中的資源才有資格被刪除。 不會刪除輔助資源群組中的資源。

跳到資源提供者命名空間:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [微軟.應用程式設定](#microsoftappconfiguration)
> - [微軟.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [微軟.授權](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [微軟.阿茲康菲格](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [微軟.Azure數據](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [微軟.區塊鏈權杖](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [微軟.經典計算](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [微軟.經典網路](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [微軟.認知服務](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [微軟.計算](#microsoftcompute)
> - [微軟.消費](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [微軟客戶鎖定箱](#microsoftcustomerlockbox)
> - [微軟.自訂提供者](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [微軟.資料共享](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [微軟.桌面虛擬化](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [微軟.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [微軟.企業知識圖](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [微軟.法爾肯](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [微軟.醫療保健Apis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [微軟.海德拉](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [微軟.鑰匙庫](#microsoftkeyvault)
> - [微軟.庫伯內斯](#microsoftkubernetes)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [微軟.維護](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [微軟.託管服務](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [微軟.微服務4春天](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [微軟.混合實境](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [微軟.網路](#microsoftnetwork)
> - [微軟.筆記本](#microsoftnotebooks)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [微軟.物件商店](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [微軟運營管理](#microsoftoperationsmanagement)
> - [微軟.對等](#microsoftpeering)
> - [微軟.政策見解](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [微軟.專案巴比隆](#microsoftprojectbabylon)
> - [微軟.昆騰](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [微軟.遠端應用程式](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [微軟.安全洞察](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [微軟.服務](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [微軟.軟體計劃](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [微軟.拼車服務](#microsoftspoolservice)
> - [微軟.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [微軟儲存快取](#microsoftstoragecache)
> - [微軟儲存複製](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [微軟.VMware雲簡單](#microsoftvmwarecloudsimple)
> - [微軟.Vnf管理員](#microsoftvnfmanager)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | DomainServices | 是 |
> | 網域服務/ ou容器 | 否 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | supportProviders | 否 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | aadsupportcases | 否 |
> | addsservices | 否 |
> | agents | 否 |
> | anonymousapiusers | 否 |
> | 組態 | 否 |
> | 記錄 | 否 |
> | reports | 否 |
> | servicehealthmetrics | 否 |
> | 服務 | 否 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 組態 | 否 |
> | generateRecommendations | 否 |
> | 中繼資料 | 否 |
> | Mahout | 否 |
> | suppressions | 否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | actionRules | 是 |
> | alerts | 否 |
> | alertsList | 否 |
> | 警示MetaData | 否 |
> | alertsSummary | 否 |
> | alertsSummaryList | 否 |
> | smartDetectorAlertRules | 是 |
> | smartGroups | 否 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | servers | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | reportFeedback | 否 |
> | 服務 | 是 |
> | validateServiceName | 否 |

## <a name="microsoftappconfiguration"></a>微軟.應用程式設定

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 設定儲存 | 是 |
> | 設定儲存/事件網格篩選器 | 否 |

## <a name="microsoftappplatform"></a>微軟.AppPlatform

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | Spring | 是 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | attestationProviders | 否 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | classicAdministrators | 否 |
> | 資料別名 | 否 |
> | denyAssignments | 否 |
> | elevateAccess | 否 |
> | 尋找孤兒角色分配 | 否 |
> | locks | 否 |
> | 權限 | 否 |
> | policyAssignments | 否 |
> | policyDefinitions | 否 |
> | policySetDefinitions | 否 |
> | providerOperations | 否 |
> | roleAssignments | 否 |
> | 角色配置使用指標 | 否 |
> | roleDefinitions | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | automationAccounts | 是 |
> | 自動化帳戶/設定 | 是 |
> | 自動化會計或作業 | 否 |
> | 自動化帳號 /專用端接原則 | 否 |
> | 自動化帳戶/專用端點連接 | 否 |
> | 自動化帳號/私人連結資源 | 否 |
> | 自動化帳錄/執行簿 | 是 |
> | 自動化帳戶/軟體更新配置 | 否 |
> | 自動化帳戶/網路鉤子 | 否 |

## <a name="microsoftazconfig"></a>微軟.阿茲康菲格

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 設定儲存 | 是 |
> | 設定儲存/事件網格篩選器 | 否 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | environments | 否 |
> | 環境/帳戶 | 否 |
> | 環境/帳戶/命名空間 | 否 |
> | 環境/ 帳戶 / 命名空間 / 設定 | 否 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | b2cDirectories | 是 |
> | b2c租戶 | 否 |

## <a name="microsoftazuredata"></a>微軟.Azure數據

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 混合資料管理員 | 是 |
> | 後實例 | 是 |
> | sqlInstances | 是 |
> | sqlServer 註冊 | 是 |
> | sqlServer 註冊 / sqlServer | 否 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 雲清單檔 | 否 |
> | registrations | 是 |
> | 註冊/客戶訂閱 | 否 |
> | 註冊/產品 | 否 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | batchAccounts | 是 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | billingAccounts | 否 |
> | 帳單帳戶/協定 | 否 |
> | 計費帳戶/計費許可權 | 否 |
> | 帳號/帳號/帳戶/帳戶 | 否 |
> | 帳號 / 帳號設定檔 / 計費權限 | 否 |
> | 帳號 / 計費設定檔 / 計費角色分配 | 否 |
> | 帳單帳號/帳單配置檔/計費角色定義 | 否 |
> | 帳號 / 帳號設定檔 / 計費訂閱 | 否 |
> | 計費帳號 /計費設定檔 / 建立計費角色分配 | 否 |
> | 帳單帳號/帳單設定檔/客戶 | 否 |
> | 帳單帳號/帳單設定檔/說明 | 否 |
> | 帳單帳目 / 帳單設定檔 / 發票 | 否 |
> | 帳單帳目 / 帳單設定檔 / 發票 / 價格表 | 否 |
> | 帳單帳目 / 帳單設定檔 / 發票 / 交易記錄 | 否 |
> | 帳單帳目 / 帳單設定檔 / 發票節 | 否 |
> | 帳號 / 帳號設定檔 / 發票節 / 計費權限 | 否 |
> | 帳號 / 帳號/ rectccco/ stccs 設定檔 / 發票節 / 計費角色分配 | 否 |
> | 帳單科目 / 帳單設定檔 / 發票節 / 計費角色定義 | 否 |
> | 帳號 / 帳號設定檔 / 發票節 / 計費訂閱 | 否 |
> | 帳單帳目 / 帳單設定檔 / 發票節 / 建立計費角色分配 | 否 |
> | 帳號 / 帳單設定檔 / 發票節 / 啟動轉移 | 否 |
> | 帳號帳號 / 帳單設定檔 / 發票節 / 產品 | 否 |
> | 帳號 / 帳單設定檔 / 發票部份 / 產品 / 轉讓 | 否 |
> | 帳號 / 帳號/ 帳號 / 發票節 / 產品 / 更新自動續訂 | 否 |
> | 帳號 / 帳單設定檔 / 發票節/ 交易記錄 | 否 |
> | 帳號帳號 / 帳單設定檔 / 發票部份 / 轉帳 | 否 |
> | 帳單帳號 / 計費設定檔 / 修補程式操作 | 否 |
> | 帳單帳號 / 帳單設定檔 / 付款條件 | 否 |
> | 帳單帳號 /帳單設定檔/原則 | 否 |
> | 帳號帳號 / 帳單設定檔 / 價格表 | 否 |
> | 帳號/ 帳單設定檔 / 價格表下載操作 | 否 |
> | 帳單帳號 / 帳單設定檔 / 產品 | 否 |
> | 帳單帳號/帳單配置檔/交易 | 否 |
> | 計費帳戶/計費角色分配 | 否 |
> | 計費帳戶/計費角色定義 | 否 |
> | 計費帳戶/計費訂閱 | 否 |
> | 帳號/ 帳號訂閱 / 發票 | 否 |
> | 計費帳號/ 建立計費角色分配 | 否 |
> | 計費帳戶/創建發票節操作 | 否 |
> | 帳單帳號/客戶 | 否 |
> | 計費帳戶/客戶/計費許可權 | 否 |
> | 帳單帳戶/客戶/計費訂閱 | 否 |
> | 帳單帳號/客戶/啟動轉帳 | 否 |
> | 帳單帳號/客戶/政策 | 否 |
> | 帳單帳號/客戶/產品 | 否 |
> | 帳單帳號/客戶/交易 | 否 |
> | 帳單帳號/客戶/轉帳 | 否 |
> | 帳號/部門 | 否 |
> | 帳單帳目/註冊帳目 | 否 |
> | 帳單帳目/發票 | 否 |
> | 帳單科目 /發票部分 | 否 |
> | 帳號/ 發票節 / 計費訂閱移動操作 | 否 |
> | 帳號 / 發票節 / 計費訂閱 | 否 |
> | 帳號 / 發票節 / 計費訂閱 / 轉帳 | 否 |
> | 帳單科目 / 發票節 / 提升 | 否 |
> | 帳單科目 /發票節 / 啟動轉移 | 否 |
> | 帳單帳目 / 發票節 / 修補程式操作 | 否 |
> | 帳單科目 / 發票節 / 產品移動操作 | 否 |
> | 帳單科目 / 發票部份 / 產品 | 否 |
> | 帳號/ 發票部份 / 產品 / 轉移 | 否 |
> | 帳號/ 發票部份 / 產品 / 更新自動續訂 | 否 |
> | 帳單科目 / 發票部份 / 交易記錄 | 否 |
> | 帳單科目 / 發票部份 / 轉帳 | 否 |
> | 帳單帳戶/信用額度 | 否 |
> | 計費帳戶/修補程式操作 | 否 |
> | 帳單帳號/付款條件 | 否 |
> | 帳單帳戶/產品 | 否 |
> | 帳單帳號/交易 | 否 |
> | billingPeriods | 否 |
> | 計費權限 | 否 |
> | billingProperty | 否 |
> | 計費角色分配 | 否 |
> | 計費角色定義 | 否 |
> | 建立計費角色配置 | 否 |
> | departments | 否 |
> | enrollmentAccounts | 否 |
> | invoices | 否 |
> | transfers | 否 |
> | 轉讓/接受轉移 | 否 |
> | 轉移/ 拒絕轉移 | 否 |
> | 轉移/操作狀態 | 否 |
> | 傳輸/驗證傳輸 | 否 |
> | 驗證位址 | 否 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | mapApis | 是 |
> | updateCommunicationPreference | 否 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 區塊鏈成員 | 是 |
> | 臍帶成員 | 是 |
> | 觀察家 | 是 |

## <a name="microsoftblockchaintokens"></a>微軟.區塊鏈權杖

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 權杖服務 | 是 |
> | 權杖服務 / 區塊鏈網路 | 否 |
> | 權杖服務/ 群組 | 否 |
> | 權杖服務 / 群組 / 帳戶 | 否 |
> | 權杖服務/權杖範本 | 否 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | blueprintAssignments | 否 |
> | 藍圖配置/配置操作 | 否 |
> | 藍圖配置/操作 | 否 |
> | blueprints | 否 |
> | 藍圖/工件 | 否 |
> | 藍圖/版本 | 否 |
> | 藍圖 /版本 / 工件 | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | botServices | 是 |
> | 機器人服務/頻道 | 否 |
> | 機器人服務/ 連線 | 否 |
> | 語言 | 否 |
> | 範本 | 否 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | Redis | 是 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | appliedReservations | 否 |
> | 自動配額增加 | 否 |
> | 計算交換 | 否 |
> | calculatePrice | 否 |
> | 計算買入價格 | 否 |
> | catalogs | 否 |
> | commercialReservationOrders | 否 |
> | 兌換 | 否 |
> | 地點採購訂單 | 否 |
> | reservationOrders | 否 |
> | 預訂訂單/計算退款 | 否 |
> | 預訂訂單/合併 | 否 |
> | 預訂訂單/預訂 | 否 |
> | 預訂單/預訂/修訂 | 否 |
> | 預訂單/退貨 | 否 |
> | 預訂訂單/拆分 | 否 |
> | 預訂單/交換 | 否 |
> | reservations | 否 |
> | 資源提供者 | 否 |
> | resources | 否 |
> | validateReservationOrder | 否 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | CdnWeb 應用程式防火牆管理規則集 | 否 |
> | CdnWeb 應用程式防火牆原則 | 是 |
> | edgenodes | 否 |
> | 設定檔 | 是 |
> | 設定檔案/點端點 | 是 |
> | 設定檔案 / 終結點 / 自訂網域 | 否 |
> | 設定檔/終結點/原點組 | 否 |
> | 設定檔/連接點/原點 | 否 |
> | validateProbe | 否 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | certificateOrders | 是 |
> | 憑證訂單/證書 | 否 |
> | validateCertificateRegistrationInformation | 否 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | capabilities | 否 |
> | domainNames | 是 |
> | 功能變數名稱/功能 | 否 |
> | 網域名稱/內部負載平衡器 | 否 |
> | 網域名稱/服務憑證 | 否 |
> | 功能變數名稱/插槽 | 否 |
> | 網域名稱 / 插槽 / 角色 | 否 |
> | 網域名稱 / 插槽 / 角色 / 指標定義 | 否 |
> | 網域名稱 / 插槽 / 角色 / 指標 | 否 |
> | moveSubscriptionResources | 否 |
> | operatingSystemFamilies | 否 |
> | operatingSystems | 否 |
> | quotas | 否 |
> | resourceTypes | 否 |
> | validateSubscriptionMoveAvailability | 否 |
> | virtualMachines | 是 |
> | 虛擬機器/診斷設定 | 否 |
> | 虛擬機器/指標定義 | 否 |
> | 虛擬機器/指標 | 否 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | classicInfrastructureResources | 否 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | capabilities | 否 |
> | expressRouteCrossConnections | 否 |
> | 快速路由交叉連接/對等互連 | 否 |
> | gatewaySupportedDevices | 否 |
> | networkSecurityGroups | 是 |
> | quotas | 否 |
> | reservedIps | 是 |
> | virtualNetworks | 是 |
> | 虛擬網路 /遠端虛擬網路對等 Proxies | 否 |
> | 虛擬網路/虛擬網路對等 | 否 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | capabilities | 否 |
> | disks | 否 |
> | images | 否 |
> | osImages | 否 |
> | osPlatformImages | 否 |
> | publicImages | 否 |
> | quotas | 否 |
> | storageAccounts | 是 |
> | 儲存帳號/blob 服務 | 否 |
> | 儲存帳號/檔案服務 | 否 |
> | 儲存帳戶/指標定義 | 否 |
> | 儲存帳戶/指標 | 否 |
> | 儲存帳戶/佇列服務 | 否 |
> | 儲存帳戶/服務 | 否 |
> | 儲存帳號/服務 / 診斷設定 | 否 |
> | 儲存帳號/ 服務 / 指標定義 | 否 |
> | 儲存帳戶/服務 / 指標 | 否 |
> | 儲存帳戶/表服務 | 否 |
> | 儲存帳號/ vmImage | 否 |
> | vmImages | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | 是 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | RateCard | 否 |
> | UsageAggregates | 否 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | availabilitySets | 是 |
> | 磁碟加密集 | 是 |
> | disks | 是 |
> | galleries | 是 |
> | 畫廊/應用程式 | 否 |
> | 畫廊 / 應用程式 / 版本 | 否 |
> | 畫廊/圖片 | 否 |
> | 畫廊 / 影像 / 版本 | 否 |
> | 主機組 | 是 |
> | 主機組/主機 | 是 |
> | images | 是 |
> | 鄰近放置群組 | 是 |
> | restorePointCollections | 是 |
> | 還原點集合/還原點 | 否 |
> | 共用 VM 延伸 | 是 |
> | 共用VM延伸/版本 | 否 |
> | sharedVMImages | 是 |
> | 共用VM影像/版本 | 否 |
> | snapshots | 是 |
> | sshPublicKeys | 是 |
> | virtualMachines | 是 |
> | 虛擬機器/擴展 | 是 |
> | 虛擬機器/指標定義 | 否 |
> | virtualMachineScaleSets | 是 |
> | 虛擬機器表集/擴充 | 否 |
> | 虛擬機器表集/網路介面 | 否 |
> | 虛擬機器級集/公共 IP 位址 | 否 |
> | 虛擬機器規模集/虛擬機器 | 否 |
> | 虛擬機器表集/ 虛擬機器/ 網路介面 | 否 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | AggregatedCost | 否 |
> | 餘額 | 否 |
> | 預算 | 否 |
> | Charges | 否 |
> | CostTags | 否 |
> | credits | 否 |
> | 活動 | 否 |
> | 預測 | 否 |
> | lots | 否 |
> | Marketplace | 否 |
> | Pricesheets | 否 |
> | products | 否 |
> | ReservationDetails | 否 |
> | ReservationRecommendations | 否 |
> | ReservationSummaries | 否 |
> | ReservationTransactions | 否 |
> | Tags | 否 |
> | tenants | 否 |
> | 詞彙 | 否 |
> | UsageDetails | 否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | containerGroups | 是 |
> | serviceAssociationLinks | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | registries | 是 |
> | 註冊表/代理池 | 是 |
> | 註冊表/產生 | 否 |
> | 註冊表 / 產生 / 取消 | 否 |
> | 註冊表 / 產生 / 取得紀錄連結 | 否 |
> | 註冊表/產生工作 | 是 |
> | 註冊表/ 產生工作 / 步驟 | 否 |
> | 註冊表/事件網格篩選器 | 否 |
> | 註冊表/生成認證 | 否 |
> | 註冊表 / 取得編譯來源網址 | 否 |
> | 註冊/ 取得認證 | 否 |
> | 登錄表/匯入影像 | 否 |
> | 註冊表 / 私有端接連線 Proxies | 否 |
> | 註冊表 / 私有端接連線Proxies / 驗證 | 否 |
> | 註冊表/專用端點連接 | 否 |
> | 註冊/ 私人連結資源 | 否 |
> | 註冊表/佇列產生 | 否 |
> | 註冊表/再生認證 | 否 |
> | 註冊表/再生認證 | 否 |
> | 登錄表/複製 | 是 |
> | 註冊表/執行 | 否 |
> | 註冊表 / 執行 / 取消 | 否 |
> | 註冊表/計劃執行 | 否 |
> | 註冊表/範圍圖 | 否 |
> | 登錄表格表 | 是 |
> | 註冊表/任務 | 是 |
> | 註冊表/權杖 | 否 |
> | 登錄表格/更新原則 | 否 |
> | 註冊表/網鉤 | 是 |
> | 註冊表 / 網鉤 / 取得回撥 Config | 否 |
> | 註冊表 / 網鉤 / ping | 否 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | containerServices | 是 |
> | managedClusters | 是 |
> | 開放式轉移託管集群 | 是 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 警示 | 否 |
> | BillingAccounts | 否 |
> | 預算 | 否 |
> | 雲連接器 | 否 |
> | 連接器 | 是 |
> | 部門 | 否 |
> | 維度 | 否 |
> | EnrollmentAccounts | 否 |
> | 出口 | 否 |
> | 外部結算帳戶 | 否 |
> | 外部結算帳戶/警報 | 否 |
> | 外部計費帳號 / 維度 | 否 |
> | 外部結算帳戶/預測 | 否 |
> | 外部計費帳戶/查詢 | 否 |
> | 外部訂閱 | 否 |
> | 外部訂閱/警報 | 否 |
> | 外部訂閱/維度 | 否 |
> | 外部訂閱/預測 | 否 |
> | 外部訂閱/查詢 | 否 |
> | 趨勢預測 | 否 |
> | 查詢 | 否 |
> | 註冊 | 否 |
> | Reportconfigs | 否 |
> | 報表 | 否 |
> | 設定 | 否 |
> | 顯示回法規則 | 否 |
> | 檢視 | 否 |

## <a name="microsoftcustomerlockbox"></a>微軟客戶鎖定箱

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | requests | 否 |

## <a name="microsoftcustomproviders"></a>微軟.自訂提供者

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 關聯 | 否 |
> | 資源提供者 | 是 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | jobs | 是 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | 是 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | workspaces | 是 |
> | 工作區/資料庫工作區 | 否 |
> | 工作區/儲存加密 | 否 |
> | 工作區/虛擬網路對等互連 | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | catalogs | 是 |
> | 資料目錄 | 是 |
> | 資料目錄/資料來源 | 否 |
> | 資料目錄 / 資料來源 / 掃描 | 否 |
> | 資料目錄 / 資料來源 / 掃描 / 資料集 | 否 |
> | 資料目錄 / 資料來源 / 掃描 / 觸發器 | 否 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | dataFactories | 是 |
> | 資料工廠/診斷設定 | 否 |
> | 資料工廠/指標定義 | 否 |
> | dataFactorySchema | 否 |
> | factories | 是 |
> | 工廠/整合執行時間 | 否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | 帳號/資料湖儲存帳戶 | 否 |
> | 帳戶/儲存帳戶 | 否 |
> | 帳號/ 儲存帳號 / 容器 | 否 |
> | 帳號/轉帳分析單位 | 否 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | 帳號/事件網格篩選器 | 否 |
> | 帳號/防火牆規則 | 否 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 服務 | 是 |
> | 服務/專案 | 是 |

## <a name="microsoftdatashare"></a>微軟.資料共享

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | 帳戶/股份 | 否 |
> | 帳號/ 分享/ 資料集 | 否 |
> | 帳號/ 股票 / 邀請 | 否 |
> | 帳號/共用 / 提供者共用訂閱 | 否 |
> | 帳號/ 共用 / 同步設定 | 否 |
> | 帳號/共用訂閱 | 否 |
> | 帳號/ 分享訂閱 / 使用者來源資料集群組 | 否 |
> | 帳號/共用訂閱/資料集映射 | 否 |
> | 帳號/ 共用訂閱 / 觸發器 | 否 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | servers | 是 |
> | 伺服器/顧問 | 否 |
> | 伺服器/金鑰 | 否 |
> | 伺服器/專用端接連線Proxies | 否 |
> | 伺服器/專用端點連接 | 否 |
> | 伺服器/專用連結資源 | 否 |
> | 伺服器/查詢文字 | 否 |
> | 伺服器/可復原伺服器 | 否 |
> | 伺服器/頂層查詢統計資訊 | 否 |
> | 伺服器/虛擬網路規則 | 否 |
> | 伺服器/等待統計 | 否 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | servers | 是 |
> | 伺服器/顧問 | 否 |
> | 伺服器/金鑰 | 否 |
> | 伺服器/專用端接連線Proxies | 否 |
> | 伺服器/專用端點連接 | 否 |
> | 伺服器/專用連結資源 | 否 |
> | 伺服器/查詢文字 | 否 |
> | 伺服器/可復原伺服器 | 否 |
> | 伺服器/頂層查詢統計資訊 | 否 |
> | 伺服器/虛擬網路規則 | 否 |
> | 伺服器/等待統計 | 否 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 伺服器組 | 是 |
> | servers | 是 |
> | 伺服器/顧問 | 否 |
> | 伺服器/金鑰 | 否 |
> | 伺服器/專用端接連線Proxies | 否 |
> | 伺服器/專用端點連接 | 否 |
> | 伺服器/專用連結資源 | 否 |
> | 伺服器/查詢文字 | 否 |
> | 伺服器/可復原伺服器 | 否 |
> | 伺服器/頂層查詢統計資訊 | 否 |
> | 伺服器/虛擬網路規則 | 否 |
> | 伺服器/等待統計 | 否 |
> | 伺服器v2 | 是 |
> | 單伺服器 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 工件來源 | 是 |
> | rollouts | 是 |
> | 服務拓撲 | 是 |
> | 服務拓撲/服務 | 是 |
> | 服務機構 / 服務 / 服務單位 | 是 |
> | steps | 是 |

## <a name="microsoftdesktopvirtualization"></a>微軟.桌面虛擬化

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 應用程式群組 | 是 |
> | 應用程式群組/應用程式 | 否 |
> | 應用程式群組/桌面 | 否 |
> | 應用程式群組/啟動選單項目 | 否 |
> | 主機池 | 是 |
> | 主機池/工作階段主機 | 否 |
> | 主機池/工作階段主機/使用者會話 | 否 |
> | 主機池/使用者工作階段 | 否 |
> | workspaces | 是 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 彈性池 | 是 |
> | 彈性池 / IotHub租戶 | 是 |
> | 彈性池 / IotHub租戶 / 安全設定 | 否 |
> | IotHubs | 是 |
> | IotHubs / 事件網格篩選器 | 否 |
> | IotHubs / 安全設定 | 否 |
> | ProvisioningServices | 是 |
> | usages | 否 |

## <a name="microsoftdevops"></a>微軟.DevOps

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 管線中 | 是 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | controllers | 是 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | labcenters | 是 |
> | labs | 是 |
> | 實驗室/環境 | 是 |
> | 實驗室/服務執行者 | 是 |
> | 實驗室/虛擬機器 | 是 |
> | schedules | 是 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | databaseAccountNames | 否 |
> | databaseAccounts | 是 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | domains | 是 |
> | 網域 /網域擁有權識別碼 | 否 |
> | generateSsoRequest | 否 |
> | topLevelDomains | 否 |
> | validateDomainRegistrationInformation | 否 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | lcsprojects | 否 |
> | lcs 專案/雲部署 | 否 |
> | lcs 專案/ 連接器 | 否 |

## <a name="microsoftenterpriseknowledgegraph"></a>微軟.企業知識圖

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 服務 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | domains | 是 |
> | 網域/佈景主題 | 否 |
> | eventSubscriptions | 否 |
> | extensionTopics | 否 |
> | 合作夥伴命名空間 | 是 |
> | 合作夥伴命名空間/ 事件頻道 | 否 |
> | 合作夥伴註冊 | 是 |
> | 合作夥伴主題 | 是 |
> | 合作夥伴主題/活動訂閱 | 否 |
> | 系統主題 | 是 |
> | 系統主題/事件訂閱 | 否 |
> | topics | 是 |
> | topicTypes | 否 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | 命名空間 | 是 |
> | 命名空間/授權規則 | 否 |
> | 命名空間/災難修復設定 | 否 |
> | 命名空間/事件中心 | 否 |
> | 命名空間/ 事件中心 / 授權規則 | 否 |
> | 命名空間 / 事件中心 / 消費組 | 否 |
> | 命名空間/網路規則集 | 否 |

## <a name="microsoftfalcon"></a>微軟.法爾肯

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 命名空間 | 是 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 功能提供者 | 否 |
> | 特性 | 否 |
> | 提供者 | 否 |
> | 訂閱功能註冊 | 否 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | enroll | 否 |
> | galleryitems | 否 |
> | generateartifactaccessuri | 否 |
> | myareas | 否 |
> | 我的區域/地區 | 否 |
> | 我的區域 / 區域 / 區域 | 否 |
> | 我的區域 / 區域 / 區域 / 畫廊項目 | 否 |
> | 我的區域 / 區域 / 畫廊項目 | 否 |
> | 我的區域 / 畫廊專案 | 否 |
> | 註冊 | 否 |
> | resources | 否 |
> | retrieveresourcesbyid | 否 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | 是 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 自動管理帳戶 | 是 |
> | 自動管理Vm設定設定檔 | 是 |
> | 設定設定檔配置 | 否 |
> | guestConfigurationAssignments | 否 |
> | software | 否 |
> | 軟體更新設定檔 | 否 |
> | 軟體更新 | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | hanaInstances | 是 |
> | 皂顯示器 | 是 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 專用的 HSM | 是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | 叢集/應用程式 | 否 |

## <a name="microsofthealthcareapis"></a>微軟.醫療保健Apis

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 服務 | 是 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 機器 | 是 |
> | 機器/擴展 | 是 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 資料管理員 | 是 |

## <a name="microsofthydra"></a>微軟.海德拉

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | components | 是 |
> | 網路範圍 | 是 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | jobs | 是 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | diagnosticSettings | 否 |
> | diagnosticSettingsCategories | 否 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 應用範本 | 否 |
> | IoTApps | 是 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 圖形 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | deletedVaults | 否 |
> | hsmPools | 是 |
> | vaults | 是 |
> | 保存庫/存取原則 | 否 |
> | 保存庫/事件網格篩選器 | 否 |
> | 保管庫/機密 | 否 |

## <a name="microsoftkubernetes"></a>微軟.庫伯內斯

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 連接的集群 | 是 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | 叢集/附加資料庫設定 | 否 |
> | 叢集/資料庫 | 否 |
> | 叢集 / 資料庫 / 資料連線 | 否 |
> | 叢集/資料庫/事件點連接 | 否 |
> | 叢集/資料庫/委託分配 | 否 |
> | 叢集/資料連線 | 否 |
> | 叢集/主體分配 | 否 |
> | 叢集/共用實體 | 否 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | labaccounts | 是 |
> | users | 否 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | hostingEnvironments | 是 |
> | integrationAccounts | 是 |
> | 整合服務環境 | 是 |
> | 整合服務環境/託管 Apis | 是 |
> | 隔離環境 | 是 |
> | workflows | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | commitmentPlans | 是 |
> | webServices | 是 |
> | 工作區 | 是 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | workspaces | 是 |
> | 工作區/計算 | 否 |
> | 工作區/事件網格篩選器 | 否 |

## <a name="microsoftmaintenance"></a>微軟.維護

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 套用更新 | 否 |
> | 設定配置 | 否 |
> | 維護設定 | 是 |
> | 更新 | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | Identities | 否 |
> | userAssignedIdentities | 是 |

## <a name="microsoftmanagedservices"></a>微軟.託管服務

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 市場註冊定義 | 否 |
> | 註冊配置 | 否 |
> | 註冊定義 | 否 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | getEntities | 否 |
> | managementGroups | 否 |
> | 管理群組/設定 | 否 |
> | resources | 否 |
> | startTenantBackfill | 否 |
> | tenantBackfillStatus | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | 帳號/事件網格篩選器 | 否 |
> | 帳號/ 私人地圖集 | 是 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | offers | 否 |
> | offerTypes | 否 |
> | 優惠類型/發佈商 | 否 |
> | 產品型態 / 發行者 / 優惠 | 否 |
> | 優惠型態 / 發佈者 / 優惠 / 計劃 | 否 |
> | 優惠型態 / 發行者 / 優惠 / 排程 / 協定 | 否 |
> | 報價型態 / 發行者 / 優惠 / 排程 / 設定 | 否 |
> | 報價類型 / 發行者 / 優惠 / 排程 / 匯入影像 | 否 |
> | privategalleryitems | 否 |
> | 專用儲存用戶端 | 否 |
> | 私人商店 | 否 |
> | 私人商店/優惠 | 否 |
> | products | 否 |
> | 發行者 | 否 |
> | 出版商/優惠 | 否 |
> | 出版者/ 優惠 / 修訂 | 否 |
> | 註冊 | 否 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | classicDevServices | 是 |
> | updateCommunicationPreference | 否 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | agreements | 否 |
> | offertypes | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | mediaservices | 是 |
> | 媒體服務/帳戶篩選器 | 否 |
> | 媒體服務/資產 | 否 |
> | 媒體服務 / 資產 / 資產過濾器 | 否 |
> | 媒體服務/內容關鍵政策 | 否 |
> | 媒體服務/事件網格篩選器 | 否 |
> | 媒體服務 / 即時活動操作 | 否 |
> | 媒體服務 /現場活動 | 是 |
> | 媒體服務 / 現場活動 / 即時輸出 | 否 |
> | 媒體服務/即時輸出操作 | 否 |
> | 媒體服務/媒體圖 | 否 |
> | 媒體服務/流式處理 | 否 |
> | 媒體服務/流式處理端點 | 是 |
> | 媒體服務/串流定位器 | 否 |
> | 媒體服務/串流式處理原則 | 否 |
> | 媒體服務/轉換 | 否 |
> | 媒體服務 / 轉換/ 工作 | 否 |

## <a name="microsoftmicroservices4spring"></a>微軟.微服務4春天

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 應用群集 | 是 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 評估專案 | 是 |
> | migrateprojects | 是 |
> | 移動集合 | 是 |
> | projects | 是 |

## <a name="microsoftmixedreality"></a>微軟.混合實境

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 全息圖形廣播帳戶 | 是 |
> | 物件理解帳戶 | 是 |
> | 遠端成像帳戶 | 是 |
> | 空間錨定帳戶 | 是 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 淨應用帳戶 | 是 |
> | 淨應用帳號/帳戶備份 | 否 |
> | 淨應用帳號/容量池 | 是 |
> | 淨應用帳號 / 容量池 / 卷 | 是 |
> | 淨應用帳號 / 容量池 / 卷 / 快照 | 是 |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | applicationGateways | 是 |
> | 應用程式閘道Web應用程式防火牆原則 | 是 |
> | applicationSecurityGroups | 是 |
> | azureFirewallFqdnTags | 否 |
> | azureFirewalls | 是 |
> | 堡壘主機 | 是 |
> | bgpServiceCommunities | 否 |
> | connections | 是 |
> | ddosCustomPolicies | 是 |
> | ddosProtectionPlans | 是 |
> | dnsOperationStatuses | 否 |
> | dnszones | 是 |
> | dns 區域 / A | 否 |
> | dns 區域 / AAAA | 否 |
> | dns 區域 / 所有 | 否 |
> | dns 區域 / CAA | 否 |
> | dns 區域 / CNAME | 否 |
> | dns 區域 / MX | 否 |
> | dns 區域 / NS | 否 |
> | dns 區域 / PTR | 否 |
> | dns 區域 / 記錄集 | 否 |
> | dns 區域 / SOA | 否 |
> | dns 區域 / SRV | 否 |
> | dns 區域 / TXT | 否 |
> | expressRouteCircuits | 是 |
> | expressRouteCrossConnections | 是 |
> | 快遞閘道 | 是 |
> | 快速路由連接埠 | 是 |
> | expressRouteServiceProviders | 否 |
> | 防火牆策略 | 是 |
> | frontdoors | 是 |
> | 前門Web應用程式防火牆管理規則集 | 否 |
> | frontdoorWebApplicationFirewallPolicies | 是 |
> | getDnsResourceReference | 否 |
> | internalNotify | 否 |
> | loadBalancers | 是 |
> | localNetworkGateways | 是 |
> | natGateways | 是 |
> | networkIntentPolicies | 是 |
> | networkInterfaces | 是 |
> | networkProfiles | 是 |
> | networkSecurityGroups | 是 |
> | networkWatchers | 是 |
> | 網路觀察器/ 連接監視器 | 是 |
> | 網路觀察家/鏡頭 | 是 |
> | 網路觀察家 / pingMeshes | 是 |
> | p2sVpn 閘道 | 是 |
> | 私有Dns操作狀態 | 否 |
> | 私有 Dns 區塊 | 是 |
> | 私有 Dns 區 / A | 否 |
> | 專用 Dns 區 / AAAA | 否 |
> | 私有 Dns 區域 / 所有 | 否 |
> | 私有 Dns 區域 / CNAME | 否 |
> | 私人 Dns 區 / MX | 否 |
> | 私人 Dns 區 / PTR | 否 |
> | 私有 Dns 區域 / SOA | 否 |
> | 專用 Dns 區 / SRV | 否 |
> | 私人 Dns 區 / TXT | 否 |
> | 私人 Dns 區域 / 虛擬網路連結 | 是 |
> | 私有終結點 | 是 |
> | privateLinkServices | 是 |
> | publicIPAddresses | 是 |
> | publicIPPrefixes | 是 |
> | routeFilters | 是 |
> | routeTables | 是 |
> | serviceEndpointPolicies | 是 |
> | trafficManagerGeographicHierarchies | 否 |
> | trafficmanagerprofiles | 是 |
> | 交通經理概況/熱圖 | 否 |
> | 流量管理員使用者指標鍵 | 否 |
> | virtualHubs | 是 |
> | virtualNetworkGateways | 是 |
> | virtualNetworks | 是 |
> | virtualNetworkTaps | 是 |
> | virtualWans | 是 |
> | vpnGateways | 是 |
> | vpnSites | 是 |
> | webApplicationFirewallPolicies | 是 |

## <a name="microsoftnotebooks"></a>微軟.筆記本

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 筆記本 | 否 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 命名空間 | 是 |
> | 命名空間/通知中心 | 是 |

## <a name="microsoftobjectstore"></a>微軟.物件商店

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | os 命名空間 | 是 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 超級網站 | 是 |
> | 匯入網站 | 是 |
> | 伺服器網站 | 是 |
> | VMwareSites | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | linkTargets | 否 |
> | storageInsightConfigs | 否 |
> | workspaces | 是 |
> | 工作區/資料匯出 | 否 |
> | 工作區/資料來源 | 否 |
> | 工作區/連結服務 | 否 |
> | 工作區/連結的儲存帳戶 | 否 |
> | 工作區/查詢 | 否 |
> | 工作區/作用域私人連結 Proxies | 否 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | managementassociations | 否 |
> | managementconfigurations | 是 |
> | solutions | 是 |
> | 檢視 | 是 |

## <a name="microsoftpeering"></a>微軟.對等

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 傳統對等互連 | 否 |
> | 對等Asns | 否 |
> | 對等互連 | 是 |
> | 對等服務國家 | 否 |
> | 對等服務提供者 | 否 |
> | 對等服務 | 是 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | policyEvents | 否 |
> | 原則中繼資料 | 否 |
> | policyStates | 否 |
> | policyTrackedResources | 否 |
> | remediations | 否 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | consoles | 否 |
> | dashboards | 是 |
> | userSettings | 否 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | workspaceCollections | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | capacities | 是 |

## <a name="microsoftprojectbabylon"></a>微軟.專案巴比隆

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | 是 |

## <a name="microsoftquantum"></a>微軟.昆騰

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 工作區 | 是 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | backupProtectedItems | 否 |
> | vaults | 是 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 命名空間 | 是 |
> | 命名空間/授權規則 | 否 |
> | 命名空間/混合連線 | 否 |
> | 命名空間 /混合連線/授權規則 | 否 |
> | 命名空間 / wcfrelay | 否 |
> | 命名空間 / wcfrelay / 授權規則 | 否 |

## <a name="microsoftremoteapp"></a>微軟.遠端應用程式

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | 否 |
> | 集合 | 是 |
> | 集合/應用程式 | 否 |
> | 集合/安全主體 | 否 |
> | 樣本影像 | 否 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 查詢 | 是 |
> | 資源變更詳細資訊 | 否 |
> | 資源變更 | 否 |
> | resources | 否 |
> | 資源歷史 | 否 |
> | subscriptionsStatus | 否 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | availabilityStatuses | 否 |
> | childAvailabilityStatuses | 否 |
> | childResources | 否 |
> | 新興問題 | 否 |
> | 活動 | 否 |
> | impactedResources | 否 |
> | 中繼資料 | 否 |
> | 通知 | 否 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | deployments | 否 |
> | 部署/操作 | 否 |
> | 部署文稿 | 是 |
> | 部署文稿/紀錄 | 否 |
> | 連結 | 否 |
> | notifyResourceJobs | 否 |
> | 提供者 | 否 |
> | resourceGroups | 否 |
> | subscriptions | 否 |
> | tenants | 否 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 應用程式所需 | 是 |
> | saasresources | 否 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | resourceHealthMetadata | 否 |
> | searchServices | 是 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 自我適應網路強化 | 否 |
> | advancedThreatProtectionSettings | 否 |
> | alerts | 否 |
> | allowedConnections | 否 |
> | applicationWhitelistings | 否 |
> | 評估中繼資料 | 否 |
> | 評量 | 否 |
> | 自動關閉警示規則 | 否 |
> | 自動化 | 是 |
> | AutoProvisioningSettings | 否 |
> | Compliances | 否 |
> | dataCollectionAgents | 否 |
> | 裝置安全組 | 否 |
> | discoveredSecuritySolutions | 否 |
> | externalSecuritySolutions | 否 |
> | InformationProtectionPolicies | 否 |
> | iot安全解決方案 | 是 |
> | iot 安全解決方案 / 分析模型 | 否 |
> | iot 安全解決方案 / 分析模型 / 聚合警報 | 否 |
> | iot 安全解決方案 / 分析模型 / 匯總建議 | 否 |
> | jitNetworkAccessPolicies | 否 |
> | 網路資料 | 否 |
> | 原則 | 否 |
> | pricings | 否 |
> | 監管合規標準 | 否 |
> | 法規合規性標準/法規合規性控制 | 否 |
> | 法規合規性標準/法規合規性控制/法規合規性評估 | 否 |
> | 安全分割控制定義 | 否 |
> | 安全分割控制 | 否 |
> | 安全分數 | 否 |
> | 安全分數/安全分數控制 | 否 |
> | securityContacts | 否 |
> | securitySolutions | 否 |
> | securitySolutionsReferenceData | 否 |
> | securityStatuses | 否 |
> | securityStatusesSummaries | 否 |
> | 伺服器漏洞評估 | 否 |
> | settings | 否 |
> | 子評估 | 否 |
> | 工作 | 否 |
> | topologies | 否 |
> | workspaceSettings | 否 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | diagnosticSettings | 否 |
> | diagnosticSettingsCategories | 否 |

## <a name="microsoftsecurityinsights"></a>微軟.安全洞察

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | aggregations | 否 |
> | 警示規則 | 否 |
> | 警示規則樣本 | 否 |
> | 書籤 | 否 |
> | 案例 | 否 |
> | 資料連接器 | 否 |
> | 資料連接器檢查要求 | 否 |
> | 實體 | 否 |
> | 實體查詢 | 否 |
> | 事件 | 否 |
> | 辦公室同意 | 否 |
> | settings | 否 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 命名空間 | 是 |
> | 命名空間/授權規則 | 否 |
> | 命名空間/災難修復設定 | 否 |
> | 命名空間/事件網格篩選器 | 否 |
> | 命名空間/網路規則集 | 否 |
> | 命名空間/佇列 | 否 |
> | 命名空間/佇列/授權規則 | 否 |
> | 命名空間/佈景主題 | 否 |
> | 命名空間/主題/授權規則 | 否 |
> | 命名空間 / 佈景主題 / 訂閱 | 否 |
> | 命名空間 / 佈景主題 / 訂閱 / 規則 | 否 |
> | premiumMessagingRegions | 否 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 應用程式所需 | 是 |
> | clusters | 是 |
> | 叢集/應用程式 | 否 |
> | containerGroups | 是 |
> | 容器群組集 | 是 |
> | edgeclusters | 是 |
> | 邊緣叢集/應用程式 | 否 |
> | managedclusters | 是 |
> | 託管叢集/節點類型 | 否 |
> | networks | 是 |
> | secretstores | 是 |
> | 秘密商店/證書 | 否 |
> | 秘密商店/秘密 | 否 |
> | 磁碟區 | 是 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 應用程式所需 | 是 |
> | containerGroups | 是 |
> | gateways | 是 |
> | networks | 是 |
> | 密碼 | 是 |
> | 磁碟區 | 是 |

## <a name="microsoftservices"></a>微軟.服務

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | providerRegistrations | 否 |
> | 提供者註冊/資源類型註冊 | 否 |
> | rollouts | 是 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | SignalR | 是 |
> | 信號R / 事件網格濾波器 | 否 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | SiteRecoveryVault | 是 |

## <a name="microsoftsoftwareplan"></a>微軟.軟體計劃

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 混合使用優勢 | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | applicationDefinitions | 是 |
> | 應用程式所需 | 是 |
> | jitRequests | 是 |

## <a name="microsoftspoolservice"></a>微軟.拼車服務

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 註冊訂閱 | 否 |
> | 線軸 | 是 |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | managedInstances | 是 |
> | 託管實例/資料庫 | 是 |
> | 託管實例/資料庫/備份短期保留原則 | 否 |
> | 託管實例 / 資料庫 / 架構 / 表 / 欄/ 敏感度標籤 | 否 |
> | 託管實例 / 資料庫 / 漏洞評估 | 否 |
> | 託管實例 / 資料庫 / 漏洞評估 / 規則 / 基線 | 否 |
> | 託管實例/加密保護器 | 否 |
> | 託管實例/金鑰 | 否 |
> | 託管實例/可還原丟棄資料庫/備份短期保留策略 | 否 |
> | 託管實例/漏洞評估 | 否 |
> | servers | 是 |
> | 伺服器/管理員 | 否 |
> | 伺服器/通訊連結 | 否 |
> | 伺服器/資料庫 | 是 |
> | 伺服器/加密保護器 | 否 |
> | 伺服器/防火牆規則 | 否 |
> | 伺服器/金鑰 | 否 |
> | 伺服器/可恢復丟棄資料庫 | 否 |
> | 伺服器/服務目標 | 否 |
> | 伺服器/ tde 憑證 | 否 |
> | 虛擬叢集 | 否 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | 是 |
> | Sql虛擬電腦群組/可用性組偵聽器 | 否 |
> | SqlVirtualMachines | 是 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | storageAccounts | 是 |
> | 儲存帳號/blob 服務 | 否 |
> | 儲存帳號/檔案服務 | 否 |
> | 儲存帳戶/佇列服務 | 否 |
> | 儲存帳戶/服務 | 否 |
> | 儲存帳號/ 服務 / 指標定義 | 否 |
> | 儲存帳戶/表服務 | 否 |
> | usages | 否 |

## <a name="microsoftstoragecache"></a>微軟儲存快取

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 快取 | 是 |
> | 快取/儲存目標 | 否 |
> | 使用模型 | 否 |

## <a name="microsoftstoragereplication"></a>微軟儲存複製

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 複製群組 | 否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | storageSyncServices | 是 |
> | 儲存同步服務/註冊伺服器 | 否 |
> | 儲存同步服務/同步群組 | 否 |
> | 儲存同步服務 / 同步群組 / 雲終結點 | 否 |
> | 儲存同步服務/ 同步群組 / 伺服器終結點 | 否 |
> | 儲存同步服務/工作流 | 否 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | storageSyncServices | 是 |
> | 儲存同步服務/註冊伺服器 | 否 |
> | 儲存同步服務/同步群組 | 否 |
> | 儲存同步服務 / 同步群組 / 雲終結點 | 否 |
> | 儲存同步服務/ 同步群組 / 伺服器終結點 | 否 |
> | 儲存同步服務/工作流 | 否 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | storageSyncServices | 是 |
> | 儲存同步服務/註冊伺服器 | 否 |
> | 儲存同步服務/同步群組 | 否 |
> | 儲存同步服務 / 同步群組 / 雲終結點 | 否 |
> | 儲存同步服務/ 同步群組 / 伺服器終結點 | 否 |
> | 儲存同步服務/工作流 | 否 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | managers | 是 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | streamingjobs | 是 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | cancel | 否 |
> | CreateSubscription | 否 |
> | enable | 否 |
> | 重新命名 | 否 |
> | SubscriptionDefinitions | 否 |
> | SubscriptionOperations | 否 |
> | subscriptions | 否 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | environments | 是 |
> | 環境/存取原則 | 否 |
> | 環境/事件來源 | 是 |
> | 環境/參考資料集集 | 是 |

## <a name="microsoftvmwarecloudsimple"></a>微軟.VMware雲簡單

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 專用雲節點 | 是 |
> | 專用雲服務 | 是 |
> | virtualMachines | 是 |

## <a name="microsoftvnfmanager"></a>微軟.Vnf管理員

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | devices | 是 |
> | 註冊訂閱 | 否 |
> | 廠商 | 否 |
> | 供應商 / 斯克烏斯 | 否 |
> | 供應商/ vnfs | 否 |
> | 虛擬網路功能 | 否 |
> | 文夫斯 | 是 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | apiManagementAccounts | 否 |
> | api管理帳號 / apiAcl | 否 |
> | api管理帳號 / aapi | 否 |
> | api管理帳號 / aapi / apiAcls | 否 |
> | api管理帳號 / apis / 連接Acl | 否 |
> | api管理帳號 / apis / 連接 | 否 |
> | api管理帳號 / apis / 連接/ 連線Acls | 否 |
> | api管理帳號 / ais / 當地語系化定義 | 否 |
> | api管理帳戶/ 連線Acl | 否 |
> | api管理帳號/連線 | 否 |
> | billingMeters | 否 |
> | certificates | 是 |
> | connectionGateways | 是 |
> | connections | 是 |
> | customApis | 是 |
> | deletedSites | 否 |
> | hostingEnvironments | 是 |
> | 託管環境/事件網格篩選器 | 否 |
> | 託管環境/多角色池 | 否 |
> | 託管環境/輔助池 | 否 |
> | 庫貝環境 | 是 |
> | publishingUsers | 否 |
> | Mahout | 否 |
> | resourceHealthMetadata | 否 |
> | runtimes | 否 |
> | serverFarms | 是 |
> | 伺服器農場/事件網格篩選器 | 否 |
> | sites | 是 |
> | 網站/設定  | 否 |
> | 網站/事件網格篩選器 | 否 |
> | 網站/主機名綁定 | 否 |
> | 網站 /網路設定 | 否 |
> | 網站/ 首播 | 是 |
> | 網站/插槽 | 是 |
> | 網站 / 插槽 / 事件網格篩選器 | 否 |
> | 網站/ 插槽 / 主機名稱繫結 | 否 |
> | 網站/ 插槽 / 網路設定 | 否 |
> | sourceControls | 否 |
> | 靜態網站 | 是 |
> | validate | 否 |
> | verifyHostingEnvironmentVnet | 否 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | diagnosticSettings | 否 |
> | diagnosticSettingsCategories | 否 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | DeviceServices | 是 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | components | 否 |
> | componentsSummary | 否 |
> | monitorInstances | 否 |
> | monitorInstancesSummary | 否 |
> | monitors | 否 |
> | notificationSettings | 否 |

## <a name="next-steps"></a>後續步驟

若要以逗點分隔值檔案的形式取得相同資料，請下載 [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)。
