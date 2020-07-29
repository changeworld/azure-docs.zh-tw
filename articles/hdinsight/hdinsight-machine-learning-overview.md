---
title: 機器學習服務概觀 - Azure HDInsight
description: Azure HDInsight 中叢集的大型資料機器學習選項總覽。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 6c7f50e4ba3803b5cb929b75f5ad1127f7f063d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75931672"
---
# <a name="machine-learning-on-hdinsight"></a>HDInsight 上的機器學習服務

HDInsight 能以巨量資料實現機器學習服務，有能力從大量 (PB，甚至是 EB ) 的結構化、非結構化和快速移動的資料中，取得寶貴的深度見解分析。 HDInsight 中有數個機器學習服務選項： SparkML 和 Apache Spark MLlib、R、Apache Hive 和 Microsoft Cognitive Toolkit。

## <a name="sparkml-and-mllib"></a>SparkML 與 MLlib

[HDInsight Spark](spark/apache-spark-overview.md) 是 Azure 代管的 [Apache Spark](https://spark.apache.org/) 供應項目，是一個支援記憶體內部處理來大幅提升巨量資料分析效能的統一、開放原始碼的平行資料處理架構。 Spark 處理引擎是專為速度、易用性及精密分析打造的產品。 Spark 的記憶體內分散式計算功能，使其成為機器學習和圖表計算中所使用反覆演算法的絕佳選擇。 有兩個可將演算法模型化功能導入此分散式環境的可調整機器學習服務程式庫：MLlib 與 SparkML。 MLlib 包含建置在 RDD 上的原始 API。 SparkML 是較新的套件，可提供建置在 DataFrames 上的較高階 API 來建構 ML 管線。 SparkML 尚不支援 MLlib 的所有功能，但會將 MLlib 取代為 Spark 的標準機器學習程式庫。

適用於 Apache Spark 的 Microsoft Machine Learning 程式庫為 [MMLSpark](https://github.com/Azure/mmlspark)。 此程式庫是設計來提升資料科學家在 Spark 上的生產力，不僅可提高實驗率，還可在非常大型的資料集上運用最先進的機器學習服務技術，包括深度學習。 建立可調整的 ML 模型時 (例如，編製索引字串)，MMLSpark 會在 SparkML 的低階 API 上提供一個層級，將資料強制轉型成機器學習服務演算法預期的版面配置，並組合功能向量。 MMLSpark 程式庫簡化了這些流程，以及在 PySpark 中建立模型的常見工作。

## <a name="r"></a>R

[R](https://www.r-project.org/) 目前是全球最常用的統計程式設計語言， 這是一種開放原始碼的資料視覺效果工具，其具有超過2500000位使用者和成長的群體。 憑藉其蓬勃發展的使用者群，加上提供超過 8,000 個封裝，許多需要機器學習服務的公司可能會選擇使用 R。 您可以使用現成可與大量資料集和模型搭配使用的 ML 服務來建立 HDInsight 叢集。 此功能可為資料科學家和統計人員提供一個熟悉的 R 介面，此介面可視需要透過 HDInsight 進行調整，而不會產生叢集設定和維護的額外負荷。

![使用 R 伺服器進行預測訓練](./media/hdinsight-machine-learning-overview/training-for-prediction.png)

叢集的邊緣節點提供便利的地方，以便連線到叢集以及執行 R 指令碼。  您也可以使用 ScaleR 的 Hadoop Map 縮減或 Spark 計算內容，跨叢集的節點執行 R 腳本。

在具有 Spark 的 HDInsight 上使用 ML 服務，您可以使用 Spark 計算內容，在叢集的節點之間進行並行訓練。 您可以視需要透過並行方式使用所有可用核心，在邊緣節點上直接執行 R 指令碼。 或者，您可以從邊緣節點執行程式碼，啟動分散在叢集中所有節點上的處理作業。 具有 Spark 的 HDInsight 上的 ML 服務也可讓您從開放原始碼 R 套件平行處理函式（如有需要）。

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning 和 Apache Hive

Azure Machine Learning 提供可建立預測性分析模型的工具，以及完全受控的服務，您可以用來將預測模型部署為可供使用的 web 服務。 Azure Machine Learning 是完整的雲端預測性分析解決方案，您可以快速地建立、測試、操作及管理預測模型。 您可以從大型演算法程式庫中選取、使用 Web 型工作室來建置模型，然後將模型輕鬆部署成 Web 服務。

![Microsoft Azure 機器學習服務總覽](./media/hdinsight-machine-learning-overview/azure-machine-learning.png)

針對使用 [Hive 查詢](../machine-learning/team-data-science-process/create-features-hive.md)之 HDInsight Hadoop 叢集中的資料建立特徵。 *特徵工程設計*藉由以學習程序的原始資料來建立特徵，以嘗試增加學習演算法的預測能力。 您可以使用 [匯[入資料] 模組](../machine-learning/studio/import-data.md)，從 Azure Machine Learning Studio （傳統）執行 HiveQL 查詢，並存取 Hive 中處理的資料，並儲存在 blob 儲存體中。

## <a name="microsoft-cognitive-toolkit"></a>Microsoft 辨識工具組

[深度學習](https://www.microsoft.com/en-us/research/group/dltc/)是機器學習服務的一個分支，使用以人類大腦的生物學程序為靈感來源的類神經網路。 許多研究人員都將深度學習視為一個有前景的增強人工智慧方法。 深度學習的範例包括口語語言翻譯工具、影像辨識系統，以及機器推理。

為了協助推展自己在深度學習方面的工作，Microsoft 開發了免費、容易使用的開放原始碼 [Microsoft 辨識工具組](https://www.microsoft.com/en-us/cognitive-toolkit/)。 各種 Microsoft 產品、世界各地需要大規模部署深度學習的公司，以及對最新演算法和技術感興趣的學生，皆使用此工具組。

## <a name="see-also"></a>另請參閱

### <a name="scenarios"></a>案例

* [Apache Spark 和機器學習服務：使用 HDInsight 中的 Spark，使用 HVAC 資料來分析建築物溫度](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 和機器學習服務：在 HDInsight 中使用 Spark 預測食品檢查結果](spark/apache-spark-machine-learning-mllib-ipython.md)
* [使用 Apache Mahout 來產生電影推薦](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive 和 Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive 和 Azure Machine Learning 端對端](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [使用 HDInsight 上的 Apache Spark 進行機器學習](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>深度學習資源

* [使用 Microsoft 辨識工具組深入了解模型與 Azure HDInsight Spark 叢集](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [資料科學虛擬機器上的深度學習和 AI 架構（DSVM）](../machine-learning/data-science-virtual-machine/dsvm-tools-deep-learning-frameworks.md)
