---
title: 解決資料庫損毀-適用於 MySQL 的 Azure 資料庫
description: 瞭解如何修正適用於 MySQL 的 Azure 資料庫的資料庫損毀問題
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 815b24d25e674e5460cc50d7eb6871f740994893
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934250"
---
# <a name="troubleshoot-database-corruption-on-azure-database-for-mysql"></a>針對適用於 MySQL 的 Azure 資料庫上的資料庫損毀進行疑難排解
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

資料庫損毀可能會導致您的應用程式停止運作，而且您也必須及時解決問題，以避免資料遺失。 發生資料庫損毀時，您會在伺服器中看到此錯誤 **InnoDB：磁片上的資料庫頁面損毀或失敗**。

在本指南中，您將瞭解如何修正資料庫或資料表是否損毀。 適用於 MySQL 的 Azure 資料庫使用 InnoDB 引擎，並提供自動損毀檢查和修復作業的功能。 InnoDB 會在每個讀取的頁面上執行總和檢查碼來檢查是否有損毀的頁面，如果發現總和檢查碼差異，則會自動停止 MySQL 伺服器。

請嘗試下列任何選項，以協助您快速減輕資料庫損毀問題。

## <a name="restart-your-mysql-server"></a>重新開機 MySQL 伺服器

您通常會注意到，當您的應用程式存取該資料表 ro 資料庫時，資料庫或資料表已損毀。 由於 InnoDB 具有損毀復原機制，可解決伺服器重新開機時的大部分問題。 因此，重新開機伺服器應該有助於伺服器從造成資料庫處於不良狀態的損毀中復原。

##  <a name="resolve-data-corruption-with-dump-and-restore-method"></a>解決傾印和還原方法的資料損毀

建議您解決傾印 **和還原方法**的損毀問題。 這包括取得損毀資料表的存取權，並使用 **mysqldump** 公用程式建立資料表的邏輯備份，這會保留資料表結構和其中的資料，然後將資料表重載回資料庫中。

### <a name="backup-your-database-or-tables"></a>備份您的資料庫或資料表

> [!Important]
> - 讓您已設定防火牆規則，以便從用戶端電腦存取伺服器。 瞭解如何[在彈性伺服器上](flexible-server/how-to-connect-tls-ssl.md)設定單一伺服器和防火牆規則的[防火牆規則](howto-manage-firewall-using-portal.md)。
> - ```--ssl-cert```如果已啟用 ssl，請使用**MYSQLDUMP**的 ssl 選項

使用此命令，使用 mysqldump 從命令列建立備份檔案

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

提供的參數如下：
- [ssl-cert =/path/to/pem]在您的用戶端電腦上下載 SSL 憑證，並在命令中設定該憑證的路徑。 請勿使用 [SSL 已停用]。
- [host] 是您的適用於 MySQL 的 Azure 資料庫伺服器
- [uname] 是您的伺服器管理員使用者名稱
- [pass] 是您系統管理員使用者的密碼
- [dbname] 是您資料庫的名稱
- [backupfile] 如果資料庫備份的檔案名

> [!Important]
> - 若為單一伺服器，請使用 ```admin-user@servername``` ```myserveradmin``` 下列命令中要取代的格式。
> - 若為彈性伺服器，請使用 ```admin-user``` ```myserveradmin``` 下列命令中要取代的格式。

如果特定資料表已損毀，請使用此範例選取您資料庫中的特定資料表進行備份
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

若要備份一或多個資料庫，請使用--database 參數，並列出以空格分隔的資料庫名稱。

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

###  <a name="restore-your-database-or-tables"></a>還原您的資料庫或資料表

下列步驟說明 tp 如何還原您的資料庫或資料表。 建立備份檔案之後，您可以使用 ***mysql** 公用程式來還原資料表或資料庫。 執行命令，如下所示：

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
以下是 ```testdb``` 從使用 **mysqldump**建立的備份檔案還原的範例。 

> [!Important]
> - 若為單一伺服器，請使用 ```admin-user@servername``` ```myserveradmin``` 下列命令中要取代的格式。
> - 若為彈性伺服器，請使用 ```admin-user``` ```myserveradmin``` 下列命令中要取代的格式。 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>後續步驟
如果上述步驟無法協助您解決問題，您一律可以還原整部伺服器。
- [還原適用於 MySQL 的 Azure 資料庫單一伺服器](howto-restore-server-portal.md)
- [還原適用於 MySQL 的 Azure 資料庫彈性的伺服器](flexible-server/how-to-restore-server-portal.md)



