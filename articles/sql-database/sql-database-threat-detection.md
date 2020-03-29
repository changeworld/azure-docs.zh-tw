---
title: 設定進階威脅防護
description: 高級威脅防護可檢測異常的資料庫活動，指示單個資料庫或彈性池中資料庫的潛在安全威脅。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 8eb8e4fccc17fe31def671cf6e8edb19d867b244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822498"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>針對單個或池資料庫的 Azure SQL 資料庫高級威脅保護

針對單個資料庫和池資料庫[的高級威脅防護](sql-database-threat-detection-overview.md)可檢測異常活動，指示訪問或利用資料庫的異常和潛在有害嘗試。 高級威脅防護可以識別**潛在的 SQL 注入**、**從異常位置或資料中心訪問**、**從不熟悉的主體或潛在有害應用程式訪問**以及**暴力 SQL 憑據**- 請參閱[高級威脅防護警報](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)中的更多詳細資訊。

您可以透過[電子郵件通知](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event)或 [Azure 入口網站](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)接收偵測到威脅的通知

[高級威脅防護](sql-database-threat-detection-overview.md)是[高級資料安全](sql-database-advanced-data-security.md)（ADS） 產品（用於高級 SQL 安全功能的統一包）的一部分。 進階威脅防護可以透過中央 SQL ADS 入口網站存取及管理。

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>在 Azure 門戶中設置高級威脅防護

1. 在 啟動 Azure[https://portal.azure.com](https://portal.azure.com)門戶。
2. 瀏覽至所要保護的 Azure SQL Database 伺服器的組態頁面。 在 [安全性] 設定中，選取 [進階資料安全性]****。
3. 在 [進階資料安全性]**** 組態頁面上：

   - 在伺服器上啟用進階資料安全性。
   - 在 [進階威脅防護設定]**** 的 [傳送警示給]**** 文字方塊中，提供要在偵測到異常資料庫活動時收到安全性警示的電子郵件清單。
  
   ![設置高級威脅防護](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > 螢幕截圖中的價格並不總是反映當前價格，這是一個例子。

## <a name="set-up-advanced-threat-protection-using-powershell"></a>使用 PowerShell 設定進階威脅防護

有關腳本示例，請參閱[使用 PowerShell 配置審核和高級威脅防護](scripts/sql-database-auditing-and-threat-detection-powershell.md)。

## <a name="next-steps"></a>後續步驟

- 瞭解有關[高級威脅防護的更多。](sql-database-threat-detection-overview.md)
- 詳細瞭解[託管實例中的高級威脅防護](sql-database-managed-instance-threat-detection.md)。  
- 深入了解[進階資料安全性](sql-database-advanced-data-security.md)。
- 深入了解[稽核](sql-database-auditing.md)
- 瞭解有關[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)詳細資訊
- 有關定價的詳細資訊，請參閱 SQL[資料庫定價頁](https://azure.microsoft.com/pricing/details/sql-database/)  
