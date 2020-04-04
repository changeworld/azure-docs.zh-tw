---
title: 管理 SQL 池的計算資源
description: 瞭解 Azure 突觸分析 SQL 池中的性能擴充功能。 藉由調整 DWU 來相應放大，或藉由暫停資料倉儲來降低成本。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: daf57c7e6ef40f75eac070c06547cf2a28338f21
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633234"
---
# <a name="manage-compute-in-azure-synapse-analytics-data-warehouse"></a>在 Azure 同步分析資料主目錄管理計算

瞭解如何在 Azure 同步分析 SQL 池中管理計算資源。 通過暫停 SQL 池或擴展數據倉庫以滿足性能要求來降低成本。

## <a name="what-is-compute-management"></a>什麼是計算管理？

數據倉庫的體系結構將存儲和計算分開,允許每個倉庫獨立擴展。 因此，您可以調整計算規模以符合與資料儲存體無關的效能需求。 您也可以暫停和繼續計算資源。 這個架構的自然結果為計算[計費](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)與儲存體無關。 如果您有一段時間不需要使用資料倉儲，您可暫停計算以節省計算成本。

## <a name="scaling-compute"></a>調整計算規模

您可以通過調整 SQL 池[的數據倉庫單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)設置來橫向擴展或縮減計算。 當您新增更多資料倉儲單位時，載入和查詢效能可以呈線性增加。

如需相應放大步驟，請參閱 [Azure 入口網站](quickstart-scale-compute-portal.md)、[PowerShell](quickstart-scale-compute-powershell.md) 或 [T-SQL](quickstart-scale-compute-tsql.md) 快速入門。 您也可以使用 [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute) 來執行相應放大作業。

要執行縮放操作,SQL 池首先終止所有傳入查詢,然後回滾事務以確保狀態一致。 只有在交易復原完成後，才會發生調整。 對於縮放作業,系統將儲存層從計算節點分離,添加計算節點,然後將存儲層重新附加到計算層。 每個 SQL 池都存儲為 60 個分佈,它們均勻分佈到計算節點。 添加更多計算節點可添加更多計算能力。 隨著計算節點數量的增加,每個計算節點的分佈數也會減少,從而為您的查詢提供更多的計算能力。 同樣,減少數據倉庫單位會減少計算節點的數量,從而減少查詢的計算資源。

下表顯示當資料倉儲單位變更時，每個計算節點的發佈數目如何隨之變更。  DW30000c 提供60個計算節點,其查詢性能遠高於DW100c。

| 資料倉儲單位  | \#計算節點 | 每節點 \# 個散發 |
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

若要查看相應放大的效能優勢 (尤其是針對較大的資料倉儲單位)，您可使用至少 1-TB 的資料集。 要查找 SQL 池的最佳數據倉庫單位數,請嘗試向上和向下擴展。 在載入您的資料之後，使用不同的資料倉儲單位數目來執行一些查詢。 由於調整很快速，您可以在一小時內嘗各種效能層級。

尋找最佳資料倉儲單位數目的建議︰

- 對於開發中的 SQL 池,首先選擇少量的數據倉庫單元。  良好的起點是 DW400c 或 DW200c。
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

暫停計算會導致儲存層與計算節點中斷連結。 計算資源將從您的帳戶中釋放。 當計算暫停時，您就不需支付計算費用。 繼續計算會將儲存體重新連結至計算節點，並繼續收取計算費用。
暫停 SQL 池時:

- 計算和記憶體資源會傳回資料中心內的可用資源集區
- 暫停期間的資料倉儲單位成本是零。
- 不會影響資料儲存體，您的資料保持不變。
- 所有正在運行的或排隊的操作都已取消。

回復 SQL 池時:

- SQL 池獲取數據倉庫單元設置的計算和記憶體資源。
- 繼續資料倉儲單位的計算費用。
- 您的資料變為可供使用。
- SQL 池連線後,需要重新啟動工作負荷查詢。

如果始終希望 SQL 池可訪問,請考慮將其縮放到最小大小,而不是暫停。

如需暫停和繼續步驟，請參閱 [Azure 入口網站](pause-and-resume-compute-portal.md) 或 [PowerShell](pause-and-resume-compute-powershell.md) 快速入門。 您也可以使用[暫停 REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) 或[繼續 REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute)。

## <a name="drain-transactions-before-pausing-or-scaling"></a>暫停或調整之前先清空交易

我們建議在起始暫停或調整規模作業之前，讓現有的交易先完成。

當您暫停或縮放 SQL 池時,在後台啟動暫停或縮放請求時,查詢將被取消。 取消簡單的 SELECT 查詢是很快的作業，對於暫停或調整執行個體所花費的時間幾乎沒有什麼影響。  不過，交易性查詢 (會修改您的資料或結構) 可能無法快速地停止。 **顧名思義，交易性查詢必須完全完成或回復變更。**  回復交易性查詢已完成的工作可能需要很長時間，甚至比查詢套用原始變更更久。 例如，如果您取消的刪除資料列查詢已經執行一小時，系統可能需要一個小時將已刪除的資料列回復插入。 如果您在交易執行中執行暫停或調整，暫停或調整作業可能需要一些時間，因為暫停和調整必須等回復完成才能繼續。

另請參閱[了解交易](sql-data-warehouse-develop-transactions.md)和[最佳化交易](sql-data-warehouse-develop-best-practices-transactions.md)。

## <a name="automating-compute-management"></a>自動進行計算管理

若要自動執行計算管理作業，請參閱[使用 Azure 函式管理計算](manage-compute-with-azure-functions.md)。

相應放大、暫停和繼續作業可能需要幾分鐘的時間才能完成。 如果您要自動調整/暫停/繼續，建議實作邏輯以確保在進行其他動作之前，先確認特定作業已完成。 通過各種終結點檢查 SQL 池狀態,可以正確實現此類操作的自動化。

要檢查 SQL 池狀態,請參閱[PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state)或[T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state)快速入門。 還可以使用[REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state)檢查 SQL 池狀態。

## <a name="permissions"></a>權限

縮放 SQL 池需要 ALTER[資料庫](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)中描述的許可權。  暫停和繼續則需要 [SQL DB 參與者](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#sql-db-contributor)權限，特別是 Microsoft.Sql/servers/databases/action。

## <a name="next-steps"></a>後續步驟

請參閱如何指導[管理計算](manage-compute-with-azure-functions.md),管理計算資源的另一個方面是為各個查詢分配不同的計算資源。 如需詳細資訊，請參閱[適用於工作負載管理的資源類別](resource-classes-for-workload-management.md)。
