---
title: 設定 PHP 執行階段
description: 了解如何設定預設的 PHP 安裝，或是在 Azure App Service 中新增自訂 PHP 安裝。
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/13/2020
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 208f4f7b4c2d8562d5237a40f52e4774ea5c5606
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272469"
---
# <a name="configure-php-in-azure-app-service"></a>在 Azure App Service 中設定 PHP

## <a name="introduction"></a>簡介

本指南介紹如何在[Azure 應用服務](https://go.microsoft.com/fwlink/?LinkId=529714)中為 Web 應用和 API 應用配置內建 PHP 運行時、提供自訂 PHP 執行時以及啟用擴展。 若要使用 App Service，請註冊 [免費試用]。 若要充分利用本指南，您應該先在 App Service 中建立 PHP 應用程式。

## <a name="how-to-change-the-built-in-php-version"></a>作法：變更內建 PHP 版本

建立 Web 應用程式時,您可以選擇將設定的 PHP 版本。 有關當前支援版本的最新資訊,請參閱[應用服務上的 PHP。](https://github.com/Azure/app-service-linux-docs/blob/master/Runtime_Support/php_support.md)

要檢查應用的現有運行時版本,可以部署調用[phpinfo()]函數的腳本。

若要更新 PHP 版本，請遵循下列方法其中之一：

### <a name="azure-portal"></a>Azure 入口網站

1. 瀏覽到[Azure 門戶](https://portal.azure.com)中的應用,然後滾動到 **「配置」** 頁。

2. 從**設定**選擇 **「常規設定」** 並選擇新的 PHP 版本。

3. 按下 **「常規設定**」邊欄選項卡頂部的 **「儲存**」按鈕。

### <a name="azure-cli"></a>Azure CLI 

若要使用 Azure 命令列介面，您必須在電腦上[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

1. 開啟 [終端機]，然後登入您的帳戶。

        az login

1. 查看支援的執行階段清單。

        az webapp list-runtimes | grep php

2. 設定應用程式的 PHP 版本。

        az webapp config set --php-version {5.6 | 7.2 | 7.3} --name {app-name} --resource-group {resource-group-name}

3. PHP 版本現在已設定完成。 您可確認這些設定：

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>作法：變更內建 PHP 組態

對於任一個內建的 PHP 執行階段，您可以遵循下列步驟來變更任何組態選項。 (如需 php.ini 指示詞的資訊，請參閱 [php.ini 指示詞的清單](英文))。

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>變更 PHP\_INI\_USER、PHP\_INI\_PERDIR、PHP\_INI\_ALL 組態設定

1. 將 [.user.ini] 檔案新增至根目錄。
1. 使用在 `php.ini` 檔案中使用的相同語法，將組態設定新增至 `.user.ini` 檔案。 例如，如果您要開啟 `display_errors` 設定，並將 `upload_max_filesize` 設定設為 10M，則 `.user.ini` 檔案將包含下列文字：

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. 部署您的應用程式。
3. 重新啟動應用程式。 (必須重新啟動，因為 PHP 讀取 `.user.ini` 檔案的頻率是由 `user_ini.cache_ttl` 設定所控制，這是系統層級設定，預設為 300 秒 (5 分鐘)。 重新啟動應用程式將強制 PHP 讀取 `.user.ini` 檔案中的新設定。)

除了使用 `.user.ini` 檔案之外，您也可以在指令碼中使用 [ini_set()] 函數，設定非系統層級指示詞的組態選項。

### <a name="changing-php_ini_system-configuration-settings"></a>變更 PHP\_INI\_SYSTEM 組態設定

1. 使用機碼 `PHP_INI_SCAN_DIR` 和值 `d:\home\site\ini` 將應用程式設定新增至應用程式
1. 在 `d:\home\site\ini` 目錄中使用 Kudo 主控台 (http://&lt;site-name&gt;.scm.azurewebsite.net) 建立 `settings.ini` 檔案。
1. 使用在 `php.ini` 檔案中使用的相同語法，將組態設定新增至 `settings.ini` 檔案。 例如,如果要將`curl.cainfo`設定`*.crt`指向 檔並將「wincache.maxfilesize」設定設定為 512`settings.ini`K,則 檔將包含以下文本:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. 若要重新載入變更，請重新啟動應用程式。

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>作法：在預設 PHP 執行階段中啟用擴充

如同上一個小節所述，若要查看預設 PHP 版本、其預設組態及啟用的擴充，最好的方法是部署呼叫 [phpinfo()]函數的指令碼。 若要啟用其他延伸模組，請依照下列步驟執行：

### <a name="configure-via-ini-settings"></a>透過 ini 設定來設定

1. 將 `ext` 目錄新增至 `d:\home\site` 目錄。
1. 將 `.dll` 擴充檔放入 `ext` 目錄中 (例如，`php_xdebug.dll`)。 請確定擴充功能與預設的 PHP 版本相容，並且與 VC9 及非執行緒安全 (nts) 相容。
1. 使用機碼 `PHP_INI_SCAN_DIR` 和值 `d:\home\site\ini` 將應用程式設定新增至應用程式
1. 在名為 `extensions.ini` 的 `d:\home\site\ini` 中建立 `ini` 檔案。
1. 使用在 `php.ini` 檔案中使用的相同語法，將組態設定新增至 `extensions.ini` 檔案。 例如，如果您要啟用 MongoDB 和 XDebug 擴充，您的 `extensions.ini` 檔案應該包含以下文字：

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. 重新啟動應用程式以載入變更。

### <a name="configure-via-app-setting"></a>透過應用程式設定來設定

1. 將 `bin` 目錄新增至根目錄。
2. 將 `.dll` 擴充檔放入 `bin` 目錄中 (例如，`php_xdebug.dll`)。 請確定擴充功能與預設的 PHP 版本相容，並且與 VC9 及非執行緒安全 (nts) 相容。
3. 部署您的應用程式。
4. 瀏覽到 Azure 門戶中的應用,然後按下 **「設置」** 部分下的 **「設定**」。
5. 從 **「設定」** 邊欄選項卡中,選擇 **「應用程式設定**」 。
6. 在「**應用程式設定」** 部分中,按一下 **「新建應用程式」設置**並創建**PHP_EXTENSIONS**鍵。 此索引鍵的值是相對於網站根目錄的路徑：**bin\your-ext-file**。
7. 按下底部的 **「更新**」按鈕,然後按下「**應用程式設定」** 選項卡上方的 **「儲存**」。

Zend 擴充功能也支援使用 **PHP_ZENDEXTENSIONS** 索引鍵。 若要啟用多個擴充功能，請針對應用程式設定值包含以逗號分隔的 `.dll` 檔案清單。

## <a name="how-to-use-a-custom-php-runtime"></a>做法：使用自訂 PHP 執行階段

除了預設的 PHP 執行階段之外，App Service 也可以使用您提供的 PHP 執行階段來執行 PHP 指令碼。 您提供的執行階段可以由也是您提供的 `php.ini` 檔案加以設定。 若要使用自訂 PHP 執行階段搭配 App Service，請遵循下列步驟。

1. 取得 PHP for Windows 的非安全執行緒 VC9 或 VC11 相容版本。 Windows 的 PHP 最新版本可在此[https://windows.php.net/download/]處找到: 。 較舊的版本可在此處的封存檔中找到[https://windows.php.net/downloads/releases/archives/]: 。
2. 為您的執行階段修改 `php.ini` 檔案。 App Service 將忽略僅系統層級指示詞的任何組態設定。 (如需僅系統層級指示詞的資訊，請參閱 [php.ini 指示詞的清單] (英文))。
3. 或者，將擴充功能新增至 PHP 執行階段，並且在 `php.ini` 檔案中啟用這些擴充功能。
4. 將 `bin` 目錄新增至根目錄，並在其中放入包含 PHP 執行階段的目錄 (例如，`bin\php`)。
5. 部署您的應用程式。
6. 瀏覽到 Azure 門戶中的應用,然後按下 **「配置」** 邊欄選項卡。
8. 從 **「設定」** 邊列選項卡中, 選擇**路徑映射**。 
9. 按下 **「新處理程式**」並添加`*.php`到「擴展」欄位,並將`php-cgi.exe`路徑添加到**文稿處理器**中的可執行檔。 如果您將 PHP 執行階段放入應用程式根目錄內的 `bin` 目錄中，該路徑將是 `D:\home\site\wwwroot\bin\php\php-cgi.exe`。
10. 在底部,按兩下 **「更新」** 以完成添加處理程式映射。
11. 按一下 [儲存]**** 來儲存變更。

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>做法︰在 Azure 中啟用編輯器自動化

App Service 預設不會對 composer.json (如果您 PHP 專案中有的話) 執行任何操作。 如果您使用 [Git 部署](deploy-local-git.md)，您可以透過啟用「編輯器」擴充功能，在 `git push` 期間啟用 composer.json 處理。

> [!NOTE]
> 您可以 [在這裡投票選擇 App Service 中的頂級編輯器支援](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)！
>

1. 在 [Azure 入口網站](https://portal.azure.com)的 PHP 應用程式刀鋒視窗中，按一下 [工具]**** > [延伸模組]****。

    ![可在 Azure 中啟用「編輯器」自動化的「Azure 入口網站」設定刀鋒視窗](./media/web-sites-php-configure/composer-extension-settings.png)
2. 按一下 [新增]****，然後按一下 [編輯器]****。

    ![新增「編輯器」擴充功能以在 Azure 中啟用「編輯器」自動化](./media/web-sites-php-configure/composer-extension-add.png)
3. 按一下 [確定] **** 以接受法律條款。 再按一次 [確定] **** 以新增擴充功能。

    [已安裝的延伸模組]**** 刀鋒視窗將顯示編輯器延伸模組。
    ![接受法律條款以在 Azure 中啟用「編輯器」自動化](./media/web-sites-php-configure/composer-extension-view.png)
4. 現在，在本機電腦的終端機視窗中執行 `git add`、`git commit` 和 `git push` 到您的應用程式。 注意編輯器正在安裝 composer.json 中定義的相依性。

    ![使用 Azure 中的「編輯器」自動化來進行 Git 部署](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [PHP 開發人員中心](https://azure.microsoft.com/develop/php/)。

[免費試用]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[php.ini 指示詞的清單]: https://www.php.net/manual/en/ini.list.php
[.user.ini]: https://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: https://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[https://windows.php.net/download/]: https://windows.php.net/download/
[https://windows.php.net/downloads/releases/archives/]: https://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
