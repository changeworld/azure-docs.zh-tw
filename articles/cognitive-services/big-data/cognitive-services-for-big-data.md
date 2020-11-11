---
title: 巨量資料的認知服務
description: 了解如何使用 Python、Java 和 Scala，在大型資料集上運用 Azure 認知服務。 透過適用於巨量資料的認知服務，您可以將持續改進的智慧型模型直接內嵌到 Apache Spark&trade; 和 SQL 計算內。
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.date: 07/09/2020
ms.author: marhamil
ms.openlocfilehash: 1b08925db12edffdaf5c85f1fa6f5934a412d81f
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363301"
---
# <a name="azure-cognitive-services-for-big-data"></a>適用於巨量資料的 Azure 認知服務

![適用於巨量資料的 Azure 認知服務](media/cognitive-services-big-data-overview.svg)

適用於巨量資料的 Azure 認知服務可讓使用者使用 [Apache Spark&trade;](/dotnet/spark/what-is-spark)，透過認知服務將數 TB 的資料進行通道傳送。 透過適用於巨量資料的認知服務，您可以使用任何資料存放區輕鬆地建立大規模的智慧型應用程式。

透過適用於巨量資料的認知服務，您可以將持續改進的智慧型模型直接內嵌到 Apache Spark&trade; 和 SQL 計算內。 這些工具可讓開發人員擺脫低層級的網路詳細資料，從而專注於建立智慧型的分散式應用程式。

## <a name="features-and-benefits"></a>功能與優點

適用於巨量資料的認知服務可以使用來自世界上任何區域的服務，以及[容器化的認知服務](../cognitive-services-container-support.md)。 容器可支援具有超低延遲回應的低連線或無連線部署。 容器化的認知服務可以在本機上執行、直接在 Spark 叢集的背景工作節點上執行，或在 Kubernetes 之類的外部協調器上執行。

## <a name="supported-services"></a>支援的服務

[認知服務](../index.yml) (透過 API 和 SDK 存取) 可協助開發人員建置智慧型應用程式，而不需要具備 AI 或資料科學技能。 透過認知服務，您可以讓應用程式看到、聽到、開口、理解和思考。 若要使用認知服務，您的應用程式必須透過網路將資料傳送至服務。 服務收到資料後，就會回傳智慧型回應。 下列服務適用於巨量資料工作負載：

### <a name="vision"></a>視覺

|服務名稱|服務描述|
|:-----------|:------------------|
|[電腦視覺](../computer-vision/index.yml "電腦視覺")| 電腦視覺服務可供您存取進階演算法，以處理影像及傳回資訊。 |
|[臉部](../face/index.yml "臉部")| 臉部辨識服務提供進階的臉部識別演算法，以偵測和辨識臉部屬性。 |

### <a name="speech"></a>語音

|服務名稱|服務描述|
|:-----------|:------------------|
|[語音服務](../speech-service/index.yml "語音服務")|語音服務可讓您存取語音辨識、語音合成、語音翻譯和說話者驗證與識別等功能。|

### <a name="decision"></a>決策

|服務名稱|服務描述|
|:-----------|:------------------|
|[異常偵測器](../anomaly-detector/index.yml "異常偵測器") | 異常偵測器 (預覽) 服務可讓您監視和偵測時間序列資料中的異常狀況。|

### <a name="language"></a>Language

|服務名稱|服務描述|
|:-----------|:------------------|
|[文字分析](../text-analytics/index.yml "文字分析")| 文字分析服務可針對原始文字進行自然語言處理，進行情感分析、關鍵片語擷取和語言偵測。|

### <a name="search"></a>搜尋

|服務名稱|服務描述|
|:-----------|:------------------|
|[Bing 影像搜尋](/azure/cognitive-services/bing-image-search "Bing 影像搜尋")|Bing 影像搜尋服務會傳回與使用者查詢相關的影像。|

## <a name="supported-programming-languages-for-cognitive-services-for-big-data"></a>針對適用於巨量資料的認知服務所支援的程式設計語言

適用於巨量資料的認知服務會建置在 Apache Spark 之上。 Apache Spark 是一種分散式計算程式庫，可支援 Java、Scala、Python、R 及其他許多語言。 目前支援以下語言。

### <a name="python"></a>Python

