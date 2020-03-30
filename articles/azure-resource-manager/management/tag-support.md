---
title: 標記資源支援
description: 顯示哪些 Azure 資源類型支援標記。 提供所有 Azure 服務的詳細資料。
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 6100c667c7df0b3e1740777565d260af9fa818a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657568"
---
# <a name="tag-support-for-azure-resources"></a>Azure 資源的標記支援
本文將說明資源類型是否支援[標記](tag-resources.md)。 標記為 **"支援"標記**的列指示資源類型是否具有標記的屬性。 成本**報表中標記為"標記"的**列指示該資源類型是否將標記傳遞到成本報表。 您可以在[成本管理成本分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options)以及[Azure 計費發票和每日使用方式資料](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)中按標記查看成本。

若要以逗號區隔值檔案的形式取得相同資料，請下載 [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)。

跳轉到資來源提供者命名空間：
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [微軟.應用程式佈建](#microsoftappconfiguration)
> - [微軟.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [微軟.授權](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [微軟.阿茲康菲格](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [微軟.Azure資料](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
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
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [微軟.客戶鎖定箱](#microsoftcustomerlockbox)
> - [微軟.自訂提供程式](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [微軟.資料共用](#microsoftdatashare)
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
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
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
> - [微軟.混合現實](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [微軟.網路](#microsoftnetwork)
> - [微軟.筆記本](#microsoftnotebooks)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [微軟.物件商店](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [微軟.運營管理](#microsoftoperationsmanagement)
> - [微軟.對等](#microsoftpeering)
> - [微軟.政策見解](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [微軟.專案巴比靈斯](#microsoftprojectbabylon)
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
> - [微軟.軟體計畫](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [微軟.拼車服務](#microsoftspoolservice)
> - [微軟.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [微軟.存儲緩存](#microsoftstoragecache)
> - [微軟.存儲複製](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [微軟.VMware雲簡單](#microsoftvmwarecloudsimple)
> - [微軟.Vnf管理器](#microsoftvnfmanager)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | DomainServices | 是 | 是 |
> | 域服務/ ou容器 | 否 | 否 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | supportProviders | 否 | 否 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | 否 | 否 |
> | addsservices | 否 | 否 |
> | agents | 否 | 否 |
> | anonymousapiusers | 否 | 否 |
> | 組態 | 否 | 否 |
> | 記錄 | 否 | 否 |
> | reports | 否 | 否 |
> | servicehealthmetrics | 否 | 否 |
> | 服務 | 否 | 否 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 組態 | 否 | 否 |
> | generateRecommendations | 否 | 否 |
> | 中繼資料 | 否 | 否 |
> | Mahout | 否 | 否 |
> | suppressions | 否 | 否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | actionRules | 是 | 是 |
> | alerts | 否 | 否 |
> | alertsList | 否 | 否 |
> | 警報MetaData | 否 | 否 |
> | alertsSummary | 否 | 否 |
> | alertsSummaryList | 否 | 否 |
> | 意見回應 | 否 | 否 |
> | smartDetectorAlertRules | 是 | 是 |
> | smartDetectorRuntimeEnvironments | 否 | 否 |
> | smartGroups | 否 | 否 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | servers | 是 | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | reportFeedback | 否 | 否 |
> | 服務 | 是 | 是 |
> | validateServiceName | 否 | 否 |

## <a name="microsoftappconfiguration"></a>微軟.應用程式佈建

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 配置存儲 | 是 | 是 |
> | 配置存儲/事件網格篩選器 | 否 | 否 |

## <a name="microsoftappplatform"></a>微軟.AppPlatform

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | Spring | 是 | 是 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | attestationProviders | 否 | 否 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | 否 | 否 |
> | 資料別名 | 否 | 否 |
> | denyAssignments | 否 | 否 |
> | elevateAccess | 否 | 否 |
> | 查找孤兒角色指派 | 否 | 否 |
> | locks | 否 | 否 |
> | 權限 | 否 | 否 |
> | policyAssignments | 否 | 否 |
> | policyDefinitions | 否 | 否 |
> | policySetDefinitions | 否 | 否 |
> | providerOperations | 否 | 否 |
> | roleAssignments | 否 | 否 |
> | 角色指派使用指標 | 否 | 否 |
> | roleDefinitions | 否 | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | automationAccounts | 是 | 是 |
> | 自動化帳戶/配置 | 是 | 是 |
> | 自動化會計/作業 | 否 | 否 |
> | 自動化帳戶 /專用端接策略 | 否 | 否 |
> | 自動化帳戶/專用端點連接 | 否 | 否 |
> | 自動化帳戶/私人連結資源 | 否 | 否 |
> | 自動化帳簿/運行簿 | 是 | 是 |
> | 自動化帳戶/軟體更新配置 | 否 | 否 |
> | 自動化帳戶/網路鉤子 | 否 | 否 |

## <a name="microsoftazconfig"></a>微軟.阿茲康菲格

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 配置存儲 | 是 | 是 |
> | 配置存儲/事件網格篩選器 | 否 | 否 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | environments | 否 | 否 |
> | 環境/帳戶 | 否 | 否 |
> | 環境/帳戶/命名空間 | 否 | 否 |
> | 環境 / 帳戶 / 命名空間 / 配置 | 否 | 否 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | 是 | 否 |
> | b2c租戶 | 否 | 否 |

## <a name="microsoftazuredata"></a>微軟.Azure資料

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 混合資料管理器 | 是 | 是 |
> | 後實例 | 是 | 是 |
> | sqlBigData 集群 | 是 | 是 |
> | sqlInstances | 是 | 是 |
> | sqlServer 註冊 | 是 | 是 |
> | sqlServer 註冊 / sqlServer | 否 | 否 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | registrations | 是 | 是 |
> | 註冊/客戶訂閱 | 否 | 否 |
> | 註冊/產品 | 否 | 否 |
> | 驗證金鑰 | 否 | 否 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | batchAccounts | 是 | 是 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | billingAccounts | 否 | 否 |
> | 帳單帳戶/協定 | 否 | 否 |
> | 計費帳戶/計費許可權 | 否 | 否 |
> | 帳單帳戶/帳單設定檔 | 否 | 否 |
> | 帳單帳戶 / 帳單設定檔 / 計費許可權 | 否 | 否 |
> | 帳單帳戶 / 計費設定檔 / 計費角色指派 | 否 | 否 |
> | 帳單帳戶/帳單設定檔/計費角色定義 | 否 | 否 |
> | 帳單帳戶 / 帳單設定檔 / 計費訂閱 | 否 | 否 |
> | 計費帳戶 /計費設定檔 / 創建計費角色指派 | 否 | 否 |
> | 帳單帳戶/帳單設定檔/客戶 | 否 | 否 |
> | 帳單帳戶/帳單設定檔/說明 | 否 | 否 |
> | 帳單帳目 / 帳單設定檔 / 發票 | 否 | 否 |
> | 帳單帳目 / 帳單設定檔 / 發票 / 價格表 | 否 | 否 |
> | 帳單帳目 / 帳單設定檔 / 發票 / 交易記錄 | 否 | 否 |
> | 帳單帳目 / 帳單設定檔 / 發票節 | 否 | 否 |
> | 帳單帳戶 / 帳單設定檔 / 發票節 / 計費許可權 | 否 | 否 |
> | 帳單帳戶 / 帳單設定檔 / 發票節 / 計費角色指派 | 否 | 否 |
> | 帳單科目 / 帳單設定檔 / 發票節 / 計費角色定義 | 否 | 否 |
> | 帳單帳戶 / 帳單設定檔 / 發票節 / 計費訂閱 | 否 | 否 |
> | 帳單帳目 / 帳單設定檔 / 發票節 / 創建計費角色指派 | 否 | 否 |
> | 帳單帳戶 / 帳單設定檔 / 發票節 / 啟動轉移 | 否 | 否 |
> | 帳單帳戶 / 帳單設定檔 / 發票節 / 產品 | 否 | 否 |
> | 帳單帳戶 / 帳單設定檔 / 發票部分 / 產品 / 轉讓 | 否 | 否 |
> | 帳單帳戶 / 帳單設定檔 / 發票節 / 產品 / 更新自動續訂 | 否 | 否 |
> | 帳單帳戶 / 帳單設定檔 / 發票節/ 交易記錄 | 否 | 否 |
> | 帳單帳戶 / 帳單設定檔 / 發票部分 / 轉帳 | 否 | 否 |
> | 帳單帳戶 / 計費設定檔 / 修補程式操作 | 否 | 否 |
> | 帳單帳戶 / 帳單設定檔 / 付款條件 | 否 | 否 |
> | 帳單帳戶 /帳單設定檔/策略 | 否 | 否 |
> | 帳單帳戶 / 帳單設定檔 / 價格表 | 否 | 否 |
> | 帳單帳戶 / 帳單設定檔 / 價格表下載操作 | 否 | 否 |
> | 帳單帳戶 / 帳單設定檔 / 產品 | 否 | 否 |
> | 帳單帳戶/帳單設定檔/交易 | 否 | 否 |
> | 計費帳戶/計費角色指派 | 否 | 否 |
> | 計費帳戶/計費角色定義 | 否 | 否 |
> | 計費帳戶/計費訂閱 | 否 | 否 |
> | 帳單帳戶 / 帳單訂閱 / 發票 | 否 | 否 |
> | 計費帳戶/ 創建計費角色指派 | 否 | 否 |
> | 計費帳戶/創建發票節操作 | 否 | 否 |
> | 帳單帳戶/客戶 | 否 | 否 |
> | 計費帳戶/客戶/計費許可權 | 否 | 否 |
> | 帳單帳戶/客戶/計費訂閱 | 否 | 否 |
> | 帳單帳戶/客戶/啟動轉帳 | 否 | 否 |
> | 帳單帳戶/客戶/政策 | 否 | 否 |
> | 帳單帳戶/客戶/產品 | 否 | 否 |
> | 帳單帳戶/客戶/交易 | 否 | 否 |
> | 帳單帳戶/客戶/轉帳 | 否 | 否 |
> | 帳單帳戶/部門 | 否 | 否 |
> | 帳單帳目/註冊帳目 | 否 | 否 |
> | 帳單帳目/發票 | 否 | 否 |
> | 帳單科目 /發票部分 | 否 | 否 |
> | 帳單帳戶 / 發票節 / 計費訂閱移動操作 | 否 | 否 |
> | 帳單帳戶 / 發票節 / 計費訂閱 | 否 | 否 |
> | 帳單帳戶 / 發票節 / 計費訂閱 / 轉帳 | 否 | 否 |
> | 帳單科目 / 發票節 / 提升 | 否 | 否 |
> | 帳單科目 /發票節 / 啟動轉移 | 否 | 否 |
> | 帳單帳目 / 發票節 / 修補程式操作 | 否 | 否 |
> | 帳單科目 / 發票節 / 產品移動操作 | 否 | 否 |
> | 帳單科目 / 發票部分 / 產品 | 否 | 否 |
> | 帳單帳戶 / 發票部分 / 產品 / 轉移 | 否 | 否 |
> | 帳單帳戶 / 發票部分 / 產品 / 更新自動續訂 | 否 | 否 |
> | 帳單科目 / 發票部分 / 交易記錄 | 否 | 否 |
> | 帳單科目 / 發票部分 / 轉帳 | 否 | 否 |
> | 帳單帳戶/信用額度 | 否 | 否 |
> | 計費帳戶/修補程式操作 | 否 | 否 |
> | 帳單帳戶/付款條件 | 否 | 否 |
> | 帳單帳戶/產品 | 否 | 否 |
> | 帳單帳戶/交易 | 否 | 否 |
> | billingPeriods | 否 | 否 |
> | 計費許可權 | 否 | 否 |
> | billingProperty | 否 | 否 |
> | 計費角色指派 | 否 | 否 |
> | 計費角色定義 | 否 | 否 |
> | 創建計費角色指派 | 否 | 否 |
> | departments | 否 | 否 |
> | enrollmentAccounts | 否 | 否 |
> | invoices | 否 | 否 |
> | transfers | 否 | 否 |
> | 轉讓/接受轉移 | 否 | 否 |
> | 轉移/拒絕轉移 | 否 | 否 |
> | 轉移/操作狀態 | 否 | 否 |
> | 傳輸/驗證傳輸 | 否 | 否 |
> | 驗證位址 | 否 | 否 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | mapApis | 是 | 是 |
> | updateCommunicationPreference | 否 | 否 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 區塊鏈成員 | 是 | 是 |
> | 臍帶成員 | 是 | 是 |
> | 觀察家 | 是 | 是 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | 否 | 否 |
> | 藍圖分配/分配操作 | 否 | 否 |
> | 藍圖分配/操作 | 否 | 否 |
> | blueprints | 否 | 否 |
> | 藍圖/工件 | 否 | 否 |
> | 藍圖/版本 | 否 | 否 |
> | 藍圖 /版本 / 工件 | 否 | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | botServices | 是 | 是 |
> | 機器人服務/頻道 | 否 | 否 |
> | 機器人服務/ 連接 | 否 | 否 |
> | 語言 | 否 | 否 |
> | 範本 | 否 | 否 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | Redis | 是 | 是 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | appliedReservations | 否 | 否 |
> | 自動配額增加 | 否 | 否 |
> | 計算交換 | 否 | 否 |
> | calculatePrice | 否 | 否 |
> | 計算買入價格 | 否 | 否 |
> | catalogs | 否 | 否 |
> | commercialReservationOrders | 否 | 否 |
> | 兌換 | 否 | 否 |
> | 地點採購訂單 | 否 | 否 |
> | reservationOrders | 否 | 否 |
> | 預訂訂單/計算退款 | 否 | 否 |
> | 預訂訂單/合併 | 否 | 否 |
> | 預訂訂單/預訂 | 否 | 否 |
> | 預訂單/預訂/修訂 | 否 | 否 |
> | 預訂單/退貨 | 否 | 否 |
> | 預訂訂單/拆分 | 否 | 否 |
> | 預訂單/交換 | 否 | 否 |
> | reservations | 否 | 否 |
> | 資源提供者 | 否 | 否 |
> | resources | 否 | 否 |
> | validateReservationOrder | 否 | 否 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | CdnWeb應用程式防火牆管理規則集 | 否 | 否 |
> | CdnWeb應用程式防火牆策略 | 是 | 是 |
> | edgenodes | 否 | 否 |
> | 設定檔 | 是 | 是 |
> | 設定檔/端點 | 是 | 是 |
> | 設定檔 / 終結點 / 自訂域 | 否 | 否 |
> | 設定檔/端點/原點 | 否 | 否 |
> | validateProbe | 否 | 否 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | certificateOrders | 是 | 是 |
> | 證書訂單/證書 | 否 | 否 |
> | validateCertificateRegistrationInformation | 否 | 否 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | capabilities | 否 | 否 |
> | domainNames | 否 | 否 |
> | 功能變數名稱/功能 | 否 | 否 |
> | 功能變數名稱/內部負載平衡器 | 否 | 否 |
> | 功能變數名稱/服務證書 | 否 | 否 |
> | 功能變數名稱/插槽 | 否 | 否 |
> | 功能變數名稱 / 插槽 / 角色 | 否 | 否 |
> | 功能變數名稱 / 插槽 / 角色 / 指標定義 | 否 | 否 |
> | 功能變數名稱 / 插槽 / 角色 / 指標 | 否 | 否 |
> | moveSubscriptionResources | 否 | 否 |
> | operatingSystemFamilies | 否 | 否 |
> | operatingSystems | 否 | 否 |
> | quotas | 否 | 否 |
> | resourceTypes | 否 | 否 |
> | validateSubscriptionMoveAvailability | 否 | 否 |
> | virtualMachines | 否 | 否 |
> | 虛擬機器/診斷設置 | 否 | 否 |
> | 虛擬機器/指標定義 | 否 | 否 |
> | 虛擬機器/指標 | 否 | 否 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | 否 | 否 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | capabilities | 否 | 否 |
> | expressRouteCrossConnections | 否 | 否 |
> | 快速路由交叉連接/對等互連 | 否 | 否 |
> | gatewaySupportedDevices | 否 | 否 |
> | networkSecurityGroups | 否 | 否 |
> | quotas | 否 | 否 |
> | reservedIps | 否 | 否 |
> | virtualNetworks | 否 | 否 |
> | 虛擬網路 /遠端虛擬網路對等 Proxies | 否 | 否 |
> | 虛擬網路/虛擬網路對等 | 否 | 否 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | capabilities | 否 | 否 |
> | disks | 否 | 否 |
> | images | 否 | 否 |
> | osImages | 否 | 否 |
> | osPlatformImages | 否 | 否 |
> | publicImages | 否 | 否 |
> | quotas | 否 | 否 |
> | storageAccounts | 否 | 否 |
> | 存儲帳戶/blob 服務 | 否 | 否 |
> | 存儲帳戶/檔服務 | 否 | 否 |
> | 存儲帳戶/指標定義 | 否 | 否 |
> | 存儲帳戶/指標 | 否 | 否 |
> | 存儲帳戶/佇列服務 | 否 | 否 |
> | 存儲帳戶/服務 | 否 | 否 |
> | 存儲帳戶/服務 / 診斷設置 | 否 | 否 |
> | 存儲帳戶 / 服務 / 指標定義 | 否 | 否 |
> | 存儲帳戶/服務 / 指標 | 否 | 否 |
> | 存儲帳戶/表服務 | 否 | 否 |
> | 存儲帳戶/ vmImage | 否 | 否 |
> | vmImages | 否 | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | RateCard | 否 | 否 |
> | UsageAggregates | 否 | 否 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | availabilitySets | 是 | 是 |
> | 磁片加密集 | 是 | 是 |
> | disks | 是 | 是 |
> | galleries | 是 | 是 |
> | 畫廊/應用程式 | 否 | 否 |
> | 畫廊 / 應用程式 / 版本 | 否 | 否 |
> | 畫廊/圖片 | 否 | 否 |
> | 畫廊 / 圖像 / 版本 | 否 | 否 |
> | 主機組 | 是 | 是 |
> | 主機組/主機 | 是 | 是 |
> | images | 是 | 是 |
> | 鄰近放置組 | 是 | 是 |
> | restorePointCollections | 是 | 是 |
> | 還原點集合/還原點 | 否 | 否 |
> | sharedVMImages | 是 | 是 |
> | 共用VM圖像/版本 | 否 | 否 |
> | snapshots | 是 | 是 |
> | virtualMachines | 是 | 是 |
> | 虛擬機器/擴展 | 是 | 是 |
> | 虛擬機器/指標定義 | 否 | 否 |
> | virtualMachineScaleSets | 是 | 是 |
> | 虛擬機器級表集/擴展 | 否 | 否 |
> | 虛擬機器級表集/網路介面 | 否 | 否 |
> | 虛擬機器級集/公共 IP 位址 | 否 | 否 |
> | 虛擬機器規模集/虛擬機器 | 否 | 否 |
> | 虛擬機器級表集 / 虛擬機器 / 網路介面 | 否 | 否 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | 否 | 否 |
> | 餘額 | 否 | 否 |
> | 預算 | 否 | 否 |
> | Charges | 否 | 否 |
> | CostTags | 否 | 否 |
> | credits | 否 | 否 |
> | 活動 | 否 | 否 |
> | 預測 | 否 | 否 |
> | lots | 否 | 否 |
> | Marketplace | 否 | 否 |
> | Pricesheets | 否 | 否 |
> | products | 否 | 否 |
> | ReservationDetails | 否 | 否 |
> | ReservationRecommendations | 否 | 否 |
> | ReservationSummaries | 否 | 否 |
> | ReservationTransactions | 否 | 否 |
> | Tags | 否 | 否 |
> | tenants | 否 | 否 |
> | 詞彙 | 否 | 否 |
> | UsageDetails | 否 | 否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | containerGroups | 是 | 是 |
> | serviceAssociationLinks | 否 | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | registries | 是 | 是 |
> | 註冊表/生成 | 否 | 否 |
> | 註冊表 / 生成 / 取消 | 否 | 否 |
> | 註冊表 / 生成 / 獲取日誌連結 | 否 | 否 |
> | 註冊表/生成任務 | 是 | 是 |
> | 註冊表/ 生成任務 / 步驟 | 否 | 否 |
> | 註冊表/事件網格篩選器 | 否 | 否 |
> | 註冊表/生成憑據 | 否 | 否 |
> | 註冊表 / 獲取構建源Url | 否 | 否 |
> | 註冊/ 獲取憑據 | 否 | 否 |
> | 註冊表/導入圖像 | 否 | 否 |
> | 註冊表 / 私有端接連接 Proxies | 否 | 否 |
> | 註冊表 / 私有端接連接Proxies / 驗證 | 否 | 否 |
> | 註冊表/專用端點連接 | 否 | 否 |
> | 註冊/ 私人連結資源 | 否 | 否 |
> | 註冊表/佇列生成 | 否 | 否 |
> | 註冊表/再生憑據 | 否 | 否 |
> | 註冊表/再生憑據 | 否 | 否 |
> | 註冊表/複製 | 是 | 是 |
> | 註冊表/運行 | 否 | 否 |
> | 註冊表 / 運行 / 取消 | 否 | 否 |
> | 註冊表/計畫運行 | 否 | 否 |
> | 註冊表/範圍圖 | 否 | 否 |
> | 註冊表/任務運行 | 是 | 是 |
> | 註冊表/任務 | 是 | 是 |
> | 註冊表/權杖 | 否 | 否 |
> | 註冊表/更新策略 | 否 | 否 |
> | 註冊表/網鉤 | 是 | 是 |
> | 註冊表 / 網鉤 / 獲取回撥 Config | 否 | 否 |
> | 註冊表 / 網鉤 / ping | 否 | 否 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | containerServices | 是 | 是 |
> | managedClusters | 是 | 是 |
> | 開放式轉移託管集群 | 是 | 是 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 警示 | 否 | 否 |
> | BillingAccounts | 否 | 否 |
> | 預算 | 否 | 否 |
> | 雲連接器 | 否 | 否 |
> | 連接器 | 是 | 是 |
> | 部門 | 否 | 否 |
> | 維度 | 否 | 否 |
> | EnrollmentAccounts | 否 | 否 |
> | 出口 | 否 | 否 |
> | 外部結算帳戶 | 否 | 否 |
> | 外部結算帳戶/警報 | 否 | 否 |
> | 外部計費帳戶 / 維度 | 否 | 否 |
> | 外部結算帳戶/預測 | 否 | 否 |
> | 外部計費帳戶/查詢 | 否 | 否 |
> | 外部訂閱 | 否 | 否 |
> | 外部訂閱/警報 | 否 | 否 |
> | 外部訂閱/維度 | 否 | 否 |
> | 外部訂閱/預測 | 否 | 否 |
> | 外部訂閱/查詢 | 否 | 否 |
> | 趨勢預測 | 否 | 否 |
> | 查詢 | 否 | 否 |
> | 註冊 | 否 | 否 |
> | Reportconfigs | 否 | 否 |
> | 報表 | 否 | 否 |
> | 設定 | 否 | 否 |
> | 顯示回法規則 | 否 | 否 |
> | 檢視 | 否 | 否 |

## <a name="microsoftcustomerlockbox"></a>微軟.客戶鎖定箱

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | requests | 否 | 否 |

## <a name="microsoftcustomproviders"></a>微軟.自訂提供程式

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 關聯 | 否 | 否 |
> | 資源提供者 | 是 | 是 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | jobs | 是 | 是 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | 是 | 是 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | workspaces | 是 | 否 |
> | 工作區/資料庫工作區 | 否 | 否 |
> | 工作區/存儲加密 | 否 | 否 |
> | 工作區/虛擬網路對等互連 | 否 | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | catalogs | 是 | 是 |
> | 資料目錄 | 是 | 是 |
> | 資料目錄/資料來源 | 否 | 否 |
> | 資料目錄 / 資料來源 / 掃描 | 否 | 否 |
> | 資料目錄 / 資料來源 / 掃描 / 資料集 | 否 | 否 |
> | 資料目錄 / 資料來源 / 掃描 / 觸發器 | 否 | 否 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | dataFactories | 是 | 否 |
> | 資料工廠/診斷設置 | 否 | 否 |
> | 資料工廠/指標定義 | 否 | 否 |
> | dataFactorySchema | 否 | 否 |
> | factories | 是 | 否 |
> | 工廠/集成執行時間 | 否 | 否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | 帳戶/資料湖存儲帳戶 | 否 | 否 |
> | 帳戶/存儲帳戶 | 否 | 否 |
> | 帳戶 / 存儲帳戶 / 容器 | 否 | 否 |
> | 帳戶/轉帳分析單位 | 否 | 否 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | 帳戶/事件網格篩選器 | 否 | 否 |
> | 帳戶/防火牆規則 | 否 | 否 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 服務 | 否 | 否 |
> | 服務/專案 | 否 | 否 |

## <a name="microsoftdatashare"></a>微軟.資料共用

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | 帳戶/股份 | 否 | 否 |
> | 帳戶/ 共用 / 資料集 | 否 | 否 |
> | 帳戶/ 股票 / 邀請 | 否 | 否 |
> | 帳戶/共用 / 供應商共用訂閱 | 否 | 否 |
> | 帳戶/ 共用 / 同步設置 | 否 | 否 |
> | 帳戶/共用訂閱 | 否 | 否 |
> | 帳戶 / 共用訂閱 / 消費者來源資料集組 | 否 | 否 |
> | 帳戶/共用訂閱/資料集映射 | 否 | 否 |
> | 帳戶 / 共用訂閱 / 觸發器 | 否 | 否 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | servers | 是 | 是 |
> | 伺服器/顧問 | 否 | 否 |
> | 伺服器/金鑰 | 否 | 否 |
> | 伺服器/專用端接連接Proxies | 否 | 否 |
> | 伺服器/專用端點連接 | 否 | 否 |
> | 伺服器/專用連結資源 | 否 | 否 |
> | 伺服器/查詢文本 | 否 | 否 |
> | 伺服器/可恢復伺服器 | 否 | 否 |
> | 伺服器/頂級查詢統計資訊 | 否 | 否 |
> | 伺服器/虛擬網路規則 | 否 | 否 |
> | 伺服器/等待統計 | 否 | 否 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | servers | 是 | 是 |
> | 伺服器/顧問 | 否 | 否 |
> | 伺服器/金鑰 | 否 | 否 |
> | 伺服器/專用端接連接Proxies | 否 | 否 |
> | 伺服器/專用端點連接 | 否 | 否 |
> | 伺服器/專用連結資源 | 否 | 否 |
> | 伺服器/查詢文本 | 否 | 否 |
> | 伺服器/可恢復伺服器 | 否 | 否 |
> | 伺服器/頂級查詢統計資訊 | 否 | 否 |
> | 伺服器/虛擬網路規則 | 否 | 否 |
> | 伺服器/等待統計 | 否 | 否 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 伺服器組 | 是 | 是 |
> | servers | 是 | 是 |
> | 伺服器/顧問 | 否 | 否 |
> | 伺服器/金鑰 | 否 | 否 |
> | 伺服器/專用端接連接Proxies | 否 | 否 |
> | 伺服器/專用端點連接 | 否 | 否 |
> | 伺服器/專用連結資源 | 否 | 否 |
> | 伺服器/查詢文本 | 否 | 否 |
> | 伺服器/可恢復伺服器 | 否 | 否 |
> | 伺服器/頂級查詢統計資訊 | 否 | 否 |
> | 伺服器/虛擬網路規則 | 否 | 否 |
> | 伺服器/等待統計 | 否 | 否 |
> | 伺服器v2 | 是 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 工件來源 | 是 | 是 |
> | rollouts | 是 | 是 |
> | 服務拓撲 | 是 | 是 |
> | 服務拓撲/服務 | 是 | 是 |
> | 服務中心 / 服務 / 服務單位 | 是 | 是 |
> | steps | 是 | 是 |

## <a name="microsoftdesktopvirtualization"></a>微軟.桌面虛擬化

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 應用程式組 | 是 | 是 |
> | 應用程式組/應用程式 | 否 | 否 |
> | 應用程式組/桌面 | 否 | 否 |
> | 應用程式組/啟動功能表項目 | 否 | 否 |
> | 主機池 | 是 | 是 |
> | 主機池/工作階段主機 | 否 | 否 |
> | 主機池/工作階段主機/使用者會話 | 否 | 否 |
> | 主機池/使用者會話 | 否 | 否 |
> | workspaces | 是 | 是 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 彈性池 | 是 | 是 |
> | 彈性池 / IotHub租戶 | 是 | 是 |
> | 彈性池 / IotHub租戶 / 安全設置 | 否 | 否 |
> | IotHubs | 是 | 是 |
> | IotHubs / 事件網格篩選器 | 否 | 否 |
> | IotHubs / 安全設置 | 否 | 否 |
> | ProvisioningServices | 是 | 是 |
> | usages | 否 | 否 |

## <a name="microsoftdevops"></a>微軟.DevOps

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 管線中 | 是 | 是 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | controllers | 是 | 是 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | labcenters | 是 | 是 |
> | labs | 是 | 是 |
> | 實驗室/環境 | 是 | 是 |
> | 實驗室/服務運行者 | 是 | 是 |
> | 實驗室/虛擬機器 | 是 | 是 |
> | schedules | 是 | 是 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | 否 | 否 |
> | databaseAccounts | 是 | 是 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | domains | 是 | 是 |
> | 域 /域擁有權識別碼 | 否 | 否 |
> | generateSsoRequest | 否 | 否 |
> | topLevelDomains | 否 | 否 |
> | validateDomainRegistrationInformation | 否 | 否 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | lcsprojects | 否 | 否 |
> | lcs專案/雲部署 | 否 | 否 |
> | lcs專案/ 連接器 | 否 | 否 |

## <a name="microsoftenterpriseknowledgegraph"></a>微軟.企業知識圖

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 服務 | 是 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | domains | 是 | 是 |
> | 域/主題 | 否 | 否 |
> | eventSubscriptions | 否 | 否 |
> | extensionTopics | 否 | 否 |
> | 合作夥伴命名空間 | 是 | 是 |
> | 合作夥伴命名空間/ 事件通道 | 否 | 否 |
> | 合作夥伴註冊 | 是 | 是 |
> | 合作夥伴主題 | 是 | 是 |
> | 合作夥伴主題/活動訂閱 | 否 | 否 |
> | 系統主題 | 是 | 是 |
> | 系統主題/事件訂閱 | 否 | 否 |
> | topics | 是 | 是 |
> | topicTypes | 否 | 否 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | 命名空間 | 是 | 是 |
> | 命名空間/授權規則 | 否 | 否 |
> | 命名空間/災害復原配置 | 否 | 否 |
> | 命名空間/事件中心 | 否 | 否 |
> | 命名空間/ 事件中心 / 授權規則 | 否 | 否 |
> | 命名空間 / 事件中心 / 消費組 | 否 | 否 |
> | 命名空間/網路規則集 | 否 | 否 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 特性 | 否 | 否 |
> | 提供者 | 否 | 否 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | enroll | 否 | 否 |
> | galleryitems | 否 | 否 |
> | generateartifactaccessuri | 否 | 否 |
> | myareas | 否 | 否 |
> | 我的區域/地區 | 否 | 否 |
> | 我的區域 / 區域 / 區域 | 否 | 否 |
> | 我的區域 / 區域 / 區域 / 畫廊專案 | 否 | 否 |
> | 我的區域 / 區域 / 畫廊專案 | 否 | 否 |
> | 我的區域 / 畫廊專案 | 否 | 否 |
> | 註冊 | 否 | 否 |
> | resources | 否 | 否 |
> | retrieveresourcesbyid | 否 | 否 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 自動管理帳戶 | 是 | 是 |
> | 自動管理Vm配置設定檔 | 是 | 是 |
> | 配置設定檔分配 | 否 | 否 |
> | guestConfigurationAssignments | 否 | 否 |
> | software | 否 | 否 |
> | 軟體更新設定檔 | 否 | 否 |
> | 軟體更新 | 否 | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | hanaInstances | 是 | 是 |
> | 皂顯示器 | 是 | 是 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 專用的 HSM | 是 | 是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | 群集/應用程式 | 否 | 否 |

## <a name="microsofthealthcareapis"></a>微軟.醫療保健Apis

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 服務 | 是 | 是 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 機器 | 是 | 是 |
> | 機器/擴展 | 是 | 是 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 資料管理器 | 是 | 是 |

## <a name="microsofthydra"></a>微軟.海德拉

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | components | 是 | 是 |
> | 網路範圍 | 是 | 是 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | jobs | 是 | 是 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 否 | 否 |
> | diagnosticSettingsCategories | 否 | 否 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 應用範本 | 否 | 否 |
> | IoTApps | 是 | 是 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 圖形 | 是 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | deletedVaults | 否 | 否 |
> | hsmPools | 是 | 是 |
> | vaults | 是 | 是 |
> | 保存庫/訪問策略 | 否 | 否 |
> | 保存庫/事件網格篩選器 | 否 | 否 |
> | 保存庫/機密 | 否 | 否 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | 群集/附加資料庫配置 | 否 | 否 |
> | 群集/資料庫 | 否 | 否 |
> | 群集 / 資料庫 / 資料連線 | 否 | 否 |
> | 群集/資料庫/事件點連接 | 否 | 否 |
> | 群集/資料庫/委託分配 | 否 | 否 |
> | 群集/主體分配 | 否 | 否 |
> | 群集/共用實體 | 否 | 否 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | labaccounts | 是 | 是 |
> | users | 否 | 否 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | 是 | 是 |
> | integrationAccounts | 是 | 是 |
> | 整合服務環境 | 是 | 是 |
> | 整合服務環境/託管 Apis | 是 | 是 |
> | 隔離環境 | 是 | 是 |
> | workflows | 是 | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | 是 | 是 |
> | webServices | 是 | 是 |
> | 工作區 | 是 | 是 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | workspaces | 是 | 是 |
> | 工作區/計算 | 否 | 否 |
> | 工作區/事件網格篩選器 | 否 | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | Identities | 否 | 否 |
> | userAssignedIdentities | 是 | 是 |

## <a name="microsoftmanagedservices"></a>微軟.託管服務

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 市場註冊定義 | 否 | 否 |
> | 註冊分配 | 否 | 否 |
> | 註冊定義 | 否 | 否 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | getEntities | 否 | 否 |
> | managementGroups | 否 | 否 |
> | 管理組/設置 | 否 | 否 |
> | resources | 否 | 否 |
> | startTenantBackfill | 否 | 否 |
> | tenantBackfillStatus | 否 | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | 帳戶/事件網格篩選器 | 否 | 否 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | offers | 否 | 否 |
> | offerTypes | 否 | 否 |
> | 優惠類型/發佈商 | 否 | 否 |
> | 產品類型 / 發佈商 / 優惠 | 否 | 否 |
> | 優惠類型 / 發佈商 / 優惠 / 計畫 | 否 | 否 |
> | 優惠類型 / 發佈商 / 優惠 / 計畫 / 協定 | 否 | 否 |
> | 報價類型 / 發佈商 / 優惠 / 計畫 / 配置 | 否 | 否 |
> | 報價類型 / 發佈商 / 優惠 / 計畫 / 配置 / 導入圖像 | 否 | 否 |
> | privategalleryitems | 否 | 否 |
> | 專用存儲用戶端 | 否 | 否 |
> | products | 否 | 否 |
> | 發行者 | 否 | 否 |
> | 出版商/優惠 | 否 | 否 |
> | 出版商/ 優惠 / 修訂 | 否 | 否 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | classicDevServices | 是 | 是 |
> | updateCommunicationPreference | 否 | 否 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | agreements | 否 | 否 |
> | offertypes | 否 | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | mediaservices | 是 | 是 |
> | 媒體服務/帳戶篩選器 | 否 | 否 |
> | 媒體服務/資產 | 否 | 否 |
> | 媒體服務 / 資產 / 資產篩檢程式 | 否 | 否 |
> | 媒體服務/內容關鍵政策 | 否 | 否 |
> | 媒體服務/事件網格篩選器 | 否 | 否 |
> | 媒體服務 / 即時活動操作 | 否 | 否 |
> | 媒體服務 /現場活動 | 是 | 是 |
> | 媒體服務 / 現場活動 / 即時輸出 | 否 | 否 |
> | 媒體服務/即時輸出操作 | 否 | 否 |
> | 媒體服務/媒體圖 | 否 | 否 |
> | 媒體服務/流式處理 | 否 | 否 |
> | 媒體服務/流式處理端點 | 是 | 是 |
> | 媒體服務/流媒體定位器 | 否 | 否 |
> | 媒體服務/流式處理策略 | 否 | 否 |
> | 媒體服務/轉換 | 否 | 否 |
> | 媒體服務 / 轉換 / 作業 | 否 | 否 |

## <a name="microsoftmicroservices4spring"></a>微軟.微服務4春天

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 應用群集 | 是 | 是 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 評估專案 | 是 | 是 |
> | migrateprojects | 是 | 是 |
> | 移動集合 | 是 | 是 |
> | projects | 是 | 是 |

## <a name="microsoftmixedreality"></a>微軟.混合現實

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 全息圖形廣播帳戶 | 是 | 是 |
> | 物件理解帳戶 | 是 | 是 |
> | 遠端渲染帳戶 | 是 | 是 |
> | 空間錨定帳戶 | 是 | 是 |
> | 表面重建帳戶 | 是 | 是 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 淨應用帳戶 | 是 | 否 |
> | 淨應用帳戶/容量池 | 是 | 否 |
> | 淨應用帳戶 / 容量池 / 卷 | 是 | 否 |
> | 淨應用帳戶 / 容量池 / 卷 / 快照 | 否 | 否 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | applicationGateways | 是 | 是 |
> | 應用程式閘道Web應用程式防火牆策略 | 是 | 是 |
> | applicationSecurityGroups | 是 | 是 |
> | azureFirewallFqdnTags | 否 | 否 |
> | azureFirewalls | 是 | 否 |
> | 堡壘主機 | 是 | 是 |
> | bgpServiceCommunities | 否 | 否 |
> | connections | 是 | 是 |
> | ddosCustomPolicies | 是 | 是 |
> | ddosProtectionPlans | 是 | 是 |
> | dnsOperationStatuses | 否 | 否 |
> | dnszones | 是 | 是 |
> | dns 區域 / A | 否 | 否 |
> | dns區域 / AAAA | 否 | 否 |
> | dns 區域 / 所有 | 否 | 否 |
> | dns 區域 / CAA | 否 | 否 |
> | dns 區域 / CNAME | 否 | 否 |
> | dns 區域 / MX | 否 | 否 |
> | dns 區域 / NS | 否 | 否 |
> | dns 區域 / PTR | 否 | 否 |
> | dns 區域 / 記錄集 | 否 | 否 |
> | dns 區域 / SOA | 否 | 否 |
> | dns 區域 / SRV | 否 | 否 |
> | dns區域 / TXT | 否 | 否 |
> | expressRouteCircuits | 是 | 是 |
> | expressRouteCrossConnections | 是 | 是 |
> | 快遞閘道 | 是 | 是 |
> | 快速路由埠 | 是 | 是 |
> | expressRouteServiceProviders | 否 | 否 |
> | 防火牆策略 | 是 | 是 |
> | frontdoors | 是，但有限（見[下面的注釋](#frontdoor)） | 是 |
> | 前門Web應用程式防火牆管理規則集 | 是，但有限（見[下面的注釋](#frontdoor)） | 否 |
> | frontdoorWebApplicationFirewallPolicies | 是，但有限（見[下面的注釋](#frontdoor)） | 是 |
> | getDnsResourceReference | 否 | 否 |
> | internalNotify | 否 | 否 |
> | loadBalancers | 是 | 否 |
> | localNetworkGateways | 是 | 是 |
> | natGateways | 是 | 是 |
> | networkIntentPolicies | 是 | 是 |
> | networkInterfaces | 是 | 是 |
> | networkProfiles | 是 | 是 |
> | networkSecurityGroups | 是 | 是 |
> | networkWatchers | 是 | 否 |
> | 網路觀察器/ 連接監視器 | 是 | 否 |
> | 網路觀察家/鏡頭 | 是 | 否 |
> | 網路觀察家 / pingMeshes | 是 | 否 |
> | p2sVpn閘道 | 是 | 是 |
> | 私有Dns操作狀態 | 否 | 否 |
> | 私有 Dns 區 | 是 | 是 |
> | 私有 Dns 區 / A | 否 | 否 |
> | 專用 Dns 區 / AAAA | 否 | 否 |
> | 私有 Dns 區域 / 所有 | 否 | 否 |
> | 私有 Dns 區域 / CNAME | 否 | 否 |
> | 專用 Dns 區 / MX | 否 | 否 |
> | 專用 Dns 區 / PTR | 否 | 否 |
> | 私有 Dns 區域 / SOA | 否 | 否 |
> | 專用 Dns 區 / SRV | 否 | 否 |
> | 專用 Dns 區 / TXT | 否 | 否 |
> | 專用 Dns 區域 / 虛擬網路連結 | 是 | 是 |
> | 私有終結點 | 是 | 是 |
> | privateLinkServices | 是 | 是 |
> | publicIPAddresses | 是 | 是 |
> | publicIPPrefixes | 是 | 是 |
> | routeFilters | 是 | 是 |
> | routeTables | 是 | 是 |
> | serviceEndpointPolicies | 是 | 是 |
> | trafficManagerGeographicHierarchies | 否 | 否 |
> | trafficmanagerprofiles | 是 | 是 |
> | trafficmanagerprofiles/heatMaps | 否 | 否 |
> | 流量管理器使用者指標鍵 | 否 | 否 |
> | virtualHubs | 是 | 是 |
> | virtualNetworkGateways | 是 | 是 |
> | virtualNetworks | 是 | 是 |
> | virtualNetworkTaps | 是 | 是 |
> | virtualWans | 是 | 是 |
> | vpnGateways | 是 | 否 |
> | vpnSites | 是 | 是 |
> | webApplicationFirewallPolicies | 是 | 是 |

<a id="frontdoor" />

> [!NOTE]
> 對於 Azure 前門服務，可以在創建資源時應用標記，但當前不支援更新或委任標記。


## <a name="microsoftnotebooks"></a>微軟.筆記本

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 筆記本 | 否 | 否 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 命名空間 | 是 | 否 |
> | 命名空間/通知中心 | 是 | 否 |

## <a name="microsoftobjectstore"></a>微軟.物件商店

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | os 命名空間 | 是 | 是 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 超級網站 | 是 | 是 |
> | 導入網站 | 是 | 是 |
> | 伺服器網站 | 是 | 是 |
> | VMwareSites | 是 | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | linkTargets | 否 | 否 |
> | storageInsightConfigs | 否 | 否 |
> | workspaces | 是 | 是 |
> | 工作區/資料匯出 | 否 | 否 |
> | 工作區/資料來源 | 否 | 否 |
> | 工作區/連結服務 | 否 | 否 |
> | 工作區/專用端接Proxie | 否 | 否 |
> | 工作區/專用端點連接 | 否 | 否 |
> | 工作區/專用連結資源 | 否 | 否 |
> | 工作區/查詢 | 否 | 否 |
> | 工作區/作用域私人連結 Proxies | 否 | 否 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | managementassociations | 否 | 否 |
> | managementconfigurations | 是 | 是 |
> | solutions | 是 | 是 |
> | 檢視 | 是 | 是 |

## <a name="microsoftpeering"></a>微軟.對等

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 傳統對等互連 | 否 | 否 |
> | 對等Asns | 否 | 否 |
> | 對等互連 | 是 | 是 |
> | 對等服務國家 | 否 | 否 |
> | 對等服務提供者 | 否 | 否 |
> | 對等服務 | 是 | 是 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | policyEvents | 否 | 否 |
> | 策略中繼資料 | 否 | 否 |
> | policyStates | 否 | 否 |
> | policyTrackedResources | 否 | 否 |
> | remediations | 否 | 否 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | consoles | 否 | 否 |
> | dashboards | 是 | 是 |
> | userSettings | 否 | 否 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | 是 | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | capacities | 是 | 是 |

## <a name="microsoftprojectbabylon"></a>微軟.專案巴比靈斯

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | 否 | 否 |
> | vaults | 是 | 是 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 命名空間 | 是 | 是 |
> | 命名空間/授權規則 | 否 | 否 |
> | 命名空間/混合連接 | 否 | 否 |
> | 命名空間 /混合連接/授權規則 | 否 | 否 |
> | 命名空間 / wcfrelay | 否 | 否 |
> | 命名空間 / wcfrelay / 授權規則 | 否 | 否 |

## <a name="microsoftremoteapp"></a>微軟.遠端應用程式

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 否 | 否 |
> | 集合 | 是 | 是 |
> | 集合/應用程式 | 否 | 否 |
> | 集合/安全主體 | 否 | 否 |
> | 範本圖像 | 否 | 否 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 查詢 | 是 | 是 |
> | 資源更改詳細資訊 | 否 | 否 |
> | 資源更改 | 否 | 否 |
> | resources | 否 | 否 |
> | 資源歷史 | 否 | 否 |
> | subscriptionsStatus | 否 | 否 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | 否 | 否 |
> | childAvailabilityStatuses | 否 | 否 |
> | childResources | 否 | 否 |
> | 新興問題 | 否 | 否 |
> | 活動 | 否 | 否 |
> | impactedResources | 否 | 否 |
> | 中繼資料 | 否 | 否 |
> | 通知 | 否 | 否 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | deployments | 是 | 否 |
> | 部署/操作 | 否 | 否 |
> | 部署腳本 | 是 | 是 |
> | 部署腳本/日誌 | 否 | 否 |
> | 連結 | 否 | 否 |
> | notifyResourceJobs | 否 | 否 |
> | 提供者 | 否 | 否 |
> | resourceGroups | 是 | 否 |
> | subscriptions | 是 | 否 |
> | tenants | 否 | 否 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 應用程式所需 | 是 | 是 |
> | saasresources | 否 | 否 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | 否 | 否 |
> | searchServices | 是 | 是 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 自我調整網路強化 | 否 | 否 |
> | advancedThreatProtectionSettings | 否 | 否 |
> | alerts | 否 | 否 |
> | allowedConnections | 否 | 否 |
> | applicationWhitelistings | 否 | 否 |
> | 評估中繼資料 | 否 | 否 |
> | 評量 | 否 | 否 |
> | 自動關閉警報規則 | 否 | 否 |
> | 自動化 | 是 | 是 |
> | AutoProvisioningSettings | 否 | 否 |
> | Compliances | 否 | 否 |
> | dataCollectionAgents | 否 | 否 |
> | 設備安全性群組 | 否 | 否 |
> | discoveredSecuritySolutions | 否 | 否 |
> | externalSecuritySolutions | 否 | 否 |
> | InformationProtectionPolicies | 否 | 否 |
> | iot安全解決方案 | 是 | 是 |
> | iot安全解決方案 / 分析模型 | 否 | 否 |
> | iot安全解決方案 / 分析模型 / 聚合警報 | 否 | 否 |
> | iot安全解決方案 / 分析模型 / 匯總建議 | 否 | 否 |
> | jitNetworkAccessPolicies | 否 | 否 |
> | 網路資料 | 否 | 否 |
> | 原則 | 否 | 否 |
> | pricings | 否 | 否 |
> | 監管合規標準 | 否 | 否 |
> | 法規合規性標準/法規合規性控制 | 否 | 否 |
> | 法規合規性標準/法規合規性控制/法規合規性評估 | 否 | 否 |
> | securityContacts | 否 | 否 |
> | securitySolutions | 否 | 否 |
> | securitySolutionsReferenceData | 否 | 否 |
> | securityStatuses | 否 | 否 |
> | securityStatusesSummaries | 否 | 否 |
> | 伺服器漏洞評估 | 否 | 否 |
> | settings | 否 | 否 |
> | 子評估 | 否 | 否 |
> | 工作 | 否 | 否 |
> | topologies | 否 | 否 |
> | workspaceSettings | 否 | 否 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 否 | 否 |
> | diagnosticSettingsCategories | 否 | 否 |

## <a name="microsoftsecurityinsights"></a>微軟.安全洞察

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | aggregations | 否 | 否 |
> | 警報規則 | 否 | 否 |
> | 警報規則範本 | 否 | 否 |
> | 書籤 | 否 | 否 |
> | 案例 | 否 | 否 |
> | 資料連線器 | 否 | 否 |
> | 資料連線器檢查要求 | 否 | 否 |
> | 實體 | 否 | 否 |
> | 實體查詢 | 否 | 否 |
> | 事件 | 否 | 否 |
> | 辦公室同意 | 否 | 否 |
> | settings | 否 | 否 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 命名空間 | 是 | 否 |
> | 命名空間/授權規則 | 否 | 否 |
> | 命名空間/災害復原配置 | 否 | 否 |
> | 命名空間/事件網格篩選器 | 否 | 否 |
> | 命名空間/網路規則集 | 否 | 否 |
> | 命名空間/佇列 | 否 | 否 |
> | 命名空間/佇列/授權規則 | 否 | 否 |
> | 命名空間/主題 | 否 | 否 |
> | 命名空間/主題/授權規則 | 否 | 否 |
> | 命名空間 / 主題 / 訂閱 | 否 | 否 |
> | 命名空間 / 主題 / 訂閱 / 規則 | 否 | 否 |
> | premiumMessagingRegions | 否 | 否 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 應用程式所需 | 是 | 是 |
> | clusters | 是 | 是 |
> | 群集/應用程式 | 否 | 否 |
> | containerGroups | 是 | 是 |
> | 容器組集 | 是 | 是 |
> | edgeclusters | 是 | 是 |
> | 邊緣群集/應用程式 | 否 | 否 |
> | managedclusters | 是 | 是 |
> | 託管群集/節點類型 | 否 | 否 |
> | networks | 是 | 是 |
> | secretstores | 是 | 是 |
> | 秘密商店/證書 | 否 | 否 |
> | 秘密商店/秘密 | 否 | 否 |
> | 磁碟區 | 是 | 是 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 應用程式所需 | 是 | 是 |
> | containerGroups | 是 | 是 |
> | gateways | 是 | 是 |
> | networks | 是 | 是 |
> | 密碼 | 是 | 是 |
> | 磁碟區 | 是 | 是 |

## <a name="microsoftservices"></a>微軟.服務

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | 否 | 否 |
> | 供應商註冊/資源類型註冊 | 否 | 否 |
> | rollouts | 是 | 是 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | SignalR | 是 | 是 |
> | 信號R / 事件網格濾波器 | 否 | 否 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | 是 | 是 |

## <a name="microsoftsoftwareplan"></a>微軟.軟體計畫

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 混合使用優勢 | 否 | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | 是 | 是 |
> | 應用程式所需 | 是 | 是 |
> | jitRequests | 是 | 是 |

## <a name="microsoftspoolservice"></a>微軟.拼車服務

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 註冊訂閱 | 否 | 否 |
> | 線軸 | 是 | 是 |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | managedInstances | 是 | 是 |
> | 託管實例/資料庫 | 是（見[以下注釋](#sqlnote)） | 是 |
> | 託管實例/資料庫/備份短期保留原則 | 否 | 否 |
> | 託管實例 / 資料庫 / 架構 / 表 / 列 / 敏感度標籤 | 否 | 否 |
> | 託管實例 / 資料庫 / 漏洞評估 | 否 | 否 |
> | 託管實例 / 資料庫 / 漏洞評估 / 規則 / 基線 | 否 | 否 |
> | 託管實例/加密保護器 | 否 | 否 |
> | 託管實例/金鑰 | 否 | 否 |
> | 託管實例/可還原丟棄資料庫/備份短期保留原則 | 否 | 否 |
> | 託管實例/漏洞評估 | 否 | 否 |
> | servers | 是 | 是 |
> | 伺服器/管理員 | 否 | 否 |
> | 伺服器/通訊連結 | 否 | 否 |
> | 伺服器/資料庫 | 是（見[以下注釋](#sqlnote)） | 是 |
> | 伺服器/加密保護器 | 否 | 否 |
> | 伺服器/防火牆規則 | 否 | 否 |
> | 伺服器/金鑰 | 否 | 否 |
> | 伺服器/可恢復丟棄資料庫 | 否 | 否 |
> | 伺服器/服務目標 | 否 | 否 |
> | 伺服器/ tde證書 | 否 | 否 |
> | 虛擬叢集 | 否 | 否 |

<a id="sqlnote" />

> [!NOTE]
> Master 資料庫不支援標籤，但其他資料庫 (包括 Azure SQL 資料倉儲資料庫) 則可支援標籤。 Azure SQL 資料倉儲資料庫的狀態必須是 [作用中] \(不是 [暫停]\)。

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | 是 | 是 |
> | Sql虛擬電腦群組/可用性組攔截器 | 否 | 否 |
> | SqlVirtualMachines | 是 | 是 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | storageAccounts | 是 | 是 |
> | 存儲帳戶/blob 服務 | 否 | 否 |
> | 存儲帳戶/檔服務 | 否 | 否 |
> | 存儲帳戶/佇列服務 | 否 | 否 |
> | 存儲帳戶/服務 | 否 | 否 |
> | 存儲帳戶 / 服務 / 指標定義 | 否 | 否 |
> | 存儲帳戶/表服務 | 否 | 否 |
> | usages | 否 | 否 |

## <a name="microsoftstoragecache"></a>微軟.存儲緩存

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 緩存 | 是 | 是 |
> | 緩存/存儲目標 | 否 | 否 |
> | 使用模型 | 否 | 否 |

## <a name="microsoftstoragereplication"></a>微軟.存儲複製

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 複製組 | 否 | 否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | 存儲同步服務/註冊伺服器 | 否 | 否 |
> | 存儲同步服務/同步組 | 否 | 否 |
> | 存儲同步服務 / 同步組 / 雲終結點 | 否 | 否 |
> | 存儲同步服務 / 同步組 / 伺服器終結點 | 否 | 否 |
> | 存儲同步服務/工作流 | 否 | 否 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | 存儲同步服務/註冊伺服器 | 否 | 否 |
> | 存儲同步服務/同步組 | 否 | 否 |
> | 存儲同步服務 / 同步組 / 雲終結點 | 否 | 否 |
> | 存儲同步服務 / 同步組 / 伺服器終結點 | 否 | 否 |
> | 存儲同步服務/工作流 | 否 | 否 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | 存儲同步服務/註冊伺服器 | 否 | 否 |
> | 存儲同步服務/同步組 | 否 | 否 |
> | 存儲同步服務 / 同步組 / 雲終結點 | 否 | 否 |
> | 存儲同步服務 / 同步組 / 伺服器終結點 | 否 | 否 |
> | 存儲同步服務/工作流 | 否 | 否 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | managers | 是 | 是 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | streamingjobs | 是 (請參閱下方注意事項) | 是 |

> [!NOTE]
> 您無法在 streamingjobs 執行時新增標記。 阻止資源新增標記。

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | cancel | 否 | 否 |
> | CreateSubscription | 否 | 否 |
> | enable | 否 | 否 |
> | 重新命名 | 否 | 否 |
> | SubscriptionDefinitions | 否 | 否 |
> | SubscriptionOperations | 否 | 否 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | environments | 是 | 否 |
> | 環境/訪問策略 | 否 | 否 |
> | 環境/事件源 | 是 | 否 |
> | 環境/參考資料集集 | 是 | 否 |

## <a name="microsoftvmwarecloudsimple"></a>微軟.VMware雲簡單

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | 專用雲節點 | 是 | 是 |
> | 專用雲服務 | 是 | 是 |
> | virtualMachines | 是 | 是 |

## <a name="microsoftvnfmanager"></a>微軟.Vnf管理器

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | devices | 是 | 是 |
> | 註冊訂閱 | 否 | 否 |
> | 廠商 | 否 | 否 |
> | 供應商 / 斯克烏斯 | 否 | 否 |
> | 供應商 / vnfs | 否 | 否 |
> | 文夫斯 | 是 | 是 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | 否 | 否 |
> | api管理帳戶 / apiAcl | 否 | 否 |
> | api管理帳戶 / aapi | 否 | 否 |
> | api管理帳戶 / aapi / apiAcls | 否 | 否 |
> | api管理帳戶 / apis / 連接Acl | 否 | 否 |
> | api管理帳戶 / apis / 連接 | 否 | 否 |
> | api管理帳戶 / apis / 連接 / 連接Acls | 否 | 否 |
> | api管理帳戶 / ais / 當地語系化定義 | 否 | 否 |
> | api管理帳戶/ 連接Acl | 否 | 否 |
> | api管理帳戶/連接 | 否 | 否 |
> | billingMeters | 否 | 否 |
> | certificates | 是 | 是 |
> | connectionGateways | 是 | 是 |
> | connections | 是 | 是 |
> | customApis | 是 | 是 |
> | deletedSites | 否 | 否 |
> | hostingEnvironments | 是 | 是 |
> | 託管環境/事件網格篩選器 | 否 | 否 |
> | 託管環境/多角色池 | 否 | 否 |
> | 託管環境/輔助池 | 否 | 否 |
> | publishingUsers | 否 | 否 |
> | Mahout | 否 | 否 |
> | resourceHealthMetadata | 否 | 否 |
> | runtimes | 否 | 否 |
> | serverFarms | 是 | 是 |
> | 伺服器農場/事件網格篩選器 | 否 | 否 |
> | sites | 是 | 是 |
> | 網站/配置  | 否 | 否 |
> | 網站/事件網格篩選器 | 否 | 否 |
> | 網站/主機名稱綁定 | 否 | 否 |
> | 網站 /網路設定 | 否 | 否 |
> | 網站 / 首播 | 是 | 是 |
> | 網站/插槽 | 是 | 是 |
> | 網站 / 插槽 / 事件網格篩選器 | 否 | 否 |
> | 網站 / 插槽 / 主機名稱稱綁定 | 否 | 否 |
> | 網站 / 插槽 / 網路設定 | 否 | 否 |
> | sourceControls | 否 | 否 |
> | 靜態網站 | 是 | 是 |
> | validate | 否 | 否 |
> | verifyHostingEnvironmentVnet | 否 | 否 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 否 | 否 |
> | diagnosticSettingsCategories | 否 | 否 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | DeviceServices | 是 | 是 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 在成本報告中標記 |
> | ------------- | ----------- | ----------- |
> | components | 否 | 否 |
> | componentsSummary | 否 | 否 |
> | monitorInstances | 否 | 否 |
> | monitorInstancesSummary | 否 | 否 |
> | monitors | 否 | 否 |
> | notificationSettings | 否 | 否 |

## <a name="next-steps"></a>後續步驟

若要了解如何將標記套用至資源，請參閱[使用標記來組織您的 Azure 資源](tag-resources.md)。
