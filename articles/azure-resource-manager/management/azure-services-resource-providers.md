---
title: Azure 服務的資源提供者
description: 列出 Azure Resource Manager 的所有資源提供者命名空間，並顯示該命名空間的 Azure 服務。
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: 6c57f3523ca8f3f4ad1565d18791d24c0e698ad6
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87808332"
---
# <a name="resource-providers-for-azure-services"></a>Azure 服務的資源提供者 \(部分機器翻譯\)

本文說明資源提供者命名空間如何對應至 Azure 服務。

## <a name="match-resource-provider-to-service"></a>將資源提供者與服務配對

| 資源提供者命名空間 | Azure 服務 |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | core |
| ADHybridHealthService<sup>1</sup> | [Azure Active Directory](../../active-directory/index.yml) |
| Microsoft.Advisor | [Azure Advisor](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](../../analysis-services/index.yml) |
| Microsoft.ApiManagement | [API 管理](../../api-management/index.yml) |
| Microsoft.AppConfiguration | [Azure 應用程式組態](../../azure-app-configuration/index.yml) |
| Microsoft.AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Attestation | Azure 證明服務 |
| Microsoft 授權<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft.Automation | [自動化](../../automation/index.yml) |
| AutonomousSystems | [自發系統](https://www.microsoft.com/ai/autonomous-systems) |
| Microsoft.AVS | [Azure VMware 解決方案](../../azure-vmware/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft.AzureData | SQL Server 登入 |
| Microsoft.AzureStack | core |
| Microsoft.AzureStackHCI | [Azure Stack HCI](/azure-stack/hci/overview) |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft 帳單<sup>1</sup> | [成本管理和計費](/azure/billing/) |
| Microsoft.BingMaps | [Bing 地圖服務](/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.Blockchain | [Azure 區塊鏈服務](../../blockchain/workbench/index.yml) |
| Microsoft.BlockchainTokens | [Azure 區塊鏈服務權杖](https://azure.microsoft.com/services/blockchain-tokens/) |
| Microsoft.Blueprint | [Azure 藍圖](../../governance/blueprints/index.yml) |
| Microsoft.BotService | [Azure Bot 服務](/azure/bot-service/) |
| Microsoft.Cache | [Azure Cache for Redis](../../azure-cache-for-redis/index.yml) |
| Microsoft.Capacity | core |
| Microsoft.Cdn | [內容傳遞網路](../../cdn/index.yml) |
| Microsoft.CertificateRegistration | [App Service 憑證](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft.ChangeAnalysis | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | 傳統部署模型虛擬機器 |
| Microsoft.ClassicInfrastructureMigrate | 傳統部署模型遷移 |
| Microsoft.ClassicNetwork | 傳統部署模型虛擬網路 |
| Microsoft.ClassicStorage | 傳統部署模型儲存體 |
| ClassicSubscription<sup>1</sup> | 傳統部署模型 |
| Microsoft.CognitiveServices | [認知服務](../../cognitive-services/index.yml) |
| Microsoft Commerce<sup>1</sup> | core |
| Microsoft.Compute | [虛擬機器](../../virtual-machines/index.yml)<br />[虛擬機器擴展集](../../virtual-machine-scale-sets/index.yml) |
| Microsoft。耗用量<sup>1</sup> | [成本管理](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Container Instances](../../container-instances/index.yml) |
| Microsoft.ContainerRegistry | [Container Registry](../../container-registry/index.yml) |
| Microsoft.ContainerService | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| CostManagement<sup>1</sup> | [成本管理](/azure/cost-management/) |
| Microsoft.CostManagementExports | [成本管理](/azure/cost-management/) |
| Microsoft.CustomerLockbox | [Microsoft Azure 的客戶加密箱](../../security/fundamentals/customer-lockbox-overview.md) |
| Microsoft.CustomProviders | [Azure 自訂提供者](../custom-providers/overview.md) |
| Microsoft.DataBox | [Azure 資料箱](../../databox/index.yml) |
| Microsoft.DataBoxEdge | [Azure Stack Edge](../../databox-online/azure-stack-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [資料目錄](../../data-catalog/index.yml) |
| Microsoft.DataFactory | [Data Factory](../../data-factory/index.yml) |
| Microsoft.DataLakeAnalytics | [Data Lake Analytics](../../data-lake-analytics/index.yml) |
| Microsoft.DataLakeStore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) \(部分機器翻譯\) |
| Microsoft.DataMigration | [Azure 資料庫移轉服務](../../dms/index.yml) |
| Microsoft.DataProtection | 資料保護 |
| Microsoft.DataShare | [Azure Data Share](../../data-share/index.yml) |
| Microsoft.DBforMariaDB | [適用於 MariaDB 的 Azure 資料庫](../../mariadb/index.yml) |
| Microsoft.DBforMySQL | [適用於 MySQL 的 Azure 資料庫](../../mysql/index.yml) |
| Microsoft.DBforPostgreSQL | [適用於 PostgreSQL 的 Azure 資料庫](../../postgresql/index.yml) |
| Microsoft.DeploymentManager | [Azure Deployment Manager](../templates/deployment-manager-overview.md) |
| Microsoft.DesktopVirtualization | [Windows 虛擬桌面](../../virtual-desktop/index.yml) |
| Microsoft.Devices | [Azure IoT 中樞](../../iot-hub/index.yml)<br />[Azure IoT 中樞裝置佈建服務](../../iot-dps/index.yml) |
| Microsoft.DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Azure Dev Spaces](../../dev-spaces/index.yml) |
| Microsoft.DevTestLab | [Azure 實驗室服務](../../lab-services/index.yml) |
| Microsoft.DigitalTwins | [Azure Digital Twins](../../digital-twins/overview.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [App Service 方案](../../app-service/index.yml) |
| Microsoft.EnterpriseKnowledgeGraph | 企業知識圖表 |
| Microsoft.EventGrid | [Event Grid](../../event-grid/index.yml) |
| Microsoft.EventHub | [事件中樞](../../event-hubs/index.yml) |
| Microsoft。功能<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft.GuestConfiguration | [Azure 原則](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [Azure 上的 SAP HANA 大型執行個體](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [Azure 專用 HSM](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft.HealthcareApis | [適用於 FHIR 的 Azure API](../../healthcare-apis/index.yml) |
| Microsoft.HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft.HybridData | [StorSimple](../../storsimple/index.yml) |
| Microsoft.ImportExport | [Azure 匯入/匯出](../../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [Azure IoT 中心](../../iot-central/index.yml) |
| Microsoft.IoTSpaces | [Azure Digital Twins](../../digital-twins/index.yml) |
| Microsoft.KeyVault | [金鑰保存庫](../../key-vault/index.yml) |
| Microsoft.Kubernetes | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft.KubernetesConfiguration | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft.Kusto | [Azure 資料總管](/azure/data-explorer/) |
| Microsoft.LabServices | [Azure 實驗室服務](../../lab-services/index.yml) |
| Microsoft.Logic | [Logic Apps](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft.Maintenance | [Azure 維護](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft.ManagedIdentity | [適用於 Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft.ManagedServices | [Azure Lighthouse](../../lighthouse/index.yml) |
| Microsoft.Management | [管理群組](../../governance/management-groups/index.yml) |
| Microsoft.Maps | [Azure 地圖服務](../../azure-maps/index.yml) |
| Microsoft.Marketplace | core |
| Microsoft.MarketplaceApps | core |
| MarketplaceOrdering<sup>1</sup> | core |
| Microsoft.Media | [媒體服務](../../media-services/index.yml) |
| Microsoft.Migrate | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft.MixedReality | [Azure Spatial Anchors](../../spatial-anchors/index.yml) |
| Microsoft.NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [應用程式閘道](../../application-gateway/index.yml)<br />[Azure Bastion](../../bastion/index.yml)<br />[Azure DDoS 保護](../../virtual-network/ddos-protection-overview.md)<br />[Azure DNS](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure 防火牆](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Azure Private Link](../../private-link/index.yml)<br />[負載平衡器](../../load-balancer/index.yml)<br />[網路監看員](../../network-watcher/index.yml)<br />[流量管理員](../../traffic-manager/index.yml)<br />[虛擬網路](../../virtual-network/index.yml)<br />[Virtual WAN](../../virtual-wan/index.yml)<br />[VPN 閘道](../../vpn-gateway/index.yml)<br /> |
| Microsoft.NotificationHubs | [通知中樞](../../notification-hubs/index.yml) |
| Microsoft.ObjectStore | 物件存放區 |
| Microsoft.OffAzure | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft.OperationalInsights | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft.Peering | [Azure 對等互連服務](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Azure 原則](../../governance/policy/index.yml) |
| Microsoft。入口網站<sup>1</sup> | [Azure 入口網站](../../azure-portal/index.yml) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| PowerPlatform | [Power Platform](/power-platform/) |
| Microsoft.Quantum | [Azure 量子](https://azure.microsoft.com/services/quantum/) |
| Microsoft.RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft.RedHatOpenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft.Relay | [Azure 轉送](../../azure-relay/relay-what-is-it.md) |
| Az.resourcegraph<sup>1</sup> | [Azure Resource Graph](../../governance/resource-graph/index.yml) |
| Microsoft.ResourceHealth | [Azure 服務健康狀態](../../service-health/index.yml) |
| Microsoft .Resources<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft.SaaS | core |
| Microsoft.Scheduler | [排程器](../../scheduler/index.yml) |
| Microsoft.Search | [Azue 認知搜尋](../../search/index.yml) |
| Microsoft.Security | [資訊安全中心](../../security-center/index.yml) |
| Microsoft.SecurityInsights | [Azure Sentinel](../../sentinel/index.yml) |
| SerialConsole<sup>1</sup> | [適用於 Windows 的 Azure 序列主控台](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [服務匯流排](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Microsoft.Services | core |
| Microsoft.SignalRService | [Azure SignalR 服務](../../azure-signalr/index.yml) |
| Microsoft.SoftwarePlan | 授權 |
| Microsoft.Solutions | [Azure 受控應用程式](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL Database](../../azure-sql/database/index.yml)<br /> [Azure SQL 受控執行個體](../../azure-sql/managed-instance/index.yml) <br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [Azure 虛擬機器上的 SQL Server](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) |
| Microsoft.Storage | [Storage](../../storage/index.yml) |
| Microsoft.StorageSync | [Storage](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](../../storsimple/index.yml) |
| Microsoft.StreamAnalytics | [Azure 串流分析](../../stream-analytics/index.yml) |
| Microsoft.Subscription | core |
| microsoft 支援<sup>1</sup> | core |
| Microsoft.Synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.TimeSeriesInsights | [Azure 時間序列深入解析](../../time-series-insights/index.yml) |
| Microsoft.Token | Token |
| Microsoft.VirtualMachineImages | [Azure Image Builder](../../virtual-machines/linux/image-builder-overview.md) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/?view=azure-devops) |
| Microsoft.VMware | [Azure VMware 解決方案](../../azure-vmware/index.yml) |
| Microsoft.VMwareCloudSimple | [由 CloudSimple 提供的 Azure VMware 解決方案](../../vmware-cloudsimple/index.md) |
| Microsoft.VSOnline | [Azure DevOps](/azure/devops/?view=azure-devops) |
| Microsoft.Web | [App Service](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Microsoft.WindowsESU | 擴充的安全性更新 |
| Microsoft.WindowsIoT | [Windows 10 IoT 核心版服務](/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| WorkloadMonitor<sup>1</sup> | [Azure 監視器](../../azure-monitor/index.yml) |

<sup>1</sup>預設已登錄

## <a name="next-steps"></a>後續步驟

如需資源提供者的詳細資訊，包括如何註冊資源提供者，請參閱[Azure 資源提供者和類型](resource-providers-and-types.md)