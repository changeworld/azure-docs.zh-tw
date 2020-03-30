---
title: 配置 Linux JAVA 應用程式
description: 瞭解如何為應用配置預構建的 JAVA 容器。 本文說明最常見的設定工作。
keywords: azure 應用程式服務， Web 應用程式， linux， oss， java， java ee， jee， javaee
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 970701606811cbd61a9bfebe39ff82cdc91d5693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245832"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>為 Azure App Service 設定 Linux Java 應用程式

Linux 上的 Azure 應用服務允許 JAVA 開發人員在完全託管的基於 Linux 的服務上快速構建、部署和擴展其 Tomcat 或 JAVA 標準版 （SE） 打包的 Web 應用程式。 從命令列或編輯器 (例如 IntelliJ、Eclipse 或 Visual Studio Code)，使用 Maven 外掛程式部署應用程式。

本指南為在應用服務中使用內置 Linux 容器的 JAVA 開發人員提供了關鍵概念和說明。 如果您從未使用過 Azure 應用服務，請按照[JAVA 快速入門](quickstart-java.md)操作。

## <a name="deploying-your-app"></a>部署應用程式

可以使用[Azure 應用服務的 Maven 外掛程式](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)來部署 .jar 和 .war 檔。 [用於 IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij)的 Azure 工具組或[用於 Eclipse 的 Azure 工具組](/java/azure/eclipse/azure-toolkit-for-eclipse)也支援使用流行的 ID 支援部署。

否則，部署方法將依賴于您的存檔類型：

- 若要將 .war 檔案部署至 Tomcat，請使用 `/api/wardeploy/` 端點透過 POST 張貼您的封存檔案。 如需有關此 API 的詳細資訊，請參閱[這份文件](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)。
- 若要部署 Java SE 映像上的 .jar 檔案，請使用 Kudu 網站的 `/api/zipdeploy/` 端點。 如需有關此 API 的詳細資訊，請參閱[這份文件](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)。

請勿使用 FTP 來部署 .war 或 .jar。 FTP 工具是為上傳啟動指令碼、相依性或其他執行階段檔案而設計的。 它不是部署網頁的最佳選擇。

## <a name="logging-and-debugging-apps"></a>記錄和偵錯應用程式

透過 Azure 入口網站，可以取得每個應用程式的效能報表、流量視覺化和健康狀態檢查。 有關詳細資訊，請參閱[Azure 應用服務診斷概述](../overview-diagnostics.md)。

### <a name="ssh-console-access"></a>SSH 主控台存取

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>資料流診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

