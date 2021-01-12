---
title: 依資源類型區分的移動作業支援
description: 列出可移至新資源群組或訂用帳戶的 Azure 資源類型。
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: fe8f6705fdce1a692dd8a969cfc4b3b21d2d2a11
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98072100"
---
# <a name="move-operation-support-for-resources"></a>資源的移動作業支援

此文章列出 Azure 資源類型是否支援移動作業。 其也提供移動資源時要考慮之特殊條件的相關資訊。

> [!IMPORTANT]
> 在大部分情況下，子資源無法從其父資源獨立移動。 子資源的資源類型具有的格式 `<resource-provider-namespace>/<parent-resource>/<child-resource>` 。 例如， `Microsoft.ServiceBus/namespaces/queues` 是的子資源 `Microsoft.ServiceBus/namespaces` 。 當您移動父資源時，會自動移動子資源。 如果您在本文中沒有看到子資源，您可以假設它與父資源一起移動。 如果父資源不支援移動，則無法移動子資源。

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
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | domainservices | 否 | 否 |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | 否 | 否 |
> | diagnosticsettingscategories | 否 | 否 |
> | privatelinkforazuread | 是 | 是 |
> | tenants | 是 | 是 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | supportproviders | 否 | 否 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
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
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 組態 | 否 | 否 |
> | generaterecommendations | 否 | 否 |
> | 中繼資料 | 否 | 否 |
> | Mahout | 否 | 否 |
> | suppressions | 否 | 否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | actionrules | 是 | 是 |
> | alerts | 否 | 否 |
> | alertslist | 否 | 否 |
> | alertsmetadata | 否 | 否 |
> | alertssummary | 否 | 否 |
> | alertssummarylist | 否 | 否 |
> | smartdetectoralertrules | 是 | 是 |
> | smartgroups | 否 | 否 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | servers | 是 | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> 無法移動設定為取用 SKU 的 API 管理服務。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | reportfeedback | 否 | 否 |
> | 服務 | 是 | 是 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | configurationstores | 是 | 是 |
> | configurationstores / eventgridfilters | 否 | 否 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | spring | 是 | 是 |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> 請參閱 [App Service 移動指引](./move-limitations/app-service-move-limitations.md)。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | apiapps | 否 | 否 |
> | appidentities | 否 | 否 |
> | gateways | 否 | 否 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | attestationproviders | 是 | 是 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | classicadministrators | 否 | 否 |
> | dataaliases | 否 | 否 |
> | denyassignments | 否 | 否 |
> | elevateaccess | 否 | 否 |
> | findorphanroleassignments | 否 | 否 |
> | locks | 否 | 否 |
> | 權限 | 否 | 否 |
> | policyassignments | 否 | 否 |
> | policydefinitions | 否 | 否 |
> | policysetdefinitions | 否 | 否 |
> | privatelinkassociations | 否 | 否 |
> | resourcemanagementprivatelinks | 否 | 否 |
> | roleassignments | 否 | 否 |
> | roleassignmentsusagemetrics | 否 | 否 |
> | roledefinitions | 否 | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!IMPORTANT]
> Runbook 必須存在於和自動化帳戶相同的資源群組中。
>
> 如需詳細資訊，請參閱 [將您的 Azure 自動化帳戶移至另一個訂用帳戶](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json)。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | automationaccounts | 是 | 是 |
> | automationaccounts / configurations | 是 | 是 |
> | automationaccounts / runbooks | 是 | 是 |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | privateclouds | 是 | 是 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | 是 | 是 |
> | b2ctenants | 否 | 否 |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | datacontrollers | 否 | 否 |
> | hybriddatamanagers | 否 | 否 |
> | postgresinstances | 否 | 否 |
> | sqlinstances | 否 | 否 |
> | sqlmanagedinstances | 否 | 否 |
> | sqlserverinstances | 否 | 否 |
> | sqlserverregistrations | 是 | 是 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | 否 | 否 |
> | registrations | 是 | 是 |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | clusters | 否 | 否 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | batchaccounts | 是 | 是 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | billingaccounts | 否 | 否 |
> | billingperiods | 否 | 否 |
> | billingpermissions | 否 | 否 |
> | billingproperty | 否 | 否 |
> | billingroleassignments | 否 | 否 |
> | billingroledefinitions | 否 | 否 |
> | departments | 否 | 否 |
> | enrollmentaccounts | 否 | 否 |
> | invoices | 否 | 否 |
> | transfers | 否 | 否 |

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
> | watchers | 否 | 否 |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

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

