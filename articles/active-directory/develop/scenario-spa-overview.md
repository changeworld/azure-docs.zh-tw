---
title: JavaScript 單頁應用方案 - 微軟身份平臺 |蔚藍
description: 瞭解如何使用 Microsoft 標識平臺構建單頁應用程式（方案概述）。
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b430778bed811656b5c8aadc75ba3cf35917f737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701870"
---
# <a name="scenario-single-page-application"></a>方案：單頁應用程式

瞭解構建單頁應用程式 （SPA） 所需的一切。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>開始使用

您可以按照 JavaScript SPA 快速入門創建第一個應用程式：

> [!div class="nextstepaction"]
> [快速入門：單頁應用程式](./quickstart-v2-javascript.md)

## <a name="overview"></a>總覽

許多現代 Web 應用程式都是作為用戶端單頁應用程式構建的。 開發人員使用 JavaScript 或 SPA 框架（如 Angular、Vue.js 和 React.js）來編寫它們。 這些應用程式在 Web 瀏覽器上運行，具有不同于傳統伺服器端 Web 應用程式的身份驗證特徵。 

微軟身份平臺允許單頁應用程式登錄使用者，並通過[OAuth 2.0隱式流](./v2-oauth2-implicit-grant-flow.md)獲取權杖來訪問後端服務或Web API。 隱式流允許應用程式獲取 ID 權杖來表示經過身份驗證的使用者，還可以訪問調用受保護的 API 所需的權杖。

![單頁應用程式](./media/scenarios/spa-app.svg)

此身份驗證流不包括使用跨平臺 JavaScript 框架（如 Electron 和 React-本機）的應用程式方案。 它們需要進一步的功能來與本機平臺交互。

## <a name="specifics"></a>細節

要為應用程式啟用此方案，您需要：

* 使用 Azure 活動目錄 （Azure AD） 的應用程式註冊。 此註冊涉及啟用隱式流和設置重定向 URI，將權杖返回。
* 具有已註冊應用程式屬性的應用程式佈建，如應用程式 ID。
* 使用 Microsoft 身份驗證庫 （MSAL） 執行身份驗證流以登錄和獲取權杖。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用註冊](scenario-spa-app-registration.md)
