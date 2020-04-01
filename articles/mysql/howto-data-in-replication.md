---
title: 設定資料內複製 ─MySQL 的 Azure 資料庫
description: 本文將說明如何為適用於 MySQL 的 Azure 資料庫設定複寫中的資料。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 18c1d8b42dc73951901ec4ae9b79715ddbd47617
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474035"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>如何為適用於 MySQL 的 Azure 資料庫設定複寫中的資料

本文介紹如何通過配置主伺服器和副本伺服器在 MySQL 的 Azure 資料庫中設置數據內複製。 本文假定您以前對 MySQL 伺服器和資料庫有一些經驗。

要在 MySQL 服務的 Azure 資料庫中創建副本,資料內複製將同步來自本地主 MySQL 伺服器、虛擬機器 (VM) 或雲端資料庫服務中的數據。

在執行本文中的步驟之前,請檢視資料內複製[的限制和要求](concepts-data-in-replication.md#limitations-and-considerations)。

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>建立 MySQL 伺服器做為複本

1. 新建適用於 MySQL 伺服器的 Azure 資料庫

   建立新的 MySQL 伺服器 (例如 "replica.mysql.database.azure.com")。 若要了解如何建立伺服器，請參閱[使用 Azure 入口網站建立適用於 MySQL 伺服器的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-portal.md)。 此伺服器是複寫中資料的「複本」伺服器。

   > [!IMPORTANT]
   > 適用於 MySQL 的 Azure 資料庫伺服器必須建立於一般用途或記憶體最佳化定價層。
   > 

2. 建立相同的使用者帳戶和對應權限

   使用者帳戶不會從主要伺服器複寫到複本伺服器。 如果您預計提供複本伺服器存取權給使用者，則必須在此新建的適用於 MySQL 伺服器的 Azure資料庫中，手動建立所有帳戶及對應權限。

3. 將主伺服器的 IP 位址添加到複本的防火牆規則中。 

   使用 [Azure 入口網站](howto-manage-firewall-using-portal.md)或 [Azure CLI](howto-manage-firewall-using-cli.md) 更新防火牆規則。

## <a name="configure-the-master-server"></a>設定主要伺服器
下列步驟會針對裝載在內部部署的 MySQL 伺服器、虛擬機器中的 MySQL 伺服器或由其他雲端提供者所代管的資料庫服務，準備及設定資料帶入複寫。 此伺服器是「資料輸入複寫」中的「主要」伺服器。


1. 在繼續操作之前,請檢視[主伺服器要求](concepts-data-in-replication.md#requirements)。 

   例如,確保主伺服器允許埠 3306 上的入站和出站流量,並且主伺服器具有公共**IP 位址**、DNS 是可公開訪問的,或者具有完全限定的功能變數名稱 (FQDN)。 
   
   通過嘗試從工具(如在另一台電腦上託管的 MySQL 命令行或 Azure 門戶中可用的[Azure 雲外殼](https://docs.microsoft.com/azure/cloud-shell/overview))進行連接,測試與主伺服器的連接。

2. 開啟二進位記錄

   執行下列命令，以檢查主要伺服器是否已啟用二進位記錄： 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   如果變數[`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin)返回的值"ON",則在伺服器上啟用二進位日誌記錄。 

   如果`log_bin`返回的值"OFF",請通過編輯 my.cnf 檔打開二進位日誌`log_bin=ON`記錄, 以便重新啟動伺服器,使更改生效。

3. 主要伺服器設定

   「資料輸入複寫」要求主要伺服器和複本伺服器之間的參數 `lower_case_table_names` 一致。 此參數在適用於 MySQL 的 Azure 資料庫中預設為 1。 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. 建立新的複寫角色並設定權限

   在使用複寫權限設定的主要伺服器上建立使用者帳戶。 此作業可透過 SQL 命令或 MySQL Workbench 等工具完成。 考慮是打算否使用 SSL 進行複寫，因為此設定必須在建立使用者時指定。 請參閱 MySQL 文件，了解如何在主要伺服器中[新增使用者帳戶](https://dev.mysql.com/doc/refman/5.7/en/user-names.html)。 

   在以下命令中，新建的複寫角色除了從裝載主要伺服器的機器存取主要伺服器，還可從任何機器存取主要伺服器。 在建立使用者命令中指定 "syncuser@'%'"，即可執行此作業。 請參閱 MySQL 文件，進一步了解[如何指定帳戶名稱](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)。

   **SQL 指令**

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

   若要在 MySQL Workbench 中建立複寫角色，請從 [管理]**** 面板開啟 [使用者和權限]**** 面板。 接著，按一下 [新增帳戶]****。 
 
   ![使用者和權限](./media/howto-data-in-replication/users_privileges.png)

   在 [登入名稱]**** 欄位中輸入使用者名稱。 

   ![同步處理使用者](./media/howto-data-in-replication/syncuser.png)
 
   按一下 [管理角色]**** 面板，然後從 [全域權限]**** 清單選取 [複寫從屬]****。 然後按一下 [套用]**** 以建立複寫角色。

   ![複寫從屬](./media/howto-data-in-replication/replicationslave.png)


5. 將主要伺服器設為唯讀模式

   開始傾印資料庫之前，伺服器必須處於唯讀模式。 處於唯讀模式時，主要伺服器無法處理任何寫入交易。 必要時可評估對業務的影響，並為巔峰和離峰時間排程唯讀時段。

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. 取得二進位記錄檔的檔案名稱和位移

   運行該[`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html)命令以確定當前二進位紀錄檔名稱和偏移量。
    
   ```sql
   show master status;
   ```
   結果應類似以下所示。 請務必記下二進位檔案的名稱，後續步驟會用到此名稱。

   ![主要狀態結果](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>傾印並還原主要伺服器

1. 從主要伺服器傾印所有資料庫

   您可使用 mysqldump 從主要伺服器傾印資料庫。 如需詳細資料，請參閱[傾印和還原](concepts-migrate-dump-restore.md)。 您不需要傾印 MySQL 程式庫和測試程式庫。

2. 將主要伺服器設為讀取/寫入模式

   傾印資料庫後，請將主要的 MySQL 伺服器重新變更為讀取/寫入模式。

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. 將傾印檔案還原至新的伺服器

   將傾印檔案還原至在適用於 MySQL 的 Azure 資料庫服務中建立的伺服器。 請參閱[傾印和還原](concepts-migrate-dump-restore.md)，了解如何將傾印檔案還原至 MySQL 伺服器。 如果傾印檔案太大，請先在與複本伺服器所在區域相同的區域中，將檔案上傳至 Azure 內的虛擬機器。 從虛擬機器還原至適用於 MySQL 伺服器的 Azure 資料庫。

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>連結主要和複本伺服器以啟動資料輸入複寫

1. 設定主要伺服器

   所有「複寫中的資料」功能都可由已儲存的程序執行完成。 您可在[複寫中的資料已儲存的程序](reference-data-in-stored-procedures.md)中找到所有程序。 已儲存的程序可在 MySQL Shell 或 MySQL Workbench 中執行。 

   若要連結兩個伺服器並啟動複寫，請在適用於 MySQL 的 Azure DB 服務中登入目標複本伺服器，然後將外部執行個體設為主要伺服器。 在適用於 MySQL 伺服器的 Azure DB 中使用 `mysql.az_replication_change_master` 已儲存的程序，即可執行此作業。

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host：主要伺服器的主機名稱
   - master_user：主要伺服器的使用者名稱
   - master_password：主要伺服器的密碼
   - master_log_file：執行 `show master status` 產生的二進位記錄檔的名稱
   - master_log_pos：執行 `show master status` 產生的二進位記錄檔的位置
   - master_ssl_ca:CA 證書的上下文。 如果不使用 SSL，請傳入空字串。
       - 建議將此參數以變數形式傳遞。 請參閱下列範例，以取得詳細資訊。

> [!NOTE]
> 若主要伺服器裝載於 Azure 虛擬機器，將「允許存取 Azure 服務」設定為「開啟」，讓主要伺服器與複本伺服器能彼此溝通。 於**連線安全性**選項可以變更此項設定。 請參閱[使用入口網站管理防火牆規則](howto-manage-firewall-using-portal.md)取得更多資訊。

   **範例**

   *使用 SSL 的複寫*

   執行下列 MySQL 命令可建立變數 `@cert`： 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   使用 SSL 進行複製是在託管在域「companya.com」的主伺服器和 MySQL Azure 資料庫中託管的副本伺服器之間設置的。 此已儲存的程序可在複本伺服器上執行。 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *不使用 SSL 的複寫*

   在沒有 SSL 的複製在託管在域"companya.com"的主伺服器和 MySQL Azure 資料庫中託管的副本伺服器之間設置。 此已儲存的程序可在複本伺服器上執行。

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. 啟動複寫

   呼叫 `mysql.az_replication_start` 已儲存的程序以起始複寫。

   ```sql
   CALL mysql.az_replication_start;
   ```

1. 檢查複寫狀態

   呼叫[`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html)副本伺服器上的命令以查看複製狀態。
    
   ```sql
   show slave status;
   ```

   如果`Slave_IO_Running``Slave_SQL_Running`和 的狀態為"是",值`Seconds_Behind_Master`為"0",則複製工作良好。 `Seconds_Behind_Master` 可指定複本的延遲時間。 如果值不是 “0”，代表複本正在處理更新。 

## <a name="other-stored-procedures"></a>其他已儲存的程序

### <a name="stop-replication"></a>停止複寫

若要停止主要和複本伺服器之間的複寫，請使用下列已儲存的程序：

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>移除複寫關聯

若要移除主要和複本伺服器之間的關聯，請使用下列已儲存的程序：

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>略過複寫錯誤

若要略過複寫錯誤並允許複寫繼續，請使用下列已儲存的程序：
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>後續步驟
- 深入了解「適用於 MySQL 的 Azure 資料庫」的[資料帶入複寫](concepts-data-in-replication.md)。 
