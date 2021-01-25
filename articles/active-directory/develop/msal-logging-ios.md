---
title: 在 iOS/macOS 的 MSAL 中記錄錯誤和例外狀況
titleSuffix: Microsoft identity platform
description: 瞭解如何在 iOS/macOS 的 MSAL 中記錄錯誤和例外狀況
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
ms.openlocfilehash: ee3837b75d586238e7ca6ac85434cc56f592929d
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763354"
---
# <a name="logging-in-msal-for-iosmacos"></a>iOS/macOS 版 MSAL 中的記錄

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>適用于 iOS 和 macOS 記錄的 MSAL-ObjC

設定回呼以捕獲 MSAL 記錄，並將它併入您自己的應用程式記錄中。 回呼的簽章看起來像這樣：

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

例如：

```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

### <a name="personal-data"></a>個人資料

根據預設，MSAL 不會捕捉或記錄任何個人資料。 程式庫可讓應用程式開發人員透過 MSALLogger 類別中的屬性來開啟此功能。 藉由開啟 `pii.Enabled` ，應用程式會負責安全地處理高度敏感的資料，並遵循法規需求。

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>記錄層級

若要在使用 MSAL for iOS 和 macOS 記錄時設定記錄層級，請使用下列其中一個值：

|層級  |描述 |
|---------|---------|
| `MSALLogLevelNothing`| 停用所有記錄 |
| `MSALLogLevelError` | 預設層級，只有在發生錯誤時才會印出資訊 |
| `MSALLogLevelWarning` | 警告 |
| `MSALLogLevelInfo` |  程式庫進入點，具有參數和各種 keychain 作業 |
|`MSALLogLevelVerbose`     |  API 追蹤 |

例如：

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>記錄訊息格式

MSAL 記錄訊息的訊息部分格式為 `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

例如：

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

提供相互關聯識別碼和時間戳記有助於追蹤問題。 時間戳記和相互關聯識別碼資訊可在記錄訊息中取得。 取得它們的唯一可靠位置是從 MSAL 記錄訊息。

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>適用于 iOS 和 macOS 記錄的 MSAL-Swift

設定回呼以捕獲 MSAL 記錄，並將它併入您自己的應用程式記錄中。 針對回呼的目標-C) 所表示的簽章 (看起來像這樣：

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

例如：

```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-data"></a>個人資料

根據預設，MSAL 不會捕捉或記錄任何個人資料。 程式庫可讓應用程式開發人員透過 MSALLogger 類別中的屬性來開啟此功能。 藉由開啟 `pii.Enabled` ，應用程式會負責安全地處理高度敏感的資料，並遵循法規需求。

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>記錄層級

若要在使用 MSAL for iOS 和 macOS 記錄時設定記錄層級，請使用下列其中一個值：

|層級  |描述 |
|---------|---------|
| `MSALLogLevelNothing`| 停用所有記錄 |
| `MSALLogLevelError` | 預設層級，只有在發生錯誤時才會印出資訊 |
| `MSALLogLevelWarning` | 警告 |
| `MSALLogLevelInfo` |  程式庫進入點，具有參數和各種 keychain 作業 |
|`MSALLogLevelVerbose`     |  API 追蹤 |

例如：

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>記錄訊息格式

MSAL 記錄訊息的訊息部分格式為 `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

例如：

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

提供相互關聯識別碼和時間戳記有助於追蹤問題。 時間戳記和相互關聯識別碼資訊可在記錄訊息中取得。 取得它們的唯一可靠位置是從 MSAL 記錄訊息。

---

## <a name="next-steps"></a>後續步驟

如需更多程式碼範例，請參閱 [Microsoft 身分識別平臺程式碼範例](sample-v2-code.md)。