---
title: 在 Azure 中的 Service Fabric 上快速建立 .NET 應用程式
description: 在本快速入門中，您會使用 Service Fabric 可靠服務範例應用程式建立適用於 Azure 的 .NET 應用程式。
author: mikkelhegn
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: mikhegn
ms.custom: mvc, devcenter, vs-azure
ms.openlocfilehash: e3d984fee75dcdb8d4e14e7b454e74a3f7c629f2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "75730134"
---
# <a name="quickstart-deploy-a-net-reliable-services-application-to-service-fabric"></a>快速入門：將 .NET 可靠服務應用程式部署至 Service Fabric

Azure Service Fabric 是一個分散式系統平台，可讓您部署及管理可調整和可信賴的微服務與容器。

本快速入門示範如何將您的第一個 .NET 應用程式部署到 Service Fabric。 當您完成時，您會有一個投票應用程式，其 ASP.NET Core Web 前端會將投票結果儲存在叢集中具狀態的後端服務。

![應用程式螢幕擷取畫面](./media/service-fabric-quickstart-dotnet/application-screenshot.png)

使用此應用程式，您將了解如何：

* 建立使用 .NET 和 Service Fabric 的應用程式
* 使用 ASP.NET Core 作為 Web 前端
* 將應用程式資料儲存在具狀態服務中
* 在本機偵錯您的應用程式
* 跨多個節點相應放大應用程式
* 執行輪流應用程式升級

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門：

