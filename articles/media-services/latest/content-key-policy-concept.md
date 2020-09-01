---
title: 媒體服務中的內容金鑰原則 - Azure | Microsoft Docs
description: 本文解釋內容金鑰原則是什麼，以及 Azure 媒體服務用它們來做什麼。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: b6d907e987cb57836997cab43e9bfa9e919bf67d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268282"
---
# <a name="content-key-policies"></a>內容金鑰原則

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

使用媒體服務，您就能傳遞利用進階加密標準 (AES-128) 或下列三個主要數位版權管理 (DRM) 系統中任一個所動態加密的即時與隨選內容：Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。 

若要在您的串流上指定加密選項，您必須建立 [串流原則](streaming-policy-concept.md) ，並將它與您的 [串流定位器](streaming-locators-concept.md)建立關聯。 您可以建立 [內容金鑰原則](/rest/api/media/contentkeypolicies) 來設定內容金鑰 (如何提供 [資產](assets-concept.md) 的安全存取，) 會傳遞給終端用戶端。 您必須在必須符合的內容金鑰原則上設定 (限制) 的需求，才能將具有指定設定的金鑰傳遞給用戶端。 清除串流或下載時不需要內容金鑰原則。 

通常，您會將內容金鑰原則與您的 [串流定位器](streaming-locators-concept.md)建立關聯。 或者，您可以在建立適用于 advanced 案例) 的自訂串流原則時，指定 [串流原則](streaming-policy-concept.md) 內的內容金鑰原則 (。 

## <a name="best-practices-and-considerations"></a>最佳做法和考慮

> [!IMPORTANT]
> 請參閱下列建議。

* 您應該為媒體服務帳戶設計一組有限的原則，並且在需要相同的選項時，對串流定位器重複使用這些原則。 如需詳細資訊，請參閱 [配額和限制](limits-quotas-constraints.md)。
* 內容金鑰原則是可更新的。 金鑰傳遞快取最多可能需要15分鐘的時間，才能更新和挑選更新的原則。 

   藉由更新原則，您將覆寫現有的 CDN 快取，這可能會導致使用快取內容的客戶發生播放問題。  
* 建議您不要為每個資產建立新的內容金鑰原則。 在需要相同原則選項的資產之間共用相同內容金鑰原則的主要優點如下：
   
   * 更輕鬆地管理少量的原則。
   * 如果您需要更新內容金鑰原則，這些變更幾乎會立即對所有新的授權要求生效。
* 如果您需要建立新的原則，您必須為資產建立新的串流定位器。
* 建議讓媒體服務自動產生內容金鑰。 

   一般來說，您會使用長時間的金鑰，並使用 [Get](/rest/api/media/contentkeypolicies/get)檢查內容金鑰原則是否存在。 若要取得金鑰，您必須呼叫個別的動作方法，以取得秘密或認證，請參閱下列範例。

## <a name="example"></a>範例

若要取得金鑰，請使用 `GetPolicyPropertiesWithSecretsAsync` ，如「 [從現有原則取得簽署金鑰](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) 」範例所示。

## <a name="filtering-ordering-paging"></a>篩選、排序、分頁

請參閱[媒體服務實體的篩選、排序、分頁](entities-overview.md)。

## <a name="additional-notes"></a>其他注意事項

* 類型的內容金鑰原則屬性 `Datetime` 一律採用 UTC 格式。
* Widevine 是 Google Inc. 所提供的服務，並受到 Google Inc. 的服務條款和隱私權原則所約束。

## <a name="next-steps"></a>後續步驟

* [使用 AES-128 動態加密和金鑰傳遞服務](protect-with-aes128.md)
* [使用 DRM 動態加密與授權傳遞服務](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
