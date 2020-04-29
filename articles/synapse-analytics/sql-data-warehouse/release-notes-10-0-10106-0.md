---
title: 版本資訊
description: Azure Synapse 分析的版本資訊。
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
ms.openlocfilehash: 813baba37684525c336bc34a49e496f54a19288d
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509732"
---
# <a name="azure-synapse-analytics-release-notes"></a>Azure Synapse 分析版本資訊

本文摘要說明 Azure Synapse 中最新版本的[SYNAPSE SQL](sql-data-warehouse-overview-what-is.md)中的新功能和改進。 本文也會列出與發行不直接相關，但在相同時間範圍內發行的值得注意的內容更新。 如需其他 Azure 服務的改進功能，請參閱[服務更新](https://azure.microsoft.com/updates)。

## <a name="check-your-azure-synapse-version"></a>檢查您的 Azure Synapse 版本

當新功能推出至所有區域時，請檢查部署至您實例的版本，以及功能可用性的最新版本資訊。 若要檢查版本，請透過 SQL Server Management Studio （SSMS）連接到您的 SQL 集`SELECT @@VERSION;`區，然後執行以傳回目前的版本。 使用此版本來確認已套用至您的 SQL 集區的版本。 輸出中的日期會識別適用于您的 SQL 集區之發行的月份。 這僅適用于服務層級的改進。 

如需工具改良功能，請確定您已在版本注意事項中指定正確的版本。 


> [!NOTE]
> SELECT @@VERSION所傳回的產品名稱將從 Microsoft Azure SQL 資料倉儲變更為 Azure Synapse Analytics。 在進行變更之前，我們會傳送 advanced 通知。 這項變更與在應用程式代碼中從 SELECT @@VERSION的結果中剖析產品名稱的客戶有關。 若要避免因為產品品牌再造而變更應用程式代碼，請使用下列命令來查詢 SERVERPROPERTY 中的資料庫產品名稱和版本：傳回版本號碼 XX。X XXXXX。X （不含產品名稱）使用此命令：
>
> ```sql
> SELECT SERVERPROPERTY('ProductVersion')
>
> --To return engine edition, use this command that returns 6 for Azure Synapse Analytics (Formerly SQL Data Warehouse):
>
> SELECT SERVERPROPERTY('EngineEdition')
> ```



## <a name="april-2020"></a>2020 年 4 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**資料庫相容性層級（預覽）**| 在此版本中，使用者現在可以設定資料庫的相容性層級，以取得特定版本 Synapse SQL 引擎的 Transact-sql 語言和查詢處理行為。 如需詳細資訊，請參閱[sys.databases database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)和[Alter database 範圍](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)設定。|
|**Sp_describe_undeclared_parameters**| 允許使用者查看 Transact-sql 批次中未宣告之參數的相關中繼資料。 如需詳細資訊，請參閱[sp_describe_undeclared_parameters](/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。|

## <a name="march-2020"></a>2020 年 3 月

| 工具改良功能                                         | 詳細資料                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **[Visual Studio 16.6 Preview 2](/visualstudio/releases/2019/release-notes-preview) SQL SERVER DATA TOOLS （SSDT）** | 此版本包含 SSDT 的下列改良功能和修正： </br> </br> -已解決變更具體化視圖（MV）所參考之資料表的問題，導致產生不支援 MVs 的 Alter View 語句<br/><br/> -已執行變更，以確保當資料庫或專案中有資料列層級安全性物件時，架構比較作業不會失敗。 目前不支援 SSDT 的資料列層級安全性物件。  <br/><br/> -SQL Server 物件總管超時閾值已增加，以避免在資料庫中列出大量物件時的超時<br/><br/> -優化 SQL Server 物件總管抓取資料庫物件清單的方式，以在擴展 [物件瀏覽器] 時減少不穩定並提升效能 |

## <a name="january-2020"></a>2020 年 1 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**工作負載管理入口網站計量（預覽）**|隨著過去10月的預覽[工作負載隔離](sql-data-warehouse-workload-isolation.md)版本，使用者可以建立自己的[工作負載群組](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)，以有效率地管理系統資源，並確保符合商務 sla。  作為 Azure Synapse 分析的整體[工作負載管理](sql-data-warehouse-workload-management.md)增強功能的一部分，現在提供新的[工作負載管理監視計量](sql-data-warehouse-workload-management-portal-monitor.md)。</br> </br> 監視您的工作負載現在具有下列計量的更深入見解： </br> -有效的 cap 資源百分比  </br> -有效的最低資源百分比 </br> -工作負載群組作用中查詢 </br> -依最大資源百分比配置的工作負載群組 </br> -依系統百分比配置的工作負載群組 </br> -工作負載群組查詢超時 </br> -工作負載群組佇列查詢 </br></br> 使用這些計量來識別[工作負載群組瓶頸](sql-data-warehouse-workload-management-portal-monitor.md#workload-group-bottleneck)或設定了使用量過[低之工作負載隔離](sql-data-warehouse-workload-management-portal-monitor.md#underutilized-workload-isolation)的工作負載群組。  這些計量可用於允許依工作負載群組分割的 Azure 入口網站。  篩選您最愛的圖形，並將其釘選到儀表板，以快速存取見解。|
|**入口網站監視計量**| 已將下列計量新增至入口網站，以監視整體查詢活動： </br> -使用中查詢 </br> -已排入佇列的查詢 </br> </br>這些計量會與[監視資源使用率和查詢使用中的檔案](sql-data-warehouse-concept-resource-utilization-query-activity.md)中的現有計量一併說明。|

## <a name="october-2019"></a>2019 年 10 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**複製（預覽）**|我們很高興宣佈公開預覽，提供簡單且彈性的資料內嵌複製語句。 只有一個語句，您現在可以使用額外的彈性順暢地內嵌資料，而不需要高許可權的使用者。 如需詳細資訊，請參閱[複製命令檔](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。|
|**工作負載隔離（預覽）**|為了支援客戶將大眾化其資料倉儲，我們宣佈推出智慧型工作負載管理的新功能。 新的[工作負載隔離](sql-data-warehouse-workload-isolation.md)功能可讓您管理異類工作負載的執行，同時提供資料倉儲資源的彈性和控制權。 這會提升執行可預測性，並增強滿足預先定義之 Sla 的能力。 </br>除了工作負載隔離之外，其他選項現在也可用於[工作負載分類](sql-data-warehouse-workload-classification.md)。  除了登入分類之外，[建立工作負載分類器](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)語法可讓您根據查詢標籤、會話內容和當日時間來分類要求。|
|**預測（預覽）**|您現在可以對資料倉儲內的機器學習模型進行評分，以避免需要進行大型且複雜的資料移動。 T-sql PREDICT 函數依賴開放式模型架構，並採用資料和機器學習模型做為輸入來產生預測。
|**SSDT CI/CD （GA）**|今天，我們很高興宣佈 SQL 分析-SQL Server Data Tools （SSDT）資料庫專案的[最高要求功能](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247--in-preview-database-project-from-visual-studio-t)的正式推出。 此版本包含 SSDT 與 Visual Studio 2019 的支援，以及原生平臺整合與 Azure DevOps，為企業層級部署提供內建的持續整合和部署（CI/CD）功能。 |
|**具體化視圖（GA）**|具體化檢視會保存從檢視定義查詢傳回的資料，並在底層資料表中的資料變更時自動取得更新。 它可以改進複雜查詢 (通常是具有聯結與會總的查詢) 的效能，同時提供簡單的維護作業。 如需詳細資訊，請參閱[使用具體化視圖進行效能微調](performance-tuning-materialized-views.md)。  安裝[SQL Server Management Studio 18.4 或更新版本](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)，以編寫具體化視圖的腳本。|
|**動態資料遮罩（GA）**|動態資料遮罩（DDM）會根據您定義的遮罩規則，在查詢結果中即時混淆，以防止未經授權存取您資料倉儲中的機密資料。如需詳細資訊，請參閱[SQL Database 動態資料遮罩](../../sql-database/sql-database-dynamic-data-masking-get-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。|
|**讀取認可的快照集隔離（GA）**|您可以使用 ALTER DATABASE 來啟用或停用使用者資料庫的快照集隔離。 若要避免影響目前的工作負載，您可能會想要在資料庫維護期間設定此選項，或等到沒有其他作用中的資料庫連接。 如需詳細資訊，請參閱[Alter database set 選項](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。|
|**已排序的叢集資料行存放區索引（GA）**|資料行存放區是用來儲存和有效率地查詢大量資料的關鍵啟用程式。 排序的叢集資料行存放區索引會藉由啟用有效率的區段刪除，進一步優化查詢執行。如需詳細資訊，請參閱使用已排序的叢集資料行存放區[索引進行效能微調](performance-tuning-ordered-cci.md)。|
|**結果集快取（GA）**|啟用結果集快取時，會在使用者資料庫中自動快取查詢結果，以供重複使用。 這可讓後續的查詢執行直接從持續性快取取得結果，因此不需要重新計算。 結果集快取可改善查詢效能並減少計算資源使用量。 此外，使用快取結果集的查詢不會使用任何平行存取插槽，因此不會計算現有的並行限制。 基於安全性，如果使用者與建立快取結果的使用者具有相同的資料存取權限，則只能存取快取的結果。 如需詳細資訊，請參閱[使用結果集](performance-tuning-result-set-caching.md)快取進行效能微調。 適用于10.0.10783.0 或更高版本。|

## <a name="september-2019"></a>2019 年 9 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**Azure 私人連結（預覽）**|使用[Azure 私人連結](https://azure.microsoft.com/blog/announcing-azure-private-link/)，您可以在虛擬網路（VNet）中建立私人端點，並將其對應至您的 SQL 集區。 接著，您可以透過 VNet 中的私人 IP 位址來存取這些資源，以啟用從內部部署到 Azure ExpressRoute 私用對等互連和（或） VPN 閘道的連線。 整體來說，這可簡化網路設定，而不需要您將它開啟到公用 IP 位址。 這也可以保護資料外泄風險。 如需詳細資訊，請參閱[總覽](../../private-link/private-link-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)和[SQL 分析檔](../../sql-database/sql-database-private-endpoint-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。|
|**資料探索 & 分類（GA）**|[資料探索和分類](../../sql-database/sql-database-data-discovery-and-classification.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)功能現已正式推出。 這項功能提供了先進的功能，可用於**探索、分類、標記 & 保護**資料庫中的敏感性資料。|
|**Azure Advisor 單鍵整合**|Azure Synapse 中的 SQL 分析現在會直接與 [總覽] 分頁中的 Azure Advisor 建議整合，並提供單鍵體驗。 您現在可以在 [總覽] 分頁中探索建議，而不是流覽至 [Azure advisor] 分頁。 [在這裡](sql-data-warehouse-concept-recommendations.md)瞭解更多建議。|
|**讀取認可的快照集隔離（預覽）**|您可以使用 ALTER DATABASE 來啟用或停用使用者資料庫的快照集隔離。  若要避免影響目前的工作負載，您可能會想要在資料庫維護期間設定此選項，或等到沒有其他作用中的資料庫連接。 如需詳細資訊，請參閱[Alter database set 選項](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。|
|**EXECUTE AS (Transact-SQL)**| [執行](/sql/t-sql/statements/execute-as-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)身分T-sql 支援現已正式運作，可讓客戶將會話的執行內容設定為指定的使用者。|
|**其他 T-sql 支援**|Synapse SQL 的 T-sql 語言介面區已擴充，可包含下列各方面的支援： </br> - [FORMAT （Transact-sql）](/sql/t-sql/functions/format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [TRY_PARSE （Transact-sql）](/sql/t-sql/functions/try-parse-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [TRY_CAST （Transact-sql）](/sql/t-sql/functions/try-cast-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [TRY_CONVERT （Transact-sql）](/sql/t-sql/functions/try-convert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [sys.databases user_token （Transact-sql）](/sql//relational-databases/system-catalog-views/sys-user-token-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|

## <a name="july-2019"></a>2019 年 7 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**具體化視圖（預覽）**|具體化檢視會保存從檢視定義查詢傳回的資料，並在底層資料表中的資料變更時自動取得更新。 它可以改進複雜查詢 (通常是具有聯結與會總的查詢) 的效能，同時提供簡單的維護作業。 如需詳細資訊，請參閱： </br> - [建立具體化 VIEW 做為 SELECT &#40;Transact-sql&#41;](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [ALTER 具體化 VIEW &#40;Transact-sql&#41;](/sql/t-sql/statements/alter-materialized-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) </br> - [Synapse SQL 中支援的 t-sql 語句](sql-data-warehouse-reference-tsql-statements.md)|
|**其他 T-sql 支援**|Synapse SQL 的 T-sql 語言介面區已擴充，可包含下列各方面的支援： </br> - [AT TIME ZONE （Transact-sql）](/sql/t-sql/queries/at-time-zone-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [STRING_AGG （Transact-sql）](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**結果集快取（預覽）**|新增的 DBCC 命令，用來管理先前宣佈的結果集快取。 如需詳細資訊，請參閱： </br> - [DBCC DROPRESULTSETCACHE &#40;Transact-sql&#41;](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  </br> - [DBCC SHOWRESULTCACHESPACEUSED &#40;Transact-sql&#41;](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) </br></br> 另請參閱 sys.databases 中的新 result_set_cache 資料行，該資料行會顯示執行中的查詢使用結果集快取的時間[dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 。|
|**已排序的叢集資料行存放區索引（預覽）**|新增至 sys.databases 的新資料行 column_store_order_ordinal， [index_columns](/sql/relational-databases/system-catalog-views/sys-index-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)識別已排序的叢集資料行存放區索引中的資料行順序。|

## <a name="may-2019"></a>2019 年 5 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**動態資料遮罩（預覽）**|動態資料遮罩（DDM）會根據您定義的遮罩規則，在查詢結果中即時混淆，以防止未經授權存取您資料倉儲中的機密資料。如需詳細資訊，請參閱[SQL Database 動態資料遮罩](../../sql-database/sql-database-dynamic-data-masking-get-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。|
|**工作負載重要性現已正式推出**|工作負載管理分類和重要性提供了影響查詢執行順序的能力。 如需工作負載重要性的詳細資訊，請參閱檔中的[分類](sql-data-warehouse-workload-classification.md)和[重要性](sql-data-warehouse-workload-importance.md)總覽文章。 也請參閱[建立工作負載分類器](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)檔。<br/><br/>請參閱下列影片中的動作中的工作負載重要性：<br/> -[工作負載管理概念](https://www.youtube.com/embed/QcCRBAhoXpM)<br/> -[工作負載管理案例](https://www.youtube.com/embed/_2rLMljOjw8)|
|**其他 T-sql 支援**|Synapse SQL 的 T-sql 語言介面區已擴充，可包含下列各方面的支援： </br> - [物](/sql/t-sql/functions/trim-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**JSON 函式**|商務分析師現在可以使用熟悉的 T-sql 語言，利用下列新的 JSON 函數來查詢和操作格式化為 JSON 資料的檔：</br> - [ISJSON](/sql/t-sql/functions/isjson-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> -  [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> -  [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**結果集快取（預覽）**|結果集快取可啟用即時查詢回應時間，同時減少商務分析師和報告使用者的時間深入解析。 如需詳細資訊，請參閱：</br> - [ALTER DATABASE （Transact-sql）](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [ALTER DATABASE SET 選項（Transact-sql）](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [設定結果集快取（Transact-sql）](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [SET 語句（Transact-sql）](/sql/t-sql/statements/set-statements-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [sys.databases （Transact-sql）](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**已排序的叢集資料行存放區索引（預覽）**|資料行存放區是用來儲存和有效率地查詢大量資料的關鍵啟用程式。 針對每個資料表，它會將傳入的資料分割成資料列群組，而資料列群組的每個資料行會形成磁片上的區段。  排序的叢集資料行存放區索引會藉由啟用有效率的區段刪除，進一步優化查詢執行。如需詳細資訊，請參閱：</br> -  [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> -  CREATE 資料行存放區[索引（transact-sql）](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。|

## <a name="march-2019"></a>2019 年 3 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**資料探索 & 分類**|Synapse SQL 的資料探索 & 分類現已提供公開預覽。 保護敏感性資料和客戶隱私權是不可或缺的。 隨著您的業務和客戶資料資產的成長，探索、分類和保護您的資料會變得難以管理。 我們在 Synapse SQL 中以原生方式引進的資料探索和分類功能，有助於讓您的資料更容易管理。 這項功能的整體優點如下：<br/>&bull;&nbsp;符合資料隱私權標準和法規合規性需求。<br/>&bull;&nbsp;限制存取和強化包含高度敏感性資料之資料倉儲的安全性。<br/>&bull;&nbsp;對敏感性資料的異常存取進行監視和警示。<br/>&bull;&nbsp; Azure 入口網站上中央儀表板中的機密資料視覺效果。 </br></br>資料探索 & 分類適用于所有 Azure 區域，它是 Advanced Data Security 的一部分，包括弱點評估和威脅偵測。 如需有關資料探索 & 分類的詳細資訊，請參閱[blog 文章](https://azure.microsoft.com/blog/announcing-public-preview-of-data-discovery-classification-for-microsoft-azure-sql-data-warehouse/)和我們的線上[檔](../../sql-database/sql-database-data-discovery-and-classification.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。|
|**GROUP BY ROLLUP**|ROLLUP 現在是支援的 GROUP BY 選項。   GROUP BY ROLLUP 會針對每個資料行運算式的組合建立群組。 GROUP BY 也會將結果「匯總」為小計和總計。 GROUP BY 函數會由右至左處理，減少建立群組和匯總所依據的資料行運算式數目。  資料行順序會影響 ROLLUP 的輸出，也會影響結果集中的資料列數。<br/><br/>如需群組依據匯總的詳細資訊，請參閱[GROUP by （transact-sql）](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
|**已改善 DWU 使用和 CPU 入口網站計量的精確度**|Synapse SQL 大幅增強了 Azure 入口網站中的計量準確度。  此版本包含 CPU 和 DWU 使用的計量定義的修正，可正確反映所有計算節點上的工作負載。 在此修正之前，會短報度量值。 預期會在 Azure 入口網站中看到 [已使用的 DWU] 和 [CPU 計量] 的增加。 |
|**資料列層級安全性**|我們在2017年11月引進了資料列層級安全性功能。 我們現在也將這種支援延伸到外部資料表。 此外，我們也新增了在定義安全性篩選述詞所需的內嵌資料表值函式（內嵌 Tvf）中呼叫不具決定性函數的支援。 這項新增功能可讓您在安全性篩選器述詞中指定 IS_ROLEMEMBER （）、USER_NAME （）等。 如需詳細資訊，請參閱資料[列層級安全性檔案](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)中的範例。|
|**其他 T-sql 支援**|Synapse SQL 的 T-sql 語言介面區已擴充為包含[STRING_SPLIT （transact-sql）](/sql/t-sql/functions/string-split-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)的支援。
|**查詢最佳化工具增強功能** |查詢優化是任何資料庫的重要元件。 對如何最佳執行查詢做出最佳選擇，會產生顯著的改善。  在分散式環境中執行複雜的分析查詢時，執行的作業數目很重要。 藉由產生較佳的品質計畫，已增強了查詢效能。 這些計畫會將昂貴的資料傳輸作業和重複的計算（例如，重複的子查詢）降到最低。 如需詳細資訊，請參閱此 Azure Synapse 的[blog 文章](https://azure.microsoft.com/blog/smarter-faster-safer-azure-sql-data-warehouse-is-simply-unmatched/)。|
| | |

### <a name="documentation-improvements"></a>文件改進

| 文件改進 | 詳細資料 |
| --- | --- |
| | |

## <a name="january-2019"></a>2019 年 1 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**傳回 Order By 最佳化**|SELECT…ORDER BY 查詢會在此版本中獲得效能提升。   現在，所有計算節點都會將其結果傳送至單一計算節點。 此節點會合並並排序結果，並將其傳回給使用者。  當查詢結果集包含大量資料列時，透過單一計算節點進行合併會讓效能顯著提升。 過去，查詢執行引擎會在每個計算節點上排序結果。 結果會將它們串流處理至控制節點。 然後，控制節點會合並結果。|
|**適用於 PartitionMove 和 BroadcastMove 的資料移動增強功能**|ShuffleMove 類型的資料移動步驟，使用即時資料移動技術。  如需詳細資訊，請參閱[效能增強功能的 blog](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)。 在此版本中，PartitionMove 和 BroadcastMove 現在已由相同的即時資料移動技術提供支援。 使用這類資料移動步驟的使用者查詢會以改善的效能執行。 您無須變更任何程式碼，即可享有這些改進的效能。|
|**值得注意的錯誤 (bug)**|不正確的 Azure Synapse `SELECT @@VERSION`版本-可能會傳回不正確的版本10.0.9999.0。 目前所發行的正確版本是 10.0.10106.0。 此錯誤 (bug) 已經被回報並正在進行檢閱。
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
|**一般供應的虛擬網路服務端點**|此版本包含在所有 Azure 區域的 Azure Synapse 中，適用于 SQL 分析的虛擬網路（VNet）服務端點正式運作。 VNet 服務端點讓您可隔離連線，從虛擬網路內的指定子網路或一組子網路連線到邏輯伺服器。 從您的 VNet 到 Azure Synapse 的流量一律會保留在 Azure 骨幹網路中。 這會優先使用此直接路由，而不是透過虛擬設備或內部部署傳送網際網路流量的任何特定路由。 透過服務端點存取虛擬網路並不會額外收費。 [Azure Synapse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)目前的計價模式適用于。<br/><br/>透過此版本，我們也讓 PolyBase 能夠透過 [Azure Blob File System](../../storage/blobs/data-lake-storage-abfs-driver.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (ABFS) 驅動程式連線到 [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (ADLS)。 Azure Data Lake Storage Gen2 所具備的品質，足以滿足 Azure 儲存體分析資料完整生命週期的一切所需。 兩個現有 Azure 儲存體服務 (Azure Blob 儲存體與 Azure Data Lake Storage Gen1) 的功能均涵蓋在內。 [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 的功能 (例如檔案系統語意、檔案層級安全性和級別) 結合了 [Azure Blob 儲存體](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)的低成本、分層式儲存體、高可用性/災害復原功能。<br/><br/>使用 Polybase，您也可以將資料匯入 Azure Synapse 中的 SQL 分析，從受保護的 Azure 儲存體到 VNet。 同樣地，將資料從 Azure Synapse 匯出至受 VNet 保護的 Azure 儲存體也是透過 Polybase 來支援。<br/><br/>如需 Azure Synapse 中 VNet 服務端點的詳細資訊，請參閱[blog 文章](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/)或[檔](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。|
|**自動的效能監視 (預覽)**|[查詢存放區](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)現在已在 Azure SYNAPSE 的 SQL 分析中提供預覽。 查詢存放區的設計目的是透過追蹤查詢、查詢計劃、執行階段統計資料以及查詢記錄等方式，協助您監視資料倉儲的活動與效能，藉此排解查詢效能的問題。 查詢存放區是一組內容存放區與「動態管理檢視」(DMV)，具備以下功能：<br/><br/>&bull;&nbsp;識別並調整熱門資源取用查詢<br/>&bull;&nbsp;找出並改善非計畫的工作負載<br/>&bull;&nbsp;依據統計資料、索引或系統大小（DWU 設定）中的變更，評估查詢效能和對計畫的影響<br/>&bull;&nbsp;查看所有已執行查詢的完整查詢文字<br/><br/>查詢存放區包含三個實際存放區：<br/>&bull;&nbsp;用於保存執行計畫資訊的計畫存放區<br/>&bull;&nbsp;用於保存執行統計資料資訊的執行時間統計資料存放區<br/>&bull;&nbsp;等候統計資料存放區，用於保存等候統計資料資訊。<br/><br/>Azure Synapse 中的 SQL 分析會自動管理這些存放區，並提供過去七天內無限期的查詢 storied，而不需要額外付費。 啟用查詢存放區很簡單，只要執行 ALTER DATABASE T-SQL 陳述式即可： <br/>sql----ALTER DATABASE [DatabaseName] 設定 QUERY_STORE = ON;-------如需查詢存放區的詳細資訊，請參閱[使用查詢存放區監視效能](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)和查詢存放區 dmv （例如[sys. query_store_query](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)）一文。 以下是版本發佈的[部落格文章](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/) (英文)。|
|**SQL 分析的較低計算層級**|Azure Synapse 中的 SQL 分析現在支援較低的計算層。 從 100 cDWU （[資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)）開始，客戶可以體驗 Azure Synapse 領先的效能、彈性和安全性功能，並在幾分鐘內調整為 30000 cDWU。 自 2018 年 12 月中起，這些[地區](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)的客戶即可在較低的運算層級享有 Gen2 的效能和彈性，其餘地區則會在 2019 年跟進。<br/><br/>注重價值的客戶希望能全面評估安全的高效能資料倉儲的所有益處，且不想要猜測決定最適合的試用環境，而 Microsoft 降低了次世代資料倉儲服務的入門門檻，對這些客戶敞開了大門。 客戶最低可從 100 cDWU 開始，比目前 500 cDWU 的入門門檻更低。 SQL 分析會繼續支援暫停和繼續作業，並超越計算的彈性。 Gen2 也支援無限制資料行存放區的儲存體容量，每個查詢的記憶體增加 2.5 倍，最多可達 128 個並行查詢，還具備[調適性快取](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/)功能。 相較于相同價格的 Gen1 上相同的資料倉儲單位，這些功能平均會帶來五倍以上的效能。 在保證內建資料保護功能的 Gen2 中，異地備援備份是標準功能。 當您在時，Azure Synapse 中的 SQL 分析已準備好進行調整。|
|**資料行存放區背景合併**|根據預設，Azure SQL 資料會以單欄式格式儲存資料，其中包含稱為[資料列群組](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)的微磁碟分割。 有時候，因為在建置索引或載入資料時記憶體受到限制，資料列群組可能會經過壓縮，且使用的壓縮大小低於最佳的 1 百萬個資料列。 資料列群組也可能會因為刪除操作而變得零碎。 小型或零碎的資料列群組會耗用更高的記憶體，執行查詢時的效率也不好。 在此版本中，資料行存放區背景維護工作會合並小型壓縮資料列群組來建立較大的資料列群組，以更佳地利用記憶體並加速查詢執行。
| | |

## <a name="october-2018"></a>2018 年 10 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**資料倉儲的 DevOps**|Azure Synapse 中的 Synapse SQL 高度要求功能現為預覽狀態，並支援 Visual Studio 中的 SQL Server 資料工具（SSDT）！ 開發人員小組現在可以透過單一的版本控制程式碼基底執行協同作業，並快速地將變更部署到全球任何執行個體。 有興趣加入嗎？ 此功能目前可供預覽！ 您可以造訪[Visual Studio SQL Server Data Tools （SSDT）-Preview 註冊表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)進行註冊。 鑑於需求量很大，我們正在對預覽進行驗收，以確保為客戶提供最佳體驗。 註冊後，我們的目標是在七個工作日內確認您的狀態。|
|**資料列層級安全性全面上市**|Azure Synapse 中的 Synapse SQL 現在支援資料列層級安全性（RLS），以新增強大的功能來保護您的機密資料。 藉由引進 RLS，您可以實作安全性原則來控制對資料表中資料列的存取，例如誰可以存取哪些資料列。 RLS 支援這種更細緻的存取控制，而不必重新設計您的資料倉儲。 RLS 簡化了整體安全性模型，因為存取限制邏輯位於資料庫層本身，而不是遠離另一個應用程式中的資料。 RLS 還消除了導入檢視以篩選掉用於存取控制管理之資料列的需要。 我們為所有客戶提供此企業級安全性功能，無需任何額外的費用。|
|**進階的建議程式**|Azure Synapse 中的 Synapse SQL 的先進調整，透過額外的資料倉儲建議和計量，讓您更輕鬆。 Azure Advisor 還提供其他進階的效能建議，包括：<br/><br/>1. 彈性快取–建議何時調整以優化快取使用率。<br/>2. 資料表散發–判斷何時複寫資料表以減少資料移動並提升工作負載效能。<br/>3. Tempdb –瞭解何時要調整和設定資源類別，以減少 Tempdb 競爭。<br/><br/>資料倉儲計量與 [Azure 監視器](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/)的整合更加深入，包括功能更強的可自訂監視圖表，能在概觀刀鋒視窗中提供近乎即時的計量。 當您監視使用量，或是驗證並套用資料倉儲建議時，不再需要離開資料倉儲概觀刀鋒視窗，即可存取 Azure 監視器計量。 此外，也提供新的計量 (例如 tempdb 及自適性快取使用率)，作為您效能建議的補充。|
|**使用整合式建議程式進行進階微調**|使用額外的資料倉儲建議和計量，並重新設計入口網站總覽分頁，以提供 Azure Advisor 和 Azure 監視器的整合式體驗，讓 Azure Synapse 的先進調整變得更簡單。|
|**加速資料庫復原 (ADR)**|Azure Synapse 加速資料庫復原（ADR）現已開放公開預覽。 ADR 是新的 SQL 資料庫引擎功能，藉由完整重新設計目前的復原程序，來大幅改善資料庫可用性 (特別是在有長時間執行的交易時)。 ADR 的主要優點是快速且一致的資料庫復原，以及即時的交易回復。|
|**Azure 監視器資源記錄**|Azure Synapse 現在藉由直接與 Azure 監視器資源記錄整合，讓您更深入瞭解分析工作負載。 這項新功能可讓開發人員分析更長一段時間內的工作負載行為，並在充分了解資訊的情況下，做出查詢最佳化或容量管理決策。 我們現在已透過[Azure 監視器資源記錄](../../azure-monitor/platform/data-platform.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#logs)來引進外部記錄程式，可為您的資料倉儲工作負載提供額外的深入解析。 只要按一下按鈕，您就可以使用[Log Analytics](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)來設定記錄查詢效能疑難排解功能的資源記錄。 Azure 監視器資源日誌支援可自訂的保留週期，方法是將記錄儲存至儲存體帳戶以供進行審核、將記錄串流至事件中樞近乎即時的遙測深入解析，以及使用 log Analytics 搭配記錄查詢來分析記錄的功能。 資源記錄包含您資料倉儲的遙測視圖，相當於 Azure Synapse 中最常使用的 SQL 分析效能疑難排解 Dmv。 我們已為此初始版本啟用下列系統動態管理檢視的檢視：<br/><br/>&bull; &nbsp; [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**資料行存放區記憶體管理**|隨著壓縮的資料行存放區資料列群組數目增加，管理這些資料列群組的內部資料行區段中繼資料所需的記憶體會隨之增加。  因此，查詢效能和針對某些資料行存放區動態管理檢視 (Dmv) 所執行的查詢可能會降級。  此版本已進行改善，讓這些情況的內部中繼資料大小達到最佳化，進而改善這類查詢的體驗和效能。|
|**Azure Data Lake Storage Gen2 整合 (GA)**|Synapse 分析現在具有與 Azure Data Lake Storage Gen2 的原生整合。 客戶現在可以使用 ABFS 中的外部資料表，將資料載入 Synapse SQL 集區。 這項功能可讓客戶與其 Data Lake Storage Gen2 中的 Data Lake 整合。|
|**值得注意的錯誤 (bug)**|CETAS 至 Parquet DW2000 和更多資料倉儲上小型資源類別中的失敗-此修正會在 [建立外部資料表] 中將 null 參考正確地識別為 Parquet 程式碼路徑。<br/><br/>在某些 CTAS 作業中，識別資料行值可能會遺失-在 CTASed 至另一個資料表時，可能不會保留識別資料行的值。 在[blog](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)中回報。<br/><br/>當查詢仍在執行時，在某些情況下會發生內部失敗-如果在查詢仍在執行時終止會話，此修正會觸發 InvalidOperationException。<br/><br/>(已在 2018 年 11 月部署) 當客戶嘗試使用 PolyBase 從 ADLS (Gen1) 載入多個小型檔案時，會有次佳的效能體驗。 -系統效能在 AAD 安全性權杖驗證期間發生瓶頸。 效能問題已藉由啟用安全性權杖的快取來降低影響。 |
| | |

## <a name="next-steps"></a>後續步驟

- [建立 SQL 集區](create-data-warehouse-portal.md)

## <a name="more-information"></a>更多資訊

- [Blog-Azure Synapse 分析](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [客戶諮詢小組部落格](https://docs.microsoft.com/archive/blogs/sqlcat/)
- [客戶成功案例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [影片](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 詞彙](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
