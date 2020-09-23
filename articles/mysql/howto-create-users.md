---
title: 建立資料庫和使用者-適用於 MySQL 的 Azure 資料庫
description: 本文說明如何建立新的使用者帳戶，來與「適用於 MySQL 的 Azure 資料庫」伺服器互動。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 4/2/2020
ms.openlocfilehash: 9b79a0f21135e91ab72a4c8a9e604b84b67df0a9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902826"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql-server"></a>在適用於 MySQL 的 Azure 資料庫伺服器中建立資料庫和使用者

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

本主題說明如在「適用於 MySQL 的 Azure 資料庫」伺服器中建立使用者。

> [!NOTE]
> 無偏差通訊
>
> Microsoft 支援多樣化且 inclusionary 的環境。 本文包含單字 _從屬_的參考。 [適用于無偏差通訊的 Microsoft 樣式指南](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)會將此視為排他性行為單字。 本文中會使用這個字來保持一致性，因為它目前是出現在軟體中的單字。 當軟體更新為移除該字時，將會更新本文以進行調整。
>

當您第一次建立「適用於 MySQL 的 Azure 資料庫」時，您提供了伺服器管理員登入使用者名稱和密碼。 如需詳細資訊，您可以遵循[快速入門](quickstart-create-mysql-server-database-using-azure-portal.md)。 您可以從 Azure 入口網站找出您的伺服器管理員登入使用者名稱。

伺服器管理員使用者可取得您伺服器的某些權限，如下所列： 

   SELECT、INSERT、UPDATE、DELETE、CREATE、DROP、RELOAD、PROCESS、REFERENCES、INDEX、ALTER、SHOW DATABASE、CREATE TEMPORARY TABLES、LOCK TABLES、EXECUTE、REPLICATION 從屬、REPLICATION CLIENT、CREATE VIEW、SHOW VIEW、CREATE 常式、ALTER 常式、CREATE USER、EVENT、TRIGGER


建立「適用於 MySQL 的 Azure 資料庫」伺服器之後，您可以使用第一個伺服器管理員使用者帳戶，建立其他使用者並授與系統管理員存取權。 此外，伺服器管理員帳戶可以用來建立較低權限的使用者，以存取個別資料庫結構描述。

> [!NOTE]
> 不支援超級許可權和 DBA 角色。 請參閱限制文章中的 [許可權](concepts-limits.md#privilege-support) ，以瞭解服務中不支援的功能。

## <a name="how-to-create-database-with-non-admin-user-in-azure-database-for-mysql"></a>如何在適用於 MySQL 的 Azure 資料庫中使用非系統管理使用者建立資料庫

1. 取得連線資訊和管理員使用者名稱。
   若要連線到您的資料庫伺服器，您需要完整伺服器名稱和系統管理員登入認證。 您可以從 Azure 入口網站的伺服器 [概觀]**** 或 [屬性]**** 頁面輕鬆尋找伺服器名稱和登入資訊。

2. 使用系統管理員帳戶和密碼來連線到資料庫伺服器。 使用您慣用的用戶端工具，例如 MySQL Workbench、mysql.exe、HeidiSQL 或其他工具。
   如果您不確定如何連線，請參閱如何使用 MySQL 工作臺連線 [和查詢單一伺服器的資料](./connect-workbench.md) ，或 [連接和查詢彈性伺服器的資料](./flexible-server/connect-workbench.md)

3. 編輯並執行下列 SQL 程式碼。 將預留位置值 `db_user` 替換為您預定的新使用者名稱，並將預留位置值 `testdb` 替換為您自己的資料庫名稱。

   這個 sql 程式碼語法會建立名為 testdb 的新資料庫作為範例。 然後在 MySQL 服務中建立新的使用者，並將所有特殊權限授與給該使用者的新資料庫結構描述 (testdb.\*)。

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

5. 指定委派的資料庫，使用新的使用者名稱和密碼來登入伺服器。 此範例會顯示 mysql 命令列。 執行此命令時，系統會提示您輸入使用者名稱的密碼。 取代您自己的伺服器名稱、資料庫名稱和使用者名稱。

# <a name="single-server"></a>[單一伺服器](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
# <a name="flexible-server"></a>[彈性的伺服器](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>如何在適用於 MySQL 的 Azure 資料庫中建立其他系統管理員使用者

1. 取得連線資訊和管理員使用者名稱。
   若要連線到您的資料庫伺服器，您需要完整伺服器名稱和系統管理員登入認證。 您可以從 Azure 入口網站的伺服器 [概觀]**** 或 [屬性]**** 頁面輕鬆尋找伺服器名稱和登入資訊。

2. 使用系統管理員帳戶和密碼來連線到資料庫伺服器。 使用您慣用的用戶端工具，例如 MySQL Workbench、mysql.exe、HeidiSQL 或其他工具。
   如果您不確定如何連線，請參閱[使用 MySQL Workbench 來連線及查詢資料](./connect-workbench.md)

3. 編輯並執行下列 SQL 程式碼。 將預留位置值 `new_master_user` 替換為您的新使用者名稱。 此語法可將所有資料庫結構描述上列出的特殊權限 (*.*) 授與給使用者名稱 (在此範例中為 new_master_user)。

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. 確認授與

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="next-steps"></a>下一步

針對新使用者電腦的 IP 位址開啟防火牆，讓使用者能夠連線：
- [在單一伺服器上建立和管理防火牆規則](howto-manage-firewall-using-portal.md) 
- [ 在彈性的伺服器上建立和管理防火牆規則](flexible-server/how-to-connect-tls-ssl.md)

如需有關使用者帳戶管理的詳細資訊，請參閱 MySQL 產品文件中的[使用者帳戶管理](https://dev.mysql.com/doc/refman/5.7/en/access-control.html)、[GRANT 語法](https://dev.mysql.com/doc/refman/5.7/en/grant.html)和[特殊權限](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html)。
