---
title: 使用傾印和還原移轉 - 適用於 MySQL 的 Azure 資料庫
description: 本文將說明兩個常見方法，讓您可在適用於 MySQL 的 Azure 資料庫中用來備份和還原資料庫，使用如 mysqldump、MySQL Workbench 和 PHPMyAdmin 的工具。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: 158dd5e1f69340e233a0c2392d3f19fd5cf562ea
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83845541"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>使用傾印和還原來將 MySQL 資料庫移轉至適用於 MySQL 的 Azure 資料庫
本文將說明兩個常見方法，讓您可在適用於 MySQL 的 Azure 資料庫中用來備份和還原資料庫
- 從命令列傾印和還原 (使用 mysqldump) 
- 使用 PHPMyAdmin 傾印和還原 

## <a name="before-you-begin"></a>開始之前
若要逐步執行本作法指南，您需要具備：
- [建立適用於 MySQL 的 Azure 資料庫伺服器 - Azure 入口網站](quickstart-create-mysql-server-database-using-azure-portal.md)
- 已安裝於電腦上的 [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) 命令列公用程式。
- MySQL Workbench [MySQL Workbench 下載](https://dev.mysql.com/downloads/workbench/) \(英文\) 或用來執行傾印和還原命令的其他協力廠商 MySQL 工具。

如果您想要移轉大型資料庫 (資料庫大小超過 1 TB)，您可以考慮使用可支援平行匯出和匯入的社群工具，例如 mydumper/myloader。 平行傾印及還原有助於大幅縮短大型資料庫的移轉時間。 如需使用 mydumper/myloader 工具將大型資料庫移轉至適用於 MySQL 的 Azure 資料庫服務的最佳做法，您可以參閱我們的[技術社群部落格](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699) \(英文\)。

## <a name="use-common-tools"></a>使用一般工具
使用一般公用程式和工具 (例如 MySQL Workbench 或 mysqldump) 從遠端連線，然後將資料還原至適用於 MySQL 的 Azure 資料庫。 在具有網際網路連接的用戶端電腦上使用這類工具，來連接到適用於 MySQL 的 Azure 資料庫。 如需使用 SSL 加密連接的最佳安全性作法，請參閱[在適用於 MySQL 的 Azure 資料庫中設定 SSL 連線能力](concepts-ssl-connection-security.md)。 在移轉到適用於 MySQL 的 Azure 資料庫時，您不需要將傾印檔案移至任何特定的雲端位置。 

## <a name="common-uses-for-dump-and-restore"></a>傾印和還原的常見用途
您可以在數個常見案例中使用 MySQL 公用程式 (例如 mysqldump 和 mysqlpump)，將資料庫傾印和載入至 Azure MySQL Database。 在其他案例中，您可以改為使用[匯入和匯出](concepts-migrate-import-export.md)方法。

- 當您移轉整個資料庫時，使用資料庫傾印。 這項建議在您移動大量 MySQL 資料，或當您想要將即時網站或應用程式的服務中斷時間降到最低時會保留。 
-  將資料載入至適用於 MySQL 的 Azure 資料庫時，請確定資料庫中的所有資料表都使用 InnoDB 儲存引擎。 適用於 MySQL 的 Azure 資料庫僅支援 InnoDB 儲存引擎，因此不支援其他儲存引擎。 如果您的資料表是使用其他儲存引擎設定，請將它們轉換成 InnoDB 引擎格式，然後再移轉至適用於 MySQL 的 Azure 資料庫。
   例如，如果您的 WordPress 或 WebApp 使用 MyISAM 資料表，請先藉由移轉至 InnoDB 格式來轉換這些資料表，然後再還原至適用於 MySQL 的 Azure 資料庫。 使用子句 `ENGINE=InnoDB` 以設定建立新資料表時使用的引擎，然後在還原之前將資料傳送到相容的資料表。 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- 若要避免任何相容性問題，請確定當傾印資料庫時，在來源和目的地系統上使用相同版本的 MySQL。 例如，如果現有的 MySQL 伺服器是 5.7 版，則您應該將適用於 MySQL 的 Azure 資料庫設定為執行 5.7 版。 `mysql_upgrade` 命令在適用於 MySQL 伺服器的 Azure 資料庫中無法運作，因此並不支援。 如果您要在 MySQL 版本之間升級，請先將較低版本的資料庫傾印或匯出到自己環境中較高版本的 MySQL。 然後執行 `mysql_upgrade`，之後再嘗試移轉至適用於 MySQL 的 Azure 資料庫。

## <a name="performance-considerations"></a>效能考量
若要最佳化效能，請在傾印大型資料庫時注意這些考量：
-   傾印資料庫時在 mysqldump 中使用 `exclude-triggers` 選項。 從傾印檔案排除觸發程序以避免在資料還原期間引發觸發程序命令。 
-   使用 `single-transaction` 選項將交易隔離模式設為 REPEATABLE READ，然後在傾印資料之前，將 START TRANSACTION 的 SQL 陳述式傳送到伺服器。 在單一交易中傾印許多資料表會導致在還原期間耗用某些額外的儲存體。 `single-transaction` 選項和 `lock-tables` 選項是互斥的，因為 LOCK TABLES 會導致隱含認可任何暫止交易。 若要傾印大型資料表，請結合 `single-transaction` 選項與 `quick` 選項。 
-   使用包含數個 VALUE 清單的 `extended-insert` 多個資料列語法。 這會產生較小的傾印檔案，並在重新載入檔案時加速插入。
-  傾印資料庫時在 mysqldump 中使用 `order-by-primary` 選項，以便將資料以主索引鍵的順序編寫指令碼。
-   傾印資料時在 mysqldump 中使用 `disable-keys` 選項，以在載入之前停用外部索引鍵限制式。 停用外部索引鍵檢查會提供效能提升。 啟用限制式並且確認載入之後的資料，以確保參考完整性。
-   適當時使用資料分割資料表。
-   平行載入資料。 避免會導致您達到資源限制的太多平行處理原則，以及使用 Azure 入口網站中可用的計量監視資源。 
-   傾印資料庫時在 mysqlpump 中使用 `defer-table-indexes` 選項，以便在載入資料表資料之後建立索引。
-   使用 mysqlpump 中的 `skip-definer` 選項，從 create 陳述式中省略檢視和預存程序的 definer 和 SQL SECURITY 子句。  當您重新載入傾印檔案時，其會建立使用預設 DEFINER 和 SQL SECURITY 值的物件。
-   請將備份檔案複製到 Azure blob/存放區，並從該處執行還原，這樣應該會比在網際網路上執行還原更快。

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>使用 mysqldump 從命令列建立備份檔案
若要在本機內部部署伺服器或虛擬機器中備份現有的 MySQL 資料庫，請執行下列命令： 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

提供的參數如下：
- [uname] 您的資料庫使用者名稱 
- [pass] 您的資料庫密碼 (請注意 -p 與密碼之間沒有空格) 
- [dbname] 您的資料庫名稱 
- [backupfile.sql] 資料庫備份的檔案名稱 
- [--opt] mysqldump 選項 

例如，若要將 MySQL 伺服器上使用者名稱為 'testuser' 且無密碼之名為 'testdb' 的資料庫備份到 testdb_backup.sql 檔案，請使用下列命令。 此命令會將 `testdb` 資料庫備份至名為 `testdb_backup.sql` 的檔案，其中包含重新建立資料庫所需的所有 SQL 陳述式。 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
若要在資料庫中選取要備份的特定資料表，請列出以空格分隔的資料表名稱。 例如，如果只要從 'testdb' 備份 table1 和 table2 資料表，請遵循下列範例： 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
若要一次備份多個資料庫，請使用 --database 參數，並列出以空格分隔的資料庫名稱。 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>在目標適用於 MySQL 伺服器的 Azure 資料庫上建立資料庫
在您要移轉資料的目標適用於 MySQL 伺服器的 Azure 資料庫上建立空白資料庫。 使用 MySQL Workbench 之類的工具來建立資料庫。 資料庫名稱可以與包含傾印資料的資料庫名稱相同，或者您可以建立名稱不同的資料庫。

若要連線，在適用於 MySQL 之 Azure 資料庫的 [概觀] 中找到連線資訊。

![在 Azure 入口網站中尋找連線資訊](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

將連線資訊新增至 MySQL Workbench。

![MySQL Workbench 連接字串](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)

## <a name="preparing-the-target-azure-database-for-mysql-server-for-fast-data-loads"></a>準備目標適用於 MySQL 的 Azure 資料庫伺服器，以快速載入資料
若要準備目標適用於 MySQL 的 Azure 資料庫伺服器，以更快速地載入資料，則必須變更下列伺服器參數和設定。
- max_allowed_packet – 設定為 1073741824 (也就是 1GB)，以防止因為長資料列而造成任何溢位問題。
- slow_query_log – 設定為 [關閉]，以關閉慢速查詢記錄。 這將會排除在資料載入期間因慢速查詢記錄而造成的額外負荷。
- query_store_capture_mode – 將兩個都設定為 NONE，以關閉查詢存放區。 這將會排除查詢存放區取樣活動所造成的額外負荷。
- innodb_buffer_pool_size – 在移轉期間，從入口網站的定價層將伺服器擴大至 32 vCore 記憶體最佳化 SKU，以提高 innodb_buffer_pool_size。 Innodb_buffer_pool_size 只能藉由擴大適用於 MySQL 的 Azure 資料庫伺服器的計算來增加。
- innodb_write_io_threads & innodb_write_io_threads - 從 Azure 入口網站中的伺服器參數變更為 16，以改善移轉的速度。
- 擴大儲存層 – 適用於 MySQL 的 Azure 資料庫伺服器的 IOPS 會隨著儲存層的成長而逐漸增加。 如需更快速的載入速度，您可以增加儲存層以增加佈建的 IOPS。 請記住，儲存體只能擴大，而不能縮小。

完成移轉之後，您可以將伺服器參數和計算層設定還原回其先前的值。 

## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>使用命令列或 MySQL Workbench 來還原 MySQL 資料庫
建立目標資料庫後，您可以使用 mysql 命令或 MySQL Workbench，從傾印檔案將資料還原至新建立的特定資料庫。
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
在此範例中，將資料還原至目標適用於 MySQL 伺服器的 Azure 資料庫上新建立的資料庫。
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```
## <a name="export-using-phpmyadmin"></a>使用 PHPMyAdmin 匯出
若要匯出，您可以使用一般工具 phpMyAdmin，而您可能已在環境中本機安裝此工具。 使用 PHPMyAdmin 匯出 MySQL 資料庫：
1. 開啟 phpMyAdmin。
2. 選取您的資料庫。 按一下左邊清單中的資料庫名稱。 
3. 按一下 [匯出] 連結。 新的分頁隨即出現，以供檢視資料庫的傾印。
4. 在 [匯出] 區域中，按一下 [全選] 連結來選擇資料庫中的資料表。 
5. 在 [SQL 選項] 區域中，按一下適當的選項。 
6. 依序按一下 [另存新檔] 和對應的壓縮選項，然後按一下 [執行] 按鈕。 接著應該會出現一個對話方塊，提示您在本機儲存檔案。

## <a name="import-using-phpmyadmin"></a>使用 PHPMyAdmin 匯入
匯入資料庫的程序與匯出類似。 請執行下列動作：
1. 開啟 phpMyAdmin。 
2. 在 [phpMyAdmin 安裝] 分頁中，按一下 [新增] 以新增適用於 MySQL 伺服器的 Azure 資料庫。 提供連線詳細資料和登入資訊。
3. 建立已適當命名的資料庫，然後在畫面左邊選取它。 若要重寫現有的資料庫，按一下資料庫名稱、選取資料表名稱旁的所有核取方塊，然後選取 [捨棄] 以刪除現有的資料表。 
4. 按一下 **SQL** 連結，以顯示您可以在其中輸入 SQL 命令或上傳 SQL 檔案的分頁。 
5. 您可以使用**瀏覽**按鈕來尋找資料庫檔案。 
6. 按一下 [執行] 按鈕以匯出備份、執行 SQL 命令，並重新建立您的資料庫。

## <a name="known-issues"></a>已知問題
如需已知問題、秘訣與技巧，建議您查看我們的[技術社群部落格](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/tips-and-tricks-in-using-mysqldump-and-mysql-restore-to-azure/ba-p/916912) \(英文\)。

## <a name="next-steps"></a>後續步驟
- [將應用程式連線至適用於 MySQL 的 Azure 資料庫](./howto-connection-string.md)。
- 若要深入了解如何將資料庫移轉至適用於 MySQL 的 Azure 資料庫，請參閱[資料庫移轉指南](https://aka.ms/datamigration)。
