---
title: 管理 Azure 媒體服務中的資產
titleSuffix: Azure Media Services
description: 您輸入媒體的資產（例如，透過上傳或即時內嵌）、輸出媒體（來自作業輸出），以及從發佈媒體（適用于串流處理）。 本主題概述如何建立新的資產並上傳檔案。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80582205"
---
# <a name="manage-assets"></a>管理資產

在 Azure 媒體服務中，[資產](https://docs.microsoft.com/rest/api/media/assets)是您在其中 

* 將媒體檔案上傳到資產中，
* 將即時串流內嵌並封存到資產中，
* 將分析作業的編碼結果輸出至資產，
* 發佈媒體以進行串流處理， 
* 從資產下載檔案。

本主題概述如何將檔案上傳到資產，以及執行一些其他常見的作業。 它也提供程式碼範例和相關主題的連結。

## <a name="prerequisite"></a>必要條件 

在開始開發之前，請檢閱：

* [概念](concepts-overview.md)
* [使用媒體服務 v3 API 進行開發](media-services-apis-overview.md) (包括存取 API、命名慣例等的詳細資訊) 

## <a name="upload-media-files-into-an-asset"></a>將媒體檔案上傳到資產

將數位檔案上傳到儲存體並與資產相關聯之後，就可以在媒體服務編碼、串流和分析內容工作流程中使用這些檔案。 其中一個常見的媒體服務工作流程是上傳、編碼和串流檔案。 本節將概述一般步驟。

1. 使用媒體服務 v3 API 建立新的「輸入」資產。 這項作業會在與您媒體服務帳戶相關聯的儲存體帳戶中建立容器。 此 API 會傳回容器名稱 (例如 `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`)。

    如果您已經有想要與資產相關聯的 blob 容器，您可以在建立資產時指定容器名稱。 媒體服務目前僅支援根目錄中的 Blob 容器，且檔案名稱中不能有路徑。 如此一來，檔案名稱為 "input.mp4" 的容器可運作。 不過，具有 "影片/輸入/input.mp4" 檔案名的容器將無法使用。

    您可以使用 Azure CLI，直接上傳至訂用帳戶中任何您有權存取的儲存體帳戶和容器。

    容器名稱必須是唯一的，並且必須遵循儲存體命名指引。 名稱不需要遵循媒體服務資產容器名稱 (資產-GUID) 的格式。

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. 以讀寫權限取得 SAS URL，這將用來將數位檔案上傳到資產容器。

    您可以使用媒體服務 API 來[列出資產容器 URL](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)。

    **AssetContainerSas. listContainerSas**接受您設定的[ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput)參數 `expiryTime` 。 時間應設定為 < 24 小時。

    [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput)會傳回多個 SAS url，因為每個儲存體帳戶都有兩個儲存體帳戶金鑰。 儲存體帳戶有兩個金鑰，因為它有助於容錯移轉和無縫輪替儲存體帳戶金鑰。 第一個 SAS URL 代表第一個儲存體帳戶金鑰，第二個 SAS URL 代表第二個金鑰。
3. 使用 Azure 儲存體 Api 或 Sdk （例如[儲存體 REST API](../../storage/common/storage-rest-api-auth.md)或[.net SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)）將檔案上傳到資產容器中。
4. 使用媒體服務 v3 API 建立可處理「輸入」資產的轉換和作業。 如需詳細資訊，請參閱[轉換和作業](transform-concept.md)。
5. 串流來自「輸出」資產的內容。

### <a name="create-a-new-asset"></a>建立新的資產

> [!NOTE]
> 資產的日期時間類型屬性一律為 UTC 格式。

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

如需 REST 範例，請參閱[使用 REST 建立資產](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples)。

此範例示範如何建立**要求**本文，您可以在其中指定描述、容器名稱、儲存體帳戶和其他有用的資訊。

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

* [從本機檔案建立作業輸入](job-input-from-local-file-how-to.md)
* [從 HTTPS URL 建立工作輸入](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>將即時資料流內嵌並封存到資產中

在媒體服務中，[即時輸出](https://docs.microsoft.com/rest/api/media/liveoutputs)物件就像是數位錄影機，它會攔截您的即時串流，並將其記錄到您媒體服務帳戶中的資產。 記錄的內容會保存到[資產](https://docs.microsoft.com/rest/api/media/assets)資源所定義的容器中。

如需詳細資訊，請參閱：

* [使用雲端 DVR](live-event-cloud-dvr.md)
* [即時串流教學課程](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>將作業的結果輸出至資產

在媒體服務中，處理您的影片時（例如，編碼或分析），您必須建立輸出[資產](assets-concept.md)來儲存[作業](transforms-jobs-concept.md)的結果。

如需詳細資訊，請參閱：

* [編碼影片](encoding-concept.md)
* [從本機檔案建立作業輸入](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>發行資產以進行串流

若要發佈資產以進行串流處理，您必須建立[串流定位器](streaming-locators-concept.md)。 串流定位器必須知道您想要發佈的資產名稱。 

如需詳細資訊，請參閱：

[教學課程：使用媒體服務 v3 上傳、編碼和串流影片](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>從輸出資產下載作業的結果

接著，您可以使用媒體服務和儲存體 Api，將您的作業結果下載到本機資料夾。 

請參閱[下載檔案](download-results-howto.md)範例。

## <a name="filtering-ordering-paging"></a>篩選、排序、分頁

請參閱[媒體服務實體的篩選、排序、分頁](entities-overview.md)。

## <a name="next-steps"></a>後續步驟

查看完整的程式碼範例，示範如何上傳、編碼、分析、即時串流和隨選： 

* [JAVA](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/)、 
* [.Net](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/)、 
* [REST](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/)。