有關詳細資訊，請參閱[雲殼中的流日誌](../troubleshoot-diagnostic-logs.md#in-cloud-shell)。

### <a name="app-logging"></a>應用程式記錄

透過 Azure 入口網站或 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) 啟用[應用程式記錄](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows)，設定 App Service 將應用程式的標準主控台輸出和標準主控台錯誤資料流寫入至本機檔案系統或 Azure Blob 儲存體。 設定後的 12 個小時會停用記錄至本機 App Service 檔案系統執行個體。 如果您需要較長的保留期，則請設定應用程式將輸出寫入至 Blob 儲存體容器。 您的 JAVA 和 Tomcat 應用日誌可以在 */home/LogFiles/應用程式/* 目錄中找到。

如果您的應用程式使用 [Logback](https://logback.qos.ch/) 或 [Log4j](https://logging.apache.org/log4j) 追蹤，則您可以使用[在 Application Insights 中探索 Java 追蹤記錄](/azure/application-insights/app-insights-java-trace-logs)中的記錄架構設定指示，將這些要檢閱的追蹤轉送至 Azure Application Insights。

### <a name="troubleshooting-tools"></a>疑難排解工具

內置JAVA映射基於[阿爾卑斯Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html)作業系統。 使用`apk`包管理器安裝任何故障排除工具或命令。

### <a name="flight-recorder"></a>飛行記錄器

應用服務上的所有 Linux JAVA 映射都安裝了祖魯飛行記錄器，因此您可以輕鬆連接到 JVM 並啟動探測器錄製或生成堆轉儲。

#### <a name="timed-recording"></a>時記錄

要開始使用，SSH 進入應用服務並`jcmd`運行該命令以查看正在運行的所有 JAVA 進程的清單。 除了 jcmd 本身之外，還應看到 JAVA 應用程式使用進程 ID 號 （pid） 運行。

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

執行下面的命令以開始 30 秒的 JVM 錄製。 這將分析 JVM 並在主目錄中創建名為*jfr_example.jfr*的 JFR 檔。 （將 116 替換為 JAVA 應用的 pid。

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

在 30 秒間隔內，您可以通過運行`jcmd 116 JFR.check`來驗證正在發生的錄製。 這將顯示給定 JAVA 進程的所有錄製。

#### <a name="continuous-recording"></a>連續錄製

您可以使用祖魯飛行記錄器持續分析 JAVA 應用程式，對運行時性能的影響最小（[源](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)）。 為此，請運行以下 Azure CLI 命令以創建名為JAVA_OPTS的應用設置，並具有必要的配置。 啟動應用時，JAVA_OPTS應用設置的內容將傳遞給`java`該命令。

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

錄製開始後，您可以隨時使用 命令`JFR.dump`轉儲當前錄製資料。

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

有關詳細資訊，請參閱[Jcmd 命令參考](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)。

### <a name="analyzing-recordings"></a>分析錄音

使用[FTPS](../deploy-ftp.md)將 JFR 檔下載到本地電腦。 要分析JFR檔，下載並安裝[祖魯任務控制](https://www.azul.com/products/zulu-mission-control/)。 有關祖魯任務控制的說明，請參閱[Azul 文檔](https://docs.azul.com/zmc/)和[安裝說明](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)。

## <a name="customization-and-tuning"></a>自訂和調整

適用于 Linux 的 Azure 應用服務支援通過 Azure 門戶和 CLI 進行開箱即用的調優和自訂。 查看以下有關非 JAVA 特定 Web 應用配置的文章：

- [進行應用程式設定](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [設定自訂網域](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [配置 SSL 綁定](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [新增 CDN](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [配置庫杜網站](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>設定 Java 執行階段選項

要在 Tomcat 和 JAVA SE 環境中設置分配的記憶體或其他 JVM 運行時選項，請創建`JAVA_OPTS`一個使用這些選項命名的[應用設置](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)。 App Service Linux 會在啟動時將此設定當成環境變數傳遞至 Java 執行階段。

在 Azure 入口網站中，於 Web 應用程式的 [應用程式設定]**** 下，建立名為 `JAVA_OPTS` 且包含其他設定的新應用程式設定 (例如 `-Xms512m -Xmx1204m`)。

要從 Maven 外掛程式配置應用設置，請在 Azure 外掛程式部分添加設置/值標記。 以下示例設置特定的最小和最大 JAVA 堆大小：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

執行具有其 App Service 方案中某個部署位置的單一應用程式開發人員，可以使用下列選項：

- B1 和 S1 實例：`-Xms1024m -Xmx1024m`
- B2 和 S2 實例：`-Xms3072m -Xmx3072m`
- B3 和 S3 實例：`-Xms6144m -Xmx6144m`

調整應用程式堆積設定時，請檢閱 App Service 方案詳細資料，並考慮多個應用程式和部署位置需求以尋找最佳的記憶體配置。

如果要部署 JAR 應用程式，則應將其命名為*app.jar，* 以便內置映射能夠正確標識你的應用。 （Maven 外掛程式會自動進行此重命名。如果您不希望將 JAR 重命名為*app.jar，* 則可以上載帶有命令的 shell 腳本來運行 JAR。 然後，在入口網站的 [設定] 區段中，將此指令碼的完整路徑貼到 [[啟動檔案](app-service-linux-faq.md#built-in-images)] 文字方塊中。 啟動指令碼不會從其放置所在的目錄來執行。 因此，請一律使用絕對路徑在啟動指令碼中參考檔案 (例如： `java -jar /home/myapp/myapp.jar`)。

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

### <a name="adjust-startup-timeout"></a>調整啟動超時

如果 JAVA 應用程式特別大，則應增加啟動時間限制。 為此，請創建一個應用程式設定，`WEBSITES_CONTAINER_START_TIME_LIMIT`並將其設置為應用服務在超時之前應等待的秒數。最大值為`1800`秒。

### <a name="pre-compile-jsp-files"></a>預編譯 JSP 檔

為了提高 Tomcat 應用程式的性能，您可以在部署到應用服務之前編譯 JSP 檔。 您可以使用 Apache Sling 提供的[Maven 外掛程式](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html)，或使用此[Ant 生成檔](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)。

## <a name="secure-applications"></a>保護應用程式

在 App Service for Linux 中執行之 Java 應用程式會有一組與其他應用程式相同的[安全性最佳做法](/azure/security/security-paas-applications-using-app-services)。

### <a name="authenticate-users-easy-auth"></a>驗證使用者（簡單身份驗證）

使用 **"身份驗證和授權"** 選項在 Azure 門戶中設置應用身份驗證。 在這裡，您可以使用 Azure Active Directory 或社交登入 (例如 Facebook、Google 或 GitHub) 來啟用驗證。 只有在設定單一驗證提供者時，Azure 入口網站設定才會運作。 如需詳細資訊，請參閱[設定 App Service 應用程式使用 Azure Active Directory 登入](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json)，以及其他身分識別提供者的相關文章。 如果您需要啟用多個登入提供者，請遵循[自訂 App Service 驗證](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json)一文中的指示。

#### <a name="tomcat"></a>Tomcat

Tomcat 應用程式可以通過將主體物件強制轉換到 Map 物件，直接從 servlet 訪問使用者的聲明。 Map 物件將每個聲明類型映射到該類型的聲明集合。 在下面的代碼中，`request`是 的`HttpServletRequest`實例。

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

現在，您可以檢查物件`Map`中是否有任何特定的聲明。 例如，以下程式碼片段遍越遍所有聲明類型並列印每個集合的內容。

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

要登出使用者，`/.auth/ext/logout`請使用路徑。 要執行其他操作，請參閱有關[應用服務身份驗證和授權使用方式](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)的文檔。 也有關于Tomcat [HttpServletRequest介面](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html)及其方法的官方文檔。 以下 servlet 方法也會根據您的應用服務配置進行水合：

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

要禁用此功能，請創建一個值`WEBSITE_AUTH_SKIP_PRINCIPAL`為`1` 要禁用應用服務添加的所有 servlet 篩選器，請創建一個`WEBSITE_SKIP_FILTERS`值 為`1`的設置。

#### <a name="spring-boot"></a>Spring Boot

Spring Boot 開發人員可以使用 [Azure Active Directory Spring Boot 簡易版](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable)，以利用熟悉的 Spring Security 註釋和 API 來保護應用程式。 請確保在*應用程式.屬性*檔中增加最大標頭大小。 建議值為 `16384`。

### <a name="configure-tlsssl"></a>設定 TLS/SSL

按照[Azure 應用服務中帶有 SSL 綁定的自訂 DNS 名稱](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)中的說明進行操作，上載現有 SSL 憑證並將其綁定到應用程式的功能變數名稱。 根據預設，您的應用程式仍然允許 HTTP 連線；請遵循教學課程中的特定步驟，以強制執行 SSL 和 TLS。

### <a name="use-keyvault-references"></a>使用金鑰庫引用

[Azure KeyVault](../../key-vault/key-vault-overview.md)提供具有訪問策略和審核歷史記錄的集中式金鑰管理。 您可以在 KeyVault 中存儲機密（如密碼或連接字串），並通過環境變數在應用程式中訪問這些機密。

首先，按照有關[授予應用對金鑰保存庫存取權限](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault)的說明，並在[應用程式設定中對您的機密進行 KeyVault 引用](../app-service-key-vault-references.md#reference-syntax)。 通過列印環境變數以遠端存取應用服務終端，可以驗證引用是否已解析為機密。

要在 Spring 或 Tomcat 設定檔中注入這些機密，請使用環境變數`${MY_ENV_VAR}`注入語法 （）。 有關 Spring 設定檔，請參閱有關[外部化配置的](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)本文檔。

### <a name="using-the-java-key-store"></a>使用 JAVA 金鑰存儲

預設情況下，在容器啟動時，[上載到 App Service Linux](../configure-ssl-certificate.md)的任何公共或私有證書都將載入到相應的 JAVA 金鑰存儲中。 上傳證書後，需要重新開機應用服務才能將其載入到 JAVA 金鑰存儲中。 公共證書載入到 的金鑰存儲中`$JAVA_HOME/jre/lib/security/cacerts`，私有憑證存放區在 中`$JAVA_HOME/lib/security/client.jks`。

使用 JAVA 金鑰存儲中的證書加密 JDBC 連接可能需要其他配置。 請參閱您選擇的 JDBC 驅動程式的文檔。

- [後格雷SQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [Mysql](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [Mongodb](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>初始化 JAVA 金鑰存儲

要初始化物件`import java.security.KeyStore`，請使用密碼載入金鑰存儲檔。 兩個金鑰存儲的預設密碼為"更改"。

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

#### <a name="manually-load-the-key-store"></a>手動載入金鑰存儲

您可以手動將證書載入到金鑰存儲。 創建應用設置 ，`SKIP_JAVA_KEYSTORE_LOAD`其值為`1`禁用應用服務自動將證書載入到金鑰存儲中。 通過 Azure 門戶上載到應用服務的所有公共證書都存儲在`/var/ssl/certs/`下。 私有憑證存放區在 下`/var/ssl/private/`。

您可以通過打開到應用服務的[SSH 連接](app-service-linux-ssh-support.md)並運行命令`keytool`來交互或調試 JAVA 金鑰工具。 有關命令清單，請參閱["關鍵工具"文檔](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html)。 有關 KeyStore API 的更多資訊，請參閱[官方文檔](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)。

## <a name="configure-apm-platforms"></a>配置 APM 平臺

本節演示如何將部署在 Linux 上的 Azure 應用服務上的 JAVA 應用程式與 NewRelic 和 AppDynamics 應用程式效能監控 （APM） 平臺連接起來。

### <a name="configure-new-relic"></a>設定 NewRelic

1. 在 [NewRelic.com](https://newrelic.com/signup) 建立 NewRelic 帳戶
2. 從NewRelic下載JAVA代理，它將有一個類似于*newrelic-java-x.x.x.zip*的檔案名。
3. 複製您的授權金鑰，以供稍後設定代理程式時使用。
4. [SSH 進入您的應用服務實例](app-service-linux-ssh-support.md)，並創建新的目錄 */home/網站/wwwroot/apm*。
5. 將未打包的 NewRelicJAVA 代理檔上載到 */home/網站/wwwroot/apm*下的目錄中。 您的代理的檔應位於 */home/網站/wwwroot/apm/newrelic。*
6. 在 */home/網站/wwwroot/apm/newrelic/newrelic.yml*處修改 YAML 檔，並將預留位置許可證值替換為您自己的許可證金鑰。
7. 在 Azure 入口網站中，瀏覽至您在 App Service 中的應用程式，並建立新的應用程式設定。
    - 如果您的應用程式使用 **Java SE**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `JAVA_OPTS` 的環境變數。
    - 如果您使用 **Tomcat**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `CATALINA_OPTS` 的環境變數。

