---
title: 從命令列建立 Java 函式 - Azure Functions
description: 了解如何從命令列建立 Java 函式，然後將本機專案發佈至 Azure Functions 中的無伺服器裝載。
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
ms.openlocfilehash: 5ebb30101d191bb254017db7b0de2f9b8bce145d
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755173"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>快速入門：從命令列在 Azure 中建立 Java 函式

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

在本文中，您會使用命令列工具建立可回應 HTTP 要求的 Java 函式。 在本機測試程式碼之後，您可以將其部署到 Azure Functions 的無伺服器環境。

完成本快速入門後，您的 Azure 帳戶中會產生幾美分或更少的少許費用。

> [!NOTE]
> 如果 Maven 不是您慣用的開發工具，請參閱供 Java 開發人員參考的類似教學課程，內容分別使用 [Gradle](./functions-create-first-java-gradle.md)、[IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) 和 [Visual Studio Code](create-first-function-vs-code-java.md) 等開發工具。

## <a name="configure-your-local-environment"></a>設定您的本機環境

開始之前，您必須具備下列條件：

+ 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#v2) 3.x 版。

+ [Azure CLI](/cli/azure/install-azure-cli) 2.4 版或更新版本。

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) 第 8 或第 11 版。 `JAVA_HOME` 環境變數必須設定為正確 JDK 版本的安裝位置。     