1. [安裝 Visual Studio 2019](https://www.visualstudio.com/)，其中包含 **Azure 開發**以及 **ASP.NET 和 Web 開發**工作負載。
2. [安裝 Git](https://git-scm.com/)
3. [安裝 Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK)
4. 執行下列命令以將 Visual Studio 部署到本機 Service Fabric 叢集：

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
   ```
    
## <a name="build-a-cluster"></a>建置叢集

在您安裝執行階段、SDK、Visual Studio 工具、Docker，且 Docker 開始執行後，建立包含五個叢集的本機開發叢集。

> [!Note]
> 當您建立叢集時讓 Docker 執行的原因是讓建立的叢集啟用容器功能。 如果未執行 Docker，您必須重建叢集才能啟用容器功能。
> 雖然此特殊快速入門沒必要這麼做，但是當您建立叢集時讓 Docker 執行的指示已納入為最佳做法。
> 請開啟終端機視窗並執行 `docker ps`，然後查看是否有錯誤發生，以測試該 Docker。 如果回應未指出錯誤，表示 Docker 正在執行中，且您已可建置叢集。
>
> [設定適用於容器的 Windows 10 或 Windows Server](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/set-up-environment?tabs=Windows-10-Client)

1. 以系統管理員身分開啟提升權限的新 PowerShell 視窗。
2. 執行下列 PowerShell 命令，以建立開發叢集：

   ```powershell
   . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
   ```
3. 執行下列命令以啟動本機叢集管理員工具：

   ```powershell
   . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
   ```

>[!NOTE]
> 在本快速入門中，範例應用程式使用的功能在 Windows 7 中無法使用。
>

## <a name="download-the-sample"></a>下載範例

在命令視窗中執行下列命令，將範例應用程式存放庫複製到本機電腦。

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="run-the-application-locally"></a>在本機執行應用程式

以滑鼠右鍵按一下 [開始] 功能表中的 Visual Studio 圖示，然後選擇 [以系統管理員身分執行]  。 若要將偵錯工具附加至您的服務，您需要以系統管理員身分執行 Visual Studio。

從您複製的存放庫開啟 **Voting.sln** Visual Studio 方案。

根據預設，投票應用程式會在連接埠 8080 上接聽。  應用程式連接埠設定於 */VotingWeb/PackageRoot/ServiceManifest.xml* 檔案中。  更新 [端點]  元素的 [連接埠]  屬性，即可變更應用程式連接埠。  若要在本機上部署並執行應用程式，則必須在您的電腦上開啟此應用程式連接埠並讓連接埠可供使用。  如果您變更了應用程式連接埠，請以新的應用程式連接埠值取代本篇文章中所有的 "8080"。

若要部署應用程式，請按 **F5**。

> [!NOTE]
> 在 Visual Studio 輸出視窗中，您會看到如下的訊息：「應用程式 URL 未設定或不是 HTTP/HTTPS URL，所以瀏覽器不會開啟應用程式。」  此訊息不表示發生錯誤，而是代表瀏覽器將無法自動啟動。

部署完成之後，啟動瀏覽器並開啟 `http://localhost:8080` 以檢視應用程式的 Web 前端。

![應用程式前端](./media/service-fabric-quickstart-dotnet/application-screenshot-new.png)

您現在可以新增一組投票選項，並開始進行投票。 應用程式會執行並將所有資料儲存在 Service Fabric 叢集中，而不需要個別資料庫。

## <a name="walk-through-the-voting-sample-application"></a>逐步解說投票範例應用程式

投票應用程式包含兩個服務：

* Web 前端服務 (VotingWeb) 是 ASP.NET Core Web 前端服務，可作為網頁，並公開 Web API 來與後端服務通訊。
* 後端服務 (VotingData) 是 ASP.NET Core Web 服務，會公開 API 來將投票結果儲存在磁碟上所保存的可靠字典中。

![應用程式圖表](./media/service-fabric-quickstart-dotnet/application-diagram.png)

當您在應用程式中投票時，會發生下列事件：

1. JavaScript 會將投票要求當做 HTTP PUT 要求，傳送至 Web 前端服務中的 Web API。

2. Web 前端服務使用 Proxy 來尋找 HTTP PUT 要求，並將其轉送至後端服務。

3. 後端服務會接受傳入要求，並將更新的結果儲存在可靠的字典中，以複寫至叢集中的多個節點並保存在磁碟上。 應用程式的所有資料都會儲存在叢集中，因此不需要資料庫。

## <a name="debug-in-visual-studio"></a>在 Visual Studio 中偵錯

應用程式應可正常執行，但是您可以使用偵錯工具查看應用程式主要組件的運作情形。 在 Visual Studio 中偵錯應用程式時，您會使用本機 Service Fabric 開發叢集。 您可以根據自己的情況調整偵錯體驗。 在這個應用程式中，我們會使用可靠的字典，將資料儲存在後端服務中。 當您停止偵錯工具時，Visual Studio 預設會移除應用程式。 移除應用程式也會導致移除後端服務中的資料。 若要保存偵錯工作階段之間的資料，您可以在 Visual Studio 中，將 [應用程式偵錯模式]  當做 [投票]  專案上的屬性來變更。

若要查看對程式碼的影響，請完成下列步驟：

1. 開啟 **/VotingWeb/Controllers/VotesController.cs** 檔案，並在 Web API 的 **Put** 方法 (第 69 行) 中設定中斷點 - 您可以在 Visual Studio 的方案總管中搜尋此檔案。

2. 開啟 **/VotingData/Controllers/VoteDataController.cs** 檔案，並在這個 Web API 的 **Put** 方法 (第 54 行) 中設定中斷點。

3. 返回到瀏覽器，並按一下投票選項或新增投票選項。 您到達 Web 前端之 API 控制器的第一個中斷點。
   * 瀏覽器中的 JavaScript 會在此步驟中，將要求傳送至前端服務中的 Web API 控制器。

     ![新增投票前端服務](./media/service-fabric-quickstart-dotnet/addvote-frontend.png)

   * 首先，請針對後端服務 **(1)** 建構 ReverseProxy 的 URL。
   * 接著，將 HTTP PUT 要求傳送至 ReverseProxy **(2)** 。
   * 最後，將後端服務的回應傳回給用戶端 **(3)** 。

4. 按 **F5** 繼續
   - 如果瀏覽器出現提示，請將偵錯模式的讀取和執行權限授與 ServiceFabricAllowedUsers 群組。
   - 您現在位於後端服務的中斷點。

     ![新增投票後端服務](./media/service-fabric-quickstart-dotnet/addvote-backend.png)

   - 在方法的第一行 **(1)** 中，`StateManager` 會取得或新增名為 `counts` 的可靠字典。
   - 與可靠字典中的值進行的所有互動，都需要交易，這會使用陳述式 **(2)** 建立該交易。
   - 在交易中，更新投票選項的相關索引鍵值，然後認可作業 **(3)** 。 一旦傳回認可方法，字典中的資料會更新並複寫至叢集中的其他節點。 資料現在會安全地儲存在叢集中，而且後端服務可以容錯移轉到仍有可用資料的其他節點。
5. 按 **F5** 繼續

若要停止偵錯工作階段，請按 **Shift+F5**。

## <a name="perform-a-rolling-application-upgrade"></a>執行輪流應用程式升級

將新的更新部署到應用程式時，Service Fabric 會以安全的方式來推出更新。 輪流升級可讓您在不需要停機的情況下進行升級，並在發生錯誤時自動復原。

若要升級應用程式，請執行下列動作：

1. 在 Visual Studio 中開啟 **/VotingWeb/Views/Home/Index.cshtml** 檔案。
2. 新增或更新文字以變更頁面上的標題。 例如，將標題變更為「Service Fabric 投票範例第 2 版」。
3. 儲存檔案。
4. 以滑鼠右鍵按一下方案總管中的 [投票]  ，並選擇 [發行]  。 [發行] 對話方塊隨即出現。
5. 按一下 [資訊清單版本]  按鈕以變更服務和應用程式的版本。
6. 例如，將 [VotingWebPkg]  下的 [Code]  元素版本變更為 "2.0.0"，然後按一下 [儲存]  。

    ![變更版本對話方塊](./media/service-fabric-quickstart-dotnet/change-version.png)
7. 在 [發行 Service Fabric 應用程式]  對話方塊中，核取 [升級應用程式]  核取方塊。
8.  將 [目標設定檔]  變更為 **PublishProfiles\Local.5Node.xml**，並確定 [連線端點]  已設定為 [本機叢集]  。 
9. 選取 [升級應用程式]  。

    ![發行對話方塊的升級設定](./media/service-fabric-quickstart-dotnet/upgrade-app.png)

10. 按一下 [發佈]  。

    執行升級期間，您仍然可以使用應用程式。 由於您有兩個服務執行個體正在叢集中執行，因此其中一些要求可能會取得應用程式的升級版本，而其他要求可能仍然會取得舊版。

11. 開啟您的瀏覽器，然後瀏覽至連接埠 19080 上的叢集位址。 例如： `http://localhost:19080/` 。
12. 按一下樹狀檢視中的 [應用程式]  節點，然後按一下右窗格中的 [正在升級]  。 您會看到在叢集的升級網域中輪流升級的情況，請確保每個網域的狀況良好，再繼續下一步。 當網域的健康狀態通過驗證時，進度列中的升級網域即會呈現綠色。
    ![Service Fabric Explorer 中的升級檢視](./media/service-fabric-quickstart-dotnet/upgrading.png)

    Service Fabric 會等候兩分鐘，再升級叢集中每個節點上的服務，以確保安全升級。 整個更新預計需要約八分鐘。

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

* 建立使用 .NET 和 Service Fabric 的應用程式
* 使用 ASP.NET Core 作為 Web 前端
* 將應用程式資料儲存在具狀態服務中
* 在本機偵錯您的應用程式
* 跨多個節點相應放大應用程式
* 執行輪流應用程式升級

若要深入了解 Service Fabric 和 .NET，請參閱下列教學課程：
> [!div class="nextstepaction"]
> [Service Fabric 上的 .NET 應用程式](service-fabric-tutorial-create-dotnet-app.md)
