---
title: 優化自動資料清理-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文說明如何在適用於 PostgreSQL 的 Azure 資料庫單一伺服器上優化自動資料清理
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 07/09/2020
ms.openlocfilehash: a94afc1ab970c2cd3f509c86efba4e455d46fd13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86274504"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>在適用於 PostgreSQL 的 Azure 資料庫單一伺服器上優化自動資料清理

本文描述如何有效地將適用於 PostgreSQL 的 Azure 資料庫伺服器上的自動資料清理最佳化。

## <a name="overview-of-autovacuum"></a>自動資料清理的概觀

PostgreSQL 使用多版本並行控制 (MVCC)，來達到更好的資料庫並行。 每次更新都會導致插入和刪除，且每次刪除都會導致資料列虛標示為要刪除。 虛標示會識別無效且會在稍後被清除的 Tuple。 為了執行這些工作，PostgreSQL 會執行資料清理作業。

資料清理作業可以由手動或自動的方式觸發。 當資料庫經歷大量更新或刪除作業時，會有更多無效 Tuple。 當資料庫閒置時，會有較少無效 Tuple。 當資料庫的負載較大時，您需要更頻繁地執行資料清理，因此「手動」** 執行資料清理作業會變得不方便。

您可以設定自動資料清理，並進行調整從中獲益。 PostgreSQL 隨附的預設值可確保產品能在各種裝置上運作。 這些裝置包括 Raspberry Pi。 理想的設定值取決於：

- 可用的總資源，如 SKU 和儲存體大小。
- 資源使用狀況。
- 個別物件特性。

## <a name="autovacuum-benefits"></a>自動資料清理的優點

如果您沒有定時執行資料清理，累積無效 Tuple 可能導致：

- 資料膨脹，如較大的資料庫和資料表。
- 較大且非最佳化的索引。
- I/O 增加。

## <a name="monitor-bloat-with-autovacuum-queries"></a>使用自動資料清理查詢監視膨脹

下列範例查詢是設計來識別名為 XYZ 的資料表中，無效和有效 Tuple 的數目：

```sql
SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;
```

## <a name="autovacuum-configurations"></a>自動資料清理設定

根據兩個重要問題的解答，來決定控制自動資料清理的設定參數：

- 它應該在何時啟動？
- 啟動之後它應該清除多少？

以下是您可以根據上述問題來更新的一些自動資料清理設定參數，以及一些指導方針。

參數|描述|預設值
---|---|---
autovacuum_vacuum_threshold|指定在任一資料表中，觸發資料清理作業所需的已更新或已刪除 Tuple 的最小數量。 預設值為 50 個 Tuple。 只在 postgresql.conf 檔案中或在伺服器命令列上設定此參數。 若要覆寫個別資料表的設定，請變更資料表儲存體參數。|50
autovacuum_vacuum_scale_factor|指定當決定是否要觸發資料清理作業時，要加入至 autovacuum_vacuum_threshold 之資料表大小的比例。 預設值為 0.2，即資料表大小的百分之 20。 只在 postgresql.conf 檔案中或在伺服器命令列上設定此參數。 若要覆寫個別資料表的設定，請變更資料表儲存體參數。|0.2
autovacuum_vacuum_cost_limit|指定用於自動資料清理作業中的成本限制值。 如果指定 -1 (這是預設值)，系統會使用一般 vacuum_cost_limit 值。 如果有超過一個的背景工作角色，該值會在執行中的自動資料清理背景工作角色之間按比例分散。 每個背景工作角色的上限之總和不會超過此變數的值。 只在 postgresql.conf 檔案中或在伺服器命令列上設定此參數。 若要覆寫個別資料表的設定，請變更資料表儲存體參數。|-1
autovacuum_vacuum_cost_delay|指定用於自動資料清理作業中的成本延遲值。 如果指定 -1，系統會使用一般 vacuum_cost_delay 值。 預設值是 20 毫秒。 只在 postgresql.conf 檔案中或在伺服器命令列上設定此參數。 若要覆寫個別資料表的設定，請變更資料表儲存體參數。|20 毫秒
autovacuum_naptime | 指定在任何給定的資料庫上，每回合自動資料清理之間的最小延遲。 精靈會在每回合中檢查資料庫，並針對該資料庫中的資料表視需要發出 VACUUM 和 ANALYZE 命令。 延遲是以秒為單位來測量。 只在 postgresql.conf 檔案中或在伺服器命令列上設定此參數。| 15 秒
autovacuum_max_workers | 指定在任何時間可執行的自動資料清理處理序 (不是自動資料清理啟動程式) 的最大數目。 預設值為三。 只在伺服器啟動時設定此參數。|3

