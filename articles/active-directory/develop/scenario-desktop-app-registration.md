---
title: 註冊呼叫 web Api 的桌面應用程式-Microsoft 身分識別平臺 |蔚藍
description: '瞭解如何建立傳統型應用程式，以呼叫 (應用程式註冊的 web Api) '
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7c0623d58bf88b8bcc5f19665a081ce012b560b6
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614861"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>呼叫 web Api 的桌面應用程式：應用程式註冊

本文涵蓋桌面應用程式的應用程式註冊細節。

## <a name="supported-account-types"></a>支援的帳戶類型

桌面應用程式中支援的帳戶類型，取決於您想要亮的體驗。 基於此關聯性，支援的帳戶類型取決於您想要使用的流程。

### <a name="audience-for-interactive-token-acquisition"></a>取得互動式權杖的物件

如果您的桌面應用程式使用互動式驗證，您可以從任何 [帳戶類型](quickstart-register-app.md)登入使用者。

### <a name="audience-for-desktop-app-silent-flows"></a>桌面應用程式無訊息流程的物件

- 若要使用整合式 Windows 驗證或使用者名稱和密碼，您的應用程式必須在您自己的租使用者中登入使用者，例如，如果您是企業營運 (LOB) 開發人員。 或者，在 Azure Active Directory 組織中，如果是 ISV 案例，您的應用程式必須在您自己的租使用者中登入使用者。 Microsoft 個人帳戶不支援這些驗證流程。
- 如果您使用可傳遞企業對商務 (B2C) 授權單位和原則的社交身分識別來登入使用者，則只能使用互動式和使用者名稱密碼驗證。

## <a name="redirect-uris"></a>重新導向 URI

要在桌面應用程式中使用的重新導向 Uri 取決於您要使用的流程。

- 如果您使用互動式驗證或裝置程式碼流程，請使用 `https://login.microsoftonline.com/common/oauth2/nativeclient` 。 若要完成此設定，請在您應用程式的 [ **驗證** ] 區段中選取對應的 URL。

  > [!IMPORTANT]
  > 現今，MSAL.NET 預設會在 Windows () 上執行的桌面應用程式中，使用另一個重新導向 URI `urn:ietf:wg:oauth:2.0:oob` 。 未來，我們會想要變更此預設值，因此建議您使用 `https://login.microsoftonline.com/common/oauth2/nativeclient` 。

- 如果您建立原生目標 C 或 Swift 應用程式以進行 macOS，請以下列格式註冊以您應用程式套件組合識別碼為基礎的重新導向 URI： `msauth.<your.app.bundle.id>://auth` 。 取代 `<your.app.bundle.id>` 為應用程式的套件組合識別碼。
- 如果您的應用程式只使用整合式 Windows 驗證或使用者名稱和密碼，您就不需要為應用程式註冊重新導向 URI。 這些流程會往返 Microsoft 身分識別平臺2.0 版端點。 您的應用程式不會被回呼任何特定的 URI。
- 若要使用在[daemon 應用](scenario-daemon-overview.md)程式中使用的用戶端認證流程來區別[裝置程式碼流程](scenario-desktop-acquire-token.md#device-code-flow)、[整合式 Windows 驗證](scenario-desktop-acquire-token.md#integrated-windows-authentication)，以及機密用戶端應用程式的使用者[名稱和密碼](scenario-desktop-acquire-token.md#username-and-password)，則不需要重新導向 URI，您必須將它設定為公用用戶端應用程式。 若要達成這項組態︰

    1. 在 [ [Azure 入口網站](https://portal.azure.com)中，選取 **應用程式註冊** 中的應用程式，然後選取 [ **驗證**]。
    1. 在 [**高級設定**] 中，  >  **允許公用用戶端流程**  >  **啟用下列行動和桌面流程：**，選取 **[是]**。

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="在 Azure 入口網站中啟用 [驗證] 窗格上的 [公用用戶端設定]":::

## <a name="api-permissions"></a>API 權限

傳統型應用程式會呼叫已登入使用者的 Api。 他們需要要求委派的許可權。 他們無法要求只在 [daemon 應用程式](scenario-daemon-overview.md)中處理的應用程式許可權。

## <a name="next-steps"></a>後續步驟

請繼續進行此案例中的下一篇文章，也就是 [應用程式程式碼](scenario-desktop-app-configuration.md)設定。
