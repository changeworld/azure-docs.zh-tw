---
title: 在 MSAL for Android 中記錄錯誤和例外狀況。
titleSuffix: Microsoft identity platform
description: 瞭解如何在適用于 Android 的 MSAL 中記錄錯誤和例外狀況。
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
ms.openlocfilehash: b5d11c1bd091e3d7fbe5be87004b91f242e807f5
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763356"
---
# <a name="logging-in-msal-for-android"></a>Android 版 MSAL 中的記錄

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="logging-in-msal-for-android-using-java"></a>使用 JAVA 登入適用于 Android 的 MSAL

藉由建立記錄回呼，在建立應用程式時開啟記錄。 回呼會採用下列參數：

- `tag` 是程式庫傳遞給回呼的字串。 它會與記錄專案相關聯，而且可以用來排序記錄訊息。
- `logLevel` 可讓您決定想要的記錄層級。 支援的記錄層級為： `Error` 、 `Warning` 、 `Info` 和 `Verbose` 。
- `message` 這是記錄專案的內容。
- `containsPII` 指定是否記錄包含個人資料或組織資料的訊息。 根據預設，這會設定為 false，讓您的應用程式不會記錄個人資料。 如果 `containsPII` 為 `true` ，則這個方法會接收訊息兩次：一次將 `containsPII` 參數設定為 `false` ，而不使用 `message` 個人資料，第二次將 `containsPii` 參數設定為，而 `true` 訊息可能包含個人資料。 在某些情況下 (當訊息不包含個人資料時)，這兩個訊息將會相同。

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

根據預設，MSAL 記錄器不會捕捉任何個人識別資訊或組織標識資訊。
若要啟用個人識別資訊或組織可識別資訊的記錄：

```java
Logger.getInstance().setEnablePII(true);
```

若要停用記錄個人資料和組織資料：

```java
Logger.getInstance().setEnablePII(false);
```

依預設，記錄至 logcat 已停用。 若要啟用：

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="next-steps"></a>後續步驟

如需更多程式碼範例，請參閱 [Microsoft 身分識別平臺程式碼範例) ](sample-v2-code.md)。