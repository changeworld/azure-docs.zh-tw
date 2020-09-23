---
title: 在 GitHub Actions 中使用 Key Vault 驗證 Azure Spring Cloud
description: 如何使用 key vault 搭配 GitHub Actions 的 Azure 春季 Cloud 的 CI/CD 工作流程
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java
ms.openlocfilehash: 995d10b3c7064e462500e0bec4d5d8aa010afe64
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90888780"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>在 GitHub Actions 中使用 Key Vault 驗證 Azure Spring Cloud

本文**適用于：** ✔️ JAVA ✔️ C#

Key vault 是儲存金鑰的安全位置。 企業使用者需要在其控制的範圍中儲存 CI/CD 環境的認證。 在金鑰保存庫中取得認證的金鑰應該受限於資源範圍。  它只能存取金鑰保存庫範圍，而不是整個 Azure 範圍。 它就像是只能開啟強式方塊的索引鍵，而不是可以開啟建築物中所有大門的主要金鑰。 它是使用另一個金鑰取得金鑰的方法，在 CICD 工作流程中很有用。 

## <a name="generate-credential"></a>產生認證
若要產生金鑰以存取金鑰保存庫，請在本機電腦上執行下列命令：
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
參數指定的範圍會 `--scopes` 限制資源的金鑰存取權。  它只能存取強式箱。

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
然後將結果儲存至 GitHub **秘密** ，如 [設定您的 github 存放庫和向 Azure 進行驗證](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate)所述。

## <a name="add-access-policies-for-the-credential"></a>新增認證的存取原則
您在上面建立的認證只能取得 Key Vault 的一般資訊，而不是它所儲存的內容。  若要取得 Key Vault 中儲存的秘密，您需要設定認證的存取原則。

移至 Azure 入口網站中的**Key Vault**儀表板，按一下 [**存取控制**] 功能表，然後開啟 [**角色指派**] 索引標籤。選取 [**類型**] 和 [範圍] 的**應用程式** `This resource` 。 **scope**  您應該會看到您在上一個步驟中建立的認證：

 ![設定存取原則](./media/github-actions/key-vault1.png)

複製認證名稱，例如 `azure-cli-2020-01-19-04-39-02` 。 開啟 [ **存取原則** ] 功能表，然後按一下 [ **+ 新增存取原則** ] 連結。  選取 `Secret Management` [ **範本**]，然後選取 [ **主體**]。 在 [**主體** / **選取**輸入] 方塊中貼上認證名稱：

 ![選取](./media/github-actions/key-vault2.png)

 按一下 [**新增存取原則**] 對話方塊中的 [**新增**] 按鈕，然後按一下 [**儲存**]。

## <a name="generate-full-scope-azure-credential"></a>產生完整範圍的 Azure 認證
這是用來開啟建築物中所有大門的主要金鑰。 此程式與上一個步驟類似，但我們會變更範圍來產生主要金鑰：

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

同樣地，結果：
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
複製整個 JSON 字串。  切換回 **Key Vault** 儀表板。 開啟 [ **秘密** ] 功能表，然後按一下 [ **產生/匯入** ] 按鈕。 輸入秘密名稱，例如 `AZURE-CREDENTIALS-FOR-SPRING` 。 將 JSON 認證字串貼到 [ **值** ] 輸入方塊。 您可能會注意到，值輸入方塊是單行文字欄位，而不是多行文字區域。  您可以在該處貼上完整的 JSON 字串。

 ![完整範圍認證](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>合併 GitHub Actions 中的認證
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
        keyvault: "<Your Key Vault Name>"
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

## <a name="next-steps"></a>下一步
* [春季雲端 GitHub Actions](./spring-cloud-howto-github-actions.md)
