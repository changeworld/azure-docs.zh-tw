---
title: 完整模式刪除
description: 顯示資源類型如何處理 Azure Resource Manager 範本中的完整模式刪除。
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: 5e247fc20a128b3dd8b3fe646ef956388e0d2c2d
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027837"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>完整模式部署的 Azure 資源刪除

此文章說明當以完整模式部署的範本中沒有資源類型時，資源類型會如何處理刪除。

當類型不在以完整模式部署的範本中時，會刪除標示為 **[是]** 的資源類型。

當不在範本中時，標記為 [**否**] 的資源類型不會自動刪除;不過，如果刪除父資源，則會刪除它們。 如需行為的完整描述，請參閱 [Azure Resource Manager 部署模式](deployment-modes.md)。

如果您[在範本中部署到一個以上的資源群組](cross-resource-group-deployment.md)，部署作業中指定的資源群組中的資源就有資格刪除。 不會刪除次要資源群組中的資源。

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
> | DomainServices | Yes |
> | DomainServices/oucontainer | No |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | supportProviders | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | aadsupportcases | No |
> | addsservices | 否 |
> | agents | No |
> | anonymousapiusers | No |
> | 組態 | No |
> | 記錄 | No |
> | reports | No |
> | servicehealthmetrics | No |
> | 服務 | No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 組態 | No |
> | generateRecommendations | No |
> | 中繼資料 | No |
> | Mahout | No |
> | suppressions | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | actionRules | Yes |
> | alerts | No |
> | alertsList | No |
> | alertsMetaData | No |
> | alertsSummary | No |
> | alertsSummaryList | No |
> | smartDetectorAlertRules | Yes |
> | smartGroups | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | servers | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | reportFeedback | No |
> | 服務 | 是 |
> | validateServiceName | No |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | configurationStores | Yes |
> | configurationStores / eventGridFilters | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | Spring | Yes |
> | 春季/應用程式 | No |
> | 春季/apps/部署 | No |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | attestationProviders | Yes |
> | defaultProviders | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | classicAdministrators | No |
> | dataAliases | No |
> | denyAssignments | No |
> | elevateAccess | No |
> | findOrphanRoleAssignments | No |
> | locks | 否 |
> | 權限 | 否 |
> | policyAssignments | No |
> | policyDefinitions | No |
> | policySetDefinitions | No |
> | privateLinkAssociations | No |
> | providerOperations | No |
> | resourceManagementPrivateLinks | No |
> | roleAssignments | No |
> | roleAssignmentsUsageMetrics | No |
> | roleDefinitions | No |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | automationAccounts | Yes |
> | automationAccounts/設定 | Yes |
> | automationAccounts/作業 | No |
> | automationAccounts/privateEndpointConnectionProxies | No |
> | automationAccounts/privateEndpointConnections | No |
> | automationAccounts/privateLinkResources | No |
> | automationAccounts / runbooks | Yes |
> | automationAccounts/softwareUpdateConfigurations | No |
> | automationAccounts / webhooks | No |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | privateClouds | Yes |
> | privateClouds/授權 | No |
> | privateClouds/叢集 | No |
> | privateClouds / hcxEnterpriseSites | No |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | environments | 否 |
> | 環境/帳戶 | No |
> | 環境/帳戶/命名空間 | No |
> | 環境/帳戶/命名空間/設定 | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | b2cDirectories | 是 |
> | b2ctenants | No |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | dataControllers | Yes |
> | hybridDataManagers | Yes |
> | postgresInstances | Yes |
> | sqlInstances | Yes |
> | sqlManagedInstances | Yes |
> | sqlServerInstances | Yes |
> | sqlServerRegistrations | Yes |
> | sqlServerRegistrations/sqlServers | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | cloudManifestFiles | No |
> | edgeSubscriptions | Yes |
> | registrations | 是 |
> | 註冊/customerSubscriptions | No |
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
> | batchAccounts | Yes |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | billingAccounts | No |
> | billingAccounts/合約 | No |
> | billingAccounts / billingPermissions | No |
> | billingAccounts / billingProfiles | No |
> | billingAccounts / billingProfiles / billingPermissions | No |
> | billingAccounts / billingProfiles / billingRoleAssignments | No |
> | billingAccounts / billingProfiles / billingRoleDefinitions | No |
> | billingAccounts / billingProfiles / billingSubscriptions | No |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | No |
> | billingAccounts/billingProfiles/客戶 | No |
> | billingAccounts/billingProfiles/指示 | No |
> | billingAccounts/billingProfiles/發票 | No |
> | billingAccounts/billingProfiles/發票/pricesheet | No |
> | billingAccounts/billingProfiles/發票/交易 | No |
> | billingAccounts / billingProfiles / invoiceSections | No |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | No |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | No |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | No |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | No |
> | billingAccounts/billingProfiles/invoiceSections/products | No |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | No |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | No |
> | billingAccounts/billingProfiles/invoiceSections/筆交易 | No |
> | billingAccounts/billingProfiles/invoiceSections/transfer | No |
> | billingAccounts / BillingProfiles / patchOperations | No |
> | billingAccounts / billingProfiles / paymentMethods | No |
> | billingAccounts/billingProfiles/原則 | No |
> | billingAccounts/billingProfiles/pricesheet | No |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | No |
> | billingAccounts/billingProfiles/products | No |
> | billingAccounts/billingProfiles/筆交易 | No |
> | billingAccounts / billingRoleAssignments | No |
> | billingAccounts / billingRoleDefinitions | No |
> | billingAccounts / billingSubscriptions | No |
> | billingAccounts/billingSubscriptions/發票 | No |
> | billingAccounts / createBillingRoleAssignment | No |
> | billingAccounts / createInvoiceSectionOperations | No |
> | billingAccounts/客戶 | No |
> | billingAccounts/customers/billingPermissions | No |
> | billingAccounts/customers/billingSubscriptions | No |
> | billingAccounts/customers/initiateTransfer | No |
> | billingAccounts/customers/原則 | No |
> | billingAccounts/customers/products | No |
> | billingAccounts/customers/交易所 | No |
> | billingAccounts/客戶/傳輸 | No |
> | billingAccounts/部門 | No |
> | billingAccounts/部門/billingPermissions | No |
> | billingAccounts/部門/billingRoleAssignments | No |
> | billingAccounts/部門/billingRoleDefinitions | No |
> | billingAccounts / enrollmentAccounts | No |
> | billingAccounts / enrollmentAccounts / billingPermissions | No |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | No |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | No |
> | billingAccounts/發票 | No |
> | billingAccounts/發票/交易 | No |
> | billingAccounts / invoiceSections | No |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | No |
> | billingAccounts / invoiceSections / billingSubscriptions | No |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | No |
> | billingAccounts/invoiceSections/提升許可權 | No |
> | billingAccounts / invoiceSections / initiateTransfer | No |
> | billingAccounts / invoiceSections / patchOperations | No |
> | billingAccounts / invoiceSections / productMoveOperations | No |
> | billingAccounts/invoiceSections/products | No |
> | billingAccounts/invoiceSections/products/transfer | No |
> | billingAccounts/invoiceSections/products/updateAutoRenew | No |
> | billingAccounts/invoiceSections/筆交易 | No |
> | billingAccounts/invoiceSections/transfer | No |
> | billingAccounts / lineOfCredit | No |
> | billingAccounts / patchOperations | No |
> | billingAccounts / paymentMethods | No |
> | billingAccounts/products | No |
> | billingAccounts/筆交易 | No |
> | billingPeriods | No |
> | billingPermissions | No |
> | billingProperty | No |
> | billingRoleAssignments | No |
> | billingRoleDefinitions | No |
> | createBillingRoleAssignment | No |
> | departments | No |
> | enrollmentAccounts | No |
> | invoices | No |
> | transfers | 否 |
> | 傳輸/acceptTransfer | No |
> | 傳輸/declineTransfer | No |
> | 傳輸/operationStatus | No |
> | 傳輸/validateTransfer | No |
> | validateAddress | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | mapApis | Yes |
> | updateCommunicationPreference | 否 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | blockchainMembers | Yes |
> | cordaMembers | Yes |
> | watchers | Yes |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | TokenServices | Yes |
> | TokenServices / BlockchainNetworks | No |
> | TokenServices/群組 | No |
> | TokenServices/群組/帳戶 | No |
> | TokenServices / TokenTemplates | No |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | blueprintAssignments | No |
> | blueprintAssignments / assignmentOperations | No |
> | blueprintAssignments/作業 | No |
> | blueprints | No |
> | blueprints / artifacts | No |
> | blueprints / versions | No |
> | blueprints / versions / artifacts | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | botServices | Yes |
> | botServices / channels | No |
> | botServices/connections | No |
> | 語言 | No |
> | 範本 | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | Redis | Yes |
> | Redis/EventGridFilters | No |
> | Redis/privateEndpointConnectionProxies | No |
> | Redis/privateEndpointConnectionProxies/validate | No |
> | Redis/privateEndpointConnections | No |
> | Redis/privateLinkResources | No |
> | redisEnterprise | Yes |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | appliedReservations | No |
> | autoQuotaIncrease | No |
> | calculateExchange | No |
> | calculatePrice | No |
> | calculatePurchasePrice | No |
> | catalogs | No |
> | commercialReservationOrders | 否 |
> | 兌換 | 否 |
> | placePurchaseOrder | No |
> | reservationOrders | No |
> | reservationOrders / calculateRefund | No |
> | reservationOrders/merge | No |
> | reservationOrders/保留 | No |
> | reservationOrders/保留/修訂 | No |
> | reservationOrders/return | No |
> | reservationOrders/split | No |
> | reservationOrders/swap | No |
> | reservations | No |
> | resourceProviders | No |
> | resources | No |
> | validateReservationOrder | No |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No |
> | CdnWebApplicationFirewallPolicies | Yes |
> | edgenodes | No |
> | 設定檔 | Yes |
> | profiles / endpoints | 是 |
> | profiles / endpoints / customdomains | No |
> | profiles / endpoints / origingroups | No |
> | profiles / endpoints / origins | No |
> | validateProbe | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | certificateOrders | Yes |
> | certificateOrders/憑證 | No |
> | validateCertificateRegistrationInformation | No |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | profile | No |
> | resourceChanges | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | capabilities | No |
> | domainNames | Yes |
> | domainNames/功能 | No |
> | domainNames/internalLoadBalancers | No |
> | domainNames/serviceCertificates | No |
> | domainNames/插槽 | No |
> | domainNames/位置/角色 | No |
> | domainNames/位置/角色/Metricdefinitions.listasync | No |
> | domainNames/位置/角色/計量 | No |
> | moveSubscriptionResources | No |
> | operatingSystemFamilies | No |
> | operatingSystems | 否 |
> | quotas | 否 |
> | resourceTypes | No |
> | validateSubscriptionMoveAvailability | No |
> | virtualMachines | Yes |
> | virtualMachines/diagnosticSettings | No |
> | virtualMachines/Metricdefinitions.listasync | No |
> | virtualMachines/計量 | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | classicInfrastructureResources | No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | capabilities | No |
> | expressRouteCrossConnections | No |
> | expressRouteCrossConnections/對等互連 | No |
> | gatewaySupportedDevices | No |
> | networkSecurityGroups | Yes |
> | quotas | No |
> | reservedIps | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | No |
> | virtualNetworks / virtualNetworkPeerings | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | capabilities | No |
> | disks | No |
> | images | No |
> | osImages | No |
> | osPlatformImages | No |
> | publicImages | No |
> | quotas | No |
> | storageAccounts | Yes |
> | storageAccounts / blobServices | No |
> | storageAccounts / fileServices | No |
> | storageAccounts/Metricdefinitions.listasync | No |
> | storageAccounts/計量 | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/服務 | No |
> | storageAccounts/services/diagnosticSettings | No |
> | storageAccounts/services/Metricdefinitions.listasync | No |
> | storageAccounts/服務/計量 | No |
> | storageAccounts/tableServices | No |
> | storageAccounts/vmImages | No |
> | vmImages | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | Yes |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | RateCard | No |
> | UsageAggregates | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | availabilitySets | Yes |
> | diskAccesses | Yes |
> | diskEncryptionSets | Yes |
> | disks | 是 |
> | galleries | Yes |
> | galleries / applications | No |
> | 資源庫/應用程式/版本 | No |
> | galleries / images | 否 |
> | galleries / images / versions | 否 |
> | hostGroups | Yes |
> | hostGroups/主機 | Yes |
> | images | Yes |
> | proximityPlacementGroups | Yes |
> | restorePointCollections | Yes |
> | restorePointCollections / restorePoints | No |
> | sharedVMExtensions | Yes |
> | sharedVMExtensions/版本 | No |
> | sharedVMImages | Yes |
> | sharedVMImages/版本 | No |
> | snapshots | 是 |
> | sshPublicKeys | Yes |
> | virtualMachines | Yes |
> | virtualMachines/extensions | Yes |
> | virtualMachines/Metricdefinitions.listasync | No |
> | virtualMachines/runCommands | Yes |
> | virtualMachineScaleSets | Yes |
> | virtualMachineScaleSets/extensions | No |
> | virtualMachineScaleSets/networkInterfaces | No |
> | virtualMachineScaleSets/publicIPAddresses | No |
> | virtualMachineScaleSets/virtualMachines | No |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | No |

