---
title: 管理 Azure 媒體服務中的資產
titleSuffix: Azure Media Services
description: 輸入媒體（例如，通過上傳或即時引入）、輸出媒體（從作業輸出）和發佈媒體（用於資料流）的資產。 本主題概述了如何創建新資產和上載檔。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: fcdb8af770fa0068e8413d4609a56223a9a20ce2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345904"
---
# <a name="manage-assets"></a>管理資產

在 Azure 媒體服務中，[資產](https://docs.microsoft.com/rest/api/media/assets)是您 

* 將媒體檔案上載到資產中，
* 將即時流引入並存檔到資產中，
* 將分析作業編碼的結果輸出到資產，
* 發佈用於資料流的媒體， 
* 從資產下載檔案。

本主題概述了如何將檔上載到資產和執行一些其他常見操作。 它還提供指向代碼示例和相關主題的連結。

## <a name="prerequisite"></a>必要條件 

在開始開發之前，請檢閱：

* [概念](concepts-overview.md)
* [使用媒體服務 v3 API 進行開發](media-services-apis-overview.md) (包括存取 API、命名慣例等的詳細資訊) 

## <a name="upload-media-files-into-an-asset"></a>將媒體檔案上載到資產

數位檔上載到存儲並與資產關聯後，可以在媒體服務編碼、資料流和分析內容工作流中使用。 其中一個常見的媒體服務工作流程是上傳、編碼和串流檔案。 本節將概述一般步驟。

1. 使用媒體服務 v3 API 建立新的「輸入」資產。 這項作業會在與您媒體服務帳戶相關聯的儲存體帳戶中建立容器。 此 API 會傳回容器名稱 (例如 `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`)。

    如果已有要與資產關聯的 Blob 容器，則可以在創建資產時指定容器名稱。 媒體服務目前僅支援根目錄中的 Blob 容器，且檔案名稱中不能有路徑。 如此一來，檔案名稱為 "input.mp4" 的容器可運作。 但是，具有"視頻/輸入/輸入.mp4"檔案名的容器將不起作用。

    您可以使用 Azure CLI，直接上傳至訂用帳戶中任何您有權存取的儲存體帳戶和容器。

    容器名稱必須是唯一的，並且必須遵循儲存體命名指引。 名稱不需要遵循媒體服務資產容器名稱 (資產-GUID) 的格式。

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. 以讀寫權限取得 SAS URL，這將用來將數位檔案上傳到資產容器。

    您可以使用媒體服務 API 來[列出資產容器 URL](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)。

    **資產容器Sas.listContainerSas**採用一個[清單容器As輸入](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput)參數，你設置`expiryTime`。 時間應設置為< 24 小時。

    [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput)返回多個 SAS URL，因為每個存儲帳戶有兩個存儲帳戶金鑰。 存儲帳戶有兩個金鑰，因為它允許無縫輪換存儲帳戶金鑰（例如，使用另一個金鑰更改一個金鑰，然後開始使用新金鑰並旋轉另一個金鑰）。 第 1 個 SAS URL 表示存儲金鑰 1 和第二個存儲金鑰2。
3. 使用 Azure 存儲 API 或 SDK（例如，[存儲 REST API](../../storage/common/storage-rest-api-auth.md)或[.NET SDK）](../../storage/blobs/storage-quickstart-blobs-dotnet.md)將檔上載到資產容器中。
4. 使用媒體服務 v3 API 建立可處理「輸入」資產的轉換和作業。 有關詳細資訊，請參閱[轉換和作業](transform-concept.md)。
5. 串流來自「輸出」資產的內容。

### <a name="create-a-new-asset"></a>建立新的資產

> [!NOTE]
> 日期時間類型的資產屬性始終採用 UTC 格式。

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

如需 REST 範例，請參閱[使用 REST 建立資產](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples)。

該示例演示如何創建**請求正文**，您可以在其中指定說明、容器名稱、存儲帳戶和其他有用資訊。

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

### <a name="see-also"></a>另請參閱

* [從本地檔創建作業輸入](job-input-from-local-file-how-to.md)
* [從 HTTPS URL 建立工作輸入](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>將即時流引入並存檔到資產中

在媒體服務中，[即時輸出](https://docs.microsoft.com/rest/api/media/liveoutputs)物件類似于數位錄影機，它將捕獲您的即時流並將其記錄到媒體服務帳戶中的資產中。 記錄的內容將保存到[資產](https://docs.microsoft.com/rest/api/media/assets)資源定義的容器中。

如需詳細資訊，請參閱

* [使用雲端 DVR](live-event-cloud-dvr.md)
* [資料流即時教程](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>將作業的結果輸出到資產

在媒體服務中，在處理視頻（例如編碼或分析）時，您需要創建一個輸出[資產](assets-concept.md)來存儲[作業](transforms-jobs-concept.md)的結果。

如需詳細資訊，請參閱

* [對視頻進行編碼](encoding-concept.md)
* [從本地檔創建作業輸入](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>發佈用於流式處理的資產

要發佈用於流式處理的資產，您需要創建[流式處理器](streaming-locators-concept.md)。 流式處理器需要知道要發佈的資產名稱。 

如需詳細資訊，請參閱

[教程：使用媒體服務 v3 上傳、編碼和資料流視頻](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>從輸出資產下載作業結果

然後，您可以使用媒體服務和存儲 API 將作業的這些結果下載到本地資料夾。 

請參閱[下載檔案](download-results-howto.md)示例。

## <a name="filtering-ordering-paging"></a>篩選、排序、分頁

請參閱[媒體服務實體的篩選、排序、分頁](entities-overview.md)。

## <a name="next-steps"></a>後續步驟

請參閱演示如何上傳、編碼、分析、即時資料流和按需資料流的完整代碼示例： 

* [爪哇](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/)， 
* [.NET](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/)， 
* [REST](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).
