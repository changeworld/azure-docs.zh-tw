---
title: 跨區域移動 Azure 資源的支援
description: 列出可跨 Azure 區域移動的 Azure 資源類型
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 05/31/2020
ms.author: raynew
ms.openlocfilehash: 0510df504c8de70cfb6a486f394db6da65dbfce2
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057682"
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
> | domainservices | No | 
> | domainservices/replicasets | No | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | tenants | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | actionrules | No | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | servers | No |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 服務 |  是（使用範本） <br/><br/> [跨區域移動 API 管理](../../api-management/api-management-howto-migrate.md)。 | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | configurationstores | No | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | apiapps | 是（使用範本）<br/><br/> [將 App Service 應用程式移至另一個區域](../../app-service/manage-move-across-regions.md) | 
> | appidentities | No | 
> | gateways | No | 


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
> | automationaccounts / configurations | No | 
> | automationaccounts / runbooks | No | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | b2cdirectories | 否 | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | sqlserverregistrations | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | registrations | No | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | batchaccounts |  Batch 帳戶無法直接從一個區域移至另一個區域，但您可以使用範本來匯出範本、加以修改，並將範本部署到新的區域。 <br/><br/> 瞭解如何[跨區域移動 Batch 帳戶](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | clusters | No <br/><br/> Azure Batch AI 服務已[淘汰](/previous-versions/azure/batch-ai/overview-what-happened-batch-ai)。
> | fileservers | No | 
> | jobs | No | 
> | workspaces | No | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | mapapis | No | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | biztalk | No | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | blockchainmembers | No <br/><br/> 區塊鏈網路不能有不同區域中的節點。 
> | watchers | No | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | blueprintassignments | No | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | botservices | No | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | redis | No | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | No |
> | 設定檔 | No | 
> | profiles / endpoints | No | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | certificateorders | No | 


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
> | virtualnetworks | No | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | storageaccounts | 是 |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | No | 
> | 認知搜尋 | 支援手動步驟。<br/><br/> 瞭解如何將[Azure 認知搜尋服務移至另一個區域](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | availabilitysets | No | 
> | diskencryptionsets | No | 
> | disks | No | 
> | galleries | No | 
> | galleries / images | No | 
> | galleries / images / versions | No | 
> | hostgroups | No | 
> | hostgroups / hosts | No | 
> | images | No | 
> | proximityplacementgroups | No | 
> | restorepointcollections | No | 
> | sharedvmimages | No | 
> | sharedvmimages / versions | No | 
> | snapshots | No | 
> | virtualmachines | 是 | 
> | virtualmachines / extensions | No | 
> | virtualmachinescalesets | No | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | containergroups | No | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | containergroups | No | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | registries | No |  
> | registries / buildtasks | No |  
> | registries / replications | No | 
> | registries / tasks | No |  
> | registries / webhooks | No | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | containerservices | 否。<br/><br/> 服務已[淘汰](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/)。
> | managedclusters | No | 
> | openshiftmanagedclusters | No | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 應用程式所需 | No | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 連接器 | No |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | hubs | 否 |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | resourceproviders | No | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | jobs | No | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | databoxedgedevices | No | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | workspaces | No | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | catalogs | No | 
> | datacatalogs | No | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | connectionmanagers | No | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | packages | No | 
> | plans | No | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | datafactories | No | 
> | factories | No |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | datalakeaccounts | No | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 服務 | No | 
> | services / projects | No | 
> | slots | No | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | servers | 如果服務是以異地多餘的備份儲存體布建，您可以使用異地還原，在其他區域中進行還原。 [深入了解](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage)

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | servers | No |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | servergroups | No | 
> | servers | No |  
> | serversv2 | No | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | artifactsources | 否 | 
> | rollouts | 否 |  
> | servicetopologies | No | 
> | servicetopologies / services | No |  
> | servicetopologies / services / serviceunits | No | 
> | steps | No | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | elasticpools | 否。 資源未公開。
> | elasticpools / iothubtenants | 否。 資源未公開。
> | iothubs | 是。 [深入了解](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | No | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | controllers | No | 
> | AKS 叢集 | No<br/><br/> [深入瞭解](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region)移至另一個區域。

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | labcenters | No | 
> | labs | No | 
> | labs / environments | No |  
> | labs / servicerunners | No | 
> | labs / virtualmachines | No |  
> | schedules | No |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | databaseaccounts | No | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | domains | No | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 服務 | No |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | domains | No |  
> | topics | No | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | clusters | No |  
> | 命名空間 | 是（使用範本）<br/><br/> [將事件中樞命名空間移至另一個區域](../../event-hubs/move-across-regions.md) | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | hanainstances | 否 | 
> | sapmonitors | No |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | clusters | No | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 服務 | No |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | machines | 否 | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | datamanagers |  No | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | jobs |  No | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | No | 
> | actiongroups |  No | 
> | activitylogalerts | No | 
> | alertrules |  否 | 
> | autoscalesettings |  No | 
> | components |  No |  
> | guestdiagnosticsettings | No | 
> | metricalerts | No | 
> | notificationgroups | No | 
> | notificationrules | No | 
> | scheduledqueryrules |  No | 
> | webtests |  No | 
> | workbooks |  No |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | checknameavailability |  否。<br/><br/> IoT Central 適用于地理位置，而不是區域。
> | graph | No

## <a name="microsoftiothub"></a>IoTHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> |  iothub |  是（複製中樞） <br/><br/> [將 IoT 中樞複製到另一個區域](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | checknameavailability |  No |  
> | graph |  No | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | hsmpools | No | 
> | vaults |  No | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | clusters |  No |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | labaccounts | No | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否，這是一種全域服務。

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | hostingenvironments | No | 
> | integrationaccounts |  No |  
> | integrationserviceenvironments | No | 
> | isolatedenvironments | 否 | 
> | workflows |  No |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | commitmentplans |  No | 
> | webservices |  No | 
> | workspaces |  No | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | operationalizationclusters |  No | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | No | 
> | accounts / workspaces | No | 
> | accounts / workspaces / projects | No | 
> | teamaccounts | No | 
> | teamaccounts / workspaces | No | 
> | teamaccounts / workspaces / projects | No | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | hostingaccounts | No | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | workspaces | No | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | userassignedidentities | No | 

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
> | mediaservices |  No | 
> | mediaservices / liveevents |  No | 
> | mediaservices / streamingendpoints |  No | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | appclusters | No | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | assessmentprojects | No | 
> | migrateprojects | No | 
> | projects | No | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | netappaccounts | No | 
> | netappaccounts / capacitypools | No | 
> | netappaccounts / capacitypools / volumes | No | 
> | netappaccounts / capacitypools / volumes / mounttargets | No | 
> | netappaccounts / capacitypools / volumes / snapshots | No | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | applicationgateways | No | 
> | applicationgatewaywebapplicationfirewallpolicies | 否 | 
> | applicationsecuritygroups |  No |  
> | azurefirewalls |  No |  
> | bastionhosts | No | 
> | connections |  No | 
> | ddoscustompolicies |  No | 
> | ddosprotectionplans | No | 
> | dnszones |  No | 
> | expressroutecircuits | No | 
> | expressroutecrossconnections | No | 
> | expressroutegateways | No | 
> | expressrouteports | No | 
> | frontdoors | No | 
> | frontdoorwebapplicationfirewallpolicies | No | 
> | loadbalancers | Yes <br/><br/> 您可以將現有的設定匯出為範本，並在新的區域中部署範本。 瞭解如何移動[外部](../..//load-balancer/move-across-regions-external-load-balancer-portal.md)或[內部](../../load-balancer/move-across-regions-internal-load-balancer-portal.md)負載平衡器。 |
> | localnetworkgateways |  No | 
> | natgateways |  No | 
> | networkintentpolicies |  No | 
> | networkinterfaces | Yes | 
> | networkprofiles | No | 
> | networksecuritygroups | 是 | 
> | networkwatchers |  No |  
> | networkwatchers / connectionmonitors |  No | 
> | networkwatchers/鏡頭 |  No | 
> | networkwatchers / pingmeshes |  No | 
> | p2svpngateways | No | 
> | privatednszones |  No |  
> | privatednszones / virtualnetworklinks |  No |  
> | privateendpoints | No | 
> | privatelinkservices | No | 
> | publicipaddresses | Yes<br/><br/> 您可以將現有的公用 IP 位址設定匯出為範本，並在新的區域中部署範本。 [深入瞭解](../../virtual-network/move-across-regions-publicip-portal.md)如何移動公用 IP 位址。 |
> | publicipprefixes | No | 
> | routefilters | No | 
> | routetables |  No | 
> | serviceendpointpolicies |  No | 
> | trafficmanagerprofiles |  No | 
> | virtualhubs | No | 
> | virtualnetworkgateways |  No |  
> | virtualnetworks |  No | 
> | virtualnetworktaps | No | 
> | virtualwans | No | 
> | vpngateways (虛擬 WAN) | 否 | 
> | vpnsites (虛擬 WAN) | 否 | 
> | webapplicationfirewallpolicies |  No | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 命名空間 |  No | 
> | namespaces / notificationhubs |  No |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | workspaces |  No | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | managementconfigurations |  No | 
> | 檢視 |  No | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | peerings | No | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | dashboards | No | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | rootresources | No | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | workspacecollections |  No | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | capacities |  No | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | vaults | 否。<br/><br/> 不支援跨 Azure 區域移動復原服務保存庫以進行 Azure 備份。<br/><br/> 在 Azure Site Recovery 的復原服務保存庫中，您可以在目的地區域中[停用並重新](../../site-recovery/move-vaults-across-regions.md)建立保存庫。 | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 命名空間 |  No | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 查詢 |  No |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 應用程式所需 |  No | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | flows |  No |  
> | jobcollections |  No | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | searchservices |  No | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  No | 
> | playbookconfigurations | No | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | gateways | No | 
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
> | 應用程式所需 | No | 
> | clusters |  No | 
> | clusters / applications | No | 
> | containergroups | No | 
> | containergroupsets | No | 
> | edgeclusters | No | 
> | networks | No | 
> | secretstores | No | 
> | 磁碟區 | No | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 應用程式所需 |  No | 
> | containergroups | No | 
> | gateways |  No | 
> | networks |  No | 
> | 密碼 |  No | 
> | 磁碟區 |  No |  

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | signalr |  No |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | appliancedefinitions | No | 
> | appliances | No | 
> | applicationdefinitions | No | 
> | 應用程式所需 | No | 
> | jitrequests | No | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | instancepools | No | 
> | managedinstances | Yes | 
> | managedinstances / databases | Yes | 
> | servers | Yes | 
> | servers / databases | Yes | 
> | servers / elasticpools | Yes | 
> | virtualclusters | 是 | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  No |  
> | sqlvirtualmachines |  No |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | dwvm | No | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | storageaccounts | Yes<br/><br/> [將 Azure 儲存體帳戶移至另一個區域](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | caches | No | 

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
> | storagesyncservices | No | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | managers | No | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | streamingjobs |  No |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | environments | No | 
> | environments / eventsources | No | 
> | 執行個體 | No | 
> | instances / environments | No | 
> | instances / environments / eventsources | No | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | providerregistrations | No | 
> | resources | No | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | environments |  No | 
> | environments / eventsources |  No |  
> | environments / referencedatasets |  No | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | stores | No | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | imagetemplates | No | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | account |  No | 
> | account / extension |  No | 
> | account / project |  No | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | No | 
> | dedicatedcloudservices | No | 
> | virtualmachines | No | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | certificates | No | 
> | connectiongateways |  No |  
> | connections |  No |  
> | customapis |  No | 
> | hostingenvironments | No | 
> | serverfarms |  No |  
> | sites |  No | 
> | sites / premieraddons |  No |  
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
> | applicationgroups | No | 
> | hostpools | No | 
> | workspaces | 否 | 

## <a name="third-party-services"></a>協力廠商服務

協力廠商服務目前不支援移動作業。
