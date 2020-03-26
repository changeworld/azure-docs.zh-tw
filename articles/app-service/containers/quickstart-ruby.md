---
title: 快速入門：建立 Linux Ruby 應用程式
description: 藉由將您的第一個 Ruby 應用程式部署至 App Service 中的 Linux 容器，在 Azure App Service 上開始使用 Linux 應用程式。
keywords: azure app service, linux, oss, ruby, rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 07/11/2019
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 33723e6dd8fbcd91d2d5863816786c0f08cdf923
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "80045841"
---
# <a name="create-a-ruby-on-rails-app-in-app-service-on-linux"></a>在 Linux 上的 App Service 中建立 Ruby on Rails 應用程式

[Linux 上的 App Service](app-service-linux-intro.md) 使用 Linux 作業系統提供可高度擴充、自我修復的 Web 主機服務。 本快速入門教學課程會說明如何使用 [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) \(部分機器翻譯\) 將 Ruby on Rails 應用程式部署至 Linux 上的 Azure App Service。

> [!NOTE]
> Ruby 開發堆疊目前僅支援 Ruby on Rails。 如果您想要使用不同的平台 (例如 Sinatra)，或是要使用[不支援的 Ruby 版本](app-service-linux-intro.md)，則必須[在自訂容器中加以執行](quickstart-docker-go.md)。

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">安裝 Ruby 2.6 或更高版本</a>
* <a href="https://git-scm.com/" target="_blank">安裝 Git</a>

## <a name="download-the-sample"></a>下載範例

在終端機視窗中執行下列命令，將範例應用程式存放庫複製到本機電腦：

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>在本機執行應用程式

在本機執行應用程式，以便您查看它在部署至 Azure 時的樣貌。 開啟終端機視窗，切換至 `hello-world` 目錄，然後使用 `rails server` 命令啟動伺服器。

第一個步驟是安裝必要的 Gem。 範例中包含 `Gemfile`，因此請直接執行下列命令：

```bash
bundle install
```

在 Gem 安裝完成後，我們將使用搭配程式來啟動應用程式：

```bash
bundle exec rails server
```

使用網頁瀏覽器，瀏覽至 `http://localhost:3000`，在本機測試應用程式。

![已設定 Hello World](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>建立 Web 應用程式

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

瀏覽至應用程式以查看您使用內建映像新建立的 Web 應用程式。 以您的 Web 應用程式名稱取代 _&lt;app name>_ 。

```bash
http://<app_name>.azurewebsites.net
```

新的 Web 應用程式看起來應該像這樣：

![啟動顯示畫面](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>部署應用程式

執行下列命令，將本機應用程式部署至您的 Azure Web 應用程式：

```bash
git remote add azure <Git deployment URL from above>
git push azure master
```

確認遠端部署作業報告成功。 此命令會產生類似下列文字的輸出：

```bash
remote: Using turbolinks 5.2.0
remote: Using uglifier 4.1.20
remote: Using web-console 3.7.0
remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
remote: Bundled gems are installed into `/tmp/bundle`
remote: Zipping up bundle contents
remote: .......
remote: ~/site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
   a6e73a2..ae34be9  master -> master
```

部署完成之後，請等候 10 秒來讓 Web 應用程式重新啟動，然後瀏覽至 Web 應用程式並確認結果。

```bash
http://<app-name>.azurewebsites.net
```

![更新的 Web 應用程式](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> 當應用程式重新啟動時，您可以在瀏覽器或 `Hey, Ruby developers!` 預設頁面中觀察 HTTP 狀態碼 `Error 503 Server unavailable`。 應用程式可能需要幾分鐘才能完全重新啟動。
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：Ruby on Rails with Postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [設定 Ruby 應用程式](configure-language-ruby.md)
