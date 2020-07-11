---
title: 配方：具有 Big Data 認知服務的預測性維護
titleSuffix: Azure Cognitive Services
description: 本快速入門說明如何使用適用于 Big Data 的認知服務執行分散式異常偵測
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: tracking-python
ms.openlocfilehash: d872e4486bdc8fcd3629feb452b0d436872c70d8
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86189396"
---
# <a name="recipe-predictive-maintenance-with-the-cognitive-services-for-big-data"></a>配方：具有 Big Data 認知服務的預測性維護

此配方會顯示如何使用 Azure Synapse 分析和 Spark 上的認知服務，來進行 IoT 裝置的預測性維護。 我們將遵循[CosmosDB 和 Synapse 連結](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples)範例。 為了簡單起見，在此配方中，我們將直接從 CSV 檔案讀取資料，而不是透過 CosmosDB 和 Synapse 連結取得資料流程資料。 我們強烈建議您查看 Synapse 連結範例。

## <a name="hypothetical-scenario"></a>假設案例

假設的案例是一種電源工廠，IoT 裝置會在其中監視[流渦輪機](https://en.wikipedia.org/wiki/Steam_turbine)。 IoTSignals 集合的每分鐘 (RPM) 和 Megawatts (MW) 資料。 正在分析來自串流渦輪機的信號，並偵測到異常信號。

資料中可能會有隨機頻率的極端值。 在這些情況下，RPM 值會增加，而 MW 輸出將會關閉，以進行線路保護。 其概念是要同時查看資料變化，但使用不同的信號。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* 使用[Spark 集](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool)區設定的[Azure Synapse 工作區](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace)

## <a name="setup"></a>安裝程式

### <a name="create-an-anomaly-detector-resource"></a>建立 Anomaly Detector 資源

Azure 認知服務會由您訂閱的 Azure 資源呈現。 使用[Azure 入口網站](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或[Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)，建立 Translator 的資源。 您也可以：

- 在[Azure 入口網站](https://portal.azure.com/)中，查看現有的資源。

請記下此資源的端點和金鑰，您將在本指南中需要它。

## <a name="enter-your-service-keys"></a>輸入您的服務金鑰

讓我們從新增您的金鑰和位置開始。

```python
service_key = None # Paste your anomaly detector key here
location = None # Paste your anomaly detector location here

assert (service_key is not None)
assert (location is not None)
```

## <a name="read-data-into-a-dataframe"></a>將資料讀取至資料框架

接下來，讓我們將 IoTSignals 檔案讀入資料框架中。 在您的 Synapse 工作區中開啟新的筆記本，並從檔案建立資料框架。

```python
df_device_info = spark.read.csv("wasbs://publicwasb@mmlspark.blob.core.windows.net/iot/IoTSignals.csv", header=True, inferSchema=True)
```

### <a name="run-anomaly-detection-using-cognitive-services-on-spark"></a>在 Spark 上使用認知服務執行異常偵測

目標是尋找來自 IoT 裝置的信號輸出異常值的實例，讓我們可以查看發生問題的時機，並進行預測性維護。 若要這麼做，讓我們在 Spark 上使用異常偵測器：

```python
from pyspark.sql.functions import col, struct
from mmlspark.cognitive import SimpleDetectAnomalies
from mmlspark.core.spark import FluentAPI

detector = (SimpleDetectAnomalies()
    .setSubscriptionKey(service_key)
    .setLocation(location)
    .setOutputCol("anomalies")
    .setGroupbyCol("grouping")
    .setSensitivity(95)
    .setGranularity("secondly"))

df_anomaly = (df_signals
    .where(col("unitSymbol") == 'RPM')
    .withColumn("timestamp", col("dateTime").cast("string"))
    .withColumn("value", col("measureValue").cast("double"))
    .withColumn("grouping", struct("deviceId"))
    .mlTransform(detector)).cache()

df_anomaly.createOrReplaceTempView('df_anomaly')
```

我們來看一下資料：

```python
df_anomaly.select("timestamp","value","deviceId","anomalies.isAnomaly").show(3)
```

此資料格應該會產生如下所示的結果：

| timestamp           |   value | deviceId   | isAnomaly   |
|:--------------------|--------:|:-----------|:------------|
| 2020-05-01 18:33:51 |    3174 | 開發-7      | 否       |
| 2020-05-01 18:33:52 |    2976 | 開發-7      | 否       |
| 2020-05-01 18:33:53 |    2714 | 開發-7      | 否       |


 ## <a name="visualize-anomalies-for-one-of-the-devices"></a>將其中一個裝置的異常視覺化

IoTSignals.csv 具有來自多個 IoT 裝置的信號。 我們會將焦點放在特定裝置上，並將裝置的異常輸出視覺化。

```python
df_anomaly_single_device = spark.sql("""
select
  timestamp,
  measureValue,
  anomalies.expectedValue,
  anomalies.expectedValue + anomalies.upperMargin as expectedUpperValue,
  anomalies.expectedValue - anomalies.lowerMargin as expectedLowerValue,
  case when anomalies.isAnomaly=true then 1 else 0 end as isAnomaly
from
  df_anomaly
where deviceid = 'dev-1' and timestamp < '2020-04-29'
order by timestamp
limit 200""")
```

既然我們已建立代表特定裝置異常的資料框架，我們可以將這些異常視覺化：

```python
import matplotlib.pyplot as plt
from pyspark.sql.functions import col

adf = df_anomaly_single_device.toPandas()
adf_subset = df_anomaly_single_device.where(col("isAnomaly") == 1).toPandas()

plt.figure(figsize=(23,8))
plt.plot(adf['timestamp'],adf['expectedUpperValue'], color='darkred', linestyle='solid', linewidth=0.25, label='UpperMargin')
plt.plot(adf['timestamp'],adf['expectedValue'], color='darkgreen', linestyle='solid', linewidth=2, label='Expected Value')
plt.plot(adf['timestamp'],adf['measureValue'], 'b', color='royalblue', linestyle='dotted', linewidth=2, label='Actual')
plt.plot(adf['timestamp'],adf['expectedLowerValue'],  color='black', linestyle='solid', linewidth=0.25, label='Lower Margin')
plt.plot(adf_subset['timestamp'],adf_subset['measureValue'], 'ro', label = 'Anomaly')
plt.legend()
plt.title('RPM Anomalies with Confidence Intervals')
plt.show()
```

如果成功，您的輸出看起來會像這樣：

![異常偵測器繪圖](../media/anomaly-output.png)

## <a name="next-steps"></a>後續步驟

瞭解如何使用 Azure 認知服務、Azure Synapse Analytics 和 Azure CosmosDB，大規模進行預測性維護。 如需詳細資訊，請參閱[GitHub](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples/tree/master/IoT)上的完整範例。
