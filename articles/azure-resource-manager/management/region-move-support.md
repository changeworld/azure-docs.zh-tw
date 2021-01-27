---
title: 跨區域移動 Azure 資源的支援
description: 列出可在 Azure 區域間移動的 Azure 資源類型
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 49c5828e02bf96a536ff14f6b84e81f7adbe3090
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806892"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>跨區域移動 Azure 資源的支援

本文確認是否支援將 Azure 資源類型移至另一個 Azure 區域。 

跳至資源提供者命名空間：
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
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
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | domainservices | 否 | 


## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | diagnosticsettings | 否 |
> | diagnosticsettingscategories | 否 |
> | privatelinkforazuread | 否 |
> | tenants |  否 |

## <a name="microsoftaddons"></a>微軟。外掛程式

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | supportproviders | 否 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
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
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | 組態 | 否 | 
> | generaterecommendations | 否 |
> | 中繼資料 | 否 |
> | Mahout | 否 |
> | suppressions | 否 | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | actionrules | 否 | 
> | alerts | 否 | 
> | alertslist | 否 | 
> | alertsmetadata | 否 | 
> | alertssummary | 否 | 
> | alertssummarylist | 否 | 
> | smartdetectoralertrules | 否 | 
> | smartgroups | 否 | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | servers | 否 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | reportfeedback | 否 |
> | 服務 |  是 (使用範本)  <br/><br/> [跨區域移動 API 管理](../../api-management/api-management-howto-migrate.md)。 | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | configurationstores | 否 | 
> | configurationstores / eventgridfilters | 否 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | spring | 否 | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | apiapps | 是 (使用範本) <br/><br/> [將 App Service 應用程式移至另一個區域](../../app-service/manage-move-across-regions.md) | 
> | appidentities | 否 | 
> | gateways | 否 | 

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | attestationproviders | 否 | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | classicadministrators | 否 | 
> | dataaliases | 否 | 
> | denyassignments | 否 | 
> | elevateaccess | 否 | 
> | findorphanroleassignments | 否 | 
> | locks | 否 | 
> | 權限 | 否 | 
> | policyassignments | 否 | 
> | policydefinitions | 否 | 
> | policysetdefinitions | 否 | 
> | privatelinkassociations | 否 | 
> | resourcemanagementprivatelinks | 否 | 
> | roleassignments | 否 | 
> | roleassignmentsusagemetrics | 否 | 
> | roledefinitions | 否 | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | automationaccounts | 是 (使用範本)  <br/><br/> [使用異地複寫](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts / configurations | 否 | 
> | automationaccounts / runbooks | 否 | 

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 訂用帳戶 |
> | ------------- | ----------- | 
> | privateclouds | 否 | 


## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | b2cdirectories | 否 | 
> | b2ctenants | 否 | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | datacontrollers | 否 | 
> | hybriddatamanagers | 否 | 
> | postgresinstances | 否 | 
> | sqlinstances | 否 | 
> | sqlmanagedinstances | 否 |
> | sqlserverinstances | 否 | 
> | sqlserverregistrations | 否 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | cloudmanifestfiles | 否 |
> | registrations | 否 | 

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | clusters | 否 | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | batchaccounts |  Batch 帳戶無法直接從某個區域移至另一個區域，但您可以使用範本來匯出範本、修改範本，然後將範本部署到新的區域。 <br/><br/> 瞭解如何 [跨區域移動 Batch 帳戶](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | billingaccounts | 否 | 
> | billingperiods | 否 | 
> | billingpermissions | 否 | 
> | billingproperty | 否 | 
> | billingroleassignments | 否 | 
> | billingroledefinitions | 否 | 
> | departments | 否 | 
> | enrollmentaccounts | 否 | 
> | invoices | 否 | 
> | transfers | 否 | 

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
> | cordamembers | 否 |
> | watchers | 否 | 

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | tokenservices | 否 |


## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | blueprintassignments | 否 | 
> | blueprints | 否 |

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
> | redisenterprise | 否 | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | appliedreservations | 否 | 
> | calculateexchange | 否 | 
> | calculateprice | 否 | 
> | calculatepurchaseprice | 否 | 
> | catalogs | 否 | 
> | commercialreservationorders | 否 | 
> | 兌換 | 否 |
> | reservationorders | 否 | 
> | reservations | 否 | 
> | resources | 否 | 
> | validatereservationorder | 否 | 

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | 否 |
> | edgenodes | 否
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
> | capabilities | 否 | 
> | domainnames | 是 | 否 |
> | quotas | 否 | 
> | resourcetypes | 否 |
> | validatesubscriptionmoveavailability | 否 | 
> | virtualmachines | 否 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | 否 | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | capabilities | 否 | 
> | expressroutecrossconnections | 否 | 
> | expressroutecrossconnections / peerings | 否 | 
> | gatewaysupporteddevices | 否 | 
> | networksecuritygroups | 否 |
> | quotas | 否 |
> | reservedips | 否 | 
> | virtualnetworks | 否 | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | disks | 否 | 
> | images | 否 | 
> | osimages | 否 | 
> | osplatformimages | 否 | 
> | publicimages | 否 | 
> | quotas | 否 | 
> | storageaccounts | 是 |  
> | vmimages | 否 |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | 作業 | 否 | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否 | 
> | 認知搜尋 | 支援手動步驟。<br/><br/> 瞭解如何將 [您的 Azure 認知搜尋服務移至另一個區域](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | ratecard | 否 | 
> | usageaggregates | 否 | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | availabilitysets | 是 <br/><br/> 使用 [Azure 資源移動器](../../resource-mover/tutorial-move-region-virtual-machines.md) 來移動可用性設定組。 | 
> | diskaccesses | 否 |
> | diskencryptionsets | 否 | 
> | disks | 是 <br/><br/> 使用 [Azure 資源移動器](../../resource-mover/tutorial-move-region-virtual-machines.md) 移動 azure vm 和相關磁片。 | 
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
> | sshpublickeys | 否 |
> | virtualmachines | 是 <br/><br/> 使用 [Azure 資源移動器](../../resource-mover/tutorial-move-region-virtual-machines.md) 移動 azure vm。 | 
> | virtualmachines / extensions | 否 | 
> | virtualmachinescalesets | 否 | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | aggregatedcost | 否 | 
> | balances | 否 | 
> | budgets | 否 | 
> | charges | 否 | 
> | costtags | 否 | 
> | credits | 否 | 
> | 活動 | 否 | 
> | forecasts | 否 | 
> | lots | 否 | 
> | marketplaces | 否 | 
> | pricesheets | 否 | 
> | products | 否 | 
> | reservationdetails | 否 | 
> | reservationrecommendationdetails | 否 | 
> | reservationrecommendations | 否 | 
> | reservationsummaries | 否 | 
> | reservationtransactions | 否 | 
> | tags | 否 | 
> | tenants | 否 | 
> | terms | 否 | 
> | usagedetails | 否 | 


## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | containergroups | 否 | 
> | serviceassociationlinks | 否 |


## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | registries | 否 |  
> | registries / agentpools | 否 | 
> | registries / buildtasks | 否 |  
> | registries / replications | 否 | 
> | registries / tasks | 否 |  
> | registries / webhooks | 否 | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | containerservices | 否 |
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
> | alerts | 否 | 
> | billingaccounts | 否 | 
> | budgets | 否 | 
> | cloudconnectors | 否 | 
> | 連接器 | 否 | 
> | departments | 否 | 
> | dimensions | 否 | 
> | enrollmentaccounts | 否 | 
> | exports | 否 | 
> | externalbillingaccounts | 否 | 
> | 預測 | 否 | 
> | 查詢 | 否 | 
> | 註冊 | 否 | 
> | reportconfigs | 否 | 
> | reports | 否 | 
> | settings | 否 | 
> | showbackrules | 否 | 
> | 檢視 | 否 | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | hubs | 否 |  

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | requests | 否 | 

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | associations | 否 |
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
> | availableskus | 否 |
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

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | ---------- |
> | backupvaults | 否 | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | servers | 您可以使用跨區域讀取複本來移動現有的伺服器。 [深入了解](../../postgresql/howto-move-regions-portal.md)。<br/><br/> 如果服務是以異地複寫備份儲存體進行布建，您可以使用「異地還原」在其他區域中還原。 [深入了解](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage)。

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


## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | applicationgroups | 否 | 
> | workspaces | 否 | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | elasticpools | 否。 未公開資源。
> | elasticpools / iothubtenants | 否。 未公開資源。
> | iothubs | 是。 [深入了解](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | 否 | 

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | controllers | 否 | 


## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | controllers | 否 | 
> | AKS 叢集 | 否<br/><br/> [深入瞭解](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) 如何移至另一個區域。

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

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | 是，藉由在新區域中重新建立資源。 [深入了解](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | databaseaccounts | 否 | 
> | databaseaccounts | 否 | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | domains | 否 | 
> | generatessorequest | 否 | 
> | topleveldomains | 否 | 
> | validatedomainregistrationinformation | 否 |

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
> | eventsubscriptions | 否 |
> | extensionTopics | 否 | 
> | partnernamespaces | 否 | 
> | partnerregistrations | 否 | 
> | partnertopics | 否 | 
> | systemtopics | 否 | 
> | topics | 否 | 
> | topictypes | 否 | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | clusters | 否 |  
> | 命名空間 | 是範本) 的 (<br/><br/> [將事件中樞命名空間移至另一個區域](../../event-hubs/move-across-regions.md) | 
> | sku | 否 |  

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | experimentworkspaces | 否 | 

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | 命名空間 | 否 | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | featureproviders | 否 | 
> | 特性 | 否 | 
> | 提供者 | 否 | 
> | subscriptionfeatureregistrations | 否 | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | automanagedaccounts | 否 | 
> | automanagedvmconfigurationprofiles | 否 | 
> | guestconfigurationassignments | 否 | 
> | software | 否 | 
> | softwareupdateprofile | 否 | 
> | softwareupdates | 否 | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | hanainstances | 否 | 
> | sapmonitors | 否 |  

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | dedicatedhsms | 否 | 


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
> | machines / extensions | 否 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | datamanagers |  否 | 

## <a name="microsofthybridnetwork"></a>HybridNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | devices | 否 | 
> | vnfs | 否 | 

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | components | 否 | 
> | networkscopes | 否 | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | jobs |  否 | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否。 [深入了解](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region)。
> | actiongroups |  否 | 
> | activitylogalerts | 否 | 
> | alertrules |  否 | 
> | autoscalesettings |  否 | 
> | baseline | 否 |
> | components |  否 |  
> | datacollectionrules | 否 | 
> | diagnosticsettings | 否 | 
> | diagnosticsettingscategories | 否 | 
> | eventcategories | 否 | 
> | eventtypes | 否 | 
> | extendeddiagnosticsettings | 否 | |
> | guestdiagnosticsettings | 否 | 
> | listmigrationdate | 否 | 
> | logdefinitions | 否 | 
> | logprofiles | 否 | 
> | 記錄 | 否 | 否 |
> | metricalerts | 否 | 
> | metricbaselines | 否 | 
> | metricbatch | 否 | 
> | metricdefinitions | 否 | 
> | metricnamespaces | 否 | 
> | metrics | 否 | 
> | migratealertrules | 否 |
> | migratetonewpricingmodel | 否 | 
> | myworkbooks | 否 |
> | notificationgroups | 否 | 
> | privatelinkscopes | 否 |
> | rollbacktolegacypricingmodel | 否 |
> | scheduledqueryrules |  否 | 
> | 拓撲 | 否 |
> | 交易 | 否 |
> | vminsightsonboardingstatuses | 否 |
> | webtests |  否 | 
> | webtest/gettestresultfile | 否 |
> | workbooks |  否 |  
> | workbooktemplates | 否 |


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | apptemplates | 否 | 
> | iotapps | 否 | 



## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> |  iothub |  是 (複製中樞)  <br/><br/> [將 IoT 中樞複製到另一個區域](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 |
> | ------------- | ----------- | 
> | graph | 否 | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | deletedvaults | 否 |
> | hsmpools | 否 | 
> | managedhsms | 否 |
> | vaults |  否 | 

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | connectedclusters | 否 | 
> | registeredsubscriptions | 否 | 

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | sourcecontrolconfigurations | 否 | 

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
> | users | 否 | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否，這是全域服務。

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | hostingenvironments | 否 | 
> | integrationaccounts |  否 |  
> | integrationserviceenvironments | 否 | 
> | integrationserviceenvironments / managedapis | 否 |
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
> | teamaccounts | 否 | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否 | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | workspaces | 否 | 

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | configurationassignments | 是。 [深入了解](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | maintenanceconfigurations | 是。 [深入了解](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | updates | 否 | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 身分識別 | 否 | 
> | userassignedidentities | 否 | 

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | managednetworks | 否 | 
> | managednetworks / managednetworkgroups | 否 |
> | managednetworks / managednetworkpeeringpolicies | 否 | 
> | 通知 | 否 | 

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | 否 | 
> | registrationassignments | 否 |
> | registrationdefinitions | 否 | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | getentities | 否 | 
> | managementgroups | 否 | 
> | managementgroups / settings | 否 | 
> | resources | 否 | 
> | starttenantbackfill | 否 | 
> | tenantbackfillstatus | 否 | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts |  否，Azure 地圖服務是地理空間服務。 
> | accounts / privateatlases | 否

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | offers | 否 | 
> | offertypes | 否 | 
> | privategalleryitems | 否 | 
> | privatestoreclient | 否 | 
> | privatestores | 否 | 
> | products | 否 | 
> | publishers | 否 | 
> | 註冊 | 否 | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | classicdevservices | 否 | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | agreements | 否 | 
> | offertypes | 否 | 

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
> | movecollections | 否
> | projects | 否 | 

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | 否 | 
> | objectunderstandingaccounts | 否 | 
> | remoterenderingaccounts | 否 | 
> | spatialanchorsaccounts | 否 | 

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
> | bgpservicecommunities | 否 |
> | connections |  否 | 
> | ddoscustompolicies |  否 | 
> | ddosprotectionplans | 否 | 
> | dnszones |  否 | 
> | expressroutecircuits | 否 | 
> | expressroutegateways | 否 | 
> | expressrouteserviceproviders | 否 | 
> | firewallpolicies | 否 |
> | frontdoors | 否 | 
> | ipallocations | 否 |
> | ipgroups | 否 |
> | loadbalancers | 是 <br/><br/> 使用 [Azure 資源移動器](../../resource-mover/tutorial-move-region-virtual-machines.md) 來移動內部和外部負載平衡器。 |
> | localnetworkgateways |  否 | 
> | natgateways |  否 | 
> | networkexperimentprofiles | 否 |
> | networkintentpolicies |  否 | 
> | networkinterfaces | 是 <br/><br/> 使用 [Azure 資源移動器](../../resource-mover/tutorial-move-region-virtual-machines.md) 移動 nic。 | 
> | networkprofiles | 否 | 
> | networksecuritygroups | 是 <br/><br/> 使用 [Azure 資源移動器](../../resource-mover/tutorial-move-region-virtual-machines.md) 將網路安全性群組移動 (ngs) 。 | 
> | networkwatchers |  否 |  
> | networkwatchers / connectionmonitors |  否 | 
> | networkwatchers / flowlogs |  否 | 
> | networkwatchers / pingmeshes |  否 | 
> | p2svpngateways | 否 | 
> | privatednszones |  否 |  
> | privatednszones / virtualnetworklinks | 否 |> | privatednszonesinternal | 否 |
> | privateendpointredirectmaps | 否 |
> | privateendpoints | 否 | 
> | privatelinkservices | 否 | 
> | publicipaddresses | 是<br/><br/> 使用 [Azure 資源移動器](../../resource-mover/tutorial-move-region-virtual-machines.md) 來移動公用 IP 位址。 |
> | publicipprefixes | 否 | 
> | routefilters | 否 | 
> | routetables |  否 | 
> | securitypartnerproviders | 否 |
> | serviceendpointpolicies |  否 | 
> | trafficmanagergeographichierarchies | 否 | 
> | trafficmanagerprofiles |  否 | 
> | trafficmanagerusermetricskeys | 否 |
> | virtualhubs | 否 | 
> | virtualnetworkgateways |  否 |  
> | virtualnetworks |  否 | 
> | virtualnetworktaps | 否 | 
> | virtualwans | 否 | 
> | vpngateways (虛擬 WAN) | 否 | 
> | vpnsites (虛擬 WAN) | 否 | 
> | vpnsites (虛擬 WAN) | 否 |


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 命名空間 |  否 | 
> | namespaces / notificationhubs |  否 |  

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | osnamespaces | 否 | 

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | hypervsites | 否 | 
> | importsites | 否 | 
> | serversites | 否 | 
> | vmwaresites | 否 | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | clusters | 否 | 
> | deletedworkspaces | 否 | 
> | linktargets | 否 | 
> | storageinsightconfigs | 否 |
> | workspaces | 否 |



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | managementassociations | 否 |
> | managementconfigurations |  否 | 
> | solutions | 否 |
> | 檢視 |  否 | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | legacypeerings | 否 | 
> | peerasns | 否 | 
> | peeringlocations | 否 | 
> | peerings | 否 | 
> | peeringservicecountries | 否 | 
> | peeringservicelocations | 否 | 
> | peeringserviceproviders | 否 | 
> | peeringservices | 否 | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | policyevents | 否 | 
> | policystates | 否 | 
> | policytrackedresources | 否 | 
> | remediations | 否 | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | consoles | 否 |
> | dashboards | 否 | 
> | usersettings | 否 | 


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

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | accounts | 否 | 

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | availableaccounts | 否 | 
> | providerregistrations | 否 | 
> | rollouts | 否 | 

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | workspaces | 否 | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | replicationeligibilityresults | 否 |
> | vaults | 否。<br/><br/> 不支援在 Azure 區域間移動 Azure 備份的復原服務保存庫。<br/><br/> 在 Azure Site Recovery 的復原服務保存庫中，您可以在目的地區域中 [停用並重新](../../site-recovery/move-vaults-across-regions.md) 建立保存庫。 | 

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | openshiftclusters | 否 | 

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
> | resourcechangedetails | 否 | 
> | resourcechanges | 否 | 
> | resources | 否 | 
> | resourceshistory | 否 | 
> | subscriptionsstatus | 否 | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | childresources | 否 | 
> | emergingissues | 否 | 
> | 活動 | 否 | 
> | 中繼資料 | 否 | 
> | 通知 | 否 | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 |
> | ------------- | ----------- |
> | deploymentScripts |  是<br/><br/>[將 Microsoft .Resources 資源移至新區域](microsoft-resources-move-regions.md) |
> | templateSpecs |  是<br/><br/>[將 Microsoft .Resources 資源移至新區域](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 應用程式所需 |  否 | 
> | saasresources | 否 | 


## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | resourcehealthmetadata | 否 |
> | searchservices |  否 | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | adaptivenetworkhardenings | 否 | 
> | advancedthreatprotectionsettings | 否 | 
> | alerts | 否 | 
> | allowedconnections | 否 | 
> | applicationwhitelistings | 否 | 
> | assessmentmetadata | 否 | 
> | assessments | 否 | 
> | autodismissalertsrules | 否 | 
> | automations | 否 | 
> | autoprovisioningsettings | 否 |
> | complianceresults | 否 | 
> | compliances | 否 | 
> | datacollectionagents | 否 | 
> | devicesecuritygroups | 否 | 
> | discoveredsecuritysolutions | 否 | 
> | externalsecuritysolutions | 否 | 
> | informationprotectionpolicies | 否 | 
> | iotsecuritysolutions | 否 | 
> | iotsecuritysolutions / analyticsmodels | 否 | 
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | 否 | 
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | 否 | 
> | jitnetworkaccesspolicies | 否 | 
> | 原則 | 否 | 
> | pricings | 否 | 
> | regulatorycompliancestandards | 否 | 
> | regulatorycompliancestandards / regulatorycompliancecontrols | 否 | 
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | 否 | 
> | securitycontacts | 否 | 
> | securitysolutions | 否 | 
> | securitysolutionsreferencedata | 否 | 
> | securitystatuses | 否 | 
> | securitystatusessummaries | 否 | 
> | servervulnerabilityassessments | 否 | 
> | settings | 否 | 
> | subassessments | 否 |
> | 工作 | 否 | 
> | topologies | 否 | 
> | workspacesettings | 否 | 

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | aggregations | 否 | 
> | alertrules | 否 | 
> | alertruletemplates | 否 | 
> | automationrules | 否 |
> | 案例 | 否 | 
> | dataconnectors | 否 | 
> | 實體 | 否 | 
> | entityqueries | 否 |
> | incidents | 否 | 
> | officeconsents | 否 | 
> | settings | 否 | 
> | threatintelligence | 否 | 

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | consoleservices | 否 | 

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
> | premiummessagingregions | 否 | 
> | sku | 否 | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | 應用程式所需 | 否 | 
> | clusters |  否 |  
> | containergroups | 否 | 
> | containergroupsets | 否 | 
> | edgeclusters | 否 | 
> | managedclusters | 否 |
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

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | rollouts | 否 | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | signalr |  否 |  

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | hybridusebenefits | 否 | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | appliancedefinitions | 否 | 
> | appliances | 否 | 
> | jitrequests | 否 | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | instancepools | 否 | 
> | 位置 | 否 |
> | managedinstances | 是 <br/><br/> [深入瞭解](../../azure-sql/database/move-resources-across-regions.md) 跨區域移動受控實例。 | 
> | managedinstances / databases | 是 | 
> | servers | 是 | 
> | servers / databases | 是 <br/><br/> [深入瞭解](../../azure-sql/database/move-resources-across-regions.md) 跨區域移動資料庫。<br/><br/> [深入瞭解](../../resource-mover/tutorial-move-region-sql.md) 如何使用 Azure 資源移動器移動 azure SQL 資料庫。  | 
> | servers / elasticpools | 是 <br/><br/> [深入瞭解](../../azure-sql/database/move-resources-across-regions.md) 跨區域移動彈性集區。<br/><br/> [深入瞭解](../../resource-mover/tutorial-move-region-sql.md) 如何使用 Azure 資源移動器移動 azure SQL 彈性集區。  | 
> | virtualclusters | 是 | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  否 |  
> | sqlvirtualmachines |  否 |  


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
> | clusters | 否 |
> | streamingjobs |  否 |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | environments | 否 | 
> | 執行個體 | 否 | 

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | subscriptions | 否 | 

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | 服務 | 否 | 
> | supporttickets | 否 | 

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | workspaces | 否 | 
> | workspaces / bigdatapools | 否 | 
> | workspaces / sqlpools | 否 | 


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

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | arczones | 否 | 
> | resourcepools | 否 | 
> | vcenters | 否 | 
> | virtualmachines | 否 | 
> | virtualmachinetemplates | 否 | 
> | virtualnetworks | 否 | 

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | 否 | 
> | dedicatedcloudservices | 否 | 
> | virtualmachines | 否 | 

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | devices | 否 | 
> | vnfs | 否 | 

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | accounts | 否 | 
> | plans | 否 | 
> | registeredsubscriptions | 否 |


## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | availablestacks | 否 | 
> | billingmeters | 否 | 
> | certificates | 否 | 
> | connectiongateways |  否 |  
> | connections |  否 |  
> | customapis |  否 | 
> | deletedsites | 否 | 
> | deploymentlocations | 否 | 
> | georegions | 否 | 
> | hostingenvironments | 否 | 
> | kubeenvironments | 否 | 
> | publishingusers | 否 |
> | Mahout | 否 | 
> | resourcehealthmetadata | 否 | 
> | runtimes | 否 | 
> | serverfarms | 否 |  
> | serverfarms / eventgridfilters | N
> | sites |  否 | 
> | sites / premieraddons |  否 |  
> | sites / slots |  否 |  
> | sourcecontrols | 否 |
> | staticsites | 否 | 

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | multipleactivationkeys | 否 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- |
> | deviceservices | 否 | 

## <a name="microsoftworkloadbuilder"></a>WorkloadBuilder

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | 工作負載 | 否 | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 資源類型 | 區域移動 | 
> | ------------- | ----------- | 
> | components | 否 |
> | componentssummary | 否 | 
> | monitorinstances | 否 | 
> | monitorinstancessummary | 否 | 
> | monitors | 否 | 
## <a name="third-party-services"></a>協力廠商服務

協力廠商服務目前不支援移動作業。

## <a name="next-steps"></a>後續步驟

[深入瞭解](../../resource-mover/overview.md) 資源移動器服務。

