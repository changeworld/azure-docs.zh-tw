---
title: 在 macOS 上設定開發環境
description: 安裝執行階段、SDK 和工具，並建立本機開發叢集。 完成此設定之後，您就可以開始在 macOS 上建立應用程式。
ms.topic: conceptual
ms.date: 10/16/2020
ms.custom: devx-track-js
ms.openlocfilehash: d08046c8f29901dd9650a1edc886efa2ff226e00
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086772"
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>在 Mac OS X 上設定開發環境
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)

您可以建置 Azure Service Fabric 應用程式，以使用 Mac OS X 在 Linux 叢集上執行。本文件涵蓋如何設定您的 Mac 進行開發。

## <a name="prerequisites"></a>Prerequisites
Azure Service Fabric 不會在 Mac OS X 上以原生方式執行。若要執行本機 Service Fabric 叢集，我們提供預先設定的 Docker 容器映像。 開始之前，您需要：

* 至少 4 GB 的 RAM。
* 最新版 [Docker](https://www.docker.com/)。

>[!TIP]
>
>若要在 Mac 上安裝 Docker，請遵循 [Docker 文件](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install)中的步驟。 在安裝之後，請[確認您的安裝](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine)。
>

## <a name="create-a-local-container-and-set-up-service-fabric"></a>建立本機容器並設定 Service Fabric
若要設定本機的 Docker 容器，並在容器上執行 Service Fabric 叢集，請執行下列步驟：

1. 使用下列設定更新您主機上的 Docker 精靈設定，然後重新啟動 Docker 精靈： 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    您可以直接在 Docker 安裝路徑的 daemon.json 檔案中更新這些設定。 您可以直接修改 Docker 中的精靈組態設定。 選取 [Docker 圖示]  ，然後選取 [喜好設定]  > [精靈]  > [進階]  。
    
    >[!NOTE]
    >
    >建議您直接在 Docker 中修改 daemon，因為檔案 daemon.js的位置可能會因電腦而異。 例如，~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json。
    >

    >[!TIP]
    >測試大型應用程式時，建議提高配置給 Docker 的資源。 選取 [Docker 圖示]  ，然後選取 [進階]  來調整核心數目和記憶體，即可完成此作業。

2. 啟動叢集。<br/>
    <b>Ubuntu 18.04 LTS：</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16.04 LTS：</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > 根據預設，這會提取包含最新版 Service Fabric 的映像。 如需特定的修訂，請造訪 Docker Hub 上的 [Service Fabric Onebox](https://hub.docker.com/_/microsoft-service-fabric-onebox) 頁面。



3. 選擇性：建立您的擴充 Service Fabric 映射。

    在新的目錄中，建立名為的檔案 `Dockerfile` 來建立自訂映射：

    >[!NOTE]
    >您可以使用 Dockerfile 來調整上述映射，以在您的容器中新增其他程式或相依性。
    >例如，新增 `RUN apt-get install nodejs -y` 將允許以客體可執行檔形式支援 `nodejs` 應用程式。
    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:u18
    RUN apt-get install nodejs -y
    EXPOSE 19080 19000 80 443
    WORKDIR /home/ClusterDeployer
    CMD ["./ClusterDeployer.sh"]
    ```
    
    >[!TIP]
    > 根據預設，這會提取包含最新版 Service Fabric 的映像。 如需特定的修訂，請造訪 [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/) 頁面。

    若要從建立可重複使用的映射 `Dockerfile` ，請開啟終端機，然後 `cd` 直接保留您的 `Dockerfile` 執行：

    ```bash 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >這項作業需要一些時間，但只需要執行一次。

    現在您可以執行下列動作，隨時快速啟動 Service Fabric 的本機副本：

    ```bash 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >為容器執行個體提供名稱，以便能以更容易辨識的方式來處理。 
    >
    >如果您的應用程式正在特定連接埠上接聽，這些連接埠就必須使用額外的 `-p` 標籤來加以指定。 例如，如果您的應用程式正在連接埠 8080 上接聽，請新增下列 `-p` 標籤：
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >

4. 叢集需要一些時間來啟動。 執行時，您可以使用下列命令來查看記錄，或跳至儀表板以查看叢集健全狀況： `http://localhost:19080`

    ```bash 
    docker logs sftestcluster
    ```


5. 若要停止並清除容器，請使用下列命令。 不過，我們將在下一個步驟使用此容器。

    ```bash 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>已知限制 
 
 在 Mac 的容器中執行的本機叢集已知有下列限制： 
 
 * DNS 服務未執行，而且目前在容器中不受支援。 [問題 #132](https://github.com/Microsoft/service-fabric/issues/132)
 * 執行以容器為基礎的應用程式需要在 Linux 主機上執行 SF。 目前不支援嵌套容器應用程式。

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>設定 Mac 上的 Service Fabric CLI (sfctl)

請遵循 [Service Fabric CLI](service-fabric-cli.md#cli-mac) 的指示在您的 Mac 安裝 Service Fabric CLI (`sfctl`)。
CLI 命令支援與 Service Fabric 實體 (包括叢集、應用程式和服務) 互動。

1. 若要在部署應用程式前連線到叢集，請執行下列命令。 

```bash
sfctl cluster select --endpoint http://localhost:19080
```

## <a name="create-your-application-on-your-mac-by-using-yeoman"></a>在 Mac 上使用 Yeoman 建立應用程式

Service Fabric 提供的 Scaffolding 工具可協助您從終端機使用 Yeoman 範本產生器建立 Service Fabric 應用程式。 請使用下列步驟，確保 Service Fabric Yeoman 範本產生器可在您的機器上運作：

1. 您的 Mac 上必須安裝 Node.js 和節點套件管理員 (NPM)。 您可以使用 [HomeBrew](https://brew.sh/) 安裝軟體，如下所示：

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. 在您的機器上透過 NPM 安裝 [Yeoman](https://yeoman.io/) 範本產生器：

    ```bash
    npm install -g yo
    ```
3. 遵循快速入門[文件](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)中的步驟，安裝您想要的 Yeoman 產生器。 若要使用 Yeoman 建立 Service Fabric 應用程式，請遵循下列步驟：

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. 在您安裝產生器後，請分別執行 `yo azuresfguest` 或 `yo azuresfcontainer`，以建立來賓可執行檔或容器服務。

5. 若要在 Mac 上建置 Service Fabric Java 應用程式，您必須在主機機器上安裝 JDK 1.8 版和 Gradle。 您可以使用 [HomeBrew](https://brew.sh/) 安裝軟體，如下所示： 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

    > [!IMPORTANT]
    > 目前的版本 `brew cask install java` 可能會安裝較新版本的 JDK。
    > 請務必安裝 JDK 8。

## <a name="deploy-your-application-on-your-mac-from-the-terminal"></a>在 Mac 上從終端機部署應用程式

在建立並建置 Service Fabric 應用程式後，您就可以使用 [Service Fabric CLI](service-fabric-cli.md#cli-mac) 部署應用程式：

1. 在 Mac 上連線到容器執行個體內執行的 Service Fabric 叢集：

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. 從您的專案目錄內執行安裝指令碼：

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-31-development"></a>設定 .NET Core 3.1 開發

安裝 [適用于 Mac 的 .Net Core 3.1 SDK](https://www.microsoft.com/net/core#macos) ，以開始 [建立 c # Service Fabric 應用程式](service-fabric-create-your-first-linux-application-with-csharp.md)。 適用于 .NET Core Service Fabric 應用程式的套件裝載于 NuGet.org 上。

## <a name="install-the-service-fabric-plug-in-for-eclipse-on-your-mac"></a>在 Mac 上安裝適用於 Eclipse 的 Service Fabric 外掛程式

Azure Service Fabric 會針對 Java IDE 提供 Eclipse Neon (或更新版本) 的外掛程式。 外掛程式可簡化建立、建置和部署 Java 服務的程序。 若要安裝或更新為最新版的 Eclipse Service Fabric 外掛程式，請遵循[這些步驟](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse)。 [Eclipse 的 Service Fabric 文件](service-fabric-get-started-eclipse.md)中的其他步驟也適用：建置應用程式、將服務新增至應用程式、解除安裝應用程式等。

最後一個步驟是使用與您的主機共用的路徑將容器具現化。 外掛程式需要這種類型的具現化，才能在您的 Mac 上使用 Docker 容器。 例如：

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox mcr.microsoft.com/service-fabric/onebox:latest
```

屬性的定義如下：
* `/Users/sayantan/work/workspaces/mySFWorkspace` 是您 Mac 上的工作區完整路徑。
* `/tmp/mySFWorkspace` 是工作區所應對應的容器內路徑。

>[!NOTE]
> 
>如果您的工作區使用不同的名稱/路徑，請在 `docker run` 命令中更新這些值。
> 
>如果您使用 `sfonebox` 以外的名稱來啟動容器，請在您 Service Fabric 執行者 Java 應用程式中的 testclient.sh 檔案更新該名稱值。
>

## <a name="next-steps"></a>下一步
<!-- Links -->
* [使用 Yeoman 在 Linux 上建立和部署第一個 Service Fabric Java 應用程式](service-fabric-create-your-first-linux-application-with-java.md)
* [使用適用于 Eclipse 的 Service Fabric 外掛程式，在 Linux 上建立及部署您的第一個 Service Fabric JAVA 應用程式](service-fabric-get-started-eclipse.md)
* [在 Azure 入口網站中建立 Service Fabric 叢集](service-fabric-cluster-creation-via-portal.md)
* [使用 Azure Resource Manager 來建立 Service Fabric 叢集](service-fabric-cluster-creation-via-arm.md)
* [了解 Service Fabric 應用程式模型](service-fabric-application-model.md)
* [使用 Service Fabric CLI 管理應用程式](service-fabric-application-lifecycle-sfctl.md)
* [在 Windows 上準備 Linux 開發環境](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
