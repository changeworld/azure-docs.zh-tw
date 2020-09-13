---
title: 資源的標記支援
description: 顯示哪些 Azure 資源類型支援標記。 提供所有 Azure 服務的詳細資料。
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: ea46e263d934c327cf6dfa343a5e0b5a170f545e
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2020
ms.locfileid: "89513909"
---
# <a name="tag-support-for-azure-resources"></a>Azure 資源的標記支援
本文將說明資源類型是否支援[標記](tag-resources.md)。 標示為 [ **支援** 標籤] 的資料行會指出資源類型是否有標記的屬性。 在 [ **成本] 報表中** 標示為 [標籤] 的資料行會指出該資源類型是否要將標記傳遞給成本報告。 您可以依 [成本管理成本分析](../../cost-management-billing/costs/group-filter.md) 和 [Azure 計費發票及每日使用量資料](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)中的標記來查看成本。

若要以逗號區隔值檔案的形式取得相同資料，請下載 [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)。

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
> - [Automanage](#microsoftautomanage)
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
> - [Codespaces](#microsoftcodespaces)
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
> - [DeviceUpdate](#microsoftdeviceupdate)
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
> - [Microsoft Insights](#microsoftinsights)
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
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | DomainServices | 是 | 是 |
> | DomainServices/oucontainer | 否 | 否 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | supportProviders | 否 | 否 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
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
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | advisorScore | 否 | 否 |
> | 組態 | 否 | 否 |
> | generateRecommendations | 否 | 否 |
> | 中繼資料 | 否 | 否 |
> | Mahout | 否 | 否 |
> | suppressions | 否 | 否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | actionRules | 是 | 是 |
> | alerts | 否 | 否 |
> | alertsList | 否 | 否 |
> | alertsMetaData | 否 | 否 |
> | alertsSummary | 否 | 否 |
> | alertsSummaryList | 否 | 否 |
> | smartDetectorAlertRules | 是 | 是 |
> | smartGroups | 否 | 否 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | servers | 是 | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | reportFeedback | 否 | 否 |
> | 服務 | 是 | 是 |
> | validateServiceName | 否 | 否 |

> [!NOTE]
> Azure API 管理只支援為每個服務建立最多15個標記名稱/值組。

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | configurationStores | 是 | 是 |
> | configurationStores / eventGridFilters | 否 | 否 |
> | configurationStores/keyValues | 否 | 否 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | Spring | 是 | 是 |
> | 春季/應用程式 | 否 | 否 |
> | 春季/apps/部署 | 否 | 否 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | attestationProviders | 是 | 是 |
> | defaultProviders | 否 | 否 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | accessReviewScheduleDefinitions | 否 | 否 |
> | accessReviewScheduleSettings | 否 | 否 |
> | classicAdministrators | 否 | 否 |
> | dataAliases | 否 | 否 |
> | denyAssignments | 否 | 否 |
> | elevateAccess | 否 | 否 |
> | findOrphanRoleAssignments | 否 | 否 |
> | locks | 否 | 否 |
> | 權限 | 否 | 否 |
> | policyAssignments | 否 | 否 |
> | policyDefinitions | 否 | 否 |
> | policyExemptions | 否 | 否 |
> | policySetDefinitions | 否 | 否 |
> | privateLinkAssociations | 否 | 否 |
> | providerOperations | 否 | 否 |
> | resourceManagementPrivateLinks | 是 | 是 |
> | roleAssignments | 否 | 否 |
> | roleAssignmentsUsageMetrics | 否 | 否 |
> | roleDefinitions | 否 | 否 |

## <a name="microsoftautomanage"></a>Automanage

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | configurationProfileAssignments | 否 | 否 |
> | configurationProfilePreferences | 是 | 是 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | automationAccounts | 是 | 是 |
> | automationAccounts/設定 | 是 | 是 |
> | automationAccounts/作業 | 否 | 否 |
> | automationAccounts/privateEndpointConnectionProxies | 否 | 否 |
> | automationAccounts/privateEndpointConnections | 否 | 否 |
> | automationAccounts/privateLinkResources | 否 | 否 |
> | automationAccounts / runbooks | 是 | 是 |
> | automationAccounts/>softwareupdateconfigurations | 否 | 否 |
> | automationAccounts / webhooks | 否 | 否 |

> [!NOTE]
> Azure 自動化只支援為每個自動化資源建立最多15個標記名稱/值組。

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | privateClouds | 是 | 是 |
> | privateClouds/授權 | 否 | 否 |
> | privateClouds/叢集 | 否 | 否 |
> | privateClouds / globalReachConnections | 否 | 否 |
> | privateClouds / hcxEnterpriseSites | 否 | 否 |
> | privateClouds / workloadNetworks | 否 | 否 |
> | privateClouds / workloadNetworks / dhcpConfigurations | 否 | 否 |
> | privateClouds/workloadNetworks/閘道 | 否 | 否 |
> | privateClouds / workloadNetworks / portMirroringProfiles | 否 | 否 |
> | privateClouds/workloadNetworks/區段 | 否 | 否 |
> | privateClouds/workloadNetworks/virtualMachines | 否 | 否 |
> | privateClouds / workloadNetworks / vmGroups | 否 | 否 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | environments | 否 | 否 |
> | 環境/帳戶 | 否 | 否 |
> | 環境/帳戶/命名空間 | 否 | 否 |
> | 環境/帳戶/命名空間/設定 | 否 | 否 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | 是 | 否 |
> | b2ctenants | 否 | 否 |
> | guestUsages | 是 | 是 |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | dataControllers | 是 | 是 |
> | postgresInstances | 是 | 是 |
> | sqlManagedInstances | 是 | 是 |
> | sqlServerInstances | 是 | 是 |
> | sqlServerRegistrations | 是 | 是 |
> | sqlServerRegistrations/sqlServers | 否 | 否 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | 否 | 否 |
> | edgeSubscriptions | 是 | 是 |
> | registrations | 是 | 是 |
> | 註冊/customerSubscriptions | 否 | 否 |
> | registrations / products | 否 | 否 |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | batchAccounts | 是 | 是 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | billingAccounts | 否 | 否 |
> | billingAccounts/協定 | 否 | 否 |
> | billingAccounts/billingPermissions | 否 | 否 |
> | billingAccounts/billingProfiles | 否 | 否 |
> | billingAccounts/billingProfiles/billingPermissions | 否 | 否 |
> | billingAccounts/billingProfiles/billingRoleAssignments | 否 | 否 |
> | billingAccounts/billingProfiles/billingRoleDefinitions | 否 | 否 |
> | billingAccounts/billingProfiles/billingSubscriptions | 否 | 否 |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | 否 | 否 |
> | billingAccounts/billingProfiles/客戶 | 否 | 否 |
> | billingAccounts/billingProfiles/指示 | 否 | 否 |
> | billingAccounts/billingProfiles/發票 | 否 | 否 |
> | billingAccounts/billingProfiles/發票/pricesheet | 否 | 否 |
> | billingAccounts/billingProfiles/發票/交易 | 否 | 否 |
> | billingAccounts/billingProfiles/invoiceSections | 否 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | 否 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | 否 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | 否 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | 否 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | 否 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | 否 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/products | 否 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | 否 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | 否 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/交易 | 否 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/transfer | 否 | 否 |
> | billingAccounts/BillingProfiles/patchOperations | 否 | 否 |
> | billingAccounts/billingProfiles/paymentMethods | 否 | 否 |
> | billingAccounts/billingProfiles/原則 | 否 | 否 |
> | billingAccounts/billingProfiles/pricesheet | 否 | 否 |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | 否 | 否 |
> | billingAccounts/billingProfiles/products | 否 | 否 |
> | billingAccounts/billingProfiles/交易 | 否 | 否 |
> | billingAccounts/billingProfiles/validateDetachPaymentMethodEligibility | 否 | 否 |
> | billingAccounts/billingRoleAssignments | 否 | 否 |
> | billingAccounts/billingRoleDefinitions | 否 | 否 |
> | billingAccounts/billingSubscriptions | 否 | 否 |
> | billingAccounts/billingSubscriptions/發票 | 否 | 否 |
> | billingAccounts/createBillingRoleAssignment | 否 | 否 |
> | billingAccounts/createInvoiceSectionOperations | 否 | 否 |
> | billingAccounts/客戶 | 否 | 否 |
> | billingAccounts/customers/billingPermissions | 否 | 否 |
> | billingAccounts/customers/billingSubscriptions | 否 | 否 |
> | billingAccounts/customers/initiateTransfer | 否 | 否 |
> | billingAccounts/customers/原則 | 否 | 否 |
> | billingAccounts/customers/products | 否 | 否 |
> | billingAccounts/customers/交易 | 否 | 否 |
> | billingAccounts/客戶/轉移 | 否 | 否 |
> | billingAccounts/部門 | 否 | 否 |
> | billingAccounts/部門/billingPermissions | 否 | 否 |
> | billingAccounts/部門/billingRoleAssignments | 否 | 否 |
> | billingAccounts/部門/billingRoleDefinitions | 否 | 否 |
> | billingAccounts/enrollmentAccounts | 否 | 否 |
> | billingAccounts/enrollmentAccounts/billingPermissions | 否 | 否 |
> | billingAccounts/enrollmentAccounts/billingRoleAssignments | 否 | 否 |
> | billingAccounts/enrollmentAccounts/billingRoleDefinitions | 否 | 否 |
> | billingAccounts/發票 | 否 | 否 |
> | billingAccounts/發票/交易 | 否 | 否 |
> | billingAccounts/invoiceSections | 否 | 否 |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | 否 | 否 |
> | billingAccounts/invoiceSections/billingSubscriptions | 否 | 否 |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | 否 | 否 |
> | billingAccounts/invoiceSections/提升 | 否 | 否 |
> | billingAccounts/invoiceSections/initiateTransfer | 否 | 否 |
> | billingAccounts/invoiceSections/patchOperations | 否 | 否 |
> | billingAccounts/invoiceSections/productMoveOperations | 否 | 否 |
> | billingAccounts/invoiceSections/products | 否 | 否 |
> | billingAccounts/invoiceSections/products/transfer | 否 | 否 |
> | billingAccounts/invoiceSections/products/updateAutoRenew | 否 | 否 |
> | billingAccounts/invoiceSections/交易 | 否 | 否 |
> | billingAccounts/invoiceSections/傳輸 | 否 | 否 |
> | billingAccounts/lineOfCredit | 否 | 否 |
> | billingAccounts/patchOperations | 否 | 否 |
> | billingAccounts/paymentMethods | 否 | 否 |
> | billingAccounts/產品 | 否 | 否 |
> | billingAccounts/交易 | 否 | 否 |
> | billingPeriods | 否 | 否 |
> | billingPermissions | 否 | 否 |
> | billingProperty | 否 | 否 |
> | billingRoleAssignments | 否 | 否 |
> | billingRoleDefinitions | 否 | 否 |
> | createBillingRoleAssignment | 否 | 否 |
> | departments | 否 | 否 |
> | enrollmentAccounts | 否 | 否 |
> | invoices | 否 | 否 |
> | transfers | 否 | 否 |
> | 傳輸/acceptTransfer | 否 | 否 |
> | 傳輸/declineTransfer | 否 | 否 |
> | 傳輸/>operationstatus | 否 | 否 |
> | 傳輸/validateTransfer | 否 | 否 |
> | validateAddress | 否 | 否 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | mapApis | 是 | 是 |
> | updateCommunicationPreference | 否 | 否 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | 是 | 是 |
> | cordaMembers | 是 | 是 |
> | watchers | 是 | 是 |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | TokenServices | 是 | 是 |
> | TokenServices / BlockchainNetworks | 否 | 否 |
> | TokenServices/群組 | 否 | 否 |
> | TokenServices/群組/帳戶 | 否 | 否 |
> | TokenServices / TokenTemplates | 否 | 否 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | 否 | 否 |
> | blueprintAssignments / assignmentOperations | 否 | 否 |
> | blueprintAssignments/作業 | 否 | 否 |
> | blueprints | 否 | 否 |
> | blueprints / artifacts | 否 | 否 |
> | blueprints / versions | 否 | 否 |
> | blueprints / versions / artifacts | 否 | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | botServices | 是 | 是 |
> | botServices / channels | 否 | 否 |
> | >botservices.cs/連接 | 否 | 否 |
> | 語言 | 否 | 否 |
> | 範本 | 否 | 否 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | Redis | 是 | 是 |
> | Redis/EventGridFilters | 否 | 否 |
> | Redis/privateEndpointConnectionProxies | 否 | 否 |
> | Redis/privateEndpointConnectionProxies/validate | 否 | 否 |
> | Redis/privateEndpointConnections | 否 | 否 |
> | Redis/privateLinkResources | 否 | 否 |
> | redisEnterprise | 是 | 是 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | appliedReservations | 否 | 否 |
> | autoQuotaIncrease | 否 | 否 |
> | calculateExchange | 否 | 否 |
> | calculatePrice | 否 | 否 |
> | calculatePurchasePrice | 否 | 否 |
> | catalogs | 否 | 否 |
> | commercialReservationOrders | 否 | 否 |
> | 兌換 | 否 | 否 |
> | placePurchaseOrder | 否 | 否 |
> | reservationOrders | 否 | 否 |
> | reservationOrders / calculateRefund | 否 | 否 |
> | reservationOrders/merge | 否 | 否 |
> | reservationOrders/保留 | 否 | 否 |
> | reservationOrders/保留/修訂 | 否 | 否 |
> | reservationOrders/return | 否 | 否 |
> | reservationOrders/分割 | 否 | 否 |
> | reservationOrders/交換 | 否 | 否 |
> | reservations | 否 | 否 |
> | resourceProviders | 否 | 否 |
> | resources | 否 | 否 |
> | validateReservationOrder | 否 | 否 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 否 | 否 |
> | CdnWebApplicationFirewallPolicies | 是 | 是 |
> | edgenodes | 否 | 否 |
> | 設定檔 | 是 | 是 |
> | profiles / endpoints | 是 | 是 |
> | profiles / endpoints / customdomains | 否 | 否 |
> | profiles / endpoints / origingroups | 否 | 否 |
> | profiles / endpoints / origins | 否 | 否 |
> | validateProbe | 否 | 否 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | certificateOrders | 是 | 是 |
> | certificateOrders/憑證 | 否 | 否 |
> | validateCertificateRegistrationInformation | 否 | 否 |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | profile | 否 | 否 |
> | resourceChanges | 否 | 否 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | capabilities | 否 | 否 |
> | domainNames | 否 | 否 |
> | domainNames/功能 | 否 | 否 |
> | domainNames/internalLoadBalancers | 否 | 否 |
> | domainNames/serviceCertificates | 否 | 否 |
> | domainNames/插槽 | 否 | 否 |
> | domainNames/插槽/角色 | 否 | 否 |
> | domainNames/插槽/角色/metricDefinitions | 否 | 否 |
> | domainNames/插槽/角色/計量 | 否 | 否 |
> | moveSubscriptionResources | 否 | 否 |
> | operatingSystemFamilies | 否 | 否 |
> | operatingSystems | 否 | 否 |
> | quotas | 否 | 否 |
> | resourceTypes | 否 | 否 |
> | validateSubscriptionMoveAvailability | 否 | 否 |
> | virtualMachines | 否 | 否 |
> | virtualMachines/diagnosticSettings | 否 | 否 |
> | virtualMachines/metricDefinitions | 否 | 否 |
> | virtualMachines/計量 | 否 | 否 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | 否 | 否 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | capabilities | 否 | 否 |
> | expressRouteCrossConnections | 否 | 否 |
> | expressRouteCrossConnections/對等互連 | 否 | 否 |
> | gatewaySupportedDevices | 否 | 否 |
> | networkSecurityGroups | 否 | 否 |
> | quotas | 否 | 否 |
> | reservedIps | 否 | 否 |
> | virtualNetworks | 否 | 否 |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | 否 | 否 |
> | virtualNetworks / virtualNetworkPeerings | 否 | 否 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | capabilities | 否 | 否 |
> | disks | 否 | 否 |
> | images | 否 | 否 |
> | osImages | 否 | 否 |
> | osPlatformImages | 否 | 否 |
> | publicImages | 否 | 否 |
> | quotas | 否 | 否 |
> | storageAccounts | 否 | 否 |
> | storageAccounts / blobServices | 否 | 否 |
> | storageAccounts / fileServices | 否 | 否 |
> | storageAccounts/metricDefinitions | 否 | 否 |
> | storageAccounts/計量 | 否 | 否 |
> | storageAccounts/queueServices | 否 | 否 |
> | storageAccounts/服務 | 否 | 否 |
> | storageAccounts/services/diagnosticSettings | 否 | 否 |
> | storageAccounts/services/metricDefinitions | 否 | 否 |
> | storageAccounts/服務/計量 | 否 | 否 |
> | storageAccounts/tableServices | 否 | 否 |
> | storageAccounts/vmImages | 否 | 否 |
> | vmImages | 否 | 否 |

## <a name="microsoftcodespaces"></a>Codespaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | plans | 是 | 是 |
> | registeredSubscriptions | 否 | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | 帳戶/privateEndpointConnectionProxies | 否 | 否 |
> | 帳戶/privateEndpointConnections | 否 | 否 |
> | 帳戶/privateLinkResources | 否 | 否 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | RateCard | 否 | 否 |
> | UsageAggregates | 否 | 否 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | availabilitySets | 是 | 是 |
> | cloudServices | 是 | 是 |
> | diskAccesses | 是 | 是 |
> | diskEncryptionSets | 是 | 是 |
> | disks | 是 | 是 |
> | galleries | 是 | 是 |
> | galleries / applications | 否 | 否 |
> | 資源庫/應用程式/版本 | 否 | 否 |
> | galleries / images | 否 | 否 |
> | galleries / images / versions | 否 | 否 |
> | hostGroups | 是 | 是 |
> | hostGroups/主機 | 是 | 是 |
> | images | 是 | 是 |
> | proximityPlacementGroups | 是 | 是 |
> | restorePointCollections | 是 | 是 |
> | restorePointCollections / restorePoints | 否 | 否 |
> | sharedVMExtensions | 是 | 是 |
> | sharedVMExtensions/版本 | 否 | 否 |
> | sharedVMImages | 是 | 是 |
> | sharedVMImages/版本 | 否 | 否 |
> | snapshots | 是 | 是 |
> | sshPublicKeys | 是 | 是 |
> | virtualMachines | 是 | 是 |
> | virtualMachines/擴充功能 | 是 | 是 |
> | virtualMachines/metricDefinitions | 否 | 否 |
> | virtualMachines/runCommands | 是 | 是 |
> | virtualMachineScaleSets | 是 | 是 |
> | virtualMachineScaleSets/擴充功能 | 否 | 否 |
> | virtualMachineScaleSets/networkInterfaces | 否 | 否 |
> | virtualMachineScaleSets/publicIPAddresses | 否 | 否 |
> | virtualMachineScaleSets/virtualMachines | 否 | 否 |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | 否 | 否 |

> [!NOTE]
> 您無法將標記新增至已標示為一般化的虛擬機器。 您可以使用 [new-azvm-一般化](/powershell/module/Az.Compute/Set-AzVM) 或 [az vm 一般化](/cli/azure/vm#az-vm-generalize)將虛擬機器標示為一般化。

## <a name="microsoftconnectedcache"></a>ConnectedCache

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | CacheNodes | 是 | 是 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
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
> | ReservationRecommendationDetails | 否 | 否 |
> | ReservationRecommendations | 否 | 否 |
> | ReservationSummaries | 否 | 否 |
> | ReservationTransactions | 否 | 否 |
> | 標籤 | 否 | 否 |
> | tenants | 否 | 否 |
> | 詞彙 | 否 | 否 |
> | UsageDetails | 否 | 否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | containerGroups | 是 | 是 |
> | serviceAssociationLinks | 否 | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | registries | 是 | 是 |
> | 登錄/agentPools | 是 | 是 |
> | registries / builds | 否 | 否 |
> | registries / builds / cancel | 否 | 否 |
> | 登錄/組建/getLogLink | 否 | 否 |
> | registries / buildTasks | 是 | 是 |
> | 登錄/buildTasks/步驟 | 否 | 否 |
> | 登錄/eventGridFilters | 否 | 否 |
> | 登錄/exportPipelines | 否 | 否 |
> | 登錄/generateCredentials | 否 | 否 |
> | 登錄/getBuildSourceUploadUrl | 否 | 否 |
> | 登錄/GetCredentials | 否 | 否 |
> | 登錄/importImage | 否 | 否 |
> | 登錄/importPipelines | 否 | 否 |
> | 登錄/pipelineRuns | 否 | 否 |
> | 登錄/privateEndpointConnectionProxies | 否 | 否 |
> | 登錄/privateEndpointConnectionProxies/驗證 | 否 | 否 |
> | 登錄/privateEndpointConnections | 否 | 否 |
> | 登錄/privateLinkResources | 否 | 否 |
> | 登錄/queueBuild | 否 | 否 |
> | 登錄/regenerateCredential | 否 | 否 |
> | 登錄/regenerateCredentials | 否 | 否 |
> | registries / replications | 是 | 是 |
> | registries / runs | 否 | 否 |
> | registries / runs / cancel | 否 | 否 |
> | 登錄/scheduleRun | 否 | 否 |
> | registries / scopeMaps | 否 | 否 |
> | 登錄/taskRuns | 否 | 否 |
> | registries / tasks | 是 | 是 |
> | registries / tokens | 否 | 否 |
> | 登錄/updatePolicies | 否 | 否 |
> | registries / webhooks | 是 | 是 |
> | 登錄/webhook/getCallbackConfig | 否 | 否 |
> | registries / webhooks / ping | 否 | 否 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | containerServices | 是 | 是 |
> | managedClusters | 是 | 是 |
> | openShiftManagedClusters | 是 | 是 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | 警示 | 否 | 否 |
> | BillingAccounts | 否 | 否 |
> | 預算 | 否 | 否 |
> | CloudConnectors | 否 | 否 |
> | 連接器 | 是 | 是 |
> | costAllocationRules | 否 | 否 |
> | 部門 | 否 | 否 |
> | 維度 | 否 | 否 |
> | EnrollmentAccounts | 否 | 否 |
> | 出口 | 否 | 否 |
> | ExternalBillingAccounts | 否 | 否 |
> | ExternalBillingAccounts/警示 | 否 | 否 |
> | ExternalBillingAccounts/維度 | 否 | 否 |
> | ExternalBillingAccounts/預測 | 否 | 否 |
> | ExternalBillingAccounts/查詢 | 否 | 否 |
> | ExternalSubscriptions | 否 | 否 |
> | ExternalSubscriptions/警示 | 否 | 否 |
> | ExternalSubscriptions/維度 | 否 | 否 |
> | ExternalSubscriptions/預測 | 否 | 否 |
> | ExternalSubscriptions/查詢 | 否 | 否 |
> | 趨勢預測 | 否 | 否 |
> | 深入解析 | 否 | 否 |
> | 查詢 | 否 | 否 |
> | 註冊 | 否 | 否 |
> | Reportconfigs | 否 | 否 |
> | 報表 | 否 | 否 |
> | 設定 | 否 | 否 |
> | showbackRules | 否 | 否 |
> | 檢視 | 否 | 否 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | requests | 否 | 否 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | associations | 否 | 否 |
> | resourceProviders | 是 | 是 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | jobs | 是 | 是 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | 是 | 是 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | workspaces | 是 | 是 |
> | 工作區/dbWorkspaces | 否 | 否 |
> | 工作區/storageEncryption | 否 | 否 |
> | 工作區/virtualNetworkPeerings | 否 | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | catalogs | 是 | 是 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | dataFactories | 是 | 是 |
> | dataFactories/diagnosticSettings | 否 | 否 |
> | dataFactories/metricDefinitions | 否 | 否 |
> | dataFactorySchema | 否 | 否 |
> | factories | 是 | 是 |
> | factories / integrationRuntimes | 否 | 否 |

> [!NOTE]
> 如果您的資料處理站中有 Azure SSIS 整合執行時間，其執行成本將會以 data factory 標記標記。  您必須停止並重新啟動執行中的 Azure SSIS 整合執行時間，才能將新的 data factory 標記套用至其執行成本。

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | accounts / dataLakeStoreAccounts | 否 | 否 |
> | accounts / storageAccounts | 否 | 否 |
> | 帳戶/storageAccounts/容器 | 否 | 否 |
> | 帳戶/transferAnalyticsUnits | 否 | 否 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | 帳戶/eventGridFilters | 否 | 否 |
> | accounts / firewallRules | 否 | 否 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | 服務 | 否 | 否 |
> | services / projects | 否 | 否 |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | BackupVaults | 是 | 是 |
> | ResourceOperationGateKeepers | 是 | 是 |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | accounts / shares | 否 | 否 |
> | accounts / shares / datasets | 否 | 否 |
> | accounts / shares / invitations | 否 | 否 |
> | accounts / shares / providersharesubscriptions | 否 | 否 |
> | 帳戶/共用/synchronizationSettings | 否 | 否 |
> | accounts / sharesubscriptions | 否 | 否 |
> | accounts/sharesubscriptions/consumerSourceDataSets | 否 | 否 |
> | accounts / sharesubscriptions / datasetmappings | 否 | 否 |
> | accounts / sharesubscriptions / triggers | 否 | 否 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | servers | 是 | 是 |
> | servers / advisors | 否 | 否 |
> | servers / keys | 否 | 否 |
> | servers/privateEndpointConnectionProxies | 否 | 否 |
> | servers/privateEndpointConnections | 否 | 否 |
> | servers/privateLinkResources | 否 | 否 |
> | servers/queryTexts | 否 | 否 |
> | servers/recoverableServers | 否 | 否 |
> | servers/topQueryStatistics | 否 | 否 |
> | servers / virtualNetworkRules | 否 | 否 |
> | servers/waitStatistics | 否 | 否 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | servers | 是 | 是 |
> | servers / advisors | 否 | 否 |
> | servers / keys | 否 | 否 |
> | servers/privateEndpointConnectionProxies | 否 | 否 |
> | servers/privateEndpointConnections | 否 | 否 |
> | servers/privateLinkResources | 否 | 否 |
> | servers/queryTexts | 否 | 否 |
> | servers/recoverableServers | 否 | 否 |
> | servers/topQueryStatistics | 否 | 否 |
> | servers / virtualNetworkRules | 否 | 否 |
> | servers/waitStatistics | 否 | 否 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | flexibleServers | 是 | 是 |
> | serverGroups | 是 | 是 |
> | servers | 是 | 是 |
> | servers / advisors | 否 | 否 |
> | servers / keys | 否 | 否 |
> | servers/privateEndpointConnectionProxies | 否 | 否 |
> | servers/privateEndpointConnections | 否 | 否 |
> | servers/privateLinkResources | 否 | 否 |
> | servers/queryTexts | 否 | 否 |
> | servers/recoverableServers | 否 | 否 |
> | servers/topQueryStatistics | 否 | 否 |
> | servers / virtualNetworkRules | 否 | 否 |
> | servers/waitStatistics | 否 | 否 |
> | serversv2 | 是 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | artifactSources | 是 | 是 |
> | rollouts | 是 | 是 |
> | serviceTopologies | 是 | 是 |
> | serviceTopologies/服務 | 是 | 是 |
> | serviceTopologies/services/serviceUnits | 是 | 是 |
> | steps | 是 | 是 |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | applicationgroups | 是 | 是 |
> | applicationgroups / applications | 否 | 否 |
> | applicationgroups / desktops | 否 | 否 |
> | applicationgroups / startmenuitems | 否 | 否 |
> | hostpools | 是 | 是 |
> | hostpools / sessionhosts | 否 | 否 |
> | hostpools / sessionhosts / usersessions | 否 | 否 |
> | hostpools / usersessions | 否 | 否 |
> | workspaces | 是 | 是 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | ElasticPools | 是 | 是 |
> | ElasticPools / IotHubTenants | 是 | 是 |
> | ElasticPools/IotHubTenants/securitySettings | 否 | 否 |
> | IotHubs | 是 | 是 |
> | IotHubs/eventGridFilters | 否 | 否 |
> | IotHubs/securitySettings | 否 | 否 |
> | ProvisioningServices | 是 | 是 |
> | usages | 否 | 否 |

## <a name="microsoftdeviceupdate"></a>DeviceUpdate

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | 帳戶/實例 | 是 | 是 |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | pipelines | 是 | 是 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | controllers | 是 | 是 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | labcenters | 是 | 是 |
> | labs | 是 | 是 |
> | labs / environments | 是 | 是 |
> | labs/serviceRunners | 是 | 是 |
> | labs/virtualMachines | 是 | 是 |
> | schedules | 是 | 是 |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | digitalTwinsInstances | 是 | 是 |
> | digitalTwinsInstances/端點 | 否 | 否 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | 否 | 否 |
> | databaseAccounts | 是 | 是 |
> | restorableDatabaseAccounts | 否 | 否 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | domains | 是 | 是 |
> | 網域/domainOwnershipIdentifiers | 否 | 否 |
> | generateSsoRequest | 否 | 否 |
> | topLevelDomains | 否 | 否 |
> | validateDomainRegistrationInformation | 否 | 否 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | lcsprojects | 否 | 否 |
> | lcsprojects / clouddeployments | 否 | 否 |
> | lcsprojects/連接器 | 否 | 否 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | 服務 | 是 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | domains | 是 | 是 |
> | domains / topics | 否 | 否 |
> | eventSubscriptions | 否 | 否 |
> | extensionTopics | 否 | 否 |
> | partnerNamespaces | 是 | 是 |
> | partnerNamespaces/eventChannels | 否 | 否 |
> | partnerRegistrations | 是 | 是 |
> | partnerTopics | 是 | 是 |
> | partnerTopics/eventSubscriptions | 否 | 否 |
> | systemTopics | 是 | 是 |
> | systemTopics/eventSubscriptions | 否 | 否 |
> | topics | 是 | 是 |
> | topicTypes | 否 | 否 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | 命名空間 | 是 | 是 |
> | namespaces / authorizationrules | 否 | 否 |
> | namespaces / disasterrecoveryconfigs | 否 | 否 |
> | namespaces / eventhubs | 否 | 否 |
> | namespaces / eventhubs / authorizationrules | 否 | 否 |
> | namespaces / eventhubs / consumergroups | 否 | 否 |
> | namespaces / networkrulesets | 否 | 否 |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | experimentWorkspaces | 是 | 是 |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | 命名空間 | 是 | 是 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | featureProviders | 否 | 否 |
> | 特性 | 否 | 否 |
> | 提供者 | 否 | 否 |
> | subscriptionFeatureRegistrations | 否 | 否 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | enroll | 否 | 否 |
> | galleryitems | 否 | 否 |
> | generateartifactaccessuri | 否 | 否 |
> | myareas | 否 | 否 |
> | myareas/區域 | 否 | 否 |
> | myareas/區域/區域 | 否 | 否 |
> | myareas/區域/區域/galleryitem | 否 | 否 |
> | myareas/areas/galleryitem | 否 | 否 |
> | myareas/galleryitem | 否 | 否 |
> | 註冊 | 否 | 否 |
> | resources | 否 | 否 |
> | retrieveresourcesbyid | 否 | 否 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | 是 | 是 |
> | autoManagedVmConfigurationProfiles | 是 | 是 |
> | configurationProfileAssignments | 否 | 否 |
> | guestConfigurationAssignments | 否 | 否 |
> | software | 否 | 否 |
> | softwareUpdateProfile | 否 | 否 |
> | softwareUpdates | 否 | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | hanaInstances | 是 | 是 |
> | sapMonitors | 是 | 是 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | 是 | 是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | clusters / applications | 否 | 否 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | 服務 | 是 | 是 |
> | 服務/iomtconnectors | 否 | 否 |
> | 服務/iomtconnectors/連接 | 否 | 否 |
> | 服務/iomtconnectors/對應 | 否 | 否 |
> | 服務/privateEndpointConnectionProxies | 否 | 否 |
> | 服務/privateEndpointConnections | 是 | 是 |
> | 服務/privateLinkResources | 是 | 是 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | machines | 是 | 是 |
> | machines / extensions | 是 | 是 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | dataManagers | 是 | 是 |

## <a name="microsofthybridnetwork"></a>HybridNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | devices | 是 | 是 |
> | registeredSubscriptions | 否 | 否 |
> | 廠商 | 否 | 否 |
> | 廠商/vendorskus | 否 | 否 |
> | 廠商/vendorskus/previewSubscriptions | 否 | 否 |
> | virtualnetworkfunctions | 是 | 是 |
> | virtualnetworkfunctionvendors | 否 | 否 |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | components | 是 | 是 |
> | networkScopes | 是 | 是 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | jobs | 是 | 是 |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | actionGroups | 是 | 是 |
> | activityLogAlerts | 是 | 是 |
> | alertrules | 是 | 是 |
> | autoscalesettings | 是 | 是 |
> | components | 是 | 是 |
> | 元件/linkedStorageAccounts | 否 | 否 |
> | 元件/ProactiveDetectionConfigs | 否 | 否 |
> | diagnosticSettings | 否 | 否 |
> | guestDiagnosticSettings | 是 | 是 |
> | 不 guestdiagnosticsettingsassociation | 是 | 是 |
> | logprofiles | 是 | 是 |
> | metricAlerts | 是 | 是 |
> | privateLinkScopes | 是 | 是 |
> | privateLinkScopes / privateEndpointConnections | 否 | 否 |
> | privateLinkScopes / scopedResources | 否 | 否 |
> | queryPacks | 是 | 是 |
> | queryPacks/查詢 | 否 | 否 |
> | scheduledQueryRules | 是 | 是 |
> | webtests | 是 | 是 |
> | workbooks | 是 | 是 |
> | workbooktemplates | 是 | 是 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 否 | 否 |
> | diagnosticSettingsCategories | 否 | 否 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | appTemplates | 否 | 否 |
> | IoTApps | 是 | 是 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | 圖形 | 是 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | deletedVaults | 否 | 否 |
> | hsmPools | 是 | 是 |
> | managedHSMs | 是 | 是 |
> | vaults | 是 | 是 |
> | 保存庫/accessPolicies | 否 | 否 |
> | 保存庫/eventGridFilters | 否 | 否 |
> | vaults / secrets | 否 | 否 |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | connectedClusters | 是 | 是 |
> | registeredSubscriptions | 否 | 否 |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | 擴充功能 | 否 | 否 |
> | sourceControlConfigurations | 否 | 否 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | clusters / attacheddatabaseconfigurations | 否 | 否 |
> | clusters / databases | 否 | 否 |
> | clusters / databases / dataconnections | 否 | 否 |
> | clusters / databases / eventhubconnections | 否 | 否 |
> | clusters / databases / principalassignments | 否 | 否 |
> | 叢集/dataconnection | 否 | 否 |
> | clusters / principalassignments | 否 | 否 |
> | 叢集/sharedidentities | 否 | 否 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | labaccounts | 是 | 是 |
> | users | 否 | 否 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | 是 | 是 |
> | integrationAccounts | 是 | 是 |
> | integrationServiceEnvironments | 是 | 是 |
> | integrationServiceEnvironments / managedApis | 否 | 否 |
> | isolatedEnvironments | 是 | 是 |
> | workflows | 是 | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | 是 | 是 |
> | webServices | 是 | 是 |
> | 工作區 | 是 | 是 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | workspaces | 是 | 是 |
> | workspaces / computes | 否 | 否 |
> | 工作區/eventGridFilters | 否 | 否 |
> | 工作區/inferenceEndpoints | 是 | 是 |
> | 工作區/inferenceEndpoints/部署 | 是 | 是 |
> | 工作區/Linkedservices.json 和 datasets.json | 否 | 否 |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | applyUpdates | 否 | 否 |
> | configurationAssignments | 否 | 否 |
> | maintenanceConfigurations | 是 | 是 |
> | publicMaintenanceConfigurations | 否 | 否 |
> | updates | 否 | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | Identities | 否 | 否 |
> | userAssignedIdentities | 是 | 是 |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | managedNetworks | 是 | 是 |
> | managedNetworks / managedNetworkGroups | 是 | 是 |
> | managedNetworks / managedNetworkPeeringPolicies | 是 | 是 |
> | 通知 | 是 | 是 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | 否 | 否 |
> | registrationAssignments | 否 | 否 |
> | registrationDefinitions | 否 | 否 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | getEntities | 否 | 否 |
> | managementGroups | 否 | 否 |
> | managementGroups/設定 | 否 | 否 |
> | resources | 否 | 否 |
> | startTenantBackfill | 否 | 否 |
> | tenantBackfillStatus | 否 | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | 帳戶/eventGridFilters | 否 | 否 |
> | 帳戶/privateAtlases | 是 | 是 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | macc | 否 | 否 |
> | offers | 否 | 否 |
> | offerTypes | 否 | 否 |
> | offerTypes/發行者 | 否 | 否 |
> | offerTypes/發行者/優惠 | 否 | 否 |
> | offerTypes/發行者/優惠/方案 | 否 | 否 |
> | offerTypes/發行者/優惠/方案/協定 | 否 | 否 |
> | offerTypes/發行者/優惠/方案/各專案 | 否 | 否 |
> | offerTypes/發行者/優惠/方案/各專案/importImage | 否 | 否 |
> | privategalleryitems | 否 | 否 |
> | privateStoreClient | 否 | 否 |
> | privateStores | 否 | 否 |
> | privateStores/優惠 | 否 | 否 |
> | products | 否 | 否 |
> | publishers | 否 | 否 |
> | publishers / offers | 否 | 否 |
> | publishers / offers / amendments | 否 | 否 |
> | 註冊 | 否 | 否 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | classicDevServices | 是 | 是 |
> | updateCommunicationPreference | 否 | 否 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | agreements | 否 | 否 |
> | offertypes | 否 | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | mediaservices | 是 | 是 |
> | windowsazure.mediaservices/accountFilters | 否 | 否 |
> | mediaservices / assets | 否 | 否 |
> | windowsazure.mediaservices/資產/區域 assetfilter | 否 | 否 |
> | windowsazure.mediaservices/contentKeyPolicies | 否 | 否 |
> | windowsazure.mediaservices/eventGridFilters | 否 | 否 |
> | windowsazure.mediaservices/liveEventOperations | 否 | 否 |
> | mediaservices / liveEvents | 是 | 是 |
> | mediaservices / liveEvents / liveOutputs | 否 | 否 |
> | windowsazure.mediaservices/liveOutputOperations | 否 | 否 |
> | windowsazure.mediaservices/mediaGraphs | 否 | 否 |
> | windowsazure.mediaservices/privateEndpointConnectionOperations | 否 | 否 |
> | windowsazure.mediaservices/privateEndpointConnectionProxies | 否 | 否 |
> | windowsazure.mediaservices/privateEndpointConnections | 否 | 否 |
> | windowsazure.mediaservices/streamingEndpointOperations | 否 | 否 |
> | mediaservices / streamingEndpoints | 是 | 是 |
> | windowsazure.mediaservices/streamingLocators | 否 | 否 |
> | windowsazure.mediaservices/streamingPolicies | 否 | 否 |
> | mediaservices / transforms | 否 | 否 |
> | mediaservices / transforms / jobs | 否 | 否 |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | appClusters | 是 | 是 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | 是 | 是 |
> | migrateprojects | 是 | 是 |
> | moveCollections | 是 | 是 |
> | projects | 是 | 是 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | 是 | 是 |
> | objectUnderstandingAccounts | 是 | 是 |
> | remoteRenderingAccounts | 是 | 是 |
> | spatialAnchorsAccounts | 是 | 是 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | 是 | 否 |
> | netAppAccounts / accountBackups | 否 | 否 |
> | netAppAccounts / capacityPools | 是 | 否 |
> | netAppAccounts/capacityPools/磁片區 | 是 | 否 |
> | netAppAccounts/capacityPools/磁片區/快照集 | 否 | 否 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | applicationGateways | 是 | 是 |
> | applicationGatewayWebApplicationFirewallPolicies | 是 | 是 |
> | applicationSecurityGroups | 是 | 是 |
> | azureFirewallFqdnTags | 否 | 否 |
> | azureFirewalls | 是 | 否 |
> | bastionHosts | 是 | 否 |
> | bgpServiceCommunities | 否 | 否 |
> | connections | 是 | 是 |
> | ddosCustomPolicies | 是 | 是 |
> | ddosProtectionPlans | 是 | 是 |
> | dnsOperationStatuses | 否 | 否 |
> | dnszones | 是 | 是 |
> | dnszones/A | 否 | 否 |
> | dnszones/AAAA | 否 | 否 |
> | dnszones / all | 否 | 否 |
> | dnszones/CAA | 否 | 否 |
> | dnszones/CNAME | 否 | 否 |
> | dnszones/MX | 否 | 否 |
> | dnszones/NS | 否 | 否 |
> | dnszones/PTR | 否 | 否 |
> | dnszones / recordsets | 否 | 否 |
> | dnszones/SOA | 否 | 否 |
> | dnszones/SRV | 否 | 否 |
> | dnszones/TXT | 否 | 否 |
> | expressRouteCircuits | 是 | 是 |
> | expressRouteCrossConnections | 是 | 是 |
> | expressRouteGateways | 是 | 是 |
> | expressRoutePorts | 是 | 是 |
> | expressRouteServiceProviders | 否 | 否 |
> | firewallPolicies | 是 | 是 |
> | frontdoors | 是，但 (請參閱 [以下附注](#frontdoor))  | 是 |
> | frontdoorWebApplicationFirewallManagedRuleSets | 是，但 (請參閱 [以下附注](#frontdoor))  | 否 |
> | frontdoorWebApplicationFirewallPolicies | 是，但 (請參閱 [以下附注](#frontdoor))  | 是 |
> | getDnsResourceReference | 否 | 否 |
> | internalNotify | 否 | 否 |
> | loadBalancers | 是 | 是 |
> | localNetworkGateways | 是 | 是 |
> | natGateways | 是 | 是 |
> | networkIntentPolicies | 是 | 是 |
> | networkInterfaces | 是 | 是 |
> | networkProfiles | 是 | 是 |
> | networkSecurityGroups | 是 | 是 |
> | networkWatchers | 是 | 是 |
> | networkWatchers / connectionMonitors | 是 | 否 |
> | networkWatchers / flowLogs | 否 | 否 |
> | networkWatchers/鏡頭 | 是 | 否 |
> | networkWatchers / pingMeshes | 是 | 否 |
> | p2sVpnGateways | 是 | 是 |
> | privateDnsOperationStatuses | 否 | 否 |
> | privateDnsZones | 是 | 是 |
> | privateDnsZones/A | 否 | 否 |
> | privateDnsZones/AAAA | 否 | 否 |
> | privateDnsZones/全部 | 否 | 否 |
> | privateDnsZones/CNAME | 否 | 否 |
> | privateDnsZones/MX | 否 | 否 |
> | privateDnsZones/PTR | 否 | 否 |
> | privateDnsZones/SOA | 否 | 否 |
> | privateDnsZones/SRV | 否 | 否 |
> | privateDnsZones/TXT | 否 | 否 |
> | privateDnsZones / virtualNetworkLinks | 是 | 是 |
> | privateEndpoints | 是 | 是 |
> | privateLinkServices | 是 | 是 |
> | publicIPAddresses | 是 | 是 |
> | publicIPPrefixes | 是 | 是 |
> | routeFilters | 是 | 是 |
> | routeTables | 是 | 是 |
> | serviceEndpointPolicies | 是 | 是 |
> | trafficManagerGeographicHierarchies | 否 | 否 |
> | trafficmanagerprofiles | 是 | 是 |
> | trafficmanagerprofiles/heatMaps | 否 | 否 |
> | trafficManagerUserMetricsKeys | 否 | 否 |
> | virtualHubs | 是 | 是 |
> | virtualNetworkGateways | 是 | 是 |
> | virtualNetworks | 是 | 是 |
> | virtualNetworks/子網 | 否 | 否 |
> | virtualNetworkTaps | 是 | 是 |
> | virtualWans | 是 | 否 |
> | vpnGateways | 是 | 是 |
> | vpnSites | 是 | 是 |
> | webApplicationFirewallPolicies | 是 | 是 |

<a id="frontdoor"></a>

> [!NOTE]
> 針對 Azure Front Door Service，您可以在建立資源時套用標記，但目前不支援更新或新增標記。


## <a name="microsoftnotebooks"></a>Microsoft 筆記本

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | 否 | 否 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | 命名空間 | 是 | 否 |
> | namespaces / notificationHubs | 是 | 否 |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | osNamespaces | 是 | 是 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | HyperVSites | 是 | 是 |
> | ImportSites | 是 | 是 |
> | MasterSites | 是 | 是 |
> | ServerSites | 是 | 是 |
> | VMwareSites | 是 | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | deletedWorkspaces | 否 | 否 |
> | linkTargets | 否 | 否 |
> | storageInsightConfigs | 否 | 否 |
> | workspaces | 是 | 是 |
> | 工作區/dataExports | 否 | 否 |
> | 工作區/資料來源 | 否 | 否 |
> | 工作區/Linkedservices.json 和 datasets.json | 否 | 否 |
> | 工作區/linkedStorageAccounts | 否 | 否 |
> | workspaces / metadata | 否 | 否 |
> | workspaces / query | 否 | 否 |
> | 工作區/scopedPrivateLinkProxies | 否 | 否 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | managementassociations | 否 | 否 |
> | managementconfigurations | 是 | 是 |
> | solutions | 是 | 是 |
> | 檢視 | 是 | 是 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | 否 | 否 |
> | peerAsns | 否 | 否 |
> | peerings | 是 | 是 |
> | peeringServiceCountries | 否 | 否 |
> | peeringServiceProviders | 否 | 否 |
> | peeringServices | 是 | 是 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | 證明 | 否 | 否 |
> | policyEvents | 否 | 否 |
> | policyMetadata | 否 | 否 |
> | policyStates | 否 | 否 |
> | policyTrackedResources | 否 | 否 |
> | remediations | 否 | 否 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | consoles | 否 | 否 |
> | dashboards | 是 | 是 |
> | userSettings | 否 | 否 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | 是 | 是 |
> | tenants | 是 | 是 |
> | 租使用者/工作區 | 否 | 否 |
> | workspaceCollections | 是 | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | capacities | 是 | 是 |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | 否 | 否 |
> | providerRegistrations / defaultRollouts | 否 | 否 |
> | providerRegistrations / resourceTypeRegistrations | 否 | 否 |
> | rollouts | 是 | 是 |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | 工作區 | 是 | 是 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | 否 | 否 |
> | vaults | 是 | 是 |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | 是 | 是 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | 命名空間 | 是 | 是 |
> | namespaces / authorizationrules | 否 | 否 |
> | namespaces / hybridconnections | 否 | 否 |
> | namespaces / hybridconnections / authorizationrules | 否 | 否 |
> | 命名空間/privateEndpointConnections | 否 | 否 |
> | namespaces / wcfrelays | 否 | 否 |
> | namespaces / wcfrelays / authorizationrules | 否 | 否 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | 查詢 | 是 | 是 |
> | resourceChangeDetails | 否 | 否 |
> | resourceChanges | 否 | 否 |
> | resources | 否 | 否 |
> | resourcesHistory | 否 | 否 |
> | subscriptionsStatus | 否 | 否 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | 否 | 否 |
> | childAvailabilityStatuses | 否 | 否 |
> | childResources | 否 | 否 |
> | emergingissues | 否 | 否 |
> | 活動 | 否 | 否 |
> | impactedResources | 否 | 否 |
> | 中繼資料 | 否 | 否 |
> | 通知 | 否 | 否 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | calculateTemplateHash | 否 | 否 |
> | deployments | 是 | 否 |
> | deployments / operations | 否 | 否 |
> | deploymentScripts | 是 | 是 |
> | deploymentScripts/記錄 | 否 | 否 |
> | 連結 | 否 | 否 |
> | notifyResourceJobs | 否 | 否 |
> | 提供者 | 否 | 否 |
> | resourceGroups | 是 | 否 |
> | subscriptions | 是 | 否 |
> | templateSpecs | 是 | 是 |
> | templateSpecs/版本 | 是 | 是 |
> | tenants | 否 | 否 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | 應用程式所需 | 是 | 是 |
> | saasresources | 否 | 否 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | 否 | 否 |
> | searchServices | 是 | 是 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | 否 | 否 |
> | advancedThreatProtectionSettings | 否 | 否 |
> | alerts | 否 | 否 |
> | alertsSuppressionRules | 否 | 否 |
> | allowedConnections | 否 | 否 |
> | applicationWhitelistings | 否 | 否 |
> | assessmentMetadata | 否 | 否 |
> | assessments | 否 | 否 |
> | autoDismissAlertsRules | 否 | 否 |
> | automations | 是 | 是 |
> | AutoProvisioningSettings | 否 | 否 |
> | Compliances | 否 | 否 |
> | 連接器 | 否 | 否 |
> | dataCollectionAgents | 否 | 否 |
> | deviceSecurityGroups | 否 | 否 |
> | discoveredSecuritySolutions | 否 | 否 |
> | externalSecuritySolutions | 否 | 否 |
> | InformationProtectionPolicies | 否 | 否 |
> | iotSecuritySolutions | 是 | 是 |
> | iotSecuritySolutions / analyticsModels | 否 | 否 |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | 否 | 否 |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | 否 | 否 |
> | iotSecuritySolutions / iotAlerts | 否 | 否 |
> | iotSecuritySolutions / iotAlertTypes | 否 | 否 |
> | iotSecuritySolutions / iotRecommendations | 否 | 否 |
> | iotSecuritySolutions / iotRecommendationTypes | 否 | 否 |
> | jitNetworkAccessPolicies | 否 | 否 |
> | jitPolicies | 否 | 否 |
> | 原則 | 否 | 否 |
> | pricings | 否 | 否 |
> | regulatoryComplianceStandards | 否 | 否 |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 否 | 否 |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 否 | 否 |
> | secureScoreControlDefinitions | 否 | 否 |
> | secureScoreControls | 否 | 否 |
> | secureScores | 否 | 否 |
> | secureScores / secureScoreControls | 否 | 否 |
> | securityContacts | 否 | 否 |
> | securitySolutions | 否 | 否 |
> | securitySolutionsReferenceData | 否 | 否 |
> | securityStatuses | 否 | 否 |
> | securityStatusesSummaries | 否 | 否 |
> | serverVulnerabilityAssessments | 否 | 否 |
> | settings | 否 | 否 |
> | subAssessments | 否 | 否 |
> | 工作 | 否 | 否 |
> | topologies | 否 | 否 |
> | workspaceSettings | 否 | 否 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 否 | 否 |
> | diagnosticSettingsCategories | 否 | 否 |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | aggregations | 否 | 否 |
> | alertRules | 否 | 否 |
> | alertRuleTemplates | 否 | 否 |
> | automationRules | 否 | 否 |
> | bookmarks | 否 | 否 |
> | 案例 | 否 | 否 |
> | dataConnectors | 否 | 否 |
> | dataConnectorsCheckRequirements | 否 | 否 |
> | 實體 | 否 | 否 |
> | entityQueries | 否 | 否 |
> | incidents | 否 | 否 |
> | officeConsents | 否 | 否 |
> | settings | 否 | 否 |
> | threatIntelligence | 否 | 否 |
> | watchlists | 否 | 否 |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | consoleServices | 否 | 否 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | 命名空間 | 是 | 是 |
> | namespaces / authorizationrules | 否 | 否 |
> | namespaces / disasterrecoveryconfigs | 否 | 否 |
> | namespaces / eventgridfilters | 否 | 否 |
> | namespaces / networkrulesets | 否 | 否 |
> | namespaces / queues | 否 | 否 |
> | namespaces / queues / authorizationrules | 否 | 否 |
> | namespaces / topics | 否 | 否 |
> | namespaces / topics / authorizationrules | 否 | 否 |
> | namespaces / topics / subscriptions | 否 | 否 |
> | namespaces / topics / subscriptions / rules | 否 | 否 |
> | premiumMessagingRegions | 否 | 否 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | 應用程式所需 | 是 | 是 |
> | clusters | 是 | 是 |
> | clusters / applications | 否 | 否 |
> | containerGroups | 是 | 是 |
> | containerGroupSets | 是 | 是 |
> | edgeclusters | 是 | 是 |
> | edgeclusters/應用程式 | 否 | 否 |
> | managedclusters | 是 | 是 |
> | managedclusters/nodetypes | 否 | 否 |
> | networks | 是 | 是 |
> | secretstores | 是 | 是 |
> | secretstores/憑證 | 否 | 否 |
> | secretstores/秘密 | 否 | 否 |
> | 磁碟區 | 是 | 是 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | 應用程式所需 | 是 | 是 |
> | containerGroups | 是 | 是 |
> | gateways | 是 | 是 |
> | networks | 是 | 是 |
> | 密碼 | 是 | 是 |
> | 磁碟區 | 是 | 是 |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | 否 | 否 |
> | providerRegistrations / resourceTypeRegistrations | 否 | 否 |
> | rollouts | 是 | 是 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | SignalR | 是 | 是 |
> | SignalR/eventGridFilters | 否 | 否 |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | 否 | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | 是 | 是 |
> | 應用程式所需 | 是 | 是 |
> | jitRequests | 是 | 是 |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | managedInstances | 是 | 是 |
> | managedInstances/資料庫 | 是 (請參閱 [下面的附注](#sqlnote))  | 是 |
> | managedInstances/資料庫/backupShortTermRetentionPolicies | 否 | 否 |
> | managedInstances/資料庫/架構/資料表/資料行/sensitivityLabels | 否 | 否 |
> | managedInstances/資料庫/vulnerabilityAssessments | 否 | 否 |
> | managedInstances/資料庫/vulnerabilityAssessments/規則/基準 | 否 | 否 |
> | managedInstances/encryptionProtector | 否 | 否 |
> | managedInstances/金鑰 | 否 | 否 |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | 否 | 否 |
> | managedInstances/vulnerabilityAssessments | 否 | 否 |
> | servers | 是 | 是 |
> | servers / administrators | 否 | 否 |
> | servers/communicationLinks | 否 | 否 |
> | servers / databases | 是 (請參閱 [下面的附注](#sqlnote))  | 是 |
> | servers/encryptionProtector | 否 | 否 |
> | servers / firewallRules | 否 | 否 |
> | servers / keys | 否 | 否 |
> | servers/restorableDroppedDatabases | 否 | 否 |
> | servers / serviceobjectives | 否 | 否 |
> | servers/tdeCertificates | 否 | 否 |
> | virtualClusters | 否 | 否 |

<a id="sqlnote"></a>

> [!NOTE]
> Master 資料庫不支援標記，但其他資料庫（包括 Azure Synapse Analytics 資料庫）則支援標記。 Azure Synapse Analytics 資料庫必須處於作用中狀態 (未暫停) 狀態。

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | 是 | 是 |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | 否 | 否 |
> | SqlVirtualMachines | 是 | 是 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | storageAccounts | 是 | 是 |
> | storageAccounts / blobServices | 否 | 否 |
> | storageAccounts / fileServices | 否 | 否 |
> | storageAccounts/queueServices | 否 | 否 |
> | storageAccounts/服務 | 否 | 否 |
> | storageAccounts/services/metricDefinitions | 否 | 否 |
> | storageAccounts/tableServices | 否 | 否 |
> | usages | 否 | 否 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | caches | 是 | 是 |
> | 快取/storageTargets | 否 | 否 |
> | usageModels | 否 | 否 |

## <a name="microsoftstoragereplication"></a>StorageReplication

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | replicationGroups | 否 | 否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | storageSyncServices / registeredServers | 否 | 否 |
> | storageSyncServices / syncGroups | 否 | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 | 否 |
> | storageSyncServices/工作流程 | 否 | 否 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | storageSyncServices / registeredServers | 否 | 否 |
> | storageSyncServices / syncGroups | 否 | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 | 否 |
> | storageSyncServices/工作流程 | 否 | 否 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | storageSyncServices / registeredServers | 否 | 否 |
> | storageSyncServices / syncGroups | 否 | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 | 否 |
> | storageSyncServices/工作流程 | 否 | 否 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | managers | 是 | 是 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | streamingjobs | 是 (請參閱下方注意事項) | 是 |

> [!NOTE]
> 您無法在 streamingjobs 執行時新增標記。 阻止資源新增標記。

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | acceptChangeTenant | 否 | 否 |
> | 別名 | 否 | 否 |
> | cancel | 否 | 否 |
> | changeTenantRequest | 否 | 否 |
> | changeTenantStatus | 否 | 否 |
> | CreateSubscription | 否 | 否 |
> | enable | 否 | 否 |
> | 重新命名 | 否 | 否 |
> | SubscriptionDefinitions | 否 | 否 |
> | SubscriptionOperations | 否 | 否 |
> | subscriptions | 否 | 否 |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | privateLinkHubs | 是 | 是 |
> | workspaces | 是 | 是 |
> | 工作區/bigDataPools | 是 | 是 |
> | 工作區/operationStatuses | 否 | 否 |
> | 工作區/sqlDatabases | 是 | 是 |
> | 工作區/sqlPools | 是 | 是 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | environments | 是 | 否 |
> | environments / accessPolicies | 否 | 否 |
> | environments / eventsources | 是 | 否 |
> | environments / referenceDataSets | 是 | 否 |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | stores | 是 | 是 |
> | 商店/accessPolicies | 否 | 否 |
> | stores / services | 否 | 否 |
> | stores / services / tokens | 否 | 否 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | imageTemplates | 是 | 是 |
> | imageTemplates / runOutputs | 否 | 否 |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | ArcZones | 是 | 是 |
> | ResourcePools | 是 | 是 |
> | VCenters | 是 | 是 |
> | VirtualMachines | 是 | 是 |
> | VirtualMachineTemplates | 是 | 是 |
> | VirtualNetworks | 是 | 是 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | 是 | 是 |
> | dedicatedCloudServices | 是 | 是 |
> | virtualMachines | 是 | 是 |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | devices | 是 | 是 |
> | registeredSubscriptions | 否 | 否 |
> | 廠商 | 否 | 否 |
> | 廠商/sku | 否 | 否 |
> | 廠商/vnfs | 否 | 否 |
> | virtualNetworkFunctionSkus | 否 | 否 |
> | vnfs | 是 | 是 |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | plans | 是 | 是 |
> | registeredSubscriptions | 否 | 否 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | 否 | 否 |
> | apiManagementAccounts/Apiacl | 否 | 否 |
> | apiManagementAccounts/api | 否 | 否 |
> | apiManagementAccounts/api/Apiacl | 否 | 否 |
> | apiManagementAccounts/api/Connectionacl | 否 | 否 |
> | apiManagementAccounts/api/連接 | 否 | 否 |
> | apiManagementAccounts/api/connections/Connectionacl | 否 | 否 |
> | apiManagementAccounts/api/localizedDefinitions | 否 | 否 |
> | apiManagementAccounts/Connectionacl | 否 | 否 |
> | apiManagementAccounts/連接 | 否 | 否 |
> | billingMeters | 否 | 否 |
> | certificates | 是 | 是 |
> | connectionGateways | 是 | 是 |
> | connections | 是 | 是 |
> | customApis | 是 | 是 |
> | deletedSites | 否 | 否 |
> | hostingEnvironments | 是 | 是 |
> | hostingEnvironments/eventGridFilters | 否 | 否 |
> | hostingEnvironments/multiRolePools | 否 | 否 |
> | hostingEnvironments/workerPools | 否 | 否 |
> | kubeEnvironments | 是 | 是 |
> | publishingUsers | 否 | 否 |
> | Mahout | 否 | 否 |
> | resourceHealthMetadata | 否 | 否 |
> | runtimes | 否 | 否 |
> | serverFarms | 是 | 是 |
> | serverFarms/eventGridFilters | 否 | 否 |
> | sites | 是 | 是 |
> | 網站/設定  | 否 | 否 |
> | sites/eventGridFilters | 否 | 否 |
> | sites/hostNameBindings | 否 | 否 |
> | sites/Networkconfig.netcfg | 否 | 否 |
> | sites / premieraddons | 是 | 是 |
> | sites / slots | 是 | 是 |
> | 網站/插槽/eventGridFilters | 否 | 否 |
> | 網站/插槽/hostNameBindings | 否 | 否 |
> | 網站/插槽/Networkconfig.netcfg | 否 | 否 |
> | sourceControls | 否 | 否 |
> | staticSites | 是 | 是 |
> | validate | 否 | 否 |
> | verifyHostingEnvironmentVnet | 否 | 否 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 否 | 否 |
> | diagnosticSettingsCategories | 否 | 否 |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | multipleActivationKeys | 是 | 是 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | DeviceServices | 是 | 是 |

## <a name="microsoftworkloadbuilder"></a>WorkloadBuilder

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | 工作負載 | 是 | 是 |
> | 工作負載/實例 | 否 | 否 |
> | 工作負載/版本 | 否 | 否 |
> | 工作負載/版本/構件 | 否 | 否 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | 成本報告中的標記 |
> | ------------- | ----------- | ----------- |
> | components | 否 | 否 |
> | componentsSummary | 否 | 否 |
> | monitorInstances | 否 | 否 |
> | monitorInstancesSummary | 否 | 否 |
> | monitors | 否 | 否 |
> | notificationSettings | 否 | 否 |

## <a name="next-steps"></a>後續步驟

若要了解如何將標記套用至資源，請參閱[使用標記來組織您的 Azure 資源](tag-resources.md)。
