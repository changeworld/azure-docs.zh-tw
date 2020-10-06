---
title: 建立資料庫和使用者-適用於 MySQL 的 Azure 資料庫
description: 本文說明如何建立新的使用者帳戶，以與適用於 MySQL 的 Azure 資料庫伺服器互動。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 3e1f24b3ae6133241660751293f52fec63dfbe73
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766868"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql"></a>在適用於 MySQL 的 Azure 資料庫中建立資料庫和使用者

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

本文說明如何在適用於 MySQL 的 Azure 資料庫中建立使用者。

> [!NOTE]
> **無偏差通訊**
>
> Microsoft 支援多樣化且 inclusionary 的環境。 本文包含單字 *從屬*的參考。 [適用于無偏差通訊的 Microsoft 樣式指南](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)會將此視為排他性行為單字。 本文會使用這個字來保持一致性，因為這是目前出現在軟體中的單字。 當軟體更新為移除該字時，將會更新本文以進行調整。
>

當您第一次建立適用於 MySQL 的 Azure 資料庫伺服器時，會提供伺服器系統管理員使用者名稱和密碼。 如需詳細資訊，請參閱本 [快速入門](quickstart-create-mysql-server-database-using-azure-portal.md)。 您可以在 Azure 入口網站中判斷您的伺服器管理員使用者名稱。

伺服器管理員使用者有下列許可權： 

   SELECT、INSERT、UPDATE、DELETE、CREATE、DROP、RELOAD、PROCESS、REFERENCES、INDEX、ALTER、SHOW DATABASES、CREATE TEMPORARY TABLES、LOCK TABLES、EXECUTE、REPLICATION SLAVE、REPLICATION CLIENT、CREATE VIEW、SHOW VIEW、CREATE ROUTINE、ALTER ROUTINE、CREATE USER、EVENT、TRIGGER


建立適用於 MySQL 的 Azure 資料庫伺服器之後，您可以使用第一個伺服器系統管理員帳戶來建立其他使用者，並授與系統管理員存取權。 您也可以使用伺服器系統管理員帳戶來建立許可權較低的使用者，以存取個別的資料庫架構。

> [!NOTE]
> 不支援超級許可權和 DBA 角色。 請參閱限制文章中的 [許可權](concepts-limits.md#privileges--data-manipulation-support) ，以瞭解服務中不支援的功能。
>
> `validate_password`服務不支援和等密碼外掛程式 `caching_sha2_password` 。


## <a name="to-create-a-database-with-a-non-admin-user-in-azure-database-for-mysql"></a>若要在適用於 MySQL 的 Azure 資料庫中建立具有非系統管理員使用者的資料庫

1. 取得連線資訊和管理員使用者名稱。
   若要連線到您的資料庫伺服器，您需要完整伺服器名稱和系統管理員登入認證。 您可以在 [伺服器 **總覽** ] 頁面或 Azure 入口網站的 [ **屬性** ] 頁面上，輕鬆找到伺服器名稱和登入資訊。

2. 使用系統管理員帳戶和密碼來連線到資料庫伺服器。 使用您慣用的用戶端工具，例如 MySQL 工作臺、mysql.exe 或 HeidiSQL。
   
   如果您不確定如何連線，請參閱 [連接和查詢單一伺服器的資料](./connect-workbench.md) ，或 [連接和查詢具有彈性之伺服器的資料](./flexible-server/connect-workbench.md)。

3. 編輯並執行下列 SQL 程式碼。 將預留位置值取代 `db_user` 為您想要的新使用者名稱。 將預留位置值取代 `testdb` 為您的資料庫名稱。

   此 SQL 程式碼會建立名為 testdb 的新資料庫。 然後，它會在 MySQL 服務中建立新的使用者，並將新資料庫架構的擁有權限授與 (testdb。 \*) 給該使用者。

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. 確認資料庫中的授與：

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. 使用新的使用者名稱和密碼來登入伺服器，並指定指定的資料庫。 此範例會顯示 mysql 命令列。 當您使用此命令時，系統會提示您輸入使用者的密碼。 使用您自己的伺服器名稱、資料庫名稱和使用者名稱。

   # <a name="single-server"></a>[單一伺服器](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   # <a name="flexible-server"></a>[彈性伺服器](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="to-create-additional-admin-users-in-azure-database-for-mysql"></a>若要在適用於 MySQL 的 Azure 資料庫中建立額外的系統管理使用者

1. 取得連線資訊和管理員使用者名稱。
   若要連線到您的資料庫伺服器，您需要完整伺服器名稱和系統管理員登入認證。 您可以在 [伺服器 **總覽** ] 頁面或 Azure 入口網站的 [ **屬性** ] 頁面上，輕鬆找到伺服器名稱和登入資訊。

2. 使用系統管理員帳戶和密碼來連線到資料庫伺服器。 使用您慣用的用戶端工具，例如 MySQL 工作臺、mysql.exe 或 HeidiSQL。
   
   如果您不確定如何連線，請參閱 [使用 MySQL 工作臺來連接和查詢資料](./connect-workbench.md)。

3. 編輯並執行下列 SQL 程式碼。 `new_master_user`以新的使用者名稱取代預留位置值。 此語法會授與所有資料庫架構的所列許可權 (*。*) `new_master_user` 此範例) 的使用者 (。

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. 確認授權：

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="azure_superuser"></a>azure_superuser

所有適用於 MySQL 的 Azure 資料庫伺服器都會以名為 "azure_superuser" 的使用者建立。 這是 Microsoft 所建立的系統帳戶，用來管理伺服器以進行監視、備份和其他定期維護。 待命工程師也可以使用此帳戶在具有憑證驗證的事件期間存取伺服器，而且必須使用即時 (JIT) 處理常式來要求存取權。

## <a name="next-steps"></a>後續步驟

針對新使用者電腦的 IP 位址開啟防火牆，讓使用者能夠連線：
- [在單一伺服器上建立和管理防火牆規則](howto-manage-firewall-using-portal.md) 
- [ 在彈性的伺服器上建立和管理防火牆規則](flexible-server/how-to-connect-tls-ssl.md)

如需使用者帳戶管理的詳細資訊，請參閱 MySQL 產品檔中的 [使用者帳戶管理](https://dev.mysql.com/doc/refman/5.7/en/access-control.html)、 [授與語法](https://dev.mysql.com/doc/refman/5.7/en/grant.html)和 [許可權](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html)。
