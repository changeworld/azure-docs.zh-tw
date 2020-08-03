---
title: 執行多個相依服務：Node.js 和 Visual Studio Code
services: azure-dev-spaces
ms.date: 11/21/2018
ms.topic: tutorial
description: 本教學課程說明如何使用 Azure Dev Spaces 和 Visual Studio Code，在 Azure Kubernetes Service 上對多服務 Node.js 應用程式進行偵錯
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s
ms.openlocfilehash: 2c87dedda1db97a033526c809de735fe036120ef
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006977"
---
# <a name="running-multiple-dependent-services-nodejs-and-visual-studio-code-with-azure-dev-spaces"></a>執行多個相依服務：搭配 Azure Dev Spaces 使用 Node.js 和 Visual Studio Code

在本教學課程中，您將了解如何使用 Azure Dev Spaces 來開發多服務應用程式，以及享有 Dev Spaces 提供的一些附加好處。

## <a name="call-a-service-running-in-a-separate-container"></a>呼叫在個別容器中執行的服務

在本節中，您會建立第二個服務 `mywebapi`，並由 `webfrontend` 加以呼叫。 每個服務都會在個別的容器中執行。 然後，您將在這兩個容器間進行偵錯。

![此圖顯示 webfrontend 服務呼叫 (以箭號表示) mywebapi 服務。](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>開啟 *mywebapi* 的範例程式碼
您在名為 `samples` 的資料夾下應該已有本指南的 `mywebapi` 適用的範例程式碼 (如果沒有，請移至 https://github.com/Azure/dev-spaces 並選取 [複製或下載]，以從 GitHub 存放庫進行下載。)本節的程式碼位於 `samples/nodejs/getting-started/mywebapi` 。

### <a name="run-mywebapi"></a>執行 *mywebapi*
1. 在個別的 VS Code 視窗中開啟資料夾 `mywebapi`。
1. 開啟 [命令選擇區] (使用 [檢視 | 命令選擇區] 功能表)，然後使用自動完成功能以輸入及選取以下命令：`Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`。 此命令不應與 `azds prep` 命令混淆；後者會設定要部署的專案。
1. 按 F5，並等候服務進行建置和部署。 當偵錯主控台中出現「正在接聽連接埠 80」的訊息時，您將會知道它已準備就緒。
1. 記下端點 URL，它看起來會像是 `http://localhost:<portnumber>`。 **提示：VS Code 狀態列會變成橘色，並顯示可點按的 URL。** 容器可能看起來像在本機執行，但實際是在 Azure 的開發環境中執行的。 localhost 位址的原因是因為 `mywebapi` 並未定義任何公用端點，而只能從 Kubernetes 執行個體中存取。 為了方便您操作以及與本機電腦上的私人服務互動，Azure 開發人員空間會建立暫存的 SSH 通道，連到在 Azure 中執行的容器。
1. 當 `mywebapi` 就緒時，請開啟瀏覽器並進入 localhost 位址。 您應該會看到 `mywebapi` 服務的回應 (「來自 mywebapi 的 Hello」)。


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>從 webfrontend 發出對 mywebapi 的要求
我們現在要在 `webfrontend` 中撰寫對 `mywebapi` 發出要求的程式碼。
1. 切換至 `webfrontend` 的 VS Code 視窗。
1. 在 `server.js` 的最上方加入下列幾行程式碼：
    ```javascript
    var request = require('request');
    ```

3. 取代`/api` 處理常式的程式碼。 在處理要求時，接著會呼叫 `mywebapi`，然後從這兩項服務傳回結果。

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```
   1. 移除 `server.js` 結尾處的 `server.close()` 行

上述程式碼範例會將傳入要求中的 `azds-route-as` 標頭轉送至傳出要求。 您稍後將了解這對小組的共同開發有何幫助。

### <a name="debug-across-multiple-services"></a>在多個服務間進行偵錯
1. 此時，`mywebapi` 仍應使用附加的偵錯工具執行中。 如果不是，在 `mywebapi` 專案中按 F5。
1. 在 [`server.js` 第 8 行](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/mywebapi/server.js#L8)上的預設 GET `/` 處理常式中設定中斷點。
1. 在 `webfrontend` 專案中，請將中斷點設定在傳送 GET 要求給 `http://mywebapi` 的前一刻。
1. 在 `webfrontend` 專案中按 F5。
1. 開啟 Web 應用程式，並在這兩項服務中逐步執行程式碼。 Web 應用程式應該會顯示由兩項服務串連的訊息：「來自 webfrontend 的 Hello 和來自 mywebapi 的 Hello」。

### <a name="well-done"></a>做得好！
現在，您已有每個容器可以個別開發和部署的多容器應用程式。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解 Dev Spaces 中的小組開發](team-development-nodejs.md)
