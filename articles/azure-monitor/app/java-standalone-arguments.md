---
title: 新增 JVM arg-適用于 JAVA 的 Azure 監視器 Application Insights
description: 如何新增可啟用 Azure 監視器 JAVA Application Insights 的 JVM 參數
ms.topic: conceptual
ms.date: 04/16/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: c73e3bd82cc87518fb2077e87e9ce943e040bf4b
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387372"
---
# <a name="adding-the-jvm-arg---azure-monitor-application-insights-for-java"></a>新增 JVM arg-適用于 JAVA 的 Azure 監視器 Application Insights



## <a name="azure-environments"></a>Azure 環境

設定 [應用程式服務](../../app-service/configure-language-java.md#set-java-runtime-options)。

## <a name="spring-boot"></a>Spring Boot

將 JVM 引數新增至 `-javaagent:path/to/applicationinsights-agent-3.0.0.jar` 之前的位置 `-jar` ，例如：

```
java -javaagent:path/to/applicationinsights-agent-3.0.0.jar -jar <myapp.jar>
```

## <a name="spring-boot-via-docker-entry-point"></a>透過 Docker 進入點的彈簧開機

如果您使用 *exec* 表單，請將參數加入參數清單中的參數 `"-javaagent:path/to/applicationinsights-agent-3.0.0.jar"` 清單 `"-jar"` ，例如：

```
ENTRYPOINT ["java", "-javaagent:path/to/applicationinsights-agent-3.0.0.jar", "-jar", "<myapp.jar>"]
```

如果您使用 *shell* 表單，請 `-javaagent:path/to/applicationinsights-agent-3.0.0.jar` 在之前的某個位置新增 JVM 參數 `-jar` ，例如：

```
ENTRYPOINT java -javaagent:path/to/applicationinsights-agent-3.0.0.jar -jar <myapp.jar>
```

## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux) 

### <a name="tomcat-installed-via-apt-get-or-yum"></a>透過或安裝 Tomcat `apt-get``yum`

如果您是透過或安裝 Tomcat `apt-get` `yum` ，您應該會有一個檔案 `/etc/tomcat8/tomcat8.conf` 。  將這一行新增至該檔案的結尾：

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>透過下載和解壓縮安裝 Tomcat

如果您是透過下載並解壓縮來安裝 Tomcat [https://tomcat.apache.org](https://tomcat.apache.org) ，您應該會有一個檔案 `<tomcat>/bin/catalina.sh` 。  使用下列內容，在名為的相同目錄中建立新檔案 `<tomcat>/bin/setenv.sh` ：

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0.jar"
```

如果檔案 `<tomcat>/bin/setenv.sh` 已存在，則修改該檔案並新增 `-javaagent:path/to/applicationinsights-agent-3.0.0.jar` 至 `CATALINA_OPTS` 。


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows) 

### <a name="running-tomcat-from-the-command-line"></a>從命令列執行 Tomcat

找出 `<tomcat>/bin/catalina.bat` 檔案。  使用下列內容，在名為的相同目錄中建立新檔案 `<tomcat>/bin/setenv.bat` ：

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0.jar
```

引號並非必要，但如果您想要包含引號，適當的位置如下：

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0.jar"
```

如果檔案 `<tomcat>/bin/setenv.bat` 已存在，只要修改該檔案並新增 `-javaagent:path/to/applicationinsights-agent-3.0.0.jar` 至 `CATALINA_OPTS` 。

### <a name="running-tomcat-as-a-windows-service"></a>以 Windows 服務的形式執行 Tomcat

找出 `<tomcat>/bin/tomcat8w.exe` 檔案。  執行該可執行檔，並將加入至索引標籤 `-javaagent:path/to/applicationinsights-agent-3.0.0.jar` `Java Options` 下的 `Java` 。


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>獨立伺服器

將檔案 `-javaagent:path/to/applicationinsights-agent-3.0.0.jar` 中的現有 `JAVA_OPTS` 環境變數新增 `JBOSS_HOME/bin/standalone.conf` (Linux) 或 `JBOSS_HOME/bin/standalone.conf.bat` (Windows) ：

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>網域伺服器

新增 `-javaagent:path/to/applicationinsights-agent-3.0.0.jar` 至現有的 `jvm-options` `JBOSS_HOME/domain/configuration/host.xml` ：

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.0.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

如果您在單一主機上執行多部受管理的伺服器，則必須將新增 `applicationinsights.agent.id` 至 `system-properties` `server` ：

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

指定的 `applicationinsights.agent.id` 值必須是唯一的。 它是用來在 applicationinsights 目錄下建立子目錄，因為每個 JVM 程式都需要自己的本機 applicationinsights config 和本機 applicationinsights 記錄檔。 此外，如果向中央收集器報告，則 `applicationinsights.properties` 會由多部受管理的伺服器共用檔案，因此需要指定的 `applicationinsights.agent.id` 才能覆寫 `agent.id` 該共用檔案中的設定。 `applicationinsights.agent.rollup.id``system-properties`如果您需要覆寫每部受管理伺服器的設定，就可以在伺服器中指定相同的 `agent.rollup.id` 。


## <a name="jetty-9"></a>Jetty 9

將下列幾行新增至 `start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0.jar
```


## <a name="payara-5"></a>Payara 5

新增 `-javaagent:path/to/applicationinsights-agent-3.0.0.jar` 至現有的 `jvm-options` `glassfish/domains/domain1/config/domain.xml` ：

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.0.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

開啟管理主控台移至 [ **伺服器] > WebSphere** 應用程式伺服器 > 應用程式伺服器]，選擇適當的應用程式伺服器，然後按一下： 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
在 [一般 JVM 引數] 中，新增下列各項：
```
-javaagent:path/to/applicationinsights-agent-3.0.0.jar
```
之後，儲存並重新啟動應用程式伺服器。


## <a name="openliberty-18"></a>OpenLiberty 18

`jvm.options`在伺服器目錄中建立新檔案 (例如 `<openliberty>/usr/servers/defaultServer`) ，然後新增這一行：
```
-javaagent:path/to/applicationinsights-agent-3.0.0.jar
```
