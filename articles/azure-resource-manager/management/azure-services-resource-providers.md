---
title: Azure 服務的資源提供者
description: 列出 Azure Resource Manager 的所有資源提供者命名空間，並顯示該命名空間的 Azure 服務。
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f419cc766b7886297ae5d63e6b2ff01cf6e95181
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297640"
---
# <a name="resource-providers-for-azure-services"></a>Azure 服務的資源提供者

本文說明資源提供者命名空間如何對應至 Azure 服務。

## <a name="match-resource-provider-to-service"></a>將資源提供者與服務比對

| 資源提供者命名空間 | Azure 服務 |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | core |
| Microsoft.ADHybridHealthService | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Advisor | [Azure Advisor](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft.ApiManagement | [API 管理](../../api-management/index.yml) |
| AppConfiguration | core |
| AppPlatform | [Azure 春季雲端](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Attestation | Azure 證明服務 |
| Microsoft.Authorization | [Azure Resource Manager](../index.yml) |
| Microsoft.Automation | [自動化](../../automation/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| AzureData | SQL Server 登入 |
| Microsoft.AzureStack | core |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft.Billing | [成本管理和計費](/azure/billing/) |
| Microsoft.BingMaps | [Bing 地圖](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.Blockchain | [Azure 區塊鏈服務](/azure/blockchain/workbench/) |
| Microsoft.Blueprint | [Azure 藍圖](/azure/governance/blueprints/) |
| Microsoft.BotService | [Azure Bot 服務](/azure/bot-service/) |
| Microsoft.Cache | [Azure Cache for Redis](/azure/azure-cache-for-redis/) |
| Microsoft.Capacity | core |
| Microsoft.Cdn | [內容傳遞網路](../../cdn/index.yml) |
| Microsoft.CertificateRegistration | [App Service 憑證](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| ChangeAnalysis | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | 傳統部署模型虛擬機器 |
| Microsoft.ClassicInfrastructureMigrate | 傳統部署模型遷移 |
| Microsoft.ClassicNetwork | 傳統部署模型虛擬網路 |
| Microsoft.ClassicStorage | 傳統部署模型儲存體 |
| ClassicSubscription | 傳統部署模型 |
| Microsoft.CognitiveServices | [認知服務](/azure/cognitive-services/) |
| Microsoft.Commerce | core |
| Microsoft.Compute | [虛擬機器](/azure/virtual-machines/)<br />[虛擬機器擴展集](/azure/virtual-machine-scale-sets/) |
| Microsoft.Consumption | [成本管理](/azure/cost-management/) |
| Microsoft.ContainerInstance | [容器實例](/azure/container-instances/) |
| Microsoft.ContainerRegistry | [Container Registry](/azure/container-registry/) |
| Microsoft.ContainerService | [Azure Kubernetes Service (AKS)](/azure/aks/) |
| Microsoft.CostManagement | [成本管理](/azure/cost-management/) |
| CostManagementExports | [成本管理](/azure/cost-management/) |
| CustomerLockbox | Microsoft Azure 的客戶加密箱 |
| CustomProviders | [Azure 自訂提供者](../custom-providers/overview.md) |
| Microsoft.DataBox | [Azure 資料箱](/azure/databox-family/) |
| Microsoft.DataBoxEdge | [Azure Stack Edge](../../databox-online/data-box-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [資料目錄](/azure/data-catalog/) |
| Microsoft.DataFactory | [Data Factory](/azure/data-factory/) |
| Microsoft.DataLakeAnalytics | [Data Lake Analytics](/azure/data-lake-analytics/) |
| Microsoft.DataLakeStore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) \(部分機器翻譯\) |
| Microsoft.DataMigration | [Azure 資料庫移轉服務](/azure/dms/) |
| DataShare | [Azure 資料共用](/azure/data-share/) |
| Microsoft.DBforMariaDB | [適用於 MariaDB 的 Azure 資料庫](/azure/mariadb/) |
| Microsoft.DBforMySQL | [適用於 MySQL 的 Azure 資料庫](/azure/mysql/) |
| Microsoft.DBforPostgreSQL | [適用於 PostgreSQL 的 Azure 資料庫](/azure/postgresql/) |
| DesktopVirtualization | [Windows 虛擬桌面](/azure/virtual-desktop/) |
| Microsoft.DeploymentManager | [Azure Deployment Manager](../templates/deployment-manager-overview.md) |
| Microsoft.Devices | [Azure IoT 中樞](/azure/iot-hub/)<br />[Azure IoT 中樞裝置佈建服務](/azure/iot-dps/) |
| DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Azure Dev Spaces](/azure/dev-spaces/) |
| Microsoft.DevTestLab | [Azure 實驗室服務](../../lab-services/index.yml) |
| 選取 | [Azure Digital Twins](../../digital-twins/about-digital-twins.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [App Service](/azure/app-service/) |
| EnterpriseKnowledgeGraph | 企業知識圖表 |
| Microsoft.EventGrid | [事件方格](/azure/event-grid/) |
| Microsoft.EventHub | [事件中樞](../../event-hubs/index.yml) |
| Microsoft.Features | [Azure Resource Manager](../index.yml) |
| Microsoft.GuestConfiguration | [Azure 原則](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [Azure 上的 SAP HANA 大型執行個體](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [Azure 專用 HSM](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| HealthcareApis | [Azure API for FHIR](../../healthcare-apis/index.yml) |
| HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft.HybridData | [StorSimple](/azure/storsimple/) |
| Microsoft.ImportExport | [Azure 匯入/匯出](../../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [Azure IoT Central](/azure/iot-central/) |
| Microsoft.IoTSpaces | [Azure 數位 Twins](../../digital-twins/index.yml) |
| Microsoft.KeyVault | [金鑰保存庫](../../key-vault/index.yml) |
| Kubernetes | [Azure Kubernetes Service (AKS)](/azure/aks/) |
| Microsoft.Kusto | [Azure 資料總管](../../data-explorer/index.yml) |
| Microsoft.LabServices | [Azure 實驗室服務](../../lab-services/index.yml) |
| Microsoft.Logic | [Logic Apps](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft. 維護 | [Azure 維護](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft.ManagedIdentity | [適用於 Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/index.yml) |
| ManagedServices | [Azure Lighthouse](/azure/lighthouse/) |
| Microsoft.Management | [管理群組](/azure/governance/management-groups/) |
| Microsoft.Maps | [Azure 地圖服務](../../azure-maps/index.yml) |
| Microsoft.Marketplace | core |
| Microsoft.MarketplaceApps | core |
| Microsoft.MarketplaceOrdering | core |
| Microsoft.Media | [媒體服務](../../media-services/index.yml) |
| Microsoft.Migrate | [Azure Migrate](../../migrate/migrate-overview.md) |
| MixedReality | [Azure Spatial Anchors](/azure/spatial-anchors/) |
| Microsoft.NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [虛擬網路](../../virtual-network/index.yml)<br />[負載平衡器](../../load-balancer/index.yml)<br />[應用程式閘道](../../application-gateway/index.yml)<br />[Azure DNS](../../dns/index.yml)<br />[ExpressRoute](../../expressroute/index.yml)<br />[VPN 閘道](../../vpn-gateway/index.yml)<br />[流量管理員](../../traffic-manager/index.yml)<br />[網路監看員](../../network-watcher/index.yml)<br />[Azure 防火牆](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Azure 防禦](/azure/bastion/) |
| Microsoft.NotificationHubs | [通知中樞](../../notification-hubs/index.yml) |
| Microsoft.OffAzure | [Azure Migrate](../../migrate/migrate-overview.md) |
| Microsoft.OperationalInsights | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft 對等互連 | [Azure 對等服務](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Azure 原則](../../governance/policy/index.yml) |
| Microsoft.Portal | [Azure 入口網站](/azure/azure-portal/) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft.RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| RedHatOpenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft.Relay | [Azure 轉送](../../service-bus-relay/relay-what-is-it.md) |
| Microsoft.ResourceGraph | [Azure Resource Graph](/azure/governance/resource-graph/) |
| Microsoft.ResourceHealth | [Azure 服務健康狀態](../../service-health/index.yml)|
| Microsoft.Resources | [Azure Resource Manager](../index.yml) |
| Microsoft.SaaS | core |
| Microsoft.Scheduler | [排程器](/azure/scheduler/) |
| Microsoft.Search | [Azure 認知搜尋](../../search/index.yml) |
| Microsoft.Security | [資訊安全中心](../../security-center/index.yml) |
| SecurityInsights | [Azure Sentinel](/azure/sentinel/) |
| SerialConsole | [適用于 Windows 的 Azure 序列主控台](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [服務匯流排](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Microsoft.SignalRService | [Azure SignalR Service](../../azure-signalr/index.yml) |
| SoftwarePlan | 授權 |
| Microsoft.Solutions | [Azure 受控應用程式](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL Database](../../sql-database/index.yml)<br />[Azure Synapse 分析](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [Azure 虛擬機器上的 SQL Server](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) |
| Microsoft.Storage | [Storage](../../storage/index.yml) |
| StorageCache | [Azure HPC 快取](/azure/hpc-cache/) |
| Microsoft.StorageSync | [Storage](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](/azure/storsimple/) |
| Microsoft.StreamAnalytics | [Azure 串流分析](../../stream-analytics/index.yml) |
| Microsoft.Subscription | core |
| microsoft.support | core |
| Synapse | [Azure Synapse 分析](/azure/sql-data-warehouse/) |
| Microsoft.TimeSeriesInsights | [Azure 時間序列深入解析](../../time-series-insights/index.yml) |
| Microsoft.VirtualMachineImages | [Azure 映射產生器](../../virtual-machines/linux/image-builder-overview.md) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/?view=azure-devops) |
| VMwareCloudSimple | [依 CloudSimple 的 Azure VMware 解決方案](/azure/vmware-cloudsimple/) |
| Microsoft.Web | [App Service](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Microsoft.WindowsIoT | [Windows 10 IoT 核心版服務](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Azure 監視器](../../azure-monitor/index.yml) |

## <a name="next-steps"></a>後續步驟

如需資源提供者的詳細資訊，請參閱[Azure 資源提供者和類型](resource-providers-and-types.md)
