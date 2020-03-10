---
title: 使用 GitHub 動作中的 Key Vault 來驗證 Azure 春季雲端
description: 如何搭配 GitHub 動作使用 key vault 搭配適用于 Azure 春季雲端的 CI/CD 工作流程
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/20/2019
ms.openlocfilehash: 78cd5945e394219be0551bbe97afef07f18b61f7
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945476"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>使用 GitHub 動作中的 Key Vault 來驗證 Azure 春季雲端
金鑰保存庫是儲存金鑰的安全位置。 企業使用者需要將 CI/CD 環境的認證儲存在他們所控制的範圍內。 要在金鑰保存庫中取得認證的金鑰應限制為資源範圍。  它只能存取金鑰保存庫範圍，而不是整個 Azure 範圍。 就像是只能開啟一個強式箱的索引鍵，而不是可以開啟大樓所有大門的主要金鑰。 這是使用另一個金鑰來取得金鑰的方法，這在 CICD 工作流程中很有用。 

## <a name="generate-credential"></a>產生認證
若要產生金鑰以存取金鑰保存庫，請在本機電腦上執行下列命令：
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
`--scopes` 參數所指定的範圍會限制資源的金鑰存取。  它只能存取 [強式] 方塊。

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
然後將結果儲存至 GitHub**秘密**，如[設定您的 github 存放庫和使用 Azure 進行驗證](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate)中所述。

## <a name="add-access-policies-for-the-credential"></a>新增認證的存取原則
您在上面建立的認證只能取得 Key Vault 的一般資訊，而不是它所儲存的內容。  若要取得儲存在 Key Vault 中的秘密，您需要設定認證的存取原則。

移至 Azure 入口網站中的 [ **Key Vault**儀表板]，按一下 [**存取控制**] 功能表，然後開啟 [**角色指派**] 索引標籤。選取 [**類型**] 的**應用程式**，並 `This resource` [**範圍**]。  您應該會看到您在上一個步驟中建立的認證：

 ![設定存取原則](./media/github-actions/key-vault1.png)

複製認證名稱，例如 `azure-cli-2020-01-19-04-39-02`。 開啟 [**存取原則**] 功能表，按一下 [ **+ 新增存取原則**] 連結。  選取 [`Secret Management`] 作為 [**範本**]，然後選取 [**主體**]。 在 [**主體**/**選取**輸入] 方塊中貼上認證名稱：

 ![選取](./media/github-actions/key-vault2.png)

 按一下 [**新增存取原則**] 對話方塊中的 [**新增**] 按鈕，然後按一下 [**儲存**]。

## <a name="generate-full-scope-azure-credential"></a>產生完整範圍的 Azure 認證
這是在大樓中開啟所有大門的主要金鑰。 此程式類似于上一個步驟，但我們在此變更範圍以產生主要金鑰：

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

同樣地，結果如下：
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
複製整個 JSON 字串。  返回**Key Vault**儀表板。 開啟 [**秘密**] 功能表，然後按一下 [**產生/匯入**] 按鈕。 輸入秘密名稱，例如 `AZURE-CRENDENTIALS-FOR-SPRING`。 將 JSON 認證字串貼入 [**值**] 輸入方塊。 您可能會注意到 [值] 輸入方塊是單行文字欄位，而不是多行文字區域。  您可以在該處貼上完整的 JSON 字串。

 ![完整範圍認證](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>結合 GitHub 動作中的認證
設定 CICD 管線執行時所使用的認證：

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
* [春季雲端 GitHub 動作](./spring-cloud-howto-github-actions.md)