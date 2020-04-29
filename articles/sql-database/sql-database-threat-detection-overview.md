---
title: 進階威脅防護
description: 先進的威脅防護會偵測異常資料庫活動，指出 Azure SQL Database 中的潛在安全性威脅。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80124764"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Azure SQL 資料庫的進階威脅防護

適用于[Azure SQL Database](sql-database-technical-overview.md)和[Azure Synapse 分析](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)的先進威脅防護會偵測異常活動，指出不尋常且可能有害的嘗試存取或惡意探索資料庫。

先進的威脅防護是先進的[資料安全性](sql-database-advanced-data-security.md)（ADS）供應專案的一部分，它是先進 SQL 安全性功能的整合套件。 進階威脅防護可以透過中央 SQL ADS 入口網站存取及管理。

> [!NOTE]
> 本主題適用于 Azure SQL server，以及在 Azure SQL server 上建立 SQL Database 和 Azure Synapse。 為了簡單起見，在同時參考 SQL Database 和 Azure Synapse 時，會使用 SQL Database。

## <a name="what-is-advanced-threat-protection"></a>什麼是先進的威脅防護

 先進的威脅防護提供一層新的安全性，藉由提供異常活動的安全性警示，讓客戶偵測並回應潛在威脅。 一旦有可疑活動、潛在弱點、SQL 插入式攻擊以及異常的資料庫存取和查詢模式發生時，使用者就會收到警示。 先進的威脅防護整合了警示與[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)，其中包括可疑活動的詳細資料，以及如何調查和緩和威脅的建議動作。 先進的威脅防護可讓您輕鬆解決資料庫的潛在威脅，而不需要是安全性專家或管理先進的安全性監視系統。

如需完整的調查體驗，建議您啟用 [SQL Database 稽核](sql-database-auditing.md)，這會將資料庫事件寫入您 Azure 儲存體帳戶中的稽核記錄。  

## <a name="advanced-threat-protection-alerts"></a>進階威脅防護警示

Azure SQL Database 的先進威脅防護會偵測異常活動，指出不尋常且可能有害的嘗試存取或惡意探索資料庫，而且它可以觸發下列警示：

- **SQL 插入的弱點**：當應用程式在資料庫中產生錯誤的 SQL 語句時，就會觸發此警示。 此警示表示 SQL 插入式攻擊的可能弱點。 錯誤的陳述式之所以產生，有兩項可能的原因：

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

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>在 Azure 入口網站中探索資料庫的先進威脅防護警示

Advanced 威脅防護會將其警示與[Azure 安全性中心](https://azure.microsoft.com/services/security-center/)整合。 [資料庫] 和 [SQL ADS] blade 內的即時 SQL Advanced 威脅防護磚會追蹤作用 Azure 入口網站中威脅的狀態。

按一下 [ **Advanced 威脅防護警示**] 以啟動 [Azure 資訊安全中心警示] 頁面，並取得在資料庫上偵測到的作用中 SQL 威脅的總覽。

   ![Advanced 威脅防護警示](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![先進的威脅防護警示2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>後續步驟

- 深入瞭解[單一和集區資料庫中的先進威脅防護](sql-database-threat-detection.md)。
- 深入瞭解[受控實例中的先進威脅防護](sql-database-managed-instance-threat-detection.md)。
- 深入瞭解[Advanced data security](sql-database-advanced-data-security.md)。
- 深入了解 [Azure SQL Database 稽核](sql-database-auditing.md)
- 深入瞭解[Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
- 如需價格的詳細資訊，請參閱[SQL Database 定價頁面](https://azure.microsoft.com/pricing/details/sql-database/)  
