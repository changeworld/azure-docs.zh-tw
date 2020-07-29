---
title: 監視在任何環境中執行的 JAVA 應用程式-Azure 監視器 Application Insights
description: 應用程式效能監視，適用于使用 JAVA 獨立代理程式在任何環境上執行的 JAVA 應用程式，不需檢測應用程式。 分散式追蹤和應用程式對應。
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: b1a23c01fdf8a0145c26b709f36709a7b710103f
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374298"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>設定適用于 Azure 監視器 Application Insights 的 JVM args JAVA 獨立代理程式



## <a name="azure-environments"></a>Azure 環境

設定[應用程式服務](../../app-service/configure-language-java.md#set-java-runtime-options)。

## <a name="spring-boot"></a>Spring Boot

`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`在之前的某個位置新增 JVM 參數 `-jar` ，例如：

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

## <a name="spring-boot-via-docker-entry-point"></a>透過 Docker 進入點的彈簧開機

如果您使用*exec*格式，請將參數加入參數的參數 `"-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"` 清單 `"-jar"` 中，例如：

```
ENTRYPOINT ["java", "-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar", "-jar", "<myapp.jar>"]
```

如果您使用*shell*表單，請在之前的某個位置新增 JVM 參數 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `-jar` ，例如：

```
ENTRYPOINT java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

## <a name="tomcat-8-linux"></a>Tomcat 8 （Linux）

### <a name="tomcat-installed-via-apt-get-or-yum"></a>透過或安裝 Tomcat `apt-get``yum`

如果您是透過或安裝 Tomcat `apt-get` `yum` ，則應該有檔案 `/etc/tomcat8/tomcat8.conf` 。  將下面這一行新增至該檔案的結尾：

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>透過下載和解壓縮安裝的 Tomcat

如果您已透過下載和解壓縮來安裝 Tomcat [https://tomcat.apache.org](https://tomcat.apache.org) ，則您應該會有一個檔案 `<tomcat>/bin/catalina.sh` 。  使用下列內容，在名為的相同目錄中建立新的檔案 `<tomcat>/bin/setenv.sh` ：

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

如果檔案 `<tomcat>/bin/setenv.sh` 已經存在，則修改該檔案，並將新增 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` 至 `CATALINA_OPTS` 。


## <a name="tomcat-8-windows"></a>Tomcat 8 （Windows）

### <a name="running-tomcat-from-the-command-line"></a>從命令列執行 Tomcat

找出 `<tomcat>/bin/catalina.bat` 檔案。  使用下列內容，在名為的相同目錄中建立新的檔案 `<tomcat>/bin/setenv.bat` ：

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

引號並不是必要的，但如果您想要將它們包含在內，適當的位置如下：

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

如果檔案 `<tomcat>/bin/setenv.bat` 已經存在，只需要修改該檔案，並將新增 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` 至 `CATALINA_OPTS` 。

### <a name="running-tomcat-as-a-windows-service"></a>執行 Tomcat 做為 Windows 服務

找出 `<tomcat>/bin/tomcat8w.exe` 檔案。  執行該可執行檔，並將新增至索引標籤底下 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` 的 `Java Options` `Java` 。


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>獨立伺服器

將新增 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` 至檔案 `JAVA_OPTS` `JBOSS_HOME/bin/standalone.conf` （Linux）或 `JBOSS_HOME/bin/standalone.conf.bat` （Windows）中的現有環境變數：

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>網域伺服器

將加入 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` 至現有 `jvm-options` 的 `JBOSS_HOME/domain/configuration/host.xml` ：

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

如果您在單一主機上執行多部受管理的伺服器，則必須將新增 `applicationinsights.agent.id` 至 `system-properties` 每個的 `server` ：

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

指定的 `applicationinsights.agent.id` 值必須是唯一的。 它可用來在 applicationinsights 目錄下建立子目錄，因為每個 JVM 程式都需要自己的本機 applicationinsights config 和本機 applicationinsights 記錄檔。 此外，如果向中央收集器報告，則檔案 `applicationinsights.properties` 會由多個受管理的伺服器共用，因此 `applicationinsights.agent.id` 需要指定才能覆寫 `agent.id` 該共用檔案中的設定。 `applicationinsights.agent.rollup.id``system-properties`如果您需要覆寫 `agent.rollup.id` 每個受管理伺服器的設定，可以在伺服器的中指定。


## <a name="jetty-9"></a>Jetty 9

將這些行新增至`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Payara 5

將加入 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` 至現有 `jvm-options` 的 `glassfish/domains/domain1/config/domain.xml` ：

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

開啟 [管理主控台]，移至 [**伺服器] > WebSphere 應用**程式伺服器] > 應用程式伺服器，選擇適當的應用程式伺服器，然後按一下： 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
在 [一般 JVM 引數] 中，新增下列內容：
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
之後，請儲存並重新啟動應用程式伺服器。


## <a name="openliberty-18"></a>OpenLiberty 18

`jvm.options`在伺服器目錄中建立新檔案（例如 `<openliberty>/usr/servers/defaultServer` ），並加入這一行：
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
