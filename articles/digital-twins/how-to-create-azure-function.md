---
title: 設定用來處理資料的 Azure 函式
titleSuffix: Azure Digital Twins
description: 瞭解如何建立可存取並由數位 twins 觸發的 Azure 函數。
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d9f9957209c6df91185059085f57636a16a3961c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589396"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>連接 Azure Functions apps 以處理資料

在預覽期間，會使用 [**事件路由**](concepts-route-events.md) 透過計算資源（例如 [Azure Functions](../azure-functions/functions-overview.md)）來處理以資料為基礎的數位 twins。 Azure 函式可能會用來更新數位對應項，以回應：
* 來自 IoT 中樞的裝置遙測資料
* 屬性變更或來自對應項圖形中其他數位對應項的其他資料

本文將逐步引導您建立 Azure 函式，以搭配 Azure 數位 Twins 使用。 

以下是其包含的步驟概述：

1. 在 Visual Studio 中建立 Azure Functions 應用程式
2. 使用 [事件方格](../event-grid/overview.md) 觸發程式撰寫 Azure 函數
3. 將驗證程式代碼新增至函式 (，以便能夠存取 Azure 數位 Twins) 
4. 將函數應用程式發佈至 Azure
5. 設定 [安全性](concepts-security.md) 存取權。 為了讓 Azure 函式在執行時間接收存取權杖以存取服務，您必須設定函數應用程式的受控服務識別。

本文的其餘部分將逐步解說 Azure function 設定步驟（一次一個）。

## <a name="create-an-azure-functions-app-in-visual-studio"></a>在 Visual Studio 中建立 Azure Functions 應用程式

在 Visual Studio 2019 中，選取 [檔案] *> [新增專案*]。 搜尋 *Azure Functions* 範本，選取它，然後按 [下一步]。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-new-project.png" alt-text="Visual Studio：新增專案對話方塊":::

指定函數應用程式的名稱，然後按 [建立]。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-config.png" alt-text="Visual Studio：設定專案對話方塊":::

選取 *事件方格觸發* 程式，然後按 [建立]。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-trigger.png" alt-text="Visual Studio： Azure 函數專案觸發程式對話方塊":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>使用事件方格觸發程式撰寫 Azure 函數

下列程式碼會建立可供您用來記錄事件的簡單 Azure 函數： 

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

這是您的基本 Azure 函數。

### <a name="run-and-debug-the-azure-function-app"></a>執行 Azure 函數應用程式並對其進行偵錯工具

您現在可以編譯並執行函數。 雖然 Azure 函式最終是要在雲端中執行，但您也可以在本機執行 Azure 函式並進行調試。

如需這方面的詳細資訊，請參閱在 [*本機偵錯工具事件方格觸發*](../azure-functions/functions-debug-event-grid-trigger-local.md)程式。

### <a name="add-the-azure-digital-twins-sdk-to-your-azure-function-app"></a>將 Azure 數位 Twins SDK 新增至您的 Azure 函數應用程式

