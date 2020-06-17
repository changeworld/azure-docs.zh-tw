---
title: 教學課程：使用 Azure 事件方格以自動調整上傳映像的大小
description: 教學課程：Azure Event Grid 可在 Azure 儲存體的 Blob 上傳項目上觸發。 您可以使用此將上傳至 Azure 儲存體的映像檔案傳送至其他服務 (例如 Azure Functions)，以調整大小和其他改善功能。
services: event-grid, functions
author: spelluru
manager: jpconnoc
editor: ''
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2020
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: 92962c376e2b800a327f44c4cad5cd9fdd4cab8d
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560529"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>教學課程：使用 Event Grid 自動調整已上傳映像的大小

[Azure Event Grid](overview.md) 是一項雲端事件服務。 Event Grid 可讓您建立由 Azure 服務或協力廠商資源引發之事件的訂閱。  

本教學課程是一系列儲存體教學課程的第二部分。 它延伸了[上一個儲存體教學課程][previous-tutorial]，以使用 Azure Event Grid 與 Azure Functions 新增無伺服器自動縮圖產生。 Event Grid 可讓 [Azure Functions](../azure-functions/functions-overview.md) 回應 [Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)事件，並產生上傳映像的縮圖。 針對 Blob 儲存體建立事件建立事件訂閱。 當 blob 加入特定的 Blob 儲存體容器時，會呼叫函式端點。 從 Event Grid 傳遞至函式繫結的資料用於存取 Blob 並產生縮圖映像。

您可以使用 Azure CLI 與 Azure 入口網站，將調整大小功能加入現有的映像上傳應用程式。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

