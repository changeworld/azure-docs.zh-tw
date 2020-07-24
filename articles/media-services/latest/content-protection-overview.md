---
title: 使用媒體服務 v3 動態加密來保護您的內容
titleSuffix: Azure Media Services
description: 瞭解在 Azure 媒體服務中使用動態加密、串流通訊協定和加密類型的內容保護。
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
ms.openlocfilehash: 0be481d90562ca611b021e2f05d9109eb51958c8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023257"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>使用媒體服務動態加密來保護您的內容

使用 Azure 媒體服務來協助保護媒體從離開電腦到進行儲存、處理和傳遞時的安全。 使用媒體服務，您就能傳遞利用進階加密標準 (AES-128) 或下列三個主要數位版權管理 (DRM) 系統中任一個所動態加密的即時與隨選內容：Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。 如果內容是使用 AES clear 金鑰進行加密，並透過 HTTPS 傳送，則在到達用戶端之前，都不會很清楚。 

在媒體服務 v3 中，內容金鑰會與串流定位器相關聯（請參閱[此範例](protect-with-aes128.md)）。 如果使用媒體服務金鑰傳遞服務，您可以讓 Azure 媒體服務為您產生內容金鑰。 如果您要使用自己的金鑰傳遞服務，或如果您需要處理高可用性案例，且在兩個資料中心內必須擁有相同的內容金鑰，則應該自行產生內容金鑰。

播放程式要求串流時，媒體服務便會使用 AES 清除金鑰或 DRM 加密，使用指定的金鑰動態加密您的內容。 為了將串流解密，播放程式會向媒體服務金鑰傳遞服務或您指定的金鑰傳遞服務要求金鑰。 為了決定使用者是否有權取得金鑰，服務會評估您為金鑰指定的內容金鑰原則。

您可以使用 REST API 或媒體服務用戶端程式庫，來設定授權和金鑰的授權和驗證原則。

下圖說明媒體服務內容保護的工作流程：

![媒體服務內容保護的工作流程](./media/content-protection/content-protection.svg)
  
&#42;*動態加密支援 AES-128 清除金鑰、CBCS 和 CENC。如需詳細資訊，請參閱[支援矩陣](#streaming-protocols-and-encryption-types)。*

本文說明可協助您瞭解媒體服務內容保護的概念與術語。

## <a name="main-components-of-a-content-protection-system"></a>內容保護系統的主要元件

若要成功完成您的內容保護系統，您必須完全瞭解該工作的範圍。 下列各節概述您需要執行的三個部分。

> [!NOTE]
> 我們強烈建議您在繼續進行下一個部分之前，先將焦點放在下列各節中的每個部分，並加以完整測試。 若要測試您的「內容保護」系統，請使用各節中指定的工具。

### <a name="media-services-code"></a>媒體服務程式碼
  
[DRM 範例會示範](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)如何使用 .net，透過媒體服務 v3 來執行多重 DRM 系統。 它也會說明如何使用媒體服務授權/金鑰傳遞服務。
  
