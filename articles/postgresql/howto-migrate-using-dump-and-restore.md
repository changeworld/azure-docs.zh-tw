---
title: 傾印和還原-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 說明如何將于 postgresql 資料庫解壓縮到傾印檔案，並從適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中 pg_dump 所建立的檔案還原。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 4fe15d1bd23f36b7289c54bedf575ae4760600e0
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710799"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>使用傾印和還原來移轉 PostgreSQL 資料庫
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

您可以使用 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 將 PostgreSQL 資料庫擷取到傾印檔案，並使用 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) 從 pg_dump 所建立的封存檔案還原 PostgreSQL 資料庫。

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- [適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-database-portal.md)，而且防火牆規則要允許存取其中的資料庫。
- 安裝 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 和 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) 命令列公用程式

請遵循下列步驟來傾印和還原 PostgreSQL 資料庫：

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>使用 pg_dump 建立傾印檔案，其中包含要載入的資料
若要備份內部部署或 VM 中的現有 PostgreSQL 資料庫，請執行下列命令︰
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
比方說，如果您有本機伺服器和名為 **testdb** 的資料庫
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postgresql-using-pg_restore"></a>使用 pg_restore 將資料還原至目標適用於 PostgreSQL 的 Azure 資料庫
在建立目標資料庫後，您可以使用 pg_restore 命令和 -d、--dbname 參數，從傾印檔案將資料還原至目標資料庫。
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

包括 --no-owner 參數會導致在還原期間建立的所有物件都由使用 --username 指定的使用者所擁有。 如需詳細資訊，請參閱 [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) 上的官方 PostgreSQL 文件。

> [!NOTE]
> 如果您的于 postgresql 伺服器需要 (在適用於 PostgreSQL 的 Azure 資料庫伺服器) 的 TLS/SSL 連線，請設定環境變數， `PGSSLMODE=require` 讓 pg_restore 工具可以與 TLS 連接。 如果沒有 TLS，可能會讀取錯誤  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> 在 Windows 命令列中，執行命令 `SET PGSSLMODE=require`，然後再執行 pg_restore 命令。 在 Linux 或 Bash中，執行命令 `export PGSSLMODE=require`，然後再執行 pg_restore 命令。
>

在此範例中，請從傾印檔案 **testdb.dump**，將資料還原至目標伺服器 **mydemoserver.postgres.database.azure.com** 上的資料庫 **mypgsqldb**。

以下是如何針對**單一伺服器**使用此**pg_restore**的範例：

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```
以下是如何將此 **pg_restore** 用於 **彈性伺服器**的範例：

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```
---

## <a name="optimizing-the-migration-process"></a>將移轉流程最佳化

將現有 PostgreSQL 資料庫移轉至 適用於 PostgreSQL 的 Azure 資料庫服務，方法之一就是備份來源上的資料庫，再於 Azure 中還原。 若要讓完成移轉所需的時間降到最低，請考慮搭配備份與還原命令命令使用下列參數。

> [!NOTE]
> 如需詳細的語法資訊，請參閱 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 與 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) 文章。
>

### <a name="for-the-backup"></a>進行備份
- 使用 -Fc 參數建立備份，能夠平行執行還原，加快執行速度。 例如：

    ```bash
    pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
    ```

### <a name="for-the-restore"></a>進行還原
- 建議您將備份檔案移至 Azure VM，而其和您即將移轉到的適用於 PostgreSQL 的 Azure 資料庫伺服器位於相同區域，然後從該 VM 執行 pg_restore，以減少網路延遲。 我們也建議您建立 VM 時，啟用[加速網路](../virtual-network/create-vm-accelerated-networking-powershell.md)。

- 根據預設，這應已完成，但請開啟傾印檔案，確認 Create Index 陳述式位在插入的資料之後。 若非如此，請將 Create Index 陳述式移至插入的資料之後。

- 使用交換器（Fc 和-j） *#* 進行還原，以平行處理還原。 *#* 這是目標伺服器上的核心數目。 您也可以嘗試 *#* 將設定為目標伺服器核心數目的兩倍，以查看影響。 例如：

以下是如何針對**單一伺服器**使用此**pg_restore**的範例：
```bash
 pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
```
以下是如何將此 **pg_restore** 用於 **彈性伺服器**的範例：
```bash
 pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
 ```

- 您也可以在開頭處新增 *set synchronous_commit = off;* 命令並在結尾處新增 *set synchronous_commit = on;*，來編輯傾印檔案。 在應用程式變更資料之前，未在結尾處將它設為開啟，可能會造成後續遺失資料。

- 在適用於 PostgreSQL 的 Azure 資料庫目標伺服器上，請在還原前考慮下列事項：
    - 關閉查詢效能追蹤，因為在移轉期間不需要這些統計資料。 將 pg_stat_statements.track、pg_qs.query_capture_mode 和 pgms_wait_sampling.query_capture_mode 設定為 NONE，即可達成。

    - 使用高計算和高記憶體 sku (例如最佳化的 32 虛擬核心記憶體) 來加速移轉。 完成還原之後，您可以輕鬆地調降回您慣用的 sku。 sku 越高，您即可藉由在 pg_restore 命令中增加對應 `-j` 參數 達成更多的平行處理原則。

    - 目標伺服器上的 IOPS 越多，可能改善還原效能。 您可以增加伺服器的儲存體大小，以佈建更多 IOPS。 此設定無法復原，但請考慮較高的 IOPS 對您未來的實際工作負載是否有所助益。

請務必在測試環境中測試和驗證這些命令之後，再於在生產環境中使用。

## <a name="next-steps"></a>後續步驟
- 若要使用匯出和匯入來移轉 PostgreSQL 資料庫，請參閱[使用匯出和匯入移轉 PostgreSQL 資料庫](howto-migrate-using-export-and-import.md)。
- 如需有關將資料庫移轉至適用於 PostgreSQL 的 Azure 資料庫詳細資訊，請參閱[資料庫移轉指南](https://aka.ms/datamigration)。
