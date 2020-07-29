---
title: 使用 Spring Boot 應用程式中的動態組態
titleSuffix: Azure App Configuration
description: 了解如何動態更新 Spring Boot 應用程式的組態資料
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.custom: devx-track-java
ms.author: lcozzens
ms.openlocfilehash: a2864d610d71c6b3a86c131dabb3c0b9ed138bec
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327917"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>教學課程：使用 Java Spring 應用程式中的動態組態

應用程式組態 Spring Boot 用戶端程式庫可支援依需求更新一組組態設定，而不會造成應用程式重新啟動。 用戶端程式庫會快取每個設定，以避免呼叫太多組態存放區。 在快取值到期前，重新整理作業都不會更新值，即使值在設定存放區中已變更也是如此。 每個要求的預設到期時間為 30 秒， 可以視需要加以覆寫。

您可以呼叫 `AppConfigurationRefresh` 的 `refreshConfigurations()` 方法，依需求檢查更新後的設定。

或者，您可以使用 `spring-cloud-azure-appconfiguration-config-web` 套件，這會相依於 `spring-web` 以處理自動重新整理。

## <a name="use-automated-refresh"></a>使用自動重新整理

若要使用自動重新整理，請從使用應用程式組態的 Spring Boot 應用程式開始；例如遵循[應用程式組態的 Spring Boot 快速入門](quickstart-java-spring-app.md)所建立的應用程式。

接著，在文字編輯器中開啟 pom.xml  檔案，並為`spring-cloud-azure-appconfiguration-config-web` 新增 `<dependency>`。

**Spring Cloud 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

**Spring Cloud 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

儲存檔案，然後如常建置並執行您的應用程式。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已啟用 Spring Boot 應用程式，以動態方式從應用程式組態重新整理組態設定。 若要了解如何使用 Azure 受控服務識別來簡化對應用程式組態的存取，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [受控識別整合](./howto-integrate-azure-managed-service-identity.md)
