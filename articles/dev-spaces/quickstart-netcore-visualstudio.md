---
title: 在 Kubernetes 上偵錯和逐一查看：Visual Studio 和 .NET Core
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: 本快速入門說明如何使用 Azure Dev Spaces 和 Visual Studio 對 Azure Kubernetes Service 上的 .NET Core 應用程式進行偵錯和快速反覆運算
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 1855fd201d7963fdf2f1b3fa292643908bbb05a0
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88079598"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>快速入門：在 Kubernetes 上偵錯和逐一查看：Visual Studio 和 .NET Core - Azure Dev Spaces

在本指南中，您將了解如何：

- 使用 Azure 中受管理的 Kubernetes 叢集，設定 Azure Dev Spaces。
- 使用 Visual Studio 在容器中反覆開發程式碼。
- 使用 Visual Studio 對叢集中執行的程式碼進行偵錯。

Azure Dev Spaces 也可讓您使用下列工具進行偵錯和逐一查看：
- [Java 和 Visual Studio Code](quickstart-java.md)
- [Node.js 和 Visual Studio Code](quickstart-nodejs.md)
- [.NET Core 和 Visual Studio Code](quickstart-netcore.md)

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/free)。
- 已安裝 Azure 開發工作負載的 Visual Studio 2019 (Windows 版)。 如果您尚未安裝 Visual Studio，請在[這裡](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)下載。
- [已安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-an-azure-kubernetes-service-cluster"></a>建立 Azure Kubernetes Service 叢集

您必須在[支援的區域][supported-regions]中建立 AKS 叢集。 下列命令會建立名為 MyResourceGroup 的資源群組與名為 MyAKS 的 AKS 叢集。

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>在 AKS 叢集上啟用 Azure Dev Spaces

使用 `use-dev-spaces` 命令在 AKS 叢集上啟用 Dev Spaces，並遵循提示來進行。 下列命令會在 MyResourceGroup 群組中的 MyAKS 叢集上啟用 Dev Spaces，並建立「預設」開發空間。

> [!NOTE]
> `use-dev-spaces` 命令也會安裝 Azure Dev Spaces CLI (如果尚未安裝)。 您無法在 Azure Cloud Shell 中安裝 Azure Dev Spaces CLI。

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="create-a-new-aspnet-web-app"></a>建立新的 ASP.NET Web 應用程式

1. 開啟 Visual Studio。
1. 建立新專案。
1. 選擇 [ASP.NET Core Web 應用程式]，然後按 [下一步]。
1. 將專案命名為 *webfrontend*，然後按一下 [建立]。
1. 出現提示時，選擇 [Web 應用程式 (Model-View-Controller)] 作為範本。
1. 選取頂端的 [.NET Core] 和 [ASP.NET Core 2.1]。
1. 按一下 [建立]。

## <a name="connect-your-project-to-your-dev-space"></a>將專案連線至您的開發人員空間

在您的專案中，從啟動設定下拉式清單中選取 [Azure Dev Spaces]，如下所示。

![反白顯示已選取 IIS Express 選項以及反白顯示 Azure Dev Spaces 選項的 Visual Studio UI 螢幕擷取畫面。](media/get-started-netcore-visualstudio/LaunchSettings.png)

在 [Azure Dev Spaces] 對話方塊中，選取您的 [訂用帳戶] 和 [Azure Kubernetes 叢集]。 讓 [空間] 保持設定為 [預設]，然後啟用 [可公開存取] 核取方塊。 按一下 [確定]。

![Azure Dev Spaces 對話方塊的螢幕擷取畫面。](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

此程序會將您的服務部署至具有可公開存取 URL 的「預設」開發空間。 如果您選擇尚未設定要搭配 Azure Dev Spaces 使用的叢集，您會看到訊息詢問您是否要設定它。 按一下 [確定]。

![新增 Azure 空間資源對話方塊的螢幕擷取畫面。](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

在「預設」開發人員空間中執行的服務所具有的公用 URL 會顯示在 [輸出] 視窗中：

```cmd
Starting warmup for project 'webfrontend'.
Waiting for namespace to be provisioned.
Using dev space 'default' with target 'MyAKS'
...
Successfully built 1234567890ab
Successfully tagged webfrontend:devspaces-11122233344455566
Built container image in 39s
Waiting for container...
36s

Service 'webfrontend' port 'http' is available at `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/`
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

在上述範例中，公用 URL 是 `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/`。 

選取 [偵錯]，然後**開始偵錯**。 幾秒鐘之後，您的服務就會啟動，而 Visual Studio 將會以服務的公用 URL 開啟瀏覽器。 如果瀏覽器未自動開啟，請在瀏覽器中流覽至您服務的公用 URL，並與在開發人員空間中執行的服務互動。

此程序可能已停用針對您服務的公用存取。 若要啟用公用存取，您可以更新[*values.yaml* 中的輸入值][ingress-update]。

## <a name="update-code"></a>更新程式碼

如果 Visual Studio 仍與您的開發人員空間保持連線，請按一下 [停止] 按鈕。 將 `Controllers/HomeController.cs` 中的第 20 行變更為：
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

儲存您的變更並選取 [偵錯]，然後選取 [開始偵錯]。 幾秒鐘之後，您的服務就會啟動，而 Visual Studio 將會以服務的公用 URL 開啟瀏覽器。 如果瀏覽器未自動開啟，請在瀏覽器中流覽服務的公用 URL，然後按一下 [關於]。 您會發現更新過的訊息跑出來。

Azure Dev Spaces 會以累加方式重新編譯現有容器中的程式碼，以提供更快的編輯/偵錯迴圈，而不是在每次進行程式碼編輯時重新建置及重新部署新的容器映像。

## <a name="setting-and-using-breakpoints-for-debugging"></a>設定和使用偵錯的中斷點

如果 Visual Studio 仍與您的開發人員空間保持連線，請按一下 [停止] 按鈕。 開啟 `Controllers/HomeController.cs`，然後在第 20 行的某處按一下來將游標放在該處。 若要設定中斷點，請按 F9，或依序按一下 [偵錯] 和 [切換中斷點]。 若要在您的開發人員空間中以偵錯模式啟動您的服務，請按 F5 或依序按一下 [偵錯] 和 [開始偵錯]。

在瀏覽器中開啟您的服務，並注意其中並未顯示任何訊息。 返回 Visual Studio，您會看到第 20 行已醒目提示。 您所設定的中斷點已讓服務在第 20 行暫停。 若要讓服務繼續，請按 F5，或依序按一下 [偵錯] 和 [繼續]。 返回您的瀏覽器，並注意現在會顯示訊息。

在連結了偵錯工具的 Kubernetes 中執行您的服務時，您可以完整地存取偵錯資訊，例如呼叫堆疊、區域變數和例外狀況資訊。

藉由將游標放在 `Controllers/HomeController.cs` 中的第 20 行上並按下 F9，即可移除中斷點。

## <a name="clean-up-your-azure-resources"></a>清除 Azure 資源

在 Azure 入口網站中瀏覽至您的資源群組，然後按一下 [刪除資源群組]。 或者，您也可以使用 [az aks delete](/cli/azure/aks#az-aks-delete) 命令：

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>後續步驟

深入了解 Azure Dev Spaces 的運作方式。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 如何運作](how-dev-spaces-works.md)

[ingress-update]: how-dev-spaces-works-up.md#how-running-your-code-is-configured
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
