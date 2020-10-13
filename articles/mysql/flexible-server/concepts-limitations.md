---
title: 限制-適用於 MySQL 的 Azure 資料庫彈性的伺服器
description: 本文說明適用於 MySQL 的 Azure 資料庫彈性伺服器的限制，例如連接和儲存引擎選項的數目。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 6fff7f22e7d265eb9b15bcec8604eeab692ac1c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650284"
---
# <a name="limitations-in-azure-database-for-mysql---flexible-server-preview"></a>適用於 MySQL 的 Azure 資料庫彈性的伺服器 (預覽) 的限制

> [!IMPORTANT] 
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。

本文說明適用於 MySQL 的 Azure 資料庫彈性伺服器服務的限制。 MySQL 資料庫引擎的[一般限制](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.7/en/limits.html)也適用。 如果您想要瞭解資源 (計算、記憶體、儲存體) 層，請參閱「 [計算和儲存](concepts-compute-storage.md) 」一文。

## <a name="server-parameters"></a>伺服器參數

> [!NOTE]
> 如果您要尋找伺服器參數的最小值/最大值（例如 `max_connections` 和 `innodb_buffer_pool_size` ），此資訊已移至伺服器參數概念 <!-- **[server parameters](./concepts-server-parameters.md)** --> 一文。

適用於 MySQL 的 Azure 資料庫支援調整伺服器參數的值。 某些參數的最小值和最大值 (例如。 `max_connections`、 `join_buffer_size` `query_cache_size`) 是由伺服器的計算層和計算大小所決定。 請參閱伺服器參數概念 <!-- [server parameters](./concepts-server-parameters.md)--> 以取得這些限制的詳細資訊。

服務不支援密碼外掛程式，例如 "validate_password" 和 "caching_sha2_password"。

## <a name="storage-engines"></a>儲存引擎

MySQL 支援許多儲存引擎。 在適用於 MySQL 的 Azure 資料庫彈性的伺服器上，支援和不支援下列儲存引擎：

### <a name="supported"></a>支援
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html) \(英文\)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html) \(英文\)

### <a name="unsupported"></a>不支援
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html) \(英文\)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html) \(英文\)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html) \(英文\)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html) \(英文\)

## <a name="privileges--data-manipulation-support"></a>& 資料操作支援的許可權

許多伺服器參數與設定可能會不慎降低伺服器效能或使 MySQL 伺服器的 ACID 屬性變差。 為了維護產品層級的服務完整性與 SLA，此服務不會公開多個角色。 

MySQL 服務不允許直接存取基礎檔案系統。 不支援某些資料操作命令。 

### <a name="unsupported"></a>不支援

以下是不支援的：
- DBA 角色：受限制。 或者，您可以使用在新的伺服器建立期間建立的系統管理員使用者 () ，讓您可以執行大部分的 DDL 和 DML 語句。 
- 超級許可權：同樣地， [超級許可權](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) 受到限制。
- 定義：需要進階的權限才能建立，而且受限制。 如果使用備份匯入資料，執行 mysqldump 時以手動方式或使用 `--skip-definer` 命令移除 `CREATE DEFINER` 命令。
- 系統資料庫： [mysql 系統資料庫](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) 是唯讀的，用來支援各種 PaaS 功能。 您無法對 `mysql` 系統資料庫進行變更。
- `SELECT ... INTO OUTFILE`：服務中不支援。

### <a name="supported"></a>支援
- 支援 `LOAD DATA INFILE`，但必須指定 `[LOCAL]` 參數並導向至 UNC 路徑 (透過 SMB 掛接的 Azure 儲存體)。

## <a name="functional-limitations"></a>功能限制：

### <a name="zone-redundant-ha"></a>區域冗余 HA
- 此設定只能在伺服器建立期間設定。
- 在高載計算層級中不支援。

### <a name="networking"></a>網路功能
- 建立伺服器之後，就無法變更連接方法。 如果伺服器是利用 *私人存取 (VNet 整合) *所建立，則在建立之後，就無法將它變更為 *公用存取 (允許的 IP 位址) * ，反之亦然
- 預設會啟用 TLS/SSL，且無法停用。
- 伺服器支援的最低 TLS 版本為 TLS 1.2。 若要深入瞭解，請參閱 [使用 TLS/SSL 連線](./how-to-connect-tls-ssl.md) 。

### <a name="stopstart-operation"></a>停止/啟動作業
- 在主要和待命)  (不支援區域冗余 HA 設定。
-  () 的來源和複本都不支援讀取複本設定。

### <a name="scale-operations"></a>調整作業
- 不支援減少布建的伺服器儲存體。

### <a name="read-replicas"></a>讀取複本
- 在主要和待命)  (不支援區域冗余 HA 設定。

### <a name="server-version-upgrades"></a>伺服器版本升級
- 不支援主要資料庫引擎版本之間的自動遷移。 如果您想要升級主要版本，請取得傾印，並將其 [還原](../concepts-migrate-dump-restore.md) 至使用新引擎版本建立的伺服器。

### <a name="restoring-a-server"></a>還原伺服器
- 使用時間點還原時，會建立新的伺服器，其計算和儲存體設定與它所依據的來源伺服器相同。 您可以在建立伺服器之後調整新還原的伺服器計算。
- 不支援還原已刪除的伺服器。

## <a name="next-steps"></a>後續步驟

- 瞭解 [計算和儲存選項的可用功能](concepts-compute-storage.md)
- 深入瞭解 [支援的 MySQL 版本](concepts-supported-versions.md)
- 瞭解 [如何使用 Azure 入口網站來備份和還原伺服器](how-to-restore-server-portal.md)