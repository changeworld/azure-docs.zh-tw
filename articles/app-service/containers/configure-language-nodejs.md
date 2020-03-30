---
title: 配置 Node.js 應用
description: 瞭解如何為應用配置預構建的 Node.js 容器。 本文說明最常見的設定工作。
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: fdc5129fc395f99cb4c244414ea952b2776dc4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252723"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>為 Azure 應用服務配置 Linux Node.js 應用

必須使用所有必需的 NPM 依賴項部署 Node.js 應用。 當您部署[Git 存儲庫](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)或啟動生成進程的`npm install --production`Zip[包](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)時，應用服務部署引擎 （Kudu） 會自動為您運行。 但是，如果使用[FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)部署檔，則需要手動上載所需的包。

本指南為在應用服務中使用內置 Linux 容器的 Node.js 開發人員提供了關鍵概念和說明。 如果您從未使用過 Azure 應用服務，請先使用 MongoDB 教程，請先按照[Node.js 快速入門](quickstart-nodejs.md)和[Node.js 進行教程](tutorial-nodejs-mongodb-app.md)。

## <a name="show-nodejs-version"></a>顯示 Node.js 版本

要顯示當前 Node.js 版本，在[雲殼](https://shell.azure.com)中運行以下命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

要顯示所有受支援的 Node.js 版本，可在[雲外殼](https://shell.azure.com)中運行以下命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>設置 Node.js 版本

要將應用設置為[受支援的 Node.js 版本](#show-nodejs-version)，請在[雲外殼](https://shell.azure.com)中運行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

此設置指定在運行時和 Kudu 自動包還原期間要使用的 Node.js 版本。

> [!NOTE]
> 應在專案的 中設置 Node.js 版本`package.json`。 部署引擎在包含所有受支援的 Node.js 版本的單獨容器中運行。

## <a name="customize-build-automation"></a>自訂構建自動化

如果使用打開的 Git 或 zip 包部署應用，應用服務將構建自動化步驟，執行以下順序：

1. 如果由`PRE_BUILD_SCRIPT_PATH`指定，則運行自訂腳本。
1. 運行`npm install`沒有任何標誌，其中包括 npm`preinstall`和`postinstall`腳本，並且還安裝`devDependencies`。
1. 如果在`npm run build`*包*中指定了生成腳本，請運行 。
1. 如果在`npm run build:azure`*包*中指定了生成：azure 腳本，請運行 。
1. 如果由`POST_BUILD_SCRIPT_PATH`指定，則運行自訂腳本。

> [!NOTE]
> 如[npm 文檔中](https://docs.npmjs.com/misc/scripts)所述，如果指定`prebuild`，`postbuild`分別在`build`之前和之後命名和運行的腳本。 `preinstall`並`postinstall`分別在之前和之後`install`運行。

`PRE_BUILD_COMMAND`和`POST_BUILD_COMMAND`是預設情況下為空的環境變數。 要運行預生成命令，請定義`PRE_BUILD_COMMAND`。 要運行生成後命令，請定義`POST_BUILD_COMMAND`。

下面的示例指定一系列命令的兩個變數，這些命令用逗號分隔。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

有關自訂生成自動化的其他環境變數，請參閱[Oryx 配置](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

有關應用服務如何運行和構建 Linux 中的 Node.js 應用的詳細資訊，請參閱[Oryx 文檔：如何檢測和生成 Node.js 應用](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)。

## <a name="configure-nodejs-server"></a>配置 Node.js 伺服器

Node.js 容器附帶生產流程管理器[PM2。](https://pm2.keymetrics.io/) 您可以將應用配置為從 PM2、NPM 或自訂命令開始。

- [運行自訂命令](#run-custom-command)
- [運行 npm 開始](#run-npm-start)
- [使用 PM2 運行](#run-with-pm2)

### <a name="run-custom-command"></a>運行自訂命令

應用服務可以使用自訂命令啟動應用，例如*run.sh*等可執行檔。例如，要運行`npm run start:prod`，在[雲殼](https://shell.azure.com)中運行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>運行 npm 開始

要使用`npm start`啟動應用，只需確保`start`腳本位於*包.json*檔中。 例如：

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

要在專案中使用自訂*包.json，* 請在[雲殼](https://shell.azure.com)中運行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>使用 PM2 運行

當專案中找到一個常見的 Node.js 檔時，容器會自動使用 PM2 啟動應用：

- *賓/www*
- *server.js*
- *app.js*
- *index.js*
- *託管 start.js*
- 以下[PM2 檔](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)之一 ：*進程.json*和*生態系統.config.js*

您還可以配置具有以下副檔名的自訂開機檔案：

- *.js*檔
- 具有副檔名 .json、.config.js、.yaml *.yaml*或 *.yml*的 *.json*[PM2 檔](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)* *

要添加自訂開機檔案，在[雲殼](https://shell.azure.com)中運行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>遠端偵錯

> [!NOTE]
> 遠端偵錯當前處於預覽狀態。

如果將 Node.js 應用配置為[使用 PM2 運行](#run-with-pm2)，則可以在[Visual Studio 代碼](https://code.visualstudio.com/)中遠端偵錯它，但使用 *.config.js、*.yml 或 *.yaml*運行該應用時除外。

在大多數情況下，你的應用不需要額外的配置。 如果應用使用*進程.json*檔（預設或自訂）運行，則它必須在 JSON 根中具有屬性`script`。 例如：

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

要設置用於遠端偵錯的視覺化工作室代碼，請安裝[應用服務擴展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)。 按照擴展頁上的說明操作，並在視覺化工作室代碼中登錄到 Azure。

在 Azure 資源管理器中，查找要調試的應用，按右鍵它並選擇 **"啟動遠端偵錯**"。 按一下"**是**"可為應用啟用它。 應用服務為您啟動隧道代理並附加調試器。 然後，您可以向應用發出請求，並看到調試器在中斷點暫停。

完成調試後，通過選擇 **"斷開連接"** 停止調試器。 當出現提示時，應按一下 **"是**"以禁用遠端偵錯。 要稍後禁用它，請在 Azure 資源管理器中再次按右鍵應用，然後選擇 **"禁用遠端偵錯**"。

## <a name="access-environment-variables"></a>存取環境變數

在 App Service 中，您可以於應用程式的程式碼外部[設定應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 然後，您可以使用標準 Node.js 模式訪問它們。 例如，若要存取稱為 `NODE_ENV` 的應用程式設定，請使用下列程式碼：

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>運行葛籣特/鮑爾/古爾普

預設情況下，庫杜在部署`npm install --production`Node.js 應用時運行。 如果你的應用需要任何流行的自動化工具，如 Grunt、Bower 或 Gulp，則需要提供[自訂部署腳本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)來運行它。

要使存儲庫能夠運行這些工具，您需要將它們添加到*包的依賴項。* 例如：

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

從本地終端視窗，將目錄更改為存儲庫根目錄並運行以下命令：

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

存儲庫根現在有兩個附加檔 *：.部署*和*deploy.sh*。

打開*deploy.sh*並查找`Deployment`該部分，如下所示：

```bash
##################################################################################################################################
# Deployment
# ----------
```

本節以運行`npm install --production`結尾。 添加在`Deployment`本節*末尾*運行所需工具所需的代碼部分：

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

請參閱[MEAN.js 示例中的一個示例](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)，其中部署腳本還運行自訂`npm install`命令。

### <a name="bower"></a>Bower

此程式碼片段`bower install`運行 。

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

此程式碼片段`gulp imagemin`運行 。

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

此程式碼片段`grunt`運行 。

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

熱門的 Web 架構可讓您在標準的應用程式模式中存取 `X-Forwarded-*` 資訊。 在[Express](https://expressjs.com/)中，您可以使用[信任代理](https://expressjs.com/guide/behind-proxies.html)。 例如：

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

當工作 Node.js 應用在應用服務中的行為不同或有錯誤時，請嘗試以下操作：

- [存取記錄資料流](#access-diagnostic-logs)。
- 在生產模式下本地測試應用。 應用服務在生產模式下運行 Node.js 應用，因此您需要確保專案在本地生產模式下按預期工作。 例如：
    - 根據您的*包.json，* 不同的套裝軟體可能安裝不同的生產模式（`dependencies`與 。 `devDependencies`
    - 某些 Web 框架在生產模式下可能以不同的方式部署靜態檔。
    - 某些 Web 框架在生產模式下運行時可能會使用自訂啟動腳本。
- 在開發模式下在應用服務中運行應用。 例如，在[MEAN.js](https://meanjs.org/)中，您可以通過[設置`NODE_ENV`應用設置](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)在運行時將應用設置為開發模式。

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教程： Node.js 應用程式與蒙戈DB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux 常見問題集](app-service-linux-faq.md)
