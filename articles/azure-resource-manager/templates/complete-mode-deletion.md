---
title: 完整模式刪除
description: 顯示資源類型如何處理 Azure Resource Manager 範本中的完整模式刪除。
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: 48a7758ce706612607b97647bc88238957b118d3
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371340"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>完整模式部署的 Azure 資源刪除

此文章說明當以完整模式部署的範本中沒有資源類型時，資源類型會如何處理刪除。

當類型不在以完整模式部署的範本中時，會刪除標示為 **[是]** 的資源類型。

當不在範本中時，標記為 [**否**] 的資源類型不會自動刪除;不過，如果刪除父資源，則會刪除它們。 如需行為的完整描述，請參閱 [Azure Resource Manager 部署模式](deployment-modes.md)。

如果您[在範本中部署到一個以上的資源群組](cross-scope-deployment.md)，部署作業中指定的資源群組中的資源就有資格刪除。 不會刪除次要資源群組中的資源。

跳至資源提供者命名空間：
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
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
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
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
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
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
> - [Microsoft 筆記本](#microsoftnotebooks)
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
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft .SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [VMwareOnAzure](#microsoftvmwareonazure)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | DomainServices | 是 |
> | DomainServices/oucontainer | 否 |

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
> | alertsMetaData | 否 |
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

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | configurationStores | 是 |
> | configurationStores / eventGridFilters | 否 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | Spring | 是 |
> | 春季/應用程式 | 否 |
> | 春季/apps/部署 | 否 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | attestationProviders | 是 |
> | defaultProviders | 否 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | classicAdministrators | 否 |
> | dataAliases | 否 |
> | denyAssignments | 否 |
> | elevateAccess | 否 |
> | findOrphanRoleAssignments | 否 |
> | locks | 否 |
> | 權限 | 否 |
> | policyAssignments | 否 |
> | policyDefinitions | 否 |
> | policySetDefinitions | 否 |
> | privateLinkAssociations | 否 |
> | providerOperations | 否 |
> | resourceManagementPrivateLinks | 否 |
> | roleAssignments | 否 |
> | roleAssignmentsUsageMetrics | 否 |
> | roleDefinitions | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | automationAccounts | 是 |
> | automationAccounts/設定 | 是 |
> | automationAccounts/作業 | 否 |
> | automationAccounts/privateEndpointConnectionProxies | 否 |
> | automationAccounts/privateEndpointConnections | 否 |
> | automationAccounts/privateLinkResources | 否 |
> | automationAccounts / runbooks | 是 |
> | automationAccounts/softwareUpdateConfigurations | 否 |
> | automationAccounts / webhooks | 否 |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | privateClouds | 是 |
> | privateClouds/授權 | 否 |
> | privateClouds/叢集 | 否 |
> | privateClouds / hcxEnterpriseSites | 否 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | environments | 否 |
> | 環境/帳戶 | 否 |
> | 環境/帳戶/命名空間 | 否 |
> | 環境/帳戶/命名空間/設定 | 否 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | b2cDirectories | 是 |
> | b2ctenants | 否 |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | dataControllers | 是 |
> | hybridDataManagers | 是 |
> | postgresInstances | 是 |
> | sqlInstances | 是 |
> | sqlManagedInstances | 是 |
> | sqlServerInstances | 是 |
> | sqlServerRegistrations | 是 |
> | sqlServerRegistrations/sqlServers | 否 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | cloudManifestFiles | 否 |
> | edgeSubscriptions | 是 |
> | registrations | 是 |
> | 註冊/customerSubscriptions | 否 |
> | registrations / products | 否 |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | clusters | 是 |

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
> | billingAccounts/合約 | 否 |
> | billingAccounts / billingPermissions | 否 |
> | billingAccounts / billingProfiles | 否 |
> | billingAccounts / billingProfiles / billingPermissions | 否 |
> | billingAccounts / billingProfiles / billingRoleAssignments | 否 |
> | billingAccounts / billingProfiles / billingRoleDefinitions | 否 |
> | billingAccounts / billingProfiles / billingSubscriptions | 否 |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | 否 |
> | billingAccounts/billingProfiles/客戶 | 否 |
> | billingAccounts/billingProfiles/指示 | 否 |
> | billingAccounts/billingProfiles/發票 | 否 |
> | billingAccounts/billingProfiles/發票/pricesheet | 否 |
> | billingAccounts/billingProfiles/發票/交易 | 否 |
> | billingAccounts / billingProfiles / invoiceSections | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | 否 |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | 否 |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | 否 |
> | billingAccounts/billingProfiles/invoiceSections/products | 否 |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | 否 |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | 否 |
> | billingAccounts/billingProfiles/invoiceSections/筆交易 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/transfer | 否 |
> | billingAccounts / BillingProfiles / patchOperations | 否 |
> | billingAccounts / billingProfiles / paymentMethods | 否 |
> | billingAccounts/billingProfiles/原則 | 否 |
> | billingAccounts/billingProfiles/pricesheet | 否 |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | 否 |
> | billingAccounts/billingProfiles/products | 否 |
> | billingAccounts/billingProfiles/筆交易 | 否 |
> | billingAccounts / billingRoleAssignments | 否 |
> | billingAccounts / billingRoleDefinitions | 否 |
> | billingAccounts / billingSubscriptions | 否 |
> | billingAccounts/billingSubscriptions/發票 | 否 |
> | billingAccounts / createBillingRoleAssignment | 否 |
> | billingAccounts / createInvoiceSectionOperations | 否 |
> | billingAccounts/客戶 | 否 |
> | billingAccounts/customers/billingPermissions | 否 |
> | billingAccounts/customers/billingSubscriptions | 否 |
> | billingAccounts/customers/initiateTransfer | 否 |
> | billingAccounts/customers/原則 | 否 |
> | billingAccounts/customers/products | 否 |
> | billingAccounts/customers/交易所 | 否 |
> | billingAccounts/客戶/傳輸 | 否 |
> | billingAccounts/部門 | 否 |
> | billingAccounts/部門/billingPermissions | 否 |
> | billingAccounts/部門/billingRoleAssignments | 否 |
> | billingAccounts/部門/billingRoleDefinitions | 否 |
> | billingAccounts / enrollmentAccounts | 否 |
> | billingAccounts / enrollmentAccounts / billingPermissions | 否 |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | 否 |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | 否 |
> | billingAccounts/發票 | 否 |
> | billingAccounts/發票/交易 | 否 |
> | billingAccounts / invoiceSections | 否 |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | 否 |
> | billingAccounts / invoiceSections / billingSubscriptions | 否 |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | 否 |
> | billingAccounts/invoiceSections/提升許可權 | 否 |
> | billingAccounts / invoiceSections / initiateTransfer | 否 |
> | billingAccounts / invoiceSections / patchOperations | 否 |
> | billingAccounts / invoiceSections / productMoveOperations | 否 |
> | billingAccounts/invoiceSections/products | 否 |
> | billingAccounts/invoiceSections/products/transfer | 否 |
> | billingAccounts/invoiceSections/products/updateAutoRenew | 否 |
> | billingAccounts/invoiceSections/筆交易 | 否 |
> | billingAccounts/invoiceSections/transfer | 否 |
> | billingAccounts / lineOfCredit | 否 |
> | billingAccounts / patchOperations | 否 |
> | billingAccounts / paymentMethods | 否 |
> | billingAccounts/products | 否 |
> | billingAccounts/筆交易 | 否 |
> | billingPeriods | 否 |
> | billingPermissions | 否 |
> | billingProperty | 否 |
> | billingRoleAssignments | 否 |
> | billingRoleDefinitions | 否 |
> | createBillingRoleAssignment | 否 |
> | departments | 否 |
> | enrollmentAccounts | 否 |
> | invoices | 否 |
> | transfers | 否 |
> | 傳輸/acceptTransfer | 否 |
> | 傳輸/declineTransfer | 否 |
> | 傳輸/operationStatus | 否 |
> | 傳輸/validateTransfer | 否 |
> | validateAddress | 否 |

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
> | blockchainMembers | 是 |
> | cordaMembers | 是 |
> | watchers | 是 |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | TokenServices | 是 |
> | TokenServices / BlockchainNetworks | 否 |
> | TokenServices/群組 | 否 |
> | TokenServices/群組/帳戶 | 否 |
> | TokenServices / TokenTemplates | 否 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | blueprintAssignments | 否 |
> | blueprintAssignments / assignmentOperations | 否 |
> | blueprintAssignments/作業 | 否 |
> | blueprints | 否 |
> | blueprints / artifacts | 否 |
> | blueprints / versions | 否 |
> | blueprints / versions / artifacts | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | botServices | 是 |
> | botServices / channels | 否 |
> | botServices/connections | 否 |
> | 語言 | 否 |
> | 範本 | 否 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | Redis | 是 |
> | Redis/EventGridFilters | 否 |
> | Redis/privateEndpointConnectionProxies | 否 |
> | Redis/privateEndpointConnectionProxies/validate | 否 |
> | Redis/privateEndpointConnections | 否 |
> | Redis/privateLinkResources | 否 |
> | redisEnterprise | 是 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | appliedReservations | 否 |
> | autoQuotaIncrease | 否 |
> | calculateExchange | 否 |
> | calculatePrice | 否 |
> | calculatePurchasePrice | 否 |
> | catalogs | 否 |
> | commercialReservationOrders | 否 |
> | 兌換 | 否 |
> | placePurchaseOrder | 否 |
> | reservationOrders | 否 |
> | reservationOrders / calculateRefund | 否 |
> | reservationOrders/merge | 否 |
> | reservationOrders/保留 | 否 |
> | reservationOrders/保留/修訂 | 否 |
> | reservationOrders/return | 否 |
> | reservationOrders/split | 否 |
> | reservationOrders/swap | 否 |
> | reservations | 否 |
> | resourceProviders | 否 |
> | resources | 否 |
> | validateReservationOrder | 否 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 否 |
> | CdnWebApplicationFirewallPolicies | 是 |
> | edgenodes | 否 |
> | 設定檔 | 是 |
> | profiles / endpoints | 是 |
> | profiles / endpoints / customdomains | 否 |
> | profiles / endpoints / origingroups | 否 |
> | profiles / endpoints / origins | 否 |
> | validateProbe | 否 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | certificateOrders | 是 |
> | certificateOrders/憑證 | 否 |
> | validateCertificateRegistrationInformation | 否 |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | profile | 否 |
> | resourceChanges | 否 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | capabilities | 否 |
> | domainNames | 是 |
> | domainNames/功能 | 否 |
> | domainNames/internalLoadBalancers | 否 |
> | domainNames/serviceCertificates | 否 |
> | domainNames/插槽 | 否 |
> | domainNames/位置/角色 | 否 |
> | domainNames/位置/角色/Metricdefinitions.listasync | 否 |
> | domainNames/位置/角色/計量 | 否 |
> | moveSubscriptionResources | 否 |
> | operatingSystemFamilies | 否 |
> | operatingSystems | 否 |
> | quotas | 否 |
> | resourceTypes | 否 |
> | validateSubscriptionMoveAvailability | 否 |
> | virtualMachines | 是 |
> | virtualMachines/diagnosticSettings | 否 |
> | virtualMachines/Metricdefinitions.listasync | 否 |
> | virtualMachines/計量 | 否 |

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
> | expressRouteCrossConnections/對等互連 | 否 |
> | gatewaySupportedDevices | 否 |
> | networkSecurityGroups | 是 |
> | quotas | 否 |
> | reservedIps | 是 |
> | virtualNetworks | 是 |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | 否 |
> | virtualNetworks / virtualNetworkPeerings | 否 |

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
> | storageAccounts / blobServices | 否 |
> | storageAccounts / fileServices | 否 |
> | storageAccounts/Metricdefinitions.listasync | 否 |
> | storageAccounts/計量 | 否 |
> | storageAccounts/queueServices | 否 |
> | storageAccounts/服務 | 否 |
> | storageAccounts/services/diagnosticSettings | 否 |
> | storageAccounts/services/Metricdefinitions.listasync | 否 |
> | storageAccounts/服務/計量 | 否 |
> | storageAccounts/tableServices | 否 |
> | storageAccounts/vmImages | 否 |
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
> | diskAccesses | 是 |
> | diskEncryptionSets | 是 |
> | disks | 是 |
> | galleries | 是 |
> | galleries / applications | 否 |
> | 資源庫/應用程式/版本 | 否 |
> | galleries / images | 否 |
> | galleries / images / versions | 否 |
> | hostGroups | 是 |
> | hostGroups/主機 | 是 |
> | images | 是 |
> | proximityPlacementGroups | 是 |
> | restorePointCollections | 是 |
> | restorePointCollections / restorePoints | 否 |
> | sharedVMExtensions | 是 |
> | sharedVMExtensions/版本 | 否 |
> | sharedVMImages | 是 |
> | sharedVMImages/版本 | 否 |
> | snapshots | 是 |
> | sshPublicKeys | 是 |
> | virtualMachines | 是 |
> | virtualMachines/extensions | 是 |
> | virtualMachines/Metricdefinitions.listasync | 否 |
> | virtualMachines/runCommands | 是 |
> | virtualMachineScaleSets | 是 |
> | virtualMachineScaleSets/extensions | 否 |
> | virtualMachineScaleSets/networkInterfaces | 否 |
> | virtualMachineScaleSets/publicIPAddresses | 否 |
> | virtualMachineScaleSets/virtualMachines | 否 |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | 否 |

## <a name="microsoftconnectedcache"></a>ConnectedCache

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | CacheNodes | 是 |

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
> | ReservationRecommendationDetails | 否 |
> | ReservationRecommendations | 否 |
> | ReservationSummaries | 否 |
> | ReservationTransactions | 否 |
> | 標籤 | 否 |
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
> | 登錄/agentPools | 是 |
> | registries / builds | 否 |
> | registries / builds / cancel | 否 |
> | 登錄/組建/getLogLink | 否 |
> | registries / buildTasks | 是 |
> | 登錄/buildTasks/步驟 | 否 |
> | 登錄/eventGridFilters | 否 |
> | 登錄/exportPipelines | 否 |
> | 登錄/generateCredentials | 否 |
> | 登錄/getBuildSourceUploadUrl | 否 |
> | 登錄/GetCredentials | 否 |
> | 登錄/importImage | 否 |
> | 登錄/importPipelines | 否 |
> | 登錄/pipelineRuns | 否 |
> | 登錄/privateEndpointConnectionProxies | 否 |
> | 登錄/privateEndpointConnectionProxies/驗證 | 否 |
> | 登錄/privateEndpointConnections | 否 |
> | 登錄/privateLinkResources | 否 |
> | 登錄/queueBuild | 否 |
> | 登錄/regenerateCredential | 否 |
> | 登錄/regenerateCredentials | 否 |
> | registries / replications | 是 |
> | registries / runs | 否 |
> | registries / runs / cancel | 否 |
> | 登錄/scheduleRun | 否 |
> | registries / scopeMaps | 否 |
> | 登錄/taskRuns | 否 |
> | registries / tasks | 是 |
> | registries / tokens | 否 |
> | 登錄/updatePolicies | 否 |
> | registries / webhooks | 是 |
> | 登錄/webhook/getCallbackConfig | 否 |
> | registries / webhooks / ping | 否 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | containerServices | 是 |
> | managedClusters | 是 |
> | openShiftManagedClusters | 是 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 警示 | 否 |
> | BillingAccounts | 否 |
> | 預算 | 否 |
> | CloudConnectors | 否 |
> | 連接器 | 是 |
> | costAllocationRules | 否 |
> | 部門 | 否 |
> | 維度 | 否 |
> | EnrollmentAccounts | 否 |
> | 多餘 | 否 |
> | ExternalBillingAccounts | 否 |
> | ExternalBillingAccounts/警示 | 否 |
> | ExternalBillingAccounts/維度 | 否 |
> | ExternalBillingAccounts/預測 | 否 |
> | ExternalBillingAccounts/查詢 | 否 |
> | ExternalSubscriptions | 否 |
> | ExternalSubscriptions/警示 | 否 |
> | ExternalSubscriptions/維度 | 否 |
> | ExternalSubscriptions/預測 | 否 |
> | ExternalSubscriptions/查詢 | 否 |
> | 趨勢預測 | 否 |
> | 查詢 | 否 |
> | 註冊 | 否 |
> | Reportconfigs | 否 |
> | 報表 | 否 |
> | 設定 | 否 |
> | showbackRules | 否 |
> | 檢視 | 否 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | requests | 否 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | associations | 否 |
> | resourceProviders | 是 |

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
> | 工作區/dbWorkspaces | 否 |
> | 工作區/storageEncryption | 否 |
> | 工作區/virtualNetworkPeerings | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | catalogs | 是 |
> | datacatalogs | 是 |
> | datacatalogs/資料來源 | 否 |
> | datacatalogs/資料來源/掃描 | 否 |
> | datacatalogs/資料來源/掃描/資料集 | 否 |
> | datacatalogs/資料來源/掃描/觸發程式 | 否 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | dataFactories | 是 |
> | Microsoft.azure.management.datafactories/diagnosticSettings | 否 |
> | Microsoft.azure.management.datafactories/Metricdefinitions.listasync | 否 |
> | dataFactorySchema | 否 |
> | factories | 是 |
> | factories / integrationRuntimes | 否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | accounts / dataLakeStoreAccounts | 否 |
> | accounts / storageAccounts | 否 |
> | 帳戶/storageAccounts/容器 | 否 |
> | 帳戶/transferAnalyticsUnits | 否 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | 帳戶/eventGridFilters | 否 |
> | accounts / firewallRules | 否 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 服務 | 是 |
> | services / projects | 是 |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | BackupVaults | 是 |
> | ResourceOperationGateKeepers | 是 |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | accounts / shares | 否 |
> | accounts / shares / datasets | 否 |
> | accounts / shares / invitations | 否 |
> | accounts / shares / providersharesubscriptions | 否 |
> | 帳戶/共用/synchronizationSettings | 否 |
> | accounts / sharesubscriptions | 否 |
> | accounts/sharesubscriptions/consumerSourceDataSets | 否 |
> | accounts / sharesubscriptions / datasetmappings | 否 |
> | accounts / sharesubscriptions / triggers | 否 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | servers | 是 |
> | servers / advisors | 否 |
> | servers / keys | 否 |
> | 伺服器/privateEndpointConnectionProxies | 否 |
> | 伺服器/privateEndpointConnections | 否 |
> | 伺服器/privateLinkResources | 否 |
> | 伺服器/queryTexts | 否 |
> | 伺服器/recoverableServers | 否 |
> | 伺服器/topQueryStatistics | 否 |
> | servers / virtualNetworkRules | 否 |
> | 伺服器/waitStatistics | 否 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | servers | 是 |
> | servers / advisors | 否 |
> | servers / keys | 否 |
> | 伺服器/privateEndpointConnectionProxies | 否 |
> | 伺服器/privateEndpointConnections | 否 |
> | 伺服器/privateLinkResources | 否 |
> | 伺服器/queryTexts | 否 |
> | 伺服器/recoverableServers | 否 |
> | 伺服器/topQueryStatistics | 否 |
> | servers / virtualNetworkRules | 否 |
> | 伺服器/waitStatistics | 否 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | serverGroups | 是 |
> | servers | 是 |
> | servers / advisors | 否 |
> | servers / keys | 否 |
> | 伺服器/privateEndpointConnectionProxies | 否 |
> | 伺服器/privateEndpointConnections | 否 |
> | 伺服器/privateLinkResources | 否 |
> | 伺服器/queryTexts | 否 |
> | 伺服器/recoverableServers | 否 |
> | 伺服器/topQueryStatistics | 否 |
> | servers / virtualNetworkRules | 否 |
> | 伺服器/waitStatistics | 否 |
> | serversv2 | 是 |
> | singleServers | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | artifactSources | 是 |
> | rollouts | 是 |
> | serviceTopologies | 是 |
> | serviceTopologies/服務 | 是 |
> | serviceTopologies/services/serviceUnits | 是 |
> | steps | 是 |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | applicationgroups | 是 |
> | applicationgroups / applications | 否 |
> | applicationgroups / desktops | 否 |
> | applicationgroups / startmenuitems | 否 |
> | hostpools | 是 |
> | hostpools / sessionhosts | 否 |
> | hostpools / sessionhosts / usersessions | 否 |
> | hostpools / usersessions | 否 |
> | workspaces | 是 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | ElasticPools | 是 |
> | ElasticPools / IotHubTenants | 是 |
> | ElasticPools/IotHubTenants/securitySettings | 否 |
> | IotHubs | 是 |
> | IotHubs/eventGridFilters | 否 |
> | IotHubs/securitySettings | 否 |
> | ProvisioningServices | 是 |
> | usages | 否 |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | pipelines | 是 |

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
> | labs / environments | 是 |
> | 實驗室/serviceRunners | 是 |
> | 實驗室/virtualMachines | 是 |
> | schedules | 是 |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | digitalTwinsInstances | 是 |
> | digitalTwinsInstances/端點 | 否 |

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
> | 網域/domainOwnershipIdentifiers | 否 |
> | generateSsoRequest | 否 |
> | topLevelDomains | 否 |
> | validateDomainRegistrationInformation | 否 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | lcsprojects | 否 |
> | lcsprojects / clouddeployments | 否 |
> | lcsprojects/連接器 | 否 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 服務 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | domains | 是 |
> | domains / topics | 否 |
> | eventSubscriptions | 否 |
> | extensionTopics | 否 |
> | partnerNamespaces | 是 |
> | partnerNamespaces/eventChannels | 否 |
> | partnerRegistrations | 是 |
> | partnerTopics | 是 |
> | partnerTopics/eventSubscriptions | 否 |
> | systemTopics | 是 |
> | systemTopics/eventSubscriptions | 否 |
> | topics | 是 |
> | topicTypes | 否 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | 命名空間 | 是 |
> | namespaces / authorizationrules | 否 |
> | namespaces / disasterrecoveryconfigs | 否 |
> | namespaces / eventhubs | 否 |
> | namespaces / eventhubs / authorizationrules | 否 |
> | namespaces / eventhubs / consumergroups | 否 |
> | namespaces / networkrulesets | 否 |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | experimentWorkspaces | 是 |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 命名空間 | 是 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | featureProviders | 否 |
> | 特性 | 否 |
> | 提供者 | 否 |
> | subscriptionFeatureRegistrations | 否 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | enroll | 否 |
> | galleryitems | 否 |
> | generateartifactaccessuri | 否 |
> | myareas | 否 |
> | myareas/區域 | 否 |
> | myareas/區域/區域 | 否 |
> | myareas/區域/區域/galleryitem | 否 |
> | myareas/areas/galleryitem | 否 |
> | myareas/galleryitem | 否 |
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
> | autoManagedAccounts | 是 |
> | autoManagedVmConfigurationProfiles | 是 |
> | configurationProfileAssignments | 否 |
> | guestConfigurationAssignments | 否 |
> | software | 否 |
> | softwareUpdateProfile | 否 |
> | softwareUpdates | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | hanaInstances | 是 |
> | sapMonitors | 是 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | dedicatedHSMs | 是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | clusters / applications | 否 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 服務 | 是 |
> | 服務/iomtconnectors | 否 |
> | 服務/iomtconnectors/連線 | 否 |
> | 服務/iomtconnectors/對應 | 否 |
> | 服務/privateEndpointConnectionProxies | 否 |
> | 服務/privateEndpointConnections | 是 |
> | 服務/privateLinkResources | 是 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | machines | 是 |
> | machines / extensions | 是 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | dataManagers | 是 |

## <a name="microsofthybridnetwork"></a>HybridNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | devices | 是 |
> | registeredSubscriptions | 否 |
> | 廠商 | 否 |
> | 廠商/sku | 否 |
> | 廠商/vnfs | 否 |
> | virtualNetworkFunctionSkus | 否 |
> | vnfs | 是 |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | components | 是 |
> | networkScopes | 是 |

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
> | appTemplates | 否 |
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
> | managedHSMs | 是 |
> | vaults | 是 |
> | 保存庫/accessPolicies | 否 |
> | 保存庫/eventGridFilters | 否 |
> | vaults / secrets | 否 |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | connectedClusters | 是 |
> | registeredSubscriptions | 否 |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | sourceControlConfigurations | 否 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | clusters / attacheddatabaseconfigurations | 否 |
> | clusters / databases | 否 |
> | clusters / databases / dataconnections | 否 |
> | clusters / databases / eventhubconnections | 否 |
> | clusters / databases / principalassignments | 否 |
> | 叢集/dataconnections | 否 |
> | clusters / principalassignments | 否 |
> | 叢集/sharedidentities | 否 |

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
> | integrationServiceEnvironments | 是 |
> | integrationServiceEnvironments / managedApis | 是 |
> | isolatedEnvironments | 是 |
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
> | workspaces / computes | 否 |
> | 工作區/eventGridFilters | 否 |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | applyUpdates | 否 |
> | configurationAssignments | 否 |
> | maintenanceConfigurations | 是 |
> | updates | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | Identities | 否 |
> | userAssignedIdentities | 是 |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | managedNetworks | 是 |
> | managedNetworks / managedNetworkGroups | 是 |
> | managedNetworks / managedNetworkPeeringPolicies | 是 |
> | 通知 | 是 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | 否 |
> | registrationAssignments | 否 |
> | registrationDefinitions | 否 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | getEntities | 否 |
> | managementGroups | 否 |
> | managementGroups/設定 | 否 |
> | resources | 否 |
> | startTenantBackfill | 否 |
> | tenantBackfillStatus | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | 帳戶/eventGridFilters | 否 |
> | 帳戶/privateAtlases | 是 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | offers | 否 |
> | offerTypes | 否 |
> | offerTypes/發行者 | 否 |
> | offerTypes/發行者/優惠 | 否 |
> | offerTypes/發行者/優惠/方案 | 否 |
> | offerTypes/發行者/優惠/方案/合約 | 否 |
> | offerTypes/發行者/供應專案/方案/專案 | 否 |
> | offerTypes/發行者/優惠/方案/importImage | 否 |
> | privategalleryitems | 否 |
> | privateStoreClient | 否 |
> | privateStores | 否 |
> | privateStores/優惠 | 否 |
> | products | 否 |
> | publishers | 否 |
> | publishers / offers | 否 |
> | publishers / offers / amendments | 否 |
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
> | windowsazure.mediaservices.extensions/accountFilters | 否 |
> | mediaservices / assets | 否 |
> | windowsazure.mediaservices.extensions/資產/Assetfilter | 否 |
> | windowsazure.mediaservices.extensions/contentKeyPolicies | 否 |
> | windowsazure.mediaservices.extensions/eventGridFilters | 否 |
> | windowsazure.mediaservices.extensions/liveEventOperations | 否 |
> | mediaservices / liveEvents | 是 |
> | mediaservices / liveEvents / liveOutputs | 否 |
> | windowsazure.mediaservices.extensions/Liveevent/privateEndpointConnectionProxies | 否 |
> | windowsazure.mediaservices.extensions/liveOutputOperations | 否 |
> | windowsazure.mediaservices.extensions/mediaGraphs | 否 |
> | windowsazure.mediaservices.extensions/streamingEndpointOperations | 否 |
> | mediaservices / streamingEndpoints | 是 |
> | windowsazure.mediaservices.extensions/Streamingendpoint/privateEndpointConnectionProxies | 否 |
> | windowsazure.mediaservices.extensions/Streaminglocator | 否 |
> | windowsazure.mediaservices.extensions/streamingPolicies | 否 |
> | windowsazure.mediaservices.extensions/streamingPrivateEndpointConnectionProxyOperations | 否 |
> | mediaservices / transforms | 否 |
> | mediaservices / transforms / jobs | 否 |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | appClusters | 是 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | assessmentProjects | 是 |
> | migrateprojects | 是 |
> | moveCollections | 是 |
> | projects | 是 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | 是 |
> | objectUnderstandingAccounts | 是 |
> | remoteRenderingAccounts | 是 |
> | spatialAnchorsAccounts | 是 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | netAppAccounts | 是 |
> | netAppAccounts / accountBackups | 否 |
> | netAppAccounts / capacityPools | 是 |
> | netAppAccounts/capacityPools/磁片區 | 是 |
> | netAppAccounts/capacityPools/磁片區/快照集 | 是 |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | applicationGateways | 是 |
> | applicationGatewayWebApplicationFirewallPolicies | 是 |
> | applicationSecurityGroups | 是 |
> | azureFirewallFqdnTags | 否 |
> | azureFirewalls | 是 |
> | bastionHosts | 是 |
> | bgpServiceCommunities | 否 |
> | connections | 是 |
> | ddosCustomPolicies | 是 |
> | ddosProtectionPlans | 是 |
> | dnsOperationStatuses | 否 |
> | dnszones | 是 |
> | dnszones/A | 否 |
> | dnszones/AAAA | 否 |
> | dnszones / all | 否 |
> | dnszones/CAA | 否 |
> | dnszones/CNAME | 否 |
> | dnszones/MX | 否 |
> | dnszones/NS | 否 |
> | dnszones/PTR | 否 |
> | dnszones / recordsets | 否 |
> | dnszones/SOA | 否 |
> | dnszones/SRV | 否 |
> | dnszones/TXT | 否 |
> | expressRouteCircuits | 是 |
> | expressRouteCrossConnections | 是 |
> | expressRouteGateways | 是 |
> | expressRoutePorts | 是 |
> | expressRouteServiceProviders | 否 |
> | firewallPolicies | 是 |
> | frontdoors | 是 |
> | frontdoorWebApplicationFirewallManagedRuleSets | 否 |
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
> | networkWatchers / connectionMonitors | 是 |
> | networkWatchers / flowLogs | 是 |
> | networkWatchers/鏡頭 | 是 |
> | networkWatchers / pingMeshes | 是 |
> | p2sVpnGateways | 是 |
> | privateDnsOperationStatuses | 否 |
> | privateDnsZones | 是 |
> | privateDnsZones/A | 否 |
> | privateDnsZones/AAAA | 否 |
> | privateDnsZones/全部 | 否 |
> | privateDnsZones/CNAME | 否 |
> | privateDnsZones/MX | 否 |
> | privateDnsZones/PTR | 否 |
> | privateDnsZones/SOA | 否 |
> | privateDnsZones/SRV | 否 |
> | privateDnsZones/TXT | 否 |
> | privateDnsZones / virtualNetworkLinks | 是 |
> | privateEndpoints | 是 |
> | privateLinkServices | 是 |
> | publicIPAddresses | 是 |
> | publicIPPrefixes | 是 |
> | routeFilters | 是 |
> | routeTables | 是 |
> | serviceEndpointPolicies | 是 |
> | trafficManagerGeographicHierarchies | 否 |
> | trafficmanagerprofiles | 是 |
> | trafficmanagerprofiles/熱度圖 | 否 |
> | trafficManagerUserMetricsKeys | 否 |
> | virtualHubs | 是 |
> | virtualNetworkGateways | 是 |
> | virtualNetworks | 是 |
> | virtualNetworks/子網 | 否 |
> | virtualNetworkTaps | 是 |
> | virtualWans | 是 |
> | vpnGateways | 是 |
> | vpnSites | 是 |
> | webApplicationFirewallPolicies | 是 |

## <a name="microsoftnotebooks"></a>Microsoft 筆記本

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | NotebookProxies | 否 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 命名空間 | 是 |
> | namespaces / notificationHubs | 是 |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | osNamespaces | 是 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | HyperVSites | 是 |
> | ImportSites | 是 |
> | ServerSites | 是 |
> | VMwareSites | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | deletedWorkspaces | 否 |
> | linkTargets | 否 |
> | storageInsightConfigs | 否 |
> | workspaces | 是 |
> | 工作區/dataExports | 否 |
> | 工作區/資料來源 | 否 |
> | 工作區/Linkedservices.json 和 datasets.json | 否 |
> | 工作區/linkedStorageAccounts | 否 |
> | workspaces / metadata | 否 |
> | workspaces / query | 否 |
> | 工作區/scopedPrivateLinkProxies | 否 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | managementassociations | 否 |
> | managementconfigurations | 是 |
> | solutions | 是 |
> | 檢視 | 是 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | legacyPeerings | 否 |
> | peerAsns | 否 |
> | peerings | 是 |
> | peeringServiceCountries | 否 |
> | peeringServiceProviders | 否 |
> | peeringServices | 是 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | policyEvents | 否 |
> | policyMetadata | 否 |
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
> | privateLinkServicesForPowerBI | 是 |
> | tenants | 是 |
> | 租使用者/工作區 | 否 |
> | workspaceCollections | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | capacities | 是 |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | 是 |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | providerRegistrations | 否 |
> | providerRegistrations / defaultRollouts | 否 |
> | providerRegistrations / resourceTypeRegistrations | 否 |
> | rollouts | 是 |

## <a name="microsoftquantum"></a>Microsoft.Quantum

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

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | OpenShiftClusters | 是 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 命名空間 | 是 |
> | namespaces / authorizationrules | 否 |
> | namespaces / hybridconnections | 否 |
> | namespaces / hybridconnections / authorizationrules | 否 |
> | 命名空間/privateEndpointConnections | 否 |
> | namespaces / wcfrelays | 否 |
> | namespaces / wcfrelays / authorizationrules | 否 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 查詢 | 是 |
> | resourceChangeDetails | 否 |
> | resourceChanges | 否 |
> | resources | 否 |
> | resourcesHistory | 否 |
> | subscriptionsStatus | 否 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | availabilityStatuses | 否 |
> | childAvailabilityStatuses | 否 |
> | childResources | 否 |
> | emergingissues | 否 |
> | 活動 | 否 |
> | impactedResources | 否 |
> | 中繼資料 | 否 |
> | 通知 | 否 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | calculateTemplateHash | 否 |
> | deployments | 否 |
> | deployments / operations | 否 |
> | deploymentScripts | 是 |
> | deploymentScripts/記錄 | 否 |
> | 連結 | 否 |
> | notifyResourceJobs | 否 |
> | 提供者 | 否 |
> | resourceGroups | 否 |
> | subscriptions | 否 |
> | templateSpecs | 是 |
> | templateSpecs/版本 | 是 |
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
> | adaptiveNetworkHardenings | 否 |
> | advancedThreatProtectionSettings | 否 |
> | alerts | 否 |
> | alertsSuppressionRules | 否 |
> | allowedConnections | 否 |
> | applicationWhitelistings | 否 |
> | assessmentMetadata | 否 |
> | assessments | 否 |
> | autoDismissAlertsRules | 否 |
> | automations | 是 |
> | AutoProvisioningSettings | 否 |
> | Compliances | 否 |
> | dataCollectionAgents | 否 |
> | deviceSecurityGroups | 否 |
> | discoveredSecuritySolutions | 否 |
> | externalSecuritySolutions | 否 |
> | InformationProtectionPolicies | 否 |
> | iotSecuritySolutions | 是 |
> | iotSecuritySolutions / analyticsModels | 否 |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | 否 |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | 否 |
> | iotSecuritySolutions / iotAlerts | 否 |
> | iotSecuritySolutions / iotAlertTypes | 否 |
> | jitNetworkAccessPolicies | 否 |
> | 原則 | 否 |
> | pricings | 否 |
> | regulatoryComplianceStandards | 否 |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 否 |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 否 |
> | secureScoreControlDefinitions | 否 |
> | secureScoreControls | 否 |
> | secureScores | 否 |
> | secureScores / secureScoreControls | 否 |
> | securityContacts | 否 |
> | securitySolutions | 否 |
> | securitySolutionsReferenceData | 否 |
> | securityStatuses | 否 |
> | securityStatusesSummaries | 否 |
> | serverVulnerabilityAssessments | 否 |
> | settings | 否 |
> | subAssessments | 否 |
> | 工作 | 否 |
> | topologies | 否 |
> | workspaceSettings | 否 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | diagnosticSettings | 否 |
> | diagnosticSettingsCategories | 否 |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | aggregations | 否 |
> | alertRules | 否 |
> | alertRuleTemplates | 否 |
> | automationRules | 否 |
> | bookmarks | 否 |
> | 案例 | 否 |
> | dataConnectors | 否 |
> | dataConnectorsCheckRequirements | 否 |
> | 實體 | 否 |
> | entityQueries | 否 |
> | incidents | 否 |
> | officeConsents | 否 |
> | settings | 否 |
> | threatIntelligence | 否 |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | consoleServices | 否 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 命名空間 | 是 |
> | namespaces / authorizationrules | 否 |
> | namespaces / disasterrecoveryconfigs | 否 |
> | namespaces / eventgridfilters | 否 |
> | namespaces / networkrulesets | 否 |
> | namespaces / queues | 否 |
> | namespaces / queues / authorizationrules | 否 |
> | namespaces / topics | 否 |
> | namespaces / topics / authorizationrules | 否 |
> | namespaces / topics / subscriptions | 否 |
> | namespaces / topics / subscriptions / rules | 否 |
> | premiumMessagingRegions | 否 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 應用程式所需 | 是 |
> | clusters | 是 |
> | clusters / applications | 否 |
> | containerGroups | 是 |
> | containerGroupSets | 是 |
> | edgeclusters | 是 |
> | edgeclusters/應用程式 | 否 |
> | managedclusters | 是 |
> | managedclusters/nodetypes | 否 |
> | networks | 是 |
> | secretstores | 是 |
> | secretstores/憑證 | 否 |
> | secretstores/秘密 | 否 |
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

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | providerRegistrations | 否 |
> | providerRegistrations / resourceTypeRegistrations | 否 |
> | rollouts | 是 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | SignalR | 是 |
> | SignalR/eventGridFilters | 否 |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | hybridUseBenefits | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | applicationDefinitions | 是 |
> | 應用程式所需 | 是 |
> | jitRequests | 是 |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | managedInstances | 是 |
> | managedInstances/資料庫 | 是 |
> | managedInstances/資料庫/backupShortTermRetentionPolicies | 否 |
> | managedInstances/資料庫/架構/資料表/資料行/sensitivityLabels | 否 |
> | managedInstances/資料庫/vulnerabilityAssessments | 否 |
> | managedInstances/資料庫/vulnerabilityAssessments/規則/基準 | 否 |
> | managedInstances/encryptionProtector | 否 |
> | managedInstances/金鑰 | 否 |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | 否 |
> | managedInstances/vulnerabilityAssessments | 否 |
> | servers | 是 |
> | servers / administrators | 否 |
> | 伺服器/communicationLinks | 否 |
> | servers / databases | 是 |
> | 伺服器/encryptionProtector | 否 |
> | servers / firewallRules | 否 |
> | servers / keys | 否 |
> | 伺服器/restorableDroppedDatabases | 否 |
> | servers / serviceobjectives | 否 |
> | 伺服器/tdeCertificates | 否 |
> | virtualClusters | 否 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | 是 |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | 否 |
> | SqlVirtualMachines | 是 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | storageAccounts | 是 |
> | storageAccounts / blobServices | 否 |
> | storageAccounts / fileServices | 否 |
> | storageAccounts/queueServices | 否 |
> | storageAccounts/服務 | 否 |
> | storageAccounts/services/Metricdefinitions.listasync | 否 |
> | storageAccounts/tableServices | 否 |
> | usages | 否 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | caches | 是 |
> | 快取/storageTargets | 否 |
> | usageModels | 否 |

## <a name="microsoftstoragereplication"></a>StorageReplication

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | replicationGroups | 否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | storageSyncServices | 是 |
> | storageSyncServices / registeredServers | 否 |
> | storageSyncServices / syncGroups | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 |
> | storageSyncServices/工作流程 | 否 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | storageSyncServices | 是 |
> | storageSyncServices / registeredServers | 否 |
> | storageSyncServices / syncGroups | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 |
> | storageSyncServices/工作流程 | 否 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | storageSyncServices | 是 |
> | storageSyncServices / registeredServers | 否 |
> | storageSyncServices / syncGroups | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 |
> | storageSyncServices/工作流程 | 否 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | managers | 是 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | clusters | 是 |
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

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | privateLinkHubs | 是 |
> | workspaces | 是 |
> | 工作區/bigDataPools | 是 |
> | 工作區/operationStatuses | 否 |
> | 工作區/sqlPools | 是 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | environments | 是 |
> | environments / accessPolicies | 否 |
> | environments / eventsources | 是 |
> | environments / referenceDataSets | 是 |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | stores | 是 |
> | 存放區/accessPolicies | 否 |
> | stores / services | 否 |
> | stores / services / tokens | 否 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | imageTemplates | 是 |
> | imageTemplates / runOutputs | 否 |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | ArcZones | 是 |
> | ResourcePools | 是 |
> | VCenters | 是 |
> | VirtualMachines | 是 |
> | VirtualMachineTemplates | 是 |
> | VirtualNetworks | 是 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | dedicatedCloudNodes | 是 |
> | dedicatedCloudServices | 是 |
> | virtualMachines | 是 |

## <a name="microsoftvmwareonazure"></a>VMwareOnAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | privateClouds | 是 |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | devices | 是 |
> | registeredSubscriptions | 否 |
> | 廠商 | 否 |
> | 廠商/sku | 否 |
> | 廠商/vnfs | 否 |
> | virtualNetworkFunctionSkus | 否 |
> | vnfs | 是 |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | plans | 是 |
> | registeredSubscriptions | 否 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | apiManagementAccounts | 否 |
> | apiManagementAccounts/Apiacl | 否 |
> | apiManagementAccounts/api | 否 |
> | apiManagementAccounts/api/Apiacl | 否 |
> | apiManagementAccounts/api/Connectionacl | 否 |
> | apiManagementAccounts/api/連線 | 否 |
> | apiManagementAccounts/api/connections/Connectionacl | 否 |
> | apiManagementAccounts/api/localizedDefinitions | 否 |
> | apiManagementAccounts/Connectionacl | 否 |
> | apiManagementAccounts/connections | 否 |
> | billingMeters | 否 |
> | certificates | 是 |
> | connectionGateways | 是 |
> | connections | 是 |
> | customApis | 是 |
> | deletedSites | 否 |
> | hostingEnvironments | 是 |
> | hostingEnvironments/eventGridFilters | 否 |
> | hostingEnvironments/multiRolePools | 否 |
> | hostingEnvironments/workerPools | 否 |
> | kubeEnvironments | 是 |
> | publishingUsers | 否 |
> | Mahout | 否 |
> | resourceHealthMetadata | 否 |
> | runtimes | 否 |
> | serverFarms | 是 |
> | serverFarms/eventGridFilters | 否 |
> | sites | 是 |
> | sites/config  | 否 |
> | sites/eventGridFilters | 否 |
> | sites/hostNameBindings | 否 |
> | sites/Networkconfig.netcfg | 否 |
> | sites / premieraddons | 是 |
> | sites / slots | 是 |
> | sites/位置/eventGridFilters | 否 |
> | sites/位置/hostNameBindings | 否 |
> | sites/位置/Networkconfig.netcfg | 否 |
> | sourceControls | 否 |
> | staticSites | 是 |
> | validate | 否 |
> | verifyHostingEnvironmentVnet | 否 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | diagnosticSettings | 否 |
> | diagnosticSettingsCategories | 否 |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | multipleActivationKeys | 是 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | DeviceServices | 是 |

## <a name="microsoftworkloadbuilder"></a>WorkloadBuilder

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 工作負載 | 是 |
> | 工作負載/實例 | 否 |
> | 工作負載/版本 | 否 |
> | 工作負載/版本/構件 | 否 |

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
