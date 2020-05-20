---
title: 教學課程 - 部署 Service Fabric Mesh 應用程式
description: 了解如何使用 Visual Studio 發佈有 ASP.NET Core 網站可與後端 Web 服務進行通訊的 Azure Service Fabric Mesh 應用程式。
author: dkkapur
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: d2bb37252bc93c982dbc090a0c3f20aef842325f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75351852"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-application"></a>教學課程：部署 Service Fabric Mesh 應用程式

本教學課程是系列中的第三部分，說明如何直接從 Visual Studio 發佈 Azure Service Fabric Mesh Web 應用程式。

在本教學課程中，您將了解如何：
> [!div class="checklist"]
> * 使用 Visual Studio 將應用程式發佈至 Azure。
> * 檢查應用程式部署狀態
> * 查看目前已部署至您的訂用帳戶的所有應用程式

在本教學課程系列中，您將了解如何：
> [!div class="checklist"]
> * [在 Visual Studio 中建立 Service Fabric Mesh 應用程式](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [偵錯在本機開發叢集中執行的 Service Fabric Mesh 應用程式](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * 部署 Service Fabric Mesh 應用程式
> * [升級 Service Fabric Mesh 應用程式](service-fabric-mesh-tutorial-upgrade.md)
> * [清除 Service Fabric Mesh 資源](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Prerequisites

開始進行本教學課程之前：

* 如果您沒有 Azure 訂用帳戶，您可以在開始前[建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 確定您已[設定開發環境](service-fabric-mesh-howto-setup-developer-environment-sdk.md)，包括安裝 Service Fabric 執行階段、SDK、Docker 和 Visual Studio 2017。

## <a name="download-the-to-do-sample-application"></a>下載待辦事項範例應用程式

如果您未在[本教學課程系列的第二部分](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)中建置待辦事項範例應用程式，您可以在此時下載。 在命令視窗中執行下列命令，將範例應用程式存放庫複製到本機電腦。

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

該應用程式位於 `src\todolistapp` 目錄下。

## <a name="publish-to-azure"></a>發佈至 Azure

若要將您的 Service Fabric Mesh 專案發佈至 Azure，請在 Visual Studio 中以滑鼠右鍵按一下 **todolistapp**，然後選取 [發佈...]  。

接著，您將會看見 [發佈 Service Fabric 應用程式]  對話方塊。

![Visual Studio 的 Service Fabric Mesh 發行對話方塊](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

選取您的 Azure 帳戶和訂用帳戶。 選擇 [位置]  。 本文使用**美國東部**。

在 [資源群組] 下方，選取 [\<建立新的資源群組...>]。 對話方塊隨即出現，而您將在其中建立新的資源群組。 本文使用**美國東部**的位置，並將群組命名為 **sfmeshTutorial1RG** (如果您的組織有多位人員使用同一個訂用帳戶，請選擇唯一的群組名稱)。  按 [建立]  以建立資源群組並返回發佈對話方塊。

![Visual Studio 的 Service Fabric Mesh 新增資源群組對話方塊](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

返回 [發佈 Service Fabric 應用程式] 對話方塊，在 [Azure Container Registry] 下方，選取 [\<建立新的容器登錄...>]。 在 [建立容器登錄]  對話方塊中，請針對**容器登錄名稱**使用唯一的名稱。 指定**位置** (本教學課程使用**美國東部**)。 從下拉式清單中選取您在上一個步驟中建立的**資源群組**，例如 **sfmeshTutorial1RG**。 將 **SKU** 設定為**基本**，然後按 [建立]  ，以建立私人 Azure 容器登錄，並回到 [發佈] 對話方塊。

![Visual Studio 的 Service Fabric Mesh 新增容器登錄對話方塊](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

如果出現資源提供者尚未在您的訂用帳戶上註冊的錯誤，則您可加以註冊。 請先查看資源提供者是否可供您的訂用帳戶使用：

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

如果容器登錄提供者 (`Microsoft.ContainerRegistry`) 可供使用，請從 PowerShell 來加以註冊：

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

在發佈對話方塊中按 [發佈]  按鈕，將您的 Service Fabric 應用程式部署至 Azure。

當您首次發行到 Azure 時，會將 Dcker 映像推送到 Azure Container Registry (ACR)，所需時間取決於映像大小。 後續發行相同專案的速度將會加快。 您可以在 Visual Studio 的 [輸出] 視窗中選取 [Service Fabric 工具]，藉以監視部署的進度。 一旦部署完成之後，**Service Fabric 工具**輸出將會以 URL 形式來顯示應用程式的 IP 位址和連接埠。

```
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

開啟網頁瀏覽器，並瀏覽至 URL 以檢視在 Azure 中執行的網站。

## <a name="set-up-service-fabric-mesh-cli"></a>設定 Service Fabric Mesh CLI

您可以使用 Azure Cloud Shell 或安裝在本機的 Azure CLI 來完成其餘步驟。 請依照下列[指示](service-fabric-mesh-howto-setup-cli.md)安裝 Azure Service Fabric Mesh CLI 擴充功能模組。

## <a name="check-application-deployment-status"></a>檢查應用程式部署狀態

此時，您的應用程式已完成部署。 您可以使用 `app show` 命令來檢查其狀態。 

教學課程應用程式的應用程式名稱是 `todolistapp`。 請使用下列命令來收集應用程式的詳細資料：

```azurecli-interactive
az mesh app show --resource-group $rg --name todolistapp
```

## <a name="get-the-ip-address-of-your-deployment"></a>取得部署的 IP 位址

如果您想要取得應用程式的 IP 位址，請使用下列命令：
  
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>查看目前已部署至您的訂用帳戶的所有應用程式

您可以使用「應用程式清單」命令，取得您已部署至訂用帳戶的應用程式清單。

```azurecli-interactive
az mesh app list --output table
```

## <a name="next-steps"></a>後續步驟

在教學課程的這個部分中，您已了解如何：
> [!div class="checklist"]
> * 將應用程式發佈至 Azure。
> * 檢查應用程式部署狀態
> * 查看目前已部署至您的訂用帳戶的所有應用程式

前進到下一個教學課程：
> [!div class="nextstepaction"]
> [升級 Service Fabric Mesh 應用程式](service-fabric-mesh-tutorial-upgrade.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
