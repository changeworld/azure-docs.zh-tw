---
title: Azure 媒體服務 v3 常見問題集 | Microsoft Docs
description: 本文提供 Azure 媒體服務 v3 常見問題集的解答。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: 11123ee04dd02a60dff0b88e2e6e85fcd613a7d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068000"
---
# <a name="media-services-v3-frequently-asked-questions"></a>媒體服務 v3 常見問題

本文提供 Azure 媒體服務 (AMS) v3 常見問題集的解答。

## <a name="general"></a>一般

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>哪些 Azure 角色可以對 Azure 媒體服務資源執行操作？ 

請參閱[媒體服務帳戶的基於角色的存取控制 （RBAC）。](rbac-overview.md)

### <a name="how-do-you-stream-to-apple-ios-devices"></a>如何串流至 Apple iOS 裝置？

請確保您的路徑末尾（URL 的"/清單"部分之後）有"（格式_m3u8-aapl）"，告訴流源伺服器返回 HLS 內容，供 Apple iOS 本機設備上使用（有關詳細資訊，請參閱[交付內容](dynamic-packaging-overview.md)）。

### <a name="how-do-i-configure-media-reserved-units"></a>如何設定編碼保留單元？

針對由媒體服務 v3 或影片索引子觸發的音訊分析和影片分析作業，強烈建議您使用 10 個 S3 MRU 佈建帳戶。 如果您需要 10 個以上的 S3 MRU，請使用 [Azure 入口網站](https://portal.azure.com/)開立支援票證。

如需詳細資訊，請參閱[使用 CLI 調整媒體處理的規模](media-reserved-units-cli-how-to.md)。

### <a name="what-is-the-recommended-method-to-process-videos"></a>建議用來處理影片的方法為何？

使用[轉換](https://docs.microsoft.com/rest/api/media/transforms)可設定視訊編碼或分析的一般工作。 每個**轉換**都會描述配方或工作流程，以便處理您的視訊或音訊檔案。 [作業](https://docs.microsoft.com/rest/api/media/jobs)是媒體服務將**轉換**應用於給定輸入視頻或音訊內容的實際請求。 一旦建立轉換，您就可以使用媒體服務 API 或使用任何已發佈的 SDK 提交作業。 有關詳細資訊，請參閱[轉換和作業](transforms-jobs-concept.md)。

### <a name="i-uploaded-encoded-and-published-a-video-what-would-be-the-reason-the-video-does-not-play-when-i-try-to-stream-it"></a>我已上傳、編碼以及發佈視訊。 當我試著串流處理視頻時，為什麼不會播放視頻？

其中一個最常見的原因就是您嘗試從中進行播放的串流端點不是處於 [執行中] 狀態。

### <a name="how-does-pagination-work"></a>分頁的運作方式為何？

使用分頁時，您應一律使用下一頁連結來列舉集合，而不應依存於特定頁面大小。 如需詳細資訊和範例，請參閱[篩選、排序、分頁](entities-overview.md)。

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Azure 媒體服務 v3 中尚未提供哪些功能？

有關詳細資訊，請參閱[與 v2 API 有關的功能間隙](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis)。

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>在訂閱之間移動媒體服務帳戶的過程是什麼？  

有關詳細資訊，請參閱[在訂閱之間移動媒體服務帳戶](media-services-account-concept.md)。

## <a name="live-streaming"></a>即時串流 

### <a name="how-to-stop-the-live-stream-after-the-broadcast-is-done"></a>廣播完成後如何停止直播？

可以從用戶端或伺服器端接近它。

#### <a name="client-side"></a>用戶端

如果使用者要關閉瀏覽器，則 Web 應用程式應提示使用者結束廣播。 這是 Web 應用程式可以處理的瀏覽器事件。

#### <a name="server-side"></a>伺服器端

您可以通過訂閱事件網格事件來監視即時事件。 有關詳細資訊，請參閱[事件網格事件架構](media-services-event-schemas.md#live-event-types)。

* 您可以[訂閱](reacting-to-media-services-events.md)流級[Microsoft.Media.LiveEventEncoder 斷開連接](media-services-event-schemas.md#liveeventencoderdisconnected)，並監視一段時間內沒有重新連接來停止和刪除即時事件。
* 或者，您可以[訂閱](reacting-to-media-services-events.md)軌道級別[活動訊號](media-services-event-schemas.md#liveeventingestheartbeat)事件。 如果所有軌道的傳入位元速率降至 0;則為 0。或上次時間戳記不再增加，那麼您也可以安全地關閉即時事件。 每個軌道每 20 秒都會出現一次活動訊號事件，因此可能有點冗長。

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>如何在即時資料流進行期間插入中斷/影片和影像靜態圖像？

媒體服務 v3 即時編碼尚未支援在即時資料流進行期間插入影片或影像靜態圖像。 

您可以使用[即時內部部署編碼器](recommended-on-premises-live-encoders.md)來切換來源影片。 許多應用程式都可讓您切換來源，包括 Telestream Wirecast、Switcher Studio (在 iOS 上)、OBS Studio (免費應用程式) 等等。

## <a name="content-protection"></a>內容保護

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>我應該使用 AES-128 清除金鑰加密還是 DRM 系統？

客戶通常不知道他們應該使用 AES 加密，還是 DRM 系統。 兩個系統之間的主要區別是，使用 AES 加密，內容金鑰通過 TLS 傳輸到用戶端，以便金鑰在傳輸過程中加密，但無需任何其他加密（"在明確"中）。 因此，用戶端播放機可以訪問用於解密內容的金鑰，並且可以在用戶端上的網路跟蹤中以純文字形式查看。 AES-128 明明金鑰加密適用于查看者是受信任方的用例（例如，加密在公司內分發的公司視頻供員工查看）。

與 AES-128 清除金鑰相比，PlayReady、Widevine 和 FairPlay 等 DRM 系統都為解密內容的金鑰提供了額外的加密級別。 除了 TLS 提供的任何傳輸級別加密外，內容金鑰已加密到受 DRM 運行時保護的金鑰。 此外，解密會在安全環境中，惡意使用者更難攻擊的作業系統層級上進行處理。 針對檢視者可能不是受信任的合作對象，而且您需要最高層級安全性的使用案例，建議使用 DRM。

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>如何在不使用 Azure AD 的情況下僅向具有特定許可權的使用者顯示視頻？

您不必使用任何特定的權杖提供程式（如 Azure AD）。 您可以使用非對稱金鑰加密創建自己的[JWT](https://jwt.io/)提供程式（所謂的 STS、安全權杖服務）。 在自訂 STS 中，您可以基於業務邏輯添加聲明。

確保發行人、受眾和聲明在 JWT 中的內容與內容關鍵政策中使用的內容關鍵策略限制之間完全符合。

有關詳細資訊，請參閱[使用媒體服務動態加密保護內容](content-protection-overview.md)。

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>如何以及在何處取得 JWT 權杖，再用來要求授權或金鑰？

1. 對於生產，您需要有一個安全權杖服務 （STS） （Web 服務），該服務在 HTTPS 請求時發出 JWT 權杖。 用於測試時，您可以使用 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) 中定義的 **GetTokenAsync** 方法所示的程式碼。
2. 播放器必須在使用者經過驗證之後，向 STS 提出此類權杖的要求，並將它指派為權杖的值。 您可以使用 [Azure 媒體播放器 API](https://amp.azure.net/libs/amp/latest/docs/)。

* 有關運行 STS 的示例，請使用對稱鍵和非對稱鍵，請參閱[https://aka.ms/jwt](https://aka.ms/jwt)。 
* 有關基於 Azure 媒體播放機使用此類 JWT 權杖的播放機的示例，[https://aka.ms/amtest](https://aka.ms/amtest)請參閱（展開"player_settings"連結以查看權杖輸入）。

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>如何授權要求以使用 AES 加密來串流處理影片？

正確的方法是利用 STS (Secure Token Service)：

在 STS 中，根據使用者設定檔，新增不同的宣告 (例如「進階使用者」、「基本使用者」、「免費試用使用者」)。 在 JWT 中使用不同的宣告，使用者可以看見不同的內容。 當然，對於不同的內容/資產，ContentKeyPolicyRestriction 會有對應的 RequiredClaims。

使用 Azure 媒體服務 API 配置許可證/金鑰傳遞並加密資產（[如本示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)所示）。

如需詳細資訊，請參閱

- [內容保護概觀](content-protection-overview.md)
- [設計具有存取控制的多重 DRM 內容保護系統](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP 或 HTTPS？
ASP.NET MVC 播放器應用程式必須支援下列功能：

* 透過 Azure AD (位於 HTTPS 底下) 的使用者驗證。
* 用戶端與 Azure AD (位於 HTTPS 底下) 之間的 JWT 交換。
* 用戶端 (必須在 HTTPS 底下) 的 DRM 授權取得，如果授權傳遞是由媒體服務提供。 PlayReady 產品套件不會針對授權傳遞來授權 HTTPS。 如果您的 PlayReady 授權伺服器是位於媒體服務以外的地方，可以使用 HTTP 或 HTTPS。

ASP.NET 播放器應用程式的最佳做法是使用 HTTPS，因此媒體播放器是在 HTTPS 的頁面上。 不過，HTTP 十分適用於串流處理，因此您必須考量混合內容的問題。

* 瀏覽器不允許混合的內容。 但是諸如 Silverlight 等外掛程式以及適用於 Smooth 和 DASH 的 OSMF 外掛程式則允許。 混合的內容有安全性考量，因為能夠插入惡意 JavaScript 的威脅，從而造成客戶資料處於風險之中。 瀏覽器預設會封鎖這項功能。 目前唯一的解決之道是在伺服器端 (來源) 允許所有網域 (不論 HTTPS 或 HTTP)。 這可能也不是個好主意。
* 避免混合的內容。 播放器應用程式和媒體播放器都應該使用 HTTP 或 HTTPS。 播放混合的內容時，silverlightSS 技術需要清除混合內容警告。 flashSS 技術會處理混合的內容，而沒有混合內容警告。
* 如果您的串流端點是在 2014 年 8 月之前建立的，將不會支援 HTTPS。 在此情況下，請針對 HTTPS 建立並使用新的串流端點。

### <a name="what-about-live-streaming"></a>即時串流呢？

您可以使用完全相同的設計和實作，藉由將與程式相關聯的資產當成「VOD 資產」，來保護媒體服務中的即時串流。 要提供即時內容的多 DRM 保護，請對資產應用相同的設置/處理，就像將資產與即時輸出關聯之前的資產是 VOD 資產一樣。

### <a name="what-about-license-servers-outside-media-services"></a>在媒體服務外部的授權伺服器呢？

通常，客戶可能會在他們自己的資料中心或由 DRM 服務提供者裝載的位置，投資授權伺服器陣列。 利用媒體服務內容保護，您就能在混合模式中運作。 您可以在媒體服務中裝載內容並以動態方式保護內容，同時伺服器會在媒體服務外部傳遞 DRM 授權。 在此情況下，請考量下列變更：

* STS 必須發出權杖，讓授權伺服器陣列可接受且可驗證這類權杖。 例如，Axinom 所提供的 Widevine 授權伺服器要求包含權利訊息的特定 JWT。 因此，您需要有 STS 才能發出這類的 JWT。 
* 您不再需要在媒體服務中設定授權傳遞服務。 當您設定 ContentKeyPolicies 時，必須提供授權取得 URL (針對 PlayReady、Widevine 和 FairPlay)。

> [!NOTE]
> Widevine 是 Google Inc. 所提供的服務，並受到 Google Inc. 的服務條款和隱私權原則所約束。

## <a name="media-services-v2-vs-v3"></a>媒體服務 v2 和 v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>是否可以使用 Azure 入口網站來管理 v3 資源？

目前，可以使用 Azure[門戶](https://portal.azure.com/)：

* 管理媒體服務 v3[即時事件](live-events-outputs-concept.md)， 
* 視圖（未管理） v3[資產](assets-concept.md)， 
* [獲取有關訪問 API 的資訊](access-api-portal.md)。 

對於所有其他管理工作（例如，[轉換、作業](transforms-jobs-concept.md)和[內容保護](content-protection-overview.md)），請使用[REST API](https://docs.microsoft.com/rest/api/media/) [、CLI](https://aka.ms/ams-v3-cli-ref)或受支援的[SDK](media-services-apis-overview.md#sdks)之一。

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3 中是否有 AssetFile 概念？

AssetFile 已從 AMS API 中移除，以便讓媒體服務與儲存體 SDK 相依性分開。 現在是由儲存體 (而非媒體服務) 保留屬於儲存體的資訊。 

如需詳細資訊，請參閱[遷移至媒體服務 v3](media-services-v2-vs-v3.md)。

### <a name="where-did-client-side-storage-encryption-go"></a>用戶端儲存體加密怎麼不見了？

現在會建議您使用伺服器端儲存體加密 (預設為開啟)。 如需詳細資訊，請參閱[待用資料的 Azure 儲存體服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

## <a name="next-steps"></a>後續步驟

[媒體服務 v3 概觀](media-services-overview.md)
