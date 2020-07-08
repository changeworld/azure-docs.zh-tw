---
title: 將 Visual Studio Code 和 Kubernetes 與本機處理序搭配使用 (預覽)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: 瞭解如何透過 Kubernetes 使用本機進程，將您的開發電腦連接到具有 Azure Dev Spaces 的 Kubernetes 叢集
keywords: 具有 Kubernetes、Azure Dev Spaces、Dev Spaces、Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、容器的本機進程
ms.openlocfilehash: 23a94528ffa4e9e412f472349ea26d1a14003616
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84316459"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-code-preview"></a>將 Visual Studio Code 和 Kubernetes 與本機處理序搭配使用 (預覽)

使用 Kubernetes 的本機程式可讓您在開發電腦上執行和偵錯工具代碼，同時繼續與您的應用程式或服務的其餘部分連線到您的 Kubernetes 叢集。 例如，如果您有一個大型的微服務架構，其中包含許多互相相依的服務與資料庫，則在開發電腦上複寫這些相依性可能會很棘手。 此外，在內部迴圈開發期間，針對每個程式碼變更建立程式碼並將其部署到您的 Kubernetes 叢集，可能會很慢、耗時，而且很容易與偵錯工具搭配使用。

透過 Kubernetes 的本機程式可避免在您的開發電腦與叢集之間直接建立連線，而必須建立程式碼並將其部署到您的叢集。 在進行偵錯工具時，將您的開發電腦連接到叢集，可讓您在完整應用程式的內容中快速測試和開發服務，而不需要建立任何 Docker 或 Kubernetes 設定。

具有 Kubernetes 的本機進程會重新導向已連線的 Kubernetes 叢集與您的開發電腦之間的流量。 此流量重新導向可讓您的開發電腦上執行的程式碼與 Kubernetes 叢集中執行的服務進行通訊，就好像它們位於相同的 Kubernetes 叢集中一樣。 使用 Kubernetes 的本機進程也會提供一種方法，將環境變數和掛接的磁片區複寫至您的開發電腦中 Kubernetes 叢集中的 pod。 在您的開發電腦上提供環境變數和裝載的磁片區存取，可讓您快速處理常式代碼，而不需要手動複寫這些相依性。

在本指南中，您將瞭解如何搭配 Kubernetes 使用本機進程，以重新導向您的 Kubernetes 叢集與在開發電腦上執行的程式碼之間的流量。 本指南也提供在 Kubernetes 叢集上部署具有多個微服務之大型範例應用程式的腳本。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定][preview-terms]即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="before-you-begin"></a>開始之前

本指南使用[Azure Dev Spaces 自行車分享範例應用程式][bike-sharing-github]，示範如何將您的開發電腦連接到 Kubernetes 叢集。 如果您已經在 Kubernetes 叢集上執行自己的應用程式，您仍然可以遵循下列步驟，並使用您自己的服務名稱。

### <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/free)。
* [已安裝 Azure CLI][azure-cli]。
* [Visual Studio Code][vs-code]在 MacOS 或 Windows 10 上執行。
* Visual Studio Code 中安裝的[Azure Dev Spaces][azds-vs-code]擴充功能版本2.0.220200601 或更高。
* [已安裝 AZURE DEV SPACES CLI][azds-cli]。

## <a name="create-a-kubernetes-cluster"></a>建立 Kubernetes 叢集

在[支援的區域][supported-regions]中建立 AKS 叢集。 下列命令會建立名為 MyResourceGroup 的資源群組與名為 MyAKS 的 AKS 叢集。

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --location eastus \
    --node-count 3 \
    --generate-ssh-keys
