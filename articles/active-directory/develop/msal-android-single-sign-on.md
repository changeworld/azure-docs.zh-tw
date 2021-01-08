---
title: 如何使用 MSAL 在 Android 上啟用跨應用程式的 SSO |蔚藍
titleSuffix: Microsoft identity platform
description: 如何使用適用于 Android 的 Microsoft 驗證程式庫 (MSAL) ，以啟用跨應用程式的單一登入。
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 10/15/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: c4c98ad377100c35b0c364607bfd3803d07a95a7
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015924"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-msal"></a>如何：使用 MSAL 在 Android 上啟用跨應用程式的 SSO

單一登入 (SSO) 可讓使用者只輸入其認證一次，並讓這些認證自動跨應用程式運作。

[Microsoft 身分識別平臺](./index.yml)和 microsoft 驗證程式庫 (MSAL) 可協助您在自己的應用程式套件中啟用 SSO。 使用訊息代理程式功能和驗證器應用程式，您可以在整個裝置上擴充 SSO。

在此操作說明中，您將瞭解如何設定應用程式所使用的 Sdk，以提供 SSO 給您的客戶。

## <a name="prerequisites"></a>必要條件

此操作說明假設您知道如何：

- 使用 Azure 入口網站布建您的應用程式。 如需本主題的詳細資訊，請參閱在[Android 教學](./tutorial-v2-android.md#create-a-project)課程中建立應用程式的指示。
- 整合您的應用程式與 [適用于 Android 的 Microsoft 驗證程式庫](https://github.com/AzureAD/microsoft-authentication-library-for-android)。

## <a name="methods-for-single-sign-on"></a>單一登入的方法

使用 MSAL for Android 的應用程式有兩種方式可達成 SSO：

* 透過訊息 [代理程式應用程式](#sso-through-brokered-authentication)
* 透過 [系統瀏覽器](#sso-through-system-browser)


   建議您使用訊息代理程式應用程式來獲得類似裝置範圍的 SSO、帳戶管理和條件式存取等優點。 不過，它會要求您的使用者下載其他應用程式。

## <a name="sso-through-brokered-authentication"></a>透過代理驗證的 SSO

建議您使用其中一個 Microsoft 驗證訊息代理程式來參與 (SSO) 的全裝置單一登入，並符合組織的條件式存取原則。 與 broker 整合可提供下列優點：

- 裝置單一登入
- 條件式存取：
  - Intune 應用程式保護
  - 裝置註冊 (Workplace Join) 
  - 行動裝置管理
- 全裝置帳戶管理
  -  透過 Android AccountManager & 帳戶設定
  - 「工作帳戶」-自訂帳戶類型

在 Android 上，Microsoft 驗證訊息代理程式是包含在 [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) 和 [Intune 公司入口網站](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) 應用程式中的元件。

下圖說明您的應用程式、Microsoft 驗證程式庫 (MSAL) 和 Microsoft 驗證代理程式之間的關聯性。

![此圖顯示應用程式與 MSAL、訊息代理程式應用程式和 Android 帳戶管理員之間的關聯。](./media/brokered-auth/brokered-deployment-diagram.png)

### <a name="installing-apps-that-host-a-broker"></a>安裝裝載 broker 的應用程式

訊息代理程式裝載應用程式可以由裝置擁有者從其 app store 安裝 (通常會 Google Play 商店) 。 不過，某些 (資源) 的 Api 會受到要求裝置必須是下列條件的條件式存取原則所保護：

- 已註冊 (workplace join) 和/或
- 註冊裝置管理或
- 在 Intune 應用程式防護中註冊

如果裝置尚未安裝 broker 應用程式，MSAL 會指示使用者在應用程式嘗試以互動方式取得權杖時，立即安裝一個。 然後，應用程式必須引導使用者完成這些步驟，才能使裝置符合所需的原則。

### <a name="effects-of-installing-and-uninstalling-a-broker"></a>安裝和卸載 broker 的效果

#### <a name="when-a-broker-is-installed"></a>當訊息代理程式安裝時

當訊息代理程式安裝在裝置上時， (呼叫) 的所有後續互動式權杖要求 `acquireToken()` 都會由訊息代理程式處理，而不是在本機 MSAL。 任何先前可供 MSAL 使用的 SSO 狀態都無法供訊息代理程式使用。 如此一來，使用者將需要再次驗證，或從裝置已知的現有帳戶清單中選取帳戶。

安裝訊息代理程式不需要使用者再次登入。 只有當使用者需要解析時 `MsalUiRequiredException` ，下一個要求才會移至訊息代理程式。 `MsalUiRequiredException` 可能會因為許多原因而擲回，且需要以互動方式解決。 例如：

- 使用者變更了與其帳戶相關聯的密碼。
- 使用者的帳戶不再符合條件式存取原則。
- 使用者已撤銷其同意，讓應用程式與他們的帳戶相關聯。

**多個** 訊息代理程式-如果裝置上安裝了多個訊息代理程式，則第一個安裝的訊息代理程式一律為使用中的訊息代理程式。 只有單一訊息代理程式可以在裝置上啟用。

#### <a name="when-a-broker-is-uninstalled"></a>當代理程式卸載時

如果只有一個已安裝的代理程式裝載應用程式，且已移除，則使用者將需要再次登入。 卸載主動 broker 會從裝置移除帳戶和相關聯的權杖。

如果 Intune 公司入口網站已安裝並以作用中的訊息代理程式運作，而且也已安裝 Microsoft Authenticator，則如果已卸載 Intune 公司入口網站 (active broker) ，則使用者將需要再次登入。 一旦使用者再次登入，Microsoft Authenticator 應用程式就會變成作用中的訊息代理程式。

### <a name="integrating-with-a-broker"></a>與 broker 整合

#### <a name="generate-a-redirect-uri-for-a-broker"></a>產生 broker 的重新導向 URI

您必須註冊與 broker 相容的重新導向 URI。 訊息代理程式的重新導向 URI 應該包含應用程式的套件名稱，以及應用程式簽章的 Base64 編碼標記法。

重新導向 URI 的格式為： `msauth://<yourpackagename>/<base64urlencodedsignature>`

您可以使用 [keytool](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) ，利用應用程式的簽署金鑰來產生 Base64 編碼的簽章雜湊，然後使用 Azure 入口網站產生使用該雜湊的重新導向 URI。

Linux 和 macOS：

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows：

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

當您使用 *keytool* 產生簽章雜湊之後，請使用 Azure 入口網站來產生重新導向 URI：

1. 登入 <a href="https://portal.azure.com/" target="_blank">Azure 入口網站 <span class="docon docon-navigate-external x-hidden-focus"></span></a> ，並在 **應用程式註冊** 中選取您的 Android 應用程式。
1. 選取 [**驗證**]  >  **新增平臺**  >  **Android**。
1. 在 [ **設定您的 Android 應用程式** ] 窗格中，輸入您稍早產生的簽章 **雜湊** 和 **套件名稱**。
1. 選取 [ **設定** ] 按鈕。

Azure 入口網站會為您產生重新導向 URI，並將其顯示在 [ **Android** 設定] 窗格的 [重新 **導向 uri** ] 欄位中。

如需有關簽署應用程式的詳細資訊，請參閱 Android Studio 使用者指南中的 [簽署應用程式](https://developer.android.com/studio/publish/app-signing) 。

> [!IMPORTANT]
> 針對應用程式的實際執行版本使用您的生產簽署金鑰。

#### <a name="configure-msal-to-use-a-broker"></a>將 MSAL 設定為使用 broker

若要在您的應用程式中使用訊息代理程式，您必須證明您已設定 broker 重新導向。 例如，包含已啟用 broker 的重新導向 URI，並指出您已註冊它，方法是在您的 MSAL 設定檔中包含下列設定：

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

#### <a name="broker-related-exceptions"></a>Broker 相關的例外狀況

MSAL 與 broker 通訊的方式有兩種：

- Broker 系結服務
- Android AccountManager

MSAL 會先使用 broker 系結服務，因為呼叫此服務不需要任何 Android 許可權。 如果系結至系結的服務失敗，MSAL 將會使用 Android AccountManager API。 只有當您的應用程式已被授與許可權時，MSAL 才會這樣做 `"READ_CONTACTS"` 。

如果您收到 `MsalClientException` 錯誤碼，則有 `"BROKER_BIND_FAILURE"` 兩個選項：

- 要求使用者停用 Microsoft Authenticator 應用程式和 Intune 公司入口網站的電源優化。
- 要求使用者授與 `"READ_CONTACTS"` 許可權

### <a name="verify-broker-integration"></a>確認 broker 整合

它可能不會立即明確地清楚訊息代理程式整合是否正常運作，但您可以使用下列步驟來檢查：

1. 在您的 Android 裝置上，使用訊息代理程式完成要求。
1. 在 Android 裝置的設定中，尋找與您用來驗證的帳戶相對應的新建立帳戶。 帳戶應該是 *工作帳戶* 類型。

如果您想要重複此測試，您可以從設定中移除帳戶。

## <a name="sso-through-system-browser"></a>透過系統瀏覽器的 SSO

Android 應用程式可以選擇使用 [瀏覽器]、[系統瀏覽器] 或 [Chrome 自訂] 索引標籤來進行驗證使用者體驗。 如果應用程式未使用代理驗證，則必須使用系統瀏覽器而不是原生的 web 程式，才能達到 SSO。

### <a name="authorization-agents"></a>授權代理程式

選擇授權代理程式的特定策略是選擇性的，並表示應用程式可以自訂的其他功能。 大部分的應用程式會使用 MSAL 預設值 (請參閱 [瞭解 ANDROID MSAL 設定檔](msal-configuration.md) ，以查看各種預設) 。

MSAL 支援使用 `WebView` 或系統瀏覽器的授權。  下圖顯示其使用方式 `WebView` ，或使用 CustomTabs 或沒有 CustomTabs 的系統瀏覽器：

![MSAL 登入範例](./media/authorization-agents/sign-in-ui.jpg)

### <a name="single-sign-on-implications"></a>單一登入含意

根據預設，與 MSAL 整合的應用程式會使用系統瀏覽器的自訂索引標籤來進行授權。 不同于 Web 檢視，自訂索引標籤會與預設的系統瀏覽器共用 cookie jar，讓您可以使用 web 或其他已與自訂索引標籤整合的原生應用程式來登入。

如果應用程式使用的 `WebView` 策略沒有將 Microsoft Authenticator 或公司入口網站支援整合至其應用程式，使用者在裝置上或原生應用程式與 web 應用程式之間將不會有單一登入體驗。

如果應用程式使用 MSAL 搭配訊息代理程式（例如 Microsoft Authenticator 或 Intune 公司入口網站），則如果使用者使用其中一個應用程式進行中的登入，則使用者可以在應用程式間擁有 SSO 體驗。

### <a name="webview"></a>WebView

若要使用應用程式內的 Web 程式，請將下列程式程式碼放在傳遞給 MSAL 的應用程式設定 JSON 中：

```json
"authorization_user_agent" : "WEBVIEW"
```

使用應用程式內的 `WebView` 使用者時，使用者會直接登入應用程式。 權杖會保留在應用程式的沙箱內，而且無法在應用程式的 cookie jar 之外使用。 因此，除非應用程式與驗證器或公司入口網站整合，否則使用者不會在應用程式間擁有 SSO 體驗。

不過， `WebView` 確實提供了自訂登入 UI 外觀和風格的功能。 如需如何進行這項自訂的詳細資訊，請參閱 [Android web 檢視](https://developer.android.com/reference/android/webkit/WebView) 。

### <a name="default-browser-plus-custom-tabs"></a>預設瀏覽器和自訂索引標籤

根據預設，MSAL 會使用瀏覽器和 [自訂](https://developer.chrome.com/multidevice/android/customtabs) 索引標籤策略。 您可以 `DEFAULT` 使用自訂設定檔中的下列 JSON 設定，明確指出此策略以防止未來版本中的變更：

```json
"authorization_user_agent" : "BROWSER"
```

使用此方法透過裝置的瀏覽器提供 SSO 體驗。 MSAL 使用共用的 cookie jar，可讓其他原生應用程式或 web 應用程式在裝置上使用 MSAL 所設定的保存會話 cookie 來達成 SSO。

### <a name="browser-selection-heuristic"></a>瀏覽器選取啟發式

因為 MSAL 無法指定要在每個各式各樣的 Android 手機上使用的確切瀏覽器套件，MSAL 會執行瀏覽器選取啟發學習法，以嘗試提供最佳的跨裝置 SSO。

MSAL 主要是從套件管理員抓取預設瀏覽器，並檢查它是否在安全瀏覽器的已測試清單中。 如果沒有，MSAL 會使用瀏覽器回復，而不是從安全清單啟動另一個非預設的瀏覽器。 無論是否支援自訂索引標籤，都會選擇預設瀏覽器。 如果瀏覽器支援自訂索引標籤，MSAL 將會啟動 [自訂] 索引標籤。自訂索引標籤的外觀和操作會更接近應用程式內建 `WebView` ，並允許基本的 UI 自訂 若要深入瞭解，請參閱 [Android 的自訂](https://developer.chrome.com/multidevice/android/customtabs) 索引標籤。

如果裝置上沒有任何瀏覽器套件，MSAL 會使用應用程式內 `WebView` 。 如果裝置預設設定未變更，則應該為每個登入啟動相同的瀏覽器，以確保 SSO 體驗。

#### <a name="tested-browsers"></a>測試的瀏覽器

下列瀏覽器已經過測試，可確認它們是否正確地重新導向至 `"redirect_uri"` 設定檔中指定的：

| 裝置 | 內建瀏覽器 | Chrome | Opera  | Microsoft Edge | UC 瀏覽器 | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
|  (API 17) 的第4結點 | 傳遞 | 傳遞 |不適用 |不適用 |不適用 |不適用 |
| Samsung S7 (API 25)  | pass<sup>1</sup> | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |
| Huawei (API 26)  |pass<sup>2</sup> | 傳遞 | 失敗 | 傳遞 | 傳遞 |傳遞 |
| Vivo (API 26)  |傳遞|傳遞|傳遞|傳遞|傳遞|失敗|
| 圖元 2 (API 26)  |傳遞 | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |
| Oppo | 傳遞 | 不適用<sup>3</sup>|不適用  |不適用 |不適用 | 不適用|
| OnePlus (API 25)  |傳遞 | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |
|  (API 28) 的結點 |傳遞 | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |
|MI | 傳遞 | 傳遞 | 傳遞 | 傳遞 | 失敗 |傳遞 |

<sup>1</sup>Samsung 的內建瀏覽器是 Samsung Internet。<br/>
<sup>2</sup>Huawei 的內建瀏覽器是 Huawei 瀏覽器。<br/>
<sup>3</sup>無法變更 Oppo 裝置設定內的預設瀏覽器。

## <a name="next-steps"></a>後續步驟

[適用于 android 裝置的共用裝置模式](msal-android-shared-devices.md) ，可讓您設定 android 裝置，讓多個員工輕鬆共用該裝置。