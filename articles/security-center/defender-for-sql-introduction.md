---
title: 適用于 SQL 的 Azure Defender-優點和功能
description: 瞭解 Azure Defender for SQL 的優點和功能。
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: e76fd33ccca73394c4ee48b3737db8b93a990659
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936556"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Azure Defender for SQL 簡介

適用于 SQL 的 azure Defender 包含兩個 Azure Defender 方案，可延伸 Azure 資訊安全中心的 [資料安全性套件](../azure-sql/database/advanced-data-security.md) ，以保護您的資料庫及其資料（不論其所在位置）。 

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|釋放狀態：|**適用于 AZURE SQL database 伺服器的 Azure Defender** -正式推出 (GA) <br>**電腦上適用于 SQL server 的 Azure Defender** -預覽|
|定價：|形成**Azure Defender FOR SQL**的兩個計畫會依[定價頁面](security-center-pricing.md)上的顯示方式計費|
|受保護的 SQL 版本：|Azure SQL Database <br>Azure SQL 受控執行個體<br>Azure Synapse Analytics (先前為 SQL DW)<br>SQL Server (所有支援的版本) |
|雲端：|![Yes](./media/icons/yes-icon.png) 商業雲端<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) 中國 Gov，其他 Gov|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Azure Defender for SQL 如何保護？

**適用于 SQL 的 Azure defender** 包含兩個不同的 azure defender 方案：

- **適用于 AZURE SQL database 伺服器的 Azure Defender** 可保護：
  - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
  - [Azure SQL 受控執行個體](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **適用于電腦上 SQL server 的 Azure Defender (預覽版) ** 擴充您 azure 原生 SQL server 的保護，以完整支援混合式環境，並保護 SQL server (裝載于 Azure、其他雲端環境，甚至是內部部署機器中的所有支援版本) 


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>適用于 SQL 的 Azure Defender 有哪些優點？

這兩個方案包含用來識別和減緩潛在資料庫弱點，以及偵測可能表示對資料庫有威脅之異常活動的功能：

- [弱點評定](../azure-sql/database/sql-vulnerability-assessment.md) -要探索、追蹤並協助您補救潛在資料庫弱點的掃描服務。 評量掃描可概述您的 SQL 機器的安全性狀態，以及任何安全性發現的詳細資料。

- [先進的威脅防護](../azure-sql/database/threat-detection-overview.md) -此偵測服務會持續監視您的 sql server 是否有威脅，例如 sql 插入式攻擊、暴力密碼破解攻擊，以及特權濫用。 這項服務在 Azure 資訊安全中心中提供動作導向的安全性警示，並提供可疑活動的詳細資料、如何緩解威脅的指引，以及使用 Azure Sentinel 繼續進行調查的選項。


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Azure Defender for SQL 提供哪一種警示？

當發生下列情況時，就會觸發安全性警示：

- **潛在的 sql 插入式攻擊** -包括應用程式在資料庫中產生錯誤的 sql 語句時所偵測到的弱點
- **異常的資料庫存取和查詢模式** -例如，使用不同認證的異常大量失敗登入嘗試 (暴力密碼破解嘗試) 
- **可疑的資料庫活動** -例如，SQL 匯入和匯出作業的匯出儲存體目的地變更

警示包含觸發警示的事件詳細資料，以及如何調查和修復威脅的建議。



## <a name="next-steps"></a>下一步

在本文中，您已瞭解 Azure Defender for SQL。

如需相關內容，請參閱下列文章： 

- [如何在電腦上啟用適用于 SQL server 的 Azure Defender](defender-for-sql-usage.md)
- [如何啟用適用于 SQL database 伺服器的 Azure Defender](../azure-sql/database/advanced-data-security.md)
- [適用于 SQL 的 Azure Defender 警示清單](alerts-reference.md#alerts-sql-db-and-warehouse)