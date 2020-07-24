---
title: 跨區域移動 Azure 資源的支援
description: 列出可跨 Azure 區域移動的 Azure 資源類型
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 07/21/2020
ms.author: raynew
ms.openlocfilehash: 70f981f2763dd36f0f417faec6c81e168e9856e7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87040959"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>跨區域移動 Azure 資源的支援

本文會確認是否支援將 Azure 資源類型移至另一個 Azure 區域。 

跳至資源提供者命名空間：
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
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
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
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
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | domainservices | 否 | 
> | domainservices/replicasets | 否 | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | tenants | 否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | actionrules | 否 | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | servers | 否 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 服務 |  是（使用範本） <br/><br/> [跨區域移動 API 管理](../../api-management/api-management-howto-migrate.md)。 | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | configurationstores | 否 | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | apiapps | 是（使用範本）<br/><br/> [將 App Service 應用程式移至另一個區域](../../app-service/manage-move-across-regions.md) | 
> | appidentities | 否 | 
> | gateways | 否 | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | policyassignments | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | automationaccounts | 是（使用範本） <br/><br/> [使用異地複寫](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts / configurations | 否 | 
> | automationaccounts / runbooks | 否 | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | b2cdirectories | 否 | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | sqlserverregistrations | 否 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | registrations | 否 | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | batchaccounts |  Batch 帳戶無法直接從一個區域移至另一個區域，但您可以使用範本來匯出範本、加以修改，並將範本部署到新的區域。 <br/><br/> 瞭解如何[跨區域移動 Batch 帳戶](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | clusters | 否 <br/><br/> Azure Batch AI 服務已[淘汰](/previous-versions/azure/batch-ai/overview-what-happened-batch-ai)。
> | fileservers | 否 | 
> | jobs | 否 | 
> | workspaces | 否 | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | mapapis | 否 | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | biztalk | 否 | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | blockchainmembers | 否 <br/><br/> 區塊鏈網路不能有不同區域中的節點。 
> | watchers | 否 | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | blueprintassignments | 否 | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | botservices | 否 | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | redis | 否 | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | 否 |
> | 設定檔 | 否 | 
> | profiles / endpoints | 否 | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | certificateorders | 否 | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | domainnames | 未規劃傳統服務的任何工作。
> | virtualmachines | 否 | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | networksecuritygroups | 未規劃傳統服務的任何工作。
> | reservedips | 否 | 
> | virtualnetworks | 否 | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | storageaccounts | 是 |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否 | 
> | 認知搜尋 | 支援手動步驟。<br/><br/> 瞭解如何將[Azure 認知搜尋服務移至另一個區域](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | availabilitysets | 否 | 
> | diskencryptionsets | 否 | 
> | disks | 否 | 
> | galleries | 否 | 
> | galleries / images | 否 | 
> | galleries / images / versions | 否 | 
> | hostgroups | 否 | 
> | hostgroups / hosts | 否 | 
> | images | 否 | 
> | proximityplacementgroups | 否 | 
> | restorepointcollections | 否 | 
> | sharedvmimages | 否 | 
> | sharedvmimages / versions | 否 | 
> | snapshots | 否 | 
> | virtualmachines | 是 | 
> | virtualmachines / extensions | 否 | 
> | virtualmachinescalesets | 否 | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | containergroups | 否 | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | containergroups | 否 | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | registries | 否 |  
> | registries / buildtasks | 否 |  
> | registries / replications | 否 | 
> | registries / tasks | 否 |  
> | registries / webhooks | 否 | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | containerservices | 不可以。<br/><br/> 服務已[淘汰](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/)。
> | managedclusters | 否 | 
> | openshiftmanagedclusters | 否 | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 應用程式所需 | 否 | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 連接器 | 否 |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | hubs | 否 |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | resourceproviders | 否 | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | jobs | 否 | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | databoxedgedevices | 否 | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | workspaces | 否 | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | catalogs | 否 | 
> | datacatalogs | 否 | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | connectionmanagers | 否 | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | packages | 否 | 
> | plans | 否 | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | datafactories | 否 | 
> | factories | 否 |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | datalakeaccounts | 否 | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 服務 | 否 | 
> | services / projects | 否 | 
> | slots | 否 | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | servers | 您可以使用跨區域讀取複本來移動現有的伺服器。 [深入了解](../../postgresql/howto-move-regions-portal.md)。<br/><br/> 如果服務是以異地多餘的備份儲存體布建，您可以使用異地還原，在其他區域中進行還原。 [深入了解](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage)。

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | servers | 您可以使用跨區域讀取複本來移動現有的伺服器。 [深入了解](../../mysql/howto-move-regions-portal.md)。

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | servergroups | 否 | 
> | servers | 您可以使用跨區域讀取複本來移動現有的伺服器。 [深入瞭解](../../postgresql/howto-move-regions-portal.md)。
> | serversv2 | 否 | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | artifactsources | 否 | 
> | rollouts | 否 |  
> | servicetopologies | 否 | 
> | servicetopologies / services | 否 |  
> | servicetopologies / services / serviceunits | 否 | 
> | steps | 否 | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | elasticpools | 不可以。 資源未公開。
> | elasticpools / iothubtenants | 不可以。 資源未公開。
> | iothubs | 是。 [深入了解](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | 否 | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | controllers | 否 | 
> | AKS 叢集 | 否<br/><br/> [深入瞭解](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region)移至另一個區域。

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | labcenters | 否 | 
> | labs | 否 | 
> | labs / environments | 否 |  
> | labs / servicerunners | 否 | 
> | labs / virtualmachines | 否 |  
> | schedules | 否 |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | databaseaccounts | 否 | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | domains | 否 | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 服務 | 否 |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | domains | 否 |  
> | topics | 否 | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | clusters | 否 |  
> | 命名空間 | 是（使用範本）<br/><br/> [將事件中樞命名空間移至另一個區域](../../event-hubs/move-across-regions.md) | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | hanainstances | 否 | 
> | sapmonitors | 否 |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | clusters | 否 | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 服務 | 否 |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | machines | 否 | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | datamanagers |  否 | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | jobs |  否 | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否 | 
> | actiongroups |  否 | 
> | activitylogalerts | 否 | 
> | alertrules |  否 | 
> | autoscalesettings |  否 | 
> | components |  否 |  
> | guestdiagnosticsettings | 否 | 
> | metricalerts | 否 | 
> | notificationgroups | 否 | 
> | notificationrules | 否 | 
> | scheduledqueryrules |  否 | 
> | webtests |  否 | 
> | workbooks |  否 |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | checknameavailability |  不可以。<br/><br/> IoT Central 適用于地理位置，而不是區域。
> | graph | 否

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> |  iothub |  是（複製中樞） <br/><br/> [將 IoT 中樞複製到另一個區域](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | checknameavailability |  否 |  
> | graph |  否 | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | hsmpools | 否 | 
> | vaults |  否 | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | clusters |  否 |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | labaccounts | 否 | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否，這是一種全域服務。

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | hostingenvironments | 否 | 
> | integrationaccounts |  否 |  
> | integrationserviceenvironments | 否 | 
> | isolatedenvironments | 否 | 
> | workflows |  否 |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | commitmentplans |  否 | 
> | webservices |  否 | 
> | workspaces |  否 | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | operationalizationclusters |  否 | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否 | 
> | accounts / workspaces | 否 | 
> | accounts / workspaces / projects | 否 | 
> | teamaccounts | 否 | 
> | teamaccounts / workspaces | 否 | 
> | teamaccounts / workspaces / projects | 否 | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | hostingaccounts | 否 | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | workspaces | 否 | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | userassignedidentities | 否 | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts |  否，Azure 地圖服務是地理空間服務。 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | classicdevservices | 未規劃傳統服務的任何工作 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | mediaservices |  否 | 
> | mediaservices / liveevents |  否 | 
> | mediaservices / streamingendpoints |  否 | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | appclusters | 否 | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | assessmentprojects | 否 | 
> | migrateprojects | 否 | 
> | projects | 否 | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | netappaccounts | 否 | 
> | netappaccounts / capacitypools | 否 | 
> | netappaccounts / capacitypools / volumes | 否 | 
> | netappaccounts / capacitypools / volumes / mounttargets | 否 | 
> | netappaccounts / capacitypools / volumes / snapshots | 否 | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | applicationgateways | 否 | 
> | applicationgatewaywebapplicationfirewallpolicies | 否 | 
> | applicationsecuritygroups |  否 |  
> | azurefirewalls |  否 |  
> | bastionhosts | 否 | 
> | connections |  否 | 
> | ddoscustompolicies |  否 | 
> | ddosprotectionplans | 否 | 
> | dnszones |  否 | 
> | expressroutecircuits | 否 | 
> | expressroutecrossconnections | 否 | 
> | expressroutegateways | 否 | 
> | expressrouteports | 否 | 
> | frontdoors | 否 | 
> | frontdoorwebapplicationfirewallpolicies | 否 | 
> | loadbalancers | 是 <br/><br/> 您可以將現有的設定匯出為範本，並在新的區域中部署範本。 瞭解如何移動[外部](../..//load-balancer/move-across-regions-external-load-balancer-portal.md)或[內部](../../load-balancer/move-across-regions-internal-load-balancer-portal.md)負載平衡器。 |
> | localnetworkgateways |  否 | 
> | natgateways |  否 | 
> | networkintentpolicies |  否 | 
> | networkinterfaces | 是 | 
> | networkprofiles | 否 | 
> | networksecuritygroups | 是 | 
> | networkwatchers |  否 |  
> | networkwatchers / connectionmonitors |  否 | 
> | networkwatchers/鏡頭 |  否 | 
> | networkwatchers / pingmeshes |  否 | 
> | p2svpngateways | 否 | 
> | privatednszones |  否 |  
> | privatednszones / virtualnetworklinks |  否 |  
> | privateendpoints | 否 | 
> | privatelinkservices | 否 | 
> | publicipaddresses | 是<br/><br/> 您可以將現有的公用 IP 位址設定匯出為範本，並在新的區域中部署範本。 [深入瞭解](../../virtual-network/move-across-regions-publicip-portal.md)如何移動公用 IP 位址。 |
> | publicipprefixes | 否 | 
> | routefilters | 否 | 
> | routetables |  否 | 
> | serviceendpointpolicies |  否 | 
> | trafficmanagerprofiles |  否 | 
> | virtualhubs | 否 | 
> | virtualnetworkgateways |  否 |  
> | virtualnetworks |  否 | 
> | virtualnetworktaps | 否 | 
> | virtualwans | 否 | 
> | vpngateways (虛擬 WAN) | 否 | 
> | vpnsites (虛擬 WAN) | 否 | 
> | webapplicationfirewallpolicies |  否 | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 命名空間 |  否 | 
> | namespaces / notificationhubs |  否 |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | workspaces |  否 | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | managementconfigurations |  否 | 
> | 檢視 |  否 | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | peerings | 否 | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | dashboards | 否 | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | rootresources | 否 | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | workspacecollections |  否 | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | capacities |  否 | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | vaults | 不可以。<br/><br/> 不支援跨 Azure 區域移動復原服務保存庫以進行 Azure 備份。<br/><br/> 在 Azure Site Recovery 的復原服務保存庫中，您可以在目的地區域中[停用並重新](../../site-recovery/move-vaults-across-regions.md)建立保存庫。 | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 命名空間 |  否 | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 查詢 |  否 |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 應用程式所需 |  否 | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | flows |  否 |  
> | jobcollections |  否 | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | searchservices |  否 | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  否 | 
> | playbookconfigurations | 否 | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | gateways | 否 | 
> | nodes | 否 | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 命名空間 |  否 | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 應用程式所需 | 否 | 
> | clusters |  否 | 
> | clusters / applications | 否 | 
> | containergroups | 否 | 
> | containergroupsets | 否 | 
> | edgeclusters | 否 | 
> | networks | 否 | 
> | secretstores | 否 | 
> | 磁碟區 | 否 | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 應用程式所需 |  否 | 
> | containergroups | 否 | 
> | gateways |  否 | 
> | networks |  否 | 
> | 密碼 |  否 | 
> | 磁碟區 |  否 |  

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | signalr |  否 |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | appliancedefinitions | 否 | 
> | appliances | 否 | 
> | applicationdefinitions | 否 | 
> | 應用程式所需 | 否 | 
> | jitrequests | 否 | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | instancepools | 否 | 
> | managedinstances | 是 | 
> | managedinstances / databases | 是 | 
> | servers | 是 | 
> | servers / databases | 是 | 
> | servers / elasticpools | 是 | 
> | virtualclusters | 是 | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  否 |  
> | sqlvirtualmachines |  否 |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | dwvm | 否 | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | storageaccounts | 是<br/><br/> [將 Azure 儲存體帳戶移至另一個區域](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | caches | 否 | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | storagesyncservices |  否 | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | storagesyncservices | 否 | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | storagesyncservices | 否 | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | managers | 否 | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | streamingjobs |  否 |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | environments | 否 | 
> | environments / eventsources | 否 | 
> | 執行個體 | 否 | 
> | instances / environments | 否 | 
> | instances / environments / eventsources | 否 | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | providerregistrations | 否 | 
> | resources | 否 | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | environments |  否 | 
> | environments / eventsources |  否 |  
> | environments / referencedatasets |  否 | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | stores | 否 | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | imagetemplates | 否 | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | account |  否 | 
> | account / extension |  否 | 
> | account / project |  否 | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | 否 | 
> | dedicatedcloudservices | 否 | 
> | virtualmachines | 否 | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | certificates | 否 | 
> | connectiongateways |  否 |  
> | connections |  否 |  
> | customapis |  否 | 
> | hostingenvironments | 否 | 
> | serverfarms |  否 |  
> | sites |  否 | 
> | sites / premieraddons |  否 |  
> | sites / slots |  否 |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | deviceservices | 否 | 

## <a name="microsoftwindowsvirtualdesktop"></a>WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | applicationgroups | 否 | 
> | hostpools | 否 | 
> | workspaces | 否 | 

## <a name="third-party-services"></a>協力廠商服務

協力廠商服務目前不支援移動作業。
