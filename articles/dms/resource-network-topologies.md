---
title: 用於 SQL 託管實例遷移的網路拓撲
titleSuffix: Azure Database Migration Service
description: 使用 Azure 資料庫移轉服務瞭解 Azure SQL 資料庫託管實例遷移的源和目標配置。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 48485b7ba0f846afa737454b092a6c1ee986b737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254965"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>使用 Azure 資料庫移轉服務為 Azure SQL DB 託管實例遷移的網路拓撲

本文討論 Azure 資料庫移轉服務可以使用的各種網路拓撲，以提供從本地 SQL 伺服器到 Azure SQL 資料庫託管實例的全面遷移體驗。

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>針對混合式工作負載設定 Azure SQL Database 受控執行個體 

如果您的 Azure SQL Database 受控執行個體是連線到內部部署網路，請使用此拓撲。 這種方法提供最簡單的網路路由，並在移轉期間產生最大的資料輸送量。

![混合式工作負載的網路拓撲](media/resource-network-topologies/hybrid-workloads.png)

**需求**

- 在這種情況下，Azure SQL 資料庫託管實例和 Azure 資料庫移轉服務實例在同一 Microsoft Azure 虛擬網路中創建，但它們使用不同的子網。  
- 此方案中使用的虛擬網路也通過使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)連接到本地網路。

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure SQL Database 受控執行個體與內部部署網路分開

如果您的環境需符合一或多個下列情況案例，請使用此網路拓撲：

- Azure SQL 資料庫託管實例與本地連接隔離，但 Azure 資料庫移轉服務實例已連接到本地網路。
- 如果基於角色的存取控制 （RBAC） 策略已到位，並且需要將使用者限制為訪問託管 Azure SQL 資料庫託管實例的相同訂閱。
- 用於 Azure SQL 資料庫託管實例和 Azure 資料庫移轉服務的虛擬網路位於不同的訂閱中。

![受控執行個體與內部部署網路分開的網路拓撲](media/resource-network-topologies/mi-isolated-workload.png)

**需求**

- Azure 資料庫移轉服務為此方案使用的虛擬網路還必須通過使用 （或https://docs.microsoft.com/azure/expressroute/expressroute-introduction)[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)） 連接到本地網路。
- 在用於 Azure SQL 資料庫託管實例的虛擬網路和 Azure 資料庫移轉服務之間設置[VNet 網路對等](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>雲到雲遷移：共用虛擬網路

如果源 SQL Server 託管在 Azure VM 中，並且與 Azure SQL 資料庫託管實例和 Azure 資料庫移轉服務共用同一虛擬網路，則使用此拓撲。

![使用共用 VNet 進行雲到雲遷移的網路拓撲](media/resource-network-topologies/cloud-to-cloud.png)

**需求**

- 沒有其他需求。

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>雲到雲遷移：隔離虛擬網路

如果您的環境需符合一或多個下列情況案例，請使用此網路拓撲：

- Azure SQL 資料庫託管實例在隔離虛擬網路中預配。
- 如果基於角色的存取控制 （RBAC） 策略已到位，並且需要將使用者限制為訪問託管 Azure SQL 資料庫託管實例的相同訂閱。
- 用於 Azure SQL 資料庫託管實例和 Azure 資料庫移轉服務的虛擬網路位於不同的訂閱中。

![使用隔離 VNet 進行雲到雲遷移的網路拓撲](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**需求**

- 在用於 Azure SQL 資料庫託管實例的虛擬網路和 Azure 資料庫移轉服務之間設置[VNet 網路對等](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。

## <a name="inbound-security-rules"></a>輸入安全性規則

| **名稱**   | **港口** | **協定** | **源** | **目的地** | **行動** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | 任意      | 任意          | DMS 子網路 | 任意             | Allow      |

## <a name="outbound-security-rules"></a>輸出安全性規則

| **名稱**                  | **港口**                                              | **協定** | **源** | **目的地**           | **行動** | **規則的原因**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 管理                | 443,9354                                              | TCP          | 任意        | 任意                       | Allow      | 通過服務匯流排和 Azure blob 存儲進行管理平面通信。 <br/>(如果已啟用 Microsoft 對等互連，您可能不需要此規則。)                                                             |
| 診斷               | 12000                                                 | TCP          | 任意        | 任意                       | Allow      | DMS 會使用此規則來收集診斷資訊，以便進行疑難排解。                                                                                                                      |
| SQL 來源伺服器         | 1433 (或 SQL Server 正在接聽的 TCP IP 連接埠) | TCP          | 任意        | 內部部署位址空間 | Allow      | 來自 DMS 的 SQL Server 來源連線 <br/>(如果您擁有站對站連線，則可能需要此規則。)                                                                                       |
| SQL Server 具名執行個體 | 1434                                                  | UDP          | 任意        | 內部部署位址空間 | Allow      | 來自 DMS 的 SQL Server 具名執行個體來源連線 <br/>(如果您擁有站對站連線，則可能需要此規則。)                                                                        |
| SMB 共用                 | 445                                                   | TCP          | 任意        | 內部部署位址空間 | Allow      | 適用於 DMS 的 SMB 網路共用可儲存資料庫備份檔案，以便遷移到 Azure VM 上的 Azure SQL Database MI 和 SQL Server <br/>(如果您擁有站對站連線，則可能需要此規則)。 |
| DMS_subnet                | 任意                                                   | 任意          | 任意        | DMS_Subnet                | Allow      |                                                                                                                                                                                                  |

## <a name="see-also"></a>另請參閱

- [將 SQL Server 遷移至 Azure SQL Database 受控執行個體](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [使用 Azure 資料庫移轉服務的先決條件概述](https://docs.microsoft.com/azure/dms/pre-reqs)
- [使用 Azure 門戶創建虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>後續步驟

- 有關 Azure 資料庫移轉服務的概述，請參閱文章["什麼是 Azure 資料庫移轉服務？"。](dms-overview.md)
- 有關 Azure 資料庫移轉服務的區域可用性的當前資訊，請參閱[按區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration)頁面。
