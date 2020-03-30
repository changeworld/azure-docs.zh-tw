---
title: 按 Azure 服務進行的資來源提供者
description: 列出 Azure 資源管理器的所有資來源提供者命名空間，並顯示該命名空間的 Azure 服務。
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 9cf18a1e4c0dd40ae9106d32547ba4795ea9fbb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472957"
---
# <a name="resource-providers-for-azure-services"></a>Azure 服務的資來源提供者

本文介紹資來源提供者命名空間如何映射到 Azure 服務。

## <a name="match-resource-provider-to-service"></a>將資來源提供者與服務匹配

| 資來源提供者命名空間 | Azure 服務 |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure 活動目錄域服務](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | core |
| Microsoft.ADHybridHealthService | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Advisor | [Azure 顧問](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft.ApiManagement | [API 管理](../../api-management/index.yml) |
| 微軟.應用程式佈建 | core |
| 微軟.AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Attestation | Azure 證明服務 |
| Microsoft.Authorization | [Azure 資源管理器](../index.yml) |
| Microsoft.Automation | [自動化](../../automation/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure 活動目錄 B2C](../../active-directory-b2c/index.yml) |
| 微軟.Azure資料 | SQL 伺服器註冊表 |
| Microsoft.AzureStack | core |
| Microsoft.Batch | [批](../../batch/index.yml) |
| Microsoft.Billing | [成本管理和計費](/azure/billing/) |
| Microsoft.BingMaps | [Bing 地圖](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.Blockchain | [Azure 區塊鏈服務](/azure/blockchain/workbench/) |
| Microsoft.Blueprint | [Azure 藍圖](/azure/governance/blueprints/) |
| Microsoft.BotService | [Azure 機器人服務](/azure/bot-service/) |
| Microsoft.Cache | [用於雷瑞斯的 Azure 緩存](/azure/azure-cache-for-redis/) |
| Microsoft.Capacity | core |
| Microsoft.Cdn | [內容交付網路](../../cdn/index.yml) |
| Microsoft.CertificateRegistration | [應用服務證書](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| 微軟.更改分析 | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | 經典部署模型虛擬機器 |
| Microsoft.ClassicInfrastructureMigrate | 經典部署模型遷移 |
| Microsoft.ClassicNetwork | 經典部署模型虛擬網路 |
| Microsoft.ClassicStorage | 經典部署模型存儲 |
| 微軟.經典訂閱 | 傳統部署模型 |
| Microsoft.CognitiveServices | [認知服務](/azure/cognitive-services/) |
| Microsoft.Commerce | core |
| Microsoft.Compute | [虛擬機器](/azure/virtual-machines/)<br />[虛擬機器擴展集](/azure/virtual-machine-scale-sets/) |
| Microsoft.Consumption | [成本管理](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Container Instances](/azure/container-instances/) |
| Microsoft.ContainerRegistry | [容器登錄](/azure/container-registry/) |
| Microsoft.ContainerService | [Azure 庫伯奈斯服務 （AKS）](/azure/aks/) |
| Microsoft.CostManagement | [成本管理](/azure/cost-management/) |
| 微軟.成本管理出口 | [成本管理](/azure/cost-management/) |
| 微軟.客戶鎖定箱 | 適用于 Microsoft Azure 的客戶密碼箱 |
| 微軟.自訂提供程式 | [Azure 自訂提供者](../custom-providers/overview.md) |
| Microsoft.DataBox | [Azure 資料箱](/azure/databox-family/) |
| Microsoft.DataBoxEdge | [Azure Stack Edge](../../databox-online/data-box-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [資料目錄](/azure/data-catalog/) |
| Microsoft.DataFactory | [Data Factory](/azure/data-factory/) |
| Microsoft.DataLakeAnalytics | [資料湖分析](/azure/data-lake-analytics/) |
| Microsoft.DataLakeStore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft.DataMigration | [Azure Database Migration Service](/azure/dms/) |
| 微軟.資料共用 | [Azure 資料共用](/azure/data-share/) |
| Microsoft.DBforMariaDB | [瑪麗亞DB的 Azure 資料庫](/azure/mariadb/) |
| Microsoft.DBforMySQL | [MySQL 的 Azure 資料庫](/azure/mysql/) |
| Microsoft.DBforPostgreSQL | [適用於 PostgreSQL 的 Azure 資料庫](/azure/postgresql/) |
| 微軟.桌面虛擬化 | [Windows 虛擬桌面](/azure/virtual-desktop/) |
| Microsoft.DeploymentManager | [Azure 部署管理員](../templates/deployment-manager-overview.md) |
| Microsoft.Devices | [Azure IoT 中心](/azure/iot-hub/)<br />[Azure IoT 中樞裝置佈建服務](/azure/iot-dps/) |
| 微軟.DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Azure 開發人員空間](/azure/dev-spaces/) |
| Microsoft.DevTestLab | [Azure 實驗室服務](../../lab-services/index.yml) |
| 微軟.數位雙胞胎 | [Azure 數位孿生](../../digital-twins/about-digital-twins.md) |
| Microsoft.DocumentDB | [Azure 宇宙資料庫](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [應用服務](/azure/app-service/) |
| 微軟.企業知識圖 | 企業知識圖 |
| Microsoft.EventGrid | [事件網格](/azure/event-grid/) |
| Microsoft.EventHub | [事件中樞](../../event-hubs/index.yml) |
| Microsoft.Features | [Azure 資源管理器](../index.yml) |
| Microsoft.GuestConfiguration | [Azure 策略](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [Azure 上的 SAP HANA 大型執行個體](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [Azure 專用 HSM](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| 微軟.醫療保健Apis | [適用於 FHIR 的 Azure API](../../healthcare-apis/index.yml) |
| Microsoft.HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft.HybridData | [StorSimple](/azure/storsimple/) |
| Microsoft.ImportExport | [Azure 匯入/匯出](../../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [Azure IoT Central](/azure/iot-central/) |
| Microsoft.IoTSpaces | [Azure 數位孿生](../../digital-twins/index.yml) |
| Microsoft.KeyVault | [金鑰保存庫](../../key-vault/index.yml) |
| 微軟.庫伯內斯 | [Azure 庫伯奈斯服務 （AKS）](/azure/aks/) |
| Microsoft.Kusto | [Azure 資料總管](../../data-explorer/index.yml) |
| Microsoft.LabServices | [Azure 實驗室服務](../../lab-services/index.yml) |
| Microsoft.Logic | [Logic Apps](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [機器學習工作室](../../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningServices | [Azure 機器學習](../../machine-learning/index.yml) |
| 微軟.維護 | [Azure 維護](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft.ManagedIdentity | [適用於 Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/index.yml) |
| 微軟.託管服務 | [Azure Lighthouse](/azure/lighthouse/) |
| Microsoft.Management | [管理組](/azure/governance/management-groups/) |
| Microsoft.Maps | [Azure 地圖](../../azure-maps/index.yml) |
| Microsoft.Marketplace | core |
| Microsoft.MarketplaceApps | core |
| Microsoft.MarketplaceOrdering | core |
| Microsoft.Media | [媒體服務](../../media-services/index.yml) |
| Microsoft.Migrate | [Azure 遷移](../../migrate/migrate-overview.md) |
| 微軟.混合現實 | [Azure Spatial Anchors](/azure/spatial-anchors/) |
| Microsoft.NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [應用程式閘道](../../application-gateway/index.yml)<br />[Azure Bastion](/azure/bastion/)<br />[Azure DDoS 保護](../../virtual-network/ddos-protection-overview.md)<br />[Azure DNS](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure 防火牆](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Azure Private Link](../../private-link/index.yml)<br />[負載平衡器](../../load-balancer/index.yml)<br />[網路監看員](../../network-watcher/index.yml)<br />[流量管理器](../../traffic-manager/index.yml)<br />[虛擬網路](../../virtual-network/index.yml)<br />[虛擬 WAN](../../virtual-wan/index.yml)<br />[VPN 閘道](../../vpn-gateway/index.yml)<br /> |
| Microsoft.NotificationHubs | [通知中樞](../../notification-hubs/index.yml) |
| Microsoft.OffAzure | [Azure 遷移](../../migrate/migrate-overview.md) |
| Microsoft.OperationalInsights | [Azure 監視器](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure 監視器](../../azure-monitor/index.yml) |
| 微軟.對等 | [Azure 對等服務](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Azure 策略](../../governance/policy/index.yml) |
| Microsoft.Portal | [Azure 門戶](/azure/azure-portal/) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft.RecoveryServices | [Azure 網站恢復](../../site-recovery/index.yml) |
| 微軟.紅帽開放移位 | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft.Relay | [Azure 轉送](../../service-bus-relay/relay-what-is-it.md) |
| Microsoft.ResourceGraph | [Azure 資源圖](/azure/governance/resource-graph/) |
| Microsoft.ResourceHealth | [Azure 服務運行狀況](../../service-health/index.yml)|
| Microsoft.Resources | [Azure 資源管理器](../index.yml) |
| Microsoft.SaaS | core |
| Microsoft.Scheduler | [排程器](/azure/scheduler/) |
| Microsoft.Search | [Azue 認知搜尋](../../search/index.yml) |
| Microsoft.Security | [資訊安全中心](../../security-center/index.yml) |
| 微軟.安全洞察 | [Azure 哨兵](/azure/sentinel/) |
| 微軟.串列主控台 | [適用于 Windows 的 Azure 串列主控台](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [服務匯流排](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [維修結構網格](../../service-fabric-mesh/index.yml) |
| Microsoft.SignalRService | [Azure SignalR 服務](../../azure-signalr/index.yml) |
| 微軟.軟體計畫 | 授權 |
| Microsoft.Solutions | [Azure 受控應用程式](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL Database](../../sql-database/index.yml)<br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [Azure 虛擬機器上的 SQL Server](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) |
| Microsoft.Storage | [儲存空間](../../storage/index.yml) |
| 微軟.存儲緩存 | [Azure HPC Cache](/azure/hpc-cache/) |
| Microsoft.StorageSync | [儲存空間](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](/azure/storsimple/) |
| Microsoft.StreamAnalytics | [Azure 流分析](../../stream-analytics/index.yml) |
| Microsoft.Subscription | core |
| microsoft.support | core |
| 微軟.Synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.TimeSeriesInsights | [Azure 時間序列見解](../../time-series-insights/index.yml) |
| Microsoft.VirtualMachineImages | [Azure Image Builder](../../virtual-machines/linux/image-builder-overview.md) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/?view=azure-devops) |
| 微軟.VMware雲簡單 | [由 CloudSimple 提供的 Azure VMware 解決方案](/azure/vmware-cloudsimple/) |
| Microsoft.Web | [應用服務](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Microsoft.WindowsIoT | [Windows 10 IoT 核心版服務](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Azure 監視器](../../azure-monitor/index.yml) |

## <a name="next-steps"></a>後續步驟

有關資來源提供者的詳細資訊，請參閱 Azure[資來源提供者和類型](resource-providers-and-types.md)
