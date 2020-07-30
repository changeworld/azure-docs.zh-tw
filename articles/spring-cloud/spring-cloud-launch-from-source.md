---
title: 快速入門 - 從原始程式碼啟動 Spring Cloud 應用程式
description: 在本快速入門中，您將了解如何直接從原始程式碼啟動 Azure Spring Cloud 應用程式
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 7057138709f97c2eba2b2301d8363e2524a2042a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87071100"
---
# <a name="quickstart-launch-your-spring-cloud-application-from-source-code"></a>快速入門：從原始程式碼啟動 Spring Cloud 應用程式

Azure Spring Cloud 可讓您輕鬆地在 Azure 上執行以 Spring Cloud 為基礎的微服務應用程式。

Azure Spring Cloud 可讓您直接從 java 原始程式碼或預先建置的 JAR 啟動應用程式。 此文章將引導您完成所需的步驟。

透過遵循本快速入門，您將能了解如何：

> [!div class="checklist"]
> * 佈建服務執行個體
> * 設定執行個體的設定伺服器
> * 於本機建置微服務應用程式
> * 部署每個微服務
> * 為您的應用程式指派公用端點

## <a name="prerequisites"></a>Prerequisites

>[!Note]
> Azure Spring Cloud 目前提供公開預覽版。 公開預覽版供應項目可讓客戶在其正式發行前，先試驗新功能。  公開預覽功能和服務不適用於生產環境。  如需有關預覽期間支援的詳細資訊，請參閱我們的[常見問題集](https://azure.microsoft.com/support/faq/)或提出[支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)以深入了解。

開始之前，請確定您的 Azure 訂用帳戶具有必要的相依性：

1. [安裝 Git](https://git-scm.com/)
2. [安裝 JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [安裝 Maven 3.0 或更新版本](https://maven.apache.org/download.cgi) \(英文\)
4. [安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [註冊 Azure 訂用帳戶](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。  它已預先安裝常用的 Azure 工具，包括 Git、JDK、Maven 和 Azure CLI 的最新版本。 如果您已登入 Azure 訂用帳戶，請從 shell.azure.com 啟動您的 [Azure Cloud Shell](https://shell.azure.com) \(英文\)。  您可以[閱讀我們的文件](../cloud-shell/overview.md)，以深入了解 Azure Cloud Shell

## <a name="install-the-azure-cli-extension"></a>安裝 Azure CLI 擴充功能

使用下列命令安裝適用於 Azure CLI 的 Azure Spring Cloud 擴充功能

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>使用 Azure CLI 佈建服務執行個體

登入 Azure CLI 並選擇您的有效定用帳戶。 請務必選擇已加入 Azure Spring Cloud 白清單的有效訂用帳戶

```azurecli
az login
az account list -o table
az account set --subscription
```

建立資源群組來包含您的 Azure Spring Cloud 服務。 您可以深入了解 [Azure 資源群組](../azure-resource-manager/management/overview.md)。

```azurecli
az group create --location eastus --name <resource group name>
```

執行下列命令來佈建 Azure Spring Cloud 的執行個體。 為您的 Azure Spring Cloud 服務準備名稱。 名稱長度必須為 4 到 32 個字元，且只能包含小寫字母、數字及連字號。 服務名稱的第一個字元必須是字母，且最後一個字元必須是字母或數字。

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

服務執行個體大約需要五分鐘的時間來部署。

使用下列命令來設定預設資源群組名稱及叢集名稱：

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=provision)

## <a name="create-the-spring-cloud-application"></a>建立 Spring Cloud 應用程式

下列命令會在您的訂用帳戶中建立 Spring Cloud 應用程式。  這會建立空的 Spring Cloud 服務，讓我們可以將應用程式上傳到其中。

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>部署 Spring Cloud 應用程式

您可以從預先建置的 JAR，或從 Gradle 或 Maven 存放庫部署您的應用程式。  請在下方尋找每個案例的指示。

### <a name="deploy-a-built-jar"></a>部署建置的 JAR

若要從您本機電腦上建置的 JAR 部署，請確定您的組建會產生 [fat-JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven)。

若要將 fat-JAR 部署至作用中部署

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

若要將 fat-JAR 部署至特定部署

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>從原始程式碼部署

Azure Spring Cloud 使用 [kpack](https://github.com/pivotal/kpack) 建置您的專案。  您可以使用 Azure CLI 上傳原始程式碼、使用 kpack 建置專案，並將其部署至目標應用程式。

> [!WARNING]
> 專案必須在 `target` (適用於 Maven 部署) 或 `build/libs` (適用於 Gradle 部署) 的 `MANIFEST.MF` 中，只產生一個具有 `main-class` 項目的 JAR 檔案。  具有 `main-class` 項目的多個 JAR 檔案會造成部署失敗。

針對單一模組 Maven/Gradle 專案：

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

針對具有多個模組的 Maven/Gradle 專案，請為每個模組重複執行：

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>顯示部署記錄

使用下列命令檢閱 kpack 組建記錄檔：

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> 如果該部署是使用 kpack 從來源建立的，則 kpack 記錄只會顯示最新的部署。

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>將公用端點指派到閘道

1. 開啟 [應用程式儀表板]  頁面。
2. 選取 `gateway` 應用程式以顯示 [應用程式詳細資料]  頁面。
3. 選取 [指派網域]  來將公用端點指派到閘道。 這可能會需要幾分鐘的時間。 
4. 將已指派的公用 IP 輸入至您的瀏覽器，以檢視正在執行的應用程式。

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

> [!div class="checklist"]
> * 佈建服務執行個體
> * 設定執行個體的設定伺服器
> * 於本機建置微服務應用程式
> * 部署每個微服務
> * 編輯應用程式的環境變數
> * 為您的應用程式閘道指派公用 IP

> [!div class="nextstepaction"]
> [準備您的 Azure Spring Cloud 應用程式以進行部署](spring-cloud-tutorial-prepare-app-deployment.md)

GitHub 上可用的其他範例：[Azure Spring Cloud 範例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql)。
