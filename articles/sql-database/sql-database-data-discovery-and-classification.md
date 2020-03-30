---
title: 資料探索與分類
description: Azure SQL 資料庫和 Azure 突觸分析的資料發現&分類
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 98b21969ce86f84b20fbe78dbdf3002769ef65f5
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387864"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL 資料庫和 Azure 突觸分析的資料發現&分類

資料發現&分類提供了內置於 Azure SQL 資料庫中的高級功能，用於**發現**、**分類**和**標記** & **報告**資料庫中的敏感性資料。

對於最具敏感性的資料 (商業、財務、醫療保健與個人識別資料 (PII) 等) 進行探索與分類，在組織的資訊保護方面扮演著關鍵角色。 它可以作為下列的基礎結構：

- 協助符合資料隱私標準和法規合規性需求。
- 各種安全性案例，例如針對敏感性資料異常存取的監視 (稽核) 及警示。
- 控制對包含高度敏感性資料之資料庫的存取，並強化安全性。

資料發現&分類是[高級資料安全](sql-database-advanced-data-security.md)（ADS） 產品（用於高級 SQL 安全功能的統一包）的一部分。 資料探索與分類可以透過中央 SQL ADS 入口網站存取及管理。

> [!NOTE]
> 本文檔與 Azure SQL 資料庫和 Azure 突觸有關。 為簡單起見，SQL 資料庫在引用 SQL 資料庫和 Azure 突觸時使用。 有關 SQL 伺服器（內部部署），請參閱[SQL 資料發現和分類](https://go.microsoft.com/fwlink/?linkid=866999)。

## <a name="what-is-data-discovery--classification"></a><a id="subheading-1"></a>什麼是資料探索與分類

資料探索與分類導入一組進階服務和新的 SQL 功能，構成目標是保護資料而不只是資料庫的全新 SQL 資訊保護典範：

- **探索與建議**

  分類引擎會掃描您的資料庫，並識別包含可能是敏感性資料的資料行。 接著，它能提供輕鬆的方式，讓您透過 Azure 入口網站檢閱並套用適當的分類建議。

- **標記**

  使用導入 SQL 引擎的全新分類中繼資料屬性，可以在資料行上持續標記敏感性分類標籤。 此中繼資料還可利用在進階的敏感性稽核和保護案例上。

- **查詢結果集敏感度**

  為執行稽核，查詢結果集的敏感度是即時計算的。

- **知名度**

  資料庫分類狀態可以在入口網站的詳細儀表板中檢視。 此外，您可以下載 Excel 格式的報表以用於合規性、稽核用途及其他需求。

## <a name="discover-classify--label-sensitive-columns"></a><a id="subheading-2"></a>針對機密資料行進行探索、分類與設定標籤

下節所描述的步驟是有關探索、分類和標示包含您資料庫中敏感性資料的資料行，以及檢視資料庫的目前分類狀態，並匯出報表。

分類包含兩個中繼資料屬性：

- 標籤 - 主分類屬性，用來定義資料行中所儲存資料的敏感度等級。  
- 資訊類型 - 提供資料行中所儲存資料類型的額外細微性。

## <a name="define-and-customize-your-classification-taxonomy"></a>定義及自訂您的類別分類法

資料發現&分類附帶一組內置的敏感度標籤和一組內置的資訊類型和發現邏輯。 您現在可以自訂此分類法，並定義專門針對您的環境建構之類別的集合和順位。

類別分類法的定義及自訂會在您整個 Azure 租用戶的一個集中位置完成。 該位置位於 [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)中，做為您的安全性原則的一部分。 只有具備租用戶根管理群組系統管理權限的人可以執行此工作。

作為資訊保護原則管理的一部分，您可以定義自訂標籤、對它們進行排名，並將它們與一組選取的資訊類型相關聯。 您也可以新增自己的自訂資訊類型，並使用字串模式對其進行設定，字串模式將新增至探索邏輯中，以便在資料庫中識別此類型的資料。
在[資訊保護原則操作指南](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409) \(英文\) 中深入了解有關自訂及管理您原則的詳細資訊。

一旦定義整個租用戶的原則，您就可以使用您的自訂原則，繼續對個別資料庫進行分類。

## <a name="classify-your-sql-database"></a>將您的 SQL Database 分類

1. 轉到[Azure 門戶](https://portal.azure.com)。

2. 瀏覽至 [Azure SQL Database] 窗格中 [安全性] 標題下的 [進階資料安全性]****。 按一下以啟用高級資料安全性，然後按一下**資料發現&分類**卡。

   ![掃描資料庫](./media/sql-data-discovery-and-classification/data_classification.png)

3. [概觀]**** 索引標籤包含資料庫目前分類狀態的摘要，包括所有已分類資料行的詳細清單，您也可以篩選這些資料行來只檢視特定的結構描述組件、資訊類型與標籤。 如果您尚未分類任何資料行，請[跳至步驟 5](#step-5)。

   ![目前分類狀態的摘要](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. 若要下載 Excel 格式的報表，請按一下視窗頂端功能表中的 [匯出]**** 選項。

5. <a id="step-5"></a>若要開始分類資料，請按一下視窗頂端的 [分類]**** 索引標籤。

6. 分類引擎掃描資料庫，以尋找包含潛在敏感性資料的列，並提供**建議的列分類**清單。 檢視並套用分類建議：

   - 要查看建議的列分類清單，請按一下視窗底部的建議面板

   - 檢閱建議清單：若要接受針對特定資料行的建議，請選取相關資料列左側資料行中的核取方塊。 您也可以核取建議資料表標頭中的核取方塊，以將「所有建議」** 標記為已接受。

       ![檢閱建議清單](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - 若要套用選取的建議，請按一下藍色的 [Accept selected recommendations]\(接受選取的建議)**** 按鈕。若要套用選取的建議，請按一下藍色的 接受選取的建議按鈕。

7. 您也可以**手動分類**資料行作為 (以及) 建議分類的替代方法：

   - 按一下視窗上方功能表中的 [新增分類]****。

   - 在開啟的內容視窗中，選取結構描述 > 資料表 > 您想要分類的資料行，以及資訊類型和敏感度標籤。 然後按一下內容視窗底部的藍色 [新增分類]**** 按鈕。

      ![選取要分類的資料行](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. 若要完成您的分類，並使用新的分類中繼資料持續標示 (標記) 資料庫資料行，請按一下視窗上方功能表中的 [儲存]****。

## <a name="auditing-access-to-sensitive-data"></a><a id="subheading-3"></a>對敏感性資料的存取進行稽核

資訊保護範例的重要層面是能夠監視對敏感性資料的存取。 我們已強化 [Azure SQL Database 稽核](sql-database-auditing.md)，並在稽核記錄中包含新欄位 *data_sensitivity_information*，其中會記錄查詢所傳回之實際資料的敏感度分類 (標籤)。

![稽核記錄檔](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="subheading-4"></a>許可權

以下內置角色可以讀取 Azure SQL 資料庫的資料`Owner`分類： 、 `Reader` `Contributor` `SQL Security Manager`和`User Access Administrator`。

以下內置角色可以修改 Azure SQL 資料庫的資料分類： `Owner` `Contributor`。 。 `SQL Security Manager`

瞭解有關[Azure 資源 RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)詳細資訊

## <a name="manage-classifications"></a><a id="subheading-5"></a>管理分類

# <a name="t-sql"></a>[T-SQL](#tab/azure-t-sql)
您可以使用 T-SQL 新增/移除資料行分類，以及擷取整個資料庫的所有分類。

> [!NOTE]
> 使用 T-SQL 管理標籤時，系統不會驗證新增到資料行的標籤是否存在於組織資訊保護原則 (顯示於入口網站建議中的那組標籤)。 因此，這項驗證需由您執行。

- 新增/更新一或多個資料行的分類：[ADD SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- 從一或多個資料行移除分類：[DROP SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- 檢視資料庫上的所有分類：[sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

# <a name="rest-apis"></a>[休息 API](#tab/azure-rest-api)
您可以使用 REST API 以程式設計方式管理分類和建議。 已發行的 REST API 支援下列作業：

- [創建或更新](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate)- 創建或更新給定列的敏感度標籤
- [刪除](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) - 刪除指定資料行的敏感度標籤
- [禁用建議](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation)- 禁用給定列上的敏感度建議
- [啟用建議](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation)- 啟用給定列的敏感度建議（預設情況下在所有列上啟用建議）
- [取得](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) - 取得指定資料行的敏感度標籤
- [依資料庫列出目前的標籤](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) \(英文\) - 取得指定資料庫目前的敏感度標籤
- [按資料庫推薦的清單](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase)- 獲取給定資料庫的建議敏感度標籤

# <a name="powershell-cmdlet"></a>[PowerShell Cmdlet](#tab/azure-powelshell)
可以使用 PowerShell 管理 Azure SQL 資料庫和託管實例的分類和建議。

### <a name="powershell-cmdlet-for-azure-sql-database"></a>用於 Azure SQL 資料庫的 PowerShell Cmdlet
- [獲取-AzSql資料庫敏感性分類](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [設置-AzSql資料庫敏感性分類](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [刪除-AzSql資料庫敏感性分類](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [獲取-AzSql資料庫敏感性建議](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [啟用-AzSqlDatabaSe敏感性建議](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [禁用-AzSql資料庫敏感性建議](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>用於託管實例的 PowerShell Cmdlet
- [獲取-AzSqlinstance 資料庫敏感性分類](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [集-AzSql實例資料庫敏感性分類](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [刪除-AzSql實例資料庫敏感性分類](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [獲取-AzSqlinstance 資料庫敏感性建議](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [啟用-AzSqlinstance 資料庫敏感性建議](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [禁用-AzSqlinstance 資料庫敏感性建議](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

---

## <a name="next-steps"></a><a id="subheading-6"></a>後續步驟

- 瞭解有關[高級資料安全性](sql-database-advanced-data-security.md)的更多。
- 請考慮設定 [Azure SQL Database 稽核](sql-database-auditing.md)，以監視和稽核對已分類敏感性資料的存取。
- 有關包含資料發現&分類的 YouTube 演示文稿，請參閱[發現、分類、標記&保護 SQL 資料 |資料暴露](https://www.youtube.com/watch?v=itVi9bkJUNc)。

<!--Anchors-->
[What is data discovery & classification]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Permissions]: #subheading-4
[Manage classifications]: #subheading-5
[Next Steps]: #subheading-6
