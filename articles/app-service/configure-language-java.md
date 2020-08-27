---
title: 設定 Windows JAVA 應用程式
description: 瞭解如何設定 JAVA 應用程式，以便在 Azure App Service 中的 Windows VM 實例上執行。 本文說明最常見的設定工作。
keywords: azure app service，web 應用程式，windows，oss，java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 5d94da91428da2270e0f690df4dcd43ae43d8597
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961647"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>設定適用于 Azure App Service 的 JAVA 應用程式

::: zone pivot="platform-windows"  

Azure App Service 可讓 JAVA 開發人員在完全受控的 Windows 服務上快速建立、部署及調整其 Tomcat web 應用程式。 從命令列或編輯器 (例如 IntelliJ、Eclipse 或 Visual Studio Code)，使用 Maven 外掛程式部署應用程式。

本指南提供 JAVA 開發人員在 App Service 中使用的重要概念和指示。 如果您從未使用過 Azure App Service，則應該先閱讀 [JAVA 快速入門](quickstart-java.md) 。 有關使用非 JAVA 開發特定 App Service 的一般問題，請在 [App Service WINDOWS 常見問題](faq-configuration-and-management.md)中獲得解答。

## <a name="deploying-your-app"></a>部署應用程式

您可以使用 [適用于 Maven 的 Azure Web 應用程式外掛程式](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) 來部署 war 檔案。 [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) 或 [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse)也支援使用熱門 Ide 進行部署。

否則部署方法將會取決於您的封存類型：

