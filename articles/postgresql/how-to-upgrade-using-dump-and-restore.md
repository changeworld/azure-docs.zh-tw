---
title: 使用傾印和還原進行升級-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 描述使用傾印和還原資料庫的離線升級方法，以遷移至更高版本的適用於 PostgreSQL 的 Azure 資料庫單一伺服器。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 42bbe1c9f4056ae0dae0ccd59b452db90a7c63c5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493656"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>使用傾印和還原升級于 postgresql 資料庫

您可以使用下列方法，將您的資料庫移轉至較高的主要版本伺服器，以升級適用於 PostgreSQL 的 Azure 資料庫單一伺服器中部署的于 postgresql 伺服器。
* 使用於 postgresql [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html)和 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)的 **離線** 方法會造成遷移資料的停機時間。 本檔說明這種升級/遷移方法。
* 使用 [資料庫移轉服務](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) (DMS) 的 **線上** 方法。 這種方法可減少停機時間，並讓目標資料庫與來源保持同步，而且您可以選擇何時要剪下。 不過，使用 DMS 有一些必要條件和限制。 如需詳細資訊，請參閱 [DMS 檔](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md)集。 

 下表提供以資料庫大小和案例為基礎的一些建議。

| **資料庫/案例** | **傾印/還原 (離線)** | **DMS (線上)** |
| ------ | :------: | :-----: |
| 您有一個小型資料庫，而且可以承受停機時間來升級  | X | |
| 小型資料庫 ( # A0 10 GB)   | X | X | 
| 中小型 Db (10 GB – 100 GB)  | X | X |
| 大型資料庫 ( # A0 100 GB)  |  | X |
| 無論資料庫大小，都能承受停機升級 ()  | X |  |
| 可以 [解決 DMS 必要條件](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md#prerequisites)，包括重新開機嗎？ |  | X |
| 在升級過程中，可以避免 Ddl 和未記錄資料表嗎？ | |  X |

本指南提供一些離線遷移方法和範例，示範如何從來源伺服器遷移到執行較高版本于 postgresql 的目標伺服器。

> [!NOTE]
> 您可以透過許多方式來執行于 postgresql 傾印和還原。 您可以選擇使用本指南所提供的其中一種方法進行遷移，或選擇任何替代方式來符合您的需求。 如需使用其他參數的詳細傾印和還原語法，請參閱 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 和 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)的文章。 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-database-for-postgresql"></a>搭配適用於 PostgreSQL 的 Azure 資料庫使用傾印和還原的必要條件
 
若要逐步執行本作法指南，您需要：

- 執行您想要升級之9.5、9.6 或10的 **來源** 于 postgresql 資料庫
- **目標** 于 postgresql 資料庫伺服器，其具有所需的主要版本 [適用於 PostgreSQL 的 Azure 資料庫 server](quickstart-create-server-database-portal.md)。 
- 用來執行傾印和還原命令的于 postgresql 用戶端系統。
  - 它可以是已安裝于 postgresql 的 Linux 或 Windows 用戶端，並已安裝 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 和 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) 命令列公用程式。 
  - 或者，您也可以使用 [Azure Cloud Shell](https://shell.azure.com) ，或在 [Azure 入口網站](https://portal.azure.com)的右上方按一下功能表列上的 [Azure Cloud Shell]。 您必須在執行傾印 `az login` 和還原命令之前，先登入您的帳戶。
- 您的于 postgresql 用戶端最好在與來源和目標伺服器相同的區域中執行。 


## <a name="additional-details-and-considerations"></a>其他詳細資料和考慮
- 您可以從入口網站按一下 [連接字串]，以尋找來源和目標資料庫的連接字串。 
- 您可能在伺服器中執行一個以上的資料庫。 您可以藉由連接到來源伺服器並執行，來尋找資料庫清單 `\l` 。
- 在目標資料庫伺服器中建立對應的資料庫。
- 您可以跳過升級 `azure_maintenance` 或範本資料庫。
- 請參閱上面的表格，以判斷資料庫適用于這種移轉模式。
- 如果您想要使用 Azure Cloud Shell，請注意會話會在20分鐘後過期。 如果您的資料庫大小 < 10 GB，您可以在沒有會話超時的情況下完成升級。否則，您可能必須透過其他方法讓會話保持開啟，例如 <Enter> 在10-15 分鐘內按下鍵一次。 


## <a name="example-database-used-in-this-guide"></a>本指南中使用的範例資料庫

在本指南中，會使用下列來源和目標伺服器和資料庫名稱來說明範例。

 | **說明** | **ReplTest1** |
 | ------- | ------- |
 | 來源伺服器 (9.5)  | pg-95.postgres.database.azure.com |
 | 來源資料庫 | bench5gb |
 | 源資料庫大小 | 5 GB |
 | 來源使用者名稱 | pg@pg-95 |
 | 目標伺服器 (v11)  | pg-11.postgres.database.azure.com |
 | 目標資料庫 | bench5gb |
 | 目標使用者名稱 | pg@pg-11 |

## <a name="upgrade-your-databases-using-offline-migration-methods"></a>使用離線遷移方法升級資料庫
您可以選擇使用本節中所述的其中一種方法來進行升級。 執行工作時，您可以使用下列秘訣。

- 如果您在來源和目標資料庫中使用相同的密碼，您可以設定 `PGPASSWORD=yourPassword` 環境變數。  然後您不需要在每次執行命令時都提供密碼，例如 psql、pg_dump 和 pg_restore。  同樣地，您也可以設定其他變數，例如等等 `PGUSER` `PGSSLMODE` 。請參閱 [于 postgresql 環境變數](https://www.postgresql.org/docs/11/libpq-envars.html)。
  
- 如果您的于 postgresql 伺服器需要 (在適用於 PostgreSQL 的 Azure 資料庫伺服器) 的 TLS/SSL 連線，請設定環境變數， `PGSSLMODE=require` 讓 pg_restore 工具可以與 TLS 連接。 如果沒有 TLS，可能會讀取錯誤  `FATAL:  SSL connection is required. Please specify SSL options and retry.`

- 在 Windows 命令列中，執行命令 `SET PGSSLMODE=require`，然後再執行 pg_restore 命令。 在 Linux 或 Bash中，執行命令 `export PGSSLMODE=require`，然後再執行 pg_restore 命令。

### <a name="method-1-migrate-using-dump-file"></a>方法1：使用傾印檔案進行遷移

此方法牽涉到兩個步驟。 首先是從來源伺服器建立傾印。 第二個步驟是將傾印檔案還原到目標伺服器。 如需詳細資訊，請參閱使用傾印 [和還原](howto-migrate-using-dump-and-restore.md) 檔的遷移。 如果您有大型資料庫，而且您的用戶端系統有足夠儲存傾印檔案的儲存體，則建議使用這種方法。

### <a name="method-2-migrate-using-streaming-the-dump-data-to-the-target-database"></a>方法2：使用資料流程將傾印資料移轉至目標資料庫

如果您沒有于 postgresql 用戶端，或想要使用 Azure Cloud Shell，則可以使用這個方法。 資料庫傾印會直接串流處理到目標資料庫伺服器，而不會在用戶端中儲存傾印。 因此，這可以與具有有限儲存空間的用戶端搭配使用，甚至可以從 Azure Cloud Shell 中執行。 

1. 請使用命令確認資料庫存在於目標伺服器中 `\l` 。 如果資料庫不存在，則建立資料庫。
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```SQL
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. 使用管道以單一命令列執行傾印和還原。 
    ```azurecli-interactive
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    例如，

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. 升級 (遷移) 程式完成之後，您就可以在目標伺服器上測試您的應用程式。 
4. 針對伺服器內的所有資料庫重複此程式。

 例如，下表說明使用串流傾印方法進行遷移所花費的時間。 使用 [pgbench](https://www.postgresql.org/docs/10/pgbench.html)填入範例資料。 由於您的資料庫可以有不同大小的不同物件數目，而不是 pgbench 產生的資料表和索引，因此強烈建議您測試資料庫的傾印和還原，以瞭解升級資料庫所需的實際時間。 

| **資料庫大小** | **大約是花費的時間** | 
| ----- | ------ |
| 1 GB  | 1-2 分鐘 |
| 5 GB | 8-10 分鐘 |
| 10 GB | 15-20 分鐘 |
| 50 GB | 1-1.5 小時 |
| 100 GB | 2.5-3 小時|
   
### <a name="method-3-migrate-using-parallel-dump-and-restore"></a>方法3：使用平行傾印和還原進行遷移 

如果您的資料庫中有幾個較大的資料表，而您想要平行處理該資料庫的傾印和還原程式，您可以考慮使用這個方法。 您的用戶端系統也需要足夠的儲存體來容納備份傾印。 這種平行傾印和還原程式可減少完成整個遷移的時間耗用量。 例如，使用方法1和2完成遷移的 50 GB pgbench 資料庫（使用此方法時）所花費的時間不超過30分鐘。

1. 針對來源伺服器中的每個資料庫，在目標伺服器上建立對應的資料庫。

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   例如，
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. 以目錄格式執行 pg_dump 命令，其中的工作數目 = 4 (資料庫) 中的資料表數目。 使用較大的計算層和更多資料表時，您可以將它增加至較高的數位。 這 pg_dump 會建立目錄來儲存每個工作的壓縮檔案。

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    例如，
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. 然後，在目標伺服器上還原備份。
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    例如，
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> 本檔中所述的程式也可以用來升級適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器（目前為預覽狀態）。 主要差異在於彈性伺服器目標的連接字串不是 `@dbName` 。  例如，如果使用者名稱是，則 `pg` 連接字串中單一伺服器的使用者名稱將會是 `pg@pg-95` ，而有彈性的伺服器則可以直接使用 `pg` 。

## <a name="next-steps"></a>後續步驟

- 當您對目標資料庫函數感到滿意之後，就可以卸載舊的資料庫伺服器。 
- 如果您想要使用與來源伺服器相同的資料庫端點，則在刪除舊的源資料庫伺服器之後，您可以使用舊的資料庫伺服器名稱來建立讀取複本。 一旦建立穩定狀態，您就可以停止複本，這會將複本伺服器升級為獨立的伺服器。 如 [需詳細資訊，請](./concepts-read-replicas.md) 參閱複寫。
- 請務必在測試環境中測試和驗證這些命令之後，再於在生產環境中使用。