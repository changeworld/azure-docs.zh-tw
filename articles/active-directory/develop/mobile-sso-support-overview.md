---
title: 在您開發的行動裝置應用程式中支援單一登入和應用程式保護原則 |蔚藍
titleSuffix: Microsoft identity platform
description: 使用 Microsoft 身分識別平臺建立支援單一登入和應用程式保護原則的行動應用程式，以及與 Azure Active Directory 整合的說明和總覽。
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2020
ms.author: nichola
ms.openlocfilehash: 9c53ea5bad88eb79940274c409218045c06d7981
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064963"
---
# <a name="support-single-sign-on-and-app-protection-policies-in-mobile-apps-you-develop"></a>在您開發的 mobile apps 中支援單一登入和應用程式保護原則

單一登入 (SSO) 是 Microsoft 身分識別平臺和 Azure Active Directory 的重要供應專案，可為您的應用程式使用者提供簡單且安全的登入。 此外，應用程式保護原則 (應用程式) 支援可保護使用者資料安全的金鑰安全性原則。 這些功能一起可讓使用者登入和管理您的應用程式資料。

本文說明 SSO 和應用程式為何很重要，並提供建立支援這些功能之行動應用程式的高階指引。 這適用于手機和平板電腦應用程式。 如果您是 IT 系統管理員，想要在組織的 Azure Active Directory 租使用者中部署 SSO，請參閱我們 [的指引，以規劃單一登入部署](../manage-apps/plan-sso-deployment.md)

## <a name="about-single-sign-on-and-app-protection-policies"></a>關於單一登入和應用程式保護原則

[單一登入 (SSO) ](../manage-apps/plan-sso-deployment.md) 可讓使用者登入一次，並取得其他應用程式的存取權，而不需要重新輸入認證。 這可讓您更輕鬆地存取應用程式，而不需要使用者記住長的使用者名稱和密碼清單。 在您的應用程式中加以執行，可讓您更輕鬆地存取和使用應用程式。