您可以使用多種加密類型 (AES-128、PlayReady、Widevine、FairPlay) 來加密每項資產。 若要查看結合的意義，請參閱[串流通訊協定和加密類型](#streaming-protocols-and-encryption-types)。

下列範例示範如何：

1. 建立和設定[內容金鑰原則](content-key-policy-concept.md)。

   您會建立內容金鑰原則，以設定內容金鑰（提供資產的安全存取）如何傳遞給終端用戶端：  

   * 定義授權傳遞授權。 根據 JSON Web Token （JWT）中的宣告指定授權檢查的邏輯。
   * 設定[PlayReady](playready-license-template-overview.md)、 [Widevine](widevine-license-template-overview.md)和/或[FairPlay](fairplay-license-overview.md)授權。 範本可讓您設定每個 Drm 的權利和許可權。

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. 建立[串流定位器](streaming-locators-concept.md)，其設定為串流加密的資產。
  
   串流定位器必須與[串流原則](streaming-policy-concept.md)相關聯。 在範例中，我們將設定 `StreamingLocator.StreamingPolicyName` 為「Predefined_MultiDrmCencStreaming」原則。

   會套用 PlayReady 和 Widevine 加密，並根據所設定的 DRM 授權將金鑰傳遞至播放用戶端。 如果您也想要使用 CBCS （FairPlay）來加密您的串流，請使用「Predefined_MultiDrmStreaming」原則。

   串流定位器也會與您定義的內容金鑰原則相關聯。

3. 建立測試權杖。

   `GetTokenAsync`方法會顯示如何建立測試 token。
4. 建置串流 URL。

   `GetDASHStreamingUrlAsync`方法會顯示如何建立串流 URL。 在此案例中，URL 會串流處理 DASH 內容。

### <a name="player-with-an-aes-or-drm-client"></a>使用 AES 或 DRM 用戶端的播放程式

以播放器 SDK (原生或以瀏覽器為基礎) 為基礎的影片播放器應用程式必須符合下列需求：

* 播放機 SDK 支援所需的 DRM 用戶端。
* 播放機 SDK 支援所需的串流通訊協定：平滑、虛線和（或） HTTP 即時串流（HLS）。
* Player SDK 可以在授權取得要求中處理傳遞 JWT 權杖。

您可以使用 [Azure 媒體播放器 API](https://amp.azure.net/libs/amp/latest/docs/) 來建立播放器。 使用 [Azure 媒體播放器的 ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) 來指定要在不同的 DRM 平台上使用哪個 DRM 技術。

若要測試 AES 或 CENC (Widevine 和/或 PlayReady) 加密的內容，您可以使用 [Azure 媒體播放器](https://aka.ms/azuremediaplayer) \(英文\)。 請確定您選取 [ **Advanced options** ]，並檢查您的加密選項。

如果您想要測試 FairPlay 加密內容，請使用[此測試播放程式](https://aka.ms/amtest)。 Player 支援 Widevine、PlayReady 和 FairPlay Drm，以及 AES-128 清除金鑰加密。

選擇正確的瀏覽器以測試不同的 Drm：

* 適用于 Widevine 的 Chrome、Opera 或 Firefox。
* 適用于 PlayReady 的 Microsoft Edge 或 Internet Explorer 11。
* 適用于 FairPlay 的 macOS 上的 Safari。

### <a name="security-token-service"></a>安全性權杖服務

Security Token Service （STS）會發出 JWT 做為後端資源存取的存取權杖。 您可以使用 Azure 媒體服務授權/金鑰傳遞服務做為後端資源。 STS 必須定義下列專案：

* 簽發者和物件（或範圍）。
* 宣告，相依于內容保護中的商務需求。
* 簽章驗證的對稱或非對稱式驗證。
* 金鑰變換支援（如有必要）。

您可以使用[此 sts 工具](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt)來測試 STS。 它支援所有三種類型的驗證金鑰：對稱、非對稱或 Azure Active Directory （Azure AD）與金鑰變換。

## <a name="streaming-protocols-and-encryption-types"></a>串流通訊協定和加密類型

您可以使用媒體服務，來傳遞藉由使用 PlayReady、Widevine 或 FairPlay 並透過 AES 未加密金鑰或 DRM 加密所動態加密的內容。 目前，您可以加密 HLS、MPEG 破折號和 Smooth Streaming 格式。 每個通訊協定都支援下列加密方法。

### <a name="hls"></a>HLS

HLS 通訊協定支援下列容器格式和加密配置：

|容器格式|加密配置|URL 範例|
|---|---|---|
|全部|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS （FairPlay） |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS （FairPlay） |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

下列裝置支援 HLS/CMAF + FairPlay （包括 HEVC/H. 265）：

* iOS 11 或更新版本。
* iPhone 8 或更新版本。
* 使用 Intel 7 代 CPU MacOS 高塞拉里昂。

### <a name="mpeg-dash"></a>MPEG-DASH

MPEG-破折號通訊協定支援下列容器格式和加密配置：

|容器格式|加密配置|URL 範例
|---|---|---|
|全部|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

Smooth Streaming 通訊協定支援下列容器格式和加密配置。

|通訊協定|容器格式|加密配置|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>瀏覽器

常見的瀏覽器支援下列 DRM 用戶端：

|瀏覽器|加密|
|---|---|
|Chrome|Widevine|
|Microsoft Edge、Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>控制內容存取

您可以設定內容金鑰原則來控制可存取內容的人員。 媒體服務支援多種方式來授權提出金鑰要求的使用者。 用戶端 (播放器) 必須先符合原則，系統才會將金鑰傳遞給用戶端。 內容金鑰原則可具有*開啟*或*權杖*限制。

當您想要在沒有授權的情況下頒發授權給任何人時，可能會使用開放限制的內容金鑰原則。 例如，如果您的收益是以 ad 為基礎，而不是以訂用帳戶為基礎。  

使用權杖限制的內容金鑰原則時，只會將內容金鑰傳送給用戶端，以在授權/金鑰要求中出示有效的 JWT 權杖或簡單 web 權杖（SWT）。 此權杖必須由 STS 發行。

您可以使用 Azure AD 做為 STS 或部署[自訂 sts](#using-a-custom-sts)。 STS 必須設定為建立使用指定的索引鍵和問題宣告您在權杖限制組態中指定簽署的權杖。 如果這兩個條件都存在，媒體服務授權/金鑰傳遞服務會將要求的授權或金鑰傳回給用戶端：

* 權杖有效。
* 權杖中的宣告符合為授權或金鑰所設定的宣告。

設定權杖限制的原則時，您必須指定主要驗證金鑰、簽發者和對象參數。 主要驗證金鑰包含簽署權杖用的金鑰。 簽發者為發行權杖的 STS。 物件（有時稱為範圍）描述權杖或權杖授權存取之資源的意圖。 媒體服務授權/金鑰傳遞服務會驗證權杖中的這些值是否符合範本中的值。

### <a name="token-replay-prevention"></a>權杖重新執行防止

「權杖重送防護」功能可讓媒體服務客戶限制以相同權杖來要求金鑰或授權的次數。 客戶可以在權杖中新增類型的宣告 `urn:microsoft:azure:mediaservices:maxuses` ，其中的值是權杖可用於取得授權或金鑰的次數。 對金鑰傳遞具有相同權杖的所有後續要求將會傳回未經授權的回應。 請參閱如何在[DRM 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601)中新增宣告。
 
#### <a name="considerations"></a>考量

* 客戶必須擁有權杖產生的控制權。 宣告必須放在權杖本身。
* 使用這項功能時，會拒絕權杖的到期時間超過一小時的要求，而不會收到未經授權的回應。
* 權杖是由其簽章唯一識別。 對裝載所做的任何變更（例如，更新到期時間或宣告）都會變更權杖的簽章，而且會計算為新的權杖，表示金鑰傳遞不會在之前。
* 如果權杖已超過客戶所設定的值，播放就會失敗 `maxuses` 。
* 這項功能可用於所有現有的受保護內容（只需要變更發行的權杖）。
* 這項功能適用于 JWT 和 SWT。

## <a name="using-a-custom-sts"></a>使用自訂 STS

客戶可能會選擇使用自訂 STS 來提供權杖。 原因包括：

* 客戶使用的識別提供者（IDP）不支援 STS。 在此情況下，自訂 STS 可能是一個選項。
* 客戶在整合 STS 與客戶的訂閱者計費系統時，可能需要更多彈性或更緊密的控制。

   例如， [OTT](https://en.wikipedia.org/wiki/Over-the-top_media_services)服務操作員可能會提供多個訂閱者封裝，例如 premium、basic 和體育。 業者可能需要讓權杖中的宣告與訂閱者套件相符，如此一來，只有正確套件中的內容可供使用。 在此情況下，自訂 STS 提供所需的彈性和控制。

* 在權杖中包含自訂宣告，以使用不同的 DRM 授權參數（訂用帳戶授權與出租授權）在不同的 ContentKeyPolicyOptions 之間進行選取。
* 包含宣告，其代表權杖授與存取權之金鑰的內容金鑰識別碼。

當您使用自訂的 STS 時，必須進行兩項變更：

* 為資產設定授權傳遞服務時，您必須指定自訂 STS 用來驗證的安全性金鑰，而不是 Azure AD 的目前金鑰。
* 產生 JTW 權杖時，會指定安全性金鑰，而不是 Azure AD 中目前 x509 憑證的私密金鑰。

有兩種類型的安全性金鑰：

* 對稱金鑰：會使用相同的金鑰來產生及驗證 JWT。
* 非對稱金鑰：搭配使用 x509 憑證中的私密-公開金鑰組，私密金鑰用來加密/產生 JWT，公開金鑰則用來驗證權杖。

如果您使用 .NET Framework/C# 作為開發平台，用於非對稱安全性金鑰的 x509 憑證之金鑰長度必須至少為 2048。 在 .NET Framework 中，此金鑰長度是 System.identitymodel.tokens.x509asymmetricsecuritykey 類別的需求。 否則，會擲回下列例外狀況： IDX10630：用於簽署的 ' Microsoft.identitymodel. System.identitymodel.tokens.x509asymmetricsecuritykey ' 不能小於 ' 2048 ' 位。

## <a name="custom-key-and-license-acquisition-url"></a>自訂金鑰和授權取得 URL

如果您想要指定不同的授權/金鑰傳遞服務（而不是媒體服務），請使用下列範本。 範本中的兩個可取代欄位是，因此您可以在許多資產之間共用串流原則，而不是針對每個資產建立串流原則。 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`：將金鑰傳遞給使用者播放程式之自訂服務的 URL 範本。 當您使用 Azure 媒體服務來發行金鑰時，這不是必要的。 

   此範本支援可取代的權杖，服務會在執行時間以要求的特定值進行更新。  目前支援的標記值為：
   * `{AlternativeMediaId}`，其以 StreamingLocatorId. AlternativeMediaId 的值取代。
   * `{ContentKeyId}`，取代為所要求金鑰的識別碼值。
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`：提供授權給使用者播放程式之自訂服務 URL 的範本。 當您使用 Azure 媒體服務來發行授權時，這不是必要的。

   此範本支援可取代的權杖，服務會在執行時間以要求的特定值進行更新。 目前支援的標記值為：  
   * `{AlternativeMediaId}`，其以 StreamingLocatorId. AlternativeMediaId 的值取代。
   * `{ContentKeyId}`，取代為所要求金鑰的識別碼值。 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`：與上一個範本相同，僅適用于 Widevine。 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`：與上一個範本相同，僅適用于 FairPlay。  

例如：

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId`具有所要求金鑰的值。 `AlternativeMediaId`如果您想要將要求對應至您端的實體，可以使用。 例如， `AlternativeMediaId` 可以用來協助您查閱許可權。

如需使用自訂授權/金鑰取得 Url 的 REST 範例，請參閱[串流原則-建立](/rest/api/media/streamingpolicies/create)。

> [!NOTE]
> Widevine 是 Google Inc. 所提供的服務，並受到 Google Inc. 的服務條款和隱私權原則所約束。

## <a name="troubleshoot"></a>疑難排解

如果您收到 `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` 錯誤，請確定您指定的是適當的串流原則。

如果您收到以結尾的錯誤 `_NOT_SPECIFIED_IN_URL` ，請確定您在 URL 中指定加密格式。 例如 `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`。 請參閱[串流通訊協定和加密類型](#streaming-protocols-and-encryption-types)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

* [使用 AES 加密保護](protect-with-aes128.md)
* [使用 DRM 保護](protect-with-drm.md)
* [設計具有存取控制的多重 DRM 內容保護系統](design-multi-drm-system-with-access-control.md)
* [儲存端加密](storage-account-concept.md#storage-side-encryption)
* [常見問題集](frequently-asked-questions.md)
* [JSON Web 語彙基元處理常式](/dotnet/framework/security/json-web-token-handler)