我們在 [Microsoft ML for Apache Spark](https://aka.ms/spark) 的 `mmlspark.cognitive` 命名空間中提供了 PySpark API。 如需詳細資訊，請參閱 [Python 開發人員 API](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/pyspark/mmlspark.cognitive.html)。 如需使用範例，請參閱 [Python 範例](samples-python.md)。

### <a name="scala-and-java"></a>Scala 和 Java

我們在 [Microsoft ML for Apache Spark](https://aka.ms/spark) 的 `com.microsoft.ml.spark.cognitive` 命名空間中提供了 Scala 和 Java 型的 Spark API。 如需詳細資訊，請參閱 [Scala 開發人員 API](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/scala/index.html#package)。 如需使用範例，請參閱 [Scala 範例](samples-scala.md)。

## <a name="supported-platforms-and-connectors"></a>支援的平台和連接器

適用於巨量資料的認知服務需要 Apache Spark。 有數個 Apache Spark 平台支援適用於巨量資料的認知服務。

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) 是一個針對 Microsoft Azure 雲端服務平台進行最佳化的 Apache Spark 分析平台。 其提供了單鍵設定、簡化的工作流程和互動式工作區，可支援資料科學家、資料工程師和商務分析師之間的共同作業。

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

[Azure Synapse Analytics](/azure/databricks/data/data-sources/azure/synapse-analytics) (先前稱為 SQL 資料倉儲) 是使用大量平行處理的企業資料倉儲。 透過 Synapse Analytics，您可以快速地在數 PB 的資料中執行複雜的查詢。 Azure Synapse Analytics 提供了受控 Spark 集區，可使用直覺式的 Jupyter Notebook 介面來執行 Spark 作業。

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

[Azure Kubernetes Service (AKS)](../../aks/index.yml) 會以大規模的方式協調 Docker 容器和分散式應用程式。 AKS 是受控的 Kubernetes 供應項目，可簡化在 Azure 中使用 Kubernetes 的方式。 Kubernetes 可以讓您更精細地控制認知服務的規模、延遲和網路。 不過，如果您不熟悉 Apache Spark，則建議您使用 Azure Databricks 或 Synapse Analytics。

### <a name="data-connectors"></a>資料連接器

當您擁有 Spark 叢集之後，下一步就是連線到您的資料。 Apache Spark 有廣泛的資料庫連接器集合。 這些連接器可讓應用程式處理大型資料集，而不論其儲存於何處。 如需所支援資料庫和連接器的詳細資訊，請參閱 [Azure Databricks 支援的資料來源清單](/azure/databricks/data/data-sources/)。

## <a name="concepts"></a>概念

### <a name="spark"></a>Spark

[Apache Spark&trade;](http://spark.apache.org/) 是用於進行大規模資料處理的整合分析引擎。 其平行處理架構會提升巨量資料和分析應用程式的效能。 Spark 可以同時以批次和串流處理系統的形式來運作，而不需要變更核心的應用程式程式碼。

Spark 的基礎是資料框架：以資料表形式收集分散在各個 Apache Spark 背景工作節點的資料。 Spark 資料框架就像是關聯式資料庫中的資料表或是 R/Python 中的資料框架，但可無限擴充。 您可以從許多來源 (例如：結構化資料檔案、Hive 中的資料表或外部資料庫) 建構資料框架。 一旦您的資料位於 Spark 資料框架中，您就可以：

   - 執行 SQL 樣式的計算，例如聯結和篩選資料表。
   - 使用 MapReduce 樣式平行處理原則，將函式套用至大型資料集。
   - 使用適用於 Apache Spark 的 Microsoft Machine Learning 來套用分散式 Machine Learning。
   - 使用適用於巨量資料的認知服務，透過立即可用的智慧型服務豐富您的資料。

### <a name="microsoft-machine-learning-for-apache-spark-mmlspark"></a>適用於 Apache Spark 的 Microsoft Machine Learning (MMLSpark)

[適用於 Apache Spark 的 Microsoft Machine Learning](https://mmlspark.blob.core.windows.net/website/index.html#install) (MMLSpark) 是建置在 Apache Spark 之上的開放原始碼分散式機器學習程式庫 (ML)。 適用於巨量資料的認知服務包含在此套件中。 此外，MMLSpark 還包含另外幾個適用於 Apache Spark 的 ML 工具，例如 LightGBM、Vowpal Wabbit、OpenCV、LIME 等等。 透過 MMLSpark，您可以從任何 Spark 資料來源建置功能強大的預測和分析模型。

### <a name="http-on-spark"></a>Spark 上的 HTTP

適用於巨量資料的認知服務是我們要如何將智慧型 Web 服務與巨量資料做整合的範例。 Web 服務可支援位於全球各地的許多應用程式，而大部分的服務都會透過超文字傳輸通訊協定 (HTTP) 來通訊。 為了大規模使用 *任意* Web 服務，我們提供了 Spark 上的 HTTP。 透過 Spark 上的 HTTP，您可以透過任何 Web 服務傳遞數 TB 的資料。 實際上，我們會使用這項技術來支援適用於巨量資料的認知服務。

## <a name="developer-samples"></a>開發人員範例

- [配方：預測性維護](recipes/anomaly-detection.md)
- [配方：智慧型藝術探索](recipes/art-explorer.md)

## <a name="blog-posts"></a>部落格文章

- [深入了解認知服務如何在 Apache Spark 上工作&trade;](https://azure.microsoft.com/blog/dear-spark-developers-welcome-to-azure-cognitive-services/)
- [透過在 Spark 上使用深度學習和電腦視覺來拯救雪豹](http://www.datawizard.io/2017/06/27/saving-snow-leopards-with-deep-learning-and-computer-vision-on-spark/)
- [Microsoft Research 播客：和 Mark Hamilton 一起了解可實現 AI for Good 的 MMLSpark](https://blubrry.com/microsoftresearch/49485070/092-mmlspark-empowering-ai-for-good-with-mark-hamilton/)
- [學術白皮書：大規模智慧型微服務](https://arxiv.org/abs/2009.08044)

## <a name="webinars-and-videos"></a>網路研討會和影片

- [Spark 上的 Azure 認知服務：具有內嵌智慧型服務的叢集](https://databricks.com/session/the-azure-cognitive-services-on-spark-clusters-with-embedded-intelligent-services)
- [Spark 高峰會專題演講：可調整的 AI for Good](https://databricks.com/session_eu19/scalable-ai-for-good)
- [Cosmos DB 中適用於巨量資料的認知服務](https://medius.studios.ms/Embed/Video-nc/B19-BRK3004?latestplayer=true&l=2571.208093)
- [大規模智慧型微服務的閃電秀](https://www.youtube.com/watch?v=BtuhmdIy9Fk&t=6s)

## <a name="next-steps"></a>後續步驟

- [開始使用適用於巨量資料的認知服務](getting-started.md)
- [簡單的 Python 範例](samples-python.md)
- [簡單的 Scala 範例](samples-scala.md)