- 若要將 .war 檔案部署至 Tomcat，請使用 `/api/wardeploy/` 端點透過 POST 張貼您的封存檔案。 如需有關此 API 的詳細資訊，請參閱[這份文件](./deploy-zip.md#deploy-war-file)。
- 若要將 .jar 檔案部署到 JAVA SE，請使用 `/api/zipdeploy/` Kudu 網站的端點。 如需有關此 API 的詳細資訊，請參閱[這份文件](./deploy-zip.md#rest)。

請勿使用 FTP 來部署 .war 或 .jar。 FTP 工具是為上傳啟動指令碼、相依性或其他執行階段檔案而設計的。 它不是部署網頁的最佳選擇。

## <a name="logging-and-debugging-apps"></a>記錄和偵錯應用程式

透過 Azure 入口網站，可以取得每個應用程式的效能報表、流量視覺化和健康狀態檢查。 如需詳細資訊，請參閱 [Azure App Service 診斷概觀](overview-diagnostics.md)。

### <a name="use-flight-recorder"></a>使用飛行記錄器

使用 Azul Jvm 的 App Service 上的所有 JAVA 執行時間都隨附于祖魯文飛行記錄器。 您可以使用此記錄來記錄 JVM、系統和 JAVA 層級的事件，以監視 JAVA 應用程式中的行為和疑難排解問題。

若要進行計時記錄，您將需要 JAVA 應用程式的 PID (處理序識別碼) 。 若要尋找 PID，請在 HTTPs://<您的網站名稱>. scm.azurewebsites.net/ProcessExplorer/中，開啟 web 應用程式的 SCM 網站瀏覽器。 此頁面會顯示您的 web 應用程式中正在執行的進程。 在資料表中尋找名為 "java" 的進程，並將對應的 PID 複製 (處理序識別碼) 。

接下來，開啟 SCM 網站頂端工具列中的 **偵錯主控台** ，然後執行下列命令。 取代 `<pid>` 為您稍早複製的處理序識別碼。 此命令會啟動您的 JAVA 應用程式的30秒分析工具記錄，並 `timed_recording_example.jfr` 在目錄中產生名為的檔案 `D:\home` 。

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

如需詳細資訊，請參閱 [Jcmd 命令參考](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)。

#### <a name="analyze-jfr-files"></a>分析 `.jfr` 檔案

使用 [FTPS](deploy-ftp.md)，將 JFR 檔案下載到本機電腦。 若要分析 JFR 檔案，請下載並安裝 [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/)。 如需有關 Zulu Mission Control 的指示，請參閱 [Azul 文件](https://docs.azul.com/zmc/)和[安裝指示](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)。

### <a name="stream-diagnostic-logs"></a>資料流診斷記錄

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

如需詳細資訊，請參閱 [Cloud Shell 中的串流處理記錄](troubleshoot-diagnostic-logs.md#in-cloud-shell)。

### <a name="app-logging"></a>應用程式記錄

透過 Azure 入口網站或 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) 啟用[應用程式記錄](troubleshoot-diagnostic-logs.md#enable-application-logging-windows)，設定 App Service 將應用程式的標準主控台輸出和標準主控台錯誤資料流寫入至本機檔案系統或 Azure Blob 儲存體。 設定後的 12 個小時會停用記錄至本機 App Service 檔案系統執行個體。 如果您需要較長的保留期，則請設定應用程式將輸出寫入至 Blob 儲存體容器。 您可以在 */LogFiles/Application/* 目錄中找到您的 JAVA 和 Tomcat 應用程式記錄檔。

如果您的應用程式使用 [Logback](https://logback.qos.ch/) 或 [Log4j](https://logging.apache.org/log4j) 追蹤，則您可以使用[在 Application Insights 中探索 Java 追蹤記錄](../azure-monitor/app/java-trace-logs.md)中的記錄架構設定指示，將這些要檢閱的追蹤轉送至 Azure Application Insights。


## <a name="customization-and-tuning"></a>自訂和調整

Azure App Service 透過 Azure 入口網站和 CLI 支援現成的微調和自訂。 請檢閱下列非 Java 特定 Web 應用程式設定的文章：

- [設定應用程式設定](configure-common.md#configure-app-settings)
- [設定自訂網域](app-service-web-tutorial-custom-domain.md)
- [設定 TLS 系結](configure-ssl-bindings.md)
- [新增 CDN](../cdn/cdn-add-to-web-app.md)
- [設定 Kudu 網站](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>設定 Java 執行階段選項

若要設定配置的記憶體或其他 JVM 執行時間選項，請使用選項來建立名為的 [應用程式設定](configure-common.md#configure-app-settings) `JAVA_OPTS` 。 App Service 會在啟動時將此設定作為環境變數傳遞給 JAVA 執行時間。

在 Azure 入口網站中，於 Web 應用程式的 [應用程式設定] 下，建立名為 `JAVA_OPTS` 且包含其他設定的新應用程式設定 (例如 `-Xms512m -Xmx1204m`)。

若要從 Maven 外掛程式設定應用程式設定，請在 Azure 外掛程式區段中新增設定/值標籤。 下列範例設定特定最小和最大 Java 堆積大小：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

執行具有其 App Service 方案中某個部署位置的單一應用程式開發人員，可以使用下列選項：

- B1 和 S1 執行個體： `-Xms1024m -Xmx1024m`
- B2 和 S2 執行個體： `-Xms3072m -Xmx3072m`
- B3 和 S3 執行個體： `-Xms6144m -Xmx6144m`

調整應用程式堆積設定時，請檢閱 App Service 方案詳細資料，並考慮多個應用程式和部署位置需求以尋找最佳的記憶體配置。

### <a name="turn-on-web-sockets"></a>開啟 Web 通訊端

在應用程式的 [應用程式設定] 中，開啟 Azure 入口網站中的 Web 通訊端支援。 您必須重新啟動應用程式，設定才會生效。

搭配使用 Azure CLI 與下列命令，以開啟 Web 通訊端支援：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

然後重新啟動您的應用程式：

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>設定預設字元編碼

在 Azure 入口網站中，於 Web 應用程式的 [應用程式設定] 下，建立名為 `JAVA_OPTS` 且值為 `-Dfile.encoding=UTF-8` 的新應用程式設定。

或者，您可以使用 App Service Maven 外掛程式來設定應用程式設定。 在外掛程式設定中新增設定名稱和值標籤：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>預先編譯 JSP 檔案

若要改善 Tomcat 應用程式的效能，您可以先編譯 JSP 檔案，再部署至 App Service。 您可以使用 Apache Sling 所提供的 [Maven 外掛程式](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html)，或使用此 [Ant 組建檔案](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)。

## <a name="secure-applications"></a>保護應用程式

在 App Service 中執行的 JAVA 應用程式，具有與其他應用程式相同的一組 [安全性最佳作法](../security/fundamentals/paas-applications-using-app-services.md) 。

### <a name="authenticate-users-easy-auth"></a>驗證使用者 (簡單驗證)

使用 [驗證與授權] 選項，設定 Azure 入口網站中的應用程式驗證。 在這裡，您可以使用 Azure Active Directory 或社交登入 (例如 Facebook、Google 或 GitHub) 來啟用驗證。 只有在設定單一驗證提供者時，Azure 入口網站設定才會運作。 如需詳細資訊，請參閱[設定 App Service 應用程式使用 Azure Active Directory 登入](configure-authentication-provider-aad.md)，以及其他身分識別提供者的相關文章。 如果您需要啟用多個登入提供者，請遵循[自訂 App Service 驗證](app-service-authentication-how-to.md)一文中的指示。

#### <a name="tomcat"></a>Tomcat

Tomcat 應用程式可以藉由將 Principal 物件轉換成 Map 物件，直接從 servlet 存取使用者的宣告。 Map 物件會將每個宣告類型對應到該類型的宣告集合。 在下列程式碼中，`request` 是 `HttpServletRequest` 的執行個體。

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

現在您可以檢查 `Map` 物件是否有任何特定的宣告。 例如，下列程式碼片段會逐一查看所有宣告類型，並列印每個集合的內容。

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

若要將使用者登出，請使用 `/.auth/ext/logout` 路徑。 若要執行其他動作，請參閱有關 [App Service 驗證和授權使用](./app-service-authentication-how-to.md)的文件。 另外，您也可以參閱有關 Tomcat [HttpServletRequest 介面](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html)及其方法的官方文件。 下列 servlet 方法也是根據 App Service 設定來更新：

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

若要停用此功能，請建立名為 `WEBSITE_AUTH_SKIP_PRINCIPAL` 的應用程式設定，其值為 `1`。 若要停用 App Service 新增的所有 servlet 篩選條件，請建立名為 `WEBSITE_SKIP_FILTERS` 的設定，其值為 `1`。

### <a name="configure-tlsssl"></a>設定 TLS/SSL

依照 Azure App Service 中的「 [使用 tls 系結保護自訂 DNS 名稱](configure-ssl-bindings.md) 」中的指示上傳現有的 TLS/SSL 憑證，並將其系結至應用程式的功能變數名稱。 根據預設，您的應用程式仍然允許 HTTP 連線；請遵循教學課程中的特定步驟，以強制執行 SSL 和 TLS。

### <a name="use-keyvault-references"></a>使用 KeyVault 參考

[Azure KeyVault](../key-vault/general/overview.md) 透過存取原則和稽核歷程記錄提供集中式祕密管理。 您可以在 KeyVault 中儲存秘密 (例如密碼或連接字串)，並透過環境變數在應用程式中存取這些秘密。

首先，請依照說明[授與應用程式對 Key Vault 的存取權限](app-service-key-vault-references.md#granting-your-app-access-to-key-vault)，並[在應用程式設定中建立對秘密的 KeyVault 參考](app-service-key-vault-references.md#reference-syntax)。 您可以在遠端存取 App Service 終端機時，列印環境變數，來驗證參考是否會解析為秘密。

若要將這些秘密插入 Spring 或 Tomcat 設定檔中，請使用環境變數插入語法 (`${MY_ENV_VAR}`)。 如需 Spring 設定檔，請參閱有關[外部化設定](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)的文件。


## <a name="configure-apm-platforms"></a>設定 APM 平台

本節說明如何在 Linux 上的 Azure App Service 中，將其中所部署的 Java 應用程式連線至 NewRelic 和 AppDynamics 應用程式效能監視 (APM) 平台。

### <a name="configure-new-relic"></a>設定 NewRelic

1. 在[NewRelic.com](https://newrelic.com/signup)建立新的 Relic 帳戶
2. 從 NewRelic 下載 Java 代理程式，其檔案名稱類似 [newrelic-java-x.x.x.zip]。
3. 複製您的授權金鑰，以供稍後設定代理程式時使用。
4. 使用 [Kudu 主控台](https://github.com/projectkudu/kudu/wiki/Kudu-console) 來建立新的目錄 */home/site/wwwroot/apm*。
5. 將解壓縮的新 Relic JAVA 代理程式檔案上傳至 */home/site/wwwroot/apm*底下的目錄。 代理程式檔案應位於 */home/site/wwwroot/apm/newrelic* 中。
6. 在 */home/site/wwwroot/apm/newrelic/newrelic.yml* 修改 YAML 檔案，並將預留位置授權值取代為您自己的授權金鑰。
7. 在 Azure 入口網站中，瀏覽至您在 App Service 中的應用程式，並建立新的應用程式設定。
    - 如果您的應用程式使用 **Java SE**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `JAVA_OPTS` 的環境變數。
    - 如果您使用 **Tomcat**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `CATALINA_OPTS` 的環境變數。

### <a name="configure-appdynamics"></a>設定 AppDynamics

1. 在 [AppDynamics.com](https://www.appdynamics.com/community/register/) 建立 AppDynamics 帳戶
2. 從 AppDynamics 網站下載 Java 代理程式，其檔案名稱會類似 *AppServerAgent-x.x.x.xxxxx.zip*
3. 使用 [Kudu 主控台](https://github.com/projectkudu/kudu/wiki/Kudu-console) 來建立新的目錄 */home/site/wwwroot/apm*。
4. 將 Java 代理程式檔案上傳至 */home/site/wwwroot/apm* 下的目錄。 代理程式檔案應位於 */home/site/wwwroot/apm/appdynamics* 中。
5. 在 Azure 入口網站中，瀏覽至您在 App Service 中的應用程式，並建立新的應用程式設定。
    - 如果您使用 **Java SE**，請使用 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 值建立名為 `JAVA_OPTS` 的環境變數，其中 `<app-name>` 是您的 App Service 名稱。
    - 如果您使用 **Tomcat**，請使用 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 值建立名為 `CATALINA_OPTS` 的環境變數，其中 `<app-name>` 是您的 App Service 名稱。

>  如果您已經有 `JAVA_OPTS` 或 `CATALINA_OPTS` 的環境變數，請將 `-javaagent:/...` 選項附加至目前值的結尾。

## <a name="data-sources"></a>資料來源

### <a name="tomcat"></a>Tomcat

這些指示適用於所有資料庫連線。 您將必須在預留位置中填入您所選資料庫的驅動程式類別名稱和 JAR 檔案。 下表提供常見資料庫的類別名稱和驅動程式下載。

| 資料庫   | 驅動程式類別名稱                             | JDBC 驅動程式                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [下載](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [下載](https://dev.mysql.com/downloads/connector/j/) (請選取 [Platform Independent] \(不受平台影響\)) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [下載](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

若要設定讓 Tomcat 使用「Java 資料庫連線」(JDBC) 或「Java 保存 API」(JPA)，請先自訂 Tomcat 在啟動時所讀入的 `CATALINA_OPTS` 環境變數。 請透過 [App Service Maven 外掛程式](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)中的應用程式設定來設定這些值：

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

或是在 Azure 入口網站的 [設定] > [應用程式設定] 頁面中設定環境變數。

接著，決定資料來源應僅供在 Tomcat Servlet 上執行的一個應用程式還是所有應用程式使用。

#### <a name="application-level-data-sources"></a>應用程式層級資料來源

1. 在專案的 *META-INF/* 目錄中，建立 *context.xml* 檔案。 建立 *META-INF/* 目錄 (如果此目錄不存在)。

2. 在 *context.xml* 中，新增 `Context` 元素以將資料來源連結至 JNDI 位址。 以上表中您驅動程式的類別名稱取代 `driverClassName` 預留位置。

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. 更新您應用程式的 *web.xml*，以使用您應用程式中的資料來源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>完成設定

最後，我們會將驅動程式 Jar 放在 Tomcat 類別路徑中，然後重新開機您的 App Service。 請確定 Tomcat classloader 可以使用 JDBC 驅動程式檔案，方法是將它們放在 [/home/tomcat/lib] 目錄中。 (如果此目錄尚未存在，請建立此目錄)。若要將這些檔案上傳至 App Service 執行個體，請執行下列步驟：

1. 在 [Cloud Shell](https://shell.azure.com) 中，安裝 webapp 擴充功能：

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. 執行下列 CLI 命令，以建立從本機系統到 App Service 的 SSH 通道：

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. 使用您的 SFTP 用戶端來連線至本機通道連接埠，然後將檔案上傳至 */home/tomcat/lib* 資料夾。

或著，您也可以使用 FTP 用戶端來上傳 JDBC 驅動程式。 請依照這些[指示來取得您的 FTP 認證](deploy-configure-credentials.md) \(英文\)。

## <a name="configuring-tomcat"></a>設定 Tomcat

若要編輯 Tomcat `server.xml` 或其他設定檔，請先記下您在入口網站中的 tomcat 主要版本。

1. 藉由執行命令來尋找您版本的 Tomcat 主目錄 `env` 。 搜尋以和開頭的環境變數，以 `AZURE_TOMCAT` 符合您的主要版本。 例如， `AZURE_TOMCAT85_HOME` 指向 tomcat 8.5 的 tomcat 目錄。
1. 一旦識別出您版本的 Tomcat 主目錄，請將設定目錄複寫到 `D:\home` 。 例如，如果的 `AZURE_TOMCAT85_HOME` 值為 `D:\Program Files (x86)\apache-tomcat-8.5.37` ，則複製的目錄的新路徑會是 `D:\home\apache-tomcat-8.5.37` 。

最後，重新啟動 App Service。 您的部署應該會 `D:\home\site\wwwroot\webapps` 像之前一樣地移至。

## <a name="configure-java-se"></a>設定 JAVA SE

執行時。在 Windows 上的 JAVA SE 上的 JAR 應用程式， `server.port` 會在應用程式啟動時作為命令列選項傳遞。 您可以從環境變數手動解析 HTTP 埠 `HTTP_PLATFORM_PORT` 。 此環境變數的值將會是您的應用程式應接聽的 HTTP 埠。 

## <a name="java-runtime-statement-of-support"></a>Java 執行階段支援聲明

### <a name="jdk-versions-and-maintenance"></a>JDK 版本和維護

Azure 支援的 Java Development Kit (JDK) 是透過 [Azul Systems](https://www.azul.com/) 提供的 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)。

主要版本更新將透過適用于 Windows Azure App Service 中的新執行時間選項提供。 客戶會更新為設定其 App Service 部署的這些新版 Java，以及負責測試並確定主要更新符合其需求。

每年 1 月、4 月、7 月和 10 月都會以季為單位自動修補支援的 JDK。 如需有關 Azure 上的 JAVA 的詳細資訊，請參閱 [這份支援檔](/azure/developer/java/fundamentals/java-jdk-long-term-support)。

### <a name="security-updates"></a>安全性更新

只要主要資訊安全漏洞的修補程式和修正程式在 Azul Systems 中變成可用，就予以發行。 在 [NIST Common Vulnerability Scoring System 第 2 版](https://nvd.nist.gov/vuln-metrics/cvss)上，「主要」弱點是由基本分數 9.0 或更大值定義。

Tomcat 8.0 已 [于2018年9月30日結束 (EOL) ](https://tomcat.apache.org/tomcat-80-eol.html)到期。 雖然執行時間仍可在 Azure App Service 上取得，但 Azure 不會將安全性更新套用至 Tomcat 8.0。 可能的話，請將您的應用程式遷移至 Tomcat 8.5 或9.0。 Tomcat 8.5 和9.0 都可在 Azure App Service 上取得。 如需詳細資訊，請參閱 [官方 Tomcat 網站](https://tomcat.apache.org/whichversion.html) 。 

### <a name="deprecation-and-retirement"></a>取代和淘汰

如果將淘汰支援的 Java 執行階段，則使用受影響執行階段的 Azure 開發人員將會在執行階段淘汰之前至少六個月收到淘汰通知。

### <a name="local-development"></a>本機開發

開發人員可以從 [Azul 的下載網站](https://www.azul.com/downloads/azure-only/zulu/)下載實際執行版本的 Azul Zulu Enterprise JDK，以進行本機開發。

### <a name="development-support"></a>開發支援

使用[合格的 Azure 支援方案](https://azure.microsoft.com/support/plans/)為 Azure 或[azure Stack](https://azure.microsoft.com/overview/azure-stack/)進行開發時，可透過 Microsoft 取得支援[AZURE 的 Azul 祖魯文 JDK](https://www.azul.com/downloads/azure-only/zulu/)產品。

### <a name="runtime-support"></a>執行階段支援

開發人員如果具有[合格的支援方案](https://azure.microsoft.com/support/plans/)，便可透過 Azure 支援服務，針對 Azul Zulu JDK [開立問題](../azure-portal/supportability/how-to-create-azure-support-request.md)。

## <a name="next-steps"></a>後續步驟

本主題針對 Windows 上的 Azure App Service 提供 JAVA 執行時間聲明支援。

- 若要深入瞭解如何使用 Azure App Service 來裝載 web 應用程式，請參閱 [App Service 總覽](overview.md)。
- 如需有關 Azure 開發 JAVA 的詳細資訊，請參閱適用 [于 JAVA 開發人員中心的 azure](/java/azure/?view=azure-java-stable)。

::: zone-end

::: zone pivot="platform-linux"

Linux 上的 Azure App Service 可讓 Java 開發人員在全受控 Linux 服務上，快速建置、部署和調整其 Tomcat 或 Java Standard Edition (SE) 封裝的 Web 應用程式。 從命令列或編輯器 (例如 IntelliJ、Eclipse 或 Visual Studio Code)，使用 Maven 外掛程式部署應用程式。

本指南為在 App Service 中使用內建 Linux 容器的 Java 開發人員提供了重要概念和指示。 如果您從未使用過 Azure App Service，請遵循 [JAVA 快速入門](quickstart-java.md)。

## <a name="deploying-your-app"></a>部署應用程式

您可以使用 [Azure App Service 的 Maven 外掛程式](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)來部署 .jar 和 .war 檔案。 [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) 或 [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse)也支援使用熱門 Ide 進行部署。

否則部署方法將會取決於您的封存類型：

- 若要將 .war 檔案部署至 Tomcat，請使用 `/api/wardeploy/` 端點透過 POST 張貼您的封存檔案。 如需有關此 API 的詳細資訊，請參閱[這份文件](./deploy-zip.md#deploy-war-file)。
- 若要部署 Java SE 映像上的 .jar 檔案，請使用 Kudu 網站的 `/api/zipdeploy/` 端點。 如需有關此 API 的詳細資訊，請參閱[這份文件](./deploy-zip.md#rest)。

請勿使用 FTP 來部署 .war 或 .jar。 FTP 工具是為上傳啟動指令碼、相依性或其他執行階段檔案而設計的。 它不是部署網頁的最佳選擇。

## <a name="logging-and-debugging-apps"></a>記錄和偵錯應用程式

透過 Azure 入口網站，可以取得每個應用程式的效能報表、流量視覺化和健康狀態檢查。 如需詳細資訊，請參閱 [Azure App Service 診斷概觀](overview-diagnostics.md)。

### <a name="ssh-console-access"></a>SSH 主控台存取

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>資料流診斷記錄

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

如需詳細資訊，請參閱 [Cloud Shell 中的串流處理記錄](troubleshoot-diagnostic-logs.md#in-cloud-shell)。

### <a name="app-logging"></a>應用程式記錄

透過 Azure 入口網站或 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) 啟用[應用程式記錄](troubleshoot-diagnostic-logs.md#enable-application-logging-windows)，設定 App Service 將應用程式的標準主控台輸出和標準主控台錯誤資料流寫入至本機檔案系統或 Azure Blob 儲存體。 設定後的 12 個小時會停用記錄至本機 App Service 檔案系統執行個體。 如果您需要較長的保留期，則請設定應用程式將輸出寫入至 Blob 儲存體容器。 JAVA 和 Tomcat 應用程式記錄可以在 */home/LogFiles/Application/* 目錄中找到。

>[!NOTE]
>在 12 小時後記錄到本機 App Service 檔案系統會停用的功能，僅適用於以 Windows 為基礎的應用程式服務。 僅能使用 [Azure 監視器 (預覽)](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview) 來設定以 Linux 為基礎應用程式服務的 Azure Blob 儲存體記錄 

如果您的應用程式使用 [Logback](https://logback.qos.ch/) 或 [Log4j](https://logging.apache.org/log4j) 追蹤，則您可以使用[在 Application Insights 中探索 Java 追蹤記錄](../azure-monitor/app/java-trace-logs.md)中的記錄架構設定指示，將這些要檢閱的追蹤轉送至 Azure Application Insights。

### <a name="troubleshooting-tools"></a>疑難排解工具

內建的 JAVA 映像是以 [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) 作業系統為基礎。 使用 `apk` 套件管理員來安裝疑難排解工具或命令。

### <a name="flight-recorder"></a>飛行記錄器

App Service 上的所有 Linux JAVA 映像都已安裝 Zulu Flight Recorder，因此您可以輕鬆連接到 JVM 並啟動分析工具記錄，或產生堆積傾印。

#### <a name="timed-recording"></a>計時錄製

首先使用 SSH 連線到 App Service，並執行 `jcmd` 命令，以查看所有執行中的 JAVA 流程清單。 除了 jcmd 本身之外，您還會看到具處理序識別碼 (pid) 且正在執行的 JAVA 應用程式。

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

執行下列命令，以啟動 JVM 的 30 秒記錄。 這會分析 JVM，並在主目錄中建立名為 *jfr_example JFR* 的 JFR 檔案。 (將 116 取代為您 JAVA 應用程式的 pid)。

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

在 30 秒的間隔期間內，您可以藉由執行 `jcmd 116 JFR.check` 來確認錄製是否正在進行。 這會顯示指定 JAVA 流程的所有錄製。

#### <a name="continuous-recording"></a>連續錄製

您可以在對執行時間效能的影響最小的情況下使用 Zulu Flight Recorder 來持續分析 JAVA 應用程式 ([來源](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf))。 若要這麼做，請執行下列 Azure CLI 命令，以使用必要的設定來建立名為 JAVA_OPTS 的應用程式設定。 當應用程式啟動時，會將 JAVA_OPTS 應用程式設定的內容傳遞至 `java` 命令。

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

記錄開始之後，您可以隨時使用 `JFR.dump` 命令傾印目前的記錄資料。

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

如需詳細資訊，請參閱 [Jcmd 命令參考](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)。

### <a name="analyzing-recordings"></a>分析錄製

使用 [FTPS](deploy-ftp.md)，將 JFR 檔案下載到本機電腦。 若要分析 JFR 檔案，請下載並安裝 [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/)。 如需有關 Zulu Mission Control 的指示，請參閱 [Azul 文件](https://docs.azul.com/zmc/)和[安裝指示](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)。

## <a name="customization-and-tuning"></a>自訂和調整

Azure App Service for Linux 支援透過 Azure 入口網站和 CLI 的預設調整和自訂。 請檢閱下列非 Java 特定 Web 應用程式設定的文章：

- [設定應用程式設定](configure-common.md#configure-app-settings)
- [設定自訂網域](app-service-web-tutorial-custom-domain.md)
- [設定 SSL 繫結](configure-ssl-bindings.md)
- [新增 CDN](../cdn/cdn-add-to-web-app.md)
- [設定 Kudu 網站](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>設定 Java 執行階段選項

若要在 Tomcat 和 Java SE 環境中設定已配置的記憶體或其他 JVM 執行階段選項，請建立[應用程式設定](configure-common.md#configure-app-settings)，以這些選項命名為 `JAVA_OPTS`。 App Service Linux 會在啟動時將此設定當成環境變數傳遞至 Java 執行階段。

在 Azure 入口網站中，於 Web 應用程式的 [應用程式設定] 下，建立名為 `JAVA_OPTS` 且包含其他設定的新應用程式設定 (例如 `-Xms512m -Xmx1204m`)。

若要從 Maven 外掛程式設定應用程式設定，請在 Azure 外掛程式區段中新增設定/值標籤。 下列範例設定特定最小和最大 Java 堆積大小：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

執行具有其 App Service 方案中某個部署位置的單一應用程式開發人員，可以使用下列選項：

- B1 和 S1 執行個體： `-Xms1024m -Xmx1024m`
- B2 和 S2 執行個體： `-Xms3072m -Xmx3072m`
- B3 和 S3 執行個體： `-Xms6144m -Xmx6144m`

調整應用程式堆積設定時，請檢閱 App Service 方案詳細資料，並考慮多個應用程式和部署位置需求以尋找最佳的記憶體配置。

如果您要部署 JAR 應用程式，應將其命名為 *app.jar*，讓內建映像能夠正確地識別應用程式。 (Maven 外掛程式會自動執行此重新命名)。如果您不想要將 JAR 重新命名為 *app.jar*，則可以使用命令上傳殼層指令碼來執行 JAR。 然後，在入口網站的 [設定] 區段中，將此指令碼的完整路徑貼到 [[啟動檔案](faq-app-service-linux.md#built-in-images)] 文字方塊中。 啟動指令碼不會從其放置所在的目錄來執行。 因此，請一律使用絕對路徑在啟動指令碼中參考檔案 (例如： `java -jar /home/myapp/myapp.jar`)。

### <a name="turn-on-web-sockets"></a>開啟 Web 通訊端

在應用程式的 [應用程式設定] 中，開啟 Azure 入口網站中的 Web 通訊端支援。 您必須重新啟動應用程式，設定才會生效。

搭配使用 Azure CLI 與下列命令，以開啟 Web 通訊端支援：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

然後重新啟動您的應用程式：

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>設定預設字元編碼

在 Azure 入口網站中，於 Web 應用程式的 [應用程式設定] 下，建立名為 `JAVA_OPTS` 且值為 `-Dfile.encoding=UTF-8` 的新應用程式設定。

或者，您可以使用 App Service Maven 外掛程式來設定應用程式設定。 在外掛程式設定中新增設定名稱和值標籤：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>調整啟動逾時

如果 JAVA 應用程式特別大，您應該提高啟動時間限制。 若要這麼做，請建立應用程式設定 `WEBSITES_CONTAINER_START_TIME_LIMIT`，並將其設定為 App Service 在逾時前應等待的秒數。最大值為 `1800` 秒。

### <a name="pre-compile-jsp-files"></a>預先編譯 JSP 檔案

若要改善 Tomcat 應用程式的效能，您可以先編譯 JSP 檔案，再部署至 App Service。 您可以使用 Apache Sling 所提供的 [Maven 外掛程式](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html)，或使用此 [Ant 組建檔案](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)。

## <a name="secure-applications"></a>保護應用程式

在 App Service for Linux 中執行之 Java 應用程式會有一組與其他應用程式相同的[安全性最佳做法](../security/fundamentals/paas-applications-using-app-services.md)。

### <a name="authenticate-users-easy-auth"></a>驗證使用者 (簡單驗證)

使用 [驗證與授權] 選項，設定 Azure 入口網站中的應用程式驗證。 在這裡，您可以使用 Azure Active Directory 或社交登入 (例如 Facebook、Google 或 GitHub) 來啟用驗證。 只有在設定單一驗證提供者時，Azure 入口網站設定才會運作。 如需詳細資訊，請參閱[設定 App Service 應用程式使用 Azure Active Directory 登入](configure-authentication-provider-aad.md)，以及其他身分識別提供者的相關文章。 如果您需要啟用多個登入提供者，請遵循[自訂 App Service 驗證](app-service-authentication-how-to.md)一文中的指示。

#### <a name="tomcat"></a>Tomcat

Tomcat 應用程式可以藉由將 Principal 物件轉換成 Map 物件，直接從 servlet 存取使用者的宣告。 Map 物件會將每個宣告類型對應到該類型的宣告集合。 在下列程式碼中，`request` 是 `HttpServletRequest` 的執行個體。

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

現在您可以檢查 `Map` 物件是否有任何特定的宣告。 例如，下列程式碼片段會逐一查看所有宣告類型，並列印每個集合的內容。

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

若要將使用者登出，請使用 `/.auth/ext/logout` 路徑。 若要執行其他動作，請參閱有關 [App Service 驗證和授權使用](./app-service-authentication-how-to.md)的文件。 另外，您也可以參閱有關 Tomcat [HttpServletRequest 介面](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html)及其方法的官方文件。 下列 servlet 方法也是根據 App Service 設定來更新：

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

若要停用此功能，請建立名為 `WEBSITE_AUTH_SKIP_PRINCIPAL` 的應用程式設定，其值為 `1`。 若要停用 App Service 新增的所有 servlet 篩選條件，請建立名為 `WEBSITE_SKIP_FILTERS` 的設定，其值為 `1`。

#### <a name="spring-boot"></a>Spring Boot

Spring Boot 開發人員可以使用 [Azure Active Directory Spring Boot 簡易版](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable)，以利用熟悉的 Spring Security 註釋和 API 來保護應用程式。 請務必要在 *application.properties* 檔案中增加標頭大小上限。 建議值為 `16384`。

### <a name="configure-tlsssl"></a>設定 TLS/SSL

請遵循[使用 Azure App Service 中的 SSL 繫結保護自訂 DNS 名稱](configure-ssl-bindings.md)中的指示，上傳現有的 SSL 憑證，並將其繫結至您應用程式的網域名稱。 根據預設，您的應用程式仍然允許 HTTP 連線；請遵循教學課程中的特定步驟，以強制執行 SSL 和 TLS。

### <a name="use-keyvault-references"></a>使用 KeyVault 參考

[Azure KeyVault](../key-vault/general/overview.md) 透過存取原則和稽核歷程記錄提供集中式祕密管理。 您可以在 KeyVault 中儲存秘密 (例如密碼或連接字串)，並透過環境變數在應用程式中存取這些秘密。

首先，請依照說明[授與應用程式對 Key Vault 的存取權限](app-service-key-vault-references.md#granting-your-app-access-to-key-vault)，並[在應用程式設定中建立對秘密的 KeyVault 參考](app-service-key-vault-references.md#reference-syntax)。 您可以在遠端存取 App Service 終端機時，列印環境變數，來驗證參考是否會解析為秘密。

若要將這些秘密插入 Spring 或 Tomcat 設定檔中，請使用環境變數插入語法 (`${MY_ENV_VAR}`)。 如需 Spring 設定檔，請參閱有關[外部化設定](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)的文件。

### <a name="using-the-java-key-store"></a>使用 JAVA 金鑰存放區

根據預設，系統會在容器啟動時將任何[上傳至 App Service Linux](configure-ssl-certificate.md) 的公開憑證或私人憑證載入個別的 JAVA 金鑰存放區。 上傳憑證後，您必須重新啟動 App Service，才能將其載入 JAVA 金鑰存放區。 系統會將公開憑證載入位於 `$JAVA_HOME/jre/lib/security/cacerts` 的金鑰存放區，而私人憑證則儲存在 `$JAVA_HOME/lib/security/client.jks` 中。

您可能需要額外的設定，才能使用 JAVA 金鑰存放區中的憑證來加密 JDBC 連接。 請參閱您所選 JDBC 驅動程式的文件。

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>初始化 JAVA 金鑰存放區

若要初始化 `import java.security.KeyStore` 物件，請使用密碼載入金鑰儲存區檔案。 這兩個金鑰存放區的預設密碼為「changeit」。

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>手動載入金鑰存放區

您可以手動將憑證載入金鑰存放區。 建立應用程式設定 `SKIP_JAVA_KEYSTORE_LOAD`，並將值設為 `1`，以停用 App Service 自動將憑證載入金鑰存放區。 透過 Azure 入口網站上傳至 App Service 的所有公開憑證都會儲存在 `/var/ssl/certs/`。 私人憑證會儲存在 `/var/ssl/private/` 下。

您可以[開啟 App Service 的 SSH 連線](configure-linux-open-ssh-session.md)，然後執行命令 `keytool`，來互動或偵錯 JAVA 金鑰工具。 如需命令清單，請參閱[金鑰工具文件](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html)。 如需金鑰儲存區 API 的詳細資訊，請參閱[官方文件](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)。

## <a name="configure-apm-platforms"></a>設定 APM 平台

本節說明如何在 Linux 上的 Azure App Service 中，將其中所部署的 Java 應用程式連線至 NewRelic 和 AppDynamics 應用程式效能監視 (APM) 平台。

### <a name="configure-new-relic"></a>設定 NewRelic

1. 在 [NewRelic.com](https://newrelic.com/signup) 建立 NewRelic 帳戶
2. 從 NewRelic 下載 Java 代理程式，其檔案名稱類似 [newrelic-java-x.x.x.zip]。
3. 複製您的授權金鑰，以供稍後設定代理程式時使用。
4. [透過 SSH 連線到您的 App Service 執行個體](configure-linux-open-ssh-session.md)，然後建立新目錄 */home/site/wwwroot/apm*。
5. 將解壓縮的 NewRelic Java 代理程式檔案上傳至 */home/site/wwwroot/apm* 之下的目錄。 代理程式檔案應位於 */home/site/wwwroot/apm/newrelic* 中。
6. 在 */home/site/wwwroot/apm/newrelic/newrelic.yml* 修改 YAML 檔案，並將預留位置授權值取代為您自己的授權金鑰。
7. 在 Azure 入口網站中，瀏覽至您在 App Service 中的應用程式，並建立新的應用程式設定。
    - 如果您的應用程式使用 **Java SE**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `JAVA_OPTS` 的環境變數。
    - 如果您使用 **Tomcat**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `CATALINA_OPTS` 的環境變數。

### <a name="configure-appdynamics"></a>設定 AppDynamics

1. 在 [AppDynamics.com](https://www.appdynamics.com/community/register/) 建立 AppDynamics 帳戶
2. 從 AppDynamics 網站下載 Java 代理程式，其檔案名稱會類似 *AppServerAgent-x.x.x.xxxxx.zip*
3. [透過 SSH 連線到您的 App Service 執行個體](configure-linux-open-ssh-session.md)，然後建立新目錄 */home/site/wwwroot/apm*。
4. 將 Java 代理程式檔案上傳至 */home/site/wwwroot/apm* 下的目錄。 代理程式檔案應位於 */home/site/wwwroot/apm/appdynamics* 中。
5. 在 Azure 入口網站中，瀏覽至您在 App Service 中的應用程式，並建立新的應用程式設定。
    - 如果您使用 **Java SE**，請使用 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 值建立名為 `JAVA_OPTS` 的環境變數，其中 `<app-name>` 是您的 App Service 名稱。
    - 如果您使用 **Tomcat**，請使用 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 值建立名為 `CATALINA_OPTS` 的環境變數，其中 `<app-name>` 是您的 App Service 名稱。

    > [!NOTE]
    > 如果您已經有 `JAVA_OPTS` 或 `CATALINA_OPTS` 的環境變數，請將 `-javaagent:/...` 選項附加至目前值的結尾。
    
## <a name="configure-jar-applications"></a>設定 JAR 應用程式

### <a name="starting-jar-apps"></a>啟動 JAR 應用程式

根據預設，App Service 預期 JAR 應用程式會命名為 *app.jar*。 如果 JAR 應用程式具有此名稱，就會自動執行。 Maven 使用者可以在 *pom .xml* 的 `<build>` 區段中納入 `<finalName>app</finalName>`，以設定 JAR 名稱。 [您可以藉由設定 `archiveFileName` 屬性，在 Gradle 中執行同樣的操作](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName)。

如果要為 JAR 使用不同的名稱，您必須另外提供執行 JAR 檔案的[啟動命令](faq-app-service-linux.md#built-in-images)。 例如： `java -jar my-jar-app.jar` 。 您可以在入口網站中的 [設定] > [一般設定] 底下，或使用名為 `STARTUP_COMMAND` 的應用程式設定，來設定啟動命令的值。

### <a name="server-port"></a>伺服器通訊埠

App Service Linux 會將傳入要求路由傳送至連接埠 80，因此應用程式也應該接聽連接埠 80。 您可以在應用程式的設定 (例如 Spring 的 *application.properties* 檔案)，或在啟動命令 (例如，`java -jar spring-app.jar --server.port=80`) 中執行此動作。 請參閱下列文件，以瞭解常見的 JAVA 架構：

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJAVA](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [播放架構](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>資料來源

### <a name="tomcat"></a>Tomcat

這些指示適用於所有資料庫連線。 您將必須在預留位置中填入您所選資料庫的驅動程式類別名稱和 JAR 檔案。 下表提供常見資料庫的類別名稱和驅動程式下載。

| 資料庫   | 驅動程式類別名稱                             | JDBC 驅動程式                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [下載](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [下載](https://dev.mysql.com/downloads/connector/j/) (請選取 [Platform Independent] \(不受平台影響\)) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [下載](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

若要設定讓 Tomcat 使用「Java 資料庫連線」(JDBC) 或「Java 保存 API」(JPA)，請先自訂 Tomcat 在啟動時所讀入的 `CATALINA_OPTS` 環境變數。 請透過 [App Service Maven 外掛程式](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)中的應用程式設定來設定這些值：

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

或是在 Azure 入口網站的 [設定] > [應用程式設定] 頁面中設定環境變數。

接著，決定資料來源應僅供在 Tomcat Servlet 上執行的一個應用程式還是所有應用程式使用。

#### <a name="application-level-data-sources"></a>應用程式層級資料來源

1. 在專案的 *META-INF/* 目錄中，建立 *context.xml* 檔案。 建立 *META-INF/* 目錄 (如果此目錄不存在)。

2. 在 *context.xml* 中，新增 `Context` 元素以將資料來源連結至 JNDI 位址。 以上表中您驅動程式的類別名稱取代 `driverClassName` 預留位置。

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. 更新您應用程式的 *web.xml*，以使用您應用程式中的資料來源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>共用伺服器層級資源

新增共用的伺服器層級資料來源，會要求您編輯 Tomcat 的 server .xml。 首先，上傳[啟動指令碼](faq-app-service-linux.md#built-in-images)，並在 [設定] > [啟動命令] 中設定指令碼的路徑。 您可以使用 [FTP](deploy-ftp.md) 上傳啟動指令碼。

啟動指令碼會對 server .xml 檔案進行 [xsl 轉換](https://www.w3schools.com/xml/xsl_intro.asp)，並將產生的 xml 檔案輸出至 `/usr/local/tomcat/conf/server.xml`。 啟動指令碼會透過 apk 安裝 libxslt。 您可以透過 FTP 上傳 xsl 檔案和啟動指令碼。 以下是啟動指令碼範例。

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

下方提供一段範例 xsl 檔案。 範例 xsl 檔案會將新的連接器節點加入至 Tomcat server .xml。

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>完成設定

最後，將驅動程式 JAR 放在 Tomcat 類別路徑中，然後重新啟動您的 App Service。

1. 請確定 Tomcat classloader 可以使用 JDBC 驅動程式檔案，方法是將它們放在 [/home/tomcat/lib] 目錄中。 (如果此目錄尚未存在，請建立此目錄)。若要將這些檔案上傳至 App Service 執行個體，請執行下列步驟：

    1. 在 [Cloud Shell](https://shell.azure.com) 中，安裝 webapp 擴充功能：

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. 執行下列 CLI 命令，以建立從本機系統到 App Service 的 SSH 通道：

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. 使用您的 SFTP 用戶端來連線至本機通道連接埠，然後將檔案上傳至 */home/tomcat/lib* 資料夾。

    或著，您也可以使用 FTP 用戶端來上傳 JDBC 驅動程式。 請依照這些[指示來取得您的 FTP 認證](deploy-configure-credentials.md) \(英文\)。

2. 如果您已建立伺服器層級的資料來源，請重新啟動 App Service Linux 應用程式。 Tomcat 會將 `CATALINA_BASE` 重設為 `/home/tomcat`，並使用已更新的設定。

### <a name="spring-boot"></a>Spring Boot

若要連接到 Spring Boot 應用程式中的資料來源，建議您建立連接字串，並將其插入 *application.properties* 檔案。

1. 在 App Service 頁面的 [設定] 區段中，設定字串的名稱，在 [值] 欄位中貼上 JDBC 連接字串，並將類型設定為 [自訂]。 您可以選擇將此連接字串設定為位置設定。

    此連接字串可以名為 `CUSTOMCONNSTR_<your-string-name>` 的環境變數提供應用程式存取。 例如，我們在上面建立的連接字串會命名為 `CUSTOMCONNSTR_exampledb`。

2. 在 *application.properties* 檔案中，使用環境變數名稱來參考此連接字串。 在我們的範例中，我們會使用下列內容。

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

請參閱[有關資料存取的 Spring Boot 文件](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html)和[外部化設定](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)，瞭解此主題的詳細資訊。

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>使用 Redis 做為工作階段快取與 Tomcat 搭配

您可以設定讓 Tomcat 使用外部工作階段存放區，例如 [Azure Redis 快取](../azure-cache-for-redis/index.yml)。 這可讓您在使用者轉移至應用程式的另一個執行個體時 (例如在發生自動調整、重新啟動或容錯移轉的情況下)，保留使用者工作階段狀態 (例如購物車資料)。

若要搭配使用 Tomcat 與 Redis，您必須將應用程式設定為使用 [PersistentManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) 實作。 下列步驟會使用 [Pivotal 工作階段管理員： redis-store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) 為例，來說明此流程。

1. 開啟 Bash 終端機，並使用 `<variable>=<value>` 來設定下列每個環境變數。

    | 變數                 | 值                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | 包含 App Service 執行個體的資源群組名稱。       |
    | WEBAPP_NAME              | 您 App Service 執行個體的名稱。                                     |
    | WEBAPP_PLAN_NAME         | 您 App Service 方案的名稱。                                         |
    | 區域                   | 託管應用程式的區域名稱。                           |
    | REDIS_CACHE_NAME         | 您 Azure Cache for Redis 執行個體的名稱。                           |
    | REDIS_PORT               | Redis 快取接聽的 SSL 連接埠。                             |
    | REDIS_PASSWORD           | 執行個體的主要存取金鑰。                                  |
    | REDIS_SESSION_KEY_PREFIX | 您指定的值，用來識別來自您應用程式的工作階段金鑰。 |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=<App Service plan>
    REGION=<region>
    REDIS_CACHE_NAME=<cache>
    REDIS_PORT=<port>
    REDIS_PASSWORD=<access key>
    REDIS_SESSION_KEY_PREFIX=<prefix>
    ```

    您可以在服務執行個體的 [屬性] 或 [存取金鑰] 區段中尋找，以找到 Azure 入口網站上的名稱、連接埠和存取金鑰資訊。

2. 使用下列內容，建立或更新您應用程式的 *src/main/webapp/META-INF/上下文 .xml* 檔案：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Context path="">
        <!-- Specify Redis Store -->
        <Valve className="com.gopivotal.manager.SessionFlushValve" />
        <Manager className="org.apache.catalina.session.PersistentManager">
            <Store className="com.gopivotal.manager.redis.RedisStore"
                   connectionPoolSize="20"
                   host="${REDIS_CACHE_NAME}.redis.cache.windows.net"
                   port="${REDIS_PORT}"
                   password="${REDIS_PASSWORD}"
                   sessionKeyPrefix="${REDIS_SESSION_KEY_PREFIX}"
                   timeout="2000"
            />
        </Manager>
    </Context>
    ```

    此檔案會指定並設定您應用程式的工作階段管理員實作。 它會使用您在上一個步驟中設定的環境變數，將帳戶資訊保留在來源檔案外。

3. 使用 FTP 將工作階段管理員的 JAR 檔案上傳到 App Service 執行個體，並將它放在 */home/tomcat/lib* 目錄中。 如需詳細資訊，請參閱[使用 FTP/S 將您的應用程式部署至 Azure App Service](./deploy-ftp.md)。

4. 停用 App Service 執行個體的 [工作階段親和性 Cookie](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)。 您可以從 Azure 入口網站執行此操作，方法是瀏覽至應用程式，然後前往 [設定] > [一般設定] > [ARR 親和性] 設定為 [關閉]。 或者您可以使用下列命令：

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    根據預設，App Service 將使用工作階段親和性 Cookie，來確保具現有工作階段的用戶端要求會路由傳送到您應用程式的相同執行個體。 此預設行為不需要任何設定，但當應用程式執行個體重新啟動時，或當流量路由傳送到另一個執行個體時，就無法保留使用者工作階段狀態。 當您[停用現有的 ARR 執行個體親和性](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) \(英文\) 設定來關閉工作階段 Cookie 型路由，讓設定的工作階段存放區在不受干擾的情況下運作。

5. 瀏覽至 App Service 執行個體的 [屬性] 區段，然後尋找 [額外的輸出 IP 位址]。 這些代表應用程式所有可能的輸出 IP 位址。 複製這些位址，以便在下一個步驟中使用。

6. 針對每個 IP 位址，在 Azure Cache for Redis 執行個體中建立防火牆規則。 您可以在 Azure 入口網站，從 Redis 執行個體的 [防火牆] 區段執行此操作。 為每個規則提供唯一的名稱，並將 **起始 IP 位址**，並 **結束 IP 位址**的值設定為相同的 IP 位址。

7. 瀏覽至 Redis 執行個體的 [進階設定] 區段，然後將**僅允許透過 SSL 的存取**設定為**否**。 這可讓 App Service 執行個體透過 Azure 基礎結構與 Redis 快取進行通訊。

8. 更新應用程式的 *pom .xml* 檔中的 `azure-webapp-maven-plugin` 設定，以參考 Redis 帳戶資訊。 此檔案會使用您先前設定的環境變數，將您的帳戶資訊保留在原始程式檔中。

    如有必要，請將 `1.9.1` 變更為最新版的 [Maven 外掛程式 (適用於 Azure App Service)](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)。

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.9.1</version>
        <configuration>            
            <!-- Web App information -->
            <schemaVersion>v2</schemaVersion>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>            
            <runtime>
                <os>linux</os>
                <javaVersion>jre8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>

            <appSettings>
                <property>
                    <name>REDIS_CACHE_NAME</name>
                    <value>${REDIS_CACHE_NAME}</value>
                </property>
                <property>
                    <name>REDIS_PORT</name>
                    <value>${REDIS_PORT}</value>
                </property>
                <property>
                    <name>REDIS_PASSWORD</name>
                    <value>${REDIS_PASSWORD}</value>
                </property>
                <property>
                    <name>REDIS_SESSION_KEY_PREFIX</name>
                    <value>${REDIS_SESSION_KEY_PREFIX}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Xms2048m -Xmx2048m -DREDIS_CACHE_NAME=${REDIS_CACHE_NAME} -DREDIS_PORT=${REDIS_PORT} -DREDIS_PASSWORD=${REDIS_PASSWORD} IS_SESSION_KEY_PREFIX=${REDIS_SESSION_KEY_PREFIX}</value>
                </property>

            </appSettings>

        </configuration>
    </plugin>
    ```

9. 重建並重新部署您的應用程式。

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

現在，應用程式會使用 Redis 快取來管理工作階段。

如需可用來測試這些指示的範例，請參閱 GitHub 上的 [scaling-stateful-java-web-app-on-azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) 存放庫。

## <a name="docker-containers"></a>Docker 容器

若要在您的容器中使用支援 Azure 的 Zulu JDK，請務必提取並使用預先建置映像 (如[適用於 Azure 的支援 Azul Zulu Enterprise 下載頁面](https://www.azul.com/downloads/azure-only/zulu/) \(英文\) 所述)，或使用來自 [Microsoft Java GitHub 存放庫](https://github.com/Microsoft/java/tree/master/docker) 的 `Dockerfile` 範例。

## <a name="statement-of-support"></a>支援聲明

### <a name="runtime-availability"></a>執行階段可用性

App Service for Linux 支援 Java Web 應用程式受控裝載的兩個執行階段：

- [Tomcat servlet 容器](https://tomcat.apache.org/)，適用於執行封裝為 Web 封存 (WAR) 檔案的應用程式。 支援的版本是 8.5 和 9.0。
- Java SE 執行階段環境，適用於執行封裝為 Java 封存 (JAR) 檔案的應用程式。 支援的版本為 Java 8 和 11。

### <a name="jdk-versions-and-maintenance"></a>JDK 版本和維護

OpenJDK 的 Azul Zulu Enterprise 組建是免費、多平台、可實際執行的 OpenJDK 散發套件，適用於 Azure 和 Azure Stack，由 Microsoft 與 Azul Systems 提供支援。 其中包含建置及執行 Java SE 應用程式所需的所有元件。 您可以透過 [JAVA JDK 安裝](https://aka.ms/azure-jdks)來安裝 JDK。

每年 1 月、4 月、7 月和 10 月都會以季為單位自動修補支援的 JDK。

### <a name="security-updates"></a>安全性更新

只要主要資訊安全漏洞的修補程式和修正程式在 Azul Systems 中變成可用，就予以發行。 在 [NIST Common Vulnerability Scoring System 第 2 版](https://nvd.nist.gov/cvss.cfm)上，「主要」弱點是由基本分數 9.0 或更大值定義。

### <a name="deprecation-and-retirement"></a>取代和淘汰

如果將淘汰支援的 Java 執行階段，則使用受影響執行階段的 Azure 開發人員將會在執行階段淘汰之前至少六個月收到淘汰通知。

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>後續步驟

瀏覽[適用於 Java 開發人員的 Azure](/java/azure/) 中心，以找出 Azure 快速入門、教學課程和 Java 參考文件。

[App Service Linux 常見問題集](faq-app-service-linux.md)中回答了非 Java 開發特定的一般 App Service for Linux 使用問題。

::: zone-end