若要覆寫個別資料表的設定，請變更資料表儲存體參數。

## <a name="autovacuum-cost"></a>自動資料清理成本

以下是執行資料清理作業的「成本」:

- 在資料分頁上執行資料清理時，該分頁會被鎖定。
- 當資料清理作業在執行時，會使用計算和記憶體資源。

因此，不應太頻繁或太不頻繁地執行記憶體清理作業。 資料清理作業需要與工作負載配合。 請測試所有自動資料清理參數變更，因為每個都有所取捨。

## <a name="autovacuum-start-trigger"></a>自動資料清理啟動觸發程序

當無效 Tuple 的數量超過 autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples 時會觸發自動資料清理。 這裡的 reltuples 是常數。

自動資料清理的清除必須跟上資料庫負載。 否則您的儲存體可能會不足，且查詢會整體變慢。 經過一段時間的攤還之後，資料清理作業清除無效 Tuple 的速率應該會等於無效 Tuple 的產生速率。

有許多更新和刪除的資料庫會有更多無效 Tuple，且需要更多空間。 一般而言，有許多更新和刪除的資料庫，受益於較低的 autovacuum_vacuum_scale_factor 和 autovacuum_vacuum_threshold 值。 低的值可防止長時間累積無效 Tuple。 對於較小的資料庫，您可以對這兩個參數使用較高的值，因為較不需要進行資料清理。 提醒您，頻繁執行資料清理的代價是耗用計算和記憶體資源。

預設的百分之 20 比例因數適用於無效 Tuple 比例低的資料表。 它不適用於無效 Tuple 比例高的資料表。 例如，在 20 GB 大的資料表上，此比例因素轉譯為 4 GB 的無效 Tuple。 在 1 TB 大的資料表上，相當於 200 GB 的無效 Tuples。

使用 PostgreSQL 時，您可以在資料表層級或執行個體層級設定這些參數。 您目前只能在適用於 PostgreSQL 的 Azure 資料庫中，於資料表層級設定這些參數。

## <a name="estimate-the-cost-of-autovacuum"></a>估計自動資料清理的成本

執行自動資料清理的成本很高，因此系統有控制資料清理作業執行階段的參數。 下列參數可協助您估計執行資料清理的成本：

- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

資料清理處理序會讀取實體頁面並檢查無效 Tuple。 shared_buffers 中的每個分頁都當作成本為 1 (vacuum_cost_page_hit)。 對於所有其他分頁，有無效 Tuple 的成本為 20 (vacuum_cost_page_dirty)，沒有無效 Tuple 的成本為 10 (vacuum_cost_page_miss)。 當處理序超過 autovacuum_vacuum_cost_limit 時，資料清理作業就會停止。

到達上限之後，處理序會根據 autovacuum_vacuum_cost_delay 參數指定的值睡眠一段時間，然後才再次啟動。 如果沒有到達上限，自動資料清理會根據 autovacuum_nap_time parameter 指定的值過一段時間之後啟動。

總而言之，autovacuum_vacuum_cost_delay 和 autovacuum_vacuum_cost_limit 參數控制每單位時間內允許清除多少資料。 請注意，預設值對於大部分的定價層都太低。 這些參數的最佳值隨定價層而不同，因此應該視情況來設定。

autovacuum_max_workers 參數決定可同時執行的自動資料清理處理序的最大數目。

使用 PostgreSQL 時，您可以在資料表層級或執行個體層級設定這些參數。 您目前只能在適用於 PostgreSQL 的 Azure 資料庫中，於資料表層級設定這些參數。

## <a name="optimize-autovacuum-per-table"></a>針對每個資料表最佳化自動資料清理

您可以針對每個資料表設定所有上述設定參數。 以下為範例：

```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

自動資料清理是個別資料表同步處理序。 資料表的無效 Tuple 百分比越大，自動資料清理的「成本」就越高。 您可以將更新和刪除比例高的資料表分割成多個資料表。 分割資料表有助於平行化自動資料清理，並減少在一個資料表上完成自動資料清理的「成本」。 您也可以增加平行自動資料清理背景工作角色的數目，以確保背景工作角色充分地排程。

## <a name="next-steps"></a>接下來的步驟

若要深入了解如何使用和微調自動資料清理，請參閱下列 PostgreSQL 文件：

- [第 18 章：伺服器設定](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html) \(英文\)
- [第 24 章：例行資料庫維護工作](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html) \(英文\)
