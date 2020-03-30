---
title: 註冊調用 Web API 的桌面應用 - 微軟身份平臺 |蔚藍
description: 瞭解如何構建調用 Web API 的桌面應用（應用註冊）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c55fc9eb94a88dba1ab9fc915fe84bc2dd7d4d40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702176"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>調用 Web API 的桌面應用：應用註冊

本文介紹了桌面應用程式的應用註冊細節。

## <a name="supported-account-types"></a>支援的帳戶類型

桌面應用程式中支援的帳戶類型取決於要照亮的體驗。 由於這種關係，受支援的帳戶類型取決於要使用的流。

### <a name="audience-for-interactive-token-acquisition"></a>互動式權杖獲取的受眾

如果您的桌面應用程式使用互動式身份驗證，則可以從任何[帳戶類型](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)登錄使用者。

### <a name="audience-for-desktop-app-silent-flows"></a>桌面應用靜默流的受眾

- 要使用集成 Windows 身份驗證或使用者名和密碼，應用程式需要在您自己的租戶中登錄使用者，例如，如果您是業務線 （LOB） 開發人員。 或者，在 Azure 活動目錄組織中，如果應用程式是 ISV 方案，則需要在您自己的租戶中登錄使用者。 Microsoft 個人帳戶不支援這些身份驗證流。
- 如果要使用設備代碼流，則尚無法使用其 Microsoft 個人帳戶登錄使用者。
- 如果您使用通過企業到商業 （B2C） 許可權和策略的社交身份登錄使用者，則只能使用互動式和使用者名和密碼身份驗證。

## <a name="redirect-uris"></a>重新導向 URI

在桌面應用程式中使用的重定向 URI 取決於要使用的流。

- 如果使用互動式身份驗證或設備代碼流，請使用`https://login.microsoftonline.com/common/oauth2/nativeclient`。 要實現此配置，請在應用程式的 **"身份驗證**"部分中選擇相應的 URL。
  
  > [!IMPORTANT]
  > 如今，MSAL.NET在 Windows 上運行的桌面應用程式中預設使用另一個重定向`urn:ietf:wg:oauth:2.0:oob`URI （ 。 將來，我們將要更改此預設值，因此我們建議您使用`https://login.microsoftonline.com/common/oauth2/nativeclient`。

- 如果為 macOS 構建本機 Objective-C 或 Swift 應用，請基於應用程式的捆綁識別碼以以下格式註冊重定向 URI：msauth.<your.app.bundle.id>：//auth.將<your.app.bundle.id>替換為應用程式的捆綁識別碼。
- 如果應用僅使用集成 Windows 身份驗證或使用者名和密碼，則無需為應用程式註冊重定向 URI。 這些流對 Microsoft 標識平臺 v2.0 終結點進行往返訪問。 您的應用程式不會在任何特定 URI 上被調用。
- 為了區分設備代碼流、集成 Windows 身份驗證以及使用者名和密碼與沒有重定向 URI 的機密用戶端應用程式流（守護進程應用程式中使用的用戶端憑據流）的使用者名和密碼，您需要表示您的應用程式是公共用戶端應用程式。 要實現此配置，請轉到應用程式的**身份驗證**部分。 在 **"高級設置**"子部分中，**在"預設用戶端類型"** 段落中，選擇 **"是**"將**應用程式視為公共用戶端**。

  ![允許公共用戶端](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API 權限

桌面應用程式為登錄使用者調用 API。 他們需要請求委派的許可權。 他們不能請求應用程式許可權，這些許可權僅在[守護進程應用程式中](scenario-daemon-overview.md)處理。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [桌面應用：應用配置](scenario-desktop-app-configuration.md)
