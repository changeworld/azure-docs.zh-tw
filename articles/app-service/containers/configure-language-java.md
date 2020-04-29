---
title: 設定 Linux JAVA 應用程式
description: 瞭解如何為您的應用程式設定預先建立的 JAVA 容器。 本文說明最常見的設定工作。
keywords: azure app service，web 應用程式，linux，oss，java，java ee，jee，javaee
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: f4f6de807628704051cdddf74bcefbed678f8fcd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457887"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>為 Azure App Service 設定 Linux Java 應用程式

Linux 上的 Azure App Service 可讓 JAVA 開發人員在完全受控的 Linux 服務上，快速建立、部署及調整其 Tomcat，或 JAVA Standard Edition （SE）封裝的 web 應用程式。 從命令列或編輯器 (例如 IntelliJ、Eclipse 或 Visual Studio Code)，使用 Maven 外掛程式部署應用程式。

本指南提供 JAVA 開發人員在 App Service 中使用內建 Linux 容器的重要概念和指示。 如果您從未使用過 Azure App Service，請遵循[JAVA 快速入門](quickstart-java.md)。

## <a name="deploying-your-app"></a>部署應用程式

您可以使用[Maven 外掛程式進行 Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) ，以部署 .jar 和 war 檔案。 [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij)或[Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse)也支援使用熱門 ide 進行部署。

否則，您的部署方法將取決於您的封存類型：

- 若要將 .war 檔案部署至 Tomcat，請使用 `/api/wardeploy/` 端點透過 POST 張貼您的封存檔案。 如需有關此 API 的詳細資訊，請參閱[這份文件](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)。
- 若要部署 Java SE 映像上的 .jar 檔案，請使用 Kudu 網站的 `/api/zipdeploy/` 端點。 如需有關此 API 的詳細資訊，請參閱[這份文件](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)。

請勿使用 FTP 來部署 .war 或 .jar。 FTP 工具是為上傳啟動指令碼、相依性或其他執行階段檔案而設計的。 它不是部署網頁的最佳選擇。

## <a name="logging-and-debugging-apps"></a>記錄和偵錯應用程式

透過 Azure 入口網站，可以取得每個應用程式的效能報表、流量視覺化和健康狀態檢查。 如需詳細資訊，請參閱[Azure App Service 診斷總覽](../overview-diagnostics.md)。

### <a name="ssh-console-access"></a>SSH 主控台存取

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>資料流診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

