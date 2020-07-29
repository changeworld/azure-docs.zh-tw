---
title: 如何使用 WhiteNoise 套件來保留資料隱私權（預覽）
titleSuffix: Azure Machine Learning
description: 了解如何使用 WhiteNoise 套件，將差異隱私權最佳做法套用至 Azure Machine Learning 模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: slbird
author: slbird
ms.reviewer: luquinta
ms.date: 07/09/2020
ms.openlocfilehash: 2182c9bc7588947ece5a309018359a8bcfa3ff41
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320199"
---
# <a name="use-differential-privacy-in-azure-machine-learning-preview"></a>在 Azure Machine Learning 中使用差異隱私權（預覽）

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

了解如何使用 WhiteNoise Python 套件，將差異隱私權最佳做法套用至 Azure Machine Learning 模型。

差異隱私權是隱私權的金級標準定義。 遵守這項隱私權定義的系統可針對廣範圍的資料重建和重新識別攻擊提供強大的保證，包括擁有輔助資訊的敵人攻擊。 深入了解[差異隱私權如何運作](./concept-differential-privacy.md)。

> [!NOTE]
> 請注意，我們會重新命名此工具組，並會在接下來的幾周內引進新的名稱。 

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。
- [Python 3](https://www.python.org/downloads/) \(英文\)

## <a name="install-whitenoise-packages"></a>安裝 WhiteNoise 套件

### <a name="standalone-installation"></a>獨立式安裝

這些程式庫是設計用來從分散式 Spark 叢集工作，而且可以像任何其他套件一樣地進行安裝。

以下指示假設您的 `python` 和 `pip` 命令對應至 `python3` 和 `pip3`。

使用 pip 來安裝 [WhiteNoise Python 套件](https://pypi.org/project/opendp-whitenoise/)。

`pip install opendp-whitenoise`

若要確認已安裝套件，請啟動 Python 提示，並輸入：

```python
import opendp.whitenoise.core
import opendp.whitenoise.sql
```

如果匯入成功，則會安裝程式庫並備妥可供使用。

### <a name="docker-image"></a>Docker 映像

您也可以搭配 Docker 使用 WhiteNoise 套件。

提取 `opendp/whitenoise` 映像以使用 Docker 容器內的程式庫，其中包含 Spark、Jupyter 和範例程式碼。

```sh
docker pull opendp/whitenoise:privacy
```

提取映像之後，請啟動 Jupyter 伺服器：

```sh
docker run --rm -p 8989:8989 --name whitenoise-run opendp/whitenoise:privacy
```

這會在您 `localhost` 上的連接埠 `8989` 使用密碼 `pass@word99` 啟動 Jupyter 伺服器。 假設您使用上述命令列來啟動名為 `whitenoise-privacy` 的容器，您可以執行下列命令，在 Jupyter 伺服器中開啟 bash 終端機：

```sh
docker exec -it whitenoise-run bash
```

Docker 執行個體會在關機時清除所有狀態，因此您會遺失在執行中執行個體中建立的任何筆記本。 若要解決此問題，您可以在啟動時將本機資料夾繫結至容器：

```sh
docker run --rm -p 8989:8989 --name whitenoise-run --mount type=bind,source=/Users/your_name/my-notebooks,target=/home/privacy/my-notebooks opendp/whitenoise:privacy
```

您在「my-notebooks」資料夾下建立的任何筆記本，都會儲存在您的本機檔案系統中。

## <a name="perform-data-analysis"></a>執行資料分析

若要準備差異隱私版本，您必須選擇資料來源、統計資料，以及一些隱私權參數，指出隱私權保護的層級。  

這個範例參考加州公共用途微資料 (PUMS)，代表公民人口統計的匿名記錄：

```python
import os
import sys
import numpy as np
import opendp.whitenoise.core as wn

data_path = os.path.join('.', 'data', 'PUMS_california_demographics_1000', 'data.csv')
var_names = ["age", "sex", "educ", "race", "income", "married", "pid"]
```

在此範例中，我們會計算年齡的平均值和變異數。  我們使用 1.0 的總計 `epsilon` (epsilon 是我們的隱私權參數，將我們的隱私權預算散佈在我們想要計算的兩個數量。 深入了解[隱私權計量](concept-differential-privacy.md#differential-privacy-metrics)。

```python
with wn.Analysis() as analysis:
    # load data
    data = wn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = wn.dp_mean(data = wn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
    # get variance of age
    age_var = wn.dp_variance(data = wn.cast(data['age'], type="FLOAT"),
                             privacy_usage = {'epsilon': .35},
                             data_lower = 0.,
                             data_upper = 100.,
                             data_n = 1000
                            )
analysis.release()

print("DP mean of age: {0}".format(age_mean.value))
print("DP variance of age: {0}".format(age_var.value))
print("Privacy usage: {0}".format(analysis.privacy_usage))
```

結果看起來如下所示：

```text
DP mean of age: 44.55598845931517
DP variance of age: 231.79044646429134
Privacy usage: approximate {
  epsilon: 1.0
}
```

關於此範例，有一些要注意的重要事項。  首先，`Analysis` 物件代表資料處理圖表。  在此範例中，會從相同的來源節點計算平均值和變異數。  不過，您可以包含更複雜的運算式，以任意方式合併輸入與輸出。

分析圖表包含 `data_upper` 和 `data_lower` 中繼資料，指定年齡的下限和上限。  這些值可用來精確地校正雜訊，以確保差異隱私權。  這些值也會在某些極端值或遺漏值的處理中使用。

最後，分析圖表會持續追蹤所花費的隱私權預算總計。

您可以使用程式庫來撰寫更複雜的分析圖表，其中包含數個機制、統計資料和公用程式函式：

| 統計資料    | 機制 | 公用事業  |
| ------------- |------------|------------|
| Count         | 高斯   | 轉換       |
| 長條圖     | 幾何  | 固定   |
| 平均值          | 拉普拉斯    | 數位化   |
| 分位數     |            | Filter     |
| Sum           |            | 插補 |
| 變異數/共變數 |      | 轉換  |

如需詳細資訊，請參閱[基本資料分析筆記本](https://github.com/opendifferentialprivacy/whitenoise-samples/blob/master/analysis/basic_data_analysis.ipynb)。

## <a name="approximate-utility-of-differentially-private-releases"></a>差異隱私版本的大約公用程式

因為差異隱私權的運作方式是藉由校正雜訊，所以版本的公用程式可能會根據隱私權風險而有所不同。  一般來說，保護每個個人所需的雜訊，會因為樣本大小變大而變得微不足道，但是會對以單一個人為目標的版本結果造成壓倒性的影響。  分析師可以檢閱版本的正確性資訊，以判斷版本的有用程度：

```python
with wn.Analysis() as analysis:
    # load data
    data = wn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = wn.dp_mean(data = wn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
analysis.release()

print("Age accuracy is: {0}".format(age_mean.get_accuracy(0.05)))
```

該作業的結果看起來應該如下所示：

```text
Age accuracy is: 0.2995732273553991
```

這個範例會如上所述計算平均值，並使用 `get_accuracy` 函式來要求正確性在 0.05 的 `alpha`。 0\.05 的 `alpha` 代表 95% 的間隔，該發行值大約有 95% 的時間將會落在所回報正確性界限內。  在此範例中，回報正確性為 0.3，這表示發行值大約有 95% 的時間將會在寬度 0.6 的間隔內。  將此值視為誤差線並不正確，因為發行值將會以 `alpha` 所指定的速率落在回報正確性範圍外，而範圍外的值則可能位於任一方向以外。

分析師可能會針對不同的 `alpha` 值查詢 `get_accuracy`，以取得較窄或較寬的信賴區間，而不會產生額外的隱私權成本。

## <a name="generate-a-histogram"></a>產生長條圖

內建的 `dp_histogram` 函式會透過下列任何一種資料類型來建立差異隱私長條圖：

- 連續變數，其中數值集合必須劃分成多個 Bin
- 只能接受兩個值的布林值或二分變數
- 類別變數，其中有不同的分類，列舉為字串

以下是針對連續變數長條圖指定 Bin 的 `Analysis` 範例：

```python
income_edges = list(range(0, 100000, 10000))

with wn.Analysis() as analysis:
    data = wn.Dataset(path = data_path, column_names = var_names)

    income_histogram = wn.dp_histogram(
            wn.cast(data['income'], type='int', lower=0, upper=100),
            edges = income_edges,
            upper = 1000,
            null_value = 150,
            privacy_usage = {'epsilon': 0.5}
        )
```

因為每個個人都是在長條圖 Bin 之間不相交地進行資料分割，因此每個長條圖只會產生隱私權成本一次，即使長條圖包含許多 Bin 也一樣。

如需長條圖的詳細資訊，請參閱[長條圖筆記本](https://github.com/opendifferentialprivacy/whitenoise-samples/blob/master/analysis/histograms.ipynb)。

## <a name="generate-a-covariance-matrix"></a>產生共變數矩陣

WhiteNoise 提供三種不同的功能，以及其 `dp_covariance` 函式：

- 兩個向量之間的共變數
- 矩陣的共變數矩陣
- 一對矩陣的交叉共變數矩陣

以下是計算純量共變數的範例：

```python
with wn.Analysis() as analysis:
    wn_data = wn.Dataset(path = data_path, column_names = var_names)

    age_income_cov_scalar = wn.dp_covariance(
      left = wn.cast(wn_data['age'], 
      type = "FLOAT"), 
      right = wn.cast(wn_data['income'], 
      type = "FLOAT"), 
      privacy_usage = {'epsilon': 1.0},
      left_lower = 0., 
      left_upper = 100., 
      left_n = 1000, 
      right_lower = 0., 
      right_upper = 500_000.,
      right_n = 1000)
```

如需詳細資訊，請參閱[共變數筆記本](
https://github.com/opendifferentialprivacy/whitenoise-samples/blob/master/analysis/covariance.ipynb)

## <a name="next-steps"></a>後續步驟

- 探索 [WhiteNoise 範例筆記本](https://github.com/opendifferentialprivacy/whitenoise-samples/tree/master/analysis)。