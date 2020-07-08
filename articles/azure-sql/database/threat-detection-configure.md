---
title: 設定進階威脅防護
description: 先進的威脅防護會偵測異常資料庫活動，指出資料庫中有潛在的安全性威脅，Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 2f4e9841a44252829fae283a12ba804219204022
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84321536"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>設定 Azure SQL Database 的先進威脅防護
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database 的[先進威脅防護](threat-detection-overview.md)會偵測異常活動，指出不尋常且可能有害的嘗試存取或惡意探索資料庫。 先進的威脅防護可以**識別潛在的 SQL 插入**式攻擊、**來自不尋常位置或資料中心的存取**、來自不**熟悉主體或潛在有害應用程式的存取權**，以及**暴力密碼破解 SQL 認證**-請參閱[先進的威脅防護警示](threat-detection-overview.md#alerts)中的詳細資料。

您可以透過[電子郵件通知](threat-detection-overview.md#explore-detection-of-a-suspicious-event)或 [Azure 入口網站](threat-detection-overview.md#explore-alerts-in-the-azure-portal)接收偵測到威脅的通知

[先進的威脅防護](threat-detection-overview.md)是先進的[資料安全性](advanced-data-security.md)供應專案的一部分，它是先進 SQL 安全性功能的整合套件。 先進的威脅防護可透過中央 SQL Advanced Data Security 入口網站來存取及管理。

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>在 Azure 入口網站中設定先進的威脅防護

1. 登入[Azure 入口網站](https://portal.azure.com)。
2. 流覽至您想要保護之伺服器的 [設定] 頁面。 在 [安全性] 設定中，選取 [進階資料安全性]****。
3. 在 [進階資料安全性]**** 組態頁面上：

   - 在伺服器上啟用進階資料安全性。
   - 在 [進階威脅防護設定]**** 的 [傳送警示給]**** 文字方塊中，提供要在偵測到異常資料庫活動時收到安全性警示的電子郵件清單。
  
   ![設定先進的威脅防護](./media/threat-detection/set_up_threat_detection.png)

## <a name="set-up-advanced-threat-protection-using-powershell"></a>使用 PowerShell 設定進階威脅防護

如需腳本範例，請參閱[使用 PowerShell 設定審核和先進的威脅防護](scripts/auditing-threat-detection-powershell-configure.md)。

## <a name="next-steps"></a>後續步驟

- 深入瞭解[先進的威脅防護](threat-detection-overview.md)。
- 深入瞭解[SQL 受控執行個體中的先進威脅防護](../managed-instance/threat-detection-configure.md)。  
- 深入了解[進階資料安全性](advanced-data-security.md)。
- 深入了解[稽核](../../azure-sql/database/auditing-overview.md)
- 深入瞭解[Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
- 如需價格的詳細資訊，請參閱[SQL Database 定價頁面](https://azure.microsoft.com/pricing/details/sql-database/)  
