---
title: 設定 Node.js 應用程式
description: 瞭解如何在 Azure App Service 中設定原生 Windows 實例或預先建立之 Linux 容器中的 Node.js 應用程式。 本文說明最常見的設定工作。
ms.custom: devx-track-js, devx-track-azurecli
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 8bdf637ab773e90a5eac42bcaa443cf6741db636
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696008"
---
# <a name="configure-a-nodejs-app-for-azure-app-service"></a>設定適用于 Azure App Service 的 Node.js 應用程式

您必須使用所有必要的 NPM 相依性來部署 Node.js 應用程式。 `npm install --production`當您部署[Git 存放庫](deploy-local-git.md)或啟用組建自動化的[Zip 套件](deploy-zip.md)時，會自動為您執行 App Service 部署引擎。 但是，如果您使用 [FTP/S](deploy-ftp.md)部署檔案，則需要手動上傳所需的套件。

本指南提供部署至 App Service 之 Node.js 開發人員的重要概念和指示。 如果您從未使用過 Azure App Service，請遵循 [Node.js 快速入門](quickstart-nodejs.md) ，並先 [ 利用 MongoDB 教學課程進行Node.js](tutorial-nodejs-mongodb-app.md) 。

## <a name="show-nodejs-version"></a>顯示 Node.js 版本

::: zone pivot="platform-windows"  

