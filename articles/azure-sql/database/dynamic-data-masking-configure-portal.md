---
title: Azure 入口網站：動態資料遮罩
description: 如何開始在 Azure 入口網站中使用 Azure SQL Database 動態資料遮罩
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/28/2020
ms.openlocfilehash: 1d4c29cdb9cf24f87df4acc3556b318a1079810a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91443611"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>透過 Azure 入口網站開始使用 SQL Database 動態資料遮罩
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文說明如何使用 Azure 入口網站來實作[動態資料遮罩](dynamic-data-masking-overview.md)。 您也可以使用 [Azure SQL Database Cmdlet](https://docs.microsoft.com/powershell/module/az.sql/) 或 [REST API](https://docs.microsoft.com/rest/api/sql/) 來實作動態資料遮罩。

> [!NOTE]
> 您無法使用 Azure Synapse 的入口網站來設定此功能 (使用 PowerShell 或 REST API) 或 SQL 受控執行個體。 如需相關資訊，請參閱 [Dynamic Data Masking](/sql/relational-databases/security/dynamic-data-masking)。

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>使用 Azure 入口網站為您的資料庫設定動態資料遮罩

1. 啟動 Azure 入口網站 [https://portal.azure.com](https://portal.azure.com) 。
2. 導覽至您要遮罩處理的敏感性資料所在資料庫的設定頁面。
3. 在資料庫的 [**安全性**] 區段下，按一下 [**動態資料遮罩**] 分頁。

   ![顯示 [安全性] 區段並醒目提示動態資料遮罩的螢幕擷取畫面。](./media/dynamic-data-masking-configure-portal/4_ddm_settings_tile.png)

4. 在 [動態資料遮罩]**** 設定頁面中，您可能會看到建議引擎已標示要進行遮罩處理的某些資料庫資料行。 若要接受建議，只要對一或多個資料行按一下 [新增遮罩]**** ，就會根據此資料行的預設類型建立遮罩。 您可按一下遮罩規則並編輯遮罩欄位格式，使其成為您選擇的不同格式，即可變更遮罩函數。 務必按一下 [儲存] **** 以儲存您的設定。

    ![顯示動態資料遮罩設定頁面的螢幕擷取畫面。](./media/dynamic-data-masking-configure-portal/5_ddm_recommendations.png)

5. 若要為資料庫中的任何資料行新增遮罩，請在 [動態資料遮罩]**** 設定頁面的頂端，按一下 [新增遮罩]**** 以開啟 [新增遮罩規則]**** 設定頁面。

    ![顯示 [新增遮罩規則設定] 頁面的螢幕擷取畫面。](./media/dynamic-data-masking-configure-portal/6_ddm_add_mask.png)

6. 請選取 [結構描述]****、[資料表]**** 和 [資料行]****，以定義要用於遮罩處理的指定欄位。
7. 從機密資料遮罩類別清單中**選取 [遮罩**]。

    ![螢幕擷取畫面，顯示 [選取如何遮罩] 區段底下的 [機密資料遮罩] 類別。](./media/dynamic-data-masking-configure-portal/7_ddm_mask_field_format.png)

8. 按一下 [資料遮罩規則] 頁面中的 [ **新增** ]，以更新動態資料遮罩原則中的遮罩規則集。
9. 輸入 SQL 使用者或 Azure Active Directory (Azure AD 應從遮罩排除的) 身分識別，並可存取未遮罩的機密資料。 這應該是以分號分隔的使用者清單。 具有系統管理員權限的使用者永遠都有未經遮罩處理之原始資料的存取權。

    ![瀏覽窗格](./media/dynamic-data-masking-configure-portal/8_ddm_excluded_users.png)

    > [!TIP]
    > 若要使應用層可顯示應用程式特殊許可權使用者的機密資料，請新增 SQL 使用者或應用程式用來查詢資料庫的 Azure AD 身分識別。 強烈建議此清單應包含最少的特殊權限使用者數目，以儘可能減少公開的敏感性資料。

10. 按一下資料遮罩組態頁面中的 [儲存]****，以儲存新的或更新的遮罩原則。

## <a name="next-steps"></a>後續步驟

- 如需動態資料遮罩的概觀，請參閱[動態資料遮罩](dynamic-data-masking-overview.md)。
- 您也可以使用 [Azure SQL Database Cmdlet](https://docs.microsoft.com/powershell/module/az.sql/) 或 [REST API](https://docs.microsoft.com/rest/api/sql/) 來實作動態資料遮罩。
