---
title: 將使用者登錄的 Web 應用移動到生產 - 微軟身份平臺 |蔚藍
description: 瞭解如何建構在使用者中簽名的 Web 應用程式(移至生產)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9c5fd444c55a20441325088912a07eb051219b84
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881463"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>在使用者中簽名的 Web 應用:移動到生產

現在,您已經知道如何獲取權杖來呼叫 Web API,請瞭解如何將其移動到生產。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>後續步驟

### <a name="same-site"></a>同一網站

確保您瞭解 Chrome 瀏覽器新版本的可能問題

> [!div class="nextstepaction"]
> [如何處理 Chrome 瀏覽器中的同一網站 Cookie 更改](howto-handle-samesite-cookie-changes-chrome-browser.md)

### <a name="scenario-for-calling-web-apis"></a>呼叫 Web API 的專案

Web 應用登錄使用者後,它可以代表登錄使用者調用 Web API。 從 Web 應用呼叫 Web API 是以下機制的物件:

> [!div class="nextstepaction"]
> [呼叫 Web API 的 Web 應用程式](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>深度潛水:ASP.NET核心網路應用程式教程

瞭解使用此 ASP.NET 核心教程登錄使用者的其他方法: 

> [!div class="nextstepaction"]
> [使 Web 應用程式能夠登入使用者,並使用適用於開發人員的 Microsoft 識別平台呼叫 API](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

此漸進式教程具有 Web 應用的生產就緒代碼,包括如何在以下方面添加帳戶登錄:

- 您的組織
- 多個組織
- 工作或學校帳戶或個人 Microsoft 帳戶
- [Azure AD B2C](https://aka.ms/aadb2c)
- 國家/地區雲端

## <a name="sample-code-java-web-app"></a>範例代碼:Java Web 應用程式

從 GitHub 上的此範例中瞭解有關 Java Web 應用的更多內容: 

> [!div class="nextstepaction"]
> [使用 Microsoft 識別平台登入使用者並呼叫 Microsoft Graph 的 Java Web 應用程式](https://github.com/Azure-Samples/ms-identity-java-webapp)
