---
title: 設定 Windows PHP 應用程式
description: 瞭解如何在 App Service 的原生 Windows 實例中設定 PHP 應用程式。 本文說明最常見的設定工作。
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 1eb4e9d349fdd0097cbde4e4cef3d5c61a167193
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907885"
---
# <a name="configure-a-windows-php-app-for-azure-app-service"></a>設定適用于 Azure App Service 的 Windows PHP 應用程式

本指南說明如何在 Azure App Service 中設定您的 PHP web 應用程式、行動後端和 API 應用程式。 本指南適用于 Windows 平臺上託管的應用程式。 如需 linux 應用程式的相關資訊，請參閱[設定適用于 Azure App Service 的 LINUX PHP 應用程式](containers/configure-language-php.md)。

本指南提供將應用程式部署至 App Service 的 PHP 開發人員的重要概念和指示。 如果您從未使用過 Azure App Service，請先遵循 [PHP 快速入門](app-service-web-get-started-php.md)和[搭配使用 PHP 和 MySQL 教學課程](app-service-web-tutorial-php-mysql.md)。

## <a name="show-php-version"></a>顯示 PHP 版本

若要顯示目前的 PHP 版本，請在 [Cloud Shell](https://shell.azure.com) 中執行下列命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

若要顯示所有支援的 PHP 版本，請在 [Cloud Shell](https://shell.azure.com) 中執行下列命令：

```azurecli-interactive
az webapp list-runtimes | grep php
```

## <a name="set-php-version"></a>設定 PHP 版本

在[Cloud Shell](https://shell.azure.com)中執行下列命令，將 PHP 版本設定為7.4：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

## <a name="run-composer"></a>執行編輯器

如果您想要 App Service 在部署階段執行[編輯器](https://getcomposer.org/)，最簡單的方式是在您的存放庫中包含編輯器。

從本機終端機視窗中，將目錄變更為您的存放庫根目錄，並遵循[下載編輯器](https://getcomposer.org/download/)中的指示，將*composer.phar*下載至目錄根目錄。

執行下列命令（您需要安裝[npm](https://www.npmjs.com/get-npm) ）：

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

您的存放庫根目錄現在有兩個額外的檔案： *. deployment*和*deploy.sh*。

開啟*deploy.sh* ，並尋找 `Deployment` 區段，如下所示：

```bash
##################################################################################################################################
# Deployment
# ----------
```

在區段*結尾處*，新增您需要執行必要工具的程式碼區段 `Deployment` ：

```bash
# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
```

認可所有變更，並使用 Git 部署您的程式碼，或使用已啟用組建自動化的 Zip 部署。 「編輯器」現在應該會當做「部署自動化」的一部分來執行。

## <a name="run-gruntbowergulp"></a>執行 Grunt/Bower/Gulp

如果您想要 App Service 在部署期間執行熱門的自動化工具，例如 Grunt、Bower 或 Gulp，您必須提供[自訂部署腳本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)。 當您使用 Git 部署，或在已啟用組建自動化的[Zip 部署](deploy-zip.md)時，App Service 執行此腳本。 

若要讓您的存放庫執行這些工具，您需要將它們新增至*package.js上*的相依性。 例如：

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

從本機終端機視窗中，將目錄切換到您的存放庫根目錄，然後執行下列命令（您需要安裝[npm](https://www.npmjs.com/get-npm) ）：

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

您的存放庫根目錄現在有兩個額外的檔案： *. deployment*和*deploy.sh*。

開啟*deploy.sh* ，並尋找 `Deployment` 區段，如下所示：

```bash
##################################################################################################################################
# Deployment
# ----------
```

本節結束于執行 `npm install --production` 。 在區段*結尾處*，新增您需要執行必要工具的程式碼區段 `Deployment` ：

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

請參閱[MEAN.js 範例中的範例](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)，其中部署腳本也會執行自訂 `npm install` 命令。

### <a name="bower"></a>Bower

此程式碼片段會執行 `bower install` 。

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

此程式碼片段會執行 `gulp imagemin` 。

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

此程式碼片段會執行 `grunt` 。

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="access-environment-variables"></a>存取環境變數

在 App Service 中，您可以於應用程式的程式碼外部[設定應用程式設定](configure-common.md#configure-app-settings)。 然後，您可以使用標準的 [getenv()](https://secure.php.net/manual/function.getenv.php) 模式來存取這些設定。 例如，若要存取稱為 `DB_HOST` 的應用程式設定，請使用下列程式碼：

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>變更網站根目錄

您選擇的 web 架構可能會使用子目錄作為網站根目錄。 例如， [Laravel](https://laravel.com/)會使用*公用/* 子目錄作為網站根目錄。

若要自訂網站根目錄，請使用命令來設定應用程式的虛擬應用程式路徑 [`az resource update`](/cli/azure/resource#az-resource-update) 。 下列範例會將網站根目錄設定為存放庫中的*公用/* 子目錄。 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

依預設，Azure App Service 會將根虛擬應用程式路徑 ( _/_ ) 指向已部署應用程式檔案的根目錄 (_sites\wwwroot_)。

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
> 查看 PHP 版本和目前 *php.ini* 設定的最佳方法是在應用程式中呼叫 [phpinfo ()](https://php.net/manual/function.phpinfo.php)。
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>自訂非 PHP_INI_SYSTEM 指示詞

若要自訂 PHP_INI_USER、PHP_INI_PERDIR 和 PHP_INI_ALL 指示詞（請參閱[php.ini](https://www.php.net/manual/ini.list.php)指示詞），請將檔案新增 `.user.ini` 至應用程式的根目錄。

使用在 `php.ini` 檔案中使用的相同語法，將組態設定新增至 `.user.ini` 檔案。 例如，如果您要開啟 `display_errors` 設定，並將 `upload_max_filesize` 設定設為 10M，則 `.user.ini` 檔案將包含下列文字：

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

經由變更重新部署應用程式，並重新啟動應用程式。

除了使用檔案之外 `.user.ini` ，您也可以在應用程式中使用[ini_set （）](https://www.php.net/manual/function.ini-set.php) ，以自訂這些非 PHP_INI_SYSTEM 的指示詞。

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>自訂 PHP_INI_SYSTEM 指示詞

若要自訂 PHP_INI_SYSTEM 指示詞 (請參閱 [php.ini 指示詞](https://www.php.net/manual/ini.list.php))，您不能使用 *.htaccess* 方法。 App Service 會使用 `PHP_INI_SCAN_DIR` 應用程式設定來提供個別的機制。

首先，在 [Cloud Shell](https://shell.azure.com) 中執行下列命令，以新增名為 `PHP_INI_SCAN_DIR` 的應用程式設定：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

流覽至 Kudu 主控台（ `https://<app-name>.scm.azurewebsites.net/DebugConsole` ），並流覽至 `d:\home\site` 。

在 `d:\home\site` 中建立名為 `ini` 的目錄，然後使用要自訂的指示詞在 `d:\home\site\ini` 目錄中建立 *.ini* 檔案 (例如 *settings.ini)* 。 使用要在 *php .ini* 檔案中使用的相同語法。 

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

```
extension=d:\home\site\wwwroot\bin\mongodb.so
zend_extension=d:\home\site\wwwroot\bin\xdebug.so
```

如果要讓變更生效，請重新啟動應用程式。

## <a name="access-diagnostic-logs"></a>存取診斷記錄

使用標準[error_log （）](https://php.net/manual/function.error-log.php)公用程式，讓您的診斷記錄顯示在 Azure App Service 中。

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>疑難排解

當運作中的 PHP 應用程式在 App Service 中有不同的行為或發生錯誤時，請嘗試下列動作：

- [存取記錄資料流](#access-diagnostic-logs)。
- 在生產模式中於本機測試應用程式。 App Service 會在生產模式中執行您的應用程式，因此您必須確定您的專案在本機的生產模式中如預期般運作。 例如：
    - 某些 web 架構可以在生產模式中以不同的方式部署靜態檔案。
    - 在生產模式中執行時，某些 web 架構可能會使用自訂啟動指令碼。
- 在偵錯模式中於 App Service 內執行應用程式。 例如，在 [Laravel](https://meanjs.org/)中，您可以透過[將 `APP_DEBUG` 應用程式設定設為 `true`](configure-common.md#configure-app-settings)，將應用程式設定為在生產環境中輸出偵錯訊息。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 MySQL 的 PHP 應用程式](app-service-web-tutorial-php-mysql.md)
