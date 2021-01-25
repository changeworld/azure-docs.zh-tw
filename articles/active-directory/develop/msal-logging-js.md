---
title: 記錄 MSAL.js 中的錯誤和例外狀況
titleSuffix: Microsoft identity platform
description: 瞭解如何在 MSAL.js 中記錄錯誤和例外狀況
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8604a38bc310cc884c2b5e99efe7a47ae5e787d7
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763355"
---
# <a name="logging-in-msaljs"></a>MSAL.js 中的記錄

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msaljs"></a>在 MSAL.js 中設定記錄

藉由在建立實例的設定期間傳遞記錄器物件，在 MSAL.js (JavaScript) 中啟用記錄 `UserAgentApplication` 。 此記錄器物件具有下列屬性：

- `localCallback`：可由開發人員提供的回呼實例，以自訂的方式取用和發佈記錄。 請依您想要將記錄重新導向的方式實作 localCallback 方法。
- `level` (選擇性) ：可設定的記錄層級。 支援的記錄層級為： `Error` 、 `Warning` 、 `Info` 和 `Verbose` 。 預設為 `Info`。
- `piiLoggingEnabled` (選擇性) ：如果設為 true，則會記錄個人和組織資料。 依預設，此值為 false，因此您的應用程式不會記錄個人資料。 個人資料記錄永遠不會被寫入如 Console、Logcat 或 NSLog 等的預設輸出。
- `correlationId` (選擇性的) ：唯一識別碼，用來將要求對應至偵測用途的回應。 預設為 RFC4122 4 版 guid (128 位元)。

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
    },
    system: {
        logger: new Msal.Logger(
            loggerCallback , {
                level: Msal.LogLevel.Verbose,
                piiLoggingEnabled: false,
                correlationId: '1234'
            }
        )
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="next-steps"></a>後續步驟

如需更多程式碼範例，請參閱 [Microsoft 身分識別平臺程式碼範例) ](sample-v2-code.md)。