---
title: 遷移以橋接至 Kubernetes
services: azure-dev-spaces
ms.date: 10/21/2020
ms.topic: conceptual
description: 描述從 Azure Dev Spaces 到 Bridge 到 Kubernetes 的遷移程式
keywords: Azure Dev Spaces、Dev Spaces、Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、容器、Bridge 到 Kubernetes
ms.openlocfilehash: 7a7642d986d8490c5d0dc3c413e658b21b010798
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895251"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>遷移以橋接至 Kubernetes

> [!IMPORTANT]
> Azure Dev Spaces 將于2023年10月31日淘汰。 客戶應該移至使用 Bridge Kubernetes，也就是用戶端開發人員工具。
>
> Azure Dev Spaces 的目的是要減輕使用者在 Kubernetes 上的開發。 Azure Dev Spaces 的方法中有一個重大的取捨，就是要對使用者造成額外的負擔，以瞭解 Docker 和 Kubernetes 設定，以及 Kubernetes 的部署概念。 經過一段時間之後，它也清楚了 Azure Dev Spaces 的方法無法有效地減少內部迴圈開發在 Kubernetes 上的速度。 橋接器至 Kubernetes 可有效地減少內部迴圈開發的速度，並避免使用者不必要的負擔。
>
> 核心任務保持不變：在較大的應用程式內容中建立開發、測試和偵測微服務程式代碼的最佳體驗。

[橋接器至 Kubernetes] 可提供更輕量的替代方案，讓您可以使用 Azure Dev Spaces 的許多開發案例。 橋接器至 Kubernetes 是一種僅限用戶端的體驗， [Visual Studio][vs]   和 [Visual Studio Code][vsc]使用擴充功能。  

Bridge 與 Kubernetes 可讓已建立的 Kubernetes 應用程式包含在本機開發工作站上執行的服務，協助您的開發體驗。 與開發人員空間不同的是，Bridge 與 Kubernetes 可讓您逐步執行建立 Docker 和 Kubernetes 設定的需求，以減少內部迴圈的複雜性，讓開發人員只專注于微服務程式代碼的商務邏輯。

[橋接器至 Kubernetes] 可協助您逐一查看在開發電腦上執行的程式碼，同時從您的 Kubernetes 環境取用相依性和現有的設定。 相反地，Azure Dev Spaces 會將您的微服務部署到 Kubernetes 環境中，然後才能從遠端偵測您的服務並逐一查看您的程式碼。

本文提供 Azure Dev Spaces 和 Bridge 與 Kubernetes 之間的比較，以及從 Azure Dev Spaces 遷移至 Kubernetes 的指示。

## <a name="development-approaches"></a>開發方法

![開發方法](media/migrate-to-btk/development-approaches.svg)

Azure Dev Spaces 協助 Kubernetes 開發人員使用直接在其 AKS 叢集中執行的程式碼，而不需要本機環境而不像部署的環境。 這種方法改進了開發的特定層面，但也引進了學習和維護其他概念（例如 Docker、Kubernetes 和 Helm）的必要條件，才能開始使用 Azure Dev Spaces。

Bridge 與 Kubernetes 可讓開發人員直接在其開發電腦上工作，同時與其叢集的其餘部分互動。 這種方法會利用直接在開發電腦上執行程式碼的熟悉度和速度，同時共用其叢集所提供的相依性和環境。 這種方法也會利用在 Kubernetes 中執行的精確度和規模調整。

## <a name="feature-comparison"></a>功能比較

Azure Dev Spaces 和 Bridge Kubernetes 有類似的功能，它們在許多方面也不同：

| 需求  | Azure Dev Spaces  | 橋接至 Kubernetes  |
|---------------|-------------------|--------------------------------|
| Azure Kubernetes Service | 在15個 Azure 區域中 | 任何 AKS 服務區域    |
| **安全性** |
| 叢集所需的安全性存取  | AKS 叢集參與者  | Kubernetes RBAC-部署更新   |
| 開發電腦上所需的安全性存取權  | N/A  | 本機系統管理員/sudo   |
| **可用性** |
| 獨立于 Kubernetes 和 Docker 構件  | 否  | 是   |
| 自動回復變更，後續的偵錯工具  | 否  | 是   |
| **支援的用戶端工具** |
| 適用于 Visual Studio 2019  | 是  | 是   |
| 適用于 Visual Studio Code  | 是  | 是   |
| 搭配 CLI 使用  | 是  | 否   |
| **作業系統相容性** |
| 適用于 Windows 10  | 是  | 是  |
| 適用于 Linux  | 是  | 是  |
| 適用于 macOS  | 是  | 是  |
| **Capabilities** |
| 開發人員隔離或小組開發  | 是  | 是  |
| 選擇性地覆寫環境變數  | 否  | 是  |
| 建立 Dockerfile 和 Helm 圖表  | 是  | 否  |
| 將程式碼持續部署至 Kubernetes  | 是  | 否  |
| Kubernetes pod 中的遠端偵錯  | 是  | 否  |
| 本機調試，連接至 Kubernetes  | 否  | 是  |
| 在相同的工作站上同時對多個服務進行調試  | 是  | 是  |

