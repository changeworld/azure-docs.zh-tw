---
title: 設定 Windows Java 應用
description: 瞭解如何將 Java 應用配置為在 Azure 應用服務中的 Windows VM 實例上運行。 本文說明最常見的設定工作。
keywords: azure 應用程式服務, Web 應用程式, 視窗, oss, java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: cebe5564767ee345c1aea96b6ac54b9398c3e9a3
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733037"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>為 Azure 應用服務設定 Windows Java 應用

Azure 應用服務允許 JAVA 開發人員在完全託管的基於 Windows 的服務上快速建構、部署和擴展其 Tomcat Web 應用程式。 從命令列或編輯器 (例如 IntelliJ、Eclipse 或 Visual Studio Code)，使用 Maven 外掛程式部署應用程式。

本指南為在應用服務中使用 JAVA 開發人員提供了關鍵概念和說明。 如果您從未使用過 Azure 應用服務,則應首先通讀[Java 快速入門](app-service-web-get-started-java.md)。 有關使用與 JAVA 開發無關的應用服務的一般問題,在[應用服務 Windows 常見問題解答](faq-configuration-and-management.md)中得到解答。

## <a name="deploying-your-app"></a>部署應用程式

您可以使用[Maven 的 Azure Web 應用程式外掛程式](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)來部署 .war 檔。 [用於 IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij)的 Azure 工具套件或[用於 Eclipse 的 Azure 工具套件](/java/azure/eclipse/azure-toolkit-for-eclipse)也支援使用流行的 ID 支援部署。

否則,部署方法將依賴於您的存檔類型:

- 若要將 .war 檔案部署至 Tomcat，請使用 `/api/wardeploy/` 端點透過 POST 張貼您的封存檔案。 如需有關此 API 的詳細資訊，請參閱[這份文件](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)。
- 要將 .jar 檔部署到 JAVA SE,請使用`/api/zipdeploy/`Kudu 網站的終結點。 如需有關此 API 的詳細資訊，請參閱[這份文件](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)。

不要使用 FTP 部署 .war。 FTP 工具是為上傳啟動指令碼、相依性或其他執行階段檔案而設計的。 它不是部署網頁的最佳選擇。

## <a name="logging-and-debugging-apps"></a>記錄和偵錯應用程式

透過 Azure 入口網站，可以取得每個應用程式的效能報表、流量視覺化和健康狀態檢查。 有關詳細資訊,請參閱[Azure 應用服務診斷概述](overview-diagnostics.md)。

### <a name="use-flight-recorder"></a>使用飛行記錄器

使用 Azul JVM 的應用程式服務上的所有 Java 運行時都配有祖魯飛行記錄器。 您可以使用它記錄 JVM、系統和 Java 級別事件,以監視 Java 應用程式中的行為和疑難解答問題。

要進行時序錄製,您需要 JAVA 應用程式的 PID(程序 ID)。 要查找 PID,請以 HTTPs://<网站名称>.scm.azure 網站.net/ProcessExplorer/)打開 Web 應用的 SCM 網站的瀏覽器。 此頁顯示 Web 應用中的運行進程。 在表中查找名為"java"的進程並複製相應的 PID(程序 ID)。

接下來,在 SCM 網站的頂部工具列中打開**調試主控台**並運行以下命令。 替換為`<pid>`前面複製的進程 ID。 此命令將啟動 Java 應用程式的 30 秒探查器`timed_recording_example.jfr`錄製,並`D:\home`生成 目錄中名為的檔。

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

有關詳細資訊,請參閱[Jcmd 命令參考](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)。

#### <a name="analyze-jfr-files"></a>分析`.jfr`檔案

