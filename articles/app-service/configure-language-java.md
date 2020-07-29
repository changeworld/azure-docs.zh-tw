---
title: 設定 Windows JAVA 應用程式
description: 瞭解如何設定 JAVA 應用程式，以在 Azure App Service 的 Windows VM 實例上執行。 本文說明最常見的設定工作。
keywords: azure app service，web 應用程式，windows，oss，java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java
ms.openlocfilehash: d0850b2192df1392a245f27588ffcb8ddb337347
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319332"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>設定適用于 Azure App Service 的 Windows JAVA 應用程式

Azure App Service 可讓 JAVA 開發人員在完全受控的 Windows 服務上，快速建立、部署及調整其 Tomcat web 應用程式。 從命令列或編輯器 (例如 IntelliJ、Eclipse 或 Visual Studio Code)，使用 Maven 外掛程式部署應用程式。

本指南提供 JAVA 開發人員在 App Service 中使用的重要概念和指示。 如果您從未使用過 Azure App Service，應該先閱讀[JAVA 快速入門](app-service-web-get-started-java.md)。 有關使用不是 JAVA 開發特定 App Service 的一般問題，請在[App Service WINDOWS 常見問題](faq-configuration-and-management.md)中獲得解答。

## <a name="deploying-your-app"></a>部署應用程式

您可以使用[適用于 Maven 的 Azure Web 應用程式外掛程式](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)來部署您的 war 檔案。 [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) 或 [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse)也支援使用熱門 Ide 進行部署。

否則部署方法將會取決於您的封存類型：

- 若要將 .war 檔案部署至 Tomcat，請使用 `/api/wardeploy/` 端點透過 POST 張貼您的封存檔案。 如需有關此 API 的詳細資訊，請參閱[這份文件](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)。
- 若要將 .jar 檔案部署至 JAVA SE，請使用 `/api/zipdeploy/` Kudu 網站的端點。 如需有關此 API 的詳細資訊，請參閱[這份文件](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)。

請勿使用 FTP 部署您的 war。 FTP 工具是為上傳啟動指令碼、相依性或其他執行階段檔案而設計的。 它不是部署網頁的最佳選擇。

## <a name="logging-and-debugging-apps"></a>記錄和偵錯應用程式

透過 Azure 入口網站，可以取得每個應用程式的效能報表、流量視覺化和健康狀態檢查。 如需詳細資訊，請參閱 [Azure App Service 診斷概觀](overview-diagnostics.md)。

### <a name="use-flight-recorder"></a>使用飛行記錄器

在 App Service 上使用 Azul Jvm 的所有 JAVA 執行時間都隨附于祖魯飛行記錄器。 您可以用它來記錄 JVM、系統和 JAVA 層級事件，以監視 JAVA 應用程式中的行為和疑難排解問題。

若要進行計時記錄，您將需要 JAVA 應用程式的 PID （處理序識別碼）。 若要尋找 PID，請將瀏覽器開啟至 web 應用程式的 SCM 網站，網址為 HTTPs://<您的網站名稱>. scm.azurewebsites.net/ProcessExplorer/。 此頁面會顯示您 web 應用程式中的執行中進程。 在資料表中尋找名為 "java" 的進程，並複製對應的 PID （處理序識別碼）。

接下來，開啟 SCM 網站頂端工具列中的 [**偵錯主控台**]，然後執行下列命令。 `<pid>`將取代為您稍早複製的處理序識別碼。 此命令將會啟動您的 JAVA 應用程式的30秒分析工具記錄，並 `timed_recording_example.jfr` 在目錄中產生名為的檔案 `D:\home` 。

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

如需詳細資訊，請參閱 [Jcmd 命令參考](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)。

#### <a name="analyze-jfr-files"></a>分析 `.jfr` 檔案

