---
title: 快速入門：使用 PHP 連線 - 適用於 MySQL 的 Azure 資料庫
description: 本快速入門提供數個 PHP 程式碼範例，您可用於從 Azure Database for MySQL 連線及查詢資料。
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: bf62eca26d846cc529df43b519b1377f88c8aede
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132897"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql"></a>快速入門：使用 PHP 來連線及查詢適用於 MySQL 的 Azure 資料庫中的資料
本快速入門示範如何使用 [PHP](https://secure.php.net/manual/intro-whatis.php) 應用程式來連線到 Azure Database for MySQL。 它會顯示如何使用 SQL 陳述式來查詢、插入、更新和刪除資料庫中的資料。

## <a name="prerequisites"></a>Prerequisites
在本快速入門中，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free)。
- 使用 [Azure 入口網站](./quickstart-create-mysql-server-database-using-azure-portal.md)建立適用於 MySQL 的 Azure 資料庫單一伺服器 <br/> 如果沒有，請使用 [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)。
- 根據您使用的是公用或私人存取，完成下列 **其中一項** 動作以啟用連線。

    |動作| 連線方法|操作指南|
    |:--------- |:--------- |:--------- |
    | **設定防火牆規則** | 公用 | [入口網站](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
    | **設定服務端點** | 公用 | [入口網站](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
    | **設定私人連結** | 私人 | [入口網站](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [建立資料庫和非管理使用者](./howto-create-users.md?tabs=single-server)
- 為您的作業系統安裝最新的 PHP 版本
    - [macOS 上的 PHP](https://secure.php.net/manual/install.macosx.php)
    - [Linux 上的 PHP](https://secure.php.net/manual/install.unix.php)
    - [Windows 上的 PHP](https://secure.php.net/manual/install.windows.php)

> [!NOTE]
> 在本快速入門中，我們使用 [MySQLi](https://www.php.net/manual/en/book.mysqli.php) 程式庫來管理連線及查詢伺服器。

## <a name="get-connection-information"></a>取得連線資訊
您可以依照下列步驟，從 Azure 入口網站取得資料庫伺服器連線資訊：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 瀏覽至 [適用於 MySQL 的 Azure 資料庫] 頁面。 您可以搜尋並選取 [適用於 MySQL 的 Azure 資料庫]。
:::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="尋找適用於 MySQL 的 Azure 資料庫":::

2. 選取您的 MySQL 伺服器 (例如 **mydemoserver**)。
3. 在 [概觀] 頁面上，複製 [伺服器名稱] 旁的完整伺服器名稱，以及 [伺服器管理員登入名稱] 旁的管理使用者名稱。 若要複製伺服器名稱或主機名稱，請將滑鼠暫留在其上方，然後選取 [複製] 圖示。

> [!IMPORTANT]
> - 如果您忘記密碼，可以[重設密碼](./howto-create-manage-server-portal.md#update-admin-password)。
> - 請將 **主機、使用者名稱、密碼** 和 **db_name** 參數取代為您自己的值**

## <a name="step-1-connect-to-the-server"></a>步驟 1：連接到伺服器
依預設會啟用 SSL。 您可能需要下載 [DigiCertGlobalRootG2 SSL 憑證](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)，才能從本機環境連線。 此程式碼會呼叫：
- [mysqli_init](https://secure.php.net/manual/mysqli.init.php)，以初始化 MySQLi。
- [mysqli_ssl_set](https://www.php.net/manual/en/mysqli.ssl-set.php)，以指向 SSL 憑證路徑。 這對於您的本機環境而言是必要的，但 App Service Web 應用程式或 Azure 虛擬機器則不需要。
- [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php)，以連線至 MySQL。
- [mysqli_close](https://secure.php.net/manual/mysqli.close.php)，以關閉連線。


```php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Initializes MySQLi
$conn = mysqli_init();

mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootG2.crt.pem", NULL, NULL);

// Establish the connection
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, NULL, MYSQLI_CLIENT_SSL);

//If connection failed, show the error
if (mysqli_connect_errno($conn))
{
    die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
[有任何問題嗎？請告訴我們](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-create-a-table"></a>步驟 2:建立資料表
使用下列程式碼進行連線。 此程式碼會呼叫：
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php)，以執行查詢。
```php
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
```

## <a name="step-3-insert-data"></a>步驟 3：插入資料
使用下列程式碼搭配 **INSERT** SQL 陳述式來插入資料。 此程式碼會使用下列方法：
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php)，用以建立備妥的 insert 陳述式
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php)，為每個插入的資料行值繫結參數。
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php)，以使用方法關閉陳述式


```php
//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)"))
{
    mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
    mysqli_stmt_execute($stmt);
    printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
    mysqli_stmt_close($stmt);
}

```

## <a name="step-4-read-data"></a>步驟 4：讀取資料
使用下列程式碼搭配 **SELECT** SQL 陳述式來讀取資料。  此程式碼會使用下列方法：
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php)，用以執行 **SELECT** 查詢
- [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php)，用以擷取產生的資料列。

```php
//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res))
 {
    var_dump($row);
 }

```


## <a name="step-5-delete-data"></a>步驟 5：刪除資料
使用 **DELETE** SQL 陳述式，以使用下列程式碼刪除資料列。 此程式碼會使用下列方法：
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php)，用以建立備妥的 delete 陳述式
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php)，用以繫結參數
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)，用以執行備妥的 delete 陳述式
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php)，用以關閉陳述式

```php
//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}
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
> [使用入口網站管理適用於 MySQL 的 Azure 資料庫伺服器](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [使用 CLI 管理適用於 MySQL 的 Azure 資料庫伺服器](./how-to-manage-single-server-cli.md)

[找不到您要找的項目嗎？請告訴我們。](https://aka.ms/mysql-doc-feedback)