此外，在您的應用程式中啟用單一登入，可將新式驗證（例如 [無密碼](../authentication/concept-authentication-passwordless.md)登入）隨附的新驗證機制解除鎖定。 使用者名稱和密碼是針對應用程式最受歡迎的攻擊媒介之一，而啟用 SSO 可讓您藉由強制執行條件式存取或無密碼登入，以新增額外的安全性或依賴更安全的驗證機制，來降低這項風險。 最後，啟用單一登入也會啟用 [單一登出](v2-protocols-oidc.md#single-sign-out)。這在將用於共用裝置的工作應用程式之類的情況下很有用。

[應用程式保護原則 (應用程式) ](/mem/intune/apps/app-protection-policy) 確保組織的資料保持安全且包含在其中。 它們可讓公司管理及保護應用程式內的資料，並可讓您控制誰可以存取應用程式和其資料。 執行應用程式保護原則可讓您的應用程式將使用者連接到受條件式存取原則保護的資源，並安全地將資料傳輸到其他受保護的應用程式。 應用程式保護原則解除鎖定的案例包括要求 PIN 以開啟應用程式、控制應用程式之間的資料共用，以及防止將公司應用程式資料儲存到個人儲存位置。

## <a name="implementing-single-sign-on"></a>執行單一登入

建議您採用下列各項，讓您的應用程式能夠利用單一登入。

### <a name="use-the-microsoft-authentication-library-msal"></a>使用 Microsoft 驗證程式庫 (MSAL) 

在您的應用程式中執行單一登入的最佳選擇是使用 [Microsoft 驗證程式庫 (MSAL) ](msal-overview.md)。 藉由使用 MSAL，您可以使用最少量的程式碼和 API 呼叫，將驗證新增至您的應用程式、取得 [Microsoft 身分識別平臺](./index.yml)的完整功能，並讓 Microsoft 處理安全驗證解決方案的維護。 根據預設，MSAL 會為您的應用程式新增 SSO 支援。 此外，如果您也計畫要執行應用程式保護原則，則需要使用 MSAL。

> [!NOTE]
> 您可以設定 MSAL 使用內嵌的 web view。 這將會防止單一登入。 使用預設行為 (也就是系統網頁瀏覽器) ，以確保 SSO 可正常運作。

如果您目前是在應用程式中使用 [ADAL 程式庫](../azuread-dev/active-directory-authentication-libraries.md) ，則強烈建議您將 [它遷移至 MSAL](msal-migration.md)，因為 [adal 即將被取代](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)。

針對 iOS 應用程式，我們有一個 [快速入門](quickstart-v2-ios.md) ，說明如何使用 MSAL 來設定登入，以及如何 [針對各種 SSO 案例設定 MSAL 的指引](single-sign-on-macos-ios.md)。

針對 Android 應用程式，我們有一個 [快速入門](quickstart-v2-android.md) ，說明如何使用 MSAL 來設定登入，以及 [如何使用 MSAL 在 Android 上啟用跨應用程式的 SSO](msal-android-single-sign-on.md)的指引。

### <a name="use-the-system-web-browser"></a>使用系統網頁瀏覽器

互動式驗證需要網頁瀏覽器。 如果行動應用程式使用 MSAL 以外的新式驗證程式庫 (也就是其他 OpenID Connect 或 SAML 程式庫) ，或如果您要執行自己的驗證程式代碼，您應該使用系統瀏覽器作為驗證介面，以啟用 SSO。

Google 有在 Android 應用程式中執行這項操作的指引： [Chrome 自訂索引標籤-Google Chrome](https://developer.chrome.com/multidevice/android/customtabs)。

Apple 具有在 iOS 應用程式中進行這項操作的指引： [透過 Web 服務驗證使用者 |Apple 開發人員檔](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service)。

> [!TIP]
> [Apple 裝置的 sso 外掛程式](apple-sso-plugin.md)可讓 iOS 應用程式使用 sso，以使用 Intune 在受管理裝置上使用內嵌的 web 流覽。 建議 MSAL 和系統瀏覽器做為開發應用程式的最佳選項，以啟用所有使用者的 SSO，但在某些情況下，這會允許 SSO。

## <a name="enable-app-protection-policies"></a>啟用應用程式保護原則

若要啟用應用程式保護原則，請使用 [Microsoft 驗證程式庫 (MSAL) ](msal-overview.md)。 MSAL 是 Microsoft 身分識別平臺的驗證和授權程式庫，而 Intune SDK 的開發目的是與它一起運作。

此外，您必須使用訊息代理程式應用程式進行驗證。 訊息代理程式會要求應用程式提供應用程式和裝置資訊，以確保應用程式合規性。 iOS 使用者將使用 [Microsoft Authenticator 應用](../user-help/user-help-auth-app-sign-in.md) 程式，而 Android 使用者將使用 Microsoft Authenticator 應用程式或 [公司入口網站應用程式](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) 進行代理 [驗證](./msal-android-single-sign-on.md)。 根據預設，MSAL 會使用 broker 做為完成驗證要求的第一個選擇，因此使用現成的 MSAL 時，將會自動為您的應用程式啟用使用訊息代理程式驗證。

最後， [將 INTUNE SDK 新增](/mem/intune/developer/app-sdk-get-started) 至您的應用程式，以啟用應用程式保護原則。 大部分的 SDK 都會遵循攔截模型，並且會自動套用應用程式保護原則，以判斷是否允許應用程式所採取的動作。 您也可以手動呼叫 Api 來告訴應用程式是否有某些動作的限制。

## <a name="additional-resources"></a>其他資源

- [規劃 Azure Active Directory 單一登入部署](../manage-apps/plan-sso-deployment.md)
- [如何：在 macOS 和 iOS 上設定 SSO](single-sign-on-macos-ios.md)
- [適用于 Apple 裝置的 Microsoft 企業 SSO 外掛程式 (Preview) ](apple-sso-plugin.md)
- [Android 中的代理驗證](./msal-android-single-sign-on.md)
- [授權代理程式及其啟用方式](./msal-android-single-sign-on.md)
- [開始使用 Microsoft Intune App SDK](/mem/intune/developer/app-sdk-get-started)
- [設定 Intune App SDK 的設定](/mem/intune/developer/app-sdk-ios#configure-settings-for-the-intune-app-sdk)
- [受 Microsoft Intune 保護的應用程式](/mem/intune/apps/apps-supported-intune-apps)