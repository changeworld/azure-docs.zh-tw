---
title: 使用重新導向進行連接-適用於 MariaDB 的 Azure 資料庫
description: 本文說明如何設定您的應用程式，以透過重新導向連接到適用於 MariaDB 的 Azure 資料庫。
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 6/8/2020
ms.openlocfilehash: 29f2ab8f70af2ca19cbffcbba6db39c5a93445fe
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664897"
---
# <a name="connect-to-azure-database-for-mariadb-with-redirection"></a>使用重新導向連接到適用於 MariaDB 的 Azure 資料庫

本主題說明如何使用重新導向模式，將應用程式連線到您的適用於 MariaDB 的 Azure 資料庫伺服器。 重新導向的目標是讓應用程式直接連接到後端伺服器節點，以降低用戶端應用程式與適用于 mariadb 伺服器之間的網路延遲。

## <a name="before-you-begin"></a>開始之前
登入 [Azure 入口網站](https://portal.azure.com)。 建立具有引擎版本10.2 或10.3 的適用於 MariaDB 的 Azure 資料庫伺服器。 

如需詳細資訊，請參閱如何使用 [Azure 入口網站](quickstart-create-mariadb-server-database-using-azure-portal.md) 或 [Azure CLI](quickstart-create-mariadb-server-database-using-azure-cli.md)建立適用於 MariaDB 的 Azure 資料庫的伺服器。

## <a name="enable-redirection"></a>啟用重新導向

在適用於 MariaDB 的 Azure 資料庫伺服器上，將 `redirect_enabled` 參數設定為， `ON` 以允許具有重新導向模式的連接。 若要更新此伺服器參數，請使用 [Azure 入口網站](howto-server-parameters.md) 或 [Azure CLI](howto-configure-server-parameters-cli.md)。

## <a name="php"></a>PHP

PHP 應用程式中的重新導向支援可透過 Microsoft 所開發的 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) \(英文\) 延伸模組來取得。 

Mysqlnd_azure 延伸模組可透過 PECL 新增至 PHP 應用程式，因此強烈建議透過正式發行的 [PECL 套件](https://pecl.php.net/package/mysqlnd_azure) \(英文\) 來安裝並設定延伸模組。

> [!IMPORTANT]
> PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) \(英文\) 延伸模組中的重新導向支援目前為預覽狀態。

### <a name="redirection-logic"></a>重新導向邏輯

>[!IMPORTANT]
> 重新導向邏輯/行為開始版本 1.1.0 已更新，**建議使用版本 1.1.0+** 。

重新導向行為取決於 `mysqlnd_azure.enableRedirect` 的值。 下表根據此參數的值 (從 **版本 1.1.0+** 開始)，列出重新導向的行為。

如果您使用較舊版本的 mysqlnd_azure 延伸模組 (1.0.0-1.0.3 版)，則重新導向行為取決於 `mysqlnd_azure.enabled` 的值。 有效值為 `off` (與下表中所述的行為類似) 和 `on` (作用如下表中的 `preferred`)。  

|**mysqlnd_azure.enableRedirect 值**| **行為**|
|----------------------------------------|-------------|
|`off` 或 `0`|將不會使用重新導向。 |
|`on` 或 `1`|- 如果連線在驅動程式端不使用 SSL，則不會建立連線。 將傳回下列錯誤：[mysqlnd_azure.enableRedirect 是 on，但連接字串中未設定 SSL 選項。只有使用 SSL 才能重新導向。]<br>-如果在驅動程式端使用 SSL，但是伺服器上不支援重新導向，則第一個連接會中止，並傳回下列錯誤：「*因為適用于 mariadb 伺服器上未啟用重新導向，或網路套件不符合重新導向通訊協定，所以連線已中止。* 」<br>-如果適用于 mariadb 伺服器支援重新導向，但重新導向的連接因任何原因而失敗，也請中止第一個 proxy 連線。 傳回重新導向連線的錯誤。|
|`preferred` 或 `2`<br> (預設值)|- mysqlnd_azure 會盡可能使用重新導向。<br>- 如果連線在驅動程式端不使用 SSL，則伺服器不支援重新導向，或者當重新導向因任何不嚴重原因而無法連線，但 Proxy 連線仍然有效時，系統會切換回第一個 Proxy 連線。|

文件的後續章節將會概述如何使用 PECL 安裝 `mysqlnd_azure` 延伸模組，並設定此參數的值。

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Prerequisites 
- PHP 版本 7.2.15+ 和 7.3.2+
- PHP PEAR 
- php-mysql
- 適用於 MariaDB 的 Azure 資料庫伺服器

1. 使用 [PECL](https://pecl.php.net/package/mysqlnd_azure) \(英文\) 安裝 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) \(英文\)。 建議使用版本 1.1.0+。

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. 藉由執行下面命令，來找出延伸模組目錄 (`extension_dir`)：

    ```bash
    php -i | grep "extension_dir"
    ```

3. 將目錄變更為傳回的資料夾，並確定 `mysqlnd_azure.so` 位於此資料夾中。 

4. 執行下面命令，找出 .ini 檔案的資料夾： 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. 將目錄變更為此傳回資料夾。 

6. 建立 `mysqlnd_azure` 的新 .ini 檔案。 請確定名稱的字母順序是在 mysqnld 之後，因為模組是根據 ini 檔案的名稱順序載入。 例如，如果 `mysqlnd` .ini 的名稱是 `10-mysqlnd.ini`，請將 mysqlnd ini 命名為 `20-mysqlnd-azure.ini`。

7. 在新的 .ini 檔案中，新增下列幾行以啟用重新導向。

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Prerequisites 
- PHP 版本 7.2.15+ 和 7.3.2+
- php-mysql
- 適用於 MariaDB 的 Azure 資料庫伺服器

1. 藉由執行下列命令來判斷您正在執行 PHP 的 x64 或 x86 版本：

    ```cmd
    php -i | findstr "Thread"
    ```

2. 從符合您 PHP 版本的 [PECL](https://pecl.php.net/package/mysqlnd_azure) \(英文\) 下載對應的 x64 或 x86 版本的 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) \(英文\) DLL。 建議使用版本 1.1.0+。

3. 將 ZIP 檔案解壓縮並找出名為 `php_mysqlnd_azure.dll` 的 DLL。

4. 藉由執行下面命令，來找出延伸模組目錄 (`extension_dir`)：

    ```cmd
    php -i | find "extension_dir"
    ```

5. 將 `php_mysqlnd_azure.dll` 檔案複製到步驟 4 中傳回的目錄。 

6. 使用下列命令，找出包含 `php.ini` 檔案的 PHP 資料夾：

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. 修改 `php.ini` 檔案並新增下列額外幾行，以啟用重新導向。 

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

您也可以使用下列範例 PHP 程式碼來確認重新導向。 建立名為 `mysqlConnect.php` 的 PHP 檔案，並貼上下面的程式碼。 以您自己的伺服器名稱、使用者名稱和密碼加以更新。 
 
 ```php
<?php
$host = '<yourservername>.mariadb.database.azure.com';
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
