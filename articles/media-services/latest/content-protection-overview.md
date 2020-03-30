---
title: 使用媒體服務 v3 動態加密保護您的內容
titleSuffix: Azure Media Services
description: 瞭解 Azure 媒體服務中使用動態加密、流式處理協定和加密類型進行的內容保護。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c1c9440f7ec70cea98f270f04c3030c800dd0fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461107"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>使用媒體服務動態加密保護您的內容

使用 Azure 媒體服務説明保護媒體從介質離開電腦一直通過存儲、處理和交付時起的安全。 使用媒體服務，您就能傳遞利用進階加密標準 (AES-128) 或下列三個主要數位版權管理 (DRM) 系統中任一個所動態加密的即時與隨選內容：Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。 如果內容使用 AES 清除金鑰進行加密，並且通過 HTTPS 發送，則在到達用戶端之前不會清除內容。 

在媒體服務 v3 中，內容金鑰與流式處理器相關聯（請參閱[此示例](protect-with-aes128.md)）。 如果使用媒體服務金鑰傳遞服務，則可以讓 Azure 媒體服務為您生成內容金鑰。 如果您使用的是金鑰傳遞服務，或者需要處理高可用性方案，需要在兩個資料中心中具有相同的內容金鑰，則應自行生成內容金鑰。

播放程式要求串流時，媒體服務便會使用 AES 清除金鑰或 DRM 加密，使用指定的金鑰動態加密您的內容。 為了將串流解密，播放程式會向媒體服務金鑰傳遞服務或您指定的金鑰傳遞服務要求金鑰。 要確定使用者是否有權獲取金鑰，該服務將評估您為金鑰指定的內容金鑰策略。

您可以使用 REST API 或媒體服務用戶端程式庫，來設定授權和金鑰的授權和驗證原則。

下圖說明瞭媒體服務內容保護的工作流：

![媒體服務內容保護工作流](./media/content-protection/content-protection.svg)
  
&#42;*動態加密支援 AES-128 清除金鑰、CBCS 和 CENC。有關詳細資訊，請參閱[支援矩陣](#streaming-protocols-and-encryption-types)。*

本文介紹了説明您瞭解媒體服務內容保護的概念和術語。

## <a name="main-components-of-a-content-protection-system"></a>內容保護系統的主要元件

要成功完成內容保護系統，您需要充分瞭解工作範圍。 以下各節概述了您需要實現的三個部分。

> [!NOTE]
> 我們強烈建議您在進入下一部分之前，對以下部分進行重點集中並完全測試每個部分。 要測試內容保護系統，請使用各節中指定的工具。

### <a name="media-services-code"></a>媒體服務代碼
  
[DRM 示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)演示如何使用 .NET 使用媒體服務 v3 實現多 DRM 系統。 它還演示如何使用媒體服務許可證/金鑰交付服務。
  
