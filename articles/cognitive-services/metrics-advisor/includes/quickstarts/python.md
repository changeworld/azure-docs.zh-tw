---
title: Metrics Advisor Python 快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 2c79773d6697ae9fb62e2b7515da60178243fe40
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523938"
---
[參考文件](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/README.md) | [套件 (PiPy)](https://pypi.org/project/azure-ai-metricsadvisor/) | [範例](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples/README.md)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="建立 Metrics Advisor 資源"  target="_blank">建立 Metrics Advisor 資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以部署您的 Metrics Advisor 執行個體。  
* 包含時間序列資料的 SQL 資料庫。
  
> [!TIP]
> * 您可以在 [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples) 上找到 Python Metrics Advisor 範例。
> * 可能需要 10 到 30 分鐘，Metrics Advisor 資源才會將服務執行個體部署完成供您使用。 一旦成功部署，請按一下 [移至資源]。 部署之後，您即可開始透過入口網站和 REST API 使用 Metrics Advisor 執行個體。
> * 您可以經由 Azure 入口網站，在資源的 [概觀] 區段中找到 REST API 的 URL。 其看起來將會像下面這樣：
>    * `https://<instance-name>.cognitiveservices.azure.com/` 
 
## <a name="setting-up"></a>設定

### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝 Python 之後，您可以透過以下項目安裝用戶端程式庫：

```console
pip install azure-ai-metricsadvisor --pre
```

### <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

建立新的 Python 檔案，並匯入下列程式庫。

```python
import os
import datetime
```

為資源的 Azure 端點和金鑰建立變數。

> [!IMPORTANT]
> 前往 Azure 入口網站。 如果您在 [必要條件] 區段中建立的 Metrics Advisor 資源成功部署，請按一下 [後續步驟] 底下的 [前往資源] 按鈕。 您可以在資源的 [金鑰和端點] 頁面中 (位於 [資源管理] 底下) 找到訂用帳戶金鑰和端點。 <br><br>若要擷取您的 API 金鑰，您必須移至 [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net)。 選取適當的：資源 [目錄]、[訂用帳戶] 和 [工作區]，然後選擇 [開始使用]。 接著，您將能夠從 [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) 擷取您的 API 金鑰。   
>
> 完成時，請記得從程式碼中移除金鑰，且不要公開張貼金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 如需詳細資訊，請參閱認知服務[安全性](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security)一文。

```python
subscription_key = "<paste-your-metrics-advisor-subscription-key-here>"
api_key = "<paste-your-metrics-advisor-api-key-here>"
service_endpoint = "<paste-your-metrics-advisor-endpoint-here>"
```

## <a name="object-model"></a>物件模型

下列類別會處理 Metrics Advisor Python SDK 的一些主要功能。

|名稱|描述|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient) | **用於**： <br> - 列出事件 <br> - 列出事件的根本原因 <br> - 擷取原始時間序列資料和由服務擴充的時間序列資料。 <br> - 列出警示 <br> - 新增意見反應以調整您的模型 |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorAdministrationClient) | **可讓您：** <br> - 管理資料摘要 <br> - 建立、設定、擷取、列出和刪除異常偵測設定 <br> - 建立、設定、擷取、列出和刪除異常警示設定 <br> - 管理攔截  | |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeed#azure.ai.metricsadvisor.models.DataFeed)| **Metrics Advisor 從您的資料來源擷取的項目。`DataFeed` 包含下列各項的資料列：** <br> - 時間戳記 <br> - 零或多個維度 <br> - 一或多個量值  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeedmetric#azure.ai.metricsadvisor.models.DataFeedMetric) | `DataFeedMetric` 是可量化的量值，用以監視及評估特定商務程序的狀態。 此量值可由分割成維度的多個時間序列值組合而成。 例如，Web 健康情況計量可包含使用者計數和美國市場的維度。 |

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Python 的 Metrics Advisor 用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [新增資料摘要](#add-a-data-feed)
* [檢查內嵌狀態](#check-the-ingestion-status)
* [設定偵測設定和警示設定](#configure-anomaly-detection)
* [建立警示設定](#create-an-alert-configuration)
* [查詢異常偵測結果](#query-the-alert)

## <a name="authenticate-the-client"></a>驗證用戶端

此範例中的用戶端是使用您的端點的 `MetricsAdvisorAdministrationClient` 物件，以及包含金鑰的 `MetricsAdvisorKeyCredential` 物件。 您不需要複製此程式碼範例。 您稍後建立的方法會具現化用戶端。 替代用戶端名為 `MetricsAdvisorClient`。如需此用戶端的詳細資訊，請參閱[參考文件](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient)。

```python
client = MetricsAdvisorAdministrationClient(service_endpoint,
                                MetricsAdvisorKeyCredential(subscription_key, api_key))
```

## <a name="add-a-data-feed"></a>新增資料摘要

在新方法中建立匯入陳述式，如下列範例所示。 將 `sql_server_connection_string` 取代為您自己的 SQL Server 連接字串，並將 `query` 取代為以單一時間戳記傳回資料的查詢。 您也必須根據自訂資料調整 `DataFeedmetric` 和 `DataFeedDimension` 值。

> [!IMPORTANT]
> 在每個時間戳記上，查詢對於每個維度組合最多僅應傳回一筆記錄。 而且，查詢所傳回的所有記錄必須具有相同的時間戳記。 Metrics Advisor 會對每個時間戳記執行此查詢，以擷取您的資料。 如需詳細資訊和範例，請參閱[查詢的常見問題集一節](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data)。 

使用您的金鑰和端點建立用戶端，並使用 `client.create_data_feed()` 來設定名稱、來源、細微性和結構描述。 您也可以設定擷取時間、彙總設定等項目。


```python
def sample_create_data_feed():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        SQLServerDataFeed,
        DataFeedSchema,
        DataFeedMetric,
        DataFeedDimension,
        DataFeedOptions,
        DataFeedRollupSettings
    )
    sql_server_connection_string = "<replace-with-your-sql-server-connection-string>"
    query = "<replace-with-metrics-advisor-sql-server-query>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    data_feed = client.create_data_feed(
    name="My data feed",
    source=SQLServerDataFeed(
        connection_string=sql_server_connection_string,
        query=query,
    ),
    granularity="Daily",
    schema=DataFeedSchema(
        metrics=[
            DataFeedMetric(name="cost", display_name="Cost"),
            DataFeedMetric(name="revenue", display_name="Revenue")
        ],
        dimensions=[
            DataFeedDimension(name="category", display_name="Category"),
            DataFeedDimension(name="city", display_name="City")
        ],
        timestamp_column="Timestamp"
    ),
    ingestion_settings=datetime.datetime(2019, 10, 1),
    options=DataFeedOptions(
        data_feed_description="cost/revenue data feed",
        rollup_settings=DataFeedRollupSettings(
            rollup_type="AutoRollup",
            rollup_method="Sum",
            rollup_identification_value="__CUSTOM_SUM__"
        ),
        missing_data_point_fill_settings=DataFeedMissingDataPointFillSettings(
            fill_type="SmartFilling"
        ),
        access_mode="Private"
    )
)

return data_feed
sample_create_data_feed()
```

## <a name="check-the-ingestion-status"></a>檢查擷取狀態

在新方法中建立匯入陳述式，如下列範例所示。 將 `data_feed_id` 取代為您所建立之資料摘要的識別碼。 使用您的金鑰和端點建立用戶端，並使用 `client.list_data_feed_ingestion_status()` 取得擷取進度。 列出詳細資料，例如最後一個作用中和成功的時間戳記。


```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient

    data_feed_id = "<replace-with-your-metrics-advisor-data-feed-id>"

   client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

ingestion_status = client.list_data_feed_ingestion_status(
    data_feed_id,
    datetime.datetime(2020, 9, 20),
    datetime.datetime(2020, 9, 25)
)
for status in ingestion_status:
    print("Timestamp: {}".format(status.timestamp))
    print("Status: {}".format(status.status))
    print("Message: {}\n".format(status.message))

```

## <a name="configure-anomaly-detection"></a>設定異常偵測

在新方法中建立匯入陳述式，如下列範例所示。 將 `metric_id` 取代為您要設定之計量的識別碼。 使用您的金鑰和端點建立用戶端，並使用 `client.create_detection_configuration` 建立新的偵測設定。 閾值條件會指定異常偵測的參數。

```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        ChangeThresholdCondition,
        HardThresholdCondition,
        SmartDetectionCondition,
        SuppressCondition,
        MetricDetectionCondition,
    )
    metric_id = "replace-with-your-metric-id"

    
client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

change_threshold_condition = ChangeThresholdCondition(
    anomaly_detector_direction="Both",
    change_percentage=20,
    shift_point=10,
    within_range=True,
    suppress_condition=SuppressCondition(
        min_number=5,
        min_ratio=2
    )
)
hard_threshold_condition = HardThresholdCondition(
    anomaly_detector_direction="Up",
    upper_bound=100,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)
smart_detection_condition = SmartDetectionCondition(
    anomaly_detector_direction="Up",
    sensitivity=10,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)

detection_config = client.create_detection_configuration(
    name="my_detection_config",
    metric_id=metric_id,
    description="anomaly detection config for metric",
    whole_series_detection_condition=MetricDetectionCondition(
        cross_conditions_operator="OR",
        change_threshold_condition=change_threshold_condition,
        hard_threshold_condition=hard_threshold_condition,
        smart_detection_condition=smart_detection_condition
    )
)
return detection_config
```

## <a name="create-a-hook"></a>建立攔截

在新方法中建立匯入陳述式，如下列範例所示。 使用您的金鑰和端點建立用戶端，並使用 `client.create_hook()` 建立攔截。 輸入描述、警示要傳送到的電子郵件清單，以及用來接收警示的外部連結。  

```python
def sample_create_hook():

    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import EmailNotificationHook

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key))

hook = client.create_hook(
    hook=EmailNotificationHook(
        name="email hook",
        description="my email hook",
        emails_to_alert=["alertme@alertme.com"],
        external_link="https://example.com/handleAlerts", # you must enter a valid webhook url to post the alert payload
    )
)
```

##  <a name="create-an-alert-configuration"></a>建立警示設定

在新方法中建立匯入陳述式，如下列範例所示。 將 `detection_configuration_id` 取代為異常偵測設定的識別碼，並將 `hook_id` 取代為您先前建立的攔截。 使用您的金鑰和端點建立用戶端，並使用 `client.create_alert_configuration()` 建立警示設定。 

```python
def sample_create_alert_config():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        MetricAlertConfiguration,
        MetricAnomalyAlertScope,
        TopNGroupScope,
        MetricAnomalyAlertConditions,
        SeverityCondition,
        MetricBoundaryCondition,
        MetricAnomalyAlertSnoozeCondition
    )
    anomaly_detection_configuration_id = "<replace-with-your-detection-configuration-id"
    hook_id = "<replace-with-your-hook-id>"

    client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

alert_config = client.create_alert_configuration(
        name="my alert config",
        description="alert config description",
        cross_metrics_operator="AND",
        metric_alert_configurations=[
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="WholeSeries"
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    severity_condition=SeverityCondition(
                        min_alert_severity="Low",
                        max_alert_severity="High"
                    )
                )
            ),
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="TopN",
                    top_n_group_in_scope=TopNGroupScope(
                        top=10,
                        period=5,
                        min_top_count=5
                    )
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    metric_boundary_condition=MetricBoundaryCondition(
                        direction="Up",
                        upper=50
                    )
                ),
                alert_snooze_condition=MetricAnomalyAlertSnoozeCondition(
                    auto_snooze=2,
                    snooze_scope="Metric",
                    only_for_successive=True
                )
            ),
        ],
        hook_ids=[hook_id]
    )

    return alert_config
```

### <a name="query-the-alert"></a>查詢警示

在新方法中建立匯入陳述式，如下列範例所示。 將 `alert_id` 取代為警示的識別碼，並將 `alert_config_id` 取代為警示設定識別碼。 使用您的金鑰和端點建立用戶端，並使用 `client.list_anomalies` 列出警示的異常狀況。 

```python
from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorClient
    
alert_id = "<replace-with-your-alert-id>"
alert_config_id = "<replace-with-your-alert-configuration-id"

client = MetricsAdvisorClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

results = client.list_alerts(
    alert_configuration_id=alert_config_id,
    start_time=datetime.datetime(2020, 1, 1),
    end_time=datetime.datetime(2020, 9, 9),
    time_mode="AnomalyTime",
)
for result in results:
    print("Alert id: {}".format(result.id))
    print("Create on: {}".format(result.created_on))

results = client.list_anomalies(
    alert_configuration_id=alert_config_id,
    alert_id=alert_id,
)
for result in results:
    print("Create on: {}".format(result.created_on))
    print("Severity: {}".format(result.severity))
    print("Status: {}".format(result.status))
```

### <a name="run-the-application"></a>執行應用程式

使用快速入門檔案上使用 `python` 命令執行應用程式。

```console
python quickstart-file.py
```