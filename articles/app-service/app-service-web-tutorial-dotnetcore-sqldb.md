---
title: 教學課程：ASP.NET Core 搭配 SQL Database
description: 了解如何讓 .NET Core 應用程式在 Azure App Service 中運作，並連線至 SQL Database。
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/23/2020
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: f8e76c90a670adb8fa5de5a33063d9de3bcc6cc3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82207639"
---
# <a name="tutorial-build-an-aspnet-core-and-sql-database-app-in-azure-app-service"></a>教學課程：在 Azure App Service 中建置 ASP.NET Core 和 SQL Database 應用程式

> [!NOTE]
> 本文會將應用程式部署至 Windows 上的 App Service。 若要在 _Linux_ 上部署至 App Service，請參閱[在 Linux 上的 Azure App Service 中建置 .NET Core 和 SQL Database 應用程式](./containers/tutorial-dotnetcore-sqldb-app.md)。
>

[App Service](overview.md) 可在 Azure 中提供可高度擴充、自我修復的 Web 主控服務。 本教學課程示範如何建立 .NET Core 應用程式，並將它連線到 SQL Database。 當您完成時，您就會有在 App Service 中執行的 .NET Core MVC 應用程式。

![在 App Service 中執行的應用程式](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 在 Azure 中建立 SQL Database
> * 將 .NET Core 應用程式連線到 SQL Database
> * 將應用程式部署至 Azure
> * 將資料模型更新並將應用程式重新部署
> * 來自 Azure 的串流診斷記錄
> * 在 Azure 入口網站中管理應用程式

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程：

* <a href="https://git-scm.com/" target="_blank">安裝 Git</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">安裝最新的 .NET Core 3.1 SDK</a>

## <a name="create-local-net-core-app"></a>建立本機 .NET Core 應用程式

在此步驟中，您要設定本機的 .NET Core 專案。

### <a name="clone-the-sample-application"></a>複製範例應用程式

在終端機視窗中，使用 `cd` 移至工作目錄。

執行下列命令來複製範例存放庫，然後變更至其根目錄。

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

範例專案包含一個基本 CRUD (建立-讀取-更新-刪除) 應用程式，使用 [Entity Framework Core](https://docs.microsoft.com/ef/core/)。

### <a name="run-the-application"></a>執行應用程式

執行下列命令以安裝必要的套件、執行資料庫移轉，然後啟動應用程式。

```bash
dotnet tool install -g dotnet-ef
dotnet ef database update
dotnet run
```

在瀏覽器中，瀏覽至 `http://localhost:5000` 。 選取 [新建]  連結，並且建立幾個 [待辦事項]  項目。

![成功連線至 SQL Database](./media/app-service-web-tutorial-dotnetcore-sqldb/local-app-in-browser.png)

如需隨時停止 .NET Core，請在終端機上按下 `Ctrl+C`。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>建立生產環境 SQL Database

在此步驟中，您要在 Azure 中建立 SQL Database。 當您的應用程式部署至 Azure 時，它會使用此雲端資料庫。

對於 SQL Database，本教學課程使用 [Azure SQL Database](/azure/sql-database/)。

### <a name="create-a-resource-group"></a>建立資源群組

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>連線至 SQL Database 邏輯伺服器

在 Cloud Shell 中，使用 [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) 命令建立 SQL Database 邏輯伺服器。

將 \<server-name>  預留位置取代為「唯一」  的 SQL Database 名稱。 這個名稱會用來做為全域唯一 SQL Database 端點 (`<server-name>.database.windows.net`) 的一部分。 有效字元為 `a`-`z`、`0`-`9`、`-`。 此外，將 *\<db-username>* 和 *\<db-password>* 取代為您選擇的使用者名稱和密碼。 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

建立 SQL Database 邏輯伺服器後，Azure CLI 會顯示類似下列範例的資訊：

<pre>
{
  "administratorLogin": "&lt;db-username&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/&lt;server-name&gt;",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "&lt;server-name&gt;",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
</pre>

### <a name="configure-a-server-firewall-rule"></a>設定伺服器防火牆規則

使用 [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create) 命令建立 [Azure SQL Database 伺服器層級防火牆規則](../sql-database/sql-database-firewall-configure.md)。 當起始 IP 和結束 IP 都設為 0.0.0.0 時，防火牆只會為其他 Azure 資源開啟。 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> [僅使用您的應用程式所用的輸出 IP 位址](overview-inbound-outbound-ips.md#find-outbound-ips)，讓您的防火牆規則更具限制性。
>

在 Cloud Shell 中，將 *\<您的 IP 位址>* 取代為[您的本機 IPv4 IP 位址](https://www.whatsmyip.org/)並再次執行命令，以允許從您的本機電腦進行存取。

```azurecli-interactive
az sql server firewall-rule create --name AllowLocalClient --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="create-a-database"></a>建立資料庫

使用 [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create) 命令在伺服器中建立具有 [S0 效能等級](../sql-database/sql-database-service-tiers-dtu.md)的資料庫。

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>建立連接字串

使用 [`az sql db show-connection-string`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-show-connection-string) 命令來取得連接字串。

```azurecli-interactive
az sql db show-connection-string --client ado.net --server cephalin-core --name coreDB
```

在命令輸出中，以您稍早使用的資料庫管理員認證取代 \<username>  及 \<password>  。

此為您 .NET Core 應用程式的連接字串。 複製它以供稍後使用。

### <a name="configure-app-to-connect-to-production-database"></a>設定應用程式以連線到生產資料庫

在您的本機存放庫中，開啟 Startup.cs 並且尋找下列程式碼：

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

將其取代為下列程式碼。

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
```

> [!IMPORTANT]
> 對於需要擴增的生產應用程式，請遵循[在生產環境中套用移轉](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production)中的最佳做法。
> 

### <a name="run-database-migrations-to-the-production-database"></a>對生產資料庫執行資料庫移轉

您的應用程式目前會連線到本機 Sqlite 資料庫。 現在，您已設定了 Azure SQL Database，接下來請重新建立初始移轉，以將其設為目標。 

從存放庫根路徑中，執行下列命令。 以您稍早建立的連接字串取代 \<connection-string>  。

```
# Delete old migrations
rm Migrations -r
# Recreate migrations
dotnet ef migrations add InitialCreate

# Set connection string to production database
# PowerShell
$env:ConnectionStrings:MyDbConnection="<connection-string>"
# CMD (no quotes)
set ConnectionStrings:MyDbConnection=<connection-string>
# Bash (no quotes)
export ConnectionStrings__MyDbConnection=<connection-string>

# Run migrations
dotnet ef database update
```

### <a name="run-app-with-new-configuration"></a>使用新組態執行應用程式

現在，資料庫移轉會在生產資料庫上執行，請執行下列程式來測試您的應用程式：

```
dotnet run
```

在瀏覽器中，瀏覽至 `http://localhost:5000` 。 選取 [新建]  連結，並且建立幾個 [待辦事項]  項目。 您的應用程式現在正在讀取及寫入資料至生產資料庫。

認可本機變更，然後將變更認可至 Git 存放庫中。 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

您現在已可開始部署您的程式碼。

## <a name="deploy-app-to-azure"></a>將應用程式部署到 Azure

在此步驟中，您會將與 SQL Database 連線的 .NET Core 應用程式部署至 App Service。

### <a name="configure-local-git-deployment"></a>設定本機 git 部署

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>建立 Web 應用程式

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="configure-connection-string"></a>設定連接字串

若要設定 Azure 應用程式的連接字串，請在 Cloud Shell 中使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 命令。 在下列命令中，將 *\<app-name>* 以及 *\<connection-string>* 參數取代為您稍早建立的連接字串。

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app-name> --settings MyDbConnection="<connection-string>" --connection-string-type SQLAzure
```

在 ASP.NET Core 中，您可以使用標準模式的這個具名連接字串 (`MyDbConnection`)，例如 appsettings.json  中指定的任何連接字串。 在此情況下，`MyDbConnection` 也會定義在 appsettings.json  中。 在 App Service 中執行時，App Service 中所定義的連接字串會優先於 appsettings.json  中所定義的連接字串。 程式碼會在本機開發期間使用 appsettings.json  ，而且相同的程式碼會在部署時使用 App Service 值。

若要了解如何在程式碼中參考連接字串，請參閱[設定應用程式以連線到生產資料庫](#configure-app-to-connect-to-production-database)。

### <a name="push-to-azure-from-git"></a>從 Git 推送至 Azure

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 268, done.
Counting objects: 100% (268/268), done.
Compressing objects: 100% (171/171), done.
Writing objects: 100% (268/268), 1.18 MiB | 1.55 MiB/s, done.
Total 268 (delta 95), reused 251 (delta 87), pack-reused 0
remote: Resolving deltas: 100% (95/95), done.
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '64821c3558'.
remote: Generating deployment script.
remote: Project file path: .\DotNetCoreSqlDb.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: App container will begin restart within 10 seconds.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

### <a name="browse-to-the-azure-app"></a>瀏覽至 Azure 應用程式

使用網頁瀏覽器，瀏覽至已部署的應用程式。

```bash
http://<app-name>.azurewebsites.net
```

新增幾個待辦事項項目。

![在 App Service 中執行的應用程式](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

**恭喜！** 您正在 App Service 中執行資料驅動的 .NET Core 應用程式。

## <a name="update-locally-and-redeploy"></a>在本機更新和重新部署

在此步驟中，您會對資料庫結構描述進行變更，並且將它發佈至 Azure。

### <a name="update-your-data-model"></a>更新資料模型

在程式碼編輯器中開啟 _Models\Todo.cs_。 將下列屬性加入至 `ToDo` 類別：

```csharp
public bool Done { get; set; }
```

### <a name="rerun-database-migrations"></a>重新執行資料庫移轉

執行幾個命令來更新生產資料庫。

```bash
dotnet ef migrations add AddProperty
dotnet ef database update
```

### <a name="use-the-new-property"></a>使用新屬性

在您的程式碼中進行一些變更以使用 `Done` 屬性。 為了簡單起見，在本教學課程中，您僅需變更 `Index` 和 `Create` 檢視，以查看作用中的屬性。

開啟 _Controllers\TodosController.cs_。

尋找 `Create([Bind("ID,Description,CreatedDate")] Todo todo)` 方法，並將 `Done` 加入至 `Bind` 屬性 (Attribute) 中的屬性 (Property) 清單。 完成時，您的 `Create()` 方法簽章應該如以下程式碼所示：

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

開啟 _Views\Todos\Create.cshtml_。

在 Razor 程式碼中，您應該會看到 `Description` 的 `<div class="form-group">` 元素，然後是另一個 `CreatedDate` 的 `<div class="form-group">` 元素。 在這兩個元素的正後方，新增另一個 `Done` 的 `<div class="form-group">` 元素：

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

開啟 _Views\Todos\Index.cshtml_。

搜尋空白的 `<th></th>` 元素。 在此元素的正上方，新增下列 Razor 程式碼：

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

尋找包含 `asp-action` 標記協助程式的 `<td>` 元素。 在此元素的正上方，新增下列 Razor 程式碼：

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

您只需查看 `Index` 和 `Create` 檢視中的變更。

### <a name="test-your-changes-locally"></a>本機測試您的變更

在本機執行應用程式。

```bash
dotnet run
```

在瀏覽器中，瀏覽至 `http://localhost:5000/`。 您現在可以新增待辦事項項目，並且勾選 [完成]  。 然後，它應該會在您的首頁中顯示為已完成的項目。 請記住，[`Edit`] 檢視不會顯示 [`Done`] 欄位，因為您沒有變更 [`Edit`] 檢視。

### <a name="publish-changes-to-azure"></a>將變更發佈至 Azure

```bash
git add .
git commit -m "added done field"
git push azure master
```

`git push` 完成之後，瀏覽至 App Service 應用程式並嘗試新增待辦事項，然後核取 [完成]  。

![Code First 移轉之後的 Azure 應用程式](./media/app-service-web-tutorial-dotnetcore-sqldb/this-one-is-done.png)

仍會顯示您現有的所有待辦事項項目。 當您重新發佈 ASP.NET Core 應用程式時，您 SQL Database 中現有的資料不會遺失。 此外，Entity Framework Code 移轉只會變更資料結構描述，並讓現有的資料保持不變。

## <a name="stream-diagnostic-logs"></a>資料流診斷記錄

當 ASP.NET Core 應用程式在 Azure App Service 中執行時，您可以透過管道將主控台記錄傳送至 Cloud Shell。 這樣一來，您就能取得相同的診斷訊息，以協助您偵錯應用程式錯誤。

範例專案已遵循 [Azure 中的 ASP.NET Core 記錄](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider)指引，其中有兩項組態變更：

- 在 *DotNetCoreSqlDb.csproj* 中包含 `Microsoft.Extensions.Logging.AzureAppServices` 的參考。
- 在 Program.cs  中呼叫 `loggerFactory.AddAzureWebAppDiagnostics()`。

若要將 App Service 中的 ASP.NET Core [記錄層級](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-level)從預設層級 `Error` 設定為 `Information`，請在 Cloud Shell 中使用 [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config) 命令。

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> 專案的記錄層級已經在 appsettings.json  中設定為 `Information`。
> 

若要開始記錄資料流，請在 Cloud Shell 中使用 [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) 命令。

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

開始記錄資料流之後，重新整理瀏覽器中的 Azure 應用程式，以取得部分 Web 流量。 您現在會看到使用管線傳送到終端機的主控台記錄。 如果您沒有立即看到主控台記錄，請在 30 秒後再查看。

若要隨時停止記錄資料流，請輸入 `Ctrl`+`C`。

如需自訂 ASP.NET Core 記錄的詳細資訊，請參閱[登入 ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)。

## <a name="manage-your-azure-app"></a>管理您的 Azure 應用程式

若要查看您建立的應用程式，請在 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [應用程式服務]  。

![在 Azure 入口網站中選取應用程式服務](./media/app-service-web-tutorial-dotnetcore-sqldb/app-services.png)

在 [應用程式服務]  頁面上，選取您的 Azure 應用程式名稱。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-dotnetcore-sqldb/access-portal.png)

根據預設，入口網站會顯示應用程式的 [概觀]  頁面。 此頁面可讓您檢視應用程式的執行方式。 您也可以在這裡執行基本管理工作，像是瀏覽、停止、啟動、重新啟動及刪除。 分頁左側的索引標籤會顯示您可開啟的各種設定分頁。

![Azure 入口網站中的 App Service 頁面](./media/app-service-web-tutorial-dotnetcore-sqldb/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>後續步驟

您已了解如何︰

> [!div class="checklist"]
> * 在 Azure 中建立 SQL Database
> * 將 .NET Core 應用程式連線到 SQL Database
> * 將應用程式部署至 Azure
> * 將資料模型更新並將應用程式重新部署
> * 將記錄從 Azure 串流到終端機
> * 在 Azure 入口網站中管理應用程式

前往下一個教學課程，了解如何將自訂的 DNS 名稱對應至應用程式。

> [!div class="nextstepaction"]
> [教學課程：將自訂 DNS 名稱對應至應用程式](app-service-web-tutorial-custom-domain.md)
