---
title: 連接到 Azure 媒體服務 v3 API - JAVA
description: 本文介紹如何使用 JAVA 連接到 Azure 媒體服務 v3 API。
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
ms.openlocfilehash: 6b0f21c3fa7a9c827f7201f4b899a33ea77eaf08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888490"
---
# <a name="connect-to-media-services-v3-api---java"></a>連接到媒體服務 v3 API - JAVA

本文介紹如何使用服務主體登錄方法連接到 Azure 媒體服務 v3 JAVA SDK。

在本文中，視覺化工作室代碼用於開發示例應用。

## <a name="prerequisites"></a>Prerequisites

- 按照[使用視覺化工作室代碼編寫 JAVA](https://code.visualstudio.com/docs/java/java-tutorial)來安裝：

   - JDK
   - Apache Maven
   - JAVA 擴展包
- 請確保設置`JAVA_HOME`和環境`PATH`變數。
- [創建媒體服務帳戶](create-account-cli-how-to.md)。 請務必記住資源組名稱和媒體服務帳戶名稱。
- 按照[訪問 API](access-api-cli-how-to.md)主題中的步驟操作。 記錄後續步驟中所需的訂閱 ID、應用程式 ID（用戶端 ID）、身份驗證金鑰（機密）和租戶 ID。

還要查看：

- [視覺化工作室代碼中的 JAVA](https://code.visualstudio.com/docs/languages/java)
- [VS 代碼中的 JAVA 專案管理](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> 查看[命名約定](media-services-apis-overview.md#naming-conventions)。

## <a name="create-a-maven-project"></a>建立 Maven 專案

打開命令列工具，並`cd`打開要創建專案的目錄。
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

運行該命令時，`pom.xml`將創建`App.java`、 和其他檔。 

## <a name="add-dependencies"></a>新增相依性

1. 在視覺化工作室代碼中，打開專案所在的資料夾
1. 查找並打開`pom.xml`
1. 添加所需的依賴項

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

1. 打開下`App.java``src\main\java\com\azure\ams`的檔，並確保您的包包含在頂部：

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
1. 要創建需要發出請求的活動目錄憑據，請向 App 類的主方法添加以下代碼，並設置從[Access API](access-api-cli-how-to.md)獲得的值：
   
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
- [JAVA SDK](https://aka.ms/ams-v3-java-sdk)
- [JAVA 引用](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01：azure-mgmt-媒體](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>後續步驟

您現在可以包括`import com.microsoft.azure.management.mediaservices.v2018_07_01.*;`並開始操作實體。

有關更多代碼示例，請參閱[JAVA SDK 示例](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/)回購。
