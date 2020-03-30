---
title: 授權代理以及如何啟用它們 |蔚藍
description: 瞭解 Microsoft 身份驗證庫 （MSAL） 允許您的 Android 應用使用的不同授權代理以及如何啟用它們。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085325"
---
# <a name="authorization-agents-android"></a>授權代理程式 (Android)

本文介紹了 Microsoft 身份驗證庫 （MSAL） 允許應用使用的不同授權代理以及如何啟用它們。

為授權代理選擇特定策略是可選的，表示應用可以自訂的其他功能。 大多數應用將使用 MSAL 預設值（請參閱[瞭解 Android MSAL 設定檔](msal-configuration.md)以查看各種預設值）。

MSAL 支援使用`WebView`或 系統瀏覽器進行授權。  下圖顯示了使用 使用`WebView`的 或帶有自訂選項卡的系統瀏覽器或沒有自訂選項卡的系統瀏覽器的外觀：

![MSAL 登錄示例](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>單一登入影響

預設情況下，與 MSAL 集成的應用程式使用系統瀏覽器的自訂選項卡進行授權。 與 WebViews 不同，自訂選項卡與預設系統瀏覽器共用一個 Cookie jar，從而與已與自訂選項卡集成的 Web 或其他本機應用共用較少的登錄。

如果應用程式在不將`WebView`Microsoft 身份驗證器或公司門戶支援集成到其應用中的情況下使用策略，則使用者在整個設備或本機應用和 Web 應用之間將不會具有單一登入 （SSO） 體驗。

如果應用程式使用 MSAL 與 Microsoft 身份驗證器或公司門戶支援，則如果使用者對其中一個應用程式具有活動登錄，則使用者可以跨應用程式獲得 SSO 體驗。

## <a name="webview"></a>WebView

要使用應用內 WebView，請使用傳遞給 MSAL 的應用配置 JSON 中的以下行：

```json
"authorization_user_agent" : "WEBVIEW"
```

使用應用`WebView`內時，使用者直接登錄到應用。 權杖保存在應用的沙箱中，在應用的 Cookie jar 外部不可用。 因此，除非應用程式與身份驗證器或公司門戶集成，否則使用者無法跨應用程式獲得 SSO 體驗。

但是，`WebView`確實提供了自訂登錄 UI 的外觀的功能。 有關如何執行此操作自訂的更多，請參閱[Android WebViews。](https://developer.android.com/reference/android/webkit/WebView)

## <a name="default-browser-plus-custom-tabs"></a>預設瀏覽器加上自訂選項卡

預設情況下，MSAL 使用瀏覽器和[自訂選項卡](https://developer.chrome.com/multidevice/android/customtabs)策略。 通過在自訂設定檔中使用以下 JSON 配置，可以顯式指示`DEFAULT`此策略，以防止將來版本中的更改：

```json
"authorization_user_agent" : "BROWSER"
```

使用此方法通過設備的瀏覽器提供 SSO 體驗。 MSAL 使用共用 Cookie jar，它允許其他本機應用或 Web 應用使用 MSAL 設置的持久性會話 Cookie 在設備上實現 SSO。

## <a name="browser-selection-heuristic"></a>瀏覽器選擇啟發式

由於 MSAL 不可能指定在每個廣泛的 Android 手機上使用的確切瀏覽器包，因此 MSAL 實現了瀏覽器選擇啟發式，試圖提供最佳的跨設備 SSO。

MSAL 檢索設備上安裝的瀏覽器的完整清單，以選擇要使用的瀏覽器。 該清單按包管理器返回的順序排列，該順序間接反映使用者的首選項。 例如，預設瀏覽器（如果已設置）是清單中的第一個條目。 無論清單中_的第一個_瀏覽器是否支援自訂選項卡，都將選擇該瀏覽器。 如果瀏覽器支援自訂選項卡，MSAL 將啟動自訂選項卡。 自訂選項卡的外觀和感覺更接近應用`WebView`內，並允許基本的 UI 自訂。 有關詳細資訊[，請參閱 Android 中的自訂選項卡](https://developer.chrome.com/multidevice/android/customtabs)。

如果設備上沒有瀏覽器包，MSAL 將使用應用`WebView`內 。

瀏覽器清單中的瀏覽器順序由作業系統決定。 這是從最首選到最少的順序。 如果未更改設備預設設置，則應為每個登錄啟動相同的瀏覽器，以確保 SSO 體驗。

> [!NOTE]
> 如果另一個瀏覽器設置為預設瀏覽器，MSAL 不再始終喜歡 Chrome。 例如，在已預先安裝 Chrome 瀏覽器和其他瀏覽器的設備上，MSAL 將使用使用者設置為預設的瀏覽器。

### <a name="tested-browsers"></a>測試的瀏覽器

已測試以下瀏覽器以查看它們是否正確重定向到設定檔中`"redirect_uri"`指定的瀏覽器：

| | 內置瀏覽器 | Chrome | Opera  | Microsoft Edge | UC 瀏覽器 | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| 連結 4 （API 17） | 傳遞 | 傳遞 |不適用 |不適用 |不適用 |不適用 |
| 三星S7 （API 25） | 通行證* | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |
| 華為（API 26） |通過* | 傳遞 | 失敗 | 傳遞 | 傳遞 |傳遞 |
| 維沃 （API 26） |傳遞|傳遞|傳遞|傳遞|傳遞|失敗|
| 圖元 2 （API 26） |傳遞 | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |
| 奧波 | 傳遞 | 不適用* |不適用  |不適用 |不適用 | 不適用|
| 一加 （API 25） |傳遞 | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |
| 連結 （API 28） |傳遞 | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |
|MI | 傳遞 | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |

*三星的內置瀏覽器是三星互聯網。  
*華為的內置瀏覽器是華為瀏覽器。  
無法在 Oppo 設備設置中更改預設瀏覽器。
