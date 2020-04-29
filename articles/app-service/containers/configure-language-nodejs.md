---
title: 設定 node.js 應用程式
description: 瞭解如何為您的應用程式設定預先建立的 node.js 容器。 本文說明最常見的設定工作。
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: fdc5129fc395f99cb4c244414ea952b2776dc4dc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79252723"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>設定適用于 Azure App Service 的 Linux Node.js 應用程式

您必須使用所有必要的 NPM 相依性來部署 node.js 應用程式。 當您部署[Git 存放庫](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)或已開啟`npm install --production`組建進程的[Zip 套件](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)時，就會自動為您執行 App Service 部署引擎（Kudu）。 不過，如果您使用[FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)部署檔案，就必須手動上傳所需的套件。

本指南提供在 App Service 中使用內建 Linux 容器的 node.js 開發人員的重要概念和指示。 如果您從未使用過 Azure App Service，請先遵循 node.js[快速入門](quickstart-nodejs.md)和 Node.js [with MongoDB 教學](tutorial-nodejs-mongodb-app.md)課程。

## <a name="show-nodejs-version"></a>顯示 node.js 版本

若要顯示目前的 node.js 版本，請在[Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要顯示所有支援的 node.js 版本，請在[Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>設定 node.js 版本

若要將您的應用程式設定為[支援的 node.js 版本](#show-nodejs-version)，請在[Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

此設定會指定要在執行時間期間以及在 Kudu 的自動化封裝還原期間使用的 node.js 版本。

> [!NOTE]
> 您應該在專案的`package.json`中設定 node.js 版本。 部署引擎會在個別容器中執行，其中包含所有支援的 node.js 版本。

## <a name="customize-build-automation"></a>自訂群組建自動化

如果您使用 Git 或已開啟組建自動化的 zip 套件來部署應用程式，App Service 組建自動化會逐步執行下列順序：

1. 執行自訂腳本（如果`PRE_BUILD_SCRIPT_PATH`有指定）。
1. 在`npm install`沒有任何旗標的情況下`preinstall`執行`postinstall` ，其中包括 npm `devDependencies`和腳本，而且也會安裝。
1. 如果`npm run build`您的*package*中指定了組建腳本，請執行。
1. 如果`npm run build:azure`組建：在您的*package*中指定了 azure 腳本，請執行。
1. 執行自訂腳本（如果`POST_BUILD_SCRIPT_PATH`有指定）。

> [!NOTE]
> 如[npm](https://docs.npmjs.com/misc/scripts)檔中所述，分別`prebuild`命名`postbuild`為並在前面`build`和之後執行的腳本（如果有指定）。 `preinstall`和`postinstall`會分別在之前`install`和之後執行。

`PRE_BUILD_COMMAND`和`POST_BUILD_COMMAND`是預設為空白的環境變數。 若要執行預先建立的命令， `PRE_BUILD_COMMAND`請定義。 若要執行建立後命令，請`POST_BUILD_COMMAND`定義。

下列範例會指定一系列命令的兩個變數，並以逗號分隔。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

如需其他環境變數以自訂群組建自動化，請參閱[Oryx configuration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

如需有關 App Service 如何在 Linux 中執行和建立 node.js 應用程式的詳細資訊，請參閱[Oryx 檔：如何偵測和建立 node.js 應用程式](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)。

## <a name="configure-nodejs-server"></a>設定 node.js 伺服器

Node.js 容器隨附[PM2](https://pm2.keymetrics.io/)，這是一個生產程式管理員。 您可以將應用程式設定為從 PM2 或使用 NPM，或使用自訂命令來啟動。

- [執行自訂命令](#run-custom-command)
- [執行 npm 開始](#run-npm-start)
- [以 PM2 執行](#run-with-pm2)

### <a name="run-custom-command"></a>執行自訂命令

App Service 可以使用自訂命令來啟動您的應用程式，例如*run.sh*之類的可執行檔。例如，若要執行`npm run start:prod`，請在[Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>執行 npm 開始

若要使用`npm start`來啟動您的應用程式， `start`請確定腳本位於*package. json*檔案中。 例如：

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

若要在專案中使用自訂*封裝 json* ，請在[Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>以 PM2 執行

當您的專案中找到其中一個通用 node.js 檔案時，容器會自動使用 PM2 啟動您的應用程式：

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart .js*
- 下列其中一個[PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)檔：*處理常式. json*和*生態系統*

您也可以使用下列延伸模組來設定自訂起始檔案：

- *.Js*檔案
- [PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)檔案，副檔名為*json*、 *.config*、 *yaml*或*yml。*

若要新增自訂啟動檔案，請在[Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>遠端偵錯

> [!NOTE]
> 遠端偵錯功能目前為預覽狀態。

如果您將 node.js 應用程式設定為以[PM2 執行](#run-with-pm2)，則您可以在[Visual Studio Code](https://code.visualstudio.com/)中從遠端進行偵錯工具，除非您使用 * .js、*. yml 或*yaml*來執行它。

在大部分情況下，您的應用程式不需要進行額外的設定。 如果您的應用程式是使用*進程. json*檔案（預設或自訂）執行，它在`script` json 根目錄中必須有屬性。 例如：

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

若要設定遠端偵錯程式的 Visual Studio Code，請安裝[App Service 延伸](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)模組。 依照 [擴充功能] 頁面上的指示進行，並在 Visual Studio Code 中登入 Azure。

在 [Azure explorer] 中，尋找您想要進行 debug 的應用程式，以滑鼠右鍵按一下它，然後選取 [**啟動遠端**偵測]。 按一下 **[是]** ，為您的應用程式啟用它。 App Service 為您啟動通道 proxy，並附加偵錯工具。 然後，您可以對應用程式提出要求，並查看偵錯工具在中斷點處暫停。

完成偵錯工具後，選取 **[中斷連線]** 來停止偵錯工具。 出現提示時，您應該按一下 **[是]** 以停用遠端偵錯。 若要稍後停用它，請在 Azure explorer 中再次以滑鼠右鍵按一下您的應用程式，然後選取 [**停用遠端**偵測]。

## <a name="access-environment-variables"></a>存取環境變數

在 App Service 中，您可以於應用程式的程式碼外部[設定應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 接著，您可以使用標準的 node.js 模式來存取它們。 例如，若要存取稱為 `NODE_ENV` 的應用程式設定，請使用下列程式碼：

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>執行 Grunt/Bower/Gulp

根據預設，Kudu 會`npm install --production`在辨識出已部署的 node.js 應用程式時執行。 如果您的應用程式需要任何熱門的自動化工具，例如 Grunt、Bower 或 Gulp，您必須提供[自訂部署腳本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)來執行它。

若要讓您的存放庫能夠執行這些工具，您需要將它們新增至*package. json*中的相依性。 例如：

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

從本機終端機視窗中，將目錄切換到您的存放庫根目錄，然後執行下列命令：

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

您的存放庫根目錄現在有兩個額外的檔案： *. deployment*和*deploy.sh*。

開啟*deploy.sh* ，並尋找`Deployment`區段，如下所示：

```bash
##################################################################################################################################
# Deployment
# ----------
```

本節結束于執行`npm install --production`。 在`Deployment`區段*結尾處*，新增您需要執行必要工具的程式碼區段：

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

請參閱[MEAN 範例中的範例](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)，其中部署腳本也會執行自訂`npm install`命令。

### <a name="bower"></a>Bower

此程式碼片段`bower install`會執行。

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

此程式碼片段`gulp imagemin`會執行。

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

此程式碼片段`grunt`會執行。

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

熱門的 Web 架構可讓您在標準的應用程式模式中存取 `X-Forwarded-*` 資訊。 在[Express](https://expressjs.com/)中，您可以使用[信任](https://expressjs.com/guide/behind-proxies.html)proxy。 例如：

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>存取診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在瀏覽器中開啟 SSH 工作階段

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>疑難排解

當工作中的 node.js 應用程式在 App Service 中有不同的行為，或發生錯誤時，請嘗試下列動作：

- [存取記錄資料流](#access-diagnostic-logs)。
- 以生產模式在本機測試應用程式。 App Service 會在生產模式中執行您的 node.js 應用程式，因此您必須確定您的專案在本機的生產模式中如預期般運作。 例如：
    - 視您的*封裝*而定，可能會針對生產模式（`dependencies` vs. `devDependencies`）安裝不同的套件。
    - 某些 web 架構可能會在生產模式中以不同的方式部署靜態檔案。
    - 某些 web 架構在生產模式中執行時，可能會使用自訂啟動腳本。
- 在開發模式的 App Service 中執行您的應用程式。 例如，在[MEAN](https://meanjs.org/)中，您可以藉由[設定`NODE_ENV`應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)，在執行時間將應用程式設定為開發模式。

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 MongoDB 的 Node.js 應用程式](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux 常見問題集](app-service-linux-faq.md)
