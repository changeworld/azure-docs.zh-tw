---
title: 什麼是 Azure 保留項目？
description: 了解 Azure Reservations 和定價，以節省虛擬機器、SQL 資料庫、Azure Cosmos DB 及其他資源的成本。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 6277a7e7dc5891a3bc67c298a31344284c92e31d
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235641"
---
# <a name="what-are-azure-reservations"></a>什麼是 Azure 保留項目？

Azure 保留可協助您藉由承諾多項產品一年或三年期的方案來節省成本。 承諾可讓您在所用的資源上取得折扣。 對於隨用隨付，Reservations 可以大幅減少 72% 的資源成本。 保留會提供計費折扣，且不會影響資源的執行階段狀態。 購買保留之後，折扣就會自動套用至相符的資源。

您可以預先或每月支付保留費用。 預付和每月付款的保留總費用是相同的，當您選擇按月支付時，您不需要支付任何額外費用。 每月付款僅適用於 Azure 保留，而不是第三方產品。

您可以在 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)購買保留。

## <a name="why-buy-a-reservation"></a>為何要購買保留？

如果您有一致的資源使用量可支援保留，則購買保留就有機會讓您降低成本。 例如，當您持續在未使用保留的情況下執行某個服務的執行個體時，必須以隨用隨付費率支付費用。 當您購買保留時，您會立即取得保留折扣。 那些資源將不再以隨用隨付費率計費。

## <a name="how-reservation-discount-is-applied"></a>保留折扣的套用方式

購買之後，保留折扣會自動套用至符合您於購買保留時所選取之屬性的資源使用量。 屬性包括 SKU、區域 (如果適用) 和範圍。 保留範圍會選取保留節省成本的適用之處。

如需如何套用折扣的詳細資訊，請參閱[保留執行個體折扣應用程式](reservation-discount-application.md)。

如需保留範圍運作方式的詳細資訊，請參閱[設定保留範圍](prepare-buy-reservation.md#scope-reservations)。


## <a name="flexibility-with-azure-reservations"></a>Azure 保留的彈性

Azure 保留可提供彈性，以協助您滿足不斷演變的需求。 您可以用保留來交換相同類型的其他保留。 您也可以將不再需要的保留退費，在 12 個月的滾動期間內最多可達 50,000 美元。 退款的上限適用於您與 Microsoft 的合約範圍內所有的保留。

如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](exchange-and-refund-azure-reservations.md)


## <a name="charges-covered-by-reservation"></a>保留所涵蓋的費用

- **保留的虛擬機器執行個體**：保留僅涵蓋虛擬機器計算成本。 這並不涵蓋其他軟體、Windows、網路或儲存體費用。
- **Azure 儲存體保留容量** - 保留涵蓋 Blob 儲存體或 Azure Data Lake Gen2 儲存體的標準儲存體帳戶儲存容量。 保留並未涵蓋頻寬或交易速率。
- **Azure Cosmos DB 保留容量**：保留涵蓋針對資源所佈建的輸送量。 它未涵蓋儲存體和網路費用。
- **SQL Database 保留的 vCore**：保留僅包含計算成本。 SQL 授權會分開計費。
- **SQL 資料倉儲**：保留涵蓋 cDWU 使用量。 但未涵蓋與 SQL 資料倉儲使用量相關的儲存或網路費用。
- **Azure Databricks** - 保留僅涵蓋 DBU 使用量。 其他費用 (例如計算、儲存體和網路) 會個別收取。
- **App Service 戳記費用**：保留涵蓋戳記使用量。 但不會套用至背景工作角色，因此任何其他與戳記相關聯的資源也會分開收費。
- **適用於 MySQL 的 Azure 資料庫** - 保留僅包含計算成本。 保留並未涵蓋與 MySQL Database 伺服器相關聯的軟體、網路或儲存體費用。
- **適用於 PostgreSQL 的 Azure 資料庫** - 保留僅包含計算成本。 保留並未涵蓋與 PostgreSQL Database 伺服器相關聯的軟體、網路或儲存體費用。
- **適用於 MariaDB 的 Azure 資料庫** - 保留僅包含計算成本。 保留並未涵蓋與 MariaDB Database 伺服器相關聯的軟體、網路或儲存體費用。
- **Azure 資料總管** - 保留涵蓋加成費用。 保留並未涵蓋與叢集相關聯的計算、網路或儲存體費用。
- **Azure Cache for Redis** - 保留僅包含計算成本。 保留並未涵蓋與 Redis 快取執行個體相關聯的網路或儲存體費用。
- **Azure 專用主機** - 專用主機只會包含計算成本。
- **Azure 磁碟儲存體保留** - 保留僅涵蓋 P30 大小或更大的進階 SSD。 其中並不涵蓋任何其他磁碟類型或小於 P30 的大小。

軟體方案：

- **SUSE Linux** - 保留涵蓋軟體方案成本。 折扣僅適用於 SUSE 計量，但不適用於虛擬機器使用量。
- **Red Hat 方案** - 保留涵蓋軟體方案成本。 折扣僅適用於 RedHat 計量，但不適用於虛擬機器使用量。
- **由 CloudSimple 提供的 Azure VMware 解決方案** - 保留涵蓋 VMWare CloudSimple 節點。 可能會收取額外的軟體費用。
- **Azure Red Hat OpenShift** - 保留適用於 OpenShift 成本，而不適用於 Azure 基礎結構成本。

對於 Windows 虛擬機器和 SQL Database，保留折扣不適用於軟體成本。 您可以利用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 來涵蓋授權成本。


## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- 使用下列文章來深入了解 Azure 保留項目：
    - [管理 Azure 保留項目](manage-reserved-vm-instance.md)
    - [了解採用隨用隨付費率的訂用帳戶的保留使用量](understand-reserved-instance-usage.md)
    - [了解 Enterprise 註冊的保留項目使用量](understand-reserved-instance-usage-ea.md)
    - [Windows 軟體的成本不包括在保留項目內](reserved-instance-windows-software-costs.md)
    - [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](/partner-center/azure-reservations)

- 深入了解服務方案的保留：
    - [具有 Azure 保留 VM 執行個體的虛擬機器](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [具有 Azure Cosmos DB 保留容量的 Azure Cosmos DB 資源](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [具有 Azure SQL Database 保留容量的 SQL Database 計算資源](../../sql-database/sql-database-reserved-capacity.md) 深入了解軟體方案的保留：
    - [Azure 保留中的 Red Hat 軟體方案](../../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Azure 保留中的 SUSE 軟體方案](../../virtual-machines/linux/prepay-suse-software-charges.md)