## <a name="kubernetes-inner-loop-development"></a>Kubernetes 內部迴圈開發

Azure Dev Spaces 和 Bridge 與 Kubernetes 之間的最大差異是您的程式碼執行所在的位置。 Azure Dev Spaces 有助於開發和微服務程式代碼，但需要您在叢集中執行該程式碼。 橋接器至 Kubernetes 可讓您直接在開發電腦上開發和偵測微服務程式代碼，同時仍在 Kubernetes 中執行的大型應用程式內容中。 橋接器至 Kubernetes 可延伸 Kubernetes 叢集的周邊，並允許本機進程從 Kubernetes 繼承設定。

![內部迴圈開發](media/migrate-to-btk/btk-graphic-non-isolated.gif)

使用 Bridge Kubernetes 時，會建立您的開發電腦與叢集之間的網路連接。在此連線的存留期內，會將 proxy 加入至您的叢集，以取代您的 Kubernetes 部署，以將要求重新導向至您的開發電腦。 當您中斷連線時，應用程式部署將會還原為使用在叢集上執行之部署的原始版本。 這種方法與 Azure Dev Spaces 的運作方式不同，那就是將程式碼同步處理至叢集、建立然後執行。 Azure Dev Spaces 也不會復原您的程式碼。

Kubernetes 可讓您彈性地使用在 Kubernetes 中執行的應用程式，而不論其部署方法為何。 如果您使用 CI/CD 來建立並執行您的應用程式，不論您是使用已建立的工具或自訂腳本，您仍然可以使用橋接器來 Kubernetes，以開發和偵測程式碼。

> [!TIP]
>  [Microsoft Kubernetes 延伸][kubernetes-extension]模組可讓您使用 Intellisense 快速開發 Kubernetes 資訊清單，並協助 scaffold Helm 圖表。  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>從 Azure Dev Spaces 轉換至橋接器以進行 Kubernetes