如需詳細資訊，請參閱[Cloud Shell 中的串流記錄](../troubleshoot-diagnostic-logs.md#in-cloud-shell)。

### <a name="app-logging"></a>應用程式記錄

透過 Azure 入口網站或 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) 啟用[應用程式記錄](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows)，設定 App Service 將應用程式的標準主控台輸出和標準主控台錯誤資料流寫入至本機檔案系統或 Azure Blob 儲存體。 設定後的 12 個小時會停用記錄至本機 App Service 檔案系統執行個體。 如果您需要較長的保留期，則請設定應用程式將輸出寫入至 Blob 儲存體容器。 您的 JAVA 和 Tomcat 應用程式記錄可在 */home/LogFiles/Application/* 目錄中找到。

如果您的應用程式使用 [Logback](https://logback.qos.ch/) 或 [Log4j](https://logging.apache.org/log4j) 追蹤，則您可以使用[在 Application Insights 中探索 Java 追蹤記錄](/azure/application-insights/app-insights-java-trace-logs)中的記錄架構設定指示，將這些要檢閱的追蹤轉送至 Azure Application Insights。

### <a name="troubleshooting-tools"></a>疑難排解工具

內建的 JAVA 映射是以[Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html)作業系統為基礎。 使用`apk`封裝管理員來安裝任何疑難排解工具或命令。

### <a name="flight-recorder"></a>飛行記錄器

App Service 上的所有 Linux JAVA 映射都已安裝了祖魯飛行記錄器，因此您可以輕鬆地連接到 JVM 並啟動 profiler 記錄，或產生堆積傾印。

#### <a name="timed-recording"></a>計時記錄

若要開始使用，請透過 SSH 連線到您`jcmd`的 App Service，然後執行命令，以查看執行的所有 JAVA 進程的清單。 除了 jcmd 本身之外，您還應該看到以處理序識別碼（pid）執行的 JAVA 應用程式。

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

執行下列命令，以啟動 JVM 的30秒記錄。 這會分析 JVM，並在主目錄中建立名為*jfr_example JFR*的 JFR 檔案。 （將116取代為 JAVA 應用程式的 pid）。

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

在30秒的間隔期間，您可以藉由執行來驗證錄製是否`jcmd 116 JFR.check`正在進行中。 這會顯示指定 JAVA 進程的所有錄製。

#### <a name="continuous-recording"></a>連續記錄

您可以使用祖魯飛行記錄器，持續對您的 JAVA 應用程式進行程式碼剖析，而對執行時間效能（[來源](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)）的影響最小。 若要這麼做，請執行下列 Azure CLI 命令，以使用必要的設定來建立名為 JAVA_OPTS 的應用程式設定。 當您的應用程式啟動時，會將 JAVA_OPTS `java`應用程式設定的內容傳遞至命令。

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

記錄開始之後，您可以隨時使用`JFR.dump`命令傾印目前的記錄資料。

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

如需詳細資訊，請參閱[Jcmd 命令參考](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)。

### <a name="analyzing-recordings"></a>分析錄製

使用[FTPS](../deploy-ftp.md)將您的 JFR 檔案下載到本機電腦。 若要分析 JFR 檔案，請下載並安裝[祖魯任務控制](https://www.azul.com/products/zulu-mission-control/)。 如需有關祖魯任務控制的指示，請參閱[Azul 檔](https://docs.azul.com/zmc/)和[安裝指示](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)。

## <a name="customization-and-tuning"></a>自訂和調整

適用于 Linux 的 Azure App Service 支援透過 Azure 入口網站和 CLI 進行的微調和自訂。 請參閱下列文章，以瞭解非 JAVA 特定的 web 應用程式設定：

- [進行應用程式設定](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [設定自訂網域](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [設定 SSL 系結](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [新增 CDN](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [設定 Kudu 網站](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>設定 Java 執行階段選項

若要在 Tomcat 和 JAVA SE 環境中設定已配置的記憶體或其他 JVM 執行時間選項，請使用`JAVA_OPTS`選項建立名為的[應用程式設定](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)。 App Service Linux 會在啟動時將此設定當成環境變數傳遞至 Java 執行階段。

在 Azure 入口網站中，於 Web 應用程式的 [應用程式設定]**** 下，建立名為 `JAVA_OPTS` 且包含其他設定的新應用程式設定 (例如 `-Xms512m -Xmx1204m`)。

若要從 Maven 外掛程式設定應用程式設定，請在 Azure 外掛程式區段中新增設定/值標記。 下列範例會設定特定的最小和最大 JAVA 堆積大小：

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

如果您要部署 JAR 應用程式，它應該命名為*app.config* ，讓內建映射能夠正確地識別您的應用程式。 （Maven 外掛程式會自動進行此重新命名）。如果您不想要將 JAR 重新命名為*app.config*，可以使用命令上傳 shell 腳本來執行 JAR。 然後，在入口網站的 [設定] 區段中，將此指令碼的完整路徑貼到 [[啟動檔案](app-service-linux-faq.md#built-in-images)] 文字方塊中。 啟動指令碼不會從其放置所在的目錄來執行。 因此，請一律使用絕對路徑在啟動指令碼中參考檔案 (例如： `java -jar /home/myapp/myapp.jar`)。

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

如果您的 JAVA 應用程式特別大，您應該增加啟動時間限制。 若要這麼做，請建立應用程式`WEBSITES_CONTAINER_START_TIME_LIMIT`設定，並將它設定為 App Service 應該在超時前等待的秒數。最大值為`1800`秒。

### <a name="pre-compile-jsp-files"></a>預先編譯 JSP 檔案

若要改善 Tomcat 應用程式的效能，您可以在部署至 App Service 之前，先編譯 JSP 檔案。 您可以使用 Apache Sling 提供的[Maven 外掛程式](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html)，或使用此[Ant 組建](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)檔案。

## <a name="secure-applications"></a>保護應用程式

在 App Service for Linux 中執行之 Java 應用程式會有一組與其他應用程式相同的[安全性最佳做法](/azure/security/security-paas-applications-using-app-services)。

### <a name="authenticate-users-easy-auth"></a>驗證使用者（簡單驗證）

使用 [**驗證和授權**] 選項，在 [Azure 入口網站中設定應用程式驗證。 在這裡，您可以使用 Azure Active Directory 或社交登入 (例如 Facebook、Google 或 GitHub) 來啟用驗證。 只有在設定單一驗證提供者時，Azure 入口網站設定才會運作。 如需詳細資訊，請參閱[設定 App Service 應用程式使用 Azure Active Directory 登入](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json)，以及其他身分識別提供者的相關文章。 如果您需要啟用多個登入提供者，請遵循[自訂 App Service 驗證](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json)一文中的指示。

#### <a name="tomcat"></a>Tomcat

Tomcat 應用程式可以藉由將主體物件轉換成對應物件，直接從 servlet 存取使用者的宣告。 Map 物件會將每個宣告類型對應到該類型的宣告集合。 在下列程式碼中`request` ，是的實例`HttpServletRequest`。

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

現在您可以檢查`Map`物件是否有任何特定的宣告。 例如，下列程式碼片段會逐一查看所有宣告類型，並列印每個集合的內容。

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

若要將使用者登出，請`/.auth/ext/logout`使用路徑。 若要執行其他動作，請參閱[App Service 驗證和授權使用](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)的檔。 Tomcat [HttpServletRequest 介面](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html)和其方法也有官方檔。 下列 servlet 方法也會根據您的 App Service 設定來序列化：

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

若要停用此功能，請使用值`WEBSITE_AUTH_SKIP_PRINCIPAL`建立名為的`1`應用程式設定。 若要停用 App Service 新增的所有 servlet 篩選器，請`WEBSITE_SKIP_FILTERS`使用值建立名`1`為的設定。

#### <a name="spring-boot"></a>Spring Boot

Spring Boot 開發人員可以使用 [Azure Active Directory Spring Boot 簡易版](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable)，以利用熟悉的 Spring Security 註釋和 API 來保護應用程式。 請務必增加您的*應用程式. properties*檔案中的標頭大小上限。 建議值為 `16384`。

### <a name="configure-tlsssl"></a>設定 TLS/SSL

依照[Azure App Service 中的使用 SSL 系結保護自訂 DNS 名稱](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)中的指示，上傳現有的 ssl 憑證，並將它系結至您應用程式的功能變數名稱。 根據預設，您的應用程式仍然允許 HTTP 連線；請遵循教學課程中的特定步驟，以強制執行 SSL 和 TLS。

### <a name="use-keyvault-references"></a>使用 KeyVault 參考

[Azure KeyVault](../../key-vault/general/overview.md)提供具有存取原則和 audit 歷程記錄的集中式秘密管理。 您可以在 KeyVault 中儲存秘密（例如密碼或連接字串），並透過環境變數存取您應用程式中的這些秘密。

首先，遵循將應用程式[存取權授與 Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault)的指示，並[在應用程式設定中 KeyVault 您的密碼參考](../app-service-key-vault-references.md#reference-syntax)。 您可以在遠端存取 App Service 終端機時，藉由列印環境變數，來驗證參考是否會解析為秘密。

若要將這些秘密插入您的春季或 Tomcat 設定檔中，請使用環境`${MY_ENV_VAR}`變數插入語法（）。 如需春季設定檔，請參閱這[外部化](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)設定的相關檔。

### <a name="using-the-java-key-store"></a>使用 JAVA 金鑰存放區

根據預設，任何[上傳至 App Service Linux](../configure-ssl-certificate.md)的公用或私用憑證，都會在容器啟動時載入個別的 JAVA 金鑰存放區中。 上傳您的憑證之後，您必須重新開機您的 App Service，才能將其載入至 JAVA 金鑰存放區。 公開憑證會載入至的金鑰存放區`$JAVA_HOME/jre/lib/security/cacerts`，而且私用憑證會儲存`$JAVA_HOME/lib/security/client.jks`在中。

您可能需要額外的設定，才能使用 JAVA 金鑰存放區中的憑證來加密 JDBC 連接。 請參閱您所選擇 JDBC 驅動程式的檔。

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>初始化 JAVA 金鑰存放區

若要初始化`import java.security.KeyStore`物件，請以密碼載入金鑰儲存區檔案。 這兩個金鑰存放區的預設密碼為 "changeit"。

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

您可以手動將憑證載入金鑰存放區。 建立應用程式設定， `SKIP_JAVA_KEYSTORE_LOAD`並將的值`1`設為，以停用 App Service 自動將憑證載入金鑰存放區。 透過 Azure 入口網站上傳至 App Service 的所有公用憑證都會儲存`/var/ssl/certs/`在底下。 私人憑證會儲存在`/var/ssl/private/`底下。

您可以藉由開啟 App Service 的 SSH 連線並[執行](app-service-linux-ssh-support.md)命令`keytool`，來互動或調試 JAVA 金鑰工具。 如需命令清單，請參閱[重要工具檔](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html)。 如需金鑰儲存區 API 的詳細資訊，請參閱[官方檔](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)。

## <a name="configure-apm-platforms"></a>設定 APM 平臺

本節說明如何將 Linux 上的 Azure App Service 上部署的 JAVA 應用程式與 NewRelic 和 AppDynamics 應用程式效能監視（APM）平臺連接在一起。

### <a name="configure-new-relic"></a>設定 NewRelic

1. 在 [NewRelic.com](https://newrelic.com/signup) 建立 NewRelic 帳戶
2. 從 NewRelic 下載 JAVA 代理程式，它會有類似*newrelic-java-x*的檔案名。
3. 複製您的授權金鑰，以供稍後設定代理程式時使用。
4. 透過[SSH 連線到您的 App Service 實例](app-service-linux-ssh-support.md)，並建立新的目錄 */home/site/wwwroot/apm*。
5. 將已解壓縮的 NewRelic JAVA 代理程式檔案上傳至 */home/site/wwwroot/apm*底下的目錄。 您的代理程式檔案應位於 */home/site/wwwroot/apm/newrelic*中。
6. 在 */home/site/wwwroot/apm/newrelic/newrelic.yml*修改 YAML 檔案，並將預留位置授權值取代為您自己的授權金鑰。
7. 在 Azure 入口網站中，瀏覽至您在 App Service 中的應用程式，並建立新的應用程式設定。
    - 如果您的應用程式使用 **Java SE**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `JAVA_OPTS` 的環境變數。
    - 如果您使用 **Tomcat**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `CATALINA_OPTS` 的環境變數。

