---
title: Metrics Advisor Java 快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 17c8de41f6c1df4a54ec6bd564df733291a707f7
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356421"
---
[參考文件](https://westus2.dev.cognitive.microsoft.com/docs/services/MetricsAdvisor/) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src) | [成品 (Maven)](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor) | [範例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 最新版的 [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle 建置工具](https://gradle.org/install/)，或其他相依性管理員。
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="建立 Metrics Advisor 資源"  target="_blank">建立 Metrics Advisor 資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以部署您的 Metrics Advisor 執行個體。  
* 包含時間序列資料的 SQL 資料庫。
  
  
> [!TIP]
> * 您可以在 [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples) 上找到 Java Metrics Advisor 範例。
> * 可能需要 10 到 30 分鐘，Metrics Advisor 資源才會將服務執行個體部署完成供您使用。 一旦成功部署，請按一下 [移至資源]。 部署之後，您即可開始透過入口網站和 REST API 使用 Metrics Advisor 執行個體。 
> * 您可以經由 Azure 入口網站，在資源的 [概觀] 區段中找到 REST API 的 URL。 其看起來將會像下面這樣：
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>設定

### <a name="create-a-new-gradle-project"></a>建立新的 Gradle 專案

本快速入門會使用 Gradle 相依性管理員。 您可以在 [Maven 中央存放庫](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor)中找到更多用戶端程式庫資訊。

在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為您的應用程式建立新的目錄，並瀏覽至該目錄。 

```console
mkdir myapp && cd myapp
```

從您的工作目錄執行 `gradle init` 命令。 此命令會建立 Gradle 的基本組建檔案，包括 *build.gradle.kts*，此檔案將在執行階段用來建立及設定您的應用程式。

```console
gradle init --type basic
```

出現選擇 **DSL** 的提示時，請選取 [Kotlin]。

### <a name="install-the-client-library"></a>安裝用戶端程式庫

找出 build.gradle.kts，並使用您慣用的 IDE 或文字編輯器加以開啟。 然後，在此組建組態中複製下列內容。 請務必包含專案相依性。

```kotlin
dependencies {
    compile("com.azure:azure-ai-metricsadvisor:1.0.0-beta.2")
}
```

### <a name="create-a-java-file"></a>建立 Java 檔案

建立範例應用程式的資料夾。 從工作目錄執行下列命令：

```console
mkdir -p src/main/java
```

瀏覽至新的資料夾，並建立名為 *MetricsAdvisorQuickstarts.java* 的檔案。 在您慣用的編輯器或 IDE 中開啟該檔案，並新增下列 `import` 陳述式：

> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples) 上找到該檔案，其中包含本快速入門中的程式碼範例。

在應用程式的 `MetricsAdvisorQuickstarts` 類別中，為資源的金鑰和端點建立變數。


> [!IMPORTANT]
> 移至 Azure 入口網站。 如果您在 [必要條件] 區段中建立的 Metrics Advisor 資源成功部署，請按一下 [後續步驟] 底下的 [前往資源] 按鈕。 您可以在資源的 [金鑰和端點] 頁面中 (位於 [資源管理] 底下) 找到訂用帳戶金鑰和端點。 <br><br>若要擷取您的 API 金鑰，您必須移至 [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net)。 選取適當的：資源 [目錄]、[訂用帳戶] 和 [工作區]，然後選擇 [開始使用]。 接著，您將能夠從 [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) 擷取您的 API 金鑰。   
>
> 完成時，請記得從程式碼中移除金鑰，且不要公開張貼金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 如需詳細資訊，請參閱認知服務[安全性](../../../cognitive-services-security.md)一文。

```java
private static String SUBSCRIPTION_KEY = "<replace-with-your-metrics-advisor-subscription-key-here>";
private static String API_KEY = "<replace-with-your-metrics-advisor-api-key-here>"
private static String ENDPOINT = "<replace-with-your-metrics-advisor-endpoint-here>";
```

在應用程式的 `Main()` 方法中，針對本快速入門中使用的方法新增呼叫。 您稍後會建立這些項目。

```java
static void Main(string[] args){

    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>物件模型

下列類別會處理 Metrics Advisor Java SDK 的一些主要功能。

|名稱|描述|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/MetricsAdvisorClient.html) | **用於**： <br> - 列出異常事件 <br> - 列出事件的根本原因 <br> - 擷取原始時間序列資料和由服務擴充的時間序列資料。 <br> - 列出警示 <br> - 新增意見反應以調整您的模型 |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/administration/MetricsAdvisorAdministrationClient.html)| **可讓您：** <br> - 管理資料摘要 <br> - 設定異常偵測設定 <br> - 設定異常警示設定 <br> - 管理攔截  |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/models/DataFeed.html) | **Metrics Advisor 從您的資料來源擷取的項目。`DataFeed` 包含下列各項的資料列：** <br> - 時間戳記 <br> - 零或多個維度 <br> - 一或多個量值  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/models/DataFeedMetric.html) | `DataFeedMetric` 是可量化的量值，用以監視及評估特定商務程序的狀態。 此量值可由分割成維度的多個時間序列值組合而成。 例如，Web 健康情況計量可包含使用者計數和美國市場的維度。 |

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Java 的 Metrics Advisor 用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [新增資料摘要](#add-a-data-feed)
* [檢查擷取狀態](#check-the-ingestion-status)
* [設定異常偵測](#configure-anomaly-detection)
* [建立攔截](#create-a-hook)
* [建立警示設定](#create-an-alert-configuration)
* [查詢警示](#query-the-alert)

## <a name="authenticate-the-client"></a>驗證用戶端

### <a name="create-a-metrics-advisor-client-using-metricsadvisorkeycredential"></a>使用 MetricsAdvisorKeyCredential 建立 Metrics Advisor 用戶端

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorClient metricsAdvisorClient = new MetricsAdvisorClientBuilder()
    .endpoint(ENDPOINT)
    .credential(credential)
    .buildClient();
```