1. 如果您使用 Visual Studio，請將 Visual Studio IDE 更新為16.7 或更高版本，並從 [Visual Studio Marketplace][vs-marketplace]安裝橋接器至 Kubernetes 擴充功能。 如果您使用 Visual Studio Code，請將 [橋接器安裝至 Kubernetes 擴充][vsc-marketplace]功能。
1. 使用 Azure 入口網站或 [AZURE DEV SPACES CLI][azds-delete]來停用 Azure Dev Spaces 控制器。
1. 使用 [Azure Cloud Shell](https://shell.azure.com)。 或在已安裝 bash 的 Mac、Linux 或 Windows 上，開啟 bash shell 提示字元。 請確定您的命令列環境中有提供下列工具： Azure CLI、docker、kubectl、捲曲、tar 和 gunzip。
1. 建立容器登錄，或使用現有的容器登錄。 您可以使用 [Azure Container Registry](../container-registry/index.yml) 或使用 [Docker Hub](https://hub.docker.com/)，在 Azure 中建立容器登錄。 使用 Azure Cloud Shell 時，只有 Azure Container Registry 可用於裝載 docker 映射。
1. 執行遷移腳本，將 Azure Dev Spaces 資產轉換為 Bridge 以 Kubernetes 資產。 此腳本會建立一個與 Bridge 相容的新映射以 Kubernetes、將它上傳至指定的登錄，然後使用 [Helm](https://helm.sh) 來更新包含映射的叢集。 您必須提供資源群組、AKS 叢集的名稱，以及容器登錄。 還有其他命令列選項，如下所示：

   ```azure-cli
   curl -sL https://aka.ms/migrate-tool | bash -s -- -g ResourceGroupName -n AKSName -h ContainerRegistryName -r PathOfTheProject -y
   ```

   腳本支援下列旗標：

   ```cmd  
    -g Name of resource group of AKS Cluster [required]
    -n Name of AKS Cluster [required]
    -h Container registry name. Examples: ACR, Docker [required]
    -k Kubernetes namespace to deploy resources (uses 'default' otherwise)
    -r Path to root of the project that needs to be migrated (default = current working directory)
    -t Image name & tag in format 'name:tag' (default is 'projectName:stable')
    -i Enable a public endpoint to access your service over internet. (default is false)
    -c Docker build context path. (default = project root path passed to '-r' option)
    -y Doesn't prompt for non-tty terminals
    -d Helm Debug switch
   ```

1. 將 *azds. yaml* 中的任何自訂專案（例如環境變數設定）手動遷移至專案的 *yml* 檔案。
1.  (選擇性) 移除 `azds.yaml` 專案中的檔案。
1. 在已部署的應用程式上設定 Bridge 以進行 Kubernetes。 如需使用 Bridge Kubernetes Visual Studio 的詳細資訊，請參閱 [Visual Studio 中的使用 bridge 來 Kubernetes][use-btk-vs]。 如 VS Code，請參閱 [VS Code 中的使用 Bridge Kubernetes][use-btk-vsc]。
1. 使用新建立的橋接器開始進行調試，以 Kubernetes debug/啟動設定檔。
1. 您可以視需要重新執行腳本，以重新部署至您的叢集。

## <a name="team-development-in-a-shared-cluster"></a>共用叢集中的小組開發

您也可以使用具有 Bridge 的開發人員專屬路由來 Kubernetes。 Azure Dev Spaces 的小組開發案例會使用多個 Kubernetes 命名空間，利用父和子命名空間的概念，將服務與應用程式的其餘部分隔離。 橋接器至 Kubernetes 提供相同的功能，但具有改良的效能特性，以及在相同的應用程式命名空間內。

Kubernetes 和 Azure Dev Spaces 的橋樑都需要有 HTTP 標頭，並在整個應用程式中傳播。 如果您已設定應用程式來處理 Azure Dev Spaces 的標頭傳播，則必須更新標頭。 若要從 Azure Dev Spaces 轉換為 Bridge 以進行 Kubernetes，請將設定的標頭從 *azds-route* 更新為 *Kubernetes-route as* 。

## <a name="evaluate-bridge-to-kubernetes"></a>評估橋接器至 Kubernetes

如果您想要在停用叢集中的 Azure Dev Spaces 之前，嘗試使用 Bridge 進行 Kubernetes，最簡單的方式是使用新的叢集。 如果您嘗試在相同的叢集上同時使用 Azure Dev Spaces 和 Bridge Kubernetes，當您在兩者上使用路由功能時，將會遇到問題。

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>使用 Visual Studio 評估 Kubernetes 的橋接器

1. 將您的 Visual Studio IDE 更新為16.7 版或更新版本，然後從 [Visual Studio Marketplace][vs-marketplace]安裝橋接器至 Kubernetes 擴充功能。
1. 建立新的 AKS 叢集，並部署您的應用程式。 您也可以使用 [範例應用程式][btk-sample-app]。
1. 在已部署的應用程式上設定 Bridge 以進行 Kubernetes。 如需使用 Bridge Kubernetes Visual Studio 的詳細資訊，請參閱 [使用 Bridge Kubernetes][use-btk-vs]。
1. 使用新建立的橋接器來 Kubernetes 調試設定檔，以在 Visual Studio 中啟動調試。

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>使用 Visual Studio Code 評估 Kubernetes 的橋接器

1. 安裝 [橋接器至 Kubernetes 擴充][vsc-marketplace]功能。
1. 建立新的 AKS 叢集，並部署您的應用程式。 您也可以使用 [範例應用程式][btk-sample-app]。
1. 在已部署的應用程式上設定 Bridge 以進行 Kubernetes。 如需使用 Bridge Kubernetes Visual Studio Code 的詳細資訊，請參閱 [使用 Bridge Kubernetes][use-btk-vsc]。
1. 使用新建立的 Bridge Kubernetes 啟動設定檔，在 Visual Studio 中開始進行調試。

## <a name="next-steps"></a>後續步驟

深入瞭解 Kubernetes 橋接器的運作方式。

> [!div class="nextstepaction"]
> [Bridge to Kubernetes 的運作方式][how-it-works-bridge-to-kubernetes]


[azds-delete]: how-to/install-dev-spaces.md#remove-azure-dev-spaces-using-the-cli
[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/
