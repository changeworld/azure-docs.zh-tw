---
title: 依資源類型的移動作業支援
description: 列出可移至新資源群組或訂用帳戶的 Azure 資源類型。
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 45450b21b1cd3236712043629f433c2c5fe20f80
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900984"
---
# <a name="move-operation-support-for-resources"></a>資源的移動作業支援
此文章列出 Azure 資源類型是否支援移動作業。 它也會提供移動資源時要考慮之特殊條件的相關資訊。

跳至資源提供者命名空間：
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [AppConfiguration](#microsoftappconfiguration)
> - [AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft。授權](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.classiccompute](#microsoftclassiccompute)
> - [Microsoft.classicnetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [認知](#microsoftcognition)
> - [CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft。耗用量](#microsoftconsumption)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [CustomProviders](#microsoftcustomproviders)
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
> - [DataProtection](#microsoftdataprotection)
> - [DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [選取](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft 實驗](#microsoftexperimentation)
> - [Falcon](#microsoftfalcon)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Hydra 等](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Kubernetes](#microsoftkubernetes)
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
> - [Microsoft. 維護](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [ManagedNetwork](#microsoftmanagednetwork)
> - [ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Objectstore 會](#microsoftobjectstore)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.operationsmanagement](#microsoftoperationsmanagement)
> - [Microsoft 對等互連](#microsoftpeering)
> - [Microsoft.policyinsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [ProviderHub](#microsoftproviderhub)
> - [Microsoft 量子](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft 服務](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft .Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft 支援](#microsoftsupport)
> - [Synapse](#microsoftsynapse)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [VnfManager](#microsoftvnfmanager)
> - [VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [WindowsESU](#microsoftwindowsesu)
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
> | actionrules | 是 | 是 |
> | alerts | 否 | 否 |
> | alertssummary | 否 | 否 |
> | smartdetectoralertrules | 是 | 是 |

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
> 無法移動設定為取用 SKU 的 API 管理服務。

## <a name="microsoftappconfiguration"></a>AppConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | configurationstores | 是 | 是 |

## <a name="microsoftappplatform"></a>AppPlatform

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 裝有 | 是 | 是 |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | apiapps | 否 | 否 |
> | appidentities | 否 | 否 |
> | gateways | 否 | 否 |

> [!IMPORTANT]
> 請參閱[App Service 移動指引](./move-limitations/app-service-move-limitations.md)。

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | checkaccess | 否 | 否 |
> | denyassignments | 否 | 否 |
> | findorphanroleassignments | 否 | 否 |
> | locks | 否 | 否 |
> | 權限 | 否 | 否 |
> | policyassignments | 否 | 否 |
> | policydefinitions | 否 | 否 |
> | policysetdefinitions | 否 | 否 |
> | roleassignments | 否 | 否 |
> | roleassignmentsusagemetrics | 否 | 否 |
> | roledefinitions | 否 | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | automationaccounts | 是 | 是 |
> | automationaccounts/設定 | 是 | 是 |
> | automationaccounts/runbook | 是 | 是 |

> [!IMPORTANT]
> Runbook 必須存在於與自動化帳戶相同的資源群組中。

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | 是 | 是 |

## <a name="microsoftazuredata"></a>AzureData

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | 否 | 否 |
> | postgresinstances | 否 | 否 |
> | sqlinstances | 否 | 否 |
> | sqlserverregistrations | 是 | 是 |

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
> | billingperiods | 否 | 否 |
> | billingpermissions | 否 | 否 |
> | billingroleassignments | 否 | 否 |
> | billingroledefinitions | 否 | 否 |
> | createbillingroleassignment | 否 | 否 |

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
> | cordamembers | 否 | 否 |
> | 監看員 | 否 | 否 |

## <a name="microsoftblockchaintokens"></a>BlockchainTokens

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | tokenservices | 否 | 否 |

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
> 如果 Azure Cache for Redis 實例是設定為使用虛擬網路，實例就無法移至不同的訂用帳戶。 請參閱[網路移動限制](./move-limitations/networking-move-limitations.md)。

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | 是 | 是 |
> | profiles | 是 | 是 |
> | 設定檔/端點 | 是 | 是 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | certificateorders | 是 | 是 |

> [!IMPORTANT]
> 請參閱[App Service 移動指引](./move-limitations/app-service-move-limitations.md)。

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | domainnames | 是 | 否 |
> | virtualmachines | 是 | 否 |

> [!IMPORTANT]
> 請參閱[傳統部署移動指引](./move-limitations/classic-model-move-limitations.md)。 傳統部署資源可以透過該案例特定的作業，跨訂用帳戶移動。

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | 否 | 否 |
> | reservedips | 否 | 否 |
> | virtualnetworks | 否 | 否 |

> [!IMPORTANT]
> 請參閱[傳統部署移動指引](./move-limitations/classic-model-move-limitations.md)。 傳統部署資源可以透過該案例特定的作業，跨訂用帳戶移動。

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | storageaccounts | 是 | 否 |

> [!IMPORTANT]
> 請參閱[傳統部署移動指引](./move-limitations/classic-model-move-limitations.md)。 傳統部署資源可以透過該案例特定的作業，跨訂用帳戶移動。

## <a name="microsoftcognition"></a>認知

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | syntheticsaccounts | 否 | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 帳戶 | 是 | 是 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | availabilitysets | 是 | 是 |
> | diskencryptionsets | 否 | 否 |
> | disks | 是 | 是 |
> | galleries | 否 | 否 |
> | 資源庫/影像 | 否 | 否 |
> | 資源庫/影像/版本 | 否 | 否 |
> | hostgroups | 否 | 否 |
> | hostgroups/主機 | 否 | 否 |
> | images | 是 | 是 |
> | proximityplacementgroups | 是 | 是 |
> | restorepointcollections | 否 | 否 |
> | sharedvmextensions | 否 | 否 |
> | sharedvmimages | 否 | 否 |
> | sharedvmimages/版本 | 否 | 否 |
> | snapshots | 是 | 是 |
> | sshpublickeys | 否 | 否 |
> | virtualmachines | 是 | 是 |
> | virtualmachines/extensions | 是 | 是 |
> | virtualmachinescalesets | 是 | 是 |

> [!IMPORTANT]
> 請參閱[虛擬機器移動指引](./move-limitations/virtual-machines-move-limitations.md)。

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | 否 | 否 |
> | 餘額 | 否 | 否 |
> | 對應 | 否 | 否 |
> | 費用 | 否 | 否 |
> | costtags | 否 | 否 |
> | credits | 否 | 否 |
> | 活動 | 否 | 否 |
> | 天氣預報 | 否 | 否 |
> | lots | 否 | 否 |
> | 市場 | 否 | 否 |
> | operationresults | 否 | 否 |
> | operationstatus | 否 | 否 |
> | pricesheets | 否 | 否 |
> | products | 否 | 否 |
> | reservationdetails | 否 | 否 |
> | reservationrecommendations | 否 | 否 |
> | reservationsummaries | 否 | 否 |
> | reservationtransactions | 否 | 否 |
> | tags | 否 | 否 |
> | tenants | 否 | 否 |
> | 術語 | 否 | 否 |
> | usagedetails | 否 | 否 |

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
> | serviceassociationlinks | 否 | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | registries | 是 | 是 |
> | 登錄/agentpools | 否 | 否 |
> | 登錄/buildtasks | 是 | 是 |
> | 登錄/複寫 | 是 | 是 |
> | 登錄/taskruns | 是 | 是 |
> | 登錄/工作 | 是 | 是 |
> | 登錄/webhook | 是 | 是 |

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
> | 帳戶 | 否 | 否 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | alerts | 否 | 否 |
> | 對應 | 否 | 否 |
> | 連接器 | 是 | 是 |
> | 維度 | 否 | 否 |
> | 匯出 | 否 | 否 |
> | externalsubscriptions | 否 | 否 |
> | forecast | 否 | 否 |
> | 查詢 | 否 | 否 |
> | reportconfigs | 否 | 否 |
> | reports | 否 | 否 |
> | showbackrules | 否 | 否 |
> | 檢視 | 否 | 否 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | hubs | 否 | 否 |

## <a name="microsoftcustomproviders"></a>CustomProviders

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 關聯 | 否 | 否 |
> | resourceproviders | 是 | 是 |

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
> | datacatalogs | 否 | 否 |

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
> | 帳戶 | 是 | 是 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 帳戶 | 是 | 是 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 服務 | 否 | 否 |
> | 服務/專案 | 否 | 否 |
> | slots | 否 | 否 |

## <a name="microsoftdataprotection"></a>DataProtection

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | backupvaults | 否 | 否 |

## <a name="microsoftdatashare"></a>DataShare

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 帳戶 | 是 | 是 |

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
> | serversv2 | 是 | 是 |
> | singleservers | 是 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | artifactsources | 是 | 是 |
> | rollouts | 是 | 是 |
> | servicetopologies | 是 | 是 |
> | servicetopologies/服務 | 是 | 是 |
> | servicetopologies/services/serviceunits | 是 | 是 |
> | steps | 是 | 是 |

## <a name="microsoftdesktopvirtualization"></a>DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | applicationgroups | 否 | 否 |
> | hostpools | 否 | 否 |
> | workspaces | 否 | 否 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | elasticpools | 否 | 否 |
> | elasticpools / iothubtenants | 否 | 否 |
> | iothubs | 是 | 是 |
> | provisioningservices | 是 | 是 |

## <a name="microsoftdevops"></a>DevOps

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
> | 實驗室/servicerunners | 是 | 是 |
> | 實驗室/virtualmachines | 是 | 否 |
> | schedules | 是 | 是 |

## <a name="microsoftdigitaltwins"></a>選取

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | 否 | 否 |

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

## <a name="microsoftenterpriseknowledgegraph"></a>EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 服務 | 是 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | domains | 是 | 是 |
> | eventSubscriptions | 否-無法獨立移動，但會自動與已訂閱的資源一起移動。 | 否-無法獨立移動，但會自動與已訂閱的資源一起移動。 |
> | eventsubscriptions | 否-無法獨立移動，但會自動與已訂閱的資源一起移動。 | 否-無法獨立移動，但會自動與已訂閱的資源一起移動。 |
> | extensiontopics | 否 | 否 |
> | partnernamespaces | 是 | 是 |
> | partnerregistrations | 否 | 否 |
> | partnertopics | 是 | 是 |
> | systemtopics | 是 | 是 |
> | topics | 是 | 是 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | clusters | 是 | 是 |
> | 命名空間 | 是 | 是 |

## <a name="microsoftexperimentation"></a>Microsoft 實驗

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | 否 | 否 |

## <a name="microsoftfalcon"></a>Falcon

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 命名空間 | 是 | 是 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 帳戶 | 否 | 否 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | 否 | 否 |
> | automanagedvmconfigurationprofiles | 否 | 否 |
> | guestconfigurationassignments | 否 | 否 |
> | software | 否 | 否 |
> | softwareupdateprofile | 否 | 否 |
> | softwareupdates | 否 | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | hanainstances | 否 | 否 |
> | sapmonitors | 是 | 是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | clusters | 是 | 是 |

> [!IMPORTANT]
> 您可以將 HDInsight 叢集移至新的訂用帳戶或資源群組。 不過，您無法跨訂用帳戶來移動連結至 HDInsight 叢集的網路資源 (例如虛擬網路、NIC 或負載平衡器)。 此外，您無法將已連結至叢集虛擬機器的 NIC 移至新的資源群組。
>
> 將 HDInsight 叢集移至新的訂用帳戶時，請先移動其他資源 (例如儲存體帳戶)。 然後，移動 HDInsight 叢集本身。

## <a name="microsofthealthcareapis"></a>HealthcareApis

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 服務 | 是 | 是 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 機時 | 是 | 是 |
> | 機器/擴充功能 | 否 | 否 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | datamanagers | 是 | 是 |

## <a name="microsofthydra"></a>Hydra 等

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | components | 否 | 否 |
> | networkscopes | 否 | 否 |

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
> | datacollectionrules | 否 | 否 |
> | diagnosticsettings | 否 | 否 |
> | diagnosticsettingscategories | 否 | 否 |
> | eventtypes | 否 | 否 |
> | extendeddiagnosticsettings | 否 | 否 |
> | guestdiagnosticsettings | 否 | 否 |
> | logdefinitions | 否 | 否 |
> | 記錄 | 否 | 否 |
> | metricalerts | 否 | 否 |
> | metricbaselines | 否 | 否 |
> | metricdefinitions.listasync | 否 | 否 |
> | metricnamespaces | 否 | 否 |
> | metrics | 否 | 否 |
> | myworkbooks | 否 | 否 |
> | notificationgroups | 否 | 否 |
> | privatelinkscopes | 是 | 是 |
> | scheduledqueryrules | 是 | 是 |
> | 拓撲 | 否 | 否 |
> | 交易 | 否 | 否 |
> | vminsightsonboardingstatuses | 否 | 否 |
> | webtests | 是 | 是 |
> | workbooks | 是 | 是 |
> | workbooktemplates | 是 | 是 |

> [!IMPORTANT]
> 請確定移至新的訂用帳戶不會超過訂用帳戶[配額](azure-subscription-service-limits.md#azure-monitor-limits)。

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
> 用於磁片加密的金鑰保存庫無法移至相同訂用帳戶中或跨訂用帳戶的資源群組。

## <a name="microsoftkubernetes"></a>Kubernetes

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | connectedclusters | 否 | 否 |

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
> | 帳戶 | 否 | 否 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 帳戶 | 否 | 否 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | 否 | 否 |
> | integrationaccounts | 是 | 是 |
> | integrationserviceenvironments | 是 | 否 |
> | integrationserviceenvironments/managedapis | 是 | 否 |
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
> | 帳戶 | 否 | 否 |
> | 帳戶/工作區 | 否 | 否 |
> | 帳戶/工作區/專案 | 否 | 否 |
> | teamaccounts | 否 | 否 |
> | teamaccounts/工作區 | 否 | 否 |
> | teamaccounts/工作區/專案 | 否 | 否 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 帳戶 | 否 | 否 |

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

## <a name="microsoftmaintenance"></a>Microsoft. 維護

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | applyupdates | 否 | 否 |
> | configurationassignments | 否 | 否 |
> | maintenanceconfigurations | 是 | 是 |
> | 更新 | 否 | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 身分識別 | 否 | 否 |
> | userassignedidentities | 否 | 否 |

## <a name="microsoftmanagednetwork"></a>ManagedNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | managednetworks | 否 | 否 |
> | managednetworks / managednetworkgroups | 否 | 否 |
> | managednetworks / managednetworkpeeringpolicies | 否 | 否 |
> | 通知 | 否 | 否 |

## <a name="microsoftmanagedservices"></a>ManagedServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | registrationassignments | 否 | 否 |
> | registrationdefinitions | 否 | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 帳戶 | 是 | 是 |
> | 帳戶/privateatlases | 是 | 是 |

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
> | windowsazure.mediaservices.extensions/liveevent | 是 | 是 |
> | windowsazure.mediaservices.extensions/streamingendpoint | 是 | 是 |

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | appclusters | 否 | 否 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | 是 | 是 |
> | migrateprojects | 是 | 是 |
> | movecollections | 否 | 否 |
> | projects | 否 | 否 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | netappaccounts | 否 | 否 |
> | netappaccounts / backuppolicies | 否 | 否 |
> | netappaccounts / capacitypools | 否 | 否 |
> | netappaccounts/capacitypools/磁片區 | 否 | 否 |
> | netappaccounts/capacitypools/磁片區/mounttargets | 否 | 否 |
> | netappaccounts/capacitypools/磁片區/快照集 | 否 | 否 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | applicationgateways | 否 | 否 |
> | applicationgatewaywebapplicationfirewallpolicies | 否 | 否 |
> | applicationsecuritygroups | 是 | 是 |
> | azurefirewalls | 是 | 是 |
> | bastionhosts | 否 | 否 |
> | connections | 是 | 是 |
> | ddoscustompolicies | 是 | 是 |
> | ddosprotectionplans | 否 | 否 |
> | dnszones | 是 | 是 |
> | expressroutecircuits | 否 | 否 |
> | expressroutegateways | 否 | 否 |
> | firewallpolicies | 是 | 是 |
> | frontdoors | 否 | 否 |
> | frontdoorwebapplicationfirewallpolicies | 否 | 否 |
> | ipgroups | 是 | 是 |
> | loadbalancers | 是-基本 SKU<br>無標準 SKU | 是-基本 SKU<br>無標準 SKU |
> | localnetworkgateways | 是 | 是 |
> | natgateways | 是 | 是 |
> | networkexperimentprofiles | 是 | 是 |
> | networkintentpolicies | 是 | 是 |
> | networkinterfaces | 是 | 是 |
> | networkprofiles | 否 | 否 |
> | networksecuritygroups | 是 | 是 |
> | networkwatchers | 是 | 否 |
> | networkwatchers / connectionmonitors | 是 | 否 |
> | networkwatchers / flowlogs | 是 | 否 |
> | networkwatchers / pingmeshes | 是 | 否 |
> | p2svpngateways | 否 | 否 |
> | privatednszones | 是 | 是 |
> | privatednszones / virtualnetworklinks | 是 | 是 |
> | privateendpointredirectmaps | 否 | 否 |
> | privateendpoints | 是 | 是 |
> | privatelinkservices | 否 | 否 |
> | publicipaddresses | 是-基本 SKU<br>無標準 SKU | 是-基本 SKU<br>無標準 SKU |
> | publicipprefixes | 是 | 是 |
> | routefilters | 否 | 否 |
> | routetables | 是 | 是 |
> | serviceendpointpolicies | 是 | 是 |
> | trafficmanagerprofiles | 是 | 是 |
> | virtualhubs | 否 | 否 |
> | virtualnetworkgateways | 是 | 是 |
> | virtualnetworks | 是 | 是 |
> | virtualnetworktaps | 否 | 否 |
> | virtualrouters | 是 | 是 |
> | virtualwans | 否 | 否 |
> | vpngateways （虛擬 WAN） | 否 | 否 |
> | vpnserverconfigurations | 否 | 否 |
> | vpnsites （虛擬 WAN） | 否 | 否 |
> | webapplicationfirewallpolicies | 是 | 是 |

> [!IMPORTANT]
> 請參閱[網路移動指引](./move-limitations/networking-move-limitations.md)。

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 命名空間 | 是 | 是 |
> | 命名空間/notificationhubs | 是 | 是 |

## <a name="microsoftobjectstore"></a>Objectstore 會

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | osnamespaces | 是 | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | clusters | 否 | 否 |
> | storageinsightconfigs | 否 | 否 |
> | workspaces | 是 | 是 |

> [!IMPORTANT]
> 請確定移動到新的訂用帳戶不會超過訂用帳戶[配額](azure-subscription-service-limits.md#azure-monitor-limits)。

> [!IMPORTANT]
> 無法移動具有連結自動化帳戶的工作區。 開始移動作業之前，請務必將任何自動化帳戶取消連結。   

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | managementassociations | 否 | 否 |
> | managementconfigurations | 是 | 是 |
> | solutions | 是 | 是 |
> | 檢視 | 是 | 是 |

## <a name="microsoftpeering"></a>Microsoft 對等互連

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 對等互連 | 是 | 是 |
> | peeringservices | 否 | 否 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | policyevents | 否 | 否 |
> | policystates | 否 | 否 |
> | policytrackedresources | 否 | 否 |
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

## <a name="microsoftprojectbabylon"></a>ProjectBabylon

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 帳戶 | 否 | 否 |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 帳戶 | 否 | 否 |

## <a name="microsoftproviderhub"></a>ProviderHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | rollouts | 否 | 否 |

## <a name="microsoftquantum"></a>Microsoft 量子

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | workspaces | 否 | 否 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | 否 | 否 |
> | replicationeligibilityresults | 否 | 否 |
> | vaults | 是 | 是 |

> [!IMPORTANT]
> 請參閱復原[服務移動指引](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)。

## <a name="microsoftredhatopenshift"></a>RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | openshiftclusters | 否 | 否 |

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
> | availabilitystatuses | 否 | 否 |
> | childavailabilitystatuses | 否 | 否 |
> | childresources | 否 | 否 |
> | 活動 | 否 | 否 |
> | 通知 | 否 | 否 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | deploymentscripts | 否 | 否 |
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
> 您無法在單一作業中移動不同區域中的數個搜尋資源。 而是要在不同作業中移動它們。

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | 否 | 否 |
> | advancedthreatprotectionsettings | 否 | 否 |
> | assessmentmetadata | 否 | 否 |
> | 評量 | 否 | 否 |
> | 自動化 | 是 | 是 |
> | complianceresults | 否 | 否 |
> | 規範 | 否 | 否 |
> | datacollectionagents | 否 | 否 |
> | devicesecuritygroups | 否 | 否 |
> | informationprotectionpolicies | 否 | 否 |
> | iotsecuritysolutions | 是 | 是 |
> | servervulnerabilityassessments | 否 | 否 |

## <a name="microsoftsecurityinsights"></a>SecurityInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | aggregations | 否 | 否 |
> | alertrules | 否 | 否 |
> | alertruletemplates | 否 | 否 |
> | 書籤 | 否 | 否 |
> | 案例 | 否 | 否 |
> | dataconnectors | 否 | 否 |
> | dataconnectorscheckrequirements | 否 | 否 |
> | 實體 | 否 | 否 |
> | entityqueries | 否 | 否 |
> | 次數 | 否 | 否 |
> | officeconsents | 否 | 否 |
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

## <a name="microsoftservices"></a>Microsoft 服務

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | rollouts | 否 | 否 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | signalr | 是 | 是 |

## <a name="microsoftsoftwareplan"></a>SoftwarePlan

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | 否 | 否 |

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
> | instancepools | 否 | 否 |
> | 位置 | 是 | 是 |
> | managedinstances | 否 | 否 |
> | managedinstances/資料庫 | 否 | 否 |
> | servers | 是 | 是 |
> | 伺服器/資料庫 | 是 | 是 |
> | 伺服器/elasticpools | 是 | 是 |
> | 伺服器/jobaccounts | 是 | 是 |
> | 伺服器/jobagents | 是 | 是 |
> | virtualclusters | 是 | 是 |

> [!IMPORTANT]
> 資料庫和伺服器必須位於相同的資源群組中。 當您移動 SQL 伺服器時，其所有資料庫也會跟著移動。 此行為會套用至 Azure SQL Database 和 Azure SQL Data Warehouse 資料庫。

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

## <a name="microsoftstoragecache"></a>StorageCache

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 緩衝區 | 否 | 否 |

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
> 在執行中狀態時，無法移動串流分析作業。

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | environments | 否 | 否 |
> | 環境/eventsources | 否 | 否 |
> | 執行個體 | 否 | 否 |
> | 實例/環境 | 否 | 否 |
> | 實例/環境/eventsources | 否 | 否 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | createsubscription | 否 | 否 |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | supporttickets | 否 | 否 |

## <a name="microsoftsynapse"></a>Synapse

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | workspaces | 否 | 否 |
> | 工作區/bigdatapools | 否 | 否 |
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
> | 環境/eventsources | 是 | 是 |
> | 環境/referencedatasets | 是 | 是 |

## <a name="microsofttoken"></a>Microsoft Token

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
> | 帳戶/擴充功能 | 是 | 是 |
> | 帳戶/專案 | 是 | 是 |

> [!IMPORTANT]
> 若要變更 Azure DevOps 的訂用帳戶，請參閱[變更用於計費的 Azure 訂用](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)帳戶。

## <a name="microsoftvmwarecloudsimple"></a>VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | 否 | 否 |
> | dedicatedcloudservices | 否 | 否 |
> | virtualmachines | 否 | 否 |

## <a name="microsoftvnfmanager"></a>VnfManager

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | devices | 否 | 否 |
> | vnfs | 否 | 否 |

## <a name="microsoftvsonline"></a>VSOnline

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 帳戶 | 是 | 是 |
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
> | kubeenvironments | 是 | 是 |
> | serverfarms | 是 | 是 |
> | sites | 是 | 是 |
> | sites/premieraddons | 是 | 是 |
> | 網站/位置 | 是 | 是 |
> | staticsites | 否 | 否 |

> [!IMPORTANT]
> 請參閱[App Service 移動指引](./move-limitations/app-service-move-limitations.md)。

## <a name="microsoftwindowsesu"></a>WindowsESU

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | multipleactivationkeys | 否 | 否 |

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
> | monitorinstances | 否 | 否 |
> | monitors | 否 | 否 |
> | notificationsettings | 否 | 否 |

## <a name="third-party-services"></a>協力廠商服務

協力廠商服務目前不支援移動作業。

## <a name="next-steps"></a>後續步驟
如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](move-resource-group-and-subscription.md)。

若要以逗號區隔值檔案的形式取得相同資料，請下載 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)。
