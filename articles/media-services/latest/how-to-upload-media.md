---
標題：上傳媒體： Azure 媒體服務描述：瞭解如何上傳媒體以進行串流或編碼。
服務： media services documentationcenter： ' ' author： IngridAtMicrosoft manager： femila editor： ' '

ms. 服務：媒體服務 ms. 工作負載： ms. 主題：作法 ms。日期： 08/31/2020 ms. 作者： inhenkel
---

# <a name="upload-media-for-streaming-or-encoding"></a>上傳媒體以進行串流或編碼

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

在媒體服務中，您會將數位檔案 (媒體) 上傳到與資產相關聯的 Blob 容器。 [資產](/rest/api/media/operations/asset)實體可以包含視訊、音訊、影像、縮圖集合、文字播放軌及隱藏式輔助字幕檔案 (以及這些檔案的相關中繼資料)。 一旦檔案會上傳到資產的容器，您的內容會安全地儲存在雲端，以便進行進一步的處理和串流。

在開始之前，您必須先收集或思考幾個值。

1. 所要上傳檔案的本機檔案路徑
1. 資產 (容器) 的資產識別碼
1. 您想要用於上傳檔案的名稱，包括副檔名
1. 所使用儲存體帳戶的名稱
1. 儲存體帳戶的存取金鑰

## <a name="portal"></a>[入口網站](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli.md)]

## <a name="rest"></a>[REST](#tab/rest/)

一旦您[使用 Postman 或其他 REST 方法建立資產並取得資產的 SAS URL](how-to-create-asset.md?tabs=rest) 之後，請使用 Azure 儲存體 API 或 SDK (例如[儲存體 REST API](../../storage/common/storage-rest-api-auth.md) 或 [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)。

---
<!-- add these to the tabs when available -->
至於其他方法，請參閱 [Azure 儲存體文件](../../storage/blobs/index.yml)，以了解如何在 [.NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)、[Java](../../storage/blobs/storage-quickstart-blobs-java.md)、[Python](../../storage/blobs/storage-quickstart-blobs-python.md) 和 [JavaScript (Node.js)](../../storage/blobs/storage-quickstart-blobs-nodejs.md) 中使用 Blob。

## <a name="next-steps"></a>後續步驟

> [媒體服務概觀](media-services-overview.md)