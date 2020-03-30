---
title: 使用重定向連接 - MySQL 的 Azure 資料庫
description: 本文介紹如何配置應用程式以通過重定向連接到 MySQL 的 Azure 資料庫。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246328"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>通過重定向連接到 MySQL 的 Azure 資料庫

本主題介紹如何使用重定向模式連接 MySQL 伺服器的應用程式。 重定向旨在通過允許應用程式直接連接到後端伺服器節點來減少用戶端應用程式和 MySQL 伺服器之間的網路延遲。

## <a name="before-you-begin"></a>開始之前
登錄到 Azure[門戶](https://portal.azure.com)。 為具有引擎版本 5.6、5.7 或 8.0 的 MySQL 伺服器創建 Azure 資料庫。 如需詳細資訊，請參閱[如何從入口網站建立適用於 MySQL 伺服器的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-portal.md)或[如何使用 CLI 建立適用於 MySQL 伺服器的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-cli.md)。

當前僅在 MySQL 伺服器的 Azure 資料庫**上啟用 SSL**時才支援重定向。 如需如何設定 SSL 的詳細資訊，請參閱[使用 SSL 與適用於 MySQL 的 Azure 資料庫](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure)。

## <a name="php"></a>PHP

通過 Microsoft 開發的[mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure)擴展，可以支援 PHP 應用程式中的重定向。 

mysqlnd_azure擴展可以通過PECL添加到PHP應用程式，強烈建議通過正式發佈的[PECL包](https://pecl.php.net/package/mysqlnd_azure)安裝和配置擴展。

> [!IMPORTANT]
> 對 PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure)擴展中重定向的支援當前處於預覽狀態。

### <a name="redirection-logic"></a>重定向邏輯

>[!IMPORTANT]
> 重定向邏輯/行為開始版本 1.1.0 已更新 **，建議使用版本 1.1.0+**。

重定向行為由 的值`mysqlnd_azure.enableRedirect`確定。 下表基於版本**1.1.0+** 中此參數的值概述了重定向的行為。

如果使用舊版本的mysqlnd_azure擴展版（版本 1.0.0-1.0.3），重定向行為由`mysqlnd_azure.enabled`的值確定。 有效值是`off`（作用類似于下表中概述的行為）和`on`（如下`preferred`表所示）。  

|**mysqlnd_azure.啟用重定向值**| **行為**|
|----------------------------------------|-------------|
|`off` 或 `0`|不會使用重定向。 |
|`on` 或 `1`|- 如果 MySQL 伺服器的 Azure 資料庫未啟用 SSL，則不會進行連接。 將返回以下錯誤 *："mysqlnd_azure.enableredirect 處於打開，但連接字串中未設置 SSL 選項。只有使用 SSL 才能重定向。*<br>- 如果在 MySQL 伺服器上啟用了 SSL，但伺服器上不支援重定向，則第一個連接將中止，並返回以下錯誤 *："連接已中止，因為 MySQL 伺服器上未啟用重定向或網路包不符合重定向協定。*<br>- 如果 MySQL 伺服器支援重定向，但重定向的連接由於任何原因失敗，則也會中止第一個代理連接。 返回重定向連接的錯誤。|
|`preferred` 或 `2`<br> (預設值)|- mysqlnd_azure將盡可能使用重定向。<br>- 如果連接不使用 SSL，伺服器不支援重定向，或者重定向的連接由於任何非致命原因無法連接，而代理連接仍然是有效的連接，它將回落到第一個代理連接。|

本文檔的後續部分將概述如何使用 PECL 安裝`mysqlnd_azure`擴展並設置此參數的值。

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Prerequisites 
- PHP 版本 7.2.15° 和 7.3.2°
- PHP PEAR 
- php-mysql
- 啟用 SSL 的 MySQL 伺服器的 Azure 資料庫

1. 使用[PECL](https://pecl.php.net/package/mysqlnd_azure)安裝[mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 。 建議使用版本 1.1.0+。

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. 通過運行以下操作找到`extension_dir`擴展目錄 （ ）：

    ```bash
    php -i | grep "extension_dir"
    ```

3. 將目錄更改為返回的資料夾，並確保`mysqlnd_azure.so`位於此資料夾中。 

4. 通過運行以下功能找到 .ini 檔案的資料夾： 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. 將目錄更改為此返回的資料夾。 

6. 為`mysqlnd_azure`創建新 .ini 檔案。 確保名稱的字母順序位於 mysqnld 之後，因為模組是根據 ini 檔的名稱順序載入的。 例如，如果`mysqlnd`名為`10-mysqlnd.ini`.ini，則將 mysqlnd ini`20-mysqlnd-azure.ini`命名為 。

7. 在新的 .ini 檔案中，添加以下行以啟用重定向。

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Prerequisites 
- PHP 版本 7.2.15° 和 7.3.2°
- php-mysql
- 啟用 SSL 的 MySQL 伺服器的 Azure 資料庫

1. 通過運行以下命令確定是否運行 x64 或 x86 版本的 PHP：

    ```cmd
    php -i | findstr "Thread"
    ```

2. 從[PECL](https://pecl.php.net/package/mysqlnd_azure)下載與您的 PHP 版本的匹配的[相應 x64](https://github.com/microsoft/mysqlnd_azure)或 x86 版本的mysqlnd_azure DLL。 建議使用版本 1.1.0+。

3. 提取 ZIP 檔案並找到名為`php_mysqlnd_azure.dll`的 DLL。

4. 通過運行以下命令找到`extension_dir`擴展目錄 （ ）：

    ```cmd
    php -i | find "extension_dir"
    ```

5. 將`php_mysqlnd_azure.dll`檔案複製到步驟 4 中返回的目錄中。 

6. 使用以下命令查找包含`php.ini`檔的 PHP 資料夾：

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. 修改`php.ini`檔並添加以下額外行以啟用重定向。 

    在"動態擴展"部分下： 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    在"模組設置"部分下：     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>確認重定向

您還可以確認重定向配置了下面的 PHP 代碼示例。 創建一個稱為`mysqlConnect.php`PHP 檔的 PHP 檔，並粘貼以下代碼。 使用您自己的伺服器名稱、使用者名和密碼補救伺服器名稱、使用者名和密碼。 
 
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
有關連接字串的詳細資訊，請參閱[連接字串](howto-connection-string.md)。