+ [Apache Maven](https://maven.apache.org) 3.0 版或更高版本。

### <a name="prerequisite-check"></a>先決條件檢查

+ 在終端機或命令視窗中執行 `func --version`，以確認 Azure Functions Core Tools 為 3.x 版。

+ 執行 `az --version` 以確認 Azure CLI 版本為 2.4 或更新版本。

+ 執行 `az login` 以登入 Azure 並驗證有效訂用帳戶。

## <a name="create-a-local-function-project"></a>建立本機函式專案

在 Azure Functions 中，函式專案是包含一或多個個別函式的容器，而每個函式分別會回應特定的觸發程序。 專案中的所有函式會共用相同的本機和裝載設定。 在本節中，您將建立包含單一函式的函式專案。

1. 在空的資料夾中，執行下列命令以從 [Maven 原型](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) \(英文\) 產生 Functions 專案。 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
    ```
    
    ---

    > [!IMPORTANT]
    > + 如果您希望函式在 Java 11 上執行，請使用 `-DjavaVersion=11`。 若要深入了解，請參閱 [Java 版本](functions-reference-java.md#java-versions)。 
    > + `JAVA_HOME` 環境變數必須設定為正確 JDK 版本的安裝位置，才能完成本文。

1. Maven 會要求您提供在部署時完成產生專案所需的值。   
    當系統提示時，提供下列值：

    | Prompt | 值 | 描述 |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | 此值可在所有專案中唯一識別您的專案，並遵循適用於 Java 的[套件命名規則](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7)。 |
    | **artifactId** | `fabrikam-functions` | 此值是 jar 的名稱 (不含版本號碼)。 |
    | **version** | `1.0-SNAPSHOT` | 選擇預設值。 |
    | **套件** | `com.fabrikam` | 此值是所產生函式程式碼的 Java 套件。 使用預設值。 |

1. 輸入 `Y` 或按 Enter 進行確認。

    Maven 會以 _artifactId_ 名稱在新資料夾中建立專案檔案，在此例中為 `fabrikam-functions`。 

1. 瀏覽至專案資料夾：

    ```console
    cd fabrikam-functions
    ```

    此資料夾會包含專案的各種檔案，包括名為 [local.settings.json](functions-run-local.md#local-settings-file) 和 [host.json](functions-host-json.md) 的組態檔。 由於 *local.settings.json* 可能會包含從 Azure 下載的秘密，因此 *.gitignore* 檔案依預設會將該檔案排除在原始檔控制以外。

### <a name="optional-examine-the-file-contents"></a>(選擇性) 檢查檔案內容

如有需要，您可以跳到[在本機執行函式](#run-the-function-locally)，並於稍後再檢查檔案內容。

#### <a name="functionjava"></a>Function.java
Function.java 包含 `run` 方法，會接收 `request` 變數中的要求資料，而該變數是一個以 [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) 註釋裝飾的 [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage)，可定義觸發程序行為。 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

回應訊息是由 [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API 所產生。

#### <a name="pomxml"></a>pom.xml

為裝載應用程式所建立的 Azure 資源設定，會定義於外掛程式的 **configuration** 元素中，並在產生的 pom.xml 檔案中使用 `com.microsoft.azure` 的 **groupId**。 例如，下方的 configuration 元素會指示以 Maven 為基礎的部署，以在 `westus` 區域的 `java-functions-group` 資源群組中建立函式應用程式。 函式應用程式本身會在 `java-functions-app-service-plan` 方案所裝載的 Windows 上執行，預設為無伺服器取用方案。

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

您可以變更這些設定，以控制在 Azure 中建立資源的方式，例如，在初始部署之前將 `runtime.os` 從 `windows` 變更為 `linux`。 如需 Maven 外掛程式支援的設定完整清單，請參閱[組態詳細資料](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details)。

#### <a name="functiontestjava"></a>FunctionTest.java

原型也會產生函式的單元測試。 變更函式以新增繫結或將新函式新增至專案時，也需要修改 FunctionTest.java 檔案中的測試。

## <a name="run-the-function-locally"></a>在本機執行函式

1. 啟動 *LocalFunctionProj* 資料夾中的本機 Azure Functions 執行階段主機，以執行您的函式：

    ```console
    mvn clean package
    mvn azure-functions:run
    ```
    
    在輸出的結尾處，應該會出現下列幾行：
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    > [!NOTE]  
    > 如果 HttpExample 未如上顯示，表示您可能不是從專案的根資料夾啟動主機。 在此情況下，請使用 **Ctrl**+**C** 停止主機，瀏覽至專案的根資料夾，然後再次執行先前的命令。

1. 從這個輸出中將 `HttpExample` 函式的 URL 複製到瀏覽器，並附加查詢字串 `?name=<YOUR_NAME>`，使其成為完整的 URL (如 `http://localhost:7071/api/HttpExample?name=Functions`)。 瀏覽器應該會顯示類似於 `Hello Functions` 的訊息：

    ![在本機瀏覽器中執行函式的結果](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    您在其中啟動專案的終端機，也會在您提出要求時顯示記錄輸出。

1. 完成作業後，請使用 **Ctrl**+**C** 並選擇 `y` 以停止函式主機。

## <a name="deploy-the-function-project-to-azure"></a>將函式專案部署至 Azure

第一次部署函式應用程式時，會在 Azure 中建立函式專案和相關資源。 為裝載應用程式所建立的 Azure 資源設定會在 [pom.xml 檔案](#pomxml)中定義。 在本文中，您將接受預設值。

> [!TIP]
> 若要建立在 Linux 上執行而不是在 Windows 上執行的函式應用程式，請將 pom.xml 檔案中的 `runtime.os` 元素從 `windows` 變更為 `linux`。 [這些區域](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions)支援以耗用量方案執行 Linux。 您的應用程式不能在相同的資源群組中在 Linux 和 Windows 上執行。

1. 您必須先使用 Azure CLI 或 Azure PowerShell 登入 Azure 訂用帳戶，才能進行部署。 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    [az login](/cli/azure/reference-index#az-login) 命令會將您登入您的 Azure 帳戶。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet 會將您登入您的 Azure 帳戶。

    ---

1. 使用下列命令，將您的專案部署至新的函式應用程式。

    ```console
    mvn azure-functions:deploy
    ```
    
    這會在 Azure 中建立下列資源：
    
    + 資源群組。 命名為 java-functions-group。
    + 儲存體帳戶。 Functions 所需。 此名稱會根據儲存體帳戶名稱需求隨機產生。
    + 主控方案。 在 westus 中以無伺服器方式主控您的函式應用程式。 名稱為 java-functions-app-service-plan。
    + 函式應用程式。 函式應用程式是您函式的部署和執行單位。 系統會根據您的 artifactId 隨機產生名稱，此名稱會附加隨機產生的號碼。
    
    部署會封裝專案檔案，並使用 [zip deployment](functions-deployment-technologies.md#zip-deploy) 將其部署至新的函式應用程式， 且程式碼會從 Azure 中的部署套件執行。

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

## <a name="clean-up-resources"></a>清除資源

如果您要繼續進行[下一個步驟](#next-steps)並新增 Azure 儲存體佇列輸出繫結，請保留您所有的資源，因為在後續的工作還會用到。

否則，請使用下列命令刪除資源群組及其包含的所有資源，以避免產生額外的成本。

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name java-functions-group
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name java-functions-group
```

---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [連線至 Azure 儲存體佇列](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
