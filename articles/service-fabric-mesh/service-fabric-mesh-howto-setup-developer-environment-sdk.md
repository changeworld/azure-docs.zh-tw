---
title: 設定 Service Fabric 網狀的 Windows 開發環境
description: 設定您的 Windows 開發環境，以便建立 Service Fabric Mesh 應用程式，並將其部署至 Azure Service Fabric Mesh。
author: georgewallace
ms.author: gwallace
ms.date: 12/12/2018
ms.topic: conceptual
ms.openlocfilehash: 479e975abd40be80e17fc187f1d13500d11cb983
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842745"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>設定 Windows 開發環境以建置 Service Fabric Mesh 應用程式

若要在 Windows 開發機器上建置並執行 Azure Service Fabric Mesh 應用程式，您需要：

* Docker
* Visual Studio 2017 或更新版本
* Service Fabric Mesh 執行階段
* Service Fabric Mesh SDK 和工具。

以及下列其中一個 Windows 版本：

* Windows 10 (Enterprise、Professional 或 Education) 1709 版 (Fall Creators Update) 或 1803 版 (Windows 10 April 2018 Update)
* Windows Server 1709 版
* Windows Server 1803 版

下列指示可協助您根據您正在執行的 Windows 版本來安裝所有專案。

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

需要 Visual Studio 2017 或更新版本，才能部署 Service Fabric 網狀應用程式。 [安裝 15.6.0 版][download-visual-studio]或更新版本，並啟用下列工作負載：

* ASP.NET 和 Web 開發
* Azure 開發

## <a name="install-docker"></a>安裝 Docker

如果您已安裝 Docker，請確定您的是最新版本。 Docker 可能會在有新版本時提示您，但請手動檢查以確定您擁有最新版本。

#### <a name="install-docker-on-windows-10"></a>在 Windows 10 上安裝 Docker

下載並安裝最新版的 [Docker Community Edition for Windows][download-docker]，以支援 Service Fabric Mesh 所使用的容器化 Service Fabric 應用程式。

在安裝期間，當系統詢問時，請選取 [使用 Windows 容器，而非 Linux 容器]****。

如果您的電腦上未啟用 Hyper-v，Docker 的安裝程式將會提供啟用它的功能。 如果出現提示，請按一下 [確定]**** 以執行此作業。

#### <a name="install-docker-on-windows-server-2016"></a>在 Windows Server 2016 上安裝 Docker

如果您未啟用 Hyper-V 角色，請以系統管理員身分開啟 PowerShell 並執行下列命令，以啟用 Hyper-V，然後重新啟動電腦。 如需詳細資訊，請參閱 [Docker Enterprise Edition for Windows Server][download-docker-server]。

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

重新啟動您的電腦。

以系統管理員身分開啟 PowerShell 並執行下列命令，以安裝 Docker：

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDK 與工具

依下列順序安裝 Service Fabric Mesh 執行階段、SDK 和工具。

1. 使用 Web Platform Installer 安裝 [Service Fabric Mesh SDK][download-sdkmesh]。 這也將安裝 Microsoft Azure Service Fabric SDK 和執行階段。
2. 從 Visual Studio Marketplace 安裝 [Visual Studio Service Fabric Mesh Tools (預覽) 擴充功能][download-tools]。

## <a name="build-a-cluster"></a>建置叢集

> [!IMPORTANT]
> **必須**先執行 Docker，您才能建置叢集。
> 請開啟終端機視窗並執行 `docker ps`，然後查看是否有錯誤發生，以測試該 Docker。 如果回應未指出錯誤，表示 Docker 正在執行中，且您已可建置叢集。

> [!Note]
> 如果您是在 Windows Fall Creators Update (1709 版) 機器上進行開發，則只能使用 Windows 1709 版 Docker 映像。
> 如果您是在 Windows 10 April 2018 update (1803 版) 機器上進行開發，則可以使用 Windows 1709 版或 1803 版的 Docker 映像。

如果您使用 Visual Studio，可以略過本節，因為如果您沒有本機叢集，Visual Studio 會為您建立本機叢集。

當您一次建立和執行單一 Service Fabric 應用程式時，為了獲得最佳的調試效能，請建立單一節點的本機開發叢集。 如果您要一次執行多個應用程式，請建立五個節點的本機開發叢集。 每當您部署或偵錯 Service Fabric Mesh 專案時，都必須執行此叢集。

在您安裝執行階段、SDK、Visual Studio 工具、Docker，且 Docker 開始執行後，請建立開發叢集。

1. 關閉 PowerShell 視窗。
2. 以系統管理員身分開啟提升權限的新 PowerShell 視窗。 必須執行此步驟，才能載入最近安裝的 Service Fabric 模組。
3. 執行下列 PowerShell 命令，以建立開發叢集：

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. 若要啟動本機叢集管理員工具，請執行下列 PowerShell 命令：

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. 當服務叢集管理員工具開始執行 (它會出現在系統匣中) 後，請以滑鼠右鍵按一下該工具，然後按一下 [啟動本機叢集]****。

![圖 1 - 啟動本機叢集](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

現在您已準備就緒，可建立 Service Fabric Mesh 應用程式！

## <a name="next-steps"></a>後續步驟

完整閱讀[建立 Azure Service Fabric 應用程式](service-fabric-mesh-tutorial-create-dotnetcore.md)教學課程。

尋找[常見問題和已知問題](service-fabric-mesh-faq.md)的解答。

[azure-cli-install]: /cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/
