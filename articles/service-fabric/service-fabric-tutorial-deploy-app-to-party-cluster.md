---
title: 將 Service Fabric 應用程式部署至 Azure 中的叢集
description: 了解如何從 Visual Studio 將現有的應用程式部署至新建立的 Azure Service Fabric 叢集。
author: athinanthny
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: 9951610732cbb1c5884a7b7e830033f427db0ab1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75646002"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>教學課程：將 Service Fabric 應用程式部署到 Azure 中的叢集

本教學課程是一個系列的第二部分。 其中說明如何將 Azure Service Fabric 應用程式部署至 Azure 中的新叢集。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 建立叢集。
> * 使用 Visual Studio 將應用程式部署至遠端叢集。

在本教學課程系列中，您將了解如何：
> [!div class="checklist"]
> * [建置 .NET Service Fabric 應用程式](service-fabric-tutorial-create-dotnet-app.md)。
> * 將應用程式部署至遠端叢集。
> * [將 HTTPS 端點新增至 ASP.NET Core 前端服務](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)。
> * [使用 Azure Pipelines 設定 CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)。
> * [設定應用程式的監視和診斷](service-fabric-tutorial-monitoring-aspnet.md)。

## <a name="prerequisites"></a>Prerequisites

開始進行本教學課程之前：

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [安裝 Visual Studio 2019](https://www.visualstudio.com/)，並安裝 **Azure 開發**以及 **ASP.NET 和 Web 開發**工作負載。
* [安裝 Service Fabric SDK](service-fabric-get-started.md)。

> [!NOTE]
> 免費帳戶可能不符合建立虛擬機器的需求。 這將使您無法完成本教學課程。 此外，在與叢集相關聯的金鑰保存庫上建立憑證時，非工作或非學校帳戶可能會發生權限問題。 如果您遇到與憑證建立相關的錯誤，請改為使用入口網站來建立叢集。 

## <a name="download-the-voting-sample-application"></a>下載投票應用程式範例

如果您未在[本教學課程系列的第一部分](service-fabric-tutorial-create-dotnet-app.md)中建置投票範例應用程式，您可以在此下載。 在命令視窗中執行下列程式碼，將範例應用程式存放庫複製到本機電腦。

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

以系統管理員身分在 Visual Studio 中開啟應用程式，並建置應用程式。

## <a name="create-a-cluster"></a>建立叢集

應用程式現在已準備就緒，您可以建立 Service Fabric 叢集，然後將應用程式部署至該叢集。 [Service Fabric 叢集](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-anywhere)是一組由網路連接的虛擬或實體機器，可用來將您的微服務部署到其中並進行管理。

在本教學課程中，您將在 Visual Studio IDE 中建立新的三節點測試叢集，然後將應用程式發佈至該叢集。 請參閱[建立和管理叢集教學課程](service-fabric-tutorial-create-vnet-and-windows-cluster.md)，以取得關於建立生產叢集的資訊。 您也可以將應用程式部署至您先前在 [Azure 入口網站](https://portal.azure.com)中使用 [PowerShel](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) 或 [Azure CLI](./scripts/cli-create-cluster.md) 指令碼，或是從 [Azure Resource Manager 範本](service-fabric-tutorial-create-vnet-and-windows-cluster.md)建立的現有叢集。

> [!NOTE]
> 投票應用程式和許多其他應用程式都會使用 Service Fabric 反向 Proxy 進行服務之間的通訊。 從 Visual Studio 和建立的叢集依預設會啟用反向 Proxy。 如果部署至現有的叢集，您就必須[啟用叢集中的反向 Proxy](service-fabric-reverseproxy-setup.md)，投票應用程式才可運作。


### <a name="find-the-votingweb-service-endpoint"></a>尋找 VotingWeb 服務端點

投票應用程式的前端 Web 服務會接聽特定連接埠 (如果您遵循[本教學課程系列的第一部分](service-fabric-tutorial-create-dotnet-app.md)中的步驟，則為 8080)。 當應用程式部署至 Azure 中的叢集時，叢集和應用程式都會在 Azure 負載平衡器後方執行。 應用程式連接埠必須使用規則在 Azure 負載平衡器中開啟。 此規則會透過負載平衡器將輸入流量傳送至 Web 服務。 您可以在 **VotingWeb/PackageRoot/ServiceManifest.xml** 檔案的 **Endpoint** 元素中尋找連接埠。 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

請記下服務端點，稍後的步驟將會用到這項資料。  如果您要部署至現有的叢集，請使用 [PowerShell 指令碼](./scripts/service-fabric-powershell-open-port-in-load-balancer.md)，或在 [Azure 入口網站](https://portal.azure.com)中透過此叢集的負載平衡器，在 Azure 負載平衡器中建立負載平衡規則和探查，以開啟此連接埠。

### <a name="create-a-test-cluster-in-azure"></a>在 Azure 中建立測試叢集
在 [方案總管] 中，以滑鼠右鍵按一下 [投票]  並選取 [發佈]  。

在 [連線端點]  中，選取 [建立新的叢集]  。  如果您要部署至現有的叢集，請從清單中選取叢集端點。  [建立 Service Fabric 叢集] 對話方塊隨即開啟。

在 [叢集]  索引標籤中輸入 [叢集名稱]  (例如 "mytestcluster")、選取您的訂用帳戶、選取叢集的區域 (例如美國中南部)、輸入叢集節點數目 (建議測試叢集使用三個節點)，然後輸入資源群組 (例如 "mytestclustergroup")。 按 [下一步]  。

![建立叢集](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

在 [憑證]  索引標籤中，輸入叢集憑證的密碼和輸出路徑。 自我簽署的憑證會以 PFX 檔案的形式建立，並儲存至指定的輸出路徑。  此憑證可用來確保節點對節點和用戶端對節點的安全性。  請勿對生產叢集使用自我簽署的憑證。  Visual Studio 會使用此憑證對叢集進行驗證，以及部署應用程式。 請選取 [匯入憑證]  ，將 PFX 安裝在電腦的 CurrentUser\My certificate 存放區中。  按 [下一步]  。

![建立叢集](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

在 [VM 詳細資料]  索引標籤中，輸入叢集管理員帳戶的 [使用者名稱]  和 [密碼]  。  選取叢集節點的 [虛擬機器映像]  和每個叢集節點的 [虛擬機器大小]  。  按一下 [進階]  索引標籤。

![建立叢集](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

在 [連接埠]  中，輸入先前步驟中的 VotingWeb 服務端點 (例如 8080)。  叢集建立後，這些應用程式連接埠即會在 Azure 負載平衡器中開啟，以將流量轉送至叢集。  請按一下 [建立]  以建立叢集，這需要幾分鐘的時間。

![建立叢集](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>將應用程式發佈至叢集

在新叢集準備就緒後，您即可直接從 Visual Studio 部署投票應用程式。

在 [方案總管] 中，以滑鼠右鍵按一下 [投票]  並選取 [發佈]  。 [發佈]  對話方塊隨即出現。

在 [連線端點]  中，為您在先前的步驟中建立的叢集選取端點。  例如 "mytestcluster.southcentral.cloudapp.azure.com:19000"。 如果您選取 [進階連線參數]  ，憑證資訊應該會自動填入。  
![發佈 Service Fabric 應用程式](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

選取 [發佈]  。

應用程式部署後，請開啟瀏覽器，並輸入叢集位址和尾隨的 **:8080**。 或者，輸入其他已設定的連接埠。 例如 `http://mytestcluster.southcentral.cloudapp.azure.com:8080`。 您應該會看到應用程式在 Azure 的叢集中執行。 在投票網頁中，嘗試新增和刪除投票選項，並投票表決一或多個選項。

![Service Fabric 投票範例](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>後續步驟
在教學課程的這個部分中，您已了解如何：

> [!div class="checklist"]
> * 建立叢集。
> * 使用 Visual Studio 將應用程式部署至遠端叢集。

前進到下一個教學課程：
> [!div class="nextstepaction"]
> [啟用 HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
