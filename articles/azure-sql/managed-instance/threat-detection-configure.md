---
title: 設定進階威脅防護
titleSuffix: Azure SQL Managed Instance
description: 先進的威脅防護會偵測異常資料庫活動，指出 Azure SQL 受控執行個體中的資料庫有潛在的安全性威脅。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: ceb6285448df2a5d87dfa87ab249c99bf22c9928
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84686319"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>在 Azure SQL 受控執行個體中設定先進的威脅防護
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[AZURE SQL 受控執行個體](sql-managed-instance-paas-overview.md)的[先進威脅防護](../database/threat-detection-overview.md)會偵測異常活動，指出不尋常且可能有害的嘗試存取或惡意探索資料庫。 先進的威脅防護可以**識別潛在的 SQL 插入**式攻擊、**來自不尋常位置或資料中心的存取**、來自不**熟悉主體或潛在有害應用程式的存取權**，以及**暴力密碼破解 SQL 認證**-請參閱[先進的威脅防護警示](../database/threat-detection-overview.md#alerts)中的詳細資料。

您可以透過[電子郵件通知](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event)或 [Azure 入口網站](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal)接收偵測到威脅的通知

[先進的威脅防護](../database/threat-detection-overview.md)是先進的[資料安全性](../database/advanced-data-security.md)供應專案的一部分，它是先進 SQL 安全性功能的整合套件。 進階威脅防護可以透過中央 SQL ADS 入口網站存取及管理。

##  <a name="azure-portal"></a>Azure 入口網站

1. 登入[Azure 入口網站](https://portal.azure.com)。 
2. 流覽至您想要保護之 SQL 受控執行個體實例的 [設定] 頁面。 在 [**設定**] 頁面中，選取 [ **Advanced Data Security**]。
3. 在 [Advanced Data Security 設定] 頁面中
   - 開啟 **[Advanced Data** Security]。
   - 設定在偵測到異常資料庫活動時收到安全性警示的**電子郵件清單**。
   - 選取儲存異常威脅稽核記錄的 [Azure 儲存體帳戶]****。
   - 選取您想要設定的 [**先進的威脅防護類型**]。 深入瞭解[先進的威脅防護警示](../database/threat-detection-overview.md)。
4. 按一下 [**儲存**] 以儲存新的或已更新的「先進資料」安全性原則。

   ![進階威脅防護](./media/threat-detection-configure/threat-detection.png)


## <a name="next-steps"></a>後續步驟

- 深入瞭解[先進的威脅防護](../database/threat-detection-overview.md)。
- 若要瞭解受控實例，請參閱[什麼是 AZURE SQL 受控執行個體](sql-managed-instance-paas-overview.md)。
- 深入瞭解[Azure SQL Database 的先進威脅防護](../database/threat-detection-configure.md)。
- 深入瞭解[SQL 受控執行個體的審核](https://go.microsoft.com/fwlink/?linkid=869430)。
- 深入瞭解[Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)。
