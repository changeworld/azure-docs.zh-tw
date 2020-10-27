---
title: 監視-適用於 MySQL 的 Azure 資料庫彈性的伺服器
description: 本文說明適用於 MySQL 的 Azure 資料庫彈性伺服器的監視和警示的計量，包括 CPU、儲存體和連線統計資料。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 5fe1d5a5a472b47abd364a89d1a65f1249c67c0d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538658"
---
# <a name="monitor-azure-database-for-mysql-flexible-servers-with-built-in-metrics"></a>使用內建計量監視適用於 MySQL 的 Azure 資料庫彈性的伺服器

> [!IMPORTANT] 
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。

適用於 MySQL 的 Azure 資料庫彈性的伺服器可透過 Azure 監視器來監視伺服器。 計量為數值，可描述伺服器資源在特定時間的某些層面。 監視您的伺服器資源可讓您監視最重要的內容，以協助您疑難排解和優化您的工作負載。 監視適當的計量可協助您保留伺服器和應用程式的效能、可靠性和可用性。

在本文中，您將瞭解可提供您的彈性伺服器的各種計量，讓您深入瞭解伺服器的行為。

## <a name="available-metrics"></a>可用的計量

適用於 MySQL 的 Azure 資料庫彈性的伺服器會提供各種計量，以瞭解您的工作負載如何執行以及根據此資料，您可以瞭解對伺服器和應用程式的影響。 例如，在彈性的伺服器中，您可以監視 **主機 CPU 百分比** 、使用中連線、 **IO 百分比** 和 **主機記憶體百分比** **，以** 找出效能的影響。 從該處，您可能必須將工作負載優化、透過變更計算層級垂直調整，或使用讀取複本水準調整。

所有 Azure 計量都有一分鐘頻率，且每個計量皆提供 30 天的記錄。 您可以在計量上設定警示。 如需逐步指引，請參閱 [如何設定警示](./how-to-alert-on-metric.md)。 其他工作包含設定自動化動作、執行進階分析，以及封存記錄。 如需詳細資訊，請參閱 [Azure 計量概觀](../../azure-monitor/platform/data-platform.md)。

### <a name="list-of-metrics"></a>計量清單
這些計量可供適用於 MySQL 的 Azure 資料庫使用：

|度量顯示名稱|計量|Unit|描述|
|---|---|---|---|
|主機 CPU 百分比|cpu_percent|百分比|伺服器上 CPU 使用率的百分比，包括客戶工作負載和 Azure MySQL 進程的 CPU 使用率|
|中的主機網路 |network_bytes_ingress|位元組|伺服器上的連入網路流量，包括來自客戶資料庫和 Azure MySQL 功能（例如複寫、監視、記錄等）的流量。|
|主機網路輸出|network_bytes_egress|位元組|伺服器上的連出網路流量，包括來自客戶資料庫和 Azure MySQL 功能（例如複寫、監視、記錄等）的流量。|
|複寫延遲|replication_lag|秒|自上次重新執行的交易以來的時間。 此計量僅適用于複本伺服器。|
|作用中的連線|active_connection|Count|伺服器的使用中連線數量。|
|已使用的備份儲存體|backup_storage_used|位元組|已使用的備份儲存體數量。|
|IO 百分比|io_consumption_percent|百分比|使用中 IO 的百分比。|
|主機記憶體百分比|memory_percent|百分比|伺服器上使用的記憶體百分比，包括客戶工作負載和 Azure MySQL 進程的記憶體使用量|
|儲存體限制|storage_limit|位元組|此伺服器的儲存體上限。|
|儲存體百分比|storage_percent|百分比|使用的儲存體佔伺服器最大值的百分比。|
|使用的儲存體|storage_used|位元組|使用中的儲存體數量。 此服務所使用的儲存體可能包括資料庫檔案、交易記錄和伺服器記錄。|
|總連線數|total_connections|Count|與伺服器的總連接數|
|中止的連線|aborted_connections|Count|連線至 MySQL 的失敗嘗試次數，例如因為認證不正確而導致連線失敗。|
|查詢|查詢|Count|每秒查詢次數|

## <a name="next-steps"></a>下一步
- 請參閱[如何設定警示](./how-to-alert-on-metric.md)，取得根據計量來建立警示的指引。
- 深入瞭解 [調整 IOPS](./concepts/../concepts-compute-storage.md#iops) 以改善效能。