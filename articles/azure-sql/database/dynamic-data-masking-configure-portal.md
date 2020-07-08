---
title: Azure 入口網站：動態資料遮罩
description: 如何開始使用 Azure 入口網站中的 Azure SQL Database 動態資料遮罩
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/28/2020
ms.openlocfilehash: 12a187b29737ba9388d9b33ac8bcfedf1339c059
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253778"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>透過 Azure 入口網站開始使用 SQL Database 動態資料遮罩
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文說明如何使用 Azure 入口網站來實作[動態資料遮罩](dynamic-data-masking-overview.md)。 您也可以使用 [Azure SQL Database Cmdlet](https://docs.microsoft.com/powershell/module/az.sql/) 或 [REST API](https://docs.microsoft.com/rest/api/sql/) 來實作動態資料遮罩。

> [!NOTE]
> 您無法使用 Azure Synapse （使用 PowerShell 或 REST API）或 SQL 受控執行個體的入口網站來設定這項功能。 如需相關資訊，請參閱 [Dynamic Data Masking](/sql/relational-databases/security/dynamic-data-masking)。

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>使用 Azure 入口網站為您的資料庫設定動態資料遮罩

1. 啟動 Azure 入口網站，網址為 [https://portal.azure.com](https://portal.azure.com) 。
2. 導覽至您要遮罩處理的敏感性資料所在資料庫的設定頁面。
3. 在資料庫的 [**安全性**] 區段下，按一下 [**動態資料遮罩**] 分頁。

   ![瀏覽窗格](./media/dynamic-data-masking-configure-portal/4_ddm_settings_tile.png)

4. 在 [動態資料遮罩]**** 設定頁面中，您可能會看到建議引擎已標示要進行遮罩處理的某些資料庫資料行。 若要接受建議，只要對一或多個資料行按一下 [新增遮罩]**** ，就會根據此資料行的預設類型建立遮罩。 您可按一下遮罩規則並編輯遮罩欄位格式，使其成為您選擇的不同格式，即可變更遮罩函數。 務必按一下 [儲存] **** 以儲存您的設定。

    ![瀏覽窗格](./media/dynamic-data-masking-configure-portal/5_ddm_recommendations.png)

5. 若要為資料庫中的任何資料行新增遮罩，請在 [動態資料遮罩]**** 設定頁面的頂端，按一下 [新增遮罩]**** 以開啟 [新增遮罩規則]**** 設定頁面。

    ![瀏覽窗格](./media/dynamic-data-masking-configure-portal/6_ddm_add_mask.png)

6. 請選取 [結構描述]****、[資料表]**** 和 [資料行]****，以定義要用於遮罩處理的指定欄位。
7. 從敏感性資料遮罩類別清單中**選取 [如何遮罩**]。

    ![瀏覽窗格](./media/dynamic-data-masking-configure-portal/7_ddm_mask_field_format.png)

8. 按一下 [資料遮罩規則] 頁面中的 [**新增**]，以更新動態資料遮罩原則中的遮罩規則集。
9. 輸入應該排除在遮罩外的 SQL 使用者或 Azure Active Directory （Azure AD）身分識別，並可存取未遮罩的敏感性資料。 這應該是以分號分隔的使用者清單。 具有系統管理員權限的使用者永遠都有未經遮罩處理之原始資料的存取權。

    ![瀏覽窗格](./media/dynamic-data-masking-configure-portal/8_ddm_excluded_users.png)

    > [!TIP]
    > 若要讓應用層能夠顯示應用程式特殊許可權使用者的敏感性資料，請新增應用程式用來查詢資料庫的 SQL 使用者或 Azure AD 身分識別。 強烈建議此清單應包含最少的特殊權限使用者數目，以儘可能減少公開的敏感性資料。

10. 按一下資料遮罩組態頁面中的 [儲存]****，以儲存新的或更新的遮罩原則。

## <a name="next-steps"></a>後續步驟

- 如需動態資料遮罩的概觀，請參閱[動態資料遮罩](dynamic-data-masking-overview.md)。
- 您也可以使用 [Azure SQL Database Cmdlet](https://docs.microsoft.com/powershell/module/az.sql/) 或 [REST API](https://docs.microsoft.com/rest/api/sql/) 來實作動態資料遮罩。
