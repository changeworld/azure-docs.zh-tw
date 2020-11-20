---
title: 快速入門：瀏覽器中適用於 JavaScript 的 Azure Blob 儲存體 v10
description: 了解如何在 HTML 網頁中使用 JavaScript v10 SDK 上傳、列出及刪除 Blob。
services: storage
author: mhopkins-msft
ms.custom: mvc, devx-track-js
ms.service: storage
ms.author: mhopkins
ms.date: 07/24/2020
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: 134408704d54479fbbe0dfb5094f2920fa2e74be
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91336185"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-browser"></a>快速入門：使用瀏覽器中的 JavaScript v10 SDK 來管理 Blob

在本快速入門中，您將了解如何使用完全在瀏覽器中執行的 JavaScript 程式碼來管理 Blob。 Blob 是可保存大量文字或二進位資料的物件，包括影像、文件、串流媒體和封存資料。 您將使用必要的安全措施來確保對 Blob 儲存體帳戶的存取會受到保護。

> [!NOTE]
> 本快速入門會使用舊版的 Azure Blob 儲存體用戶端程式庫。 若要開始使用最新版本，請參閱[快速入門：在瀏覽器中使用 JavaScript v12 SDK 來管理 Blob](quickstart-blobs-javascript-browser.md)。

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Azure 儲存體帳戶。 [建立儲存體帳戶](../common/storage-account-create.md)。
- 本機 Web 伺服器。 本文使用 [Node.js](https://nodejs.org) 來開啟基本伺服器。
- [Visual Studio Code](https://code.visualstudio.com) \(英文\)。
- 用於瀏覽器偵錯的 VS Code 延伸模組，例如 [Chrome 偵錯工具](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)或 [Microsoft Edge 偵錯工具](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-edge)。

## <a name="setting-up-storage-account-cors-rules"></a>設定儲存體帳戶 CORS 規則

您必須先設定帳戶以啟用[跨原始資源共用](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) (或簡稱為 CORS)，Web 應用程式才可從用戶端存取 Blob 儲存體。

返回 Azure 入口網站，然後選取您的儲存體帳戶。 若要定義新的 CORS 規則，請瀏覽至 [設定]  區段，然後按一下 [CORS]  連結。 接著，按一下 [新增]  按鈕，以開啟 [新增 CORS 規則]  視窗。 在此快速入門中，您會建立開啟的 CORS 規則：

![Azure Blob 儲存體帳戶 CORS 設定](media/storage-quickstart-blobs-javascript-client-libraries-v10/azure-blob-storage-cors-settings.png)

下表說明每個 CORS 設定以及用來定義規則的值。

|設定  |值  | 描述 |
|---------|---------|---------|
| 允許的原始來源 | * | 接受設為可接受原始來源之網域的逗號分隔清單。 將值設為 `*` 時，會允許所有網域存取儲存體帳戶。 |
| 允許的方法     | delete、get、head、merge、post、options 和 put | 列出可對儲存體帳戶執行的 HTTP 動詞。 基於此快速入門的用途，請選取所有可用選項。 |
| 允許的標頭 | * | 定義儲存體帳戶所允許的要求標頭清單 (包括帶有前置詞的標頭)。 將值設為 `*`，會允許所有標頭存取。 |
| 公開的標頭 | * | 列出帳戶允許的回應標頭。 將值設為 `*`，會允許帳戶傳送任何標頭。  |
| 最長使用期限 (秒) | 86400 | 瀏覽器快取預檢 OPTIONS 要求的時間量上限。 值為 *86400* 時，會允許快取保留一天。 |

> [!IMPORTANT]
> 請確定您在生產環境中使用的任何設定，都只會公開對您的儲存體帳戶所需的最低存取數量，以維護安全存取。 此處所說明的 CORS 設定定義的是寬鬆的安全性原則，適用於快速入門。 不過，不建議將這些設定用於實際的環境中。

接下來，您將使用 Azure Cloud Shell 建立安全性權杖。

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>建立共用存取簽章

在瀏覽器中執行的程式碼會使用共用存取簽章 (SAS) 為 Blob 儲存體的要求授權。 藉由使用 SAS，用戶端不需要帳戶存取金鑰或連接字串，即可為儲存體資源的存取授權。 如需 SAS 的詳細資訊，請參閱[使用共用存取簽章 (SAS)](../common/storage-sas-overview.md)。

您可以透過 Azure Cloud Shell，或是搭配 Azure 入口網站或 Azure 儲存體總管使用 Azure CLI 建立 SAS。 下表說明為使用 CLI 產生 SAS 而需為其提供值的參數。

| 參數      |說明  | 預留位置 |
|----------------|-------------|-------------|
| *expiry*       | 存取權杖到期日 (格式為 YYYY-MM-DD)。 請輸入明天的日期，用於此快速入門。 | *FUTURE_DATE* |
| *account-name* | 儲存體帳戶名稱。 使用在先前的步驟中設定的名稱。 | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | 儲存體帳戶金鑰。 使用在先前的步驟中設定的金鑰。 | *YOUR_STORAGE_ACCOUNT_KEY* |

請使用下列 CLI 命令搭配每個預留位置的實際值，來產生可以在 JavaScript 程式碼中使用的 SAS。

```azurecli-interactive
az storage account generate-sas \
  --permissions racwdl \
  --resource-types sco \
  --services b \
  --expiry FUTURE_DATE \
  --account-name YOUR_STORAGE_ACCOUNT_NAME \
  --account-key YOUR_STORAGE_ACCOUNT_KEY
```

您可能會發現每個參數後面的值序列都有點難以理解。 這些參數值取自其各自權限的第一個字母。 下表說明這些值來自於何處：

| 參數        | 值   | 描述  |
|------------------|---------|---------|
| *權限*    | racwdl  | 此 SAS 允許讀取  、附加  、建立  、寫入  、刪除  和列出  功能。 |
| *resource-types* | sco     | 受到 SAS 影響的資源為服務  、容器  和物件  。 |
| *services*       | b       | 受到 SAS 影響的服務為 Blob  服務。 |

現在，SAS 已產生，請複製傳回值並將其儲存至某處，以用於後續的步驟。 如果您已使用 Azure CLI 以外的方法產生 SAS ，您必須移除開頭的 `?` (如果有的話)。 此字元是 URL 分隔符號，在使用 SAS 的 URL 範本中已提供此符號，本主題稍後會用到此範本。

> [!IMPORTANT]
> 在生產環境中，請一律使用 TLS 傳遞 SAS 權杖。 此外，SAS 權杖應在伺服器上產生，並傳送至 HTML 網頁，以傳回至 Azure Blob 儲存體。 您可以考慮的其中一個方法，是使用無伺服器函式產生 SAS 權杖。 Azure 入口網站包含函式範本，可提供透過 JavaScript 函式產生 SAS 的功能。

## <a name="implement-the-html-page"></a>實作 HTML 網頁

在本節中，您會建立基本網頁，以及設定 VS Code 來啟動頁面並進行偵錯。 但是，啟動之前，您必須在瀏覽器有要求時，使用 Node.js 來啟動本機 Web 伺服器並提供頁面。 接下來，您會新增 JavaScript 程式碼來呼叫各種 Blob 儲存體 API，並在網頁中顯示結果。 您也可以在 [Azure 入口網站](https://portal.azure.com)、[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer)和 VS Code 的 [Azure 儲存體擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)中查看這些呼叫的結果。

### <a name="set-up-the-web-application"></a>設定 Web 應用程式

首先，建立名為 azure-blobs-javascript  的新資料夾，並在 VS Code 中將其開啟。 然後在 VS Code 中建立新檔案，加入下列 HTML，並在 azure-blobs-javascript  資料夾中，將其儲存為 index.html  。

```html
<!DOCTYPE html>
<html>

<body>
    <button id="create-container-button">Create container</button>
    <button id="delete-container-button">Delete container</button>
    <button id="select-button">Select and upload files</button>
    <input type="file" id="file-input" multiple style="display: none;" />
    <button id="list-button">List files</button>
    <button id="delete-button">Delete selected files</button>
    <p><b>Status:</b></p>
    <p id="status" style="height:160px; width: 593px; overflow: scroll;" />
    <p><b>Files:</b></p>
    <select id="file-list" multiple style="height:222px; width: 593px; overflow: scroll;" />
</body>

<!-- You'll add code here later in this quickstart. -->

</html>
```

### <a name="configure-the-debugger"></a>設定偵錯工具

若要在 VS Code 中設定偵錯工具擴充功能，請選取 [偵錯] > [新增組態...]  ，然後選取 [Chrome]  或 [Edge]  ，這取決於您稍早在必要條件一節中安裝的擴充功能。 此動作會建立 launch.json  檔案，並在編輯器中將其開啟。

接下來，修改 launch.json  檔案，讓 `url` 包含 `/index.html`，如下所示：

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:8080/index.html",
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```

這項設定會告訴 VS Code 要啟動哪一個瀏覽器和要載入哪一個 URL。

### <a name="launch-the-web-server"></a>啟動 Web 伺服器

若要啟動本機的 Node.js Web 伺服器，請選取 [檢視] > [終端機]  ，以開啟 VS Code 內的主控台視窗中，然後輸入下列命令。

```console
npx http-server
```

此命令會安裝 http-server  套件，然後啟動伺服器，讓目前資料夾可透過預設 URL 提供 (包括上一個步驟中指出的 URL)。

### <a name="start-debugging"></a>開始偵錯

若要在連結 VS Code 偵錯工具的瀏覽器中啟動 index.html  ，請選取 [偵錯] > [開始偵錯]  ，或在 VS Code 中按 F5。

顯示的 UI 還不會執行任何動作，但您將在下一節中新增 JavaScript 程式碼來實作每個顯示的函式。 然後，您可以設定中斷點，並在偵錯工具於程式碼上暫停時與之互動。

當您變更 index.html  時，請務必重新載入頁面，如此才能在瀏覽器中看到變更。 在 VS Code 中，您也可以選取 [偵錯] > [重新啟動偵錯]  或按 CTRL + SHIFT + F5。

### <a name="add-the-blob-storage-client-library"></a>新增 Blob 儲存體用戶端程式庫

若要對 Blob 儲存體 API 啟用呼叫，請先 [下載適用於 JavaScript 的 Azure 儲存體 SDK - Blob 用戶端程式庫](https://aka.ms/downloadazurestoragejsblob)、將 zip 內容解壓縮，然後將 *azure-storage-blob.js* 檔案放在 *azure-blobs-javascript* 資料夾中。

接下來，將下列 HTML 貼到 index.html  的 `</body>` 結尾標記後面，並取代預留位置註解。

```html
<script src="azure-storage-blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

此程式碼會將參考新增至指令碼檔案，並為您自己的 JavaScript 程式碼提供位置。 基於本快速入門的目的，我們會使用 *azure-storage-blob.js* 指令碼檔案，讓您可以在 VS Code 中將其開啟、讀取其內容，然後設定中斷點。 在生產環境中，您應該使用較精簡的 *azure-storage.blob.min.js* 檔案，這也會以 zip 檔案提供。

您可以在[參考文件](https://docs.microsoft.com/javascript/api/%40azure/storage-blob/index)中深入了解每個 Blob 儲存體函式。 請注意，SDK 中的某些函式只適用於 Node.js，或是僅適用於瀏覽器。

*azure-storage-blob.js* 中的程式碼會匯出名為 `azblob` 的全域變數，您將會在 JavaScript 程式碼中使用此變數來存取 Blob 儲存體 API。

### <a name="add-the-initial-javascript-code"></a>新增最初的 JavaScript 程式碼

接下來，將下列程式碼貼到先前程式碼區塊中顯示的 `<script>` 元素，並取代預留位置註解。

```javascript
const createContainerButton = document.getElementById("create-container-button");
const deleteContainerButton = document.getElementById("delete-container-button");
const selectButton = document.getElementById("select-button");
const fileInput = document.getElementById("file-input");
const listButton = document.getElementById("list-button");
const deleteButton = document.getElementById("delete-button");
const status = document.getElementById("status");
const fileList = document.getElementById("file-list");

const reportStatus = message => {
    status.innerHTML += `${message}<br/>`;
    status.scrollTop = status.scrollHeight;
}
```

此程式碼會為下列程式碼使用的每個 HTML 元素建立欄位，並實作 `reportStatus` 函式來顯示輸出。

在下列各節中，您會將每個新的 JavaScript 程式碼區塊加在前一個區塊後面。

### <a name="add-your-storage-account-info"></a>新增您的儲存體帳戶資訊

接下來，新增程式碼來存取您的儲存體帳戶，將預留位置取代為您的帳戶名稱和您在上一個步驟中產生的 SAS。

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

此程式碼會使用您的帳戶資訊和 SAS 來建立 [ContainerURL](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL) 執行個體，這可用於建立和管理儲存體容器。

### <a name="create-and-delete-a-storage-container"></a>建立及刪除儲存體容器

接下來，新增當您按下對應的按鈕時，建立和刪除儲存體容器的程式碼。

```javascript
const createContainer = async () => {
    try {
        reportStatus(`Creating container "${containerName}"...`);
        await containerURL.create(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

const deleteContainer = async () => {
    try {
        reportStatus(`Deleting container "${containerName}"...`);
        await containerURL.delete(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

createContainerButton.addEventListener("click", createContainer);
deleteContainerButton.addEventListener("click", deleteContainer);
```

此程式碼會呼叫 ContainerURL 的[建立](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-)和[刪除](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-)函式，無須使用 [Aborter](https://docs.microsoft.com/javascript/api/@azure/storage-blob/aborter) 執行個體。 為了簡化本快速入門，此程式碼會假設您已建立並啟用儲存體帳戶。 在生產環境的程式碼中，請使用 Aborter 執行個體來新增逾時功能。

### <a name="list-blobs"></a>列出 Blob

接下來，新增當您按下 [列出檔案]  按鈕時會列出儲存體容器內容的程式碼。

```javascript
const listFiles = async () => {
    fileList.size = 0;
    fileList.innerHTML = "";
    try {
        reportStatus("Retrieving file list...");
        let marker = undefined;
        do {
            const listBlobsResponse = await containerURL.listBlobFlatSegment(
                azblob.Aborter.none, marker);
            marker = listBlobsResponse.nextMarker;
            const items = listBlobsResponse.segment.blobItems;
            for (const blob of items) {
                fileList.size += 1;
                fileList.innerHTML += `<option>${blob.name}</option>`;
            }
        } while (marker);
        if (fileList.size > 0) {
            reportStatus("Done.");
        } else {
            reportStatus("The container does not contain any files.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

listButton.addEventListener("click", listFiles);
```

此程式碼會在迴圈中呼叫 [ContainerURL.listBlobFlatSegment](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) 函式，以確保擷取所有區段。 該函式會針對每個區段，在所包含的 Blob 項目清單上循環，並更新 **檔案** 清單。

### <a name="upload-blobs"></a>上傳 Blob

接下來，新增當您按下 [選取並上傳檔案]  按鈕時，將檔案上傳至儲存體容器的程式碼。

```javascript
const uploadFiles = async () => {
    try {
        reportStatus("Uploading files...");
        const promises = [];
        for (const file of fileInput.files) {
            const blockBlobURL = azblob.BlockBlobURL.fromContainerURL(containerURL, file.name);
            promises.push(azblob.uploadBrowserDataToBlockBlob(
                azblob.Aborter.none, file, blockBlobURL));
        }
        await Promise.all(promises);
        reportStatus("Done.");
        listFiles();
    } catch (error) {
        reportStatus(error.body.message);
    }
}

selectButton.addEventListener("click", () => fileInput.click());
fileInput.addEventListener("change", uploadFiles);
```

此程式碼會將 [選取並上傳檔案]  按鈕連結到隱藏的 `file-input` 元素。 如此一來，`click` 按鈕事件會觸發 `click` 檔案輸入事件，並顯示檔案選擇器。 當您選取檔案並關閉對話方塊後，`input` 事件就會發生，並呼叫 `uploadFiles` 函式。 此函式會針對您選取的每個檔案呼叫瀏覽器專用的 [uploadBrowserDataToBlockBlob](https://docs.microsoft.com/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) 函式。 每個呼叫都會傳回可新增至清單的 Promise，讓所有檔案同時一起等候，這麼一來，檔案就能平行地上傳。

### <a name="delete-blobs"></a>刪除 Blob

接下來，新增當您按下 [刪除選取檔案]  按鈕時，即可從儲存體容器中刪除檔案的程式碼。

```javascript
const deleteFiles = async () => {
    try {
        if (fileList.selectedOptions.length > 0) {
            reportStatus("Deleting files...");
            for (const option of fileList.selectedOptions) {
                const blobURL = azblob.BlobURL.fromContainerURL(containerURL, option.text);
                await blobURL.delete(azblob.Aborter.none);
            }
            reportStatus("Done.");
            listFiles();
        } else {
            reportStatus("No files selected.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

deleteButton.addEventListener("click", deleteFiles);
```

此程式碼會呼叫 [BlobURL.delete](https://docs.microsoft.com/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) 函式來移除清單中選定的每個檔案。 然後呼叫稍早說明的 `listFiles` 函式，來重新整理 **檔案** 清單的內容。

### <a name="run-and-test-the-web-application"></a>執行並測試 Web 應用程式

此時，您可以啟動頁面及進行實驗，以了解 Blob 儲存體的運作方式。 如果發生任何錯誤 (例如您嘗試在建立容器之前列出檔案)，**狀態** 窗格將會顯示收到的錯誤訊息。 您也可以在 JavaScript 程式碼中設定中斷點，以檢查儲存體 API 所傳回的值。

## <a name="clean-up-resources"></a>清除資源

若要清除本快速入門建立的資源，請移至 [Azure 入口網站](https://portal.azure.com)並刪除您在必要條件一節中建立的資源群組。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立可從瀏覽器型 JavaScript 中存取 Blob 儲存體的簡易網站。 若要了解如何在 Blob 儲存體上裝載網站本身，請繼續下列教學課程：

> [!div class="nextstepaction"]
> [將靜態網站裝載於 Blob 儲存體上](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)
