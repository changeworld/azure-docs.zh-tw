---
title: 建立會呼叫 web Api 的桌面應用程式-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立會呼叫 web Api 的桌面應用程式（總覽）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 09cc43dec2eff48754f5a6e693badd6bb1907cce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882995"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>案例：呼叫 web Api 的傳統型應用程式

瞭解您所需的一切，以建立會呼叫 web Api 的桌面應用程式。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>開始使用

如果您還沒有這麼做，請遵循 .NET 桌面快速入門、通用 Windows 平臺（UWP）快速入門或 macOS 原生應用程式快速入門，建立您的第一個應用程式：

> [!div class="nextstepaction"]
> [快速入門：取得權杖，並從 Windows 傳統型應用程式呼叫 Microsoft Graph API](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [快速入門：取得權杖，並從 UWP 應用程式呼叫 Microsoft Graph API](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [快速入門：從 macOS 原生應用程式取得權杖並呼叫 Microsoft Graph API](./quickstart-v2-ios.md)

## <a name="overview"></a>概觀

您會撰寫桌面應用程式，而且您想要將使用者登入應用程式，並呼叫 web Api，例如 Microsoft Graph、其他 Microsoft Api 或您自己的 Web API。 您有幾種可能性：

- 您可以使用互動式權杖取得：

  - 如果您的桌面應用程式支援圖形控制項，例如，它是 Windows Form 應用程式、WPF 應用程式或 macOS 原生應用程式。
  - 或者，如果是 .NET Core 應用程式，而且您同意與系統瀏覽器中的 Azure Active Directory （Azure AD）互動，就會發生驗證。

- 對於 Windows 裝載的應用程式，也可以使用整合式 Windows 驗證，在加入 Windows 網域或 Azure AD 加入的電腦上執行的應用程式，以無訊息方式取得權杖。
- 最後，雖然不建議這麼做，但您可以在公用用戶端應用程式中使用使用者名稱和密碼。 在某些情況下，仍然需要用到，例如 DevOps。 使用它會在您的應用程式上施加條件約束。 比方說，它無法登入需要執行多重要素驗證（條件式存取）的使用者。 此外，您的應用程式不會受益于單一登入（SSO）。

  它也違反了新式驗證的原則，僅供舊版的理由之用。

  ![桌面應用程式](media/scenarios/desktop-app.svg)

- 如果您撰寫可移植的命令列工具（可能是在 Linux 或 Mac 上執行的 .NET Core 應用程式），而且如果您接受將委派給系統瀏覽器的驗證，則可以使用互動式驗證。 .NET Core 不提供[網頁瀏覽器](https://aka.ms/msal-net-uses-web-browser)，因此會在系統瀏覽器中進行驗證。 否則，在此情況下，最佳選項是使用裝置程式碼流程。 此流程也會用於沒有瀏覽器的應用程式，例如 IoT 應用程式。

  ![Browserless 應用程式](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>瞭解

桌面應用程式有數個 specificities。 它們主要取決於您的應用程式是否使用互動式驗證。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [桌面應用程式：應用程式註冊](scenario-desktop-app-registration.md)
