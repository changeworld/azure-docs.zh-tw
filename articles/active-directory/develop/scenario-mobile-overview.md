---
title: 構建調用 Web API 的移動應用 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何構建調用 Web API 的移動應用（概述）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6675d67299091325fcc3e12572a906716bf5b88d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132426"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>方案：調用 Web API 的移動應用程式

瞭解如何構建調用 Web API 的移動應用。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>開始使用

創建第一個移動應用程式並嘗試快速入門。

> [!div class="nextstepaction"]
> [快速入門：獲取權杖並從 Android 應用調用 Microsoft 圖形 API](./quickstart-v2-android.md)
>
> [快速入門：獲取權杖並從 iOS 應用調用 Microsoft 圖形 API](./quickstart-v2-ios.md)
>
> [快速入門：獲取權杖，並從 Xamarin iOS 和 Android 應用程式調用 Microsoft 圖形 API](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>總覽

個人化、無縫的使用者體驗對於移動應用至關重要。  Microsoft 身份平臺使移動開發人員能夠為 iOS 和 Android 使用者創建該體驗。 您的應用程式可以登錄 Azure 活動目錄 （Azure AD） 使用者、個人 Microsoft 帳戶使用者和 Azure AD B2C 使用者。 它還可以獲取權杖來代表他們調用 Web API。 為了實現這些流，我們將使用 Microsoft 身份驗證庫 （MSAL）。 MSAL 實現行業標準[OAuth2.0 授權代碼流](v2-oauth2-auth-code-flow.md)。

![精靈應用程式](./media/scenarios/mobile-app.svg)

移動應用的注意事項：

- **使用者體驗是關鍵**：允許使用者在請求登錄之前查看應用的價值。 僅請求所需的許可權。
- **支援所有使用者配置**：許多移動企業用戶必須遵守條件訪問策略和設備合規性策略。 請務必支援這些關鍵方案。
- **實現單一登入 （SSO）：** 通過使用 MSAL 和 Microsoft 標識平臺，您可以通過設備的瀏覽器或 Microsoft 身份驗證器（Android 上的 Intune 公司門戶）啟用單一登入。

## <a name="specifics"></a>細節

在 Microsoft 標識平臺上構建移動應用時，請記住以下注意事項：

- 根據平臺的不同，使用者首次登錄時可能需要一些使用者交互。 例如，iOS 要求應用在首次通過 Microsoft 身份驗證器（Android 上的 Intune 公司門戶）首次使用 SSO 時顯示使用者交互。
- 在 iOS 和 Android 上，MSAL 可能使用外部瀏覽器登錄使用者。 外部瀏覽器可能顯示在應用頂部。 您可以自訂配置以改用應用內 WebViews。
- 切勿在移動應用程式中使用機密。 在這些應用程式中，所有使用者都可以訪問機密。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用註冊](scenario-mobile-app-registration.md)
