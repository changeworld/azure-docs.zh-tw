---
title: 快速入門 - 使用 Maven 和 Jib 建置 Java 容器映像並將其推送至 Azure Container Registry
description: 建置容器化 Java 應用程式，並使用 Maven Jib 外掛程式將其推送至 Azure Container Registry。
author: KarlErickson
ms.custom: devx-track-java, devx-track-azurecli
ms.author: karler
ms.topic: quickstart
ms.date: 02/26/2020
ms.openlocfilehash: e87e2d59dd70fbf96c04d9cd467594aa6576639b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501299"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>快速入門：建置 Java 容器映像並將其推送至 Azure Container Registry

本快速入門說明如何建置容器化 Java 應用程式，並使用 Maven Jib 外掛程式將其推送至 Azure Container Registry。 使用 Maven 和 Jib 是使用開發人員工具來與 Azure Container Registry 互動的其中一個範例。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶；如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或註冊[免費的 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial)。
* [Azure 命令列介面 (CLI)](/cli/azure/overview)。
* 受支援的 Java 開發套件 (JDK)。 如需在 Azure 上進行開發時可使用的 JDK 相關資訊，請參閱 <https://aka.ms/azure-jdks>。
* Apache 的 [Maven](http://maven.apache.org) 建置工具 (第 3 版或以上)。
* [Git](https://git-scm.com) 用戶端。
* [Docker](https://www.docker.com) 用戶端。
* [ACR Docker 認證協助程式](https://github.com/Azure/acr-docker-credential-helper)。

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>建立 Spring Boot on Docker Getting Started Web 應用程式

下列步驟會引導您建置 Spring Boot Web 應用程式，並在本機加以測試。

1. 從命令提示字元，使用下列命令來複製 [Docker 上的 Spring Boot 使用者入門](https://github.com/spring-guides/gs-spring-boot-docker)範例專案。

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. 將目錄變更至已完成的專案。

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. 使用 Maven 來建置及執行範例應用程式。

   ```bash
   mvn package spring-boot:run
   ```

1. 瀏覽至 `http://localhost:8080` 來測試 Web 應用程式，或使用下列 `curl` 命令：

   ```bash
   curl http://localhost:8080
   ```

您應該會看到下列訊息：**Hello Docker World**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>使用 Azure CLI 建立 Azure Container Registry

接下來，您將使用下列步驟來建立 Azure 資源群組和您的 ACR：

1. 使用下列命令來登入您的 Azure 帳戶：

   ```azurecli
   az login
   ```

1. 指定要使用的 Azure 訂用帳戶：

   ```azurecli
   az account set -s <subscription ID>
   ```

1. 為此教學課程中使用的 Azure 資源建立資源群組。 在下列命令中，請務必以自己的資源名稱和位置取代預留位置，例如 `eastus`。

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. 使用下列命令，在資源群組中建立私人 Azure Container Registry。 請務必以實際值取代預留位置。 本教學課程會在稍後的步驟中，將範例應用程式推送為此登錄的 Docker 映像。

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>透過 Jib 將應用程式推送至容器登錄

最後，您將更新您的專案組態，並使用命令提示字元來建置和部署您的映像。

> [!NOTE]
> 若要登入您剛建立的 Azure Container Registry，您必須執行 Docker 精靈。 若要在電腦上安裝 Docker，[這裡是官方的 Docker 文件](https://docs.docker.com/install/)。

1. 使用下列命令，從 Azure CLI 登入您的 Azure Container Registry。 請務必將以自己的登錄名稱取代預留位置。

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   `az configure` 命令會設定要搭配 `az acr` 命令使用的預設登錄名稱。

1. 巡覽至 Spring Boot 應用程式已完成的專案目錄 (例如，"*C:\SpringBoot\gs-spring-boot-docker\complete*" 或 " */users/robert/SpringBoot/gs-spring-boot-docker/complete*")，並使用文字編輯器開啟 *pom.xml* 檔案。

1. 以下列 XML 更新 *pom.xml* 檔案中的 `<properties>` 集合。 請以您的登錄名稱取代預留位置，並新增值為 `2.2.0` 的 `<jib-maven-plugin.version>` 屬性，或新增較新版的 [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin)。

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <jib-maven-plugin.version>2.2.0</jib-maven-plugin.version>
   </properties>
   ```

1. 更新 *pom.xml* 檔案中的 `<plugins>` 集合，讓 `<plugin>` 元素包含 `jib-maven-plugin` 項目，如下列範例所示。 請注意，我們會使用 Microsoft Container Registry (MCR) 中的基底映像：`mcr.microsoft.com/java/jdk:8-zulu-alpine`，其中包含適用於 Azure 的正式支援 JDK。 如需其他具有正式支援 JDK 的 MCR 基底映像，請參閱 [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)、[Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)、[Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless) 及 [Java SE JDK 和 Maven](https://hub.docker.com/_/microsoft-java-maven)。

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. 瀏覽至 Spring Boot 應用程式已完成的專案目錄，然後執行下列命令來建置映像，並將映像推送到登錄：

   ```bash
   az acr login && mvn compile jib:build
   ```

> [!NOTE]
>
> 基於安全性理由，`az acr login` 所建立的認證僅在 1 小時內有效。 如果您收到「401 未授權」  錯誤，您可以再次執行 `az acr login -n <your registry name>` 命令重新進行驗證。

## <a name="verify-your-container-image"></a>驗證容器映像

恭喜！ 您現已將建置於 Azure 支援 JDK 的容器化 Java 應用程式推送至您的 ACR。 您現在可藉由將映像部署到 Azure App Service，或使用命令將其提取至本機 (取代預留位置)，以測試映像：

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker
```

## <a name="next-steps"></a>後續步驟

如需其他版本的正式 Microsoft 支援 Java 基底映像，請參閱：

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK 和 Maven](https://hub.docker.com/_/microsoft-java-maven)

若要深入了解 Spring 和 Azure，請繼續閱讀「Azure 上的 Spring」文件中心中的資訊。

> [!div class="nextstepaction"]
> [Azure 上的 Spring](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>其他資源

如需詳細資訊，請參閱下列資源：

* [適用於 Java 開發人員的 Azure](/azure/java)
* [使用 Azure DevOps 和 Java](/azure/devops/java)
* [Spring Boot on Docker Getting Started](https://spring.io/guides/gs/spring-boot-docker)
* [Spring Initializr](https://start.spring.io)
* [將 Spring Boot 應用程式部署到 Azure App Service](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin)
* [針對 Linux 上的 Azure Web 應用程式使用自訂 Docker 映像](../app-service/containers/tutorial-custom-docker-image.md)
