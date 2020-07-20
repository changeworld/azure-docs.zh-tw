---
title: 配方：利用適用于大型資料的認知服務進行智慧型美工探索
titleSuffix: Azure Cognitive Services
description: 此配方會示範如何使用 Azure 搜尋服務和 MMLSpark 來建立可搜尋的藝術資料庫。
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: tracking-python
ms.openlocfilehash: e1f17cad784137af927b9ec80d07bd3be092b82d
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86189389"
---
# <a name="recipe-intelligent-art-exploration-with-the-cognitive-services-for-big-data"></a>配方：利用適用于大型資料的認知服務進行智慧型美工探索

在此範例中，我們將使用大型資料的認知服務，從符合) 的圖片 (的大都市博物館中，將智慧型注釋新增至 Open Access 集合。 如此一來，即使沒有手動注釋，我們也可以使用 Azure 搜尋服務來建立智慧型搜尋引擎。 

## <a name="prerequisites"></a>先決條件

* 您必須有電腦視覺和認知搜尋的訂用帳戶金鑰。 依照[建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的指示訂閱電腦視覺並取得您的金鑰。
  > [!NOTE]
  > 如需定價資訊，請參閱[Azure 認知搜尋](https://azure.microsoft.com/services/search/#pricing)。

## <a name="import-libraries"></a>匯入程式庫

執行下列命令，以匯入此配方的程式庫。

```python
import os, sys, time, json, requests
from pyspark.ml import Transformer, Estimator, Pipeline
from pyspark.ml.feature import SQLTransformer
from pyspark.sql.functions import lit, udf, col, split
```

## <a name="set-up-subscription-keys"></a>設定訂用帳戶金鑰

執行下列命令來設定服務金鑰的變數。 插入您的訂用帳戶金鑰以進行電腦視覺和 Azure 認知搜尋。

```python
VISION_API_KEY = 'INSERT_COMPUTER_VISION_SUBSCRIPTION_KEY'
AZURE_SEARCH_KEY = 'INSERT_AZURE_COGNITIVE_SEARCH_SUBSCRIPTION_KEY'
search_service = "mmlspark-azure-search"
search_index = "test"
```

## <a name="read-the-data"></a>讀取資料

執行下列命令，從符合的開啟存取集合中載入資料。

```python
data = spark.read\
  .format("csv")\
  .option("header", True)\
  .load("wasbs://publicwasb@mmlspark.blob.core.windows.net/metartworks_sample.csv")\
  .withColumn("searchAction", lit("upload"))\
  .withColumn("Neighbors", split(col("Neighbors"), ",").cast("array<string>"))\
  .withColumn("Tags", split(col("Tags"), ",").cast("array<string>"))\
  .limit(25)
```

<a name="AnalyzeImages"></a>

## <a name="analyze-the-images"></a>分析影像

執行下列命令，以在符合的開啟存取權 artworks 集合上使用電腦視覺。 因此，您將會從 artworks 取得視覺功能。

```python
from mmlspark.cognitive import AnalyzeImage
from mmlspark.stages import SelectColumns

#define pipeline
describeImage = (AnalyzeImage()
  .setSubscriptionKey(VISION_API_KEY)
  .setLocation("eastus")
  .setImageUrlCol("PrimaryImageUrl")
  .setOutputCol("RawImageDescription")
  .setErrorCol("Errors")
  .setVisualFeatures(["Categories", "Tags", "Description", "Faces", "ImageType", "Color", "Adult"])
  .setConcurrency(5))

df2 = describeImage.transform(data)\
  .select("*", "RawImageDescription.*").drop("Errors", "RawImageDescription")
```

<a name="CreateSearchIndex"></a>

## <a name="create-the-search-index"></a>建立搜尋索引

執行下列命令，將結果寫入 Azure 搜尋服務，以使用來自電腦視覺的擴充中繼資料來建立 artworks 的搜尋引擎。

```python
from mmlspark.cognitive import *
df2.writeToAzureSearch(
  subscriptionKey=AZURE_SEARCH_KEY,
  actionCol="searchAction",
  serviceName=search_service,
  indexName=search_index,
  keyCol="ObjectID"
)
```

## <a name="query-the-search-index"></a>查詢搜尋索引

執行下列命令來查詢 Azure 搜尋服務索引。

```python
url = 'https://{}.search.windows.net/indexes/{}/docs/search?api-version=2019-05-06'.format(search_service, search_index)
requests.post(url, json={"search": "Glass"}, headers = {"api-key": AZURE_SEARCH_KEY}).json()
```

## <a name="next-steps"></a>後續步驟

瞭解如何使用[認知服務的大型資料進行異常偵測](anomaly-detection.md)。

