---
title: 使用 GitHub Actions 將靜態網站部署至 Azure 儲存體
description: 使用 GitHub Actions Azure 儲存體靜態網站裝載
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 09/11/2020
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 3adbc0a2d55a2adc2ab7c1f82b0a358542eefc2a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842442"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>設定要在 Azure 儲存體中部署靜態網站的 GitHub Actions 工作流程

使用工作流程將靜態網站部署至 Azure 儲存體帳戶，開始使用 [GitHub Actions](https://docs.github.com/en/actions) 。 設定 GitHub Actions 工作流程之後，您將能夠在對網站的程式碼進行變更時，從 GitHub 自動將您的網站部署至 Azure。

> [!NOTE]
> 如果您使用 [Azure 靜態 Web Apps](https://docs.microsoft.com/azure/static-web-apps/)，則不需要手動設定 GitHub Actions 的工作流程。
> Azure 靜態 Web Apps 會為您自動建立 GitHub Actions 的工作流程。 

## <a name="prerequisites"></a>先決條件

Azure 訂用帳戶和 GitHub 帳戶。 

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 具有您靜態網站程式碼的 GitHub 存放庫。 如果您沒有 GitHub 帳戶，請[免費註冊](https://github.com/join)。  
- Azure 儲存體中裝載的工作靜態網站。 瞭解如何 [在 Azure 儲存體中裝載靜態網站](storage-blob-static-website-how-to.md)。 若要遵循此範例，您也應該部署 [AZURE CDN](static-website-content-delivery-network.md)。

> [!NOTE]
> 通常會使用內容傳遞網路 (CDN) 將延遲減少給全球各地的使用者，並將交易數目減少至儲存體帳戶。 將靜態內容部署到雲端式儲存體服務可以減少可能昂貴的計算實例需求。 如需詳細資訊，請參閱 [靜態內容裝載模式](/azure/architecture/patterns/static-content-hosting)。

## <a name="generate-deployment-credentials"></a>產生部署認證

您可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) 命令來建立[服務主體](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 請使用 Azure 入口網站中的 [Azure Cloud Shell](https://shell.azure.com/)，或選取 [試試看] 按鈕來執行此命令。

將預留位置取代 `myStaticSite` 為您在 Azure 儲存體中主控的網站名稱。 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

在上述範例中，請將預留位置取代為您的訂用帳戶識別碼和資源組名。 輸出是具有角色指派認證的 JSON 物件，可提供您儲存體帳戶的存取權，如下所示。 複製此 JSON 物件以供稍後之用。

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> 授與最小存取權永遠是最佳作法。 上一個範例中的範圍僅限於特定的 App Service 應用程式，而非整個資源群組。

## <a name="configure-the-github-secret"></a>設定 GitHub 密碼

1. 在 [GitHub](https://github.com/) 中，瀏覽您的存放庫。

1. 選取 [設定] > [秘密] > [新增秘密]。

1. 將得自 Azure CLI 命令的整個 JSON 輸出貼到祕密的 [值] 欄位中。 為秘密命名，如下所示 `AZURE_CREDENTIALS` 。

    當您稍後設定工作流程檔案時，會將祕密用於 Azure 登入動作的輸入 `creds`。 例如：

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>新增您的工作流程

1. 移至 GitHub 存放庫的 [動作]。 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="GitHub 動作功能表項目":::

1. 選取 [自行設定工作流程]。 

1. 刪除工作流程檔案 `on:` 區段之後的所有內容。 例如，剩餘的工作流程看起來可能像這樣。 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. 重新命名工作流程 `Blob storage website CI`，並新增簽出和登入動作。 這些動作會簽出您的站台碼，並使用您稍早建立的 `AZURE_CREDENTIALS` GitHub 祕密向 Azure 進行驗證。 

    ```yaml
    name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. 使用 Azure CLI 動作，將您的程式碼上傳至 blob 儲存體，並清除您的 CDN 端點。 針對 `az storage blob upload-batch` ，將預留位置取代為您的儲存體帳戶名稱。 腳本會上傳至 `$web` 容器。 針對 `az cdn endpoint purge` ，將預留位置取代為您的 cdn 設定檔名稱、cdn 端點名稱和資源群組。

    ```yaml
        - name: Upload to blob storage
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
    ``` 

1. 藉由新增動作至 Azure 的登出，來完成您的工作流程。 以下是完成後的工作流程。 檔案會出現在存放庫的 `.github/workflows` 資料夾中。

    ```yaml
   name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Azure Login
        uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}    
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
            # Azure logout 
        - name: logout
          run: |
                az logout
    ```

## <a name="review-your-deployment"></a>檢閱您的部署

1. 移至 GitHub 存放庫的 [動作]。 

1. 開啟第一個結果，以查看工作流程的執行詳細記錄。 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="GitHub 動作執行的記錄":::

## <a name="clean-up-resources"></a>清除資源

當您的靜態網站和 GitHub 存放庫不再需要時，請刪除資源群組和您的 GitHub 存放庫，以清除您所部署的資源。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解 Azure 靜態 Web Apps](https://docs.microsoft.com/azure/static-web-apps/)
