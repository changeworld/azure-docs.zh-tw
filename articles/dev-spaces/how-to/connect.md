---
title: 將開發電腦連接到 AKS 群集（預覽版）
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: 瞭解如何將開發電腦連接到具有 Azure 開發空間的 AKS 群集
keywords: Azure 開發空間、開發空間、Docker、庫伯奈斯、Azure、AKS、Azure 庫伯奈斯服務、容器
ms.openlocfilehash: 772f221a8047a71902f36fa98ded6c24b5e02d27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235010"
---
# <a name="connect-your-development-computer-to-an-aks-cluster-preview"></a>將開發電腦連接到 AKS 群集（預覽版）

Azure 開發人員空間允許您在開發電腦上運行和調試具有或不帶容器的代碼，同時仍與應用程式或服務的其餘部分連接到 Kubernetes 群集。 將開發電腦連接到群集可説明您快速開發應用程式並執行端到端測試，而無需創建任何 Docker 或 Kubernetes 配置。 您還可以連接到 AKS 群集，而不會影響可能使用相同的群集的其他工作負載或使用者。

Azure 開發空間重定向連接的 AKS 群集和開發電腦之間的流量。 此流量重定向允許開發電腦上的代碼和在 AKS 群集中運行的服務進行通信，就像它們位於同一 AKS 群集中一樣。 由於代碼在開發電腦上運行，因此在用於運行和調試該代碼的開發工具中，您也有靈活性。 Azure 開發人員空間還提供一種複製開發電腦中 AKS 群集中可用於 pod 的環境變數和裝載的檔的方法。

在本指南中，您將了解如何：

* 在 Azure 中於受控 Kubernetes 叢集上設定 Azure Dev Spaces。
* 將具有多個微服務的大型應用程式部署至開發人員空間。
* 使用 Azure 開發空間在 AKS 群集和開發電腦上運行的代碼之間重定向流量。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="before-you-begin"></a>開始之前

本指南使用[Azure 開發空間自行車共用應用程式範例](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp)來演示將開發電腦連接到 AKS 群集。 按照[Azure 開發人員空間自行車共用應用程式範例 README](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md)中的說明運行應用程式範例。 或者，如果您在 AKS 群集上擁有自己的應用程式，您仍然可以按照以下步驟操作並使用您自己的服務和 pod 的名稱。

### <a name="limitations"></a>限制

* 目前不支援 UDP。

### <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/free)。
* [已安裝 Azure CLI][azure-cli]。
* 在 MacOS 或 Windows 10 上安裝並運行[Azure 開發空間][azds-vs-code]擴展的[視覺化工作室代碼][vs-code]。
* [Azure 開發空間自行車共用應用程式範例](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp)或您自己的應用程式在 AKS 群集上運行。

## <a name="connect-your-development-computer"></a>連接開發電腦

在視覺化工作室代碼中打開*開發空間/示例/自行車共用應用/自行車*，並使用 Azure 開發空間擴展將開發電腦連接到 AKS 群集。

要使用 Azure 開發人員空間擴展，請按一下"*查看*然後*命令調色板*"，在視覺化工作室代碼中打開命令調色板。 開始鍵入`Azure Dev Spaces: Redirect`並按一下`Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`。，`Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`或`Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`。

