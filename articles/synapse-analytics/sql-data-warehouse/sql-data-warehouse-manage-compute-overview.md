---
title: '管理適用于專用 SQL 集區的計算資源 (先前為 SQL DW) '
description: 瞭解 Azure Synapse Analytics 中先前的 SQL DW) 專用 SQL (集區的效能 scale out 功能。 藉由調整 Dwu 來相應放大，或藉由暫停專用的 SQL 集區來降低成本。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4a2ce18f4a02ccaadf66a96049b00f3837b927e4
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677032"
---
# <a name="manage-compute-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中管理專用 SQL 集區的計算 (先前的 SQL DW) 

深入瞭解如何在 Azure Synapse Analytics 中管理計算資源專用 SQL 集區 (先前的 SQL DW) 。 藉由暫停專用的 SQL 集區來降低成本，或調整專用的 SQL 集區以符合效能需求。

## <a name="what-is-compute-management"></a>什麼是計算管理？

專用 SQL 集區的架構 (先前的 SQL DW) 可分隔儲存體和計算，讓每一個都能獨立調整。 因此，您可以調整計算規模以符合與資料儲存體無關的效能需求。 您也可以暫停和繼續計算資源。 這個架構的自然結果為計算[計費](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)與儲存體無關。 如果您不需要使用專用的 SQL 集區 (先前的 SQL DW) 一段時間，您可以藉由暫停計算來節省計算成本。

## <a name="scaling-compute"></a>調整計算規模

您可以藉由調整專用 SQL 集區 (先前的 SQL DW) 的 [資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md) 設定，來相應放大或縮小計算。 當您新增更多資料倉儲單位時，載入和查詢效能可能會呈線性增加。

如需相應放大步驟，請參閱 [Azure 入口網站](quickstart-scale-compute-portal.md)、[PowerShell](quickstart-scale-compute-powershell.md) 或 [T-SQL](quickstart-scale-compute-tsql.md) 快速入門。 您也可以使用 [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute) 來執行相應放大作業。

若要執行調整規模作業，專用 SQL 集區 (先前的 SQL DW) 先刪除所有傳入的查詢，然後復原交易，以確保一致的狀態。 只有在交易復原完成後，才會發生調整。 針對調整規模作業，系統會從計算節點卸離儲存層、新增計算節點，然後將儲存層會重新連結至計算層。 每個專用的 SQL 集區 (先前的 SQL DW) 都會儲存為60散發套件，並平均分散至計算節點。 新增更多計算節點會增加更多計算能力。 當計算節點數目增加時，每個計算節點的散發數目會減少，為您的查詢提供更多計算能力。 同樣地，減少資料倉儲單位可減少計算節點數目，進而減少查詢的計算資源。

下表顯示當資料倉儲單位變更時，每個計算節點的發佈數目如何隨之變更。  DW30000c 提供60計算節點，並可達到比 DW100c 更高的查詢效能。

| 資料倉儲單位  | \# 的計算節點 | 每節點 \# 個散發 |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
| DW500c   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |

## <a name="finding-the-right-size-of-data-warehouse-units"></a>尋找適當大小的資料倉儲單位

若要查看相應放大的效能優勢 (尤其是針對較大的資料倉儲單位)，您可使用至少 1-TB 的資料集。 若要找出您專用 SQL 集區的最大資料倉儲單位數目 (先前為 SQL DW) ，請嘗試相應增加和減少。 在載入您的資料之後，使用不同的資料倉儲單位數目來執行一些查詢。 由於調整很快速，您可以在一小時內嘗各種效能層級。

尋找最佳資料倉儲單位數目的建議︰

- 針對在開發中 (先前為 SQL DW) 的專用 SQL 集區，請從選取較少的資料倉儲單位開始。  DW400c 或 DW200c 是不錯的起點。
- 監視應用程式效能，觀察比較所選資料倉儲單位數目與您觀察到的效能。
- 假設線性尺度，並判斷您需要增加或減少多少資料倉儲單位。
- 繼續進行調整，直到達到您業務需求的最佳效能為止。

## <a name="when-to-scale-out"></a>相應放大時機

相應放大資料倉儲單位會影響效能的下列層面：

- 以線性方式改善掃描、彙總和 CTAS 陳述式的系統效能。
- 針對載入作業增加讀取器和寫入器的數目。
- 並行查詢和並行存取插槽數目上限。

相應放大資料倉儲單位時機的建議︰

- 執行大量資料載入或轉換作業之前，相應放大進而讓資料更快速可供使用。
- 在尖峰營業時間內，相應放大以適應大量的並行查詢。

## <a name="what-if-scaling-out-does-not-improve-performance"></a>如果相應放大並未改善效能，該怎麼辦？

新增資料倉儲單位可提供平行處理能力。 如果工作平均分割於計算節點之間，額外的平行處理能力可改善查詢效能。 如果相應放大並未改變效能，有一些原因致使發生這種情形。 您的資料可能會呈現偏態分佈，或查詢可能會引進大量的資料移動。 若要調查查詢效能問題，請參閱[效能疑難排解](sql-data-warehouse-troubleshoot.md#performance)。

## <a name="pausing-and-resuming-compute"></a>暫停和繼續計算

暫停計算會導致儲存層與計算節點中斷連結。 系統會從您的帳戶釋放計算資源。 當計算暫停時，您就不需支付計算費用。 繼續計算會將儲存體重新連結至計算節點，並繼續收取計算費用。
當您暫停專用的 SQL 集區時 (先前的 SQL DW) ：

- 計算和記憶體資源會傳回資料中心內的可用資源集區
- 暫停期間的資料倉儲單位成本是零。
- 不會影響資料儲存體，您的資料保持不變。
- 所有執行中或已排入佇列的作業都會取消。

當您繼續 (先前為 SQL DW) 的專用 SQL 集區時：

- 專用的 SQL 集區 (先前的 SQL DW) 會取得資料倉儲單位設定的計算和記憶體資源。
- 繼續資料倉儲單位的計算費用。
- 您的資料變為可供使用。
- 在專用的 SQL 集區 (先前的 SQL DW) 上線之後，您需要重新開機工作負載查詢。

如果您想要讓專用的 SQL 集區 (先前的 SQL DW) 可供存取，請考慮將它相應減少到最小的大小，而不是暫停。

如需暫停和繼續步驟，請參閱 [Azure 入口網站](pause-and-resume-compute-portal.md) 或 [PowerShell](pause-and-resume-compute-powershell.md) 快速入門。 您也可以使用[暫停 REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) 或[繼續 REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute)。

## <a name="drain-transactions-before-pausing-or-scaling"></a>暫停或調整之前先清空交易

我們建議在起始暫停或調整規模作業之前，讓現有的交易先完成。

當您暫停或調整專用 SQL 集區 (先前的 SQL DW) ，在幕後，您的查詢會在您起始暫停或調整要求時取消。 取消簡單的 SELECT 查詢是很快的作業，對於暫停或調整執行個體所花費的時間幾乎沒有什麼影響。  不過，交易性查詢 (會修改您的資料或結構) 可能無法快速地停止。 **顧名思義，交易性查詢必須完全完成或回復變更。**  回復交易性查詢已完成的工作可能需要很長時間，甚至比查詢套用原始變更更久。 例如，如果您取消的刪除資料列查詢已經執行一小時，系統可能需要一個小時將已刪除的資料列回復插入。 如果您在交易執行中執行暫停或調整，暫停或調整作業可能需要一些時間，因為暫停和調整必須等回復完成才能繼續。

另請參閱[了解交易](sql-data-warehouse-develop-transactions.md)和[最佳化交易](sql-data-warehouse-develop-best-practices-transactions.md)。

## <a name="automating-compute-management"></a>自動進行計算管理

若要自動執行計算管理作業，請參閱[使用 Azure 函式管理計算](manage-compute-with-azure-functions.md)。

相應放大、暫停和繼續作業可能需要幾分鐘的時間才能完成。 如果您要自動調整/暫停/繼續，建議實作邏輯以確保在進行其他動作之前，先確認特定作業已完成。 透過各種端點檢查專用的 SQL 集區 (先前的 SQL DW) 狀態，可讓您正確地執行這類作業的自動化。

若要檢查專用的 SQL 集區 (先前的 SQL DW) 狀態，請參閱 [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) 或 [t-sql](quickstart-scale-compute-tsql.md#check-dedicated-sql-pool-formerly-sql-dw-state) 快速入門。 您也可以使用 [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state)來檢查專用的 sql 集區 (先前的 sql DW) 狀態。

## <a name="permissions"></a>權限

 (先前的 SQL DW) 調整專用 SQL 集區，需要 [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)中所述的許可權。  暫停和繼續則需要 [SQL DB 參與者](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#sql-db-contributor)權限，特別是 Microsoft.Sql/servers/databases/action。

## <a name="next-steps"></a>下一步

若要瞭解 [管理](manage-compute-with-azure-functions.md) 計算資源的操作指南，請參閱如何針對個別查詢配置不同的計算資源。 如需詳細資訊，請參閱[適用於工作負載管理的資源類別](resource-classes-for-workload-management.md)。
