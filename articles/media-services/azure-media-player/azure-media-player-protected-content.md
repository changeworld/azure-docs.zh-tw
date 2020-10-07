---
title: Azure 媒體播放器受保護內容
description: Azure 媒體播放器目前支援 AES-128 位元信封加密內容和一般加密內容。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 83f144c06c23f3ab5507e3561be4a12350e20a42
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91329681"
---
# <a name="protected-content"></a>受保護內容 #

Azure 媒體播放器目前支援 AES-128 位元信封加密內容和一般加密內容 (透過 PlayReady 和 Widevine)，或透過 FairPlay 的加密內容。 為了正確播放受保護內容，您必須讓 Azure 媒體播放器知道 `protectionInfo`。 此資訊存在於每個來源，而且可以透過 `data-setup` 直接新增至 `<source>` 標籤上。  如果將來源設定為動態，則也可以將 `protectionInfo` 直接新增為參數。

`protectionInfo` 接受 JSON 物件，並包含：

- `type`：`AES` 或 `PlayReady` 或 `Widevine` 或 `FairPlay`
- `certificateUrl`：這應該是所裝載 FairPlay 憑證的直接連結

- `authenticationToken`：這是要新增未編碼驗證權杖的選項欄位

> [!IMPORTANT]
> 只有 FairPlay DRM 才需要 **certificateUrl** 物件。***
>[!NOTE]
> 預設 techOrder 已變更為配合新的技術 - 特別是 `html5FairPlayHLS`，以在支援的瀏覽器上以原生方式播放 FairPlay 內容 (OSX 8+ 上的 Safari)。 如果您有要播放的 FairPlay 內容**而且**您已在應用程式中將預設 techOrder 變更為自訂值，則必須將這個新的技術新增到 techOrder 物件中。 建議您將其納入到 silverlightSS 之前，以免內容透過 PlayReady 來播放。

## <a name="code-sample"></a>程式碼範例 ##

```html
Ex:

    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source
            src="//example/path/to/myVideo.ism/manifest"
            type="application/vnd.ms-sstr+xml"
            data-setup='{"protectionInfo": [{"type": "AES", "authenticationToken": "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=8130520b-c116-45a9-824e-4a0082f3cb3c&Audience=urn%3atest&ExpiresOn=1450207516&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=eV7HDgZ9msp9H9bnEPGN91sBdU7XsZ9OyB6VgFhKBAU%3d"}]}'
        />
    </video>
or

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
            type: "PlayReady",
            authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
        }] }, ]
    );
```

或者，使用多個 DRM

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
                type: "PlayReady",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                type: "Widevine",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                   type: "FairPlay",
                  certificateUrl: "//example/path/to/myFairplay.der",
                   authenticationToken: "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1cm46bWljcm9zb2Z0OmF6dXJlOm1lZGlhc2VydmljZXM6Y29udGVudGtleWlkZW50aWZpZXIiOiIyMTI0M2Q2OC00Yjc4LTRlNzUtYTU5MS1jZWMzMDI0NDNhYWMiLCJpc3MiOiJodHRwOi8vY29udG9zbyIsImF1ZCI6InVybjp0ZXN0IiwiZXhwIjoxNDc0NTkyNDYzLCJuYmYiOjE0NzQ1ODg1NjN9.mE7UxgNhkieMMqtM_IiYQj-FK1KKIzB6lAptw4Mi67A"
        }] } ]
    );
```

> [!NOTE]
> 並非所有瀏覽器/平台都能夠播放受保護內容。 如需所支援項目的詳細資訊，請參閱[播放技術](azure-media-player-playback-technology.md)一節。
> [!IMPORTANT]
> 傳入播放器的權杖適用於受保護內容，且只會用於已驗證的使用者。 應用程式應該會使用 SSL 或某種形式的安全性措施。 此外，使用者也應該是值得信任的，不會濫用權杖；如果並非如此，請洽詢您的安全性專家。

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)