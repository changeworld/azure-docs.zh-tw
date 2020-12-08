---
title: 在 Linux 上使用自訂映像建立 Azure Functions
description: 了解如何建立在自訂 Linux 映像上執行的 Azure Functions。
ms.date: 03/30/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: af63eb68ec82a0725befed723298c079e82bdfdb
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327095"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>在 Linux 上使用自訂容器建立函式

在本教學課程中，您會使用 Linux 基礎映像，建立您的程式碼並將其部署至 Azure Functions 作為自訂 Docker 容器。 當您的函式需要特定的語言版本，或有內建映像所未提供的特定相依性或組態時，您通常會想使用自訂映像。

在自訂 Linux 容器中部署您的函式程式碼需要[進階方案](functions-premium-plan.md#features)或[專用 (App Service) 方案](functions-scale.md#app-service-plan)裝載。 完成本教學課程會在您的 Azure 帳戶中產生費用 (以美元計價)，您可以在完成時[清除資源](#clean-up-resources)，將這些成本降到最低。

您也可以如[建立您在 Linux 上託管的第一個函式](./create-first-function-cli-csharp.md?pivots=programming-language-python)所述，使用預設 Azure App Service 容器。 在 [Azure Functions 基底映像存放庫](https://hub.docker.com/_/microsoft-azure-functions-base)中可找到針對 Azure Functions 支援的基底映像。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 使用 Azure Functions Core Tools 建立函式應用程式和 Dockerfile。
> * 使用 Docker 建置自訂映像。
> * 將自訂映像發佈到容器登錄中。
> * 在 Azure 中建立函式應用程式的支援資源
> * 從 Docker Hub 部署函式應用程式。
> * 將應用程式設定加入函式應用程式。
> * 啟用持續部署。
> * 啟用容器的 SSH 連線。
> * 新增佇列儲存體輸出繫結。 

您可以在任何執行 Windows、macOS 或 Linux 的電腦上遵循此教學課程。 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ [Docker 識別碼](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ 執行 `docker login` 以登入 Docker。 如果 Docker 並未執行，此命令就會失敗，在此情況下，請啟動 Docker 並重試命令。

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>建立和測試本機 Functions 專案

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
在終端機或命令提示字元中，針對您所選擇的語言執行下列命令，以在名為 `LocalFunctionsProject` 的資料夾中建立函式應用程式專案。  
::: zone-end  
::: zone pivot="programming-language-csharp"  
```console
func init LocalFunctionsProject --worker-runtime dotnet --docker
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```console
func init LocalFunctionsProject --worker-runtime node --language javascript --docker
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```console
func init LocalFunctionsProject --worker-runtime powershell --docker
```
::: zone-end  
::: zone pivot="programming-language-python"  
```console
func init LocalFunctionsProject --worker-runtime python --docker
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
func init LocalFunctionsProject --worker-runtime node --language typescript --docker
```
::: zone-end
::: zone pivot="programming-language-java"  
在空的資料夾中，執行下列命令以從 [Maven 原型](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) \(英文\) 產生 Functions 專案。

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8 -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
---

`-DjavaVersion` 參數會告知函式執行階段要使用哪個 JAVA 版本。 如果您希望函式在 Java 11 上執行，請使用 `-DjavaVersion=11`。 若未指定 `-DjavaVersion`，Maven 預設為 JAVA 8。 如需詳細資訊，請參閱 [JAVA 版本](functions-reference-java.md#java-versions)。

> [!IMPORTANT]
> `JAVA_HOME` 環境變數必須設定為正確 JDK 版本的安裝位置，才能完成本文。

Maven 會要求您提供在部署時完成產生專案所需的值。   
當系統提示時，提供下列值：

| Prompt | 值 | 描述 |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | 此值可在所有專案中唯一識別您的專案，並遵循適用於 Java 的[套件命名規則](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7)。 |
| **artifactId** | `fabrikam-functions` | 此值是 jar 的名稱 (不含版本號碼)。 |
| **version** | `1.0-SNAPSHOT` | 選擇預設值。 |
| **套件** | `com.fabrikam.functions` | 此值是所產生函式程式碼的 Java 套件。 使用預設值。 |

輸入 `Y` 或按 Enter 進行確認。

Maven 會以 _artifactId_ 名稱在新資料夾中建立專案檔案，在此例中為 `fabrikam-functions`。 
::: zone-end
`--docker` 選項會產生專案的 `Dockerfile`，這會定義適合用於 Azure Functions 和所選執行階段的自訂容器。

瀏覽至專案資料夾：
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```console
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
使用下列命令，將函式新增至您的專案，其中 `--name` 引數是函式的唯一名稱，而 `--template` 引數可指定函式的觸發程序。 `func new` 建立符合函式名稱的子資料夾，其中包含適合專案所選語言的程式碼檔案，以及名為 *function.json* 的組態檔。

```console
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end  
若要在本機測試函式，請啟動專案根資料夾中的本機 Azure Functions 執行階段主機： 
::: zone pivot="programming-language-csharp"  
```console
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
mvn clean package  
mvn azure-functions:run
```
::: zone-end
一旦看到 `HttpExample` 端點出現在輸出中，請瀏覽至 `http://localhost:7071/api/HttpExample?name=Functions`。 瀏覽器應該會顯示 "hello" 訊息，回應 `Functions`，這是提供給 `name` 查詢參數的值。

使用 **Ctrl**-**C** 將主機停止。

## <a name="build-the-container-image-and-test-locally"></a>建立容器映像並在本機進行測試

(選用) 檢查專案根資料夾中的 *Dockerfile*。 Dockerfile 描述在 Linux 上執行函式應用程式所需的環境。  在 [Azure Functions 基底映像頁面](https://hub.docker.com/_/microsoft-azure-functions-base)中可找到針對 Azure Functions 支援的完整基底映像清單。
    
在根專案資料夾中，執行 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 命令，然後提供名稱、`azurefunctionsimage` 和標記 (`v1.0.0`)。 將 `<DOCKER_ID>` 取代為 Docker Hub 帳戶識別碼。 此命令會建置容器的 Docker 映像。

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

當命令完成時，您就能在本機執行新容器。
    
若要測試組建，請使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令在本機容器中執行映像，再以您的 Docker ID 取代 `<DOCKER_ID` 並新增連接埠引數 `-p 8080:80`：

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
映像一旦在本機容器中執行，請將瀏覽器開啟至 `http://localhost:8080`，這應會顯示如下所示的預留位置影像。 此影像會在此時出現，因為您的函式是在本機容器中執行，就像在 Azure 中一樣，這表示其利用 `"authLevel": "function"` 屬性受到 *function.json* 中所定義的存取金鑰保護。 然而，容器尚未在 Azure 中發佈至函式應用程式，因此還沒有可用的金鑰。 如果您想針對本機容器進行測試，請停止 Docker、將授權屬性變更為 `"authLevel": "anonymous"`、重建映像，然後重新啟動 Docker。 然後在 *function.json* 中重設 `"authLevel": "function"`。 如需詳細資訊，請參閱[授權金鑰](functions-bindings-http-webhook-trigger.md#authorization-keys)。

![表示容器正在本機執行的預留位置影像](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
映像一旦在本機容器中執行，請瀏覽至 `http://localhost:8080/api/HttpExample?name=Functions`，這應會顯示如之前所示的相同 "hello" 訊息。 由於 Maven 原型會產生使用匿名授權的 HTTP 觸發函式，因此即使函式正在容器中執行，您仍然可以呼叫函式。 
::: zone-end  

在容器中驗證過函式應用程式容器之後，請利用 **Ctrl**+**C** 停止 Docker。

## <a name="push-the-image-to-docker-hub"></a>將映像推送至 Docker 中樞

Docker Hub 是一個容器登錄，其裝載映像並提供映像和容器服務。 若要共用您的映像 (包括部署至 Azure)，您必須將其推送到登錄。

1. 如果您尚未登入 Docker，請使用 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令，將 `<docker_id>` 取代為您的 Docker 識別碼。 此命令會提示您輸入使用者名稱和密碼。 「登入成功」訊息會確認您已登入。

    ```console
    docker login
    ```
    
1. 登入之後，使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 命令將映像推送到 Docker Hub，再次將 `<docker_id>` 取代為您的 Docker 識別碼。

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. 根據您的網路速度而定，第一次推送映像可能需要幾分鐘的時間 (推送後續變更的速度會更快)。 在等候時，您可以繼續進行下一節，並在另一個終端機中建立 Azure 資源。

## <a name="create-supporting-azure-resources-for-your-function"></a>為您的函式建立支援的 Azure 資源

若要將您的函式程式碼部署至 Azure，您需要建立三個資源：

- 資源群組，這是相關資源的邏輯容器。
- Azure 儲存體帳戶，其可維護專案的狀態和其他資訊。
- Azure 函式應用程式，其可提供環境來執行函式程式碼。 函式應用程式可對應至您的本機函式專案，並可讓您將函式分組為邏輯單位，以便管理、部署和共用資源。

您可以使用 Azure CLI 命令來建立這些項目。 每個命令都會在完成時提供 JSON 輸出。

1. 使用 [az login](/cli/azure/reference-index#az-login) 命令登入 Azure：

    ```azurecli
    az login
    ```
    
1. 使用 [az group create](/cli/azure/group#az-group-create) 命令來建立資源群組。 下列範例會在 `westeurope` 區域建立名為 `AzureFunctionsContainers-rg` 的資源群組。 (您通常會使用來自 `az account list-locations` 命令的可用區域，在您附近的區域中建立資源群組和資源。)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > 您無法在相同的資源群組中裝載 Linux 和 Windows 應用程式。 如果您有名為 `AzureFunctionsContainers-rg` 的現有資源群組，且其中包含 Windows 函式應用程式或 Web 應用程式，則必須使用不同的資源群組。
    
1. 使用 [az storage account create](/cli/azure/storage/account#az-storage-account-create) 命令，在您的資源群組和區域中建立一般用途的儲存體帳戶。 在下列範例中，使用適合您的全域唯一名稱取代 `<storage_name>`。 名稱只能包含 3 到 24 個字元的數字和小寫字母。 `Standard_LRS` 可指定典型的一般用途帳戶。

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    在本教學課程中，儲存體帳戶只會產生幾美分的費用。
    
1. 使用命令，針對 [彈性進階 1] 定價層 (`--sku EP1`)、西歐區域 (`-location westeurope` 或使用您附近的適當區域) 和 Linux 容器 (`--is-linux`) 中名為 `myPremiumPlan` 的 Azure Functions 建立進階方案。

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    我們在此使用進階方案，其可視需要進行調整。 若要深入了解裝載，請參閱 [Azure Functions 裝載方案比較](functions-scale.md)。 若要計算成本，請參閱 [Functions 定價頁面](https://azure.microsoft.com/pricing/details/functions/)。

    此命令也會在相同的資源群組中佈建相關聯的 Azure Application Insights 執行個體，您可將其用於監視函式應用程式和檢視記錄。 如需詳細資訊，請參閱[監視 Azure Functions](functions-monitoring.md)。 在您啟用此執行個體之前，並不會產生任何成本。

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>使用映像在 Azure 上建立及設定函式應用程式

函式應用程式可管理您主控方案中函式的執行。 在這一節中，您會使用上一節的 Azure 資源，從 Docker Hub 上的映像建立函式應用程式，並使用 Azure 儲存體的連接字串加以設定。

1. 使用 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 命令來建立 Functions 應用程式。 在下列範例中，請以您在上一節中用於儲存體帳戶的名稱取代 `<storage_name>`。 此外，使用適合您的全域唯一名稱取代 `<app_name>`，並以您的 Docker 識別碼取代 `<docker_id>`。

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime <functions runtime stack> --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    *deployment-container-image-name* 參數可指定要用於函式應用程式的映像。 您可使用 [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) 命令來檢視部署所用映像的相關資訊。 您也可使用 [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) 命令，從不同映像進行部署。

1. 使用 [az storage account show-connection-string](/cli/azure/storage/account) 命令，擷取您所建立儲存體帳戶的連接字串，並將其指派給殼層變數 `storageConnectionString`：

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. 使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 命令，將此設定新增至函式應用程式。 在下列命令中，將 `<app_name>` 取代為函式應用程式的名稱，並將 `<connection_string>` 取代為上一個步驟中的連接字串 (以 "DefaultEndpointProtocol=" 開頭的長編碼字串)：
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. 函式現在可以使用此連接字串來存取儲存體帳戶。

    > [!TIP]
    > 在 Bash 中，您可以使用殼層變數 (而不是使用剪貼簿) 來擷取連接字串。 首先，使用下列命令來建立具有連接字串的變數：
    > 
    > ```bash
    > storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
    > ```
    > 
    > 然後參考第二個命令中的變數：
    > 
    > ```azurecli
    > az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
    > ```

> [!NOTE]    
> 如果您將自訂映像發佈至私人容器帳戶，您應該改為在連接字串的 Dockerfile 中使用環境變數。 如需詳細資訊，請參閱 [ENV 指示](https://docs.docker.com/engine/reference/builder/#env)。 您也應該設定變數 `DOCKER_REGISTRY_SERVER_USERNAME` 和 `DOCKER_REGISTRY_SERVER_PASSWORD`。 若要使用這些值，您必須重建映像、將映像推送至登錄，然後在 Azure 上重新啟動函式應用程式。

## <a name="verify-your-functions-on-azure"></a>在 Azure 上驗證您的函式

將映像部署至 Azure 上的函式應用程式後，您現在可以透過 HTTP 要求叫用函式。 因為 *function.json* 定義包含屬性 `"authLevel": "function"`，所以您必須先取得存取金鑰 (也稱為「函式金鑰」)，並將其當作 URL 參數包含在端點的任何要求中。

1. 使用 Azure 入口網站，或使用 Azure CLI 搭配 `az rest` 命令，利用存取 (函式) 金鑰擷取函式 URL。)

    # <a name="portal"></a>[入口網站](#tab/portal)

    1. 登入 Azure 入口網站，然後搜尋並選取 [函式應用程式]。

    1. 選取您要驗證的函式。

    1. 在左側導覽面板中選取 [函式]，然後選取您要驗證的函式。

        ![選擇 Azure 入口網站中的函式](./media/functions-create-function-linux-custom-image/functions-portal-select-function.png)   

    
    1. 選取 [取得函式 URL]。

        ![從 Azure 入口網站取得函式 URL](./media/functions-create-function-linux-custom-image/functions-portal-get-function-url.png)   

    
    1. 在快顯視窗中選取 [預設 (函式金鑰)]，然後將 URL 複製到剪貼簿。 此金鑰是緊接在 `?code=` 後面的字元字串。

        ![選擇預設函式存取金鑰](./media/functions-create-function-linux-custom-image/functions-portal-copy-url.png)   


    > [!NOTE]  
    > 因為您的函數應用程式會部署為容器，所以，您無法在入口網站中對函式程式碼進行變更。 您必須改為更新本機映像中的專案、再次將映像推送至登錄，然後重新部署至 Azure。 您可以在後面的章節中設定持續部署。
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

    1. 以下列格式建構 URL 字串，並分別以您的 Azure 訂用帳戶識別碼、函式應用程式的資源群組和函式應用程式的名稱取代 `<subscription_id>`、`<resource_group>` 和 `<app_name>`：

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        例如，URL 可能如下列位址所示：

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > 為了方便起見，您可改為將 URL 指派給環境變數，並在 `az rest` 命令中加以使用。
    
    1. 執行下列 `az rest` 命令 (可在 Azure CLI 2.0.77 版和更新版本中取得)，將 `<uri>` 取代為最後一個步驟中的 URI 字串，包括引號：

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. 命令的輸出為函式金鑰。 然後完整的函式 URL 為 `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`，並以您特定的值取代 `<app_name>`、`<function_name>` 和 `<key>`。
    
        > [!NOTE]
        > 此處所擷取的金鑰是「主機」金鑰，其適用於函式應用程式中的所有函式；針對入口網站顯示的方法只會擷取一個函式的金鑰。

    ---

1. 將函式 URL 貼入瀏覽器的網址列中，將 `&name=Azure` 參數新增至此 URL 的結尾。 "Hello Azure" 之類的文字應會出現在瀏覽器中。

    ![瀏覽器中的函式回應。](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. 若要測試授權，請移除 URL 中的 `code=` 參數，並確認您不會取得來自函式的回應。


## <a name="enable-continuous-deployment-to-azure"></a>啟用持續部署至 Azure

您可以在每次更新登錄中的映像時，讓 Azure Functions 自動更新您的映像部署。

1. 使用 [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) 命令來啟用持續部署，並以您的函式應用程式名稱取代 `<app_name>`：

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    此命令會啟用持續部署並傳回部署 Webhook URL。 (您可以使用 [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) 命令稍後擷取此 URL。)

1. 將部署 Webhook URL 複製到剪貼簿。

1. 開啟 [Docker Hub](https://hub.docker.com/)、登入，然後選取導覽列上的 [存放庫]。 找出並選取映像，選取 [Webhook] 索引標籤、指定 [Webhook 名稱]、在 [Webhook URL] 中貼入您的 URL，然後選取 [建立]：

    ![在 DockerHub 存放庫中新增 Webhook](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. 設定 Webhook 後，每當您在 Docker Hub 中更新映像時，Azure Functions 就會重新部署您的映像。

## <a name="enable-ssh-connections"></a>啟用 SSH 連線

SSH 可讓容器和用戶端之間進行安全通訊。 啟用 SSH 之後，您就能使用 App Service 進階工具 (Kudu) 連線到您的容器。 為了讓您可以輕鬆地使用 SSH 連線到容器，Azure Functions 會提供已經啟用 SSH 的基底映像。 您只需要編輯 Dockerfile，然後重建並重新部署映像。 接著，您可以透過進階工具 (Kudu) 連線到容器

1. 在您的 Dockerfile 中，將字串 `-appservice` 附加至 `FROM` 指令中的基底映像：

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end
    
1. 再次使用 `docker build` 命令重建映像，並以您的 Docker ID 取代 `<docker_id>`：

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. 將已更新的映像推送至 Docker Hub，這應該比第一次只推送所需上傳的映像更新後區段少很多時間。

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions 會自動將映像重新部署至您的函式應用程式；此程序所需的時間不到一分鐘。

1. 在瀏覽器中，開啟 `https://<app_name>.scm.azurewebsites.net/`，以您的唯一名稱取代 `<app_name>`。 此 URL 是您函式應用程式容器的進階工具(Kudu) 端點。

1. 登入您的 Azure 帳戶，然後選取 [SSH]，以建立與容器的連線。 如果 Azure 仍在更新容器映像，連線可能需要幾分鐘的時間。

1. 建立與容器的連線之後，執行 `top` 命令來檢視目前執行中的程序。 

    ![在 SSH 工作階段中執行的 Linux top 命令](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>寫入至 Azure 儲存體佇列

Azure Functions 可讓您無須撰寫自己的整合程式碼，就能將函式連線到其他 Azure 服務與資源。 這些 *繫結* 同時代表輸入和輸出，會宣告於函式定義內。 繫結中的資料會提供給函式作為參數。 「觸發程序」是一種特殊的輸入繫結。 雖然函式只有一個觸發程序，但可以有多個輸入和輸出繫結。 若要深入了解，請參閱 [Azure Functions 觸發程序和繫結概念](functions-triggers-bindings.md)。

本節說明如何將您的函式與 Azure 儲存體佇列整合。 您新增至此函式的輸出繫結，會將資料從 HTTP 要求寫入至佇列中的訊息。

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

## <a name="add-code-to-use-the-output-binding"></a>新增程式碼以使用輸出繫結

定義了佇列繫結後，您現在可以更新函式以接收 `msg` 輸出參數，並將訊息寫入至佇列。

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

### <a name="update-the-image-in-the-registry"></a>更新登錄中的映像

1. 在根資料夾中，再次執行 `docker build`，這次會將標記中的版本更新為 `v1.0.1`。 和之前一樣，以您的 Docker Hub 帳戶識別碼取代 `<docker_id>`：

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. 使用 `docker push` 將已更新的映像推送回存放庫：

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. 因為您設定了持續傳遞，所以再次更新登錄中的映像，會自動在 Azure 中更新您的函式應用程式。

## <a name="view-the-message-in-the-azure-storage-queue"></a>檢視 Azure 儲存體佇列中的訊息

在瀏覽器中，使用與之前相同的 URL 來叫用您的函式。 瀏覽器應會顯示與之前相同的回應，因為您未修改該部分的函式程式碼。 不過，新增的程式碼會使用 `name` URL 參數，將訊息寫入 `outqueue` 儲存體佇列。

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="clean-up-resources"></a>清除資源

如果您想使用您在本教學課程中建立的資源來繼續使用 Azure 函式，您可以保留所有這些資源。 由於您已建立 Azure Functions 的進階方案，因此您會每天持續產生一或兩美元的成本。

若要避免持續產生成本，請刪除 `AzureFunctionsContainer-rg` 資源群組，以清除該群組中的所有資源： 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>後續步驟

+ [監視函式](functions-monitoring.md)
+ [調整和裝載選項](functions-scale.md)
+ [以 Kubernetes 為基礎的無伺服器裝載](functions-kubernetes-keda.md)