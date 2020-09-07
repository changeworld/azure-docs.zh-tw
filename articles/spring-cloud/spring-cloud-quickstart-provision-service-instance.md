---
title: 快速入門 - 佈建 Azure Spring Cloud 服務
description: 說明如何建立用於部署應用程式的 Azure Spring Cloud 服務執行個體。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: c91237e3a14c60e477f58be0bf62f634b462960b
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951669"
---
# <a name="quickstart-provision-azure-spring-cloud-service"></a>快速入門：佈建 Azure Spring Cloud 服務

您可以使用 Azure 入口網站或 Azure CLI 來具現化 Azure Spring Cloud。  下列程序會說明這兩種方法。
## <a name="prerequisites"></a>先決條件

* [安裝 JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [註冊 Azure 訂用帳戶](https://azure.microsoft.com/free/)
* (選擇性) [安裝 Azure CLI 版本 2.0.67 或更高版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，並使用下列命令安裝 Azure Spring Cloud 擴充功能：`az extension add --name spring-cloud`
* (選擇性) [安裝 Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) 並[登入](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="provision-an-instance-of-azure-spring-cloud"></a>佈建 Azure Spring Cloud 的執行個體

#### <a name="portal"></a>[入口網站](#tab/Azure-portal)

下列程序會使用 Azure 入口網站來建立 Azure Spring Cloud 的執行個體。

1. 在新的索引標籤中，開啟 [Azure 入口網站](https://ms.portal.azure.com/)。 

2. 從頂端的搜尋方塊，搜尋 **Azure Spring Cloud**。

3. 從結果中選取 **Azure Spring Cloud**。

    ![ASC 圖示啟動](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. 在 Azure Spring Cloud 頁面上，按一下 [+ 新增]。

    ![ASC 圖示新增](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. 填寫 Azure Spring Cloud [建立] 頁面上的表單。  請參考下列指引：
    - 訂用帳戶：選取您想要用來支付此資源費用的訂用帳戶。
    - **資源群組**：為新資源建立新資源群組是最佳做法。 請注意，稍後將會使用該資訊作為 **\<resource group name\>** 。
    - **服務詳細資料/名稱**：指定 **\<service instance name\>** 。  名稱長度必須為 4 到 32 個字元，且只能包含小寫字母、數字及連字號。  服務名稱的第一個字元必須是字母，且最後一個字元必須是字母或數字。
    - **位置**：選取您服務執行個體的位置。

    ![ASC 入口網站啟動](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. 按一下 [檢閱及建立]。

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

下列程序會使用 Azure CLI 擴充功能來佈建 Azure Spring Cloud 的執行個體。

1. 登入 Azure CLI 並選擇您的有效定用帳戶。 請務必選擇已加入 Azure Spring Cloud 白清單的有效訂用帳戶

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. 為您的 Azure Spring Cloud 服務準備名稱。  名稱長度必須為 4 到 32 個字元，且只能包含小寫字母、數字及連字號。  服務名稱的第一個字元必須是字母，且最後一個字元必須是字母或數字。

1. 建立資源群組來包含您的 Azure Spring Cloud 服務。

    ```azurecli
    az group create --location eastus --name <resource group name>
    ```

    深入了解 [Azure 資源群組](../azure-resource-manager/management/overview.md)。

1. 開啟 Azure CLI 視窗並執行下列命令來佈建 Azure Spring Cloud 的執行個體。

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    服務執行個體大約需要五分鐘的時間來完成部署。
---

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [設定組態伺服器](spring-cloud-quickstart-setup-config-server.md)