> [!IMPORTANT]
> 如果 Azure Cache for Redis 執行個體是使用虛擬網路設定的，該執行個體便無法移至不同的訂用帳戶。 請參閱[網路移動限制](./move-limitations/networking-move-limitations.md)。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | redis | 是 | 是 |
> | redisenterprise | 否 | 否 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | appliedreservations | 否 | 否 |
> | calculateexchange | 否 | 否 |
> | calculateprice | 否 | 否 |
> | calculatepurchaseprice | 否 | 否 |
> | catalogs | 否 | 否 |
> | commercialreservationorders | 否 | 否 |
> | 兌換 | 否 | 否 |
> | reservationorders | 否 | 否 |
> | reservations | 否 | 否 |
> | resources | 否 | 否 |
> | validatereservationorder | 否 | 否 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | 否 | 否 |
> | cdnwebapplicationfirewallpolicies | 是 | 是 |
> | edgenodes | 否 | 否 |
> | 設定檔 | 是 | 是 |
> | profiles / endpoints | 是 | 是 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> 請參閱 [App Service 移動指引](./move-limitations/app-service-move-limitations.md)。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | certificateorders | 是 | 是 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> 請參閱[傳統部署移動指引](./move-limitations/classic-model-move-limitations.md)。 傳統部署資源可以透過該案例專屬的作業，跨訂用帳戶移動。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | capabilities | 否 | 否 |
> | domainnames | 是 | 否 |
> | quotas | 否 | 否 |
> | resourcetypes | 否 | 否 |
> | validatesubscriptionmoveavailability | 否 | 否 |
> | virtualmachines | 是 | 是 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> 請參閱[傳統部署移動指引](./move-limitations/classic-model-move-limitations.md)。 傳統部署資源可以透過該案例專屬的作業，跨訂用帳戶移動。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | 否 | 否 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> 請參閱[傳統部署移動指引](./move-limitations/classic-model-move-limitations.md)。 傳統部署資源可以透過該案例專屬的作業，跨訂用帳戶移動。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | capabilities | 否 | 否 |
> | expressroutecrossconnections | 否 | 否 |
> | expressroutecrossconnections / peerings | 否 | 否 |
> | gatewaysupporteddevices | 否 | 否 |
> | networksecuritygroups | 否 | 否 |
> | quotas | 否 | 否 |
> | reservedips | 否 | 否 |
> | virtualnetworks | 否 | 否 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> 請參閱[傳統部署移動指引](./move-limitations/classic-model-move-limitations.md)。 傳統部署資源可以透過該案例專屬的作業，跨訂用帳戶移動。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | disks | 否 | 否 |
> | images | 否 | 否 |
> | osimages | 否 | 否 |
> | osplatformimages | 否 | 否 |
> | publicimages | 否 | 否 |
> | quotas | 否 | 否 |
> | storageaccounts | 是 | 否 |
> | vmimages | 否 | 否 |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> 請參閱[傳統部署移動指引](./move-limitations/classic-model-move-limitations.md)。 傳統部署資源可以透過該案例專屬的作業，跨訂用帳戶移動。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 作業 | 否 | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 是 | 是 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | ratecard | 否 | 否 |
> | usageaggregates | 否 | 否 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> 請參閱[虛擬機器移動指引](./move-limitations/virtual-machines-move-limitations.md)。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | availabilitysets | 是 | 是 |
> | diskaccesses | 否 | 否 |
> | diskencryptionsets | 否 | 否 |
> | disks | 是 | 是 |
> | galleries | 否 | 否 |
> | galleries / images | 否 | 否 |
> | galleries / images / versions | 否 | 否 |
> | hostgroups | 否 | 否 |
> | hostgroups / hosts | 否 | 否 |
> | images | 是 | 是 |
> | proximityplacementgroups | 是 | 是 |
> | restorepointcollections | 否 | 否 |
> | restorepointcollections / restorepoints | 否 | 否 |
> | sharedvmextensions | 否 | 否 |
> | sharedvmimages | 否 | 否 |
> | sharedvmimages / versions | 否 | 否 |
> | snapshots | 是 | 是 |
> | sshpublickeys | 否 | 否 |
> | virtualmachines | 是 | 是 |
> | virtualmachines / extensions | 是 | 是 |
> | virtualmachinescalesets | 是 | 是 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | 否 | 否 |
> | balances | 否 | 否 |
> | budgets | 否 | 否 |
> | charges | 否 | 否 |
> | costtags | 否 | 否 |
> | credits | 否 | 否 |
> | 活動 | 否 | 否 |
> | forecasts | 否 | 否 |
> | lots | 否 | 否 |
> | marketplaces | 否 | 否 |
> | pricesheets | 否 | 否 |
> | products | 否 | 否 |
> | reservationdetails | 否 | 否 |
> | reservationrecommendationdetails | 否 | 否 |
> | reservationrecommendations | 否 | 否 |
> | reservationsummaries | 否 | 否 |
> | reservationtransactions | 否 | 否 |
> | tags | 否 | 否 |
> | tenants | 否 | 否 |
> | terms | 否 | 否 |
> | usagedetails | 否 | 否 |

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
> | registries / agentpools | 是 | 是 |
> | registries / buildtasks | 是 | 是 |
> | registries / replications | 是 | 是 |
> | registries / tasks | 是 | 是 |
> | registries / webhooks | 是 | 是 |

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
> | billingaccounts | 否 | 否 |
> | budgets | 否 | 否 |
> | cloudconnectors | 否 | 否 |
> | 連接器 | 是 | 是 |
> | departments | 否 | 否 |
> | dimensions | 否 | 否 |
> | enrollmentaccounts | 否 | 否 |
> | exports | 否 | 否 |
> | externalbillingaccounts | 否 | 否 |
> | 預測 | 否 | 否 |
> | 查詢 | 否 | 否 |
> | 註冊 | 否 | 否 |
> | reportconfigs | 否 | 否 |
> | reports | 否 | 否 |
> | settings | 否 | 否 |
> | showbackrules | 否 | 否 |
> | 檢視 | 否 | 否 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | hubs | 否 | 否 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | requests | 否 | 否 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | associations | 否 | 否 |
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
> | availableskus | 否 | 否 |
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
> | services / projects | 否 | 否 |
> | slots | 否 | 否 |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | backupvaults | 否 | 否 |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

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
> | flexibleServers | 是 | 是 |
> | servers | 是 | 是 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | flexibleServers | 是 | 是 |
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
> | servicetopologies / services | 是 | 是 |
> | servicetopologies / services / serviceunits | 是 | 是 |
> | steps | 是 | 是 |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | applicationgroups | 是 | 是 |
> | hostpools | 是 | 是 |
> | workspaces | 是 | 是 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | elasticpools | 否 | 否 |
> | elasticpools / iothubtenants | 否 | 否 |
> | iothubs | 是 | 是 |
> | provisioningservices | 是 | 是 |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | pipelines | 是 | 是 |

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
> | labs / environments | 是 | 是 |
> | labs / servicerunners | 是 | 是 |
> | labs / virtualmachines | 是 | 否 |
> | schedules | 是 | 是 |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | 否 | 否 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | 否 | 否 |
> | databaseaccounts | 是 | 是 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | domains | 是 | 是 |
> | generatessorequest | 否 | 否 |
> | topleveldomains | 否 | 否 |
> | validatedomainregistrationinformation | 否 | 否 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 服務 | 是 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | domains | 是 | 是 |
> | eventsubscriptions | 否 - 無法獨立移動，但會自動與已訂閱的資源一起移動。 | 否 - 無法獨立移動，但會自動與已訂閱的資源一起移動。 |
> | extensionTopics | 否 | 否 |
> | partnernamespaces | 是 | 是 |
> | partnerregistrations | 否 | 否 |
> | partnertopics | 是 | 是 |
> | systemtopics | 是 | 是 |
> | topics | 是 | 是 |
> | topictypes | 否 | 否 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | clusters | 是 | 是 |
> | 命名空間 | 是 | 是 |
> | sku | 否 | 否 |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | 否 | 否 |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 命名空間 | 是 | 是 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | featureproviders | 否 | 否 |
> | 特性 | 否 | 否 |
> | 提供者 | 否 | 否 |
> | subscriptionfeatureregistrations | 否 | 否 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |

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
> | sapmonitors | 否 | 否 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | 否 | 否 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> 您可以將 HDInsight 叢集移至新的訂用帳戶或資源群組。 不過，您無法跨訂用帳戶來移動連結至 HDInsight 叢集的網路資源 (例如虛擬網路、NIC 或負載平衡器)。 此外，您無法將已連結至叢集虛擬機器的 NIC 移至新的資源群組。
>
> 將 HDInsight 叢集移至新的訂用帳戶時，請先移動其他資源 (例如儲存體帳戶)。 然後，移動 HDInsight 叢集本身。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | clusters | 是 | 是 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 服務 | 是 | 是 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | machines | 是 | 是 |
> | machines / extensions | 是 | 是 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | datamanagers | 是 | 是 |

