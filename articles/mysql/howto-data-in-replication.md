---
title: 設定資料-in replication-適用於 MySQL 的 Azure 資料庫
description: 本文將說明如何為適用於 MySQL 的 Azure 資料庫設定複寫中的資料。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: 58df34ae6a6ff3304304da192b429ac83c1b55c3
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92544030"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>如何為適用於 MySQL 的 Azure 資料庫設定複寫中的資料

本文描述如何藉由設定來源和複本伺服器，在適用於 MySQL 的 Azure 資料庫中設定 [資料輸入複寫](concepts-data-in-replication.md) 。 本文假設您先前已有 MySQL 伺服器和資料庫的經驗。

> [!NOTE]
> 無偏差通訊
>
> Microsoft 支援多元和包容性的環境。 本文包含 _slave_ 單字的參考。 Microsoft [無偏差通訊的樣式指南](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)會將這個單字辨識為排他性的單字。 本文中會使用這個單字來保持一致性，因為這個單字是目前出現在軟體中的單字。 當軟體更新為移除此單字時，此文章將會更新以保持一致。
>

若要在適用於 MySQL 的 Azure 資料庫服務中建立複本， [資料輸入複寫](concepts-data-in-replication.md)  在虛擬機器 (vm) 或雲端資料庫服務中，同步處理來源 MySQL 伺服器內部部署的資料。 資料帶入複寫是建立在以二進位記錄 (binlog) 檔案位置為基礎的 MySQL 原生複寫之上。 若要深入了解 binlog 複寫，請參閱 [MySQL binlog 複寫概觀](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) \(英文\)。

