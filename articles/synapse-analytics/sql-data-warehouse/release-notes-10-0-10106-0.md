---
title: 版本資訊
description: Azure Synapse Analytics 的版本資訊。
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 4/30/2020
author: anumjs
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 6f7af74cce6bbafea7924d505f768503c7b1f108
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89457986"
---
# <a name="azure-synapse-analytics-release-notes"></a>Azure Synapse Analytics 版本資訊

本文摘要說明 Azure SQL 中 [Synapse SQL](sql-data-warehouse-overview-what-is.md) 最新版本的新功能與改進功能。 本文也會列出值得注意的內容更新，這些更新並未與該版本直接相關，但會在同一個時間範圍內發佈。 如需其他 Azure 服務的改進功能，請參閱[服務更新](https://azure.microsoft.com/updates)。

## <a name="check-your-azure-synapse-version"></a>檢查您的 Azure Synapse 版本

新功能會推出到所有區域，請檢查部署至您執行個體的版本和最新版本資訊是否提供此功能。 若要檢查版本，請透過 SQL Server Management Studio (SSMS) 連線至您的 SQL 集區，並執行 `SELECT @@VERSION;` 以傳回目前的版本。 使用此版本以確認 SQL 集區已套用哪個版本。 輸出中的日期會識別套用至您 SQL 集區的版本月份。 這僅適用於服務層級的改進。 

如需工具改進，請確定您已安裝在版本資訊中指定的正確版本。 

> [!NOTE]
> 由 SELECT @@VERSION 傳回的產品名稱會從 Microsoft Azure SQL 資料倉儲變更為 Microsoft Azure Synapse Analytics。 在進行變更之前，我們會預先傳送通知。 這項變更與在其應用程式程式碼中從 SELECT @@VERSION 的結果剖析產品名稱的客戶相關。 若要避免應用程式程式碼因為產品品牌重新命名而變更，請使用以下命令來查詢 SERVERPROPERTY，以取得資料庫產品名稱和版本：若要傳回版本號碼 XX.X.XXXXX.X (沒有產品名稱)，請使用以下命令：
>
> ```sql
> SELECT SERVERPROPERTY('ProductVersion')
>
> --To return engine edition, use this command that returns 6 for Azure Synapse Analytics (formerly SQL Data Warehouse):
>
> SELECT SERVERPROPERTY('EngineEdition')
> ```

## <a name="aug-2020"></a>2020年8月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**工作負載管理-入口網站體驗**|使用者可以透過 Azure 入口網站來設定及管理其工作負載管理設定。 可以設定[工作群組](/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-portal)和具有重要性的[工作負載分類器](/azure/synapse-analytics/sql-data-warehouse/quickstart-create-a-workload-classifier-portal)。|
|**改進的資料表對應目錄檢視**|新的目錄檢視 [sys.pdw_permanent_table_mappings](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-pdw-permanent-table-mappings-transact-sql?view=sqlallproducts-allversions) 會將永久使用者資料表 **object_ids** 對應至其實體資料表名稱。|

## <a name="july-2020"></a>2020 年 7 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**資料行層級加密 (公開預覽) **|使用 Transact-sql 將對稱式加密套用至資料行，以保護您 Azure Synapse Analytics 中的機密資訊。 資料行層級加密具有內建函數，可讓您使用以憑證、密碼、對稱金鑰或非對稱金鑰進一步保護的對稱金鑰來加密資料。 如需詳細資訊，請造訪 [加密資料行](/sql/relational-databases/security/encryption/encrypt-a-column-of-data?view=azure-sqldw-latest)。|
|** (GA) 的相容性層級支援 **|在此版本中，使用者現在可以設定資料庫的相容性層級，以取得特定版本 Synapse SQL 引擎的 Transact-SQL 語言和查詢處理行為。 如需詳細資訊，請參閱 [sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 和[改變資料庫範圍設定](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。|
|**資料列層級安全性**|此版本包含在其上強制執行 RLS 之資料列的更新和刪除作業的改進。 在此版本中，如果內建函式未參考 DML 目標資料表中的任何資料行，則具有 ' is_rolemember ' 等內建函式的更新和刪除作業將會成功。 在這項改進之前，這些作業會因為基礎 DML 作業的限制而失敗。|
|**DBCC SHRINKDATABASE (GA) **|您現在可以壓縮指定之資料庫中的資料和記錄檔大小。 如需詳細資訊，請參閱 [檔](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql?view=sql-server-ver15)。|

## <a name="may-2020"></a>2020 年 5 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**工作負載隔離 (GA)**|[工作負載隔離](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-isolation)現已正式推出。  您可以透過[工作負載群組](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)保留和包含資源。  也可以設定查詢逾時來取消失控查詢。|
|**工作負載管理入口網站體驗 (預覽)**| 使用者可以透過 Azure 入口網站來設定及管理其工作負載管理設定。  可以設定[工作群組](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-create-a-workload-classifier-portal)和具有重要性的[工作負載分類器](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-create-a-workload-classifier-portal)。|
|**改變工作負載群組**|現在可以使用 [ALTER WORKLOAD GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-workload-group-transact-sql?view=azure-sqldw-latest) 命令。  使用改變來變更現有[工作負載群組](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-isolation)的設定。|
|**使用複製命令 (預覽) 自動偵測 Parquet 檔案的架構 **|[COPY 命令](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)現在支援在載入 Parquet 檔案時進行自動結構描述偵測。 此命令會自動偵測 Parquet 檔案結構描述，並且在載入之前建立資料表。 前往下列電子郵件通訊群組清單，以啟用此功能： sqldwcopypreview@service.microsoft.com 。 |
|**使用複製命令 (預覽版載入複雜的 Parquet 資料類型) **|[COPY 命令](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)現在支援載入複雜的 Parquet 類型。 您可以將例如「地圖」和「清單」的複雜類型載入至字串資料行。  前往下列電子郵件通訊群組清單，以啟用此功能： sqldwcopypreview@service.microsoft.com 。 |
|**使用 COPY 命令進行 Parquet 檔案的自動壓縮偵測**|[COPY 命令](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)現在支援 Parquet 檔案的壓縮方法自動偵測。 前往下列電子郵件通訊群組清單，以啟用此功能： sqldwcopypreview@service.microsoft.com 。|
|**其他載入建議**|[載入建議](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations)現在適用於 Synapse SQL。 當您應該分割檔案以取得最大輸送量時，請將您的儲存體帳戶與您的 SQL 集區共置，或在使用 SQLBulkCopy API 或 BCP 等載入公用程式時增加批次大小，以取得主動式通知|
|**T-SQL 可更新散發資料行 (GA)**|使用者現在可以更新儲存在散發資料行中的資料。 如需詳細資訊，請參閱[在 Synapse SQL 集區中設計分散式資料表的指引](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute)。|
|**T-SQL 更新/刪除來源...聯結 (GA)**|現在可以根據與其他資料表聯結的結果更新和刪除。 如需詳細資訊，請參閱[更新](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql?view=azure-sqldw-latest)和[刪除](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql?view=azure-sqldw-latest)文件。|
|**T-SQL PREDICT (預覽)**|您現在可以在資料倉儲中預測機器學習模型，而不需要進行大型且複雜的資料移動。 T-SQL PREDICT 函式依賴開放式模型架構，並且會採用資料和機器學習模型作為輸入來產生預測。 如需詳細資訊，請參閱[文件](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)。|

## <a name="april-2020"></a>2020 年 4 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**資料庫相容性層級 (預覽)**| 在此版本中，使用者現在可以設定資料庫的相容性層級，以取得特定版本 Synapse SQL 引擎的 Transact-SQL 語言和查詢處理行為。 如需詳細資訊，請參閱 [sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 和[改變資料庫範圍設定](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。|
|**Sp_describe_undeclared_parameters**| 允許使用者查看 Transact-SQL 批次中未宣告參數的相關中繼資料。 如需詳細資訊，請參閱 [sp_describe_undeclared_parameters](/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。| <br/><br/><br/>

| 工具改進                                         | 詳細資料                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **[Visual Studio 16.6 Preview 5](https://docs.microsoft.com/visualstudio/releases/2019/release-notes-preview#--visual-studio-2019-version-166-preview-5-) - SQL Server Data Tools (SSDT)** | 此版本包含 SSDT 的下列改進： </br> </br> - 資料探索與分類<br/> - COPY 陳述式 <br/> - 具有唯一條件約束的資料表<br/> - 具有已排序叢集資料行存放區索引的資料表<br/> <br/>此版本包含 SSDT 的下列修正︰ </br></br>  - 當變更散發資料行資料類型時， SSDT 所產生的更新指令碼會執行 CTAS 和 RENAME 作業，而不是捨棄然後重新建立資料表。 </br> |

## <a name="march-2020"></a>2020 年 3 月

| 工具改進                                         | 詳細資料                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **[Visual Studio 16.6 Preview 2](https://docs.microsoft.com/visualstudio/releases/2019/release-notes-preview#whats-new-in-visual-studio-2019) - SQL Server Data Tools (SSDT)** | 此版本包含 SSDT 的下列改進和修正： </br> </br> - 已解決變更具體化檢視 (MV) 所參考資料表會導致產生 MV 不支援的 Alter View 陳述式<br/><br/> - 已實作變更以確定當資料列層級安全性物件顯示在資料庫或專案中時，結構描述比較作業不會失敗。 目前不支援 SSDT 的資料列層級安全性物件。  <br/><br/> - SQL Server 物件總管逾時閾值已增加，以避免在資料庫中列出大量物件時逾時<br/><br/> - 最佳化 SQL Server 物件總管擷取資料庫物件清單的方式，以在填入物件總管時減少不穩定並提升效能 |

## <a name="january-2020"></a>2020 年 1 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**工作負載管理入口網站計量 (預覽)**|隨著過去 10 月的預覽版本[工作負載隔離](sql-data-warehouse-workload-isolation.md)，使用者可以建立自己的[工作負載群組](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)以有效率地管理系統資源，並確保符合商務 SLA。  在 Azure Synapse Analytics 的整體[工作負載管理](sql-data-warehouse-workload-management.md)增強功能當中，現在提供新的[工作負載管理監視計量](sql-data-warehouse-workload-management-portal-monitor.md)。</br> </br> 監視您的工作負載現在有具有下列計量的更佳深入解析： </br> - 有效的容量資源百分比  </br> - 有效的最低資源百分比 </br> - 工作負載群組使用中查詢 </br> - 依最大資源百分比配置的工作負載群組 </br> - 依系統百分比配置的工作負載群組 </br> - 工作負載群組查詢逾時 </br> - 已排入佇列的工作負載群組查詢 </br></br> 使用這些計量來識別[工作負載群組瓶頸](sql-data-warehouse-workload-management-portal-monitor.md#workload-group-bottleneck)，或使用[使用量過低的工作負載隔離](sql-data-warehouse-workload-management-portal-monitor.md#underutilized-workload-isolation)所設定的工作負載群組。  這些計量可用於允許依工作負載群組分割的 Azure 入口網站。  篩選您最愛的圖表，並將其釘選到儀表板，以快速存取深入解析。|
|**入口網站監視計量**| 已將下列計量新增至入口網站，以監視整體查詢活動： </br> - 使用中查詢 </br> - 已排入佇列的查詢 </br> </br>這些計量會與[監視資源使用率和查詢活動文件](sql-data-warehouse-concept-resource-utilization-query-activity.md)中的現有計量一併描述。|

## <a name="october-2019"></a>2019 年 10 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**複製 (預覽)**|我們很高興宣佈提供適用於資料內嵌的簡單且彈性 COPY 陳述式公開預覽。 只要使用一個陳述式，您現在可以更有彈性地順暢內嵌資料，而不需要高權限的使用者。 如需詳細資訊，請參閱 [COPY 命令文件](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。|
|**工作負載隔離 (預覽)**|為了支援客戶將其資料倉儲大眾化，我們宣佈推出智慧型工作負載管理的新功能。 新的[工作負載隔離](sql-data-warehouse-workload-isolation.md)功能可讓您管理異質工作負載的執行，同時提供資料倉儲資源的彈性和控制。 這會提升執行可預測性，並增強滿足預先定義 SLA 的能力。 </br>除了工作負載隔離以外，其他選項現在適用於[工作負載分類](sql-data-warehouse-workload-classification.md)。  除了登入分類之外，[建立工作負載分類器](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)語法提供根據查詢標籤、工作階段內容和當天時間來分類要求的功能。|
|**PREDICT (預覽)**|您現在可以對資料倉儲中的機器學習模型評分，而不需要進行大型且複雜的資料移動。 T-SQL PREDICT 函式依賴開放式模型架構，並且會採用資料和機器學習模型作為輸入來產生預測。
|**SSDT CI/CD (GA)**|今天我們很高興宣佈正式推出適用於 SQL Analytics 的[最高要求功能](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247--in-preview-database-project-from-visual-studio-t)：SQL Server Data Tools (SSDT) 資料庫專案。 此版本包含 SSDT 與 Visual Studio 2019 的支援，以及與 Azure DevOps 的原生平台整合，為企業層級部署提供內建的持續整合和部署 (CI/CD) 功能。 |
|**具體化檢視 (GA)**|具體化檢視會保存從檢視定義查詢傳回的資料，並在底層資料表中的資料變更時自動取得更新。 它可以改進複雜查詢 (通常是具有聯結與會總的查詢) 的效能，同時提供簡單的維護作業。 如需詳細資訊，請參閱[使用具體化檢視進行效能微調](performance-tuning-materialized-views.md)。  安裝 [SQL Server Management Studio 18.4 或更新版本](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)以編寫具體化檢視的指令碼。|
|**動態資料遮罩 (GA)**|動態資料遮罩 (DDM) 會根據您定義的遮罩規則，在查詢結果中即時模糊化，以防止未經授權存取您資料倉儲中的敏感性資料。 如需詳細資訊，請參閱 [SQL Database 動態資料遮罩](../../azure-sql/database/dynamic-data-masking-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。|
|**讀取認可快照隔離 (GA)**|您可以使用 ALTER DATABASE 來啟用或停用使用者資料庫的快照隔離。 若要避免影響目前的工作負載，您可以在資料庫維護期間設定此選項，或等到資料庫沒有其他作用中連線時進行設定。 如需詳細資訊，請參閱 [ALTER DATABASE SET 選項](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。|
|**已排序的叢集資料行存放區索引 (GA)**|資料行存放區是用來儲存和有效率地查詢大量資料的關鍵啟用程式。 已排序的叢集資料行存放區索引會藉由啟用有效率區段刪除，進一步最佳化查詢執行。   如需詳細資訊，請參閱[使用已排序的叢集資料行存放區索引進行效能微調](performance-tuning-ordered-cci.md)。|
|**結果集快取 (GA)**|啟用結果集快取時，會在使用者資料庫中自動快取查詢結果，以供重複使用。 這可讓後續的查詢執行直接從永續性快取取得結果，因此不需要重新計算。 結果集快取可改善查詢效能並減少計算資源使用量。 此外，使用快取結果集的查詢不會使用任何並行位置，因此不會計入現有的並行限制。 基於安全性，如果使用者與建立快取結果的使用者具有相同的資料存取權限，則只能存取快取的結果。 如需詳細資訊，請參閱[使用結果集快取進行效能微調](performance-tuning-result-set-caching.md)。 適用於 10.0.10783.0 版或更新版本。|

## <a name="september-2019"></a>2019 年 9 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**Azure Private Link (預覽)**|使用 [Azure Private Link](https://azure.microsoft.com/blog/announcing-azure-private-link/)，您可以在您的虛擬網路 (VNet) 中建立私人端點，並且將其對應至您的 SQL 集區。 接著，您可以透過 VNet 中的私人 IP 位址來存取這些資源，以啟用透過 Azure ExpressRoute 私人對等互連和/或 VPN 閘道的內部部署連線。 整體來說，因為不需要您將其開啟到公用 IP 位址，所以可以簡化網路設定。 也可以保護資料外洩風險。 如需詳細資訊，請參閱[概觀](../../private-link/private-link-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)和 [SQL Analytics 文件](../../azure-sql/database/private-endpoint-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。|
|**資料探索與分類 (GA)**|[資料探索與分類](../../azure-sql/database/data-discovery-and-classification-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)功能現已正式推出。 這項功能提供**探索、分類、標記與保護**資料庫中敏感性資料的進階功能。|
|**Azure Advisor 單鍵整合**|Azure Synapse 中的 SQL Analytics 現在會直接與概觀刀鋒視窗中的 Azure Advisor 建議整合，並提供單鍵體驗。 您現在可以在概觀刀鋒視窗中探索建議，而不用瀏覽至 Azure Advisor 刀鋒視窗。 在[這裡](sql-data-warehouse-concept-recommendations.md)了解更多建議。|
|**讀取認可快照隔離 (預覽)**|您可以使用 ALTER DATABASE 來啟用或停用使用者資料庫的快照隔離。  若要避免影響目前的工作負載，您可以在資料庫維護期間設定此選項，或等到資料庫沒有其他作用中連線時進行設定。 如需詳細資訊，請參閱 [ALTER DATABASE SET 選項](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。|
|**EXECUTE AS (Transact-SQL)**| [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL 支援現在可以讓客戶將工作階段的執行內容設定給指定使用者。|
|**其他 T-SQL 支援**|Synapse SQL 的 T-SQL 語言介面區已擴充為包含以下支援： </br> - [FORMAT (Transact-SQL)](/sql/t-sql/functions/format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [TRY_PARSE (Transact-SQL)](/sql/t-sql/functions/try-parse-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [TRY_CAST (Transact-SQL)](/sql/t-sql/functions/try-cast-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [TRY_CONVERT (Transact-SQL)](/sql/t-sql/functions/try-convert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [sys.user_token (Transact-SQL)](/sql//relational-databases/system-catalog-views/sys-user-token-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|

## <a name="july-2019"></a>2019 年 7 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**具體化檢視 (預覽)**|具體化檢視會保存從檢視定義查詢傳回的資料，並在底層資料表中的資料變更時自動取得更新。 它可以改進複雜查詢 (通常是具有聯結與會總的查詢) 的效能，同時提供簡單的維護作業。 如需詳細資訊，請參閱 </br> - [CREATE MATERIALIZED VIEW AS SELECT &#40;Transact-SQL&#41;](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [ALTER MATERIALIZED VIEW &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-materialized-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) </br> - [Synapse SQL 中支援的 T-SQL 陳述式](sql-data-warehouse-reference-tsql-statements.md)|
|**其他 T-SQL 支援**|Synapse SQL 的 T-SQL 語言介面區已擴充為包含以下支援： </br> - [AT TIME ZONE (Transact-SQL)](/sql/t-sql/queries/at-time-zone-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [STRING_AGG (Transact-SQL)](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**結果集快取 (預覽)**|已新增 DBCC 命令以管理先前宣佈的結果集快取。 如需詳細資訊，請參閱 </br> - [DBCC DROPRESULTSETCACHE &#40;Transact-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  </br> - [DBCC SHOWRESULTCACHESPACEUSED &#40;Transact-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) </br></br> 另請參閱 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 中新的 result_set_cache 資料行，其中顯示執行快取使用結果集快取的時機。|
|**已排序的叢集資料行存放區索引 (預覽)**|新的資料行 column_store_order_ordinal 已新增至 [sys.index_columns](/sql/relational-databases/system-catalog-views/sys-index-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)，以識別已排序的叢集資料行存放區索引中資料行的順序。|

## <a name="may-2019"></a>2019 年 5 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**動態資料遮罩 (預覽)**|動態資料遮罩 (DDM) 會根據您定義的遮罩規則，在查詢結果中即時模糊化，以防止未經授權存取您資料倉儲中的敏感性資料。 如需詳細資訊，請參閱 [SQL Database 動態資料遮罩](../../azure-sql/database/dynamic-data-masking-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。|
|**工作負載重要性現已正式推出**|工作負載管理分類和重要性提供影響查詢執行順序的能力。 如需工作負載重要性的詳細資訊，請參閱文件中的[分類](sql-data-warehouse-workload-classification.md)和[重要性](sql-data-warehouse-workload-importance.md)概觀文章。 另請參閱 [CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 文件。<br/><br/>請參閱下列影片中作用中的工作負載重要性：<br/> -[工作負載管理概念](https://www.youtube.com/embed/QcCRBAhoXpM)<br/> -[工作負載管理案例](https://www.youtube.com/embed/_2rLMljOjw8)|
|**其他 T-SQL 支援**|Synapse SQL 的 T-SQL 語言介面區已擴充為包含以下支援： </br> - [TRIM](/sql/t-sql/functions/trim-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**JSON 函式**|商務分析師現在可以使用熟悉的 T-SQL 語言，利用下列新的 JSON 函式來查詢和操作格式化為 JSON 資料的文件：</br> - [ISJSON](/sql/t-sql/functions/isjson-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> -  [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> -  [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**結果集快取 (預覽)**|結果集快取可啟用即時查詢回應時間，同時減少商務分析師和報告使用者的深入解析時間。 如需詳細資訊，請參閱</br> - [ALTER DATABASE (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [ALTER DATABASE SET 選項 (Transact SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [SET RESULT SET CACHING (Transact-SQL)](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [SET 陳述式 (Transact-SQL)](/sql/t-sql/statements/set-statements-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [sys.databases (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**已排序的叢集資料行存放區索引 (預覽)**|資料行存放區是用來儲存和有效率地查詢大量資料的關鍵啟用程式。 針對每個資料表，會將傳入的資料分割成資料列群組，而資料列群組的每個資料行會形成磁碟上的區段。  已排序的叢集資料行存放區索引會藉由啟用有效率區段刪除，進一步最佳化查詢執行。   如需詳細資訊，請參閱</br> -  [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> -  [CREATE COLUMNSTORE INDEX (Transact-SQL)](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。|

## <a name="march-2019"></a>2019 年 3 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**資料探索與分類**|Synapse SQL 的資料探索與分類現在提供公開預覽。 保護敏感性資料和客戶隱私權是不可或缺的。 隨著您的業務和客戶資料資產成長，探索、分類和保護您的資料會變得難以管理。 我們在 Synapse SQL 中以原生方式引進的資料探索和分類功能，有助於讓您的資料更容易管理。 這項功能的整體優點如下：<br/>&bull; &nbsp; 符合資料隱私標準和法規合規性需求。<br/>&bull; &nbsp; 對包含高度敏感性資料的資料倉儲限制存取權並強化安全性。<br/>&bull; &nbsp; 對敏感性資料的異常存取進行監視和警示。<br/>&bull; &nbsp; Azure 入口網站上中央儀表板的敏感性資料視覺效果。 </br></br>資料探索與分類在所有 Azure 區域都可以使用，是進階資料安全性的一部分，包括弱點評估和威脅偵測。 如需資料探索與分類的詳細資訊，請參閱[部落格文章](https://azure.microsoft.com/blog/announcing-public-preview-of-data-discovery-classification-for-microsoft-azure-sql-data-warehouse/)和我們的線上[文件](../../azure-sql/database/data-discovery-and-classification-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。|
|**GROUP BY ROLLUP**|ROLLUP 現在是支援的 GROUP BY 選項。   GROUP BY ROLLUP 會為每個資料行運算式的組合建立群組。 GROUP BY 也會將結果「積存」到小計和總計。 GROUP BY 函式會從右向左進行處理，減少建立群組和彙總的資料行運算式數目。  資料行順序會影響 ROLLUP 的輸出，也會影響結果集中的資料列數。<br/><br/>如需 GROUP BY ROLLUP 的詳細資訊，請參閱 [GROUP BY (Transact-SQL)](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
|**已改善已使用 DWU 和 CPU 入口網站計量的精確度**|Synapse SQL 大幅增強了 Azure 入口網站中的計量精確度。  此版本包含已使用 CPU 和 DWU 計量定義的修正，可正確反映所有計算節點上的工作負載。 在此修正之前，會短報計量值。 預期會在 Azure 入口網站中看到已使用 DWU 和 CPU 計量增加。 |
|**資料列層級安全性**|我們在 2017 年 11 月引進了資料列層級安全性功能。 我們現在也將這個支援延伸到外部資料表。 此外，我們也新增了在定義安全性篩選述詞所需的內嵌資料表值函式 (內嵌 TVF) 中，呼叫非決定性函式的支援。 這項新增可讓您在安全性篩選述詞中指定 IS_ROLEMEMBER()、USER_NAME() 等等。 如需詳細資訊，請參閱[資料列層級安全性文件](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)中的範例。|
|**其他 T-SQL 支援**|Synapse SQL 的 T-SQL 語言介面區已擴充為包含 [STRING_SPLIT (Transact-SQL)](/sql/t-sql/functions/string-split-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 的支援。
|**查詢最佳化工具增強功能** |查詢最佳化是任何資料庫的重要元件。 對如何最佳執行查詢做出最佳選擇，會產生顯著的改善。  在分散式環境中執行複雜的分析查詢時，執行的作業數目很重要。 藉由產生較佳的品質計劃，增強了查詢效能。 這些計劃會將昂貴的資料傳輸作業和冗餘計算 (例如，重複的子查詢) 降到最低。 如需詳細資訊，請參閱此 Azure Synapse [部落格文章](https://azure.microsoft.com/blog/smarter-faster-safer-azure-sql-data-warehouse-is-simply-unmatched/)。|
| | |

### <a name="documentation-improvements"></a>文件改進

| 文件改進 | 詳細資料 |
| --- | --- |
| | |

## <a name="january-2019"></a>2019 年 1 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**傳回 Order By 最佳化**|SELECT…ORDER BY 查詢會在此版本中獲得效能提升。   現在，所有計算節點都會將其結果傳送至單一計算節點。 此節點會合併及排序結果，並將其傳回給使用者。  當查詢結果集包含大量資料列時，透過單一計算節點進行合併會讓效能顯著提升。 在過去，查詢執行引擎會在每個計算節點上排序結果。 然後結果會串流處理至控制節點。 接著，控制節點會合併結果。|
|**適用於 PartitionMove 和 BroadcastMove 的資料移動增強功能**|ShuffleMove 類型的資料移動步驟，使用即時資料移動技術。  如需詳細資訊，請參閱[效能增強功能部落格](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)。 在此版本中，PartitionMove 和 BroadcastMove 現在可受到相同即時資料移動技術的支援。 使用這些類型資料移動步驟的使用者查詢，將會改進執行的效能。 您無須變更任何程式碼，即可享有這些改進的效能。|
|**值得注意的錯誤 (bug)**|錯誤的 Azure Synapse 版本 - `SELECT @@VERSION` 可能會傳回錯誤的版本，10.0.9999.0。 目前所發行的正確版本是 10.0.10106.0。 此錯誤 (bug) 已經被回報並正在進行檢閱。
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
|**虛擬網路服務端點已正式推出**|此版本具有所有 Azure 區域內 Azure Synapse 中 SQL Analytics 虛擬網路 (VNet) 服務端點的一般可用性。 VNet 服務端點可讓您從虛擬網路內的指定子網或一組子網，將連線能力隔離到您的伺服器。 從 VNet 傳送至 Azure Synapse 的流量一律會保留在 Azure 骨幹網路中。 這會優先使用此直接路由，而不是透過虛擬設備或內部部署傳送網際網路流量的任何特定路由。 透過服務端點存取虛擬網路並不會額外收費。 [Azure Synapse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/) 的目前定價模型照常適用。<br/><br/>透過此版本，我們也讓 PolyBase 能夠透過 [Azure Blob File System](../../storage/blobs/data-lake-storage-abfs-driver.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (ABFS) 驅動程式連線到 [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (ADLS)。 Azure Data Lake Storage Gen2 所具備的品質，足以滿足 Azure 儲存體分析資料完整生命週期的一切所需。 兩個現有 Azure 儲存體服務 (Azure Blob 儲存體與 Azure Data Lake Storage Gen1) 的功能均涵蓋在內。 [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 的功能 (例如檔案系統語意、檔案層級安全性和級別) 結合了 [Azure Blob 儲存體](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)的低成本、分層式儲存體、高可用性/災害復原功能。<br/><br/>透過使用 Polybase，您也可將資料從固定於 VNet 的 Azure 儲存體匯入到 Azure Synapse 中的 SQL Analytics。 同樣的，也支援經由 PolyBase 將資料從 Azure Synapse 匯出至固定於 VNet 的 Azure 儲存體。<br/><br/>如需 Azure Synapse 中 VNet 服務端點的詳細資訊，請參閱[部落格文章](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/)或[文件](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。|
|**自動效能監視 (預覽)**|[查詢存放區](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)現已在 Azure Synapse 的 SQL Analytics 中提供預覽。 查詢存放區的設計目的是透過追蹤查詢、查詢計劃、執行階段統計資料以及查詢記錄等方式，協助您監視資料倉儲的活動與效能，藉此排解查詢效能的問題。 查詢存放區是一組內容存放區與「動態管理檢視」(DMV)，具備以下功能：<br/><br/>&bull; &nbsp; 識別與調整資源使用查詢<br/>&bull; &nbsp; 識別並改善未計劃工作負載<br/>&bull; &nbsp; 透過變更統計資料、索引或系統大小 (DWU 設定) 的方式，評估查詢的效能以及對計劃的影響<br/>&bull; &nbsp; 請查看所有已執行查詢的完整查詢文字<br/><br/>查詢存放區包含三個實際存放區：<br/>&bull; &nbsp; 計劃存放區，用於保存執行計劃資訊<br/>&bull; &nbsp; 執行階段統計資料存放區，用於保存查詢執行統計資料資訊<br/>&bull; &nbsp; 等候統計資料存放區，用於保存等候統計資料資訊。<br/><br/>Azure Synapse 中的 SQL Analytics 會自動管理這些存放區，並免費提供過去七天儲存的查詢，數量不受限。 啟用查詢存放區很簡單，只要執行 ALTER DATABASE T-SQL 陳述式即可： <br/>sql ----ALTER DATABASE [DatabaseName] SET QUERY_STORE = ON;-------如需查詢存放區的詳細資訊，請參閱以下文章，[使用查詢存放區監視效能](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)，和查詢存放區 DMV，例如 [sys.query_store_query](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。 以下是版本發佈的[部落格文章](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/) (英文)。|
|**SQL Analytics 的較低計算層**|Azure Synapse 中的 SQL Analytics 現在支援較低的計算層。 客戶可從 100 cDWU ([資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)) 開始使用，在數分鐘內調整為 30,000 cDWU，體驗 Azure Synapse 卓越的效能、彈性及安全性功能。 自 2018 年 12 月中起，這些[地區](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)的客戶即可在較低的運算層級享有 Gen2 的效能和彈性，其餘地區則會在 2019 年跟進。<br/><br/>注重價值的客戶希望能全面評估安全的高效能資料倉儲的所有益處，且不想要猜測決定最適合的試用環境，而 Microsoft 降低了次世代資料倉儲服務的入門門檻，對這些客戶敞開了大門。 客戶最低可從 100 cDWU 開始，比目前 500 cDWU 的入門門檻更低。 SQL Analytics 會進一步支援暫停和繼續的作業，不單只將彈性侷限在運算方面。 Gen2 也支援無限制資料行存放區的儲存體容量，每個查詢的記憶體增加 2.5 倍，最多可達 128 個並行查詢，還具備[調適性快取](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/)功能。 與同價格的 Gen1 一樣的資料倉儲單位相比，上述功能的效能平均高出五倍。 在保證內建資料保護功能的 Gen2 中，異地備援備份是標準功能。 Azure Synapse 中的 SQL Analytics 已經準備好進行調整。|
|**資料行存放區背景合併**|根據預設，Azure SQL 資料會使用稱為[資料列群組](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)的微型分割，以資料行格式儲存資料。 有時候，因為在建置索引或載入資料時記憶體受到限制，資料列群組可能會經過壓縮，且使用的壓縮大小低於最佳的 1 百萬個資料列。 資料列群組也可能會因為刪除操作而變得零碎。 小型或零碎的資料列群組會耗用更高的記憶體，執行查詢時的效率也不好。 使用這個版本時，資料行存放區的背景維護工作會合併經過壓縮的小型資料列群組，以建立較大型的資料列群組，從而更加充分地利用記憶體並加快查詢的執行速度。
| | |

## <a name="october-2018"></a>2018 年 10 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**資料倉儲的 DevOps**|Azure Synapse 中的 Synapse SQL 高度要求功能目前為預覽狀態，Visual Studio 中支援 SQL Server Data Tool (SSDT)！ 開發人員小組現在可以透過單一的版本控制程式碼基底執行協同作業，並快速地將變更部署到全球任何執行個體。 有興趣加入嗎？ 此功能目前可供預覽！ 您可以透過造訪 [Visual Studio SQL Server Data Tools (SSDT) - 預覽註冊表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)進行註冊。 鑑於需求量很大，我們正在對預覽進行驗收，以確保為客戶提供最佳體驗。 註冊後，我們的目標是在七個工作日內確認您的狀態。|
|**資料列層級安全性正式推出**|Azure Synapse 中的 Synapse SQL 現在支援資料列層級安全性 (RLS)，增加了保護機密資料的強大功能。 藉由引進 RLS，您可以實作安全性原則來控制對資料表中資料列的存取，例如誰可以存取哪些資料列。 RLS 支援這種更細緻的存取控制，而不必重新設計您的資料倉儲。 RLS 簡化了整體安全性模型，因為存取限制邏輯位於資料庫層本身，而不是遠離另一個應用程式中的資料。 RLS 還消除了導入檢視以篩選掉用於存取控制管理之資料列的需要。 我們為所有客戶提供此企業級安全性功能，無需任何額外的費用。|
|**進階的建議程式**|Azure Synapse 中的 Synapse SQL 進階微調透過額外的資料倉儲相關建議和計量，變得更加簡單。 Azure Advisor 還提供其他進階的效能建議，包括：<br/><br/>1.自適性快取 – 取得何時調整規模以發揮最佳快取使用率的建議。<br/>2.資料表散發 – 判斷何時複寫資料表以減少資料移動並提升工作負載效能。<br/>3.Tempdb – 了解何時調整和設定資源類別，以減少 tempdb 競爭。<br/><br/>資料倉儲計量與 [Azure 監視器](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/)的整合更加深入，包括功能更強的可自訂監視圖表，能在概觀刀鋒視窗中提供近乎即時的計量。 當您監視使用量，或是驗證並套用資料倉儲建議時，不再需要離開資料倉儲概觀刀鋒視窗，即可存取 Azure 監視器計量。 此外，也提供新的計量 (例如 tempdb 及自適性快取使用率)，作為您效能建議的補充。|
|**使用整合式建議程式進行進階微調**|Azure Synapse 的進階微調透過額外的資料倉儲相關建議和計量以及入口網站概觀刀鋒視窗的重新設計，變得更加簡單，該刀鋒視窗提供了 Azure Advisor 和 Azure 監視器的整合式體驗。|
|**加速資料庫復原 (ADR)**|Azure Synapse 加速資料庫復原 (ADR) 現在處於公開預覽狀態。 ADR 是新的 SQL 資料庫引擎功能，藉由完整重新設計目前的復原程序，來大幅改善資料庫可用性 (特別是在有長時間執行的交易時)。 ADR 的主要優點是快速且一致的資料庫復原，以及即時的交易回復。|
|**Azure 監視器資源記錄**|藉由直接與 Azure 監視器資源記錄整合，Azure Synapse 現在提供更強的分析工作負載深入解析。 這項新功能可讓開發人員分析更長一段時間內的工作負載行為，並在充分了解資訊的情況下，做出查詢最佳化或容量管理決策。 我們現在透過 [Azure 監視器資源記錄](../../azure-monitor/platform/data-platform.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#logs)引進了外部的記錄處理程序，這些記錄提供了有關資料倉儲工作負載的額外深入解析。 只要按一下按鈕，您就可以使用 [Log Analytics](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 為歷程查詢效能疑難排解功能設定資源記錄。 Azure 監視器資源記錄透過將記錄儲存至儲存體帳戶以供稽核，來支援可自訂的保留期間；也支援將記錄串流至事件中樞，以獲得近乎即時的遙測深入解析功能；以及使用具有記錄查詢的 Log Analytics 來分析記錄的功能。 資源記錄是由您資料倉儲的遙測檢視所組成，其等同於Azure Synapse 中的 Synapse SQL 最常使用的效能疑難排解 DMV。 我們已為此初始版本啟用下列系統動態管理檢視的檢視：<br/><br/>&bull; &nbsp; [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**資料行存放區記憶體管理**|隨著壓縮的資料行存放區資料列群組數目增加，管理這些資料列群組的內部資料行區段中繼資料所需的記憶體會隨之增加。  因此，查詢效能和針對某些資料行存放區動態管理檢視 (Dmv) 所執行的查詢可能會降級。  此版本已進行改善，讓這些情況的內部中繼資料大小達到最佳化，進而改善這類查詢的體驗和效能。|
|**Azure Data Lake Storage Gen2 整合 (GA)**|Synapse Analytics 現在已與 Azure Data Lake Storage Gen2 原生整合。 客戶現在可以使用 ABFS 中的外部資料表將資料載入至 Synapse SQL 集區。 這項功能可讓客戶與其 Data Lake Storage Gen2 中的 Data Lake 整合。|
|**值得注意的錯誤 (bug)**|資料倉儲 DW2000 上小型資源類別中的 CETAS to Parquet 失敗及其他 - 此修正程式正確地識別出 Create External Table As to Parquet 程式碼路徑中的 Null 參考。<br/><br/>在某些 CTAS 作業中，識別欄位值可能會遺失 - 在 CTASed 至另一個資料表時，可能不會保留識別欄位的值。 在[部落格](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)中報告。<br/><br/>當查詢仍在執行時，在某些情況下終止工作階段會發生內部失敗 - 如果在查詢仍在執行時終止工作階段，則此修正會觸發 InvalidOperationException。<br/><br/>(已在 2018 年 11 月部署) 當客戶嘗試使用 PolyBase 從 ADLS (Gen1) 載入多個小型檔案時，會有次佳的效能體驗。 - 系統效能會在 AAD 安全性權杖驗證期間受到阻礙。 效能問題已藉由啟用安全性權杖的快取來降低影響。 |
| | |

## <a name="next-steps"></a>後續步驟

- [建立 SQL 集區](create-data-warehouse-portal.md)

## <a name="more-information"></a>詳細資訊

- [部落格 - Azure Synapse Analytics](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [客戶諮詢小組部落格](https://docs.microsoft.com/archive/blogs/sqlcat/)
- [客戶成功案例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [影片](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 詞彙](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
