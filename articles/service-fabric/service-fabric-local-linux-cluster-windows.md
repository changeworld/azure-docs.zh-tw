---
title: 在 Windows 上設定 Azure Service Fabric Linux 叢集
description: 本文說明如何設定在 Windows 開發電腦上執行的 Service Fabric Linux 叢集。 這特別適用於跨平台開發。
author: suhuruli
ms.topic: conceptual
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: 1798d57f9fc4004fd2c7d1f1901fcddf850626c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193321"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>在 Windows 開發人員電腦上設定 Linux Service Fabric 叢集

本文件涵蓋如何在 Windows 開發電腦上設定本機 Linux Service Fabric。 設定本機 Linux 叢集，對快速測試以 Linux 叢集為目標但在 Windows 電腦上開發的應用程式而言，會很有用。

## <a name="prerequisites"></a>必要條件
以 Linux 為基礎的 Service Fabric 叢集無法在 Windows 上原生執行。 為執行本機 Service Fabric 叢集，我們提供預先設定的 Docker 容器映像。 開始之前，您需要：

* 至少 4 GB 的 RAM
* 最新版 [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker 必須在 Linux 模式上執行

>[!TIP]
> * 您可以遵循官方 Docker [文件](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions)中所述的步驟，在 Windows 上安裝 Docker。 
> * 一旦您完成安裝後，如果遵循[這裡](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine)所述的步驟正確安裝，就進行驗證


## <a name="create-a-local-container-and-setup-service-fabric"></a>建立本機容器並設定 Service Fabric
若要設定本機的 Docker 容器，並在容器上執行 Service Fabric 叢集，請在 PowerShell 中執行下列步驟：


1. 使用下列內容更新您主機上的 Docker 精靈設定，然後重新啟動 Docker 精靈： 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    建議的更新方法是：移至 [Docker] 圖示 > [設定] > [精靈] > [進階]，並在該處進行更新。 接下來，重新啟動 Docker 精靈以讓變更生效。 

2. 在新目錄中建立一個名為 `Dockerfile` 的檔案，以建置 Service Fabric 映像：

    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:latest
    WORKDIR /home/ClusterDeployer
    RUN ./setup.sh
    #Generate the local
    RUN locale-gen en_US.UTF-8
    #Set environment variables
    ENV LANG=en_US.UTF-8
    ENV LANGUAGE=en_US:en
    ENV LC_ALL=en_US.UTF-8
    EXPOSE 19080 19000 80 443
    #Start SSH before running the cluster
    CMD /etc/init.d/ssh start && ./run.sh
    ```

    >[!NOTE]
    >您可以調整這個檔案，以在您的容器中新增其他程式或相依性。
    >例如，新增 `RUN apt-get install nodejs -y` 將允許以客體可執行檔形式支援 `nodejs` 應用程式。
    
    >[!TIP]
    > 根據預設，這會提取包含最新版 Service Fabric 的映像。 如需特定版本，請造訪 [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/) 頁面。

3. 若要從 `Dockerfile` 建置可重複使用的映像，請開啟終端機並 `cd` 到直接保留的 `Dockerfile`，然後執行：

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >這項作業需要一些時間，但只需要執行一次。

4. 您現在可以在需要時快速啟動 Service Fabric 的本機複本，方法是執行以下項目：

    ```powershell 
    docker run --name sftestcluster -d -v //var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >為容器執行個體提供名稱，以便能以更容易辨識的方式來處理。 
    >
    >如果您的應用程式正在特定連接埠上接聽，這些連接埠就必須使用額外的 `-p` 標籤來加以指定。 例如，如果您的應用程式正在連接埠 8080 上接聽，請新增下列 `-p` 標籤：
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:latest`
    >

5. 叢集會在短時間內啟動，您可使用下列命令來檢視記錄，或跳至儀表板來檢視叢集健康狀態 `http://localhost:19080`：

    ```powershell 
    docker logs sftestcluster
    ```

6. 順利完成步驟 5 之後，您可以從 Windows 移至 ``http://localhost:19080``，就能看到 Service Fabric 總管。 此時，您可以使用 Windows 開發人員電腦上的任何工具連線到這個叢集，並部署以 Linux Service Fabric 叢集為目標的應用程式。 

    > [!NOTE]
    > Windows 上目前不支援 Eclipse 外掛程式。 

7. 當您完成時，請使用此命令來停止和清除容器：

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>已知限制 
 
 在 Mac 的容器中執行的本機叢集已知有下列限制： 
 
 * DNS 服務未執行，且不受支援 ([問題 #132](https://github.com/Microsoft/service-fabric/issues/132))

## <a name="next-steps"></a>後續步驟
* 開始使用 [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* 查看其他 [Java 範例](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
