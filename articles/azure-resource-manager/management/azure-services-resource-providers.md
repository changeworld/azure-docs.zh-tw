---
title: Azure 服務的資源提供者
description: 列出 Azure Resource Manager 的所有資源提供者命名空間，並顯示該命名空間的 Azure 服務。
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 5e89b2b7cf7c27a501d93f63c74d81d8fb40883c
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033539"
---
# <a name="resource-providers-for-azure-services"></a>Azure 服務的資源提供者 \(部分機器翻譯\)

本文說明資源提供者命名空間對應至 Azure 服務的方式。

## <a name="match-resource-provider-to-service"></a>將資源提供者與服務配對

標示為 **已註冊** 的資源提供者預設會針對您的訂用帳戶註冊。 如需詳細資訊，請參閱 [註冊](#registration)。

| 資源提供者命名空間 | Azure 服務 |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure Active Directory 網域服務](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | core |
| ADHybridHealthService- [已註冊](#registration) | [Azure Active Directory](../../active-directory/index.yml) |
| Microsoft.Advisor | [Azure Advisor](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](../../analysis-services/index.yml) |
| Microsoft.ApiManagement | [API 管理](../../api-management/index.yml) |
| Microsoft.AppConfiguration | [Azure 應用程式組態](../../azure-app-configuration/index.yml) |
| Microsoft.AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Attestation | Azure 證明服務 |
| [已註冊](#registration)的 Microsoft 授權 | [Azure Resource Manager](../index.yml) |
| Microsoft.Automation | [自動化](../../automation/index.yml) |
| AutonomousSystems | [自主系統](https://www.microsoft.com/ai/autonomous-systems) |
| Microsoft.AVS | [Azure VMware 解決方案](../../azure-vmware/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| AzureArcData | Azure Arc 啟用的資料服務登錄 |
| Microsoft.AzureData | SQL Server 登入 |
| Microsoft.AzureStack | core |
| Microsoft.AzureStackHCI | [Azure Stack HCI](/azure-stack/hci/overview) |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| [已註冊](#registration)Microsoft 帳單 | [成本管理和計費](/azure/billing/) |
| Microsoft.BingMaps | [Bing 地圖](/BingMaps/#pivot=main&panel=BingMapsAPI) |
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
| ClassicSubscription- [已註冊](#registration) | 傳統部署模型 |
| Microsoft.CognitiveServices | [認知服務](../../cognitive-services/index.yml) |
| Microsoft Commerce- [已註冊](#registration) | core |
| Microsoft.Compute | [虛擬機器](../../virtual-machines/index.yml)<br />[虛擬機器擴展集](../../virtual-machine-scale-sets/index.yml) |
| [已註冊](#registration)Microsoft | [成本管理](/azure/cost-management/) |
| Microsoft.ContainerInstance | [容器執行個體](../../container-instances/index.yml) |
| Microsoft.ContainerRegistry | [Container Registry](../../container-registry/index.yml) |
| Microsoft.ContainerService | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| CostManagement- [已註冊](#registration) | [成本管理](/azure/cost-management/) |
| Microsoft.CostManagementExports | [成本管理](/azure/cost-management/) |
| Microsoft.CustomerLockbox | [適用於 Microsoft Azure 的客戶加密箱](../../security/fundamentals/customer-lockbox-overview.md) |
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
| Microsoft.Devices | [Azure IoT 中心](../../iot-hub/index.yml)<br />[Azure IoT 中樞裝置佈建服務](../../iot-dps/index.yml) |
| Microsoft.DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Azure Dev Spaces](../../dev-spaces/index.yml) \(英文\) |
| Microsoft.DevTestLab | [Azure 實驗室服務](../../lab-services/index.yml) |
| Microsoft.DigitalTwins | [Azure Digital Twins](../../digital-twins/overview.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [App Service](../../app-service/index.yml) |
| Microsoft.DynamicsLcs | [生命週期服務](https://lcs.dynamics.com/Logon/Index ) |
| Microsoft.EnterpriseKnowledgeGraph | 企業知識圖表 |
| Microsoft.EventGrid | [Event Grid](../../event-grid/index.yml) |
| Microsoft.EventHub | [事件中樞](../../event-hubs/index.yml) |
| Microsoft。功能- [已註冊](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft.GuestConfiguration | [Azure 原則](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [Azure 上的 SAP HANA 大型執行個體](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [Azure 專用 HSM](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft.HealthcareApis | [適用於 FHIR 的 Azure API](../../healthcare-apis/index.yml) |
| Microsoft.HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft.HybridData | [StorSimple](../../storsimple/index.yml) |
| HybridNetwork  | [私人 Edge 區域](../../networking/edge-zones-overview.md) |
| Microsoft.ImportExport | [Azure 匯入/匯出](../../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [Azure IoT Central](../../iot-central/index.yml) |
| Microsoft.IoTSpaces | [Azure Digital Twins](../../digital-twins/index.yml) |
| Microsoft.Intune | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft.KeyVault | [金鑰保存庫](../../key-vault/index.yml) |
| Microsoft.Kubernetes | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft.KubernetesConfiguration | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft.Kusto | [Azure 資料總管](/azure/data-explorer/) |
| Microsoft.LabServices | [Azure 實驗室服務](../../lab-services/index.yml) |
| Microsoft.Logic | [Logic Apps](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../../machine-learning/classic/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft.Maintenance | [Azure 維護](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft.ManagedIdentity | [適用於 Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft.ManagedNetwork | PaaS 服務所管理的虛擬網路 |
| Microsoft.ManagedServices | [Azure Lighthouse](../../lighthouse/index.yml) |
| Microsoft.Management | [管理群組](../../governance/management-groups/index.yml) |
| Microsoft.Maps | [Azure 地圖服務](../../azure-maps/index.yml) |
| Microsoft.Marketplace | core |
| Microsoft.MarketplaceApps | core |
| MarketplaceOrdering- [已註冊](#registration) | core |
| Microsoft.Media | [媒體服務](../../media-services/index.yml) |
| Microsoft.Microservices4Spring | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Migrate | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft.MixedReality | [Azure Spatial Anchors](../../spatial-anchors/index.yml) |
| Microsoft.NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [應用程式閘道](../../application-gateway/index.yml)<br />[Azure Bastion](../../bastion/index.yml)<br />[Azure DDoS 保護](../../ddos-protection/ddos-protection-overview.md)<br />[Azure DNS](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure 防火牆](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Azure Private Link](../../private-link/index.yml)<br />[負載平衡器](../../load-balancer/index.yml)<br />[網路監看員](../../network-watcher/index.yml)<br />[流量管理員](../../traffic-manager/index.yml)<br />[虛擬網路](../../virtual-network/index.yml)<br />[Virtual WAN](../../virtual-wan/index.yml)<br />[VPN 閘道](../../vpn-gateway/index.yml)<br /> |
| Microsoft 筆記本 | [Azure Notebooks](https://notebooks.azure.com/help/introduction) |
| Microsoft.NotificationHubs | [通知中樞](../../notification-hubs/index.yml) |
| Microsoft.ObjectStore | 物件存放區 |
| Microsoft.OffAzure | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft.OperationalInsights | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft.Peering | [Azure 對等互連服務](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Azure 原則](../../governance/policy/index.yml) |
| Microsoft 入口網站- [已註冊](#registration) | [Azure 入口網站](../../azure-portal/index.yml) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| >admin.powerplatform.microsoft.com | [Power Platform](/power-platform/) |
| Microsoft.ProjectBabylon | [Azure 資料目錄](../../data-catalog/overview.md) |
| Microsoft.Quantum | [Azure Quantum](https://azure.microsoft.com/services/quantum/) |
| Microsoft.RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft.RedHatOpenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft.Relay | [Azure 轉送](../../azure-relay/relay-what-is-it.md) |
| >az.resourcegraph- [已註冊](#registration) | [Azure Resource Graph](../../governance/resource-graph/index.yml) |
| Microsoft.ResourceHealth | [Azure 服務健康狀態](../../service-health/index.yml) |
| Microsoft .Resources- [已註冊](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft.SaaS | core |
| Microsoft.Scheduler | [排程器](../../scheduler/index.yml) |
| Microsoft.Search | [Azue 認知搜尋](../../search/index.yml) |
| Microsoft.Security | [資訊安全中心](../../security-center/index.yml) |
| Microsoft.SecurityInsights | [Azure Sentinel](../../sentinel/index.yml) |
| SerialConsole- [已註冊](#registration) | [適用於 Windows 的 Azure 序列主控台](../../virtual-machines/troubleshooting/serial-console-windows.md) |
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
| Microsoft.StorageCache | [Azure HPC Cache](../../hpc-cache/index.yml) |
| Microsoft.StorageSync | [Storage](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](../../storsimple/index.yml) |
| Microsoft.StreamAnalytics | [Azure 串流分析](../../stream-analytics/index.yml) |
| Microsoft.Subscription | core |
| microsoft. 支援- [已註冊](#registration) | core |
| Microsoft.Synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.TimeSeriesInsights | [Azure 時間序列深入解析](../../time-series-insights/index.yml) |
| Microsoft.Token | 權杖 |
| Microsoft.VirtualMachineImages | [Azure Image Builder](../../virtual-machines/linux/image-builder-overview.md) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/) |
| Microsoft.VMware | [Azure VMware 解決方案](../../azure-vmware/index.yml) |
| Microsoft.VMwareCloudSimple | [由 CloudSimple 提供的 Azure VMware 解決方案](../../vmware-cloudsimple/index.md) |
| Microsoft.VSOnline | [Azure DevOps](/azure/devops/) |
| Microsoft.Web | [App Service](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Microsoft.WindowsDefenderATP | [Microsoft Defender 進階威脅防護](../../security-center/security-center-wdatp.md) |
| Microsoft.WindowsESU | 延伸安全性更新 |
| Microsoft.WindowsIoT | [Windows 10 IoT 核心版服務](/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Azure 監視器](../../azure-monitor/index.yml) |

## <a name="registration"></a>註冊

以上標示為 **已註冊** 的資源提供者預設會針對您的訂用帳戶註冊。 若要使用其他資源提供者，您必須 [註冊它們](resource-providers-and-types.md)。 不過，當您採取特定動作時，許多資源提供者都會註冊給您。 例如，如果您透過入口網站建立資源，入口網站會自動註冊所需的任何未註冊資源提供者。 透過 [Azure Resource Manager 範本](../templates/overview.md)部署資源時，也會註冊任何必要的資源提供者。

> [!IMPORTANT]
> 當您準備好要使用資源提供者時，請只註冊資源提供者。 註冊步驟可讓您在訂用帳戶內維持最低許可權。 惡意使用者無法使用未註冊的資源提供者。

## <a name="next-steps"></a>後續步驟

如需資源提供者的詳細資訊，包括如何註冊資源提供者，請參閱 [Azure 資源提供者和類型](resource-providers-and-types.md)。
