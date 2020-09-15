---
title: 適用於 Apple 裝置的 Microsoft 企業單一登入外掛程式
titleSuffix: Microsoft identity platform | Azure
description: 瞭解適用于 iOS 和 macOS 裝置的 Microsoft Azure Active Directory SSO 外掛程式。
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: e43ce318ca9e9b14ad059dd296799667653e0f95
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90561341"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>適用于 Apple 裝置的 Microsoft 企業 SSO 外掛程式 (Preview) 

> [!NOTE]
> 這項功能處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

*適用于 apple 裝置的 Microsoft 企業 SSO 外掛程式*可在所有支援 Apple [ (](https://developer.apple.com/documentation/authenticationservices)功能的應用程式中，為 Azure Active Directory Azure AD) 企業單一登入的帳戶提供單一登入 (SSO) 。 Microsoft 與 Apple 密切合作來開發此外掛程式，以提高應用程式的可用性，同時提供 Apple 和 Microsoft 可提供的最佳保護。

在此公開預覽版本中，企業 SSO 外掛程式僅適用于 iOS 裝置，並在特定的 Microsoft 應用程式中散發。

## <a name="features"></a>特性

適用于 Apple 裝置的 Microsoft 企業 SSO 外掛程式提供下列優點：

- 在支援 Apple 企業單一登入功能的所有應用程式中，為 Azure AD 帳戶提供 SSO。
- 在 Microsoft Authenticator 中自動傳遞，並可由任何行動裝置管理 (MDM) 解決方案啟用。

## <a name="requirements"></a>需求

若要使用適用于 Apple 裝置的 Microsoft 企業 SSO 外掛程式：

- 裝置上必須安裝 iOS 13.0 或更新版本。
- 您必須在裝置上安裝 Microsoft 應用程式，以提供適用于 Apple 裝置的 Microsoft 企業 SSO 外掛程式。 在公開預覽中，這些應用程式包含 [Microsoft Authenticator 應用](../user-help/user-help-auth-app-overview.md)程式。
- 裝置必須是 MDM 註冊的 (例如，具有 Microsoft Intune) 。
- 必須將設定推送至裝置，才能在裝置上啟用 Apple 裝置的 Microsoft 企業 SSO 外掛程式。 Apple 需要此安全性條件約束。

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>使用 (MDM) 的行動裝置管理來啟用 SSO 外掛程式

若要啟用 Apple 裝置的 Microsoft 企業 SSO 外掛程式，您的裝置必須透過 MDM 服務傳送信號。 因為 Microsoft 在 [Microsoft Authenticator 應用程式](..//user-help/user-help-auth-app-overview.md)中包含企業 SSO 外掛程式，請使用您的 MDM 來設定應用程式，以啟用 Microsoft 企業 SSO 外掛程式。

使用下列參數設定適用于 Apple 裝置的 Microsoft 企業 SSO 外掛程式：

- **類型**：重新導向
- **延伸模組識別碼**： `com.microsoft.azureauthenticator.ssoextension`
- **小組識別碼**： (iOS) 不需要此欄位
- **Url**：
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="additional-configuration-options"></a>其他組態選項
您可以新增其他設定選項，以將 SSO 功能延伸至其他應用程式。

#### <a name="enable-sso-for-apps-that-dont-use-msal"></a>針對不使用 MSAL 的應用程式啟用 SSO

SSO 外掛程式可讓任何應用程式參與單一登入，即使它不是使用 Microsoft SDK （例如 Microsoft 驗證程式庫）來開發， (MSAL) 。

已下載 Microsoft Authenticator 應用程式並向您的組織註冊其裝置的裝置會自動安裝 SSO 外掛程式。 您的組織可能會基於多重要素驗證、無密碼驗證及條件式存取等案例，立即使用驗證器應用程式。 您可以使用任何 MDM 提供者來為您的應用程式開啟該應用程式，雖然 Microsoft 讓您能夠輕鬆地在 Intune 的 Microsoft 端點管理員內進行設定。 允許清單是用來將這些應用程式設定為使用驗證器應用程式所安裝的 SSO 外掛程式。

僅支援使用原生 Apple 網路技術或 web 檢視的應用程式。 如果應用程式有自己的網路層執行，則不支援 Microsoft 企業 SSO 外掛程式。  

使用下列參數，為不是使用 MSAL 的應用程式設定 Microsoft 企業 SSO 外掛程式：

- 機**碼**：`AppAllowList`
- **輸入**： `String`
- **值**：允許參與 SSO 之應用程式的應用程式套件組合識別碼清單（以逗號分隔）
- **範例**：`com.contoso.workapp, com.contoso.travelapp`

MDM 系統管理員允許參與 SSO 的[同意應用程式](./application-consent-experience.md)，可以無訊息方式取得終端使用者的權杖。 因此，請務必只將受信任的應用程式新增至允許清單。 

您不需要將使用 MSAL 或 ASWebAuthenticationSession 的應用程式新增至此清單。 預設會啟用這些應用程式。 

#### <a name="allow-creating-sso-session-from-any-application"></a>允許從任何應用程式建立 SSO 會話

根據預設，只有當 SSO 外掛程式已經有共用認證時，Microsoft 企業 SSO 外掛程式才會為已授權的應用程式提供 SSO。 當其他 ADAL 或 MSAL 架構的應用程式在權杖取得期間呼叫時，Microsoft 企業 SSO 外掛程式可以取得共用認證。 大部分的 Microsoft 應用程式都會使用 Microsoft Authenticator 或 SSO 外掛程式。 這表示，根據預設，原生應用程式流程外部的 SSO 是最好的工作。  

啟用 `browser_sso_interaction_enabled` 旗標可讓非 MSAL 應用程式和 Safari 瀏覽器進行初始啟動載入，並取得共用認證。 如果 Microsoft 企業 SSO 外掛程式尚無共用認證，則會在每次從 Safari 瀏覽器、ASWebAuthenticationSession、SafariViewController 或另一個允許清單的原生應用程式中的 Azure AD URL 要求登入時，嘗試取得一個認證。  

- 機**碼**：`browser_sso_interaction_enabled`
- **輸入**： `Integer`
- **值**：1或0

建議您啟用此旗標，以在所有應用程式中獲得更一致的體驗。 此選項預設為停用。 

#### <a name="disable-oauth2-application-prompts"></a>停用 OAuth2 應用程式提示

Microsoft 企業 SSO 外掛程式會將共用認證附加至來自允許應用程式的網路要求，以提供 SSO。 某些 OAuth2 應用程式可能會在通訊協定層強制執行使用者提示。 這些應用程式會略過共用認證。  

啟用 `disable_explicit_app_prompt` 旗標可限制原生和 web 應用程式在通訊協定層上強制執行使用者提示，並略過 SSO 的能力。

- 機**碼**：`disable_explicit_app_prompt`
- **輸入**： `Integer`
- **值**：1或0

建議您啟用此旗標，以在所有應用程式中獲得更一致的體驗。 此選項預設為停用。 

#### <a name="use-intune-for-simplified-configuration"></a>使用 Intune 進行簡化設定

您可以使用 Microsoft Intune 作為 MDM 服務，以簡化 Microsoft 企業 SSO 外掛程式的設定。 如需詳細資訊，請參閱 [Intune 設定檔](/intune/configuration/ios-device-features-settings)。

## <a name="using-the-sso-plug-in-in-your-application"></a>在您的應用程式中使用 SSO 外掛程式

適用于 Apple 裝置版本1.1.0 和更新版本的 [Microsoft 驗證程式庫 (MSAL) ](https://github.com/AzureAD/microsoft-authentication-library-for-objc) 支援 apple 裝置的 microsoft 企業 SSO 外掛程式。

如果您要建立 Frontline Worker 案例的應用程式，請參閱適用于 [iOS 裝置的共用裝置模式](msal-ios-shared-devices.md) ，以進行其他的功能設定。

## <a name="how-the-sso-plug-in-works"></a>SSO 外掛程式的運作方式

Microsoft 企業 SSO 外掛程式依賴 [Apple 的企業單一登入架構](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc)。 上架至架構的身分識別提供者可以攔截其網域的網路流量，並增強或變更這些要求的處理方式。 例如，SSO 外掛程式可以顯示額外的 UI，以安全地收集終端使用者認證、要求 MFA，或以無訊息方式提供權杖給應用程式。

原生應用程式也可以執行自訂作業，並直接與 SSO 外掛程式進行交談。
您可以[從 Apple 瞭解此 2019 WWDC 影片](https://developer.apple.com/videos/play/tech-talks/301/)中的單一登入架構

### <a name="applications-that-use-msal"></a>使用 MSAL 的應用程式

適用于 Apple 裝置版本1.1.0 和更高版本的 [Microsoft 驗證程式庫 (MSAL) ](https://github.com/AzureAD/microsoft-authentication-library-for-objc) ，可針對公司和學校帳戶以原生方式支援 apple 裝置的 microsoft 企業 SSO 外掛程式。 

如果您已遵循 [所有建議的步驟](./quickstart-v2-ios.md) ，並使用預設的重新 [導向 URI 格式](./redirect-uris-ios.md)，就不需要進行任何特殊設定。 在具有 SSO 外掛程式的裝置上執行時，MSAL 會自動對所有互動式和無訊息權杖要求叫用它，以及帳戶列舉和帳戶移除作業。 由於 MSAL 會執行依賴自訂作業的原生 SSO 外掛程式通訊協定，因此，此設定可為終端使用者提供最順暢的原生體驗。 

如果 MDM 未啟用 SSO 外掛程式，但裝置上有 Microsoft Authenticator 應用程式，MSAL 會改為針對任何互動式權杖要求使用 Microsoft Authenticator 應用程式。 SSO 外掛程式會與 Microsoft Authenticator 應用程式共用 SSO。

### <a name="applications-that-dont-use-msal"></a>不使用 MSAL 的應用程式

如果系統管理員明確地將這些應用程式新增到允許清單，則未使用 MSAL 的應用程式仍可取得 SSO。 

只要滿足下列條件，就不需要在這些應用程式中變更程式碼：

- 應用程式會使用 Apple 架構來執行 (的網路要求，例如 [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview)、 [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession))  
- 應用程式會使用標準通訊協定與 Azure AD (例如，OAuth2、SAML、WS-同盟) 
- 應用程式不會在原生 UI 中收集純文字使用者名稱和密碼

在此情況下，當應用程式建立網路要求並開啟網頁瀏覽器來登入使用者時，就會提供 SSO。 當使用者重新導向至 Azure AD 登入 URL 時，SSO 外掛程式會驗證 URL，並檢查該 URL 是否有可用的 SSO 認證。 如果有的話，SSO 外掛程式會將 SSO 認證傳遞給 Azure AD，這會授權應用程式完成網路要求，而不要求使用者輸入其認證。 此外，如果已知裝置 Azure AD，SSO 外掛程式也會傳遞裝置憑證來滿足以裝置為基礎的條件式存取檢查。 

為了支援非 MSAL 應用程式的 SSO，SSO 外掛程式會執行類似于「主要重新整理 [權杖](../devices/concept-primary-refresh-token.md#browser-sso-using-prt)」中所述的 Windows 瀏覽器外掛程式的通訊協定。 

相較于 MSAL 型應用程式，SSO 外掛程式可透過與應用程式提供的現有瀏覽器登入體驗整合，更明確地針對非 MSAL 的應用程式運作。 終端使用者會看到他們熟悉的體驗，而不需要在每個應用程式中執行其他登入。 例如，SSO 外掛程式不會顯示原生帳戶選擇器，而是會將 SSO 會話新增至網頁型帳戶選擇器體驗。 

## <a name="next-steps"></a>後續步驟

如需 iOS 上共用裝置模式的詳細資訊，請參閱 [適用于 ios 裝置的共用裝置模式](msal-ios-shared-devices.md)。
