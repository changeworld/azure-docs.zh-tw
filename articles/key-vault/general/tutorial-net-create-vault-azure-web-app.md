---
title: 教學課程 - 在 .NET 中透過 Azure Web 應用程式使用 Azure Key Vault
description: 在本教學課程中，您將在 ASP.NET Core 應用程式中設定 Azure Web 應用程式，以從金鑰保存庫讀取祕密。
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 2960726cf687908e8e4aed9333fce490dd7ff006
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788732"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-in-net"></a>教學課程：使用受控識別透過 .NET 將 Key Vault 連線至 Azure Web 應用程式

[Azure Key Vault](./overview.md) 可讓您以更高的安全性儲存認證和其他秘密。 不過，您的程式碼必須向 Key Vault 進行驗證，才能擷取認證。 [Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)可在 Azure Active Directory (Azure AD) 中將自動受控的識別提供給 Azure 服務，以利解決此問題。 您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要在程式碼中顯示認證。

在本教學課程中，您將建立 Azure Web 應用程式，並將其部署至 [Azure App Service](../../app-service/overview.md)。 您將透過[適用於 .NET 的 Azure Key Vault 秘密用戶端程式庫](/dotnet/api/overview/azure/key-vault)和 [Azure CLI](/cli/azure/get-started-with-azure-cli)，使用受控識別向 Azure 金鑰保存庫驗證您的 Azure Web 應用程式。 當您使用自選的開發語言、Azure PowerShell 和 (或) Azure 入口網站時，適用的基本原則是一樣的。

如需本教學課程中介紹的 Azure App Service Web 應用程式和部署的詳細資訊，請參閱：
- [App Service 概觀](../../app-service/overview.md)
- [在 Azure App Service 中建立 ASP.NET Core Web 應用程式](../../app-service/quickstart-dotnetcore.md)
- [本機 Git 部署至 Azure App Service](../../app-service/deploy-local-git.md)

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要：

