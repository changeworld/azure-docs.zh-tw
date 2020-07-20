---
title: 設定用來處理資料的 Azure 函式
titleSuffix: Azure Digital Twins
description: 瞭解如何建立可存取並由數位 twins 觸發的 Azure 函數。
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3b416e6ccb035ede06a360c2697a9b20ca417d98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84725897"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>連接 Azure Functions 應用程式以處理資料

在預覽期間，更新以資料為基礎的數位 twins 是透過計算資源的[**事件路由**](concepts-route-events.md)來處理，例如[Azure Functions](../azure-functions/functions-overview.md)。 Azure 函數可能用來更新數位對應項，以回應：
* 來自 IoT 中樞的裝置遙測資料
* 屬性變更或來自對應項圖形中另一個數位對應項的其他資料

本文會逐步引導您建立 Azure 函式，以與 Azure 數位 Twins 搭配使用。 

以下概述其中包含的步驟：

1. 在 Visual Studio 中建立 Azure Functions 應用程式
2. 撰寫具有[事件方格](../event-grid/overview.md)觸發程式的 Azure 函數
3. 將驗證程式代碼新增至函式（以便能夠存取 Azure 數位 Twins）
4. 將函數應用程式發行至 Azure
5. 設定[安全性](concepts-security.md)存取。 為了讓 Azure 函式在執行時間接收存取權杖以存取服務，您必須設定函數應用程式的受控服務識別。

本文的其餘部分會逐步解說 Azure 函數設定步驟，一次一個。

## <a name="create-an-azure-functions-app-in-visual-studio"></a>在 Visual Studio 中建立 Azure Functions 應用程式

在 Visual Studio 2019 中，選取 [檔案] *> [新增專案*]。 搜尋*Azure Functions*範本，加以選取，然後按 [下一步]。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-new-project.png" alt-text="Visual Studio：新增專案對話方塊":::

指定函數應用程式的名稱，然後按 [建立]。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-config.png" alt-text="Visual Studio：設定專案對話方塊":::

選取 [*事件方格] 觸發*程式，然後按 [建立]。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-trigger.png" alt-text="Visual Studio： Azure function 專案觸發程式對話方塊":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>撰寫具有事件方格觸發程式的 Azure 函數

下列程式碼會建立可供您用來記錄事件的簡短 Azure 函數： 

```csharp
// Default URL for triggering Event Grid function in the local environment
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;

namespace FunctionSample
{
    public static class FooFunction    {
        [FunctionName("Foo")]
        public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

這是您的基本 Azure 函式。

### <a name="run-and-debug-the-azure-function-app"></a>執行 Azure 函數應用程式並對其進行 debug

您現在可以編譯並執行函數。 雖然 Azure 函式的最終目的是要在雲端中執行，但您也可以在本機執行 Azure 函式並對其進行調試。

如需這方面的詳細資訊，請參閱在[本機偵錯工具事件方格觸發](../azure-functions/functions-debug-event-grid-trigger-local.md)程式。

### <a name="add-the-azure-digital-twins-sdk-to-your-azure-function-app"></a>將 Azure 數位 Twins SDK 新增至您的 Azure 函數應用程式

函式應用程式會使用適用于 .NET 的[Azure IoT 數位對應項用戶端程式庫（c #）](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)與 Azure 數位 Twins 進行互動。 若要使用 SDK，您必須在專案中包含下列套件：
* `Azure.DigitalTwins.Core`（版本 `1.0.0-preview.2` ）
* `Azure.Identity`

若要設定適當的 Azure SDK 管線以進行 Azure Functions，您也需要：
* `Azure.Net.Http`
* `Azure.Core`

視您選擇的工具而定，您可以使用 Visual Studio 套件管理員或命令列工具來執行此動作 `dotnet` 。 

將下列 using 語句新增至您的 Azure function。

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
using System.Net.Http;
using Azure.Core.Pipeline;
```

## <a name="add-authentication-code-to-the-azure-function"></a>將驗證程式代碼新增至 Azure function

接下來，新增可讓函式存取 Azure 數位 Twins 的驗證程式代碼。

將變數新增至您的函式類別以取得這些值： 
* Azure 數位 Twins 應用程式識別碼
* Azure 數位 Twins 實例的 URL。 最好的作法是從環境變數讀取服務 URL，而不是在函式中將其硬式編碼。
* 用來保存 HttpClient 實例的靜態變數。 HttpClient 的建立成本相當高，因此我們想要避免必須針對每個函式呼叫執行此動作。

此外，在函式內新增本機變數，以將您的 Azure 數位 Twins 用戶端實例保存至函式專案。 請勿*將此設為*類別內的靜態變數。

最後，將函數簽名變更為非同步。

這些變更之後，您的函式程式碼應如下所示：

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

