---
title: Azure 資訊安全中心中的 SQL 資訊保護原則
description: 了解如何在 Azure 資訊安全中心自訂資訊保護原則。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2020
ms.author: memildin
ms.openlocfilehash: 0a487f778693e87e680033edd0d80c55d1a85f66
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013780"
---
# <a name="sql-information-protection-policy-in-azure-security-center"></a>Azure 資訊安全中心中的 SQL 資訊保護原則
 
SQL 資訊保護的 [資料探索和分類機制](../azure-sql/database/data-discovery-and-classification-overview.md) 提供探索、分類、標記和報告資料庫中敏感性資料的先進功能。 它內建于 [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)、 [Azure SQL 受控執行個體](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)和 [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)。

分類機制是以下列兩個元素為基礎：

- **標籤** -主要分類屬性，用來定義資料行中 *所儲存資料的敏感度層級* 。 
- **資訊類型** -提供資料行中所儲存 *資料類型的* 額外細微性。

資訊安全中心內的資訊保護原則選項提供一組預先定義的標籤和資訊類型，作為分類引擎的預設值。 您可以根據貴組織的需求自訂原則，如下所述。

> [!IMPORTANT]
> 若要自訂 Azure 租使用者的資訊保護原則，您需要租使用者根管理群組的系統管理許可權。 深入瞭解如何 [取得 Azure 資訊安全中心的全租使用者可見度](security-center-management-groups.md)。

:::image type="content" source="./media/security-center-info-protection-policy/sql-information-protection-policy-page.png" alt-text="顯示您的 SQL 資訊保護原則的頁面":::
 



## <a name="how-do-i-access-the-sql-information-protection-policy"></a>如何? 存取 SQL 資訊保護原則？

有三種方式可存取訊號保護原則：

- **(建議的)** 從安全性中心的 [定價和設定] 頁面
- 從安全性建議的「應該將您的 SQL 資料庫中的敏感性資料分類」
- 從 Azure SQL DB 資料探索頁面

上述各項都會顯示在下方的相關索引標籤中。



### <a name="from-security-centers-settings"></a>[**從安全性中心的設定**](#tab/sqlip-tenant)

### <a name="access-the-policy-from-security-centers-pricing-and-settings-page"></a>從安全性中心的 [定價和設定] 頁面存取原則 <a name="sqlip-tenant"></a>

從資訊安全中心的 [ **定價和設定** ] 頁面，選取 **[SQL 資訊保護**]。

> [!NOTE]
> 此選項只會針對具有租使用者層級許可權的使用者顯示。 

:::image type="content" source="./media/security-center-info-protection-policy/pricing-settings-link-to-information-protection.png" alt-text="從 Azure 資訊安全中心的 [定價和設定] 頁面存取 SQL 資訊保護原則":::



### <a name="from-security-centers-recommendation"></a>[**從安全性中心的建議**](#tab/sqlip-db)

### <a name="access-the-policy-from-the-security-center-recommendation"></a>從安全性中心建議存取原則 <a name="sqlip-db"></a>

使用「安全性中心」的建議：「您的 SQL 資料庫中的敏感性資料應進行分類」，以查看您資料庫的 [資料探索與分類] 頁面。 在那裡，您也會看到探索到的資料行包含我們建議您分類的資訊。

1. 在 [安全性中心的 **建議** ] 頁面中，搜尋 **您的 SQL 資料庫中應分類的建議機密資料**。

    :::image type="content" source="./media/security-center-info-protection-policy/sql-sensitive-data-recommendation.png" alt-text="尋找可存取 SQL 資訊保護原則的建議":::

1. 在 [建議詳細資料] 頁面中，從 **狀況良好** 或 **狀況不良** 的索引標籤中選取資料庫。

1. [ **資料探索 & 分類** ] 頁面隨即開啟。 選取 [設定]  。

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-security-center-recommendation.png" alt-text="從 Azure 資訊安全中心的相關建議開啟 SQL 資訊保護原則":::



### <a name="from-azure-sql"></a>[**從 Azure SQL**](#tab/sqlip-azuresql)

