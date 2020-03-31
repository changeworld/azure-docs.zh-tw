---
title: 版本資訊
description: Azure 同步分析的版本資訊。
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 3/26/2020
author: anumjs
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 6d04756bfba965e49a8b51457d2c679eb7bbfd13
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350762"
---
# <a name="azure-synapse-analytics-release-notes"></a>Azure 突觸分析版本資訊

本文總結了 Azure Synapse 中 SQL[分析](sql-data-warehouse-overview-what-is.md)的最新版本中的新功能和改進。 本文還列出了與發佈沒有直接關係但在同一時間範圍內發佈的顯著內容更新。 有關其他 Azure 服務的改進，請參閱[服務更新](https://azure.microsoft.com/updates)。

## <a name="check-your-azure-synapse-version"></a>檢查 Azure 突觸版本

將新功能部署到所有區域時，請檢查部署到實例的版本和最新版本說明，瞭解功能可用性。 要檢查版本，請通過 SQL 伺服器管理工作室 （SSMS） 連接到 SQL`SELECT @@VERSION;`池，然後運行以返回當前版本。

使用標識的版本確認哪個版本已應用於 SQL 池。 輸出中的日期標識應用於 SQL 池的發佈月的月份。

> [!NOTE]
> SELECT 返回的產品名稱將從@VERSIONMicrosoft Azure SQL 資料倉儲更改為 Azure 同步分析。 我們將在進行更改之前提前發出通知。 此更改與在其應用程式代碼中從 SELECT +@VERSION結果解析產品名稱的客戶相關。 為避免由於產品品牌重塑而更改應用程式代碼，請使用這些命令查詢伺服器屬性的資料庫產品名稱和版本：返回版本號 XX。X.XXXXX.X（沒有產品名稱）使用此命令： 
> ```
> SELECT SERVERPROPERTY('ProductVersion')
> ```
> 要返回引擎版本，請使用返回 6 的 Azure 突觸分析（以前的 SQL 資料倉儲）的命令： 
> ```
> SELECT SERVERPROPERTY('EngineEdition')
> ```

## <a name="march-2020"></a>2020 年 3 月

| 服務改進功能                                         | 詳細資料                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **[視覺化工作室 16.6 預覽 2](https://docs.microsoft.com/visualstudio/releases/2019/release-notes-preview) - SQL 伺服器資料工具 （SSDT）** | 此版本包括以下改進和修復： </br> </br> - 解決了更改具體視圖 （MV） 引用的表導致生成 MV 不支援的"更改視圖"語句的問題<br/><br/> - 實現更改，以確保在資料庫或專案中存在行級安全物件時，架構比較操作不會失敗。 SSDT 當前不支援行級安全物件。  <br/><br/> - SQL Server 物件資源管理器超時閾值已提高，以避免在資料庫中列出大量物件時超時<br/><br/> - 優化 SQL Server 物件資源管理器檢索資料庫物件清單的方式，以減少在填充物件資源管理器時的不穩定性並提高性能 |

## <a name="february-2020"></a>2020 年 2 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**資料庫相容性級別（預覽）**| 使用此版本，使用者現在可以設置資料庫的相容性級別，以獲取 SQL 分析引擎的特定版本的 Transact-SQL 語言和查詢處理行為。 有關詳細資訊，請參閱[sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql?view=azure-sqldw-latest&branch=pr-en-us-13797) [並更改資料庫範圍配置](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql?view=sql-server-ver15)。 |
|**Sp_describe_undeclared_parameters**| 允許使用者查看有關 Transact-SQL 批次處理中未聲明參數的中繼資料。 有關詳細資訊，請參閱[sp_describe_undeclared_parameters](/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql?view=sql-server-ver15)。|

## <a name="january-2020"></a>2020 年 1 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**工作負載監管中心指標（預覽）**|隨著今年 10 月發佈的用於預覽的[工作負載隔離](/azure/sql-data-warehouse/sql-data-warehouse-workload-isolation)，使用者可以創建自己的[工作負載組](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)，以高效管理系統資源並確保滿足業務 SL。  作為 Azure Synapse 分析總體[工作負載管理](/azure/sql-data-warehouse/sql-data-warehouse-workload-management)增強功能的一部分，新的[工作負載管理監視指標](/azure/sql-data-warehouse/sql-data-warehouse-workload-management-portal-monitor)現已可用。</br> </br> 現在，通過以下指標監視工作負荷，可以更深入地洞察： </br> - 有效上限資源百分比  </br> - 有效最小資源百分比 </br> - 工作負載組活動查詢 </br> - 按最大資源百分比分配工作負載組 </br> - 按系統百分比分配工作負載組 </br> - 工作負載組查詢超時 </br> - 工作負載組排隊查詢 </br></br> 使用這些指標可以標識使用[不足的工作負載隔離](/azure/sql-data-warehouse/sql-data-warehouse-workload-management-portal-monitor#underutilized-workload-isolation)配置[的工作負載組瓶頸](/azure/sql-data-warehouse/sql-data-warehouse-workload-management-portal-monitor#workload-group-bottleneck)或工作負載組。  這些指標可用於 Azure 門戶，允許按工作負荷組拆分。  篩選您最喜愛的圖形並將其固定到儀表板，以便快速訪問見解。|
|**門戶監控指標**| 以下指標已添加到門戶中，用於監視整個查詢活動： </br> - 活動查詢 </br> - 排隊查詢 </br> </br>這些指標與[監視資源利用率和查詢活動文檔中](/azure/sql-data-warehouse/sql-data-warehouse-concept-resource-utilization-query-activity)的現有指標一起描述。|


## <a name="october-2019"></a>2019 年 10 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**複製（預覽）**|我們很高興地宣佈一個簡單和靈活的COPY語句用於資料引入的公開預覽。 只需一個語句，您現在就可以無縫地引入具有額外靈活性且無需高特權使用者的資料。 有關詳細資訊，請參閱[COPY 命令文檔](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)。|
|**工作負載隔離（預覽）**|為了支援客戶實現資料倉儲民主化，我們宣佈了智慧工作負載管理的新功能。 新的[工作負載隔離](/azure/sql-data-warehouse/sql-data-warehouse-workload-isolation)功能允許您管理異構工作負載的執行，同時提供對資料倉儲資源的靈活性和控制。 這提高了執行可預測性，並增強了滿足預定義的 SL 的能力。 </br>除了工作負載隔離之外，[現在還提供工作負載分類](/azure/sql-data-warehouse/sql-data-warehouse-workload-classification)的其他選項。  除了登錄分類之外，"[創建工作負載分類器"](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)語法還提供基於查詢標籤、會話上下文和一天中時間對請求進行分類的功能。|
|**預測（預覽）**|您現在可以在資料倉儲中對機器學習模型進行評分，從而避免需要大型和複雜的資料移動。 T-SQLPREDICT 函數依賴于開放式模型框架，並將資料和機器學習模型作為輸入來生成預測。
|**SSDT CI/CD （GA）**|今天，我們很高興地宣佈 SQL 分析 – SQL 伺服器資料工具 （SSDT） 資料庫專案[的最高請求功能](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247--in-preview-database-project-from-visual-studio-t)的通用性。 此版本包括支援具有 Visual Studio 2019 的 SSDT，以及與 Azure DevOps 的本機平臺集成，為企業級部署提供內置的持續集成和部署 （CI/CD） 功能。 |
|**具體化視圖 （GA）**|具體化檢視會保存從檢視定義查詢傳回的資料，並在底層資料表中的資料變更時自動取得更新。 它可以改進複雜查詢 (通常是具有聯結與會總的查詢) 的效能，同時提供簡單的維護作業。 有關詳細資訊，請參閱[使用具體化視圖進行性能調優](/azure/sql-data-warehouse/performance-tuning-materialized-views)。  安裝[SQL 伺服器管理工作室 18.4 或更高版本](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)，以便編寫腳本化視圖。|
|**動態資料掩蔽 （GA）**|動態資料遮罩 （DDM） 會根據定義的遮罩規則，在查詢結果中即時混淆資料，從而防止未經授權訪問資料倉儲中的敏感性資料。有關詳細資訊，請參閱[SQL 資料庫動態資料掩蔽](/azure/sql-database/sql-database-dynamic-data-masking-get-started)。|
|**讀取已提交的快照隔離 （GA）**|您可以使用 ALTER DATABSE 為使用者資料庫啟用或禁用快照隔離。 為了避免對當前工作負荷造成影響，您可能需要在資料庫維護時段設置此選項，或者等到沒有其他活動連接到資料庫。 有關詳細資訊，請參閱[更改資料庫集選項](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)。|
|**有序的群集列存儲索引 （GA）**|列存儲是存儲和高效地查詢大量資料的關鍵推動因素。 有序的群集列存儲索引通過實現高效的段消除來進一步優化查詢執行。有關詳細資訊，請參閱[使用有序群集列存儲索引的性能調優](/azure/sql-data-warehouse/performance-tuning-ordered-cci)。|
|**結果集緩存 （GA）**|啟用結果集緩存後，SQL Analytics 會自動緩存使用者資料庫中的查詢結果，以便重複使用。 這允許後續查詢執行直接從持久緩存獲取結果，因此不需要重新計算。 結果集緩存提高了查詢性能，減少了計算資源使用。 此外，使用緩存結果集的查詢不使用任何併發槽，因此不計入現有併發限制。 出於安全考慮，使用者只有在具有與創建緩存結果的使用者具有相同的資料存取權限時才能訪問緩存的結果。 有關詳細資訊，請參閱[使用結果集緩存進行性能調優](/azure/sql-data-warehouse/performance-tuning-result-set-caching)。 適用于版本 10.0.10783.0 或更高版本。|

## <a name="september-2019"></a>2019 年 9 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**Azure 專用連結（預覽）**|使用[Azure 專用連結](https://azure.microsoft.com/blog/announcing-azure-private-link/)，可以在虛擬網路 （VNet） 中創建專用終結點並將其映射到 SQL 池。 然後，這些資源可通過 VNet 中的私人 IP 位址進行訪問，通過 Azure ExpressRoute 專用對等互連和/或 VPN 閘道實現從本地連接。 總體而言，這簡化了網路設定，無需將網路設定打開到公共 IP 位址。 這也能夠防止資料滲漏風險。 有關詳細資訊，請參閱[概述](/azure/private-link/private-link-overview)和[SQL 分析文檔](/azure/sql-database/sql-database-private-endpoint-overview)。|
|**資料發現&分類 （GA）**|[資料發現和分類](/azure/sql-database/sql-database-data-discovery-and-classification?toc=/azure/sql-data-warehouse/toc.json)功能現在通常可用。 此功能提供了用於**發現、分類、標記&保護**資料庫中敏感性資料的高級功能。|
|**Azure 顧問一鍵集成**|Azure Synaps 中的 SQL 分析現在直接與概述欄選項卡中的 Azure 顧問建議集成，同時提供一鍵式體驗。 現在，您可以在概述邊欄選項卡中發現建議，而不是導航到 Azure 顧問邊欄選項卡。 在此處瞭解有關建議的更多[。](sql-data-warehouse-concept-recommendations.md)|
|**讀取已提交的快照隔離（預覽）**|您可以使用 ALTER DATABSE 為使用者資料庫啟用或禁用快照隔離。  為了避免對當前工作負荷造成影響，您可能需要在資料庫維護時段設置此選項，或者等到沒有其他活動連接到資料庫。 有關詳細資訊，請參閱[更改資料庫集選項](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)。|
|**EXECUTE AS (Transact-SQL)**| [執行為](/sql/t-sql/statements/execute-as-transact-sql?view=azure-sqldw-latest)T-SQL 支援現在在 SQL Analytics 中提供，使客戶能夠將會話的執行上下文設置為指定的使用者。|
|**其他 T-SQL 支援**|SQL 分析的 T-SQL 語言表面積已擴展，以包括對以下方面的支援： </br> - [FORMAT（轉用-SQL）](/sql/t-sql/functions/format-transact-sql?view=azure-sqldw-latest)</br> - [TRY_PARSE（轉算-SQL）](/sql/t-sql/functions/try-parse-transact-sql?view=azure-sqldw-latest)</br> - [TRY_CAST（轉算-SQL）](/sql/t-sql/functions/try-cast-transact-sql?view=azure-sqldw-latest)</br> - [TRY_CONVERT（轉算-SQL）](/sql/t-sql/functions/try-convert-transact-sql?view=azure-sqldw-latest)</br> - [系統user_token（轉算-SQL）](/sql//relational-databases/system-catalog-views/sys-user-token-transact-sql?view=azure-sqldw-latest)|

## <a name="july-2019"></a>2019 年 7 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**具體化視圖（預覽）**|具體化檢視會保存從檢視定義查詢傳回的資料，並在底層資料表中的資料變更時自動取得更新。 它可以改進複雜查詢 (通常是具有聯結與會總的查詢) 的效能，同時提供簡單的維護作業。 如需詳細資訊，請參閱 </br> - [在"轉換-SQL&#41;&#40;選擇時創建具體化視圖](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)</br> - [更改具體化視圖&#40;轉算-SQL&#41;](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) </br> - [SQL 分析中支援的 T-SQL 語句](/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-statements)|
|**其他 T-SQL 支援**|SQL 分析的 T-SQL 語言表面積已擴展，以包括對以下方面的支援： </br> - [時區（轉算-SQL）](/sql/t-sql/queries/at-time-zone-transact-sql?view=azure-sqldw-latest)</br> - [STRING_AGG（轉算-SQL）](/sql/t-sql/functions/string-agg-transact-sql?view=azure-sqldw-latest)|
|**結果集緩存（預覽）**|添加 DBCC 命令以管理以前宣佈的結果集緩存。 如需詳細資訊，請參閱 </br> - [dbCC DROPRESULTCACHE&#40;轉算-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)  </br> - [DBCC 顯示，&#40;轉算-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest) </br></br> 另請參閱[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest)中新的result_set_cache列，該列顯示執行查詢何時使用結果集緩存。|
|**有序的群集列存儲索引（預覽）**|新列（column_store_order_ordinal）添加到[sys.index_columns](/sql/relational-databases/system-catalog-views/sys-index-columns-transact-sql?view=azure-sqldw-latest)以標識有序的群集列存儲索引中的列順序。|

## <a name="may-2019"></a>2019 年 5 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**動態資料遮罩（預覽）**|動態資料遮罩 （DDM） 會根據定義的遮罩規則，在查詢結果中即時混淆資料，從而防止未經授權訪問資料倉儲中的敏感性資料。有關詳細資訊，請參閱[SQL 資料庫動態資料掩蔽](/azure/sql-database/sql-database-dynamic-data-masking-get-started)。|
|**工作負載重要性現在一般可用**|工作負載管理分類和重要性提供了影響查詢運行順序的能力。 有關工作負載重要性的詳細資訊，請參閱文檔中的[分類](sql-data-warehouse-workload-classification.md)和[重要性](sql-data-warehouse-workload-importance.md)概述文章。 也請查看["創建任務"](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)文檔。<br/><br/>請參閱以下視頻中的操作中的工作負載重要性：<br/> -[工作負載管理概念](https://www.youtube.com/embed/QcCRBAhoXpM)<br/> -[工作負載管理方案](https://www.youtube.com/embed/_2rLMljOjw8)|
|**其他 T-SQL 支援**|SQL 分析的 T-SQL 語言表面積已擴展，以包括對以下方面的支援： </br> - [修剪](/sql/t-sql/functions/trim-transact-sql?view=azure-sqldw-latest)|
|**JSON 函數**|業務分析人員現在可以使用熟悉的 T-SQL 語言使用以下新的 JSON 函數查詢和操作格式化為 JSON 資料的文檔：</br> - [伊斯伊森](/sql/t-sql/functions/isjson-transact-sql?view=azure-sqldw-latest)</br> - [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest)</br> -  [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?view=azure-sqldw-latest)</br> -  [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql?view=azure-sqldw-latest)</br> - [迪維森](/sql/t-sql/functions/openjson-transact-sql?view=azure-sqldw-latest)|
|**結果集緩存（預覽）**|結果集緩存支援即時查詢回應時間，同時縮短商務分析師和報告使用者的洞察時間。 如需詳細資訊，請參閱</br> - [交流資料庫（轉算-SQL）](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest)</br> - [更改資料庫設置選項（轉算 SQL）](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)</br> - [設置結果設置緩存（轉算-SQL）](/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)</br> - [SET 語句（轉算 SQL）](/sql/t-sql/statements/set-statements-transact-sql)</br> - [系統資料庫（轉用-SQL）](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?view=azure-sqldw-latest)|
|**有序的群集列存儲索引（預覽）**|列存儲是存儲和高效地查詢大量資料的關鍵推動因素。 對於每個表，它將傳入的資料劃分為行組，行組的每一列在磁片上形成段。  有序的群集列存儲索引通過實現高效的段消除來進一步優化查詢執行。如需詳細資訊，請參閱</br> -  [創建表（SQL 分析）](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest)</br> -  [創建列清單存儲索引 （轉算 SQL）](/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest)。|

## <a name="march-2019"></a>2019 年 3 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**資料發現&分類**|資料發現&分類現已在 SQL 分析的公共預覽版中提供。 保護敏感性資料和客戶隱私至關重要。 隨著您的業務和客戶資料資產的增長，發現、分類和保護資料變得難以管理。 我們使用 SQL Analytics 本機引入的資料發現和分類功能有助於保護您的資料更易於管理。 此功能的總體優勢是：<br/>&bull;&nbsp;滿足資料隱私標準和法規合規性要求。<br/>&bull;&nbsp;限制對包含高度敏感性資料的資料倉儲的訪問並強化安全性。<br/>&bull;&nbsp;監視和警報對敏感性資料的異常訪問。<br/>&bull;&nbsp; Azure 門戶上的中央儀表板中敏感性資料的視覺化。 </br></br>資料發現&分類可用於所有 Azure 區域中的 SQL 分析，它是高級資料安全的一部分，包括漏洞評估和威脅檢測。 有關資料發現&分類的更多資訊，請參閱[博客文章](https://azure.microsoft.com/blog/announcing-public-preview-of-data-discovery-classification-for-microsoft-azure-sql-data-warehouse/)和我們的線上[文檔](/azure/sql-database/sql-database-data-discovery-and-classification)。|
|**GROUP BY ROLLUP**|在 Azure Synaps 中 SQL 分析中，ROLLUP 現在是受支援的 GROUP BY 選項。   分組分組為列運算式的每個組合創建一個組。 GROUP BY 還將結果"匯總"為子計和總計。 GROUP BY 函數從右向左處理，減少其創建組和聚合的列運算式數。  資料行順序會影響 ROLLUP 的輸出，也會影響結果集中的資料列數。<br/><br/>有關分組分組的更多資訊，請參閱[組 BY（轉接 SQL）](/sql/t-sql/queries/select-group-by-transact-sql?view=azure-sqldw-latest)
|**提高 DWU 使用的和 CPU 門戶指標的準確性**|SQL 分析可顯著提高 Azure 門戶中的指標準確性。  此版本包括 CPU 和 DWU 使用的指標定義的修復程式，以在所有計算節點之間正確反映您的工作負載。 在此修復之前，指標值報告不足。 預期 Azure 門戶中使用的 DWU 和 CPU 指標將會增加。 |
|**資料列層級安全性**|早在 2017 年 11 月，我們就引入了行級安全功能。 現在，我們已將此支援擴展到外部表。 此外，我們還添加了對在定義安全篩選器謂詞所需的內聯表值函數（內聯 TVF） 中調用非確定性函數的支援。 此添加允許您在安全篩選器謂詞中指定IS_ROLEMEMBER（）、USER_NAME（）等。 有關詳細資訊，請參閱[行級安全文檔中](/sql/relational-databases/security/row-level-security)的示例。|
|**其他 T-SQL 支援**|SQL 分析的 T-SQL 語言表面積已擴展為包括對[STRING_SPLIT （Transact-SQL）](/sql/t-sql/functions/string-split-transact-sql)的支援。
|**查詢最佳化工具增強功能** |查詢優化是任何資料庫的關鍵元件。 對如何最好地執行查詢做出最佳選擇可以產生顯著的改進。  在分散式環境中執行複雜的分析查詢時，執行的運算元很重要。 通過生成更好的品質計畫，查詢性能得到了提高。 這些計畫最大限度地減少了昂貴的資料傳輸操作和冗余計算，如重複的子查詢。 有關詳細資訊，請參閱此 Azure Synapse[博客文章](https://azure.microsoft.com/blog/smarter-faster-safer-azure-sql-data-warehouse-is-simply-unmatched/)。|
| | |

### <a name="documentation-improvements"></a>文件改進

| 文件改進 | 詳細資料 |
| --- | --- |
| | |

## <a name="january-2019"></a>2019 年 1 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**傳回 Order By 最佳化**|SELECT…ORDER BY 查詢會在此版本中獲得效能提升。   現在，所有計算節點將其結果發送到單個計算節點。 此節點合併和排序結果，並將它們返回給使用者。  當查詢結果集包含大量資料列時，透過單一計算節點進行合併會讓效能顯著提升。 以前，查詢執行引擎將命令每個計算節點上的結果。 結果將資料流到控制節點。 然後，控制節點將合併結果。|
|**適用於 PartitionMove 和 BroadcastMove 的資料移動增強功能**|在 SQL 分析中，使用隨機移動類型的資料移動步驟，使用即時資料移動技術。  有關詳細資訊，請參閱[性能增強博客](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)。 使用此版本，分區移動和廣播移動現在由相同的即時資料移動技術提供支援。 使用這些類型的資料移動步驟的使用者查詢將運行，從而提高性能。 您無須變更任何程式碼，即可享有這些改進的效能。|
|**值得注意的錯誤 (bug)**|不正確的 Azure Synapse`SELECT @@VERSION`版本 - 可能會返回不正確的版本 10.0.9999.0。 目前所發行的正確版本是 10.0.10106.0。 此錯誤 (bug) 已經被回報並正在進行檢閱。
| | |

### <a name="documentation-improvements"></a>文件改進

| 文件改進 | 詳細資料 |
| --- | --- |
|無 | |
| | |

## <a name="december-2018"></a>2018 年 12 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**一般供應的虛擬網路服務端點**|此版本包括所有 Azure 區域中 Azure 突觸中 SQL 分析的虛擬網路 （VNet） 服務終結點的通用性。 VNet 服務端點讓您可隔離連線，從虛擬網路內的指定子網路或一組子網路連線到邏輯伺服器。 從 VNet 到 Azure 突觸的流量將始終保留在 Azure 骨幹絡內。 這會優先使用此直接路由，而不是透過虛擬設備或內部部署傳送網際網路流量的任何特定路由。 透過服務端點存取虛擬網路並不會額外收費。 [Azure 突觸](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)的當前定價模型可如現在適用。<br/><br/>透過此版本，我們也讓 PolyBase 能夠透過 [Azure Blob File System](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver) (ABFS) 驅動程式連線到 [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (ADLS)。 Azure Data Lake Storage Gen2 所具備的品質，足以滿足 Azure 儲存體分析資料完整生命週期的一切所需。 兩個現有 Azure 儲存體服務 (Azure Blob 儲存體與 Azure Data Lake Storage Gen1) 的功能均涵蓋在內。 [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index) 的功能 (例如檔案系統語意、檔案層級安全性和級別) 結合了 [Azure Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)的低成本、分層式儲存體、高可用性/災害復原功能。<br/><br/>使用 Polybase 還可以將資料導入 Azure 同步器中的 SQL 分析，從保護到 VNet 的 Azure 存儲。 同樣，通過 Polybase 支援將資料從 Azure 突觸匯出到保護到 VNet 的 Azure 存儲。<br/><br/>有關 Azure Synaps 中 VNet 服務終結點的詳細資訊，請參閱[博客文章](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/)或[文檔](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json)。|
|**自動的效能監視 (預覽)**|[查詢存儲](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017)現在在 Azure Synaps 中的 SQL 分析中的預覽版中可用。 查詢存放區的設計目的是透過追蹤查詢、查詢計劃、執行階段統計資料以及查詢記錄等方式，協助您監視資料倉儲的活動與效能，藉此排解查詢效能的問題。 查詢存放區是一組內容存放區與「動態管理檢視」(DMV)，具備以下功能：<br/><br/>&bull;&nbsp;識別和調整使用資源的頂級查詢<br/>&bull;&nbsp;確定和改進計畫外工作負載<br/>&bull;&nbsp;通過統計資訊、索引或系統大小的更改（DWU 設置）評估查詢性能和對計畫的影響<br/>&bull;&nbsp;查看執行的所有查詢的完整查詢文本<br/><br/>查詢存放區包含三個實際存放區：<br/>&bull;&nbsp;用於持久化執行計畫資訊的計畫存儲<br/>&bull;&nbsp;用於持久化執行統計資訊的運行時統計資訊存儲<br/>&bull;&nbsp;等待統計資訊存儲，用於持久等待統計資訊。<br/><br/>Azure Synapse 中的 SQL 分析會自動管理這些存儲，並提供無限數量的查詢，這些查詢在過去七天內無需支付額外費用。 啟用查詢存放區很簡單，只要執行 ALTER DATABASE T-SQL 陳述式即可： <br/>sql ----ALTER 資料庫 [資料庫名稱] 設置QUERY_STORE = on;------- 有關查詢存儲的詳細資訊，請參閱文章，[使用查詢存儲監視性能](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)，以及查詢存儲 DMV，如[sys.query_store_query](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql)。 以下是版本發佈的[部落格文章](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/) (英文)。|
|**用於 SQL 分析的較低計算層**|Azure Synaps 中的 SQL 分析現在支援較低的計算層。 客戶可以體驗 Azure Synapse 領先的性能、靈活性和安全功能，從 100 cDWU（[資料倉儲單元](what-is-a-data-warehouse-unit-dwu-cdwu.md)）開始，並在幾分鐘內擴展到 30，000 cDWU。 自 2018 年 12 月中起，這些[地區](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)的客戶即可在較低的運算層級享有 Gen2 的效能和彈性，其餘地區則會在 2019 年跟進。<br/><br/>注重價值的客戶希望能全面評估安全的高效能資料倉儲的所有益處，且不想要猜測決定最適合的試用環境，而 Microsoft 降低了次世代資料倉儲服務的入門門檻，對這些客戶敞開了大門。 客戶最低可從 100 cDWU 開始，比目前 500 cDWU 的入門門檻更低。 SQL Analytics 繼續支援暫停和恢復操作，並不僅僅局限于計算的靈活性。 Gen2 也支援無限制資料行存放區的儲存體容量，每個查詢的記憶體增加 2.5 倍，最多可達 128 個並行查詢，還具備[調適性快取](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/)功能。 與 Gen1 上相同的資料倉儲單元相比，這些功能的平均性能是同一價格的五倍。 在保證內建資料保護功能的 Gen2 中，異地備援備份是標準功能。 Azure 突觸中的 SQL 分析已準備就緒，可以在需要時進行擴展。|
|**資料行存放區背景合併**|預設情況下，Azure SQL 資料以列格式存儲資料，其中稱為[行組](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)的微分區。 有時候，因為在建置索引或載入資料時記憶體受到限制，資料列群組可能會經過壓縮，且使用的壓縮大小低於最佳的 1 百萬個資料列。 資料列群組也可能會因為刪除操作而變得零碎。 小型或零碎的資料列群組會耗用更高的記憶體，執行查詢時的效率也不好。 使用此版本，列存儲後臺維護任務將合併小型壓縮行組以創建更大的行組，以更好地利用記憶體並加快查詢執行速度。
| | |

## <a name="october-2018"></a>2018 年 10 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**資料倉儲的 DevOps**|Azure 突觸中高度請求的 SQL 分析功能現已預覽，在視覺化工作室中支援 SQL 伺服器資料工具 （SSDT）！ 開發人員小組現在可以透過單一的版本控制程式碼基底執行協同作業，並快速地將變更部署到全球任何執行個體。 有興趣加入嗎？ 此功能目前可供預覽！ 您可以通過訪問[視覺化工作室 SQL 伺服器資料工具 （SSDT） - 預覽註冊表單進行註冊](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)。 鑑於需求量很大，我們正在對預覽進行驗收，以確保為客戶提供最佳體驗。 註冊後，我們的目標是在七個工作日內確認您的狀態。|
|**資料列層級安全性全面上市**|Azure Synaps 中的 SQL 分析現在支援行級安全性 （RLS）， 添加強大的功能來保護敏感性資料。 藉由引進 RLS，您可以實作安全性原則來控制對資料表中資料列的存取，例如誰可以存取哪些資料列。 RLS 支援這種更細緻的存取控制，而不必重新設計您的資料倉儲。 RLS 簡化了整體安全性模型，因為存取限制邏輯位於資料庫層本身，而不是遠離另一個應用程式中的資料。 RLS 還消除了導入檢視以篩選掉用於存取控制管理之資料列的需要。 我們為所有客戶提供此企業級安全性功能，無需任何額外的費用。|
|**進階的建議程式**|Azure Synapse 中 SQL 分析的高級調優只需通過其他資料倉儲建議和指標變得更加簡單。 Azure Advisor 還提供其他進階的效能建議，包括：<br/><br/>1. 自我調整緩存 – 建議何時縮放以優化緩存利用率。<br/>2. 表分佈 – 確定何時複製表以減少資料移動並提高工作負載性能。<br/>3. Tempdb – 瞭解何時縮放和配置資源類以減少 tempdb 爭用。<br/><br/>資料倉儲計量與 [Azure 監視器](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/)的整合更加深入，包括功能更強的可自訂監視圖表，能在概觀刀鋒視窗中提供近乎即時的計量。 當您監視使用量，或是驗證並套用資料倉儲建議時，不再需要離開資料倉儲概觀刀鋒視窗，即可存取 Azure 監視器計量。 此外，也提供新的計量 (例如 tempdb 及自適性快取使用率)，作為您效能建議的補充。|
|**使用整合式建議程式進行進階微調**|通過其他資料倉儲建議和指標以及重新設計門戶概述邊欄選項卡，使用 Azure Synapse 提供集成體驗，Azure Synapse 的高級調優非常簡單。|
|**加速資料庫復原 (ADR)**|Azure 同步加速資料庫恢復 （ADR） 現已處於公共預覽版。 ADR 是新的 SQL 資料庫引擎功能，藉由完整重新設計目前的復原程序，來大幅改善資料庫可用性 (特別是在有長時間執行的交易時)。 ADR 的主要優點是快速且一致的資料庫復原，以及即時的交易回復。|
|**Azure 監視器診斷記錄**|Azure Synapse 現在通過直接與 Azure 監視器診斷日誌集成，從而增強了對分析工作負荷的見解。 這項新功能可讓開發人員分析更長一段時間內的工作負載行為，並在充分了解資訊的情況下，做出查詢最佳化或容量管理決策。 我們現在透過 [Azure 監視器診斷記錄](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs)引進了外部的記錄處理程序，這些記錄提供了有關資料倉儲工作負載的額外見解。 只要按一下按鈕，您就可以使用 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries) 為歷程查詢效能疑難排解功能設定診斷記錄。 Azure 監視器診斷記錄透過將記錄儲存至儲存體帳戶以供稽核，來支援可自訂的保留期間；也支援將記錄串流至事件中樞，以獲得近乎即時的遙測深入解析功能；以及使用具有記錄查詢之 Log Analytics 來分析記錄的功能。 診斷日誌由資料倉儲的遙測視圖組成，相當於 Azure 突觸中最常用的用於 SQL 分析的性能故障排除 DMV。 我們已為此初始版本啟用下列系統動態管理檢視的檢視：<br/><br/>&bull; &nbsp; [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)|
|**資料行存放區記憶體管理**|隨著壓縮的資料行存放區資料列群組數目增加，管理這些資料列群組的內部資料行區段中繼資料所需的記憶體會隨之增加。  因此，查詢效能和針對某些資料行存放區動態管理檢視 (Dmv) 所執行的查詢可能會降級。  此版本已進行改善，讓這些情況的內部中繼資料大小達到最佳化，進而改善這類查詢的體驗和效能。|
|**Azure Data Lake Storage Gen2 整合 (GA)**|Synapse 分析現在具有與 Azure 資料存儲 Gen2 的本機集成。 客戶現在可以使用 ABFS 的外部表將資料載入到 SQL 分析中。 這項功能可讓客戶與其 Data Lake Storage Gen2 中的 Data Lake 整合。|
|**值得注意的錯誤 (bug)**|DW2000 及更多資料倉儲上的小資源類中的 PARAS 到 Parquet 失敗 - 此修復程式正確標識"創建外部表與 Parquet 代碼路徑"中的空引用。<br/><br/>識別欄位值在某些 CTAS 操作中可能會丟失 - 當 CTAS 到另一個表時，識別欄位的值可能無法保留。 在[博客](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)中報導。<br/><br/>在某些情況下，當會話在查詢仍在運行時終止時，內部故障 - 如果會話在查詢仍在運行時終止，則此修復將觸發無效操作異常。<br/><br/>(已在 2018 年 11 月部署) 當客戶嘗試使用 PolyBase 從 ADLS (Gen1) 載入多個小型檔案時，會有次佳的效能體驗。 - 在 AAD 安全權杖驗證期間，系統性能受到瓶頸。 效能問題已藉由啟用安全性權杖的快取來降低影響。 |
| | |

## <a name="next-steps"></a>後續步驟
- [創建 SQL 池](create-data-warehouse-portal.md)

## <a name="more-information"></a>詳細資訊
- [博客 - Azure 突觸分析](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [客戶諮詢小組部落格](https://docs.microsoft.com/archive/blogs/sqlcat/)
- [客戶成功案例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [影片](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 詞彙](../../azure-glossary-cloud-terminology.md)
