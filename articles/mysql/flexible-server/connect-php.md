---
title: 快速入門：使用 PHP 連線 - 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器
description: 本快速入門提供數個 PHP 程式碼範例，您可用來從 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器連線和查詢資料。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: 77e4e2e1548beaa840f46953ef5bb4e94345416f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943626"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>快速入門：使用 PHP 來連線和查詢適用於 MySQL 的 Azure 資料庫 - 彈性伺服器中的資料

> [!IMPORTANT]
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。

本快速入門示範如何使用 [PHP](https://secure.php.net/manual/intro-whatis.php) 應用程式來連線到適用於 MySQL 的 Azure 資料庫彈性伺服器。 它會顯示如何使用 SQL 陳述式來查詢、插入、更新和刪除資料庫中的資料。 本文章假設您已熟悉使用 PHP 進行開發，但不熟悉適用於 MySQL 的 Azure 資料庫彈性伺服器。

## <a name="prerequisites"></a>Prerequisites
本快速入門使用在以下任一指南中建立的資源作為起點︰

- [使用 Azure 入口網站建立適用於 MySQL 的 Azure 資料庫彈性伺服器](./quickstart-create-server-portal.md)
- [使用 Azure CLI 建立適用於 MySQL 的 Azure 資料庫彈性伺服器](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>準備用戶端工作站
1. 如果您使用私人存取 (VNet 整合) 建立彈性伺服器，就必須從與伺服器相同的 VNet 中的資源連線到您的伺服器。 您可以建立虛擬機器，並新增至使用您彈性伺服器建立的 VNet。 請參閱[使用 Azure CLI 建立和管理適用於 MySQL 的 Azure 資料庫彈性伺服器虛擬網路](./how-to-manage-virtual-network-cli.md)。

2. 如果您使用公用存取 (允許的 IP 位址)建立彈性伺服器，可以將本機 IP 位址新增至伺服器上的防火牆規則清單。 請參閱[使用 Azure CLI 建立和管理適用於 MySQL 的 Azure 資料庫彈性伺服器防火牆規則](./how-to-manage-firewall-cli.md)。

### <a name="install-php"></a>安裝 PHP

在自己的伺服器上安裝 PHP，或建立 Azure [Web 應用程式](https://docs.microsoft.com/azure/app-service/overview) (包括 PHP)。  若要了解如何建立防火牆規則，請參閱[建立和管理防火牆規則](./how-to-manage-firewall-portal.md)。

#### <a name="macos"></a>macOS

1. 下載 [PHP 7.1.4 版本](https://secure.php.net/downloads.php)。
2. 安裝 PHP 並參考 [PHP 手冊](https://secure.php.net/manual/install.macosx.php)以便進一步設定。

#### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. 下載 [PHP 7.1.4 非執行緒安全 (x64) 版本](https://secure.php.net/downloads.php)。
2. 安裝 PHP 並參考 [PHP 手冊](https://secure.php.net/manual/install.unix.php)以便進一步設定。

#### <a name="windows"></a>Windows

1. 下載 [PHP 7.1.4 非執行緒安全 (x64) 版本](https://windows.php.net/download#php-7.1)。
2. 安裝 PHP 並參考 [PHP 手冊](https://secure.php.net/manual/install.windows.php)以便進一步設定。

## <a name="get-connection-information"></a>取得連線資訊

取得連線到適用於 MySQL 的 Azure 資料庫彈性伺服器所需的連線資訊。 您需要完整的伺服器名稱和登入認證。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從 Azure 入口網站的左側功能表中，選取 [所有資源]，然後搜尋您所建立的伺服器 (例如 **mydemoserver**)。
3. 選取伺服器名稱。
4. 從伺服器的 [概觀] 面板，記下 [伺服器名稱] 和 [伺服器管理員登入名稱]。 如果您忘記密碼，您也可以從此面板重設密碼。
 <!---:::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connecting-to-flexible-server-using-tlsssl-in-php"></a>使用 PHP 中的 TLS/SSL 連線到彈性伺服器

若要從您的彈性伺服器透過 TLS/SSL 從應用程式建立加密連線，請參閱下列程式碼範例。 您可以從 [https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) 下載透過 TLS/SSL 進行通訊所需的憑證

```php using SSL
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

## <a name="connect-and-create-a-table"></a>連線及建立資料表

使用下列程式碼搭配 **CREATE TABLE** SQL 陳述式來連線和建立資料表。

程式碼會使用 PHP 內含的 **MySQL 改良擴充功能** (mysqli) 類別。 程式碼會呼叫 [mysqli_init](https://secure.php.net/manual/mysqli.init.php) 和 [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) 方法來連線到 MySQL。 然後它會呼叫 [mysqli_query](https://secure.php.net/manual/mysqli.query.php) 方法來執行查詢。 然後它會呼叫 [mysqli_close](https://secure.php.net/manual/mysqli.close.php) 方法來關閉連線。

以自己的值取代主機、使用者名稱、密碼和 db_name 參數。

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
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
$username = 'myadmin';
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
$username = 'myadmin';
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
$username = 'myadmin';
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
$username = 'myadmin';
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
- [在適用於 MySQL 的 Azure 資料庫 - 彈性伺服器中使用傳輸層安全性 (TLS 1.2) 的加密連線](./how-to-connect-tls-ssl.md)。
- 深入了解[適用於 MySQL 的 Azure 資料庫彈性伺服器中的網路功能](./concepts-networking.md)。
- [使用 Azure 入口網站建立和管理適用於 MySQL 的 Azure 資料庫彈性伺服器防火牆規則](./how-to-manage-firewall-portal.md)。
- [使用 Azure 入口網站建立和管理適用於 MySQL 的 Azure 資料庫彈性伺服器虛擬網路](./how-to-manage-virtual-network-portal.md)。
