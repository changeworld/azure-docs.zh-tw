---
title: Stream Widevine Android offline
description: 本主題說明如何設定 Azure 媒體服務 v3 帳戶，以離線串流 Widevine 受保護的內容。
services: media-services
keywords: DASH, DRM, Widevine Offline Mode, ExoPlayer, Android, Widevine 離線模式
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: 4c7adab9c14075e12cf5656f9e798e78ff26ff0b
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955202"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>使用媒體服務 v3 進行 Android 的離線 Widevine 串流

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

除了保護線上串流的內容之外，媒體內容訂用帳戶與租賃服務能提供可在未連線到網際網路時使用的可下載內容。 在和網路中斷連線的飛行途中，您可能需要先將內容下載至手機或平板電腦，以在飛航模式中播放。 您可能會想要下載內容的其他案例如下：

- 某些內容提供者可能不允許在國家/地區的邊界之外傳遞 DRM 授權。 如果使用者想要在出國旅行時觀賞內容，則需要離線下載。
- 在某些國家/地區，網際網路可用性和/或頻寬有限。 使用者可能會選擇下載內容，以便能以較高的解析度觀賞，來獲得令人滿意的檢視體驗。

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

本文討論如何針對 Android 裝置上受 Widevine 保護的 DASH 內容實作離線模式播放。 離線 DRM 可讓您為內容提供訂用帳戶、租賃及採購模式，讓服務的客戶能在和網際網路中斷連線時，輕鬆地隨身攜帶內容。

我們會概述建置 Android 播放應用程式的三種選項：

> [!div class="checklist"]
> * 使用 ExoPlayer SDK 的 Java API 來建置播放程式
> * 使用 ExoPlayer SDK 的 Xamarin 繫結來建置播放程式
> * 使用 Chrome 行動瀏覽器 v62 或更新版本中的加密媒體延伸模組 (EME) 與媒體來源延伸模組 (MSE) 來建置播放程式

本文也會回答一些和離線串流受 Widevine 保護的內容相關的常見問題。

> [!NOTE]
> 離線 DRM 只會在您下載內容時，針對授權提出單一要求付費。 任何錯誤都不計費。

## <a name="prerequisites"></a>必要條件 

在 Android 裝置上實作適用於 Widevine 的離線 DRM 之前，您應該先：

- 熟悉由使用 Widevine DRM 來保護線上內容所導入的概念。 下列文件或範例會詳細說明這一點：
    - [設計具有存取控制的多重 DRM 內容保護系統](design-multi-drm-system-with-access-control.md)
    - [使用 DRM 動態加密與授權傳遞服務](protect-with-drm.md)
- 複製 https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git。

    您必須修改[使用 .NET 以 DRM 加密](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM)中的程式碼，以新增 Widevine 組態。  
