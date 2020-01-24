---
title: 建立會呼叫 web Api 的行動應用程式 |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何建立呼叫 web Api 的行動應用程式（總覽）
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
ms.openlocfilehash: 0a33b5bb4fc7220a9cf66f40e9805d3fde9fccef
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702040"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>案例：呼叫 web Api 的行動應用程式

瞭解如何建立會呼叫 web Api 的行動應用程式。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>開始使用

建立您的第一個行動應用程式，並試用快速入門。

> [!div class="nextstepaction"]
> [快速入門：從 Android 應用程式取得權杖並呼叫 Microsoft Graph API](./quickstart-v2-android.md)
>
> [快速入門：從 iOS 應用程式取得權杖並呼叫 Microsoft Graph API](./quickstart-v2-ios.md)
>
> [快速入門：從 Xamarin iOS & Android 應用程式取得權杖並呼叫 Microsoft Graph API](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>概觀

個人化、順暢的使用者體驗對行動應用程式而言是必要的。  Microsoft 身分識別平臺可讓行動開發人員建立適用于 iOS 和 Android 使用者的體驗。 您的應用程式可以登入 Azure Active Directory （Azure AD）使用者、個人 Microsoft 帳戶使用者和 Azure AD B2C 使用者，並取得權杖來代表他們呼叫 Web API。 為了執行這些流程，我們將使用 Microsoft Authentication Library （MSAL），它會實行業界標準的[oauth 2.0 授權碼流程](v2-oauth2-auth-code-flow.md)。

![精靈應用程式](./media/scenarios/mobile-app.svg)

Mobile apps 的考慮：

- **使用者體驗是關鍵**：允許使用者在要求登入之前查看應用程式的值，並只要求必要的許可權。
- **支援所有使用者**設定：許多行動商務使用者都在條件式存取和裝置合規性原則之下。 請務必支援這些主要案例。
- **執行單一登入（SSO）** ： MSAL 和 Microsoft 身分識別平臺可讓您透過裝置的瀏覽器或 Microsoft Authenticator （和 Android 上的 Intune 公司入口網站）簡單地啟用單一登入。

## <a name="specifics"></a>瞭解

當您在 Microsoft 身分識別平臺上建立行動裝置應用程式時，請記住下列考慮：

- 視平臺而定，使用者第一次登入時可能需要進行某些使用者互動。 例如，iOS 需要應用程式在第一次透過 Microsoft Authenticator （在 Android 上為 Intune 公司入口網站）時，顯示使用者與 SSO 的互動。
- 在 iOS 和 Android 上，MSAL 可能會使用外部瀏覽器（可能會出現在您的應用程式頂端）來登入使用者。 您可以自訂設定，改為使用應用程式內網站。
- 永不使用行動應用程式中的秘密。 所有使用者都可以存取它。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-mobile-app-registration.md)
