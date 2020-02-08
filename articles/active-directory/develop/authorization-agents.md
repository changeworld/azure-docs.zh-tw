---
title: 授權代理程式和如何啟用它們 |Azure
description: 瞭解不同的授權代理程式 Microsoft 驗證程式庫（MSAL）可讓您的 Android app 使用，以及如何加以啟用。
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
ms.openlocfilehash: 4f1b3fc5b60069cfa47d437e4341ded141204418
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085325"
---
# <a name="authorization-agents-android"></a>授權代理程式 (Android)

本文說明 Microsoft 驗證程式庫（MSAL）允許您的應用程式使用的不同授權代理程式，以及如何加以啟用。

選擇授權代理程式的特定策略是選擇性的，而且代表可自訂的其他功能。 大部分的應用程式會使用 MSAL 預設值（請參閱[瞭解 ANDROID MSAL 設定檔](msal-configuration.md)以查看各種預設值）。

MSAL 支援使用 `WebView`或系統瀏覽器的授權。  下圖顯示如何使用 `WebView`或具有 CustomTabs 或不含 CustomTabs 的系統瀏覽器：

![MSAL 登入範例](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>單一登入的含意

根據預設，與 MSAL 整合的應用程式會使用系統瀏覽器的自訂索引標籤來進行授權。 不同于網站，自訂索引標籤會與預設的系統瀏覽器共用 cookie jar，讓使用 web 或其他與自訂索引標籤整合的原生應用程式更少登入。

如果應用程式使用 `WebView` 策略，而不將 Microsoft Authenticator 或公司入口網站支援整合到其應用程式中，則使用者在裝置上或原生應用程式與 web 應用程式之間不會有單一登入（SSO）體驗。

如果應用程式使用具有 Microsoft Authenticator 或公司入口網站支援的 MSAL，則使用者若有使用其中一個應用程式的作用中登入，就可以在應用程式之間擁有單一登入體驗。

## <a name="webview"></a>WebView

若要使用應用程式內建的 Web 工作，請將下列這一行放在傳遞給 MSAL 的應用程式設定 JSON 中：

```json
"authorization_user_agent" : "WEBVIEW"
```

使用應用程式內 `WebView`時，使用者會直接登入應用程式。 權杖會保留在應用程式的沙箱中，而且無法在應用程式的 cookie jar 外使用。 因此，除非應用程式與驗證器或公司入口網站整合，否則使用者不能在應用程式之間擁有 SSO 體驗。

不過，`WebView` 確實提供了自訂登入 UI 外觀與風格的功能。 如需如何進行這項自訂的詳細資訊，請參閱[Android 網站](https://developer.android.com/reference/android/webkit/WebView)。

## <a name="default-browser-plus-custom-tabs"></a>預設瀏覽器加上自訂索引標籤

根據預設，MSAL 會使用瀏覽器和[自訂](https://developer.chrome.com/multidevice/android/customtabs)索引標籤策略。 您可以使用自訂設定檔中的下列 JSON 設定，明確指出此策略，以防止未來版本中的變更 `DEFAULT`：

```json
"authorization_user_agent" : "BROWSER"
```

使用此方法，透過裝置的瀏覽器提供 SSO 體驗。 MSAL 會使用共用的 cookie jar，讓其他原生應用程式或 web 應用程式使用 MSAL 所設定的保存會話 cookie，在裝置上達成 SSO。

## <a name="browser-selection-heuristic"></a>瀏覽器選取啟發學習法

由於 MSAL 無法指定要在每個廣泛的 Android 手機陣列上使用的確切瀏覽器套件，MSAL 會執行一個瀏覽器選取啟發學習法，以嘗試提供最佳的跨裝置 SSO。

MSAL 會抓取裝置上安裝的完整瀏覽器清單，以選取要使用的瀏覽器。 此清單會依照封裝管理員傳回的順序，間接反映使用者的喜好設定。 例如，如果設定了預設瀏覽器，就是清單中的第一個專案。 系統會挑選清單中的_第一個_瀏覽器，不論它是否支援自訂索引標籤。 如果瀏覽器支援自訂索引標籤，MSAL 將會啟動 [自訂] 索引標籤。自訂索引標籤可讓您更接近應用程式內 `WebView`，並允許基本 UI 自訂。 若要深入瞭解，請參閱[Android 中的自訂](https://developer.chrome.com/multidevice/android/customtabs)索引標籤。

如果裝置上沒有瀏覽器套件，MSAL 會使用應用程式內 `WebView`。

瀏覽器清單中的瀏覽器順序是由作業系統決定。 其順序是從最慣用到最少。 如果裝置預設設定未變更，則應針對每個登入啟動相同的瀏覽器，以確保 SSO 體驗。

> [!NOTE]
> 如果另一個瀏覽器設為預設值，MSAL 就不一定會優先于 Chrome。 例如，在已預先安裝 Chrome 和另一個瀏覽器的裝置上，MSAL 會使用使用者已設定為預設值的瀏覽器。

### <a name="tested-browsers"></a>測試的瀏覽器

下列瀏覽器已經過測試，可以查看是否正確地重新導向至設定檔中所指定的 `"redirect_uri"`：

| | 內建瀏覽器 | Chrome | Opera  | Microsoft Edge | UC 瀏覽器 | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| 結點4（API 17） | 傳遞 | 傳遞 |不適用 |不適用 |不適用 |不適用 |
| Samsung S7 （API 25） | 密碼 | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |
| Huawei （API 26） |pass * * | 傳遞 | 失敗 | 傳遞 | 傳遞 |傳遞 |
| Vivo （API 26） |傳遞|傳遞|傳遞|傳遞|傳遞|失敗|
| 圖元2（API 26） |傳遞 | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |
| Oppo | 傳遞 | 不適用 * * * |不適用  |不適用 |不適用 | 不適用|
| OnePlus （API 25） |傳遞 | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |
| 結點（API 28） |傳遞 | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |
|MI | 傳遞 | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |

\* Samsung 的內建瀏覽器是 Samsung Internet。  
\* * Huawei 的內建瀏覽器為 Huawei 瀏覽器。  
在 Oppo 裝置設定內無法變更預設瀏覽器。
