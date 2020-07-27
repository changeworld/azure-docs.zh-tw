---
title: 設定 Windows Node.js 應用程式
description: 瞭解如何在 App Service 的原生 Windows 實例中設定 Node.js 應用程式。 本文說明最常見的設定工作。
ms.custom: devx-track-javascript
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 0fc6ed5cb090653e381d82f484d355a514520c62
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170909"
---
# <a name="configure-a-windows-nodejs-app-for-azure-app-service"></a>設定適用于 Azure App Service 的 Windows Node.js 應用程式

Node.js 應用程式必須使用所有必要的 NPM 相依性進行部署。 `npm install --production`當您部署[Git 存放庫](deploy-local-git.md)或已啟用組建自動化的[Zip 套件](deploy-zip.md)時，會自動為您執行 App Service 部署引擎。 不過，如果您使用[FTP/S](deploy-ftp.md)部署檔案，就必須手動上傳所需的套件。 如需 Linux 應用程式的相關資訊，請參閱[設定適用于 Azure App Service 的 LINUX PHP 應用程式](containers/configure-language-nodejs.md)。

本指南提供部署至 App Service 之 Node.js 開發人員的重要概念和指示。 如果您從未使用過 Azure App Service，請先遵循[Node.js 快速入門](app-service-web-get-started-nodejs.md)和[使用 MongoDB 進行Node.js 教學](app-service-web-tutorial-nodejs-mongodb-app.md)課程。

## <a name="show-nodejs-version"></a>顯示 Node.js 版本

若要顯示目前的 Node.js 版本，請在[Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

若要顯示所有支援的 Node.js 版本，請在[Cloud Shell](https://shell.azure.com)中執行下列命令：

```azurecli-interactive
az webapp list-runtimes | grep node
```

## <a name="set-nodejs-version"></a>設定 Node.js 版本

若要將您的應用程式設定為[支援的 Node.js 版本](#show-nodejs-version)，請在[Cloud Shell](https://shell.azure.com)中執行下列命令，以將設定 `WEBSITE_NODE_DEFAULT_VERSION` 為支援的版本：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

此設定會指定在執行時間期間以及在 App Service 組建自動化期間自動封裝還原期間，所要使用的 Node.js 版本。

> [!NOTE]
> 您應該在專案的中設定 Node.js 版本 `package.json` 。 部署引擎會在個別的進程中執行，其中包含所有支援的 Node.js 版本。

## <a name="access-environment-variables"></a>存取環境變數

在 App Service 中，您可以於應用程式的程式碼外部[設定應用程式設定](configure-common.md)。 接著，您可以使用標準 Node.js 模式來存取它們。 例如，若要存取稱為 `NODE_ENV` 的應用程式設定，請使用下列程式碼：

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>執行 Grunt/Bower/Gulp

根據預設，App Service 組建自動化會 `npm install --production` 在辨識出透過 Git 部署的 Node.js 應用程式時執行（或已啟用組建自動化的 Zip 部署）。 如果您的應用程式需要任何熱門的自動化工具，例如 Grunt、Bower 或 Gulp，您必須提供[自訂部署腳本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)來執行它。

若要讓您的存放庫執行這些工具，您需要將它們新增至*package.js上*的相依性。 例如：

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

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>疑難排解

當工作 Node.js 應用程式在 App Service 中的行為不同，或發生錯誤時，請嘗試下列動作：

- [存取記錄資料流](#access-diagnostic-logs)。
- 在生產模式中於本機測試應用程式。 App Service 會在生產模式中執行 node.js 應用程式，因此您必須確定項目生產模式下按預期在本機中運作。 例如：
    - 根據您*的package.js*而定，可能會針對生產模式（ `dependencies` vs.）安裝不同的套件 `devDependencies` 。
    - 某些 web 架構可以在生產模式中以不同的方式部署靜態檔案。
    - 在生產模式中執行時，某些 web 架構可能會使用自訂啟動指令碼。
- 在開發模式的 App Service 中執行您的應用程式。 例如，在[MEAN.js](https://meanjs.org/)中，您可以藉由[設定 `NODE_ENV` 應用程式設定](configure-common.md)，在執行時間中將應用程式設定為開發模式。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 MongoDB 的 Node.js 應用程式](app-service-web-tutorial-nodejs-mongodb-app.md)

