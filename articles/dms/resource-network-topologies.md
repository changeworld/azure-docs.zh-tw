---
title: SQL 受控執行個體遷移的網路拓撲
titleSuffix: Azure Database Migration Service
description: 瞭解 Azure SQL 受控執行個體使用 Azure 資料庫移轉服務進行遷移的來源和目標設定。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: reference
ms.date: 01/08/2020
ms.openlocfilehash: ae036b7d893eb268ea55026054bf364dad0b610e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961544"
---
# <a name="network-topologies-for-azure-sql-managed-instance-migrations-using-azure-database-migration-service"></a>Azure SQL 受控執行個體使用 Azure 資料庫移轉服務進行遷移的網路拓撲

本文討論 Azure 資料庫移轉服務可使用的各種網路拓撲，以提供從 SQL Server 到 Azure SQL 受控執行個體的全方位遷移體驗。

## <a name="azure-sql-managed-instance-configured-for-hybrid-workloads"></a>為混合式工作負載設定的 Azure SQL 受控執行個體 

如果您的 Azure SQL 受控執行個體連接到您的內部部署網路，請使用此拓撲。 這種方法提供最簡單的網路路由，並在移轉期間產生最大的資料輸送量。

![混合式工作負載的網路拓撲](media/resource-network-topologies/hybrid-workloads.png)

**Requirements**

- 在此案例中，會在相同的 Microsoft Azure 虛擬網路中建立 SQL 受控執行個體和 Azure 資料庫移轉服務實例，但它們會使用不同的子網。  
- 此案例中使用的虛擬網路也會使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 或 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)連線到內部部署網路。

## <a name="sql-managed-instance-isolated-from-the-on-premises-network"></a>SQL 受控執行個體與內部部署網路隔離

如果您的環境需符合一或多個下列情況案例，請使用此網路拓撲：

- SQL 受控執行個體與內部部署連線隔離，但您的 Azure 資料庫移轉服務實例已連線到內部部署網路。
- 如果 Azure 角色型存取控制 (Azure RBAC) 原則，而且您需要限制使用者存取裝載 SQL 受控執行個體的相同訂用帳戶。
- 用於 SQL 受控執行個體和 Azure 資料庫移轉服務的虛擬網路位於不同的訂用帳戶中。

![受控執行個體與內部部署網路分開的網路拓撲](media/resource-network-topologies/mi-isolated-workload.png)

**Requirements**

- Azure 資料庫移轉服務用於此案例的虛擬網路，也必須使用 (或 VPN 連線到內部部署網路 https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 。 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
- 設定用於 SQL 受控執行個體和 Azure 資料庫移轉服務的虛擬網路之間的 [VNet 網路對等互連](../virtual-network/virtual-network-peering-overview.md) 。

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>雲端到雲端的遷移：共用的虛擬網路

如果來源 SQL Server 裝載在 Azure VM 中，並與 SQL 受控執行個體和 Azure 資料庫移轉服務共用相同的虛擬網路，請使用此拓撲。

![使用共用 VNet 進行雲端到雲端遷移的網路拓撲](media/resource-network-topologies/cloud-to-cloud.png)

**Requirements**

- 沒有其他需求。

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>雲端到雲端的遷移：隔離的虛擬網路

如果您的環境需符合一或多個下列情況案例，請使用此網路拓撲：

- SQL 受控執行個體是在隔離的虛擬網路中布建。
- 如果 Azure 角色型存取控制 (Azure RBAC) 原則，而且您需要限制使用者存取裝載 SQL 受控執行個體的相同訂用帳戶。
- 用於 SQL 受控執行個體和 Azure 資料庫移轉服務的虛擬網路位於不同的訂用帳戶中。

![使用隔離式 VNet 進行雲端到雲端遷移的網路拓撲](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Requirements**

- 設定用於 SQL 受控執行個體和 Azure 資料庫移轉服務的虛擬網路之間的 [VNet 網路對等互連](../virtual-network/virtual-network-peering-overview.md) 。

## <a name="inbound-security-rules"></a>輸入安全性規則

| **名稱**   | **港口** | **協定** | **源** | **目的地** | **動作** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | 任意      | 任意          | DMS 子網路 | 任意             | Allow      |

## <a name="outbound-security-rules"></a>輸出安全性規則

| **名稱**                  | **港口**                                              | **協定** | **源** | **目的地**           | **動作** | **規則的原因**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 管理                | 443,9354                                              | TCP          | 任意        | 任意                       | Allow      | 透過服務匯流排和 Azure blob 儲存體的管理平面通訊。 <br/>(如果已啟用 Microsoft 對等互連，您可能不需要此規則。)                                                             |
| 診斷               | 12000                                                 | TCP          | 任意        | 任意                       | Allow      | DMS 會使用此規則來收集診斷資訊，以便進行疑難排解。                                                                                                                      |
| SQL 來源伺服器         | 1433 (或 SQL Server 正在接聽的 TCP IP 連接埠) | TCP          | 任意        | 內部部署位址空間 | Allow      | 來自 DMS 的 SQL Server 來源連線 <br/>(如果您擁有站對站連線，則可能需要此規則。)                                                                                       |
| SQL Server 具名執行個體 | 1434                                                  | UDP          | 任意        | 內部部署位址空間 | Allow      | 來自 DMS 的 SQL Server 具名執行個體來源連線 <br/>(如果您擁有站對站連線，則可能需要此規則。)                                                                        |
| SMB 共用                 | 445                                                   | TCP          | 任意        | 內部部署位址空間 | Allow      | 適用於 DMS 的 SMB 網路共用可儲存資料庫備份檔案，以便遷移到 Azure VM 上的 Azure SQL Database MI 和 SQL Server <br/>(如果您擁有站對站連線，則可能需要此規則)。 |
| DMS_subnet                | 任意                                                   | 任意          | 任意        | DMS_Subnet                | Allow      |                                                                                                                                                                                                  |

## <a name="see-also"></a>另請參閱

- [將 SQL Server 遷移至 SQL 受控執行個體](./tutorial-sql-server-to-managed-instance.md)
- [使用 Azure 資料庫移轉服務的必要條件總覽](./pre-reqs.md)
- [使用 Azure 入口網站建立虛擬網路](../virtual-network/quick-create-portal.md)

## <a name="next-steps"></a>後續步驟

- 如需 Azure 資料庫移轉服務的總覽，請參閱「 [什麼是 Azure 資料庫移轉服務？](dms-overview.md)」一文。
- 如需 Azure 資料庫移轉服務區域可用性的最新資訊，請參閱 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) 頁面。