---
title: 資料探索與分類
description: Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse Analytics 的資料探索 & 分類
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
titleSuffix: Azure SQL Database, Azure SQL Managed Instance, and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 12/01/2020
tags: azure-synapse
ms.openlocfilehash: ece76f0017a72484f5c4e66b2ab8c7e71d32f1a7
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132015"
---
# <a name="data-discovery--classification"></a>資料探索與分類
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

資料探索 & 分類內建于 Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse Analytics。 其提供在資料庫中探索、分類、標記及報告敏感性資料的進階功能。

您最敏感的資料可能包括商務、財務、醫療保健或個人資訊。 探索和分類此資料可以在組織的資訊保護方法中扮演 pivotal 角色。 它可以作為以下的基礎結構：

- 協助符合法規合規性的資料隱私權和需求標準。
- 各種安全性案例，例如監視 (的) 存取機密資料。
- 控制對包含高度敏感性資料之資料庫的存取和強化安全性。

> [!NOTE]
> 如需 SQL Server 內部部署的相關資訊，請參閱 [SQL 資料探索 & 分類](/sql/relational-databases/security/sql-data-discovery-and-classification)。

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>什麼是資料探索 & 分類？

資料探索 & 分類引進一組先進的服務和 Azure 中的新功能。 它會形成 SQL Database、SQL 受控執行個體和 Azure Synapse 的新資訊保護範例，目的是要保護資料，而不只是資料庫。 此範例包含：

- **探索和建議：** 分類引擎會掃描您的資料庫，並識別包含潛在敏感性資料的資料行。 接著，它會提供簡單的方法，讓您透過 Azure 入口網站來檢查和套用建議的分類。

- 卷 **標：** 您可以使用已新增至 SQL Server database engine 的新中繼資料屬性，持續將敏感度分類標籤套用到資料行。 然後，此中繼資料可用於高階、以敏感度為基礎的審核和保護案例。

- **查詢結果集敏感度：** 查詢結果集的敏感度是為了進行審核而即時計算。

- **可見度：** 您可以在 Azure 入口網站的詳細儀表板中，查看資料庫分類狀態。 此外，您也可以下載 Excel 格式的報表以用於合規性和審核用途，以及其他需求。

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>探索、分類及標記敏感性資料行

本節說明的步驟如下：

- 探索、分類及標記包含資料庫中敏感性資料的資料行。
- 查看資料庫目前的分類狀態並匯出報表。

分類包含兩個中繼資料屬性：

- **標籤**：主要分類屬性，用來定義資料行中所儲存資料的敏感度層級。  
- **資訊類型**：提供有關資料行中所儲存資料類型之更細微資訊的屬性。

### <a name="define-and-customize-your-classification-taxonomy"></a>定義及自訂您的類別分類法

資料探索 & 分類隨附一組內建的敏感度標籤，以及一組內建的資訊類型和探索邏輯。 您現在可以自訂此分類法，並定義專門針對您的環境建構之類別的集合和順位。

您可以針對整個 Azure 組織，在一個集中位置定義和自訂分類分類法。 該位置會在 [Azure 資訊安全中心](../../security-center/security-center-introduction.md)中，作為安全性原則的一部分。 只有在組織的根管理群組中具有系統管理許可權的人員，才能進行這項工作。

作為資訊保護原則管理的一部分，您可以定義自訂標籤、對其進行排名，並將它們與一組選取的資訊類型建立關聯。 您也可以新增自己的自訂資訊類型，並使用字串模式進行設定。 這些模式會新增至探索邏輯中，以便在您的資料庫中識別此類型的資料。

如需詳細資訊，請參閱 [Azure 資訊安全中心 (Preview) 中自訂 SQL 資訊保護原則 ](../../security-center/security-center-info-protection-policy.md)。

在定義整個組織的原則之後，您可以使用自訂原則繼續分類個別的資料庫。

### <a name="classify-your-database"></a>分類資料庫

> [!NOTE]
> 下列範例會使用 Azure SQL Database，但您應該選取要設定資料探索 & 分類的適當產品。

