---
title: Azure 媒體播放器錯誤代碼
description: Azure 媒體播放器的錯誤代碼引用。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 13d804ec39c3d7753d4ee04962a88d4451fb04d9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727251"
---
# <a name="error-codes"></a>錯誤碼 #

當播放無法啟動或停止時,將觸發錯誤事件,`error()`該函數將返回代碼和可選訊息,以説明應用開發人員獲取更多詳細資訊。 `error().message`不是向用戶顯示的消息。  向用戶顯示的消息基於`error().code`位 27-20,請參閱下表。

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>錯誤代碼,位元 [31-28] (4 位元) ##

描述錯誤的區域。

- 0 - 不明
- 1 - 安培
- 2 - AzureHtml5JS
- 3 - 閃光
- 4 - 銀光
- 5 - Html5
- 6 - Html5 公平播放

## <a name="error-codes-bits-27-0-28-bits"></a>錯誤代碼,位元 [27-0] (28 位元) ##

描述錯誤的詳細資訊,位 27-20 提供了高級別,位 19-0 提供了更多詳細資訊(如果可用)。


| 和錯誤代碼。[名稱] | 代碼,位元 [27-0] (28 位元) | 描述 |
|---|---:|---|
| **MEDIA_ERR_ABORTED誤差範圍 (0x0100000 - 0x01FFFFF)** | | |
| 中止的未知數 | 0x010000 | 一般中止錯誤 |
| 中止執行 | 0x0100001 | 取消錯誤,未實作 |
| 中止的ErHTTP混合內容被阻止 | 0x010002 | 中止錯誤,混合內容被阻止 - 通常`http://``https://`發生在從 頁面載入流時 |
| **MEDIA_ERR_NETWORK錯誤起始值 (0x0200000 - 0x02FFF)** | | |
| 網路Err未知 | 0x020000 | 通用網路錯誤 |
| 網路勘誤巴德爾格式 | 0x0200190 | HTTP 400 錯誤回應 |
| 網路ErrHttpUserAuth需要 | 0x0200191 | HTTP 401 錯誤回應 |
| 網路ErHTTPuser禁止 | 0x0200193 | HTTP 403 錯誤回應 |
| 網路ErHTTP網址未發現 | 0x0200194 | HTTP 404 錯誤回應 |
| 網路ErrHTTPNot | 0x0200195 | HTTP 405 錯誤回應 |
| 網路埃爾HTTPGone | 0x020019A | HTTP 410 錯誤回應 |
| 網路錯誤預置失敗 | 0x020019C | HTTP 412 錯誤回應 |
| 網路ErrHTTP內部伺服器失敗 | 0x02001F4 | HTTP 500 錯誤回應
| 網路勘誤巴閘道 | 0x02001F6 | HTTP 502 錯誤回應 |
| 網路ErrHttp服務無法用 | 0x02001F7 | HTTP 503 錯誤回應 |
| 網路勘誤閘道超時 | 0x02001F8 | HTTP 504 錯誤回應 |
| 網路勘誤超時 | 0x0200258 | 網路逾時錯誤
| 網路ErrErr | 0x0200259 | 網路連線錯誤回應 |
| **MEDIA_ERR_DECODE錯誤 (0x0300000 - 0x03FFF)** | | |
| 解碼未知 | 0x030000 | 通用解碼錯誤 |
| **MEDIA_ERR_SRC_NOT_SUPPORTED錯誤 (0x0400000 - 0x04FFFFF)** | | |
| srcErr 未知 | 0x040000 | 不支援的一般源錯誤 |
| srcErrParse 範例 | 0x0400001 | 簡報分析錯誤 |
| srcErrParse 分段 | 0x040002 | 段剖析錯誤 |
| srcErrUn 支援簡報文稿 | 0x0400003 | 不支援簡報文件 |
| srcErr 不合法 | 0x0400004 | 不合法 |
| **MEDIA_ERR_ENCRYPTED錯誤起始值(0x0500000 - 0x05FFF)** | | |
| 加密錯誤未知 | 0x050000 | 通用加密錯誤 | 
| 加密Er解密未發現 | 0x0500001 | 找不到解密器 |
| 加密的解密器 | 0x050002 | 解密器初始化錯誤 |
| 加密的解密器不支援 | 0x0500003 | 不支援解密程式 |
| 加密ErKeyAcquire | 0x0500004 | 金鑰抓取失敗 |
| 加密的解密 | 0x0500005 | 段解密失敗 |
| 加密的埃爾許可證取得 | 0x050006 | 授權取得失敗 |
| **SRC_PLAYER_MISMATCH錯誤起始值(0x0600000 - 0x06FFFFF)** | | |
| srcPlayer身份不符未知 | 0x060000 | 一般沒有符合的技術播放器 |
| srcPlayer不相儲存記憶體未安裝 | 0x0600001 |未安裝快閃記憶體外掛程式,如果安裝源可能會播放。 *或*快閃記憶體 30 已安裝並播放 AES 內容。  如果是這種情況,請嘗試其他瀏覽器。 截至 6 月 7 日,快閃記憶體 30 今天不受支援。 有關詳細資訊,請參閱[已知問題](azure-media-player-known-issues.md)。 注意:如果 0x00600003,如果技術訂單中指定,則不會安裝快閃記憶體和銀光。|
| srcPlayer不相比銀燈未安裝 | 0x060002 | 銀光外掛程式未安裝,如果安裝源可能會播放。 注意:如果 0x00600003,如果技術訂單中指定,則不會安裝快閃記憶體和銀光。 |
| | 0x00600003 | 如果技術訂單中指定,則不會安裝閃光燈和銀光。 |
| **未知的錯誤 (0x0FF0000)** | | |
| 錯誤未知 | 0xFF00000 | 未知的錯誤 |

## <a name="user-error-messages"></a>使用者錯誤訊息 ##

顯示的使用者消息基於錯誤代碼的位 27-20。

- MEDIA_ERR_ABORTED (1) - "您中止了視頻播放"
- MEDIA_ERR_NETWORK (2) - 「網路錯誤導致視頻下載部分失敗。
- MEDIA_ERR_DECODE (3) - 「視頻播放由於損壞問題或瀏覽器不支援的視頻功能而中止。
- MEDIA_ERR_SRC_NOT_SUPPORTED (4) - "無法載入影片,因為伺服器或網路出現故障,或者不支援格式。
- MEDIA_ERR_ENCRYPTED (5) - 「視頻已加密,我們沒有解密的密鑰。
- SRC_PLAYER_MISMATCH (6) - "未找到此視頻的相容源。
- MEDIA_ERR_UNKNOWN (0xFF) - "發生未知錯誤"

## <a name="examples"></a>範例 ##

### <a name="0x10600001"></a>0x10600001 ##

此視頻未找到相容源。 顯示給最終使用者。

沒有技術播放機可以播放請求的源,但如果安裝了 Flash 外掛程式,則很可能可以播放源。  

### <a name="0x20200194"></a>0x20200194 ###

網路錯誤導致視頻下載部分失敗。 顯示給最終使用者。

AzureHtml5JS 無法從 HTTP 404 回應中播放。

### <a name="categorizing-errors"></a>類別錯誤 ###

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

### <a name="catching-a-specific-error"></a>捕捉錯誤 ###

以下代碼僅捕捉 404 個錯誤:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)