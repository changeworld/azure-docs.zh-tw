---
title: 連線到 Azure 媒體服務 v3 API - Java
description: 本文說明如何使用 Java 來連線到 Azure 媒體服務 v3 API。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/31/2020
ms.custom: devx-track-java
ms.author: inhenkel
ms.openlocfilehash: 8f5044a5fb43956e857bc7b93b92e15d40225326
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92013231"
---
# <a name="connect-to-media-services-v3-api---java"></a>連線到媒體服務 v3 API - Java

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文說明如何使用服務主體登入方法來連線到 Azure 媒體服務 v3 Java SDK。

在本文中，Visual Studio Code 用來開發範例應用程式。

## <a name="prerequisites"></a>先決條件

- 請遵循[透過 Visual Studio Code 撰寫 Java](https://code.visualstudio.com/docs/java/java-tutorial) 以安裝：

   - JDK
   - Apache Maven
   - Java 擴充套件
- 請務必設定 `JAVA_HOME` 和 `PATH` 環境變數。
- [建立媒體服務帳戶](./create-account-howto.md)。 請務必記住資源群組名稱和「媒體服務」帳戶名稱。
- 請依照[存取 API](./access-api-howto.md) 主題中的步驟進行。 記錄訂用帳戶識別碼、應用程式識別碼 (用戶端識別碼)、驗證金鑰 (秘密)，以及您在稍後步驟中需要的租用戶識別碼。

同時檢閱：

- [Visual Studio Code 中的 Java](https://code.visualstudio.com/docs/languages/java)
- [VS Code 中的 Java 專案管理](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> 檢閱[命名慣例](media-services-apis-overview.md#naming-conventions)。

## <a name="create-a-maven-project"></a>建立 Maven 專案

開啟命令列工具並 `cd` 至您要在其中建立專案的目錄。
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

當您執行命令時，會建立 `pom.xml`、`App.java` 和其他檔案。 

## <a name="add-dependencies"></a>新增相依性

1. 在 Visual Studio Code 中，開啟專案所在的資料夾
1. 尋找並開啟 `pom.xml`
1. 新增所需的相依性

    ```xml
   <dependency>
     <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
     <artifactId>azure-mgmt-media</artifactId>
     <version>1.0.0-beta-3</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.rest</groupId>
     <artifactId>client-runtime</artifactId>
     <version>1.6.6</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-client-authentication</artifactId>
     <version>1.6.6</version>
   </dependency>
    ```

## <a name="connect-to-the-java-client"></a>連線到 Java 用戶端

1. 開啟 `src\main\java\com\azure\ams` 之下的 `App.java` 檔案，並確定您的套件包含在最上方：

    ```java
    package com.azure.ams;
    ```
1. 在 package 陳述式下，新增下列 import 陳述式：
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. 若要建立您提出要求所需的 Active Directory 認證，請將下列程式碼新增至 App 類別的 Main 方法並設定您從[存取 API](./access-api-howto.md) 取得的值：
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. 執行應用程式。

## <a name="see-also"></a>另請參閱

- [媒體服務概念](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Java 參考](/java/api/overview/azure/mediaservices/management)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>後續步驟

您現在可以包含 `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` 並開始操作實體。

如需更多程式碼範例，請參閱 [Java SDK 範例](/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/)存放庫。