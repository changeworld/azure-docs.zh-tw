---
title: 執行多個相依服務：.NET Core 和 Visual Studio
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 07/09/2018
ms.topic: tutorial
description: 本教學課程說明如何使用 Azure Dev Spaces 和 Visual Studio，在 Azure Kubernetes Service 上對多服務 .NET Core 應用程式進行偵錯
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s
ms.openlocfilehash: 073019a75f78263e9d300a82469b36268d032679
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87072933"
---
# <a name="running-multiple-dependent-services-net-core-and-visual-studio-with-azure-dev-spaces"></a>執行多個相依服務：搭配 Azure Dev Spaces 使用 .NET Core 和 Visual Studio

在本教學課程中，您將了解如何使用 Azure Dev Spaces 來開發多服務應用程式，以及享有 Dev Spaces 提供的一些附加好處。

## <a name="call-another-container"></a>呼叫另一個容器
在本節中，您會建立第二個服務 `mywebapi`，並由 `webfrontend` 加以呼叫。 每個服務都會在個別的容器中執行。 然後，您將在這兩個容器間進行偵錯。

![此圖顯示 webfrontend 服務呼叫 (以箭號表示) mywebapi 服務。](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>下載 *mywebapi* 的範例程式碼
為了節省時間，我們將從 GitHub 存放庫下載範例程式碼。 請移至 https://github.com/Azure/dev-spaces ，然後選取 [複製或下載]，從 GitHub 存放庫進行下載。 本節的程式碼位於 `samples/dotnetcore/getting-started/mywebapi`。

### <a name="run-mywebapi"></a>執行 *mywebapi*
1. 在個別的 Visual Studio 視窗中開啟專案 `mywebapi`。
1. 從啟動設定下拉式清單中選取 [Azure 開發人員空間]，如同您先前對 `webfrontend` 專案所做的。 這次請不要建立新的 AKS 叢集，而應選取您已建立的同一個叢集。 與之前相同，將 [空間] 保留為預設的 `default`，然後按一下 [確定]。 在 [輸出] 視窗中，您可能會發現 Visual Studio 開始在開發人員空間中「準備」這項新服務，以在您開始偵錯時加速作業。
1. 按 F5，並等候服務進行建置和部署。 當 Visual Studio 的狀態列變成橙色時，即表示已就緒
1. 記下 [輸出] 視窗的 [適用於 AKS 的 Azure 開發人員空間] 窗格中顯示的 URL 端點。 輸出應該會類似於 `http://localhost:<portnumber>`。 容器可能看起來像在本機執行，但實際是在 Azure 的開發人員空間中執行。
2. 當 `mywebapi` 就緒時，請開啟瀏覽器並進入 localhost 位址，然後將 `/api/values` 附加至 URL，以叫用 `ValuesController` 的預設 GET API。 
3. 如果所有步驟都已成功，您應該會看到 `mywebapi` 服務產生如下的回應。

    ![網頁會顯示兩個字串的 JSON 陣列： "value1" 和 "value2"。](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>從 webfrontend 發出對 mywebapi 的要求
我們現在要在 `webfrontend` 中撰寫對 `mywebapi` 發出要求的程式碼。 切換至含有 `webfrontend` 專案的 Visual Studio 視窗。 在 `HomeController.cs` 檔案中，將 About 方法的程式碼取代為下列程式碼：

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

      return View();
   }
   ```

上述程式碼範例會將傳入要求中的 `azds-route-as` 標頭轉送至傳出要求。 您稍後將會了解此類別如何促進[小組案例](team-development-netcore-visualstudio.md)中的生產力開發體驗。

### <a name="debug-across-multiple-services"></a>在多個服務間進行偵錯
1. 此時，`mywebapi` 仍應使用附加的偵錯工具執行中。 如果不是，在 `mywebapi` 專案中按 F5。
1. 在 `Controllers/ValuesController.cs` 檔案中負責處理 `api/values/{id}` GET 要求的 `Get(int id)` 方法內設定中斷點。
1. 在您已貼上前述程式碼的 `webfrontend` 專案中，請將中斷點設定在傳送 GET 要求給 `mywebapi/api/values` 的前一刻。
1. 在 `webfrontend` 專案中按 F5。 Visual Studio 會重新開啟瀏覽器並進入適當 localhost 連接埠，且會顯示 Web 應用程式。
1. 按一下頁面頂端的 [關於] 連結，以觸發 `webfrontend` 專案中的中斷點。 
1. 按 F10 繼續作業。 此時會觸發 `mywebapi` 專案中的中斷點。
1. 按 f5 繼續作業，且您會返回 `webfrontend` 專案中的程式碼。
1. 再按一次 F5 將會完成要求，並在瀏覽器中傳回頁面。 在 Web 應用程式中，[關於] 頁面會顯示由兩項服務串連的訊息：「來自 webfrontend 的 Hello 和來自 mywebapi 的 Hello」。

### <a name="well-done"></a>做得好！
現在，您已有每個容器可以個別開發和部署的多容器應用程式。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解 Dev Spaces 中的小組開發](team-development-netcore-visualstudio.md)
