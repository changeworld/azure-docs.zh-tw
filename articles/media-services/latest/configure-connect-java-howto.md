---
title: 連接到 Azure 媒體服務 v3 API-JAVA
description: 本文說明如何使用 JAVA 連接到 Azure 媒體服務 v3 API。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: 2337a7543f84d7eba5f565d875129cf22e8b27ed
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001208"
---
# <a name="connect-to-media-services-v3-api---java"></a>連接到媒體服務 v3 API-JAVA

本文說明如何使用服務主體登入方法來連線到 Azure 媒體服務 v3 JAVA SDK。

在本文中，Visual Studio Code 是用來開發範例應用程式。

## <a name="prerequisites"></a>先決條件

- 遵循[使用 Visual Studio Code 撰寫 JAVA](https://code.visualstudio.com/docs/java/java-tutorial)以安裝：

   - JDK
   - Apache Maven
   - JAVA 擴充功能套件
- 請務必設定 `JAVA_HOME` 和 `PATH` 環境變數。
- [建立媒體服務帳戶](./create-account-howto.md)。 請務必記住資源組名和媒體服務帳戶名稱。
- 依照[存取 api](./access-api-howto.md)主題中的步驟進行。 記錄訂用帳戶識別碼、應用程式識別碼（用戶端識別碼）、驗證金鑰（密碼），以及您在稍後步驟中需要的租使用者識別碼。

另請參閱：

- [Visual Studio Code 中的 JAVA](https://code.visualstudio.com/docs/languages/java)
- [VS Code 中的 JAVA 專案管理](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> 檢查[命名慣例](media-services-apis-overview.md#naming-conventions)。

## <a name="create-a-maven-project"></a>建立 Maven 專案

開啟命令列工具，以及 `cd` 您要建立專案的目錄。
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

當您執行命令時， `pom.xml` `App.java` 會建立、和其他檔案。 

## <a name="add-dependencies"></a>新增相依性

1. 在 Visual Studio Code 中，開啟您的專案所在的資料夾
1. 尋找並開啟`pom.xml`
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

## <a name="connect-to-the-java-client"></a>連接到 JAVA 用戶端

1. 開啟 `App.java` 下的檔案 `src\main\java\com\azure\ams` ，並確定您的套件包含在頂端：

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
1. 若要建立您提出要求所需的 Active Directory 認證，請將下列程式碼新增至 App 類別的 main 方法，並設定您從[存取 api](./access-api-howto.md)取得的值：
   
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
- [JAVA 參考](https://aka.ms/ams-v3-java-ref)
- [windowsazure.mediaservices.extensions。 v2018_07_01： azure-管理-媒體](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>接下來的步驟

您現在可以包含 `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` 並開始操作實體。

如需更多程式碼範例，請參閱[JAVA SDK 範例](/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/)存放庫。
