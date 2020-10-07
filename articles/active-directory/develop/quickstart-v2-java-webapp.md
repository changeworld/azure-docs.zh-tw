---
title: Microsoft 身分識別平台 Java Web 應用程式快速入門 | Azure
description: 了解如何使用 OpenID Connect 在 Java Web 應用程式上實作 Microsoft 登入
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
ms.openlocfilehash: d1b79d60bba89ef01b261c403fe3b25939669d0b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258093"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>快速入門：將「使用 Microsoft 登入」新增至 Java Web 應用程式

在本快速入門中，您將了解如何整合 Java Web 應用程式與 Microsoft 身分識別平台。 您的應用程式會讓使用者登入、取得存取權杖來呼叫 Microsoft Graph API，以及對 Microsoft Graph API 提出要求。

完成本快速入門後，您的應用程式會接受使用個人Microsoft 帳戶 (包括 outlook.com、live.com 和其他帳戶)，以及採用 Azure Active Directory 的公司或組織所提供的公司或學校帳戶登入。 (如需圖例，請參閱[此範例的運作方式](#how-the-sample-works)。)

## <a name="prerequisites"></a>必要條件

若要執行此範例，您將需要：

- [Java 開發套件 (JDK)](https://openjdk.java.net/) 8 或更新版本，以及 [Maven](https://maven.apache.org/)。

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>註冊並下載快速入門應用程式
> 有兩個選項可用來啟動快速入門應用程式：快速 (選項 1) 或手動 (選項 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>選項 1：註冊和自動設定您的應用程式，然後下載程式碼範例
>
> 1. 移至 [Azure 入口網站 - 應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs)快速入門體驗。
> 1. 輸入應用程式的名稱，並選取 [註冊]。
> 1. 遵循入口網站快速入門體驗中的指示，下載自動設定的應用程式程式碼。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>選項 2：註冊並手動設定您的應用程式和程式碼範例
>
> #### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式
>
> 若要註冊您的應用程式，並手動將應用程式註冊資訊新增到您的應用程式，請執行下列步驟：
>
> 1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
> 1. 如果您的帳戶可讓您存取多個租用戶，請在右上角選取帳戶，然後將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
>
> 1. 瀏覽至 Microsoft 身分識別平台，以取得開發人員的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面。
> 1. 選取 [新增註冊]。
> 1. 當 [註冊應用程式] 頁面出現時，輸入您應用程式的註冊資訊：
>    - 在 [名稱] 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱，例如 `java-webapp`。
>    - 選取 [註冊]。
> 1. 在 [概觀] 頁面上，尋找應用程式的 [應用程式 (用戶端) 識別碼] 和 [目錄 (租用戶) 識別碼] 值。 請複製這些值以供後續使用。
> 1. 從功能表中選取 [驗證]，然後新增下列資訊：
>    - 新增 **Web** 平台組態。  將這些 `https://localhost:8443/msal4jsample/secure/aad` 和 `https://localhost:8443/msal4jsample/graph/me` 新增為**重新導向 URI**。
>    - 選取 [儲存]。
> 1. 從功能表中選取 [憑證和秘密]，然後在 [用戶端密碼] 區段中，按一下 [新增用戶端密碼]：
>
>    - 輸入 (執行個體應用程式祕密的) 金鑰描述。
>    - 選取 [1 年] 作為金鑰持續時間。
>    - 當您選取 [新增] 時，將會顯示金鑰值。
>    - 複製金鑰的值以供後續使用。 此金鑰值不會再次顯示，也無法透過任何其他方式來擷取，因此，請於此值出現在 Azure 入口網站後就立即記錄下來。
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>步驟 1:在 Azure 入口網站中設定您的應用程式
>
> 若要讓本快速入門中的程式碼範例能正常運作，您需要：
>
> 1. 將回覆 URL 新增為 `https://localhost:8443/msal4jsample/secure/aad` 和 `https://localhost:8443/msal4jsample/graph/me`
> 1. 建立用戶端密碼。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [為我進行這些變更]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已設定](media/quickstart-v2-aspnet-webapp/green-check.png) 您的應用程式已設定了這些屬性。

#### <a name="step-2-download-the-code-sample"></a>步驟 2:下載程式碼範例
> [!div renderon="docs"]
> [下載程式碼範例](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> 下載專案，並將 zip 檔案解壓縮至根資料夾附近的本機資料夾 - 例如 **C:\Azure-Samples**
>
> 若要搭配使用 https 與 localhost，請填入 server.ssl.key 屬性。 若要產生自我簽署憑證，請使用 keytool 公用程式 (隨附於 JRE)。
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   將產生的金鑰儲存區檔案放在「資源」資料夾中。

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [下載程式碼範例](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>步驟 3：設定程式碼範例
> 1. 將 ZIP 檔案解壓縮至本機資料夾。
> 1. 如果您使用整合式開發環境，請在您慣用的 IDE 中開啟範例 (選用)。
> 1. 開啟位於 src/main/resources/ 資料夾中的 application.properties 檔案，並將 *aad.clientId*、*aad.authority* 和 *aad.secretKey* 欄位的值分別取代為 [應用程式識別碼]、[租用戶識別碼] 和 [用戶端密碼] 的值，如下所示：
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
> 其中：
>
> - `Enter_the_Application_Id_here` - 是您註冊的應用程式所具備的應用程式識別碼。
> - `Enter_the_Client_Secret_Here` - 是您在 [憑證與祕密] 中為您所註冊的應用程式建立的 [用戶端密碼]。
> - `Enter_the_Tenant_Info_Here` - 是您所註冊之應用程式的 [目錄 (租用戶) 識別碼] 值。
> 1. 若要搭配使用 https 與 localhost，請填入 server.ssl.key 屬性。 若要產生自我簽署憑證，請使用 keytool 公用程式 (隨附於 JRE)。
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   將產生的金鑰儲存區檔案放在「資源」資料夾中。


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>步驟 3：執行程式碼範例
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>步驟 4：執行程式碼範例

若要執行專案，您可以：

使用內嵌的 Spring Boot 伺服器直接從您的 IDE 執行專案，或使用 [maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) 將專案封裝到 WAR 檔案，然後將其部署至 J2EE 容器解決方案，例如 [Apache Tomcat](http://tomcat.apache.org/)。

##### <a name="running-from-ide"></a>從 IDE 執行

如果您從 IDE 執行 Web 應用程式，請按一下 [執行]，然後瀏覽至專案的首頁。 在此範例中，標準首頁 URL 為 https://localhost:8443

1. 在首頁上選取 [登入] 按鈕，以重新導向至 Azure Active Directory，並提示使用者輸入其認證。

1. 使用者通過驗證後，會重新導向至 *https://localhost:8443/msal4jsample/secure/aad* 。 他們現在已登入，且頁面將會顯示登入帳戶的相關資訊。 範例 UI 有下列按鈕：
    - 登出：從應用程式登出目前的使用者，並將他們重新導向至首頁。
    - *顯示使用者資訊*：取得 Microsoft Graph 的權杖，並使用包含該權杖的要求呼叫 Microsoft Graph，這會傳回與已登入的使用者有關的基本資訊。

##### <a name="running-from-tomcat"></a>從 Tomcat 執行

如果您想將 web 範例部署至 Tomcat，必須對原始程式碼進行幾項變更。

1. 開啟 ms-identity-java-webapp/pom.xml
    - 在 `<name>msal-web-sample</name>` 下新增 `<packaging>war</packaging>`

2. 開啟 ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication

    - 刪除所有原始程式碼，並以下列程式碼取代：

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

3.   Tomcat 的預設 HTTP 連接埠是 8080，但需要透過連接埠 8443 的 HTTPS 連線。 若要進行此設定：
        - 前往 tomcat/conf/server.xml
        - 搜尋 `<connector>` 標記，並將現有的連接器取代為：

        ```xml
        <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
        ```

4. 開啟命令提示字元，移至此範例的根資料夾 (pom.xml 檔案所在的位置)，然後執行 `mvn package` 以建立專案
    - 這會在您的 /targets 目錄中產生 `msal-web-sample-0.1.0.war` 檔案。
    - 將此檔案重新命名為 `msal4jsample.war`。
    - 使用 Tomcat 或任何其他 J2EE 容器解決方案部署此 War 檔案。
        - 若要進行部署，請將 msal4jsample.war 檔案複製到 Tomcat 安裝中的 `/webapps/` 目錄，然後啟動 Tomcat 伺服器。

5. 一旦部署後，請在瀏覽器中移至 https://localhost:8443/msal4jsample


> [!IMPORTANT]
> 此快速入門應用程式會使用用戶端密碼，將自己識別為機密用戶端。 由於用戶端密碼會以純文字形式新增至您的專案檔，因此，基於安全考量，在考慮將應用程式當作生產應用程式之前，建議您使用憑證，而非用戶端密碼。 如需如何使用憑證的詳細資訊，請參閱[適用於應用程式驗證的憑證認證](./active-directory-certificate-credentials.md)。

## <a name="more-information"></a>詳細資訊

### <a name="how-the-sample-works"></a>此範例的運作方式
![示範本快速入門所產生之範例應用程式的運作方式](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="getting-msal"></a>取得 MSAL

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

### <a name="msal-initialization"></a>MSAL 初始化

將下列程式碼新增至要使用 MSAL4J 的檔案頂端，以將參考新增至 MSAL for Java：

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>後續步驟

如需深度討論如何建置 Web 應用程式，在 Microsoft 身分識別平台上登入使用者，請移至我們的多部分案例系列：

> [!div class="nextstepaction"]
[案例：登入使用者的 Web 應用程式](scenario-web-app-sign-user-overview.md?tabs=java)
