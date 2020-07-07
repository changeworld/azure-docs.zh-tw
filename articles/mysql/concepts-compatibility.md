---
title: 驅動程式和工具相容性-適用於 MySQL 的 Azure 資料庫
description: 本文說明 MySQL 驅動程式和管理工具與適用於 MySQL 的 Azure 資料庫之相容性。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: e8917a0a5678c4c6b72352a0d4c1523bfea3c96d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79537205"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>MySQL 驅動程式和管理工具與適用於 MySQL 的 Azure 資料庫的相容性
本文說明驅動程式和管理工具與適用於 MySQL 的 Azure 資料庫之相容性。

## <a name="mysql-drivers"></a>MySQL 驅動程式
適用於 MySQL 的 Azure 資料庫使用世界上最熱門的 MySQL 資料庫社群版本。 因此，此版本與多種程式設計語言和驅動程式相容。 目標是支援三個最新版本的 MySQL 驅動程式，並繼續與開放原始碼社群作者一起努力，持續改善 MySQL 驅動程式的功能和可用性。 下表提供的驅動程式清單經過測試，證明與適用於 MySQL 5.6 和 5.7 的 Azure 資料庫相容：

| **程式設計語言** | **驅動程式** | **連結** | **相容的版本** | **不相容的版本** | **備註** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli、pdo_mysql、mysqlnd | https://secure.php.net/downloads.php | 5.5、5.6、7.x | 5.3 | 若要連接 PHP 7.0 與 SSL MySQLi，請在連接字串中加入 MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT。 <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO 組：```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` 選項為 false。|
| .NET | 適用于 .NET 的非同步 MySQL 連接器 | https://github.com/mysql-net/MySqlConnector <br> [來自 Nuget 的安裝套件](https://www.nuget.org/packages/MySqlConnector/) \(英文\) | 0.27 及更新版本 | 0.26.5 及更舊版本 | |
| .NET | MySQL Connector/NET | https://github.com/mysql/mysql-connector-net | 6.6.3、7.0、8。0 |  | 編碼錯誤 (bug) 可能會導致在某些非 UTF8 Windows 系統上的連線失敗。 |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> 來自 NPM 的安裝套件：<br> 從 NPM 執行 `npm install mysql` | 2.15 | 2.14.1 及更舊版本 | |
| Node.js | 節點-mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4 + | | |
| Go | Go MySQL 驅動程式 | https://github.com/go-sql-driver/mysql/releases | 1.3、1。4 | 1.2 及更舊版本 | `allowNativePasswords=true`在1.3 版的連接字串中使用。 1.4 版包含修正程式，不再 `allowNativePasswords=true` 需要。 |
| Python | MySQL 連接器/Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3，2.0，2.1，2.2，使用 8.0.16 + 搭配 MySQL 8。0  | 1.2.2 及更舊版本 | |
| Python | PyMySQL | https://pypi.org/project/PyMySQL/ | 0.7.11、0.8.0 版、0.8.1 版、0.9.3 + | 0.9.0-0.9.2 （web2py 中的回歸） | |
| Java | 適用于 mariadb 連接器/J | https://downloads.mariadb.org/connector-java/ | 2.1、2.0、1.6 | 1.5.5 及更舊版本 | | 
| Java | MySQL 連接器/J | https://github.com/mysql/mysql-connector-j | 5.1.21 +，使用 8.0.17 + 搭配 MySQL 8。0 | 5.1.20 和以下 | |
| C | MySQL 連接器/C （libmysqlclient） | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2 + | | |
| C | MySQL 連接器/ODBC （myodbc） | https://github.com/mysql/mysql-connector-odbc | 3.51.29 + | | |
| C++ | MySQL 連接器/c + + | https://github.com/mysql/mysql-connector-cpp | 1.1.9 + | 1.1.3 和以下 | | 
| C++ | MySQL + +| https://tangentsoft.net/mysql++ | 3.2.3 + | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0.4.10 + | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16 + | | |
| Swift | mysql-swift | https://github.com/novi/mysql-swift | 0.7.2 版 + | | |
| Swift | vapor/mysql | https://github.com/vapor/mysql-kit | 2.0.1 + | | |

## <a name="management-tools"></a>管理工具
相容性的優點也會延伸到資料庫管理工具。 只要資料庫操作在使用者權限的範圍內運作，您現有的工具應該就可以繼續搭配適用於 MySQL 的 Azure 資料庫使用。 下表列出的三個常用資料庫管理工具經過測試，證明與適用於 MySQL 5.6 和 5.7 的 Azure 資料庫相容：

|                                     | **MySQL Workbench 6.x 與更新版本** | **Navicat 12** | **PHPMyAdmin 4.x 與更新版本** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| 建立、更新、讀取、寫入、刪除 | X | X | X |
| SSL 連線 | X | X | X |
| SQL 查詢自動完成 | X | X |  |
| 匯入和匯出資料 | X | X | X | 
| 匯出成多種格式 | X | X | X |
| 備份與還原 |  | X |  |
| 顯示伺服器參數 | X | X | X |
| 顯示用戶端連線 | X | X | X |

## <a name="next-steps"></a>後續步驟

- [針對適用於 MySQL 的 Azure 資料庫的連線問題進行疑難排解](howto-troubleshoot-common-connection-issues.md)