函數應用程式會使用 [適用于 .net (c # ) 的 Azure IoT 數位對應項用戶端程式庫 ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)，與 Azure 數位 Twins 互動。 您必須在專案中包含下列套件，才能使用 SDK：
* `Azure.DigitalTwins.Core` (版本 `1.0.0-preview.2`) 
* `Azure.Identity` (版本 `1.1.1`) 

若要設定 Azure SDK 管線以針對 Azure Functions 適當地設定，您也將需要：
* `Azure.Net.Http`
* `Azure.Core`

您可以使用 Visual Studio 套件管理員或命令列工具，視您選擇的工具而定 `dotnet` 。 

將下列 using 語句新增至您的 Azure 函數。

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
using System.Net.Http;
using Azure.Core.Pipeline;
```

## <a name="add-authentication-code-to-the-azure-function"></a>將驗證程式代碼新增至 Azure 函數

接下來，新增可讓函式存取 Azure 數位 Twins 的驗證程式代碼。

將變數新增至您的函式類別以用於這些值： 
* Azure 數位 Twins 應用程式識別碼
* Azure 數位 Twins 實例的 URL。 從環境變數讀取服務 URL 是很好的作法，而不是在函式中進行硬式編碼。
* 用來保存 HttpClient 實例的靜態變數。 HttpClient 的建立成本相當昂貴，而且我們想要避免在每次函式呼叫時都必須這麼做。

此外，在您的函式內新增一個區域變數，以將您的 Azure 數位 Twins 用戶端實例保存到函式專案。 請勿 *將此變數設為* 您類別內的靜態變數。

最後，將函數簽章變更為非同步。

這些變更之後，您的函式程式碼應該如下所示：

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

為了讓您的函式應用程式能夠存取 Azure 數位 Twins，它必須有系統管理的身分識別，而且有權存取您的 Azure 數位 Twins 實例。

使用下列命令建立系統管理的身分識別。 記下輸出中的 *principalId* 欄位。

```azurecli
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

在下列命令中，使用 *principalId* 值將函數應用程式的身分識別指派給 Azure Digital Twins 執行個體的「擁有者」角色：

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

如需受控識別的詳細資訊，請參閱 [*如何使用受控識別進行 App Service 和 Azure Functions*](../app-service/overview-managed-identity.md)。

最後，您可以藉由設定環境變數，讓您的 Azure 數位 Twins 實例的 URL 可供您的函式存取。 如需有關這個的詳細資訊，請參閱 [*環境變數*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables)。

> [!TIP]
> Azure 數位 Twins 實例的 URL 是藉由將 *HTTPs://* 新增至 Azure 數位 Twins 實例 *主機名稱*的開頭來建立。 若要查看主機名稱，以及實例的所有屬性，您可以執行 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 。

```azurecli
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-URL>"
```

## <a name="publish-the-azure-function-app"></a>發佈 Azure 函數應用程式

若要將函式應用程式發佈至 Azure，請在方案總管中，以滑鼠右鍵選取函數專案 (而不是 [方案) ]，然後選擇 [ *發行 ( # B3 *]。

將會顯示下列索引標籤：

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-1.png" alt-text="Visual Studio：發佈函式對話方塊，第1頁":::

選取或建立要搭配 Azure Functions 使用的 App Service 計畫。 如果不確定，請先使用預設取用方案。

> [!IMPORTANT] 
> 發佈 Azure 函式將會在您的訂用帳戶上產生額外費用，與 Azure 數位 Twins 無關。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-2.png" alt-text="Visual Studio：發佈函式對話方塊，第2頁":::

在下列頁面上，輸入新函數應用程式的所需名稱、資源群組和其他詳細資料。

## <a name="set-up-security-access-for-the-azure-function-app"></a>設定 Azure 函數應用程式的安全性存取權

先前範例中的 Azure 函式基本架構需要將持有人權杖傳遞給它，才能使用 Azure 數位 Twins 進行驗證。 若要確定已通過此持有人權杖，您必須為函數應用程式設定 [ (MSI) 的受控服務識別 ](../active-directory/managed-identities-azure-resources/overview.md) 。 每個函數應用程式只需要執行一次。

若要進行此設定，請移至 [Azure 入口網站](https://portal.azure.com/) 並流覽至您的函數應用程式。

在 [ *平臺功能* ] 索引標籤中，選取 [身分 *識別*：

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-1.png" alt-text="Azure 入口網站：選取 Azure 函數的身分識別":::

在 [身分識別] 頁面上，將 [ *狀態* ] 切換為 [ *開啟*]。 

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-2.png" alt-text="Azure 入口網站：開啟身分識別狀態":::

也請注意此頁面上顯示的 **物件識別碼** ，因為它將在下一節中使用。

### <a name="assign-access-roles"></a>指派存取角色

因為 Azure 數位 Twins 會使用角色型存取控制來管理存取 (請參閱 [*概念： Azure 數位 Twins 解決方案的安全性*](concepts-security.md) ，如需此) 的詳細資訊，您也需要為每個您想要允許存取 Azure 數位 Twins 的函式應用程式新增角色。

若要指派角色，您需要您所建立的 Azure 數位 Twins 實例的 **資源識別碼** 。 如果您在建立實例之前未記錄此檔案，您可以使用下列命令來取得它：

```bash
az dt show --name <your-instance-name> -g <your-resource-group-name>
```
資源識別碼將會是輸出的一部分，且名稱為 "ID" 且開頭為字母 "/subscriptions/..." 的長字串。

在下列命令中使用資源識別碼以及 Azure 函式的物件識別碼：

```azurecli
az role assignment create --role "Azure Digital Twins Owner (Preview)" --assignee <object-ID> --scope <resource-ID>
```

## <a name="next-steps"></a>後續步驟

在本文中，您已遵循設定 Azure 函式以搭配 Azure 數位 Twins 使用的步驟。 接下來，您可以將 Azure 函式訂閱至事件方格，以在端點上接聽。 此端點可能是：
* 附加至 Azure 數位 Twins 的事件方格端點，可處理來自 Azure 數位 Twins 本身的訊息 (例如屬性變更訊息、對應項圖形中 [數位 Twins](concepts-twins-graph.md) 所產生的遙測訊息，或是生命週期訊息) 
* IoT 中樞用來傳送遙測和其他裝置事件的 IoT 系統主題
* 接收來自其他服務之訊息的事件方格端點

接下來，請參閱如何建立基本 Azure 函式，以將 IoT 中樞資料內嵌至 Azure 數位 Twins：
* [*作法：從 IoT 中樞內嵌遙測*](how-to-ingest-iot-hub-data.md)