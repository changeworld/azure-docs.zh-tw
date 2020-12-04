---
title: 如何搭配 Azure Application Insights Java SDK 使用 Micrometer
description: 搭配 Application Insights Spring Boot 和非 Spring Boot 應用程式使用 Micrometer 的逐步解說指南。
ms.topic: conceptual
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.date: 11/01/2018
ms.openlocfilehash: a80684fbaa34f8906d321f56d3819039d3798ecb
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2020
ms.locfileid: "96600977"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk-not-recommended"></a>如何使用 Micrometer 搭配 Azure 應用程式 Insights JAVA SDK (不建議使用) 

> [!IMPORTANT]
> 監視 JAVA 應用程式的建議方法是使用自動檢測，而不需要變更程式碼。 Micrometer 遙測會隨 Application Insights JAVA 3.0 代理程式自動收集-請遵循 [Application Insights java 3.0 代理程式](./java-in-process-agent.md)的指導方針。

> [!NOTE]
> Application Insights JAVA SDK 不支援春季 Webflux-請改用 [Application Insights JAVA 3.0 代理程式](./java-in-process-agent.md) 。 
>
> Application Insights 不需要檢測的 [JAVA 3.0 代理程式](./java-on-premises.md) 都支援 Webflux 和 Micrometer。 

Micrometer 應用程式監視會測量以 JVM 為基礎之應用程式程式碼的計量，並可讓您將資料匯出到您慣用的監視系統。 這篇文章會教導您如何搭配 Application Insights Spring Boot 和非 Spring Boot 應用程式使用 Micrometer。

## <a name="using-spring-boot-15x"></a>使用 Spring Boot 1.5x
將以下相依性新增至您的 pom.xml 或 build.gradle 檔案： 
* [Application Insights 春季開機-starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter) 2.5.0 或更新版本
* Micrometer Azure Registry 1.1.0 或更新版本
* [Micrometer Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 或更新版本 (這會反向移植 Spring Framework 中的自動設定程式碼)。
* [ApplicationInsights 資源](./create-new-resource.md)

步驟

1. 更新您的 Spring Boot 應用程式的 pom.xml 檔案，並在其中新增下列相依性：

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>2.5.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. 使用以下屬性搭配 Application Insights 檢測金鑰更新 application.properties 或 yml 檔案：

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. 建置應用程式並執行
2. 上述動作應該能讓您的應用程式啟動，並使用自動收集至 Azure 監視器的預先彙總計量。 如需如何微調 Application Insights Spring Boot 簡易版的詳細資訊，請參閱 [GitHub 上的讀我檔案](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter/README.md)。

## <a name="using-spring-2x"></a>使用 Spring 2.x

將以下相依性新增至您的 pom.xml 或 build.gradle 檔案：

* Application Insights Spring-boot-starter 2.1.2 或更新版本
* Azure-彈簧開機-計量-初學者2.0.7 或更新版本
* [Application Insights 資源](./create-new-resource.md)

步驟：

1. 更新您 Spring Boot 應用程式的 pom.xml 檔案，並在其中新增以下相依性：

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. 使用以下屬性搭配 Application Insights 檢測金鑰更新 application.properties 或 yml 檔案：

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. 建置應用程式並執行
4. 上述動作應該能讓您的應用程式使用自動收集至 Azure 監視器的預先彙總計量。 如需如何微調 Application Insights Spring Boot 簡易版的詳細資訊，請參閱 [GitHub 上的讀我檔案](https://github.com/Microsoft/azure-spring-boot/releases/latest)。

預設度量：

*    針對 Tomcat、JVM、Logback 度量、Log4J 度量、執行時間度量、處理器度量，以及 FileDescriptorMetrics 自動設定的度量。
*    例如，如果類別路徑上有 Netflix Hystrix，我們也會取得這些計量。 
*    下列計量可以藉由新增個別的 bean 來提供使用。 
        - CacheMetrics (CaffeineCache、EhCache2、GuavaCache、HazelcastCache、JCache)      
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - OkHttp3 度量 
        - Kafka 度量 

 

如何關閉自動度量收集： 
 
- JVM 度量： 
    - management.metrics.binders.jvm.enabled=false 
- Logback 度量： 
    - management.metrics.binders.jvm.enabled=false
- 執行時間度量： 
    - management.metrics.binders.jvm.enabled=false 
- 處理器度量：
    -  management.metrics.binders.jvm.enabled=false 
- FileDescriptorMetrics：
    - management.metrics.binders.jvm.enabled=false 
- 如果類別路徑上的程式庫有 Hystrix 度量： 
    - management.metrics.binders.hystrix.enabled=false 
- 如果類別路徑上的程式庫有 AspectJ 度量： 
    - spring.aop.enabled=false 

> [!NOTE]
> 在 Spring Boot 應用程式的 application.properties 或 application.yml 檔案中指定上述屬性

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>搭配非 Spring Boot web 應用程式使用 Micrometer

將以下相依性新增至您的 pom.xml 或 build.gradle 檔案：

* Application Insights Web Auto 2.5.0 或更新版本
* Micrometer Azure Registry 1.1.0 或更新版本
* [Application Insights 資源](./create-new-resource.md)

步驟：

1. 在您的 pom.xml 或 build.gradle 檔案中新增以下相依性：

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web-auto</artifactId>
            <version>2.5.0</version>
        </dependency>
     ```

2. 將檔案放 `ApplicationInsights.xml` 在 [資源] 資料夾中：

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
    
       <!-- The key from the portal: -->
       <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
    
       <!-- HTTP request component (not required for bare API) -->
       <TelemetryModules>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
       </TelemetryModules>
    
       <!-- Events correlation (not required for bare API) -->
       <!-- These initializers add context data to each event -->
       <TelemetryInitializers>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
       </TelemetryInitializers>
    
    </ApplicationInsights>
    ```

3. 範例 Servlet 類別 (會發出計時器度量)：

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {
    
          private static final long serialVersionUID = -4751096228274971485L;
    
          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
    
            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");
    
        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }
    
        }
    
    ```

4. 範例組態類別：

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {
     
           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {
     
         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}
     
             @Override
             public boolean enabled() {
                 return false;
             }
         };
     
      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);
     
             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);
     
           }
     
           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {
     
           }
         }
    ```

若要深入了解計量，請參閱 [Micrometer 文件](https://micrometer.io/docs/)。

您可以在[官方 Micrometer GitHub 存放庫](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples)找到有關如何建立不同類型度量的其他範例程式碼。

## <a name="how-to-bind-additional-metrics-collection"></a>如何繫結其他度量集合

### <a name="springbootspring"></a>SpringBoot/Spring

建立個別計量類別目錄的 bean。 例如，假設我們需要 Guava 快取計量：

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
有數個計量是預設為不啟用的，但可以透過上述方式結合。 如需完整清單，請參閱[官方 Micrometer GitHub 存放庫](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder )。

### <a name="non-spring-apps"></a>非 Spring 應用程式
將下列繫結程式碼新增至組態檔：
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解 Micrometer，請參閱官方 [Micrometer 檔](https://micrometer.io/docs)。
* 若要深入瞭解 Azure 上的春季，請參閱 [azure 上的官方春季檔](/java/azure/spring-framework/?view=azure-java-stable)。