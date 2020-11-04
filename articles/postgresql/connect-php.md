---
title: 快速入門：使用 PHP 連線 - 適用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器
description: 本快速入門提供 PHP 程式碼範例，供您在連線至「適用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器」及查詢其資料時使用。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.devlang: php
ms.topic: quickstart
ms.date: 2/28/2018
ms.openlocfilehash: 4e82c8402584f694cb32bb37ae3e6eae9366eaf7
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341428"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>快速入門：使用 PHP 來連線和查詢適用於 PostgreSQL 的 Azure 資料庫中的資料 - 單一伺服器

本快速入門示範如何使用 [PHP](https://secure.php.net/manual/intro-whatis.php) 應用程式來連線到 Azure Database for PostgreSQL。 它會顯示如何使用 SQL 陳述式來查詢、插入、更新和刪除資料庫中的資料。 本文中的步驟假設您已熟悉使用 PHP 進行開發，但不熟悉適用於 PostgreSQL 的 Azure 資料庫。

## <a name="prerequisites"></a>Prerequisites
本快速入門使用在以下任一指南中建立的資源作為起點︰
- [建立 DB - 入口網站](quickstart-create-server-database-portal.md)
- [建立 DB - Azure CLI](quickstart-create-server-database-azure-cli.md)

## <a name="install-php"></a>安裝 PHP
在自己的伺服器上安裝 PHP，或建立 Azure [Web 應用程式](../app-service/overview.md) (包括 PHP)。

### <a name="windows"></a>Windows
- 下載 [PHP 7.1.4 非執行緒安全 (x64) 版本](https://windows.php.net/download#php-7.1)
- 安裝 PHP 並參考 [PHP 手冊](https://secure.php.net/manual/install.windows.php)以便進一步設定
- 程式碼會使用 PHP 安裝內含的 **pgsql** 類別 (ext/php_pgsql.dll)。 
- 藉由編輯 php.ini 組態 (通常位於 `C:\Program Files\PHP\v7.1\php.ini`)，啟用 **pgsql** 擴充功能。 組態檔應包含具有 `extension=php_pgsql.so` 文字的一行。 如果未顯示，請新增文字並儲存檔案。 如果此文字存在，但以分號前置詞標註，請藉由移除分號來取消註解文字。

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
- 下載 [PHP 7.1.4 非執行緒安全 (x64) 版本](https://secure.php.net/downloads.php) 
- 安裝 PHP 並參考 [PHP 手冊](https://secure.php.net/manual/install.unix.php)以便進一步設定
- 程式碼會使用 **pgsql** 類別 (php_pgsql.so)。 透過執行 `sudo apt-get install php-pgsql` 來進行安裝。
- 藉由編輯 `/etc/php/7.0/mods-available/pgsql.ini` 組態檔，啟用 **pgsql** 擴充功能。 組態檔應包含具有 `extension=php_pgsql.so` 文字的一行。 如果未顯示，請新增文字並儲存檔案。 如果此文字存在，但以分號前置詞標註，請藉由移除分號來取消註解文字。

### <a name="macos"></a>MacOS
- 下載 [PHP 7.1.4 版本](https://secure.php.net/downloads.php)
- 安裝 PHP 並參考 [PHP 手冊](https://secure.php.net/manual/install.macosx.php)以便進一步設定

## <a name="get-connection-information"></a>取得連線資訊
取得連線到 Azure Database for PostgreSQL 所需的連線資訊。 您需要完整的伺服器名稱和登入認證。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從 Azure 入口網站的左側功能表中，按一下 [所有資源]，然後搜尋您所建立的伺服器 (例如 **mydemoserver** )。
3. 按一下伺服器名稱。
4. 從伺服器的 [概觀] 面板，記下 [伺服器名稱] 和 [伺服器管理員登入名稱]。 如果您忘記密碼，您也可以從此面板重設密碼。
 :::image type="content" source="./media/connect-php/1-connection-string.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫伺服器名稱":::

## <a name="connect-and-create-a-table"></a>連線及建立資料表
使用下列程式碼搭配 **CREATE TABLE** SQL 陳述式 (後面接著 **INSERT INTO** SQL 陳述式) 來連線和建立資料表，進而將資料列新增至資料表中。

程式碼會呼叫 [pg_connect()](https://secure.php.net/manual/en/function.pg-connect.php) 方法以連線至 Azure Database for PostgreSQL。 然後它會呼叫 [pg_query()](https://secure.php.net/manual/en/function.pg-query.php) 方法數次來執行數個命令，以及呼叫 [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php) 來檢查詳細資料，是否每次都會發生錯誤。 然後它會呼叫 [pg_close()](https://secure.php.net/manual/en/function.pg-close.php) 方法來關閉連線。

以您自己的值取代 `$host`、`$database`、`$user` 和 `$password` 參數。 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password") 
        or die("Failed to create connection to database: ". pg_last_error(). "<br/>");
    print "Successfully created connection to database.<br/>";

    // Drop previous table of same name if one exists.
    $query = "DROP TABLE IF EXISTS inventory;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    print "Finished dropping table (if existed).<br/>";

    // Create table.
    $query = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    print "Finished creating table.<br/>";

    // Insert some data into table.
    $name = '\'banana\'';
    $quantity = 150;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($1, $2);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");

    $name = '\'orange\'';
    $quantity = 154;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($name, $quantity);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");

    $name = '\'apple\'';
    $quantity = 100;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($name, $quantity);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error()). "<br/>";

    print "Inserted 3 rows of data.<br/>";

    // Closing connection
    pg_close($connection);
?>
```

## <a name="read-data"></a>讀取資料
使用下列程式碼搭配 **SELECT** SQL 陳述式來連線和讀取資料。 

 程式碼會呼叫 [pg_connect()](https://secure.php.net/manual/en/function.pg-connect.php) 方法以連線至 Azure Database for PostgreSQL。 然後它會呼叫 [pg_query()](https://secure.php.net/manual/en/function.pg-query.php) 方法來執行 SELECT 命令，並將結果保留在結果集中，以及呼叫 [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php) 來檢查詳細資料，是否發生錯誤。  若要讀取結果集，則會在迴圈中呼叫 [pg_fetch_row()](https://secure.php.net/manual/en/function.pg-fetch-row.php) 方法 (每列一次)，並在 `$row` 陣列中擷取資料列資料，而每個陣列位置中的每個資料行都有一個資料值。  若要釋出結果集，則會呼叫 [pg_free_result()](https://secure.php.net/manual/en/function.pg-free-result.php)。 然後它會呼叫 [pg_close()](https://secure.php.net/manual/en/function.pg-close.php) 方法來關閉連線。

以您自己的值取代 `$host`、`$database`、`$user` 和 `$password` 參數。 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";
    
    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
                or die("Failed to create connection to database: ". pg_last_error(). "<br/>");

    print "Successfully created connection to database. <br/>";

    // Perform some SQL queries over the connection.
    $query = "SELECT * from inventory";
    $result_set = pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    while ($row = pg_fetch_row($result_set))
    {
        print "Data row = ($row[0], $row[1], $row[2]). <br/>";
    }

    // Free result_set
    pg_free_result($result_set);

    // Closing connection
    pg_close($connection);
?>
```

## <a name="update-data"></a>更新資料
使用下列程式碼搭配 **UPDATE** SQL 陳述式來連線和更新資料。

程式碼會呼叫 [pg_connect()](https://secure.php.net/manual/en/function.pg-connect.php) 方法以連線至 Azure Database for PostgreSQL。 然後它會呼叫 [pg_query()](https://secure.php.net/manual/en/function.pg-query.php) 方法來執行命令，以及呼叫 [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php) 來檢查詳細資料，是否會發生錯誤。 然後它會呼叫 [pg_close()](https://secure.php.net/manual/en/function.pg-close.php) 方法來關閉連線。

以您自己的值取代 `$host`、`$database`、`$user` 和 `$password` 參數。 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
                or die("Failed to create connection to database: ". pg_last_error(). ".<br/>");

    print "Successfully created connection to database. <br/>";

    // Modify some data in table.
    $new_quantity = 200;
    $name = '\'banana\'';
    $query = "UPDATE inventory SET quantity = $new_quantity WHERE name = $name;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). ".<br/>");
    print "Updated 1 row of data. </br>";

    // Closing connection
    pg_close($connection);
?>
```


## <a name="delete-data"></a>刪除資料
使用下列程式碼搭配 **DELETE** SQL 陳述式來連線和讀取資料。 

 程式碼會呼叫 [pg_connect()](https://secure.php.net/manual/en/function.pg-connect.php) 方法以連線至 Azure Database for PostgreSQL。 然後它會呼叫 [pg_query()](https://secure.php.net/manual/en/function.pg-query.php) 方法來執行命令，以及呼叫 [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php) 來檢查詳細資料，是否會發生錯誤。 然後它會呼叫 [pg_close()](https://secure.php.net/manual/en/function.pg-close.php) 方法來關閉連線。

以您自己的值取代 `$host`、`$database`、`$user` 和 `$password` 參數。 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
            or die("Failed to create connection to database: ". pg_last_error(). ". </br>");

    print "Successfully created connection to database. <br/>";

    // Delete some data from table.
    $name = '\'orange\'';
    $query = "DELETE FROM inventory WHERE name = $name;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). ". <br/>");
    print "Deleted 1 row of data. <br/>";

    // Closing connection
    pg_close($connection);
?>
```

## <a name="clean-up-resources"></a>清除資源

若要清除在此快速入門期間使用的所有資源，請使用下列命令刪除資源群組：

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用匯出和匯入來移轉資料庫](./howto-migrate-using-export-and-import.md)
