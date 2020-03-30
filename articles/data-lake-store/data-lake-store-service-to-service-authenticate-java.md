---
title: 服務到服務身份驗證 - 資料存儲第 1 代 = JAVA SDK
description: 了解如何使用 Java 透過 Azure Active Directory 向 Azure Data Lake Storage Gen1 完成服務對服務驗證
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: f355da7cd9c035b4ed0845bbd374a93bfb4a7350
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904546"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>使用 Java 向 Azure Data Lake Storage Gen1 進行服務對服務驗證

> [!div class="op_single_selector"]
> * [使用 Java](data-lake-store-service-to-service-authenticate-java.md)
> * [使用 .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [使用 Python](data-lake-store-service-to-service-authenticate-python.md)
> * [使用 REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

在本文中，您會了解如何使用 Java SDK 向 Azure Data Lake Store 進行服務對服務驗證。 不支援使用 Java SDK 向 Data Lake Storage Gen1 進行使用者驗證。

## <a name="prerequisites"></a>Prerequisites

* **Azure 訂閱**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* **建立 Azure Active Directory "Web" 應用程式**。 您必須已經完成[使用 Azure Active Directory 向 Data Lake Storage Gen1 進行服務對服務驗證](data-lake-store-service-to-service-authenticate-using-active-directory.md)中的步驟。

* [馬文](https://maven.apache.org/install.html). 本教學課程使用 Maven 來處理組建和專案相依性。 儘管無需使用 Maven 或 Gradle 等生成系統即可生成，但這些系統使得管理依賴項變得更加容易。

* (選擇性) [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) 或 [Eclipse](https://www.eclipse.org/downloads/) 或類似的 IDE。

## <a name="service-to-service-authentication"></a>服務對服務驗證

1. 從命令列或透過 IDE，使用 [mvn 原型](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)建立 Maven 專案。 如需有關如何使用 IntelliJ 建立 Java 專案的指示，請參閱[這裡](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html)。 如需有關如何使用 Eclipse 建立專案的指示，請參閱[這裡](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm)。

2. 將下列相依性新增至 Maven **pom.xml** 檔案。 在**\</project>** 標記之前添加以下程式碼片段：

        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.2.3</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>

    第一個相依性是使用來自 maven 存放庫的 Data Lake Storage Gen1 SDK (`azure-data-lake-store-sdk`)。 第二個相依性是指定要用於此應用程式的記錄架構 (`slf4j-nop`)。 Data Lake Storage Gen1 SDK 會使用 [slf4j](https://www.slf4j.org/) 記錄外觀，讓您從數個常用的記錄架構中進行選擇，例如 log4j、Java 記錄、logback 等，或是不予記錄。 在此範例中，我們停用記錄，因此會使用 **slf4j-nop** 繫結。 若要在應用程式中使用其他記錄選項，請參閱[這裡](https://www.slf4j.org/manual.html#projectDep)。

3. 在應用程式中新增下列 import 陳述式。

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

4. 在 Java 應用程式中使用下列程式碼片段，來為您稍早使用 `AccessTokenProvider` 的其中一個子類別 (下列範例使用 `ClientCredsTokenProvider`) 建立的 Active Directory Web 應用程式取得權杖。 權杖提供者會快取認證，以便用來取得記憶體中的權杖，以及自動更新即將過期的權杖。 您可以建立自己的 `AccessTokenProvider` 子類別，讓您的客戶程式碼取得權杖。 現在，我們只要使用 SDK 提供的子類別即可。

    以 Azure Active Directory Web 應用程式的實際值取代 **FILL-IN-HERE**。

        private static String clientId = "FILL-IN-HERE";
        private static String authTokenEndpoint = "FILL-IN-HERE";
        private static String clientKey = "FILL-IN-HERE";
    
        AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   

Data Lake Storage Gen1 SDK 提供簡便的方法，讓您管理與 Data Lake Storage Gen1 帳戶互動所需的安全性權杖。 不過，SDK 不會要求只能使用這些方法。 您也可以使用任何其他方法來取得權杖，像是使用 [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java)，或您自己的自訂程式碼。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何使用 Java SDK，使用使用者驗證向 Data Lake Storage Gen1 進行驗證。 您現在可以看看下列文章，了解如何配合使用 Java SDK 與 Data Lake Storage Gen1。

* [使用 Java SDK 對 Data Lake Storage Gen1 進行資料作業](data-lake-store-get-started-java-sdk.md)
