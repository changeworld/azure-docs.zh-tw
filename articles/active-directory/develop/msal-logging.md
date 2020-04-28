---
title: 在 MSAL apps 中記錄 |Azure
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 驗證程式庫 (MSAL) 應用程式中的記錄。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/11/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 58697cc535357710c6889f05060b5e04e129ae7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77084885"
---
# <a name="logging-in-msal-applications"></a>MSAL 應用程式中的記錄

Microsoft 驗證程式庫（MSAL）應用程式會產生記錄訊息，以協助診斷問題。 應用程式可透過幾行程式碼來設定記錄，並針對詳細資料層級和是否要記錄個人和組織資料具有自訂控制。 我們建議您建立 MSAL 記錄回呼，並提供一種方法，讓使用者在有驗證問題時提交記錄。

## <a name="logging-levels"></a>記錄層級

MSAL 提供數種層級的記錄詳細資料：

- 錯誤：指出發生錯誤，並產生錯誤。 用來進行偵錯及識別問題。
- 警告：不一定會發生錯誤或失敗，但適用于診斷和查明問題。
- Info： MSAL 會記錄適用于資訊目的的事件，不一定是用於進行偵錯工具。
- 詳細資訊：預設值。 MSAL 會記錄程式庫行為的完整詳細資料。

## <a name="personal-and-organizational-data"></a>個人和組織資料

根據預設，MSAL 記錄器不會捕捉任何高度敏感的個人或組織資料。 如果您決定要這麼做，程式庫會提供啟用記錄個人和組織資料的選項。

如需以特定語言 MSAL 記錄的詳細資訊，請選擇符合您語言的索引標籤：

## <a name="net"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>MSAL.NET 中的記錄

 > [!NOTE]
 > 如需 MSAL.NET 記錄的範例，請參閱[MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) 。

在 MSAL 3.x 中，記錄必須在每個應用程式的建立期間，使用 `.WithLogging` 建立器修飾詞針對該應用程式進行個別設定。 此方法可接受選擇性參數：

- `Level`可讓您決定您想要的記錄層級。 將它設定為 Errors 將只會取得錯誤
- `PiiLoggingEnabled`如果設定為 true，則可讓您記錄個人和組織的資料。 此參數預設為 false，這會使您的應用程式不會記錄個人資料。
- `LogCallback`設定為執行記錄的委派。 如果`PiiLoggingEnabled`為 true，則這個方法會接收兩次訊息：一次`containsPii` ，參數等於 false，而不含個人資料的訊息，第二次`containsPii`的參數等於 true，而訊息可能包含個人資料。 在某些情況下 (當訊息不包含個人資料時)，這兩個訊息將會相同。
- `DefaultLoggingEnabled`啟用平臺的預設記錄。 根據預設，它是 false。 如果您將它設定為 true，它會使用桌面/UWP 應用程式中的事件追蹤、iOS 上的 NSLog，以及 Android 上的 logcat。

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

## <a name="android"></a>[Android](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>使用 JAVA 登入適用于 Android 的 MSAL

藉由建立記錄回呼，在應用程式建立時開啟登入。 回呼會採用下列參數：

- `tag`是程式庫傳遞給回呼的字串。 它會與記錄專案相關聯，並可用於排序記錄訊息。
- `logLevel`可讓您決定您想要的記錄層級。 支援的記錄層級為`Error`： `Warning`、 `Info`、和`Verbose`。
- `message`這是記錄專案的內容。
- `containsPII`指定是否記錄包含個人資料或組織資料的訊息。 根據預設，這會設定為 false，讓您的應用程式不會記錄個人資料。 如果`containsPII`為`true`，則這個方法會接收兩次訊息：一次`containsPII`是將參數`false`設定為`message` ，而不使用個人資料，第二次`containsPii`是將參數`true`設定為，而訊息可能包含個人資料。 在某些情況下 (當訊息不包含個人資料時)，這兩個訊息將會相同。

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

根據預設，MSAL 記錄器不會捕捉任何個人識別資訊或組織識別資訊。
若要啟用個人識別資訊或組織識別資訊的記錄功能：

```java
Logger.getInstance().setEnablePII(true);
```

若要停用記錄個人資料和組織資料：

```java
Logger.getInstance().setEnablePII(false);
```

預設會停用記錄至 logcat。 若要啟用：

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

 藉由在建立`UserAgentApplication`實例的設定期間傳遞記錄器物件，在 MSAL 中啟用記錄功能。 此記錄器物件具有下列屬性：

- `localCallback`：可由開發人員提供的回呼實例，以自訂的方式取用和發行記錄。 請依您想要將記錄重新導向的方式實作 localCallback 方法。
- `level`（選擇性）：可設定的記錄層級。 支援的記錄層級為`Error`： `Warning`、 `Info`、和`Verbose`。 預設值為 `Info`。
- `piiLoggingEnabled`（選擇性）：如果設定為 true，則會記錄個人和組織資料。 根據預設，此值為 false，因此您的應用程式不會記錄個人資料。 個人資料記錄永遠不會被寫入如 Console、Logcat 或 NSLog 等的預設輸出。
- `correlationId`（選擇性）：唯一識別碼，用來將要求對應至用於進行偵錯工具的回應。 預設為 RFC4122 4 版 guid (128 位元)。

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
                                loggerCallback ,{
                                     level: Msal.LogLevel.Verbose,
                                     piiLoggingEnabled: false,
                                     correlationId: '1234'
                                }
                        )
     }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>適用于 iOS 和 macOS 記錄的 MSAL-ObjC