![瀏覽器中已發佈的 Web 應用程式](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

![瀏覽器中已發佈的 Web 應用程式](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立 Azure 儲存體帳戶
> * 使用 Azure Functions 部署無伺服器程式碼
> * 在 Event Grid 中建立 Blob 儲存體事件訂閱

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要完成本教學課程：

您必須已完成先前的 Blob 儲存體教學課程︰[使用 Azure 儲存體在雲端中上傳影像資料][previous-tutorial]。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本教學課程需要 Azure CLI 2.0.14 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

如果您未使用 Cloud Shell，您必須先使用 `az login` 登入。

如果您先前未在訂用帳戶中註冊事件方格資源提供者，請務必註冊。

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

## <a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶

Azure Functions 需要一般的儲存體帳戶。 除了您在上一個教學課程所建立的 Blob 儲存體帳戶外，請再使用 [az storage account create](/cli/azure/storage/account) 命令，於資源群組中另外建立一般的儲存體帳戶。 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。

1. 設定一個變數，用以保存您在上一個教學課程中建立的資源群組名稱。

    ```azurecli-interactive
    resourceGroupName="myResourceGroup"
    ```
2. 設定一個變數，用以保存您要建立的資源位置。 

    ```azurecli-interactive
    location="eastus"
    ```    
3. 針對 Azure Functions 所需新儲存體帳戶的名稱來設定變數。
    ```azurecli-interactive
    functionstorage="<name of the storage account to be used by the function>"
    ```
4. 建立 Azure 函式的儲存體帳戶。

    ```azurecli-interactive
    az storage account create --name $functionstorage --location $location \
    --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

## <a name="create-a-function-app"></a>建立函數應用程式  

您必須擁有函式應用程式以便主控函式的執行。 函式應用程式會提供環境來讓您的函式程式碼進行無伺服器執行。 使用 [az functionapp create](/cli/azure/functionapp) 命令來建立函式應用程式。

在下列命令中，請提供您自己的唯一函式應用程式名稱。 函式應用程式會作為函式應用程式的預設 DNS 網域，所以此名稱在 Azure 的所有應用程式中都必須是唯一的名稱。

1. 指定要建立的函式應用程式名稱。

    ```azurecli-interactive
    functionapp="<name of the function app>"
    ```
2. 建立 Azure 函式。

    ```azurecli-interactive
    az functionapp create --name $functionapp --storage-account $functionstorage \
      --resource-group $resourceGroupName --consumption-plan-location $location \
      --functions-version 2
    ```

現在請設定函式應用程式，才能連線到您在[上一個教學課程][previous-tutorial]中建立的 Blob 儲存體帳戶。

## <a name="configure-the-function-app"></a>設定函式應用程式

此函式需要 Blob 儲存體帳戶的認證，認證會使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 命令來新增至函式應用程式的應用程式設定。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails \
  THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

```azurecli-interactive
blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName \
  -n $blobStorageAccount --query [0].value --output tsv)

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails \
  AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey \
  AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

---

`FUNCTIONS_EXTENSION_VERSION=~2` 設定會讓函式應用程式在 2.x 版的 Azure Functions 執行階段上執行。

您現在可以將函式程式碼專案部署到此函式應用程式。

## <a name="deploy-the-function-code"></a>部署函式程式碼 

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

範例 C# 大小調整函式可從 [GitHub](https://github.com/Azure-Samples/function-image-upload-resize) 取得。 使用 [az functionapp deployment source config](/cli/azure/functionapp/deployment/source) 命令，將此程式碼專案部署至函式應用程式。

```azurecli-interactive
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

範例 Node.js 大小調整函式可從 [GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10) 取得。 使用 [az functionapp deployment source config](/cli/azure/functionapp/deployment/source) 命令，將此函式程式碼專案部署至函式應用程式。

```azurecli-interactive
az functionapp deployment source config --name $functionapp \
  --resource-group $resourceGroupName --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```
---

影像大小調整函式是由從 Event Grid 服務傳送給它的 HTTP 要求所觸發。 您可藉由建立事件訂閱，告訴 Event Grid 您想要在您函式的 URL 取得這些通知。 在此教學課程中，您會訂閱 Blob 建立的事件。

從 Event Grid 通知傳遞給函式的資料包括 Blob 的 URL。 該 URL 接著會傳遞至輸入繫結，以從 Blob 儲存體獲取上傳的映像。 此函式會產生縮圖映像，並將產生的串流寫入 Blob 儲存體中的個別容器。

此專案使用 `EventGridTrigger` 作為觸發程序類型。 建議透過一般 HTTP 觸發程序使用 Event Grid 觸發程序。 Event Grid 會自動驗證 Event Grid 函式的觸發程序。 若要使用 HTTP 觸發程序，您必須實作[驗證回應](security-authentication.md)。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

若要深入了解此函式，請參閱 [function.json 和 run.csx 檔案](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/ImageFunctions)。

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

若要深入了解此函式，請參閱 [function.json 和 index.js 檔案](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10/tree/master/Thumbnail)。

---

函式專案程式碼會直接從公用範例存放庫部署。 若要深入了解 Azure Functions 的部署選項，請參閱[Azure Functions 的持續部署](../azure-functions/functions-continuous-deployment.md)。

## <a name="create-an-event-subscription"></a>建立事件訂閱

事件訂閱表示您想要傳送至特定端點之提供者產生的事件。 在此情況下，端點會由函式公開。 使用下列步驟，在 Azure 入口網站中建立事件訂閱，以傳送通知給您的函式：

1. 在 [Azure 入口網站](https://portal.azure.com)中，於頁面頂端搜尋並選取 `Function App`，然後選擇您剛才建立的函式應用程式。 選取 [函式]，然後選擇 [縮圖] 函式。

    :::image type="content" source="media/resize-images-on-storage-blob-upload-event/choose-thumbnail-function.png" alt-text="選擇入口網站中的縮圖功能":::

1.  選取 [整合]，然後選擇 [事件方格觸發程式]並選取 [建立事件方格訂用帳戶]。

    :::image type="content" source="./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png" alt-text="瀏覽至 Azure 入口網站中的新增事件方格訂用帳戶" :::

1. 使用表格中指定的事件訂閱設定。
    
    ![從 Azure 入口網站中的函式建立事件訂閱](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | 設定      | 建議的值  | 描述                                        |
    | ------------ | ---------------- | -------------------------------------------------- |
    | **名稱** | imageresizersub | 用以識別新事件訂閱的名稱。 |
    | **主題類型** | 儲存體帳戶 | 選擇儲存體帳戶事件提供者。 |
    | **訂用帳戶** | 您的 Azure 訂用帳戶 | 預設會選取您目前的 Azure 訂用帳戶。 |
    | **資源群組** | myResourceGroup | 選取 [使用現有]，並選擇您在本教學課程中一直使用的資源群組。 |
    | **Resource** | 您的 Blob 儲存體帳戶 | 選擇您建立的 Blob 儲存體帳戶。 |
    | **系統主題名稱** | imagestoragesystopic | 指定系統主題的名稱。 若要了解系統主題，請參閱[系統主題概觀](system-topics.md)。 |    
    | **事件類型** | 已建立 Blob | 取消勾選 [已建立 Blob] 以外的所有類型。 只有 `Microsoft.Storage.BlobCreated` 的事件類型會傳遞至函式。 |
    | **端點類型** | 自動產生 | 預先定義為 **Azure 函式**。 |
    | **端點** | 自動產生 | 函數的名稱。 在此案例中，此名稱為**縮圖**。 |

1. 切換至 [篩選條件] 索引標籤，並執行下列動作：
    1. 選取 [啟用主旨篩選] 選項。
    2. 針對 [主旨開頭]，輸入下列值： **/blobServices/default/containers/images/blobs/** 。

        ![指定事件訂閱的篩選條件](./media/resize-images-on-storage-blob-upload-event/event-subscription-filter.png)

1. 選取 [建立] 以新增事件訂閱。 當 Blob 新增至 `images` 容器時，這會建立可觸發 `Thumbnail` 函式的事件訂閱。 此函式會調整映像大小，並將其新增至 `thumbnails` 容器。

既然已設定了後端服務，您可以在範例 Web 應用程式中測試映像調整大小功能。

## <a name="test-the-sample-app"></a>測試範例應用程式

若要在 Web 應用程式中測試映像調整大小，請瀏覽至已發佈應用程式的 URL。 Web 應用程式的預設 URL 是 `https://<web_app>.azurewebsites.net`。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

按一下 [上傳相片] 區域，以選取並上傳檔案。 您也可以將相片拖曳到此區域。

請注意，上傳的映像消失之後，上傳映像的複本會顯示在 [產生縮圖] 浮動切換中。 此映像已由函式調整大小、新增至 *thumbnails* 容器，並由 Web 用戶端下載。

![瀏覽器中已發佈的 Web 應用程式](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

按一下 [選擇檔案] 來選取檔案，然後按一下 [上傳映像]。 上傳成功時，瀏覽器會瀏覽至成功頁面。 按一下連結以返回首頁。 所上傳映像的複本會顯示在 [產生縮圖] 區域中。 (如果映像一開始並未出現，請嘗試重新載入頁面)。此映像已由函式調整大小、新增至 *thumbnails* 容器，並由 Web 用戶端下載。

![瀏覽器中已發佈的 Web 應用程式](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立一般的 Azure 儲存體帳戶
> * 使用 Azure Functions 部署無伺服器程式碼
> * 在 Event Grid 中建立 Blob 儲存體事件訂閱

進入儲存體教學課程系列的第三部分，了解如何保護儲存體帳戶的存取權。

> [!div class="nextstepaction"]
> [保護雲端中應用程式資料的存取權](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ 如需深入了解 Event Grid，請參閱 [Event Grid 的簡介](overview.md)。
+ 若要嘗試另一個隨附 Azure Functions 的教學課程，請參閱[建立與 Azure Logic Apps 整合的函式](../azure-functions/functions-twitter-email.md)。

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
