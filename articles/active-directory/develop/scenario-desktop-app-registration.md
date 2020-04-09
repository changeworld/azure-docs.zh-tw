---
title: 註冊調用 Web API 的桌面應用 - 微軟身份平臺 |蔚藍
description: 瞭解如何建構 Web API 的桌面應用(應用程式註冊)
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885152"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>呼叫 Web API 的桌面應用:應用程式註冊

本文介紹了桌面應用程式的應用註冊細節。

## <a name="supported-account-types"></a>支援的帳戶類型

桌面應用程式中支援的帳戶類型取決於要照亮的體驗。 由於這種關係,受支援的帳戶類型取決於要使用的流。

### <a name="audience-for-interactive-token-acquisition"></a>互動式權杖抓取的受眾

如果您的桌面應用程式使用互動式身份驗證,則可以從任何[帳戶類型](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)登錄使用者。

### <a name="audience-for-desktop-app-silent-flows"></a>桌面應用靜默流的受眾

- 要使用整合的 Windows 身份驗證或使用者名稱和密碼,應用程式需要在您自己的租戶中登入使用者,例如,如果您是業務線 (LOB) 開發人員。 或者,在 Azure 活動目錄組織中,如果應用程式是 ISV 方案,則需要在您自己的租戶中登錄使用者。 Microsoft 個人帳戶不支援這些身份驗證流。
- 如果要使用設備代碼流,則尚無法使用其 Microsoft 個人帳戶登錄使用者。
- 如果您使用通過企業到商業 (B2C) 許可權和策略的社交身份登錄使用者,則只能使用互動式和使用者名和密碼身份驗證。

## <a name="redirect-uris"></a>重新導向 URI

在桌面應用程式中使用的重定向URI取決於要使用的流。

- 如果使用互動式認證或裝置碼串流,請使用`https://login.microsoftonline.com/common/oauth2/nativeclient`。 要實現此設定,請在應用程式的 **「身份驗證**」部分中選擇相應的 URL。
  
  > [!IMPORTANT]
  > 如今,MSAL.NET 在 Windows 上執行的桌面應用程式中預設使用另一個`urn:ietf:wg:oauth:2.0:oob`重定向 URI ( 。 將來,我們將要更改此預設值,因此我們建議您使用`https://login.microsoftonline.com/common/oauth2/nativeclient`。

- 如果為 macOS 構建本機 Objective-C 或 Swift 應用,請基於應用程式的捆綁標識符以以下格式註冊重定向 URI:msauth.<your.app.bundle.id>TPs.將<your.app.bundle.id>替換為應用程式的捆綁標識符。
- 如果應用僅使用整合 Windows 身份驗證或使用者名稱和密碼,則無需為應用程式註冊重定向 URI。 這些流對 Microsoft 標識平臺 v2.0 終結點進行往返訪問。 您的應用程式不會在任何特定 URI 上被呼叫。
- 為了區分設備代碼流、集成 Windows 身份驗證以及使用者名稱和密碼與沒有重定向 URI 的機密用戶端應用程式流(守護程序應用程式中使用的用戶端認證串流),您需要表示應用程式是公共用戶端應用程式。 要實現此配置,請轉到應用程式的**身份驗證**部分。 在 **「進階設定**」子部份中,**預設的客戶端類型「 將**應用程式」 , 選擇 **「是**「 將**應用程式視為公共用戶端**。

  ![允許公共用戶端](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API 權限

桌面應用程式為登錄使用者調用 API。 他們需要請求委派的許可權。 他們不能請求應用程式許可權,這些許可權僅在[守護程序應用程式中](scenario-daemon-overview.md)處理。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [桌面應用:應用程式設定](scenario-desktop-app-configuration.md)
