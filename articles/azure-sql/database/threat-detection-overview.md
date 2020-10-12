---
title: 進階威脅防護
titleSuffix: Azure SQL Database, SQL Managed Instance, & Azure Synapse Analytics
description: 先進的威脅防護會偵測異常資料庫活動，指出 Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse Analytics 中的潛在安全性威脅。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, sstein
ms.date: 09/21/2020
tags: azure-synapse
ms.openlocfilehash: bf228d31180f5c2223fde7433d2d9b335e2b8853
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91284154"
---
# <a name="advanced-threat-protection-for-azure-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Azure SQL Database、SQL 受控執行個體和 Azure Synapse Analytics 的 Advanced 威脅防護
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

適用于 [Azure SQL Database](sql-database-paas-overview.md)、 [Azure SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md) 和 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 的 Advanced 威脅防護會偵測異常活動，指出有不尋常且可能有害的嘗試存取或惡意探索資料庫。

Advanced 威脅防護是 [Azure Defender FOR SQL](azure-defender-for-sql.md) 供應專案的一部分，這是適用于 advanced sql 安全性功能的整合套件。 您可以透過中央 Azure Defender for SQL 入口網站來存取和管理 Advanced 威脅防護。

## <a name="overview"></a>概觀

先進的威脅防護提供新的安全性層級，可讓客戶在發生異常活動時，藉由提供安全性警示來偵測和回應潛在威脅。 一旦有可疑活動、潛在弱點、SQL 插入式攻擊以及異常的資料庫存取和查詢模式發生時，使用者就會收到警示。 先進的威脅防護會將警示與 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)整合，包括可疑活動的詳細資料，以及如何調查和緩和威脅的建議動作。 先進的威脅防護可讓您輕鬆地解決資料庫的潛在威脅，而不需要是安全性專家或管理安全性監視系統。

如需完整的調查體驗，建議您啟用「審核」，這會將資料庫事件寫入您 Azure 儲存體帳戶中的 audit 記錄檔。  若要啟用審核，請參閱[AZURE SQL 受控執行個體](../managed-instance/auditing-configure.md)[的 Azure SQL Database 和 Azure Synapse 的審核](../../azure-sql/database/auditing-overview.md)。

## <a name="alerts"></a>警示

適用於 Azure SQL Database 的進階威脅防護會偵測異常活動，這些活動代表有不尋常及可能有害的活動試圖存取或惡意探索資料庫。 如需 Azure SQL Database 的警示清單，請參閱 [) 中的 SQL Database 和 Azure Synapse Analytics (先前的 SQL 資料倉儲 Azure 資訊安全中心的警示](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-sql-db-and-warehouse)。

## <a name="explore-detection-of-a-suspicious-event"></a>探索可疑事件的偵測

偵測到異常資料庫活動時，您會收到電子郵件通知。 電子郵件會提供可疑安全性事件的相關資訊，包括異常活動的性質、資料庫名稱、伺服器名稱、應用程式名稱和事件時間。 此外，該電子郵件還會提供可能原因和建議動作的相關資訊，以協助您調查和減輕資料庫的潛在威脅。

![異常活動報告](./media/threat-detection-overview/anomalous_activity_report.png)

1. 按一下電子郵件中的 [ **查看最近的 SQL 警示** ] 連結以啟動 Azure 入口網站，並顯示 Azure 資訊安全中心警示] 頁面，其中提供在資料庫上偵測到的作用中威脅的總覽。

   ![活動威脅](./media/threat-detection-overview/active_threats.png)

1. 按一下特定警示可取得其他詳細資料和調查此威脅的建議，並對未來的威脅採取補救措施。

   例如，SQL 插入式攻擊是網際網路上最常見的 Web 應用程式安全性問題之一，用於攻擊資料導向應用程式。 攻擊者利用應用程式弱點將惡意的 SQL 陳述式插入應用程式輸入欄位，破壞或修改資料庫中的資料。 針對 SQL 插入式攻擊，警示的詳細資料會包括已遭利用且有弱點的 SQL 陳述式。

   ![特定警示](./media/threat-detection-overview/specific_alert.png)

## <a name="explore-alerts-in-the-azure-portal"></a>探索 Azure 入口網站中的警示

Advanced 威脅防護會將其警示與 [Azure 安全性中心](https://azure.microsoft.com/services/security-center/)整合。 資料庫中的即時 SQL Advanced 威脅防護圖格和 Azure 入口網站中的 SQL Azure Defender blade 會追蹤作用中威脅的狀態。

按一下 [ **Advanced 威脅防護警示** ] 以啟動 [Azure 資訊安全中心警示] 頁面，並概要說明在資料庫上偵測到的作用中 SQL 威脅。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure SQL Database & Azure Synapse 中的「先進威脅防護](threat-detection-configure.md)」。
- 深入瞭解 [AZURE SQL 受控執行個體中的「先進威脅防護](../managed-instance/threat-detection-configure.md)」。
- 深入瞭解 [Azure Defender FOR SQL](azure-defender-for-sql.md)。
- 深入了解 [Azure SQL Database 稽核](../../azure-sql/database/auditing-overview.md)
- 深入瞭解 [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
- 如需定價的詳細資訊，請參閱 [Azure SQL Database 定價頁面](https://azure.microsoft.com/pricing/details/sql-database/)  
