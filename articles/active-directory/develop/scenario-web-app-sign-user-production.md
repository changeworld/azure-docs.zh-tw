---
title: 將登入使用者的 web 應用程式移至生產-Microsoft 身分識別平臺 |蔚藍
description: '瞭解如何建立 web 應用程式，以登入使用者 (移至生產環境) '
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
ms.openlocfilehash: fd9890cb94bf6bb4b82ebbb585ab8bbb9d5ba46a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169285"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>登入使用者的 Web 應用程式：移至生產環境

既然您已瞭解如何取得權杖來呼叫 web Api，請瞭解如何將它移至生產環境。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="troubleshooting"></a>疑難排解

> [!NOTE]
> 當使用者第一次登入 web 應用程式時，他們就必須同意。 不過，在某些組織中，使用者可以看到如下所示的訊息：
>
> *AppName 需要有權存取組織中只有系統管理員可以授與的資源。請先要求系統管理員授與此應用程式的許可權，然後才能使用它。*
>
> 這是因為您的租使用者系統管理員已 **停用** 使用者同意的能力。 在此情況下，您需要洽詢租使用者系統管理員，讓他們對應用程式所需的範圍進行系統管理員同意。

## <a name="same-site"></a>相同網站

請確定您瞭解新版 Chrome 瀏覽器的可能問題： [如何處理 chrome 瀏覽器中的 SameSite cookie 變更](howto-handle-samesite-cookie-changes-chrome-browser.md)。

Web.config NuGet 套件可處理最常見的 SameSite 問題。

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>深入探討： ASP.NET Core web 應用程式教學課程

深入瞭解使用此 ASP.NET Core 教學課程登入使用者的其他方法： 

[讓您的 web 應用程式使用適用于開發人員的 Microsoft 身分識別平臺來登入使用者並呼叫 Api](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)

此漸進式教學課程具有適用于 web 應用程式的生產環境就緒程式碼，包括如何在下列情況中新增帳戶的登入：

- 您的組織
- 多個組織
- 公司或學校帳戶，或個人 Microsoft 帳戶
- [Azure AD B2C](../../active-directory-b2c/overview.md)
- 國家雲端

## <a name="sample-code-java-web-app"></a>範例程式碼： JAVA web 應用程式

在 GitHub 上深入瞭解此範例中的 JAVA web 應用程式： 

[使用 Microsoft 身分識別平臺登入使用者並呼叫 Microsoft Graph 的 JAVA Web 應用程式](https://github.com/Azure-Samples/ms-identity-java-webapp)

## <a name="next-steps"></a>後續步驟

當您的 web 應用程式登入使用者之後，就可以代表已登入的使用者呼叫 web Api。 從 web 應用程式呼叫 web Api 是下列案例的物件： [呼叫 Web api 的 web 應用程式](scenario-web-app-call-api-overview.md)。