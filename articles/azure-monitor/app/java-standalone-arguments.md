---
title: 監視在任何環境中執行的 Java 應用程式 - Azure 監視器應用程式見解
description: 應用程式效能監視,適用於使用 JAVA 獨立代理在任何環境中運行的 Java 應用程式,而無需檢測應用程式。 分散式跟蹤和應用程式映射。
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 08a83fbc05276808b62a0391a5c4217cc09f6d00
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641871"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>為 Azure 監視器應用程式見解設定 JVM args Java 獨立代理



## <a name="azure-environments"></a>Azure 環境

設定[套用服務](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options)。

## <a name="spring-boot"></a>Spring Boot

在之前`-jar <myapp.jar>`的某個地方添加`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`JVM arg,例如:

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

> [!NOTE]
> 之後放置的`-jar <myapp.jar>`Args 作為程式 args 傳遞給應用程式。


## <a name="tomcat-8-linux"></a>湯姆卡特 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>通過`apt-get`或`yum`

如果您通過`apt-get``yum`或 () 安裝了 Tomcat,則應`/etc/tomcat8/tomcat8.conf`有一個檔。  將此行新增到該檔案的末尾:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>透過下載和解壓縮安裝的 Tomcat

如果你通過下載和解壓縮安裝[https://tomcat.apache.org](https://tomcat.apache.org)Tomcat,那麼你應該有一個檔`<tomcat>/bin/catalina.sh`。  在同一目錄`<tomcat>/bin/setenv.sh`建立包含以下內容的新檔案:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

如果檔案`<tomcat>/bin/setenv.sh`已存在,則修改此檔案並加入`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar``CATALINA_OPTS`。


## <a name="tomcat-8-windows"></a>湯姆卡特 8 (視窗)

### <a name="running-tomcat-from-the-command-line"></a>從命令列執行 Tomcat

找出 `<tomcat>/bin/catalina.bat` 檔案。  在同一目錄`<tomcat>/bin/setenv.bat`建立包含以下內容的新檔案:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

報價不是必需的,但如果要包括報價,正確的放置位置是:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

如果檔案`<tomcat>/bin/setenv.bat`已存在,只需修改此檔案並加入`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar``CATALINA_OPTS`。

### <a name="running-tomcat-as-a-windows-service"></a>將 Tomcat 為 Windows 服務執行

找出 `<tomcat>/bin/tomcat8w.exe` 檔案。  運行該可執行檔並`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`添加到選項`Java Options`卡`Java`下。


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>獨立伺服器

新增到`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar``JAVA_OPTS``JBOSS_HOME/bin/standalone.conf`檔案 (Linux) 或`JBOSS_HOME/bin/standalone.conf.bat`(Windows) 中的現有環境變數:

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>網域伺服器

新增到`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`的現有`jvm-options``JBOSS_HOME/domain/configuration/host.xml`:

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

如果在單一主機上執行多個託管伺服器,則需要為每個伺服器新增`applicationinsights.agent.id`到 : `system-properties` `server`

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

指定的`applicationinsights.agent.id`值必須是唯一的。 它用於在應用程式見解目錄下創建子目錄,因為每個 JVM 進程都需要其自己的本地應用程式見解配置和本地應用程式見解日誌檔。 此外,如果向中央收集器報告,`applicationinsights.properties`該檔將由多個託管伺服器共用,因此需要指定`applicationinsights.agent.id`來覆蓋該共享檔`agent.id`中的 設置。 `applicationinsights.agent.rollup.id``system-properties`如果需要覆蓋每個託管伺服器的設定,`agent.rollup.id`可以在伺服器中同樣指定。


## <a name="jetty-9"></a>碼頭 9

新增這些行`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>帕亞拉 5

新增到`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`的現有`jvm-options``glassfish/domains/domain1/config/domain.xml`:

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

## <a name="websphere-8"></a>網路圈 8

開放管理主控台轉到**WebSphere 應用程式伺服器>應用程式伺服器>伺服器**,選擇適當的應用程式伺服器,然後按一下: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
在「通用 JVM 參數」中新增以下內容:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
之後,保存並重新啟動應用程式伺服器。


## <a name="openliberty-18"></a>開放自由 18

在伺服器目錄中建立新`jvm.options`檔案(`<openliberty>/usr/servers/defaultServer`例如 ),然後新增此行:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
