---
title: 使用 Azure 媒體服務 v3 .NET SDK 建立篩選器
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
ms.date: 08/11/2020
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 96fd2fbc087fda422271aef0dfac34f066f09b11
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89009656"
---
# <a name="create-filters-with-media-services-net-sdk"></a>使用媒體服務 .NET SDK 建立篩選器

當提供您的內容給客戶 (串流處理即時事件或點播視訊) 時，您的用戶端可能需要比預設資產資訊清單檔案中所述還大的彈性。 Azure 媒體服務可讓您為您的內容定義帳戶篩選器與資產篩選器。 

如需這項功能的詳細說明，以及使用它的案例，請參閱 [動態資訊清單](filters-dynamic-manifest-overview.md) 和 [篩選器](filters-concept.md)。

本主題說明如何使用媒體服務 .NET SDK 為點播視訊資產定義篩選器，以及如何建立[帳戶篩選器](/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet)與[資產篩選器](/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet)。 

> [!NOTE]
> 請務必查看 [presentationTimeRange](filters-concept.md#presentationtimerange)。

## <a name="prerequisites"></a>先決條件 

- 檢閱[篩選器與動態資訊清單](filters-dynamic-manifest-overview.md)。
- [建立媒體服務帳戶](./create-account-howto.md)。 請務必記住資源群組名稱和「媒體服務」帳戶名稱。 
- 取得所需資訊來[存取 API](./access-api-howto.md)
- 檢閱[使用 Azure 媒體服務執行上傳、編碼和串流](stream-files-tutorial-with-api.md)，以了解如何[開始使用 .NET SDK](stream-files-tutorial-with-api.md#start-using-media-services-apis-with-net-sdk)

## <a name="define-a-filter"></a>定義篩選器  

在 .NET 中，您可以使用 [FilterTrackSelection](/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) 和 [FilterTrackPropertyCondition](/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) 類別來設定曲目選取範圍。 

下列程式碼會定義篩選，其中包括具有 EC-3 的任何音訊曲目，以及位元速率介於 0-1000000 範圍之間的任何視訊曲目。

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>建立帳戶篩選器

下列程式碼示範如何使用 .NET 來建立帳戶篩選器，其中包括[以上定義](#define-a-filter)的所有曲目選取範圍。 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>建立資產篩選器

下列程式碼示範如何使用 .NET 來建立資產篩選器，其中包括[以上定義](#define-a-filter)的所有曲目選取範圍。 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>將篩選準則與串流定位器建立關聯

您可以指定資產或帳戶篩選器的清單，這會套用至您的串流定位器。 [動態封裝 (串流端點) ](dynamic-packaging-overview.md)會將這份篩選清單與您的用戶端在 URL 中指定的篩選準則一起套用。 此組合會產生 [動態資訊清單](filters-dynamic-manifest-overview.md)，這是以 URL 中的篩選器和您在串流定位器上指定的篩選器為基礎。 如果您想要套用篩選，但不想公開 URL 中的篩選名稱，建議您使用這項功能。

下列 c # 程式碼說明如何建立串流定位器並指定 `StreamingLocator.Filters` 。 這是選擇性的屬性，接受 `IList<string>` 篩選名稱的。

```csharp
IList<string> filters = new List<string>();
filters.Add("filterName");

StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        Filters = filters
    });
```
      
## <a name="stream-using-filters"></a>使用篩選的資料流程

定義篩選條件後，您的用戶端就可以在串流 URL 中使用它們。 篩選器可以套用至自適性串流通訊協定：Apple HTTP 即時串流 (HLS)、MPEG-DASH 和 Smooth Streaming。

下表顯示包含篩選器之 URL 的一些範例：

|通訊協定|範例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>後續步驟

[串流影片](stream-files-tutorial-with-api.md) 
