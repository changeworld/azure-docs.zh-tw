---
title: 設定 PHP 應用程式
description: 瞭解如何為您的應用程式設定預先建置的 PHP 容器。 本文說明最常見的設定工作。
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9933205095587d9e8e0d8a5641d213f159512450
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234944"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>為 Azure App Service 設定 Linux PHP 應用程式

本指南示範如何為 Azure App Service 中的 Web 應用程式、行動後端和 API 應用程式設定內建的 PHP 執行階段。

本指南為在 App Service 中使用內建 Linux 容器的 PHP 開發人員提供了重要概念和指示。 如果您從未使用過 Azure App Service，請先遵循 [PHP 快速入門](quickstart-php.md)和[搭配使用 PHP 和 MySQL 教學課程](tutorial-php-mysql-app.md)。

## <a name="show-php-version"></a>顯示 PHP 版本

若要顯示目前的 PHP 版本，請在 [Cloud Shell](https://shell.azure.com) 中執行下列命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要顯示所有支援的 PHP 版本，請在 [Cloud Shell](https://shell.azure.com) 中執行下列命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>設定 PHP 版本

在 [Cloud Shell](https://shell.azure.com) 中執行下列命令，以將 PHP 版本設定為 7.2：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>自訂組建自動化

如果您使用 Git 或 zip 套件並開啟組建自動化來部署應用程式，App Service 組建自動化將會依下列順序逐步執行：

1. 執行自訂指令碼 (如果 `PRE_BUILD_SCRIPT_PATH` 已指定)。
1. 執行 `php composer.phar install`。
1. 執行自訂指令碼 (如果 `POST_BUILD_SCRIPT_PATH` 已指定)。

`PRE_BUILD_COMMAND` 和 `POST_BUILD_COMMAND` 是預設為空值的環境變數。 若要執行建置前命令，請定義 `PRE_BUILD_COMMAND`。 若要執行建置後命令，請定義 `POST_BUILD_COMMAND`。

下列範例會將兩個變數指定給一系列的命令 (以逗號分隔)。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

若要了解其他可自訂組建自動化的環境變數，請參閱 [Oryx 設定](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

若要深入瞭解 App Service 如何在 Linux 中執行和建置 PHP 應用程式，請參閱 [Oryx 文件：如何偵測和建置 PHP 應用程式](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)。

## <a name="customize-start-up"></a>自訂啟動

根據預設，內建 PHP 容器會執行 Apache 伺服器。 它會在啟動時執行 `apache2ctl -D FOREGROUND"`。 如有需要，您可以在啟動時執行不同的命令，方法是在 [Cloud Shell](https://shell.azure.com) 中執行下列命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>存取環境變數

在 App Service 中，您可以於應用程式的程式碼外部[設定應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 然後，您可以使用標準的 [getenv()](https://secure.php.net/manual/function.getenv.php) 模式來存取這些設定。 例如，若要存取稱為 `DB_HOST` 的應用程式設定，請使用下列程式碼：

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>變更網站根目錄

您選擇的 web 架構可能會使用子目錄作為網站根目錄。 例如，[Laravel](https://laravel.com/) 使用 `public/` 子目錄作為網站根目錄。

App Service 的預設 PHP 映像會使用 Apache，且不會讓您自訂應用程式的網站根目錄。 若要避開這項限制，請將 *.htaccess* 檔案新增至存放庫根目錄，其中包含下列內容：

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^/$
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

如果您不要使用 .htaccess 重寫，您可以改為使用[自訂 Docker 映像](quickstart-docker-go.md)來部署 Laravel 應用程式。

## <a name="detect-https-session"></a>偵測 HTTPS 工作階段

在 App Service 中，[SSL 終止](https://wikipedia.org/wiki/TLS_termination_proxy)會在網路負載平衡器上發生，因此所有的 HTTPS 要求都會以未加密 HTTP 要求的形式進入您的應用程式。 如果您的應用程式邏輯需要檢查使用者要求是否有加密，請檢查 `X-Forwarded-Proto` 標頭。

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

熱門的 Web 架構可讓您在標準的應用程式模式中存取 `X-Forwarded-*` 資訊。 在 [CodeIgniter](https://codeigniter.com/) 中，[is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) 預設會檢查 `X_FORWARDED_PROTO` 的值。

## <a name="customize-phpini-settings"></a>自訂 php .ini 設定

如果您需要對 PHP 安裝進行變更，您可以依照下列步驟來變更 [php.ini 指示詞](https://www.php.net/manual/ini.list.php)。

> [!NOTE]
> 查看 PHP 版本和目前 *php.ini* 設定的最佳方法是在應用程式中呼叫 [phpinfo ()](https://www.php.net/manual/function.phpinfo.php)。
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>自訂非 PHP_INI_SYSTEM 指示詞

若要自訂 PHP_INI_USER、PHP_INI_PERDIR 和 PHP_INI_ALL 指示詞 (請參閱 [php.ini指示詞](https://www.php.net/manual/ini.list.php))，請將 *.htaccess* 檔案新增至應用程式的根目錄。

在 *.htaccess* 檔案中，使用 `php_value <directive-name> <value>` 語法加入指示詞。 例如：

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

經由變更重新部署應用程式，並重新啟動應用程式。 如果您使用 Kudu (例如使用 [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)) 部署應用程式，則應用程式會在部署後自動重新啟動。

除了使用 *.htaccess*之外，您還可以在應用程式中使用 [ini_set () ](https://www.php.net/manual/function.ini-set.php) 來自訂這些非 PHP_INI_SYSTEM 的指示詞。

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>自訂 PHP_INI_SYSTEM 指示詞

若要自訂 PHP_INI_SYSTEM 指示詞 (請參閱 [php.ini 指示詞](https://www.php.net/manual/ini.list.php))，您不能使用 *.htaccess* 方法。 App Service 會使用 `PHP_INI_SCAN_DIR` 應用程式設定來提供個別的機制。

首先，在 [Cloud Shell](https://shell.azure.com) 中執行下列命令，以新增名為 `PHP_INI_SCAN_DIR` 的應用程式設定：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` 是 *php .ini* 所在的預設目錄。 `/home/site/ini` 是您要在其中新增自訂 *.ini* 檔案的自訂目錄。 您可以使用 `:` 來分隔這些值。

使用 Linux 容器 (`https://<app-name>.scm.azurewebsites.net/webssh/host`) 瀏覽至 web SSH 工作階段。

在 `/home/site` 中建立名為 `ini` 的目錄，然後使用要自訂的指示詞在 `/home/site/ini` 目錄中建立 *.ini* 檔案 (例如 *settings.ini)* 。 使用要在 *php .ini* 檔案中使用的相同語法。 

> [!TIP]
> 在 App Service 的內建 Linux 容器中，會使用 */home* 作為保存的共用儲存體。 
>

例如，若要變更 [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) 的值，請執行下列命令：

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

如果要讓變更生效，請重新啟動應用程式。

## <a name="enable-php-extensions"></a>啟用 PHP 擴充功能

內建 PHP 安裝包含最常使用的擴充功能。 您可以使用與[自訂 php.ini 指示詞](#customize-php_ini_system-directives)相同的方式來啟用其他擴充功能。

> [!NOTE]
> 查看 PHP 版本和目前 *php.ini* 設定的最佳方法是在應用程式中呼叫 [phpinfo ()](https://php.net/manual/function.phpinfo.php)。
>

若要啟用其他延伸模組，請依照下列步驟執行：

將 `bin` 目錄新增至應用程式的根目錄，並在其中放入 `.so` 擴充功能檔案 (例如 *mongodb.so*)。 請確定擴充功能與 Azure 中的 PHP 版本相容，並且與 VC9 及非執行緒安全 (nts) 相容。

部署您的變更。

依照[自訂 PHP_INI_SYSTEM 指示詞](#customize-php_ini_system-directives)中的步驟，將擴充功能新增至使用 [extension](https://www.php.net/manual/ini.core.php#ini.extension) 或 [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) 指示詞的自訂 *.ini* 檔案。

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

如果要讓變更生效，請重新啟動應用程式。

## <a name="access-diagnostic-logs"></a>存取診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在瀏覽器中開啟 SSH 工作階段

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>疑難排解

當運作中的 PHP 應用程式在 App Service 中有不同的行為或發生錯誤時，請嘗試下列動作：

- [存取記錄資料流](#access-diagnostic-logs)。
- 在生產模式中於本機測試應用程式。 App Service 會在生產模式中執行 node.js 應用程式，因此您必須確定項目生產模式下按預期在本機中運作。 例如：
    - 視 *composer.json* 而定，可能會為生產模式安裝不同的套件 (`require` 與 `require-dev`)。
    - 某些 web 架構可以在生產模式中以不同的方式部署靜態檔案。
    - 在生產模式中執行時，某些 web 架構可能會使用自訂啟動指令碼。
- 在偵錯模式中於 App Service 內執行應用程式。 例如，在 [Laravel](https://meanjs.org/)中，您可以透過[將 `APP_DEBUG` 應用程式設定設為 `true`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)，將應用程式設定為在生產環境中輸出偵錯訊息。

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 MySQL 的 PHP 應用程式](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [App Service Linux 常見問題集](app-service-linux-faq.md)