### <a name="create-a-metrics-administration-client-using-metricsadvisorkeycredential"></a>使用 MetricsAdvisorKeyCredential 建立 Metrics Administration 用戶端

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorAdministrationClient metricsAdvisorAdministrationClient =
    new MetricsAdvisorAdministrationClientBuilder()
        .endpoint(ENDPOINT)
        .credential(credential)
        .buildClient();

``` 

## <a name="add-a-data-feed"></a>新增資料摘要

將 `sql_server_connection_string` 取代為您自己的 SQL Server 連接字串，並將 `query` 取代為以單一時間戳記傳回資料的查詢。 您也必須根據自訂資料調整 `DataFeedMetric` 和 `DataFeedDimension` 值。

> [!IMPORTANT]
> 在每個時間戳記上，查詢對於每個維度組合最多僅應傳回一筆記錄。 而且，查詢所傳回的所有記錄必須具有相同的時間戳記。 Metrics Advisor 會對每個時間戳記執行此查詢，以擷取您的資料。 如需詳細資訊和範例，請參閱[查詢的常見問題集一節](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data)。 

```java
DataFeed dataFeed = new DataFeed()
    .setName("dataFeedName")
    .setSource(new MySqlDataFeedSource("sql_server_connection_string", "query"))
    .setGranularity(new DataFeedGranularity().setGranularityType(DataFeedGranularityType.DAILY))
    .setSchema(new DataFeedSchema(
        Arrays.asList(
            new DataFeedMetric().setName("cost"),
            new DataFeedMetric().setName("revenue")
        )).setDimensions(
        Arrays.asList(
            new DataFeedDimension().setName("city"),
            new DataFeedDimension().setName("category")
        ))
    )
    .setIngestionSettings(new DataFeedIngestionSettings(OffsetDateTime.parse("2020-01-01T00:00:00Z")))
    .setOptions(new DataFeedOptions()
        .setDescription("data feed description")
        .setRollupSettings(new DataFeedRollupSettings()
            .setRollupType(DataFeedRollupType.AUTO_ROLLUP)));
final DataFeed createdSqlDataFeed = metricsAdvisorAdminClient.createDataFeed(dataFeed);

System.out.printf("Data feed Id : %s%n", createdSqlDataFeed.getId());
System.out.printf("Data feed name : %s%n", createdSqlDataFeed.getName());
System.out.printf("Is the query user is one of data feed administrator : %s%n", createdSqlDataFeed.isAdmin());
System.out.printf("Data feed created time : %s%n", createdSqlDataFeed.getCreatedTime());
System.out.printf("Data feed granularity type : %s%n",
    createdSqlDataFeed.getGranularity().getGranularityType());
System.out.printf("Data feed granularity value : %d%n",
    createdSqlDataFeed.getGranularity().getCustomGranularityValue());
System.out.println("Data feed related metric Ids:");
createdSqlDataFeed.getMetricIds().forEach(System.out::println);
System.out.printf("Data feed source type: %s%n", createdSqlDataFeed.getSourceType());

if (SQL_SERVER_DB == createdSqlDataFeed.getSourceType()) {
    System.out.printf("Data feed sql server query: %s%n",
        ((SQLServerDataFeedSource) createdSqlDataFeed.getSource()).getQuery());
}
```

## <a name="check-the-ingestion-status"></a>檢查擷取狀態

此範例會檢查先前提供之資料摘要來源的擷取狀態。

```java
String dataFeedId = "<use-the-data-feed-id-from-createdSqlDataFeed.getId()"; 

metricsAdvisorAdminClient.listDataFeedIngestionStatus(
    dataFeedId,
    new ListDataFeedIngestionOptions(
        OffsetDateTime.parse("2020-01-01T00:00:00Z"),
        OffsetDateTime.parse("2020-09-09T00:00:00Z"))
).forEach(dataFeedIngestionStatus -> {
    System.out.printf("Message : %s%n", dataFeedIngestionStatus.getMessage());
    System.out.printf("Timestamp value : %s%n", dataFeedIngestionStatus.getTimestamp());
    System.out.printf("Status : %s%n", dataFeedIngestionStatus.getStatus());
});
```
## <a name="configure-anomaly-detection"></a>設定異常偵測 

此範例示範使用者如何設定其資料的異常偵測設定。

```java
String metricId = "<metric-id-from-adding-data-feed>";

