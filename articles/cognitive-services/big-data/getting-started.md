---
title: 開始使用適用於巨量資料的認知服務
description: 在 Azure Databricks 中使用認知服務設定您的 MMLSpark 管線，並執行範例。
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 5256db4547f8c19960a6f470ce3c84b2eef25e24
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776607"
---
# <a name="getting-started"></a>開始使用

設定您的環境是為資料建置管線的第一步。 環境準備就緒後，就能輕鬆快速地執行範例了。

在本文中，我們將執行下列步驟以協助您開始：

1. [建立認知服務資源](#create-a-cognitive-services-resource)
1. [建立 Apache Spark 叢集](#create-an-apache-spark-cluster)
1. [試用範例](#try-a-sample)

## <a name="create-a-cognitive-services-resource"></a>建立認知服務資源

若要使用巨量資料認知服務，我們必須先為工作流程建立認知服務。 認知服務有兩大類型：裝載於 Azure 的雲端服務，以及由使用者管理的容器化服務。 建議您從較簡單的雲端式認知服務來開始。

### <a name="cloud-services"></a>雲端服務

雲端式認知服務是裝載於 Azure 的智慧型演算法。 這些服務可供在未經訓練的情況下立即使用，您只需要有網際網路連線即可。 您可以[在 Azure 入口網站中](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows)或使用 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli?tabs=windows) 來建立認知服務。

### <a name="containerized-services-optional"></a>容器化服務 (選擇性)

如果您的應用程式或工作負載使用非常大型的資料集、需要私人網路功能，或無法連線到雲端，則可能無法與雲端服務通訊。 在此情況下，容器化的認知服務具有下列優點：

* **低連線能力**：您可以在任何計算環境中 (雲端上還是雲端下都行) 部署容器化的認知服務。 如果您的應用程式無法與雲端連線，請考慮在應用程式上部署容器化的認知服務。

* **低延遲**：因為容器化服務不需要與雲端來回通訊，所以在傳回回應時的延遲較低。

* **隱私權和資料安全性**：您可以將容器化服務部署到私人網路，以免敏感性資料離開網路。

* **高可擴縮性**：容器化服務沒有「速率限制」，且會在使用者管理的電腦上執行。 因此，您可以無限制地擴大認知服務，以處理更大的工作負載。

請遵循[本指南](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support?tabs=luis)以建立容器化認知服務。

## <a name="create-an-apache-spark-cluster"></a>建立 Apache Spark 叢集

[Apache Spark&trade;](http://spark.apache.org/) 是專為處理巨量資料而設計的分散式計算架構。 使用者可以使用 Azure Databricks、Azure Synapse Analytics、HDInsight 和 Azure Kubernetes Services 等服務，在 Azure 中使用 Apache Spark。 若要使用巨量資料認知服務，我們必須先建立叢集。 如果您已經有 Spark 叢集，則可以隨意試用範例。

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks 是 Apache Spark 型的分析平台，具有單鍵設定、簡化的工作流程和互動式工作區等功能。 其通常用來在資料科學家、工程師和商務分析師之間共同作業。 若要在 Azure Databricks 上使用巨量資料認知服務，請遵循下列步驟：

1. [建立 Azure Databricks 工作區](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)
1. [在 Databricks 中建立 Spark 叢集](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-a-spark-cluster-in-databricks)
1. 安裝巨量資料認知服務
    * 在 Databricks 工作區中建立新的程式庫  
       <img src="media/create-library.png" alt="Create library" width="50%"/>
    * 輸入下列 Maven 座標：`com.microsoft.ml.spark:mmlspark_2.11:1.0.0-rc3` 存放庫：`https://mmlspark.azureedge.net/maven`  
      <img src="media/library-coordinates.png" alt="Library Coordinates" width="50%"/>
    * 將程式庫安裝到叢集上  
      <img src="media/install-library.png" alt="Install Library on Cluster" width="50%"/>

### <a name="synapse-analytics-optional"></a>Synapse Analytics (選擇性)

(選擇性) 您可以使用 Synapse Analytics 來建立 Spark 叢集。 Azure Synapse Analytics 可將企業資料倉儲和巨量資料分析整合在一起。 其可讓您自由使用無伺服器隨選或佈建資源，隨意且大規模地查詢您的資料。 若要開始使用 Synapse Analytics，請遵循下列步驟：

1. [建立 Synapse 工作區 (預覽)](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace)。
1. [使用 Azure 入口網站建立新的 Apache Spark 集區 (預覽)](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool-portal)。

在 Synapse Analytics 中，預設會安裝巨量資料認知服務。

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

如果您使用容器化認知服務，其中一個用來一同部署 Spark 與容器的熱門選項就是 Azure Kubernetes Service。

若要開始使用 Azure Kubernetes Service，請遵循下列步驟：

1. [使用 Azure 入口網站部署 Azure Kubernetes Service (AKS) 叢集](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
1. [安裝 Apache Spark 2.4.0 Helm Chart](https://hub.helm.sh/charts/microsoft/spark)
1. [使用 Helm 安裝認知服務容器](https://docs.microsoft.com/azure/cognitive-services/computer-vision/deploy-computer-vision-on-premises)

## <a name="try-a-sample"></a>試用範例

在設定好 Spark 叢集和環境之後，我們可以執行簡短的範例。 本節會示範如何在 Azure Databricks 中使用巨量資料認知服務。

首先，我們可以在 Azure Databricks 中建立筆記本。 針對其他 Spark 叢集提供者，請使用其筆記本或 Spark 提交。

1. 從 [Azure Databricks] 功能表選擇 [新增筆記本]，以建立新的 Databricks 筆記本。

    <img src="media/new-notebook.png" alt="Create a new notebook" width="50%"/>

1. 在 [建立筆記本] 對話方塊中輸入名稱，選取 [Python] 作為語言，然後選取您先前建立的 Spark 叢集。

    <img src="media/databricks-notebook-details.jpg" alt="New notebook details" width="50%"/>

    選取 [建立]。

1. 將此程式碼片段貼到新的筆記本中。

```python
from mmlspark.cognitive import *
from pyspark.sql.functions import col

# Add your subscription key from Text Analytics (or a general Cognitive Service key)
service_key = "ADD-SUBSCRIPTION-KEY-HERE"

df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

results = sentiment.transform(df)

# Show the results in a table
display(results.select("text", col("sentiment")[0].getItem("score").alias("sentiment")))

```

1. 從 Azure 入口網站的 [文字分析] 儀表板中，從 [金鑰和端點] 功能表中取得您的訂用帳戶金鑰。
1. 以您的訂用帳戶金鑰取代 Databricks 筆記本程式碼中的訂用帳戶金鑰預留位置。
1. 選取筆記本資料格右上方的 [播放] 或 [三角形] 符號，以執行範例。 (選擇性) 選取筆記本頂端的 [全部執行]，以執行所有資料格。 答案會顯示在資料表的資料格下方。

### <a name="expected-results"></a>預期的結果

| text                                      |   情感 |
|:------------------------------------------|------------:|
| 今天我很高興，天氣真晴朗！(I am so happy today, its sunny!)           |   0.978959  |
| 我因為此尖峰時間的交通感到沮喪 (I am frustrated by this rush hour traffic) |   0.0237956 |
| Spark 上的認知服務還不錯 (The cognitive services on spark aint bad)  |   0.888896  |

## <a name="next-steps"></a>後續步驟

- [簡短的 Python 範例](samples-python.md)
- [簡短的 Scala 範例](samples-scala.md)
- [配方：預測性維護](recipes/anomaly-detection.md)
- [配方：智慧型藝術探索](recipes/art-explorer.md)
