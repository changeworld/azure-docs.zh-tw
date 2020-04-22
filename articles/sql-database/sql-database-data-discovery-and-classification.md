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
ms.date: 04/21/2020
tags: azure-synapse
ms.openlocfilehash: f05b4d4fec99aaa2fb79da46e2167d883d1f15ec
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766961"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL 資料庫和 Azure 突觸分析的資料發現&分類

數據發現&分類內置於 Azure SQL 資料庫中。 它提供了用於發現、分類、標記和報告資料庫中敏感數據的高級功能。

您最敏感的數據可能包括業務、財務、醫療保健或個人資訊。 發現和分類此數據可以在組織的資訊保護方法中發揮關鍵作用。 它可以作為下列的基礎結構：

- 幫助滿足數據隱私標準和法規遵從性要求。
- 各種安全性案例，例如針對敏感性資料異常存取的監視 (稽核) 及警示。
- 控制對包含高度敏感數據的資料庫的訪問並強化安全性。

數據發現&分類是[高級數據安全](sql-database-advanced-data-security.md)產品(用於高級 SQL 安全功能的統一包)的一部分。 您可以通過 Azure 門戶的中央**SQL 高級數據安全**部分訪問和管理數據發現&分類。

> [!NOTE]
> 本文涉及 Azure SQL 資料庫和 Azure 突觸分析。 為簡單起見,我們在此處使用*SQL 資料庫*來引用 SQL 資料庫和 Azure 突觸。 有關 SQL 伺服器 (本地)的資訊,請參考[SQL 資料發現與分類](https://go.microsoft.com/fwlink/?linkid=866999)。

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>什麼是數據發現&分類?

數據發現&分類引入了一組高級服務和新的SQL資料庫功能。 它為 SQL 資料庫形成了一種新的資訊保護範例,旨在保護數據,而不僅僅是資料庫。 範例包括:

- **找到與建議:** 分類引擎掃描資料庫並標識包含潛在敏感數據的列。 然後,它提供了一種通過 Azure 門戶查看和應用建議分類的簡單方法。

- **標籤:** 通過使用已添加到 SQL 資料庫引擎的新元數據屬性,可以持久地將敏感度分類標籤應用於列。 然後,此元數據可用於高級、基於靈敏度的審核和保護方案。

- **查詢結果集敏感度:** 查詢結果集的靈敏度是即時計算的,以便進行審核。

- **可見度:** 您可以在 Azure 門戶中的詳細儀表板中查看資料庫分類狀態。 此外,您還可以下載 Excel 格式的報告,用於合規性和審核目的以及其他需求。

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>找到、分類和標記敏感欄位

本節介紹以下步驟:

- 發現、分類和標記資料庫中包含敏感數據的列。
- 查看資料庫的當前分類狀態並匯出報表。

分類包含兩個中繼資料屬性：

- **標籤**:用於定義存儲在列中的數據的敏感度級別的主要分類屬性。  
- **資訊類型**:提供有關列中存儲的數據類型的更精細資訊的屬性。

### <a name="define-and-customize-your-classification-taxonomy"></a>定義及自訂您的類別分類法

數據發現&分類附帶一組內置的敏感度標籤和一組內置的資訊類型和發現邏輯。 您現在可以自訂此分類法，並定義專門針對您的環境建構之類別的集合和順位。

在整個 Azure 組織的一個中心位置定義和自定義分類分類。 該位置位於[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)中,作為安全策略的一部分。 只有具有組織根管理組管理許可權的人員才能執行此任務。

作為 SQL 資訊保護原則管理的一部分,您可以定義自訂標籤、對它們進行排名,並將它們與一組選定的資訊類型相關聯。 您還可以添加自己的自定義資訊類型,並配置它們與字串模式。 模式將添加到發現邏輯中,用於標識資料庫中的此類數據。

在[SQL 資訊保護原則「指南」](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409)中瞭解有關自定義和管理原則的詳細資訊。

定義全組織策略后,可以使用自定義策略繼續對各個資料庫進行分類。

### <a name="classify-your-sql-database"></a>SQL 資料庫進行類別

1. 移至 [Azure 入口網站](https://portal.azure.com)。

2. 跳到 Azure SQL 資料庫窗格中 **「安全**」標題下的 **「進階資料安全**」。 選擇**進階資料安全性**,然後選擇 **「資料發現&分類**卡。

   ![Azure 門戶中的進階資料安全窗格](./media/sql-data-discovery-and-classification/data_classification.png)

3. 在 **「資料發現&分類**頁上,」**概述「** 選項卡包含資料庫當前分類狀態的摘要。 摘要包括所有分類列的詳細清單,您還可以篩選該清單以僅顯示特定的架構部件、資訊類型和標籤。 如果尚未對任何欄位進行類別,[請跳到步驟 5](#step-5)。

   ![目前分類狀態的摘要](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. 要下載 Excel 格式的報表,請在窗格的頂部功能表中選擇 **「匯出**」。

5. <a id="step-5"></a>要開始對資料進行分類,請選擇 **「資料發現&分類**頁上的 **」分類」** 選項卡。

    分類引擎掃描資料庫,以尋找包含潛在敏感數據的列,並提供建議的列分類清單。

6. 檢視並套用類別建議:

   - 要查看建議的列分類清單,請選擇窗格底部的建議面板。

   - 要接受特定列的建議,請選擇相關行左列中的複選框。 要將所有建議標記為已接受,請在建議表標題中選擇最左側的複選框。

       ![從分類建議清單中查看與選擇](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - 要應用所選建議,請選擇"**接受所選建議**"。

7. 您還可以手動對列進行分類,作為替代分類,或者除基於建議的分類之外:

   1. 在窗格的頂部功能表中選擇 **「添加分類**」。

   1. 在打開的上下文視窗中,選擇要分類的架構、表和列以及資訊類型和敏感度標籤。

   1. 選擇 **「在**上下文視窗底部添加分類」。

      ![選擇要分類的欄位](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. 要使用新的分類元資料完成分類並持續標記資料庫列,請選擇「**保存在**視窗的頂部功能表」。

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>稽核資料存取

信息保護範式的一個重要方面是能夠監視對敏感數據的訪問。 [Azure SQL 資料庫審核](sql-database-auditing.md)已得到增強,以在審核日誌中包括`data_sensitivity_information`一個名為 的新欄位。 此欄位記錄查詢返回的數據的敏感度分類(標籤)。 以下是範例：

![稽核記錄檔](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>權限

這些內建角色可以讀取 Azure SQL 資料庫的資料類別:

- 擁有者
- 讀取者
- 參與者
- SQL 安全性管理員
- 使用者存取系統管理員

這些內建角色可以修改 Azure SQL 資料庫的資料類別:

- 擁有者
- 參與者
- SQL 安全性管理員

詳細瞭解[Azure 資源的 RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)中基於角色的許可權。

## <a name="manage-classifications"></a><a id="manage-classification"></a>管理分類

您可以使用 TSQL、REST API 或 PowerShell 來管理分類。

### <a name="use-t-sql"></a>使用 T-SQL

可以使用 T-SQL 添加或刪除列分類,並檢索整個資料庫的所有分類。

> [!NOTE]
> 使用 T-SQL 管理標籤時,無法驗證添加到列中的標籤存在於組織的資訊保護策略(門戶建議中顯示的標籤集)中。 因此,由您來驗證這一點。

有關將 T-SQL 用於分類的資訊,請參閱以下引用:

- 要新增或更新一個或多個欄的類別:[加入敏感類別](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- 要從一個或多個欄中移除類別[:DROP 敏感分類](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- 要檢視資料庫上的所有類別[:sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-the-rest-api"></a>使用休息 API

您可以使用 REST API 以程式設計方式管理分類和建議。 已發布的 REST API 支援以下操作:

- [建立或更新](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate):創建或更新指定列的敏感度標籤。
- [刪除](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete):刪除指定列的敏感度標籤。
- [禁用建議](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation):禁用指定列上的敏感度建議。
- [啟用建議](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation):啟用指定列上的敏感度建議。 (默認情況下在所有列上啟用建議。
- [獲取](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get):獲取指定列的敏感度標籤。
- [依資料庫列出當前](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase)值值 :獲取指定資料庫的當前敏感度標籤。
- [依資料庫推薦清單](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase):獲取指定資料庫的建議敏感度標籤。

### <a name="use-powershell-cmdlets"></a>使用 PowerShell Cmdlet
可以使用 PowerShell 管理 Azure SQL 資料庫和託管實例的分類和建議。

#### <a name="powershell-cmdlets-for-sql-database"></a>SQL 資料庫的 PowerShell cmdlet

- [取得-AzSql資料庫敏感性分類](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [設定-AzSql資料庫敏感性分類](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [刪除-AzSql資料庫敏感性分類](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [取得-AzSql資料庫敏感性建議](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [開啟-AzSqlDatabaSe敏感性建議](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [關閉-AzSql資料庫敏感性建議](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>管理管實體的 PowerShell cmdlet

- [取得-AzSqlinstance 資料庫敏感性分類](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [集-AzSql實例資料庫敏感性分類](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [刪除-AzSql實體資料庫敏感性分類](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [取得-AzSqlinstance 資料庫敏感性建議](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [開啟-AzSqlinstance 資料庫敏感性建議](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [關閉-AzSqlinstance 資料庫敏感性建議](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>後續步驟

- 瞭解有關[高級數據安全性](sql-database-advanced-data-security.md)的更多。
- 請考慮設定 [Azure SQL Database 稽核](sql-database-auditing.md)，以監視和稽核對已分類敏感性資料的存取。
- 有關包含數據發現和分類的演示文稿,請參閱[發現、分類、標記&保護 SQL 數據 |資料暴露](https://www.youtube.com/watch?v=itVi9bkJUNc)。
