---
title: 在 Azure Dev Spaces 中使用 .NET Core 和 Visual Studio 進行小組開發
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: 本教學課程說明如何使用 Azure Dev Spaces 和 Visual Studio，在 Azure Kubernetes Service 中 .NET Core 應用程式上進行小組開發
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s '
ms.openlocfilehash: b4520ce35807fb022fa39ae9b00347a27e192380
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "78245058"
---
# <a name="team-development-using-net-core-and-visual-studio-with-azure-dev-spaces"></a>在 Azure Dev Spaces 中使用 .NET Core 和 Visual Studio 進行小組開發

在本教學課程中，您將了解一組開發人員如何使用 Dev Spaces 同時在相同的 Kubernetes 叢集中共同作業。

## <a name="learn-about-team-development"></a>了解小組開發

目前為止，您已如同您是應用程式唯一的開發人員一般，執行您的應用程式程式碼。 在本節中，您將會了解 Azure Dev Spaces 如何簡化小組開發：
* 藉由在在共用的開發人員空間中或相異的開發人員空間中工作 (依需求)，讓開發人員小組能夠在相同的環境中工作。
* 支援每位開發人員獨立在其程式碼中反覆執行，而不需要擔心會打斷其他開發人員。
* 在程式碼認可之前端對端測試程式碼，不需要建立模擬或模擬相依性。

### <a name="challenges-with-developing-microservices"></a>開發微服務的挑戰
您的範例應用程式目前並不複雜。 但是在實務開發中，隨著您新增更多服務以及開發小組成長，很快就會出現挑戰。

* 您的開發機器可能沒有足夠的資源可以一次執行每一個您所需的應用程式。
* 某些服務可能需要能夠公開連線。 例如，某個服務可能需要有端點來回應 Webhook。
* 如果您想要執行一部分的服務，就必須知道所有服務之間的完整相依性階層。 要判斷此階層並不容易，尤其是隨著服務的數目增加時。
* 某些開發人員會針對他們的許多服務相依性，進行模擬或製作模型。 這個方法會有幫助，但管理這些模型很快就會影響到開發成本。 再者，此方法會導致您的開發環境與生產環境出現差異，進而發生微小的 Bug。
* 其後果就是，執行任何類型的整合測試都變得非常困難。 整合測試實際上只能在認可後發生，這表示您稍後會在開發週期中看到問題。

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>在共用的開發人員空間中工作
透過 Azure Dev Spaces，您可以在 Azure 中設定「共用的  」開發人員空間。 每位開發人員可以只專注於他們自己的應用程式部分，並且可以在開發人員空間 (已經包含其他所有服務和他們的案例依賴的雲端資源) 中反覆地開發「認可前程式碼  」。 相依性會永遠保持最新狀態，且開發人員會以能反映生產環境的方式工作。

### <a name="work-in-your-own-space"></a>在您自己的空間工作
當您為服務開發程式碼時，且在您準備將其簽入之前，程式碼通常不會處於良好狀態。 您仍在反覆地使其成形、進行測試，並且實驗解決方案。 Azure Dev Spaces 提供**空間**的概念，可讓您獨立作業，而不需要擔心打斷您的小組成員。

## <a name="use-dev-spaces-for-team-development"></a>使用 Dev Spaces 進行小組開發
讓我們示範一下這些想法，具體的範例會使用 webfrontend   -> mywebapi  應用程式範例。 我們會設想一個案例，Scott 這位開發人員需要變更 mywebapi  服務，而且需要變更的也只有  該服務。 Scott 的更新過程並不需要變更 webfrontend  。

如果不  使用 Dev Spaces，Scott 有幾種方式可以用來開發和測試其更新，但不論是哪一種方式都不理想：
* 在本機執行所有元件，這種方式需要用到安裝了 Docker 的開發電腦，可能的選項是 MiniKube。
* 在 Kubernetes 叢集上的隔離命名空間中執行所有元件。 由於 webfrontend  不會變更，使用隔離命名空間會浪費叢集資源。
* 只執行 mywebapi  ，並手動發出 REST 呼叫來進行測試。 這種類型的測試不會測試完整的端對端流程。
* 將著重在開發用途的程式碼新增至 webfrontend  ，讓開發人員能夠傳送要求給 mywebapi  的不同執行個體。 新增此程式碼會讓 webfrontend  服務變得複雜。

