---
title: Azure 服務結構網格 Maven 參考
description: 包含如何使用 Service Fabric Mesh 的 Maven 外掛程式參考
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.openlocfilehash: bcc3fb7c6c3adce0997d0960c4d98227089b048b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75459012"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Service Fabric Mesh 的 Maven 外掛程式

## <a name="prerequisites"></a>Prerequisites

- Java SDK
- Maven
- Azure CLI 與 Mesh 延伸模組
- Service Fabric CLI

## <a name="goals"></a>目標

### `azure-sfmesh:init`
- 建立包含 `appresources` 資料夾 (其中有 `application.yaml` 檔案) 的 `servicefabric` 資料夾。 

### `azure-sfmesh:addservice`
- 以服務名稱在 `servicefabric` 內建立一個資料夾，並建立服務的 YAML 檔案。 

### `azure-sfmesh:addnetwork`
- 以提供的網路名稱在 `appresources` 資料夾中產生 `network` YAML 

### `azure-sfmesh:addgateway`
- 以提供的閘道名稱在 `appresources` 資料夾中產生 `gateway` YAML。 

#### `azure-sfmesh:addvolume`
- 生成具有`volume`資料夾中提供的卷名稱的`appresources`YAML。

### `azure-sfmesh:addsecret`
- 以提供的祕密名稱在 `appresources` 資料夾中產生 `secret` YAML 

### `azure-sfmesh:addsecretvalue`
- 以提供的祕密和祕密值名稱在 `appresources` 資料夾中產生 `secretvalue` YAML 

### `azure-sfmesh:deploy`
- 從 `servicefabric` 資料夾合併 YAML，並在目前資料夾中建立 Azure Resource Manager 範本 JSON。
- 將所有資源都部署至 Azure Service Fabric Mesh 環境 

### `azure-sfmesh:deploytocluster`
- 建立資料夾 (`meshDeploy`)，其中包含部署 JSON，這是從適用於 Service Fabric 叢集的 YAML 中所產生
- 將所有資源都部署至 Service Fabric 叢集
 

## <a name="usage"></a>使用量

若要在您的 Maven Java 應用程式中使用 Maven 外掛程式，請在 pom.xml 檔案中新增下列程式碼片段：

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

## <a name="common-configuration"></a>常見組態

Maven 外掛程式目前不支援適用於 Azure 的 Maven 外掛程式常見組態。

## <a name="how-to"></a>作法

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>初始化 Azure Service Fabric Mesh 的 Maven 專案
執行下列命令以建立應用程式資源 YAML 檔案。

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- 在包含 `app_helloworldserver`應用程式 YAML 的根資料夾中建立名為 `servicefabric->appresources` 的資料夾

### <a name="add-resource-to-your-application"></a>將資源新增至您的應用程式

#### <a name="add-a-new-network-to-your-application"></a>將新網路新增至您的應用程式
執行下列命令以建立網路資源 YAML。 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- 在 `servicefabric->appresources` 資料夾中建立名為 `network_helloworldservicenetwork` 的網路 YAML

#### <a name="add-a-new-service-to-your-application"></a>將新服務新增至應用程式
執行下列命令以建立服務 YAML。 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- 在 `servicefabric->helloworldservice` 資料夾中建立名為 `service_helloworldservice` 服務 YAML，並參考 `helloworldservicenetwork` 和 `helloworldserver` 應用程式
- 服務會接聽連接埠 8080
- 服務會使用 ***helloworldserver:latest***，因為它是容器映像。

#### <a name="add-a-new-gateway-resource-to-your-application"></a>將新閘道資源新增至您的應用程式
執行下列命令以建立閘道資源 YAML。 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- 在 `servicefabric->appresources` 資料夾中建立名為 `gateway_helloworldgateway` 的閘道 YAML
- 將 `helloworldservicelistener` 作為服務接聽程式來進行參考，以接聽來自此閘道的呼叫。 也會將 `helloworldservice` 作為服務、將 `helloworldservicenetwork` 作為網路，以及將 `helloworldserver` 作為應用程式來進行參考。 
- 接聽連接埠 80 上的要求

#### <a name="add-a-new-volume-to-your-application"></a>將新磁碟區新增至應用程式
執行下列命令以建立磁碟區資源 YAML。 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- 在 `servicefabric->appresources` 資料夾中建立名為 `volume_vol1` 的磁碟區 YAML
- 設定必要參數的屬性 (`volumeAccountKey` 和 `volumeShareName`)，如上所述
- 若要深入了解如何參考這個已建立的磁碟區，請瀏覽下方的[使用 Azure 檔案服務磁碟區部署應用程式](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>將新祕密資源新增至您的應用程式
執行下列命令以建立祕密資源 YAML。 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- 在 `servicefabric->appresources` 資料夾中建立名為 `secret_secret1` 的祕密 YAML
- 若要深入了解如何參考這個已建立的祕密，請瀏覽下方的[管理密碼](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>將新祕密值資源新增至您的應用程式
執行下列命令以建立祕密值資源 YAML。 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- 在 `servicefabric->appresources` 資料夾中建立名為 `secretvalue_secret1_v1` 的祕密值 YAML

### <a name="run-the-application-locally"></a>在本機執行應用程式

透過 `azure-sfmesh:deploytocluster` 目標的協助，您可以使用下列命令在本機執行應用程式：

```cmd
mvn azure-sfmesh:deploytocluster
```

根據預設，此目標會將資源部署到本機叢集。 如果您要部署到本機叢集，我們假設您已啟動並執行本機 Service Fabric 叢集。 目前僅 [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md) 支援用於資源的本機 Service Fabric 叢集。

- 從適用於 Service Fabric 叢集的 YAML 建立 JSON
- 然後部署至叢集端點

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>將應用程式部署到 Azure Service Fabric Mesh

透過 `azure-sfmesh:deploy` 目標的協助，您可以執行下列命令來部署到 Service Fabric Mesh 環境：

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- 建立稱為 `todoapprg` 的資源群組 (如果不存在的話)。
- 藉由合併 YAML 來建立 Azure Resource Manager 範本 JSON。 
- 將 JSON 部署至 Azure Service Fabric Mesh 環境。
