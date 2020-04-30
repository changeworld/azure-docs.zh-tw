---
title: 設定 PHP 應用程式
description: 瞭解如何為您的應用程式設定預先建立的 PHP 容器。 本文說明最常見的設定工作。
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9e87466f810dc4ebf767c36ad74c358cbf6069e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758885"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>設定適用于 Azure App Service 的 Linux PHP 應用程式

本指南說明如何在 Azure App Service 中設定 web 應用程式、行動後端和 API 應用程式的內建 PHP 執行時間。

本指南提供在 App Service 中使用內建 Linux 容器的 PHP 開發人員的重要概念和指示。 如果您從未使用過 Azure App Service，請先遵循[php 快速入門](quickstart-php.md)和[php with MySQL 教學](tutorial-php-mysql-app.md)課程。

## <a name="show-php-version"></a>顯示 PHP 版本

若要顯示目前的 PHP 版本，請在[Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要顯示所有支援的 PHP 版本，請在[Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>設定 PHP 版本

在[Cloud Shell](https://shell.azure.com)中執行下列命令，將 PHP 版本設定為7.2：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>自訂群組建自動化

如果您使用 Git 或已開啟組建自動化的 zip 套件來部署應用程式，App Service 組建自動化會逐步執行下列順序：

1. 執行自訂腳本（如果`PRE_BUILD_SCRIPT_PATH`有指定）。
1. 執行 `php composer.phar install`。
1. 執行自訂腳本（如果`POST_BUILD_SCRIPT_PATH`有指定）。

`PRE_BUILD_COMMAND`和`POST_BUILD_COMMAND`是預設為空白的環境變數。 若要執行預先建立的命令， `PRE_BUILD_COMMAND`請定義。 若要執行建立後命令，請`POST_BUILD_COMMAND`定義。

下列範例會指定一系列命令的兩個變數，並以逗號分隔。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

如需其他環境變數以自訂群組建自動化，請參閱[Oryx configuration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

如需有關 App Service 如何在 Linux 中執行和建立 PHP 應用程式的詳細資訊，請參閱[Oryx 檔：如何偵測和建立 php 應用程式](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)。

## <a name="customize-start-up"></a>自訂啟動

根據預設，內建 PHP 容器會執行 Apache 伺服器。 啟動時，它會執行`apache2ctl -D FOREGROUND"`。 如有需要，您可以在啟動時執行不同的命令，方法是在[Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>存取環境變數

在 App Service 中，您可以於應用程式的程式碼外部[設定應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 然後您可以使用標準[getenv （）](https://secure.php.net/manual/function.getenv.php)模式來存取它們。 例如，若要存取稱為 `DB_HOST` 的應用程式設定，請使用下列程式碼：

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>變更網站根目錄

您選擇的 web 架構可能會使用子目錄作為網站根目錄。 例如， [Laravel](https://laravel.com/)會使用`public/`子目錄作為網站根目錄。

App Service 的預設 PHP 映射會使用 Apache，而且不會讓您自訂應用程式的網站根目錄。 若要解決這項限制，請將 *.htaccess*檔案新增至您的存放庫根目錄，其中包含下列內容：

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

## <a name="customize-phpini-settings"></a>自訂 php .ini 設定

如果您需要變更 PHP 安裝，可以遵循這些步驟來變更任何[php .ini](https://www.php.net/manual/ini.list.php)指示詞。

> [!NOTE]
> 查看 PHP 版本和目前*php .ini*設定的最佳方式，是在您的應用程式中呼叫[phpinfo （）](https://php.net/manual/function.phpinfo.php) 。
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>自訂-非 PHP_INI_SYSTEM 指示詞

若要自訂 PHP_INI_USER、PHP_INI_PERDIR 和 PHP_INI_ALL 指示詞（請參閱[PHP .ini](https://www.php.net/manual/ini.list.php)指示詞），請將 *.htaccess*檔案新增至應用程式的根目錄。

在 *.htaccess*檔案中，使用`php_value <directive-name> <value>`語法加入指示詞。 例如：

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

使用變更重新部署您的應用程式，並將其重新開機。 如果您使用 Kudu （例如，使用[Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)）進行部署，它會在部署後自動重新開機。

除了使用 *.htaccess*，您也可以在應用程式中使用[ini_set （）](https://www.php.net/manual/function.ini-set.php) ，以自訂這些非 PHP_INI_SYSTEM 的指示詞。

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>自訂 PHP_INI_SYSTEM 指示詞

若要自訂 PHP_INI_SYSTEM 指示詞（請參閱[PHP .ini](https://www.php.net/manual/ini.list.php)指示詞），您無法使用 *.htaccess*方法。 App Service 使用`PHP_INI_SCAN_DIR`應用程式設定來提供個別的機制。

首先，在[Cloud Shell](https://shell.azure.com)中執行下列命令，以新增名`PHP_INI_SCAN_DIR`為的應用程式設定：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`是*php .ini*所在的預設目錄。 `/home/site/ini`是您要在其中新增自訂 *.ini*檔案的自訂目錄。 您可以使用來分隔這些`:`值。

流覽至您的 Linux 容器（`https://<app-name>.scm.azurewebsites.net/webssh/host`）的 web SSH 會話。

`/home/site`在中，建立名`ini`為的目錄，然後在`/home/site/ini`目錄中建立 *.ini*檔案（例如，*設定 .ini）* 並加上您想要自訂的指示詞。 使用您在*php .ini*檔案中使用的相同語法。 

> [!TIP]
> 在 App Service 的內建 Linux 容器中，會使用 */home*作為保存的共用儲存體。 
>

例如，若要變更[expose_php](https://php.net/manual/ini.core.php#ini.expose-php)的值，請執行下列命令：

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

若要讓變更生效，請重新開機應用程式。

## <a name="enable-php-extensions"></a>啟用 PHP 擴充功能

內建 PHP 安裝包含最常使用的延伸模組。 您可以用您[自訂 php .ini](#customize-php_ini_system-directives)指示詞的相同方式來啟用其他擴充功能。

> [!NOTE]
> 查看 PHP 版本和目前*php .ini*設定的最佳方式，是在您的應用程式中呼叫[phpinfo （）](https://php.net/manual/function.phpinfo.php) 。
>

若要啟用其他延伸模組，請依照下列步驟執行：

將`bin`目錄新增至應用程式的根目錄，並在其中放`.so`入擴充檔案（例如， *mongodb.so*）。 請確定擴充功能與 Azure 中的 PHP 版本相容，而且與 VC9 和非安全線程（nts）相容。

部署您的變更。

遵循[自訂 PHP_INI_SYSTEM](#customize-php_ini_system-directives)指示詞中的步驟，將擴充功能新增至具有[副檔名](https://www.php.net/manual/ini.core.php#ini.extension)或[zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension)指示詞的自訂 *.ini*檔案。

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

若要讓變更生效，請重新開機應用程式。

## <a name="access-diagnostic-logs"></a>存取診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在瀏覽器中開啟 SSH 工作階段

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>疑難排解

當運作中的 PHP 應用程式在 App Service 中有不同的行為，或發生錯誤時，請嘗試下列動作：

- [存取記錄資料流](#access-diagnostic-logs)。
- 以生產模式在本機測試應用程式。 App Service 會在生產模式中執行您的 node.js 應用程式，因此您必須確定您的專案在本機的生產模式中如預期般運作。 例如：
    - 視您的*編輯器 json*而定，可能會針對生產模式（`require` vs. `require-dev`）安裝不同的套件。
    - 某些 web 架構可能會在生產模式中以不同的方式部署靜態檔案。
    - 某些 web 架構在生產模式中執行時，可能會使用自訂啟動腳本。
- 在 debug 模式的 App Service 中執行您的應用程式。 例如，在[Laravel](https://meanjs.org/)中，您可以將應用程式[設定設為`APP_DEBUG` `true` ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)，將您的應用程式設定為在生產環境中輸出 debug 訊息。

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 MySQL 的 PHP 應用程式](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [App Service Linux 常見問題集](app-service-linux-faq.md)