* Azure 訂用帳戶。 [建立免費訂用帳戶。](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [.NET Core 3.1 SDK (或更新版本)](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
* [Git](https://www.git-scm.com/downloads) 安裝。
* [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/)。
* [Azure Key Vault](./overview.md)。 您可以使用 [Azure 入口網站](quick-create-portal.md)、[Azure CLI](quick-create-cli.md) 或 [Azure PowerShell](quick-create-powershell.md) 來建立金鑰保存庫。
* Key Vault [秘密](../secrets/about-secrets.md)。 您可以使用 [Azure 入口網站](../secrets/quick-create-portal.md)、[PowerShell](../secrets/quick-create-powershell.md) 或 [Azure CLI](../secrets/quick-create-cli.md) 來建立秘密。

如果您已在 Azure App Service 中部署 Web 應用程式，您可以跳至[設定對金鑰保存庫的 Web 應用程式存取權](#create-and-assign-a-managed-identity)和[修改 Web 應用程式程式碼](#modify-the-app-to-access-your-key-vault)小節。

## <a name="create-a-net-core-app"></a>建立 .NET Core 應用程式
在此步驟中，您將設定本機的 .NET Core 專案。

在您電腦上的終端機視窗中，建立名為 `akvwebapp` 的目錄，並將其設為目前的目錄：

```bash
mkdir akvwebapp
cd akvwebapp
```

使用 [dotnet new web](/dotnet/core/tools/dotnet-new) 命令建立 .NET Core 應用程式：

```bash
dotnet new web
```

在本機執行應用程式，以便得知其部署至 Azure 時的樣貌：

```bash
dotnet run
```

在網頁瀏覽器中，移至 `http://localhost:5000` 上的應用程式。

您會看到來自應用程式的 "Hello World!" 訊息顯示在頁面上。

若要進一步了解如何建立適用於 Azure 的 Web 應用程式，請參閱[在 Azure App Service 中建立 ASP.NET Core Web 應用程式](../../app-service/quickstart-dotnetcore.md)

## <a name="deploy-the-app-to-azure"></a>將應用程式部署至 Azure

在此步驟中，您會使用本機 Git 將 .NET Core 應用程式部署至 Azure App Service。 如需如何建立和部署應用程式的詳細資訊，請參閱[在 Azure 中建立 ASP.NET Core Web 應用程式](../../app-service/quickstart-dotnetcore.md)。

### <a name="configure-the-local-git-deployment"></a>設定本機 Git 部署

在終端機視窗中，選取 **Ctrl+C** 以關閉網頁伺服器。  初始化 .NET Core 專案的 Git 存放庫：

```bash
git init
git add .
git commit -m "first commit"
```

您可以使用 FTP 和本機 Git，透過「部署使用者」來部署 Azure Web 應用程式。 部署使用者只需設定一次，就能供所有 Azure 部署使用。 使用者名稱和密碼屬於帳戶等級部署，因此與 Azure 訂用帳戶認證不同。 

若要設定部署使用者，請執行 [az webapp deployment user set](/cli/azure/webapp/deployment/user?#az-webapp-deployment-user-set) 命令。 選擇符合下列指導方針的使用者名稱和密碼： 

- 使用者名稱必須是 Azure 中唯一的。 若為本機 Git 推送，其中不可包含 @ 符號。 
- 密碼長度必須至少為 8 個字元，且包含下列三個元素的其中兩個：字母、數字及符號。 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

JSON 輸出會將密碼顯示為 `null`。 如果您收到 `'Conflict'. Details: 409` 錯誤，請變更使用者名稱。 如果您收到 `'Bad Request'. Details: 400` 錯誤，請使用更強的密碼。 

將使用者名稱和密碼記錄下來，以便在部署 Web 應用程式時使用。

### <a name="create-a-resource-group"></a>建立資源群組

資源群組是一種邏輯容器，您可在其中部署和管理 Azure 資源。 使用 [az group create](/cli/azure/group?#az-group-create) 命令建立資源群組，將您的金鑰保存庫和 Web 應用程式包含於其中：

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

使用 Azure CLI 的 [az appservice plan create](/cli/azure/appservice/plan) 命令，建立 [App Service 方案](../../app-service/overview-hosting-plans.md)。 下面這個範例會在`FREE`定價層中建立名為 `myAppServicePlan` 的 App Service 方案：

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

建立 App Service 方案後，Azure CLI 會顯示如下所示的資訊：

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>

如需詳細資訊，請參閱[管理 Azure 中的 App Service 方案](../../app-service/app-service-plan-manage.md)。

### <a name="create-a-web-app"></a>建立 Web 應用程式

在 `myAppServicePlan` App Service 方案中建立 [Azure Web 應用程式](../../app-service/overview.md)。 

> [!Important]
> 如同金鑰保存庫，Azure Web 應用程式必須有唯一名稱。 在下列範例中，請將 `<your-webapp-name>` 取代為您的 Web 應用程式名稱。


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Web 應用程式建立後，Azure CLI 會顯示如下所示的輸出：

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


Git 遠端的 URL 會顯示在 `deploymentLocalGitUrl` 屬性中，其格式為 `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git`。 要儲存此 URL。 稍後您將會用到此資訊。

使用下列命令移至您的新應用程式。 請將 `<your-webapp-name>` 取代為您的應用程式名稱。

```bash
https://<your-webapp-name>.azurewebsites.net
```

您會看到新 Azure Web 應用程式的預設網頁。

### <a name="deploy-your-local-app"></a>部署您的本機應用程式

回到本機終端視窗，將 Azure 遠端新增至本機 Git 存放庫。 在下列命令中，請將 `<deploymentLocalGitUrl-from-create-step>` 取代為您在[建立 Web 應用程式](#create-a-web-app)一節中儲存的 Git 遠端 URL。

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

使用下列命令推送到 Azure 遠端，以部署您的應用程式。 當 Git 認證管理員提示您輸入認證時，請使用您在[設定本機 Git 部署](#configure-the-local-git-deployment)一節中建立的認證。

```bash
git push azure main
```

執行此命令可能需要幾分鐘的時間。 命令執行時，會顯示如下所示的資訊：
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  main -> main
</pre>

使用網頁瀏覽器移至 (或重新整理) 已部署的應用程式：

```bash
http://<your-webapp-name>.azurewebsites.net
```

您會看到來自應用程式的 "Hello World!" 訊息，如同您先前造訪 `http://localhost:5000` 時所看到的。

如需使用 Git 部署 Web 應用程式的詳細資訊，請參閱[本機 Git 部署至 Azure App Service](../../app-service/deploy-local-git.md)
 
## <a name="configure-the-web-app-to-connect-to-key-vault"></a>設定 Web 應用程式以連線至 Key Vault

在本節中，您將設定對 Key Vault 的 Web 存取，並更新應用程式程式碼以從 Key Vault 擷取秘密。

### <a name="create-and-assign-a-managed-identity"></a>建立並指派受控識別

在本教學課程中，我們將使用[受控識別](../../active-directory/managed-identities-azure-resources/overview.md)向 Key Vault 進行驗證。 受控識別會自動管理應用程式認證。

為了建立應用程式的身分識別，請在 Azure CLI 中執行 [az webapp-identity assign](/cli/azure/webapp/identity?#az-webapp-identity-assign) 命令：

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

命令會傳回此 JSON 程式碼片段：

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

若要向 Web 應用程式提供權限以便對金鑰保存庫執行 **取得** 和 **列出** 作業，請將 `principalId` 傳至 Azure CLI 的 [az keyvault set-policy](/cli/azure/keyvault?#az-keyvault-set-policy) 命令：

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

您也可以使用 [Azure 入口網站](./assign-access-policy-portal.md)或 [PowerShell](./assign-access-policy-powershell.md) 來指派存取原則。

### <a name="modify-the-app-to-access-your-key-vault"></a>修改應用程式以存取您的金鑰保存庫

在本教學課程中，您將使用 [Azure Key Vault 秘密用戶端程式庫](/dotnet/api/overview/azure/security.keyvault.secrets-readme)進行示範。 您也可以使用 [Azure Key Vault 憑證用戶端程式庫](/dotnet/api/overview/azure/security.keyvault.certificates-readme)或 [Azure Key Vault 金鑰用戶端程式庫](/dotnet/api/overview/azure/security.keyvault.keys-readme)。

#### <a name="install-the-packages"></a>安裝套件

在終端機視窗中，安裝適用於 .NET 的 Azure Key Vault 秘密用戶端程式庫，以及 Azure 身分識別用戶端程式庫套件：

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>更新程式碼

在 akvwebapp 專案中尋找並開啟 Startup.cs 檔案。 

將以下幾行新增至標頭：

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

在 `app.UseEndpoints` 呼叫前新增以下幾行，並更新 URI 以反映金鑰保存庫的 `vaultUri`。 此程式碼會使用 [DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential) 向 Key Vault 進行驗證，這會使用來自受控識別的權杖進行驗證。 如需對 Key Vault 進行驗證的詳細資訊，請參閱[開發人員指南](./developers-guide.md#authenticate-to-key-vault-in-code)。 如果 Key Vault 正在進行節流處理，程式碼也會使用指數輪詢進行重試。 如需 Key Vault 交易限制的詳細資訊，請參閱 [Azure Key Vault 節流指引](./overview-throttling.md)。

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("<mySecret>");

string secretValue = secret.Value;
```

更新 `await context.Response.WriteAsync("Hello World!");` 這一行，使其如以下這一行所示：

```csharp
await context.Response.WriteAsync(secretValue);
```

請務必先儲存變更，再繼續進行下一個步驟。

#### <a name="redeploy-your-web-app"></a>重新部署您的 Web 應用程式

現在您已更新程式碼，接下來可以使用下列 Git 命令將其重新部署至 Azure：

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure main
```

## <a name="go-to-your-completed-web-app"></a>移至您已完成的 Web 應用程式

```bash
http://<your-webapp-name>.azurewebsites.net
```

在您先前看到 "Hello World!" 之處，現在應該會顯示秘密值。

## <a name="next-steps"></a>後續步驟

- [在 .NET 中搭配使用 Azure Key Vault 與部署至虛擬機器的應用程式](./tutorial-net-virtual-machine.md)
- 深入了解 [Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)
- 檢視[開發人員指南](./developers-guide.md)
- [針對金鑰保存庫的存取進行保護](./secure-your-key-vault.md)