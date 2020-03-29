---
title: 構建調用 Web API 的桌面應用 - 微軟身份平臺 |蔚藍
description: 瞭解如何構建調用 Web API 的桌面應用（概述）
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 196102769c1f5f68df316918a63079b09baad32d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702142"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>方案：調用 Web API 的桌面應用

瞭解構建調用 Web API 的桌面應用所需的全部內容。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>開始使用

如果尚未創建，請按照 .NET 桌面快速入門、通用 Windows 平臺 （UWP） 快速入門或 macOS 本機應用快速入門創建第一個應用程式：

> [!div class="nextstepaction"]
> [快速入門：取得權杖，並從 Windows 傳統型應用程式呼叫 Microsoft Graph API](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [快速入門：獲取權杖並從 UWP 應用調用 Microsoft 圖形 API](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [快速入門：獲取權杖並從 macOS 本機應用調用 Microsoft 圖形 API](./quickstart-v2-ios.md)

## <a name="overview"></a>總覽

編寫桌面應用程式，並希望將使用者登錄到應用程式並調用 Web API，如 Microsoft 圖形、其他 Microsoft API 或您自己的 Web API。 您有多種可能性：

- 您可以使用互動式權杖獲取：

  - 例如，如果桌面應用程式支援圖形控制項，則如果是 Windows.Form 應用程式、WPF 應用程式或 macOS 本機應用程式。
  - 或者，如果它是 .NET Core 應用程式，並且您同意在系統瀏覽器中與 Azure 活動目錄 （Azure AD） 進行身份驗證交互。

- 對於 Windows 託管應用程式，在加入到 Windows 域或加入 Azure AD 的電腦上運行的應用程式也可以使用集成 Windows 身份驗證靜默地獲取權杖。
- 最後，儘管不建議使用，但您可以在公共用戶端應用程式中使用使用者名和密碼。 在某些情況下，如 DevOps 中仍需要它。 使用它會對應用程式施加限制。 例如，它無法登錄需要執行多重要素驗證（條件訪問）的使用者。 此外，您的應用程式不會從單一登入 （SSO） 中受益。

  它也違反了新式驗證的原則，僅供舊版的理由之用。

  ![桌面應用程式](media/scenarios/desktop-app.svg)

- 如果您編寫一個可擕式命令列工具，可能是在 Linux 或 Mac 上運行的 .NET Core 應用程式，並且如果您接受該身份驗證將委派給系統瀏覽器，則可以使用互動式身份驗證。 .NET Core 不提供[Web 瀏覽器](https://aka.ms/msal-net-uses-web-browser)，因此身份驗證發生在系統瀏覽器中。 否則，在這種情況下，最佳選項是使用設備代碼流。 此流還用於沒有瀏覽器的應用程式，如 IoT 應用程式。

  ![無瀏覽器應用程式](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>細節

桌面應用程式具有許多特性。 它們主要取決於應用程式是否使用互動式身份驗證。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [桌面應用：應用程式註冊](scenario-desktop-app-registration.md)
