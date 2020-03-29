---
title: 媒體服務中的內容金鑰原則 - Azure | Microsoft Docs
description: 本文解釋內容金鑰原則是什麼，以及 Azure 媒體服務用它們來做什麼。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7ddef1e78b4f8f62145e10b4cabc4537e28aba2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74969899"
---
# <a name="content-key-policies"></a>內容金鑰原則

使用媒體服務，您就能傳遞利用進階加密標準 (AES-128) 或下列三個主要數位版權管理 (DRM) 系統中任一個所動態加密的即時與隨選內容：Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。 

要在流中指定加密選項，您需要創建[流式處理策略](streaming-policy-concept.md)並將其與[流式處理器](streaming-locators-concept.md)相關聯。 創建[內容金鑰策略](https://docs.microsoft.com/rest/api/media/contentkeypolicies)以配置內容金鑰（提供對[資產](assets-concept.md)的安全訪問）如何傳遞到最終用戶端。 您需要設置內容金鑰策略上必須滿足的要求（限制），以便將具有指定配置的金鑰傳遞到用戶端。 清除流式處理或下載不需要內容金鑰策略。 

通常，您將內容金鑰策略與[流式處理器](streaming-locators-concept.md)相關聯。 或者，您可以在[流式處理策略](streaming-policy-concept.md)中指定內容金鑰策略（為高級方案創建自訂流式處理策略時）。 

## <a name="best-practices-and-considerations"></a>最佳實踐和注意事項

> [!IMPORTANT]
> 請查看以下建議。

* 您應該為媒體服務帳戶設計一組有限的策略，並在需要相同選項時為流式處理器重用它們。 如需詳細資訊，請參閱 [配額和限制](limits-quotas-constraints.md)。
* 內容關鍵策略是可備份的。 金鑰傳遞緩存最多可能需要 15 分鐘才能更新和選取更新的策略。 

   通過更新策略，您將覆蓋現有的 CDN 緩存，這可能導致使用緩存內容的客戶產生播放問題。  
* 我們建議您不要為每個資產創建新的內容金鑰策略。 在需要相同策略選項的資產之間共用相同內容金鑰策略的主要好處是：
   
   * 管理少量策略更容易。
   * 如果需要更新內容金鑰策略，這些更改幾乎立即對所有新的許可證請求生效。
* 如果需要創建新策略，必須為資產創建新的流式處理器。
* 建議讓媒體服務自動生成內容金鑰。 

   通常，您將使用長壽命的金鑰，並使用[Get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get)檢查內容金鑰策略是否存在。 若要取得金鑰，您必須呼叫個別的動作方法，以取得秘密或認證，請參閱下列範例。

## <a name="example"></a>範例

要獲取該鍵，請使用`GetPolicyPropertiesWithSecretsAsync`，如[從現有策略示例中獲取簽名金鑰](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets)所示。

## <a name="filtering-ordering-paging"></a>篩選、排序、分頁

請參閱[媒體服務實體的篩選、排序、分頁](entities-overview.md)。

## <a name="additional-notes"></a>其他注意事項

* `Datetime`類型的內容金鑰策略的屬性始終採用 UTC 格式。
* Widevine 是 Google Inc. 所提供的服務，並受到 Google Inc. 的服務條款和隱私權原則所約束。

## <a name="next-steps"></a>後續步驟

* [使用 AES-128 動態加密和金鑰傳遞服務](protect-with-aes128.md)
* [使用 DRM 動態加密與授權傳遞服務](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
