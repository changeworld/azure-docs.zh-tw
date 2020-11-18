---
title: 設定 Azure Service Fabric 網狀 CLI
description: 必須使用 Service Fabric Mesh 命令列介面 (CLI)，才能在本機和 Azure Service Fabric Mesh 中部署及管理資源。 以下是設定它的方式。
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: ea4a7764cf1ede1cfaf53b1097034c5894660376
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660673"
---
# <a name="set-up-service-fabric-mesh-cli"></a>設定 Service Fabric Mesh CLI
必須使用 Service Fabric Mesh 命令列介面 (CLI)，才能在本機和 Azure Service Fabric Mesh 中部署及管理資源。 以下是設定它的方式。

有三種類型的 CLI 可供使用，其摘要說明請見下表。

| CLI 模組 | 目標環境 |  說明 | 
|---|---|---|
| az mesh | Azure Service Fabric Mesh | 可讓您在 Azure Service Fabric Mesh 環境中部署應用程式和管理資源的主要 CLI。 
| sfctl | 本機叢集 | 可讓您對本機叢集部署及測試 Service Fabric 資源的 Service Fabric CLI。  
| Maven CLI | 本機叢集和 Azure Service Fabric Mesh | 的包裝函式，可 `az mesh` `sfctl` 讓 JAVA 開發人員針對本機和 Azure 開發體驗使用熟悉的命令列體驗。  

針對預覽版本，Azure Service Fabric Mesh CLI 是以 Azure CLI 擴充功能的形式撰寫。 您可以將其安裝在 Azure Cloud Shell 或 Azure CLI 的本機安裝上。 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 的版本2.0.67 版或更新版本。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="install-the-azure-service-fabric-mesh-cli"></a>安裝 Azure Service Fabric Mesh CLI

如果您尚未這麼做，請使用下列命令來安裝 Azure Service Fabric 網狀 CLI 延伸模組模組： 
 
```azurecli-interactive
az extension add --name mesh
```

如果已安裝，請使用下列命令來更新您現有的 Azure Service Fabric 網狀 CLI 模組：

```azurecli-interactive
az extension update --name mesh
```

## <a name="install-the-service-fabric-cli-sfctl"></a>安裝 Service Fabric CLI (sfctl) 

請依照[設定 Service Fabric CLI](../service-fabric/service-fabric-cli.md) 的指示操作。 **Sfctl** 模組可讓您根據資源模型，對本機電腦上的 Service Fabric 叢集部署應用程式。 

## <a name="install-the-maven-cli"></a>安裝 Maven CLI 

若要使用 Maven CLI，必須在您的電腦上安裝下列項目： 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Azure Mesh CLI (az mesh) - 以 Azure Service Fabric Mesh 為目標 
* SFCTL (sfctl) - 以本機叢集為目標 

適用於 Service Fabric 的 Maven CLI 仍處於預覽狀態。 

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
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

請參閱 [Maven CLI 參考](service-fabric-mesh-reference-maven.md)一節，以了解詳細的使用方式。

## <a name="next-steps"></a>後續步驟

您也可以設定 [Windows 開發環境](service-fabric-mesh-howto-setup-developer-environment-sdk.md)。

尋找[常見問題](service-fabric-mesh-faq.md)的解答。

[azure-cli-install]: /cli/azure/install-azure-cli
