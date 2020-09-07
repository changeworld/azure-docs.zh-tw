---
title: 快速入門 - 設定 Azure Spring Cloud 設定伺服器
description: 說明如何設定用於部署應用程式的 Azure Spring Cloud Config Server。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 47f74b551919177b13f5a72d6eedeae3c77b9f14
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951658"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>快速入門：設定 Azure Spring Cloud 設定伺服器

Azure Spring Cloud Config Server 是分散式系統的集中式設定服務。 其會使用目前支援本機儲存體、Git 和 Subversion 的可插入存放庫層。  設定 Config Server 以將微服務應用程式部署至 Azure Spring Cloud。

## <a name="prerequisites"></a>先決條件

* [安裝 JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [註冊 Azure 訂用帳戶](https://azure.microsoft.com/free/)
* (選擇性) [安裝 Azure CLI 版本 2.0.67 或更高版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，並使用下列命令安裝 Azure Spring Cloud 擴充功能：`az extension add --name spring-cloud`
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

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [建置和部署應用程式](spring-cloud-quickstart-deploy-apps.md)
