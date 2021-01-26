---
title: 快速入門 - 在 GitHub Actions 工作流程中使用 Azure Key Vault 秘密
description: 在 GitHub Actions 中使用 Key Vault 秘密，將您的軟體開發工作流程自動化
author: juliakm
ms.custom: github-actions-azure
ms.author: jukullam
ms.date: 11/24/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: c1e8062210de1d7d99f57a4e0b155492f7dfdd9a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785971"
---
# <a name="use-key-vault-secrets-in-github-actions-workflows"></a>在 GitHub Actions 工作流程中使用 Key Vault 秘密

在您的 [GitHub Actions](https://help.github.com/en/articles/about-github-actions) 中使用 Key Vault 秘密，並安全地將密碼和其他秘密儲存在 Azure 金鑰保存庫中。 深入了解 [Key Vault](../general/overview.md)。 

透過 Key Vault 和 GitHub Actions，您將享有集中式秘密管理工具的好處，以及 GitHub 動作的所有優勢。 GitHub Actions 是 GitHub 中的一個功能套件，可將您的軟體開發工作流程自動化。 您可以在儲存程式碼以及在提取要求和問題上共同作業的相同位置部署工作流程。 


## <a name="prerequisites"></a>先決條件 
- GitHub 帳戶。 如果您沒有帳戶，請[免費](https://github.com/join)註冊。  
- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 連線至 GitHub 存放庫的 Azure App。 此範例使用[將容器部署至 Azure App Service](/azure/developer/javascript/tutorial-vscode-docker-node-01)。 
- Azure 金鑰保存庫。  您可以使用 Azure 入口網站、Azure CLI 或 Azure PowerShell 建立 Azure Key Vault。

## <a name="workflow-file-overview"></a>工作流程檔案概觀

工作流程是由您存放庫內 `/.github/workflows/` 路徑中的 YAML (. yml) 檔案所定義的。 此定義包含組成工作流程的各種步驟與參數。

該檔案有兩個用於 GitHub Actions 驗證的區段：

|區段  |工作  |
|---------|---------|
|**驗證** | 1.定義服務主體。 <br /> 2.建立 GitHub 祕密。 <br /> 3.新增角色指派。 |
|**金鑰保存庫** | 1.新增金鑰保存庫動作。 <br /> 2.參考金鑰保存庫祕密。 |

## <a name="authentication"></a>驗證

您可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) 命令來建立[服務主體](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 請使用 Azure 入口網站中的 [Azure Cloud Shell](https://shell.azure.com/)，或選取 [試試看] 按鈕來執行此命令。

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

在上述範例中，請將預留位置取代為您的訂用帳戶識別碼和資源群組名稱。 請將預留位置 `myApp` 取代為您的應用程式名稱。 輸出是一個 JSON 物件，內有角色指派認證可讓您存取 App Service 應用程式，如下所示。 複製此 JSON 物件以供後續使用。 您只需要具有 `clientId`、`clientSecret`、`subscriptionId` 和 `tenantId` 值的區段。 

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

### <a name="configure-the-github-secret"></a>設定 GitHub 密碼

為您的 Azure 認證、資源群組和訂用帳戶建立秘密。 

1. 在 [GitHub](https://github.com/) 中，瀏覽您的存放庫。

1. 選取 [設定] > [秘密] > [新增秘密]。

1. 將得自 Azure CLI 命令的整個 JSON 輸出貼到祕密的 [值] 欄位中。 將祕密命名為 `AZURE_CREDENTIALS`。

1. 複製 `clientId` 的值，以供後續使用。 

### <a name="add-a-role-assignment"></a>新增角色指派 
 
您必須授與 Azure 服務主體的存取權，以便能存取金鑰保存庫以進行 `get` 和 `list` 作業。 若未這麼做，您將無法使用服務主體。 

請將 `keyVaultName` 取代為您的金鑰保存庫名稱，並將 `clientIdGUID` 取代為 `clientId` 的值。 

```azurecli-interactive
    az keyvault set-policy -n {keyVaultName} --secret-permissions get list --spn {clientIdGUID}
```

## <a name="use-the-azure-key-vault-action"></a>使用 Azure 金鑰保存庫動作

透過 [Azure 金鑰保存庫動作](https://github.com/marketplace/actions/azure-key-vault-get-secrets)，您可以從 Azure 金鑰保存庫執行個體中擷取一或多個秘密，並在 GitHub Action 工作流程中加以使用。

擷取的秘密會設定為輸出，也會作為環境變數。 當變數列印至主控台或記錄時，會自動進行遮罩。

```yaml
    - uses: Azure/get-keyvault-secrets@v1
      with:
        keyvault: "my Vault" # name of key vault in Azure portal
        secrets: 'mySecret'  # comma separated list of secret keys to fetch from key vault 
      id: myGetSecretAction # ID for secrets that you will reference
```

若要在您的工作流程中使用金鑰保存庫，您需要金鑰保存庫動作，並參考該動作。 

在此範例中，金鑰保存庫的名稱為 `containervault`。 有兩個金鑰保存庫秘密會透過金鑰保存庫動作新增至環境 - `containerPassword` 和 `containerUsername`。 

後續將會在 Docker 登入工作中以前置詞 `steps.myGetSecretAction.outputs` 參考金鑰保存庫值。 

```yaml
name: Example key vault flow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - uses: actions/checkout@v2
    - uses: Azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - uses: Azure/get-keyvault-secrets@v1
      with: 
        keyvault: "containervault"
        secrets: 'containerPassword, containerUsername'
      id: myGetSecretAction
    - uses: azure/docker-login@v1
      with:
        login-server: myregistry.azurecr.io
        username: ${{ steps.myGetSecretAction.outputs.containerUsername }}
        password: ${{ steps.myGetSecretAction.outputs.containerPassword }}
    - run: |
        docker build . -t myregistry.azurecr.io/myapp:${{ github.sha }}
        docker push myregistry.azurecr.io/myapp:${{ github.sha }}     
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'myregistry.azurecr.io/myapp:${{ github.sha }}'
```

## <a name="clean-up-resources"></a>清除資源

當您不再需要 Azure 應用程式、GitHub 存放庫和金鑰保存庫時，請刪除應用程式、GitHub 存放庫和金鑰保存庫的資源群組，以清除您所部署的資源。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 Azure Key Vault](../general/overview.md)