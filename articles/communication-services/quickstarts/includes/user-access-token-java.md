---
title: 包含檔案
description: 包含檔案
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: de578ec286a8232ee8d4e259b2f37fb76101f7a5
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506212"
---
## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) 第 8 版或更新版本。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- 已部署的 Azure 通訊服務資源和連接字串。 [建立通訊服務資源](../create-communication-resource.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-java-application"></a>建立新的 Java 應用程式

開啟您的終端機或命令視窗。 瀏覽至您要在其中建立 Java 應用程式的目錄。 執行下列命令，以從 maven-archetype-quickstart 範本產生 Java 專案。

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
    <version>1.0.0-beta.2</version> 
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
import com.azure.communication.administration.*;
import com.azure.communication.common.*;
import java.io.*;
import java.util.*;
import java.time.*;

import com.azure.core.http.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-client"></a>驗證用戶端

使用資源的存取金鑰和端點，將 `CommunicationIdentityClient` 具現化。 了解如何[管理資源的連接字串](../create-communication-resource.md#store-your-connection-string)。

將下列程式碼加入 `main` 方法：

```java
// Your can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
// -> Add "import com.azure.core.http.netty.*;"
// -> Add azure-core-http-netty dependency to file pom.xml

HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationClientCredential(accessKey))
    .httpClient(httpClient)
    .buildClient();
```

您可以使用會實作 `com.azure.core.http.HttpClient` 介面的任何自訂 HTTP 用戶端來初始化用戶端。 上述程式碼示範如何使用 `azure-core` 所提供的 [Azure Core Netty HTTP 用戶端](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true)。

## <a name="create-an-identity"></a>建立身分識別

Azure 通訊服務會維護輕量身分識別目錄。 使用 `createUser` 方法，在目錄中建立具有唯一 `Id` 的新項目。 儲存已接收的身分識別，並對應至您應用程式的使用者。 例如，將其儲存在您應用程式伺服器的資料庫中。 後續將需要以身分識別簽發存取權杖。

```java
CommunicationUser identity = communicationIdentityClient.createUser();
System.out.println("\nCreated an identity with ID: " + identity.getId());
```

## <a name="issue-access-tokens"></a>發行存取權杖

使用 `issueToken` 方法來簽發現有通訊服務身分識別的存取權杖。 參數 `scopes` 會定義一組基本類型，進行此存取權杖的授權。 請參閱[支援的動作清單](../../concepts/authentication.md)。 您可以根據 Azure 通訊服務身分識別的字串表示法，建立參數 `user` 的新執行個體。

```java
// Issue an access token with the "voip" scope for an identity
List<String> scopes = new ArrayList<>(Arrays.asList("voip"));
CommunicationUserToken response = communicationIdentityClient.issueToken(identity, scopes);
OffsetDateTime expiresOn = response.getExpiresOn();
String token = response.getToken();
String identityId = response.getUser().getId();
System.out.println("\nIssued a access token with 'voip' scope for identity with ID: " + identityId + ": " + token);
```

存取權杖是需要重新簽發的短期認證。 若未這麼做，可能會導致應用程式的使用者體驗中斷。 `expiresAt` 回應屬性會指出存取權杖的存留期。

## <a name="refresh-access-tokens"></a>重新整理存取權杖

若要重新整理存取權杖，請使用 `CommunicationUser` 物件來重新簽發：

```java  
// Value existingIdentity represents identity of Azure Communication Services stored during identity creation
CommunicationUser identity = new CommunicationUser(existingIdentity);
response = communicationIdentityClient.issueToken(identity, scopes);
```

## <a name="revoke-access-tokens"></a>撤銷存取權杖

在某些情況下，您可能要明確地撤銷存取權杖。 例如，當應用程式的使用者變更用來向您的服務進行驗證的密碼時。 方法 `revokeTokens` 會使簽發給身分識別的所有作用中存取權杖失效。

```java  
communicationIdentityClient.revokeTokens(identity, OffsetDateTime.now());
System.out.println("\nRevoked access tokens for the user with ID: " + identity.getId());
```

## <a name="delete-an-identity"></a>刪除身分識別

刪除身分識別會撤銷所有作用中的存取權杖，並防止您核發身分識別的存取權杖。 此外也會移除所有與身分識別相關聯的保存內容。

```java
communicationIdentityClient.deleteUser(identity);
System.out.println("\nSuccessfully deleted the identity with ID: " + identity.getId());
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
