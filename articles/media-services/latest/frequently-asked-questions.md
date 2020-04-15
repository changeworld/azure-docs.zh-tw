---
title: Azure 媒體服務 v3 常見問題集 | Microsoft Docs
description: 本文回答了有關 Azure 媒體服務 v3 的常見問題。
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309193"
---
# <a name="media-services-v3-frequently-asked-questions"></a>媒體服務 v3 常見問題

本文回答了有關 Azure 媒體服務 v3 的常見問題。

## <a name="general"></a>一般

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>哪些 Azure 角色可以對 Azure 媒體服務資源執行操作? 

請參閱[媒體服務帳戶的基於角色的訪問控制 (RBAC)。](rbac-overview.md)

### <a name="how-do-i-stream-to-apple-ios-devices"></a>如何流式傳輸到 Apple iOS 設備?

確保路徑末尾(URL **/Manifest**部分之後)有 **(格式_m3u8-aapl)** 告訴流源伺服器返回 HTTP 即時流式處理 (HLS) 內容,供 Apple iOS 本機裝置上使用。 有關詳細資訊,請參閱[傳遞內容](dynamic-packaging-overview.md)。

### <a name="how-do-i-configure-media-reserved-units"></a>如何設定編碼保留單元？

對於由媒體服務 v3 或視頻索引器觸發的音訊分析和視頻分析作業,我們建議您使用 10 個 S3 媒體保留單位 (MRUs) 預配帳戶。 如果需要超過 10 個 S3 MR 並網,請使用 Azure[門戶](https://portal.azure.com/)打開支援票證。

有關詳細資訊,請參閱[縮放媒體處理](media-reserved-units-cli-how-to.md)。

### <a name="what-is-the-recommended-method-to-process-videos"></a>建議用來處理影片的方法為何？

使用[轉換](https://docs.microsoft.com/rest/api/media/transforms)可設定視訊編碼或分析的一般工作。 每個轉換都會描述配方或工作流程，以便處理您的視訊或音訊檔案。 [作業](https://docs.microsoft.com/rest/api/media/jobs)是媒體服務應用轉換到輸入視頻或音訊內容的實際請求。 創建轉換後,可以使用媒體服務 API 或任何已發布的 SDK 提交作業。 如需詳細資訊，請參閱[轉換和作業](transforms-jobs-concept.md)。

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>我已上傳、編碼以及發佈視訊。 當我嘗試流式傳輸視頻時,為什麼不會播放視頻?

最常見的原因之一是,您沒有嘗試在"運行"狀態中播放的流式處理終結點。

### <a name="how-does-pagination-work"></a>分頁的運作方式為何？

使用分頁時,應始終使用下一個連結枚舉集合,而不是依賴於特定的頁面大小。 如需詳細資訊和範例，請參閱[篩選、排序、分頁](entities-overview.md)。

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Azure 媒體服務 v3 中尚未提供哪些功能?

有關詳細資訊,請參閱[與 v2 API 有關的功能間隙](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis)。

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>在訂閱之間移動媒體服務帳戶的過程是什麼?  

有關詳細資訊,請參閱[在訂閱之間行動媒體服務帳戶](media-services-account-concept.md)。

## <a name="live-streaming"></a>即時串流 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>廣播完成後如何停止直播?

可以從用戶端或伺服器端接近它。

#### <a name="client-side"></a>用戶端

如果使用者想在關閉瀏覽器時結束廣播,則 Web 應用程式應提示使用者。 這是 Web 應用程式可以處理的瀏覽器事件。

#### <a name="server-side"></a>伺服器端

您可以通過訂閱 Azure 事件網格事件來監視即時事件。 有關詳細資訊,請參閱[事件網格事件架構](media-services-event-schemas.md#live-event-types)。

您可以：

* [訂閱](reacting-to-media-services-events.md)串流級[Microsoft.Media.LiveEventEncoder 斷開連接](media-services-event-schemas.md#liveeventencoderdisconnected)事件,並監視一段時間內沒有重新連接來停止和刪除即時事件。
* [訂閱](reacting-to-media-services-events.md)軌道級[檢測信號](media-services-event-schemas.md#liveeventingestheartbeat)事件。 如果所有軌道的傳入比特率降為 0,或者上次時間戳不再增加,則可以安全地關閉即時事件。 每個軌道每 20 秒都會出現一次檢測信號事件,因此可能有點冗長。

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>如何在即時流期間插入中斷/視頻和圖像板?

媒體服務 v3 即時編碼尚未支援在即時資料流進行期間插入影片或影像靜態圖像。 

您可以使用[即時內部部署編碼器](recommended-on-premises-live-encoders.md)來切換來源影片。 許多應用程式提供切換源的功能,包括遠端有線廣播、切換工作室(iOS 上)和 OBS 工作室(免費應用程式)。

## <a name="content-protection"></a>內容保護

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>我應該使用 AES-128 清除金鑰加密還是 DRM 系統?

客戶通常不知道他們應該使用 AES 加密，還是 DRM 系統。 兩個系統之間的主要區別是,通過 AES 加密,內容密鑰通過 TLS 傳輸到用戶端,以便密鑰在傳輸過程中加密,但無需任何其他加密("在明確"中)。 因此,用戶端播放器可以訪問用於解密內容的密鑰,並且可以在用戶端上的網路跟蹤中以純文本形式查看。 AES-128 未加密金鑰加密適用於檢視者為受信任合作對象的使用案例 (例如，將在公司內部散發的公司影片加密，以供員工檢視)。

與 AES-128 清除金鑰相比,PlayReady、Widevine 和 FairPlay 等 DRM 系統都為用於解密內容的金鑰提供了額外的加密級別。 除了 TLS 提供的任何傳輸級加密外,內容密鑰還加密到受 DRM 執行時保護的密鑰。 此外，解密會在安全環境中，惡意使用者更難攻擊的作業系統層級上進行處理。 對於查看器可能不是受信任方且需要最高級別的安全性的用例,我們建議 DRM。

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>如何在不使用 Azure AD 的情況下僅向具有特定許可權的使用者顯示視頻?

您不必使用任何特定的權杖提供程式,如 Azure 活動目錄 (Azure AD)。 您可以使用非對稱密鑰加密創建自己的[JWT](https://jwt.io/)提供程式(所謂的安全權杖服務或 STS)。 在自訂 STS 中,您可以基於業務邏輯添加聲明。

確保頒發者、受眾和聲明在所有內容與 中`ContentKeyPolicyRestriction``ContentKeyPolicy`使用的 值之間完全匹配。

關於詳細資訊,請參閱[使用媒體服務動態加密保護內容](content-protection-overview.md)。

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>在使用 JWT 權杖請求許可證或密鑰之前,我是如何以及從哪裡獲得 JWT 權杖的?

對於生產,您需要有安全權杖服務(即 Web 服務),該服務在 HTTPS 請求時發出 JWT 令牌。 對於測試,可以使用`GetTokenAsync`[Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)中定義的方法中顯示的代碼。

玩家在使用者經過身份驗證後向 STS 發出此類權杖的請求,並將其分配為權杖的值。 您可以使用 [Azure 媒體播放器 API](https://amp.azure.net/libs/amp/latest/docs/)。

有關使用對稱鍵或非對稱鍵執行 STS 的範例,請參閱[JWT 工具](https://aka.ms/jwt)。 有關使用此類 JWT 權杖以 Azure 媒體播放器的播放器的範例,請參考[Azure 媒體測試工具](https://aka.ms/amtest)。 (展開**player_settings**連結以查看權杖輸入。

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>如何授權使用 AES 加密流式傳輸視頻的請求?

正確的方法是使用安全權杖服務。 在 STS 中,根據使用者配置檔添加不同的聲明(如「高級使用者」、「基本使用者」、「免費試用使用者」 。 在 JWT 中使用不同的宣告，使用者可以看見不同的內容。 對於不同的內容或資產,`ContentKeyPolicyRestriction`將具有`RequiredClaims`相應的 值。

使用 Azure 媒體服務 API 配置許可證/金鑰傳遞並加密資產([如本範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)所示)。

如需詳細資訊，請參閱

- [內容保護概觀](content-protection-overview.md)
- [設計具有存取控制的多重 DRM 內容保護系統](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>我應該使用 HTTP 還是 HTTPS?
ASP.NET MVC 播放器應用程式必須支援下列功能：

* 透過 Azure AD (位於 HTTPS 底下) 的使用者驗證。
* 用戶端與 Azure AD (位於 HTTPS 底下) 之間的 JWT 交換。
* 用戶端 (必須在 HTTPS 底下) 的 DRM 授權取得，如果授權傳遞是由媒體服務提供。 PlayReady 產品套件不會針對授權傳遞來授權 HTTPS。 如果您的 PlayReady 授權伺服器是位於媒體服務以外的地方，可以使用 HTTP 或 HTTPS。

ASP.NET 播放器應用程式的最佳做法是使用 HTTPS，因此媒體播放器是在 HTTPS 的頁面上。 但是,HTTP 是流式處理的首選,因此您需要考慮包含混合內容的問題:

* 瀏覽器不允許混合的內容。 但是,像 Silverlight 和 OSMF 外掛程式這樣的外掛程式,適用於平滑和 DASH 確實允許它。 混合內容是一個安全問題,因為注入惡意 JavaScript 的能力受到威脅,這可能會使客戶數據面臨風險。 瀏覽器預設會封鎖這項功能。 目前唯一的解決之道是在伺服器端 (來源) 允許所有網域 (不論 HTTPS 或 HTTP)。 這可能也不是個好主意。
* 避免混合的內容。 播放器應用程式和媒體播放器都應該使用 HTTP 或 HTTPS。 當您播放混合內容時,SilverlightSS 技術需要清除混合內容警告。 FlashSS 技術無需混合內容警告即可處理混合內容。
* 如果您的串流端點是在 2014 年 8 月之前建立的，將不會支援 HTTPS。 在此情況下，請針對 HTTPS 建立並使用新的串流端點。

### <a name="what-about-live-streaming"></a>即時串流呢？

通過將與程式關聯的資產視為 VOD 資產,可以使用完全相同的設計和實現來幫助保護媒體服務中的即時流。 要提供即時內容的多 DRM 保護,在將資產與即時輸出關聯之前,對資產應用與 VOD 資產相同的設置/處理。

### <a name="what-about-license-servers-outside-media-services"></a>在媒體服務外部的授權伺服器呢？

通常,客戶在自己的資料中心或由 DRM 服務提供者託管的許可證伺服器場中投資。 利用媒體服務內容保護，您就能在混合模式中運作。 內容可以在媒體服務中託管和動態保護,而DRM許可證由媒體服務外部的伺服器提供。 在此情況下，請考量下列變更：

* STS 必須發出權杖，讓授權伺服器陣列可接受且可驗證這類權杖。 例如，Axinom 所提供的 Widevine 授權伺服器要求包含權利訊息的特定 JWT。 您需要有一個 STS 才能發出這樣的 JWT。 
* 您不再需要在媒體服務中設定授權傳遞服務。 設定`ContentKeyPolicy`時,您需要提供許可證獲取 URL(用於 PlayReady、Widevine 和 FairPlay)。

> [!NOTE]
> Widevine 是Google提供的服務,受Google的服務條款和隱私政策的約束。

## <a name="media-services-v2-vs-v3"></a>媒體服務 v2 vs. v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>是否可以使用 Azure 入口網站來管理 v3 資源？

目前,可以使用 Azure[門戶](https://portal.azure.com/):

* 在媒體服務 v3[中管理 即時事件](live-events-outputs-concept.md)。 
* 檢視(未管理)v3[資產](assets-concept.md)。 
* [取得存取 API 的資訊](access-api-portal.md)。 

對於所有其他管理任務(例如,[轉換和作業](transforms-jobs-concept.md)以及[內容保護](content-protection-overview.md)),請使用[REST API、Azure](https://docs.microsoft.com/rest/api/media/) [CLI](https://aka.ms/ams-v3-cli-ref)或受支援的[SDK](media-services-apis-overview.md#sdks)之一。

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3 中是否有 AssetFile 概念？

該`AssetFile`概念從媒體服務 API 中刪除,以將媒體服務與儲存 SDK 依賴項分開。 現在,Azure 存儲(而不是媒體服務)保留屬於存儲 SDK 中的資訊。 

如需詳細資訊，請參閱[遷移至媒體服務 v3](media-services-v2-vs-v3.md)。

### <a name="where-did-client-side-storage-encryption-go"></a>用戶端儲存體加密怎麼不見了？

現在,我們建議您使用伺服器端存儲加密(預設情況下處於打開狀態)。 有關詳細資訊,請參閱[Azure 儲存服務加密,瞭解靜態資料](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

## <a name="offline-streaming"></a>離線流式處理

### <a name="fairplay-streaming-for-ios"></a>iOS 的公平播放流式處理

以下常見問題有助於為 iOS 解決離線 FairPlay 流式處理問題。

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>為何在離線模式期間只播放音訊，但不會播放影片呢？

這個行為似乎是範例應用程式的設計使然。 當在離線模式下存在備用音軌(HLS就是這種情況),iOS 10 和 iOS 11 預設為備用音軌。要補償 FPS 離線模式的此行為,請從流中刪除備用音軌。 在媒體服務上執行此操作,新增動態清單篩選器**僅音訊 _false**。 換句話說,HLS URL 以 **.ism/清單(格式_m3u8-aapl,僅音訊=false)** 結尾。 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>當我新增 audio-only=false 之後，為什麼它在離線模式期間仍舊只播放音訊，而不會播放影片呢？

根據內容交付網路的緩存密鑰設計,可能會緩存內容。 所以，請清除快取。

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>除了 iOS 10 之外,iOS 11 上是否支援 FPS 離線模式?

是。 iOS 10 和 iOS 11 均可支援 FPS 離線模式。

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>為什麼我在 FPS Server SDK 中找不到《Offline Playback with FairPlay Streaming and HTTP Live Streaming》(使用 FairPlay 串流和 HTTP 即時串流進行離線播放) 文件呢？

從 FPS Server SDK 第 4 版開始，此文件已合併至《FairPlay Streaming Programming Guide》(FairPlay 串流程式設計指南)。

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>在 iOS 裝置上的下載/離線檔案結構為何？

iOS 裝置上所下載的檔案結構看起來如下列螢幕擷取畫面。 `_keys` 資料夾會儲存下載的 FPS 授權，每個授權服務主機一個存放區檔案。 `.movpkg` 資料夾會儲存音訊和影片內容。 

名稱以破折號結尾後跟數位的第一個資料夾包含視頻內容。 數值是視頻再現的峰值頻寬。 第二個資料夾名稱的結尾是一個破折號後面接著 0，此資料夾包含音訊內容。 第三個資料夾`Data`包含 FPS 內容的主播放清單。 最後，boot.xml 會提供 `.movpkg` 資料夾內容的完整描述。 

![公平播放 iOS 範例應用的離線檔結構](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

下面是一個範例 boot.xml 檔:

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

### <a name="widevine-streaming-for-android"></a>適用於安卓的寬文流

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>如何為某些用戶端/使用者提供持久許可證(啟用離線許可證),為其他用戶端/使用者和非持久許可證(離線禁用)? 我是否要複製內容並使用單獨的內容鍵?

由於媒體服務 v3 允許資產`StreamingLocator`具有多個 實例,因此您可以具有:

* 一`ContentKeyPolicy``license_type = "persistent"``ContentKeyPolicyRestriction`個實體 ,其中有`"persistent"`宣告,`StreamingLocator`其 。
* 另`ContentKeyPolicy`一個`license_type="nonpersistent"`實體`ContentKeyPolicyRestriction`,`"nonpersistent`與宣告`StreamingLocator`的 「, 及其 」 。
* 具有`StreamingLocator`不同`ContentKey`值的兩個實例。

根據自訂 STS 的商務邏輯，JWT 權杖中會發出不同宣告。 使用權杖時，只能取得對應的授權，且只能播放對應的 URL。

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Widevine 和媒體服務 DRM 安全級別之間的映射是什麼?

谷歌的「威文DRM架構概述」定義了三個安全級別。 但是[,Widevine 許可證範本上的 Azure 媒體服務文檔](widevine-license-template-overview.md)概述了五個安全級別(播放的用戶端魯棒性要求)。 本節介紹安全級別如何映射。

這兩組安全級別都由 Google Widevine 定義。 區別在於使用級別:體系結構或 API。 Widevine API 中使用的是五個安全性層級。 包含`content_key_specs`的物件`security_level`被反序列化,並通過 Azure 媒體服務 Widevine 許可證服務傳遞給 Widevine 全球交付服務。 下表顯示了兩組安全級別之間的映射。

| **在 Widevine 架構結構中定義的安全等級** |**Widevine API 中使用的安全等級**|
|---|---| 
| **安全級別 1:** 所有內容處理、加密和控制都在受信任的執行環境 (TEE) 中執行。 在某些實現模型中,安全處理可能在不同的晶元中執行。|**security_level=5**:媒體的加密、解碼和所有處理(壓縮和未壓縮)必須在硬體支援的 TEE 中處理。<br/><br/>**security_level_4**: 內容的加密和解碼必須在硬體支援的 TEE 中執行。|
**安全級別 2**:加密(但不視訊處理)在 TEE 中執行。 解密的緩衝區將返回到應用程式域,並通過單獨的視頻硬體或軟體進行處理。 但是,在級別 2 中,加密資訊仍僅在 TEE 內處理。| **security_level=3:** 金鑰材料和加密操作必須在硬體支援的 TEE 中執行。 |
| **安全級別 3**:設備上沒有 TEE。 可以採取適當的措施來保護主機操作系統上的加密資訊和解密內容。 級別 3 實現可能還包括硬體加密引擎,但僅增強性能,而不是安全性。 | **security_level=2**:軟體加密和模糊解碼器是必需的。<br/><br/>**security_level=1:** 需要基於軟體的白箱加密。|

#### <a name="why-does-content-download-take-so-long"></a>為什麼內容的下載時間這麼久？

有兩種方式可提升下載速度：

* 啟用內容交付網路,以便使用者更有可能點擊該網路,而不是內容下載的原點/流式處理終結點。 如果使用者命中流式處理終結點,則每個 HLS 段或 DASH 片段都會動態打包和加密。 即使此延遲對於每個段或片段以毫秒為單位,但當您具有一小時的視頻時,累積的延遲可能很大,並導致下載時間更長。
* 為使用者提供選擇性下載視頻品質圖層和音軌(而不是所有內容)的選項。 對於離線模式,下載所有品質圖層沒有意義。 作法有二：

  * 用戶端控制:播放器應用自動選擇或使用者選擇視頻品質圖層和要下載的音軌。
  * 服務控制:您可以使用 Azure 媒體服務中的動態清單功能建立(全域)篩選器,該篩選器將 HLS 播放清單或 DASH MPD 限制為單一視訊品質圖層和選擇的音軌。 然後,向使用者顯示的下載 URL 將包括此篩選器。

## <a name="next-steps"></a>後續步驟

[媒體服務 v3 概觀](media-services-overview.md)