在執行本文中的步驟之前，請先參閱資料入複寫的 [限制和需求](concepts-data-in-replication.md#limitations-and-considerations) 。

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>建立 MySQL 伺服器做為複本

1. 新建適用於 MySQL 伺服器的 Azure 資料庫

   建立新的 MySQL 伺服器 (例如 "replica.mysql.database.azure.com")。 若要了解如何建立伺服器，請參閱[使用 Azure 入口網站建立適用於 MySQL 伺服器的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-portal.md)。 此伺服器是複寫中資料的「複本」伺服器。

   > [!IMPORTANT]
   > 適用於 MySQL 的 Azure 資料庫伺服器必須建立於一般用途或記憶體最佳化定價層。
   > 

1. 建立相同的使用者帳戶和對應權限

   使用者帳戶不會從來源伺服器複寫到複本伺服器。 如果您預計提供複本伺服器存取權給使用者，則必須在此新建的適用於 MySQL 伺服器的 Azure資料庫中，手動建立所有帳戶及對應權限。

1. 將來源伺服器的 IP 位址新增至複本的防火牆規則。 

   使用 [Azure 入口網站](howto-manage-firewall-using-portal.md)或 [Azure CLI](howto-manage-firewall-using-cli.md) 更新防火牆規則。

## <a name="configure-the-source-server"></a>設定來源伺服器
下列步驟會針對裝載在內部部署的 MySQL 伺服器、虛擬機器中的 MySQL 伺服器或由其他雲端提供者所代管的資料庫服務，準備及設定資料帶入複寫。 此伺服器是「資料輸入複寫」中的「主要」伺服器。


1. 繼續之前，請先檢查 [主伺服器需求](concepts-data-in-replication.md#requirements) 。 

2. 請確定來源伺服器允許埠3306上的輸入和輸出流量，且來源伺服器具有 **公用 IP 位址** 、可公開存取 DNS，或具有 (FQDN) 的完整功能變數名稱。 
   
   藉由嘗試從另一部電腦上裝載的 MySQL 命令列，或從 Azure 入口網站提供的 [Azure Cloud Shell](../cloud-shell/overview.md) ，來測試來源伺服器的連線能力。

   如果您的組織具有嚴格的安全性原則，且不允許來源伺服器上的所有 IP 位址啟用從 Azure 到來源伺服器的通訊，您可能會使用下列命令來判斷 MySQL 伺服器的 IP 位址。

   1. 使用 MySQL 命令列之類的工具來登入您的適用於 MySQL 的 Azure 資料庫。
   2. 執行下列查詢。
      ```bash
      mysql> SELECT @@global.redirect_server_host;
      ```
      以下是一些範例輸出：
      ```bash 
      +-----------------------------------------------------------+
      | @@global.redirect_server_host                             |
      +-----------------------------------------------------------+
      | e299ae56f000.tr1830.westus1-a.worker.database.windows.net |
       +-----------------------------------------------------------+
      ```
   3. 從 MySQL 命令列結束。
   4. 在 ping 公用程式中執行下列命令，以取得 IP 位址。
      ```bash
      ping <output of step 2b>
      ``` 
      例如： 
      ```bash      
      C:\Users\testuser> ping e299ae56f000.tr1830.westus1-a.worker.database.windows.net
      Pinging tr1830.westus1-a.worker.database.windows.net (**11.11.111.111**) 56(84) bytes of data.
      ```

   5. 設定來源伺服器的防火牆規則，以在埠3306上包含上一個步驟的輸出 IP 位址。

   > [!NOTE]
   > 此 IP 位址可能會因為維護/部署作業而變更。 這種連線方法只適用于無法讓3306埠上的所有 IP 位址都能承受的客戶。
   
1. 開啟二進位記錄

   藉由執行下列命令來查看來源上是否已啟用二進位記錄： 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   如果傳回的變數 [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) 值為 "ON"，則會在您的伺服器上啟用二進位記錄。 

   如果 `log_bin` 傳回值為 "OFF" 的，請藉由編輯 my.cnf 檔案來開啟二進位記錄， `log_bin=ON` 然後重新開機伺服器，變更才會生效。

1. 來源伺服器設定

   資料輸入複寫需要 `lower_case_table_names` 在來源與複本伺服器之間保持一致的參數。 此參數在適用於 MySQL 的 Azure 資料庫中預設為 1。 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

1. 建立新的複寫角色並設定權限

   在使用複寫許可權設定的來源伺服器上建立使用者帳戶。 此作業可透過 SQL 命令或 MySQL Workbench 等工具完成。 考慮是打算否使用 SSL 進行複寫，因為此設定必須在建立使用者時指定。 請參閱 MySQL 檔，瞭解如何在來源伺服器上 [新增使用者帳戶](https://dev.mysql.com/doc/refman/5.7/en/user-names.html) 。 

   在下列命令中，建立的新複寫角色可以從任何電腦（而不只是裝載來源本身的電腦）存取來源。 在建立使用者命令中指定 "syncuser@'%'"，即可執行此作業。 請參閱 MySQL 文件，進一步了解[如何指定帳戶名稱](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)。

   **SQL 命令**

   *使用 SSL 的複寫*

   若要讓所有使用者連線都使用 SSL，請使用以下命令建立使用者： 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *不使用 SSL 的複寫*

   若所有連線皆不需要使用 SSL，請使用以下命令建立使用者：

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   若要在 MySQL Workbench 中建立複寫角色，請從 [管理]  面板開啟 [使用者和權限]  面板。 接著，按一下 [新增帳戶]  。 
 
   :::image type="content" source="./media/howto-data-in-replication/users_privileges.png" alt-text="使用者和權限":::

   在 [登入名稱]  欄位中輸入使用者名稱。 

   :::image type="content" source="./media/howto-data-in-replication/syncuser.png" alt-text="使用者和權限":::
 
   按一下 [管理角色]  面板，然後從 [全域權限]  清單選取 [複寫從屬]  。 然後按一下 [套用]  以建立複寫角色。

   :::image type="content" source="./media/howto-data-in-replication/replicationslave.png" alt-text="使用者和權限":::

1. 將來源伺服器設定為唯讀模式

   開始傾印資料庫之前，伺服器必須處於唯讀模式。 在唯讀模式中，來源將無法處理任何寫入交易。 必要時可評估對業務的影響，並為巔峰和離峰時間排程唯讀時段。

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

1. 取得二進位記錄檔的檔案名稱和位移

   執行 [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) 命令以判斷目前的二進位記錄檔名稱和位移。
    
   ```sql
    show master status;
   ```
   結果應類似以下所示。 請務必記下二進位檔案的名稱，後續步驟會用到此名稱。

   :::image type="content" source="./media/howto-data-in-replication/masterstatus.png" alt-text="使用者和權限":::
 
## <a name="dump-and-restore-source-server"></a>傾印和還原來源伺服器

1. 判斷您想要複寫至適用於 MySQL 的 Azure 資料庫的資料庫和資料表，並從來源伺服器執行傾印。
 
    您可使用 mysqldump 從主要伺服器傾印資料庫。 如需詳細資料，請參閱[傾印和還原](concepts-migrate-dump-restore.md)。 您不需要傾印 MySQL 程式庫和測試程式庫。

1. 將來源伺服器設定為讀取/寫入模式

   傾印資料庫後，請將來源 MySQL 伺服器重新變更為讀取/寫入模式。

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

1. 將傾印檔案還原至新的伺服器

   將傾印檔案還原至在適用於 MySQL 的 Azure 資料庫服務中建立的伺服器。 請參閱[傾印和還原](concepts-migrate-dump-restore.md)，了解如何將傾印檔案還原至 MySQL 伺服器。 如果傾印檔案太大，請先在與複本伺服器所在區域相同的區域中，將檔案上傳至 Azure 內的虛擬機器。 從虛擬機器還原至適用於 MySQL 伺服器的 Azure 資料庫。

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>連結來源與複本伺服器以開始資料輸入複寫

1. 設定來源伺服器

   所有「複寫中的資料」功能都可由已儲存的程序執行完成。 您可在[複寫中的資料已儲存的程序](./reference-stored-procedures.md)中找到所有程序。 已儲存的程序可在 MySQL Shell 或 MySQL Workbench 中執行。 

   若要連結兩個伺服器並啟動複寫，請在適用于 MySQL 的 Azure DB 服務中登入目標複本伺服器，然後將外部實例設定為來源伺服器。 在適用於 MySQL 伺服器的 Azure DB 中使用 `mysql.az_replication_change_master` 已儲存的程序，即可執行此作業。

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host：來源伺服器的主機名稱
   - master_user：來源伺服器的使用者名稱
   - master_password：來源伺服器的密碼
   - master_log_file：執行 `show master status` 產生的二進位記錄檔的名稱
   - master_log_pos：執行 `show master status` 產生的二進位記錄檔的位置
   - master_ssl_ca： CA 憑證的內容。 如果不使用 SSL，請傳入空字串。
       - 建議將此參數以變數形式傳遞。 請參閱下列範例，以取得詳細資訊。

   > [!NOTE]
   > 如果來源伺服器是裝載于 Azure VM，請將「允許存取 Azure 服務」設定為「開啟」，讓來源和複本伺服器能夠彼此通訊。 於 **連線安全性** 選項可以變更此項設定。 請參閱[使用入口網站管理防火牆規則](howto-manage-firewall-using-portal.md)取得更多資訊。
      
   **範例**
   
   *使用 SSL 的複寫*
   
   執行下列 MySQL 命令可建立變數 `@cert`： 
   
      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```
   
   使用 SSL 的複寫是在裝載于網域 "companya.com" 的來源伺服器和適用於 MySQL 的 Azure 資料庫中裝載的複本伺服器之間設定。 此已儲存的程序可在複本伺服器上執行。 
   
      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```
   *不使用 SSL 的複寫*
   
   在託管于網域 "companya.com" 的來源伺服器和適用於 MySQL 的 Azure 資料庫中裝載的複本伺服器之間，會設定不含 SSL 的複寫。 此已儲存的程序可在複本伺服器上執行。
   
      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```

1. 篩選 
 
   如果您想要略過從主目錄複寫部分資料表，請更新 `replicate_wild_ignore_table` 複本伺服器上的伺服器參數。 您可以使用以逗號分隔的清單來提供多個資料表模式。

   請檢閱 [MySQL 文件](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) \(英文\)，以深入了解此參數。 
    
   若要更新參數，您可以使用 [Azure 入口網站](howto-server-parameters.md) 或 [Azure CLI](howto-configure-server-parameters-using-cli.md)。

1. 啟動複寫

   呼叫 `mysql.az_replication_start` 已儲存的程序以起始複寫。

   ```sql
   CALL mysql.az_replication_start;
   ```

1. 檢查複寫狀態

   在 [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) 複本伺服器上呼叫命令以查看複寫狀態。
    
   ```sql
   show slave status;
   ```

   如果的狀態為「是」， `Slave_IO_Running` `Slave_SQL_Running` 而的值 `Seconds_Behind_Master` 為 "0"，則複寫運作良好。 `Seconds_Behind_Master` 可指定複本的延遲時間。 如果值不是 “0”，代表複本正在處理更新。 

## <a name="other-stored-procedures"></a>其他已儲存的程序

### <a name="stop-replication"></a>停止複寫

若要停止來源與複本伺服器之間的複寫，請使用下列預存程式：

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>移除複寫關聯

若要移除來源與複本伺服器之間的關聯性，請使用下列預存程式：

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>略過複寫錯誤

若要略過複寫錯誤並允許複寫繼續，請使用下列已儲存的程序：
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>下一步
- 深入了解「適用於 MySQL 的 Azure 資料庫」的[資料帶入複寫](concepts-data-in-replication.md)。