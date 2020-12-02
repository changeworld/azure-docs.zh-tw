---
title: Synapse SQL 架構
description: 瞭解 Azure Synapse SQL 如何結合分散式查詢處理功能與 Azure 儲存體，以達到高效能和擴充性。
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: da6c9f6df0e9e74de297cf6c8f655b62e3446bad
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462710"
---
# <a name="azure-synapse-sql-architecture"></a>Azure Synapse SQL 架構 

本文說明 Synapse SQL 的架構元件。

## <a name="synapse-sql-architecture-components"></a>Synapse SQL 架構元件

Synapse SQL 會利用擴增架構，將資料的計算處理散發到多個節點。 計算與儲存體分隔開來，讓您可以在系統中單獨調整資料的計算。 

針對專用的 SQL 集區，小數位數是計算能力的抽象概念，稱為 [資料倉儲單位](resource-consumption-models.md)。 

針對無伺服器 SQL 集區，在無伺服器的情況下，會自動進行調整以配合查詢資源需求。 當拓撲隨著時間新增、移除節點或容錯移轉而有所變更，會調整變更，並確保您的查詢具有足夠的資源並順利完成。 例如，下圖顯示利用4個計算節點執行查詢的無伺服器 SQL 集區。

![Synapse SQL 架構](./media//overview-architecture/sql-architecture.png)

Synapse SQL 使用以節點為基礎的架構。 應用程式會連線到控制節點並發出 T-SQL 命令，這是 Synapse SQL 的單一進入點。 

Azure Synapse SQL 控制節點會利用分散式查詢引擎，將查詢優化以進行平行處理，然後將作業傳遞到計算節點，以平行方式執行其工作。 

無伺服器 SQL 集區控制節點會利用分散式查詢處理 (DQP) 引擎，藉由將使用者查詢分割成將在計算節點上執行的較小查詢，來優化和協調分散式執行。 每個小型查詢稱為工作，代表分散式執行單位。 會從儲存體讀取檔案、從其他工作聯結結果，將從其他工作擷取的資料分組或排序。 

計算節點會在 Azure 儲存體中儲存所有使用者資料，並執行平行查詢。 資料移動服務 (DMS) 是系統層級的內部服務，其會視需要在節點之間移動資料，以平行方式執行查詢並傳回精確的結果。 

在使用分離儲存體和計算的情況下，使用 Synapse SQL 時，可以受益於獨立大小的計算能力，不論您的儲存體需求如何。 針對無伺服器的 SQL 集區調整會自動完成，而針對專用的 SQL 集區，則可以：

* 在專用的 SQL 集區中增加或縮小計算能力，而不移動資料。
* 暫停計算容量，同時讓資料保持不變，因此您只需支付儲存體的費用。
* 在營運時間期間繼續計算容量。

## <a name="azure-storage"></a>Azure 儲存體

Synapse SQL 會利用 Azure 儲存體來保護您使用者資料的安全。 因為您的資料是由 Azure 儲存體儲存及管理的，所以您的儲存體耗用量會分開計費。 

無伺服器 SQL 集區可讓您以唯讀方式查詢 data lake 中的檔案，而 SQL 集區也可讓您內嵌資料。 當資料內嵌到專用的 SQL 集區時，資料會分區化到 **散發中，以將** 系統效能優化。 當您定義資料表時，可以選擇要用來散發資料的分區化模式。 支援以下分區化模式：

* 雜湊
* 循環配置資源
* 複寫

## <a name="control-node"></a>控制節點

控制節點是架構的大腦。 它是與所有應用程式與連接互動的前端。 

在 Synapse SQL 中，分散式查詢引擎會在控制節點上執行，以優化和協調平行查詢。 當您將 T-SQL 查詢提交到專用的 SQL 集區時，控制節點會將它轉換成針對每個散發平行執行的查詢。

在無伺服器 SQL 集區中，DQP 引擎會在控制節點上執行，藉由將使用者查詢分割成較小的查詢（將在計算節點上執行）來優化和協調分散式執行。 也會指派每個節點要處理的檔案集合。

## <a name="compute-nodes"></a>計算節點數量

計算節點提供計算能力。 

在專用的 SQL 集區中，發行版本會對應到計算節點以進行處理。 當您需要支付更多計算資源的費用時，集區會將散發重新對應到可用的計算節點。 計算節點的數目範圍從1到60，由專用 SQL 集區的服務層級決定。 每個計算節點都有會在系統檢視中顯示的節點識別碼。 您可以在名稱開頭為 sys.pdw_nodes 的系統檢視中尋找 node_id 資料行，以查看計算節點識別碼。 如需這些系統檢視的清單，請參閱 [SYNAPSE SQL 系統檢視](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest)。

在無伺服器 SQL 集區中，每個計算節點都會被指派一項工作和一組檔案來執行工作。 工作是分散式查詢執行單位，實際上是使用者已提交查詢的一部分。 自動調整會生效，以確保使用足夠的計算節點來執行使用者查詢。

## <a name="data-movement-service"></a>資料移動服務

資料移動服務 (DMS) 是專用 SQL 集區中的資料傳輸技術，可協調計算節點之間的資料移動。 某些查詢需要資料移動，以確保平行查詢會傳回精確的結果。 若資料移動是必要的，DMS 確保正確的資料會到達正確的位置。

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>散發

散發是在專用 SQL 集區的分散式資料上執行的平行查詢儲存和處理的基本單位。 當專用的 SQL 集區執行查詢時，工作會分割成以平行方式執行的60較小查詢。 

這 60 個較小查詢中的每一個都會在其中一個資料散發中執行。 每個計算節點都會管理這 60 個散發中的一或多個。 具有最多計算資源的專用 SQL 集區在每個計算節點上都有一個散發。 具有最小計算資源的專用 SQL 集區具有一個計算節點上的所有散發。 

## <a name="hash-distributed-tables"></a>雜湊分散式資料表
雜湊分散式資料表可以針對大型資料表上的聯結和彙總提供最高查詢效能。 

為了將資料分區到雜湊分散式資料表中，專用的 SQL 集區會使用雜湊函數，將每個資料列決定性地指派給一個散發。 在資料表定義中，其中一個資料行會指定為發佈資料行。 雜湊函式會使用散發資料行中的值，將每個資料列指派給一個散發。

下圖說明完整 (非分散式資料表) 會如何儲存為雜湊分散式資料表。 

![分散式資料表](media//overview-architecture/hash-distributed-table.png "分散式資料表") 

* 每個資料列屬於一種發佈。 
* 具決定性的雜湊演算法會將每個資料列指派給一個發佈。 
* 每個發佈的資料表資料列數目會隨著顯示不同資料表大小而有所不同。

選取散發資料行有效能考量，例如區分、資料扭曲，以及在系統上執行的查詢類型。

## <a name="round-robin-distributed-tables"></a>循環配置資源分散式資料表

循環配置資源資料表是要建立的最簡單資料表，可以在用來作為暫存資料表以供載入使用時提供快速效能。

循環配置資源分散式資料表會在整個資料表中平均散發資料，但不需任何進一步最佳化。 系統會先隨機選擇一個散發，然後將資料列的緩衝區循序指派給散發。 將資料快速載入循環配置資源資料表，但搭配雜湊分散式資料表通常會有較佳的查詢效能。 循環配置資源資料表上的聯結需要重新輪換資料，這需要額外時間。

## <a name="replicated-tables"></a>複寫資料表
複寫的資料表會為小型資料表提供最快速的查詢效能。

複寫的資料表會在每個計算節點上快取一份完整的資料表複本。 因此，複寫資料表就不需在進行聯結或彙總之前，於計算節點之間傳輸資料。 複寫的資料表最好能與小型資料表搭配使用。 需要額外的儲存體，而且，會在寫入資料而導致大型資料表不適用時產生額外的負擔。 

下圖顯示會在每個計算節點中第一個散發上快取的複寫資料表。 

![複寫資料表](media/overview-architecture/replicated-table.png "複寫的資料表") 

## <a name="next-steps"></a>後續步驟

現在您已瞭解有關 Synapse SQL 的一些資訊，請瞭解如何快速 [建立專用的 SQL 集](../quickstart-create-sql-pool-portal.md) 區，並將 [範例資料載入](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./sql-data-warehouse-load-sample-databases.md) 。 或您開始 [使用無伺服器 SQL 集](../quickstart-sql-on-demand.md)區。 如果您不熟悉 Azure，您可能會發現 [Azure 詞彙](../../azure-glossary-cloud-terminology.md) 在您遇到新術語時很有幫助。 
