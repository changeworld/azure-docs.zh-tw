---
title: 教學課程：驗證使用者 E2E
description: 了解如何使用 App Service 驗證和授權來保護 App Service 應用程式端對端，包括對遠端 API 的存取。
keywords: App Service, Azure App Service, authN, authZ, 保護, 安全性, 多層式, Azure Active Directory, Azure AD
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/29/2020
ms.custom: devx-track-csharp, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 8c3fca6cf7782c3aaac91388a8f8395e288f5ea5
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558737"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service"></a>教學課程：在 Azure App Service 中對使用者進行端對端驗證和授權

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) 可提供可高度擴充、自我修復的 Web 主控服務。 此外，App Service 具有[使用者驗證和授權](overview-authentication-authorization.md)的內建支援。 本教學課程說明如何使用 App Service 驗證和授權來保護您的應用程式。 其使用 ASP.NET Core 應用程式，並搭配作為範例的 Angular.js 前端。 App Service 驗證和授權支援所有語言執行階段，且您可以透過本教學課程了解如何將其套用至您偏好的語言。

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) 使用 Linux 作業系統提供可高度擴充、自我修復的 Web 主機服務。 此外，App Service 具有[使用者驗證和授權](overview-authentication-authorization.md)的內建支援。 本教學課程說明如何使用 App Service 驗證和授權來保護您的應用程式。 其使用 ASP.NET Core 應用程式，並搭配作為範例的 Angular.js 前端。 App Service 驗證和授權支援所有語言執行階段，且您可以透過本教學課程了解如何將其套用至您偏好的語言。

::: zone-end

![簡單驗證和授權](./media/tutorial-auth-aad/simple-auth.png)

