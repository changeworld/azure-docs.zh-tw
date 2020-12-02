---
title: 設定進階威脅防護
description: 先進的威脅防護會偵測異常資料庫活動，指出資料庫的潛在安全性威脅 Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: 1425003c718ca52c0bea712e9d25cd3e4c035cf1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453978"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>設定 Azure SQL Database 的 Advanced 威脅防護
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database 的[Advanced 威脅防護](threat-detection-overview.md)會偵測異常活動，指出有不尋常且可能有害的嘗試存取或惡意探索資料庫。 Advanced 威脅防護可以識別 **潛在的 SQL 插入**、 **從不尋常的位置或資料中心進行** 存取、 **從不熟悉的主體或可能有害的應用程式存取**，以及 **暴力密碼破解 SQL 認證** -請參閱 [Advanced 威脅防護警示](threat-detection-overview.md#alerts)中的詳細資料。

您可以透過[電子郵件通知](threat-detection-overview.md#explore-detection-of-a-suspicious-event)或[Azure 入口網站](threat-detection-overview.md#explore-alerts-in-the-azure-portal)，收到有關偵測到之威脅的通知

[Advanced 威脅防護](threat-detection-overview.md) 是 [AZURE Defender for SQL](azure-defender-for-sql.md) 供應專案的一部分，這是適用于 Advanced sql 安全性功能的整合套件。 您可以透過中央 Azure Defender for SQL 入口網站來存取和管理 Advanced 威脅防護。

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>在 Azure 入口網站中設定 Advanced 威脅防護

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 流覽至您要保護之伺服器的設定頁面。 在 [安全性設定] 中，選取 [ **安全性中心**]。
3. 在 [ **Azure Defender FOR SQL** 設定] 頁面上：

   - 在伺服器上啟用 **Azure Defender FOR SQL** 。
   - 在 [ **Advanced 威脅防護設定**] 中，提供在 [ **傳送警示至** ] 文字方塊中偵測到異常資料庫活動時收到安全性警示的電子郵件清單。
   
   :::image type="content" source="media/azure-defender-for-sql/set-up-advanced-threat-protection.png" alt-text="設定 advanced 威脅防護":::

## <a name="set-up-advanced-threat-protection-using-powershell"></a>使用 PowerShell 設定進階威脅防護

如需腳本範例，請參閱 [使用 PowerShell 設定審核和先進的威脅防護](scripts/auditing-threat-detection-powershell-configure.md)。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [先進的威脅防護](threat-detection-overview.md)。
- 深入瞭解 [SQL 受控執行個體中的「先進威脅防護](../managed-instance/threat-detection-configure.md)」。  
- 深入瞭解 [Azure Defender FOR SQL](azure-defender-for-sql.md)。
- 深入了解[稽核](../../azure-sql/database/auditing-overview.md)
- 深入瞭解 [Azure 資訊安全中心](../../security-center/security-center-introduction.md)
- 如需定價的詳細資訊，請參閱 [SQL Database 定價頁面](https://azure.microsoft.com/pricing/details/sql-database/)