---
title: 設定應用程式單一登入
description: 如何為您正在開發並向 Azure AD 註冊的自訂應用程式設定單一登入。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 62f4f629e44d317d36e182adb48f8f00b9f1c2b3
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063054"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>如何設定應用程式的單一登入

在您的應用程式中啟用同盟單一登入 (SSO)，會在針對 OpenID Connect、SAML 2.0 或 WS-Fed 透過 Azure AD 進行同盟時自動啟用。 如果您的使用者儘管目前已經有含 Azure AD 的工作階段還是必須登入，很可能是您的應用程式設定錯誤。

* 如果您使用 ADAL/MSAL，請確定已將 **PromptBehavior** 設為 **Auto**，而不是 **Always**。

* 如果您正在建置行動裝置應用程式，您可能需要其他設定來啟用代理或非代理的 SSO。

若是 Android，請參閱[在 Android 上啟用跨應用程式的 SSO](../azuread-dev/howto-v1-enable-sso-android.md)。<br>

若是 iOS，請參閱[在 iOS 上啟用跨應用程式的 SSO](../azuread-dev/howto-v1-enable-sso-ios.md)。

## <a name="next-steps"></a>後續步驟

[Azure AD SSO](../manage-apps/what-is-single-sign-on.md)<br>

[在 Android 上啟用跨應用程式的 SSO](../azuread-dev/howto-v1-enable-sso-android.md)<br>

[在 iOS 上啟用跨應用程式的 SSO](../azuread-dev/howto-v1-enable-sso-ios.md)<br>

[將應用程式整合到 AzureAD](./quickstart-register-app.md)<br>

[Microsoft 身分識別平台端點中的權限和同意](./v2-permissions-and-consent.md)<br>

[AzureAD StackOverflow (英文)](https://stackoverflow.com/questions/tagged/azure-active-directory)