使用 [FTPS](deploy-ftp.md)，將 JFR 檔案下載到本機電腦。 若要分析 JFR 檔案，請下載並安裝 [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/)。 如需有關 Zulu Mission Control 的指示，請參閱 [Azul 文件](https://docs.azul.com/zmc/)和[安裝指示](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)。

### <a name="stream-diagnostic-logs"></a>資料流診斷記錄

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

如需詳細資訊，請參閱 [Cloud Shell 中的串流處理記錄](troubleshoot-diagnostic-logs.md#in-cloud-shell)。

### <a name="app-logging"></a>應用程式記錄

透過 Azure 入口網站或 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) 啟用[應用程式記錄](troubleshoot-diagnostic-logs.md#enable-application-logging-windows)，設定 App Service 將應用程式的標準主控台輸出和標準主控台錯誤資料流寫入至本機檔案系統或 Azure Blob 儲存體。 設定後的 12 個小時會停用記錄至本機 App Service 檔案系統執行個體。 如果您需要較長的保留期，則請設定應用程式將輸出寫入至 Blob 儲存體容器。 您的 JAVA 和 Tomcat 應用程式記錄可在 */LogFiles/Application/* 目錄中找到。

如果您的應用程式使用 [Logback](https://logback.qos.ch/) 或 [Log4j](https://logging.apache.org/log4j) 追蹤，則您可以使用[在 Application Insights 中探索 Java 追蹤記錄](/azure/application-insights/app-insights-java-trace-logs)中的記錄架構設定指示，將這些要檢閱的追蹤轉送至 Azure Application Insights。


## <a name="customization-and-tuning"></a>自訂和調整

Azure App Service 支援透過 Azure 入口網站和 CLI 進行的微調和自訂。 請檢閱下列非 Java 特定 Web 應用程式設定的文章：

- [設定應用程式設定](configure-common.md#configure-app-settings)
- [設定自訂網域](app-service-web-tutorial-custom-domain.md)
- [設定 TLS 系結](configure-ssl-bindings.md)
- [新增 CDN](../cdn/cdn-add-to-web-app.md)
- [設定 Kudu 網站](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>設定 Java 執行階段選項

若要設定配置的記憶體或其他 JVM 執行時間選項，請使用選項來建立名為的[應用程式設定](configure-common.md#configure-app-settings) `JAVA_OPTS` 。 App Service 啟動時，會將此設定做為環境變數傳遞至 JAVA 執行時間。

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

在 App Service 中執行的 JAVA 應用程式與其他應用程式有一組相同的[安全性最佳作法](/azure/security/security-paas-applications-using-app-services)。

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

若要將使用者登出，請使用 `/.auth/ext/logout` 路徑。 若要執行其他動作，請參閱有關 [App Service 驗證和授權使用](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)的文件。 另外，您也可以參閱有關 Tomcat [HttpServletRequest 介面](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html)及其方法的官方文件。 下列 servlet 方法也是根據 App Service 設定來更新：

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

若要停用此功能，請建立名為 `WEBSITE_AUTH_SKIP_PRINCIPAL` 的應用程式設定，其值為 `1`。 若要停用 App Service 新增的所有 servlet 篩選條件，請建立名為 `WEBSITE_SKIP_FILTERS` 的設定，其值為 `1`。

### <a name="configure-tlsssl"></a>設定 TLS/SSL

依照[Azure App Service 中的使用 TLS 系結保護自訂 DNS 名稱](configure-ssl-bindings.md)中的指示，上傳現有的 TLS/SSL 憑證，並將它系結至您應用程式的功能變數名稱。 根據預設，您的應用程式仍然允許 HTTP 連線；請遵循教學課程中的特定步驟，以強制執行 SSL 和 TLS。

### <a name="use-keyvault-references"></a>使用 KeyVault 參考

[Azure KeyVault](../key-vault/general/overview.md) 透過存取原則和稽核歷程記錄提供集中式祕密管理。 您可以在 KeyVault 中儲存秘密 (例如密碼或連接字串)，並透過環境變數在應用程式中存取這些秘密。

首先，請依照說明[授與應用程式對 Key Vault 的存取權限](app-service-key-vault-references.md#granting-your-app-access-to-key-vault)，並[在應用程式設定中建立對秘密的 KeyVault 參考](app-service-key-vault-references.md#reference-syntax)。 您可以在遠端存取 App Service 終端機時，列印環境變數，來驗證參考是否會解析為秘密。

若要將這些秘密插入 Spring 或 Tomcat 設定檔中，請使用環境變數插入語法 (`${MY_ENV_VAR}`)。 如需 Spring 設定檔，請參閱有關[外部化設定](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)的文件。


## <a name="configure-apm-platforms"></a>設定 APM 平台

本節說明如何在 Linux 上的 Azure App Service 中，將其中所部署的 Java 應用程式連線至 NewRelic 和 AppDynamics 應用程式效能監視 (APM) 平台。

### <a name="configure-new-relic"></a>設定 NewRelic

1. 在[NewRelic.com](https://newrelic.com/signup)建立新的 new relic 帳戶
2. 從 NewRelic 下載 Java 代理程式，其檔案名稱類似 [newrelic-java-x.x.x.zip]。
3. 複製您的授權金鑰，以供稍後設定代理程式時使用。
4. 使用[Kudu 主控台](https://github.com/projectkudu/kudu/wiki/Kudu-console)來建立新的目錄 */home/site/wwwroot/apm*。
5. 將已解壓縮的新 New relic JAVA 代理程式檔案上傳至 */home/site/wwwroot/apm*底下的目錄。 代理程式檔案應位於 */home/site/wwwroot/apm/newrelic* 中。
6. 在 */home/site/wwwroot/apm/newrelic/newrelic.yml* 修改 YAML 檔案，並將預留位置授權值取代為您自己的授權金鑰。
7. 在 Azure 入口網站中，瀏覽至您在 App Service 中的應用程式，並建立新的應用程式設定。
    - 如果您的應用程式使用 **Java SE**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `JAVA_OPTS` 的環境變數。
    - 如果您使用 **Tomcat**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `CATALINA_OPTS` 的環境變數。

### <a name="configure-appdynamics"></a>設定 AppDynamics

1. 在 [AppDynamics.com](https://www.appdynamics.com/community/register/) 建立 AppDynamics 帳戶
2. 從 AppDynamics 網站下載 Java 代理程式，其檔案名稱會類似 *AppServerAgent-x.x.x.xxxxx.zip*
3. 使用[Kudu 主控台](https://github.com/projectkudu/kudu/wiki/Kudu-console)來建立新的目錄 */home/site/wwwroot/apm*。
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
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [下載](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

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

最後，我們會將驅動程式 Jar 放在 Tomcat 類路徑中，然後重新開機您的 App Service。 請確定 Tomcat classloader 可以使用 JDBC 驅動程式檔案，方法是將它們放在 [/home/tomcat/lib] 目錄中。 (如果此目錄尚未存在，請建立此目錄)。若要將這些檔案上傳至 App Service 執行個體，請執行下列步驟：

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

## <a name="configuring-tomcat"></a>正在設定 Tomcat

若要編輯 Tomcat `server.xml` 或其他設定檔，請先記下您在入口網站中的 tomcat 主要版本。

1. 執行命令，尋找您版本的 Tomcat 主目錄 `env` 。 搜尋以開頭的環境變數 `AZURE_TOMCAT` ，並符合您的主要版本。 例如， `AZURE_TOMCAT85_HOME` 指向 tomcat 8.5 的 tomcat 目錄。
1. 一旦您識別出版本的 Tomcat 主目錄，請將設定目錄複寫到 `D:\home` 。 例如，如果的 `AZURE_TOMCAT85_HOME` 值為 `D:\Program Files (x86)\apache-tomcat-8.5.37` ，則複製之目錄的新路徑會是 `D:\home\apache-tomcat-8.5.37` 。

最後，重新啟動 App Service。 您的部署應該會 `D:\home\site\wwwroot\webapps` 如同之前一樣。

## <a name="configure-java-se"></a>設定 JAVA SE

執行時。Windows 上的 JAVA SE 上的 JAR 應用程式， `server.port` 會在應用程式啟動時當做命令列選項來傳遞。 您可以從環境變數手動解析 HTTP 埠 `HTTP_PLATFORM_PORT` 。 此環境變數的值將會是您的應用程式應該接聽的 HTTP 埠。 

## <a name="java-runtime-statement-of-support"></a>Java 執行階段支援聲明

### <a name="jdk-versions-and-maintenance"></a>JDK 版本和維護

Azure 支援的 Java Development Kit (JDK) 是透過 [Azul Systems](https://www.azul.com/) 提供的 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)。

主要版本更新將透過 Windows Azure App Service 中的新執行時間選項來提供。 客戶會更新為設定其 App Service 部署的這些新版 Java，以及負責測試並確定主要更新符合其需求。

每年 1 月、4 月、7 月和 10 月都會以季為單位自動修補支援的 JDK。 如需有關 Azure 上 JAVA 的詳細資訊，請參閱[這份支援檔](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)。

### <a name="security-updates"></a>安全性更新

只要主要資訊安全漏洞的修補程式和修正程式在 Azul Systems 中變成可用，就予以發行。 在 [NIST Common Vulnerability Scoring System 第 2 版](https://nvd.nist.gov/vuln-metrics/cvss)上，「主要」弱點是由基本分數 9.0 或更大值定義。

Tomcat 8.0 已[于2018年9月30日到達生命週期結束（EOL）](https://tomcat.apache.org/tomcat-80-eol.html)。 雖然執行時間仍是在 Azure App Service 上可，但 Azure 不會將安全性更新套用至 Tomcat 8.0。 可能的話，請將您的應用程式遷移至 Tomcat 8.5 或9.0。 Tomcat 8.5 和9.0 都可以在 Azure App Service 上取得。 如需詳細資訊，請參閱[官方 Tomcat 網站](https://tomcat.apache.org/whichversion.html)。 

### <a name="deprecation-and-retirement"></a>取代和淘汰

如果將淘汰支援的 Java 執行階段，則使用受影響執行階段的 Azure 開發人員將會在執行階段淘汰之前至少六個月收到淘汰通知。

### <a name="local-development"></a>本機開發

開發人員可以從 [Azul 的下載網站](https://www.azul.com/downloads/azure-only/zulu/)下載實際執行版本的 Azul Zulu Enterprise JDK，以進行本機開發。

### <a name="development-support"></a>開發支援

使用[合格的 Azure 支援方案](https://azure.microsoft.com/support/plans/)來開發 azure 或[Azure Stack](https://azure.microsoft.com/overview/azure-stack/)時，可透過 Microsoft 取得支援[azure 的 Azul 祖魯 JDK](https://www.azul.com/downloads/azure-only/zulu/)的產品支援。

### <a name="runtime-support"></a>執行階段支援

開發人員如果具有[合格的支援方案](https://azure.microsoft.com/support/plans/)，便可透過 Azure 支援服務，針對 Azul Zulu JDK [開立問題](/azure/azure-portal/supportability/how-to-create-azure-support-request)。

## <a name="next-steps"></a>後續步驟

本主題提供 JAVA 執行時間對 Windows Azure App Service 的支援聲明。

- 若要深入瞭解如何使用 Azure App Service 裝載 web 應用程式，請參閱[App Service 總覽](overview.md)。
- 如需有關 Azure 開發之 JAVA 的詳細資訊，請參閱適用[于 JAVA 開發人員中心的 Azure](https://docs.microsoft.com/java/azure/?view=azure-java-stable)。
