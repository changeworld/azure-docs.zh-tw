---
title: 教學課程 - Web 應用程式以應用程式身分存取 Microsoft Graph | Azure
description: 在本教學課程中，您將了解如何使用受控識別存取 Microsoft Graph 中的資料。
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/30/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: facc6a4ab8344f9f72fc7abc27433c18ab435504
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436533"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-app"></a>教學課程：從安全的應用程式以應用程式身分存取 Microsoft Graph

了解如何從在 Azure App Service 上執行的 Web 應用程式存取 Microsoft Graph。

:::image type="content" alt-text="顯示存取 Microsoft Graph 的圖表。" source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

您想要呼叫 Web 應用程式的 Microsoft Graph。 讓 Web 應用程式存取資料的安全方式，是使用[系統指派的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。 來自 Azure Active Directory 的受控識別可讓應用程式服務透過角色型存取控制 (RBAC) 存取資源，而不需要應用程式認證。 將受控識別指派給您的 Web 應用程式後，Azure 會負責建立和散發憑證。 您不需要擔心管理祕密或應用程式認證。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 在 Web 應用程式上建立系統指派的受控識別。
> * 將 Microsoft Graph API 權限新增至受控識別。
> * 使用受控識別從 Web 應用程式呼叫 Microsoft Graph。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

* 在 Azure App Service 上執行且已啟用 [App Service 驗證/授權模組](scenario-secure-app-authentication-app-service.md)的 Web 應用程式。

## <a name="enable-managed-identity-on-app"></a>啟用應用程式上的受控識別

如果您透過 Visual Studio 建立並發佈 Web 應用程式，則系統會為您的應用程式啟用受控識別。 在應用程式服務中，選取左側窗格中的 [身分識別]，然後選取 [系統指派]。 確定 **狀態** 設為 **開啟**。 若未設定為開啟，請依序選取 [儲存] 和 [是] 以啟用系統指派的受控識別。 啟用受控識別時，狀態會設定為「開啟」，且物件識別碼可供使用。

請記下 [物件識別碼] 值，您會在下一個步驟用到。

:::image type="content" alt-text="顯示 [系統指派的身分識別] 的螢幕擷取畫面。" source="./media/scenario-secure-app-access-microsoft-graph/create-system-assigned-identity.png":::

## <a name="grant-access-to-microsoft-graph"></a>授與 Microsoft Graph 連線

存取 Microsoft Graph 時，受控識別必須具有要執行之作業的適當權限。 目前沒有任何選項可透過 Azure 入口網站指派這類權限。 下列指令碼會將要求的 Microsoft Graph API 權限新增至受控識別服務主體物件。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD.

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$TenantID="<tenant-id>"
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp-20201102125811"

# Get the ID of the managed identity for the web app.
$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid

# Check the Microsoft Graph documentation for the permission you need for the operation.
$PermissionName = "User.Read.All"

Connect-AzureAD -TenantId $TenantID

# Get the service principal for Microsoft Graph.
$GraphServicePrincipal = Get-AzureADServicePrincipal -SearchString "Microsoft Graph"

# Assign permissions to the managed identity service principal.
$AppRole = $GraphServicePrincipal.AppRoles | `
Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}

New-AzureAdServiceAppRoleAssignment -ObjectId $spID -PrincipalId $spID `
-ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az login

webAppName="SecureWebApp-20201106120003"

spId=$(az resource list -n $webAppName --query [*].identity.principalId --out tsv)

graphResourceId=$(az ad sp list --display-name "Microsoft Graph" --query [0].objectId --out tsv)

appRoleId=$(az ad sp list --display-name "Microsoft Graph" --query "[0].appRoles[?value=='User.Read.All' && contains(allowedMemberTypes, 'Application')].id" --output tsv)

uri=https://graph.microsoft.com/v1.0/servicePrincipals/$spID/appRoleAssignments

body="{'principalId':'$spId','resourceId':'$graphResourceId','appRoleId':'$appRoleId'}"

az rest --method post --uri $uri --body $body --headers "Content-Type=application/json"
```

---

執行指令碼之後，您可以在 [Azure 入口網站](https://portal.azure.com)中驗證要求的 API 權限是否已指派給受控識別。

移至 **Azure Active Directory**，然後選取 [企業應用程式]。 此窗格會顯示您租用戶中的所有服務主體。 在 **所有應用程式** 中，選取受控識別的服務主體。 

如果您遵循本教學課程，則會有兩個具有相同顯示名稱 (例如 SecureWebApp2020094113531) 的服務主體。 具有「首頁 URL」的服務主體代表您租用戶中的 Web 應用程式。 沒有「首頁 URL」的服務主體代表您 Web 應用程式的系統指派受控識別。 受控識別的 **物件識別碼** 值符合您先前建立之受控識別的物件識別碼。

在選取受控識別的服務主體。

:::image type="content" alt-text="顯示 [所有應用程式] 選項的螢幕擷取畫面。" source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-all-applications.png":::

在 [概觀] 中選取 [權限]，您便會看到 Microsoft Graph 的新增權限。

:::image type="content" alt-text="顯示 [權限] 窗格的螢幕擷取畫面。" source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-permissions.png":::

## <a name="call-microsoft-graph-net"></a>呼叫 Microsoft Graph (.NET)

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 類別是用來取得程式碼的權杖認證，以授權 Microsoft Graph 的要求。 建立 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 類別的執行個體，此執行個體會使用受控識別擷取權杖，並將其附加至服務用戶端。 下列程式碼範例會取得已驗證的權杖認證，並用來建立服務用戶端物件，以取得群組中的使用者。

若要在範例應用程式中查看此程式碼，請參閱 [GitHub 上的範例](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/3-WebApp-graphapi-managed-identity)。

### <a name="install-the-microsoftgraph-client-library-package"></a>安裝 Microsoft.Graph 用戶端程式庫套件

使用 .NET Core 命令列介面或 Visual Studio 中的套件管理員主控台，在您的專案中安裝 [Microsoft.Graph NuGet 套件](https://www.nuget.org/packages/Microsoft.Graph)。

# <a name="command-line"></a>[命令列](#tab/command-line)

開啟命令列，並切換至包含專案檔的目錄。

執行安裝命令。

```dotnetcli
dotnet add package Microsoft.Graph
```

# <a name="package-manager"></a>[套件管理員](#tab/package-manager)

在 Visual Studio 中開啟專案/方案，然後使用 **工具** > **NuGet 套件管理員** > **套件管理員主控台** 命令開啟主控台。

執行安裝命令。
```powershell
Install-Package Microsoft.Graph
```

---

### <a name="example"></a>範例

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Azure.Identity;
using Microsoft.Graph.Core;
using System.Net.Http.Headers;

...

public IList<MSGraphUser> Users { get; set; }

public async Task OnGetAsync()
{
    // Create the Microsoft Graph service client with a DefaultAzureCredential class, which gets an access token by using the available Managed Identity.
    var credential = new DefaultAzureCredential();
    var token = credential.GetToken(
        new Azure.Core.TokenRequestContext(
            new[] { "https://graph.microsoft.com/.default" }));

    var accessToken = token.Token;
    var graphServiceClient = new GraphServiceClient(
        new DelegateAuthenticationProvider((requestMessage) =>
        {
            requestMessage
            .Headers
            .Authorization = new AuthenticationHeaderValue("bearer", accessToken);

            return Task.CompletedTask;
        }));

    List<MSGraphUser> msGraphUsers = new List<MSGraphUser>();
    try
    {
        var users =await graphServiceClient.Users.Request().GetAsync();
        foreach(var u in users)
        {
            MSGraphUser user = new MSGraphUser();
            user.userPrincipalName = u.UserPrincipalName;
            user.displayName = u.DisplayName;
            user.mail = u.Mail;
            user.jobTitle = u.JobTitle;

            msGraphUsers.Add(user);
        }
    }
    catch(Exception ex)
    {
        string msg = ex.Message;
    }

    Users = msGraphUsers;
}
```

## <a name="clean-up-resources"></a>清除資源

如果您已完成本教學課程，且不再需要 Web 應用程式或相關聯的資源，請[清除您建立的資源](scenario-secure-app-clean-up-resources.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
>
> * 在 Web 應用程式上建立系統指派的受控識別。
> * 將 Microsoft Graph API 權限新增至受控識別。
> * 使用受控識別從 Web 應用程式呼叫 Microsoft Graph。

了解如何將 [.NET Core 應用程式](tutorial-dotnetcore-sqldb-app.md)、[Python 應用程式](tutorial-python-postgresql-app.md)、[JAVA 應用程式](tutorial-java-spring-cosmosdb.md)或 [Node.js 應用程式](tutorial-nodejs-mongodb-app.md)連線到資料庫。