---
title: 資料探索與分類
description: 適用于 Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse 分析的資料探索 & 分類
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
ms.date: 04/28/2020
tags: azure-synapse
ms.openlocfilehash: 8e04cb87895f00e1ee992745c43a884596bd8398
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85987260"
---
# <a name="data-discovery--classification"></a>資料探索與分類
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

資料探索 & 分類內建于 Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse Analytics 中。 它提供先進的功能來探索、分類、標記和報告您資料庫中的敏感性資料。

您最敏感的資料可能包括商務、財務、醫療保健或個人資訊。 探索和分類這項資料可以在貴組織的資訊保護方法中扮演 pivotal 角色。 它可以作為下列的基礎結構：

- 協助符合法規合規性的資料隱私權和需求的標準。
- 各種安全性案例，例如針對敏感性資料異常存取的監視 (稽核) 及警示。
- 控制存取和強化包含高度敏感性資料之資料庫的安全性。

資料探索 & 分類是[先進的資料安全性](advanced-data-security.md)供應專案的一部分，這是適用于先進 Azure SQL 安全性功能的整合套件。 您可以透過 Azure 入口網站的中央**SQL Advanced Data Security**區段來存取和管理資料探索 & 分類。

> [!NOTE]
> 如需 SQL Server 內部部署的詳細資訊，請參閱[SQL 資料探索 & 分類](https://go.microsoft.com/fwlink/?linkid=866999)。

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>什麼是資料探索 & 分類？

資料探索 & 分類引進了一組先進的服務，以及 Azure 中的新功能。 它會形成 SQL Database、SQL 受控執行個體和 Azure Synapse 的新資訊保護範例，其目標是保護資料而不只是資料庫。 此範例包含：

- **探索和建議：** 分類引擎會掃描您的資料庫，並識別包含潛在敏感性資料的資料行。 接著，它會提供您一個簡單的方法，讓您透過 Azure 入口網站來檢查並套用建議的分類。

- 卷**標：** 您可以使用已加入至 SQL Server 資料庫引擎的新中繼資料屬性，將敏感度分類標籤持續套用至資料行。 然後，此中繼資料可用於高級、以敏感度為基礎的審核和保護案例。

- **查詢結果-設定敏感度：** 查詢結果集的敏感度是以即時方式計算，以供進行審核之用。

- **可見度：** 您可以在 Azure 入口網站的詳細儀表板中，查看資料庫分類狀態。 此外，您也可以下載 Excel 格式的報表，以用於合規性和審核用途及其他需求。

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>探索、分類和標記敏感性資料行

本節說明下列步驟：

- 探索、分類和標記包含資料庫中敏感性資料的資料行。
- 查看資料庫的目前分類狀態，並匯出報表。

分類包含兩個中繼資料屬性：

- **標籤**：主要分類屬性，用來定義儲存在資料行中之資料的敏感度層級。  
- **資訊類型**：提供有關儲存在資料行中之資料類型的更細微資訊的屬性。

### <a name="define-and-customize-your-classification-taxonomy"></a>定義及自訂您的類別分類法

資料探索 & 分類隨附一組內建的敏感度標籤，以及一組內建的資訊類型和探索邏輯。 您現在可以自訂此分類法，並定義專門針對您的環境建構之類別的集合和順位。

您可以在一個集中位置定義和自訂您的分類分類，以供整個 Azure 組織。 該位置位於[Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)中，作為安全性原則的一部分。 只有在組織根管理群組上具有系統管理許可權的人員可以執行這項工作。

作為資訊保護的原則管理的一部分，您可以定義自訂標籤、對其進行排序，並將它們與一組選取的資訊類型產生關聯。 您也可以加入自己的自訂資訊類型，並使用字串模式來設定它們。 系統會將模式新增至探索邏輯，以便在您的資料庫中識別這種類型的資料。

如需詳細資訊，請參閱[在 Azure 資訊安全中心（預覽）中自訂 SQL 資訊保護原則](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409)。

定義整個組織的原則之後，您可以使用自訂原則繼續分類個別的資料庫。

### <a name="classify-your-database"></a>分類您的資料庫

> [!NOTE]
> 下列範例使用 Azure SQL Database，但您應該選取要設定資料探索 & 分類的適當產品。

1. 移至 [Azure 入口網站](https://portal.azure.com)。

2. 在 [Azure SQL Database] 窗格中，移至 [**安全性**] 標題下的 [ **Advanced Data Security** ]。 選取 [ **Advanced data security**]，然後選取 [**資料探索 & 分類**卡]。

   ![Azure 入口網站中的 [Advanced Data Security] 窗格](./media/data-discovery-and-classification-overview/data_classification.png)

3. 在 [**資料探索 & 分類**] 頁面上，[**總覽**] 索引標籤包含資料庫目前分類狀態的摘要。 摘要包含所有已分類資料行的詳細清單，您也可以篩選這些資料行，只顯示特定的架構元件、資訊類型和標籤。 如果您還沒有分類任何資料行，請[跳到步驟 5](#step-5)。

   ![目前分類狀態的摘要](./media/data-discovery-and-classification-overview/2_data_classification_overview_dashboard.png)

4. 若要下載 Excel 格式的報表，請選取窗格頂端功能表中的 [**匯出**]。

5. <a id="step-5"></a>若要開始分類您的資料，請選取 [**資料探索 & 分類**] 頁面上的 [**分類**] 索引標籤。

    分類引擎會掃描您的資料庫中是否有包含潛在敏感性資料的資料行，並提供建議的資料行分類清單。

6. 查看和套用分類建議：

   - 若要查看建議的資料行分類清單，請選取窗格底部的 [建議] 面板。

   - 若要接受特定資料行的建議，請選取相關資料列左欄中的核取方塊。 若要將所有建議標示為已接受，請選取 [建議] 資料表標頭中最左邊的核取方塊。

       ![審查並從分類建議清單中選取](./media/data-discovery-and-classification-overview/6_data_classification_recommendations_list.png)

   - 若要套用選取的建議，請選取 [**接受選取的建議**]。

7. 您也可以手動將資料行分類，以替代方式或除了以建議為基礎的分類之外：

   1. 在窗格的上方功能表中，選取 [**新增分類**]。

   1. 在開啟的內容視窗中，選取您想要分類的架構、資料表和資料行，以及 [資訊類型] 和 [敏感度] 標籤。

   1. 選取內容視窗底部的 [**新增分類**]。

      ![選取要分類的資料行](./media/data-discovery-and-classification-overview/9_data_classification_manual_classification.png)

8. 若要完成您的分類，並使用新的分類中繼資料持續加上標籤（標記）資料庫資料行，請選取視窗頂端功能表中的 [**儲存**]。

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>對敏感性資料的審核存取

資訊保護範例的一個重要層面是能夠監視機密資料的存取權。 [AZURE SQL 審核](../../azure-sql/database/auditing-overview.md)功能已增強，可在名為的 audit 記錄檔中加入新的欄位 `data_sensitivity_information` 。 這個欄位會記錄查詢所傳回之資料的敏感度分類（標籤）。 以下是範例：

![稽核記錄](./media/data-discovery-and-classification-overview/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Permissions

這些內建角色可以讀取資料庫的資料分類：

- 擁有者
- 讀取者
- 參與者
- SQL 安全性管理員
- 使用者存取系統管理員

這些內建角色可以修改資料庫的資料分類：

- 擁有者
- 參與者
- SQL 安全性管理員

深入瞭解[適用于 Azure 資源的 RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)中以角色為基礎的許可權。

## <a name="manage-classifications"></a><a id="manage-classification"></a>管理分類

您可以使用 T-sql、REST API 或 PowerShell 來管理分類。

### <a name="use-t-sql"></a>使用 T-SQL

您可以使用 T-sql 來新增或移除資料行分類，以及取得整個資料庫的所有分類。

> [!NOTE]
> 當您使用 T-sql 來管理標籤時，不會驗證您新增至資料行的標籤是否存在於組織的資訊保護原則中（出現在入口網站建議中的標籤集合）。 因此，您必須自行驗證。

如需使用 T-sql 進行分類的相關資訊，請參閱下列參考：

- 若要新增或更新一或多個資料行的分類：[新增敏感度分類](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- 若要從一或多個資料行移除分類： [DROP 敏感度分類](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- 若要查看資料庫上的所有分類： [sys. sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-powershell-cmdlets"></a>使用 PowerShell Cmdlet
使用 PowerShell 管理 Azure SQL Database 和 Azure SQL 受控執行個體的分類和建議。

#### <a name="powershell-cmdlets-for-azure-sql-database"></a>適用于 Azure SQL Database 的 PowerShell Cmdlet

- [AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [設定-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [移除-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [啟用-AzSqlDatabaSesensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [停用-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-azure-sql-managed-instance"></a>適用于 Azure SQL 受控執行個體的 PowerShell Cmdlet

- [AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [設定-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [移除-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [啟用-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [停用-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

### <a name="use-the-rest-api"></a>使用 Rest API

您可以使用 REST API 以程式設計方式管理分類和建議。 已發佈的 REST API 支援下列作業：

- [建立或更新](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate)：建立或更新指定資料行的敏感度標籤。
- [Delete](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete)：刪除指定資料行的敏感度標籤。
- [停用建議](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation)：停用指定資料行的敏感度建議。
- [啟用建議](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation)：在指定的資料行上啟用敏感度建議。 （預設會在所有資料行上啟用建議）。
- [Get：取得](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get)指定資料行的敏感度標籤。
- [依資料庫列出目前](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase)的：取得指定資料庫目前的敏感度標籤。
- [資料庫所建議的清單](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase)：取得指定之資料庫的建議敏感度標籤。

## <a name="next-steps"></a><a id="next-steps"></a>後續步驟

- 深入瞭解[Advanced Data Security](advanced-data-security.md)。
- 請考慮設定[AZURE SQL 審核](../../azure-sql/database/auditing-overview.md)，以監視和審核已分類敏感性資料的存取權。
- 如需包含資料探索 & 分類的簡報，請參閱[探索、分類、標記 & 保護 SQL 資料 |公開的資料](https://www.youtube.com/watch?v=itVi9bkJUNc)。
