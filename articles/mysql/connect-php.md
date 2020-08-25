---
title: 快速入門：使用 PHP 連線 - 適用於 MySQL 的 Azure 資料庫
description: 本快速入門提供數個 PHP 程式碼範例，您可用於從 Azure Database for MySQL 連線及查詢資料。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: ec4b4d705e6cc8b9cfc26952083292b74b3d4abf
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185871"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql"></a>快速入門：使用 PHP 來連線及查詢適用於 MySQL 的 Azure 資料庫中的資料
本快速入門示範如何使用 [PHP](https://secure.php.net/manual/intro-whatis.php) 應用程式來連線到 Azure Database for MySQL。 它會顯示如何使用 SQL 陳述式來查詢、插入、更新和刪除資料庫中的資料。 本主題假設您已熟悉使用 PHP 進行開發，但不熟悉適用於 MySQL 的 Azure 資料庫。

## <a name="prerequisites"></a>Prerequisites
本快速入門使用在以下任一指南中建立的資源作為起點︰
- [使用 Azure 入口網站建立適用於 MySQL 的 Azure 資料庫伺服器](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [使用 Azure CLI 建立適用於 MySQL 的 Azure 資料庫伺服器](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT] 
> 確保您用於連線的 IP 位址已使用 [Azure 入口網站](./howto-manage-firewall-using-portal.md)或 [Azure CLI](./howto-manage-firewall-using-cli.md) 新增伺服器的防火牆規則

## <a name="install-php"></a>安裝 PHP
在自己的伺服器上安裝 PHP，或建立 Azure [Web 應用程式](../app-service/overview.md) (包括 PHP)。

### <a name="macos"></a>MacOS
- 下載 [PHP 7.1.4 版本](https://secure.php.net/downloads.php)。
- 安裝 PHP 並參考 [PHP 手冊](https://secure.php.net/manual/install.macosx.php)以便進一步設定。

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
- 下載 [PHP 7.1.4 非執行緒安全 (x64) 版本](https://secure.php.net/downloads.php)。
- 安裝 PHP 並參考 [PHP 手冊](https://secure.php.net/manual/install.unix.php)以便進一步設定。

### <a name="windows"></a>Windows
- 下載 [PHP 7.1.4 非執行緒安全 (x64) 版本](https://windows.php.net/download#php-7.1)。
- 安裝 PHP 並參考 [PHP 手冊](https://secure.php.net/manual/install.windows.php)以便進一步設定。

## <a name="get-connection-information"></a>取得連線資訊
取得連線到 Azure Database for MySQL 所需的連線資訊。 您需要完整的伺服器名稱和登入認證。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從 Azure 入口網站的左側功能表中，按一下 [所有資源]，然後搜尋您所建立的伺服器 (例如 **mydemoserver**)。
3. 按一下伺服器名稱。
4. 從伺服器的 [概觀] 面板，記下 [伺服器名稱] 和 [伺服器管理員登入名稱]。 如果您忘記密碼，您也可以從此面板重設密碼。
 ![Azure Database for MySQL 伺服器名稱](./media/connect-php/1_server-overview-name-login.png)

## <a name="connect-and-create-a-table"></a>連線及建立資料表
使用下列程式碼搭配 **CREATE TABLE** SQL 陳述式來連線和建立資料表。 

程式碼會使用 PHP 內含的 **MySQL 改良擴充功能** (mysqli) 類別。 程式碼會呼叫 [mysqli_init](https://secure.php.net/manual/mysqli.init.php) 和 [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) 方法來連線到 MySQL。 然後它會呼叫 [mysqli_query](https://secure.php.net/manual/mysqli.query.php) 方法來執行查詢。 然後它會呼叫 [mysqli_close](https://secure.php.net/manual/mysqli.close.php) 方法來關閉連線。

以自己的值取代主機、使用者名稱、密碼和 db_name 參數。 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="insert-data"></a>插入資料
使用下列程式碼搭配 **INSERT** SQL 陳述式來連線和插入資料。

程式碼會使用 PHP 內含的 **MySQL 改良擴充功能** (mysqli) 類別。 程式碼會使用 [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) 方法來建立已備妥的 insert 陳述式，然後使用 [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) 方法來繫結每個插入資料行值的參數。 程式碼會使用 [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) 方法執行陳述式，之後使用 [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) 方法關閉陳述式。

以自己的值取代主機、使用者名稱、密碼和 db_name 參數。 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)")) {
mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
mysqli_stmt_execute($stmt);
printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

// Close the connection
mysqli_close($conn);
?>
```

## <a name="read-data"></a>讀取資料
使用下列程式碼搭配 **SELECT** SQL 陳述式來連線和讀取資料。  程式碼會使用 PHP 內含的 **MySQL 改良擴充功能** (mysqli) 類別。 程式碼會使用 [mysqli_query](https://secure.php.net/manual/mysqli.query.php) 方法執行 SQL 查詢，並使用 [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) 方法來擷取結果資料列。

以自己的值取代主機、使用者名稱、密碼和 db_name 參數。 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res)) {
var_dump($row);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="update-data"></a>更新資料
使用下列程式碼搭配 **UPDATE** SQL 陳述式來連線和更新資料。

程式碼會使用 PHP 內含的 **MySQL 改良擴充功能** (mysqli) 類別。 程式碼會使用 [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) 方法來建立已備妥的 update 陳述式，然後使用 [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) 方法來繫結每個更新資料行值的參數。 程式碼會使用 [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) 方法執行陳述式，之後使用 [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) 方法關閉陳述式。

以自己的值取代主機、使用者名稱、密碼和 db_name 參數。 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Update statement
$product_name = 'BrandNewProduct';
$new_product_price = 15.1;
if ($stmt = mysqli_prepare($conn, "UPDATE Products SET Price = ? WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 'ds', $new_product_price, $product_name);
mysqli_stmt_execute($stmt);
printf("Update: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));

//Close the connection
mysqli_stmt_close($stmt);
}

mysqli_close($conn);
?>
```


## <a name="delete-data"></a>刪除資料
使用下列程式碼搭配 **DELETE** SQL 陳述式來連線和讀取資料。 

程式碼會使用 PHP 內含的 **MySQL 改良擴充功能** (mysqli) 類別。 程式碼會使用 [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) 方法來建立已備妥的 delete 陳述式，然後使用 [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) 方法來繫結陳述式中 where 子句的參數。 程式碼會使用 [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) 方法執行陳述式，之後使用 [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) 方法關閉陳述式。

以自己的值取代主機、使用者名稱、密碼和 db_name 參數。 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [透過 SSL 連線到適用於 MySQL 的 Azure 資料庫](howto-configure-ssl.md)