### <a name="set-up-your-baseline"></a>設定基準
首先，我們必須部署服務基準。 此部署所代表的是「已知良好的最近配置」，因此您可以輕鬆地比較本機程式碼與所簽入版本的行為。 接著，我們會根據此基準建立子空間，以便在較大的應用程式內容中，測試我們對 mywebapi  所做的變更。

1. 複製 [Dev Spaces 應用程式範例](https://github.com/Azure/dev-spaces)：`git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. 簽出遠端分支 azds_updates  ：`git checkout -b azds_updates origin/azds_updates`
1. 關閉這兩項服務的任何 F5/偵錯工作階段，但讓專案在 Visual Studio 視窗中保持開啟。
1. 切換至含有 mywebapi  專案的 Visual Studio 視窗。
1. 以滑鼠右鍵按一下 [方案總管]  中的專案，然後選取 [屬性]  。
1. 選取位於左側的 [偵錯]  索引標籤，以顯示 [Azure 開發人員空間] 設定。
1. 選取 [變更]  來建立空間，以供您在對服務按下 F5 或 Ctrl+F5 時使用。
1. 在 [空間] 下拉式清單中，選取 [**建立新空間...\<]\>** 。
1. 確定父空間設定為 [**無\<]\>** ，然後輸入空間名稱 **dev**。 按一下 [確定]。
1. 按 Ctrl+F5，以執行 mywebapi  而不連結偵錯工具。
1. 切換至含有 webfrontend  專案的 Visual Studio 視窗，同時按 Ctrl+F5 加以執行。

> [!Note]
> 按 Ctrl + F5 顯示初始的網頁之後，有時候需要重新整理瀏覽器。

> [!TIP]
> 上述步驟會手動設定基準，但我們對小組的建議是，使用 CI/CD 讓基準自動與所認可的程式碼保持最新狀態。
>
> 請參閱我們的[使用 Azure DevOps 來設定 CI/CD 的指南](how-to/setup-cicd.md)，以建立類似下圖的工作流程。
>
> ![CI/CD 圖表範例](media/common/ci-cd-complex.png)

開啟公用 URL 並瀏覽至 Web 應用程式的任何人，都會叫用您先前撰寫、會透過兩項服務使用預設 dev  空間執行的程式碼路徑。 現在，假設您想要繼續開發 mywebapi  - 當您這麼做的時候，要如何才能不影響到其他使用開發人員空間的開發人員？ 為此，您應設定自己的空間。

### <a name="create-a-new-dev-space"></a>建立新的開發人員空間
在 Visual Studio 中，您可以建立對服務執行 F5 或 Ctrl+F5 時所將使用的額外空間。 您可以任意為空間命名，並且可靈活地賦予其意義 (例如 sprint4  或 demo  )。

若要建立新的空間，請執行下列動作：
1. 切換至含有 mywebapi  專案的 Visual Studio 視窗。
2. 以滑鼠右鍵按一下 [方案總管]  中的專案，然後選取 [屬性]  。
3. 選取位於左側的 [偵錯]  索引標籤，以顯示 [Azure 開發人員空間] 設定。
4. 從此處，您可以變更或建立您按 F5 或 Ctrl+F5 時所將使用的叢集和/或空間。 *請確定已選取您稍早建立的 Azure 開發人員空間*。
5. 在 [空間] 下拉式清單中，選取 [**建立新空間...\<]\>** 。

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. 在 [新增空間]  對話方塊中，將父空間設定為 **dev**，並輸入新空間的名稱。 您可以為新空間使用您的名稱 (例如 "scott")，讓您的同事知道您正在使用哪個空間。 按一下 [確定]  。

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. 您現在應該會看到專案屬性頁面上選取了您的 AKS 叢集和新的空間。

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>更新 *mywebapi* 的程式碼

1. 在 mywebapi  專案中，對檔案 `string Get(int id)` 中的 `Controllers/ValuesController.cs` 方法進行程式碼變更，如下所示：
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. 在這個更新的程式碼區塊中設定中斷點 (您可能已有先前設定的中斷點)。
3. 按 F5 以啟動 mywebapi  服務，這會在您的叢集中使用選取的空間啟動服務。 在此案例中，選取的空間是 scott  。

下圖有助於您了解不同空間的運作方式。 紫色路徑顯示透過 dev  空間的要求，這是未將空間附加至 URL 時所使用的預設路徑。 粉紅色路徑顯示透過 dev/scott  空間的要求。

![](media/common/Space-Routing.png)

Azure 開發人員空間的這個內建功能，可讓您在共用環境中端對端測試程式碼，而不需要每位開發人員在其空間中重新建立服務的完整堆疊。 使用此路由時，您必須在應用程式的程式碼中轉送傳播標頭，如本指南的上一個步驟所說明。

### <a name="test-code-running-in-the-_devscott_-space"></a>測試在 dev/scott  空間中執行的程式碼
若要搭配 webfrontend  來測試新版的 mywebapi  ，請在瀏覽器中開啟到 webfrontend  的公用存取點 URL (例如， http://dev.webfrontend.123456abcdef.eus.azds.io) )，然後移至 [關於] 頁面。 您應該會看到原始訊息「來自 webfrontend 的 Hello 和來自 mywebapi 的 Hello」。

現在，請將 "scott.s." 部分新增到 URL，讓它成為 http\://scott.s.dev.webfrontend.123456abcdef.eus.azds.io，並重新整理瀏覽器。 系統應會叫用您在 mywebapi  專案中設定的中斷點。 請按 f5 繼續作業，而您此時應會在瀏覽器中看到新的訊息「來自 webfrontend 和 mywebapi 的 Hello 現在顯示了新的內容。」 這是因為您已更新的程式碼在 mywebapi  中的路徑執行於 dev/scott  空間中。

一旦您擁有的 dev  空間一定會包含最新的變更，而且如果您應用程式的設計目的是要利用 DevSpace 的空間型路由 (如本教學課程這一節所述)，您應該就能輕鬆地發現，Dev Spaces 會對在較大的應用程式內容中測試新功能大有助益。 您不必在私用空間中部署所有  服務，而是可以建立衍生自 dev  的私用空間，並只「啟動」您實際使用的服務。 Dev Spaces 的路由基礎結構會處理其餘工作，其會盡可能使用所有可以從私用空間找到的服務，同時將其預設回到 dev  空間中所執行的最新版本。 更棒的是，多個  開發人員可以同時在自己的空間積極開發不同的服務，卻又不會干擾到彼此。

### <a name="well-done"></a>做得好！
您已經完成快速入門指南！ 您已了解如何︰

> [!div class="checklist"]
> * 使用 Azure 中受管理的 Kubernetes 叢集，設定 Azure Dev Spaces。
> * 在容器中反覆開發程式碼。
> * 獨立開發兩種不同的服務，並且使用 Kubernetes 的 DNS 服務探索來呼叫另一個服務。
> * 在小組環境中有效率地開發及測試您的程式碼。
> * 使用 Dev Spaces 來建立功能基準，以便輕鬆地在較大型微服務應用程式的內容中，測試隔離的變更

現在您已充分了解 Azure Dev Spaces，請[與小組成員共用您的開發人員空間](how-to/share-dev-spaces.md)，並協助他們了解共同作業有多麼容易。

## <a name="clean-up"></a>清除
若要完全刪除叢集上的 Azure Dev Spaces 執行個體，包括所有開發人員空間及其中正在執行的服務，請使用 `az aks remove-dev-spaces` 命令。 請記住此動作無法復原。 您可以在叢集上再次新增 Azure Dev Spaces 的支援，但會如同您再次開始執行。 將不會還原您的舊服務和空間。

下列範例列出您使用中訂用帳戶中的 Azure Dev Spaces 控制項，然後刪除資源群組 'myaks-rg' 中與 AKS 叢集 'myaks' 相關聯的 Azure Dev Spaces 控制項。

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
