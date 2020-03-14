---
title: 設定 Azure Service Fabric 網格 CLI
description: 必須使用 Service Fabric Mesh 命令列介面 (CLI)，才能在本機和 Azure Service Fabric Mesh 中部署及管理資源。 設定方式如下。
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 0fb65ceeabca9331130083f8ec5b3fe8acce13b8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259184"
---
# <a name="set-up-service-fabric-mesh-cli"></a>設定 Service Fabric Mesh CLI
必須使用 Service Fabric Mesh 命令列介面 (CLI)，才能在本機和 Azure Service Fabric Mesh 中部署及管理資源。 設定方式如下。

有三種類型的 CLI 可供使用，其摘要說明請見下表。

| CLI 模組 | 目標環境 |  描述 | 
|---|---|---|
| az mesh | Azure Service Fabric Mesh | 可讓您在 Azure Service Fabric Mesh 環境中部署應用程式和管理資源的主要 CLI。 
| sfctl | 本機叢集 | 可讓您對本機叢集部署及測試 Service Fabric 資源的 Service Fabric CLI。  
| Maven CLI | 本機叢集和 Azure Service Fabric Mesh | `az mesh` 和 `sfctl` 的包裝函式，可讓 JAVA 開發人員使用熟悉的命令列體驗來進行本機和 Azure 開發體驗。  

針對預覽版本，Azure Service Fabric Mesh CLI 是以 Azure CLI 擴充功能的形式撰寫。 您可以將其安裝在 Azure Cloud Shell 或 Azure CLI 的本機安裝上。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>安裝 Azure Service Fabric Mesh CLI
1. 您必須安裝 Azure CLI 版2.0.67 或更新版本。 執行 `az --version` 以尋找版本。 若要安裝或升級至最新版本的 CLI，請參閱[安裝 Azure CLI][azure-cli-install]。

2. 使用下列命令安裝 Azure Service Fabric Mesh CLI 擴充功能模組。 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. 使用下列命令更新現有的 Azure Service Fabric Mesh CLI 模組。

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>安裝 Service Fabric CLI (sfctl) 

請依照[設定 Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) 的指示操作。 **Sfctl** 模組可讓您根據資源模型，對本機電腦上的 Service Fabric 叢集部署應用程式。 

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