此外也會說明如何藉由代表已驗證的使用者存取受保護的後端 API ([從伺服器程式碼](#call-api-securely-from-server-code)和[從瀏覽器程式碼](#call-api-securely-from-browser-code))，來保護多層式應用程式。

![進階驗證和授權](./media/tutorial-auth-aad/advanced-auth.png)

這些只是 App Service 中可能出現的部分驗證和授權案例。 

以下更完整地列出您在本教學課程中可了解的事項：

> [!div class="checklist"]
> * 啟用內建驗證和授權
> * 防止應用程式接受未經驗證的要求
> * 以 Azure Active Directory 作為身分識別提供者
> * 代表已登入的使用者存取遠端應用程式
> * 使用權杖驗證保護服務對服務呼叫
> * 從伺服器程式碼使用存取權杖
> * 從用戶端 (瀏覽器) 程式碼使用存取權杖

您可以在 macOS、Linux、Windows 上依照本教學課程中的步驟進行。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程：

- <a href="https://git-scm.com/" target="_blank">安裝 Git</a>
- <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">安裝最新的 .NET Core 3.1 SDK</a>
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-local-net-core-app"></a>建立本機 .NET Core 應用程式

在此步驟中，您要設定本機的 .NET Core 專案。 您可以使用相同的專案來部署後端 API 應用程式和前端 Web 應用程式。

### <a name="clone-and-run-the-sample-application"></a>複製及執行範例應用程式

執行下列命令，以複製範例存放庫並加以執行。

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

瀏覽至 `http://localhost:5000`，並嘗試新增、編輯和移除待辦事項項目。 

![在本機執行的 ASP.NET Core API](./media/tutorial-auth-aad/local-run.png)

如需隨時停止 ASP.NET Core，請在終端機上按下 `Ctrl+C`。

## <a name="deploy-apps-to-azure"></a>將應用程式部署至 Azure

在此步驟中，您會將專案部署至兩個 App Service 應用程式。 一個是前端應用程式，另一個是後端應用程式。

### <a name="configure-a-deployment-user"></a>設定部署使用者

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-azure-resources"></a>建立 Azure 資源

::: zone pivot="platform-windows"  

在 Cloud Shell 中執行下列命令，以建立兩個 Windows Web 應用程式。 以兩個全域唯一的應用程式名稱取代 _\<front-end-app-name>_ 和 _\<back-end-app-name>_ (有效的字元為 `a-z`、`0-9` 和 `-`)。 如需各個命令的詳細資訊，請參閱 [Azure App Service 中具有 CORS 支援的 RESTful API](app-service-web-tutorial-rest-api.md)。

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

::: zone pivot="platform-linux"

在 Cloud Shell 中執行下列命令，以建立兩個 Web 應用程式。 以兩個全域唯一的應用程式名稱取代 _\<front-end-app-name>_ 和 _\<back-end-app-name>_ (有效的字元為 `a-z`、`0-9` 和 `-`)。 如需每個命令的詳細資訊，請參閱[在 Azure App Service 中建立 .NET Core 應用程式](quickstart-dotnetcore.md)。

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE --is-linux
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

> [!NOTE]
> 儲存前端應用程式和後端應用程式之 Git 遠端的 URL，如 `az webapp create` 的輸出所顯示。
>

### <a name="push-to-azure-from-git"></a>從 Git 推送至 Azure

回到 _本機終端機視窗_ ，執行下列 Git 命令，以部署至後端應用程式。 以您從 [建立 Azure 資源](#create-azure-resources)儲存的 Git 遠端 URL 取代 _\<deploymentLocalGitUrl-of-back-end-app>_ 。 當 Git 認證管理員提示您輸入認證時，請務必輸入[您的部署認證](deploy-configure-credentials.md)，而不是您用來登入 Azure 入口網站的認證。

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

在本機終端機視窗中執行下列 Git 命令，以將相同的程式碼部署至前端應用程式。 以您從 [建立 Web 應用程式](#create-azure-resources)儲存的 Git 遠端 URL 取代 _\<deploymentLocalGitUrl-of-front-end-app>_ 。

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-apps"></a>瀏覽至應用程式

在瀏覽器中瀏覽至下列 URL，並確認兩個應用程式都在運作中。

```
http://<back-end-app-name>.azurewebsites.net
http://<front-end-app-name>.azurewebsites.net
```

:::image type="content" source="./media/tutorial-auth-aad/azure-run.png" alt-text="瀏覽器視窗中 Azure App Service Rest API 範例的螢幕擷取畫面，其中顯示「待辦事項清單」應用程式。":::

> [!NOTE]
> 如果應用程式重新啟動，您可能會發現新的資料已被清除。 這是刻意設計的行為，因為範例 ASP.NET Core 應用程式會使用記憶體內部資料庫。
>
>

## <a name="call-back-end-api-from-front-end"></a>從前端呼叫後端 API

在此步驟中，您會使用前端應用程式的伺服器程式碼存取後端 API。 接著，您會啟用從前端到後端的驗證存取。

### <a name="modify-front-end-code"></a>修改前端程式碼

在本機存放庫中，開啟 _Controllers/TodoController.cs_。 在 `TodoController` 類別的開頭處加上以下幾行，並將 _\<back-end-app-name>_ 取代為後端應用程式的名稱：

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back-end-app-name>.azurewebsites.net";
```

尋找以 `[HttpGet]` 裝飾的方法，並將大括號內的程式碼取代為：

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo");
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

第一行會對後端 API 應用程式發出 `GET /api/Todo` 呼叫。

接著，尋找以 `[HttpGet("{id}")]` 裝飾的方法，並將大括號內的程式碼取代為：

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}");
return Content(data, "application/json");
```

第一行會對後端 API 應用程式發出 `GET /api/Todo/{id}` 呼叫。

接著，尋找以 `[HttpPost]` 裝飾的方法，並將大括號內的程式碼取代為：

```cs
var response = await _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", todoItem);
var data = await response.Content.ReadAsStringAsync();
return Content(data, "application/json");
```

第一行會對後端 API 應用程式發出 `POST /api/Todo` 呼叫。

接著，尋找以 `[HttpPut("{id}")]` 裝飾的方法，並將大括號內的程式碼取代為：

```cs
var res = await _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", todoItem);
return new NoContentResult();
```

第一行會對後端 API 應用程式發出 `PUT /api/Todo/{id}` 呼叫。

接著，尋找以 `[HttpDelete("{id}")]` 裝飾的方法，並將大括號內的程式碼取代為：

```cs
var res = await _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}");
return new NoContentResult();
```

第一行會對後端 API 應用程式發出 `DELETE /api/Todo/{id}` 呼叫。

儲存您的所有變更。 在本機終端機視窗中，使用下列 Git 命令，將您的變更部署至前端應用程式：

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>檢查您的變更

瀏覽至 `http://<front-end-app-name>.azurewebsites.net` 並新增一些項目，例如 `from front end 1` 和 `from front end 2`。

瀏覽至 `http://<back-end-app-name>.azurewebsites.net`，以查看從前端應用程式新增的項目。 此外，請新增一些項目 (例如 `from back end 1` 和 `from back end 2`)，然後重新整理前端應用程式，以查看它是否反映這些變更。

:::image type="content" source="./media/tutorial-auth-aad/remote-api-call-run.png" alt-text="瀏覽器視窗中 Azure App Service Rest API 範例的螢幕擷取畫面，其中顯示「待辦事項清單」應用程式，其中包含從前端應用程式新增的項目。":::

## <a name="configure-auth"></a>設定驗證

在此步驟中，您會為兩個應用程式啟用驗證和授權。 您也會設定前端應用程式，以產生可用來對後端應用程式發出已驗證呼叫的存取權杖。

您會以 Azure Active Directory 作為身分識別提供者。 如需詳細資訊，請參閱[為您的 App Service 應用程式設定 Azure Active Directory 驗證](configure-authentication-provider-aad.md)。

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>為後端應用程式啟用驗證和授權

在 [Azure 入口網站](https://portal.azure.com)功能表中，選取 [資源群組]，或從任何頁面搜尋並選取 [資源群組]。

在 [資源群組] 中，尋找並選取資源群組。 在 [概觀] 中，選取後端應用程式的管理頁面。

:::image type="content" source="./media/tutorial-auth-aad/portal-navigate-back-end.png" alt-text="[資源群組] 視窗的螢幕擷取畫面，其中顯示已選取範例資源群組和後端應用程式管理頁面的概觀。":::

在後端應用程式的左側功能表中，按一下 [驗證/授權]，然後選取 [開啟] 以啟用 App Service 驗證。

在 [當要求未經驗證時所要採取的動作] 中，選取 [使用 Azure Active Directory 登入]。

在 [驗證提供者] 底下，選取 [Azure Active Directory]。

:::image type="content" source="./media/tutorial-auth-aad/configure-auth-back-end.png" alt-text="後端應用程式左側功能表的螢幕擷取畫面，其中顯示已選取的驗證/授權，以及在右功能表中選取的設定。":::

選取 [快速]，然後接受預設設定以建立新的 AD 應用程式，並選取 [確定]。

在 [驗證/授權] 頁面中，選取 [儲存]。

當您看到通知中的 `Successfully saved the Auth Settings for <back-end-app-name> App` 訊息後，請重新整理入口網站頁面。

再次選取 [Azure Active Directory]，然後選取 [Azure AD 應用程式]。

將 Azure AD 應用程式的 **用戶端識別碼** 複製到記事本。 您後續會用到此值。

:::image type="content" source="./media/tutorial-auth-aad/get-application-id-back-end.png" alt-text="Azure Active Directory [設定] 視窗的螢幕擷取畫面，其中顯示 [Azure AD App]，以及 [Azure AD 應用程式] 視窗，其中顯示要複製的用戶端識別碼。":::

如果您在這裡停止，您會有一個已受到 App Service 驗證和授權保護的獨立應用程式。 其餘各節將說明如何藉由讓已驗證的使用者從前端「流向」後端，以保護多應用程式解決方案。 

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>為前端應用程式啟用驗證和授權

請遵循前端應用程式的相同步驟，但略過最後一個步驟。 您的前端應用程式不需要用戶端識別碼。

如果您想要，瀏覽至 `http://<front-end-app-name>.azurewebsites.net`。 它此時應會將您導向至安全的登入頁面。 登入之後，「您仍然無法從後端應用程式存取資料」，因為後端應用程式現在需要來自前端應用程式的 Azure Active Directory 登入。 需要執行三個步驟：

- 授與前端對後端的存取權
- 設定 App Service，以傳回可使用的權杖
- 在程式碼中使用權杖

> [!TIP]
> 如果發生錯誤，且您重新設定應用程式的驗證/授權設定，權杖存放區中的權杖可能不會從新的設定重新產生。 為了確保您的權杖會重新產生，您必須先登出應用程式，然後再重新登入。 您可以透過簡單的方式完成此動作，即以私用模式使用瀏覽器，並在變更應用程式中的設定後關閉，再以私用模式重新開啟瀏覽器。

### <a name="grant-front-end-app-access-to-back-end"></a>授與前端應用程式對後端的存取權

現在，您已為這兩個應用程式啟用驗證和授權，兩者皆受到 AD 應用程式的支援。 在此步驟中，您會為前端應用程式提供代表使用者存取後端的權限。 (技術上，您會為前端的 _AD 應用程式_ 提供代表使用者存取後端 _AD 應用程式_ 的權限。)

在 [Azure 入口網站](https://portal.azure.com)功能表中，選取 [Azure Active Directory]，或從任何頁面搜尋並選取 [Azure Active Directory]。

選取 [應用程式註冊] > [擁有的應用程式] > [檢視此目錄中的所有應用程式]。 選取您的前端應用程式名稱，然後選取 [API 權限]。

:::image type="content" source="./media/tutorial-auth-aad/add-api-access-front-end.png" alt-text="[Microsoft - 應用程式註冊] 視窗的螢幕擷取畫面，其中包含所擁有的應用程式、前端應用程式名稱，以及所選取的 API 許可權。":::

選取 [新增權限]，然後選取 [我的組織使用的 API] >  **\<back-end-app-name>** 。

在後端應用程式的 [要求 API 權限] 頁面中，選取 [委派的權限]和 [user_impersonation]，然後選取 [新增權限]。

:::image type="content" source="./media/tutorial-auth-aad/select-permission-front-end.png" alt-text="[要求 API 許可權] 頁面的螢幕擷取畫面，其中顯示已選取 [委派的許可權]、[user_impersonation] 和 [新增權限] 按鈕。":::

### <a name="configure-app-service-to-return-a-usable-access-token"></a>設定 App Service，以傳回可使用的存取權杖

前端應用程式現在具有必要權限，能夠以登入使用者的身分存取後端應用程式。 在此步驟中，您會設定 App Service 驗證和授權，讓自己取得可用來存取後端的存取權杖。 此步驟中，您需要後端的用戶端識別碼，也就是您從[為後端應用程式啟用驗證和授權](#enable-authentication-and-authorization-for-back-end-app)中複製的識別碼。

瀏覽至 [Azure 資源總管](https://resources.azure.com)並使用資源樹狀結構，找出您的前端 Web 應用程式。

現在，在資源樹狀結構中選取您的前端應用程式，以開啟 [Azure 資源總管](https://resources.azure.com)。 在頁面頂端按一下 [讀取/寫入]，以啟用 Azure 資源的編輯。

:::image type="content" source="./media/tutorial-auth-aad/resources-enable-write.png" alt-text="[Azure 資源總管] 頁面頂端 [唯讀] 和 [讀取/寫入] 按鈕的螢幕擷取畫面，並已選取 [讀取/寫入] 按鈕。":::

在左側瀏覽器中，向下切入至 [config] > [authsettings]。

在 **authsettings** 檢視中，按一下 [編輯]。 使用您複製的用戶端識別碼，將 `additionalLoginParams` 設為下列 JSON 字串。 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back-end-client-id>"],
```

:::image type="content" source="./media/tutorial-auth-aad/additional-login-params-front-end.png" alt-text="Authsettings 視圖中程式碼範例的螢幕擷取畫面，其中顯示 additionalLoginParams 字串，並包含用戶端識別碼的範例。":::

按一下 **PUT** 以儲存您的設定。

您的應用程式已完成設定。 前端現在已可使用適當的存取權杖存取後端。

如需如何為其他提供者設定存取權杖的相關資訊，請參閱[重新整理識別提供者權杖](app-service-authentication-how-to.md#refresh-identity-provider-tokens)。

## <a name="call-api-securely-from-server-code"></a>從伺服器程式碼安全地呼叫 API

在此步驟中，您會啟用您先前修改的伺服器程式碼，對後端 API 發出已驗證的呼叫。

您的前端應用程式現已具有必要的權限，且會將後端的用戶端識別碼新增至登入參數。 因此，它可以取得對後端應用程式進行驗證的存取權杖。 App Service 會藉由在每個已驗證的要求中插入 `X-MS-TOKEN-AAD-ACCESS-TOKEN` 標頭將此權杖提供給您的伺服器程式碼 (請參閱[擷取應用程式程式碼中的權杖](app-service-authentication-how-to.md#retrieve-tokens-in-app-code))。

> [!NOTE]
> 在所有支援的語言中都會插入這些標頭。 您可以使用個別語言的標準模式存取這些標頭。

在本機存放庫中，再次開啟 _Controllers/TodoController.cs_。 在 `TodoController(TodoContext context)` 建構函式下，新增下列程式碼：

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
}
```

此程式碼會將標準 HTTP 標頭 `Authorization: Bearer <access-token>` 新增至所有遠端 API 呼叫。 在 ASP.NET Core MVC 要求執行管線中，`OnActionExecuting` 會在各自的動作之前執行，因此您的每個傳出 API 呼叫現在都會提供存取權杖。

儲存您的所有變更。 在本機終端機視窗中，使用下列 Git 命令，將您的變更部署至前端應用程式：

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

再次登入 `https://<front-end-app-name>.azurewebsites.net`。 在 [使用者資料使用合約] 頁面上，按一下 [接受]。

現在，您應該能夠和之前一樣從後端應用程式建立、讀取、更新和刪除資料。 唯一的差別在於，現在這兩個應用程式都受到 App Service 驗證和授權的保護，包括服務對服務呼叫。

恭喜！ 您的伺服器程式碼現在已可代表已驗證的使用者存取後端資料。

## <a name="call-api-securely-from-browser-code"></a>從瀏覽器程式碼安全地呼叫 API

在此步驟中，您會將前端 Angular.js 應用程式指向後端 API。 如此，您即了解如何擷取存取權杖，並且用它對後端應用程式發出 API 呼叫。

伺服器程式碼可存取要求標頭，而用戶端程式碼則可存取 `GET /.auth/me` 以取得相同的存取權杖 (請參閱[擷取應用程式程式碼中的權杖](app-service-authentication-how-to.md#retrieve-tokens-in-app-code))。

> [!TIP]
> 本節會使用標準 HTTP 方法來示範安全的 HTTP 呼叫。 不過，您可以利用[適用於 JavaScript 的 Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-js) 來簡化 Angular.js 應用程式模式。
>

### <a name="configure-cors"></a>設定 CORS

在 Cloud Shell 中，使用 [`az webapp cors add`](/cli/azure/webapp/cors#az-webapp-cors-add) 命令，對您的用戶端 URL 啟用 CORS。 取代 _\<back-end-app-name>_ 和 _\<front-end-app-name>_ 預留位置。

```azurecli-interactive
az webapp cors add --resource-group myAuthResourceGroup --name <back-end-app-name> --allowed-origins 'https://<front-end-app-name>.azurewebsites.net'
```

此步驟與驗證和授權無關。 但您仍需加以執行，您的瀏覽器才會允許來自 Angular.js 應用程式的跨網域 API 呼叫。 如需詳細資訊，請參閱[新增 CORS 功能](app-service-web-tutorial-rest-api.md#add-cors-functionality)。

### <a name="point-angularjs-app-to-back-end-api"></a>將 Angular.js 應用程式指向後端 API

在本機存放庫中，開啟 wwwroot/index.html。

在第 51 行中，將 `apiEndpoint` 變數設為後端應用程式的 HTTPS URL (`https://<back-end-app-name>.azurewebsites.net`)。 以您在 App Service 中的應用程式名稱取代 _\<back-end-app-name>_ 。

在本機存放庫中開啟 _wwwroot/app/scripts/todoListSvc.js_，並確認所有 API 呼叫前面都加上了 `apiEndpoint`。 您的 Angular.js 應用程式現在會呼叫後端 API。 

### <a name="add-access-token-to-api-calls"></a>將存取權杖新增至 API 呼叫

在 _wwwroot/app/scripts/todoListSvc.js_ 中的 API 呼叫清單上方 (`getItems : function(){` 行上方)，將下列函式新增至清單中：

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

呼叫此函式可設定含有存取權杖的預設 `Authorization` 標頭。 您將在下一個步驟中加以呼叫。

在本機存放庫中開啟 _wwwroot/app/scripts/app.js_，並尋找下列程式碼：

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

將這一整個程式碼區塊取代為下列程式碼：

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

新的變更會新增呼叫 `/.auth/me` 和設定存取權杖的 `resolve` 對應。 它可確保您在具現化 `todoListCtrl` 控制器之前具有存取權杖。 如此，該控制器所發出的所有 API 呼叫都會包含權杖。

### <a name="deploy-updates-and-test"></a>部署更新和測試

儲存您的所有變更。 在本機終端機視窗中，使用下列 Git 命令，將您的變更部署至前端應用程式：

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

再次瀏覽至 `https://<front-end-app-name>.azurewebsites.net`。 現在，您應該能夠直接在 Angular.js 應用程式中從後端應用程式建立、讀取、更新和刪除資料。

恭喜！ 您的用戶端程式碼現在已可代表已驗證的使用者存取後端資料。

## <a name="when-access-tokens-expire"></a>當存取權杖到期

存取權杖會在一段時間後到期。 若要了解如何重新整理存取權杖，而不需要讓使用者向應用程式重新驗證，請參閱[重新整理識別提供者權杖](app-service-authentication-how-to.md#refresh-identity-provider-tokens)。

## <a name="clean-up-resources"></a>清除資源

在前述步驟中，您在資源群組中建立了 Azure 資源。 如果您在未來不需要這些資源，請在 Cloud Shell 中執行下列命令，刪除資源群組：

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

此命令可能會花一分鐘執行。

<a name="next"></a>
## <a name="next-steps"></a>後續步驟

您已了解如何︰

> [!div class="checklist"]
> * 啟用內建驗證和授權
> * 防止應用程式接受未經驗證的要求
> * 以 Azure Active Directory 作為身分識別提供者
> * 代表已登入的使用者存取遠端應用程式
> * 使用權杖驗證保護服務對服務呼叫
> * 從伺服器程式碼使用存取權杖
> * 從用戶端 (瀏覽器) 程式碼使用存取權杖

前往下一個教學課程，了解如何將自訂的 DNS 名稱對應至應用程式。

> [!div class="nextstepaction"]
> [將現有的自訂 DNS 名稱對應至 Azure App Service](app-service-web-tutorial-custom-domain.md)
