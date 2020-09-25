---
title: 建置可呼叫 Web API 的傳統型應用程式 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何建置可呼叫 Web API 的傳統型應用程式
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a6cbb574245c7b11d2437693d60fd952becedba3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257549"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>案例：呼叫 Web API 的傳統型應用程式

了解建置可呼叫 Web API 的傳統型應用程式所需的一切。

## <a name="get-started"></a>開始使用

如果您還沒有這麼做，請完成快速入門以建立您的第一個應用程式：

- [快速入門：取得權杖，並從 Windows 桌面應用程式呼叫 Microsoft Graph API](./quickstart-v2-windows-desktop.md)
- [快速入門：取得權杖，並從 UWP 應用程式呼叫 Microsoft Graph API](./quickstart-v2-uwp.md)
- [快速入門：取得權杖，並從 macOS 原生應用程式呼叫 Microsoft Graph API](./quickstart-v2-ios.md)

## <a name="overview"></a>概觀

您撰寫傳統型應用程式，而且想要讓使用者登入您的應用程式，並呼叫 Web API (例如 Microsoft Graph)、其他 Microsoft API 或您自己的 Web API。 您有幾種選項：

- 在下列情況，您可使用互動式權杖取得：

  - 如果您的傳統型應用程式支援圖形化控制項，例如，若是 Windows Form 應用程式、WPF 應用程式或 macOS 原生應用程式。
  - 或者，若是 .NET Core 應用程式，而且您同意與系統瀏覽器中的 Azure Active Directory (Azure AD) 進行驗證互動。

- 若為 Windows 裝載的應用程式，在已加入 Windows 網域或已加入 Azure AD 的電腦上執行的應用程式，也可使用整合式 Windows 驗證以無訊息方式取得權杖。
- 最後，雖然不建議這麼做，但您可在公用用戶端應用程式中使用使用者名稱和密碼。 在某些案例 (例如 DevOps) 中，仍然需要這麼做。 這麼做會對您的應用程式施加條件約束。 例如，這麼做無法讓需要執行[多重要素驗證](../authentication/concept-mfa-howitworks.md) (條件式存取) 的使用者登入。 此外，您的應用程式也不會受益於單一登入 (SSO)。

  它也違反了新式驗證的原則，僅供舊版的理由之用。

  ![傳統型應用程式](media/scenarios/desktop-app.svg)

- 如果您撰寫可攜式命令列工具 (可能是在 Linux 或 Mac 上執行的 .NET Core 應用程式)，而且若接受將驗證委派給系統瀏覽器，則可使用互動式驗證。 .NET Core 不會提供[網頁瀏覽器](https://aka.ms/msal-net-uses-web-browser)，因此會在系統瀏覽器中進行驗證。 否則，在該情況下，最佳選項是使用裝置碼流程。 此流程也會用於沒有瀏覽器的應用程式，例如 IoT 應用程式。

  ![無瀏覽器應用程式](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>特性

傳統型應用程式有幾個特性。 其主要取決於您的應用程式是否使用互動式驗證。

## <a name="recommended-reading"></a>建議閱讀資料

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [傳統型應用程式：應用程式註冊](scenario-desktop-app-registration.md)
