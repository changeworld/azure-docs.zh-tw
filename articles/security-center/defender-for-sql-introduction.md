---
title: 適用於 SQL 的 Azure Defender - 優點和功能
description: 了解適用於 SQL 的 Azure Defender 的優點和功能。
author: memildin
ms.author: memildin
ms.date: 12/13/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 81ae46a7b6c19557eeadd2d5b28ad2fa46e77115
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881362"
---
# <a name="introduction-to-azure-defender-for-sql"></a>適用於 SQL 的 Azure Defender 簡介

適用於 SQL 的 Azure Defender 包含兩個 Azure Defender 方案，可擴充 Azure 資訊安全中心的[資料安全性套件](../azure-sql/database/azure-defender-for-sql.md)，以保護您的資料庫及其資料 (不論位於何處)。 

> [!VIDEO https://www.youtube.com/embed/V7RdB6RSVpc]

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|**適用於 Azure SQL 資料庫伺服器的 Azure Defender** - 正式發行 (GA)<br>**適用於機器上 SQL 伺服器的 Azure Defender** - 正式發行 (GA) |
|定價：|構成 **適用於 SQL 的 Azure Defender** 的兩個方案，會依照 [定價頁面](security-center-pricing.md)顯示的方式計費|
|受保護的 SQL 版本：|[Azure 虛擬機器上的 SQL](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)<br>[已啟用 Azure Arc 的 SQL Server](/sql/sql-server/azure-arc/overview)<br>沒有 Azure Arc 的 Windows 機器上的內部部署 SQL Server<br>Azure SQL [單一資料庫](../azure-sql/database/single-database-overview.md)和[彈性集區](../azure-sql/database/elastic-pool-overview.md)<br>[Azure SQL 受控執行個體](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Azure Synapse Analytics (先前稱為 SQL DW) 專用 SQL 集區](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|雲端：|![是](./media/icons/yes-icon.png) 商用雲端<br>![US Gov](./media/icons/yes-icon.png)<br>![是](./media/icons/yes-icon.png) 中國 Gov (**部分**：SQL Server 的警示和弱點評量的子集。 無法使用行為威脅防護)。|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>適用於 SQL 的 Azure Defender 可保護哪些項目？

**適用於 SQL 的 Azure Defender** 包含兩個不同的 Azure Defender 方案：

- **適用於 Azure SQL 資料庫伺服器的 Azure Defender** 可保護：
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
    - [Azure SQL 受控執行個體](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
    - [Azure Synapse 中的專用 SQL 集區](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **適用於機器上 SQL 伺服器的 Azure Defender** 可擴充 Azure 原生 SQL Server 的保護，以完整支援混合式環境，並保護裝載於 Azure、其他雲端環境，甚至是內部部署機器的 SQL 伺服器 (所有支援的版本)：
    - [虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)
    - 內部部署 SQL Server：
        - [已啟用 Azure Arc 的 SQL Server (預覽)](/sql/sql-server/azure-arc/overview)
        - [在沒有 Azure Arc 的Windows 機器上執行的 SQL Server](../azure-monitor/platform/agent-windows.md)


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>適用於 SQL 的 Azure Defender 有哪些優點？

這兩個方案包含下列功能：識別潛在資料庫弱點並降低其風險，以及偵測可能表示資料庫面臨威脅的異常活動：

- [弱點評量](../azure-sql/database/sql-vulnerability-assessment.md) - 掃描服務以探索、追蹤及協助您修復潛在的資料庫弱點。 評量掃描可讓您概略了解 SQL 機器的安全性狀態，並提供任何安全性結果的詳細資料。

- [進階威脅防護](../azure-sql/database/threat-detection-overview.md) - 此偵測服務會持續監視您的 SQL 伺服器是否有威脅，例如 SQL 插入式攻擊、暴力密碼破解攻擊和權限濫用。 這項服務會在 Azure 資訊安全中心提供動作導向的安全性警示，以及可疑活動的詳細資料、如何降低威脅的指引，以及使用 Azure Sentinel 繼續進行調查的選項。 
    > [!TIP]
    > 在[警示參考頁面中](alerts-reference.md#alerts-sql-db-and-warehouse)，檢視 SQL 伺服器的安全性警示清單。


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>適用於 SQL 的 Azure Defender 提供何種警示？

發生下列情況時，會觸發威脅情報擴充的安全性警示：

- **潛在的 SQL 插入式攻擊** - 包括應用程式在資料庫中產生錯誤的 SQL 陳述式時所偵測到的弱點
- **異常的資料庫存取和查詢模式** - 例如，使用不同認證的登入嘗試 (暴力密碼破解嘗試) 次數異常偏高
- **可疑的資料庫活動** - 例如，合法使用者從遭入侵的電腦存取 SQL Server，而該電腦與加密編譯的 C&C 伺服器通訊

警示中包含加以觸發之事件的詳細資料，以及如何調查和補救威脅的建議。



## <a name="next-steps"></a>後續步驟

在本文中，您已了解適用於 SQL 的 Azure Defender。

> [!div class="nextstepaction"]
> [使用 Azure Defender 掃描 SQL Server 中的弱點](defender-for-sql-usage.md)