設定回呼來捕捉 MSAL 記錄，並將它併入您自己的應用程式記錄中。 回呼的簽章看起來像這樣：

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

根據預設，MSAL 不會捕捉或記錄任何個人資料（PII）。 程式庫可讓應用程式開發人員透過 MSALLogger 類別中的屬性來開啟此功能。 藉由開啟`pii.Enabled`，應用程式會負責安全地處理高度敏感的資料，並遵循法規需求。

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>記錄層級

若要在使用 iOS 和 macOS 的 MSAL 記錄時設定記錄層級，請使用下列其中一個值：

|層級  |描述 |
|---------|---------|
| `MSALLogLevelNothing`| 停用所有記錄 |
| `MSALLogLevelError` | 預設層級，只有在發生錯誤時才會列印資訊 |
| `MSALLogLevelWarning` | 警告 |
| `MSALLogLevelInfo` |  程式庫進入點，包含參數和各種 keychain 作業 |
|`MSALLogLevelVerbose`     |  API 追蹤 |

例如：

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>記錄訊息格式

MSAL 記錄訊息的訊息部分格式為`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

例如：

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

提供相互關聯識別碼和時間戳記有助於追蹤問題。 時間戳記和相互關聯識別碼資訊可在記錄訊息中取得。 唯一可取得的可靠位置是從 MSAL 記錄訊息。

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>適用于 iOS 和 macOS 記錄的 MSAL-Swift

設定回呼來捕捉 MSAL 記錄，並將它併入您自己的應用程式記錄中。 回呼的簽章（以目標-C 表示）如下所示：

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

根據預設，MSAL 不會捕捉或記錄任何個人資料（PII）。 程式庫可讓應用程式開發人員透過 MSALLogger 類別中的屬性來開啟此功能。 藉由開啟`pii.Enabled`，應用程式會負責安全地處理高度敏感的資料，並遵循法規需求。

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>記錄層級

若要在使用 iOS 和 macOS 的 MSAL 記錄時設定記錄層級，請使用下列其中一個值：

|層級  |描述 |
|---------|---------|
| `MSALLogLevelNothing`| 停用所有記錄 |
| `MSALLogLevelError` | 預設層級，只有在發生錯誤時才會列印資訊 |
| `MSALLogLevelWarning` | 警告 |
| `MSALLogLevelInfo` |  程式庫進入點，包含參數和各種 keychain 作業 |
|`MSALLogLevelVerbose`     |  API 追蹤 |

例如：

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>記錄訊息格式

MSAL 記錄訊息的訊息部分格式為`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

例如：

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

提供相互關聯識別碼和時間戳記有助於追蹤問題。 時間戳記和相互關聯識別碼資訊可在記錄訊息中取得。 唯一可取得的可靠位置是從 MSAL 記錄訊息。

## <a name="java"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>適用于 JAVA 記錄的 MSAL

MSAL for JAVA 可讓您使用已在應用程式中使用的記錄程式庫，前提是它與 SLF4J 相容。 MSAL for JAVA 會使用 JAVA （SLF4J）的[簡單記錄外觀](http://www.slf4j.org/)作為各種記錄架構的簡單外觀或抽象概念，例如[util。記錄](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html)、 [Logback](http://logback.qos.ch/)和[Log4j](https://logging.apache.org/log4j/2.x/)。 SLF4J 可讓使用者在部署階段插入所需的記錄架構。

例如，若要在您的應用程式中使用 Logback 作為記錄架構，請將 Logback 相依性新增至應用程式的 Maven pom 檔案：

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

然後新增 Logback 設定檔：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J 會在部署階段自動系結至 Logback。 MSAL 記錄將會寫入主控台。

如需如何系結至其他記錄架構的指示，請參閱[SLF4J 手冊](http://www.slf4j.org/manual.html)。

### <a name="personal-and-organization-information"></a>個人和組織資訊

根據預設，MSAL 記錄不會捕捉或記錄任何個人或組織的資料。 在下列範例中，記錄個人或組織的資料預設為關閉：

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

在用戶端應用程式產生器上設定`logPii()` ，以開啟個人和組織資料記錄。 如果您開啟個人或組織資料記錄，您的應用程式必須負責安全地處理高度敏感的資料，並遵守任何法規需求。

在下列範例中，會啟用記錄個人或組織資料：

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="python"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>適用于 Python 記錄的 MSAL

MSAL Python 中的記錄會使用標準的 Python 記錄機制， `logging.info("msg")`例如，您可以依照下列方式設定 MSAL 記錄（並在[username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)中查看其運作方式）：

### <a name="enable-debug-logging-for-all-modules"></a>啟用所有模組的偵錯工具記錄

根據預設，會關閉任何 Python 腳本中的記錄功能。 如果您想要針對整個 Python 腳本中的所有模組啟用 debug 記錄，請使用：

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>僅無聲 MSAL 記錄

若只要 MSAL 程式庫記錄，同時在 Python 腳本的所有其他模組中啟用 debug 記錄，請關閉 MSAL Python 所使用的記錄器：

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Python 中的個人和組織資料

適用于 Python 的 MSAL 不會記錄個人資料或組織資料。 沒有可開啟或關閉個人或組織資料記錄的屬性。

您可以使用標準的 Python 記錄來記錄任何您想要的內容，但您必須負責安全地處理敏感性資料及遵循法規需求。

如需有關在 Python 中進行記錄的詳細資訊，請參閱 Python 的[記錄做法](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)。

---