1. 移至 [Azure 入口網站](https://portal.azure.com)。

1. 在 [Azure SQL Database] 窗格中，移至 [**安全性**] 標題下的 [**資料探索 & 分類**]。 [總覽] 索引標籤包含資料庫目前分類狀態的摘要。 摘要中包含所有已分類資料行的詳細清單，您也可以篩選這些資料行，只顯示特定的架構部分、資訊類型及標籤。 如果您還沒有分類任何資料行，請 [跳至步驟 4](#step-4)。

    ![概觀](./media/data-discovery-and-classification-overview/data-discovery-and-classification.png)

1. 若要下載 Excel 格式的報表，請在窗格的上方功能表中選取 [ **匯出** ]。

1. <a id="step-4"></a>若要開始分類您的資料，請選取 [**資料探索 & 分類**] 頁面上的 [**分類**] 索引標籤。

    分類引擎會掃描您的資料庫中是否有包含潛在敏感性資料的資料行，並提供建議的資料行分類清單。

1. 查看並套用分類建議：

   - 若要查看建議的資料行分類清單，請選取窗格底部的 [建議] 面板。

   - 若要接受特定資料行的建議，請選取相關資料列左側資料行中的核取方塊。 若要將所有建議標記為 [已接受]，請選取 [建議] 表格標頭中最左邊的核取方塊。

   - 若要套用選取的建議，請選取 [ **接受選取的建議**]。

   ![分類的建議](./media/data-discovery-and-classification-overview/recommendation.png)

1. 您也可以手動將資料行分類為替代方式，或除了以建議為基礎的分類之外：

   1. 選取窗格上方功能表中的 [ **新增分類** ]。

   1. 在開啟的內容視窗中，選取您要分類的架構、資料表和資料行，以及資訊類型和敏感度標籤。

   1. 選取內容視窗底部的 [ **新增分類** ]。

   ![手動新增分類](./media/data-discovery-and-classification-overview/manually-add-classification.png)


1. 若要完成分類，並持續以新的分類中繼資料來標記 (標籤) 資料庫資料行，請選取 [**分類**] 頁面中的 [**儲存**]。

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>對敏感性資料的審核存取

資訊保護範例的重要層面，就是監視機密資料存取的能力。 [AZURE SQL 審核](../../azure-sql/database/auditing-overview.md) 已增強，可在名為的 audit 記錄中包含新的欄位 `data_sensitivity_information` 。 此欄位會記錄查詢所傳回的資料 (標籤) 的敏感度分類。 以下為範例：

![稽核記錄](./media/data-discovery-and-classification-overview/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Permissions

這些內建角色可以讀取資料庫的資料分類：

- 擁有者
- 讀者
- 參與者
- SQL 安全性管理員
- 使用者存取系統管理員

這些內建角色可以修改資料庫的資料分類：

- 擁有者
- 參與者
- SQL 安全性管理員

深入瞭解 [AZURE RBAC](../../role-based-access-control/overview.md)中以角色為基礎的許可權。

## <a name="manage-classifications"></a><a id="manage-classification"></a>管理分類

您可以使用 T-sql、REST API 或 PowerShell 來管理分類。

### <a name="use-t-sql"></a>使用 T-SQL

您可以使用 T-sql 來新增或移除資料行分類，以及取得整個資料庫的所有分類。

> [!NOTE]
> 當您使用 T-sql 來管理標籤時，不會驗證您新增至資料行的標籤是否存在於組織的資訊保護原則中， (出現在入口網站建議) 中的標籤集合。 因此，您可以自行驗證這一點。

如需使用 T-sql 進行分類的詳細資訊，請參閱下列參考：

- 新增或更新一或多個資料行的分類： [新增敏感度分類](/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- 移除一或多個資料行中的分類： [捨棄敏感度分類](/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- 若要查看資料庫上的所有分類： [sys.sensitivity_classifications](/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-powershell-cmdlets"></a>使用 PowerShell Cmdlet
使用 PowerShell 管理 Azure SQL Database 和 Azure SQL 受控執行個體的分類和建議。

#### <a name="powershell-cmdlets-for-azure-sql-database"></a>適用于 Azure SQL Database 的 PowerShell Cmdlet

- [AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [設定-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [移除-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [啟用-AzSqlDatabaSesensitivityRecommendation](/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [停用-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-azure-sql-managed-instance"></a>適用于 Azure SQL 受控執行個體的 PowerShell Cmdlet

- [AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [設定-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [移除-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [啟用-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [停用-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

### <a name="use-the-rest-api"></a>使用 Rest API

您可以使用 REST API，以程式設計方式管理分類和建議。 已發佈的 REST API 支援下列作業：

- [Create 或 Update](/rest/api/sql/sensitivitylabels/createorupdate)：建立或更新指定資料行的敏感度標籤。
- [Delete](/rest/api/sql/sensitivitylabels/delete)：刪除指定之資料行的敏感度標籤。
- [停用建議](/rest/api/sql/sensitivitylabels/disablerecommendation)：停用指定之資料行的敏感度建議。
- [啟用建議](/rest/api/sql/sensitivitylabels/enablerecommendation)：在指定的資料行上啟用敏感度建議。 預設會在所有資料行上啟用 (建議。 ) 
- [Get：取得](/rest/api/sql/sensitivitylabels/get)指定之資料行的敏感度標籤。
- [依資料庫列出目前的清單](/rest/api/sql/sensitivitylabels/listcurrentbydatabase)：取得指定之資料庫目前的敏感度標籤。
- [資料庫建議的清單](/rest/api/sql/sensitivitylabels/listrecommendedbydatabase)：取得所指定資料庫的建議敏感度標籤。

## <a name="next-steps"></a><a id="next-steps"></a>後續步驟

- 請考慮設定 [AZURE SQL 審核](../../azure-sql/database/auditing-overview.md) ，以監視及審核對您分類敏感性資料的存取。
- 如需包含資料探索 & 分類的簡報，請參閱 [探索、分類、標記保護 SQL 資料 & |公開的資料](https://www.youtube.com/watch?v=itVi9bkJUNc)。