## <a name="microsoftconnectedcache"></a>ConnectedCache

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | CacheNodes | Yes |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | AggregatedCost | No |
> | 餘額 | No |
> | 預算 | No |
> | Charges | No |
> | CostTags | No |
> | credits | No |
> | 活動 | No |
> | 預測 | No |
> | lots | No |
> | Marketplace | No |
> | Pricesheets | No |
> | products | 否 |
> | ReservationDetails | No |
> | ReservationRecommendationDetails | No |
> | ReservationRecommendations | No |
> | ReservationSummaries | No |
> | ReservationTransactions | No |
> | 標籤 | No |
> | tenants | No |
> | 詞彙 | No |
> | UsageDetails | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | containerGroups | Yes |
> | serviceAssociationLinks | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | registries | Yes |
> | 登錄/agentPools | Yes |
> | registries / builds | No |
> | registries / builds / cancel | No |
> | 登錄/組建/getLogLink | No |
> | registries / buildTasks | Yes |
> | 登錄/buildTasks/步驟 | No |
> | 登錄/eventGridFilters | No |
> | 登錄/exportPipelines | No |
> | 登錄/generateCredentials | No |
> | 登錄/getBuildSourceUploadUrl | No |
> | 登錄/GetCredentials | No |
> | 登錄/importImage | No |
> | 登錄/importPipelines | No |
> | 登錄/pipelineRuns | No |
> | 登錄/privateEndpointConnectionProxies | No |
> | 登錄/privateEndpointConnectionProxies/驗證 | No |
> | 登錄/privateEndpointConnections | No |
> | 登錄/privateLinkResources | No |
> | 登錄/queueBuild | No |
> | 登錄/regenerateCredential | No |
> | 登錄/regenerateCredentials | No |
> | registries / replications | 是 |
> | registries / runs | No |
> | registries / runs / cancel | No |
> | 登錄/scheduleRun | No |
> | registries / scopeMaps | No |
> | 登錄/taskRuns | No |
> | registries / tasks | Yes |
> | registries / tokens | No |
> | 登錄/updatePolicies | No |
> | registries / webhooks | 是 |
> | 登錄/webhook/getCallbackConfig | No |
> | registries / webhooks / ping | 否 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | containerServices | Yes |
> | managedClusters | Yes |
> | openShiftManagedClusters | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 警示 | No |
> | BillingAccounts | No |
> | 預算 | No |
> | CloudConnectors | No |
> | 連接器 | Yes |
> | costAllocationRules | No |
> | 部門 | No |
> | 維度 | No |
> | EnrollmentAccounts | No |
> | 多餘 | No |
> | ExternalBillingAccounts | No |
> | ExternalBillingAccounts/警示 | No |
> | ExternalBillingAccounts/維度 | No |
> | ExternalBillingAccounts/預測 | No |
> | ExternalBillingAccounts/查詢 | No |
> | ExternalSubscriptions | No |
> | ExternalSubscriptions/警示 | No |
> | ExternalSubscriptions/維度 | No |
> | ExternalSubscriptions/預測 | No |
> | ExternalSubscriptions/查詢 | No |
> | 趨勢預測 | No |
> | 查詢 | No |
> | 註冊 | No |
> | Reportconfigs | No |
> | 報表 | No |
> | 設定 | No |
> | showbackRules | No |
> | 檢視 | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | requests | No |

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
> | DataBoxEdgeDevices | Yes |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | workspaces | Yes |
> | 工作區/dbWorkspaces | No |
> | 工作區/storageEncryption | No |
> | 工作區/virtualNetworkPeerings | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | catalogs | Yes |
> | datacatalogs | Yes |
> | datacatalogs/資料來源 | No |
> | datacatalogs/資料來源/掃描 | No |
> | datacatalogs/資料來源/掃描/資料集 | No |
> | datacatalogs/資料來源/掃描/觸發程式 | No |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | dataFactories | Yes |
> | Microsoft.azure.management.datafactories/diagnosticSettings | No |
> | Microsoft.azure.management.datafactories/Metricdefinitions.listasync | No |
> | dataFactorySchema | No |
> | factories | Yes |
> | factories / integrationRuntimes | 否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | Yes |
> | accounts / dataLakeStoreAccounts | No |
> | accounts / storageAccounts | No |
> | 帳戶/storageAccounts/容器 | No |
> | 帳戶/transferAnalyticsUnits | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | Yes |
> | 帳戶/eventGridFilters | No |
> | accounts / firewallRules | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 服務 | 是 |
> | services / projects | Yes |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | BackupVaults | Yes |
> | ResourceOperationGateKeepers | Yes |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | Yes |
> | accounts / shares | No |
> | accounts / shares / datasets | No |
> | accounts / shares / invitations | No |
> | accounts / shares / providersharesubscriptions | No |
> | 帳戶/共用/synchronizationSettings | No |
> | accounts / sharesubscriptions | No |
> | accounts/sharesubscriptions/consumerSourceDataSets | No |
> | accounts / sharesubscriptions / datasetmappings | No |
> | accounts / sharesubscriptions / triggers | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | servers | 是 |
> | servers / advisors | 否 |
> | servers / keys | 否 |
> | 伺服器/privateEndpointConnectionProxies | No |
> | 伺服器/privateEndpointConnections | No |
> | 伺服器/privateLinkResources | No |
> | 伺服器/queryTexts | No |
> | 伺服器/recoverableServers | No |
> | 伺服器/topQueryStatistics | No |
> | servers / virtualNetworkRules | No |
> | 伺服器/waitStatistics | No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | servers | 是 |
> | servers / advisors | 否 |
> | servers / keys | 否 |
> | 伺服器/privateEndpointConnectionProxies | No |
> | 伺服器/privateEndpointConnections | No |
> | 伺服器/privateLinkResources | No |
> | 伺服器/queryTexts | No |
> | 伺服器/recoverableServers | No |
> | 伺服器/topQueryStatistics | No |
> | servers / virtualNetworkRules | No |
> | 伺服器/waitStatistics | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | serverGroups | Yes |
> | servers | Yes |
> | servers / advisors | No |
> | servers / keys | No |
> | 伺服器/privateEndpointConnectionProxies | No |
> | 伺服器/privateEndpointConnections | No |
> | 伺服器/privateLinkResources | No |
> | 伺服器/queryTexts | No |
> | 伺服器/recoverableServers | No |
> | 伺服器/topQueryStatistics | No |
> | servers / virtualNetworkRules | No |
> | 伺服器/waitStatistics | No |
> | serversv2 | Yes |
> | singleServers | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | artifactSources | Yes |
> | rollouts | Yes |
> | serviceTopologies | Yes |
> | serviceTopologies/服務 | Yes |
> | serviceTopologies/services/serviceUnits | Yes |
> | steps | Yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | applicationgroups | Yes |
> | applicationgroups / applications | No |
> | applicationgroups / desktops | No |
> | applicationgroups / startmenuitems | No |
> | hostpools | Yes |
> | hostpools / sessionhosts | No |
> | hostpools / sessionhosts / usersessions | No |
> | hostpools / usersessions | No |
> | workspaces | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | ElasticPools | Yes |
> | ElasticPools / IotHubTenants | Yes |
> | ElasticPools/IotHubTenants/securitySettings | No |
> | IotHubs | Yes |
> | IotHubs/eventGridFilters | No |
> | IotHubs/securitySettings | No |
> | ProvisioningServices | Yes |
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
> | controllers | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | labcenters | Yes |
> | labs | Yes |
> | labs / environments | Yes |
> | 實驗室/serviceRunners | Yes |
> | 實驗室/virtualMachines | Yes |
> | schedules | Yes |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | digitalTwinsInstances | Yes |
> | digitalTwinsInstances/端點 | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | databaseAccountNames | No |
> | databaseAccounts | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | domains | Yes |
> | 網域/domainOwnershipIdentifiers | No |
> | generateSsoRequest | No |
> | topLevelDomains | No |
> | validateDomainRegistrationInformation | No |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | lcsprojects | No |
> | lcsprojects / clouddeployments | No |
> | lcsprojects/連接器 | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 服務 | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | domains | Yes |
> | domains / topics | No |
> | eventSubscriptions | No |
> | extensionTopics | No |
> | partnerNamespaces | Yes |
> | partnerNamespaces/eventChannels | No |
> | partnerRegistrations | Yes |
> | partnerTopics | Yes |
> | partnerTopics/eventSubscriptions | No |
> | systemTopics | Yes |
> | systemTopics/eventSubscriptions | No |
> | topics | Yes |
> | topicTypes | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | clusters | Yes |
> | 命名空間 | 是 |
> | namespaces / authorizationrules | No |
> | namespaces / disasterrecoveryconfigs | 否 |
> | namespaces / eventhubs | No |
> | namespaces / eventhubs / authorizationrules | No |
> | namespaces / eventhubs / consumergroups | No |
> | namespaces / networkrulesets | No |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | experimentWorkspaces | Yes |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 命名空間 | 是 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | featureProviders | No |
> | 特性 | No |
> | 提供者 | No |
> | subscriptionFeatureRegistrations | No |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | enroll | No |
> | galleryitems | No |
> | generateartifactaccessuri | No |
> | myareas | No |
> | myareas/區域 | No |
> | myareas/區域/區域 | No |
> | myareas/區域/區域/galleryitem | No |
> | myareas/areas/galleryitem | No |
> | myareas/galleryitem | No |
> | 註冊 | No |
> | resources | No |
> | retrieveresourcesbyid | No |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | Yes |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | autoManagedAccounts | Yes |
> | autoManagedVmConfigurationProfiles | Yes |
> | configurationProfileAssignments | No |
> | guestConfigurationAssignments | No |
> | software | No |
> | softwareUpdateProfile | No |
> | softwareUpdates | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | hanaInstances | Yes |
> | sapMonitors | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | dedicatedHSMs | Yes |

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
> | 服務 | Yes |
> | 服務/iomtconnectors | No |
> | 服務/iomtconnectors/連線 | No |
> | 服務/iomtconnectors/對應 | No |
> | 服務/privateEndpointConnectionProxies | No |
> | 服務/privateEndpointConnections | Yes |
> | 服務/privateLinkResources | Yes |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | machines | 是 |
> | machines / extensions | Yes |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | dataManagers | Yes |

