---
title: 教學課程：使用 MongoDB 的 Node.js 應用程式
description: 了解如何藉由連線至 Azure 中的 MongoDB 資料庫 (Cosmos DB)，讓 Node.js 應用程式在 Azure 中運作。 此教學課程中將使用 MEAN.js。
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/16/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-js
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 4fc79f8508f46f5003b99289d725b303feef78aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91312000"
---
# <a name="tutorial-build-a-nodejs-and-mongodb-app-in-azure"></a>教學課程：在 Azure 中建置 Node.js 和 MongoDB 應用程式

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) 可提供可高度擴充、自我修復的 Web 主控服務。 本教學課程示範如何在 Windows 上的 App Service 中建立 Node.js 應用程式，並將其連線到 MongoDB 資料庫。 完成之後，您的 MEAN 應用程式 (MongoDB、Express、AngularJS 及 Node.js) 將會在 [Azure App Service](overview.md) 中執行。 為了簡單起見，範例應用程式會使用 [MEAN.js web 架構](https://meanjs.org/)。

::: zone-end

::: zone pivot="platform-linux"


[Azure App Service](overview.md) 使用 Linux 作業系統提供可高度擴充、自我修復的 Web 主機服務。 本教學課程說明如何在 Linux 上的 App Service 中建立 Node.js 應用程式，將其連線至本機 MongoDB 資料庫，然後在 Azure Cosmos DB 的 MongoDB 版 API 中將其部署至資料庫。 完成之後，您的 MEAN 應用程式 (MongoDB、Express、AngularJS 及 Node.js) 將會在 Linux 上的 App Service 中執行。 為了簡單起見，範例應用程式會使用 [MEAN.js web 架構](https://meanjs.org/)。

::: zone-end

![在 Azure App Service 中執行的 MEAN.js 應用程式](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

您將學到什麼：

> [!div class="checklist"]
> * 在 Azure 中建立 MongoDB 資料庫
> * 將 Node.js 應用程式連線至 MongoDB
> * 將應用程式部署至 Azure
> * 將資料模型更新並將應用程式重新部署
> * 來自 Azure 的串流診斷記錄
> * 在 Azure 入口網站中管理應用程式

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

若要完成本教學課程：

1. [安裝 Git](https://git-scm.com/)
2. [安裝 Node.js 和 NPM](https://nodejs.org/)
3. [安裝 Bower](https://bower.io/) ([MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started) 的必要項目)
4. [安裝 Gulp.js](https://gulpjs.com/) ([MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started) 的必要項目)
5. [安裝及執行 MongoDB Community 版本](https://docs.mongodb.com/manual/administration/install-community/) 

## <a name="test-local-mongodb"></a>測試本機的 MongoDB

開啟終端機視窗，然後 `cd` 至 MongoDB 安裝的 `bin` 目錄。 您可使用這個終端機視窗來執行本教學課程中的所有命令。

在終端機上執行 `mongo`，以連接到本機的 MongoDB 伺服器。

```bash
mongo
```

如果連接成功，則您的 MongoDB 資料庫已在執行中。 如果沒有，請確定您的本機 MongoDB 資料庫已遵循[安裝 MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) 中的步驟來啟動。 MongoDB 通常已安裝，但您仍需要執行 `mongod` 才能將它啟動。 

當您完成測試 MongoDB 資料庫時，在終端機上輸入 `Ctrl+C`。 

## <a name="create-local-nodejs-app"></a>建立本機的 Node.js 應用程式

在此步驟中，您要設定本機的 Node.js 專案。

### <a name="clone-the-sample-application"></a>複製範例應用程式

在終端機視窗中，使用 `cd` 移至工作目錄。  

執行下列命令來複製範例存放庫。 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

此範例存放庫包含 [MEAN.js 存放庫](https://github.com/meanjs/mean)的副本。 若要在 App Service 上執行，會將它做修改 (如需詳細資訊，請參閱 MEAN.js 存放庫[讀我檔案](https://github.com/Azure-Samples/meanjs/blob/master/README.md))。

### <a name="run-the-application"></a>執行應用程式

執行下列命令以安裝必要的套件，然後啟動應用程式。

```bash
cd meanjs
npm install
npm start
```

忽略 config.domain 警告。 當應用程式完全載入時，您會看到類似下列的訊息：

<pre>
--
MEAN.JS - 開發環境

環境：     開發伺服器：        http://0.0.0.0:3000 資料庫：        mongodb://localhost/mean-dev 應用程式版本：   0.5.0 MEAN.JS 版本：0.5.0 --
</pre>

在瀏覽器中，瀏覽至 `http://localhost:3000` 。 按一下上層功能表中的 [註冊]，然後建立測試使用者。 

MEAN.js 範例應用程式會將使用者資料儲存於資料庫中。 如果您成功建立使用者並且登入，則您的應用程式正在將資料寫入本機 MongoDB 資料庫。

![MEAN.js 成功連線至 MongoDB](./media/tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

選取 [系統管理員] > [管理文章] 來新增一些文章。

如需隨時停止 Node.js，請在終端機上按下 `Ctrl+C`。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>建立生產環境 MongoDB

在此步驟中，您要在 Azure 中建立 MongoDB 資料庫。 當您的應用程式部署至 Azure 時，它會使用此雲端資料庫。

針對 MongoDB，本教學課程使用 [Azure Cosmos DB](/azure/documentdb/)。 Cosmos DB 支援 MongoDB 用戶端連線。

### <a name="create-a-resource-group"></a>建立資源群組

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-cosmos-db-account"></a>建立 Cosmos DB 帳戶

> [!NOTE]
> 在本教學課程中，當您在自己的 Azure 訂用帳戶中建立 Azure Cosmos DB 資料庫時會產生費用。 若要使用為期七天的免費 Azure Cosmos DB 帳戶，您可以使用[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 的體驗。 直接按一下 [MongoDB] 圖格中的 [建立] 按鈕，在 Azure 上建立免費的 MongoDB 資料庫。 資料庫建立好之後，在入口網站中瀏覽至**連接字串**，並擷取 Azure Cosmos DB 連線字串以供在本教學課程稍後使用。
>

在 Cloud Shell 中，使用 [`az cosmosdb create`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-create) 命令來建立 Cosmos DB 帳戶。

在下列命令中，以唯一的 Cosmos DB 名稱替代 *\<cosmosdb-name>* 預留位置。 這個名稱會用來作為 Cosmos DB 端點 `https://<cosmosdb-name>.documents.azure.com/` 的一部分，因此，這個名稱在 Azure 中的所有 Cosmos DB 帳戶上必須是唯一的。 名稱只能包含小寫字母、數字及連字號 (-) 字元，且長度必須為 3 到 50 個字元。

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

--kind MongoDB 參數會啟用 MongoDB 用戶端連線。

建立 Cosmos DB 帳戶之後，Azure CLI 會顯示類似下列範例的資訊：

<pre>
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://&lt;cosmosdb-name&gt;.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  &lt; Output truncated for readability &gt;
}
</pre>

## <a name="connect-app-to-production-mongodb"></a>將應用程式連線至生產環境 MongoDB

在此步驟中，您要使用 MongoDB 連接字串，將 MEAN.js 範例應用程式連線至您剛才建立的 Cosmos DB 資料庫。 

### <a name="retrieve-the-database-key"></a>擷取資料庫索引鍵

若要連線至 Cosmos DB 資料庫，您需要資料庫金鑰。 在 Cloud Shell 中，使用 [`az cosmosdb list-keys`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-list-keys) 命令來擷取主要金鑰。

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup
```

Azure CLI 會顯示類似下列範例的資訊：

<pre>
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
</pre>

複製 `primaryMasterKey` 的值。 您需要在下一個步驟中用到此資訊。

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>在 Node.js 應用程式中設定連接字串

在本機 MEAN.js 存放庫的 _config/env/_ 資料夾中，建立名為 _local-production.js_ 的檔案。 _.gitignore_ 已設定為在存放庫外保留此檔案。 

請將下列程式碼複製到其中。 務必以您的 Cosmos DB 資料庫名稱取代這兩個 *\<cosmosdb-name>* 預留位置，並以您在前一個步驟中複製的索引鍵取代 *\<primary-master-key>* 預留位置。

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

需要 `ssl=true` 選項，因為 [Cosmos DB 需要 SSL](../cosmos-db/connect-mongodb-account.md#connection-string-requirements)。 

儲存您的變更。

### <a name="test-the-application-in-production-mode"></a>在生產模式中測試應用程式 

在本機終端機視窗中，執行下列命令以縮短及組合生產環境的指令碼。 這個流程會產生生產環境所需的檔案。

```bash
gulp prod
```

在本機終端機視窗中，執行下列命令，可使用您在 _config/env/local-production.js_ 中設定的連接字串。 忽略憑證錯誤和 config.domain 警告。

```bash
# Bash
NODE_ENV=production node server.js

# Windows PowerShell
$env:NODE_ENV = "production" 
node server.js
```

`NODE_ENV=production` 會設定環境變數，告知 Node.js 在生產環境中執行。  `node server.js` 會啟動存放庫根目錄中的 Node.js 伺服器與 `server.js`。 這是在 Azure 中載入 Node.js 應用程式的方式。 

載入應用程式之後，請檢查以確定它正在生產環境中執行：

<pre>
--
MEAN.JS

環境：     實際執行伺服器：        http://0.0.0.0:8443 資料庫：        mongodb://&lt; cosmosdb-name&gt;:&lt; primary-master-key&gt;@&lt; cosmosdb-name&gt;.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false 應用程式版本：   0.5.0 MEAN.JS 版本：0.5.0
</pre>

在瀏覽器中，瀏覽至 `http://localhost:8443` 。 按一下上層功能表中的 [註冊]，然後建立測試使用者。 如果您成功建立使用者並且登入，則您的應用程式正在將資料寫入 Azure 中的 Cosmos DB 資料庫。 

在終端機中，輸入 `Ctrl+C` 以停止 Node.js。 

## <a name="deploy-app-to-azure"></a>將應用程式部署到 Azure

在此步驟中，您要將已與 MongoDB 連接的 Node.js 應用程式部署至 Azure App Service。

### <a name="configure-a-deployment-user"></a>設定部署使用者

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

<a name="create"></a>
### <a name="create-a-web-app"></a>建立 Web 應用程式

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-linux-no-h.md)] 

::: zone-end

### <a name="configure-an-environment-variable"></a>設定環境變數

根據預設，MEAN.js 專案會將 _config/env/local-production.js_ 屏除在 Git 存放庫之外。 因此會針對您的 Azure 應用程式，使用應用程式設定來定義 MongoDB 連接字串。

若要設定應用程式的設定，請在 Cloud Shell 中使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 命令。 

下列範例會在 Azure 應用程式中設定 `MONGODB_URI` 應用程式設定。 取代 *\<app-name>* 、 *\<cosmosdb-name>* 和 *\<primary-master-key>* 預留位置。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true"
```

在 Node.js 程式碼中，您可以利用 `process.env.MONGODB_URI` 來[存取此應用程式設定](configure-language-nodejs.md#access-environment-variables)，就像存取任何環境變數一樣。 

在本機 MEAN.js 存放庫中，開啟 _config/env/production.js_ (而不是 _config/env/local-production.js_)，它具有生產環境特定設定。 預設的 MEAN.js 應用程式已經設定為使用您建立的 `MONGODB_URI` 環境變數。

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>從 Git 推送至 Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

您可能會注意到，部署程序會在 `npm install` 之後執行 [Gulp](https://gulpjs.com/)。 App Service 不會在部署期間執行 Gulp 或 Grunt 工作，因此，這個範例存放庫在其根目錄中有兩個其他檔案可啟用它： 

- _.deployment_ - 此檔案會告訴 App Service，執行 `bash deploy.sh` 以作為自訂部署指令碼。
- _deploy.sh_ - 自訂部署指令碼。 如果您檢閱檔案，您將看到它會在 `npm install` 和 `bower install` 之後執行 `gulp prod`。 

您可以使用這種方法，將任何步驟新增至您的 Git 部署。 如果您在任一時間點重新啟動 Azure 應用程式，App Service 並不會重新執行這些自動化工作。 如需詳細資訊，請參閱[執行 Grunt/Bower/Gulp](configure-language-nodejs.md#run-gruntbowergulp)。

### <a name="browse-to-the-azure-app"></a>瀏覽至 Azure 應用程式 

使用網頁瀏覽器，瀏覽至已部署的應用程式。 

```bash 
http://<app-name>.azurewebsites.net 
``` 

按一下上層功能表中的 [註冊]，然後建立一位虛擬使用者。 

如果成功且應用程式會自動登入已建立的使用者，則您在 Azure 中的 MEAN.js 應用程式就已連線到 MongoDB (Cosmos DB) 資料庫。 

![在 Azure App Service 中執行的 MEAN.js 應用程式](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

選取 [系統管理員] > [管理文章] 來新增一些文章。 

**恭喜！** 您正在 Azure App Service 中執行資料驅動的 Node.js 應用程式。

## <a name="update-data-model-and-redeploy"></a>更新資料模型並重新部署

在此步驟中，您會變更 `article` 資料模型，並將變更發佈至 Azure。

### <a name="update-the-data-model"></a>更新資料模型

在本機 MEAN.js 存放庫中，開啟 _modules/articles/server/models/article.server.model.js_。

在 `ArticleSchema` 中，新增名為 `comment` 的 `String` 類型。 完成時，您的結構描述程式碼應該如下所示：

```javascript
const ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>更新 articles 程式碼

更新 `articles` 程式碼的其餘部分以使用 `comment`。

有五個需要修改的檔案：伺服器控制器和四個用戶端檢視。 

開啟 _modules/articles/server/controllers/articles.server.controller.js_。

在 `update` 函式中，新增 `article.comment` 的指派。 下列程式碼會顯示已完成的 `update` 函式：

```javascript
exports.update = function (req, res) {
  let article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

開啟 _modules/articles/client/views/view-article.client.view.html_。

就在結尾 `</section>` 標記的正上方，新增下列程式碼行來顯示 `comment` 以及剩餘的文章資料：

```html
<p class="lead" ng-bind="vm.article.comment"></p>
```

開啟 _modules/articles/client/views/list-articles.client.view.html_。

就在結尾 `</a>` 標記的正上方，新增下列程式碼行來顯示 `comment` 以及剩餘的文章資料：

```html
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

開啟 _modules/articles/client/views/admin/list-articles.client.view.html_。

在 `<div class="list-group">` 元素內部且就在結尾 `comment` 標記的正上方，新增下列程式碼行來顯示 `</a>` 以及剩餘的文章資料：

```html
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

開啟 _modules/articles/client/views/admin/form-article.client.view.html_。

找到包含提交按鈕的 `<div class="form-group">` 元素，如下所示：

```html
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

就在此標記的正上方，新增另一個 `<div class="form-group">` 元素，讓使用者能夠編輯 `comment` 欄位。 新的元素應該如下所示：

```html
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>本機測試您的變更

儲存您的所有變更。

在本機終端機視窗中，再次於生產模式中測試變更。

```bash
# Bash
gulp prod
NODE_ENV=production node server.js

# Windows PowerShell
gulp prod
$env:NODE_ENV = "production" 
node server.js
```

在瀏覽器中，瀏覽至 `http://localhost:8443`，並確定您已登入。

選取 [系統管理員] > [管理文章]，然後選取 [+] 按鈕來新增文章。

您現在會看到新的 `Comment` 文字方塊。

![已將註解欄位新增到文章中](./media/tutorial-nodejs-mongodb-app/added-comment-field.png)

在終端機中，輸入 `Ctrl+C` 以停止 Node.js。 

### <a name="publish-changes-to-azure"></a>將變更發佈至 Azure

在本機終端機視窗中，在 Git 中認可您的變更，然後將程式碼變更推送至 Azure。

```bash
git commit -am "added article comment"
git push azure master
```

完成 `git push` 之後，巡覽至 Azure 應用程式，然後嘗試執行新功能。

![發佈至 Azure 的模型和資料庫變更](media/tutorial-nodejs-mongodb-app/added-comment-field-published.png)

如果您先前已新增任何文章，您仍然可以看到它們。 Cosmos DB 中的現有資料不會遺失。 此外，您的變更會更新至資料結構描述，並讓現有資料保留不變。

## <a name="stream-diagnostic-logs"></a>資料流診斷記錄 

::: zone pivot="platform-windows"  

當 Node.js 應用程式在 Azure App Service 中執行時，您可以使用管線將主控台記錄傳送至終端機。 這樣一來，您就能取得相同的診斷訊息，以協助您偵錯應用程式錯誤。

若要開始記錄資料流，請在 Cloud Shell 中使用 [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) 命令。

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
``` 

開始記錄串流之後，在瀏覽器中重新整理 Azure 應用程式以取得部分 Web 流量。 您現在會看到使用管線傳送到終端機的主控台記錄。

您隨時可以輸入 `Ctrl+C` 以停止記錄資料流。 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

## <a name="manage-your-azure-app"></a>管理您的 Azure 應用程式

移至 [Azure 入口網站](https://portal.azure.com)，以查看您所建立的應用程式。

按一下左側功能表中的 [應用程式服務]，然後按一下 Azure 應用程式的名稱。

![入口網站瀏覽至 Azure 應用程式](./media/tutorial-nodejs-mongodb-app/access-portal.png)

根據預設，入口網站會顯示應用程式的 [概觀] 頁面。 此頁面可讓您檢視應用程式的執行方式。 您也可以在這裡執行基本管理工作，像是瀏覽、停止、啟動、重新啟動及刪除。 分頁左側的索引標籤會顯示您可開啟的各種設定分頁。

![Azure 入口網站中的 App Service 頁面](./media/tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>後續步驟

您已了解如何︰

> [!div class="checklist"]
> * 在 Azure 中建立 MongoDB 資料庫
> * 將 Node.js 應用程式連線至 MongoDB
> * 將應用程式部署至 Azure
> * 將資料模型更新並將應用程式重新部署
> * 將記錄從 Azure 串流到終端機
> * 在 Azure 入口網站中管理應用程式

前往下一個教學課程，了解如何將自訂的 DNS 名稱對應至該應用程式。

> [!div class="nextstepaction"] 
> [將現有的自訂 DNS 名稱對應至 Azure App Service](app-service-web-tutorial-custom-domain.md)

或者，查看其他資源：

> [!div class="nextstepaction"]
> [設定 Node.js 應用程式](configure-language-nodejs.md)
