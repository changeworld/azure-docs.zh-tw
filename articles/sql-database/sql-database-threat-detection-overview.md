---
title: 進階威脅防護
description: 高級威脅防護可檢測異常的資料庫活動，指示 Azure SQL 資料庫中的潛在安全威脅。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 17ca8cbb7a55e9c0d44af099f4884f71b1cd457a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124764"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Azure SQL 資料庫的進階威脅防護

Azure SQL[資料庫](sql-database-technical-overview.md)和[Azure 突觸分析](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)的高級威脅保護可檢測異常活動，指示訪問或利用資料庫的異常和潛在有害嘗試。

高級威脅防護是[高級資料安全](sql-database-advanced-data-security.md)（ADS） 產品（用於高級 SQL 安全功能的統一包）的一部分。 進階威脅防護可以透過中央 SQL ADS 入口網站存取及管理。

> [!NOTE]
> 本主題適用于 Azure SQL 伺服器，以及在 Azure SQL 伺服器上創建的 SQL 資料庫和 Azure 突觸。 為簡單起見，SQL 資料庫在引用 SQL 資料庫和 Azure 突觸時使用。

## <a name="what-is-advanced-threat-protection"></a>什麼是高級威脅防護

 高級威脅防護提供了新的安全層，使客戶能夠通過針對異常活動提供安全警報來檢測和回應潛在威脅的發生。 一旦有可疑活動、潛在弱點、SQL 插入式攻擊以及異常的資料庫存取和查詢模式發生時，使用者就會收到警示。 高級威脅防護將警報與[Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成，其中包括可疑活動的詳細資訊，並建議如何調查和減輕威脅的操作。 高級威脅防護使解決資料庫的潛在威脅變得簡單，無需成為安全專家或管理高級安全監控系統。

如需完整的調查體驗，建議您啟用 [SQL Database 稽核](sql-database-auditing.md)，這會將資料庫事件寫入您 Azure 儲存體帳戶中的稽核記錄。  

## <a name="advanced-threat-protection-alerts"></a>進階威脅防護警示

Azure SQL 資料庫的高級威脅保護可檢測異常活動，指示訪問或利用資料庫的異常和潛在有害嘗試，並可能引發以下警報：

- **SQL 注入的漏洞**：當應用程式在資料庫中建置錯誤的 SQL 語句時觸發此警報。 此警示表示 SQL 插入式攻擊的可能弱點。 錯誤的陳述式之所以產生，有兩項可能的原因：

  - 應用程式的程式碼中有缺失，而建構了錯誤的 SQL 陳述式
  - 應用程式的程式碼或預存程序在建構錯誤的 SQL 陳述式時未處理使用者輸入，這可能會遭到 SQL 插入式攻擊的侵害
- **可能的 SQL 插入**：在 SQL 插入的已知應用程式弱點遭到作用中的攻擊時，會觸發此警示。 這表示有攻擊者嘗試使用有弱點的應用程式程式碼或預存程序插入惡意 SQL 陳述式。
- **從不尋常的位置存取**：有人從不尋常的地理位置登入 SQL Server，而使 SQL Server 的存取模式有所變更時，會觸發此警示。 在某些情況下，警示會偵測到合法的動作 (新的應用程式或開發人員維護)。 在其他情況下，警示則是偵測惡意動作 (離職員工、外部攻擊者)。
- **從不尋常的 Azure 資料中心存取**：有人從不尋常的 Azure 資料中心登入 SQL Server (在近期曾在此伺服器上發現此資料中心)，而使 SQL Server 的存取模式有所變更時，會觸發此警示。 在某些情況下，警示會偵測到合法的動作 (您在 Azure、Power BI、Azure SQL 查詢編輯器中使用新的應用程式)。 在其他情況下，警示則是偵測來自 Azure 資源/服務的惡意動作 (離職員工、外部攻擊者)。
- **從不熟悉的主體存取**：有人使用不尋常的主體 (SQL 使用者) 登入 SQL Server，而使 SQL Server 的存取模式有所變更時，會觸發此警示。 在某些情況下，警示會偵測到合法的動作 (新的應用程式、開發人員維護)。 在其他情況下，警示則是偵測惡意動作 (離職員工、外部攻擊者)。
- **從可能有害的應用程式存取**：使用可能有害的應用程式用存取資料庫時，會觸發此警示。 在某些情況下，警示會偵測到執行中的滲透測試。 在其他情況下，警示則是偵測使用常見攻擊工具的攻擊。
- **暴力 SQL 認證**：有使用不同認證的異常大量登入失敗時，會觸發此警示。 在某些情況下，警示會偵測到執行中的滲透測試。 在其他情況下，警示則是偵測暴力攻擊。

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>偵測到可疑事件時探索異常資料庫活動

偵測到異常資料庫活動時，您會收到電子郵件通知。 電子郵件會提供可疑安全性事件的相關資訊，包括異常活動的性質、資料庫名稱、伺服器名稱、應用程式名稱和事件時間。 此外，該電子郵件還會提供可能原因和建議動作的相關資訊，以協助您調查和減輕資料庫的潛在威脅。

![異常活動報告](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. 按一下電子郵件中的 [檢視最近的 SQL 警示]**** 連結來啟動 Azure 入口網站，並顯示 Azure 資訊安全中心警示頁面，其中會概述在 SQL 資料庫上偵測到的作用中威脅。

   ![活動威脅](./media/sql-database-threat-detection/active_threats.png)

2. 按一下特定警示可取得其他詳細資料和調查此威脅的建議，並對未來的威脅採取補救措施。

   例如，SQL 插入式攻擊是網際網路上最常見的 Web 應用程式安全性問題之一，用於攻擊資料導向應用程式。 攻擊者利用應用程式弱點將惡意的 SQL 陳述式插入應用程式輸入欄位，破壞或修改資料庫中的資料。 針對 SQL 插入式攻擊，警示的詳細資料會包括已遭利用且有弱點的 SQL 陳述式。

   ![特定警示](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>在 Azure 門戶中流覽資料庫的高級威脅防護警報

高級威脅防護將其警報與 Azure[安全中心](https://azure.microsoft.com/services/security-center/)集成。 資料庫中的即時 SQL 高級威脅保護磁貼和 Azure 門戶中的 SQL ADS 邊欄選項卡跟蹤活動威脅的狀態。

按一下**高級威脅防護警報**以啟動 Azure 安全中心警報頁，並獲取資料庫中檢測到的活動 SQL 威脅的概覽。

   ![高級威脅防護警報](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![高級威脅防護警報2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>後續步驟

- 詳細瞭解[單個資料庫和池資料庫中的高級威脅防護](sql-database-threat-detection.md)。
- 詳細瞭解[託管實例中的高級威脅防護](sql-database-managed-instance-threat-detection.md)。
- 瞭解有關[高級資料安全性](sql-database-advanced-data-security.md)的更多。
- 深入了解 [Azure SQL Database 稽核](sql-database-auditing.md)
- 瞭解有關[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)詳細資訊
- 有關定價的詳細資訊，請參閱 SQL[資料庫定價頁](https://azure.microsoft.com/pricing/details/sql-database/)  