### <a name="configure-appdynamics"></a>設定 AppDynamics

1. 在 [AppDynamics.com](https://www.appdynamics.com/community/register/) 建立 AppDynamics 帳戶
2. 從 AppDynamics 網站下載 JAVA 代理，檔案名將類似于*AppServerAgent-x.x.x.xxxxx.zip*
3. [SSH 進入您的應用服務實例](app-service-linux-ssh-support.md)，並創建新的目錄 */home/網站/wwwroot/apm*。
4. 將 JAVA 代理檔上載到 */home/網站/wwwroot/apm*下的目錄中。 您的代理的檔應位於 */home/網站/wwwroot/apm/appdynamics*。
5. 在 Azure 入口網站中，瀏覽至您在 App Service 中的應用程式，並建立新的應用程式設定。
    - 如果您使用 **Java SE**，請使用 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 值建立名為 `JAVA_OPTS` 的環境變數，其中 `<app-name>` 是您的 App Service 名稱。
    - 如果您使用 **Tomcat**，請使用 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 值建立名為 `CATALINA_OPTS` 的環境變數，其中 `<app-name>` 是您的 App Service 名稱。

> [!NOTE]
> 如果您已經有 `JAVA_OPTS` 或 `CATALINA_OPTS` 的環境變數，請將 `-javaagent:/...` 選項附加至目前值的結尾。

## <a name="configure-jar-applications"></a>配置 JAR 應用程式

### <a name="starting-jar-apps"></a>啟動 JAR 應用

預設情況下，應用服務希望您的 JAR 應用程式被命名為*app.jar*。 如果它具有此名稱，它將自動運行。 對於 Maven 使用者，您可以通過在`<finalName>app</finalName>`*pom.xml*`<build>`節中包括 JAR 名稱來設置 JAR 名稱。 您可以通過設置`archiveFileName`屬性[在 Gradle 中執行相同的操作](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName)。

如果要對 JAR 使用不同的名稱，還必須提供執行 JAR 檔的[啟動命令](app-service-linux-faq.md#built-in-images)。 例如： `java -jar my-jar-app.jar` 。 您可以在門戶、配置>常規設置下或使用名為 的應用程式`STARTUP_COMMAND`設置設置啟動命令的值。

### <a name="server-port"></a>伺服器連接埠

App Service Linux 將傳入請求路由到埠 80，因此應用程式也應偵聽埠 80。 您可以在應用程式的配置（如 Spring*的應用程式.屬性*檔）或啟動命令（例如。 `java -jar spring-app.jar --server.port=80` 請參閱以下常見 JAVA 框架的文檔：

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [斯派克賈瓦](http://sparkjava.com/documentation#embedded-web-server)
- [米微特](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [播放框架](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [頂點](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [誇庫斯](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>資料來源

### <a name="tomcat"></a>Tomcat

這些指示適用於所有資料庫連線。 您將必須在預留位置中填入您所選資料庫的驅動程式類別名稱和 JAR 檔案。 下表提供常見資料庫的類別名稱和驅動程式下載。

| 資料庫   | 驅動程式類別名稱                             | JDBC 驅動程式                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [下載](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [下載](https://dev.mysql.com/downloads/connector/j/) (請選取 [Platform Independent] \(不受平台影響\)) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [下載](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

要將 Tomcat 配置為使用 JAVA 資料庫連接 （JDBC） 或 JAVA 持久性`CATALINA_OPTS`API （JPA），請首先自訂 Tomcat 在啟動時讀取的環境變數。 請透過 [App Service Maven 外掛程式](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)中的應用程式設定來設定這些值：

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

或者在 Azure 門戶中的 **"配置** > **應用程式設定"** 頁中設置環境變數。

接著，決定資料來源應僅供在 Tomcat Servlet 上執行的一個應用程式還是所有應用程式使用。

#### <a name="application-level-data-sources"></a>應用程式級資料來源

1. 在專案的*META-INF/* 目錄中創建*上下文.xml*檔。 如果*META-INF/* 目錄不存在，則創建該目錄。

2. 在*coNtext.xml*中`Context`，添加一個元素以將資料來源連結到 JNDI 位址。 以上表中您驅動程式的類別名稱取代 `driverClassName` 預留位置。

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

3. 更新應用程式的*Web.xml*以在應用程式中使用資料來源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>共用伺服器級資源

添加共用的伺服器級資料來源需要編輯 Tomcat 的伺服器.xml。 首先，上載[啟動腳本](app-service-linux-faq.md#built-in-images)，並在**配置** > **啟動命令**中將路徑設置為腳本。 您可以使用[FTP](../deploy-ftp.md)上載啟動腳本。

啟動腳本將[xsl 轉換為](https://www.w3schools.com/xml/xsl_intro.asp)server.xml 檔，並將生成的 xml 檔輸出`/usr/local/tomcat/conf/server.xml`到 。 啟動腳本應通過 apk 安裝 libxslt。 您的 xsl 檔和啟動腳本可以通過 FTP 上傳。 下面是一個啟動腳本示例。

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

下面提供了一個示例 xsl 檔。 示例 xsl 檔向 Tomcat server.xml 添加了一個新的連接器節點。

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

最後，將驅動程式 JAR 放在 Tomcat 類路徑中並重新啟動應用服務。

1. 通過將 JDBC 驅動程式檔放在 */home/tomcat/lib*目錄中，確保它們可供 Tomcat 類載入器使用。 （如果此目錄不存在，請創建該目錄。要將這些檔上載到應用服務實例，請執行以下步驟：

    1. 在[雲外殼](https://shell.azure.com)中，安裝 Webapp 擴展：

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. 運行以下 CLI 命令以創建從本地系統到應用服務的 SSH 隧道：

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. 使用 SFTP 用戶端連接到本地隧道埠，並將檔上載到 */home/tomcat/lib*資料夾。

    或著，您也可以使用 FTP 用戶端來上傳 JDBC 驅動程式。 請依照這些[指示來取得您的 FTP 認證](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json) \(英文\)。

2. 如果您已建立伺服器層級的資料來源，請重新啟動 App Service Linux 應用程式。 Tomcat 會將 `CATALINA_BASE` 重設為 `/home/tomcat`，並使用已更新的設定。

### <a name="spring-boot"></a>Spring Boot

要連接到 Spring Boot 應用程式中的資料來源，我們建議創建連接字串並將其注入*到應用程式.*

1. 在應用服務頁面的"配置"部分中，為字串設置名稱，將 JDBC 連接字串粘貼到值欄位中，並將類型設置為"自訂"。 您可以選擇將此連接字串設置為插槽設置。

    此連接字串可供我們的應用程式作為名為 的環境變數訪問`CUSTOMCONNSTR_<your-string-name>`。 例如，我們在上面創建的連接字串將命名為`CUSTOMCONNSTR_exampledb`。

2. 在*應用程式.屬性*檔中，使用環境變數名稱引用此連接字串。 對於我們的示例，我們將使用以下內容。

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

有關本主題的詳細資訊，請參閱[有關資料訪問](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html)和[外部化配置](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)的 Spring Boot 文檔。

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>使用 Redis 作為與 Tomcat 的會話緩存

您可以將 Tomcat 配置為使用外部會話存儲，如[Redis 的 Azure 緩存](/azure/azure-cache-for-redis/)。 這使您能夠在使用者轉移到應用的另一個實例時保留使用者會話狀態（如購物車資料），例如發生自動縮放、重新開機或容錯移轉時。

要將 Tomcat 與 Redis 一起使用，必須將應用配置為使用[持久管理器](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html)實現。 以下步驟使用[Pivotal 會話管理器：redis 存儲](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store)作為示例來解釋此過程。

1. 打開 Bash 終端，`<variable>=<value>`並用於設置以下每個環境變數。

    | 變數                 | 值                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | 包含應用服務實例的資源組的名稱。       |
    | WEBAPP_NAME              | 應用服務實例的名稱。                                     |
    | WEBAPP_PLAN_NAME         | 應用服務方案的名稱。                                         |
    | 區域                   | 託管應用的區域的名稱。                           |
    | REDIS_CACHE_NAME         | Redis 實例的 Azure 緩存的名稱。                           |
    | REDIS_PORT               | Redis 緩存偵聽的 SSL 埠。                             |
    | REDIS_PASSWORD           | 實例的主訪問金鑰。                                  |
    | REDIS_SESSION_KEY_PREFIX | 指定用於標識來自應用的會話鍵的值。 |

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

    通過查看服務**實例的屬性或****便捷鍵**部分，可以在 Azure 門戶上查找名稱、埠和訪問金鑰資訊。

2. 創建或更新應用的*src/主/webapp/META-INF/coNtext.xml*檔，包含以下內容：

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

    此檔指定並配置應用的會話管理器實現。 它使用您在上一步中設置的環境變數來將您的帳戶資訊從原始檔案中排除。

3. 使用 FTP 將會話管理器的 JAR 檔上載到應用服務實例，並將其放在 */home/tomcat/lib*目錄中。 有關詳細資訊，請參閱使用[FTP/S 將應用部署到 Azure 應用服務](https://docs.microsoft.com/azure/app-service/deploy-ftp)。

4. 禁用應用服務實例的[會話關聯 Cookie。](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) 可以通過導航到應用，然後設置 **"配置>常規設置> ARR 關聯**設置為**Off，** 從 Azure 門戶執行此操作。 或者，您可以使用以下命令：

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    預設情況下，應用服務將使用會話關聯 Cookie 來確保具有現有會話的用戶端請求路由到應用程式的同一實例。 此預設行為不需要任何配置，但在重新開機應用實例或將流量重新路由到其他實例時，它無法保留使用者會話狀態。 禁用[現有 ARR 實例關聯](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)配置以關閉基於會話 Cookie 的路由時，允許配置的會話存儲在不受干擾的情況下運行。

5. 導航到應用服務實例**的屬性部分，** 並查找**其他出站 IP 位址**。 這些表示應用的所有可能的出站 IP 位址。 複製這些，以便在下一步中使用。

6. 對於每個 IP 位址，請在 Azure 緩存中為 Redis 實例創建防火牆規則。 您可以在 Redis 實例的**防火牆**部分在 Azure 門戶上執行此操作。 為每個規則提供唯一的名稱，並將 **"開始 IP 位址"** 和 **"結束 IP 位址**"值設置為相同的 IP 位址。

7. 導航到 Redis 實例的高級**設置**部分，並將**僅通過 SSL 允許訪問**設置為 **"否**"。 這使應用服務實例能夠通過 Azure 基礎結構與 Redis 緩存進行通信。

8. 更新`azure-webapp-maven-plugin`應用*pom.xml*檔中的配置，以參考您的 Redis 帳戶資訊。 此檔使用以前設置的環境變數來將帳戶資訊從原始檔案中排除。

    如有必要，請將 `1.7.0` 變更為最新版的 [Maven 外掛程式 (適用於 Azure App Service)](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)。

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>
            <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>

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

9. 重新生成和重新部署應用。

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

你的應用現在將使用 Redis 緩存進行會話管理。

有關可用於測試這些說明的示例，請參閱 GitHub 上的[縮放狀態 java-Web 應用](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure)在 azure 存儲庫。

## <a name="docker-containers"></a>Docker 容器

若要在您的容器中使用支援 Azure 的 Zulu JDK，請務必提取並使用預先建置映像 (如[適用於 Azure 的支援 Azul Zulu Enterprise 下載頁面](https://www.azul.com/downloads/azure-only/zulu/) \(英文\) 所述)，或使用來自 [Microsoft Java GitHub 存放庫](https://github.com/Microsoft/java/tree/master/docker) 的 `Dockerfile` 範例。

## <a name="statement-of-support"></a>支援聲明

### <a name="runtime-availability"></a>運行時可用性

App Service for Linux 支援 Java Web 應用程式受控裝載的兩個執行階段：

- [Tomcat servlet 容器](https://tomcat.apache.org/)，適用於執行封裝為 Web 封存 (WAR) 檔案的應用程式。 支援的版本是 8.5 和 9.0。
- Java SE 執行階段環境，適用於執行封裝為 Java 封存 (JAR) 檔案的應用程式。 支援的版本是 JAVA 8 和 11。

### <a name="jdk-versions-and-maintenance"></a>JDK 版本和維護

OpenJDK 的 Azul Zulu Enterprise 組建是免費、多平台、可實際執行的 OpenJDK 散發套件，適用於 Azure 和 Azure Stack，由 Microsoft 與 Azul Systems 提供支援。 其中包含建置及執行 Java SE 應用程式所需的所有元件。 你可以從[JAVAJDK安裝JDK](https://aka.ms/azure-jdks)安裝。

每年 1 月、4 月、7 月和 10 月都會以季為單位自動修補支援的 JDK。

### <a name="security-updates"></a>安全性更新

只要主要資訊安全漏洞的修補程式和修正程式在 Azul Systems 中變成可用，就予以發行。 在 [NIST Common Vulnerability Scoring System 第 2 版](https://nvd.nist.gov/cvss.cfm)上，「主要」弱點是由基本分數 9.0 或更大值定義。

### <a name="deprecation-and-retirement"></a>取代和淘汰

如果將淘汰支援的 Java 執行階段，則使用受影響執行階段的 Azure 開發人員將會在執行階段淘汰之前至少六個月收到淘汰通知。

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>後續步驟

瀏覽[適用於 Java 開發人員的 Azure](/java/azure/) 中心，以找出 Azure 快速入門、教學課程和 Java 參考文件。

[App Service Linux 常見問題集](app-service-linux-faq.md)中回答了非 Java 開發特定的一般 App Service for Linux 使用問題。
