---
title: 設定資料傳入複寫-適用於 MariaDB 的 Azure 資料庫
description: 本文說明如何在適用於 MariaDB 的 Azure 資料庫中設定資料入複寫。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/11/2020
ms.openlocfilehash: 0b23b01faf1b6ba09f1c55db2ddabd1696e452be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84738102"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>在適用於 MariaDB 的 Azure 資料庫中設定資料入複寫

本文說明如何藉由設定主要和複本伺服器，在適用於 MariaDB 的 Azure 資料庫中設定[資料傳入](concepts-data-in-replication.md)複寫。 本文假設您先前已有適用于 mariadb 伺服器和資料庫的經驗。

若要在適用於 MariaDB 的 Azure 資料庫服務中建立複本，[資料複製](concepts-data-in-replication.md)複寫會同步處理內部部署的主要適用于 mariadb 伺服器、虛擬機器（vm）或雲端資料庫服務中的資料。 資料輸入複寫是建立在以二進位記錄 (binlog) 檔案位置為基礎的 MariaDB 原生複寫之上。 若要深入了解 binlog 複寫，請參閱 [binlog 複寫概觀](https://mariadb.com/kb/en/library/replication-overview/) \(英文\)。

執行本文中的步驟之前，請先參閱複寫資料的[限制和需求](concepts-data-in-replication.md#limitations-and-considerations)。

> [!NOTE]
> 如果您的主伺服器是10.2 版或更新版本，建議您使用[全域交易識別碼](https://mariadb.com/kb/en/library/gtid/)來設定複寫資料。


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>建立適用于 mariadb 伺服器做為複本使用

1. 建立新的適用於 MariaDB 的 Azure 資料庫伺服器（例如，replica.mariadb.database.azure.com）。 伺服器是複寫資料的複本伺服器。

    若要瞭解伺服器建立的相關資訊，請參閱[使用 Azure 入口網站建立適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-portal.md)。

   > [!IMPORTANT]
   > 您必須在一般用途或記憶體優化定價層中建立適用於 MariaDB 的 Azure 資料庫伺服器。

2. 建立相同的使用者帳戶和對應的許可權。
    
    使用者帳戶不會從主伺服器複寫到複本伺服器。 若要為使用者提供複本伺服器的存取權，您必須在新建立的適用於 MariaDB 的 Azure 資料庫伺服器上，手動建立所有帳戶和對應的許可權。

3. 將主伺服器的 IP 位址新增至複本的防火牆規則。 

   使用 [Azure 入口網站](howto-manage-firewall-portal.md)或 [Azure CLI](howto-manage-firewall-cli.md) 更新防火牆規則。

> [!NOTE]
> 偏差-免費通訊
>
> Microsoft 支援多樣化和 inclusionary 的環境。 本文包含對_一詞的_參考。 [適用于無偏差通訊的 Microsoft 樣式指南](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)可辨識此為 exclusionary 單字。 本文中會使用這個字來進行一致性，因為它目前是出現在軟體中的單字。 當軟體更新為移除此單字時，此文章將會更新為對齊。
>

## <a name="configure-the-master-server"></a>設定主要伺服器

下列步驟會準備和設定裝載于內部部署、VM 或雲端資料庫服務中的適用于 mariadb 伺服器，以進行資料複製。 適用于 mariadb 伺服器是資料入複寫中的主要複本。

1. 請先檢查[主伺服器需求](concepts-data-in-replication.md#requirements)，再繼續進行。 

   例如，請確定主伺服器允許埠3306上的輸入和輸出流量，而且主伺服器具有**公用 IP 位址**、DNS 可公開存取，或具有完整功能變數名稱（FQDN）。 
   
   嘗試從另一部電腦上裝載的 MySQL 命令列之類的工具連線，或從 Azure 入口網站中提供的[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) ，來測試主伺服器的連線能力。

2. 開啟二進位記錄。
    
    若要查看是否已在主機上啟用二進位記錄，請輸入下列命令：

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   如果變數傳回 [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) 值 `ON` ，則會在您的伺服器上啟用二進位記錄。

   如果傳回 `log_bin` 值 `OFF` ，請編輯**my.cnf**檔案，以便 `log_bin=ON` 開啟二進位記錄。 重新開機伺服器，讓變更生效。

3. 設定主伺服器設定。

    資料複製複寫需要 `lower_case_table_names` 主要和複本伺服器之間的參數一致。 `lower_case_table_names`在適用於 MariaDB 的 Azure 資料庫中，參數預設會設定為 `1` 。

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. 建立新的複寫角色並設定許可權。

   在使用複寫許可權設定的主伺服器上建立使用者帳戶。 您可以使用 SQL 命令或 MySQL 工作臺來建立帳戶。 如果您打算使用 SSL 進行複寫，則必須在建立使用者帳戶時指定此方式。
   
   若要瞭解如何在主伺服器上新增使用者帳戶，請參閱[適用于 mariadb 檔](https://mariadb.com/kb/en/library/create-user/)。

   藉由使用下列命令，新的複寫角色可以從任何機器存取主伺服器，而不只是裝載主機本身的電腦。 針對此存取權，請在命令中指定**syncuser \@ '% '** 來建立使用者。
   
   若要深入瞭解適用于 mariadb 檔，請參閱[指定帳戶名稱](https://mariadb.com/kb/en/library/create-user/#account-names)。

   **SQL 命令**

   - 使用 SSL 的複寫

       若要針對所有使用者連線要求 SSL，請輸入下列命令以建立使用者：

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - 不使用 SSL 的複寫

       如果所有連接都不需要 SSL，請輸入下列命令以建立使用者：
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   若要在 MySQL 工作臺中建立複寫角色，請在 [**管理**] 窗格中，選取 [**使用者和許可權**]。 然後選取 [**新增帳戶**]。
 
   ![使用者和權限](./media/howto-data-in-replication/users_privileges.png)

   在 [**登入名稱**] 欄位中輸入 username。

   ![同步處理使用者](./media/howto-data-in-replication/syncuser.png)
 
   選取 [系統**管理角色**] 面板，然後在**全域許可權**清單中，選取 [複寫**從屬**]。 選取 **[** 套用] 以建立複寫角色。

   ![複寫從屬](./media/howto-data-in-replication/replicationslave.png)


5. 將主伺服器設為唯讀模式。

   在傾印資料庫之前，必須先將伺服器置於唯讀模式。 在唯讀模式中，主伺服器無法處理任何寫入交易。 為避免業務影響，請在離峰時段排程唯讀視窗。

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. 取得目前的二進位記錄檔名稱和位移。

   若要判斷目前的二進位記錄檔名稱和位移，請執行命令 [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) 。
    
   ```sql
   show master status;
   ```
   結果應該類似下表：
   
   ![主要狀態結果](./media/howto-data-in-replication/masterstatus.png)

   請記下二進位檔案名稱，因為稍後的步驟中將會用到它。
   
7. 取得 GTID 位置（選擇性，使用 GTID 進行複寫所需）。

   執行函式 [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) ，以取得對應 binlog 檔案名和位移的 GTID 位置。
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>傾印並還原主伺服器

1. 從主伺服器傾印所有資料庫。

   使用 mysqldump 從主伺服器傾印所有資料庫。 不需要傾印 MySQL 程式庫和測試程式庫。

    如需詳細資訊，請參閱傾印[和還原](howto-migrate-dump-restore.md)。

2. 將主伺服器設定為讀取/寫入模式。

   傾印資料庫之後，請將主要適用于 mariadb 伺服器變更回讀取/寫入模式。

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. 將傾印檔案還原到新的伺服器。

   將傾印檔案還原至在適用於 MariaDB 的 Azure 資料庫服務中建立的伺服器。 如需如何將傾印檔案還原至適用于 mariadb 伺服器的詳細說明，請參閱傾印[& 還原](howto-migrate-dump-restore.md)。

   如果傾印檔案很大，請將它上傳至與複本伺服器位於相同區域的 Azure VM。 將其從 VM 還原至適用於 MariaDB 的 Azure 資料庫伺服器。

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>連結主要和複本伺服器以啟動資料複製

1. 設定主伺服器。

   所有「複寫中的資料」功能都可由已儲存的程序執行完成。 您可在[複寫中的資料已儲存的程序](reference-data-in-stored-procedures.md)中找到所有程序。 預存程式可以在 MySQL shell 或 MySQL 工作臺中執行。

   若要連結兩部伺服器並啟動複寫，請在 Azure DB for 適用于 mariadb 服務中登入目標複本伺服器。 接下來，在 `mysql.az_replication_change_master` `mysql.az_replication_change_master_with_gtid` 適用于適用于 mariadb 的 Azure DB 伺服器上使用或預存程式，將外部實例設為主伺服器。

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   或
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host：主要伺服器的主機名稱
   - master_user：主要伺服器的使用者名稱
   - master_password：主要伺服器的密碼
   - master_log_file：執行 `show master status` 產生的二進位記錄檔的名稱
   - master_log_pos：執行 `show master status` 產生的二進位記錄檔的位置
   - master_gtid_pos：從執行中 GTID 位置`select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca： CA 憑證的內容。 如果您不是使用 SSL，請傳入空字串。 *
    
    
    * 建議以變數的形式傳入 master_ssl_ca 參數。 如需詳細資訊，請參閱下列範例。

   **範例**

   - 使用 SSL 的複寫

       `@cert`執行下列命令來建立變數：

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       使用 SSL 的複寫是在裝載于網域 companya.com 的主伺服器與適用於 MariaDB 的 Azure 資料庫中託管的複本伺服器之間設定。 此已儲存的程序可在複本伺服器上執行。
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - 不使用 SSL 的複寫

       不使用 SSL 的複寫會在 companya.com 網域的主伺服器和裝載于適用於 MariaDB 的 Azure 資料庫的複本伺服器之間進行設定。 此已儲存的程序可在複本伺服器上執行。

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. 開始複寫。

   呼叫 `mysql.az_replication_start` 預存程式以開始複寫。

   ```sql
   CALL mysql.az_replication_start;
   ```

3. 檢查複寫狀態。

   呼叫 [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) 複本伺服器上的命令，以查看複寫狀態。
    
   ```sql
   show slave status;
   ```

   如果 `Slave_IO_Running` 和處於 `Slave_SQL_Running` 狀態 `yes` ，且的值 `Seconds_Behind_Master` 為，則複寫 `0` 會正常運作。 `Seconds_Behind_Master` 可指定複本的延遲時間。 如果值不是 `0` ，則複本會處理更新。

4. 更新對應的伺服器變數，以更安全地進行資料輸入複寫（只有在沒有 GTID 的情況下才需要）。
    
    由於適用于 mariadb 中的原生複寫限制，您必須 [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) 在複寫上設定和變數， [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) 而不需要 GTID 案例。

    檢查從屬伺服器的 `sync_master_info` 和 `sync_relay_log_info` 變數，以確保資料入複寫穩定，並將變數設定為 `1` 。
    
## <a name="other-stored-procedures"></a>其他已儲存的程序

### <a name="stop-replication"></a>停止複寫

若要停止主要和複本伺服器之間的複寫，請使用下列已儲存的程序：

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>移除複寫關聯性

若要移除主要和複本伺服器之間的關聯性，請使用下列預存程式：

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>略過複寫錯誤

若要略過複寫錯誤並允許複寫，請使用下列預存程式：
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>後續步驟
深入了解「適用於 MariaDB 的 Azure 資料庫」的[資料輸入複寫](concepts-data-in-replication.md)。
