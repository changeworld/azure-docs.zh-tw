---
title: 專用的 SQL 集區 (先前為 SQL DW) 架構
description: 瞭解 Azure Synapse Analytics 的專用 SQL 集區 (先前的 SQL DW) 如何結合分散式查詢處理功能與 Azure 儲存體，以達到高效能和擴充性。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: c537d3109f770c1ce77be6495bdacd24270dad5f
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98119589"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-architecture-in-azure-synapse-analytics"></a>專用的 SQL 集區 (先前的 SQL DW) 架構 Azure Synapse Analytics

Azure Synapse Analytics 是一種分析服務，可將企業資料倉儲和巨量資料分析整合在一起。 它可讓您自由地依據您的條件來查詢資料。

> [!NOTE]
>探索 [Azure Synapse Analytics 文件](../overview-what-is.md)。
>


> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-architecture-components"></a>Synapse SQL 架構元件

[專用的 sql 集區 (先前的 SQL DW) ](sql-data-warehouse-overview-what-is.md) 會利用向外延展架構，將資料的計算處理散發到多個節點。 縮放單位是稱為[資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)之計算能力的抽象概念。 計算與儲存體分隔開來，讓您可以在系統中單獨調整資料的計算。

![專用的 SQL 集區 (先前為 SQL DW) 架構](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

專用的 SQL 集區 (先前的 SQL DW) 使用以節點為基礎的架構。 應用程式會連接併發出 T-sql 命令到控制節點。 控制節點裝載分散式查詢引擎，可將查詢優化以進行平行處理，然後將作業傳遞到計算節點，以平行方式執行其工作。

計算節點會在 Azure 儲存體中儲存所有使用者資料，並執行平行查詢。 資料移動服務 (DMS) 是系統層級的內部服務，其會視需要在節點之間移動資料，以平行方式執行查詢並傳回精確的結果。

使用分離的儲存和計算時，使用專用的 SQL 集區時， (先前的 SQL DW) 一種：

- 獨立估算計算能力，而不論您的儲存體需求為何。
- 在專用的 SQL 集區中增加或減少計算能力， (先前的 SQL DW) ，而不需要移動資料。
- 暫停計算容量，同時讓資料保持不變，因此您只需支付儲存體的費用。
- 在營運時間期間繼續計算容量。

### <a name="azure-storage"></a>Azure 儲存體

專用 SQL 集區 SQL (先前的 SQL DW) 利用 Azure 儲存體來保護您的使用者資料安全。  因為您的資料是由 Azure 儲存體儲存及管理的，所以您的儲存體耗用量會分開計費。 資料會分區化到 **散發** 中，以將系統效能最佳化。 當您定義資料表時，可以選擇要用來散發資料的分區化模式。 支援以下分區化模式：

- 雜湊
- 循環配置資源
- 複寫

### <a name="control-node"></a>控制節點

控制節點是架構的大腦。 它是與所有應用程式與連接互動的前端。 分散式查詢引擎會在控制節點上執行，以優化和協調平行查詢。 當您提交 T-SQL 查詢時，控制節點會將其轉換為要根據每個散發平行執行的查詢。

### <a name="compute-nodes"></a>計算節點數量

計算節點提供計算能力。 散發會對應到計算節點以進行處理。 當您需要支付更多計算資源的費用時，散發會重新對應到可用的計算節點。 計算節點數目範圍是從 1 到 60，取決於 Synapse SQL 的服務等級。

每個計算節點都有會在系統檢視中顯示的節點識別碼。 您可以在名稱開頭為 sys.pdw_nodes 的系統檢視中尋找 node_id 資料行，以查看計算節點識別碼。 如需這些系統檢視的清單，請參閱 [SYNAPSE SQL 系統檢視](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

### <a name="data-movement-service"></a>資料移動服務

資料移動服務 (DMS) 是資料傳輸技術，可協調計算節點之間的資料移動。 某些查詢需要資料移動，以確保平行查詢會傳回精確的結果。 若資料移動是必要的，DMS 確保正確的資料會到達正確的位置。

## <a name="distributions"></a>散發

散發是儲存體的基本單位，可處理在分散式資料上執行的平行查詢。 當 Synapse SQL 執行查詢時，會將工作分成 60 個平行執行的較小查詢。

這 60 個較小查詢中的每一個都會在其中一個資料散發中執行。 每個計算節點都會管理這 60 個散發中的一或多個。 專用的 SQL 集區 (先前的 SQL DW) 具有最多計算資源，每個計算節點都有一個散發。 專用的 SQL 集區 (先前的 SQL DW) 具有最少的計算資源會在一個計算節點上擁有所有散發。  

## <a name="hash-distributed-tables"></a>雜湊分散式資料表

雜湊分散式資料表可以針對大型資料表上的聯結和彙總提供最高查詢效能。

為了將資料分區化到雜湊分散式資料表，系統會使用雜湊函式，決定性地將每個資料列指派給一個散發。 在資料表定義中，其中一個資料行會指定為發佈資料行。 雜湊函式會使用散發資料行中的值，將每個資料列指派給一個散發。

下圖說明完整 (非分散式資料表) 會如何儲存為雜湊分散式資料表。

![分散式資料表](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "分散式資料表")  

- 每個資料列屬於一種發佈。  
- 具決定性的雜湊演算法會將每個資料列指派給一個發佈。  
- 每個發佈的資料表資料列數目會隨著顯示不同資料表大小而有所不同。

選取散發資料行有效能考量，例如區分、資料扭曲，以及在系統上執行的查詢類型。

## <a name="round-robin-distributed-tables"></a>循環配置資源分散式資料表

循環配置資源資料表是要建立的最簡單資料表，可以在用來作為暫存資料表以供載入使用時提供快速效能。

循環配置資源分散式資料表會在整個資料表中平均散發資料，但不需任何進一步最佳化。 系統會先隨機選擇一個散發，然後將資料列的緩衝區循序指派給散發。 將資料快速載入循環配置資源資料表，但搭配雜湊分散式資料表通常會有較佳的查詢效能。 循環配置資源資料表上的聯結需要重新輪換資料，這需要額外時間。

## <a name="replicated-tables"></a>複寫的資料表

複寫的資料表會為小型資料表提供最快速的查詢效能。

複寫的資料表會在每個計算節點上快取一份完整的資料表複本。 因此，複寫資料表就不需在進行聯結或彙總之前，於計算節點之間傳輸資料。 複寫的資料表最好能與小型資料表搭配使用。 需要額外的儲存體，而且，會在寫入資料而導致大型資料表不適用時產生額外的負擔。  

下圖顯示會在每個計算節點中第一個散發上快取的複寫資料表。  

![複寫資料表](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "複寫的資料表")

## <a name="next-steps"></a>後續步驟

既然您已瞭解 Azure Synapse，請瞭解如何快速 [建立專用的 sql 集區 (先前的 SQL DW) ](create-data-warehouse-portal.md) 並 [載入範例資料](./load-data-from-azure-blob-storage-using-copy.md)。 如果您不熟悉 Azure，您可能會發現 [Azure 詞彙](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 在您遇到新術語時很有幫助。 或者，您也可以看看以下其他 Azure Synapse 資源。  

- [客戶成功案例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [部落格](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [功能要求](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [影片](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [建立支援票證](sql-data-warehouse-get-started-create-support-ticket.md)
- [Microsoft 問與答頁面](/answers/topics/azure-synapse-analytics.html)
- [Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)