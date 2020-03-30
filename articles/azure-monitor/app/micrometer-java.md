---
title: 如何搭配 Azure Application Insights Java SDK 使用 Micrometer
description: 搭配 Application Insights Spring Boot 和非 Spring Boot 應用程式使用 Micrometer 的逐步解說指南。
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 11/01/2018
ms.openlocfilehash: dd04087db32f0bbfa75dafa7e12c355e5ab7b515
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670061"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>如何搭配 Azure Application Insights Java SDK 使用 Micrometer
Micrometer 應用程式監視會測量以 JVM 為基礎之應用程式程式碼的計量，並可讓您將資料匯出到您慣用的監視系統。 這篇文章會教導您如何搭配 Application Insights Spring Boot 和非 Spring Boot 應用程式使用 Micrometer。

## <a name="using-spring-boot-15x"></a>使用 Spring Boot 1.5x
將以下相依性新增至您的 pom.xml 或 build.gradle 檔案： 
* [應用程式見解彈簧啟動啟動器](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter)2.5.0 或更高版本
* Micrometer Azure Registry 1.1.0 或更新版本
* [Micrometer Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 或更新版本 (這會反向移植 Spring Framework 中的自動設定程式碼)。
* [ApplicationInsights 資源](../../azure-monitor/app/create-new-resource.md )

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
2. 上述動作應該能讓您的應用程式啟動，並使用自動收集至 Azure 監視器的預先彙總計量。 如需如何微調 Application Insights Spring Boot 簡易版的詳細資訊，請參閱 [GitHub 上的讀我檔案](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md)。

## <a name="using-spring-2x"></a>使用 Spring 2.x

將以下相依性新增至您的 pom.xml 或 build.gradle 檔案：

* Application Insights Spring-boot-starter 2.1.2 或更新版本
* Azure 彈簧啟動指標 - 啟動器 2.0.7 或更高版本
* [應用程式見解資源](../../azure-monitor/app/create-new-resource.md )

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
*    例如，如果 Netflix Hystrix 存在於班級路徑上，我們也得到這些指標。 
*    下列計量可以藉由新增個別的 bean 來提供使用。 
        - 緩存指標（咖啡因緩存、EhCache2、瓜瓦緩存、黑茲爾卡斯特緩存、JCache）     
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

* 應用程式見解 Web 自動 2.5.0 或更高版本
* Micrometer Azure Registry 1.1.0 或更新版本
* [應用程式見解資源](../../azure-monitor/app/create-new-resource.md )

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

2. 將`ApplicationInsights.xml`檔放入資源資料夾中：

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

* 要瞭解有關微米數的更多，請參閱官方[微數文檔](https://micrometer.io/docs)。
* 要瞭解 Azure 上的 Spring，請參閱[Azure 文檔上的正式 Spring。](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable)
