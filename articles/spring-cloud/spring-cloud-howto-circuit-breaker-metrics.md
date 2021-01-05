---
title: 收集春季 Cloud Resilience4J 斷路器計量
description: 如何收集春季 Cloud Resilience4J 斷路器計量。
author: MikeDodaro
ms.author: brendanm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.openlocfilehash: 3fd1e644d69fe1d721526afcacb362adca48bf7a
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831800"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-preview"></a>收集春季 Cloud Resilience4J 斷路器計量 (預覽) 

本檔說明如何使用 Application Insights java 同進程代理程式來收集春季 Cloud Resilience4j 斷路器計量。  使用此功能，您可以從 Application Insights 監視 resilience4j 斷路器的度量。

我們會使用「 [雲端電路斷路](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) 器」示範來示範其運作方式。

## <a name="prerequisites"></a>必要條件

* 從 [適用于 Application Insights 指南的 java In-Process 代理程式](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-application-insights#enable-java-in-process-agent-for-application-insights)啟用 java In-Process 代理程式。 

* 從 [Application Insights 指南](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation)啟用 resilience4j 計量的維度集合。

* 如果開發電腦尚未使用 git、Maven 和 JAVA，請加以安裝。

## <a name="build-and-deploy-apps"></a>建置和部署應用程式

下列程式會建立並部署應用程式。

1. 複製並建立示範存放庫。

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. 建立具有端點的應用程式

```azcli
az spring-cloud app create --name resilience4j --is-public \
    -s ${asc-service-name} -g ${asc-resource-group}
az spring-cloud app create --name reactive-resilience4j --is-public \
    -s ${asc-service-name} -g ${asc-resource-group}
```

3. 部署應用程式。

```azcli
az spring-cloud app deploy -n resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-resilience4j/target/spring-cloud-circuitbreaker-demo-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
az spring-cloud app deploy -n reactive-resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-reactive-resilience4j/target/spring-cloud-circuitbreaker-demo-reactive-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
```

> [!Note]
>
> * 包含 Resilience4j 所需的相依性：
>
>   ```xml
>   <dependency>
>       <groupId>io.github.resilience4j</groupId>
>       <artifactId>resilience4j-micrometer</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
>   </dependency>
>   ```
> * 客戶程式碼必須使用的 API，此 API `CircuitBreakerFactory` 會在 `bean` 您加入春季雲端斷路器 starter 時實作為自動建立。 如需詳細資訊，請參閱 [春季雲端斷路](https://spring.io/projects/spring-cloud-circuitbreaker#overview)器。
>
> * 下列2個相依性與上述 resilient4j 套件衝突。  請確定客戶未包含這些專案。
>
>   ```xml
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-sleuth</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-zipkin</artifactId>
>   </dependency>
>   ```
>
>
> 流覽至閘道應用程式所提供的 URL，然後從 [春天雲端斷路器-示範](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) 存取端點，如下所示：
>
>   ```
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>從入口網站找出 Resilence4j 計量

1. 從 Azure 春季 Cloud 入口網站選取 **Application Insights** 的分頁，然後按一下 [ **Application Insights**]。

   [![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. 從 **Application Insights** ] 頁面選取 [**計量**]。  從 **計量命名空間** 選取 **azure. applicationinsights** 。  也請選取 [**平均**] **resilience4j_circuitbreaker_buffered_calls** 計量。

   [![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. 選取 **resilience4j_circuitbreaker_calls** 計量和 **平均值**。

   [![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. 選取 **resilience4j_circuitbreaker_calls**  計量和 **平均值**。  按一下 [ **新增篩選**]，然後選取 [名稱] 作為 [ **createNewAccount**]。

   [![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. 選取 **resilience4j_circuitbreaker_calls**  計量和 **平均值**。  然後按一下 [套用 **分割**]，然後選取 [ **種類**]。

   [![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. 選取 **resilience4j_circuitbreaker_calls**]、[**resilience4j_circuitbreaker_buffered_calls**]，並使用 [**平均**] **resilience4j_circuitbreaker_slow_calls** 計量。

   [![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>請參閱

* [Application insights](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-application-insights)
* [分散式追蹤](spring-cloud-tutorial-distributed-tracing.md)
* [斷路器儀表板](spring-cloud-tutorial-circuit-breaker.md)