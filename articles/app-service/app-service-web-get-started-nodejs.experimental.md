---
title: 快速入門：建立 Node.js Web 應用程式
description: 在短短幾分鐘內將您的第一個 Node.js Hello World 部署至 Azure App Service。 您可以使用 ZIP 套件進行部署，這是部署至 App Service 的眾多方式之一。
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/23/2019
ms.custom: mvc, devcenter, seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: 84953c79093ebd4e53e7e7feef5ab72b8afce002
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047533"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>在 Azure 中建立 Node.js Web 應用程式

> [!NOTE]
> 本文會將應用程式部署至 Windows 上的 App Service。 若要部署至 _Linux_ 上的 App Service，請參閱[在 Linux 上的 Azure App Service 中建立 Node.js Web 應用程式](./containers/quickstart-nodejs.md)。
>

[Azure App Service](overview.md) 可提供可高度擴充、自我修復的 Web 主控服務。  本快速入門會顯示如何將 Node.js 應用程式部署至 Azure App Service。 您將使用 [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 建立 Web 應用程式，但您也可以在本機使用 [Azure CLI](/cli/azure/install-azure-cli) 來執行這些命令。 您將使用 [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) 命令，將範例 Node.js 程式碼部署至 Web 應用程式。  

![在 Azure 中執行的範例應用程式](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

您可以使用 Mac、Windows 或 Linux 機器，依照此處的步驟操作。 完成這些步驟大約需要三分鐘。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>下載範例

在 Cloud Shell 中，建立快速入門目錄並變更為此目錄。

```console
mkdir quickstart

cd $HOME/quickstart
```

下一步，執行下列命令，將範例應用程式存放庫複製到您的快速入門目錄。

```console
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

執行上述命令時，會顯示類似下列範例的資訊：

```output
Cloning into 'nodejs-docs-hello-world'...
remote: Counting objects: 40, done.
remote: Total 40 (delta 0), reused 0 (delta 0), pack-reused 40
Unpacking objects: 100% (40/40), done.
Checking connectivity... done.
```

> [!NOTE]
> 範例 index.js 會將接聽的連接埠設定為 process.env.PORT。 這個環境變數是由 App Service 指派。
>

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-scus.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-scus.md)]

## <a name="create-a-web-app"></a>建立 Web 應用程式

在 Cloud Shell 中，使用 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 命令，在 `myAppServicePlan` App Service 方案中建立 Web 應用程式。

在下列範,了中，使用全域唯一的應用程式名稱 (有效的字元為 `a-z`、`0-9` 和 `-`) 取代 `<app_name>`。

```azurecli-interactive
# Bash and Powershell
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name>
```

建立 Web 應用程式後，Azure CLI 會顯示類似下列範例的輸出：

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="set-nodejs-runtime"></a>設定 Node.js 執行階段

將 Node 執行階段設定為 10.14.1。 若要查看所有支援的執行階段，請執行 [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes)。

```azurecli-interactive
# Bash and Powershell
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITE_NODE_DEFAULT_VERSION=10.14.1
```

瀏覽至您剛建立的 Web 應用程式。 以唯一的應用程式名稱取代 `<app_name>`。

```http
http://<app_name>.azurewebsites.net
```

新的 Web 應用程式看起來應該像這樣：![空的 Web 應用程式頁面](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

## <a name="deploy-zip-file"></a>部署 ZIP 檔案

在 Cloud Shell 中瀏覽至應用程式的根目錄，然後為範例專案建立新的 ZIP 檔案。

```console
cd nodejs-docs-hello-world  

zip -r myUpdatedAppFiles.zip *.*
```

使用 [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) 命令，將 ZIP 檔案部署至 Web 應用程式。  

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

此命令會將檔案和目錄從 ZIP 檔案部署到預設的 App Service 應用程式資料夾 (`\home\site\wwwroot`)，並重新啟動應用程式。 如果已設定任何其他的自訂建置程序，它也會執行。 如需詳細資訊，請參閱 [Kudu 文件](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)。

## <a name="browse-to-the-app"></a>瀏覽至應用程式

使用 web 瀏覽器瀏覽至已部署的應用程式。

```http
http://<app_name>.azurewebsites.net
```

Node.js 範例程式碼正在 Azure App Service Web 應用程式中執行。

![在 Azure 中執行的範例應用程式](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

> [!NOTE]
> 在 Azure App Service 中，應用程式會使用 [iisnode](https://github.com/Azure/iisnode) 執行於 IIS 中。 為了讓應用程式可使用 iisnode 執行，應用程式的根目錄會包含 web.config 檔案。 此檔案可供 IIS 讀取，且 iisnode 相關設定記錄在 [iisnode GitHub 存放庫](https://github.com/Azure/iisnode/blob/master/src/samples/configuration/web.config)中。

**恭喜！** 您已將第一個 Node.js 應用程式部署至 App Service。

## <a name="update-and-redeploy-the-code"></a>更新和重新部署程式碼

在 Cloud Shell 中，輸入 `code index.js` 以開啟 Cloud Shell 編輯器。

![Code index.js](media/app-service-web-get-started-nodejs-poc/code-indexjs.png)

在呼叫 `response.end` 時，對文字進行小幅變更：

```javascript
response.end("Hello Azure!");
```

儲存您的變更並結束編輯器。 使用 `^S` 命令進行儲存，以及使用 `^Q` 來結束作業。

建立 ZIP 檔案，並使用 [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) 命令加以部署。  

```azurecli-interactive
# Bash
zip -r myUpdatedAppFiles.zip *.*

az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

切換回在**瀏覽至應用程式**步驟中開啟的瀏覽器視窗，然後重新整理頁面。

![在 Azure 中執行的已更新範例應用程式](media/app-service-web-get-started-nodejs-poc/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>管理新的 Azure 應用程式

請移至 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>，以管理您所建立的 Web 應用程式。

按一下左側功能表中的 [應用程式服務]  ，然後按一下 Azure 應用程式的名稱。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

您會看到 Web 應用程式的 [概觀] 頁面。 您可以在這裡執行基本管理工作，像是瀏覽、停止、啟動、重新啟動及刪除。

![Azure 入口網站中的 App Service 頁面](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

左側功能表提供不同的頁面來設定您的應用程式。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Node.js with MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)
