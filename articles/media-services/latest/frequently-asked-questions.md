---
title: Azure 媒體服務 v3 常見問題集 | Microsoft Docs
description: 本文提供 Azure 媒體服務 v3 常見問題的解答。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/07/2020
ms.author: juliako
ms.openlocfilehash: 713acbd098255af2869d7a462c9990f3d7e10bf1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81309193"
---
# <a name="media-services-v3-frequently-asked-questions"></a>媒體服務 v3 常見問題

本文提供 Azure 媒體服務 v3 常見問題的解答。

## <a name="general"></a>一般

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>哪些 Azure 角色可以對 Azure 媒體服務資源執行動作？ 

請參閱[角色型存取控制（RBAC）以取得媒體服務帳戶](rbac-overview.md)。

### <a name="how-do-i-stream-to-apple-ios-devices"></a>如何? 串流至 Apple iOS 裝置嗎？

請確定您在路徑結尾處（**格式 = m3u8-m3u8-aapl-v3）** （在 URL 的 **/manifest**部分之後）告訴串流源伺服器傳回 HTTP 即時串流（HLS）內容以供在 Apple iOS 原生裝置上取用。 如需詳細資訊，請參閱[傳遞內容](dynamic-packaging-overview.md)。

### <a name="how-do-i-configure-media-reserved-units"></a>如何設定編碼保留單元？

