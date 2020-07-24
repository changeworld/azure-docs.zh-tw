---
title: 使用 Azure 媒體服務 v3 REST API 建立篩選器
description: 本主題說明如何建立篩選器，讓您的用戶端可以使用篩選器來串流特定的資料流區段。 媒體服務會建立動態資訊清單來完成此選擇性資料流。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 93ef342ecee9bb9e2f32400655db0cacc01835c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023104"
---
# <a name="creating-filters-with-media-services-rest-api"></a>使用媒體服務 REST API 建立篩選器

當提供您的內容給客戶 (串流處理即時事件或點播視訊) 時，您的用戶端可能需要比預設資產資訊清單檔案中所述還大的彈性。 Azure 媒體服務可讓您為您的內容定義帳戶篩選器與資產篩選器。 

如需這項功能和使用方式的詳細描述，請參閱[動態資訊清單](filters-dynamic-manifest-overview.md)和[篩選](filters-concept.md)。

此主題說明如何為點播視訊資產建立篩選器，以及如何使用 REST API 來建立[帳戶篩選器](/rest/api/media/accountfilters) \(英文\) 與[資產篩選器](/rest/api/media/assetfilters) \(英文\)。 

> [!NOTE]
> 請務必參閱[presentationTimeRange](filters-concept.md#presentationtimerange)。

## <a name="prerequisites"></a>先決條件 

若要完成此主題中所述的步驟，您必須：

- 檢閱[篩選器與動態資訊清單](filters-dynamic-manifest-overview.md)。
- [設定 Postman 以進行 Azure 媒體服務 REST API 呼叫](media-rest-apis-with-postman.md)。

    請務必遵循本[取得 Azure AD 權杖](media-rest-apis-with-postman.md#get-azure-ad-token)主題的最後一個步驟。 

## <a name="define-a-filter"></a>定義篩選器  

下面提 **要求本文**範例，它定義新增到資訊清單的曲目選取條件。 此篩選包含格式為 EC-3 的所有音軌，以及位元速率介於 0-1000000 的所有影像軌。

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>建立帳戶篩選器

在您下載的 Postman 集合中，選取 [**帳戶篩選器**] [ -> **建立或更新帳戶篩選器**]。

**PUT** HTTP 要求方法類似：

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

選取 [本文]**** 索引標籤，並貼上您[稍早定義的 json 程式碼](#define-a-filter)。

選取 [傳送]。 

篩選器已建立。

如需詳細資訊，請參閱 [建立或更新](/rest/api/media/accountfilters/createorupdate) \(英文\)。 此外，也請參閱[篩選器的 JSON 範例](/rest/api/media/accountfilters/createorupdate#create-an-account-filter)。

## <a name="create-asset-filters"></a>建立資產篩選器  

在您下載的 "媒體服務 v3" Postman 集合中，選取 [**資產**] [ -> **建立或更新資產篩選器**]。

**PUT** HTTP 要求方法類似：

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

選取 [本文]**** 索引標籤，並貼上您[稍早定義的 json 程式碼](#define-a-filter)。

選取 [傳送]。 

資產篩選器已建立。

如需有關如何建立或更新資產篩選器的詳細資訊，請參閱[建立或更新](/rest/api/media/assetfilters/createorupdate) \(英文\)。 此外，也請參閱[篩選器的 JSON 範例](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter)。 

## <a name="associate-filters-with-streaming-locator"></a>將篩選器與串流定位器建立關聯

您可以指定資產或帳戶篩選器的清單，其適用于您的串流定位器。 動態封裝程式[（串流端點）](dynamic-packaging-overview.md)會將這份篩選器清單與您的用戶端在 URL 中指定的篩選準則一起套用。 這個組合會產生[動態資訊清單](filters-dynamic-manifest-overview.md)，這是根據 URL 中的篩選器，以及您在串流定位器上指定的篩選準則。 如果您想要套用篩選，但不想要在 URL 中公開篩選名稱，建議您使用這項功能。

若要使用 REST 建立篩選器，並將其與串流定位器建立關聯，請使用[串流定位器-建立](/rest/api/media/streaminglocators/create)API，並 `properties.filters` 在[要求主體](/rest/api/media/streaminglocators/create#request-body)中指定。
                                
## <a name="stream-using-filters"></a>使用篩選器串流

定義篩選條件後，您的用戶端就可以在串流 URL 中使用它們。 篩選器可以套用至自適性串流通訊協定：Apple HTTP 即時串流 (HLS)、MPEG-DASH 和 Smooth Streaming。

下表顯示包含篩選器之 URL 的一些範例：

|通訊協定|範例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>接下來的步驟

[串流影片](stream-files-tutorial-with-rest.md) 
