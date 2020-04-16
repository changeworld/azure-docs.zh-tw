---
title: Azure 突觸 SQL 架構結構
description: 瞭解 Azure Synapse SQL 如何將大規模並行處理 (MPP) 與 Azure 儲存相結合,以實現高性能和可擴充性。
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 570e84b3a545736aad6983c7f0d8c0f0296ca589
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431809"
---
# <a name="azure-synapse-sql-architecture"></a>Azure 突觸 SQL 架構結構 

本文介紹了 Synapse SQL 的體系結構元件。

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>突觸 SQL 架構結構元件

Synapse SQL 利用橫向擴充架構結構跨多個節點分發數據的計算處理。 計算與儲存是分開的,使您能夠獨立於系統中的數據進行縮放計算。 

對於 SQL 池,比例單位是計算能力的抽象,稱為[資料倉儲單元](resource-consumption-models.md)。 

對於 SQL 按需(無伺服器),自動執行縮放以適應查詢資源需求。 由於拓撲通過添加、刪除節點或故障轉移來隨時間而變化,它可適應更改並確保查詢有足夠的資源並成功完成。 例如,下圖顯示了 SQL 按需使用 4 個計算節點執行查詢。

![突觸 SQL 架構結構](./media//overview-architecture/sql-architecture.png)

突觸 SQL 使用基於節點的體系結構。 應用程式將 T-SQL 命令連接到 Control 節點,這是 Synapse SQL 的單一入口點。 

SQL 池控制節點利用 MPP 引擎優化查詢進行並行處理,然後將操作傳遞給計算節點以並行執行其工作。 

SQL 按需控制節點利用分散式查詢處理 (DQP) 引擎,通過將使用者查詢拆分為將在計算節點上執行的較小查詢來優化和協調使用者查詢的分散式執行。 每個小查詢都稱為任務,表示分散式執行單元。 它從存儲讀取檔、聯接從其他任務、組或從其他任務檢索的數據的結果。 

計算節點會在 Azure 儲存體中儲存所有使用者資料，並執行平行查詢。 資料移動服務 (DMS) 是系統層級的內部服務，其會視需要在節點之間移動資料，以平行方式執行查詢並傳回精確的結果。 

使用分離存儲和計算時,無論存儲需求如何,使用 Synapse SQL 都可以從獨立調整計算能力大小中獲益。 對於 SQL 按需縮放,自動完成,而對於 SQL 池,可以:

* 在 SQL 池(數據倉庫)內增大或縮小計算能力,而無需移動數據。
* 暫停計算容量，同時讓資料保持不變，因此您只需支付儲存體的費用。
* 在營運時間期間繼續計算容量。

## <a name="azure-storage"></a>Azure 儲存體

Synapse SQL 利用 Azure 儲存來保證使用者數據的安全。 由於數據由 Azure 存儲存儲存儲和管理,因此存儲消耗需要單獨收費。 

SQL 按需允許您以唯讀方式查詢資料湖中的檔,而 SQL 池還允許您引入資料。 將資料引入 SQL 池時,數據將分片化為**分佈**,以優化系統的性能。 當您定義資料表時，可以選擇要用來散發資料的分區化模式。 支援這些分片模式:

* 雜湊
* 循環配置資源
* 複寫

## <a name="control-node"></a>控制節點

控制節點是體系結構的大腦。 它是與所有應用程式與連接互動的前端。 

在 SQL 池中,MPP 引擎在 Control 節點上運行以優化和協調並行查詢。 將 T-SQL 查詢提交到 SQL 池時,Control 節點將其轉換為並行針對每個分佈運行的查詢。

在 SQL 按需中,DQP 引擎在 Control 節點上運行,通過將使用者查詢拆分為將在計算節點上執行的較小查詢,從而優化和協調用戶查詢的分散式執行。 它還分配要由每個節點處理的檔集。

## <a name="compute-nodes"></a>計算節點數量

計算節點提供計算能力。 

在 SQL 池中,分佈映射到計算節點進行處理。 在為更多計算資源付費時,池將分佈重新映射到可用的計算節點。 計算節點的數量範圍從 1 到 60,由 SQL 池的服務級別決定。 每個計算節點都有會在系統檢視中顯示的節點識別碼。 您可以在名稱開頭為 sys.pdw_nodes 的系統檢視中尋找 node_id 資料行，以查看計算節點識別碼。 如需這些系統檢視的清單，請參閱 [MPP 系統檢視](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest)。

在 SQL 按需中,為每個計算節點分配任務和要執行任務的檔集。 任務是分散式查詢執行單元,實際上是提交查詢使用者的一部分。 自動縮放有效,以確保使用足夠的計算節點執行用戶查詢。

## <a name="data-movement-service"></a>資料移動服務

數據行動服務 (DMS) 是 SQL 池中協調計算節點之間的數據行動的數據傳輸技術。 某些查詢需要資料移動，以確保平行查詢會傳回精確的結果。 若資料移動是必要的，DMS 確保正確的資料會到達正確的位置。

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>散發

分佈是在 SQL 池中分散式數據上運行的並行查詢的基本存儲和處理單元。 當 SQL 池運行查詢時,工作被劃分為 60 個並行運行的較小查詢。 

這 60 個較小查詢中的每一個都會在其中一個資料散發中執行。 每個計算節點都會管理這 60 個散發中的一或多個。 具有最大計算資源的 SQL 池每個計算節點有一個分佈。 具有最小計算資源的 SQL 池具有一個計算節點上的所有分佈。 

## <a name="hash-distributed-tables"></a>雜湊分散式資料表
雜湊分散式資料表可以針對大型資料表上的聯結和彙總提供最高查詢效能。 

要將數據分片分到哈希分佈的表中,SQL 池使用哈希函數確定將每行分配給一個分佈。 在資料表定義中，其中一個資料行會指定為發佈資料行。 雜湊函式會使用散發資料行中的值，將每個資料列指派給一個散發。

下圖說明完整 (非分散式資料表) 會如何儲存為雜湊分散式資料表。 

![分散式資料表](media//overview-architecture/hash-distributed-table.png "分散式資料表") 

* 每個資料列屬於一種發佈。 
* 具決定性的雜湊演算法會將每個資料列指派給一個發佈。 
* 每個發佈的資料表資料列數目會隨著顯示不同資料表大小而有所不同。

選取散發資料行有效能考量，例如區分、資料扭曲，以及在系統上執行的查詢類型。

## <a name="round-robin-distributed-tables"></a>循環配置資源分散式資料表

循環配置資源資料表是要建立的最簡單資料表，可以在用來作為暫存資料表以供載入使用時提供快速效能。

循環配置資源分散式資料表會在整個資料表中平均散發資料，但不需任何進一步最佳化。 系統會先隨機選擇一個散發，然後將資料列的緩衝區循序指派給散發。 將資料快速載入循環配置資源資料表，但搭配雜湊分散式資料表通常會有較佳的查詢效能。 在迴圈表上的聯接需要重新洗牌數據,這需要額外的時間。

## <a name="replicated-tables"></a>複寫資料表
複寫的資料表會為小型資料表提供最快速的查詢效能。

複寫的資料表會在每個計算節點上快取一份完整的資料表複本。 因此，複寫資料表就不需在進行聯結或彙總之前，於計算節點之間傳輸資料。 複寫的資料表最好能與小型資料表搭配使用。 需要額外的存儲,並且寫入數據時會產生額外的開銷,這使得大型表不切實際。 

下圖顯示了緩存在每個計算節點上的第一個分佈上的複製表。 

![複寫的資料表](media/overview-architecture/replicated-table.png "複寫的資料表") 

## <a name="next-steps"></a>後續步驟

現在,您已經瞭解了 Synapse SQL,請瞭解如何快速[創建 SQL 池](../quickstart-create-sql-pool.md)並[載入範例資料](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)(./sql-data-warehouse-load-sample-databases.md)。 或者您開始[使用 SQL 按一個 。](../quickstart-sql-on-demand.md) 如果您不熟悉 Azure，您可能會發現 [Azure 詞彙](../../azure-glossary-cloud-terminology.md) 在您遇到新術語時很有幫助。 
