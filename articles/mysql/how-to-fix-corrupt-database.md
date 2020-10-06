---
title: 解決資料庫損毀-適用於 MySQL 的 Azure 資料庫
description: 在本文中，您將瞭解如何修正適用於 MySQL 的 Azure 資料庫中的資料庫損毀問題。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 21e4189e56f704129710da5b1d39613c4e1b1df5
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766890"
---
# <a name="troubleshoot-database-corruption-in-azure-database-for-mysql"></a>針對適用於 MySQL 的 Azure 資料庫中的資料庫損毀進行疑難排解
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

資料庫損毀可能會導致您的應用程式停止運作。 也請務必及時解決損毀問題，以避免資料遺失。 發生資料庫損毀時，您會在伺服器記錄中看到此錯誤： `InnoDB: Database page corruption on disk or a failed.`

在本文中，您將瞭解如何解決資料庫或資料表損毀問題。 適用於 MySQL 的 Azure 資料庫使用 InnoDB 引擎。 它具備自動損毀檢查和修復作業的功能。 InnoDB 會在每個讀取的頁面上執行總和檢查碼來檢查是否有損毀的頁面。 如果發現總和檢查碼差異，則會自動停止 MySQL 伺服器。

請嘗試下列選項，以快速減少資料庫損毀問題。

## <a name="restart-your-mysql-server"></a>重新開機 MySQL 伺服器

您通常會注意到當您的應用程式存取資料表或資料庫時，資料庫或資料表已損毀。 InnoDB 具有損毀復原機制，可解決伺服器重新開機時的大部分問題。 因此，重新開機伺服器有助於伺服器從造成資料庫處於不良狀態的損毀中復原。

## <a name="use-the-dump-and-restore-method"></a>使用 dump 和 restore 方法

建議您使用傾印 *和 restore* 方法解決損毀問題。 此方法包含：
1. 存取損毀的資料表。
1. 使用 mysqldump 公用程式建立資料表的邏輯備份。 備份會保留資料表結構和其中的資料。
1. 將資料表重載到資料庫中。

### <a name="back-up-your-database-or-tables"></a>備份您的資料庫或資料表

> [!Important]
> - 確定您已設定防火牆規則，以從用戶端電腦存取伺服器。 如需詳細資訊，請參閱在 [單一伺服器上設定防火牆規則](howto-manage-firewall-using-portal.md) 和 [在彈性伺服器上設定防火牆規則](flexible-server/how-to-connect-tls-ssl.md)。
> - `--ssl-cert`如果您已啟用 ssl，請使用 mysqldump 的 ssl 選項。

使用 mysqldump 從命令列建立備份檔案。 使用此命令：

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

參數描述：
- `[ssl-cert=/path/to/pem]`： SSL 憑證的路徑。 在您的用戶端電腦上下載 SSL 憑證，並在命令中設定它的路徑。 如果停用 SSL，請不要使用此參數。
- `[host]`：您的適用於 MySQL 的 Azure 資料庫伺服器。
- `[uname]`：您的伺服器管理員使用者名稱。
- `[pass]`：系統管理員使用者的密碼。
- `[dbname]`：資料庫的名稱。
- `[backupfile.sql]`：資料庫備份的檔案名。

> [!Important]
> - 若為單一伺服器，請使用 `admin-user@servername` 下列命令中要取代的格式 `myserveradmin` 。
> - 針對有彈性的伺服器，請使用 `admin-user` `myserveradmin` 下列命令中要取代的格式。

如果特定資料表已損毀，請選取您資料庫中的特定資料表進行備份：
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

若要備份一或多個資料庫，請使用 `--database` 參數，並列出以空格分隔的資料庫名稱：

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-database-or-tables"></a>還原您的資料庫或資料表

下列步驟說明如何還原您的資料庫或資料表。 建立備份檔案之後，您可以使用 mysql 公用程式來還原資料表或資料庫。 請執行這個命令：

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
以下是 `testdb` 從使用 mysqldump 建立的備份檔案還原的範例： 

> [!Important]
> - 若為單一伺服器，請使用 `admin-user@servername` 下列命令中要取代的格式 `myserveradmin` 。
> - 針對有彈性的伺服器，請使用 ```admin-user``` `myserveradmin` 下列命令中要取代的格式。 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>後續步驟
如果上述步驟無法解決問題，您一律可以還原整部伺服器：
- [適用於 MySQL 的 Azure 資料庫-單一伺服器中的還原伺服器](howto-restore-server-portal.md)
- [在適用於 MySQL 的 Azure 資料庫彈性的伺服器中還原伺服器](flexible-server/how-to-restore-server-portal.md)



