---
title: 在 Windows 上設定 Azure Service Fabric Linux 叢集
description: 本文說明如何設定在 Windows 開發電腦上執行的 Service Fabric Linux 叢集。 這種方法適用于跨平臺開發。
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: e25c6adf5e5f5101025aa883ef2ff9750c113a76
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164103"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>在 Windows 開發人員電腦上設定 Linux Service Fabric 叢集

本檔涵蓋如何在 Windows 開發電腦上設定本機 Linux Service Fabric 叢集。 設定本機 Linux 叢集，對快速測試以 Linux 叢集為目標但在 Windows 電腦上開發的應用程式而言，會很有用。

## <a name="prerequisites"></a>必要條件
以 linux 為基礎的 Service Fabric 叢集不會在 Windows 上執行，但為了啟用跨平臺原型設計，我們提供了 Linux Service Fabric 一個 box 叢集 docker 容器，可透過適用於 Windows 的 Docker 來部署。

開始之前，您需要：

* 至少 4 GB 的 RAM
* [適用於 Windows 的 Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)的最新版本
* Docker 必須以 Linux 容器模式執行

>[!TIP]
> 若要在您的 Windows 電腦上安裝 Docker，請依照 [docker 檔](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions)中的步驟執行。 在安裝之後，請[確認您的安裝](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine)。
>

## <a name="create-a-local-container-and-setup-service-fabric"></a>建立本機容器並設定 Service Fabric
若要設定本機 Docker 容器，並在其上執行 Service Fabric 叢集，請執行下列步驟：


1. 使用下列內容更新您主機上的 Docker 精靈設定，然後重新啟動 Docker 精靈： 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    建議的更新方法是前往： 

    * Docker 引擎 > docker 圖示 > 設定
    * 新增上面所列的新欄位
    * 套用 & 重新開機-重新開機 Docker daemon，變更才會生效。

2. 透過 PowerShell 啟動叢集。<br/>
    <b>Ubuntu 18.04 LTS：</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16.04 LTS：</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > 根據預設，這會提取包含最新版 Service Fabric 的映像。 如需特定的修訂，請造訪 [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/) 頁面。



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

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >這項作業需要一些時間，但只需要執行一次。

    現在您可以執行下列動作，隨時快速啟動 Service Fabric 的本機副本：

    ```powershell 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >為容器執行個體提供名稱，以便能以更容易辨識的方式來處理。 
    >
    >如果您的應用程式正在特定連接埠上接聽，這些連接埠就必須使用額外的 `-p` 標籤來加以指定。 例如，如果您的應用程式正在連接埠 8080 上接聽，請新增下列 `-p` 標籤：
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >


4. 叢集會在短時間內啟動，您可使用下列命令來檢視記錄，或跳至儀表板來檢視叢集健康狀態 `http://localhost:19080`：

    ```powershell 
    docker logs sftestcluster
    ```

5. 成功部署叢集之後，如步驟4所觀察到的一樣，您可以 ``http://localhost:19080`` 從 Windows 電腦移至，以尋找 Service Fabric Explorer 儀表板。 至此，您可以使用 Windows 開發人員電腦的工具連接到此叢集，並部署以 Linux Service Fabric 叢集為目標的應用程式。 

    > [!NOTE]
    > Windows 上目前不支援 Eclipse 外掛程式。 

6. 當您完成時，請使用下列命令來停止和清除容器：

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>已知限制 
 
 在 Mac 的容器中執行的本機叢集已知有下列限制： 
 
 * DNS 服務未執行，而且目前在容器中不受支援。 [問題 #132](https://github.com/Microsoft/service-fabric/issues/132)
 * 執行以容器為基礎的應用程式需要在 Linux 主機上執行 SF。 目前不支援嵌套容器應用程式。

## <a name="next-steps"></a>後續步驟
* [使用 Yeoman 在 Linux 上建立和部署第一個 Service Fabric Java 應用程式](service-fabric-create-your-first-linux-application-with-java.md)
* 開始使用 [Eclipse](./service-fabric-get-started-eclipse.md)
* 查看其他 [Java 範例](https://github.com/Azure-Samples/service-fabric-java-getting-started)
* 瞭解 [Service Fabric 支援選項](service-fabric-support.md)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
