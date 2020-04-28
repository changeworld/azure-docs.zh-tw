---
title: 註冊呼叫 web Api 的桌面應用程式-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立呼叫 web Api 的傳統型應用程式（應用程式註冊）
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
ms.openlocfilehash: 599603ba867e21694392e38e9692280f010e08eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885152"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>呼叫 web Api 的桌面應用程式：應用程式註冊

本文涵蓋桌面應用程式的應用程式註冊細節。

## <a name="supported-account-types"></a>支援的帳戶類型

桌面應用程式中支援的帳戶類型取決於您想要亮起的體驗。 基於此關聯性，支援的帳戶類型取決於您想要使用的流程。

### <a name="audience-for-interactive-token-acquisition"></a>取得互動式權杖的物件

如果您的桌面應用程式使用互動式驗證，則可以從任何[帳戶類型](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)登入使用者。

### <a name="audience-for-desktop-app-silent-flows"></a>桌面應用程式無訊息流程的物件

- 若要使用整合式 Windows 驗證或使用者名稱和密碼，您的應用程式必須在您自己的租使用者（例如，如果您是企業營運（LOB）開發人員）中登入使用者。 或者，在 Azure Active Directory 組織中，如果您的應用程式是 ISV 案例，則必須在您自己的租使用者中登入他們。 Microsoft 個人帳戶不支援這些驗證流程。
- 如果您想要使用裝置程式碼流程，則無法使用他們的 Microsoft 個人帳戶登入使用者。
- 如果您登入的使用者具有通過企業對商務（B2C）授權單位和原則的社交身分識別，您只能使用互動式和使用者名稱密碼驗證。

## <a name="redirect-uris"></a>重新導向 URI

桌面應用程式中所使用的重新導向 Uri 取決於您想要使用的流程。

- 如果您使用互動式驗證或裝置程式碼流程， `https://login.microsoftonline.com/common/oauth2/nativeclient`請使用。 若要完成這項設定，請在應用程式的 [**驗證**] 區段中選取對應的 URL。
  
  > [!IMPORTANT]
  > 目前，MSAL.NET 會在 Windows 上執行的桌面應用程式中，預設使用另`urn:ietf:wg:oauth:2.0:oob`一個重新導向 URI （）。 在未來，我們會想要變更此預設值，因此建議您使用`https://login.microsoftonline.com/common/oauth2/nativeclient`。

- 如果您為 macOS 建立原生目標 C 或 Swift 應用程式，請根據應用程式的套件組合識別碼，以下列格式註冊重新導向 URI： msauth. <your.app.bundle.id>：//auth. 以應用程式的套件組合識別碼取代 <your.app.bundle.id>。
- 如果您的應用程式只使用整合式 Windows 驗證或使用者名稱和密碼，您就不需要為應用程式註冊重新導向 URI。 這些流程會執行 Microsoft 身分識別平臺 v2.0 端點的往返。 您的應用程式不會在任何特定的 URI 上回呼。
- 若要區分裝置程式碼流程、整合式 Windows 驗證，以及不具重新導向 Uri 的機密用戶端應用程式流程中的使用者名稱和密碼（在背景工作應用程式中使用的用戶端認證流程），您需要表示您的應用程式是公用用戶端應用程式。 若要達到此設定，請移至應用程式的 [**驗證**] 區段。 在 [**高級設定**] 子區段的 [**預設用戶端類型**] 段落中，針對 [**將應用程式視為公用用戶端** **] 選取 [是]** 。

  ![允許公用用戶端](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API 權限

桌面應用程式會呼叫已登入使用者的 Api。 他們需要要求委派的許可權。 他們無法要求應用程式許可權，而只會在[後臺應用程式](scenario-daemon-overview.md)中處理。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [桌面應用程式：應用程式設定](scenario-desktop-app-configuration.md)
