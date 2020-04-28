---
title: 設定進階威脅防護
description: 先進的威脅防護會偵測異常資料庫活動，指出單一資料庫或彈性集區中的資料庫有潛在的安全性威脅。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "73822498"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>針對單一或集區資料庫 Azure SQL Database 先進的威脅防護

單一和集區資料庫的[先進威脅防護](sql-database-threat-detection-overview.md)會偵測異常活動，指出不尋常且可能有害的嘗試存取或惡意探索資料庫。 先進的威脅防護可以**識別潛在的 SQL 插入**式攻擊、**來自不尋常位置或資料中心的存取**、來自不**熟悉主體或潛在有害應用程式的存取權**，以及**暴力密碼破解 SQL 認證**-請參閱[先進的威脅防護警示](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)中的詳細資料。

您可以透過[電子郵件通知](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event)或 [Azure 入口網站](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)接收偵測到威脅的通知

[先進的威脅防護](sql-database-threat-detection-overview.md)是先進的[資料安全性](sql-database-advanced-data-security.md)（ADS）供應專案的一部分，它是先進 SQL 安全性功能的整合套件。 進階威脅防護可以透過中央 SQL ADS 入口網站存取及管理。

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>在 Azure 入口網站中設定先進的威脅防護

1. 啟動 Azure 入口網站，網址[https://portal.azure.com](https://portal.azure.com)為。
2. 瀏覽至所要保護的 Azure SQL Database 伺服器的組態頁面。 在 [安全性] 設定中，選取 [進階資料安全性]****。
3. 在 [進階資料安全性]**** 組態頁面上：

   - 在伺服器上啟用進階資料安全性。
   - 在 [進階威脅防護設定]**** 的 [傳送警示給]**** 文字方塊中，提供要在偵測到異常資料庫活動時收到安全性警示的電子郵件清單。
  
   ![設定先進的威脅防護](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > 螢幕擷取畫面中的價格不一定會反映目前的價格，而是一個範例。

## <a name="set-up-advanced-threat-protection-using-powershell"></a>使用 PowerShell 設定進階威脅防護

如需腳本範例，請參閱[使用 PowerShell 設定審核和先進的威脅防護](scripts/sql-database-auditing-and-threat-detection-powershell.md)。

## <a name="next-steps"></a>後續步驟

- 深入瞭解[先進的威脅防護](sql-database-threat-detection-overview.md)。
- 深入瞭解[受控實例中的先進威脅防護](sql-database-managed-instance-threat-detection.md)。  
- 深入了解[進階資料安全性](sql-database-advanced-data-security.md)。
- 深入了解[稽核](sql-database-auditing.md)
- 深入瞭解[Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
- 如需價格的詳細資訊，請參閱[SQL Database 定價頁面](https://azure.microsoft.com/pricing/details/sql-database/)  
