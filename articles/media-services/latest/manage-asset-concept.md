---
title: 管理 Azure 媒體服務中的資產
titleSuffix: Azure Media Services
description: 輸入媒體(例如,通過上傳或即時引入)、輸出媒體(從作業輸出)和發佈媒體(用於流式傳輸)的資產。 本主題概述了如何創建新資產和上傳檔。
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
ms.openlocfilehash: 9136fd702fad5c12a8ec97a68ff8a592a203d7d2
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582205"
---
# <a name="manage-assets"></a>管理資產

在 Azure 媒體服務中,[資產](https://docs.microsoft.com/rest/api/media/assets)是您 

* 將媒體檔上載到資產中,
* 將即時流引入並存檔到資產中,
* 將分析作業編碼的結果輸出到資產,
* 發佈用於流式傳輸的媒體, 
* 從資產下載檔。

本主題概述了如何將檔上載到資產和執行一些其他常見操作。 它還提供指向代碼示例和相關主題的連結。

## <a name="prerequisite"></a>必要條件 

在開始開發之前，請檢閱：

* [概念](concepts-overview.md)
* [使用媒體服務 v3 API 進行開發](media-services-apis-overview.md) (包括存取 API、命名慣例等的詳細資訊) 

## <a name="upload-media-files-into-an-asset"></a>將媒體檔案上傳到資產

數位檔上載到存儲並與資產關聯後,可以在媒體服務編碼、流式傳輸和分析內容工作流中使用。 其中一個常見的媒體服務工作流程是上傳、編碼和串流檔案。 本節將概述一般步驟。

1. 使用媒體服務 v3 API 建立新的「輸入」資產。 這項作業會在與您媒體服務帳戶相關聯的儲存體帳戶中建立容器。 此 API 會傳回容器名稱 (例如 `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`)。

    如果已有要與資產關聯的 Blob 容器,則可以在創建資產時指定容器名稱。 媒體服務目前僅支援根目錄中的 Blob 容器，且檔案名稱中不能有路徑。 如此一來，檔案名稱為 "input.mp4" 的容器可運作。 但是,具有「視頻/輸入/輸入.mp4」檔名的容器將不起作用。

    您可以使用 Azure CLI，直接上傳至訂用帳戶中任何您有權存取的儲存體帳戶和容器。

    容器名稱必須是唯一的，並且必須遵循儲存體命名指引。 名稱不需要遵循媒體服務資產容器名稱 (資產-GUID) 的格式。

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. 以讀寫權限取得 SAS URL，這將用來將數位檔案上傳到資產容器。

    您可以使用媒體服務 API 來[列出資產容器 URL](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)。

    **資產容器Sas.listContainerSas**以[清單容器As輸入](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput)參數,你設定`expiryTime`。 時間應設置為< 24 小時。

    [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput)傳回多個 SAS URL,因為每個儲存帳戶有兩個儲存帳戶金鑰。 存儲帳戶有兩個密鑰,因為它有助於故障轉移和存儲帳戶密鑰的無縫輪換。 第一個 SAS URL 表示第一個儲存帳戶金鑰,第二個 SAS URL 表示第二個密鑰。
3. 使用 Azure 儲存 API 或 SDK(例如,[儲存 REST API](../../storage/common/storage-rest-api-auth.md)或[.NET SDK)](../../storage/blobs/storage-quickstart-blobs-dotnet.md)將檔案上載到資產容器中。
4. 使用媒體服務 v3 API 建立可處理「輸入」資產的轉換和作業。 有關詳細資訊,請參閱[轉換和作業](transform-concept.md)。
5. 串流來自「輸出」資產的內容。

### <a name="create-a-new-asset"></a>建立新的資產

> [!NOTE]
> 日期時間類型的資產屬性始終採用UTC格式。

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

如需 REST 範例，請參閱[使用 REST 建立資產](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples)。

該範例示範如何建立**請求正文**,您可以在其中指定說明、容器名稱、儲存帳戶和其他有用資訊。

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

* [從本地檔案建立工作輸入](job-input-from-local-file-how-to.md)
* [從 HTTPS URL 建立工作輸入](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>將即時流引入並存檔到資產中

在媒體服務中,[即時輸出](https://docs.microsoft.com/rest/api/media/liveoutputs)物件類似於數位錄影機,它將捕獲您的即時流並將其記錄到媒體服務帳戶中的資產中。 記錄的內容將保存到[資產](https://docs.microsoft.com/rest/api/media/assets)資源定義的容器中。

如需詳細資訊，請參閱

* [使用雲端 DVR](live-event-cloud-dvr.md)
* [串流式傳輸即時教學](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>將工作的結果輸出到資產

在媒體服務中,在處理視頻(例如編碼或分析)時,您需要創建一個輸出[資產](assets-concept.md)來儲存[作業](transforms-jobs-concept.md)的結果。

如需詳細資訊，請參閱

* [對影片進行編碼](encoding-concept.md)
* [從本地檔案建立工作輸入](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>發佈用於流式處理的資產

要發佈用於流式處理的資產,您需要建立[串流式處理器](streaming-locators-concept.md)。 流式處理器需要知道要發佈的資產名稱。 

如需詳細資訊，請參閱

[教學:使用媒體服務 v3 上傳、編碼和串流式傳輸視訊](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>從輸出資產下載作業結果

然後,您可以使用媒體服務和存儲 API 將作業的這些結果下載到本地資料夾。 

請參閱[下載檔](download-results-howto.md)範例。

## <a name="filtering-ordering-paging"></a>篩選、排序、分頁

請參閱[媒體服務實體的篩選、排序、分頁](entities-overview.md)。

## <a name="next-steps"></a>後續步驟

請參考示範如何上傳、編碼、分析、即時流式傳輸和按需流式傳輸的完整代碼範例: 

* [爪哇](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.NET](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [REST](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).
