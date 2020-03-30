---
title: 登錄 MSAL 應用 |蔚藍
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084885"
---
# <a name="logging-in-msal-applications"></a>在 MSAL 應用程式中登錄

Microsoft 身份驗證庫 （MSAL） 應用生成日誌消息，以説明診斷問題。 應用程式可透過幾行程式碼來設定記錄，並針對詳細資料層級和是否要記錄個人和組織資料具有自訂控制。 我們建議您創建 MSAL 日誌記錄回檔，並提供一種方法，當使用者遇到身份驗證問題時提交日誌。

## <a name="logging-levels"></a>記錄層級

MSAL 提供了多個級別的日誌記錄詳細資訊：

- 錯誤：指示出現問題並建置錯誤。 用來進行偵錯及識別問題。
- 警告： 不一定存在錯誤或故障，但用於診斷和查明問題。
- 資訊：MSAL 將記錄用於資訊目的的事件，而不需要用於調試。
- 詳細：預設值。 MSAL 記錄庫行為的完整詳細資訊。

## <a name="personal-and-organizational-data"></a>個人和組織資料

預設情況下，MSAL 記錄器不會捕獲任何高度敏感的個人或組織資料。 如果您決定這樣做，該庫提供了啟用記錄個人和組織資料的選項。

有關使用特定語言進行 MSAL 日誌記錄的詳細資訊，請選擇與您的語言匹配的選項卡：

