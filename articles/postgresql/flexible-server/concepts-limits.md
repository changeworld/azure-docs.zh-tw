---
title: 限制-適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器
description: 本文說明適用於 PostgreSQL 的 Azure 資料庫彈性伺服器的限制，例如連線和儲存引擎選項的數目。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: f270ac736bb5dbc429dc8659cc88e63d0b51a523
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366633"
---
# <a name="limits-in-azure-database-for-postgresql---flexible-server"></a>適用於 PostgreSQL 的 Azure 資料庫-彈性伺服器的限制

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

下列各節說明資料庫服務中的容量和功能限制。 如果您想要瞭解資源 (計算、記憶體、儲存體) 層，請參閱「 [計算和儲存](concepts-compute-storage.md) 」一文。

## <a name="maximum-connections"></a>連線數目上限

每個定價層和虛擬核心的連線數目上限如下所示。 Azure 系統需要三個連接來監視適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器。

| SKU 名稱             | 虛擬核心 | 記憶體大小 | 連線數目上限 | 最大使用者連接數 |
|----------------------|--------|-------------|-----------------|----------------------|
| **高載**        |        |             |                 |                      |
| B1ms                 | 1      | 2 GiB       | 50              | 47                   |
| B2s                  | 2      | 4 GiB       | 100             | 97                   |
| **一般用途**  |        |             |                 |                      |
| D2s_v3               | 2      | 8 GiB       | 214             | 211                  |
| D4s_v3               | 4      | 16 GiB      | 429             | 426                  |
| D8s_v3               | 8      | 32 GiB      | 859             | 856                  |
| D16s_v3              | 16     | 64 GiB      | 1718            | 1715                 |
| D32s_v3              | 32     | 128 GiB     | 3437            | 3434                 |
| D48s_v3              | 48     | 192 GiB     | 5000            | 4997                 |
| D64s_v3              | 64     | 256 GiB     | 5000            | 4997                 |
| **記憶體優化** |        |             |                 |                      |
| E2s_v3               | 2      | 16 GiB      | 1718            | 1715                 |
| E4s_v3               | 4      | 32 GiB      | 3437            | 3434                 |
| E8s_v3               | 8      | 64 GiB      | 5000            | 4997                 |
| E16s_v3              | 16     | 128 GiB     | 5000            | 4997                 |
| E32s_v3              | 32     | 256 GiB     | 5000            | 4997                 |
| E48s_v3              | 48     | 384 GiB     | 5000            | 4997                 |
| E64s_v3              | 64     | 432 GiB     | 5000            | 4997                 |

當連線超過限制時，則可能會收到下列錯誤：
> 嚴重錯誤︰很抱歉，已經有太多用戶端

> [!IMPORTANT]
> 為了獲得最佳體驗，我們建議您使用連接共用器（例如 Pgbouncer) ）來有效率地管理連接。

于 postgresql 連接（甚至是閒置）可能會佔用大約 10 MB 的記憶體。 此外，建立新的連接需要一些時間。 大部分應用程式會要求許多短期連線，這會加重這種情況。 結果會減少實際工作負載的可用資源，因而導致效能降低。 連接共用可以用來減少閒置的連線，並重複使用現有的連接。 若要深入瞭解，請造訪我們的 [blog 文章](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)。

## <a name="functional-limitations"></a>功能限制：

### <a name="scale-operations"></a>調整作業

- 調整伺服器儲存體需要重新開機伺服器。
- 伺服器儲存體只能以2x 增量調整，請參閱 [計算和儲存體](concepts-compute-storage.md) 以取得詳細資料。
- 目前不支援減少伺服器儲存體大小。

### <a name="server-version-upgrades"></a>伺服器版本升級

- 目前不支援在主要資料庫引擎版本之間進行自動轉換。 如果您希望升級至下個主要版本，請將資料庫[備份和還原](../howto-migrate-using-dump-and-restore.md)至使用新引擎版本所建立的伺服器。

### <a name="networking"></a>網路功能

- 目前不支援從 VNET 移入和移出。
- 目前不支援將公用存取與 VNET 內的部署結合在一起。
- VNET 不支援防火牆規則，而是可以改用網路安全性群組。
- 公用存取資料庫伺服器可以連線到公用網際網路，例如透過 `postgres_fdw` ，而且無法限制此存取。 以 VNET 為基礎的伺服器可以使用網路安全性群組來限制輸出存取。

### <a name="high-availability-ha"></a>高可用性 (HA) 

- 高載伺服器目前不支援 Zone-Redundant HA。
- 當伺服器容錯移轉至 HA 待命時，資料庫伺服器的 IP 位址會變更。 請確定您使用的是 DNS 記錄，而不是伺服器 IP 位址。
- 如果邏輯複寫是以 HA 設定的彈性伺服器設定，則在容錯移轉至待命伺服器的情況下，不會將邏輯複寫位置複製到待命伺服器。 
- 如需區域冗余 HA （包括限制）的詳細資訊，請參閱 [概念-ha 檔](concepts-high-availability.md) 頁面。

### <a name="availability-zones"></a>可用性區域

- 目前不支援將伺服器手動移至不同的可用性區域。
- 無法手動設定 HA 待命伺服器的可用性區域。

### <a name="postgres-engine-extensions-and-pgbouncer"></a>Postgres 引擎、延伸模組和 Pgbouncer) 

- 不支援 Postgres 10 和更舊版本。 如果您需要較舊的 Postgres 版本，建議使用 [單一伺服器](../overview-single-server.md) 選項。
- 延伸模組支援目前僅限於 Postgres `contrib` 延伸模組。
- 內建的 Pgbouncer) 連接共用器目前不適用於 VNET 或高載伺服器中的資料庫伺服器。

### <a name="stopstart-operation"></a>停止/啟動作業

- 伺服器無法停止超過七天。

### <a name="scheduled-maintenance"></a>排程維護

- 在已規劃的升級之前，將維護期間變更為少於五天，將不會影響該升級。 變更只會在下一次排程的維護時生效。

### <a name="backing-up-a-server"></a>備份伺服器

- 系統會管理備份，目前沒有任何方法可以手動執行這些備份。 建議 `pg_dump` 改用。
- 備份一律是以快照集為基礎的完整備份， (不是差異備份) ，可能會導致更高的備份儲存體使用率。 請注意，交易記錄 (預先寫入記錄檔-WAL) 與完整/差異備份分開，而且會持續保存。

### <a name="restoring-a-server"></a>還原伺服器

- 使用時間點還原功能時，會建立新的伺服器，其計算和儲存體設定與其所依據的伺服器相同。
- 當您從備份還原時，VNET 型資料庫伺服器會還原至相同的 VNET。
- 在還原期間建立的新伺服器不會有原始伺服器中的防火牆規則。 針對新的伺服器，必須另外建立防火牆規則。
- 不支援還原已刪除的伺服器。
- 不支援跨區域還原。

### <a name="other-features"></a>其他功能

* 尚未支援 Azure AD 驗證。 如果您需要 Azure AD authentication，建議使用 [單一伺服器](../overview-single-server.md) 選項。
* 尚未支援讀取複本。 如果您需要讀取複本，建議使用 [單一伺服器](../overview-single-server.md) 選項。


## <a name="next-steps"></a>後續步驟

- 瞭解 [計算和儲存選項的可用功能](concepts-compute-storage.md)
- 了解[支援的 PostgreSQL 資料庫版本](concepts-supported-versions.md)
- 檢閱[如何使用 Azure 入口網站，在適用於 PostgreSQL 的 Azure 資料庫中備份和還原伺服器](how-to-restore-server-portal.md)