為了讓您的函式應用程式能夠存取 Azure 數位 Twins，它必須具有系統管理的身分識別，並具有存取 Azure 數位 Twins 實例的許可權。

使用下列命令建立系統管理的身分識別。 記下輸出中的 *principalId* 欄位。

```azurecli
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

在下列命令中，使用 *principalId* 值將函數應用程式的身分識別指派給 Azure Digital Twins 執行個體的「擁有者」角色：

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

如需受控識別的詳細資訊，請參閱[如何使用適用于 App Service 和 Azure Functions 的受控](../app-service/overview-managed-identity.md)識別。

最後，您可以藉由設定環境變數，讓您的函式能夠存取您的 Azure 數位 Twins 實例 URL。 如需這種情況的詳細資訊，請參閱[環境變數](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables)。

> [!TIP]
> Azure 數位 Twins 實例的 URL 是藉由將*HTTPs://* 新增至 Azure 數位 Twins 實例*主機名稱*的開頭來建立。 若要查看主機名稱，以及實例的所有屬性，您可以執行 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 。

```azurecli
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-URL>"
```

## <a name="publish-the-azure-function-app"></a>發佈 Azure function 應用程式

若要將函式應用程式發佈至 Azure，請以滑鼠右鍵選取方案總管中的函式專案（而非方案），然後選擇 [*發行] （）*。

將會出現下列索引標籤：

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-1.png" alt-text="Visual Studio： [發行函數] 對話方塊，第1頁":::

選取或建立要搭配 Azure Functions 使用的 App Service 計畫。 如果不確定，請開始使用預設取用方案。

> [!IMPORTANT] 
> 發佈 Azure 函式將會在您的訂用帳戶上產生額外費用，與 Azure 數位 Twins 無關。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-2.png" alt-text="Visual Studio： [發行函數] 對話方塊，第2頁":::

在下列頁面上，輸入新函數應用程式的所需名稱、資源群組和其他詳細資料。

## <a name="set-up-security-access-for-the-azure-function-app"></a>設定 Azure function 應用程式的安全性存取

先前範例中的 Azure 函式基本架構會要求將持有人權杖傳遞給它，以便能夠向 Azure 數位 Twins 進行驗證。 若要確定已通過此持有人權杖，您必須設定函數應用程式的[受控服務識別（MSI）](../active-directory/managed-identities-azure-resources/overview.md) 。 這只需要針對每個函數應用程式執行一次。

若要設定此功能，請移至[Azure 入口網站](https://portal.azure.com/)並流覽至您的函式應用程式。

在 [*平臺功能*] 索引標籤中，選取 [身分*識別*]：

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-1.png" alt-text="Azure 入口網站：選取 Azure 函式的身分識別":::

在 [身分識別] 頁面上，將 [*狀態*] 切換為 [*開啟*]。 

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-2.png" alt-text="Azure 入口網站：開啟身分識別狀態":::

另請注意此頁面上顯示的**物件識別碼**，因為它將在下一節中使用。

### <a name="assign-access-roles"></a>指派存取角色

因為 Azure 數位 Twins 會使用角色型存取控制來管理存取權（如需有關此動作的詳細資訊，請參閱[概念： Azure 數位 Twins 解決方案的安全性](concepts-security.md)），您也需要為每個您想要允許存取 Azure 數位 Twins 的函式應用程式新增角色。

若要指派角色，您需要您所建立的 Azure 數位 Twins 實例的**資源識別碼**。 如果您稍早在建立實例時未記錄，您可以使用下列命令來抓取它：

```bash
az dt show --name <your-instance-name> -g <your-resource-group-name>
```
資源識別碼會是輸出的一部分，這是名為 "ID" 的長字串，以字母 "/subscriptions/..." 開頭。

在下列命令中，使用資源識別碼以及先前的 Azure 函式的物件識別碼：

```azurecli
az role assignment create --role "Azure Digital Twins Owner (Preview)" --assignee <object-ID> --scope <resource-ID>
```

## <a name="next-steps"></a>後續步驟

在本文中，您已遵循設定 Azure 函式以與 Azure 數位 Twins 搭配使用的步驟。 接下來，您可以將 Azure 函式訂閱至事件方格，以接聽端點。 此端點可能是：
* 連接到 Azure 數位 Twins 的事件方格端點，用來處理來自 Azure 數位 Twins 本身的訊息（例如屬性變更訊息、在對應項圖形中由[數位 Twins](concepts-twins-graph.md)產生的遙測訊息，或生命週期訊息）
* IoT 中樞用來傳送遙測和其他裝置事件的 IoT 系統主題
* 接收來自其他服務之訊息的事件方格端點

接下來，請參閱如何在您的基本 Azure 函式上建立，以將 IoT 中樞資料內嵌至 Azure 數位 Twins：
* [如何：從 IoT 中樞內嵌遙測](how-to-ingest-iot-hub-data.md)