---
title: 快速入門 - 佈建 Azure Spring Cloud 服務
description: 說明如何建立用於部署應用程式的 Azure Spring Cloud 服務執行個體。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 16d40c334d51a66df4a4d2d56e2fa2379dda3726
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905410"
---
# <a name="quickstart-provision-azure-spring-cloud-service"></a>快速入門：佈建 Azure Spring Cloud 服務

::: zone pivot="programming-language-csharp"
在本快速入門中，您會使用 Azure CLI 來佈建 Azure Spring Cloud 服務的執行個體。

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。 Azure Spring Cloud 服務支援 .NET Core 3.1 和更新版本。
* [Azure CLI 2.0.67 版或更新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)。
* [Git](https://git-scm.com/)。

## <a name="install-azure-cli-extension"></a>安裝 Azure CLI 擴充功能

確認您的 Azure CLI 版本為 2.0.67 或更新版本：

```azurecli
az --version
```

使用下列命令安裝適用於 Azure CLI 的 Azure Spring Cloud 擴充功能：

```azurecli
az extension add --name spring-cloud
```

## <a name="log-in-to-azure"></a>登入 Azure

1. 登入 Azure CLI。

    ```azurecli
    az login
    ```

1. 如果您有多個訂用帳戶，請選擇您想要用於本快速入門的訂用帳戶。

   ```azurecli
   az account list -o table
   ```

   ```azurecli
   az account set --subscription <Name or ID of a subscription from the last step>
   ```

## <a name="provision-an-instance-of-azure-spring-cloud"></a>佈建 Azure Spring Cloud 的執行個體

1. 建立[資源群組](../azure-resource-manager/management/overview.md)來包含您的 Azure Spring Cloud 服務。 資源群組名稱可以包含英數字元、底線、括號、連字號、句號 (結尾除外) 及 Unicode 字元。

   ```azurecli
   az group create --location eastus --name <resource group name>
   ```

1. 佈建 Azure Spring Cloud 服務的執行個體。 服務執行個體的名稱必須是唯一的、長度必須為 4 到 32 個字元，且只能包含小寫字母、數字及連字號。 服務名稱的第一個字元必須是字母，且最後一個字元必須是字母或數字。

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    此命令可能需要幾分鐘的時間才能完成。

1. 請設定預設的資源群組名稱和服務執行個體名稱，如此一來，您就不必在後續命令中重複指定這些值。

   ```azurecli
   az configure --defaults group=<resource group name>
   ```

   ```azurecli
   az configure --defaults spring-cloud=<service instance name>
   ```
::: zone-end

::: zone pivot="programming-language-java"
您可以使用 Azure 入口網站或 Azure CLI 來具現化 Azure Spring Cloud。  下列程序會說明這兩種方法。
## <a name="prerequisites"></a>先決條件

* [安裝 JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)
* [註冊 Azure 訂用帳戶](https://azure.microsoft.com/free/)
* (選擇性) [安裝 Azure CLI 版本 2.0.67 或更高版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)，並使用下列命令安裝 Azure Spring Cloud 擴充功能：`az extension add --name spring-cloud`
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

1. 登入 Azure CLI 並選擇您的有效定用帳戶。

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
::: zone-end

## <a name="clean-up-resources"></a>清除資源

如果您想要繼續進行本系列的下一個快速入門，請略過此步驟。

在本快速入門中，您已建立 Azure 資源，這些資源如果留在訂用帳戶中，將會繼續產生費用。 如果您不打算繼續進行下一個快速入門，而且未來也不會用到這些資源，則請使用入口網站或在 Cloud Shell 中執行下列命令來刪除資源群組：

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

在本快速入門中，您也設定了預設的資源群組名稱。 如果您不打算繼續進行下一個快速入門，則請執行下列 CLI 命令來清除該預設值：

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [設定組態伺服器](spring-cloud-quickstart-setup-config-server.md)