### <a name="configure-appdynamics"></a>設定 AppDynamics

1. 在 [AppDynamics.com](https://www.appdynamics.com/community/register/) 建立 AppDynamics 帳戶
2. 從 AppDynamics 網站下載 JAVA 代理程式，檔案名會類似*AppServerAgent-x*的名稱。 *
3. 透過[SSH 連線到您的 App Service 實例](app-service-linux-ssh-support.md)，並建立新的目錄 */home/site/wwwroot/apm*。
4. 將 JAVA 代理程式檔案上傳至 */home/site/wwwroot/apm*底下的目錄。 您的代理程式檔案應位於 */home/site/wwwroot/apm/appdynamics*中。
5. 在 Azure 入口網站中，瀏覽至您在 App Service 中的應用程式，並建立新的應用程式設定。
    - 如果您使用 **Java SE**，請使用 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 值建立名為 `JAVA_OPTS` 的環境變數，其中 `<app-name>` 是您的 App Service 名稱。
    - 如果您使用 **Tomcat**，請使用 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 值建立名為 `CATALINA_OPTS` 的環境變數，其中 `<app-name>` 是您的 App Service 名稱。

> [!NOTE]
> 如果您已經有 `JAVA_OPTS` 或 `CATALINA_OPTS` 的環境變數，請將 `-javaagent:/...` 選項附加至目前值的結尾。

## <a name="configure-jar-applications"></a>設定 JAR 應用程式

### <a name="starting-jar-apps"></a>啟動 JAR 應用程式

根據預設，App Service 會預期您的 JAR 應用程式會命名為*app.config*。 如果它有此名稱，則會自動執行。 針對 Maven 使用者，您可以在*pom*的`<build>`區段中包含`<finalName>app</finalName>`來設定 JAR 名稱。 您可以藉由設定`archiveFileName`屬性，[在 Gradle 中執行相同的](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName)動作。

如果您想要為 JAR 使用不同的名稱，您也必須提供可執行 JAR 檔案的[啟動命令](app-service-linux-faq.md#built-in-images)。 例如： `java -jar my-jar-app.jar` 。 您可以在入口網站中的 [設定] > [一般設定] 底下，或使用名為`STARTUP_COMMAND`的應用程式設定，設定啟動命令的值。

### <a name="server-port"></a>伺服器連接埠

App Service Linux 會將傳入要求路由傳送至埠80，讓您的應用程式也應該在埠80上接聽。 您可以在應用程式的設定中執行此動作（例如，春天的*應用程式. 屬性*檔），或在您的啟動命令`java -jar spring-app.jar --server.port=80`中進行（例如，）。 請參閱下列適用于一般 JAVA 架構的檔：

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJAVA](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play 架構](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>資料來源

### <a name="tomcat"></a>Tomcat

這些指示適用於所有資料庫連線。 您將必須在預留位置中填入您所選資料庫的驅動程式類別名稱和 JAR 檔案。 下表提供常見資料庫的類別名稱和驅動程式下載。

| 資料庫   | 驅動程式類別名稱                             | JDBC 驅動程式                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [下載](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [下載](https://dev.mysql.com/downloads/connector/j/) (請選取 [Platform Independent] \(不受平台影響\)) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [下載](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

若要將 Tomcat 設定為使用 JAVA 資料庫連線（JDBC）或 JAVA 持續性 API （JPA），請`CATALINA_OPTS`先自訂 tomcat 在啟動時所讀入的環境變數。 請透過 [App Service Maven 外掛程式](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)中的應用程式設定來設定這些值：

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

或者 **，在 Azure 入口網站的 [** > 設定**應用程式設定**] 頁面中設定環境變數。

接著，決定資料來源應僅供在 Tomcat Servlet 上執行的一個應用程式還是所有應用程式使用。

#### <a name="application-level-data-sources"></a>應用層級的資料來源

1. 在您專案的*中繼 INF/* 目錄中，建立一個*內容 .xml*檔案。 建立*中繼 INF/* 目錄（如果不存在）。

2. 在*內容 .xml*中，新增`Context`元素以將資料來源連結至 JNDI 位址。 以上表中您驅動程式的類別名稱取代 `driverClassName` 預留位置。

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

3. 更新應用程式的*web.config* ，以在您的應用程式中使用資料來源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>共用伺服器層級資源

新增共用的伺服器層級資料來源，會要求您編輯 Tomcat 的 server .xml。 首先，上傳[啟動腳本](app-service-linux-faq.md#built-in-images)，並在 [設定] [啟動] > **命令****中設定腳本的路徑**。 您可以使用[FTP](../deploy-ftp.md)上傳啟動腳本。

您的啟動腳本會對 server .xml 檔案進行[xsl 轉換](https://www.w3schools.com/xml/xsl_intro.asp)，並將產生的 xml 檔案輸出至`/usr/local/tomcat/conf/server.xml`。 啟動腳本應該透過 apk 安裝 libxslt。 您可以透過 FTP 上傳您的 xsl 檔案和啟動腳本。 以下是啟動腳本範例。

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

以下提供範例 xsl 檔案。 範例 xsl 檔案會將新的連接器節點加入至 Tomcat server .xml。

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

最後，將驅動程式 Jar 放在 Tomcat 路徑中，然後重新開機您的 App Service。

1. 藉由將 JDBC 驅動程式檔案放在 */home/tomcat/lib*目錄中，確保其可供 Tomcat classloader 使用。 （如果此目錄不存在，請加以建立）。若要將這些檔案上傳至您的 App Service 實例，請執行下列步驟：

    1. 在[Cloud Shell](https://shell.azure.com)中，安裝 webapp 擴充功能：

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. 執行下列 CLI 命令，從您的本機系統建立 SSH 通道，以 App Service：

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. 使用您的 SFTP 用戶端連線到本機通道埠，並將檔案上傳至 */home/tomcat/lib*資料夾。

    或著，您也可以使用 FTP 用戶端來上傳 JDBC 驅動程式。 請依照這些[指示來取得您的 FTP 認證](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json) \(英文\)。

2. 如果您已建立伺服器層級的資料來源，請重新啟動 App Service Linux 應用程式。 Tomcat 會將 `CATALINA_BASE` 重設為 `/home/tomcat`，並使用已更新的設定。

### <a name="spring-boot"></a>Spring Boot

若要連接到春季開機應用程式中的資料來源，建議您建立連接字串，並將它們插入您的*應用程式中。 properties*檔案。

1. 在 [App Service] 頁面的 [設定] 區段中，設定字串的名稱，在 [值] 欄位中貼上您的 JDBC 連接字串，並將類型設定為 [自訂]。 您可以選擇性地將此連接字串設定為位置設定。

    我們的應用程式可存取此連接字串，作為名為`CUSTOMCONNSTR_<your-string-name>`的環境變數。 例如，我們在上面建立的連接字串將會命名`CUSTOMCONNSTR_exampledb`為。

2. 在您的*應用程式. properties*檔案中，使用環境變數名稱來參考此連接字串。 在我們的範例中，我們會使用下列程式。

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

如需有關此主題的詳細資訊，請參閱有關資料存取和[外部化](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)設定的[彈簧開機檔](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html)。

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>使用 Redis 作為 Tomcat 的會話快取

您可以設定 Tomcat 使用外部會話存放區，例如[Azure Cache For Redis](/azure/azure-cache-for-redis/)。 這可讓您在使用者轉移至應用程式的另一個實例時，保留使用者會話狀態（例如購物車資料），例如在發生自動調整、重新開機或容錯移轉的情況下。

若要搭配使用 Tomcat 與 Redis，您必須將應用程式設定為使用[PersistentManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html)的執行。 下列步驟會使用[Pivotal 會話管理員來說明此程式： redis-store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store)作為範例。

1. 開啟 Bash 終端機，並`<variable>=<value>`使用來設定下列每個環境變數。

    | 變數                 | 值                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | 包含您 App Service 實例之資源群組的名稱。       |
    | WEBAPP_NAME              | App Service 實例的名稱。                                     |
    | WEBAPP_PLAN_NAME         | App Service 計畫的名稱。                                         |
    | 區域                   | 裝載應用程式的區功能變數名稱稱。                           |
    | REDIS_CACHE_NAME         | Azure Cache for Redis 實例的名稱。                           |
    | REDIS_PORT               | 您的 Redis 快取接聽的 SSL 埠。                             |
    | REDIS_PASSWORD           | 實例的主要存取金鑰。                                  |
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

    您可以在服務實例的 [**屬性**] 或 [**存取金鑰**] 區段中，尋找 Azure 入口網站上的名稱、埠和存取金鑰資訊。

2. 使用下列內容建立或更新您應用程式的*src/main/webapp/META-INF/上下文 .xml*檔案：

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

    此檔案會指定並設定您應用程式的會話管理員執行。 它會使用您在上一個步驟中設定的環境變數，將您的帳戶資訊保留在原始程式檔中。

3. 使用 FTP 將會話管理員的 JAR 檔案上傳到您的 App Service 實例，並將它放在 */home/tomcat/lib*目錄中。 如需詳細資訊，請參閱[使用 FTP/S 將您的應用程式部署到 Azure App Service](https://docs.microsoft.com/azure/app-service/deploy-ftp)。

4. 停用 App Service 實例的[會話親和性 cookie](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) 。 若要這麼做，您可以流覽至您 Azure 入口網站的應用程式，然後將 **[設定 > 一般設定] > [ARR 親和性**] 設為 [**關閉**]。 或者，您可以使用下列命令：

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    根據預設，App Service 會使用會話親和性 cookie，以確保具有現有會話的用戶端要求會路由傳送至應用程式的相同實例。 此預設行為不需要任何設定，但當您的應用程式實例重新開機時，或當流量路由傳送到另一個實例時，就無法保留使用者會話狀態。 當您[停用現有的 ARR 實例相似性](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)設定以關閉以會話 cookie 為基礎的路由時，您可以讓已設定的會話存放區在不受干擾的情況下運作。

5. 流覽至 App Service 實例的 [**屬性**] 區段，然後尋找**其他的輸出 IP 位址**。 這些代表您的應用程式所有可能的輸出 IP 位址。 複製這些，以便在下一個步驟中使用。

6. 針對每個 IP 位址，在您的 Azure Cache for Redis 實例中建立防火牆規則。 您可以從 Redis 實例的 [**防火牆**] 區段中的 [Azure 入口網站] 執行此動作。 為每個規則提供唯一的名稱，並將 [**起始 ip 位址**] 和 [**結束 ip 位址**] 值設為相同的 IP 位址。

7. 流覽至 Redis 實例的 [ **Advanced settings** ] 區段，並將 [**僅允許透過 SSL 存取**] 設定為 [**否**]。 這可讓您的 App Service 實例透過 Azure 基礎結構與 Redis 快取進行通訊。

8. 更新您`azure-webapp-maven-plugin`應用程式的*pom .xml*檔案中的設定，以參考您的 Redis 帳戶資訊。 此檔案會使用您先前設定的環境變數，將您的帳戶資訊保留在原始程式檔中。

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

9. 重建並重新部署您的應用程式。

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

您的應用程式現在會使用您的 Redis 快取來進行會話管理。

如需您可以用來測試這些指示的範例，請參閱 GitHub 上的[調整狀態-java-web 應用程式-azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure)存放庫。

## <a name="docker-containers"></a>Docker 容器

若要在您的容器中使用支援 Azure 的 Zulu JDK，請務必提取並使用預先建置映像 (如[適用於 Azure 的支援 Azul Zulu Enterprise 下載頁面](https://www.azul.com/downloads/azure-only/zulu/) \(英文\) 所述)，或使用來自 [Microsoft Java GitHub 存放庫](https://github.com/Microsoft/java/tree/master/docker) 的 `Dockerfile` 範例。

## <a name="statement-of-support"></a>支援聲明

### <a name="runtime-availability"></a>執行時間可用性

App Service for Linux 支援 Java Web 應用程式受控裝載的兩個執行階段：

- [Tomcat servlet 容器](https://tomcat.apache.org/)，適用於執行封裝為 Web 封存 (WAR) 檔案的應用程式。 支援的版本是 8.5 和 9.0。
- Java SE 執行階段環境，適用於執行封裝為 Java 封存 (JAR) 檔案的應用程式。 支援的版本為 JAVA 8 和11。

### <a name="jdk-versions-and-maintenance"></a>JDK 版本和維護

OpenJDK 的 Azul Zulu Enterprise 組建是免費、多平台、可實際執行的 OpenJDK 散發套件，適用於 Azure 和 Azure Stack，由 Microsoft 與 Azul Systems 提供支援。 其中包含建置及執行 Java SE 應用程式所需的所有元件。 您可以從[JAVA Jdk 安裝](https://aka.ms/azure-jdks)安裝 JDK。

每年 1 月、4 月、7 月和 10 月都會以季為單位自動修補支援的 JDK。

### <a name="security-updates"></a>安全性更新

只要主要資訊安全漏洞的修補程式和修正程式在 Azul Systems 中變成可用，就予以發行。 在 [NIST Common Vulnerability Scoring System 第 2 版](https://nvd.nist.gov/cvss.cfm)上，「主要」弱點是由基本分數 9.0 或更大值定義。

### <a name="deprecation-and-retirement"></a>取代和淘汰

如果將淘汰支援的 Java 執行階段，則使用受影響執行階段的 Azure 開發人員將會在執行階段淘汰之前至少六個月收到淘汰通知。

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>後續步驟

瀏覽[適用於 Java 開發人員的 Azure](/java/azure/) 中心，以找出 Azure 快速入門、教學課程和 Java 參考文件。

[App Service Linux 常見問題集](app-service-linux-faq.md)中回答了非 Java 開發特定的一般 App Service for Linux 使用問題。