若要顯示目前的 Node.js 版本，請在 [Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp config appsettings list --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

若要顯示所有支援的 Node.js 版本，請在 [Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp list-runtimes | grep node
```

::: zone-end

::: zone pivot="platform-linux"

若要顯示目前的 Node.js 版本，請在 [Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要顯示所有支援的 Node.js 版本，請在 [Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

::: zone-end

## <a name="set-nodejs-version"></a>設定 Node.js 版本

::: zone pivot="platform-windows"  

若要將您的應用程式設定為 [支援的 Node.js 版本](#show-nodejs-version)，請在 [Cloud Shell](https://shell.azure.com) 中執行下列命令，以設定 `WEBSITE_NODE_DEFAULT_VERSION` 為支援的版本：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

這項設定會指定在執行時間期間，以及在 App Service 組建自動化期間自動封裝還原期間要使用的 Node.js 版本。

> [!NOTE]
> 您應該設定專案中的 Node.js 版本 `package.json` 。 部署引擎會在個別的進程中執行，其中包含所有支援的 Node.js 版本。

::: zone-end

::: zone pivot="platform-linux"

若要將您的應用程式設定為 [支援的 Node.js 版本](#show-nodejs-version)，請在 [Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

這項設定會指定在執行時間，以及在 Kudu 中自動封裝還原期間要使用的 Node.js 版本。

> [!NOTE]
> 您應該設定專案中的 Node.js 版本 `package.json` 。 部署引擎會在個別的容器中執行，其中包含所有支援的 Node.js 版本。

::: zone-end

## <a name="get-port-number"></a>取得埠號碼

您 Node.js 應用程式需要接聽正確的埠，才能接收傳入要求。

::: zone pivot="platform-windows"  

在 Windows App Service 中，Node.js 應用程式是以 [IISNode](https://github.com/Azure/iisnode)裝載，而您的 Node.js 應用程式應接聽變數中指定的埠 `process.env.PORT` 。 下列範例示範如何在簡單的 Express 應用程式中執行它：

::: zone-end

::: zone pivot="platform-linux"  

App Service 設定 `PORT` Node.js 容器中的環境變數，並將傳入要求轉送至您的容器（位於該埠號碼）。 若要接收要求，您的應用程式應該使用接聽該埠 `process.env.PORT` 。 下列範例示範如何在簡單的 Express 應用程式中執行它：

::: zone-end

```javascript
const express = require('express')
const app = express()
const port = process.env.PORT || 3000

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`)
})
```

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>自訂組建自動化

如果您使用 Git 或 zip 套件並開啟組建自動化來部署應用程式，App Service 組建自動化將會依下列順序逐步執行：

1. 執行自訂指令碼 (如果 `PRE_BUILD_SCRIPT_PATH` 已指定)。
1. 在 `npm install` 不含任何旗標的情況下執行，其中包括 npm `preinstall` 和 `postinstall` 腳本，也會進行安裝 `devDependencies` 。
1. `npm run build`如果您在 *package.js* 中指定組建腳本，請執行。
1. `npm run build:azure`如果組建： azure 腳本是在您 *的package.js* 中指定的，請執行。
1. 執行自訂指令碼 (如果 `POST_BUILD_SCRIPT_PATH` 已指定)。

> [!NOTE]
> 如 [npm](https://docs.npmjs.com/misc/scripts)檔中所述，在 `prebuild` 指定的情況下，分別在 `postbuild` 之前和之後執行的腳本 `build` 。 `preinstall` 和 `postinstall` `install` 分別執行。

`PRE_BUILD_COMMAND` 和 `POST_BUILD_COMMAND` 是預設為空值的環境變數。 若要執行建置前命令，請定義 `PRE_BUILD_COMMAND`。 若要執行建置後命令，請定義 `POST_BUILD_COMMAND`。

下列範例會將兩個變數指定給一系列的命令 (以逗號分隔)。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

若要了解其他可自訂組建自動化的環境變數，請參閱 [Oryx 設定](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

如需 App Service 如何在 Linux 中執行和建立 Node.js 應用程式的詳細資訊，請參閱 [>oryx 檔：如何偵測和建立 Node.js 應用程式](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)。

## <a name="configure-nodejs-server"></a>設定 Node.js server

Node.js 的容器隨附于 [PM2](https://pm2.keymetrics.io/)，也就是生產過程管理員。 您可以將應用程式設定為以 PM2、使用 NPM 或使用自訂命令來開始。

- [執行自訂命令](#run-custom-command)
- [執行 npm 開始](#run-npm-start)
- [使用 PM2 執行](#run-with-pm2)

### <a name="run-custom-command"></a>執行自訂命令

App Service 可以使用自訂命令來啟動應用程式，例如可執行檔（例如 *run.sh*）。例如，若要執行 `npm run start:prod` ，請在 [Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>執行 npm 開始

若要使用來啟動您的應用程式 `npm start` ，請確定 `start` 腳本位於 *package.js* 檔案中。 例如：

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

若要在您的專案中使用自訂 *package.js* ，請在 [Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>使用 PM2 執行

當您的專案中找到其中一個常見 Node.js 檔案時，容器會自動使用 PM2 啟動您的應用程式：

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- 下列其中一個 [PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)檔： *process.json* 和 *ecosystem.config.js*

您也可以使用下列延伸模組來設定自訂啟動檔案：

- *.Js* 檔案
- 副檔名為 *.config.js*、 *. yaml* 或 *yml* 的 [PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) *檔。*

若要新增自訂啟動檔案，請在 [Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>遠端偵錯

> [!NOTE]
> 遠端偵錯程式目前為預覽狀態。

如果您將 Node.js 應用程式設定為以 [PM2 執行](#run-with-pm2)，則可以在 [Visual Studio Code](https://code.visualstudio.com/)中遠端進行偵錯工具，除非您使用 * .config.js、*. yml 或 *yaml* 來執行它。

在大部分情況下，您的應用程式不需要額外的設定。 如果您的應用程式是以檔案 *上的process.js* 執行 (預設或自訂) ，它必須 `script` 在 JSON 根目錄中有屬性。 例如：

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

若要設定遠端偵錯程式的 Visual Studio Code，請安裝 [App Service 擴充](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)功能。 遵循 [擴充功能] 頁面上的指示，並在 Visual Studio Code 中登入 Azure。

在 [Azure explorer] 中，尋找您想要進行偵錯工具的應用程式，並以滑鼠右鍵按一下它，然後選取 [ **開始遠端偵錯**]。 按一下 **[是]** 為您的應用程式啟用它。 App Service 會為您啟動通道 proxy，並附加偵錯工具。 然後，您可以對應用程式提出要求，並查看偵錯工具在中斷點處暫停。

完成偵錯工具之後，請選取 **[中斷連線]** 以停止偵錯工具。 出現提示時，您應該按一下 **[是]** ，以停用遠端偵錯。 若要在稍後停用它，請在 Azure explorer 中，再次以滑鼠右鍵按一下您的應用程式，然後選取 [**停用遠端偵錯**

::: zone-end

## <a name="access-environment-variables"></a>存取環境變數

在 App Service 中，您可以於應用程式的程式碼外部[設定應用程式設定](configure-common.md)。 然後，您可以使用標準 Node.js 模式來存取它們。 例如，若要存取稱為 `NODE_ENV` 的應用程式設定，請使用下列程式碼：

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>執行 Grunt/Bower/Gulp

根據預設，當 App Service build automation `npm install --production` 可辨識 Node.js 的應用程式是透過 Git 進行部署，或啟用組建自動化的 Zip 部署時，即會執行。 如果您的應用程式需要任何熱門的自動化工具，例如 Grunt、Bower 或 Gulp，您需要提供 [自訂部署腳本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) 來執行它。

若要讓您的存放庫能夠執行這些工具，您必須將它們新增至 *package.js* 的相依性。 例如：

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

從本機終端機視窗中，將目錄變更為您的存放庫根目錄，然後執行下列命令：

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

您的存放庫根目錄現在有兩個額外的檔案： *. deployment* 和 *deploy.sh*。

開啟 *deploy.sh* 並尋找 `Deployment` 區段，如下所示：

```bash
##################################################################################################################################
# Deployment
# ----------
```

本節的結尾是執行 `npm install --production` 。 在區段 *結尾* 新增執行必要工具所需的程式碼區段 `Deployment` ：

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

請參閱 [MEAN.js 範例中的範例](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)，其中部署腳本也會執行自訂 `npm install` 命令。

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

## <a name="detect-https-session"></a>偵測 HTTPS 工作階段

在 App Service 中，[SSL 終止](https://wikipedia.org/wiki/TLS_termination_proxy)會在網路負載平衡器上發生，因此所有的 HTTPS 要求都會以未加密 HTTP 要求的形式進入您的應用程式。 如果您的應用程式邏輯需要檢查使用者要求是否有加密，請檢查 `X-Forwarded-Proto` 標頭。

熱門的 Web 架構可讓您在標準的應用程式模式中存取 `X-Forwarded-*` 資訊。 在 [Express](https://expressjs.com/)中，您可以使用 [信任](https://expressjs.com/guide/behind-proxies.html)proxy。 例如：

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>存取診斷記錄

::: zone pivot="platform-windows"  

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>疑難排解

當工作的 Node.js 應用程式在 App Service 中的行為不同，或發生錯誤時，請嘗試下列動作：

- [存取記錄資料流](#access-diagnostic-logs)。
- 在生產模式中於本機測試應用程式。 App Service 會在生產模式中執行 node.js 應用程式，因此您必須確定項目生產模式下按預期在本機中運作。 例如：
    - 根據您 *的package.js*，可能會針對生產模式安裝不同的封裝， (`dependencies` 與 `devDependencies`) 。
    - 某些 web 架構可以在生產模式中以不同的方式部署靜態檔案。
    - 在生產模式中執行時，某些 web 架構可能會使用自訂啟動指令碼。
- 在開發模式中 App Service 執行您的應用程式。 例如，在 [MEAN.js](https://meanjs.org/)中，您可以藉由 [設定 `NODE_ENV` 應用程式設定](configure-common.md)，在執行時間中將應用程式設定為開發模式。

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 MongoDB 的 Node.js 應用程式](tutorial-nodejs-mongodb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [App Service Linux 常見問題集](faq-app-service-linux.md)

::: zone-end