ChangeThresholdCondition changeThresholdCondition = new ChangeThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.BOTH)
    .setChangePercentage(20)
    .setShiftPoint(10)
    .setWithinRange(true)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

HardThresholdCondition hardThresholdCondition = new HardThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.DOWN)
    .setLowerBound(5.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(1));

SmartDetectionCondition smartDetectionCondition = new SmartDetectionCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.UP)
    .setSensitivity(10.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

final AnomalyDetectionConfiguration anomalyDetectionConfiguration =
    metricsAdvisorAdminClient.createMetricAnomalyDetectionConfig(
        metricId,
        new AnomalyDetectionConfiguration("My dataPoint anomaly detection configuration")
            .setDescription("anomaly detection config description")
            .setWholeSeriesDetectionCondition(
                new MetricWholeSeriesDetectionCondition()
                    .setChangeThresholdCondition(changeThresholdCondition)
                    .setHardThresholdCondition(hardThresholdCondition)
                    .setSmartDetectionCondition(smartDetectionCondition)
                    .setCrossConditionOperator(DetectionConditionsOperator.OR))
    );
```

## <a name="create-a-hook"></a>建立攔截

此範例會建立接收異常事件警示的電子郵件攔截。

```java
NotificationHook emailNotificationHook = new EmailNotificationHook("email Hook")
    .setDescription("my email Hook")
    .addEmailToAlert("alertme@alertme.com")
    .setExternalLink("https://example.com/handleAlerts"); // you must enter a valid webhook url to post the alert payload

final NotificationHook notificationHook = metricsAdvisorAdminClient.createHook(emailNotificationHook);
EmailNotificationHook createdEmailHook = (EmailNotificationHook) notificationHook;
System.out.printf("Email Hook Id: %s%n", createdEmailHook.getId());
System.out.printf("Email Hook name: %s%n", createdEmailHook.getName());
System.out.printf("Email Hook description: %s%n", createdEmailHook.getDescription());
System.out.printf("Email Hook external Link: %s%n", createdEmailHook.getExternalLink());
System.out.printf("Email Hook emails to alert: %s%n",
    String.join(",", createdEmailHook.getEmailsToAlert()));
```

##  <a name="create-an-alert-configuration"></a>建立警示設定

此範例示範使用者如何為在其資料中偵測到的異常設定警示設定。

```java
String detectionConfigurationId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String detectionConfigurationId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

final AnomalyAlertConfiguration anomalyAlertConfiguration
    = metricsAdvisorAdminClient.createAnomalyAlertConfig(
        new AnomalyAlertConfiguration("My anomaly alert config name")
            .setDescription("alert config description")
            .setMetricAlertConfigurations(
                Arrays.asList(
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId1,
                        MetricAnomalyAlertScope.forWholeSeries()),
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId2,
                        MetricAnomalyAlertScope.forWholeSeries())
                        .setAlertConditions(new MetricAnomalyAlertConditions()
                            .setSeverityRangeCondition(new SeverityCondition()
                                .setMaxAlertSeverity(AnomalySeverity.HIGH)))
                ))
            .setCrossMetricsOperator(MetricAnomalyAlertConfigurationsOperator.AND)
            .setIdOfHooksToAlert(Arrays.asList(hookId1, hookId2)));
```

## <a name="query-the-alert"></a>查詢警示

此範例示範使用者如何查詢為警示偵測設定觸發的警示，以及取得該警示的異常。

```java
String alertConfigurationId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
final OffsetDateTime startTime = OffsetDateTime.parse("2020-01-01T00:00:00Z");
final OffsetDateTime endTime = OffsetDateTime.parse("2020-09-09T00:00:00Z");
metricsAdvisorClient.listAlerts(
    alertConfigurationId,
        startTime, endTime)
    .forEach(alert -> {
        System.out.printf("AnomalyAlert Id: %s%n", alert.getId());
        System.out.printf("AnomalyAlert created on: %s%n", alert.getCreatedTime());

        // List anomalies for returned alerts
        metricsAdvisorClient.listAnomaliesForAlert(
            alertConfigurationId,
            alert.getId())
            .forEach(anomaly -> {
                System.out.printf("DataPoint Anomaly was created on: %s%n", anomaly.getCreatedTime());
                System.out.printf("DataPoint Anomaly severity: %s%n", anomaly.getSeverity().toString());
                System.out.printf("DataPoint Anomaly status: %s%n", anomaly.getStatus());
                System.out.printf("DataPoint Anomaly related series key: %s%n", anomaly.getSeriesKey().asMap());
            });
    });
```

您可以使用下列命令來建置應用程式：

```console
gradle build
```
### <a name="run-the-application"></a>執行應用程式

使用 `run` 目標執行應用程式：

```console
gradle run
```