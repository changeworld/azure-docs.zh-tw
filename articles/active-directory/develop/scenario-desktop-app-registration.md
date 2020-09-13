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
ms.openlocfilehash: 6796ac42a10d3b976b23f5af1418b1789011d61b
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89440944"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>呼叫 web Api 的桌面應用程式：應用程式註冊

本文涵蓋桌面應用程式的應用程式註冊細節。

## <a name="supported-account-types"></a>支援的帳戶類型

桌面應用程式中支援的帳戶類型，取決於您想要亮的體驗。 基於此關聯性，支援的帳戶類型取決於您想要使用的流程。

### <a name="audience-for-interactive-token-acquisition"></a>取得互動式權杖的物件

如果您的桌面應用程式使用互動式驗證，您可以從任何 [帳戶類型](quickstart-register-app.md)登入使用者。

### <a name="audience-for-desktop-app-silent-flows"></a>桌面應用程式無訊息流程的物件

- 若要使用整合式 Windows 驗證或使用者名稱和密碼，您的應用程式必須在您自己的租使用者中登入使用者，例如，如果您是企業營運 (LOB) 開發人員。 或者，在 Azure Active Directory 組織中，如果是 ISV 案例，您的應用程式必須在您自己的租使用者中登入使用者。 Microsoft 個人帳戶不支援這些驗證流程。
- 如果您想要使用裝置程式碼流程，您還無法使用其 Microsoft 個人帳戶登入使用者。
- 如果您使用可傳遞企業對商務 (B2C) 授權單位和原則的社交身分識別來登入使用者，則只能使用互動式和使用者名稱密碼驗證。

## <a name="redirect-uris"></a>重新導向 URI

要在桌面應用程式中使用的重新導向 Uri 取決於您要使用的流程。

- 如果您使用互動式驗證或裝置程式碼流程，請使用 `https://login.microsoftonline.com/common/oauth2/nativeclient` 。 若要完成此設定，請在您應用程式的 [ **驗證** ] 區段中選取對應的 URL。

  > [!IMPORTANT]
  > 現今，MSAL.NET 預設會在 Windows () 上執行的桌面應用程式中，使用另一個重新導向 URI `urn:ietf:wg:oauth:2.0:oob` 。 未來，我們會想要變更此預設值，因此建議您使用 `https://login.microsoftonline.com/common/oauth2/nativeclient` 。

- 如果您建立原生目標 C 或 Swift 應用程式以進行 macOS，請以下列格式註冊以您應用程式套件組合識別碼為基礎的重新導向 URI： msauth. <your.app.bundle.id>：//auth. 以您應用程式的套件組合識別碼取代 <your.app.bundle.id>。
- 如果您的應用程式只使用整合式 Windows 驗證或使用者名稱和密碼，您就不需要為應用程式註冊重新導向 URI。 這些流程會往返 Microsoft 身分識別平臺2.0 版端點。 您的應用程式不會被回呼任何特定的 URI。
- 若要區別裝置程式碼流程、整合式 Windows 驗證，以及不具重新導向 Uri 的機密用戶端應用程式流程的密碼，請 () 的 daemon 應用程式中使用的用戶端認證流程，您必須表達您的應用程式是公用用戶端應用程式。 若要完成此設定，請移至應用程式的 [ **驗證** ] 區段。 在 [ **Advanced settings** ] 子區段的 [ **預設用戶端類型** ] 段落中，選取 **[是** ，將 **應用程式視為公用用戶端**]。

  ![允許公用用戶端](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API 權限

傳統型應用程式會呼叫已登入使用者的 Api。 他們需要要求委派的許可權。 他們無法要求只在 [daemon 應用程式](scenario-daemon-overview.md)中處理的應用程式許可權。

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [桌面應用程式：應用程式設定](scenario-desktop-app-configuration.md)
