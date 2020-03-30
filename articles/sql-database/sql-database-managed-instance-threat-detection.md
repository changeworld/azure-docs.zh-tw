---
title: 配置高級威脅保護 - 託管實例
description: 高級威脅防護可檢測異常的資料庫活動，指示託管實例中資料庫的潛在安全威脅。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 69292a934af8b8777f11ab58ed3fe306abf8b408
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822559"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>在 Azure SQL 資料庫託管實例中配置高級威脅保護

[託管實例](sql-database-managed-instance-index.yml)[的高級威脅防護](sql-database-threat-detection-overview.md)可檢測異常活動，指示訪問或利用資料庫的異常和潛在有害嘗試。 高級威脅防護可以識別**潛在的 SQL 注入**、**從異常位置或資料中心訪問**、**從不熟悉的主體或潛在有害應用程式訪問**以及**暴力 SQL 憑據**- 請參閱[高級威脅防護警報](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)中的更多詳細資訊。

您可以透過[電子郵件通知](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event)或 [Azure 入口網站](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)接收偵測到威脅的通知

[高級威脅防護](sql-database-threat-detection-overview.md)是[高級資料安全](sql-database-advanced-data-security.md)（ADS） 產品（用於高級 SQL 安全功能的統一包）的一部分。 進階威脅防護可以透過中央 SQL ADS 入口網站存取及管理。

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>在 Azure 門戶中設置高級威脅防護

1. 在 啟動 Azure[https://portal.azure.com](https://portal.azure.com)門戶。
2. 瀏覽至您要保護的受控執行個體組態頁面。 在 **"設置"** 頁中，選擇**高級資料安全性**。
3. 在高級資料安全配置頁中
   - **打開**高級資料安全性。
   - 將**電子郵件清單**配置為在檢測到異常資料庫活動時接收安全警報。
   - 選取儲存異常威脅稽核記錄的 [Azure 儲存體帳戶]****。
   - 選擇要配置**的高級威脅防護類型**。 瞭解有關[高級威脅防護警報](sql-database-threat-detection-overview.md)的更多。
4. 按一下 **"保存**"以保存新的或更新的高級資料安全性原則。

   ![進階威脅防護](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > 螢幕截圖中的價格並不總是反映當前價格，這是一個例子。

## <a name="next-steps"></a>後續步驟

- 瞭解有關[高級威脅防護的更多。](sql-database-threat-detection-overview.md)
- 若要了解受控執行個體，請參閱[受控執行個體是什麼](sql-database-managed-instance.md)。
- 詳細瞭解[單個資料庫的高級威脅防護](sql-database-threat-detection.md)。
- 深入了解[受控執行個體稽核](https://go.microsoft.com/fwlink/?linkid=869430)。
- 瞭解有關[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)的更多詳細資訊。
