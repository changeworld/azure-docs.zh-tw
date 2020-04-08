---
title: 依資源型態移動操作支援
description: 列出可移至新資源群組或訂用帳戶的 Azure 資源類型。
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 90fbec4dc076feb1fee8c38cf9757d3c5ddbafaf
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804787"
---
# <a name="move-operation-support-for-resources"></a>資源的移動作業支援
此文章列出 Azure 資源類型是否支援移動作業。 它還提供有關移動資源時需要考慮的特殊條件的資訊。

跳到資源提供者命名空間:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [微軟.應用程式設定](#microsoftappconfiguration)
> - [微軟.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [微軟.授權](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [微軟.Azure數據](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [微軟.區塊鏈權杖](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [微軟.經典計算](#microsoftclassiccompute)
> - [微軟.經典網路](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [微軟.認知](#microsoftcognition)
> - [微軟.認知服務](#microsoftcognitiveservices)
> - [微軟.計算](#microsoftcompute)
> - [微軟.消費](#microsoftconsumption)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [微軟.自訂提供者](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [微軟.資料保護](#microsoftdataprotection)
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
> - [微軟.數字雙胞胎](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [微軟.企業知識圖](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [微軟.實驗](#microsoftexperimentation)
> - [微軟.法爾肯](#microsoftfalcon)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [微軟.醫療保健Apis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [微軟.海德拉](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [微軟.鑰匙庫](#microsoftkeyvault)
> - [微軟.庫伯內斯](#microsoftkubernetes)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [微軟.維護](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [微軟.託管網路](#microsoftmanagednetwork)
> - [微軟.託管服務](#microsoftmanagedservices)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [微軟.微服務4春天](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [微軟.網路](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [微軟.物件商店](#microsoftobjectstore)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [微軟運營管理](#microsoftoperationsmanagement)
> - [微軟.對等](#microsoftpeering)
> - [微軟.政策見解](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [微軟.專案巴比隆](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [微軟.供應商中心](#microsoftproviderhub)
> - [微軟.昆騰](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [微軟.紅帽開放移位](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [微軟.安全洞察](#microsoftsecurityinsights)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [微軟.服務](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [微軟.軟體計劃](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [微軟.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [微軟儲存快取](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [微軟.支援](#microsoftsupport)
> - [微軟.Synapse](#microsoftsynapse)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [微軟.權杖](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [微軟.VMware雲簡單](#microsoftvmwarecloudsimple)
> - [微軟.Vnf管理員](#microsoftvnfmanager)
> - [微軟.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [微軟.視窗](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | domainservices | 否 | 否 |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | tenants | 否 | 否 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 組態 | 否 | 否 |
> | Mahout | 否 | 否 |
> | suppressions | 否 | 否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 動作規則 | 是 | 是 |
> | alerts | 否 | 否 |
> | 警示摘要 | 否 | 否 |
> | 智慧探測器警示規則 | 是 | 是 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | servers | 是 | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 服務 | 是 | 是 |

> [!IMPORTANT]
> 無法移動設置為消耗 SKU 的 API 管理服務。

## <a name="microsoftappconfiguration"></a>微軟.應用程式設定

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 設定儲存 | 是 | 是 |

## <a name="microsoftappplatform"></a>微軟.AppPlatform

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 春天 | 是 | 是 |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | apiapps | 否 | 否 |
> | appidentities | 否 | 否 |
> | gateways | 否 | 否 |

> [!IMPORTANT]
> 請參考[應用程式服務移動指南](./move-limitations/app-service-move-limitations.md)。

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 檢查存取 | 否 | 否 |
> | 拒絕分配 | 否 | 否 |
> | 發現孤兒角色分配 | 否 | 否 |
> | locks | 否 | 否 |
> | 權限 | 否 | 否 |
> | policyassignments | 否 | 否 |
> | 原則定義 | 否 | 否 |
> | 原則組定義 | 否 | 否 |
> | 角色分配 | 否 | 否 |
> | 角色配置使用度量 | 否 | 否 |
> | 角色定義 | 否 | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | automationaccounts | 是 | 是 |
> | 自動化帳戶/設定 | 是 | 是 |
> | 自動化帳戶/執行簿 | 是 | 是 |

> [!IMPORTANT]
> Runbook 必須與自動化帳戶位於同一資源組中。

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | 是 | 是 |

## <a name="microsoftazuredata"></a>微軟.Azure數據

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 混合資料管理員 | 否 | 否 |
> | 後實例 | 否 | 否 |
> | 實體 | 否 | 否 |
> | sqlserver 註冊 | 是 | 是 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | registrations | 是 | 是 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | batchaccounts | 是 | 是 |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | clusters | 否 | 否 |
> | fileservers | 否 | 否 |
> | jobs | 否 | 否 |
> | workspaces | 否 | 否 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 計費週期 | 否 | 否 |
> | 計費權限 | 否 | 否 |
> | 計費角色分配 | 否 | 否 |
> | 計費角色定義 | 否 | 否 |
> | 建立計費角色配置 | 否 | 否 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | mapapis | 否 | 否 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | biztalk | 否 | 否 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | 否 | 否 |
> | 臍帶成員 | 否 | 否 |
> | 觀察家 | 否 | 否 |

## <a name="microsoftblockchaintokens"></a>微軟.區塊鏈權杖

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 權杖服務 | 否 | 否 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | 否 | 否 |
> | blueprints | 否 | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | botservices | 是 | 是 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | redis | 是 | 是 |

> [!IMPORTANT]
> 如果 Redis 實例的 Azure 緩存配置了虛擬網路,則無法將實例移動到其他訂閱。 請參考[網路移動限制](./move-limitations/networking-move-limitations.md)。

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | cdnweb 應用程式防火牆原則 | 是 | 是 |
> | 設定檔 | 是 | 是 |
> | 設定檔案/點端點 | 是 | 是 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | certificateorders | 是 | 是 |

> [!IMPORTANT]
> 請參考[應用程式服務移動指南](./move-limitations/app-service-move-limitations.md)。

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | domainnames | 是 | 否 |
> | virtualmachines | 是 | 否 |

> [!IMPORTANT]
> 請參考[經典部署移動指南](./move-limitations/classic-model-move-limitations.md)。 經典部署資源可以通過特定於該方案的操作跨訂閱移動。

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | 否 | 否 |
> | reservedips | 否 | 否 |
> | virtualnetworks | 否 | 否 |

> [!IMPORTANT]
> 請參考[經典部署移動指南](./move-limitations/classic-model-move-limitations.md)。 經典部署資源可以通過特定於該方案的操作跨訂閱移動。

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | storageaccounts | 是 | 否 |

> [!IMPORTANT]
> 請參考[經典部署移動指南](./move-limitations/classic-model-move-limitations.md)。 經典部署資源可以通過特定於該方案的操作跨訂閱移動。

## <a name="microsoftcognition"></a>微軟.認知

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 合成帳戶 | 否 | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 是 | 是 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | availabilitysets | 是 | 是 |
> | 磁碟加密集 | 否 | 否 |
> | disks | 是 | 是 |
> | galleries | 否 | 否 |
> | 畫廊/圖片 | 否 | 否 |
> | 畫廊 / 影像 / 版本 | 否 | 否 |
> | 主機組 | 否 | 否 |
> | 主機組/主機 | 否 | 否 |
> | images | 是 | 是 |
> | proximityplacementgroups | 是 | 是 |
> | restorepointcollections | 否 | 否 |
> | 共用vm延伸 | 否 | 否 |
> | sharedvmimages | 否 | 否 |
> | 共用 vm 影像/版本 | 否 | 否 |
> | snapshots | 是 | 是 |
> | sshpublickey | 否 | 否 |
> | virtualmachines | 是 | 是 |
> | 虛擬機器/擴展 | 是 | 是 |
> | virtualmachinescalesets | 是 | 是 |

> [!IMPORTANT]
> 請參考[虛擬機器移動指南](./move-limitations/virtual-machines-move-limitations.md)。

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 彙總成本 | 否 | 否 |
> | 平衡 | 否 | 否 |
> | 預算 | 否 | 否 |
> | 收費 | 否 | 否 |
> | 成本標記 | 否 | 否 |
> | credits | 否 | 否 |
> | 活動 | 否 | 否 |
> | 預測 | 否 | 否 |
> | lots | 否 | 否 |
> | 市場 | 否 | 否 |
> | 動作結果 | 否 | 否 |
> | 操作狀態 | 否 | 否 |
> | 價格表 | 否 | 否 |
> | products | 否 | 否 |
> | 預訂詳情 | 否 | 否 |
> | 預訂建議 | 否 | 否 |
> | 預訂摘要 | 否 | 否 |
> | 預留交易 | 否 | 否 |
> | tags | 否 | 否 |
> | tenants | 否 | 否 |
> | 術語 | 否 | 否 |
> | 使用詳細資訊 | 否 | 否 |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | containergroups | 否 | 否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | containergroups | 否 | 否 |
> | 服務關聯連結 | 否 | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | registries | 是 | 是 |
> | 註冊表/代理池 | 否 | 否 |
> | 註冊表/編譯工作 | 是 | 是 |
> | 登錄表/複製 | 是 | 是 |
> | 登錄表格表 | 是 | 是 |
> | 註冊表/任務 | 是 | 是 |
> | 註冊表/網鉤 | 是 | 是 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | containerservices | 否 | 否 |
> | managedclusters | 否 | 否 |
> | openshiftmanagedclusters | 否 | 否 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 應用程式所需 | 否 | 否 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | alerts | 否 | 否 |
> | 預算 | 否 | 否 |
> | 連接器 | 是 | 是 |
> | 維度 | 否 | 否 |
> | 匯出 | 否 | 否 |
> | 外部訂閱 | 否 | 否 |
> | forecast | 否 | 否 |
> | 查詢 | 否 | 否 |
> | 報告設定 | 否 | 否 |
> | reports | 否 | 否 |
> | 顯示回向規則 | 否 | 否 |
> | 檢視 | 否 | 否 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | hubs | 否 | 否 |

## <a name="microsoftcustomproviders"></a>微軟.自訂提供者

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 關聯 | 否 | 否 |
> | 資源提供者 | 是 | 是 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | jobs | 否 | 否 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | 否 | 否 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | workspaces | 否 | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | catalogs | 是 | 是 |
> | 資料目錄 | 否 | 否 |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | 否 | 否 |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | packages | 否 | 否 |
> | plans | 否 | 否 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | datafactories | 是 | 是 |
> | factories | 是 | 是 |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | 否 | 否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 是 | 是 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 是 | 是 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 服務 | 否 | 否 |
> | 服務/專案 | 否 | 否 |
> | slots | 否 | 否 |

## <a name="microsoftdataprotection"></a>微軟.資料保護

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 備份庫 | 否 | 否 |

## <a name="microsoftdatashare"></a>微軟.資料共享

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 是 | 是 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | servers | 是 | 是 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | servers | 是 | 是 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | servergroups | 否 | 否 |
> | servers | 是 | 是 |
> | 伺服器v2 | 是 | 是 |
> | 單伺服器 | 是 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | artifactsources | 是 | 是 |
> | rollouts | 是 | 是 |
> | servicetopologies | 是 | 是 |
> | 服務/服務 | 是 | 是 |
> | 服務/服務/服務單位 | 是 | 是 |
> | steps | 是 | 是 |

## <a name="microsoftdesktopvirtualization"></a>微軟.桌面虛擬化

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 應用程式群組 | 否 | 否 |
> | 主機池 | 否 | 否 |
> | workspaces | 否 | 否 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | elasticpools | 否 | 否 |
> | 彈性池/擴展租戶 | 否 | 否 |
> | iothubs | 是 | 是 |
> | provisioningservices | 是 | 是 |

## <a name="microsoftdevops"></a>微軟.DevOps

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 管線中 | 是 | 是 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | controllers | 是 | 是 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | labcenters | 否 | 否 |
> | labs | 是 | 否 |
> | 實驗室/環境 | 是 | 是 |
> | 實驗室/服務執行者 | 是 | 是 |
> | 實驗室/虛擬機器 | 是 | 否 |
> | schedules | 是 | 是 |

## <a name="microsoftdigitaltwins"></a>微軟.數字雙胞胎

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 數位雙星實例 | 否 | 否 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | 是 | 是 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | domains | 是 | 是 |

## <a name="microsoftenterpriseknowledgegraph"></a>微軟.企業知識圖

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 服務 | 是 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | domains | 是 | 是 |
> | eventSubscriptions | 否 - 無法獨立移動,但使用訂閱的資源自動移動。 | 否 - 無法獨立移動,但使用訂閱的資源自動移動。 |
> | 事件訂閱 | 否 - 無法獨立移動,但使用訂閱的資源自動移動。 | 否 - 無法獨立移動,但使用訂閱的資源自動移動。 |
> | 延伸主題 | 否 | 否 |
> | 合作夥伴命名空間 | 是 | 是 |
> | 合作夥伴註冊 | 否 | 否 |
> | 合作夥伴主題 | 是 | 是 |
> | 系統主題 | 是 | 是 |
> | topics | 是 | 是 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | clusters | 是 | 是 |
> | 命名空間 | 是 | 是 |

## <a name="microsoftexperimentation"></a>微軟.實驗

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 實驗工作區 | 否 | 否 |

## <a name="microsoftfalcon"></a>微軟.法爾肯

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 命名空間 | 是 | 是 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 自動管理帳戶 | 否 | 否 |
> | 自動託管vm設定檔 | 否 | 否 |
> | 來賓配置配置 | 否 | 否 |
> | software | 否 | 否 |
> | 軟體更新設定檔 | 否 | 否 |
> | 軟體更新 | 否 | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | hanainstances | 否 | 否 |
> | 皂監測器 | 是 | 是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | clusters | 是 | 是 |

> [!IMPORTANT]
> 您可以將 HDInsight 叢集移至新的訂用帳戶或資源群組。 不過，您無法跨訂用帳戶來移動連結至 HDInsight 叢集的網路資源 (例如虛擬網路、NIC 或負載平衡器)。 此外，您無法將已連結至叢集虛擬機器的 NIC 移至新的資源群組。
>
> 將 HDInsight 叢集移至新的訂用帳戶時，請先移動其他資源 (例如儲存體帳戶)。 然後，移動 HDInsight 叢集本身。

## <a name="microsofthealthcareapis"></a>微軟.醫療保健Apis

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 服務 | 是 | 是 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 機器 | 是 | 是 |
> | 機器/擴展 | 否 | 否 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | datamanagers | 是 | 是 |

## <a name="microsofthydra"></a>微軟.海德拉

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | components | 否 | 否 |
> | 網路範圍 | 否 | 否 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | jobs | 是 | 是 |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | actiongroups | 是 | 是 |
> | activitylogalerts | 否 | 否 |
> | alertrules | 是 | 是 |
> | autoscalesettings | 是 | 是 |
> | baseline | 否 | 否 |
> | calculatebaseline | 否 | 否 |
> | components | 是 | 是 |
> | 資料收集規則 | 否 | 否 |
> | 診斷設定 | 否 | 否 |
> | 診斷設定類別 | 否 | 否 |
> | eventtypes | 否 | 否 |
> | 延伸診斷設定 | 否 | 否 |
> | guestdiagnosticsettings | 否 | 否 |
> | 紀錄定義 | 否 | 否 |
> | 記錄 | 否 | 否 |
> | metricalerts | 否 | 否 |
> | 公制基線 | 否 | 否 |
> | 指標定義 | 否 | 否 |
> | 指標命名空間 | 否 | 否 |
> | metrics | 否 | 否 |
> | 我的工作手冊 | 否 | 否 |
> | notificationgroups | 否 | 否 |
> | 私人連結鏡 | 是 | 是 |
> | scheduledqueryrules | 是 | 是 |
> | 拓撲 | 否 | 否 |
> | 交易 | 否 | 否 |
> | vminsights 上板狀態 | 否 | 否 |
> | webtests | 是 | 是 |
> | workbooks | 是 | 是 |
> | 工作簿範本 | 是 | 是 |

> [!IMPORTANT]
> 確保移動到新訂閱不超過[訂閱配額](azure-subscription-service-limits.md#azure-monitor-limits)。

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | iotapps | 是 | 是 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 是 | 是 |
> | graph | 是 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | hsmpools | 否 | 否 |
> | vaults | 是 | 是 |

> [!IMPORTANT]
> 用於磁碟加密的金鑰保管庫不能移動到同一訂閱中的資源組或跨訂閱。

## <a name="microsoftkubernetes"></a>微軟.庫伯內斯

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 連接的叢集 | 否 | 否 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | clusters | 是 | 是 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | labaccounts | 否 | 否 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | 否 | 否 |
> | integrationaccounts | 是 | 是 |
> | integrationserviceenvironments | 是 | 否 |
> | 整合服務環境/託管 | 是 | 否 |
> | isolatedenvironments | 否 | 否 |
> | workflows | 是 | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | commitmentplans | 否 | 否 |
> | webservices | 是 | 否 |
> | workspaces | 是 | 是 |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | 否 | 否 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |
> | 帳戶/工作區 | 否 | 否 |
> | 帳號/ 工作區 / 專案 | 否 | 否 |
> | teamaccounts | 否 | 否 |
> | 團隊帳戶/工作區 | 否 | 否 |
> | 團隊帳號/ 工作區 / 專案 | 否 | 否 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | 否 | 否 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | workspaces | 否 | 否 |
> | 工作區/計算 | 否 | 否 |

## <a name="microsoftmaintenance"></a>微軟.維護

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 套用更新 | 否 | 否 |
> | 設定配置 | 否 | 否 |
> | 維護設定 | 是 | 是 |
> | 更新 | 否 | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 身分識別 | 否 | 否 |
> | userassignedidentities | 否 | 否 |

## <a name="microsoftmanagednetwork"></a>微軟.託管網路

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 託管網路 | 否 | 否 |
> | 託管網路/託管網路群組 | 否 | 否 |
> | 託管網路/託管網路對等策略 | 否 | 否 |
> | 通知 | 否 | 否 |

## <a name="microsoftmanagedservices"></a>微軟.託管服務

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 註冊配置 | 否 | 否 |
> | 註冊定義 | 否 | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 是 | 是 |
> | 帳號/私人地圖集 | 是 | 是 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | classicdevservices | 否 | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | mediaservices | 是 | 是 |
> | 媒體服務/現場活動 | 是 | 是 |
> | 媒體服務/流式處理點 | 是 | 是 |

## <a name="microsoftmicroservices4spring"></a>微軟.微服務4春天

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 應用群集 | 否 | 否 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | 是 | 是 |
> | migrateprojects | 是 | 是 |
> | 移動集合 | 否 | 否 |
> | projects | 否 | 否 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | netappaccounts | 否 | 否 |
> | 網路應用帳戶/備份原則 | 否 | 否 |
> | 網路應用帳戶/容量池 | 否 | 否 |
> | 網路應用程式 / 容量池 / 卷 | 否 | 否 |
> | 網路應用程式 / 容量池 / 捲/ 載入目標 | 否 | 否 |
> | 網路應用程式 / 容量池 / 卷 / 快照 | 否 | 否 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | applicationgateways | 否 | 否 |
> | 應用程式閘道Web應用程式防火牆原則 | 否 | 否 |
> | applicationsecuritygroups | 是 | 是 |
> | azurefirewalls | 是 | 是 |
> | bastionhosts | 否 | 否 |
> | connections | 是 | 是 |
> | ddoscustompolicies | 是 | 是 |
> | ddosprotectionplans | 否 | 否 |
> | dnszones | 是 | 是 |
> | expressroutecircuits | 否 | 否 |
> | expressroutegateways | 否 | 否 |
> | 防火牆策略 | 是 | 是 |
> | frontdoors | 否 | 否 |
> | frontdoorwebapplicationfirewallpolicies | 否 | 否 |
> | IP群組 | 是 | 是 |
> | loadbalancers | 是 - 基本 SKU<br>否 - 標準 SKU | 是 - 基本 SKU<br>否 - 標準 SKU |
> | localnetworkgateways | 是 | 是 |
> | natgateways | 是 | 是 |
> | 網路實驗設定檔 | 是 | 是 |
> | networkintentpolicies | 是 | 是 |
> | networkinterfaces | 是 | 是 |
> | networkprofiles | 否 | 否 |
> | networksecuritygroups | 是 | 是 |
> | networkwatchers | 是 | 否 |
> | 網路觀察器/連接監視器 | 是 | 否 |
> | 網路觀察器/流量日誌 | 是 | 否 |
> | 網路觀察器/ 平模 | 是 | 否 |
> | p2svpngateways | 否 | 否 |
> | 私人 dns 區域 | 是 | 是 |
> | 專用網區/虛擬網路鏈路 | 是 | 是 |
> | 私有端點重定向映射 | 否 | 否 |
> | 私有端點 | 是 | 是 |
> | privatelinkservices | 否 | 否 |
> | publicipaddresses | 是 - 基本 SKU<br>否 - 標準 SKU | 是 - 基本 SKU<br>否 - 標準 SKU |
> | publicipprefixes | 是 | 是 |
> | routefilters | 否 | 否 |
> | routetables | 是 | 是 |
> | serviceendpointpolicies | 是 | 是 |
> | trafficmanagerprofiles | 是 | 是 |
> | virtualhubs | 否 | 否 |
> | virtualnetworkgateways | 是 | 是 |
> | virtualnetworks | 是 | 是 |
> | virtualnetworktaps | 否 | 否 |
> | 虛擬路由器 | 是 | 是 |
> | virtualwans | 否 | 否 |
> | vpn閘道(虛擬 WAN) | 否 | 否 |
> | vpnserver 設定 | 否 | 否 |
> | vpnsites(虛擬 WAN) | 否 | 否 |
> | webapplicationfirewallpolicies | 是 | 是 |

> [!IMPORTANT]
> 請參考[網路移動指南](./move-limitations/networking-move-limitations.md)。

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 命名空間 | 是 | 是 |
> | 命名空間/通知中心 | 是 | 是 |

## <a name="microsoftobjectstore"></a>微軟.物件商店

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 名稱空間 | 是 | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | clusters | 否 | 否 |
> | 儲存洞察設定 | 否 | 否 |
> | workspaces | 是 | 是 |

> [!IMPORTANT]
> 確保移動到新訂閱不超過[訂閱配額](azure-subscription-service-limits.md#azure-monitor-limits)。

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | managementassociations | 否 | 否 |
> | managementconfigurations | 是 | 是 |
> | solutions | 是 | 是 |
> | 檢視 | 是 | 是 |

## <a name="microsoftpeering"></a>微軟.對等

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 對等互連 | 是 | 是 |
> | 對等服務 | 否 | 否 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 原則事件 | 否 | 否 |
> | 原則狀態 | 否 | 否 |
> | 原則追蹤資源 | 否 | 否 |
> | remediations | 否 | 否 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | dashboards | 是 | 是 |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | rootresources | 否 | 否 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | workspacecollections | 是 | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | capacities | 是 | 是 |

## <a name="microsoftprojectbabylon"></a>微軟.專案巴比隆

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |

## <a name="microsoftproviderhub"></a>微軟.供應商中心

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | rollouts | 否 | 否 |

## <a name="microsoftquantum"></a>微軟.昆騰

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | workspaces | 否 | 否 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 備份保護專案 | 否 | 否 |
> | 複製資格結果 | 否 | 否 |
> | vaults | 是 | 是 |

> [!IMPORTANT]
> 請參考[服務移動指南](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)。

## <a name="microsoftredhatopenshift"></a>微軟.紅帽開放移位

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 開放式移位叢集 | 否 | 否 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 命名空間 | 是 | 是 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 查詢 | 是 | 是 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 可用性狀態 | 否 | 否 |
> | 兒童可用性狀態 | 否 | 否 |
> | 兒童資源 | 否 | 否 |
> | 活動 | 否 | 否 |
> | 通知 | 否 | 否 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 部署文稿 | 否 | 否 |
> | 連結 | 否 | 否 |
> | tags | 否 | 否 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 應用程式所需 | 是 | 否 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | searchservices | 是 | 是 |

> [!IMPORTANT]
> 不能在一個操作中移動不同區域中的多個搜索資源。 而是要在不同作業中移動它們。

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 自我化網路硬化 | 否 | 否 |
> | 進階威脅保護設定 | 否 | 否 |
> | 評估中繼資料 | 否 | 否 |
> | 評量 | 否 | 否 |
> | 自動化 | 是 | 是 |
> | 合規結果 | 否 | 否 |
> | 合規性 | 否 | 否 |
> | 資料收集器 | 否 | 否 |
> | 裝置安全組 | 否 | 否 |
> | 資訊保護政策 | 否 | 否 |
> | iot 安全解決方案 | 是 | 是 |
> | 伺服器漏洞評估 | 否 | 否 |

## <a name="microsoftsecurityinsights"></a>微軟.安全洞察

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | aggregations | 否 | 否 |
> | alertrules | 否 | 否 |
> | 警示規則樣本 | 否 | 否 |
> | 書籤 | 否 | 否 |
> | 案例 | 否 | 否 |
> | 資料連接器 | 否 | 否 |
> | 資料連接器檢查要求 | 否 | 否 |
> | 實體 | 否 | 否 |
> | 實體查詢 | 否 | 否 |
> | 事件 | 否 | 否 |
> | 辦公室同意 | 否 | 否 |
> | settings | 否 | 否 |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | gateways | 否 | 否 |
> | nodes | 否 | 否 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 命名空間 | 是 | 是 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 應用程式所需 | 否 | 否 |
> | clusters | 是 | 是 |
> | 叢集/應用程式 | 否 | 否 |
> | containergroups | 否 | 否 |
> | containergroupsets | 否 | 否 |
> | edgeclusters | 否 | 否 |
> | managedclusters | 否 | 否 |
> | networks | 否 | 否 |
> | secretstores | 否 | 否 |
> | 磁碟區 | 否 | 否 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 應用程式所需 | 是 | 是 |
> | containergroups | 否 | 否 |
> | gateways | 是 | 是 |
> | networks | 是 | 是 |
> | 密碼 | 是 | 是 |
> | 磁碟區 | 是 | 是 |

## <a name="microsoftservices"></a>微軟.服務

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | rollouts | 否 | 否 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | signalr | 是 | 是 |

## <a name="microsoftsoftwareplan"></a>微軟.軟體計劃

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 雜交優勢 | 否 | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | 否 | 否 |
> | 應用程式所需 | 否 | 否 |
> | jitrequests | 否 | 否 |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 實例池 | 否 | 否 |
> | managedinstances | 否 | 否 |
> | 託管實例/資料庫 | 否 | 否 |
> | servers | 是 | 是 |
> | 伺服器/資料庫 | 是 | 是 |
> | 伺服器/彈性池 | 是 | 是 |
> | 伺服器/工作帳戶 | 是 | 是 |
> | 伺服器/工作台 | 是 | 是 |
> | virtualclusters | 是 | 是 |

> [!IMPORTANT]
> 資料庫和伺服器必須位於同一資源組中。 當您移動 SQL 伺服器時，其所有資料庫也會跟著移動。 此行為會套用至 Azure SQL Database 和 Azure SQL Data Warehouse 資料庫。

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | 是 | 是 |
> | sqlvirtualmachines | 是 | 是 |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | dwvm | 否 | 否 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | storageaccounts | 是 | 是 |

## <a name="microsoftstoragecache"></a>微軟儲存快取

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 快取 | 否 | 否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 是 | 是 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 否 | 否 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 否 | 否 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | managers | 否 | 否 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | streamingjobs | 是 | 是 |

> [!IMPORTANT]
> 流分析作業在運行狀態時無法移動。

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | environments | 否 | 否 |
> | 環境/事件來源 | 否 | 否 |
> | 執行個體 | 否 | 否 |
> | 實體/環境 | 否 | 否 |
> | 實體/環境/事件來源 | 否 | 否 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 建立訂閱 | 否 | 否 |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | supporttickets | 否 | 否 |

## <a name="microsoftsynapse"></a>微軟.Synapse

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | workspaces | 否 | 否 |
> | 工作區/大數據池 | 否 | 否 |
> | 工作區/sqlpools | 否 | 否 |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | providerregistrations | 否 | 否 |
> | resources | 否 | 否 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | environments | 是 | 是 |
> | 環境/事件來源 | 是 | 是 |
> | 環境/參考資料集 | 是 | 是 |

## <a name="microsofttoken"></a>微軟.權杖

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | stores | 是 | 是 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | imagetemplates | 否 | 否 |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | account | 否 | 否 |
> | 帳號/擴展 | 是 | 是 |
> | 帳號/ 專案 | 是 | 是 |

> [!IMPORTANT]
> 要更改 Azure DevOps 的訂閱,請參閱[更改用於計費 的 Azure 訂閱](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)。

## <a name="microsoftvmwarecloudsimple"></a>微軟.VMware雲簡單

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 專用雲節點 | 否 | 否 |
> | 專用雲服務 | 否 | 否 |
> | virtualmachines | 否 | 否 |

## <a name="microsoftvnfmanager"></a>微軟.Vnf管理員

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | devices | 否 | 否 |
> | 文夫斯 | 否 | 否 |

## <a name="microsoftvsonline"></a>微軟.VSOnline

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 是 | 是 |
> | plans | 是 | 是 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | certificates | 否 | 是 |
> | connectiongateways | 是 | 是 |
> | connections | 是 | 是 |
> | customapis | 是 | 是 |
> | hostingenvironments | 否 | 否 |
> | 庫貝環境 | 是 | 是 |
> | serverfarms | 是 | 是 |
> | sites | 是 | 是 |
> | 網站/ 首播 | 是 | 是 |
> | 網站/插槽 | 是 | 是 |
> | 靜態網站 | 否 | 否 |

> [!IMPORTANT]
> 請參考[應用程式服務移動指南](./move-limitations/app-service-move-limitations.md)。

## <a name="microsoftwindowsesu"></a>微軟.視窗

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 多活化金鑰 | 否 | 否 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | deviceservices | 否 | 否 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | components | 否 | 否 |
> | 監視器實例 | 否 | 否 |
> | monitors | 否 | 否 |
> | 通知設定 | 否 | 否 |

## <a name="third-party-services"></a>協力廠商服務

協力廠商服務目前不支援移動作業。

## <a name="next-steps"></a>後續步驟
如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](move-resource-group-and-subscription.md)。

若要以逗號區隔值檔案的形式取得相同資料，請下載 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)。
