---
title: 使用 GitHub 操作在 Azure 函數中進行代碼更新
description: 瞭解如何使用 GitHub 操作定義工作流以在 GitHub 中生成和部署 Azure 函數專案。
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: cshoe
ms.openlocfilehash: 54010269e5b61ebf28a29dd3165c4310f3472817
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878199"
---
# <a name="continuous-delivery-by-using-github-action"></a>使用 GitHub 操作連續交付

[GitHub 操作](https://github.com/features/actions)允許您定義工作流,以自動生成函數代碼並將其部署到 Azure 中的函數應用。 

在 GitHub 操作中,[工作流](https://help.github.com/articles/about-github-actions#workflow)是在 GitHub 儲存庫中定義的自動化過程。 此過程告訴 GitHub 如何在 GitHub 上構建和部署函數應用專案。 

工作流由儲存庫中路徑中的`/.github/workflows/`YAML (.yml) 檔案定義。 此定義包含構成工作流的各種步驟和參數。 

對於 Azure 函數工作流,該檔有三個部分: 

| 區段 | 工作 |
| ------- | ----- |
| [驗證]**** | <ol><li>定義服務主體。</li><li>下載發佈配置檔。</li><li>建立 GitHub 金鑰。</li></ol>|
| **Build** | <ol><li>設置環境。</li><li>建置函式應用程式。</li></ol> |
| **部署** | <ol><li>部署功能應用。</li></ol>|

> [!NOTE]
> 如果您決定使用發佈配置檔進行身份驗證,則無需創建服務主體。

## <a name="create-a-service-principal"></a>建立服務主體

可以使用[Azure CLI](/cli/azure/)中的[az ad sp 建立 rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)指令建立[服務主體](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 可以使用 Azure 門戶中的[Azure 雲外殼](https://shell.azure.com)運行此命令,也可以選擇「**試用」** 按鈕。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

在此示例中,將資源中的占位符替換為訂閱 ID、資源組和函數應用名稱。 輸出是提供對函數應用的訪問的角色分配憑據。 複製此 JSON 物件,可用於從 GitHub 進行身份驗證。

> [!IMPORTANT]
> 給予最低訪問許可權總是一種好的做法。 這就是為什麼上一個範例中的範圍僅限於特定的函數應用,而不是整個資源組。

## <a name="download-the-publishing-profile"></a>下載設定檔

您可以通過轉到應用的 **「概述」** 頁面並按下 **「獲取發布設定檔**」 來下載函數應用的發佈設定檔。

   ![Download publish profile](media/functions-how-to-github-actions/get-publish-profile.png)

複製文件的內容。

## <a name="configure-the-github-secret"></a>設定 GitHub 金鑰

1. 在[GitHub](https://github.com)中,瀏覽到您的儲存庫,選擇 **「設定** > **機密** > **添加新機密**」。

   ![新增機密](media/functions-how-to-github-actions/add-secret.png)

1. 添加新機密。

   * 如果使用使用 Azure CLI 建立的服務主體`AZURE_CREDENTIALS`,請使用**Name**。 然後貼上複製的 JSON 物件**輸出**的值,然後選擇 **「添加機密**」。
   * 如果您使用的設定檔,請使用`SCM_CREDENTIALS` **Name**。 然後使用設定檔的檔案內容為**值**,並選擇**新增機密**。

GitHub 現在可以對 Azure 中的函數應用進行身份驗證。

## <a name="set-up-the-environment"></a>設定 Azure 環境 

使用特定於語言的發佈設置操作完成環境設置。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下面的範例顯示了使用`actions/setup-node`操作設定環境的工作流部分:

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

下面的範例顯示了使用`actions/setup-python`操作設定環境的工作流部分:

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

下面的範例顯示了使用`actions/setup-dotnet`操作設定環境的工作流部分:

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

下面的範例顯示了使用`actions/setup-java`操作設定環境的工作流部分:

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

## <a name="build-the-function-app"></a>產生函數應用

這取決於 Azure 函數支援的語言和語言,此部分應該是每種語言的標準生成步驟。

下面的範例顯示了生成函數應用的工作流部分,該部分特定於語言:

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

要將代碼部署到函數應用,您需要使用該`Azure/functions-action`操作。 此操作具有兩個參數:

|參數 |說明  |
|---------|---------|
|**_套用名稱_** | (強制性)函數應用的名稱。 |
|_**插槽名稱**_ | ( 選擇性的 )要部署到的部署[槽](functions-deployment-slots.md)的名稱。 必須在函數應用中定義該槽。 |


下面的範例使用 版本`functions-action`1 的 :

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>後續步驟

要查看完整的工作流 .yaml,請參閱[Azure GitHub 操作工作流範例儲存庫](https://aka.ms/functions-actions-samples)中的名稱`functionapp`中的一個檔。 您可以將這些示例用作工作流的起點。

> [!div class="nextstepaction"]
> [瞭解有關 GitHub 操作的更多](https://help.github.com/en/articles/about-github-actions)
