---
title: 授權代理程式及其啟用方式 |蔚藍
description: 瞭解 Microsoft 驗證程式庫 (MSAL) 可讓您的 Android 應用程式使用的不同授權代理程式，以及如何加以啟用。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2019
ms.author: marsma
ms.reviewer: shoatman, brianmel, hahamil
ms.custom: aaddev
ms.openlocfilehash: 83a33fa3891e01c484f298f22d67467bc54a7618
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85551980"
---
# <a name="authorization-agents-android"></a>授權代理程式 (Android)

本文說明 Microsoft 驗證程式庫 (MSAL) 可讓您的應用程式使用的不同授權代理程式，以及如何加以啟用。

選擇授權代理程式的特定策略是選擇性的，並表示應用程式可以自訂的其他功能。 大部分的應用程式會使用 MSAL 預設值 (請參閱 [瞭解 ANDROID MSAL 設定檔](msal-configuration.md) ，以查看各種預設) 。

MSAL 支援使用 `WebView` 或系統瀏覽器的授權。  下圖顯示其使用方式 `WebView` ，或使用 CustomTabs 或沒有 CustomTabs 的系統瀏覽器：

![MSAL 登入範例](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>單一登入含意

根據預設，與 MSAL 整合的應用程式會使用系統瀏覽器的自訂索引標籤來進行授權。 不同于 Web 檢視，自訂索引標籤會與預設的系統瀏覽器共用 cookie jar，讓您可以使用 web 或其他已與自訂索引標籤整合的原生應用程式來登入。

如果應用程式使用的 `WebView` 策略沒有將 Microsoft Authenticator 或公司入口網站支援整合到其應用程式中，使用者就不會在裝置上或原生應用程式與 web 應用程式之間使用單一登入 (SSO) 體驗。

如果應用程式使用 MSAL 搭配 Microsoft Authenticator 或公司入口網站支援，則使用者如果使用者具有其中一個應用程式的使用中登入，就可以在應用程式間擁有 SSO 體驗。

## <a name="webview"></a>WebView

若要使用應用程式內的 Web 程式，請將下列程式程式碼放在傳遞給 MSAL 的應用程式設定 JSON 中：

```json
"authorization_user_agent" : "WEBVIEW"
```

使用應用程式內的 `WebView` 使用者時，使用者會直接登入應用程式。 權杖會保留在應用程式的沙箱內，而且無法在應用程式的 cookie jar 之外使用。 因此，除非應用程式與驗證器或公司入口網站整合，否則使用者不會在應用程式間擁有 SSO 體驗。

不過， `WebView` 確實提供了自訂登入 UI 外觀和風格的功能。 如需如何進行這項自訂的詳細資訊，請參閱 [Android web 檢視](https://developer.android.com/reference/android/webkit/WebView) 。

## <a name="default-browser-plus-custom-tabs"></a>預設瀏覽器和自訂索引標籤

根據預設，MSAL 會使用瀏覽器和 [自訂](https://developer.chrome.com/multidevice/android/customtabs) 索引標籤策略。 您可以 `DEFAULT` 使用自訂設定檔中的下列 JSON 設定，明確指出此策略以防止未來版本中的變更：

```json
"authorization_user_agent" : "BROWSER"
```

使用此方法透過裝置的瀏覽器提供 SSO 體驗。 MSAL 使用共用的 cookie jar，可讓其他原生應用程式或 web 應用程式在裝置上使用 MSAL 所設定的保存會話 cookie 來達成 SSO。

## <a name="browser-selection-heuristic"></a>瀏覽器選取啟發式

因為 MSAL 無法指定要在每個各式各樣的 Android 手機上使用的確切瀏覽器套件，MSAL 會執行瀏覽器選取啟發學習法，以嘗試提供最佳的跨裝置 SSO。

MSAL 會抓取裝置上已安裝的瀏覽器完整清單，以選取要使用的瀏覽器。 此清單會依照套件管理員所傳回的順序，此順序會間接反映使用者的喜好設定。 例如，預設瀏覽器（如果設定的話）是清單中的第一個專案。 系統會挑選清單中的 _第一個_ 瀏覽器，不論它是否支援自訂索引標籤。 如果瀏覽器支援自訂索引標籤，MSAL 將會啟動 [自訂] 索引標籤。自訂索引標籤的外觀和操作會更接近應用程式內建 `WebView` ，並允許基本的 UI 自訂 若要深入瞭解，請參閱 [Android 的自訂](https://developer.chrome.com/multidevice/android/customtabs) 索引標籤。

如果裝置上沒有任何瀏覽器套件，MSAL 會使用應用程式內 `WebView` 。

瀏覽器清單中的瀏覽器順序是由作業系統決定。 從最慣用到最少的順序。 如果裝置預設設定未變更，則應該為每個登入啟動相同的瀏覽器，以確保 SSO 體驗。

> [!NOTE]
> 如果另一個瀏覽器設定為預設值，MSAL 就不會再優先偏好 Chrome。 例如，在已預先安裝 Chrome 和另一個瀏覽器的裝置上，MSAL 會使用使用者已設定為預設的瀏覽器。

### <a name="tested-browsers"></a>測試的瀏覽器

下列瀏覽器已經過測試，可確認它們是否正確地重新導向至 `"redirect_uri"` 設定檔中指定的：

| 裝置 | 內建瀏覽器 | Chrome | Opera  | Microsoft Edge | UC 瀏覽器 | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
|  (API 17) 的第4結點 | 傳遞 | 傳遞 |不適用 |不適用 |不適用 |不適用 |
| Samsung S7 (API 25)  | 階段 | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |
| Huawei (API 26)  |pass * * | 傳遞 | 失敗 | 傳遞 | 傳遞 |傳遞 |
| Vivo (API 26)  |傳遞|傳遞|傳遞|傳遞|傳遞|失敗|
| 圖元 2 (API 26)  |傳遞 | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |
| Oppo | 傳遞 | 不適用 * * * |不適用  |不適用 |不適用 | 不適用|
| OnePlus (API 25)  |傳遞 | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |
|  (API 28) 的結點 |傳遞 | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |
|MI | 傳遞 | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |

* Samsung 的內建瀏覽器是 Samsung 網際網路。  
* * Huawei 的內建瀏覽器是 Huawei 瀏覽器。  
無法變更 Oppo 裝置設定內的預設瀏覽器。