### <a name="access-the-policy-from-azure-sql"></a>從 Azure SQL 存取原則 <a name="sqlip-azuresql"></a>

1. 從 Azure 入口網站中，開啟 Azure SQL。

    :::image type="content" source="./media/security-center-info-protection-policy/open-azure-sql.png" alt-text="從 Azure 入口網站開啟 Azure SQL":::

1. 選取任何資料庫。

1. 從功能表的 [ **安全性** ] 區域中，開啟 **資料探索 & 分類** 頁面 (1) 然後選取 [ **設定** (2]) 。

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-azure-sql.png" alt-text="從 Azure SQL 開啟 SQL 資訊保護原則":::

--- 


## <a name="customize-your-information-types"></a>自訂您的資訊類型

若要管理和自訂資訊類型：

1. 選取 [ **管理資訊類型**]。

    :::image type="content" source="./media/security-center-info-protection-policy/manage-types.png" alt-text="管理資訊保護原則的資訊類型":::

1. 若要加入新的類型，請選取 [ **建立資訊類型**]。 您可以設定資訊類型的名稱、描述和搜尋模式字串。 搜尋模式字串可以選擇性地使用具有萬用字元 (使用 '%' 字元) 的關鍵字，而自動化探索引擎會使用它，以根據資料行的中繼資料來識別您資料庫中的敏感性資料。
 
    :::image type="content" source="./media/security-center-info-protection-policy/configure-new-type.png" alt-text="為您的資訊保護原則設定新的資訊類型":::

1. 您也可以藉由新增其他搜尋模式字串、停用一些現有的字串，或變更描述，來修改內建類型。 

    > [!TIP]
    > 您無法刪除內建類型或變更其名稱。 

1. **資訊類型** 會以遞增探索排名順序列出，這表示會先嘗試比對清單中較高的類型。 若要變更資訊類型之間的排名，請將類型拖曳至資料表中的正確位置，或使用 [Move up] \(上移\) 和 [Move down] \(下移\) 按鈕變更順序。 

1. 當您完成時，請選取 **[確定]** 。

1. 在您完成資訊類型的管理之後，請務必按一下特定標籤的 [設定]，並視需要新增或刪除資訊類型，以建立相關類型與相關標籤的關聯。

1. 若要套用變更，請選取 [主要 **標籤**] 頁面中的 [**儲存**]。
 

## <a name="exporting-and-importing-a-policy"></a>匯出和匯入原則

您可以使用已定義的標籤和資訊類型來下載 JSON 檔案，在您選擇的編輯器中編輯檔案，然後匯入更新的檔案。 

:::image type="content" source="./media/security-center-info-protection-policy/export-import.png" alt-text="匯出及匯入您的資訊保護原則":::

> [!NOTE]
> 您將需要租使用者層級的許可權，才能匯入原則檔。 


## <a name="manage-sql-information-protection-using-azure-powershell"></a>使用 Azure PowerShell 管理 SQL 資訊保護

- [AzSqlInformationProtectionPolicy：取得](/powershell/module/az.security/get-azsqlinformationprotectionpolicy)有效的租使用者 SQL 資訊保護原則。
- [AzSqlInformationProtectionPolicy](/powershell/module/az.security/set-azsqlinformationprotectionpolicy)：設定有效的租使用者 SQL 資訊保護原則。
 

## <a name="next-steps"></a>後續步驟
 
在本文中，您已瞭解如何在 Azure 資訊安全中心中定義資訊保護原則。 若要深入了解如何使用 SQL Information Protection 分類和保護 SQL 資料庫中的敏感性資料，請參閱 [Azure SQL Database 的資料探索與分類](../azure-sql/database/data-discovery-and-classification-overview.md)。

如需資訊安全中心的安全性原則和資料安全性的詳細資訊，請參閱下列文章：
 
- [在 Azure 資訊安全中心中設定安全性原則](tutorial-security-policy.md)：瞭解如何為您的 Azure 訂用帳戶和資源群組設定安全性原則
- [Azure 資訊安全中心資料安全性](security-center-data-security.md)：瞭解安全中心如何管理和保護資料
