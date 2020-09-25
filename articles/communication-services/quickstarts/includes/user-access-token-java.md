---
title: 包含檔案
description: 包含檔案
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 1235cb299fe887f20dd3f7e47c22eed2b9df6dc9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943883"
---
## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) 第 8 版或更新版本。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- 已部署的 Azure 通訊服務資源和連接字串。 [建立通訊服務資源](../create-communication-resource.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-java-application"></a>建立新的 Java 應用程式

開啟您的終端機或命令視窗，然後瀏覽至您想要在其中建立 Java 應用程式的目錄。 執行下列命令，以從 maven-archetype-quickstart 範本產生 Java 專案。

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

您會發現 'generate' 工作建立了與 `artifactId` 同名的目錄。 在此目錄下，src/main/java 目錄包含專案原始程式碼，`src/test/java directory` 目錄包含測試來源，而 `pom.xml` 檔案是專案的「專案物件模型」(簡稱 POM)。

### <a name="install-the-package"></a>安裝套件

在文字編輯器中開啟 **pom.xml** 檔案。 將下列相依性元素加入至相依性群組。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-administration</artifactId>
    <version>1.0.0-beta.1</version> 
</dependency>
```

### <a name="set-up-the-app-framework"></a>設定應用程式架構

從專案目錄：

1. 瀏覽至 /src/main/java/com/communication/quickstart 目錄
1. 在編輯器中開啟 *App.java* 檔案
1. 取代 `System.out.println("Hello world!");` 陳述式
1. 新增 `import` 指示詞

使用下列程式碼開始作業：

```java
import com.azure.communication.common.CommunicationUser;
import com.azure.communication.administration.models.CommunicationIdentityToken;
import com.azure.communication.administration.CommunicationIdentityClient;
import com.azure.communication.administration.CommunicationIdentityClientBuilder;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - User Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>驗證用戶端

使用資源的存取金鑰和端點，將 `CommunicationIdentityClient` 具現化。 了解如何[管理資源的連接字串](../create-communication-resource.md#store-your-connection-string)。

將下列程式碼加入 `main` 方法：

```java
// Your can find your endpoint and access token from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessToken = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationClientCredential(accessToken))
    .httpClient(httpClient)
    .buildClient();
```

您可以使用會實作 `com.azure.core.http.HttpClient` 介面的任何自訂 HTTP 用戶端來初始化用戶端。 上述程式碼示範如何使用 `azure-core` 所提供的 [Azure Core Netty HTTP 用戶端](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true)。

## <a name="create-a-user"></a>建立使用者

Azure 通訊服務會維護輕量身分識別目錄。 使用 `createUser` 方法，在目錄中建立具有唯一 `Id` 的新項目。 您應該維護應用程式使用者與通訊服務所產生身分識別之間的對應 (例如，將其儲存在應用程式伺服器的資料庫中)。

```java
CommunicationUser user = communicationIdentityClient.createUser();
System.out.println("\nCreated a user with ID: " + user.getId());
```

## <a name="issue-user-access-tokens"></a>發行使用者存取權杖

使用 `issueToken` 方法來發行通訊服務使用者的存取權杖。 如果您未提供選擇性的 `user` 參數，則會建立新的使用者，並連同權杖一起傳回。

```java
// Issue an access token with the "voip" scope for a new user
List<String> scopes = new ArrayList<>(Arrays.asList("voip"));
CommunicationUserToken response = communicationIdentityClient.issueToken(user, scopes);
OffsetDateTime expiresOn = response.getExpiresOn();
String token = response.getToken();
String userId = response.getUser().getId();
System.out.println("\nIssued a access token with 'voip' scope for identity with ID: " + userId + ": " + token);
System.out.println(token);
```

使用者存取權杖是短期的認證，需要重新發行才能防止使用者發生服務中斷。 `expiresAt` 回應屬性會指出權杖的存留期。

## <a name="revoke-user-access-tokens"></a>撤銷使用者存取權杖

在某些情況下，您可能需要明確撤銷使用者存取權杖，例如，當使用者變更用來向服務進行驗證的密碼時。 這會使用 `revokeTokens` 方法，讓所有使用者的存取權杖失效。

```java  
communicationIdentityClient.revokeTokens(user, OffsetDateTime.now());
System.out.println("\nRevoked tokens for the user with ID: " + user.getId());
```

## <a name="delete-a-user"></a>刪除使用者

刪除使用者會撤銷所有作用中的權杖，並防止您發行身分識別的後續權杖。 也會移除所有與使用者相關聯的保存內容。

```java
communicationIdentityClient.deleteUser(user);
System.out.println("\nSuccessfully deleted the identity with ID: " + user.getId());
```

## <a name="run-the-code"></a>執行程式碼

瀏覽至包含 *pom.xml* 檔案的目錄，然後使用下列 `mvn` 命令來編譯專案。

```console
mvn compile
```

接著，建置封裝。

```console
mvn package
```

執行下列 `mvn` 命令以執行應用程式。

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
