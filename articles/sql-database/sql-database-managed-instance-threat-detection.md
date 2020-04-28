---
title: 設定 Advanced 威脅防護-受控實例
description: 先進的威脅防護會偵測異常資料庫活動，指出受控實例中的資料庫有潛在的安全性威脅。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "73822559"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>在 Azure SQL Database 受控實例中設定 Advanced 威脅防護

[受控實例](sql-database-managed-instance-index.yml)的[先進威脅防護](sql-database-threat-detection-overview.md)會偵測異常活動，指出不尋常且可能有害的嘗試存取或惡意探索資料庫。 先進的威脅防護可以**識別潛在的 SQL 插入**式攻擊、**來自不尋常位置或資料中心的存取**、來自不**熟悉主體或潛在有害應用程式的存取權**，以及**暴力密碼破解 SQL 認證**-請參閱[先進的威脅防護警示](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)中的詳細資料。

您可以透過[電子郵件通知](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event)或 [Azure 入口網站](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)接收偵測到威脅的通知

[先進的威脅防護](sql-database-threat-detection-overview.md)是先進的[資料安全性](sql-database-advanced-data-security.md)（ADS）供應專案的一部分，它是先進 SQL 安全性功能的整合套件。 進階威脅防護可以透過中央 SQL ADS 入口網站存取及管理。

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>在 Azure 入口網站中設定先進的威脅防護

1. 啟動 Azure 入口網站，網址[https://portal.azure.com](https://portal.azure.com)為。
2. 瀏覽至您要保護的受控執行個體組態頁面。 在 [**設定**] 頁面中，選取 [ **Advanced Data Security**]。
3. 在 [Advanced Data Security 設定] 頁面中
   - 開啟 **[Advanced Data** Security]。
   - 設定在偵測到異常資料庫活動時收到安全性警示的**電子郵件清單**。
   - 選取儲存異常威脅稽核記錄的 [Azure 儲存體帳戶]****。
   - 選取您想要設定的 [**先進的威脅防護類型**]。 深入瞭解[先進的威脅防護警示](sql-database-threat-detection-overview.md)。
4. 按一下 [**儲存**] 以儲存新的或已更新的「先進資料」安全性原則。

   ![進階威脅防護](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > 螢幕擷取畫面中的價格不一定會反映目前的價格，而是一個範例。

## <a name="next-steps"></a>後續步驟

- 深入瞭解[先進的威脅防護](sql-database-threat-detection-overview.md)。
- 若要了解受控執行個體，請參閱[受控執行個體是什麼](sql-database-managed-instance.md)。
- 深入瞭解[單一資料庫的先進威脅防護](sql-database-threat-detection.md)。
- 深入了解[受控執行個體稽核](https://go.microsoft.com/fwlink/?linkid=869430)。
- 深入瞭解[Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)。
