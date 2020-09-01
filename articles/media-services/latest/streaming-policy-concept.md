---
title: Azure 媒體服務中的串流原則 | Microsoft Docs
description: 本文解釋串流原則是什麼，以及 Azure 媒體服務用它們來做什麼。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/28/2019
ms.author: inhenkel
ms.openlocfilehash: 529d27a831945123e1d2eda225e40864a81ee18e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89257563"
---
# <a name="streaming-policies"></a>串流原則

Azure 媒體服務 v3 中，[串流原則](/rest/api/media/streamingpolicies)可讓您設定[串流定位器](streaming-locators-concept.md)的串流通訊協定和加密選項。 媒體服務 v3 提供一些預先定義的串流原則，讓您可以直接在試用或生產環境中使用它們。 

目前可用的預先定義串流原則：<br/>
* ' Predefined_DownloadOnly '
* ' Predefined_ClearStreamingOnly '
* ' Predefined_DownloadAndClearStreaming '
* ' Predefined_ClearKey '
* ' Predefined_MultiDrmCencStreaming ' 
* ' Predefined_MultiDrmStreaming '

下列「決策樹」可協助您為您的案例選擇預先定義的串流原則。

> [!IMPORTANT]
> * 屬於日期時間類型的**串流原則**屬性一律為 UTC 格式。
> * 您應該為媒體服務帳戶設計一組受限的原則，並且在需要相同的選項時，對串流定位器重新使用這些原則。 如需詳細資訊，請參閱 [配額和限制](limits-quotas-constraints.md)。

## <a name="decision-tree"></a>決策樹

按一下影像可以完整大小檢視。  

[![圖表顯示決策樹，其設計目的是要協助您為您的案例選擇預先定義的串流原則。](./media/streaming-policy/large.png)](./media/streaming-policy/large.png#lightbox)

如果您要加密內容，您需要建立 [內容金鑰原則](content-key-policy-concept.md)，則不需要 **內容金鑰原則** 來進行清除串流或下載。 

如果您有特殊需求 (例如，如果您想要指定不同的通訊協定、需要使用自訂金鑰傳遞服務，或需要使用明確的音訊播放軌) ，您可以 [建立](/rest/api/media/streamingpolicies/create) 自訂的串流原則。 

## <a name="get-a-streaming-policy-definition"></a>取得串流原則定義  

如果您想要查看串流原則的定義，請使用 [Get](/rest/api/media/streamingpolicies/get) 並指定原則名稱。 例如：

### <a name="rest"></a>REST

要求：

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

回應：

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>篩選、排序、分頁

請參閱[媒體服務實體的篩選、排序、分頁](entities-overview.md)。

## <a name="next-steps"></a>後續步驟

* [串流處理檔案](stream-files-dotnet-quickstart.md)
* [使用 AES-128 動態加密和金鑰傳遞服務](protect-with-aes128.md)
* [使用 DRM 動態加密與授權傳遞服務](protect-with-drm.md)
