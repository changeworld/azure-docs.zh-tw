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
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: f98be2ef6a82c099425655fd7e5d25a4358844bf
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115436"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>適用于 Apple 裝置的 Microsoft 企業 SSO 外掛程式 (預覽) 

> [!NOTE]
> 這項功能處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

*適用于 apple 裝置的 Microsoft 企業 SSO 外掛程式*會提供單一登入 (SSO) ，以在支援 Apple 的「[企業單一登入](https://developer.apple.com/documentation/authenticationservices)」功能的所有應用程式中 Azure Active Directory (Azure AD) 帳戶。 Microsoft 與 Apple 密切合作來開發此外掛程式，以提高應用程式的可用性，同時提供 Apple 和 Microsoft 可以提供的最佳保護。

在此公開預覽版本中，企業 SSO 外掛程式僅適用于 iOS 裝置，並會散發在特定 Microsoft 應用程式中。

第一次使用企業 SSO 外掛程式是使用新的「[共用裝置模式](msal-ios-shared-devices.md)」功能。

## <a name="features"></a>特性

適用于 Apple 裝置的 Microsoft 企業 SSO 外掛程式提供下列優點：

- 為所有支援 Apple 的「企業單一登入」功能的應用程式提供 Azure AD 帳戶的 SSO。
- 會在 Microsoft Authenticator 中自動傳遞，並可由任何行動裝置管理 (MDM) 解決方案啟用。

## <a name="requirements"></a>規格需求

若要使用適用于 Apple 裝置的 Microsoft 企業 SSO 外掛程式：

- 裝置上必須安裝 iOS 13.0 或更新版本。
- 為 Apple 裝置提供 Microsoft 企業 SSO 外掛程式的 Microsoft 應用程式必須安裝在裝置上。 在公開預覽中，這些應用程式包括[Microsoft Authenticator 應用](../user-help/user-help-auth-app-overview.md)程式。
- 裝置必須是已註冊 MDM 的 (例如，具有 Microsoft Intune) 。
- 設定必須推送至裝置，才能在裝置上啟用適用于 Apple 裝置的 Microsoft 企業 SSO 外掛程式。 Apple 需要此安全性條件約束。

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>啟用 SSO 擴充功能與行動裝置管理 (MDM) 

若要啟用適用于 Apple 裝置的 Microsoft 企業 SSO 外掛程式，您的裝置必須透過 MDM 服務傳送信號。 由於 Microsoft 在[Microsoft Authenticator 應用程式](..//user-help/user-help-auth-app-overview.md)中包含企業 SSO 外掛程式，請使用您的 MDM 設定應用程式，以啟用 Microsoft 企業 SSO 外掛程式。

使用下列參數來設定適用于 Apple 裝置的 Microsoft 企業 SSO 外掛程式：

- **類型**：重新導向
- **延伸模組識別碼**：`com.microsoft.azureauthenticator.ssoextension`
- **小組識別碼**：`SGGM6D27TK`
- **Url**：
  - `https://login.microsoftonline.com`
  - `https://login.windows.net`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`

您可以使用 Microsoft Intune 作為 MDM 服務，以輕鬆設定 Microsoft 企業 SSO 外掛程式。 如需詳細資訊，請參閱[Intune 設定檔](/intune/configuration/ios-device-features-settings)。

## <a name="using-the-sso-extension-in-your-application"></a>在您的應用程式中使用 SSO 擴充功能

適用于 Apple 裝置版本1.1.0 和更新版本的[Microsoft Authentication Library (MSAL) ](https://github.com/AzureAD/microsoft-authentication-library-for-objc)支援 apple 裝置適用的 microsoft 企業 SSO 外掛程式。

如果您想要支援適用于 Apple 裝置的 Microsoft 企業 SSO 外掛程式所提供的共用裝置模式，請確定您的應用程式使用所指定的最低必要版本 MSAL。

## <a name="next-steps"></a>後續步驟

如需 iOS 上共用裝置模式的詳細資訊，請參閱[ios 裝置的共用裝置模式](msal-ios-shared-devices.md)。