## <a name="net"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>MSAL.NET 中的記錄

 > [!NOTE]
 > 有關MSAL.NET日誌記錄等示例，請參閱[MSAL.NET wiki。](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

在 MSAL 3.x 中，記錄必須在每個應用程式的建立期間，使用 `.WithLogging` 建立器修飾詞針對該應用程式進行個別設定。 此方法可接受選擇性參數：

- `Level`使您能夠決定所需的日誌記錄級別。 將它設定為 Errors 將只會取得錯誤
- `PiiLoggingEnabled`使您能夠記錄個人和組織資料（如果設置為 true）。 此參數預設為 false，這會使您的應用程式不會記錄個人資料。
- `LogCallback`設置為執行日誌記錄的委託。 如果`PiiLoggingEnabled`為 true，此方法將接收兩次消息：一次參數`containsPii`等於 false，消息不帶個人資料，第二次使用`containsPii`參數等於 true，並且消息可能包含個人資料。 在某些情況下 (當訊息不包含個人資料時)，這兩個訊息將會相同。
- `DefaultLoggingEnabled`啟用平臺的預設日誌記錄。 根據預設，它是 false。 如果您將它設定為 true，它會使用桌面/UWP 應用程式中的事件追蹤、iOS 上的 NSLog，以及 Android 上的 logcat。

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

## <a name="logging-in-msal-for-android-using-java"></a>使用 JAVA 登錄 Android 的 MSAL

通過創建日誌記錄回檔在應用創建時打開日誌記錄。 回檔採用以下參數：

- `tag`是庫傳遞給回檔的字串。 它與日誌條目相關聯，可用於對日誌記錄消息進行排序。
- `logLevel`使您能夠決定所需的日誌記錄級別。 支援的日誌級別是： `Error`、、`Info``Verbose``Warning`和 。
- `message`是日誌條目的內容。
- `containsPII`指定是否記錄包含個人資料的郵件或組織資料。 預設情況下，這設置為 false，以便應用程式不會記錄個人資料。 如果是`containsPII``true`，此方法將接收兩次消息：`containsPII`一次將參數設置為`false`，`message`而不包含個人資料，第二次使用`containsPii`參數設置為`true`和消息可能包含個人資料。 在某些情況下 (當訊息不包含個人資料時)，這兩個訊息將會相同。

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

預設情況下，MSAL 記錄器不會捕獲任何個人可識別資訊或組織可識別資訊。
要啟用個人可識別資訊或組織可識別資訊的日誌記錄：

```java
Logger.getInstance().setEnablePII(true);
```

要禁用記錄個人資料和組織資料：

```java
Logger.getInstance().setEnablePII(false);
```

預設情況下，將禁用日誌記錄以登錄。 要啟用：

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascript"></a>[JAVAscript](#tab/javascript)

 通過在配置期間傳遞記錄器物件來創建實例，從而在 MSAL.js （JavaScript）`UserAgentApplication`中啟用日誌記錄。 此記錄器物件具有下列屬性：

- `localCallback`：開發人員可以提供的回檔實例，用於以自訂方式使用和發佈日誌。 請依您想要將記錄重新導向的方式實作 localCallback 方法。
- `level`（可選）：可配置的日誌級別。 支援的日誌級別是： `Error`、、`Info``Verbose``Warning`和 。 預設值為 `Info`。
- `piiLoggingEnabled`（可選）：如果設置為 true，則記錄個人和組織資料。 預設情況下，這是錯誤的，以便您的應用程式不會記錄個人資料。 個人資料記錄永遠不會被寫入如 Console、Logcat 或 NSLog 等的預設輸出。
- `correlationId`（可選）：唯一識別碼，用於映射請求與回應以進行調試。 預設為 RFC4122 4 版 guid (128 位元)。

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

## <a name="objective-c"></a>[目標C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>適用于 iOS 和 macOS 日誌記錄-ObjC 的 MSAL

設置回檔以捕獲 MSAL 日誌記錄並將其合併到您自己的應用程式的日誌記錄中。 回檔的簽名如下所示：

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

預設情況下，MSAL 不會捕獲或記錄任何個人資料 （PII）。 該庫允許應用開發人員通過 MSALLogger 類中的屬性打開此功能。 通過打開`pii.Enabled`，應用程式負責安全處理高度敏感的資料並遵循法規要求。

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>記錄層級

要在使用 MSAL 進行 iOS 和 macOS 進行登錄時設置日誌記錄級別，請使用以下值之一：

|層級  |描述 |
|---------|---------|
| `MSALLogLevelNothing`| 禁用所有日誌記錄 |
| `MSALLogLevelError` | 預設級別，僅在發生錯誤時列印資訊 |
| `MSALLogLevelWarning` | 警告 |
| `MSALLogLevelInfo` |  庫進入點，具有參數和各種鑰匙串操作 |
|`MSALLogLevelVerbose`     |  API 追蹤 |

例如：

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>日誌消息格式

MSAL 日誌消息的消息部分的格式為`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

例如：

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

提供相關標識和時間戳記有助於跟蹤問題。 時間戳記和相關 ID 資訊在日誌消息中可用。 唯一可靠的位置從 MSAL 日誌記錄消息中檢索它們。

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>適用于 iOS 和 macOS 日誌記錄的 MSAL-Swift

設置回檔以捕獲 MSAL 日誌記錄並將其合併到您自己的應用程式的日誌記錄中。 回檔的簽名（在目標 C 中表示）如下所示：

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

預設情況下，MSAL 不會捕獲或記錄任何個人資料 （PII）。 該庫允許應用開發人員通過 MSALLogger 類中的屬性打開此功能。 通過打開`pii.Enabled`，應用程式負責安全處理高度敏感的資料並遵循法規要求。

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>記錄層級

要在使用 MSAL 進行 iOS 和 macOS 進行登錄時設置日誌記錄級別，請使用以下值之一：

|層級  |描述 |
|---------|---------|
| `MSALLogLevelNothing`| 禁用所有日誌記錄 |
| `MSALLogLevelError` | 預設級別，僅在發生錯誤時列印資訊 |
| `MSALLogLevelWarning` | 警告 |
| `MSALLogLevelInfo` |  庫進入點，具有參數和各種鑰匙串操作 |
|`MSALLogLevelVerbose`     |  API 追蹤 |

例如：

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>日誌消息格式

MSAL 日誌消息的消息部分的格式為`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

例如：

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

提供相關標識和時間戳記有助於跟蹤問題。 時間戳記和相關 ID 資訊在日誌消息中可用。 唯一可靠的位置從 MSAL 日誌記錄消息中檢索它們。

## <a name="java"></a>[JAVA](#tab/java)

## <a name="msal-for-java-logging"></a>用於 JAVA 日誌記錄的 MSAL

JAVA 的 MSAL 允許您使用已與應用一起使用的日誌記錄庫，只要它與 SLF4J 相容。 JAVA 的 MSAL 使用[JAVA 的簡單日誌記錄門面](http://www.slf4j.org/)（SLF4J） 作為各種日誌記錄框架的簡單外觀或抽象，例如[java.util.log、Logback](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html)和[Log4j](https://logging.apache.org/log4j/2.x/)。 [Logback](http://logback.qos.ch/) SLF4J 允許使用者在部署時插入所需的日誌記錄框架。

例如，要在應用程式中使用 Logback 作為日誌記錄框架，請向應用程式的 Maven pom 檔添加日誌返回依賴項：

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

然後添加記錄備份設定檔：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J 在部署時自動綁定到日誌。 MSAL 日誌將寫入主控台。

有關如何綁定到其他日誌記錄框架的說明，請參閱[SLF4J 手冊](http://www.slf4j.org/manual.html)。

### <a name="personal-and-organization-information"></a>個人和組織資訊

預設情況下，MSAL 日誌記錄不會捕獲或記錄任何個人或組織資料。 在以下示例中，預設情況下，記錄個人或組織資料處於關閉狀態：

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

通過在用戶端應用程式產生器上設置`logPii()`來打開個人和組織資料日誌記錄。 如果您打開個人或組織資料日誌記錄，則應用必須負責安全處理高度敏感的資料並遵守任何法規要求。

在以下示例中，啟用日誌記錄個人或組織資料：

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="python"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>用於 Python 日誌記錄的 MSAL

在 MSAL Python 中日誌記錄使用標準的 Python`logging.info("msg")`日誌記錄機制，例如，您可以按如下方式配置 MSAL 日誌記錄（並在[username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)中查看它的操作）：

### <a name="enable-debug-logging-for-all-modules"></a>為所有模組啟用調試日誌記錄

預設情況下，任何 Python 腳本中的日誌記錄都處於關閉狀態。 如果要對整個 Python 腳本中的所有模組啟用調試日誌記錄，請使用：

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>僅靜默 MSAL 日誌記錄

要僅靜默 MSAL 庫日誌記錄，同時在 Python 腳本中所有其他模組中啟用調試日誌記錄，請關閉 MSAL Python 使用的記錄器：

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Python 中的個人和組織資料

Python 的 MSAL 不記錄個人資料或組織資料。 沒有屬性可以打開或關閉個人或組織資料日誌記錄。

您可以使用標準 Python 日誌記錄來記錄所需的任何內容，但您有責任安全地處理敏感性資料和遵守法規要求。

有關在 Python 中登錄的詳細資訊，請參閱 Python 的[日誌記錄 HOWTO](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)。

---
