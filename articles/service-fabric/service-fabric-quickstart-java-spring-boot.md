---
title: 快速入門：在 Azure Service Fabric 上建立 Spring Boot 應用程式
description: 在本教學課程中，您會使用 Spring Boot 範例應用程式來部署適用於 Azure Service Fabric 的 Spring Boot 應用程式。
author: suhuruli
ms.topic: quickstart
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: eb96989b4a2731e78471b848d690b48352408d1c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121476"
---
# <a name="quickstart-deploy-a-java-spring-boot-app-on-azure-service-fabric"></a>快速入門：在 Azure Service Fabric 上部署 Java Spring Boot 應用程式

在本快速入門中，您會在 Linux 或 MacOS 上使用熟悉的命令列工具，將 Java Spring Boot 應用程式部署至 Azure Service Fabric。 Azure Service Fabric 是一個分散式系統平台，可讓您部署及管理微服務與容器。 

## <a name="prerequisites"></a>Prerequisites

#### <a name="linux"></a>[Linux](#tab/linux)

- [Java 環境](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)和 [Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Service Fabric SDK 和 Service Fabric 命令列介面 (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
- [Git](https://git-scm.com/downloads)

#### <a name="macos"></a>[MacOS](#tab/macos)

- [Java 環境和 Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)
- [Service Fabric SDK 和 Service Fabric 命令列介面 (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli#cli-mac)
- [Git](https://git-scm.com/downloads)

--- 

## <a name="download-the-sample"></a>下載範例

在終端機視窗中執行下列命令，將 Spring Boot [Getting Started](https://github.com/spring-guides/gs-spring-boot) 範例應用程式複製到本機電腦。

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>建置 Spring Boot 應用程式 
在 gs-spring-boot/complete  目錄中，執行下列命令以建置應用程式 

```bash
./gradlew build
``` 

## <a name="package-the-spring-boot-application"></a>封裝 Spring Boot 應用程式 
1. 在複本的 gs-spring-boot  目錄中，執行 `yo azuresfguest` 命令。 

1. 針對每個提示，輸入下列詳細資料。

    ![Spring Boot Yeoman 項目](./media/service-fabric-quickstart-java-spring-boot/yeoman-entries-spring-boot.png)

1. 在 SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code  資料夾中，建立稱為 entryPoint.sh  的檔案。將下列程式碼新增至 entryPoint.sh  檔案。 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

1. 在 *gs-spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml* 檔案中新增**端點**資源

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    *ServiceManifest.xml* 現在如下所示： 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

在這個階段，您已經針對可以部署到 Service Fabric 的 Spring Boot Getting Started 範例，建立 Service Fabric 應用程式。

## <a name="run-the-application-locally"></a>在本機執行應用程式

1. 執行下列命令，以在 Ubuntu 機器上啟動本機叢集：

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    如果使用 Mac，請從 Docker 映像啟動本機叢集 (假設您已遵循[必要條件](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-a-local-container-and-set-up-service-fabric)針對 Mac 設定您的本機叢集)。 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    啟動本機叢集需要一些時間。 若要確認該叢集完全啟動，請存取位於 `http://localhost:19080` 的 Service Fabric Explorer。 五個狀況良好的節點表示本機叢集已啟動並執行。 
    
    ![Service Fabric Explorer 顯示狀況良好的節點](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-healthy-nodes.png)

1. 開啟 gs-spring-boot/SpringServiceFabric  資料夾。
1. 執行下列命令，以連接到本機叢集。

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. 執行 install.sh  指令碼。

    ```bash
    ./install.sh
    ```

1. 開啟您最愛的網頁瀏覽器，並存取 `http://localhost:8080` 以存取應用程式。

    ![Spring Boot Service Fabric 範例](./media/service-fabric-quickstart-java-spring-boot/spring-boot-service-fabric-sample.png)

您現在可以存取已部署至 Service Fabric 叢集的 Spring Boot 應用程式。

如需詳細資訊，請參閱 Spring 網站上的 Spring Boot [Getting Started](https://spring.io/guides/gs/spring-boot/) 範例。

## <a name="scale-applications-and-services-in-a-cluster"></a>調整叢集中的應用程式和服務

您可以在整個叢集內調整服務，以因應服務的負載變更。 您可以藉由變更叢集中執行的執行個體數目來調整服務。 您可以透過多種方式調整服務，例如，可以使用 Service Fabric CLI (sfctl) 中的指令碼或命令。 在下列步驟中，請使用 Service Fabric Explorer。

Service Fabric Explorer 會在所有 Service Fabric 叢集中執行，並可藉由瀏覽至叢集 HTTP 管理連接埠 (19080) 從瀏覽器存取，例如 `http://localhost:19080`。

若要調整 Web 前端服務，請執行下列動作：

1. 在您的叢集中開啟 Service Fabric Explorer，例如 `http://localhost:19080`。
1. 選取樹狀檢視中 **fabric:/SpringServiceFabric/SpringGettingStarted** 節點旁邊的省略符號 ( **...** )，然後選取 [調整服務]  。

    ![Service Fabric Explorer 調整服務範例](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-scale-sample.png)

    您現在可以選擇調整服務的執行個體數目。

1. 將數字變更為 **3**，然後選取 [縮放服務]  。

    使用命令列調整服務的替代方式如下所示。

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. 選取樹狀檢視中的 **fabric:/SpringServiceFabric/SpringGettingStarted** 節點，然後展開資料分割節點 (以 GUID 表示)。

    ![Service Fabric Explorer 調整服務完成](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-partition-node.png)

    此服務有三個執行個體，且樹狀檢視會顯示執行個體在其中執行的節點。

藉由這項簡單的管理工作，您已讓前端服務可用來處理使用者負載的資源倍增。 請務必了解，您不需要多個服務執行個體，就能讓服務確實可靠地執行。 如果服務失敗，Service Fabric 可確保會有新的服務執行個體在叢集中執行。

## <a name="fail-over-services-in-a-cluster"></a>叢集中的容錯移轉服務

若要示範服務容錯移轉，可以使用 Service Fabric Explorer 來模擬節點重新啟動。 請確定您的服務只有一個執行個體正在執行。

1. 在您的叢集中開啟 Service Fabric Explorer，例如 `http://localhost:19080`。
1. 選取執行您服務執行個體之節點旁邊的省略符號 ( **...** )，然後重新啟動節點。

    ![Service Fabric Explorer 重新啟動節點](./media/service-fabric-quickstart-java-spring-boot/service=fabric-explorer-restart=node.png)
1. 您服務的執行個體會移至其他節點，而且您的應用程式不會有任何停機時間。

    ![Service Fabric Explorer 重新啟動節點成功](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-service-moved.png)

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

* 將 Spring Boot 應用程式部署到 Service Fabric
* 將應用程式部署到本機叢集
* 跨多個節點相應放大應用程式
* 無法使用時，執行服務的容錯移轉

若要深入了解如何在 Service Fabric 中使用 Java 應用程式，請繼續進行教學課程以了解 Java 應用程式。

> [!div class="nextstepaction"]
> [部署 Java 應用程式](./service-fabric-tutorial-create-java-app.md)
