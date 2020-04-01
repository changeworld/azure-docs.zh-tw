---
title: 設定資料內複製 - MariaDB 的 Azure 資料庫
description: 本文介紹如何在 Azure 資料庫中為 MariaDB 設置數據內複製。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/30/2020
ms.openlocfilehash: 332feffead74174ba0b9b278d8de1c5957d5b9e6
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422462"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>在 Azure 資料庫中為 MariaDB 設定資料內複製

本文介紹如何通過配置主伺服器和副本伺服器在 Azure 資料庫中為 MariaDB 設置數據內複製。 本文假定您以前對 MariaDB 伺服器和資料庫有一些經驗。

要在 MariaDB 服務的 Azure 資料庫中建立副本,資料內複製將同步來自本地主 MariaDB 伺服器、虛擬機器 (VM) 或雲端資料庫服務中的數據。

在執行本文中的步驟之前,請檢視資料內複製[的限制和要求](concepts-data-in-replication.md#limitations-and-considerations)。

> [!NOTE]
> 如果主伺服器版本為 10.2 或較新版本,我們建議您使用[全域事務 ID](https://mariadb.com/kb/en/library/gtid/)設置「數據內複製」。


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>建立 MariaDB 伺服器以用作複本

1. 為 MariaDB 伺服器建立新的 Azure 資料庫(例如,replica.mariadb.database.azure.com)。 伺服器是數據內複製中的副本伺服器。

    要瞭解伺服器建立,請參閱[使用 Azure 門戶為 MariaDB 伺服器建立 Azure 資料庫](quickstart-create-mariadb-server-database-using-azure-portal.md)。

   > [!IMPORTANT]
   > 您必須在通用或記憶體優化定價層中為 MariaDB 伺服器創建 Azure 資料庫。

2. 創建相同的使用者帳戶和相應的許可權。
    
    使用者帳戶不會從主伺服器複製到副本伺服器。 要提供使用者對副本伺服器的訪問許可權,您必須在新創建的 MariaDB 伺服器上手動創建所有帳戶和相應的許可權。

3. 將主伺服器的 IP 位址添加到複本的防火牆規則中。 

   使用 [Azure 入口網站](howto-manage-firewall-portal.md)或 [Azure CLI](howto-manage-firewall-cli.md) 更新防火牆規則。

## <a name="configure-the-master-server"></a>設定主要伺服器

以下步驟準備和配置託管在本地、VM 或用於資料內複製的雲端資料庫服務中的 MariaDB 伺服器。 MariaDB 伺服器是數據內複製的主伺服器。

1. 在繼續操作之前,請檢視[主伺服器要求](concepts-data-in-replication.md#requirements)。 

   例如,確保主伺服器允許埠 3306 上的入站和出站流量,並且主伺服器具有公共**IP 位址**、DNS 是可公開訪問的,或者具有完全限定的功能變數名稱 (FQDN)。 
   
   通過嘗試從工具(如在另一台電腦上託管的 MySQL 命令行或 Azure 門戶中可用的[Azure 雲外殼](https://docs.microsoft.com/azure/cloud-shell/overview))進行連接,測試與主伺服器的連接。

2. 打開二進位紀錄記錄。
    
    要檢視在主伺服器上是否啟用了二進位紀錄記錄,請輸入以下命令:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   如果變數[`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin)返回值`ON`,則在伺服器上啟用二進位日誌記錄。

   如果`log_bin`返回該值`OFF`,請編輯**my.cnf**檔`log_bin=ON`,以便打開二進位紀錄記錄。 重新啟動伺服器以使更改生效。

3. 配置主伺服器設置。

    數據輸入複製要求參數`lower_case_table_names`在主伺服器和副本伺服器之間保持一致。 `1`默認情況下,該`lower_case_table_names`參數在 MariaDB 的 Azure 資料庫中設置為。

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. 建立新的複製角色並設置許可權。

   在主伺服器上創建配置具有複製許可權的使用者帳戶。 您可以使用 SQL 命令或 MySQL 工作台創建帳戶。 如果計劃使用 SSL 進行複製,則必須在創建使用者帳戶時指定此項。
   
   要瞭解如何在主伺服器上添加使用者帳戶,請參閱[MariaDB 文件](https://mariadb.com/kb/en/library/create-user/)。

   通過使用以下命令,新的複製角色可以從任何計算機(而不僅僅是承載主機本身的計算機)訪問主計算機。 此存取權限,請在指令中指定**同步\@使用者 「%」以**建立使用者。
   
   要瞭解有關 MariaDB 文件的詳細資訊,請參閱[指定帳號名稱](https://mariadb.com/kb/en/library/create-user/#account-names)。

   **SQL (命令)**

   - 使用 SSL 的複寫

       要要求所有使用者連線使用 SSL,請輸入以下指令以建立使用者:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - 不使用 SSL 的複寫

       如果並非所有連線都需要 SSL,請輸入以下指令以建立使用者:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   要在 MySQL 工作台中創建複製角色,請在 **「管理」** 窗格中選擇 **「使用者和許可權**」。 然後選擇 **「添加帳戶**」。
 
   ![使用者和權限](./media/howto-data-in-replication/users_privileges.png)

   在 **「登入名」** 欄位中輸入使用者名稱。

   ![同步處理使用者](./media/howto-data-in-replication/syncuser.png)
 
   選擇 **「管理角色**」面板,然後在**全域權限**列表中選擇 **「複製從屬**」 。 選擇 **「應用」** 以建立複製角色。

   ![複寫從屬](./media/howto-data-in-replication/replicationslave.png)


5. 將主伺服器設置為唯讀模式。

   在轉儲資料庫之前,必須將伺服器置於唯讀模式。 在唯讀模式下,主控形狀無法處理任何寫入事務。 為避免業務影響,請安排非高峰期間的唯讀視窗。

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. 獲取當前二進位紀錄檔名稱和偏移量。

   要確定目前的紀錄檔名稱和偏移,請執行[`show master status`](https://mariadb.com/kb/en/library/show-master-status/)指令 。
    
   ```sql
   show master status;
   ```
   結果應類似於下表:
   
   ![主要狀態結果](./media/howto-data-in-replication/masterstatus.png)

   請注意二進位檔名,因為它將在後續步驟中使用。
   
7. 取得 GTID 位置(選擇可選,使用 GTID 進行複製所需的)。

   執行該函數[`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/)以獲取相應 binlog 檔名和偏移的 GTID 位置。
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>傾印和還原伺服器

1. 從主伺服器轉儲所有資料庫。

   使用 mysqldump 從主伺服器轉儲所有資料庫。 無需轉儲 MySQL 庫和測試庫。

    有關詳細資訊,請參閱[轉儲和還原](howto-migrate-dump-restore.md)。

2. 將主伺服器設置為讀/寫模式。

   轉印資料庫後,將主 MariaDB 伺服器更改回讀/寫模式。

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. 將轉儲檔還原到新伺服器。

   將傾印檔案還原至在適用於 MariaDB 的 Azure 資料庫服務中建立的伺服器。 有關如何將轉儲檔還原到 MariaDB 伺服器[,請參閱轉儲&還原](howto-migrate-dump-restore.md)。

   如果轉儲檔很大,請將其上傳到與副本伺服器位於同一區域中的 Azure 中的 VM。 將其從 VM 還原到 MariaDB 伺服器的 Azure 資料庫。

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>連結伺服器與複本伺服器以啟動資料內複製

1. 設置主伺服器。

   所有「複寫中的資料」功能都可由已儲存的程序執行完成。 您可在[複寫中的資料已儲存的程序](reference-data-in-stored-procedures.md)中找到所有程序。 存儲過程可以在 MySQL 外殼或 MySQL 工作台中運行。

   要連結兩台伺服器並開始複製,請登錄到 MariaDB 服務的 Azure DB 中的目標副本伺服器。 接下來,使用 MariaDB`mysql.az_replication_change_master`伺服器的 Azure DB`mysql.az_replication_change_master_with_gtid`上或儲存過程將外部實例設置為主伺服器。

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
   - master_gtid_pos:從執行到 GTID 位置`select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca:CA 證書的上下文。 如果您不使用 SSL,則傳遞一個空字串。
    
    
    *我們建議將master_ssl_ca參數作為變數傳遞。 如需詳細資訊，請參閱下列範例。

   **範例**

   - 使用 SSL 的複寫

       以執行以下`@cert`指令建立變數:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       使用 SSL 進行複製是在網域 companya.com 託管的主伺服器和在 MariaDB Azure 資料庫中託管的副本伺服器之間設置的。 此已儲存的程序可在複本伺服器上執行。
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - 不使用 SSL 的複寫

       在沒有 SSL 的複製在網域 companya.com 託管的主伺服器和託管在 MariaDB Azure 資料庫中的副本伺服器之間設置。 此已儲存的程序可在複本伺服器上執行。

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. 開始複製。

   調用`mysql.az_replication_start`儲存過程以開始複製。

   ```sql
   CALL mysql.az_replication_start;
   ```

3. 檢查複製狀態。

   呼叫[`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/)副本伺服器上的命令以查看複製狀態。
    
   ```sql
   show slave status;
   ```

   如果`Slave_IO_Running``Slave_SQL_Running`和`yes`處於 狀態,`Seconds_Behind_Master`並且`0`值 為 ,複製工作。 `Seconds_Behind_Master` 可指定複本的延遲時間。 如果值不是`0`,則副本正在處理更新。

4. 更新相應的伺服器變數,使資料中的複製更安全(僅需要在沒有 GTID 的情況下進行複製)。
    
    由於 MariaDB 中的本機複製限制,因此必須[`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info)[`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info)在沒有 GTID 方案的情況下設置複製和變數。

    檢查從伺服器和`sync_master_info``sync_relay_log_info`變數以確保資料簽入複製穩定,並將變數設定為`1`。
    
## <a name="other-stored-procedures"></a>其他已儲存的程序

### <a name="stop-replication"></a>停止複寫

若要停止主要和複本伺服器之間的複寫，請使用下列已儲存的程序：

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>移除複製關係

要刪除主伺服器與副本伺服器之間的關係,請使用以下儲存過程:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>跳過複製錯誤

要跳過複製錯誤並允許複製,請使用以下儲存過程:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>後續步驟
深入了解「適用於 MariaDB 的 Azure 資料庫」的[資料輸入複寫](concepts-data-in-replication.md)。
