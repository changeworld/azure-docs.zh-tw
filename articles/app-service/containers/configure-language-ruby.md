---
title: 設定 Ruby 應用程式 - Azure App Service
description: 了解如何為您的應用程式設定預先建置的 Ruby 容器。 本文說明最常見的設定工作。
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18
ms.openlocfilehash: 804e6d562322eff20de8eb7e33caae98418ea3fe
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905675"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>為於 Azure App Service 設定 Linux Ruby 應用程式

本文說明 [Azure App Service](app-service-linux-intro.md) 會如何執行 Ruby 應用程式，以及要如何在需要時自訂 App Service 的行為。 您必須使用所有必要的 [gems](https://rubygems.org/gems) 來部署 Ruby 應用程式。

本指南為在 App Service 中使用內建 Linux 容器的 Ruby 開發人員提供了重要概念和指示。 如果您從未使用過 Azure App Service，則首先應該遵循 [Ruby 快速入門](quickstart-ruby.md)和 [Ruby with PostgreSQL 教學課程](tutorial-ruby-postgres-app.md)。

## <a name="show-ruby-version"></a>顯示 Ruby 版本

若要顯示目前的 Ruby 版本，請在 [Cloud Shell](https://shell.azure.com) 中執行下列命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要顯示所有支援的 Ruby 版本，請在 [Cloud Shell](https://shell.azure.com) 中執行下列命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

您可以改為建置您自己的容器映像，以執行不支援的 Ruby 版本。 如需詳細資訊，請參閱[使用自訂 Docker 映像](tutorial-custom-docker-image.md)。

## <a name="set-ruby-version"></a>設定 Ruby 版本

在 [Cloud Shell](https://shell.azure.com) 中執行下列命令，以將 Ruby 版本設定為 2.3：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> 如果在部署期間看到類似下列內容的錯誤：
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> 或
> ```
> rbenv: version `2.3.1' is not installed
> ```
> 這表示您的專案中設定的 Ruby 版本，與您正在執行的容器中安裝的版本不同 (上述範例中為 `2.3.3`)。 在上述範例中，檢查 *Gemfile* 和 *.ruby-version* 並確認未設定 Ruby 版本，或者設定為您正在執行的容器中安裝的版本 (上述範例中為 `2.3.3`)。

## <a name="access-environment-variables"></a>存取環境變數

在 App Service 中，您可以於應用程式的程式碼外部[設定應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 然後，您可以使用標準 [ENV['\<path-name>']](https://ruby-doc.org/core-2.3.3/ENV.html) 模式來存取這些設定。 例如，若要存取稱為 `WEBSITE_SITE_NAME` 的應用程式設定，請使用下列程式碼：

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>自訂部署

部署 [Git 存放庫](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)或 [Zip 套件](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)並開啟建置程序時，部署引擎 (Kudu) 預設會自動執行下列部署後步驟：

1. 檢查 *Gemfile* 是否存在。
1. 執行 `bundle clean`。 
1. 執行 `bundle install --path "vendor/bundle"`。
1. 執行 `bundle package` 將 gems 封裝到 vendor/cache 資料夾中。

### <a name="use---without-flag"></a>使用 --without 旗標

若要使用 [--without](https://bundler.io/man/bundle-install.1.html) 旗標執行 `bundle install`，請將 `BUNDLE_WITHOUT` [應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)設定為以逗號分隔的群組清單。 例如，下列命令會將它設定為 `development,test`。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

如果定義了此設定，則部署引擎將使用 `--without $BUNDLE_WITHOUT` 執行 `bundle install`。

### <a name="precompile-assets"></a>先行編譯的資產

預設情況下，部署後步驟不會先行編譯資產。 若要開啟資產先行編譯，請將 `ASSETS_PRECOMPILE` [應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)設定為 `true`。 然後在部署後步驟結束時執行命令 `bundle exec rake --trace assets:precompile`。 例如：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

如需詳細資訊，請參閱[提供靜態資產](#serve-static-assets)。

## <a name="customize-start-up"></a>自訂啟動

根據預設，Ruby 容器會依下列順序啟動 Rails 伺服器 (如需詳細資訊，請參閱[啟動指令碼](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh))：

1. 產生 [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) 值 (如果尚不存在)。 應用程式在生產模式中執行時需要此值。
1. 將 `RAILS_ENV` 環境變數設定為 `production`。
1. 刪除先前執行的 Rails 伺服器留下的 *tmp/pids* 目錄中的任何 *.pid* 檔案。
1. 檢查是否已安裝所有相依性。 如果沒有，請嘗試從本機 *vendor/cache* 目錄安裝 gems。
1. 執行 `rails server -e $RAILS_ENV`。

您可以透過下列方式自訂啟動過程：

- [提供靜態資產](#serve-static-assets)
- [在非生產模式中執行](#run-in-non-production-mode)
- [手動設定 secret_key_base](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>提供靜態資產

根據預設，Ruby 容器中的 Rails 伺服器以生產模式執行，並[假設資產已先行編譯並由 Web 伺服器提供服務](https://guides.rubyonrails.org/asset_pipeline.html#in-production)。 若要從 Rails 伺服器提供靜態資產，您需要做兩件事：

- **先行編譯的資產** - [在本機先行編譯靜態資產](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation)並以手動方式加以部署。 或者，讓部署引擎代替處理它 (請參閱[先行編譯資產](#precompile-assets)。
- **啟用提供靜態檔案** - 若要提供 Ruby 容器中的靜態資產，[請將`RAILS_SERVE_STATIC_FILES`應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)設定為 `true`。 例如：

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>在非生產模式中執行

根據預設，Rails 伺服器會以生產模式執行。 若要在開發模式中執行，例如，將 `RAILS_ENV` [應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)設定為 `development`。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

不過，僅此設定會導致 Rails 伺服器以開發模式啟動，該模式僅接受 localhost 要求，並且無法在容器之外存取。 若要接受遠端用戶端要求，請將 `APP_COMMAND_LINE` [應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)設定為 `rails server -b 0.0.0.0`。 此應用程式設定允許您在 Ruby 容器中執行自訂命令。 例如：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a> 手動設定 secret_key_base

若要使用您自己的 `secret_key_base` 值而不是讓 App Service 為您產生一個值，請使用您想要的值設定 `SECRET_KEY_BASE` [應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 例如：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>存取診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在瀏覽器中開啟 SSH 工作階段

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 PostgreSQL 的 Rails 應用程式](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [App Service Linux 常見問題集](app-service-linux-faq.md)
