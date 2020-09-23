---
title: 監視和計量-適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器
description: 本文說明適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中的監視和計量功能。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 7d1d7f9759fc8e2826da6232981eb3a389576c07
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933891"
---
# <a name="monitor-metrics-azure-database-for-postgresql---flexible-server"></a>監視計量適用於 PostgreSQL 的 Azure 資料庫-彈性伺服器

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

監視伺服器的相關資料，可協助您疑難排解並最佳化您的工作負載。 「適用於 PostgreSQL 的 Azure 資料庫」提供各種監視選項，可讓您深入了解伺服器的行為。

## <a name="metrics"></a>計量
適用於 PostgreSQL 的 Azure 資料庫提供多種計量，可讓您深入了解支援 PostgreSQL 伺服器資源的行為。 每個計量都會以一分鐘的頻率發出，最多可達 [93 天的歷程記錄](../../azure-monitor/platform/data-platform-metrics.md#retention-of-metrics)。 您可以在計量上設定警示。 其他選項包括設定自動化動作、執行先進的分析，以及封存歷程記錄。 如需詳細資訊，請參閱 [Azure 計量概觀](../../azure-monitor/platform/data-platform-metrics.md)。

### <a name="list-of-metrics"></a>計量清單
下列計量適用于于 postgresql 彈性伺服器：


|計量|計量顯示名稱|單位|描述|
|---|---|---|---|
| backup_storage_used | 已使用的備份儲存體 | 位元組 | 使用的備份儲存體數量。 此計量代表所有完整資料庫備份、差異備份和記錄備份所使用的儲存體總和（根據針對伺服器所設定的備份保留期限而定）。 備份的頻率是服務管理的。 針對異地複寫儲存體，備份儲存體使用量會是本機冗余儲存體的兩倍。 |
| connections_failed | 失敗的連線 | Count | 失敗的連接。 |
| connections_succeeded | 成功的連接 | Count | 成功的連接。 |
| cpu_credits_consumed | CPU Credits Consumed | Count | 彈性伺服器所使用的信用額度數目。 適用于高載層。 |
| cpu_credits_remaining | 剩餘 CPU 信用額度 | Count | 可供高載的點數數目。 適用于高載層。 |
| cpu_percent | CPU 百分比 | 百分比 | 使用中的 CPU 百分比。 | 
| disk_queue_depth | 磁片佇列深度 | Count | 資料磁片的未完成 i/o 作業數目。 |
| iops | IOPS | Count | 每秒磁片的 i/o 作業數目。 |
| maximum_used_transactionIDs | 使用的最大交易識別碼 | Count | 使用中的最大交易識別碼。 |
| memory_percent | 記憶體百分比 | 百分比 | 使用中記憶體的百分比。 |
| network_bytes_egress | Network Out | 位元組 | 連出網路流量的數量。 |
| network_bytes_ingress | Network In | 位元組 | 傳入網路流量的數量。 |
| read_iops | 讀取 IOPS | Count | 每秒的資料磁片 i/o 讀取作業數。 |
| read_throughput | 讀取輸送量 | 位元組 | 每秒從磁片讀取的位元組數。 |
| storage_free | 免費儲存體 | 位元組 | 可用的儲存空間量。 |
| storage_percent | 儲存體百分比 | 百分比 | 使用的儲存空間百分比。 此服務所使用的儲存體可能包括資料庫檔案、交易記錄和伺服器記錄。|
| storage_used | 使用的儲存體 | 位元組 | 使用的儲存空間百分比。 此服務所使用的儲存體可能包括資料庫檔案、交易記錄和伺服器記錄。 |
| txlogs_storage_used | 使用的交易記錄儲存體 | 位元組 | 交易記錄所使用的儲存空間量。 | 
| write_throughput | 寫入輸送量 | 位元組 | 每秒寫入磁片的位元組數。 |
| write_iops | 寫入 IOPS | Count | 每秒的資料磁片 i/o 寫入作業數。 |

## <a name="server-logs"></a>伺服器記錄
適用於 PostgreSQL 的 Azure 資料庫可讓您設定和存取 Postgres 的標準記錄。 若要深入瞭解記錄，請造訪 [記錄概念](concepts-logging.md)檔。
