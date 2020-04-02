---
title: 適用於 Apple 裝置的微軟企業 SSO 外掛程式
titleSuffix: Microsoft identity platform | Azure
description: 瞭解適用於 iOS 和 macOS 裝置的 Microsoft Azure 活動目錄 SSO 外掛程式。
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
ms.openlocfilehash: 7233a0bba5cf30eab018bfa744a7322303300604
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550314"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>適用於 Apple 裝置的微軟企業 SSO 外掛程式(預覽版)

> [!NOTE]
> 這項功能處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

*適用於 Apple 裝置的 Microsoft 企業 SSO 外掛程式*為支援 Apple[企業單一登入](https://developer.apple.com/documentation/authenticationservices)功能的所有應用程式的活動目錄帳戶提供單一登錄 (SSO)。 微軟與蘋果密切合作,開發此外掛程式,以提高應用程式的可用性,同時提供蘋果和微軟所能提供的最佳保護。

在此公共預覽版本中,企業 SSO 外掛程式僅適用於 iOS 設備,並在某些 Microsoft 應用程式中分發。

我們首次使用企業 SSO 外掛程式是使用我們新的[共享設備模式](msal-ios-shared-devices.md)功能。

## <a name="features"></a>特性

適用於 Apple 裝置的 Microsoft 企業 SSO 外掛程式具有以下優點:

- 為支援 Apple 的企業單一登入功能的所有應用程式為活動目錄帳戶提供 SSO。
- 在 Microsoft 身份驗證器中自動交付,並且可以通過任何行動裝置管理 (MDM) 解決方案啟用。

## <a name="requirements"></a>需求

要將 Microsoft 企業 SSO 外掛程式用於 Apple 設備,請:

- 必須在設備上安裝 iOS 13.0 或更高版本。
- 必須在設備上安裝為 Apple 設備提供 Microsoft 企業 SSO 外掛程式的 Microsoft 應用程式。 對於公共預覽,這些應用程式包括[微軟身份驗證器應用程式](../user-help/user-help-auth-app-overview.md)。
- 設備必須註冊 MDM(例如,使用 Microsoft Intune)。
- 必須將配置推送到設備,才能為設備上的 Apple 設備啟用 Microsoft 企業 SSO 外掛程式。 此安全約束是Apple要求的。

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>使用行動裝置管理 (MDM) 啟用 SSO 擴充

要為 Apple 設備啟用 Microsoft 企業 SSO 外掛程式,您的裝置需要透過 MDM 服務發送訊號。 由於 Microsoft 在[Microsoft 驗證器應用中](..//user-help/user-help-auth-app-overview.md)包含企業 SSO 外掛程式,請使用 MDM 設定應用以啟用 Microsoft 企業 SSO 外掛程式。

使用以下參數為 Apple 裝置設定 Microsoft 企業 SSO 外掛程式:

- **型態**: 重定向
- **群組代碼**:`com.microsoft.azureauthenticator.ssoextension`
- **小組代碼**:`SGGM6D27TK`
- **網址**:
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

您可以使用 Microsoft Intune 作為 MDM 服務,以簡化 Microsoft 企業 SSO 外掛程式的配置。 有關詳細資訊,請參閱[Intune 設定文件](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)。

## <a name="using-the-sso-extension-in-your-application"></a>在應用程式中使用 SSO 擴充

適用於 Apple 裝置版本 1.1.0 及更高版本的[Microsoft 身份驗證庫 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-objc)支援適用於 Apple 裝置的 Microsoft 企業 SSO 外掛程式。

如果您想支援 Microsoft 企業 SSO 外掛程式為 Apple 裝置提供的共享設備模式,請確保應用程式使用指定的最低要求版本的 MSAL。

## <a name="next-steps"></a>後續步驟

有關 iOS 上的共享裝置模式的詳細資訊,請參閱[iOS 裝置的共享裝置模式](msal-ios-shared-devices.md)。
