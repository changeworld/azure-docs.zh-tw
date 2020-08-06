---
title: 快速入門：Azure Blob 儲存體程式庫 v12 - JavaScript
description: 在本快速入門中，您將了解如何使用適用於 JavaScript 的 Azure Blob 儲存體用戶端程式庫 12 版，在 Blob (物件) 儲存體中建立容器與 Blob。 接下來，您要了解如何將 Blob 下載到本機電腦，以及如何列出容器中的所有 Blob。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-javascript
ms.openlocfilehash: 8798808741fe5d659ed7afe96f415964296d356f
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87429137"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>快速入門：使用 Node.js 中的 JavaScript v12 SDK 來管理 Blob

在本快速入門中，您將了解如何使用 Node.js 來管理 Blob。 Blob 是可保存大量文字或二進位資料的物件，包括影像、文件、串流媒體和封存資料。 您將上傳、下載及列出 Blob，且將建立和刪除容器。

其他資源：

* [API 參考文件](/javascript/api/@azure/storage-blob)
* [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob)
* [套件 (節點套件管理員)](https://www.npmjs.com/package/@azure/storage-blob)
* [範例](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Azure 儲存體帳戶。 [建立儲存體帳戶](../common/storage-account-create.md)。
- [Node.js](https://nodejs.org/en/download/).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>設定

本節會引導您準備專案以搭配適用於 JavaScript 的 Azure Blob 儲存體用戶端程式庫 v12 使用。

### <a name="create-the-project"></a>建立專案

建立一個名為 *blob-quickstart-v12* 的 JavaScript 應用程式。

1. 在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為專案建立一個新目錄。

    ```console
    mkdir blob-quickstart-v12
    ```

1. 切換至新建立的 *blob-quickstart-v12* 目錄。

    ```console
    cd blob-quickstart-v12
    ```

1. 建立一個名為 *package.json* 的新文字檔。 此檔案會定義 Node.js 專案。 將此檔案儲存在 *blob-quickstart-v12* 目錄中。 以下是此檔案的內容：

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    如有需要，您可以將自己的姓名放在 `author` 欄位中。

### <a name="install-the-package"></a>安裝套件

若您仍在 *blob-quickstart-v12* 目錄中，請使用 `npm install` 命令安裝適用於 JavaScript 套件的 Azure Blob 儲存體用戶端程式庫。 此命令會讀取 *package.json* 檔案，並安裝適用於 JavaScript 套件的 Azure Blob 儲存體用戶端程式庫 v12，以及它所相依的所有程式庫。

```console
npm install
```

### <a name="set-up-the-app-framework"></a>設定應用程式架構

從專案目錄：

1. 在程式碼編輯器中開啟另一個新的文字檔
1. 加入 `require` 呼叫以載入 Azure 和 Node.js 模組
1. 建立程式的結構，包括基本例外狀況處理

    此程式碼如下：

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');

    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }

    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. 將新檔案以 *blob-quickstart-v12.js* 儲存在 *blob-quickstart-v12* 目錄中。

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>物件模型

Azure Blob 儲存體經過最佳化，已能妥善儲存大量的非結構化資料。 非結構化資料是指不遵守特定資料模型或定義的資料，例如文字或二進位資料。 Blob 儲存體提供三種類型資源：

* 儲存體帳戶
* 儲存體帳戶中的容器
* 容器中的 Blob

下圖顯示資源之間的關係。

![Blob 儲存體架構圖](./media/storage-blobs-introduction/blob1.png)

使用下列 JavaScript 類別與這些資源互動：

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient)：`BlobServiceClient` 類別可讓您操作 Azure 儲存體資源和 Blob 容器。
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient)：`ContainerClient` 類別可讓您操作 Azure 儲存體容器及其 Blob。
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient)：`BlobClient` 類別可讓您操作 Azure 儲存體 Blob。

## <a name="code-examples"></a>程式碼範例

這些範例程式碼片段會示範如何使用適用於 JavaScript 的 Azure Blob 儲存體用戶端程式庫執行下列動作：

* [取得連接字串](#get-the-connection-string)
* [建立容器](#create-a-container)
* [將 Blob 上傳至容器](#upload-blobs-to-a-container)
* [列出容器中的 Blob](#list-the-blobs-in-a-container)
* [下載 Blob](#download-blobs)
* [刪除容器](#delete-a-container)

### <a name="get-the-connection-string"></a>取得連接字串

下列程式碼會從[設定儲存體連接字串](#configure-your-storage-connection-string)一節中建立的環境變數，擷取儲存體帳戶的連接字串。

在 `main` 函式內新增此程式碼：

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-container"></a>建立容器

決定新容器的名稱。 下列程式碼會將 UUID 值附加到容器名稱，以確保它是唯一的。

> [!IMPORTANT]
> 容器名稱必須是小寫字母。 如需為容器和 Blob 命名的詳細資訊，請參閱[命名和參考容器、Blob 及中繼資料](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

呼叫 [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) 方法以建立 [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) 類別的執行個體。 接著，呼叫 [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) 方法以取得容器的參考。 最後，呼叫 [create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-)，以實際在您的儲存體帳戶中建立容器。

將此程式碼加入到 `main` 函式的結尾處：

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>將 Blob 上傳至容器

下列程式碼片段：

1. 建立要上傳至 Blob 的文字字串。
1. 從[建立容器](#create-a-container)一節的 [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) 上呼叫 [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) 方法，以取得 [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) 物件的參考。
1. 呼叫 [upload](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) 方法，將文字字串資料上傳至 Blob。

將此程式碼加入到 `main` 函式的結尾處：

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
```

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

呼叫 [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) 方法，以列出容器中的 Blob。 在此案例中，只有一個 Blob 新增至容器，所以清單作業只會傳回一個 Blob。

將此程式碼加入到 `main` 函式的結尾處：

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>下載 Blob

呼叫 [download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) 方法，以下載先前建立的 Blob。 範例程式碼包含名為 `streamToString` 的協助程式函式，可用來將 Node.js 可讀取資料流讀取到字串中。

將此程式碼加入到 `main` 函式的結尾處：

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

在 `main` 函式*後面*，加入此 helper 函式：

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>刪除容器

下列程式碼會使用 [delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) 方法移除整個容器，以清除應用程式所建立的資源。 您也可以視需要刪除本機檔案。

將此程式碼加入到 `main` 函式的結尾處：

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>執行程式碼

此應用程式會建立一個文字字串，並將其上傳至 Blob 儲存體。 接著，此範例會列出容器中的 Blob、下載 Blob，並顯示已下載的資料。

從主控台提示中，瀏覽至包含 *blob-quickstart-v12.py* 檔案的目錄，然後執行下列 `node` 命令來執行應用程式。

```console
node blob-quickstart-v12.js
```

應用程式的輸出類似下列範例：

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

使用偵錯工具逐步執行程式碼，並在整個過程中檢查您的 [Azure 入口網站](https://portal.azure.com)。 請檢查是否正在建立容器。 您可以在容器內開啟 Blob，並查看內容。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 JavaScript 上傳、下載及列出 Blob。

如需教學課程、範例、快速入門及其他文件，請瀏覽：

> [!div class="nextstepaction"]
> [Azure for JavaScript 文件](/azure/developer/javascript/)

* 若要深入了解，請參閱[適用於 JavaScript 的 Azure Blob 儲存體用戶端程式庫](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob)。
* 若要查看 Blob 儲存體範例應用程式，請繼續執行 [Azure Blob 儲存體用戶端程式庫 v12 JavaScript 範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)。
