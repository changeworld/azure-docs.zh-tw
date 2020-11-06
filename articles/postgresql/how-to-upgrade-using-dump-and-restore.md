---
title: 使用傾印和還原進行升級-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 描述用來傾印和還原資料庫以遷移至更高版本適用於 PostgreSQL 的 Azure 資料庫單一伺服器的幾個方法。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/05/2020
ms.openlocfilehash: 6dfcf0b2ec1d46821007123908a8e7ba8df29744
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421759"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>使用傾印和還原升級于 postgresql 資料庫

在適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中，建議使用下列其中一種方法，將您的于 postgresql 資料庫引擎升級為較高的主要版本：
* 使用於 postgresql [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 和 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)的離線方法。 在此方法中，您會先從來源伺服器執行傾印，然後在目標伺服器中還原該傾印。
* 使用 [**資料庫移轉服務**](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal) (DMS) 的線上方法。 這個方法會使目標資料庫與來源保持同步，而且您可以選擇何時要剪下。 不過，有一些必要條件和限制要解決。 

當您在線上和離線方法之間做決定，以執行主要版本升級時，您可以使用下列建議。

| **資料庫** | **傾印/還原 (離線)** | **DMS (線上)** |
| ------ | :------: | :-----: |
| 您有一個小型資料庫，而且可以承受停機時間來升級  | X | |
| 小型資料庫 ( # A0 10 GB)   | X | X | 
| 中小型 Db (10 GB – 100 GB)  | X | X |
| 大型資料庫 ( # A0 100 GB)  |  | X |
| 無論資料庫大小，都能承受停機升級 ()  | X |  |
| 可以解決 DMS 必要條件 [，](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal#prerequisites) 包括重新開機嗎？ |  | X |
| 在升級過程中，可以避免 Ddl 和未記錄資料表嗎？ | |  X |

本操作指南提供兩種範例方法，說明如何使用於 postgresql pg_dump 和 pg_restore 命令升級資料庫。 這份檔中的程式稱為「 **升級** 」，不過資料庫會從來源伺服器  **遷移** 至目標伺服器。 

> [!NOTE]
> 您可以透過許多方式來執行于 postgresql 傾印和還原。 您可以選擇使用與本檔中所指的不同方法。 例如，若要執行傾印，然後從于 postgresql 用戶端進行還原，請參閱 [檔](./howto-migrate-using-dump-and-restore.md) ，以取得逐步程式詳細資料和最佳作法。 如需使用其他參數的詳細傾印和還原語法，請參閱 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 和 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)的文章。 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-postgresql"></a>使用 Azure 于 postgresql 進行傾印和還原的必要條件
 
若要逐步執行本作法指南，您需要：
- 執行9.5、9.6 或 10 (適用於 PostgreSQL 的 Azure 資料庫–單一伺服器) 的源資料庫
- 目標資料庫伺服器與所需的于 postgresql 主要版本 [適用於 PostgreSQL 的 Azure 資料庫 server](quickstart-create-server-database-portal.md)。 
- 用戶端系統 (已安裝于 postgresql 的 Linux) ，並且已安裝 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 和 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) 命令列公用程式。 
- 或者，您也可以使用 [Azure Cloud Shell](https://shell.azure.com) ，或在 [Azure 入口網站](https://portal.azure.com)的右上方按一下功能表列上的 [Azure Cloud Shell]。 您必須在執行傾印 `az login` 和還原命令之前，先登入您的帳戶。
- 您的于 postgresql 用戶端位置（例如 VM）最好是在與來源和目標伺服器相同的區域中執行) 。 

## <a name="additional-details-and-considerations"></a>其他詳細資料和考慮
- 您可以從入口網站按一下 [連接字串]，以尋找來源和目標資料庫的連接字串。 
- 您可能在伺服器中執行一個以上的資料庫。 您可以藉由連接到來源伺服器並執行，來尋找資料庫清單 `\l` 。
- 在目標資料庫伺服器中建立對應的資料庫。
- 您可以跳過升級 `azure_maintenance` 或範本資料庫。
- 請參閱上面的表格，以判斷資料庫適用于這種移轉模式。
- 如果您想要使用 Azure Cloud Shell，會話會在20分鐘後過期。 如果您的資料庫大小是 < 10 GB，您可能會在沒有時間的情況下完成升級。否則，您可能必須透過其他方法讓會話保持開啟，例如 <Enter> 在10-15 分鐘內按下鍵一次。 

> [!TIP] 
> - 如果您在來源和目標資料庫中使用相同的密碼，您可以設定 `PGPASSWORD=yourPassword` 環境變數。  然後您就不需要在每次執行命令時都提供密碼，例如 psql、pg_dump 和 pg_restore。  同樣地，您也可以設定其他變數，例如等等 `PGUSER` `PGSSLMODE` 。請參閱 [于 postgresql 環境變數](https://www.postgresql.org/docs/11/libpq-envars.html)。
>  
> - 如果您的于 postgresql 伺服器需要 (在適用於 PostgreSQL 的 Azure 資料庫伺服器) 的 TLS/SSL 連線，請設定環境變數， `PGSSLMODE=require` 讓 pg_restore 工具可以與 TLS 連接。 如果沒有 TLS，可能會讀取錯誤  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> - 在 Windows 命令列中，執行命令 `SET PGSSLMODE=require`，然後再執行 pg_restore 命令。 在 Linux 或 Bash中，執行命令 `export PGSSLMODE=require`，然後再執行 pg_restore 命令。

## <a name="example-database-used-in-this-guide"></a>本指南中使用的範例資料庫

 | **說明** | **ReplTest1** |
 | ------- | ------- |
 | 來源伺服器 (9.5)  | pg-95.postgres.database.azure.com |
 | 來源資料庫 | bench5gb |
 | 源資料庫大小 | 5 GB |
 | 來源使用者名稱 | pg@pg-95 |
 | 目標伺服器 (v11)  | pg-11.postgres.database.azure.com |
 | 目標資料庫 | bench5gb |
 | 目標使用者名稱 | pg@pg-11 |

## <a name="method-1-upgrade-with-streaming-backups-to-the-target"></a>方法1：使用串流備份升級至目標 

在此方法中，會將整個資料庫傾印直接串流處理到目標資料庫伺服器，而不會在用戶端中儲存傾印。 因此，這可以與具有有限儲存空間的用戶端搭配使用，甚至可以從 Azure Cloud Shell 中執行。 

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

    例如，套用至物件的

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. 升級 (遷移) 程式完成之後，您就可以在目標伺服器上測試您的應用程式。 
4. 針對伺服器內的所有資料庫重複此程式。

 下表說明使用此方法進行升級所需的時間。 使用 [pgbench](https://www.postgresql.org/docs/10/pgbench.html)填入資料。 由於您的資料庫可以有不同大小的不同物件數目，而不是 pgbench 產生的資料表和索引，因此強烈建議您測試資料庫的傾印和還原，以瞭解升級資料庫所需的實際時間。 

| **資料庫大小** | **大約是花費的時間** | 
| ----- | ------ |
| 1 GB  | 1-2 分鐘 |
| 5 GB | 8-10 分鐘 |
| 10 GB | 15-20 分鐘 |
| 50 GB | 1-1.5 小時 |
| 100 GB | 2.5-3 小時|
   
## <a name="method-2-upgrade-with-parallel-dump-and-restore"></a>方法2：使用平行傾印和還原進行升級 

如果您的資料庫中有幾個較大的資料表，而您想要平行處理該資料庫的傾印和還原程式，這個方法會很有用。 您需要足夠的本機磁片儲存體來容納您資料庫的備份傾印。 此平行傾印和還原程式可減少完成整個遷移/升級所耗用的時間。 例如，在30分鐘內完成遷移的 50 GB pgbench 資料庫已完成 1-1.5 小時。

1. 針對來源伺服器中的每個資料庫，在目標伺服器上建立對應的資料庫。

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   例如，套用至物件的
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. 以目錄格式執行 pg_dump 命令，其中的工作數目 = 4 (資料庫) 中的資料表數目。 使用較大的計算層和更多資料表時，您可以將它增加至較高的數位。 這 pg_dump 會建立目錄來儲存每個工作的壓縮檔案。

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    例如，套用至物件的
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. 然後，在目標伺服器上還原備份。
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    例如，套用至物件的
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> 本檔中所述的程式也可以用來升級適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器（目前為預覽狀態）。 主要差異在於彈性伺服器目標的連接字串不是 `@dbName` 。  例如，如果使用者名稱是，則 `pg` 連接字串中單一伺服器的使用者名稱將會是 `pg@pg-95` ，而有彈性的伺服器則可以直接使用 `pg` 。

## <a name="next-steps"></a>後續步驟

- 當您對目標資料庫函數感到滿意之後，就可以卸載舊的資料庫伺服器。 
- 如果您想要使用與來源伺服器相同的資料庫端點，則在刪除舊的源資料庫伺服器之後，您可以使用舊的資料庫伺服器名稱來建立讀取複本。 一旦建立穩定狀態，您就可以停止複本，這會將複本伺服器升級為獨立的伺服器。 如 [需詳細資訊，請](./concepts-read-replicas.md) 參閱複寫。
- 請務必在測試環境中測試和驗證這些命令之後，再於在生產環境中使用。
