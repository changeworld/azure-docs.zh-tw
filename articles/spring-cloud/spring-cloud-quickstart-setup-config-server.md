---
title: 快速入門 - 設定 Azure Spring Cloud 設定伺服器
description: 說明如何設定用於部署應用程式的 Azure Spring Cloud Config Server。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 639672bdeff2f833c280a041e497197286c9ff24
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885698"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>快速入門：設定 Azure Spring Cloud 設定伺服器

Azure Spring Cloud Config Server 是分散式系統的集中式設定服務。 其會使用目前支援本機儲存體、Git 和 Subversion 的可插入存放庫層。 在本快速入門中，您會設定「設定伺服器」以從 Git 存放庫取得資料。

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>先決條件

* 完成此系列的上一個快速入門：[佈建 Azure Spring Cloud 服務](spring-cloud-quickstart-provision-service-instance.md)。

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure Spring Cloud Config Server 程序

執行下列命令，使用專案的 Git 存放庫位置來設定您的 config-server。 將 `<service instance name>` 取代為您先前建立的服務名稱。 您在前面的快速入門中設定的服務執行個體名稱預設值無法用於此命令。

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples --search-paths steeltoe-sample/config
```

此命令會告知設定伺服器在範例應用程式存放庫的 [steeltoe-sample/config](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample/config) 資料夾中尋找設定資料。 因為會取得設定資料的應用程式名稱是 `planet-weather-provider`，所以將使用的檔案是 [planet-weather-provider.yml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/steeltoe-sample/config/planet-weather-provider.yml)。

::: zone-end

::: zone pivot="programming-language-java"
Azure Spring Cloud Config Server 是分散式系統的集中式設定服務。 其會使用目前支援本機儲存體、Git 和 Subversion 的可插入存放庫層。  設定 Config Server 以將微服務應用程式部署至 Azure Spring Cloud。

## <a name="prerequisites"></a>先決條件

* [安裝 JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)
* [註冊 Azure 訂用帳戶](https://azure.microsoft.com/free/)
* (選擇性) [安裝 Azure CLI 版本 2.0.67 或更高版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)，並使用下列命令安裝 Azure Spring Cloud 擴充功能：`az extension add --name spring-cloud`
* (選擇性) [安裝 Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) 並[登入](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure Spring Cloud Config Server 程序

#### <a name="portal"></a>[入口網站](#tab/Azure-portal)

下列程序會使用 Azure 入口網站來設定 Config Server，以部署 [Piggymetrics 範例](spring-cloud-quickstart-sample-app-introduction.md)。

1. 移至服務 [概觀] 頁面並選取 [設定伺服器]。

2. 在 [預設存放庫] 區段中，將 [URI] 設定為 [https://github.com/Azure-Samples/piggymetrics-config ]。

3. 選取 [套用] 以儲存變更。

    ![ASC 入口網站的螢幕擷取畫面](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

下列程序會使用 Azure CLI 來設定 Config Server，以部署 [Piggymetrics 範例](spring-cloud-quickstart-sample-app-introduction.md)。

使用專案的 Git 存放庫位置來設定您的 config-server：

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

---
::: zone-end

## <a name="clean-up-resources"></a>清除資源

如果您想要繼續進行本系列的下一個快速入門，請略過此步驟。

在這些快速入門中，您已建立 Azure 資源，這些資源如果留在訂用帳戶中，將會繼續產生費用。 如果您不打算繼續進行下一個快速入門，而且未來也不會用到這些資源，則請使用入口網站或在 Cloud Shell 中執行下列命令來刪除資源群組：

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

在先前的快速入門中，您也設定了預設的資源群組名稱。 如果您不打算繼續進行下一個快速入門，則請執行下列 CLI 命令來清除該預設值：

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置和部署應用程式](spring-cloud-quickstart-deploy-apps.md)
