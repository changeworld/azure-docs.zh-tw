---
title: 快速入門：您的第一個 .NET Core 查詢
description: 在本快速入門中，您將遵循步驟以啟用 .NET Core 的 Resource Graph NuGet 套件，並執行第一個查詢。
ms.date: 10/14/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 5f825b22712956c0b94531ddb163301167f30f00
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057462"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-net-core"></a>快速入門：使用 .NET Core 執行您的第一個 Resource Graph 查詢

使用 Azure Resource Graph 的第一個步驟，是確認已安裝適用於 .NET Core 的必要套件。 此快速入門逐步引導您完成將套件新增至 .NET Core 安裝的程序。

在此程序結束時，套件將新增到您的 .NET Core 安裝中，並執行您的第一個 Resource Graph 查詢。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- Azure 服務主體，包括 _clientId_ 和 _clientSecret_。 如果您沒有可與 Resource Graph 搭配使用的服務主體，或想要建立新帳戶，請參閱[適用於 .NET 驗證的 Azure 管理程式庫](/dotnet/azure/sdk/authentication#mgmt-auth)。
  請略過安裝 .NET Core 套件的步驟，因為我們會在接下來的步驟中進行。

## <a name="create-the-resource-graph-project"></a>建立 Resource Graph 專案

若要啟用 .NET Core 來查詢 Azure Resource Graph，請建立新的主控台應用程式，並安裝必要套件。

1. 確認已安裝最新版的 .NET Core (至少 **3.1.5**)。 如果尚未安裝，請在 [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core) 上下載 .NET Core。

1. 初始化名為 "argQuery" 的新 .NET Core 主控台應用程式：

   ```dotnetcli
   dotnet new console --name "argQuery"
   ```

1. 將目錄變更為新的專案資料夾，並安裝 Azure Resource Graph 所需的套件：

   ```dotnetcli
   # Add the Resource Graph package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceGraph --version 2.0.0

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
   using Microsoft.Azure.Management.ResourceGraph;
   using Microsoft.Azure.Management.ResourceGraph.Models;

   namespace argQuery
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strQuery = args[4];

               AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/" + strTenant);
               AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://management.core.windows.net", new ClientCredential(strClientId, strClientSecret));
               ServiceClientCredentials serviceClientCreds = new TokenCredentials(authResult.AccessToken);

               ResourceGraphClient argClient = new ResourceGraphClient(serviceClientCreds);
               QueryRequest request = new QueryRequest();
               request.Subscriptions = new List<string>(){ strSubscriptionId };
               request.Query = strQuery;

               QueryResponse response = argClient.Resources(request);
               Console.WriteLine("Records: " + response.Count);
               Console.WriteLine("Data:\n" + response.Data);
           }
       }
   }
   ```

1. 建置並發佈 `argQuery` 主控台應用程式：

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="run-your-first-resource-graph-query"></a>執行第一個 Resource Graph 查詢

建立並發佈 .NET Core 主控台應用程式之後，您就可以嘗試進行簡單的 Resource Graph 查詢。 查詢會傳回前五個 Azure 資源，以及每個資源的**名稱**與**資源類型**。

在 `argQuery` 的每個呼叫中都有一些變數，需要您以自己的值取代這些變數：

- `{tenantId}` - 以您的租用戶識別碼取代
- `{clientId}` - 以您服務主體的用戶端識別碼取代
- `{clientSecret}` - 以您服務主體的用戶端密碼取代
- `{subscriptionId}` - 以您的訂用帳戶識別碼取代

1. 將目錄變更為您先前使用 `dotnet publish` 命令所定義的 `{run-folder}`。

1. 使用編譯的 .NET Core 主控台應用程式執行第一個 Azure Resource Graph 查詢：

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > 當此查詢範例未提供排序修飾詞，例如 `order by`，多次執行此查詢可能會為每個要求產生不同的資源集。

1. 將最後一個參數變更為 `argQuery.exe`，並變更查詢，以 `order by` **名稱**屬性：

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > 如同第一個查詢一樣，多次執行此查詢可能會為每個要求產生不同的資源集。 查詢命令的順序很重要。 在此範例中，`order by` 會出現在 `limit` 之後。 此命令順序會先限制查詢結果，然後再加以排序。

1. 將最後一個參數變更為 `argQuery.exe`，並變更查詢，以先 `order by` **名稱**屬性，然後 `limit` 為只顯示前五個結果：

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

執行最終查詢數次後，假設您的環境中未變更任何內容，傳回的結果將會一致，且依**名稱**屬性排序，但仍限制為只顯示前五個結果。

## <a name="clean-up-resources"></a>清除資源

如果您想要移除 .NET Core 主控台應用程式和已安裝的套件，您可以藉由刪除 `argQuery` 專案資料夾來完成。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用必要的 Resource Graph 套件建立 .NET Core 主控台應用程式，並執行您的第一個查詢。 若要深入了解 Resource Graph 語言，請繼續前往查詢語言詳細資料頁面。

> [!div class="nextstepaction"]
> [取得有關查詢語言的詳細資訊](./concepts/query-language.md)