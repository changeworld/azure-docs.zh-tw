---
title: 快速入門：使用 .NET Core 建立管理群組
description: 在本快速入門中，您會使用 .NET Core 來建立管理群組，以將您的資源組織成資源階層。
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 138998fdc23fd8a296ca50093e2952017888041f
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91604532"
---
# <a name="quickstart-create-a-management-group-with-net-core"></a>快速入門：使用 .NET Core 建立管理群組

管理群組是可協助您跨多個訂用帳戶管理容器存取、原則及合規性的容器。 建立這些容器來建置可與 [Azure 原則](../policy/overview.md)和 [Azure 角色型存取控制](../../role-based-access-control/overview.md)搭配使用的高效率階層。 如需有關管理群組的詳細資訊，請參閱[使用 Azure 管理群組來組織資源](overview.md)。

在目錄中建立的第一個管理群組可能需要 15 分鐘的時間才能完成。 第一次在 Azure 中設定目錄的管理群組服務時會執行一些程序。 程序完成時，您會收到通知。 如需詳細資訊，請參閱[管理群組的初始設定](./overview.md#initial-setup-of-management-groups)。

## <a name="prerequisites"></a>Prerequisites

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

- Azure 服務主體，包括 _clientId_ 和 _clientSecret_。 如果您沒有可與 Azure 原則搭配使用的服務主體，或想要建立新帳戶，請參閱[適用於 .NET 驗證的 Azure 管理程式庫](/dotnet/azure/sdk/authentication#mgmt-auth)。
  請略過安裝 .NET Core 套件的步驟，因為我們會在接下來的步驟中進行。

- 如果未啟用[階層保護](./how-to/protect-resource-hierarchy.md#setting---require-authorization)，則租用戶中的任何 Azure AD 使用者都可以建立管理群組，而不需將管理群組寫入權限指派給該使用者。 這個新管理群組會變成根管理群組或[預設管理群組](./how-to/protect-resource-hierarchy.md#setting---default-management-group)的子系，而建立者會獲得「擁有者」角色指派。 管理群組服務允許此功能，因此在根層級不需要角色指派。 沒有任何使用者可以存取建立的根管理群組。 為了避免尋找 Azure AD 全域管理員的困難以開始使用管理群組，我們允許在根層級建立初始管理群組。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>應用程式設定

若要啟用 .NET Core 來管理管理群組，請建立新的主控台應用程式，並安裝必要套件。

1. 確認已安裝最新版的 .NET Core (至少 **3.1.8**)。 如果尚未安裝，請在 [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core) 上下載 .NET Core。

1. 初始化名為 "mgCreate" 的新 .NET Core 主控台應用程式：

   ```dotnetcli
   dotnet new console --name "mgCreate"
   ```

1. 將目錄變更為新的專案資料夾，並安裝 Azure 原則所需的套件：

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ManagementGroups --version 1.1.1-preview

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. 使用下列程式碼取代預設的 `program.cs`，並儲存更新的檔案：

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ManagementGroups;
   using Microsoft.Azure.Management.ManagementGroups.Models;
   
   namespace mgCreate
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strGroupId = args[3];
               string strDisplayName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                      "https://management.core.windows.net",
                      new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new ManagementGroupsAPIClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var mgRequest = new CreateManagementGroupRequest
                   {
                       DisplayName = strDisplayName
                   };
                   var response = await client.ManagementGroups.CreateOrUpdateAsync(strGroupId, mgRequest);
               }
           }
       }
   }
   ```

1. 建置並發佈 `mgCreate` 主控台應用程式：

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-the-management-group"></a>建立管理群組

在本快速入門中，您會在根管理群組中建立新的管理群組。

1. 將目錄變更為您先前使用 `dotnet publish` 命令所定義的 `{run-folder}`。

1. 在終端機中輸入下列命令：

   ```bash
   mgCreate.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{groupID}" `
      "{displayName}"
   ```

上述命令會使用下列資訊：

- `{tenantId}` - 以您的租用戶識別碼取代
- `{clientId}` - 以您服務主體的用戶端識別碼取代
- `{clientSecret}` - 以您服務主體的用戶端密碼取代
- `{groupID}` - 取代為您的管理群組識別碼
- `{displayName}` - 取代為您的管理群組易記名稱

結果是根管理群組中的新管理群組。

## <a name="clean-up-resources"></a>清除資源

- 透過入口網站刪除新的管理群組。

- 如果您想要移除 .NET Core 主控台應用程式和已安裝的套件，請刪除 `mgCreate` 專案資料夾。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立管理群組來組織您的資源階層。 管理群組可以保留訂用帳戶或其他管理群組。

若要深入了解管理群組，以及如何管理您的資源階層，請繼續：

> [!div class="nextstepaction"]
> [利用管理群組來管理您的資源](./manage.md)