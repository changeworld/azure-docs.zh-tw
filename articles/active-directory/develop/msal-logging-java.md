---
title: 在 MSAL for JAVA 中記錄錯誤和例外狀況
titleSuffix: Microsoft identity platform
description: 瞭解如何在 MSAL for JAVA 中記錄錯誤和例外狀況
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
ms.openlocfilehash: d3fa13a6751b2d8acf1fc99aecbf174f1823fb0b
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954913"
---
# <a name="logging-in-msal-for-java"></a>Java 版 MSAL 中的記錄

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-java-logging"></a>適用于 JAVA 記錄的 MSAL

適用于 JAVA 的 MSAL 可讓您使用您已在應用程式中使用的記錄程式庫，前提是它與 SLF4J 相容。 適用于 java 的 MSAL 會使用 [簡單的記錄外觀](http://www.slf4j.org/) (SLF4J) 作為各種記錄架構的簡單外觀或抽象，例如 [util。記錄](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html)、 [Logback](http://logback.qos.ch/) 和 [Log4j](https://logging.apache.org/log4j/2.x/)。 SLF4J 可讓使用者在部署時插入所需的記錄架構。

例如，若要在您的應用程式中使用 Logback 作為記錄架構，請將 Logback 相依性新增至您應用程式的 Maven pom 檔案：

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

SLF4J 會在部署時自動系結至 Logback。 MSAL 記錄會寫入主控台。

如需如何系結至其他記錄架構的指示，請參閱 [SLF4J 手冊](http://www.slf4j.org/manual.html)。

### <a name="personal-and-organization-information"></a>個人和組織資訊

根據預設，MSAL 記錄不會捕捉或記錄任何個人或組織資料。 在下列範例中，記錄個人或組織資料預設為關閉：

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

在用戶端應用程式產生器上設定，以開啟個人和組織資料記錄 `logPii()` 。 如果您開啟個人或組織資料記錄，您的應用程式必須負責安全地處理高度敏感的資料，並遵守任何法規需求。

下列範例會啟用記錄個人或組織資料：

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="next-steps"></a>下一步

如需更多程式碼範例，請參閱 [Microsoft 身分識別平臺程式碼範例](sample-v2-code.md)。