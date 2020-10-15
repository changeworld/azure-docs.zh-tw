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
ms.openlocfilehash: 653fcb6f6590fd503a97800ec8196025cf14a3b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88121556"
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
    <version>1.1.5</version>
</dependency>
```

**Spring Cloud 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.7</version>
</dependency>
```

## <a name="run-and-test-the-app-locally"></a>在本機執行和測試應用程式

1. 使用 Maven 建置 Spring Boot 應用程式並加以執行。

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. 開啟瀏覽器視窗並移至 URL：`http://localhost:8080`。  您會看到與金鑰相關聯的訊息。 

    您也可以使用 *curl* 來測試您的應用程式，例如： 
    
    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. 若要測試動態設定，請開啟與應用程式相關聯的 Azure 應用程式組態入口網站。 選取 [組態總管]，然後更新所顯示金鑰的值，例如：
    | 機碼 | 值 |
    |---|---|
    | application/config.message | Hello - 已更新 |

1. 重新整理瀏覽器頁面，以查看所顯示的新訊息。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已啟用 Spring Boot 應用程式，以動態方式從應用程式組態重新整理組態設定。 若要了解如何使用 Azure 受控服務識別來簡化對應用程式組態的存取，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [受控識別整合](./howto-integrate-azure-managed-service-identity.md)