您可以使用多種加密類型 (AES-128、PlayReady、Widevine、FairPlay) 來加密每項資產。 要查看組合什麼有意義，請參閱[流協定和加密類型](#streaming-protocols-and-encryption-types)。

下列範例示範如何：

1. 創建和配置[內容金鑰策略](content-key-policy-concept.md)。

   創建內容金鑰策略以配置內容金鑰（提供對資產的安全訪問）如何傳遞到最終用戶端：  

   * 定義許可證交付授權。 基於 JSON Web 權杖 （JWT） 中的聲明指定授權檢查的邏輯。
   * 配置[播放就緒](playready-license-template-overview.md)、[寬文](widevine-license-template-overview.md)和/或[公平播放](fairplay-license-overview.md)許可證。 範本允許您為每個 DRM 配置許可權和許可權。

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. 創建配置為資料流加密資產的[流式處理器。](streaming-locators-concept.md)
  
   流式處理器必須與[流式處理策略](streaming-policy-concept.md)相關聯。 在此示例中，我們將設置為`StreamingLocator.StreamingPolicyName`"Predefined_MultiDrmCencStreaming"策略。

   應用 PlayReady 和 Widevine 加密，並根據配置的 DRM 許可證將金鑰傳遞到播放用戶端。 如果還希望使用 CBCS （FairPlay） 加密流，請使用"Predefined_MultiDrmStreaming"策略。

   流式處理器還與您定義的內容金鑰策略相關聯。

3. 建立測試權杖。

   該方法`GetTokenAsync`演示如何創建測試權杖。
4. 建置串流 URL。

   該方法`GetDASHStreamingUrlAsync`演示如何生成流 URL。 在此案例中，URL 會串流處理 DASH 內容。

### <a name="player-with-an-aes-or-drm-client"></a>具有 AES 或 DRM 用戶端的播放機

以播放器 SDK (原生或以瀏覽器為基礎) 為基礎的影片播放器應用程式必須符合下列需求：

* 播放機 SDK 支援所需的 DRM 用戶端。
* 播放機 SDK 支援所需的流式處理協定：平滑、DASH 和/或 HTTP 即時流式處理 （HLS）。
* 播放機 SDK 可以處理在授權擷取請求中傳遞 JWT 權杖。

您可以使用 [Azure 媒體播放器 API](https://amp.azure.net/libs/amp/latest/docs/) 來建立播放器。 使用 [Azure 媒體播放器的 ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) 來指定要在不同的 DRM 平台上使用哪個 DRM 技術。

若要測試 AES 或 CENC (Widevine 和/或 PlayReady) 加密的內容，您可以使用 [Azure 媒體播放器](https://aka.ms/azuremediaplayer) \(英文\)。 請確保選擇 **"高級"選項**並檢查加密選項。

如果您想要測試 FairPlay 加密內容，請使用[此測試播放程式](https://aka.ms/amtest)。 播放機支援 Widevine、PlayReady 和 FairPlay DRM，以及 AES-128 清除金鑰加密。

選擇正確的瀏覽器來測試不同的 DRM：

* 鉻，歌劇，或火狐為維德文。
* 微軟邊緣或互聯網瀏覽器11為播放準備。
* 在 macOS 上為公平遊戲進行野生動物園。

### <a name="security-token-service"></a>安全權杖服務

安全權杖服務 （STS） 發出 JWT 作為後端資源訪問的訪問權杖。 可以使用 Azure 媒體服務許可證/金鑰傳遞服務作為後端資源。 STS 必須定義以下事項：

* 發行人和受眾（或範圍）。
* 聲明，這些聲明取決於內容保護中的業務需求。
* 用於簽名驗證的對稱或非對稱驗證。
* 金鑰滾動支援（如有必要）。

您可以使用此[STS 工具](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt)測試 STS。 它支援所有三種類型的驗證金鑰：對稱、非對稱或 Azure 活動目錄 （Azure AD）與金鑰滾動。

## <a name="streaming-protocols-and-encryption-types"></a>串流通訊協定和加密類型

您可以使用媒體服務，來傳遞藉由使用 PlayReady、Widevine 或 FairPlay 並透過 AES 未加密金鑰或 DRM 加密所動態加密的內容。 目前，您可以加密 HLS、MPEG DASH 和平滑流式處理格式。 每個協定都支援以下加密方法。

### <a name="hls"></a>HLS

HLS 協定支援以下容器格式和加密方案：

|容器格式|加密配置|URL 示例|
|---|---|---|
|全部|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS（公平播放） |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS（公平播放） |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

以下設備上支援 HLS/CMAF – 公平播放（包括 HEVC/H.265）：

* iOS 11 或更高版本。
* iPhone 8 或更高版本。
* MacOS 高塞拉與英特爾第七代 CPU。

### <a name="mpeg-dash"></a>MPEG-DASH

MPEG-DASH 協定支援以下容器格式和加密方案：

|容器格式|加密配置|URL 示例
|---|---|---|
|全部|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

平滑流式處理協定支援以下容器格式和加密方案。

|通訊協定|容器格式|加密配置|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>瀏覽器

通用瀏覽器支援以下 DRM 用戶端：

|瀏覽器|加密|
|---|---|
|Chrome|Widevine|
|微軟邊緣， 互聯網瀏覽器 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>控制內容訪問

您可以設定內容金鑰原則來控制可存取內容的人員。 媒體服務支援多種方式來授權提出金鑰要求的使用者。 用戶端 (播放器) 必須先符合原則，系統才會將金鑰傳遞給用戶端。 內容金鑰原則可具有*開啟*或*權杖*限制。

當您希望未經授權向任何人頒發許可證時，可能會使用開放限制的內容金鑰策略。 例如，如果您的收入是基於廣告的，而不是基於訂閱的。  

使用權杖限制的內容金鑰策略，內容金鑰僅發送到在許可證/金鑰請求中顯示有效的 JWT 權杖或簡單 Web 權杖 （SWT） 的用戶端。 此權杖必須由 STS 頒發。

您可以將 Azure AD 用作 STS 或部署[自訂 STS](#using-a-custom-sts)。 STS 必須設定為建立使用指定的索引鍵和問題宣告您在權杖限制組態中指定簽署的權杖。 如果存在以下兩個條件，媒體服務許可證/金鑰交付服務會將請求的許可證或金鑰返回給用戶端：

* 權杖有效。
* 權杖中的聲明與為許可證或金鑰配置的聲明匹配。

設定權杖限制的原則時，您必須指定主要驗證金鑰、簽發者和對象參數。 主要驗證金鑰包含簽署權杖用的金鑰。 簽發者為發行權杖的 STS。 訪問群體（有時稱為作用域）描述權杖或權杖授權訪問的資源的意圖。 媒體服務許可證/金鑰交付服務驗證權杖中的這些值與範本中的值匹配。

### <a name="token-replay-prevention"></a>權杖重播預防

*權杖重播防止*功能允許媒體服務客戶對同一權杖可用於請求金鑰或許可證的次數設置限制。 客戶可以在權杖中添加類型`urn:microsoft:azure:mediaservices:maxuses`聲明，其中值是權杖可用於獲取許可證或金鑰的次數。 所有對金鑰傳遞具有相同權杖的後續請求都將返回未經授權的回應。 瞭解如何在[DRM 示例中](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601)添加聲明。
 
#### <a name="considerations"></a>考量

* 客戶必須控制權杖生成。 聲明需要放在權杖本身中。
* 使用此功能時，過期時間超過一小時，並且使用未經授權的回應拒絕具有過期時間超過一小時的權杖的請求。
* 權杖由其簽名唯一標識。 對負載的任何更改（例如，更新到到期時間或聲明）都會更改權杖的簽名，它將算作金鑰交付以前未遇到的新權杖。
* 如果權杖已超過客戶設置的值，`maxuses`則重播將失敗。
* 此功能可用於所有現有的受保護內容（只需更改頒發的權杖）。
* 此功能適用于 JWT 和 SWT。

## <a name="using-a-custom-sts"></a>使用自訂 STS

客戶可能選擇使用自訂 STS 來提供權杖。 原因包括：

* 客戶使用的標識提供程式 （IDP） 不支援 STS。 在此情況下，自訂 STS 可能是一個選項。
* 客戶在整合 STS 與客戶的訂閱者計費系統時，可能需要更多彈性或更緊密的控制。

   例如[，OTT](https://en.wikipedia.org/wiki/Over-the-top_media_services)服務運營商可能提供多個訂閱者包，如高級、基本和體育。 業者可能需要讓權杖中的宣告與訂閱者套件相符，如此一來，只有正確套件中的內容可供使用。 在此情況下，自訂 STS 提供所需的彈性和控制。

* 在權杖中包含自訂聲明，以選擇具有不同 DRM 許可證參數的不同 ContentKeyPolicy 選項（訂閱許可證與租賃許可證）。
* 包括表示權杖授予存取權限的金鑰的內容金鑰識別碼的聲明。

當您使用自訂的 STS 時，必須進行兩項變更：

* 為資產設定授權傳遞服務時，您必須指定自訂 STS 用來驗證的安全性金鑰，而不是 Azure AD 的目前金鑰。
* 產生 JTW 權杖時，會指定安全性金鑰，而不是 Azure AD 中目前 x509 憑證的私密金鑰。

有兩種類型的安全性金鑰：

* 對稱金鑰：會使用相同的金鑰來產生及驗證 JWT。
* 非對稱金鑰：搭配使用 x509 憑證中的私密-公開金鑰組，私密金鑰用來加密/產生 JWT，公開金鑰則用來驗證權杖。

如果您使用 .NET Framework/C# 作為開發平台，用於非對稱安全性金鑰的 x509 憑證之金鑰長度必須至少為 2048。 此金鑰長度是類系統的要求。標識模型.tokens.X509A對稱安全金鑰在 .NET 框架中。 否則，將引發以下異常：IDX10630：用於簽名的"System.身份模型.Tokens.X509A對稱安全金鑰"不能小於"2048"位。

## <a name="custom-key-and-license-acquisition-url"></a>自訂金鑰和授權擷取 URL

如果要指定其他許可證/金鑰交付服務（而不是媒體服務），請使用以下範本。 範本中的兩個可替換欄位都在那裡，以便您可以跨多個資產共用流式處理策略，而不是按資產創建流式處理策略。 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`：向最終使用者播放機提供金鑰的自訂服務的 URL 的範本。 當您使用 Azure 媒體服務頒發金鑰時，它不需要。 

   該範本支援服務將在運行時使用特定于請求的值更新的可替換權杖。  當前支援的權杖值包括：
   * `{AlternativeMediaId}`，替換為流式處理Id.替代媒體Id 的值。
   * `{ContentKeyId}`，替換為請求的金鑰的識別碼的值。
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`：向最終使用者播放機提供許可證的自訂服務的 URL 的範本。 當您使用 Azure 媒體服務頒發許可證時，它不需要。

   該範本支援服務將在運行時使用特定于請求的值更新的可替換權杖。 當前支援的權杖值包括：  
   * `{AlternativeMediaId}`，替換為流式處理Id.替代媒體Id 的值。
   * `{ContentKeyId}`，替換為請求的金鑰的識別碼的值。 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`：與以前的範本相同，僅適用于 Widevine。 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`：與以前的範本相同，僅適用于公平播放。  

例如：

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId`具有請求的金鑰的值。 `AlternativeMediaId`如果要將請求映射到您身邊的實體，則可以使用。 例如，`AlternativeMediaId`可用於説明您查找許可權。

有關使用自訂許可證/金鑰獲取 URL 的 REST 示例，請參閱[流式處理策略 - 創建](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)。

> [!NOTE]
> Widevine 是 Google Inc. 所提供的服務，並受到 Google Inc. 的服務條款和隱私權原則所約束。

## <a name="troubleshoot"></a>疑難排解

如果收到錯誤，`MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY`請確保指定適當的流式處理策略。

如果收到以`_NOT_SPECIFIED_IN_URL`結尾的錯誤，請確保在 URL 中指定加密格式。 例如 `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`。 請參閱[流式處理協定和加密類型](#streaming-protocols-and-encryption-types)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

* [使用 AES 加密保護](protect-with-aes128.md)
* [使用 DRM 保護](protect-with-drm.md)
* [設計帶存取控制的多 DRM 內容保護系統](design-multi-drm-system-with-access-control.md)
* [儲存端加密](storage-account-concept.md#storage-side-encryption)
* [常見問題集](frequently-asked-questions.md)
* [JSON Web 語彙基元處理常式](https://docs.microsoft.com/dotnet/framework/security/json-web-token-handler)
