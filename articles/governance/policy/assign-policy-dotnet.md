---
title: 快速入門：使用 .NET Core 進行新原則指派
description: 在本快速入門中，使用 .NET Core 建立 Azure 原則指派，以識別不符合規範的資源。
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: c4c8f8e9df544b6fc00b5b7701435f5a606f9764
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91604549"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-net-core"></a>快速入門：使用 .NET Core 建立原則指派，以識別不符合規範的資源

了解 Azure 中合規性的第一個步驟是識別您資源的狀態。 在本快速入門中，您會建立原則指派，以識別未使用受控磁碟的虛擬機器。 完成時，您會識別出「不符合規範」__ 的虛擬機器。

.NET Core 程式庫可用來管理 Azure 資源。 本指南說明如何使用適用於 Azure 原則的 .NET Core 程式庫建立原則指派。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- Azure 服務主體，包括 _clientId_ 和 _clientSecret_。 如果您沒有可與 Azure 原則搭配使用的服務主體，或想要建立新帳戶，請參閱[適用於 .NET 驗證的 Azure 管理程式庫](/dotnet/azure/sdk/authentication#mgmt-auth)。
  請略過安裝 .NET Core 套件的步驟，因為我們會在接下來的步驟中進行。

## <a name="create-the-azure-policy-project"></a>建立 Azure 原則專案

若要啟用 .NET Core 來管理 Azure 原則，請建立新的主控台應用程式，並安裝必要套件。

1. 確認已安裝最新版的 .NET Core (至少 **3.1.8**)。 如果尚未安裝，請在 [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core) 上下載 .NET Core。

1. 初始化名為 "policyAssignment" 的新 .NET Core 主控台應用程式：

   ```dotnetcli
   dotnet new console --name "policyAssignment"
   ```

1. 將目錄變更為新的專案資料夾，並安裝 Azure 原則所需的套件：

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceManager --version 3.10.0-preview

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
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.ResourceManager.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
               string strDisplayName = args[5];
               string strPolicyDefID = args[6];
               string strDescription = args[7];
               string strScope = args[8];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));

               using (var client = new PolicyClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyAssignment = new PolicyAssignment
                   {
                       DisplayName = strDisplayName,
                       PolicyDefinitionId = strPolicyDefID,
                       Description = strDescription
                   };
                   var response = await client.PolicyAssignments.CreateAsync(strScope, strName, policyAssignment);
               }
           }
       }
   }
   ```

1. 建置並發佈 `policyAssignment` 主控台應用程式：

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-a-policy-assignment"></a>建立原則指派

在本快速入門中，您會建立一個原則指派，並且指派**稽核沒有受控磁碟的虛擬機器** (`06a78e20-9358-41c9-923c-fb736d382a4d`) 定義。 此原則定義會識別與原則定義中設定之條件不相符的資源。

1. 將目錄變更為您先前使用 `dotnet publish` 命令所定義的 `{run-folder}`。

1. 在終端機中輸入下列命令：

   ```bash
   policyAssignment.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks" `
      "Audit VMs without managed disks Assignment" `
      "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      "Shows all virtual machines not using managed disks" `
      "{scope}"
   ```

上述命令會使用下列資訊：

- `{tenantId}` - 以您的租用戶識別碼取代
- `{clientId}` - 以您服務主體的用戶端識別碼取代
- `{clientSecret}` - 以您服務主體的用戶端密碼取代
- `{subscriptionId}` - 以您的訂用帳戶識別碼取代
- **名稱** - 原則指派物件的唯一名稱。 上述範例使用 _audit-vm-manageddisks_。
- **displayName** - 原則指派的顯示名稱。 在此案例中，您會使用_稽核沒有受控磁碟指派的虛擬機器_。
- **policyDefID** - 原則定義路徑，這是您用來建立指派的根基。 在此案例中，即為原則定義_稽核沒有受控磁碟的虛擬機器_的 ID。
- **description** - 原則用途或為何指派給此範圍的更深入說明。
- **scope** - 範圍會決定在哪些資源或資源群組上強制執行原則指派。 此範圍可包含管理群組到個別資源。 請務必將 `{scope}` 取代為下列其中一個模式：
  - 管理群組：`/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - 訂用帳戶：`/subscriptions/{subscriptionId}`
  - 資源群組：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - 資源：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

您現在可以識別不相容的資源，以了解環境的相容性狀態。

## <a name="identify-non-compliant-resources"></a>識別不符合規範的資源

現在您已建立原則指派，您可以識別不符合規範的資源。

1. 初始化名為 "policyCompliance" 的新 .NET Core 主控台應用程式：

   ```dotnetcli
   dotnet new console --name "policyCompliance"
   ```

1. 將目錄變更為新的專案資料夾，並安裝 Azure 原則所需的套件：

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.PolicyInsights --version 3.1.0

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
   using Microsoft.Azure.Management.PolicyInsights;
   using Microsoft.Azure.Management.PolicyInsights.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new PolicyInsightsClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyQueryOptions = new QueryOptions
                   {
                       Filter = $"IsCompliant eq false and PolicyAssignmentId eq '{strName}'",
                       Apply = "groupby(ResourceId)"
                   };
   
                   var response = await client.PolicyStates.ListQueryResultsForSubscriptionAsync(
                       "latest", strSubscriptionId, policyQueryOptions);
                   Console.WriteLine(response.Odatacount);
               }
           }
       }
   }
   ```

1. 建置並發佈 `policyAssignment` 主控台應用程式：

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

1. 將目錄變更為您先前使用 `dotnet publish` 命令所定義的 `{run-folder}`。

1. 在終端機中輸入下列命令：

   ```bash
   policyCompliance.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks"
   ```

上述命令會使用下列資訊：

- `{tenantId}` - 以您的租用戶識別碼取代
- `{clientId}` - 以您服務主體的用戶端識別碼取代
- `{clientSecret}` - 以您服務主體的用戶端密碼取代
- `{subscriptionId}` - 以您的訂用帳戶識別碼取代
- **名稱** - 原則指派物件的唯一名稱。 上述範例使用 _audit-vm-manageddisks_。

`response` 中的結果會符合原則指派在 Azure 入口網站檢視中的 [資源合規性] 索引標籤所顯示的內容。

## <a name="clean-up-resources"></a>清除資源

- 刪除原則指派 透過入口網站_稽核沒有受控磁碟指派的 VM_。 原則定義是內建的，因此沒有任何定義可供移除。

- 如果您想要移除 .NET Core 主控台應用程式和已安裝的套件，請刪除 `policyAssignment` 和 `policyCompliance` 專案資料夾。

## <a name="next-steps"></a>後續步驟

在這個快速入門中，您指派原則定義以識別 Azure 環境中的不相容資源。

若要深入了解指派原則定義，以驗證新資源是符合規範的，請繼續進行以下的教學課程：

> [!div class="nextstepaction"]
> [建立及管理原則](./tutorials/create-and-manage.md)
