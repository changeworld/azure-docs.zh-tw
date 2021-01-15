---
title: 建立使用者-適用於 MariaDB 的 Azure 資料庫
description: 本文描述如何建立新的使用者帳戶，來與「適用於 MariaDB 的 Azure 資料庫」伺服器互動。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 62aadb366fd216cb6dcefff6cdde24dc21b8483a
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231905"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>在適用於 MariaDB 的 Azure 資料庫中建立使用者

本文描述如何在適用於 MariaDB 的 Azure 資料庫中建立使用者。

> [!NOTE]
> 無偏差通訊
>
> Microsoft 支援多元和包容性的環境。 本文包含「 _主要_ 」和「 _從屬_」這些單字的參考。 [適用于無偏差通訊的 Microsoft 樣式指南](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)會將這些視為排他性行為單字。 本文中使用的單字是為了保持一致性，因為它們目前是出現在軟體中的單字。 當軟體更新為移除這些字組時，將會更新本文以進行調整。
>

當您第一次建立適用於 MariaDB 的 Azure 資料庫時，您提供了伺服器管理員登入使用者名稱和密碼。 如需詳細資訊，您可以遵循[快速入門](quickstart-create-mariadb-server-database-using-azure-portal.md)。 您可以從 Azure 入口網站找出您的伺服器管理員登入使用者名稱。

伺服器管理員使用者可取得您伺服器的某些權限，如下所列：SELECT、INSERT、UPDATE、DELETE、CREATE、DROP、RELOAD、PROCESS、REFERENCES、INDEX、ALTER、SHOW DATABASES、CREATE TEMPORARY TABLES、LOCK TABLES、EXECUTE、REPLICATION SLAVE、REPLICATION CLIENT、CREATE VIEW、SHOW VIEW、CREATE ROUTINE、ALTER ROUTINE、CREATE USER、EVENT、TRIGGER

建立適用於 MariaDB 的 Azure 資料庫伺服器之後，您可以使用第一個伺服器系統管理員使用者帳戶來建立更多使用者並授與系統管理員存取權。 此外，伺服器管理員帳戶可以用來建立較低權限的使用者，以存取個別資料庫結構描述。

> [!NOTE]
> 不支援超級許可權和 DBA 角色。 請參閱限制文章中的 [許可權](concepts-limits.md#privileges--data-manipulation-support) ，以瞭解服務中不支援的功能。<br><br>
> 服務不支援密碼外掛程式，例如 "validate_password" 和 "caching_sha2_password"。

## <a name="create-more-admin-users"></a>建立更多系統管理員使用者

1. 取得連線資訊和管理員使用者名稱。
   若要連線到您的資料庫伺服器，您需要完整伺服器名稱和系統管理員登入認證。 您可以從 Azure 入口網站的伺服器 [概觀] 或 [屬性] 頁面輕鬆尋找伺服器名稱和登入資訊。

2. 使用系統管理員帳戶和密碼來連線到資料庫伺服器。 使用您慣用的用戶端工具，例如 MySQL Workbench、mysql.exe、HeidiSQL 或其他工具。
   如果您不確定如何連線，請參閱 [使用 MySQL 工作臺來連接和查詢資料](./connect-workbench.md)

3. 編輯並執行下列 SQL 程式碼。 將預留位置值 `new_master_user` 替換為您的新使用者名稱。 此語法可將所有資料庫結構描述上列出的特殊權限 (*.*) 授與給使用者名稱 (在此範例中為 new_master_user)。 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. 驗證授權。

   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="create-database-users"></a>建立資料庫使用者

1. 取得連線資訊和管理員使用者名稱。
   若要連線到您的資料庫伺服器，您需要完整伺服器名稱和系統管理員登入認證。 您可以從 Azure 入口網站的伺服器 [概觀] 或 [屬性] 頁面輕鬆尋找伺服器名稱和登入資訊。 

2. 使用系統管理員帳戶和密碼來連線到資料庫伺服器。 使用您慣用的用戶端工具，例如 MySQL Workbench、mysql.exe、HeidiSQL 或其他工具。 
   如果您不確定如何連線，請參閱[使用 MySQL Workbench 來連線及查詢資料](./connect-workbench.md)

3. 編輯並執行下列 SQL 程式碼。 將預留位置值 `db_user` 替換為您預定的新使用者名稱，並將預留位置值 `testdb` 替換為您自己的資料庫名稱。

   這個 sql 程式碼語法會建立名為 testdb 的新資料庫作為範例。 然後在適用於 MariaDB 的 Azure 資料庫服務中建立新的使用者，並將所有特殊權限授與給該使用者的新資料庫結構描述 (testdb.\*)。 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. 確認資料庫中的授與。

   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. 使用新的使用者名稱和密碼來指定指定的資料庫，以登入伺服器。 此範例會顯示 mysql 命令列。 使用此命令時，系統會提示您輸入使用者名稱的密碼。 取代您自己的伺服器名稱、資料庫名稱和使用者名稱。

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

   如需使用者帳戶管理的詳細資訊，請參閱 [使用者帳戶管理](https://mariadb.com/kb/en/library/user-account-management/)、 [授與語法](https://mariadb.com/kb/en/library/grant/)和 [許可權](https://mariadb.com/kb/en/library/grant/#privilege-levels)的適用于 mariadb 檔。

## <a name="azure_superuser"></a>azure_superuser

所有適用於 MySQL 的 Azure 資料庫伺服器都會以名為 "azure_superuser" 的使用者建立。 這是 Microsoft 所建立的系統帳戶，用來管理伺服器以進行監視、備份和其他定期維護。 待命工程師也可以使用此帳戶在具有憑證驗證的事件期間存取伺服器，而且必須使用即時 (JIT) 處理常式來要求存取權。

## <a name="next-steps"></a>後續步驟

針對新使用者電腦的 IP 位址開啟防火牆，讓使用者能夠連線：[使用 Azure 入口網站建立及管理適用於 MariaDB 的 Azure 資料庫防火牆規則](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