## <a name="microsofthybridnetwork"></a>HybridNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | devices | Yes |
> | registeredSubscriptions | No |
> | 廠商 | No |
> | 廠商/sku | No |
> | 廠商/vnfs | No |
> | virtualNetworkFunctionSkus | 否 |
> | vnfs | Yes |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | components | Yes |
> | networkScopes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | jobs | Yes |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | appTemplates | No |
> | IoTApps | Yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 圖形 | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | deletedVaults | No |
> | hsmPools | Yes |
> | managedHSMs | Yes |
> | vaults | 是 |
> | 保存庫/accessPolicies | No |
> | 保存庫/eventGridFilters | No |
> | vaults / secrets | 否 |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | connectedClusters | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | sourceControlConfigurations | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | clusters | Yes |
> | clusters / attacheddatabaseconfigurations | No |
> | clusters / databases | No |
> | clusters / databases / dataconnections | No |
> | clusters / databases / eventhubconnections | No |
> | clusters / databases / principalassignments | No |
> | 叢集/dataconnections | No |
> | clusters / principalassignments | No |
> | 叢集/sharedidentities | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | labaccounts | Yes |
> | users | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | hostingEnvironments | Yes |
> | integrationAccounts | Yes |
> | integrationServiceEnvironments | Yes |
> | integrationServiceEnvironments / managedApis | Yes |
> | isolatedEnvironments | Yes |
> | workflows | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | commitmentPlans | Yes |
> | webServices | Yes |
> | 工作區 | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | workspaces | Yes |
> | workspaces / computes | No |
> | 工作區/eventGridFilters | No |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | applyUpdates | No |
> | configurationAssignments | No |
> | maintenanceConfigurations | 是 |
> | updates | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | Identities | No |
> | userAssignedIdentities | Yes |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | managedNetworks | Yes |
> | managedNetworks / managedNetworkGroups | Yes |
> | managedNetworks / managedNetworkPeeringPolicies | Yes |
> | 通知 | Yes |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | No |
> | registrationAssignments | No |
> | registrationDefinitions | No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | getEntities | No |
> | managementGroups | No |
> | managementGroups/設定 | No |
> | resources | No |
> | startTenantBackfill | No |
> | tenantBackfillStatus | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | Yes |
> | 帳戶/eventGridFilters | No |
> | 帳戶/privateAtlases | Yes |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | offers | No |
> | offerTypes | No |
> | offerTypes/發行者 | No |
> | offerTypes/發行者/優惠 | No |
> | offerTypes/發行者/優惠/方案 | No |
> | offerTypes/發行者/優惠/方案/合約 | No |
> | offerTypes/發行者/供應專案/方案/專案 | No |
> | offerTypes/發行者/優惠/方案/importImage | No |
> | privategalleryitems | No |
> | privateStoreClient | No |
> | privateStores | No |
> | privateStores/優惠 | No |
> | products | No |
> | publishers | No |
> | publishers / offers | No |
> | publishers / offers / amendments | No |
> | 註冊 | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | classicDevServices | Yes |
> | updateCommunicationPreference | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | agreements | No |
> | offertypes | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | mediaservices | 是 |
> | windowsazure.mediaservices.extensions/accountFilters | 否 |
> | mediaservices / assets | 否 |
> | windowsazure.mediaservices.extensions/資產/Assetfilter | No |
> | windowsazure.mediaservices.extensions/contentKeyPolicies | No |
> | windowsazure.mediaservices.extensions/eventGridFilters | No |
> | windowsazure.mediaservices.extensions/liveEventOperations | No |
> | mediaservices / liveEvents | Yes |
> | mediaservices / liveEvents / liveOutputs | No |
> | windowsazure.mediaservices.extensions/Liveevent/privateEndpointConnectionProxies | No |
> | windowsazure.mediaservices.extensions/liveOutputOperations | No |
> | windowsazure.mediaservices.extensions/mediaGraphs | No |
> | windowsazure.mediaservices.extensions/streamingEndpointOperations | No |
> | mediaservices / streamingEndpoints | Yes |
> | windowsazure.mediaservices.extensions/Streamingendpoint/privateEndpointConnectionProxies | No |
> | windowsazure.mediaservices.extensions/Streaminglocator | No |
> | windowsazure.mediaservices.extensions/streamingPolicies | No |
> | windowsazure.mediaservices.extensions/streamingPrivateEndpointConnectionProxyOperations | No |
> | mediaservices / transforms | No |
> | mediaservices / transforms / jobs | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | appClusters | Yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | assessmentProjects | Yes |
> | migrateprojects | Yes |
> | moveCollections | Yes |
> | projects | Yes |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Yes |
> | objectUnderstandingAccounts | Yes |
> | remoteRenderingAccounts | Yes |
> | spatialAnchorsAccounts | 是 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | netAppAccounts | Yes |
> | netAppAccounts / accountBackups | No |
> | netAppAccounts / capacityPools | Yes |
> | netAppAccounts/capacityPools/磁片區 | Yes |
> | netAppAccounts/capacityPools/磁片區/快照集 | Yes |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | applicationGateways | Yes |
> | applicationGatewayWebApplicationFirewallPolicies | Yes |
> | applicationSecurityGroups | Yes |
> | azureFirewallFqdnTags | No |
> | azureFirewalls | Yes |
> | bastionHosts | Yes |
> | bgpServiceCommunities | No |
> | connections | Yes |
> | ddosCustomPolicies | Yes |
> | ddosProtectionPlans | Yes |
> | dnsOperationStatuses | No |
> | dnszones | 是 |
> | dnszones/A | No |
> | dnszones/AAAA | 否 |
> | dnszones / all | 否 |
> | dnszones/CAA | No |
> | dnszones/CNAME | No |
> | dnszones/MX | No |
> | dnszones/NS | No |
> | dnszones/PTR | No |
> | dnszones / recordsets | 否 |
> | dnszones/SOA | No |
> | dnszones/SRV | No |
> | dnszones/TXT | No |
> | expressRouteCircuits | Yes |
> | expressRouteCrossConnections | Yes |
> | expressRouteGateways | Yes |
> | expressRoutePorts | Yes |
> | expressRouteServiceProviders | No |
> | firewallPolicies | Yes |
> | frontdoors | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | No |
> | frontdoorWebApplicationFirewallPolicies | Yes |
> | getDnsResourceReference | No |
> | internalNotify | No |
> | loadBalancers | Yes |
> | localNetworkGateways | Yes |
> | natGateways | Yes |
> | networkIntentPolicies | Yes |
> | networkInterfaces | Yes |
> | networkProfiles | Yes |
> | networkSecurityGroups | Yes |
> | networkWatchers | Yes |
> | networkWatchers / connectionMonitors | Yes |
> | networkWatchers / flowLogs | Yes |
> | networkWatchers/鏡頭 | Yes |
> | networkWatchers / pingMeshes | Yes |
> | p2sVpnGateways | Yes |
> | privateDnsOperationStatuses | No |
> | privateDnsZones | Yes |
> | privateDnsZones/A | No |
> | privateDnsZones/AAAA | No |
> | privateDnsZones/全部 | No |
> | privateDnsZones/CNAME | No |
> | privateDnsZones/MX | No |
> | privateDnsZones/PTR | No |
> | privateDnsZones/SOA | No |
> | privateDnsZones/SRV | No |
> | privateDnsZones/TXT | No |
> | privateDnsZones / virtualNetworkLinks | Yes |
> | privateEndpoints | Yes |
> | privateLinkServices | Yes |
> | publicIPAddresses | Yes |
> | publicIPPrefixes | Yes |
> | routeFilters | Yes |
> | routeTables | Yes |
> | serviceEndpointPolicies | Yes |
> | trafficManagerGeographicHierarchies | 否 |
> | trafficmanagerprofiles | Yes |
> | trafficmanagerprofiles/熱度圖 | No |
> | trafficManagerUserMetricsKeys | No |
> | virtualHubs | Yes |
> | virtualNetworkGateways | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks/子網 | No |
> | virtualNetworkTaps | Yes |
> | virtualWans | Yes |
> | vpnGateways | Yes |
> | vpnSites | Yes |
> | webApplicationFirewallPolicies | Yes |