![命令](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>選擇重定向選項

如果運行`Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`，系統將要求您選擇現有的 Kubernetes 服務：

![選擇服務](../media/how-to-connect/connect-choose-service.png)

此選項將此服務的 AKS 群集中的所有流量重定向到開發電腦上運行的應用程式的版本。 如果此服務在 AKS 群集中運行多個 pod，則此服務的所有流量將僅路由到開發電腦。 Azure 開發空間還會將應用程式的所有出站流量路由回 AKS 群集。

如果運行`Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`，系統將要求您選擇特定的窗格：

![選擇 Pod](../media/how-to-connect/connect-choose-pod.png)

此選項連接到特定窗格。 此選項可用於與不發送或接收流量的 pod 進行交互以及複製已終止的 pod。 如果 Pod 確實發送和接收流量，則此選項以與 AKS 群集`Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`中的所有流量相同的方式重定向與所選窗格的服務相關的所有 pod。

如果運行`Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`，系統不會提示您選擇現有的 Pod 或服務。 此選項將所有出站流量從開發電腦上運行的應用程式重定向到 AKS 群集。

在此示例中，選擇`Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`並選擇*自行車*服務。

### <a name="select-a-connection-mode"></a>選擇連接模式

選擇重定向選項後，系統將提示您選擇 *"替換*"或 *"克隆"* 連接模式。

![替換或克隆](../media/how-to-connect/connect-replace-clone.png)

*"替換*"選項將替換 AKS 群集中的當前 pod 或服務，並將該服務的所有流量重定向到開發電腦。 此選項可能會破壞 AKS 群集中與您重定向的服務交互的其他服務，在開發電腦上啟動應用程式之前，這些服務可能無法正常工作。 *"克隆"* 選項允許您選擇現有的子開發空間或創建新的子開發空間，以便將 Pod 或服務的流量重定向到開發電腦。 此選項允許您單獨工作，並且不會中斷其他服務，因為只有到該子開發空間的流量才會重定向到開發電腦。 "*克隆"* 選項要求 AKS 群集啟用 Azure 開發空間。

在此示例中，選擇 *"替換*"。

> [!NOTE]
> 如果現有服務的 pod 有多個容器，系統也會提示您選擇應用程式的容器。

### <a name="select-a-port-for-your-application"></a>為應用程式選擇埠

選擇連接模式後，系統將提示您輸入本地應用程式的 TCP 埠。 如果應用程式打開多個埠，請用逗號（例如*80，81）* 分隔它們。 如果應用程式不接受任何網路請求，請輸入*0*。 在此示例中，輸入*3000*。

![連接選擇埠](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>確認您已連接

選擇應用程式的 TCP 埠後，Azure 開發空間將建立到 AKS 群集的連接。 Azure 開發空間將代理注入 AKS 群集，以重定向 AKS 群集和開發電腦之間的流量。 建立此連接可能需要幾分鐘時間。 Azure 開發空間還將要求管理員存取權限，以便修改開發電腦上的*主機*檔。

> [!IMPORTANT]
> Azure 開發空間建立到 AKS 群集的連接後，如果選擇 *"替換*連接模式"，則 AKS 群集中的其他服務可能無法正常運行，直到您在開發電腦中啟動服務。 您可以選擇*克隆*連接模式，以創建用於重定向的子開發空間，並避免對父空間造成任何中斷。 此外，如果服務具有開發電腦上不可用的依賴項，則可能需要修改應用程式或提供[其他配置](#additional-configuration)

Azure 開發空間在建立與 AKS 群集的連接後，將打開名為 *"AZDS 連接 - 自行車"* 的終端視窗。 此終端視窗具有從 AKS 群集配置的所有環境變數和 DNS 條目。 在此終端視窗中運行或使用 Visual Studio 代碼調試器的任何代碼都連接到 AKS 群集。

![終端](../media/how-to-connect/connect-terminal.png)

此外，Azure 開發空間會創建一個名為 *"開發空間連接*其所有輸出"的視窗。

![輸出](../media/how-to-connect/connect-output.png)

Azure 開發空間還具有顯示連接狀態的狀態列項。

![狀態](../media/how-to-connect/connect-status.png)

驗證狀態列顯示*開發人員空間：連接到本地埠 3000 上的 dev/自行車*。

### <a name="configure-your-application-on-your-development-computer"></a>在開發電腦上配置應用程式

打開*AZDS 連接 - 自行車*終端`npm install`視窗並運行 ：

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

按一下 *"調試"* 然後*打開配置*。 如果提示選擇環境，請選擇*Node.js*。這將創建一`.vscode/launch.json`個檔。 將該檔的內容替換為以下內容：

```json
{
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Launch via NPM",
            "runtimeExecutable": "npm",
            "runtimeArgs": [
                "run-script",
                "debug"
            ],
            "port": 9229
        }
    ]
}
```

打開[包.json](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json)並添加調試腳本：

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-computer"></a>在開發電腦上啟動應用程式

按一下左側的*調試*圖示，然後按一下頂部的 *"通過 NPM 通過啟動"旁邊的啟動*按鈕。

![通過 NPM 啟動](../media/how-to-connect/launch-npm.png)

應用程式將啟動，Azure 開發人員空間將重定向 AKS 群集和開發電腦之間的流量。 您將在*調試主控台*中看到類似于以下內容的消息：

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

通過按一下 Azure 開發人員空間狀態列並選擇應用程式的公共 URL 導航到*自行車共用 Web*服務。 您還可以從之前運行的命令`azds list-uris`中找到公共 URL。 如果在群集上不使用 Azure 開發空間，請使用正在使用的命名空間的應用程式的 IP 或 URL。 在上述範例中，*bikesharingweb* 服務的公用 URL 是 `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`。 選擇*奧雷利亞布裡格斯（客戶）* 作為使用者，然後選擇自行車出租。

### <a name="set-a-break-point"></a>設置中斷點

打開[伺服器.js，](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233)然後按一下第233行的某處，將游標放在那裡。 通過點擊*F9*或按一下 *"調試*"然後*切換中斷點來設置中斷點*。

通過打開公共 URL 導航到*自行車共用 Web*服務。 選擇*奧雷利亞布裡格斯（客戶）* 作為使用者，然後選擇自行車出租。 請注意，自行車的圖像未載入。 返回到視覺化工作室代碼並突出顯示觀察行 233。 您設置的中斷點已暫停第 233 行的服務。 若要讓服務繼續，請按 F5**，或依序按一下 [偵錯]** 和 [繼續]**。 返回到您的瀏覽器並驗證您看到自行車的預留位置圖像。

通過將游標放在第 233 行中`server.js`並擊到*F9*來移除中斷點。

### <a name="update-your-application"></a>更新您的應用程式

編輯`server.js`以刪除行 232 和 233：

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

該部分現在應如下所示：

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

保存更改，然後按一下 *"調試"* 然後*重新開機調試*。 刷新瀏覽器並驗證您是否不再看到自行車的預留位置圖像。

依序按一下 [偵錯]** 和 [停止偵錯]** 來停止偵錯工具。 按一下 Azure 開發空間狀態列以斷開與 AKS 群集的連接。

## <a name="additional-configuration"></a>其他設定

Azure 開發人員空間可以處理路由流量和複製環境變數，而無需任何其他配置。 如果需要下載安裝在 AKS 群集中容器的任何檔（如 ConfigMap 檔），則可以創建 將`azds-local.env`這些檔下載到開發電腦。

下面是一個示例`azds-local.env`：

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

## <a name="using-logging-and-diagnostics"></a>使用日誌記錄和診斷

將開發電腦連接到 AKS 群集後，日誌記錄輸出將寫入*開發空間連接*視窗。

![輸出](../media/how-to-connect/connect-output.png)

按一下 Azure 開發空間狀態列並選擇 *"顯示診斷資訊*"。 此命令在日誌記錄輸出中列印當前環境變數和 DNS 整個。

![帶診斷的輸出](../media/how-to-connect/connect-output-diagnostics.png)

此外，您還可以在`Azure Dev Spaces`[開發電腦的*TEMP*目錄中][azds-tmp-dir]找到目錄中的診斷日誌。

## <a name="next-steps"></a>後續步驟

瞭解如何在拉取請求合併到存儲庫的主分支之前，使用 Azure 開發空間和 GitHub 操作直接在 AKS 中測試從拉取請求中的更改。

> [!div class="nextstepaction"]
> [GitHub 操作& Azure 庫伯奈斯服務][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download