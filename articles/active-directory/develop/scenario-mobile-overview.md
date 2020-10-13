---
title: 建立可呼叫 web Api 的行動應用程式 |蔚藍
titleSuffix: Microsoft identity platform | Azure
description: '瞭解如何建立可呼叫 web Api 的行動應用程式 (總覽) '
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 70ddc2191cc06a1bede87adc5940e0f7dea63384
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91257532"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>案例：呼叫 web Api 的行動應用程式

了解如何建置會呼叫 Web API 的行動應用程式。

## <a name="getting-started"></a>開始使用

如果您還沒有這麼做，請完成快速入門以建立您的第一個應用程式：

- [快速入門：從 Android 應用程式取得權杖並呼叫 Microsoft Graph API](./quickstart-v2-android.md)
- [快速入門：取得權杖，並從 iOS 應用程式呼叫 Microsoft Graph API](./quickstart-v2-ios.md)
- [快速入門：從 Xamarin iOS 和 Android 應用程式 (GitHub 取得權杖並呼叫 MICROSOFT GRAPH API](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)) 

## <a name="overview"></a>概觀

行動裝置應用程式必須具備個人化、流暢的使用者體驗。  Microsoft 身分識別平臺可讓行動開發人員為 iOS 和 Android 使用者建立該體驗。 您的應用程式可以 Azure Active Directory (Azure AD) 使用者、個人 Microsoft 帳戶使用者和 Azure AD B2C 使用者登入。 它也可以取得權杖來代表他們呼叫 web API。 為了實施這些流程，我們將使用 Microsoft 驗證程式庫 (MSAL) 。 MSAL 實行業界標準 [oauth 2.0 授權碼流程](v2-oauth2-auth-code-flow.md)。

![精靈應用程式](./media/scenarios/mobile-app.svg)

Mobile apps 的考慮：

- **使用者體驗是關鍵**：允許使用者在要求登入之前，先查看您的應用程式的值。 只要求必要的許可權。
- **支援所有使用者**設定：許多行動商務使用者必須遵守條件式存取原則和裝置合規性原則。 請務必支援這些重要案例。
- **執行單一登入 (SSO) **：藉由使用 MSAL 和 Microsoft 身分識別平臺，您可以透過裝置的瀏覽器或 Microsoft Authenticator (和 Android Intune 公司入口網站上的) 來啟用單一登入。
- **實行共用裝置模式**：讓您的應用程式可用於共用裝置案例，例如醫院、製造、零售和財務。 [深入瞭解如何支援共用裝置模式](msal-shared-devices.md)。

## <a name="specifics"></a>特性

當您在 Microsoft 身分識別平臺上建立行動裝置應用程式時，請記住下列考慮：

- 視平臺而定，第一次使用者登入時，可能需要某些使用者互動。 例如，iOS 要求應用程式在第一次透過 Microsoft Authenticator (和 Intune 公司入口網站在 Android) 上時，顯示使用者的互動。
- 在 iOS 和 Android 上，MSAL 可能會使用外部瀏覽器來登入使用者。 外部瀏覽器可能會顯示在您的應用程式頂端。
- 請勿在行動應用程式中使用秘密。 在這些應用程式中，所有使用者都可以存取秘密。

## <a name="recommended-reading"></a>建議閱讀資料

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-mobile-app-registration.md)
