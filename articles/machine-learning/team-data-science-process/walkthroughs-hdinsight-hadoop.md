---
title: 在 Azure HDInsight Hadoop 上使用 Hive 的分析 - Team Data Science Process
description: 以 Team Data Science Process 為例，逐步解說如何在 Azure HDInsight Hadoop 上使用 Hive 來執行預測性分析。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f72ea6ed5f0eec076d181ef56c99c4f1308a7741
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864157"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>在 Azure 上使用 Hive 的 HDInsight Hadoop 資料科學逐步解說 

這些逐步解說會使用 Hive 搭配 HDInsight Hadoop 叢集來執行預測性分析。 其遵循 Team Data Science Process 中所述的步驟。 如需 Team Data Science Process 的概觀，請參閱 [Data Science Process](overview.md)。 如需 Azure HDInsight 的簡介，請參閱 [Azure HDInsight、Hadoop 技術堆疊和 Hadoop 叢集簡介](../../hdinsight/hadoop/apache-hadoop-introduction.md)。

執行團隊資料科學流程的其他資料科學演練按他們使用**的平臺**分組。 如需這些範例的列述，請參閱[執行 Team Data Science Process 的逐步解說](walkthroughs.md)。


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>使用 Hive 搭配 HDInsight Hadoop 來預測計程車小費

[使用 HDInsight Hadoop 叢集](hive-walkthrough.md)逐步解說會使用紐約計程車行的資料來預測： 

- 是否給予小費 
- 小費金額的分佈

此案例是透過 Hive 搭配 [Azure HDInsight Hadoop 叢集](https://azure.microsoft.com/services/hdinsight/)來實作。 您將了解如何對公開使用之 NYC 計程車車程和車資資料集中的資料，進行儲存、探索及特性工程設計。 您也可以使用 Azure Machine Learning 來建置及部署模型。

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>使用 Hive 搭配 HDInsight Hadoop 來預測廣告點擊數

[在 1 TB 資料集演練中使用 Azure HDInsight Hadoop 群集](hive-criteo-walkthrough.md)使用公開可用的[Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/)按一下資料集來預測是否支付了小費和預期金額。 此案例是透過 Hive 搭配 [Azure HDInsight Hadoop 叢集](https://azure.microsoft.com/services/hdinsight/)來實作，以對資料進行儲存、探索、特性工程設計及縮減取樣。 它使用 Azure Machine Learning 來建立、定型二元分類模型並對其進行評分，以預測使用者是否點擊廣告。 此逐步解說最後會示範如何將其中一個模型發佈為 Web 服務。


## <a name="next-steps"></a>後續步驟

如需組成 Team Data Science Process 之主要元件的討論，請參閱 [Team Data Science Process 概觀](overview.md)。

如需您可以用來結構化資料科學專案之 Team Data Science Process 生命週期的討論，請參閱 [Team Data Science Process 生命週期](lifecycle.md)。 生命週期會概述專案在執行時通常會遵循之從開始到完成的步驟。 