使用[FTPS](deploy-ftp.md)將 JFR 檔案下載到本地電腦。 要分析JFR檔案,下載並安裝[祖魯任務控制](https://www.azul.com/products/zulu-mission-control/)。 有關祖魯任務控制的說明,請參閱[Azul 文件](https://docs.azul.com/zmc/)與[安裝說明](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)。

### <a name="stream-diagnostic-logs"></a>資料流診斷記錄

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

有關詳細資訊,請參閱[雲殼中的流日誌](troubleshoot-diagnostic-logs.md#in-cloud-shell)。

### <a name="app-logging"></a>應用程式記錄

透過 Azure 入口網站或 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) 啟用[應用程式記錄](troubleshoot-diagnostic-logs.md#enable-application-logging-windows)，設定 App Service 將應用程式的標準主控台輸出和標準主控台錯誤資料流寫入至本機檔案系統或 Azure Blob 儲存體。 設定後的 12 個小時會停用記錄至本機 App Service 檔案系統執行個體。 如果您需要較長的保留期，則請設定應用程式將輸出寫入至 Blob 儲存體容器。 您的 Java 和 Tomcat 應用日誌可以在 */LogFiles/應用程式/* 目錄中找到。

如果您的應用程式使用 [Logback](https://logback.qos.ch/) 或 [Log4j](https://logging.apache.org/log4j) 追蹤，則您可以使用[在 Application Insights 中探索 Java 追蹤記錄](/azure/application-insights/app-insights-java-trace-logs)中的記錄架構設定指示，將這些要檢閱的追蹤轉送至 Azure Application Insights。


## <a name="customization-and-tuning"></a>自訂和調整

Azure 應用服務支援透過 Azure 門戶和 CLI 進行開箱即用的調優和自定義。 檢視以下有關非 Java 特定 Web 應用設定的文章:

- [進行應用程式設定](configure-common.md#configure-app-settings)
- [設定自訂網域](app-service-web-tutorial-custom-domain.md)
- [設定 TLS 繫結](configure-ssl-bindings.md)
- [新增 CDN](../cdn/cdn-add-to-web-app.md)
- [設定庫杜網站](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>設定 Java 執行階段選項

要設置分配的記憶體或其他 JVM 執行時選項,請創建一個`JAVA_OPTS`應用設置,該[設置](configure-common.md#configure-app-settings)名為這些選項。 應用服務在啟動時將此設置作為環境變數傳遞給 JAVA 運行時。

在 Azure 入口網站中，於 Web 應用程式的 [應用程式設定]**** 下，建立名為 `JAVA_OPTS` 且包含其他設定的新應用程式設定 (例如 `-Xms512m -Xmx1204m`)。

要從 Maven 外掛程式配置應用設定,請在 Azure 外掛程式部分添加設置/值標記。 以下範例設定特定的最小和最大 Java 堆大小:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

執行具有其 App Service 方案中某個部署位置的單一應用程式開發人員，可以使用下列選項：

- B1 與 S1 實體:`-Xms1024m -Xmx1024m`
- B2 與 S2 實體:`-Xms3072m -Xmx3072m`
- B3 與 S3 實體:`-Xms6144m -Xmx6144m`

調整應用程式堆積設定時，請檢閱 App Service 方案詳細資料，並考慮多個應用程式和部署位置需求以尋找最佳的記憶體配置。

### <a name="turn-on-web-sockets"></a>開啟 Web 通訊端

在應用程式的 [應用程式設定]**** 中，開啟 Azure 入口網站中的 Web 通訊端支援。 您必須重新啟動應用程式，設定才會生效。

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

在 Azure 入口網站中，於 Web 應用程式的 [應用程式設定]**** 下，建立名為 `JAVA_OPTS` 且值為 `-Dfile.encoding=UTF-8` 的新應用程式設定。

或者，您可以使用 App Service Maven 外掛程式來設定應用程式設定。 在外掛程式設定中新增設定名稱和值標籤：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>預寫 JSP 檔

為了提高 Tomcat 應用程式的性能,您可以在部署到應用服務之前編譯 JSP 檔。 您可以使用 Apache Sling 提供的[Maven 外掛程式](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html),或使用此[Ant 產生檔案](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)。

## <a name="secure-applications"></a>保護應用程式

在應用服務中運行的 JAVA 應用程式具有與其他應用程式相同的[安全最佳實踐](/azure/security/security-paas-applications-using-app-services)集。

### <a name="authenticate-users-easy-auth"></a>認證使用者(簡單認證)

使用 **「身份驗證和授權」** 選項在 Azure 入口中設置應用身份驗證。 在這裡，您可以使用 Azure Active Directory 或社交登入 (例如 Facebook、Google 或 GitHub) 來啟用驗證。 只有在設定單一驗證提供者時，Azure 入口網站設定才會運作。 如需詳細資訊，請參閱[設定 App Service 應用程式使用 Azure Active Directory 登入](configure-authentication-provider-aad.md)，以及其他身分識別提供者的相關文章。 如果您需要啟用多個登入提供者，請遵循[自訂 App Service 驗證](app-service-authentication-how-to.md)一文中的指示。

#### <a name="tomcat"></a>Tomcat

Tomcat 應用程式可以通過將主體物件強制轉換到Map物件,直接從servlet訪問使用者的聲明。 Map 物件將每個聲明類型映射到該類型的聲明集合。 在下面的代碼中,`request`是`HttpServletRequest`的實體。

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

現在,您可以檢查物件`Map`中是否有任何特定的聲明。 例如,以下代碼段遍越遍所有聲明類型並列印每個集合的內容。

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

要登出使用者,`/.auth/ext/logout`請使用路徑。 要執行其他操作,請參閱有關[應用服務身份驗證和授權使用方式](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)的文檔。 也有關於Tomcat [HHH ServletRequest介面](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html)及其方法的官方文檔。 以下 servlet 方法也會根據您的應用服務設定進行水合:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

要關閉此功能,請建立值`WEBSITE_AUTH_SKIP_PRINCIPAL`為`1` 要禁用應用服務添加的所有 servlet 篩選器,請建立`WEBSITE_SKIP_FILTERS`一個`1`值為的設定。

### <a name="configure-tlsssl"></a>設定 TLS/SSL

按照[Azure 應用服務中具有 TLS 綁定的自訂 DNS 名稱](configure-ssl-bindings.md)中的說明進行操作,上載現有的 TLS/SSL 證書並將其綁定到應用程式的功能變數名稱。 根據預設，您的應用程式仍然允許 HTTP 連線；請遵循教學課程中的特定步驟，以強制執行 SSL 和 TLS。

### <a name="use-keyvault-references"></a>使用金鑰庫參考

[Azure KeyVault](../key-vault/general/overview.md)提供具有存取策略和審核歷史記錄的集中式密鑰管理。 您可以在 KeyVault 中儲存機密(如密碼或連接字串),並透過環境變數在應用程式中訪問這些機密。

首先,按照有關[授予應用對金鑰保管庫存取權限](app-service-key-vault-references.md#granting-your-app-access-to-key-vault)的說明,並在[應用程式設定中對您的機密進行 KeyVault 參考](app-service-key-vault-references.md#reference-syntax)。 通過列印環境變數以遠端訪問應用服務終端,可以驗證引用是否已解析為機密。

要在 Spring 或 Tomcat 配置檔中注入這些機密,請`${MY_ENV_VAR}`使用環境變數注入語法 ()。 有關 Spring 設定檔,請參閱有關[外部化配置的](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)本文件。


## <a name="configure-apm-platforms"></a>設定 APM 平台

本節演示如何將部署在 Linux 上的 Azure 應用服務上的 Java 應用程式與 NewRelic 和 AppDynamics 應用程式效能監視 (APM) 平台連接起來。

### <a name="configure-new-relic"></a>設定 NewRelic

1. 在[NewRelic.com](https://newrelic.com/signup)建立新的遺物帳戶
2. 從NewRelic下載JAVA代理,它將有一個類似於*newrelic-java-x.x.x.zip*的檔名。
3. 複製您的授權金鑰，以供稍後設定代理程式時使用。
4. 使用[Kudu 主控台](https://github.com/projectkudu/kudu/wiki/Kudu-console)建立新的目錄 */home/網站/wwwroot/apm*。
5. 上傳解壓縮的新文物JAVA代理檔到目錄下 */home/網站/wwwroot/apm*. 您的代理的檔案應位於 */home/網站/wwwroot/apm/newrelic。*
6. 在 */home/網站/wwwroot/apm/newrelic/newrelic.yml*處修改 YAML 檔,並將占位符許可證值替換為您自己的許可證金鑰。
7. 在 Azure 入口網站中，瀏覽至您在 App Service 中的應用程式，並建立新的應用程式設定。
    - 如果您的應用程式使用 **Java SE**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `JAVA_OPTS` 的環境變數。
    - 如果您使用 **Tomcat**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `CATALINA_OPTS` 的環境變數。

### <a name="configure-appdynamics"></a>設定 AppDynamics

1. 在 [AppDynamics.com](https://www.appdynamics.com/community/register/) 建立 AppDynamics 帳戶
2. 從 AppDynamics 網站下載 Java 代理,檔案名將類似於*AppServerAgent-x.x.x.xxxxx.zip*
3. 使用[Kudu 主控台](https://github.com/projectkudu/kudu/wiki/Kudu-console)建立新的目錄 */home/網站/wwwroot/apm*。
4. 將 Java 代理檔上傳到 */home/網站/wwwroot/apm*下的目錄中。 您的代理的檔案應位於 */home/網站/wwwroot/apm/appdynamics*。
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

要將 Tomcat 配置為使用 JAVA 資料庫連接 (JDBC)`CATALINA_OPTS`或 Java 持久性 API (JPA),請首先自定義 Tomcat 在啟動時讀取的環境變數。 請透過 [App Service Maven 外掛程式](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)中的應用程式設定來設定這些值：

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

或者在 Azure 門戶中的 **「設定** > **應用程式設定」** 頁中設定環境變數。

接著，決定資料來源應僅供在 Tomcat Servlet 上執行的一個應用程式還是所有應用程式使用。

#### <a name="application-level-data-sources"></a>應用程式資料來源

1. 在專案的*META-INF/* 目錄中建立*上下文.xml*檔。 如果*META-INF/* 目錄不存在,則創建該目錄。

2. 在*context.xml*中`Context`,添加一個元素以將資料源連結到 JNDI 位址。 以上表中您驅動程式的類別名稱取代 `driverClassName` 預留位置。

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

3. 更新應用程式的*Web.xml*以在應用程式中使用數據源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>完成設定

最後,我們將將驅動程式 JAR 放在 Tomcat 類路徑中,然後重新啟動應用服務。 通過將 JDBC 驅動程式檔放在 */home/tomcat/lib*目錄中,確保它們可供 Tomcat 類載入器使用。 (如果此目錄不存在,請創建該目錄。要將這些檔案載到應用服務實例,請執行以下步驟:

1. 在[雲外殼](https://shell.azure.com)中,安裝 Webapp 擴展:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. 執行以下 CLI 指令以建立從本地系統到應用服務的 SSH 隧道:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. 使用 SFTP 用戶端連接到本地隧道埠,並將檔上載到 */home/tomcat/lib*資料夾。

或著，您也可以使用 FTP 用戶端來上傳 JDBC 驅動程式。 請依照這些[指示來取得您的 FTP 認證](deploy-configure-credentials.md) \(英文\)。

## <a name="configuring-tomcat"></a>設定湯姆卡特

要編輯 Tomcat`server.xml`或其他配置檔,請先在門戶中記下 Tomcat 的主要版本。

1. 通過運行`env`該命令查找版本的 Tomcat 主目錄。 搜索以`AZURE_TOMCAT`主要版本開頭並匹配的環境變數。 例如,`AZURE_TOMCAT85_HOME`指向 Tomcat 8.5 的 Tomcat 目錄。
1. 為版本識別 Tomcat 家目錄後,請將設定目錄`D:\home`複製到 。 例如,如果`AZURE_TOMCAT85_HOME`值為`D:\Program Files (x86)\apache-tomcat-8.5.37`,則複製目錄的新路徑將`D:\home\apache-tomcat-8.5.37`為 。

最後，重新啟動 App Service。 您的部署應該和以前`D:\home\site\wwwroot\webapps`一樣。

## <a name="configure-java-se"></a>設定 Java SE

運行 時。Windows 上的 Java SE`server.port`上的 JAR 應用程式在應用程式啟動時作為命令列選項傳遞。 可以從環境變數 手動解析 HTTP`HTTP_PLATFORM_PORT`埠 。 此環境變數的值將是應用程式應偵聽的 HTTP 埠。 

## <a name="java-runtime-statement-of-support"></a>Java 執行階段支援聲明

### <a name="jdk-versions-and-maintenance"></a>JDK 版本和維護

Azure 支援的 Java Development Kit (JDK) 是透過 [Azul Systems](https://www.azul.com/) 提供的 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)。

主要版本更新將透過 Windows Azure 應用服務中的新運行時選項提供。 客戶會更新為設定其 App Service 部署的這些新版 Java，以及負責測試並確定主要更新符合其需求。

每年 1 月、4 月、7 月和 10 月都會以季為單位自動修補支援的 JDK。 有關 Azure 上的 Java 的詳細資訊,請參閱[此支援文件](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)。

### <a name="security-updates"></a>安全性更新

只要主要資訊安全漏洞的修補程式和修正程式在 Azul Systems 中變成可用，就予以發行。 在 [NIST Common Vulnerability Scoring System 第 2 版](https://nvd.nist.gov/cvss.cfm)上，「主要」弱點是由基本分數 9.0 或更大值定義。

截至[2018年9月30日,Tomcat 8.0已達到壽命終止(EOL)。](https://tomcat.apache.org/tomcat-80-eol.html) 雖然運行時在 Azure 應用服務上仍然有效,但 Azure 不會將安全更新應用於 Tomcat 8.0。 如果可能,請將應用程式遷移到 Tomcat 8.5 或 9.0。 Tomcat 8.5 和 9.0 在 Azure 應用服務中都可用。 有關詳細資訊,請參閱[官方 Tomcat 網站](https://tomcat.apache.org/whichversion.html)。 

### <a name="deprecation-and-retirement"></a>取代和淘汰

如果將淘汰支援的 Java 執行階段，則使用受影響執行階段的 Azure 開發人員將會在執行階段淘汰之前至少六個月收到淘汰通知。

### <a name="local-development"></a>本機開發

開發人員可以從 [Azul 的下載網站](https://www.azul.com/downloads/azure-only/zulu/)下載實際執行版本的 Azul Zulu Enterprise JDK，以進行本機開發。

### <a name="development-support"></a>開發支援

在 Azure 或[Azure 堆疊](https://azure.microsoft.com/overview/azure-stack/)開發具有合格的 Azure 支援計畫時,可透過 Microsoft 獲得[Azure 支援的 Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) [的產品支援](https://azure.microsoft.com/support/plans/)。

### <a name="runtime-support"></a>執行階段支援

開發人員如果具有[合格的支援方案](https://azure.microsoft.com/support/plans/)，便可透過 Azure 支援服務，針對 Azul Zulu JDK [開立問題](/azure/azure-portal/supportability/how-to-create-azure-support-request)。

## <a name="next-steps"></a>後續步驟

本主題提供 Windows 上對 Azure 應用服務的支援的 Java 運行時語句。

- 要瞭解有關使用 Azure 應用服務託管 Web 應用程式的詳細資訊,請參閱[應用服務概述](overview.md)。
- 有關 Azure 開發上的 Java 的資訊,請參閱[Java 開發人員中心的 Azure。](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
