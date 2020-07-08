---
title: Azure 媒體播放機錯誤碼
description: Azure 媒體播放機的錯誤碼參考。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 13d804ec39c3d7753d4ee04962a88d4451fb04d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81727251"
---
# <a name="error-codes"></a>錯誤碼 #

當播放無法啟動或停止時，將會引發錯誤事件，且函式 `error()` 會傳回代碼和選擇性的訊息，以協助應用程式開發人員取得更多詳細資料。 `error().message`不是向使用者顯示的訊息。  向使用者顯示的訊息是根據 `error().code` bits 27-20，請參閱下表。

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>錯誤碼，bits [31-28] （4位） ##

描述錯誤的區域。

- 0 - 不明
- 1-AMP
- 2-AzureHtml5JS
- 3-FlashSS
- 4-SilverlightSS
- 5-Html5
- 6-Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>錯誤碼，bits [27-0] （28位） ##

描述錯誤的詳細資料，bits 27-20 提供高階，bits 19-0 則提供更多詳細資料（如果有的話）。


| amp。檔案名 | 代碼，位 [27-0] （28位） | Description |
|---|---:|---|
| **MEDIA_ERR_ABORTED 錯誤範圍（0x0100000-0x01FFFFF）** | | |
| abortedErrUnknown | 0x0100000 | 一般中止錯誤 |
| abortedErrNotImplemented | 0x0100001 | 中止錯誤，未執行 |
| abortedErrHttpMixedContentBlocked | 0x0100002 | 中止錯誤，已封鎖混合內容-通常會在 `http://` 從頁面載入資料流程 `https://` 時發生 |
| **MEDIA_ERR_NETWORK 錯誤開始值（0x0200000-0x02FFFFF）** | | |
| networkErrUnknown | 0x0200000 | 一般網路錯誤 |
| networkErrHttpBadUrlFormat | 0x0200190 | Http 400 錯誤回應 |
| networkErrHttpUserAuthRequired | 0x0200191 | Http 401 錯誤回應 |
| networkErrHttpUserForbidden | 0x0200193 | Http 403 錯誤回應 |
| networkErrHttpUrlNotFound | 0x0200194 | Http 404 錯誤回應 |
| networkErrHttpNotAllowed | 0x0200195 | Http 405 錯誤回應 |
| networkErrHttpGone | 0x020019A | Http 410 錯誤回應 |
| networkErrHttpPreconditionFailed | 0x020019C | Http 412 錯誤回應 |
| networkErrHttpInternalServerFailure | 0x02001F4 | Http 500 錯誤回應
| networkErrHttpBadGateway | 0x02001F6 | Http 502 錯誤回應 |
| networkErrHttpServiceUnavailable | 0x02001F7 | Http 503 錯誤回應 |
| networkErrHttpGatewayTimeout | 0x02001F8 | Http 504 錯誤回應 |
| networkErrTimeout | 0x0200258 | 網路逾時錯誤
| networkErrErr | 0x0200259 | 網路連接錯誤回應 |
| **MEDIA_ERR_DECODE 錯誤（0x0300000-0x03FFFFF）** | | |
| decodeErrUnknown | 0x0300000 | 一般解碼錯誤 |
| **MEDIA_ERR_SRC_NOT_SUPPORTED 錯誤（0x0400000-0x04FFFFF）** | | |
| srcErrUnknown | 0x0400000 | 不支援一般來源錯誤 |
| srcErrParsePresentation | 0x0400001 | 簡報剖析錯誤 |
| srcErrParseSegment | 0x0400002 | 區段剖析錯誤 |
| srcErrUnsupportedPresentation | 0x0400003 | 不支援簡報 |
| srcErrInvalidSegment | 0x0400004 | 不正確區段 |
| **MEDIA_ERR_ENCRYPTED 錯誤開始值（0x0500000-0x05FFFFF）** | | |
| encryptErrUnknown | 0x0500000 | 一般加密錯誤 | 
| encryptErrDecrypterNotFound | 0x0500001 | 找不到 Decrypter |
| encryptErrDecrypterInit | 0x0500002 | Decrypter 初始化錯誤 |
| encryptErrDecrypterNotSupported | 0x0500003 | 不支援 Decrypter |
| encryptErrKeyAcquire | 0x0500004 | 金鑰取得失敗 |
| encryptErrDecryption | 0x0500005 | 區段解密失敗 |
| encryptErrLicenseAcquire | 0x0500006 | 授權取得失敗 |
| **SRC_PLAYER_MISMATCH 錯誤開始值（0x0600000-0x06FFFFF）** | | |
| srcPlayerMismatchUnknown | 0x0600000 | 一般沒有符合的技術播放機來播放來源 |
| srcPlayerMismatchFlashNotInstalled | 0x0600001 |未安裝 Flash 外掛程式，如果已安裝，則會播放來源。 *或*已安裝 Flash 30 並播放 AES 內容。  如果是這種情況，請嘗試不同的瀏覽器。 今天6月7日起不支援 Flash 30。 如需詳細資訊，請參閱[已知問題](azure-media-player-known-issues.md)。 注意：如果已在 techOrder 中指定0x00600003，則不會安裝 Flash 和 Silverlight。|
| srcPlayerMismatchSilverlightNotInstalled | 0x0600002 | 未安裝 Silverlight 外掛程式，如果已安裝，則可能會播放來源。 注意：如果已在 techOrder 中指定0x00600003，則不會安裝 Flash 和 Silverlight。 |
| | 0x00600003 | 如果在 techOrder 中指定，則不會安裝 Flash 和 Silverlight。 |
| **未知的錯誤（0x0FF00000）** | | |
| errUnknown | 0xFF00000 | 未知的錯誤 |

## <a name="user-error-messages"></a>使用者錯誤訊息 ##

顯示的使用者訊息是以錯誤碼的 bits 27-20 為基礎。

- MEDIA_ERR_ABORTED （1）-「您已中止影片播放」
- MEDIA_ERR_NETWORK （2）-「網路錯誤導致影片下載失敗，無法進行部分。」
- MEDIA_ERR_DECODE （3）-「影片播放因損毀問題而中止，或因為您的瀏覽器不支援使用的影片功能」。
- MEDIA_ERR_SRC_NOT_SUPPORTED （4）-「無法載入影片，可能是因為伺服器或網路失敗，或因為格式不受支援。」
- MEDIA_ERR_ENCRYPTED （5）-「影片已加密，但我們沒有金鑰可將其解密。」
- SRC_PLAYER_MISMATCH （6）-「找不到此影片的相容來源。」
- MEDIA_ERR_UNKNOWN （0xFF）-「發生未知的錯誤。」

## <a name="examples"></a>範例 ##

### <a name="0x10600001"></a>0x10600001 ##

「在這段影片中找不到相容的來源。」 會向使用者顯示。

沒有任何技術播放機可以播放要求的來源，但如果安裝了 Flash 外掛程式，可能會播放來源。  

### <a name="0x20200194"></a>0x20200194 ###

「網路錯誤導致影片下載失敗，無法進行部分。」 會向使用者顯示。

AzureHtml5JS 無法從 HTTP 404 回應播放。

### <a name="categorizing-errors"></a>分類錯誤 ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>捕捉特定錯誤 ###

下列程式碼只會攔截404錯誤：

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)