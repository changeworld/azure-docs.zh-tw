---
title: Azure Synapse Analytics （先前稱為 SQL DW）架構
description: 瞭解 Azure Synapse Analytics （先前稱為 SQL DW）如何結合大量平行處理（MPP）與 Azure 儲存體，以達到高效能和擴充性。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1d808210861d971b2915206e7be0fe9b955616c5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720311"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Azure Synapse Analytics （先前稱為 SQL DW）架構 

Azure Synapse 是一種無限制的分析服務，可將企業資料倉儲和巨量資料分析整合在一起。 您可以自由使用無伺服器隨選資源或佈建資源，照自己的決定大規模查詢資料。 Azure Synapse 將這兩者結合在一起，透過整合的體驗擷取、準備、管理和處理資料，以滿足立即的 BI 和機器學習需求。

 Azure Synapse 有四個元件：
- SQL 分析：完成 T-sql 型分析 
    - SQL 集區（每個布建的 DWU 付費）-正式推出
    - SQL 隨選 (依據處理的 TB 量付費) – (預覽)
- Spark：深層整合的 Apache Spark （預覽） 
- 資料整合：混合式資料整合（預覽）
- Studio：統一的使用者體驗。  (預覽)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="sql-analytics-mpp-architecture-components"></a>SQL 分析 MPP 架構元件

[SQL 分析](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse)會利用向外延展架構，將資料的計算處理散發到多個節點上。 縮放單位是稱為[資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)之計算能力的抽象概念。 計算與儲存體分開，可讓您獨立調整系統中資料的計算。

![SQL 分析架構](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL 分析會使用以節點為基礎的架構。 應用程式會連接併發出 T-sql 命令到控制節點，這是 SQL 分析的單一進入點。 控制節點會執行 MPP 引擎，將查詢優化以進行平行處理，然後將作業傳遞到計算節點，以平行方式執行其工作。 

計算節點會在 Azure 儲存體中儲存所有使用者資料，並執行平行查詢。 資料移動服務 (DMS) 是系統層級的內部服務，其會視需要在節點之間移動資料，以平行方式執行查詢並傳回精確的結果。 

使用 SQL 分析時，使用分離的儲存體和計算，可以：

* 獨立估算計算能力，而不論您的儲存體需求為何。
* 在 SQL 集區（資料倉儲）內增加或縮小計算能力，而不移動資料。
* 暫停計算容量，同時讓資料保持不變，因此您只需支付儲存體的費用。
* 在營運時間期間繼續計算容量。

### <a name="azure-storage"></a>Azure 儲存體

SQL 分析會利用 Azure 儲存體來保護您的使用者資料。  由於您的資料會由 Azure 儲存體儲存和管理，因此您的儲存體耗用量會有個別的費用。 資料會**分區化到散發中，以將**系統效能優化。 當您定義資料表時，可以選擇要用來散發資料的分區化模式。 支援這些分區化模式：

* 雜湊
* 循環配置資源
* 複寫

### <a name="control-node"></a>控制節點

控制節點是架構的大腦。 它是與所有應用程式與連接互動的前端。 MPP 引擎會在控制節點上執行，以便將平行查詢最佳化並加以協調。 當您提交 T-SQL 查詢至 SQL 分析時，控制節點會將它轉換成平行執行每個散發的查詢。

### <a name="compute-nodes"></a>計算節點數量

計算節點提供計算能力。 散發會對應到計算節點以進行處理。 當您支付更多計算資源的費用時，SQL 分析會將分佈重新對應到可用的計算節點。 計算節點的數目範圍是從1到60，而且是由 SQL 分析的服務層級所決定。

每個計算節點都有會在系統檢視中顯示的節點識別碼。 您可以在名稱開頭為 sys.pdw_nodes 的系統檢視中尋找 node_id 資料行，以查看計算節點識別碼。 如需這些系統檢視的清單，請參閱 [MPP 系統檢視](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7)。

### <a name="data-movement-service"></a>資料移動服務
資料移動服務 (DMS) 是資料傳輸技術，可協調計算節點之間的資料移動。 某些查詢需要資料移動，以確保平行查詢會傳回精確的結果。 若資料移動是必要的，DMS 確保正確的資料會到達正確的位置。 

## <a name="distributions"></a>散發

散發是儲存體的基本單位，可處理在分散式資料上執行的平行查詢。 當 SQL 分析執行查詢時，會將工作分成60以平行方式執行的較小查詢。 

這 60 個較小查詢中的每一個都會在其中一個資料散發中執行。 每個計算節點都會管理這 60 個散發中的一或多個。 具有最多計算資源的 SQL 集區具有每個計算節點的一個散發。 具有最少計算資源的 SQL 集區會在一個計算節點上具有所有散發。  

## <a name="hash-distributed-tables"></a>雜湊分散式資料表
雜湊分散式資料表可以針對大型資料表上的聯結和彙總提供最高查詢效能。 

為了將資料分區到雜湊分散式資料表，SQL 分析會使用雜湊函式，以決定性的方式將每一列指派給一個散發。 在資料表定義中，其中一個資料行會指定為發佈資料行。 雜湊函式會使用散發資料行中的值，將每個資料列指派給一個散發。

下圖說明完整 (非分散式資料表) 會如何儲存為雜湊分散式資料表。 

![分散式資料表](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "分散式資料表")  

* 每個資料列屬於一種發佈。  
* 具決定性的雜湊演算法會將每個資料列指派給一個發佈。  
* 每個發佈的資料表資料列數目會隨著顯示不同資料表大小而有所不同。

選取散發資料行有效能考量，例如區分、資料扭曲，以及在系統上執行的查詢類型。

## <a name="round-robin-distributed-tables"></a>循環配置資源分散式資料表
循環配置資源資料表是要建立的最簡單資料表，可以在用來作為暫存資料表以供載入使用時提供快速效能。

循環配置資源分散式資料表會在整個資料表中平均散發資料，但不需任何進一步最佳化。 系統會先隨機選擇一個散發，然後將資料列的緩衝區循序指派給散發。 將資料快速載入循環配置資源資料表，但搭配雜湊分散式資料表通常會有較佳的查詢效能。 迴圈配置資源資料表上的聯結需要重新輪換資料，這會花費額外的時間。


## <a name="replicated-tables"></a>複寫的資料表
複寫的資料表會為小型資料表提供最快速的查詢效能。

複寫的資料表會在每個計算節點上快取一份完整的資料表複本。 因此，複寫資料表就不需在進行聯結或彙總之前，於計算節點之間傳輸資料。 複寫的資料表最好能與小型資料表搭配使用。 需要額外的儲存體，而且在寫入資料時會產生額外的負擔，這使得大型資料表變得不切實際。  

下圖顯示在每個計算節點上第一次散發時快取的複寫資料表。  

![複寫資料表](media/sql-data-warehouse-distributed-data/replicated-table.png "複寫的資料表") 

## <a name="next-steps"></a>後續步驟
既然您已深入瞭解 Azure Synapse，請瞭解如何快速[建立 SQL 集](./sql-data-warehouse-get-started-provision.md)區並[載入範例資料](./sql-data-warehouse-load-sample-databases.md)。 如果您不熟悉 Azure，您可能會發現 [Azure 詞彙](../azure-glossary-cloud-terminology.md) 在您遇到新術語時很有幫助。 或查看其中一些其他 Azure Synapse 資源。  

* [客戶成功案例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
* [部落格](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [功能要求](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [影片](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [建立支援票證](./sql-data-warehouse-get-started-create-support-ticket.md)
* [MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
* [Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)