## <a name="microsoftnotebooks"></a>Microsoft 筆記本

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | NotebookProxies | No |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 命名空間 | Yes |
> | namespaces / notificationHubs | Yes |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | osNamespaces | Yes |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | HyperVSites | Yes |
> | ImportSites | Yes |
> | ServerSites | Yes |
> | VMwareSites | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | clusters | Yes |
> | deletedWorkspaces | No |
> | linkTargets | No |
> | storageInsightConfigs | No |
> | workspaces | Yes |
> | 工作區/dataExports | No |
> | 工作區/資料來源 | No |
> | 工作區/Linkedservices.json 和 datasets.json | No |
> | 工作區/linkedStorageAccounts | No |
> | workspaces / metadata | No |
> | workspaces / query | No |
> | 工作區/scopedPrivateLinkProxies | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | managementassociations | 否 |
> | managementconfigurations | Yes |
> | solutions | Yes |
> | 檢視 | 是 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | legacyPeerings | No |
> | peerAsns | 否 |
> | peerings | 是 |
> | peeringServiceCountries | No |
> | peeringServiceProviders | No |
> | peeringServices | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | policyEvents | No |
> | policyMetadata | No |
> | policyStates | No |
> | policyTrackedResources | No |
> | remediations | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | consoles | No |
> | dashboards | Yes |
> | userSettings | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Yes |
> | tenants | Yes |
> | 租使用者/工作區 | No |
> | workspaceCollections | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | capacities | Yes |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | Yes |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations / defaultRollouts | No |
> | providerRegistrations / resourceTypeRegistrations | No |
> | rollouts | Yes |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 工作區 | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | backupProtectedItems | No |
> | vaults | Yes |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | OpenShiftClusters | Yes |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 命名空間 | Yes |
> | namespaces / authorizationrules | No |
> | namespaces / hybridconnections | No |
> | namespaces / hybridconnections / authorizationrules | No |
> | 命名空間/privateEndpointConnections | No |
> | namespaces / wcfrelays | No |
> | namespaces / wcfrelays / authorizationrules | 否 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 查詢 | 是 |
> | resourceChangeDetails | No |
> | resourceChanges | No |
> | resources | No |
> | resourcesHistory | No |
> | subscriptionsStatus | No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | availabilityStatuses | No |
> | childAvailabilityStatuses | No |
> | childResources | No |
> | emergingissues | No |
> | 活動 | No |
> | impactedResources | No |
> | 中繼資料 | No |
> | 通知 | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | calculateTemplateHash | No |
> | deployments | No |
> | deployments / operations | No |
> | deploymentScripts | Yes |
> | deploymentScripts/記錄 | No |
> | 連結 | No |
> | notifyResourceJobs | No |
> | 提供者 | No |
> | resourceGroups | No |
> | subscriptions | No |
> | templateSpecs | Yes |
> | templateSpecs/版本 | Yes |
> | tenants | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 應用程式所需 | Yes |
> | saasresources | No |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | resourceHealthMetadata | No |
> | searchServices | Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | No |
> | advancedThreatProtectionSettings | No |
> | alerts | No |
> | alertsSuppressionRules | No |
> | allowedConnections | No |
> | applicationWhitelistings | No |
> | assessmentMetadata | No |
> | assessments | 否 |
> | autoDismissAlertsRules | 否 |
> | automations | 是 |
> | AutoProvisioningSettings | No |
> | Compliances | No |
> | dataCollectionAgents | No |
> | deviceSecurityGroups | No |
> | discoveredSecuritySolutions | No |
> | externalSecuritySolutions | No |
> | InformationProtectionPolicies | No |
> | iotSecuritySolutions | Yes |
> | iotSecuritySolutions / analyticsModels | No |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | No |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | No |
> | iotSecuritySolutions / iotAlerts | No |
> | iotSecuritySolutions / iotAlertTypes | No |
> | jitNetworkAccessPolicies | No |
> | 原則 | No |
> | pricings | No |
> | regulatoryComplianceStandards | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | No |
> | secureScoreControlDefinitions | No |
> | secureScoreControls | No |
> | secureScores | No |
> | secureScores / secureScoreControls | No |
> | securityContacts | No |
> | securitySolutions | No |
> | securitySolutionsReferenceData | No |
> | securityStatuses | No |
> | securityStatusesSummaries | No |
> | serverVulnerabilityAssessments | No |
> | settings | No |
> | subAssessments | No |
> | 工作 | No |
> | topologies | No |
> | workspaceSettings | No |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | aggregations | No |
> | alertRules | No |
> | alertRuleTemplates | No |
> | automationRules | No |
> | bookmarks | No |
> | 案例 | No |
> | dataConnectors | No |
> | dataConnectorsCheckRequirements | No |
> | 實體 | No |
> | entityQueries | No |
> | incidents | 否 |
> | officeConsents | No |
> | settings | No |
> | threatIntelligence | 否 |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | consoleServices | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 命名空間 | Yes |
> | namespaces / authorizationrules | No |
> | namespaces / disasterrecoveryconfigs | No |
> | namespaces / eventgridfilters | No |
> | namespaces / networkrulesets | No |
> | namespaces / queues | No |
> | namespaces / queues / authorizationrules | No |
> | namespaces / topics | No |
> | namespaces / topics / authorizationrules | No |
> | namespaces / topics / subscriptions | No |
> | namespaces / topics / subscriptions / rules | 否 |
> | premiumMessagingRegions | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 應用程式所需 | Yes |
> | clusters | Yes |
> | clusters / applications | No |
> | containerGroups | Yes |
> | containerGroupSets | Yes |
> | edgeclusters | Yes |
> | edgeclusters/應用程式 | No |
> | managedclusters | Yes |
> | managedclusters/nodetypes | No |
> | networks | Yes |
> | secretstores | Yes |
> | secretstores/憑證 | No |
> | secretstores/秘密 | No |
> | 磁碟區 | 是 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 應用程式所需 | Yes |
> | containerGroups | Yes |
> | gateways | Yes |
> | networks | Yes |
> | 密碼 | Yes |
> | 磁碟區 | 是 |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations / resourceTypeRegistrations | No |
> | rollouts | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | SignalR | Yes |
> | SignalR/eventGridFilters | No |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | hybridUseBenefits | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | applicationDefinitions | Yes |
> | 應用程式所需 | Yes |
> | jitRequests | Yes |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | managedInstances | Yes |
> | managedInstances/資料庫 | Yes |
> | managedInstances/資料庫/backupShortTermRetentionPolicies | No |
> | managedInstances/資料庫/架構/資料表/資料行/sensitivityLabels | No |
> | managedInstances/資料庫/vulnerabilityAssessments | No |
> | managedInstances/資料庫/vulnerabilityAssessments/規則/基準 | No |
> | managedInstances/encryptionProtector | No |
> | managedInstances/金鑰 | No |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | No |
> | managedInstances/vulnerabilityAssessments | No |
> | servers | Yes |
> | servers / administrators | No |
> | 伺服器/communicationLinks | No |
> | servers / databases | 是 |
> | 伺服器/encryptionProtector | No |
> | servers / firewallRules | No |
> | servers / keys | No |
> | 伺服器/restorableDroppedDatabases | No |
> | servers / serviceobjectives | 否 |
> | 伺服器/tdeCertificates | No |
> | virtualClusters | No |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Yes |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | No |
> | SqlVirtualMachines | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | storageAccounts | Yes |
> | storageAccounts / blobServices | No |
> | storageAccounts / fileServices | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/服務 | No |
> | storageAccounts/services/Metricdefinitions.listasync | No |
> | storageAccounts/tableServices | No |
> | usages | No |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | caches | Yes |
> | 快取/storageTargets | No |
> | usageModels | No |

