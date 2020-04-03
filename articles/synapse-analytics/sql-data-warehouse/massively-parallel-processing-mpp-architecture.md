---
title: Azure 突觸分析(以前是 SQL DW)體系結構
description: 瞭解 Azure 突觸分析(以前為 SQL DW)如何將大規模並行處理 (MPP) 與 Azure 儲存相結合,以實現高性能和可擴充性。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: a42ec523bb1f77c48f7382283a52565c9c9273b6
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584490"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Azure 突觸分析(以前是 SQL DW)體系結構 

Azure Synapse 是一種無限制的分析服務，可將企業資料倉儲和巨量資料分析整合在一起。 可讓您自由使用無伺服器隨選或佈建資源，隨意且大規模地查詢您的資料。 Azure Synapse 將這兩個世界與統一體驗相結合,可進行攝用、準備、管理和服務數據,以滿足即時 BI 和機器學習需求。

 Azure Synapse 有四個元件：
- 突觸 SQL:基於 T-SQL 的完整分析 
    - SQL 池(按 DWU 預配付費) = 一般可用
    - SQL 隨選 (依據處理的 TB 量付費) – (預覽)
- 火花:深度集成的阿帕奇火花(預覽)
- 資料整合式:混合資料整合(預覽)
- 工作室:統一的用戶體驗。  (預覽)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-mpp-architecture-components"></a>突觸 SQL MPP 架構結構元件

[Synapse SQL](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse)利用橫向擴充架構結構跨多個節點分發數據的計算處理。 比例單位是計算能力的抽象,稱為[資料倉儲單元](what-is-a-data-warehouse-unit-dwu-cdwu.md)。 計算與儲存是分開的,使您能夠獨立於系統中的數據進行縮放計算。

![突觸 SQL 架構結構](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

突觸 SQL 使用基於節點的體系結構。 應用程式將 T-SQL 命令連接到 Control 節點,該節點是 Synapse SQL 池的單個入口點。 控制節點運行 MPP 引擎,該引擎優化用於並行處理的查詢,然後將操作傳遞給計算節點以並行執行其工作。 

計算節點會在 Azure 儲存體中儲存所有使用者資料，並執行平行查詢。 資料移動服務 (DMS) 是系統層級的內部服務，其會視需要在節點之間移動資料，以平行方式執行查詢並傳回精確的結果。 

使用分離儲存和計算時,使用 Synapse SQL 池可以:

* 獨立估算計算能力，而不論您的儲存體需求為何。
* 在 SQL 池(數據倉庫)內增大或縮小計算能力,而無需移動數據。
* 暫停計算容量，同時讓資料保持不變，因此您只需支付儲存體的費用。
* 在營運時間期間繼續計算容量。

### <a name="azure-storage"></a>Azure 儲存體

Synapse SQL 利用 Azure 儲存來保證使用者數據的安全。  由於數據由 Azure 存儲存儲存儲和管理,因此存儲消耗需要單獨收費。 將數據分片分到**分佈中**,以優化系統的性能。 當您定義資料表時，可以選擇要用來散發資料的分區化模式。 支援這些分片模式:

* 雜湊
* 循環配置資源
* 複寫

### <a name="control-node"></a>控制節點

控制節點是體系結構的大腦。 它是與所有應用程式與連接互動的前端。 MPP 引擎會在控制節點上執行，以便將平行查詢最佳化並加以協調。 提交 T-SQL 查詢時,Control 節點將其轉換為並行針對每個分佈運行的查詢。

### <a name="compute-nodes"></a>計算節點數量

計算節點提供計算能力。 散發會對應到計算節點以進行處理。 當您為更多的計算資源付費時,分發將重新映射到可用的計算節點。 計算節點的數量範圍從 1 到 60,由 Synapse SQL 的服務層級決定。

每個計算節點都有會在系統檢視中顯示的節點識別碼。 您可以在名稱開頭為 sys.pdw_nodes 的系統檢視中尋找 node_id 資料行，以查看計算節點識別碼。 如需這些系統檢視的清單，請參閱 [MPP 系統檢視](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7)。

### <a name="data-movement-service"></a>資料移動服務

資料移動服務 (DMS) 是資料傳輸技術，可協調計算節點之間的資料移動。 某些查詢需要資料移動，以確保平行查詢會傳回精確的結果。 若資料移動是必要的，DMS 確保正確的資料會到達正確的位置。 

## <a name="distributions"></a>散發

散發是儲存體的基本單位，可處理在分散式資料上執行的平行查詢。 運行查詢時,該工作將分為 60 個並行運行的較小查詢。 

這 60 個較小查詢中的每一個都會在其中一個資料散發中執行。 每個計算節點都會管理這 60 個散發中的一或多個。 具有最大計算資源的 SQL 池每個計算節點有一個分佈。 具有最小計算資源的 SQL 池具有一個計算節點上的所有分佈。  

## <a name="hash-distributed-tables"></a>雜湊分散式資料表

雜湊分散式資料表可以針對大型資料表上的聯結和彙總提供最高查詢效能。 

要將數據分片分到哈希分佈的表中,哈希函數用於確定將每行分配給一個分佈。 在資料表定義中，其中一個資料行會指定為發佈資料行。 雜湊函式會使用散發資料行中的值，將每個資料列指派給一個散發。

下圖說明完整 (非分散式資料表) 會如何儲存為雜湊分散式資料表。 

![分散式資料表](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "分散式資料表")  

* 每個資料列屬於一種發佈。  
* 具決定性的雜湊演算法會將每個資料列指派給一個發佈。  
* 每個發佈的資料表資料列數目會隨著顯示不同資料表大小而有所不同。

選取散發資料行有效能考量，例如區分、資料扭曲，以及在系統上執行的查詢類型。

## <a name="round-robin-distributed-tables"></a>循環配置資源分散式資料表

循環配置資源資料表是要建立的最簡單資料表，可以在用來作為暫存資料表以供載入使用時提供快速效能。

循環配置資源分散式資料表會在整個資料表中平均散發資料，但不需任何進一步最佳化。 系統會先隨機選擇一個散發，然後將資料列的緩衝區循序指派給散發。 將資料快速載入循環配置資源資料表，但搭配雜湊分散式資料表通常會有較佳的查詢效能。 在迴圈表上的聯接需要重新洗牌數據,這需要額外的時間。

## <a name="replicated-tables"></a>複寫的資料表

複寫的資料表會為小型資料表提供最快速的查詢效能。

複寫的資料表會在每個計算節點上快取一份完整的資料表複本。 因此，複寫資料表就不需在進行聯結或彙總之前，於計算節點之間傳輸資料。 複寫的資料表最好能與小型資料表搭配使用。 需要額外的存儲,並且寫入數據時會產生額外的開銷,這使得大型表不切實際。  

下圖顯示了緩存在每個計算節點上的第一個分佈上的複製表。  

![複寫的資料表](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "複寫的資料表") 

## <a name="next-steps"></a>後續步驟

現在,您已經瞭解了 Azure 突觸,請瞭解如何快速[創建 SQL 池](create-data-warehouse-portal.md)並[載入範例資料](load-data-from-azure-blob-storage-using-polybase.md)。 如果您不熟悉 Azure，您可能會發現 [Azure 詞彙](../../azure-glossary-cloud-terminology.md) 在您遇到新術語時很有幫助。 或者查看這些其他 Azure 同步資源。  
