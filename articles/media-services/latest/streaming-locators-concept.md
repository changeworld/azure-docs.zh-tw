---
title: Azure 媒體服務中的串流定位器 | Microsoft Docs
description: 本文解釋串流定位器是什麼，以及 Azure 媒體服務用它們來做什麼。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/04/2020
ms.author: juliako
ms.openlocfilehash: 41b2d0ad1e072fb2bf5860ae80f8f25f886b37f7
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582682"
---
# <a name="streaming-locators"></a>串流定位器

若要讓輸出資產中的影片可供用戶端播放，您必須建立[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators)，然後建置串流 URL。 若要建置 URL，您需要串連串流端點主機名稱和串流定位器路徑。 如需 .NET 範例，請參閱[取得串流定位器](stream-files-tutorial-with-api.md#get-a-streaming-locator)。

創建**流式處理器的過程**稱為發佈。 預設情況下,**串流式處理器在**進行 API 呼叫後立即有效,並持續到刪除為止,除非您配置可選的開始和結束時間。 

建立**流式處理器時**,必須指定**資產**名稱和**流式處理策略**名稱。 如需詳細資訊，請參閱下列主題：

* [Assets](assets-concept.md)
* [串流原則](streaming-policy-concept.md)
* [內容金鑰原則](content-key-policy-concept.md)

您還可以在流式處理器上指定開始和結束時間,這僅允許使用者在這些時間之間播放內容(例如,在 2019 年 5 月 1 日至 2019 年 5 月 5 日之間)。  

## <a name="considerations"></a>考量

* **流式處理器**不可上用。 
* 屬於日期時間類型的**串流定位器**屬性一律為 UTC 格式。
* 您應該為媒體服務帳戶設計一組受限的原則，並且在需要相同的選項時，對串流定位器重新使用這些原則。 有關詳細資訊,請參閱[配額和限制](limits-quotas-constraints.md)。

## <a name="create-streaming-locators"></a>建立流式處理器  

### <a name="not-encrypted"></a>未加密

如果要在清除中流式傳輸檔(非加密),請將預定義的透明流式處理策略設置為"Predefined_ClearStreamingOnly"(在 .NET 中,可以使用預定義的流式處理策略.ClearStreamOnly 枚舉)。

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>已加密 

如果需要使用CENC加密加密內容,請將策略設置為"Predefined_MultiDrmCencStreaming"。 寬文加密將應用於 DASH 流,並播放就緒平滑。 金鑰將根據配置的 DRM 許可證傳遞到播放用戶端。

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

如果您還希望使用 CBCS (FairPlay) 加密 HLS 流,請使用"Predefined_MultiDrmStreaming"。

> [!NOTE]
> Widevine 是 Google Inc. 所提供的服務，並受到 Google Inc. 的服務條款和隱私權原則所約束。

## <a name="associate-filters-with-streaming-locators"></a>將篩選器與串流式處理器關聯

請參考[篩選器:與串流式處理器關聯](filters-concept.md#associating-filters-with-streaming-locator)。

## <a name="filter-order-page-streaming-locator-entities"></a>篩選器、訂單、頁面流式處理器實體

請參閱[媒體服務實體的篩選、排序、分頁](entities-overview.md)。

## <a name="list-streaming-locators-by-asset-name"></a>依資產名稱列出流式處理器

要根據關聯的資產名稱獲取流式處理器,請使用以下操作:

|Language|API|
|---|---|
|REST|[清單串流定位器](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams 資產清單-串流式定位器](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[清單串流定位器](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[資產流定位器](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>另請參閱

* [Assets](assets-concept.md)
* [串流原則](streaming-policy-concept.md)
* [內容金鑰原則](content-key-policy-concept.md)
* [教學:使用 .NET 上傳、編碼和串流式傳輸視訊](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>後續步驟

[如何建立流式定位器和產生網址](create-streaming-locator-build-url.md)
