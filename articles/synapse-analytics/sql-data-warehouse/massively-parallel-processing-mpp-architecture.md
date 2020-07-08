---
title: Azure Synapse Analytics (先前稱為 SQL DW) 架構
description: 了解 Azure Synapse Analytics (先前稱為 SQL DW) 如何將大量平行處理 (MPP) 與 Azure 儲存體結合，以達到高效能和延展性。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: cde6cb514b6f87315400b3c40d8b86bcb7ff0adb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85210961"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Azure Synapse Analytics (先前稱為 SQL DW) 架構

Azure Synapse 是一種無限制的分析服務，可將企業資料倉儲和巨量資料分析整合在一起。 可讓您自由使用無伺服器隨選或佈建資源，隨意且大規模地查詢您的資料。 Azure Synapse 會將這兩個世界結合在一起，透過整合的經驗擷取、準備、管理和處理資料，以滿足立即的 BI 和機器學習需求。

 Azure Synapse 有四個元件：

- Synapse SQL：完成以 T-SQL 為基礎的分析

  - SQL 集區 (依據佈建的 DWU 付費) - 正式推出
  - SQL 隨選 (依據處理的 TB 量付費) – (預覽)
- Spark：深入整合的 Apache Spark (預覽)
- 資料整合：混合式資料整合 (預覽)
- Studio：整合的使用者體驗。  (預覽)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-mpp-architecture-components"></a>Synapse SQL MPP 架構元件

[Synapse SQL](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse) 會利用擴增架構，將資料的計算處理散發到多個節點。 縮放單位是稱為[資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)之計算能力的抽象概念。 計算與儲存體分隔開來，讓您可以在系統中單獨調整資料的計算。

![Synapse SQL 架構](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Synapse SQL 使用以節點為基礎的架構。 應用程式會連線到控制節點並發出 T-SQL 命令，這是 Synapse SQL 的單一進入點。 控制節點會執行 MPP 引擎，將查詢最佳化以進行平行處理，然後將作業傳遞到計算節點，以平行方式執行其工作。

計算節點會在 Azure 儲存體中儲存所有使用者資料，並執行平行查詢。 資料移動服務 (DMS) 是系統層級的內部服務，其會視需要在節點之間移動資料，以平行方式執行查詢並傳回精確的結果。

利用低耦合儲存體和計算，當使用 Synapse SQL 集區時，使用者可以：

- 獨立估算計算能力，而不論您的儲存體需求為何。
- 在 SQL 集區 (資料倉儲) 內增加或縮小計算能力，而不用移動資料。
- 暫停計算容量，同時讓資料保持不變，因此您只需支付儲存體的費用。
- 在營運時間期間繼續計算容量。

### <a name="azure-storage"></a>Azure 儲存體

Synapse SQL 會利用 Azure 儲存體來保護您使用者資料的安全。  因為您的資料是由 Azure 儲存體儲存及管理的，所以您的儲存體耗用量會分開計費。 資料會分區化到**散發**中，以將系統效能最佳化。 當您定義資料表時，可以選擇要用來散發資料的分區化模式。 支援以下分區化模式：

- 雜湊
- 循環配置資源
- 複寫

### <a name="control-node"></a>控制節點

控制節點是架構的大腦。 它是與所有應用程式與連接互動的前端。 MPP 引擎會在控制節點上執行，以便將平行查詢最佳化並加以協調。 當您提交 T-SQL 查詢時，控制節點會將其轉換為要根據每個散發平行執行的查詢。

### <a name="compute-nodes"></a>計算節點數量

計算節點提供計算能力。 散發會對應到計算節點以進行處理。 當您需要支付更多計算資源的費用時，散發會重新對應到可用的計算節點。 計算節點數目範圍是從 1 到 60，取決於 Synapse SQL 的服務等級。

每個計算節點都有會在系統檢視中顯示的節點識別碼。 您可以在名稱開頭為 sys.pdw_nodes 的系統檢視中尋找 node_id 資料行，以查看計算節點識別碼。 如需這些系統檢視的清單，請參閱 [MPP 系統檢視](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

### <a name="data-movement-service"></a>資料移動服務

資料移動服務 (DMS) 是資料傳輸技術，可協調計算節點之間的資料移動。 某些查詢需要資料移動，以確保平行查詢會傳回精確的結果。 若資料移動是必要的，DMS 確保正確的資料會到達正確的位置。

## <a name="distributions"></a>散發

散發是儲存體的基本單位，可處理在分散式資料上執行的平行查詢。 當 Synapse SQL 執行查詢時，會將工作分成 60 個平行執行的較小查詢。

這 60 個較小查詢中的每一個都會在其中一個資料散發中執行。 每個計算節點都會管理這 60 個散發中的一或多個。 具有最大計算資源的 SQL 集區，在每個計算節點上都有一個散發。 具有最小計算資源的 SQL 集區，其所有散發都會在一個計算節點上。  

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

現在您已稍微了解 Azure Synapse，請了解如何快速[建立 SQL 集區](create-data-warehouse-portal.md)和[載入範例資料](load-data-from-azure-blob-storage-using-polybase.md)。 如果您不熟悉 Azure，您可能會發現 [Azure 詞彙](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 在您遇到新術語時很有幫助。 或者，您也可以看看以下其他 Azure Synapse 資源。  

- [客戶成功案例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [部落格](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [功能要求](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [影片](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [建立支援票證](sql-data-warehouse-get-started-create-support-ticket.md)
- [Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
- [Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
