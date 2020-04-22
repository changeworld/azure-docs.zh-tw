---
title: 設定 PHP 應用
description: 瞭解如何為應用配置預建構的 PHP 容器。 本文說明最常見的設定工作。
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9e87466f810dc4ebf767c36ad74c358cbf6069e5
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758885"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>為 Azure 應用服務設定 Linux PHP 應用

本指南介紹如何在 Azure 應用服務中為 Web 應用、移動後端和 API 應用配置內建 PHP 執行時。

本指南為在應用服務中使用內建 Linux 容器的 PHP 開發人員提供了關鍵概念和說明。 如果您從未使用過 Azure 應用服務,請先使用 MySQL 教程,請先按照[PHP 快速入門](quickstart-php.md)和 PHP 進行[MySQL 教程](tutorial-php-mysql-app.md)。

## <a name="show-php-version"></a>顯示 PHP 版本

要顯示目前的 PHP 版本,在[雲殼](https://shell.azure.com)中執行以下命令:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

要顯示所有受支援的 PHP 版本,可在[雲端外殼](https://shell.azure.com)中執行以下命令:

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>設定 PHP 版本

在[雲殼](https://shell.azure.com)中執行以下指令,將 PHP 版本設定為 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>自訂建構自動化

如果使用開啟的 Git 或 zip 套件部署應用,應用服務將建構自動化步驟,執行以下順序:

1. 如果由`PRE_BUILD_SCRIPT_PATH`指定,則運行自訂文稿。
1. 執行 `php composer.phar install`。
1. 如果由`POST_BUILD_SCRIPT_PATH`指定,則運行自訂文稿。

`PRE_BUILD_COMMAND`和`POST_BUILD_COMMAND`是默認情況下為空的環境變數。 要執行預產生指令,請定義`PRE_BUILD_COMMAND`。 要執行產生後指令,請定義`POST_BUILD_COMMAND`。

下面的示例指定一系列命令的兩個變數,這些命令用逗號分隔。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

有關自訂產生自動化的其他環境變數,請參閱[Oryx 設定](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

有關應用服務如何在 Linux 中執行與建構 PHP 應用程式的詳細資訊,請參閱[Oryx 文件:如何偵測和建構 PHP 應用程式](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)。

## <a name="customize-start-up"></a>自訂啟動

預設情況下,內建 PHP 容器運行 Apache 伺服器。 啟動時, 執行時`apache2ctl -D FOREGROUND"`。 如果您願意,可以通過在[雲殼](https://shell.azure.com)中運行以下命令,在啟動時運行不同的命令:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>存取環境變數

在 App Service 中，您可以於應用程式的程式碼外部[設定應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 然後,您可以使用標準[getenv()](https://secure.php.net/manual/function.getenv.php)模式訪問它們。 例如，若要存取稱為 `DB_HOST` 的應用程式設定，請使用下列程式碼：

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>變更網站根

您選擇的 Web 框架可能使用子目錄作為網站根目錄。 例如[,Laravel](https://laravel.com/)使用`public/`子目錄作為網站根目錄。

應用服務的預設 PHP 映射使用 Apache,它不允許您自訂應用的網站根。 要解決此限制,請向儲存庫根加入包含以下內容的 *.htaccess*檔案:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

如果您不要使用 .htaccess** 重寫，您可以改為使用[自訂 Docker 映像](quickstart-docker-go.md)來部署 Laravel 應用程式。

## <a name="detect-https-session"></a>偵測 HTTPS 工作階段

在 App Service 中，[SSL 終止](https://wikipedia.org/wiki/TLS_termination_proxy)會在網路負載平衡器上發生，因此所有的 HTTPS 要求都會以未加密 HTTP 要求的形式進入您的應用程式。 如果您的應用程式邏輯需要檢查使用者要求是否有加密，請檢查 `X-Forwarded-Proto` 標頭。

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

熱門的 Web 架構可讓您在標準的應用程式模式中存取 `X-Forwarded-*` 資訊。 在 [CodeIgniter](https://codeigniter.com/) 中，[is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) 預設會檢查 `X_FORWARDED_PROTO` 的值。

## <a name="customize-phpini-settings"></a>自訂 php.ini 設定

如果需要更改 PHP 安裝,可以透過執行以下步驟變更任何[php.ini 指令](https://www.php.net/manual/ini.list.php)。

> [!NOTE]
> 查看 PHP 版本和當前*php.ini*設定的最佳方法是在應用程式中調用[phpinfo()。](https://php.net/manual/function.phpinfo.php)
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>自訂非PHP_INI_SYSTEM指令

要自訂PHP_INI_USER、PHP_INI_PERDIR和PHP_INI_ALL指令(請參閱[php.ini 指令](https://www.php.net/manual/ini.list.php)),請向應用的根目錄添加 *.htaccess*檔。

在 *.htaccess*檔中`php_value <directive-name> <value>`,使用語法添加指令。 例如：

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

使用更改重新部署應用並重新啟動它。 如果使用 Kudu 部署它(例如,使用[Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)),則部署後會自動重新啟動它。

作為使用 *.htaccess*的替代方法,您可以在應用中使用[ini_set()](https://www.php.net/manual/function.ini-set.php)來自定義這些非PHP_INI_SYSTEM指令。

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>自訂PHP_INI_SYSTEM指令

要自定義PHP_INI_SYSTEM指令(請參閱[php.ini 指令](https://www.php.net/manual/ini.list.php)),不能使用 *.htaccess*方法。 應用服務使用`PHP_INI_SCAN_DIR`應用設置提供單獨的機制。

首先,在[雲殼](https://shell.azure.com)中運行以下命令以添加`PHP_INI_SCAN_DIR`名為 的應用設置:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`是*存在 php.ini 的*預設目錄。 `/home/site/ini`是在其中添加自定義 *.ini*檔的自定義目錄。 使用分隔值`:`。

使用 Linux 容器`https://<app-name>.scm.azurewebsites.net/webssh/host`(導航 到 Web SSH 會話)。

在`/home/site``ini`中 創建目錄,`/home/site/ini`然後在 目錄中創建 *.ini*檔(例如,*設定.ini),* 該目錄包含要自定義的指令。 使用在*php.ini*檔案中使用的語法。 

> [!TIP]
> 在應用服務中的內建 Linux 容器中 *,/home*用作持久共用存儲。 
>

例如,要更改[expose_php](https://php.net/manual/ini.core.php#ini.expose-php)執行以下指令的值:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

要使更改生效,請重新啟動應用。

## <a name="enable-php-extensions"></a>開啟 PHP 延伸

內置 PHP 安裝包含最常用的擴展。 您可以啟用其他擴展的方式與[自訂 php.ini 指令](#customize-php_ini_system-directives)相同。

> [!NOTE]
> 查看 PHP 版本和當前*php.ini*設定的最佳方法是在應用程式中調用[phpinfo()。](https://php.net/manual/function.phpinfo.php)
>

若要啟用其他延伸模組，請依照下列步驟執行：

將`bin`目錄添加到應用的根目錄,`.so`並將擴展檔放入其中(例如 *,mongodb.so*)。 確保擴展與 Azure 中的 PHP 版本相容,並且與 VC9 和非線程安全 (nts) 相容。

部署更改。

按照[自定義PHP_INI_SYSTEM指令](#customize-php_ini_system-directives)中的步驟,將擴展添加到自定義 *.ini*檔中,並[帶有擴展名](https://www.php.net/manual/ini.core.php#ini.extension)或[zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension)指令。

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

要使更改生效,請重新啟動應用。

## <a name="access-diagnostic-logs"></a>存取診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在瀏覽器中開啟 SSH 工作階段

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>疑難排解

當工作中的 PHP 應用在應用服務中的行為不同或有錯誤時,請嘗試以下操作:

- [存取記錄資料流](#access-diagnostic-logs)。
- 在生產模式下本地測試應用。 應用服務在生產模式下運行 Node.js 應用,因此您需要確保專案在本地生產模式下按預期工作。 例如：
    - 根據您的*作曲家.json,* 不同的套件可能安裝生產模式`require`( 與`require-dev`)。
    - 某些 Web 框架在生產模式下可能以不同的方式部署靜態檔。
    - 某些 Web 框架在生產模式下運行時可能會使用自訂啟動文稿。
- 在調試模式下在應用服務中運行應用。 例如,在[Laravel](https://meanjs.org/)中,可以通過[`APP_DEBUG``true`將應用設置設置為](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)() 來配置應用以在生產中輸出調試消息。

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 MySQL 的 PHP 應用程式](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [App Service Linux 常見問題集](app-service-linux-faq.md)
