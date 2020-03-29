---
title: Azure 媒體服務中的串流原則 | Microsoft Docs
description: 本文解釋串流原則是什麼，以及 Azure 媒體服務用它們來做什麼。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.openlocfilehash: a813c77e81e51bfe13e75ed6c8d0e24b4d0fa645
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "66392915"
---
# <a name="streaming-policies"></a>串流原則

Azure 媒體服務 v3 中，[串流原則](https://docs.microsoft.com/rest/api/media/streamingpolicies)可讓您設定[串流定位器](streaming-locators-concept.md)的串流通訊協定和加密選項。 媒體服務 v3 提供了一些預定義的流式處理策略，以便您可以直接使用它們進行試用或生產。 

當前可用的預定義流式處理策略：<br/>
* "Predefined_DownloadOnly"
* "Predefined_ClearStreamingOnly"
* 'Predefined_DownloadAndClearStreaming'
* "Predefined_ClearKey"
* 'Predefined_MultiDrmCencStreaming' 
* "Predefined_MultiDrmStreaming"

以下"決策樹"可説明您為方案選擇預定義的流式處理策略。

> [!IMPORTANT]
> * 屬於日期時間類型的**串流原則**屬性一律為 UTC 格式。
> * 您應該為媒體服務帳戶設計一組受限的原則，並且在需要相同的選項時，對串流定位器重新使用這些原則。 如需詳細資訊，請參閱 [配額和限制](limits-quotas-constraints.md)。

## <a name="decision-tree"></a>決策樹

按一下影像可以完整大小檢視。  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

如果加密內容，則需要創建[內容金鑰策略](content-key-policy-concept.md)，不需要清除流式處理或下載**內容金鑰策略**。 

如果您有特殊要求（例如，如果要指定不同的協定、需要使用自訂金鑰傳遞服務或使用清晰的音軌），則可以[創建](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)自訂流式處理策略。 

## <a name="get-a-streaming-policy-definition"></a>獲取流式處理策略定義  

如果要查看流式處理策略的定義，請使用[Get](https://docs.microsoft.com/rest/api/media/streamingpolicies/get)並指定策略名稱。 例如：

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
