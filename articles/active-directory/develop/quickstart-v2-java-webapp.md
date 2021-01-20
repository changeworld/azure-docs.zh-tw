---
title: 快速入門：將「使用 Microsoft 登入」新增至 Java Web 應用程式 | Azure
titleSuffix: Microsoft identity platform
description: 在本快速入門中，了解如何使用 OpenID Connect 將「使用 Microsoft 登入」新增至 Java Web 應用程式。
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 91aa6c96c714bff26ea7e0df5b2b6971c68edec0
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178563"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>快速入門：將「使用 Microsoft 登入」新增至 Java Web 應用程式

在本快速入門中，您會下載並執行程式碼範例，該範例會示範 Java Web 應用程式如何登入使用者並呼叫 Microsoft Graph API。 來自任何 Azure Active Directory (Azure AD) 組織的使用者都可以登入應用程式。

 如需概觀，請參閱[範例運作方式圖](#how-the-sample-works)。

## <a name="prerequisites"></a>必要條件

若要執行此範例，您需要：

- [Java 開發套件 (JDK)](https://openjdk.java.net/) 8 或更新版本。 
- [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>註冊並下載快速入門應用程式
> 有兩種方法可啟動快速入門應用程式：快速 (選項 1) 和手動 (選項 2)。
>
> ### <a name="option-1-register-and-automatically-configure-your-app-and-then-download-the-code-sample"></a>選項 1：註冊和自動設定您的應用程式，然後下載程式碼範例
>
> 1. 移至 <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs" target="_blank">Azure 入口網站 - 應用程式註冊<span class="docon docon-navigate-external x-hidden-focus"></span></a>快速入門體驗。
> 1. 輸入應用程式的名稱，然後選取 [註冊]。
> 1. 遵循入口網站快速入門體驗中的指示，下載自動設定的應用程式程式碼。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>選項 2：註冊並手動設定您的應用程式和程式碼範例
>
> #### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式
>
> 若要註冊您的應用程式並手動新增應用程式的註冊資訊，請執行下列步驟：
>
> 1. 登入 <a href="https://portal.azure.com/" target="_blank">Azure 入口網站<span class="docon docon-navigate-external x-hidden-focus"></span></a>。
> 1. 如果您有多個租用的存取權，請使用頂端功能表中的 **目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: 來選取要在其中註冊應用程式的租用戶。
> 1. 搜尋並選取 [Azure Active Directory]。
> 1. 在 [管理]  底下選取 [應用程式註冊]  。
> 1. 選取 [新增註冊]  。
> 1. 輸入您應用程式的 [名稱]，例如 **java-webapp**。 您應用程式的使用者可能會看到此名稱。 您可以稍後再變更。
> 1. 選取 [註冊]。
> 1. 在 [概觀] 頁面上，記下 [應用程式 (用戶端) 識別碼] 和 [目錄 (租用戶) 識別碼]。 您稍後將需要這些值。
> 1. 在 [管理] 底下，選取 [驗證]。
> 1. 選取 [新增平台] > [Web]。
> 1. 在 [重新導向 URI] 區段中，輸入 `https://localhost:8443/msal4jsample/secure/aad`。
> 1. 選取 [設定] 。
> 1. 在 [Web] 區段的 [重新導向 URI] 底下，輸入 `https://localhost:8443/msal4jsample/graph/me` 作為第二個重新導向 URI。
> 1. 在 [管理]  下，選取 [憑證和密碼]  。 在 [用戶端密碼] 區段中，選取 [新增用戶端密碼]。
> 1. 輸入金鑰描述 (例如「應用程式秘密」)、保留預設到期日，然後選取 [新增]。
> 1. 請記下用戶端秘密的 [值]。 稍後您將會用到此資訊。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>步驟 1:在 Azure 入口網站中設定您的應用程式
>
> 若要在本快速入門中使用程式碼範例，您需要：
>
> 1. 新增回覆 URL `https://localhost:8443/msal4jsample/secure/aad` 和 `https://localhost:8443/msal4jsample/graph/me`。
> 1. 建立用戶端秘密。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [為我進行這些變更]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已設定](media/quickstart-v2-aspnet-webapp/green-check.png) 您的應用程式已設定了這些屬性。

#### <a name="step-2-download-the-code-sample"></a>步驟 2:下載程式碼範例
> [!div renderon="docs"]
> [下載程式碼範例](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> 下載專案並將 .zip 檔解壓縮至磁碟機根目錄附近的資料夾。 例如，*C:\Azure-Samples*。
>
> 若要使用 HTTPS 搭配 localhost，請提供 `server.ssl.key` 屬性。 若要產生自我簽署憑證，請使用 keytool 公用程式 (隨附於 JRE)。
>
> 以下為範例：
>  ```
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   將產生的金鑰儲存區檔案放在 *resources* 資料夾中。

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [下載程式碼範例](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>步驟 3：設定程式碼範例
> 1. 將 ZIP 檔案解壓縮至本機資料夾。
> 1. *選擇性。* 如果您使用整合式開發環境，請在該環境中開啟範例。
> 1. 開啟 application.properties 檔案。 您可以在 *src/main/resources/* 資料夾中找到該檔案。 分別以應用程式識別碼、租用戶識別碼和用戶端秘密值取代 `aad.clientId`、`aad.authority` 和 `aad.secretKey` 欄位中的值。 其應會顯示如下：
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
>    在先前的程式碼中：
>
>    - `Enter_the_Application_Id_here` 是您註冊的應用程式所具備的應用程式識別碼。
>    - `Enter_the_Client_Secret_Here` 是您在 [憑證與祕密] 中為您所註冊的應用程式建立的 [用戶端秘密]。
>    - `Enter_the_Tenant_Info_Here` 是您所註冊之應用程式的 [目錄 (租用戶) 識別碼] 值。
> 1. 若要使用 HTTPS 搭配 localhost，請提供 `server.ssl.key` 屬性。 若要產生自我簽署憑證，請使用 keytool 公用程式 (隨附於 JRE)。
>
>    以下為範例：
>
>     ```
>      keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>      server.ssl.key-store-type=PKCS12
>      server.ssl.key-store=classpath:keystore.p12
>      server.ssl.key-store-password=password
>      server.ssl.key-alias=testCert
>      ```
>   1. 將產生的金鑰儲存區檔案放在 *resources* 資料夾中。


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>步驟 3：執行程式碼範例
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>步驟 4：執行程式碼範例

若要執行專案，請採取下列其中一個步驟：

- 使用內嵌的 Spring Boot 伺服器，直接從您的 IDE 加以執行。 
- 使用 [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) 將其封裝到 WAR 檔案，然後將其部署到 J2EE 容器解決方案，例如 [Apache Tomcat](http://tomcat.apache.org/)。

##### <a name="running-the-project-from-an-ide"></a>從 IDE 執行專案

若要從 IDE 執行 Web 應用程式，請選取 [執行]，然後移至專案的首頁。 在此範例中，標準首頁 URL 為 https://localhost:8443 。

1. 在首頁上選取 [登入] 按鈕，以將使用者重新導向至 Azure Active Directory 並提示他們輸入認證。

1. 使用者經過驗證之後，系統會將其重新導向至 `https://localhost:8443/msal4jsample/secure/aad`。 他們現在已登入，且頁面將會顯示使用者帳戶的相關資訊。 範例 UI 具有下列按鈕：
    - 登出：從應用程式登出目前的使用者，並將該使用者重新導向至首頁。
    - **顯示使用者資訊**：取得 Microsoft Graph 的權杖，並使用包含該權杖的要求呼叫 Microsoft Graph，這會傳回與已登入的使用者有關的基本資訊。

##### <a name="running-the-project-from-tomcat"></a>從 Tomcat 執行專案

如果您想要將 Web 範例部署至 Tomcat，則必須對原始程式碼進行幾項變更。

1. 開啟 *ms-identity-java-webapp/pom.xml*。
    - 在 `<name>msal-web-sample</name>` 下新增 `<packaging>war</packaging>`。

2. 開啟 *ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication*。

    - 刪除所有原始程式碼，並以下列程式碼加以取代：

      ```Java
       package com.microsoft.azure.msalwebsample;

       import org.springframework.boot.SpringApplication;
       import org.springframework.boot.autoconfigure.SpringBootApplication;
       import org.springframework.boot.builder.SpringApplicationBuilder;
       import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

       @SpringBootApplication
       public class MsalWebSampleApplication extends SpringBootServletInitializer {

        public static void main(String[] args) {
         SpringApplication.run(MsalWebSampleApplication.class, args);
        }

        @Override
        protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
         return builder.sources(MsalWebSampleApplication.class);
        }
       }
      ```

3.   Tomcat 的預設 HTTP 連接埠是 8080，但您需要透過連接埠 8443 的 HTTPS 連線。 若要進行此設定：
        - 前往 *tomcat/conf/server.xml*。
        - 搜尋 `<connector>` 標記，並以下列連結器取代現有的連接器：

          ```xml
          <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
          ```

4. 開啟命令提示字元視窗。 移至此範例的根資料夾 (pom.xml 檔案所在的位置)，然後執行 `mvn package` 以建立專案。
    - 此命令將會在您的 */targets* 目錄中產生 *msal-web-sample-0.1.0.war* 檔案。
    - 將此檔案重新命名為 *msal4jsample.war*。
    - 使用 Tomcat 或任何其他 J2EE 容器解決方案來部署 WAR 檔案。
        - 若要部署 msal4jsample.war 檔案，請將其複製到 Tomcat 安裝中的 */webapps/* 目錄，然後啟動 Tomcat 伺服器。

5. 部署該檔案之後，請使用瀏覽器移至 https://localhost:8443/msal4jsample 。


> [!IMPORTANT]
> 此快速入門應用程式會使用用戶端秘密，將自己識別為機密用戶端。 由於用戶端秘密會以純文字形式新增至您的專案檔，因此基於安全考量，在生產環境中使用應用程式之前，建議您使用憑證，而非用戶端秘密。 如需如何使用憑證的詳細資訊，請參閱[適用於應用程式驗證的憑證認證](./active-directory-certificate-credentials.md)。

## <a name="more-information"></a>詳細資訊

### <a name="how-the-sample-works"></a>此範例的運作方式
![此圖顯示本快速入門所產生範例應用程式的運作方式。](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="get-msal"></a>取得 MSAL

MSAL for Java (MSAL4J) 是 Java 程式庫，用來登入使用者，以及要求用來對 Microsoft 身分識別平台所保護的 API 進行存取的權杖。

請對應用程式中的 pom.xml (Maven) 或 build.gradle (Gradle) 檔案進行下列變更，以使用 Maven 或 Gradle 來管理您的相依性，進而將 MSAL4J 新增至您的應用程式。

在 pom.xml 中：

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

在 build.gradle 中：

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="initialize-msal"></a>將 MSAL 初始化

將下列程式碼新增在要使用 MSAL4J 的檔案開頭，以新增 MSAL for Java 的參考：

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>後續步驟

如需深度討論如何建置 Web 應用程式，在 Microsoft 身分識別平台上登入使用者，請參閱多部分案例系列：

> [!div class="nextstepaction"]
> [案例：登入使用者的 Web 應用程式](scenario-web-app-sign-user-overview.md?tabs=java)