- 熟悉適用於 Android 的 Google ExoPlayer SDK，這是能夠支援離線 Widevine DRM 播放的開放原始碼視訊播放程式 SDK。 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer) \(英文\)
    - [ExoPlayer 開發人員指南](https://google.github.io/ExoPlayer/guide.html) \(英文\)
    - [ExoPlayer 開發人員部落格](https://medium.com/google-exoplayer) \(英文\)

## <a name="configure-content-protection-in-azure-media-services"></a>在 Azure 媒體服務中設定內容保護

在 [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) 方法中會有下列必要步驟：

1. 指定在授權傳遞服務中如何授權內容金鑰的傳遞： 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. 設定 Widevine 授權範本：  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. 建立 ContentKeyPolicyOptions：

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>啟用離線模式

若要針對 Widevine 授權啟用 **離線** 模式，您必須設定 [Widevine 授權範本](widevine-license-template-overview.md)。 在 **policy_overrides** 物件中，將 **can_persist** 屬性設定為 **true** (預設值為 false)，如 [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563) 中所述。 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>設定 Android 播放程式以進行離線播放

若要開發適用於 Android 裝置的原生播放應用程式，最簡單的方法就是使用 [Google ExoPlayer SDK](https://github.com/google/ExoPlayer) \(英文\) 開放原始碼視訊播放程式 SDK。 ExoPlayer 支援 Android 原生 MediaPlayer API 目前不支援的功能，包括 MPEG 破折號和 Microsoft Smooth Streaming 傳遞通訊協定。

ExoPlayer 2.6 版及更新版本包括許多支援離線 Widevine DRM 播放的類別。 其中 OfflineLicenseHelper 類別能提供公用程式函式，以利於使用 DefaultDrmSessionManager 進行下載、更新及發行離線授權。 SDK 資料夾 "library/core/src/main/java/com/google/android/exoplayer2/offline/" 中所提供的類別支援下載離線視訊內容。

以下列出的類別有助於在適用於 Android 的 ExoPlayer SDK 中提供離線模式：

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- library/core/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

開發人員在應用程式開發期間，應參考 [ExoPlayer 開發人員指南](https://google.github.io/ExoPlayer/guide.html) \(英文\) 與對應的[開發人員部落格](https://medium.com/google-exoplayer) \(英文\)。 Google 目前尚未針對 ExoPlayer 應用程式在 Widevine 離線支援上發行完整記錄參考實作或範例程式碼，因此可用的資訊僅限於開發人員指南與部落格。 

### <a name="working-with-older-android-devices"></a>使用較舊的 Android 裝置

對於較舊的 Android 裝置，您必須針對下列 **policy_overrides** 屬性 (定義於 [Widevine 授權範本](widevine-license-template-overview.md)) 設定值：**rental_duration_seconds**、**playback_duration_seconds** 及 **license_duration_seconds**。 或者，您可以將它們設定為零，表示無限長/無限制的持續時間。  

請務必設定這些值，以避免發生整數溢位錯誤。 如需此問題的詳細說明，請參閱 https://github.com/google/ExoPlayer/issues/3150 和 https://github.com/google/ExoPlayer/issues/3112。 <br/>如果您未明確設定這些值，系統將會為 **PlaybackDurationRemaining** 與 **LicenseDurationRemaining** 指定非常大的值 (例如 9223372036854775807，其為 64 位元整數的最大正值 )。 這會導致 Widevine 授權呈現過期，因此不會進行解密。 

Android 5.0 Lollipop 或更新版本不會發生此問題，因為 Android 5.0 是第一個完全支援 ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) 與 64 位元平台的 Android 版本，而 Android 4.4 KitKat 和其他舊版 Android 則原本設計為支援 ARMv7 與 32 位元平台。

## <a name="using-xamarin-to-build-an-android-playback-app"></a>使用 Xamarin 建置 Android 播放應用程式

您可以使用下列連結找到適用於 ExoPlayer 的 Xamarin 繫結：

- [適用於 Google ExoPlayer 程式庫的 Xamarin 繫結程式庫](https://github.com/martijn00/ExoPlayerXamarin) \(英文\)
- [適用於 ExoPlayer NuGet 的 Xamarin 繫結](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/) \(英文\)

此外，請參閱下列討論：[Xamarin 繫結](https://github.com/martijn00/ExoPlayerXamarin/pull/57) \(英文\)。 

## <a name="chrome-player-apps-for-android"></a>適用於 Android 的 Chrome 播放應用程式

從 [適用于 Android v. 62 的 Chrome](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates)版本開始，支援 EME 中的持續性授權。 適用於 Android 的 Chrome 現在也支援 [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) \(英文\)。 如果您的使用者具有此版本 (或更新版本) 的 Chrome，這可讓您在 Chrome 中建立離線播放應用程式。 

此外，Google 已推出 Progressive Web App (PWA) 範例，並將它作為開放原始碼提供： 

- [原始程式碼](https://github.com/GoogleChromeLabs/sample-media-pwa) \(英文\)
- [Google 裝載的版本 ](https://biograf-155113.appspot.com/ttt/episode-2/) \(英文\) (僅適用於 Android 裝置上的 Chrome v 62 和更新版本)

如果將 Android 手機上的 Chrome 行動瀏覽器升級至 v62 (或更新版本)，並測試上述裝載的範例應用程式，線上串流和離線播放將同時皆能夠運作。

上述的開放原始碼 PWA 應用程式是以 Node.js 撰寫。 如果您想在 Ubuntu 伺服器上裝載自己的版本，請留意下列會阻礙播放的常見問題：

1. CORS 問題：範例應用程式中的範例視訊是裝載於 https://storage.googleapis.com/biograf-video-files/videos/。 Google 已為所有裝載於 Google Cloud Storage 貯體中的測試範例設定 CORS。 它們也會搭配 CORS 標頭提供，明確指定出 CORS 項目 `https://biograf-155113.appspot.com` (Google 用來裝載其範例的網域)，防止由其他網站存取。 如果您嘗試，將會看到下列 HTTP 錯誤： `Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. 憑證問題：從 Chrome v 58 開始，針對 Widevine 的 EME 將要求使用 HTTPS。 因此，您必須搭配 X509 憑證透過 HTTPS 裝載範例應用程式。 由於下列需求使得一般測試憑證沒有作用：您必須先取得符合以下最低需求的憑證：
    - Chrome 和 Firefox 都需要憑證中存在 SAN (主體別名) 設定
    - 憑證必須要有信任的 CA，自我簽署的開發憑證將不會有作用
    - 憑證必須要有符合 Web 伺服器或閘道 DNS 名稱的 CN

## <a name="faqs"></a>常見問題集

如需詳細資訊，請參閱 [Widevine 常見問題](frequently-asked-questions.md#widevine-streaming-for-android)。

## <a name="additional-notes"></a>其他注意事項

Widevine 是 Google Inc. 所提供的服務，並受到 Google Inc. 的服務條款和隱私權原則所約束。

## <a name="summary"></a>摘要

本文已討論如何針對 Android 裝置上受 Widevine 保護的 DASH 內容實作離線模式播放。  文中也回答一些和離線串流受 Widevine 保護的內容相關的常見問題。
