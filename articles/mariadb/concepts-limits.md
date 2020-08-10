---
title: 限制 - 適用於 MariaDB 的 Azure 資料庫
description: 本文說明適用於 MariaDB 的 Azure 資料庫有何限制，例如連線數量和儲存引擎選項。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: 51aff856aa5bdeb042493d47f100be0ca32dfbbb
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88032674"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>適用於 MariaDB 的 Azure 資料庫相關限制
下列各節說明資料庫服務中的容量、儲存引擎支援、權限支援、資料操作陳述式支援，以及功能限制。

## <a name="server-parameters"></a>伺服器參數

> [!NOTE]
> 如果您要尋找伺服器參數（如和）的最小/最大值 `max_connections` `innodb_buffer_pool_size` ，這項資訊已移至**[伺服器參數](./concepts-server-parameters.md)** 文章。

適用於 MariaDB 的 Azure 資料庫支援微調伺服器參數的值。 某些參數的最小和最大值， (例如。 `max_connections`、 `join_buffer_size` `query_cache_size`) 是由伺服器的定價層和虛擬核心所決定。 如需這些限制的詳細資訊，請參閱[伺服器參數](./concepts-server-parameters.md)。

在初始部署時，Azure for 適用于 mariadb 伺服器會包含時區資訊的系統資料表，但不會填入這些資料表。 時區資料表可藉由從 MySQL 命令列或 MySQL Workbench 等工具呼叫 `mysql.az_load_timezone` 預存程序來填入。 請參閱 [Azure 入口網站](howto-server-parameters.md#working-with-the-time-zone-parameter)或 [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) 文章，以了解如何呼叫預存程序，以及設定全域或工作階段層級的時區。

## <a name="storage-engine-support"></a>儲存引擎支援

### <a name="supported"></a>支援
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/) \(英文\)
- [MEMORY](https://mariadb.com/kb/en/library/memory-storage-engine/) \(英文\)

### <a name="unsupported"></a>不支援
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/) \(英文\)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/) \(英文\)
- [ARCHIVE](https://mariadb.com/kb/en/library/archive/) \(英文\)

## <a name="privilege-support"></a>權限支援

### <a name="unsupported"></a>不支援
- DBA 角色：許多伺服器參數與設定可能會在無意中造成伺服器效能降級，或是取消 DBMS 的 ACID 屬性。 因此，為了維護產品層級的服務完整性與 SLA，此服務並不會公開 DBA 角色。 在建立新資料庫行個體時所建構的預設使用者帳戶，可讓使用者在受管理的資料庫執行個體中執行大部分的 DDL 與 DML 陳述式。
- SUPER 權限：同樣地，[SUPER 權限](https://mariadb.com/kb/en/library/grant/#global-privileges)也受到限制。
- 定義：需要進階的權限才能建立，而且受限制。 如果使用備份匯入資料，執行 mysqldump 時以手動方式或使用 `--skip-definer` 命令移除 `CREATE DEFINER` 命令。
- 系統資料庫：在適用於 MariaDB 的 Azure 資料庫中， [mysql 系統資料庫](https://mariadb.com/kb/en/the-mysql-database-tables/)是唯讀的，因為它是用來支援各種 PaaS 服務功能。 請注意，您無法變更系統資料庫中的任何專案 `mysql` 。

## <a name="data-manipulation-statement-support"></a>資料操作陳述式支援

### <a name="supported"></a>支援
- 支援 `LOAD DATA INFILE`，但必須指定 `[LOCAL]` 參數並導向至 UNC 路徑 (透過 SMB 掛接的 Azure 儲存體)。

### <a name="unsupported"></a>不支援
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>功能限制：

### <a name="scale-operations"></a>調整作業
- 目前不支援基本定價層的雙向動態調整。
- 不支援減少伺服器儲存體大小。

### <a name="server-version-upgrades"></a>伺服器版本升級
- 目前不支援在主要資料庫引擎版本之間進行自動轉換。

### <a name="point-in-time-restore"></a>還原時間點
- 使用 PITR 功能時，所建立新伺服器的設定會與作為新伺服器基礎的伺服器設定相同。
- 不支援還原已刪除的伺服器。

### <a name="subscription-management"></a>訂用帳戶管理
- 目前不支援跨訂用帳戶和資源群組動態移動預先建立的伺服器。

### <a name="vnet-service-endpoints"></a>VNet 服務端點
- VNet 服務端點的支援僅適用於一般用途伺服器和記憶體最佳化伺服器。

### <a name="storage-size"></a>儲存體大小
- 如需每個定價層的儲存體大小限制，請參閱[定價層](concepts-pricing-tiers.md)。

## <a name="current-known-issues"></a>目前已知問題
- MariaDB 伺服器執行個體於建立連線後會顯示不正確的伺服器版本。 若要取得正確的伺服器執行個體引擎版本，請使用 `select version();` 命令。

## <a name="next-steps"></a>後續步驟
- [每個服務層級中可用的項目](concepts-pricing-tiers.md)
- [支援的 MariaDB 資料庫版本](concepts-supported-versions.md)