針對媒體服務 v3 或影片索引子所觸發的音訊分析和影片分析工作，我們建議您以10個 S3 媒體保留單元（Mru）布建您的帳戶。 如果您需要超過10個 S3 Mru，請使用[Azure 入口網站](https://portal.azure.com/)開啟支援票證。

如需詳細資訊，請參閱[調整媒體處理](media-reserved-units-cli-how-to.md)。

### <a name="what-is-the-recommended-method-to-process-videos"></a>建議用來處理影片的方法為何？

使用[轉換](https://docs.microsoft.com/rest/api/media/transforms)可設定視訊編碼或分析的一般工作。 每個轉換都會描述配方或工作流程，以便處理您的視訊或音訊檔案。 「[作業](https://docs.microsoft.com/rest/api/media/jobs)」（Job）是媒體服務將轉換套用到輸入影片或音訊內容的實際要求。 建立轉換之後，您可以使用媒體服務 Api 或任何已發佈的 Sdk 提交作業。 如需詳細資訊，請參閱[轉換和作業](transforms-jobs-concept.md)。

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>我已上傳、編碼以及發佈視訊。 當我嘗試串流時，影片為什麼不會播放？

其中一個最常見的原因是您沒有嘗試在執行狀態中播放的串流端點。

### <a name="how-does-pagination-work"></a>分頁的運作方式為何？

當您使用分頁時，您應該一律使用下一個連結來列舉集合，而不是相依于特定頁面大小。 如需詳細資訊和範例，請參閱[篩選、排序、分頁](entities-overview.md)。

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Azure 媒體服務 v3 中尚無法使用哪些功能？

如需詳細資訊，請參閱[V2 api 相關的功能差距](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis)。

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>在訂閱之間移動媒體服務帳戶的程式為何？  

如需詳細資訊，請參閱[在訂閱之間移動媒體服務帳戶](media-services-account-concept.md)。

## <a name="live-streaming"></a>即時串流 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>廣播完成後，如何? 停止即時串流？

您可以從用戶端或伺服器端進行處理。

#### <a name="client-side"></a>用戶端

如果您的 web 應用程式想要在關閉瀏覽器時結束廣播，則應該提示使用者。 這是您的 web 應用程式可以處理的瀏覽器事件。

#### <a name="server-side"></a>伺服器端

您可以藉由訂閱 Azure 事件方格事件來監視即時事件。 如需詳細資訊，請參閱[EventGrid 事件架構](media-services-event-schemas.md#live-event-types)。

您可以：

* [訂閱](reacting-to-media-services-events.md)串流層級的[LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected)事件，並監視不會有任何重新進行的重新建立，以停止及刪除您的實況活動。
* [訂閱](reacting-to-media-services-events.md)追蹤層級的[心跳](media-services-event-schemas.md#liveeventingestheartbeat)事件。 如果所有追蹤的傳入位元速率下降為0，或最後一個時間戳記已不再增加，您可以安全地關閉實況活動。 每個追蹤的每隔20秒就會出現一次的心跳事件，因此可能有點冗長。

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>在即時串流期間如何? 插入中斷/影片和影像平板？

媒體服務 v3 即時編碼尚未支援在即時資料流進行期間插入影片或影像靜態圖像。 

您可以使用[即時內部部署編碼器](recommended-on-premises-live-encoders.md)來切換來源影片。 許多應用程式都提供切換來源的能力，包括 Telestream Wirecast、切換器 Studio （在 iOS 上）和 OBS Studio （免費應用程式）。

## <a name="content-protection"></a>內容保護

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>我應該使用 AES-128 清除金鑰加密或 DRM 系統嗎？

客戶通常不知道他們應該使用 AES 加密，還是 DRM 系統。 這兩個系統之間的主要差異是，使用 AES 加密時，會透過 TLS 將內容金鑰傳輸至用戶端，讓金鑰在傳輸中加密，但不含任何額外的加密（「明文」）。 因此，用來解密內容的金鑰可供用戶端播放程式存取，而且可以在用戶端的網路追蹤中以純文字方式查看。 AES-128 未加密金鑰加密適用於檢視者為受信任合作對象的使用案例 (例如，將在公司內部散發的公司影片加密，以供員工檢視)。

PlayReady、Widevine 和 FairPlay 等 DRM 系統都會針對用來解密內容的金鑰提供額外的加密層級，相較于 AES-128 清除金鑰。 除了 TLS 所提供的任何傳輸層級加密，內容金鑰也會加密為受 DRM 執行時間保護的金鑰。 此外，解密會在安全環境中，惡意使用者更難攻擊的作業系統層級上進行處理。 對於檢視器可能不是受信任的合作物件，而且您需要最高層級安全性的使用案例，我們建議您使用 DRM。

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>如何? 只向具有特定許可權的使用者顯示影片，而不使用 Azure AD 嗎？

您不需要使用任何特定的權杖提供者，例如 Azure Active Directory （Azure AD）。 您可以使用非對稱金鑰加密來建立自己的[JWT](https://jwt.io/)提供者（所謂的「安全權杖服務」或 STS）。 在您的自訂 STS 中，您可以根據您的商務邏輯新增宣告。

請確定簽發者、物件和宣告全都完全符合 JWT 中的內容和中`ContentKeyPolicyRestriction` `ContentKeyPolicy`所使用的值。

如需詳細資訊，請參閱[使用媒體服務動態加密來保護您的內容](content-protection-overview.md)。

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>如何和哪裡取得 JWT 權杖，然後再使用它來要求授權或金鑰？

針對生產環境，您必須擁有安全權杖服務（也就是 web 服務），這會在 HTTPS 要求發出 JWT 權杖。 若要進行測試，您可以使用[Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)中定義`GetTokenAsync`的方法所顯示的程式碼。

播放程式會在使用者經過驗證之後，向 STS 提出這類權杖的邀請，並將其指派為權杖的值。 您可以使用 [Azure 媒體播放器 API](https://amp.azure.net/libs/amp/latest/docs/)。

如需使用對稱金鑰或非對稱金鑰執行 STS 的範例，請參閱[JWT 工具](https://aka.ms/jwt)。 如需以使用這類 JWT 權杖 Azure 媒體播放機為基礎的播放程式範例，請參閱[Azure 媒體測試控管](https://aka.ms/amtest)。 （展開 [ **player_settings** ] 連結以查看權杖輸入）。

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>如何? 授權使用 AES 加密來串流影片的要求？

正確的方法是使用「安全權杖服務」。 在 STS 中，根據使用者設定檔，新增不同的宣告（例如「Premium 使用者」、「基本使用者」、「免費試用使用者」）。 在 JWT 中使用不同的宣告，使用者可以看見不同的內容。 對於不同的內容或資產`ContentKeyPolicyRestriction` ，會有對應`RequiredClaims`的值。

使用 Azure 媒體服務 Api 來設定授權/金鑰傳遞和加密您的資產（如[本範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)所示）。

如需詳細資訊，請參閱：

- [內容保護概觀](content-protection-overview.md)
- [設計具有存取控制的多重 DRM 內容保護系統](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>我應該使用 HTTP 或 HTTPS 嗎？
ASP.NET MVC 播放器應用程式必須支援下列功能：

* 透過 Azure AD (位於 HTTPS 底下) 的使用者驗證。
* 用戶端與 Azure AD (位於 HTTPS 底下) 之間的 JWT 交換。
* 用戶端 (必須在 HTTPS 底下) 的 DRM 授權取得，如果授權傳遞是由媒體服務提供。 PlayReady 產品套件不會針對授權傳遞來授權 HTTPS。 如果您的 PlayReady 授權伺服器是位於媒體服務以外的地方，可以使用 HTTP 或 HTTPS。

ASP.NET 播放器應用程式的最佳做法是使用 HTTPS，因此媒體播放器是在 HTTPS 的頁面上。 不過，HTTP 是用於串流處理的最佳方式，因此您需要考慮混合內容的下列問題：

* 瀏覽器不允許混合的內容。 但是像 Silverlight 和 OSMF 外掛程式之類的外掛程式，可以讓它更平滑和虛線。 混合內容是一項安全性考慮，因為能夠插入惡意的 JavaScript，這可能會使客戶資料面臨風險。 瀏覽器預設會封鎖這項功能。 目前唯一的解決之道是在伺服器端 (來源) 允許所有網域 (不論 HTTPS 或 HTTP)。 這可能也不是個好主意。
* 避免混合的內容。 播放器應用程式和媒體播放器都應該使用 HTTP 或 HTTPS。 當您現正播放混合內容時，SilverlightSS 技術需要清除混合內容警告。 FlashSS 技術會處理混合內容，而不會有混合內容的警告。
* 如果您的串流端點是在 2014 年 8 月之前建立的，將不會支援 HTTPS。 在此情況下，請針對 HTTPS 建立並使用新的串流端點。

### <a name="what-about-live-streaming"></a>即時串流呢？

您可以使用完全相同的設計和執行方式，將與程式相關聯的資產視為 VOD 資產，以協助保護媒體服務中的即時串流。 若要提供即時內容的多重 DRM 保護，請在將資產與即時輸出產生關聯之前，將相同的設定/處理套用至資產，如同它是 VOD 資產。

### <a name="what-about-license-servers-outside-media-services"></a>在媒體服務外部的授權伺服器呢？

客戶通常會在他們自己的資料中心或由 DRM 服務提供者主控的授權伺服器陣列中投資。 利用媒體服務內容保護，您就能在混合模式中運作。 內容可以在媒體服務中進行裝載和動態保護，而 DRM 授權是由媒體服務以外的伺服器所傳遞。 在此情況下，請考量下列變更：

* STS 必須發出權杖，讓授權伺服器陣列可接受且可驗證這類權杖。 例如，Axinom 所提供的 Widevine 授權伺服器要求包含權利訊息的特定 JWT。 您需要有 STS 來發出這類 JWT。 
* 您不再需要在媒體服務中設定授權傳遞服務。 當您設定`ContentKeyPolicy`時，您必須提供授權取得 url （針對 PlayReady、Widevine 和 FairPlay）。

> [!NOTE]
> Widevine 是 Google 所提供的服務，並受 Google 的服務條款和隱私權原則所約束。

## <a name="media-services-v2-vs-v3"></a>媒體服務 v2 與 v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>是否可以使用 Azure 入口網站來管理 v3 資源？

目前，您可以使用[Azure 入口網站](https://portal.azure.com/)來執行下列動作：

* 在媒體服務 v3 中管理[實況活動](live-events-outputs-concept.md)。 
* View （不管理） v3[資產](assets-concept.md)。 
* [取得存取 api 的相關資訊](access-api-portal.md)。 

針對所有其他管理工作（例如，[轉換和作業](transforms-jobs-concept.md)和[內容保護](content-protection-overview.md)），請使用[REST API](https://docs.microsoft.com/rest/api/media/)、 [Azure CLI](https://aka.ms/ams-v3-cli-ref)或其中一個支援的[sdk](media-services-apis-overview.md#sdks)。

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3 中是否有 AssetFile 概念？

`AssetFile`概念已從媒體服務 API 中移除，以將媒體服務與儲存體 SDK 相依性分開。 現在 Azure 儲存體，而不是媒體服務，會保留屬於儲存體 SDK 的資訊。 

如需詳細資訊，請參閱[遷移至媒體服務 v3](media-services-v2-vs-v3.md)。

### <a name="where-did-client-side-storage-encryption-go"></a>用戶端儲存體加密怎麼不見了？

我們現在建議您使用伺服器端儲存體加密（預設為開啟）。 如需詳細資訊，請參閱待用[資料的 Azure 儲存體服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

## <a name="offline-streaming"></a>離線串流

### <a name="fairplay-streaming-for-ios"></a>適用于 iOS 的 FairPlay 串流

下列常見問題可協助您針對 iOS 的離線 FairPlay 串流進行疑難排解。

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>為何在離線模式期間只播放音訊，但不會播放影片呢？

這個行為似乎是範例應用程式的設計使然。 在離線模式期間，如果有替代的音訊播放軌（這是 HLS 的情況），則 iOS 10 和 iOS 11 都會預設為替代的音訊播放軌。若要彌補 FPS 離線模式的這種行為，請從串流中移除替代的音訊播放軌。 若要在媒體服務上執行此動作，請新增動態資訊清單篩選**僅限音訊的 = false**。 換句話說，HLS URL 的結尾會是 **. ism/manifest （format = m3u8-m3u8-aapl-v3，audio only = false）**。 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>當我新增 audio-only=false 之後，為什麼它在離線模式期間仍舊只播放音訊，而不會播放影片呢？

視內容傳遞網路的快取索引鍵設計而定，可能會快取內容。 所以，請清除快取。

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>IOS 11 除了 iOS 10 之外是否支援 FPS 離線模式？

是。 iOS 10 和 iOS 11 均可支援 FPS 離線模式。

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>為什麼我在 FPS Server SDK 中找不到《Offline Playback with FairPlay Streaming and HTTP Live Streaming》(使用 FairPlay 串流和 HTTP 即時串流進行離線播放) 文件呢？

從 FPS Server SDK 第 4 版開始，此文件已合併至《FairPlay Streaming Programming Guide》(FairPlay 串流程式設計指南)。

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>在 iOS 裝置上的下載/離線檔案結構為何？

iOS 裝置上所下載的檔案結構看起來如下列螢幕擷取畫面。 `_keys` 資料夾會儲存下載的 FPS 授權，每個授權服務主機一個存放區檔案。 `.movpkg` 資料夾會儲存音訊和影片內容。 

第一個資料夾的名稱結尾為虛線，後面接著一個數位，包含影片內容。 數值是影片呈現的尖峰頻寬。 第二個資料夾名稱的結尾是一個破折號後面接著 0，此資料夾包含音訊內容。 第三個名`Data`為的資料夾包含 FPS 內容的主要播放清單。 最後，boot.xml 會提供 `.movpkg` 資料夾內容的完整描述。 

![FairPlay iOS 範例應用程式的離線檔案結構](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

以下是範例 boot .xml 檔案：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>適用于 Android 的 Widevine 串流

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>如何為其他用戶端/使用者和非持續性授權（離線停用）提供持續性授權（離線啟用）給其他人？ 我是否必須複製內容，並使用不同的內容金鑰？

因為媒體服務 v3 允許資產有多個`StreamingLocator`實例，您可以擁有：

* 一個`ContentKeyPolicy`實例`license_type = "persistent"`，其中`ContentKeyPolicyRestriction`包含了的`"persistent"`宣告，以及`StreamingLocator`其。
* 另`ContentKeyPolicy`一個實例`license_type="nonpersistent"`， `ContentKeyPolicyRestriction`其中的宣告`"nonpersistent`為，且其`StreamingLocator`為。
* 具有`StreamingLocator`不同`ContentKey`值的兩個實例。

根據自訂 STS 的商務邏輯，JWT 權杖中會發出不同宣告。 使用權杖時，只能取得對應的授權，且只能播放對應的 URL。

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Widevine 和媒體服務 DRM 安全性層級之間的對應為何？

Google 的「Widevine DRM 架構總覽」定義了三個安全性層級。 不過， [Widevine 授權範本上的 Azure 媒體服務檔概述了](widevine-license-template-overview.md)五個安全性層級（播放的用戶端健全需求）。 本節說明安全性層級的對應方式。

這兩組安全性層級都是由 Google Widevine 所定義。 差異在於使用層級：架構或 API。 Widevine API 中使用的是五個安全性層級。 `content_key_specs`物件（包含`security_level`）會還原序列化，並由 Azure 媒體服務 Widevine 授權服務傳遞給 Widevine 的全域傳遞服務。 下表顯示兩組安全性層級之間的對應。

| **Widevine 架構中定義的安全性等級** |**Widevine API 中使用的安全性層級**|
|---|---| 
| **安全性層級 1**：所有內容處理、密碼編譯和控制都是在信任的執行環境（t）內執行。 在某些實作為模型中，安全性處理可能會在不同的晶片中執行。|**security_level = 5**：媒體的加密、解碼和所有處理（壓縮和未壓縮）都必須在硬體支援的中處理。<br/><br/>**security_level = 4**：內容的加密和解碼必須在硬體支援的中執行。|
**安全性層級 2**：在此範圍內執行密碼編譯（但非視頻處理）。 解密的緩衝區會傳回到應用程式網域，並透過個別的影片硬體或軟體進行處理。 不過，在層級2，密碼編譯資訊仍然只會在後端進行處理。| **security_level = 3**：金鑰資料和加密作業必須在硬體支援的機碼中執行。 |
| **安全性層級 3**：裝置上沒有任何 t。 您可以採取適當的措施，以保護主機作業系統上的密碼編譯資訊和解密內容。 層級3的執行可能也包含硬體密碼編譯引擎，但只會增強效能，而不是安全性。 | **security_level = 2**：軟體加密和模糊的解碼器都是必要的。<br/><br/>**security_level = 1**：需要以軟體為基礎的白箱加密。|

#### <a name="why-does-content-download-take-so-long"></a>為什麼內容的下載時間這麼久？

有兩種方式可提升下載速度：

* 啟用內容傳遞網路，讓使用者更有可能被叫用，而不是來源/串流端點來下載內容。 如果使用者叫用串流端點，則會動態地封裝和加密每個 HLS 區段或虛線片段。 雖然此延遲是每個區段或片段的毫秒數，但當您有一個小時的影片時，累積的延遲可能會很大，並會導致下載較長的時間。
* 提供選項，讓使用者選擇性地下載視頻品質層和音訊播放軌，而不是所有內容。 若是離線模式，則不需要下載所有品質層。 作法有二：

  * 由用戶端控制：播放機應用程式會自動選取或使用者選取 [視頻品質] 圖層和 [音訊播放軌] 來下載。
  * 服務控制：您可以使用 Azure 媒體服務中的動態資訊清單功能來建立（全域）篩選器，將 HLS 播放清單或虛線 MPD 限制為單一視頻品質層和選取的音軌。 然後，呈現給使用者的下載 URL 將包含此篩選準則。

## <a name="next-steps"></a>後續步驟

[媒體服務 v3 概觀](media-services-overview.md)
