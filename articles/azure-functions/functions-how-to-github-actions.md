---
title: 使用 GitHub 動作在 Azure Functions 中進行程式碼更新
description: 瞭解如何使用 GitHub 動作來定義工作流程，以在 GitHub 中建立及部署 Azure Functions 專案。
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: cshoe
ms.custom: tracking-python
ms.openlocfilehash: 1a7cc37f297f902fb5de473303f1dc260cbea9ca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84559098"
---
# <a name="continuous-delivery-by-using-github-action"></a>使用 GitHub 動作進行持續傳遞

[GitHub 動作](https://github.com/features/actions)可讓您定義工作流程，以自動建立函式程式碼並將其部署至 Azure 中的函式應用程式。 

在 GitHub 動作中，[工作流程](https://help.github.com/articles/about-github-actions#workflow)是您在 github 存放庫中定義的自動化進程。 此程式會告訴 GitHub 如何在 GitHub 上建立及部署函式應用程式專案。 

工作流程是由您存放庫內 `/.github/workflows/` 路徑中的 YAML (. yml) 檔案所定義的。 此定義包含組成工作流程的各種步驟與參數。 

若為 Azure Functions 工作流程，檔案有三個區段： 

| 區段 | 工作 |
| ------- | ----- |
| **驗證** | <ol><li>定義服務主體。</li><li>下載發行設定檔。</li><li>建立 GitHub 秘密。</li></ol>|
| **建置** | <ol><li>設定環境。</li><li>建置函式應用程式。</li></ol> |
| **部署** | <ol><li>部署函數應用程式。</li></ol>|

> [!NOTE]
> 如果您決定使用發行設定檔進行驗證，則不需要建立服務主體。

## <a name="create-a-service-principal"></a>建立服務主體

您可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令來建立[服務主體](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 您可以使用 Azure 入口網站中的 [Azure Cloud Shell](https://shell.azure.com)，或選取 [試試看] 按鈕來執行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

在此範例中，將資源中的預留位置取代為您的訂用帳戶識別碼、資源群組和函數應用程式名稱。 輸出是可提供函數應用程式存取權的角色指派認證。 複製這個 JSON 物件，您可以用它從 GitHub 進行驗證。

> [!IMPORTANT]
> 授與最小存取權永遠是最佳作法。 這就是為什麼上述範例中的範圍僅限於特定的函式應用程式，而非整個資源群組。

## <a name="download-the-publishing-profile"></a>下載發行設定檔

若要下載函數應用程式的發行設定檔：

1. 選取函數應用程式的 **[總覽**] 頁面，然後選取 [**取得發行設定檔**]。

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="Download publish profile":::

1. 儲存並複製發佈設定檔案的內容。

## <a name="configure-the-github-secret"></a>設定 GitHub 密碼

1. 在[GitHub](https://github.com)中，流覽至您的存放庫，選取 [**設定**] [秘密] [新增  >  **Secrets**  >  **密碼**]。

   :::image type="content" source="media/functions-how-to-github-actions/add-secret.png" alt-text="新增秘密":::

1. 加入新的秘密。

   * 如果您使用的是使用 Azure CLI 所建立的服務主體，請使用做 `AZURE_CREDENTIALS` 為**名稱**。 然後，貼上已複製的 JSON 物件輸出**值**，然後選取 [**新增密碼**]。
   * 如果您使用的是發行設定檔，請使用做 `SCM_CREDENTIALS` 為**名稱**。 然後，使用發行設定檔的檔案內容作為 [**值**]，然後選取 [**新增密碼**]。

GitHub 現在可以在 Azure 中向您的函數應用程式進行驗證。

## <a name="set-up-the-environment"></a>設定 Azure 環境 

設定環境是使用特定語言的發行設定動作來完成。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下列範例顯示使用 `actions/setup-node` 動作來設定環境的工作流程部分：

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

# <a name="python"></a>[Python](#tab/python)

下列範例顯示使用 `actions/setup-python` 動作來設定環境的工作流程部分：

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

# <a name="c"></a>[C#](#tab/csharp)

下列範例顯示使用 `actions/setup-dotnet` 動作來設定環境的工作流程部分：

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

# <a name="java"></a>[Java](#tab/java)

下列範例顯示使用 `actions/setup-java` 動作來設定環境的工作流程部分：

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```
---

## <a name="build-the-function-app"></a>建立函數應用程式

這取決於 Azure Functions 支援的語言和語言，本節應該是每種語言的標準組建步驟。

下列範例會顯示建立函式應用程式的工作流程部分，這是語言特定的：

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

# <a name="c"></a>[C#](#tab/csharp)

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    - name: 'Run mvn'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for maven build in pushd
        pushd . ./POM_ARTIFACT_ID
        mvn clean package
        mvn azure-functions:package
        popd
```
---

## <a name="deploy-the-function-app"></a>部署函式應用程式

若要將程式碼部署至函式應用程式，您必須使用 `Azure/functions-action` 動作。 此動作有兩個參數：

|參數 |說明  |
|---------|---------|
|**_app-name_** | 不必函數應用程式的名稱。 |
|_**slot-name**_ | 選擇性您想要部署的[部署](functions-deployment-slots.md)位置名稱。 位置必須已在您的函式應用程式中定義。 |


下列範例會使用的第1版 `functions-action` ：

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>後續步驟

若要查看完整的 yaml 檔案，請參閱[Azure GitHub 動作工作流程範例](https://aka.ms/functions-actions-samples)存放庫中的其中一個檔案，其 `functionapp` 名稱為。 您可以使用這些範例做為工作流程的起點。

> [!div class="nextstepaction"]
> [深入瞭解 GitHub 動作](https://help.github.com/en/articles/about-github-actions)