## <a name="microsofthybridnetwork"></a>HybridNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | devices | 否 | 否 |
> | vnfs | 否 | 否 |

## <a name="microsofthydra"></a>Microsoft.Hydra

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

> [!IMPORTANT]
> 確定移至新的訂用帳戶不會超過[訂用帳戶配額](azure-subscription-service-limits.md#azure-monitor-limits)。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | actiongroups | 是 | 是 |
> | activitylogalerts | 否 | 否 |
> | alertrules | 是 | 是 |
> | autoscalesettings | 是 | 是 |
> | baseline | 否 | 否 |
> | components | 是 | 是 |
> | datacollectionrules | 否 | 否 |
> | diagnosticsettings | 否 | 否 |
> | diagnosticsettingscategories | 否 | 否 |
> | eventcategories | 否 | 否 |
> | eventtypes | 否 | 否 |
> | extendeddiagnosticsettings | 否 | 否 |
> | guestdiagnosticsettings | 否 | 否 |
> | listmigrationdate | 否 | 否 |
> | logdefinitions | 否 | 否 |
> | logprofiles | 否 | 否 |
> | 記錄 | 否 | 否 |
> | metricalerts | 否 | 否 |
> | metricbaselines | 否 | 否 |
> | metricbatch | 否 | 否 |
> | metricdefinitions | 否 | 否 |
> | metricnamespaces | 否 | 否 |
> | metrics | 否 | 否 |
> | migratealertrules | 否 | 否 |
> | migratetonewpricingmodel | 否 | 否 |
> | myworkbooks | 否 | 否 |
> | notificationgroups | 否 | 否 |
> | privatelinkscopes | 否 | 否 |
> | rollbacktolegacypricingmodel | 否 | 否 |
> | scheduledqueryrules | 是 | 是 |
> | 拓撲 | 否 | 否 |
> | 交易 | 否 | 否 |
> | vminsightsonboardingstatuses | 否 | 否 |
> | webtests | 是 | 是 |
> | webtest/gettestresultfile | 否 | 否 |
> | workbooks | 是 | 是 |
> | workbooktemplates | 是 | 是 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | apptemplates | 否 | 否 |
> | iotapps | 是 | 是 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | graph | 是 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> 用於磁碟加密的 Key Vault 無法移至相同訂用帳戶中或跨訂用帳戶的資源群組。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | deletedvaults | 否 | 否 |
> | hsmpools | 否 | 否 |
> | managedhsms | 否 | 否 |
> | vaults | 是 | 是 |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | connectedclusters | 是 | 是 |
> | registeredsubscriptions | 否 | 否 |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | 否 | 否 |

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
> | users | 否 | 否 |

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
> | integrationserviceenvironments / managedapis | 是 | 否 |
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
> | teamaccounts | 否 | 否 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | workspaces | 否 | 否 |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | configurationassignments | 否 | 否 |
> | maintenanceconfigurations | 是 | 是 |
> | updates | 否 | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 身分識別 | 否 | 否 |
> | userassignedidentities | 否 | 否 |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | managednetworks | 否 | 否 |
> | managednetworks / managednetworkgroups | 否 | 否 |
> | managednetworks / managednetworkpeeringpolicies | 否 | 否 |
> | 通知 | 否 | 否 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | 否 | 否 |
> | registrationassignments | 否 | 否 |
> | registrationdefinitions | 否 | 否 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | getentities | 否 | 否 |
> | managementgroups | 否 | 否 |
> | managementgroups / settings | 否 | 否 |
> | resources | 否 | 否 |
> | starttenantbackfill | 否 | 否 |
> | tenantbackfillstatus | 否 | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 是 | 是 |
> | accounts / privateatlases | 是 | 是 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | offers | 否 | 否 |
> | offertypes | 否 | 否 |
> | privategalleryitems | 否 | 否 |
> | privatestoreclient | 否 | 否 |
> | privatestores | 否 | 否 |
> | products | 否 | 否 |
> | publishers | 否 | 否 |
> | 註冊 | 否 | 否 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | classicdevservices | 否 | 否 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | agreements | 否 | 否 |
> | offertypes | 否 | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | mediaservices | 是 | 是 |
> | mediaservices / liveevents | 是 | 是 |
> | mediaservices / streamingendpoints | 是 | 是 |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | appclusters | 否 | 否 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | 否 | 否 |
> | migrateprojects | 否 | 否 |
> | movecollections | 否 | 否 |
> | projects | 否 | 否 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | 否 | 否 |
> | objectunderstandingaccounts | 否 | 否 |
> | remoterenderingaccounts | 是 | 是 |
> | spatialanchorsaccounts | 是 | 是 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | netappaccounts | 否 | 否 |
> | netappaccounts / capacitypools | 否 | 否 |
> | netappaccounts / capacitypools / volumes | 否 | 否 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> 請參閱[網路移動指引](./move-limitations/networking-move-limitations.md)。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | applicationgateways | 否 | 否 |
> | applicationgatewaywebapplicationfirewallpolicies | 否 | 否 |
> | applicationsecuritygroups | 是 | 是 |
> | azurefirewalls | 否 | 否 |
> | bastionhosts | 否 | 否 |
> | bgpservicecommunities | 否 | 否 |
> | connections | 是 | 是 |
> | ddoscustompolicies | 是 | 是 |
> | ddosprotectionplans | 否 | 否 |
> | dnszones | 是 | 是 |
> | expressroutecircuits | 否 | 否 |
> | expressroutegateways | 否 | 否 |
> | expressrouteserviceproviders | 否 | 否 |
> | firewallpolicies | 是 | 是 |
> | frontdoors | 否 | 否 |
> | ipallocations | 是 | 是 |
> | ipgroups | 是 | 是 |
> | loadbalancers | 是 - 基本 SKU<br> 是-標準 SKU | 是 - 基本 SKU<br>否 - 標準 SKU |
> | localnetworkgateways | 是 | 是 |
> | natgateways | 否 | 否 |
> | networkexperimentprofiles | 否 | 否 |
> | networkintentpolicies | 是 | 是 |
> | networkinterfaces | 是 | 是 |
> | networkprofiles | 否 | 否 |
> | networksecuritygroups | 是 | 是 |
> | networkwatchers | 否 | 否 |
> | networkwatchers / connectionmonitors | 是 | 否 |
> | networkwatchers / flowlogs | 是 | 否 |
> | networkwatchers / pingmeshes | 是 | 否 |
> | p2svpngateways | 否 | 否 |
> | privatednszones | 是 | 是 |
> | privatednszones / virtualnetworklinks | 是 | 是 |
> | privatednszonesinternal | 否 | 否 |
> | privateendpointredirectmaps | 否 | 否 |
> | privateendpoints | 否 | 否 |
> | privatelinkservices | 否 | 否 |
> | publicipaddresses | 是 - 基本 SKU<br>是-標準 SKU | 是 - 基本 SKU<br>否 - 標準 SKU |
> | publicipprefixes | 是 | 是 |
> | routefilters | 否 | 否 |
> | routetables | 是 | 是 |
> | securitypartnerproviders | 是 | 是 |
> | serviceendpointpolicies | 是 | 是 |
> | trafficmanagergeographichierarchies | 否 | 否 |
> | trafficmanagerprofiles | 是 | 是 |
> | trafficmanagerprofiles / heatmaps | 否 | 否 |
> | trafficmanagerusermetricskeys | 否 | 否 |
> | virtualhubs | 否 | 否 |
> | virtualnetworkgateways | 是 | 是 |
> | virtualnetworks | 是 | 是 |
> | virtualnetworktaps | 否 | 否 |
> | virtualrouters | 是 | 是 |
> | virtualwans | 否 | 否 |
> | vpngateways (虛擬 WAN) | 否 | 否 |
> | vpnserverconfigurations | 否 | 否 |
> | vpnsites (虛擬 WAN) | 否 | 否 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 命名空間 | 是 | 是 |
> | namespaces / notificationhubs | 是 | 是 |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | osnamespaces | 是 | 是 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | hypervsites | 否 | 否 |
> | importsites | 否 | 否 |
> | serversites | 否 | 否 |
> | vmwaresites | 否 | 否 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> 確定移至新的訂用帳戶不會超過[訂用帳戶配額](azure-subscription-service-limits.md#azure-monitor-limits)。
>
> 無法移動具有連結自動化帳戶的工作區。 開始移動作業之前，請務必將任何自動化帳戶取消連結。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | clusters | 否 | 否 |
> | deletedworkspaces | 否 | 否 |
> | linktargets | 否 | 否 |
> | storageinsightconfigs | 否 | 否 |
> | workspaces | 是 | 是 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | managementassociations | 否 | 否 |
> | managementconfigurations | 是 | 是 |
> | solutions | 是 | 是 |
> | 檢視 | 是 | 是 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | legacypeerings | 否 | 否 |
> | peerasns | 否 | 否 |
> | peeringlocations | 否 | 否 |
> | peerings | 否 | 否 |
> | peeringservicecountries | 否 | 否 |
> | peeringservicelocations | 否 | 否 |
> | peeringserviceproviders | 否 | 否 |
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
> | consoles | 否 | 否 |
> | dashboards | 是 | 是 |
> | usersettings | 否 | 否 |

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

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | availableaccounts | 否 | 否 |
> | providerregistrations | 否 | 否 |
> | rollouts | 否 | 否 |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | workspaces | 否 | 否 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!IMPORTANT]
> 請參閱[復原服務移動指引](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | replicationeligibilityresults | 否 | 否 |
> | vaults | 是 | 是 |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

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
> | resourcechangedetails | 否 | 否 |
> | resourcechanges | 否 | 否 |
> | resources | 否 | 否 |
> | resourceshistory | 否 | 否 |
> | subscriptionsstatus | 否 | 否 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | childresources | 否 | 否 |
> | emergingissues | 否 | 否 |
> | 活動 | 否 | 否 |
> | 中繼資料 | 否 | 否 |
> | 通知 | 否 | 否 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | deployments | 否 | 否 |
> | deploymentscripts | 否 | 否 |
> | deploymentscripts / logs | 否 | 否 |
> | 連結 | 否 | 否 |
> | 提供者 | 否 | 否 |
> | resourcegroups | 否 | 否 |
> | resources | 否 | 否 |
> | subscriptions | 否 | 否 |
> | tags | 否 | 否 |
> | templatespecs | 否 | 否 |
> | templatespecs / versions | 否 | 否 |
> | tenants | 否 | 否 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 應用程式所需 | 是 | 否 |
> | saasresources | 否 | 否 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> 您無法在一個作業中移動不同區域中的數個搜尋資源， 而是要在不同作業中移動它們。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | resourcehealthmetadata | 否 | 否 |
> | searchservices | 是 | 是 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | 否 | 否 |
> | advancedthreatprotectionsettings | 否 | 否 |
> | alerts | 否 | 否 |
> | allowedconnections | 否 | 否 |
> | applicationwhitelistings | 否 | 否 |
> | assessmentmetadata | 否 | 否 |
> | assessments | 否 | 否 |
> | autodismissalertsrules | 否 | 否 |
> | automations | 是 | 是 |
> | autoprovisioningsettings | 否 | 否 |
> | complianceresults | 否 | 否 |
> | compliances | 否 | 否 |
> | datacollectionagents | 否 | 否 |
> | devicesecuritygroups | 否 | 否 |
> | discoveredsecuritysolutions | 否 | 否 |
> | externalsecuritysolutions | 否 | 否 |
> | informationprotectionpolicies | 否 | 否 |
> | iotsecuritysolutions | 是 | 是 |
> | iotsecuritysolutions / analyticsmodels | 否 | 否 |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | 否 | 否 |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | 否 | 否 |
> | jitnetworkaccesspolicies | 否 | 否 |
> | 原則 | 否 | 否 |
> | pricings | 否 | 否 |
> | regulatorycompliancestandards | 否 | 否 |
> | regulatorycompliancestandards / regulatorycompliancecontrols | 否 | 否 |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | 否 | 否 |
> | securitycontacts | 否 | 否 |
> | securitysolutions | 否 | 否 |
> | securitysolutionsreferencedata | 否 | 否 |
> | securitystatuses | 否 | 否 |
> | securitystatusessummaries | 否 | 否 |
> | servervulnerabilityassessments | 否 | 否 |
> | settings | 否 | 否 |
> | subassessments | 否 | 否 |
> | 工作 | 否 | 否 |
> | topologies | 否 | 否 |
> | workspacesettings | 否 | 否 |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | aggregations | 否 | 否 |
> | alertrules | 否 | 否 |
> | alertruletemplates | 否 | 否 |
> | automationrules | 否 | 否 |
> | bookmarks | 否 | 否 |
> | 案例 | 否 | 否 |
> | dataconnectors | 否 | 否 |
> | 實體 | 否 | 否 |
> | entityqueries | 否 | 否 |
> | incidents | 否 | 否 |
> | officeconsents | 否 | 否 |
> | settings | 否 | 否 |
> | threatintelligence | 否 | 否 |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | consoleservices | 否 | 否 |

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
> | premiummessagingregions | 否 | 否 |
> | sku | 否 | 否 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 應用程式所需 | 否 | 否 |
> | clusters | 是 | 是 |
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

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | rollouts | 否 | 否 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | signalr | 是 | 是 |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

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

> [!IMPORTANT]
> 資料庫和伺服器必須位於相同的資源群組。 當您移動 SQL 伺服器時，其所有資料庫也會跟著移動。 此行為適用于 Azure SQL Database 和 Azure Synapse Analytics 資料庫。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | instancepools | 否 | 否 |
> | 位置 | 是 | 是 |
> | managedinstances | 否 | 否 |
> | servers | 是 | 是 |
> | servers / databases | 是 | 是 |
> | servers / databases / backuplongtermretentionpolicies | 是 | 是 |
> | servers / elasticpools | 是 | 是 |
> | servers / jobaccounts | 是 | 是 |
> | servers / jobagents | 是 | 是 |
> | virtualclusters | 是 | 是 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | 是 | 是 |
> | sqlvirtualmachines | 是 | 是 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | storageaccounts | 是 | 是 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | caches | 否 | 否 |

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

> [!IMPORTANT]
> 無法移動執行中狀態的串流分析作業。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | clusters | 否 | 否 |
> | streamingjobs | 是 | 是 |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | environments | 否 | 否 |
> | 執行個體 | 否 | 否 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | subscriptions | 否 | 否 |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 服務 | 否 | 否 |
> | supporttickets | 否 | 否 |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | workspaces | 否 | 否 |
> | workspaces / bigdatapools | 否 | 否 |
> | workspaces / sqlpools | 否 | 否 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | environments | 是 | 是 |
> | environments / eventsources | 是 | 是 |
> | environments / referencedatasets | 是 | 是 |

## <a name="microsofttoken"></a>Microsoft.Token

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

> [!IMPORTANT]
> 若要變更 Azure DevOps 的訂用帳戶，請參閱[變更用於計費的 Azure 訂用帳戶](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | account | 否 | 否 |
> | account / extension | 否 | 否 |
> | account / project | 否 | 否 |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | arczones | 否 | 否 |
> | resourcepools | 否 | 否 |
> | vcenters | 否 | 否 |
> | virtualmachines | 否 | 否 |
> | virtualmachinetemplates | 否 | 否 |
> | virtualnetworks | 否 | 否 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | 否 | 否 |
> | dedicatedcloudservices | 否 | 否 |
> | virtualmachines | 否 | 否 |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | devices | 否 | 否 |
> | vnfs | 否 | 否 |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | accounts | 否 | 否 |
> | plans | 否 | 否 |
> | registeredsubscriptions | 否 | 否 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> 請參閱 [App Service 移動指引](./move-limitations/app-service-move-limitations.md)。

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | availablestacks | 否 | 否 |
> | billingmeters | 否 | 否 |
> | certificates | 否 | 是 |
> | connectiongateways | 是 | 是 |
> | connections | 是 | 是 |
> | customapis | 是 | 是 |
> | deletedsites | 否 | 否 |
> | deploymentlocations | 否 | 否 |
> | georegions | 否 | 否 |
> | hostingenvironments | 否 | 否 |
> | kubeenvironments | 是 | 是 |
> | publishingusers | 否 | 否 |
> | Mahout | 否 | 否 |
> | resourcehealthmetadata | 否 | 否 |
> | runtimes | 否 | 否 |
> | serverfarms | 是 | 是 |
> | serverfarms / eventgridfilters | 否 | 否 |
> | sites | 是 | 是 |
> | sites / premieraddons | 是 | 是 |
> | sites / slots | 是 | 是 |
> | sourcecontrols | 否 | 否 |
> | staticsites | 否 | 否 |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | multipleactivationkeys | 否 | 否 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | deviceservices | 否 | 否 |

## <a name="microsoftworkloadbuilder"></a>WorkloadBuilder

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | 工作負載 | 否 | 否 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 資源類型 | 資源群組 | 訂用帳戶 |
> | ------------- | ----------- | ---------- |
> | components | 否 | 否 |
> | componentssummary | 否 | 否 |
> | monitorinstances | 否 | 否 |
> | monitorinstancessummary | 否 | 否 |
> | monitors | 否 | 否 |

## <a name="third-party-services"></a>協力廠商服務

協力廠商服務目前不支援移動作業。

## <a name="next-steps"></a>後續步驟

如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](move-resource-group-and-subscription.md)。

若要以逗號區隔值檔案的形式取得相同資料，請下載 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)。
