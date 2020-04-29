---
title: 使用重新導向來連接-適用於 MySQL 的 Azure 資料庫
description: 本文說明如何設定應用程式，以使用重新導向連接到適用於 MySQL 的 Azure 資料庫。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246328"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>使用重新導向連接到適用於 MySQL 的 Azure 資料庫

本主題說明如何使用重新導向模式將應用程式連線到您的適用於 MySQL 的 Azure 資料庫伺服器。 重新導向的目標是允許應用程式直接連線到後端伺服器節點，以減少用戶端應用程式和 MySQL 伺服器之間的網路延遲。

## <a name="before-you-begin"></a>開始之前
登入 [Azure 入口網站](https://portal.azure.com)。 建立具有引擎版本5.6、5.7 或8.0 的適用於 MySQL 的 Azure 資料庫伺服器。 如需詳細資訊，請參閱[如何從入口網站建立適用於 MySQL 伺服器的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-portal.md)或[如何使用 CLI 建立適用於 MySQL 伺服器的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-cli.md)。

目前只有當您的適用於 MySQL 的 Azure 資料庫伺服器上**已啟用 SSL**時，才支援重新導向。 如需如何設定 SSL 的詳細資訊，請參閱[使用 SSL 與適用於 MySQL 的 Azure 資料庫](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure)。

## <a name="php"></a>PHP

PHP 應用程式中的重新導向支援可透過 Microsoft 所開發的[mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure)延伸模組取得。 

Mysqlnd_azure 擴充功能可透過 PECL 新增至 PHP 應用程式，因此強烈建議透過正式發行的[PECL 套件](https://pecl.php.net/package/mysqlnd_azure)來安裝和設定擴充功能。

> [!IMPORTANT]
> PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure)延伸模組中的重新導向支援目前為預覽狀態。

### <a name="redirection-logic"></a>重新導向邏輯

>[!IMPORTANT]
> 重新導向邏輯/行為開始版本1.1.0 已更新，**建議使用版本 1.1.0 +**。

重新導向行為是由的值決定`mysqlnd_azure.enableRedirect`。 下表根據此參數的值（從**版本 1.1.0 +** 開始），列出重新導向的行為。

如果您使用較舊版本的 mysqlnd_azure 延伸模組（1.0.3 版），則重新導向行為取決於的值`mysqlnd_azure.enabled`。 有效值為`off` （其作用類似下表中所述的行為）和`on` （ `preferred`如下表所示）。  

|**mysqlnd_azure enableRedirect 值**| **行為**|
|----------------------------------------|-------------|
|`off` 或 `0`|將不會使用重新導向。 |
|`on` 或 `1`|-如果適用於 MySQL 的 Azure 資料庫伺服器上未啟用 SSL，則不會進行任何連接。 將傳回下列錯誤：「 *Mysqlnd_azure enableRedirect 已開啟，但是連接字串中未設定 SSL 選項。只有 SSL 可以*重新導向。」<br>-如果 MySQL 伺服器上已啟用 SSL，但伺服器不支援重新導向，則會中止第一個連接，並傳回下列錯誤：「連線已*中止，因為 MySQL 伺服器上未啟用重新導向，或網路套件不符合重新導向通訊協定」。*<br>-如果 MySQL 伺服器支援重新導向，但因為任何原因而導致重新導向連接失敗，也請中止第一個 proxy 連線。 傳回重新導向連接的錯誤。|
|`preferred` 或 `2`<br> (預設值)|-mysqlnd_azure 會盡可能使用重新導向。<br>-如果連線不使用 SSL，則伺服器不支援重新導向，或者當 proxy 連線仍然是有效的時，重新導向的連線無法連線，而是會切換回第一個 proxy 連接。|

檔的後續章節將概述如何使用 PECL 安裝`mysqlnd_azure`延伸模組，並設定此參數的值。

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>先決條件 
- PHP 版本 7.2.15 + 和 7.3.2 +
- PHP 梨 
- php-mysql
- 已啟用 SSL 的適用於 MySQL 的 Azure 資料庫伺服器

1. 使用[PECL](https://pecl.php.net/package/mysqlnd_azure)安裝[mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 。 建議使用版本 1.1.0 +。

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. 執行下列程式，找`extension_dir`出延伸模組目錄（）：

    ```bash
    php -i | grep "extension_dir"
    ```

3. 將目錄變更為傳回的資料夾， `mysqlnd_azure.so`並確定位於此資料夾中。 

4. 執行下列程式，找出 .ini 檔案的資料夾： 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. 將目錄變更為這個傳回的資料夾。 

6. 為`mysqlnd_azure`建立新的 .ini 檔案。 請確定名稱的字母順序是在 mysqnld 之後，因為模組是根據 ini 檔案的名稱順序載入。 例如，如果`mysqlnd` .ini 名`10-mysqlnd.ini`為，請將 mysqlnd ini 命名為。 `20-mysqlnd-azure.ini`

7. 在新的 .ini 檔案中，新增下列幾行以啟用重新導向。

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>先決條件 
- PHP 版本 7.2.15 + 和 7.3.2 +
- php-mysql
- 已啟用 SSL 的適用於 MySQL 的 Azure 資料庫伺服器

1. 藉由執行下列命令來判斷您是否正在執行 x64 或 x86 版本的 PHP：

    ```cmd
    php -i | findstr "Thread"
    ```

2. 從符合您 PHP 版本的[PECL](https://pecl.php.net/package/mysqlnd_azure)下載對應的 x64 或 x86 版本的[mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) DLL。 建議使用版本 1.1.0 +。

3. 解壓縮 zip 檔案，並尋找名為`php_mysqlnd_azure.dll`的 DLL。

4. 執行下列命令，找`extension_dir`出延伸模組目錄（）：

    ```cmd
    php -i | find "extension_dir"
    ```

5. `php_mysqlnd_azure.dll`將檔案複製到步驟4中傳回的目錄。 

6. 使用下列命令，找出`php.ini`包含檔案的 PHP 資料夾：

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. `php.ini`修改檔案並新增下列額外的行，以啟用重新導向。 

    在 [動態擴充] 區段底下： 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    在 [模組設定] 區段底下：     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>確認重新導向

您也可以使用下列範例 PHP 程式碼來確認重新導向。 建立名`mysqlConnect.php`為的 PHP 檔案，並貼上下列程式碼。 以您自己的名稱、使用者名稱和密碼來補救伺服器。 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
    $db->close();
  }
?>
 ```

## <a name="next-steps"></a>後續步驟
如需連接字串的詳細資訊，請參閱[連接字串](howto-connection-string.md)。
