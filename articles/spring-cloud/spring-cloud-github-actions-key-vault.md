---
title: 在 GitHub 操作中使用金鑰保存庫對 Azure 春雲進行身份驗證
description: 如何使用金鑰保存庫與 CI/CD 工作流一起使用 Azure 春雲操作
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/20/2019
ms.openlocfilehash: 78cd5945e394219be0551bbe97afef07f18b61f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945476"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>在 GitHub 操作中使用金鑰保存庫對 Azure 春雲進行身份驗證
金鑰保存庫是存儲金鑰的安全位置。 企業使用者需要將 CI/CD 環境的憑據存儲在他們控制的範圍內。 在金鑰保存庫中獲取憑據的鍵應限於資源範圍。  它只能訪問金鑰保存庫作用域，而不是整個 Azure 作用域。 它就像一個鑰匙，只能打開一個強大的盒子，而不是主鑰匙，可以打開所有的門在建築物。 這是一種使用另一個金鑰獲取金鑰的方法，這在 CICD 工作流中非常有用。 

## <a name="generate-credential"></a>生成憑據
要生成金鑰以訪問金鑰保存庫，請在本地電腦上執行以下命令：
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
`--scopes`參數指定的作用域限制對資源的金鑰訪問。  它只能訪問強框。

結果：
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
然後，將結果保存到 GitHub**機密**，如[設置 GitHub 存儲庫中所述，並使用 Azure 進行身份驗證](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate)。

## <a name="add-access-policies-for-the-credential"></a>添加憑據的訪問策略
上面創建的憑據只能獲取有關金鑰保存庫的一般資訊，而不能獲取它存儲的內容。  要獲取存儲在金鑰保存庫中的機密，您需要為憑據設置訪問策略。

轉到 Azure**scope**門戶中的 **"金鑰保存庫**"儀表板，按一下 **"存取控制"** 功能表，然後打開**角色指派**選項卡。 **Type** `This resource` **Apps**  您應該會看到您在上一步中創建的憑據：

 ![設置訪問策略](./media/github-actions/key-vault1.png)

複製憑據名稱，例如。 `azure-cli-2020-01-19-04-39-02` 打開 **"訪問策略"** 功能表，按一下 **"添加訪問策略"** 連結。  為`Secret Management`**範本**選擇 ，然後選擇**主體**。 將憑據名稱粘貼在**主體**/**選擇**輸入框中：

 ![選取](./media/github-actions/key-vault2.png)

 按一下"**添加訪問策略"** 對話方塊中的 **"添加**"按鈕，然後按一下"**保存**"。

## <a name="generate-full-scope-azure-credential"></a>生成全範圍 Azure 憑據
這是打開大樓所有門的主鑰匙。 該過程與上一步類似，但在這裡我們更改範圍以生成主金鑰：

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

同樣，結果：
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
複製整個 JSON 字串。  Bo 返回**金鑰保存庫**儀表板。 打開 **"機密"** 功能表，然後按一下 **"生成/導入**"按鈕。 輸入機密名稱，如`AZURE-CRENDENTIALS-FOR-SPRING`。 將 JSON 憑據字串粘貼到 **"值"** 輸入框。 您可能會注意到值輸入框是一行文字欄位，而不是多行文本區域。  您可以在此處粘貼完整的 JSON 字串。

 ![全範圍憑據](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>在 GitHub 操作中合併憑據
設置執行 CICD 管道時使用的憑據：

```
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "zlhe-test"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>後續步驟
* [春雲 GitHub 操作](./spring-cloud-howto-github-actions.md)