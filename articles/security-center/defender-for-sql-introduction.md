---
title: 適用於 SQL 的 Azure Defender - 優點和功能
description: 了解適用於 SQL 的 Azure Defender 的優點和功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 328a565dbb22a13c71a7001d43941e7be062dff9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91449051"
---
# <a name="introduction-to-azure-defender-for-sql"></a>適用於 SQL 的 Azure Defender 簡介

適用於 SQL 的 Azure Defender 包含兩個 Azure Defender 方案，可擴充 Azure 資訊安全中心的[資料安全性套件](../azure-sql/database/advanced-data-security.md)，以保護您的資料庫及其資料 (不論位於何處)。 

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|**適用於 Azure SQL 資料庫伺服器的 Azure Defender** - 正式發行 (GA)<br>**適用於機器上的 SQL 伺服器的 Azure Defender** - 預覽|
|定價：|構成**適用於 SQL 的 Azure Defender** 的兩個方案，會依照[定價頁面](security-center-pricing.md)顯示的方式計費|
|受保護的 SQL 版本：|Azure SQL Database <br>Azure SQL 受控執行個體<br>Azure Synapse Analytics (先前為 SQL DW)<br>SQL Server (所有支援的版本)|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![是](./media/icons/yes-icon.png) US Gov<br>![否](./media/icons/no-icon.png) 中國 Gov、其他 Gov|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>適用於 SQL 的 Azure Defender 可保護哪些項目？

**適用於 SQL 的 Azure Defender** 包含兩個不同的 Azure Defender 方案：

- **適用於 Azure SQL 資料庫伺服器的 Azure Defender** 可保護：
  - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
  - [Azure SQL 受控執行個體](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **適用於機器上的 SQL 伺服器的 Azure Defender (預覽)** 可擴充 Azure 原生 SQL Server 的保護，以完整支援混合式環境，並保護裝載於 Azure、其他雲端環境，甚至是內部部署機器的 SQL 伺服器 (所有支援的版本)


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>適用於 SQL 的 Azure Defender 有哪些優點？

這兩個方案包含下列功能：識別潛在資料庫弱點並降低其風險，以及偵測可能表示資料庫面臨威脅的異常活動：

- [弱點評量](../azure-sql/database/sql-vulnerability-assessment.md) - 掃描服務以探索、追蹤及協助您修復潛在的資料庫弱點。 評量掃描可讓您概略了解 SQL 機器的安全性狀態，並提供任何安全性結果的詳細資料。

- [進階威脅防護](../azure-sql/database/threat-detection-overview.md) - 此偵測服務會持續監視您的 SQL 伺服器是否有威脅，例如 SQL 插入式攻擊、暴力密碼破解攻擊和權限濫用。 這項服務會在 Azure 資訊安全中心提供動作導向的安全性警示，以及可疑活動的詳細資料、如何降低威脅的指引，以及使用 Azure Sentinel 繼續進行調查的選項。


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>適用於 SQL 的 Azure Defender 提供何種警示？

出現下列情況時，就會觸發安全性警示：

- **潛在的 SQL 插入式攻擊** - 包括應用程式在資料庫中產生錯誤的 SQL 陳述式時所偵測到的弱點
- **異常的資料庫存取和查詢模式** - 例如，使用不同認證的登入嘗試 (暴力密碼破解嘗試) 次數異常偏高
- **可疑的資料庫活動** - 例如，SQL 匯入和匯出作業的匯出儲存體目的地有所變更

警示中包含加以觸發之事件的詳細資料，以及如何調查和補救威脅的建議。



## <a name="next-steps"></a>後續步驟

在本文中，您已了解適用於 SQL 的 Azure Defender。

如需相關內容，請參閱下列文章： 

- [如何啟用適用於機器上的 SQL 伺服器的 Azure Defender](defender-for-sql-usage.md)
- [如何啟用適用於 SQL 資料庫伺服器的 Azure Defender](../azure-sql/database/advanced-data-security.md)
- [適用於 SQL 的 Azure Defender 警示清單](alerts-reference.md#alerts-sql-db-and-warehouse)