## <a name="microsoftstoragereplication"></a>StorageReplication

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | replicationGroups | 否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices / registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices/工作流程 | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices / registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices/工作流程 | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices / registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices/工作流程 | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | managers | Yes |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | clusters | Yes |
> | streamingjobs | Yes |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | cancel | No |
> | CreateSubscription | No |
> | enable | No |
> | 重新命名 | No |
> | SubscriptionDefinitions | No |
> | SubscriptionOperations | No |
> | subscriptions | No |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | privateLinkHubs | Yes |
> | workspaces | Yes |
> | 工作區/bigDataPools | Yes |
> | 工作區/operationStatuses | No |
> | 工作區/sqlPools | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | environments | Yes |
> | environments / accessPolicies | No |
> | environments / eventsources | Yes |
> | environments / referenceDataSets | Yes |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | stores | 是 |
> | 存放區/accessPolicies | No |
> | stores / services | 否 |
> | stores / services / tokens | 否 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | imageTemplates | Yes |
> | imageTemplates / runOutputs | No |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | ArcZones | Yes |
> | ResourcePools | Yes |
> | VCenters | Yes |
> | VirtualMachines | Yes |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Yes |
> | dedicatedCloudServices | Yes |
> | virtualMachines | Yes |

## <a name="microsoftvmwareonazure"></a>VMwareOnAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | privateClouds | Yes |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | devices | Yes |
> | registeredSubscriptions | No |
> | 廠商 | No |
> | 廠商/sku | No |
> | 廠商/vnfs | No |
> | virtualNetworkFunctionSkus | No |
> | vnfs | Yes |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | Yes |
> | plans | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | apiManagementAccounts | No |
> | apiManagementAccounts/Apiacl | No |
> | apiManagementAccounts/api | No |
> | apiManagementAccounts/api/Apiacl | No |
> | apiManagementAccounts/api/Connectionacl | No |
> | apiManagementAccounts/api/連線 | No |
> | apiManagementAccounts/api/connections/Connectionacl | No |
> | apiManagementAccounts/api/localizedDefinitions | No |
> | apiManagementAccounts/Connectionacl | No |
> | apiManagementAccounts/connections | No |
> | billingMeters | 否 |
> | certificates | Yes |
> | connectionGateways | 是 |
> | connections | 是 |
> | customApis | Yes |
> | deletedSites | No |
> | hostingEnvironments | Yes |
> | hostingEnvironments/eventGridFilters | No |
> | hostingEnvironments/multiRolePools | No |
> | hostingEnvironments/workerPools | No |
> | kubeEnvironments | Yes |
> | publishingUsers | No |
> | Mahout | No |
> | resourceHealthMetadata | No |
> | runtimes | No |
> | serverFarms | Yes |
> | serverFarms/eventGridFilters | No |
> | sites | Yes |
> | sites/config  | No |
> | sites/eventGridFilters | No |
> | sites/hostNameBindings | No |
> | sites/Networkconfig.netcfg | No |
> | sites / premieraddons | 是 |
> | sites / slots | 是 |
> | sites/位置/eventGridFilters | No |
> | sites/位置/hostNameBindings | No |
> | sites/位置/Networkconfig.netcfg | No |
> | sourceControls | No |
> | staticSites | Yes |
> | validate | No |
> | verifyHostingEnvironmentVnet | No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | multipleActivationKeys | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | DeviceServices | Yes |

## <a name="microsoftworkloadbuilder"></a>WorkloadBuilder

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 工作負載 | Yes |
> | 工作負載/實例 | No |
> | 工作負載/版本 | No |
> | 工作負載/版本/構件 | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | components | No |
> | componentsSummary | No |
> | monitorInstances | No |
> | monitorInstancesSummary | 否 |
> | monitors | 否 |
> | notificationSettings | 否 |

## <a name="next-steps"></a>後續步驟

若要以逗點分隔值檔案的形式取得相同資料，請下載 [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)。