```

## <a name="install-the-sample-application"></a>安裝範例應用程式

使用所提供的腳本，在您的叢集上安裝範例應用程式。 您可以在開發電腦上執行此腳本，或使用[Azure Cloud Shell][azure-cloud-shell]。

> [!IMPORTANT]
> 您必須擁有叢集的*擁有*者或*參與者*存取權，才能執行腳本。

```azurecli-interactive
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/
chmod +x ./local-process-quickstart.sh
./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
```

流覽至執行叢集的範例應用程式，方法是開啟其 [公用 URL]，這會顯示在安裝腳本的輸出中。

```console
$ ./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
Defaulting Dev spaces repository root to current directory : ~/dev-spaces
Setting the Kube context
...
To try out the app, open the url:
dev.bikesharingweb.EXTERNAL_IP.nip.io
```

在上述範例中，公用 URL 是 `dev.bikesharingweb.EXTERNAL_IP.nip.io` 。

## <a name="connect-to-your-cluster-and-debug-a-service"></a>連接到您的叢集並對服務進行偵錯工具

在您的開發電腦上，下載並設定 Kubernetes CLI，使用[az aks get-認證][az-aks-get-credentials]連接到您的 Kubernetes 叢集。

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

從 Visual Studio Code 中的[Azure Dev Spaces 自行車分享範例應用程式][bike-sharing-github]開啟*開發人員空間/範例/BikeSharingApp/自行車*。 開啟 [Azure Kubernetes Service] 延伸模組，然後在*MyAKS*叢集中選取*dev*命名空間。

![選取命名空間](../media/local-process-kubernetes-vs-code/select-namespace.png)

使用 `npm install` 命令來安裝應用程式的相依性。

```console
npm install
```

選取左側的 [*調試*程式] 圖示，然後選取頂端的 [*使用 Kubernetes （預覽）的本機進程*]。

![選擇使用 Kubernetes 的本機進程](../media/local-process-kubernetes-vs-code/choose-local-process.png)

按一下 [*使用 Kubernetes （預覽）的本機進程*] 旁的 [開始] 按鈕。 第一次執行此啟動設定時，系統會提示您設定您想要取代的服務、要從開發電腦轉寄的埠，以及要使用的啟動工作。

選擇 [*自行車*] 服務。

![選擇服務](../media/local-process-kubernetes-vs-code/choose-service.png)

Kubernetes 叢集中的所有流量都會重新導向至*您的開發*電腦中執行的應用程式版本。 具有 Kubernetes 的本機進程也會將應用程式的所有輸出流量，路由傳送回您的 Kubernetes 叢集。

> [!IMPORTANT]
> 您只能重新導向具有單一 pod 的服務。

選取您的服務之後，系統會提示您輸入本機應用程式的 TCP 埠。 在此範例中，請輸入*3000*。

![連接選擇埠](../media/local-process-kubernetes-vs-code/choose-port.png)

選擇 [透過*NPM 啟動*] 作為啟動工作。

![Connect 選擇啟動工作](../media/local-process-kubernetes-vs-code/choose-launch.png)

> [!NOTE]
> 系統會提示您允許*KubernetesDNSManager*以提高許可權執行並修改您的主機檔案。

當狀態列變成橙色，而 Dev Spaces 延伸模組顯示已連接時，您的開發電腦就會連接。

![已連接的開發電腦](../media/local-process-kubernetes-vs-code/development-computer-connected.png)

> [!NOTE]
> 在 subesquent 啟動時，系統不會提示您輸入服務名稱、埠或啟動工作。 這些值會儲存在. *vscode/tasks.json*。

在您的開發電腦連線之後，流量就會開始重新導向至您的開發電腦，以取得您要取代的服務。

## <a name="set-a-break-point"></a>設定中斷點

開啟[server.js][server-js-breakpoint] ，然後按一下第233行的某處，將游標放在該處。 藉由叫用*F9*來設定中斷點，或按一下 [*執行*]，然後*切換中斷點*。

藉由開啟公用 URL，流覽至範例應用程式。 選取 [ *Aurelia Briggs （customer）* ] 作為使用者，然後選取要出租的自行車。 請注意，自行車的影像不會載入。 回到 Visual Studio Code，觀察行233已反白顯示。 您設定的中斷點已在第233行暫停服務。 若要繼續服務，請按*F5*或按一下 [*執行*]，然後*繼續*。 返回您的瀏覽器，並確認您看到自行車的預留位置影像。

將游標放在第233行，然後叫用 `server.js` *F9*，即可移除中斷點。

### <a name="update-your-application"></a>更新您的應用程式

編輯 `server.js` 以移除234和235行：

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

區段現在看起來應該像這樣：

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

儲存您的變更，然後按一下 [*執行*]，然後*重新開機調試*程式。 重新連線之後，請重新整理您的瀏覽器，並確認您不再看到自行車的預留位置影像。

按一下 [*執行*]，然後*停止調試*程式。

> [!NOTE]
> 根據預設，停止偵錯工具工作也會中斷您的開發電腦與 Kubernetes 叢集的連線。 您可以變更此行為，方法是在 Visual Studio Code 設定中，*使用 Kubernetes： [在偵錯工具後中斷連線] 來搜尋本機進程*，並移除 [*當偵錯工具結束時自動中斷*連線] 旁的複選 更新此設定之後，當您停止並開始進行偵錯工具時，您的開發電腦仍會保持連線。 若要中斷您的開發電腦與叢集的連線，請按一下狀態列上的 [Azure Dev Spaces] 延伸模組，然後選擇 [*中斷連接目前的會話]*。
>
> 如果 Visual Studio Code 突然結束叢集的連線或終止，則您要重新導向的服務可能不會還原為其原始狀態，而是使用 Kubernetes 與本機進程連接。 若要修正此問題，請參閱[疑難排解指南][troubleshooting]。

## <a name="using-logging-and-diagnostics"></a>使用記錄和診斷

在您的開發電腦連線到 Kubernetes 叢集之後，記錄輸出會寫入至*Dev Spaces*視窗。

![輸出](../media/local-process-kubernetes-vs-code/output.png)

按一下 [Azure Dev Spaces] 狀態列，然後選擇 [*顯示連接診斷資訊*]。 此命令會將目前的環境變數和 DNS entires 列印到記錄輸出中。

![診斷輸出](../media/local-process-kubernetes-vs-code/output-diagnostics.png)

此外，您可以在 `Azure Dev Spaces` [開發電腦的*TEMP*目錄][azds-tmp-dir]中，找到目錄中的診斷記錄。

## <a name="remove-the-sample-application-from-your-cluster"></a>從您的叢集中移除範例應用程式

使用提供的腳本從您的叢集中移除範例應用程式。

```azurecli-interactive
./local-process-quickstart.sh -c -g MyResourceGroup -n MyAKS
```

## <a name="next-steps"></a>後續步驟

瞭解如何使用 Azure Dev Spaces 和 GitHub 動作，直接在 AKS 中測試提取要求的變更，然後將提取要求合併至存放庫的主要分支。

> [!div class="nextstepaction"]
> [GitHub 動作 & Azure Kubernetes Service][gh-actions]

[azds-cli]: install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-cloud-shell]: ../../cloud-shell/overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[server-js-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[vs-code]: https://code.visualstudio.com/download