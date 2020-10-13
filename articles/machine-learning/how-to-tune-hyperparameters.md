---
title: 為您的模型微調超參數
titleSuffix: Azure Machine Learning
description: 使用 Azure Machine Learning 有效率地微調深度學習和機器學習模型的超參數。
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 16a1c966b3f5a674f0ae1dc9c7ee078f45f8bdc2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91598228"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>使用 Azure Machine Learning 為您的模型微調超參數


使用 Azure Machine Learning [HyperDrive 套件](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py&preserve-view=true)將有效率的超參數微調自動化。 瞭解如何使用 [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true)完成微調超參數所需的步驟：

1. 定義參數搜尋空間
1. 指定要最佳化的主要計量  
1. 針對低執行的執行指定提早終止原則
1. 配置資源
1. 使用已定義的設定來啟動實驗
1. 視覺化定型執行
1. 為您的模型選取最佳設定

## <a name="what-are-hyperparameters"></a>什麼是超參數？

**超參數** 是可調整的參數，可讓您控制模型定型流程。 例如，使用類神經網路時，您可以決定隱藏層的數目和每個圖層中的節點數目。 模型效能主要取決於超參數。

 **超參數微調** 是尋找可產生最佳效能之超參數設定的程式。 此程式通常會耗費大量運算資源和手動。

Azure Machine Learning 可讓您將超參數調整自動化，並以平行方式執行實驗，以有效率地優化超參數。


## <a name="define-the-search-space"></a>定義搜尋空間

藉由探索為每個超參數定義的值範圍來調整超參數。

超參數可以是離散或連續的，而且具有由 [參數運算式](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py&preserve-view=true)所描述的值分佈。

### <a name="discrete-hyperparameters"></a>離散超參數 

離散超參數會指定為離散值之間的一個 `choice`。 `choice` 可以是：

* 一個或多個以逗點分隔的值
* `range` 物件
* 任意 `list` 物件


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

在此情況下， `batch_size` 其中一個值 [16，32，64，128]，並 `number_of_hidden_layers` 採用其中一個值 [1，2，3，4]。

您也可以使用散發來指定下列 advanced 離散超參數：

* `quniform(low, high, q)` - 傳回 round(uniform(low, high) / q) * q 之類的值
* `qloguniform(low, high, q)` - 傳回 round(exp(uniform(low, high)) / q) * q 之類的值
* `qnormal(mu, sigma, q)` - 傳回 round(normal(mu, sigma) / q) * q 之類的值
* `qlognormal(mu, sigma, q)` - 傳回 round(exp(normal(mu, sigma)) / q) * q 之類的值

### <a name="continuous-hyperparameters"></a>連續超參數 

連續的超參數會指定為連續範圍值的分佈：

* `uniform(low, high)` - 傳回在低值與高值之間均勻分佈的值
* `loguniform(low, high)` - 傳回根據 exp(uniform(low, high)) 繪製的值，讓傳回值的對數均勻分佈
* `normal(mu, sigma)` - 傳回以平均值 mu 和標準差 sigma 進行常態分佈的實數值
* `lognormal(mu, sigma)` - 傳回根據 exp(normal(mu, sigma)) 繪製的值，讓傳回值的對數常態分佈

參數空間定義的範例：

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

此程式碼會以兩個參數定義搜尋空間 - `learning_rate` 和 `keep_probability`。 `learning_rate` 有平均值 10 和標準差 3 的常態分佈。 `keep_probability` 有最小值 0.05 和最大值 0.1 的均勻分佈。

### <a name="sampling-the-hyperparameter-space"></a>取樣超參數空間

指定要在超參數空間上使用的參數取樣方法。 Azure Machine Learning 支援下列方法：

* 隨機取樣
* 網格取樣
* 貝氏取樣

#### <a name="random-sampling"></a>隨機取樣

[隨機取樣](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py&preserve-view=true) 支援離散和連續超參數。 它支援提早終止低效能執行。 有些使用者會進行隨機取樣的初始搜尋，然後精簡搜尋空間來改善結果。

在隨機取樣中，超參數值會從定義的搜尋空間隨機選取。 

```Python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import normal, uniform, choice
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>網格取樣

[方格取樣](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py&preserve-view=true) 支援離散超參數。 如果您可以透過預算來對搜尋空間進行徹底搜尋，請使用格線取樣。 支援低效能執行的早期終止。

對所有可能的值執行簡單的方格搜尋。 方格取樣只能與超參數搭配使用 `choice` 。 例如，下列空間有六個範例：

```Python
from azureml.train.hyperdrive import GridParameterSampling
from azureml.train.hyperdrive import choice
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>貝氏取樣

[貝氏取樣](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py&preserve-view=true) 是以貝氏優化演算法為基礎。 它會根據先前樣本的執行方式來挑選範例，讓新的範例改善主要度量。

如果您有足夠的預算可探索超參數空間，建議使用貝氏取樣。 為了獲得最佳結果，我們建議的執行數目上限大於或等於調整超參數數目的20倍。 

並存執行的數目會影響微調程式的有效性。 較少的並存執行可能會導致更好的取樣聚合，因為較小的平行處理原則程度會增加從先前完成的執行中獲益的執行數目。

貝氏取樣只支援 `choice` `uniform` `quniform` 搜尋空間上的、和分佈。

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```



## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> 指定主要度量

指定您想要超參數微調優化的 [主要度量](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py&preserve-view=true) 。 每個定型執行會針對此主要計量進行評估。 早期終止原則會使用主要度量來識別低效能的執行。

為您的主要度量指定下列屬性：

* `primary_metric_name`：主要度量的名稱必須與定型腳本所記錄的度量名稱完全相符
* `primary_metric_goal`：它可以是 `PrimaryMetricGoal.MAXIMIZE` 或 `PrimaryMetricGoal.MINIMIZE`，而且會在評估執行時，決定要最大化或最小化主要計量。 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

此範例最大化「精確度」。

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>記錄用於超參數微調的計量

您模型的定型腳本 **必須** 在模型定型期間記錄主要度量，讓 HyperDrive 可以存取它以進行超參數微調。

使用下列範例程式碼片段，在定型腳本中記錄主要度量：

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

定型腳本 `val_accuracy` 會計算，並將其記錄為「精確度」的主要度量。 每次記錄計量時，超參數微調服務都會收到此度量。 您可以決定報告的頻率。

如需有關在模型定型執行中記錄值的詳細資訊，請參閱 [在 AZURE ML 定型回合中啟用記錄](how-to-track-experiments.md)。

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> 指定提早終止原則

使用提早終止原則，自動終止效能不佳的執行。 提早終止可改善計算效率。

您可以設定下列參數來控制何時套用原則：

* `evaluation_interval`：套用原則的頻率。 每次定型指令碼記錄主要計量都算是一個間隔。 `evaluation_interval`1 會在每次定型腳本報告主要計量時套用原則。 `evaluation_interval`2 會在每隔時間套用原則。 如果未指定，`evaluation_interval` 會預設為 1。
* `delay_evaluation`：將第一次原則評估延遲到指定間隔數目之後。 這是選擇性參數，允許所有設定執行最小的間隔數目，以避免定型執行過早終止。 如果指定，則會每隔 evaluation_interval (大於或等於 delay_evaluation) 的倍數套用原則一次。

Azure Machine Learning 支援下列提早終止原則：
* [Bandit 原則](#bandit-policy)
* [中位數停止原則](#median-stopping-policy)
* [截斷選取原則](#truncation-selection-policy)
* [無終止原則](#no-termination-policy-default)


### <a name="bandit-policy"></a>Bandit 原則

[Bandit 原則](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py&preserve-view=true#&preserve-view=truedefinition) 是以寬限時間因數/時差和評估間隔為基礎。 相較于效能最佳的執行，Bandit 會終止執行，其中主要計量不在指定的時差係數/時差量內。

> [!NOTE]
> 貝氏取樣不支援提早終止。 使用貝氏取樣時，請設定 `early_termination_policy = None` 。

指定下列設定參數：

* `slack_factor` 或 `slack_amount`：為取得效能最佳之定型執行所允許的寬限時間。 `slack_factor` 將允許的寬限時間指定為小數比。 `slack_amount` 將允許的寬限時間指定為絕對數量，而不是小數比。

    例如，假設 Bandit 原則套用於間隔10。 假設在間隔10執行效能最佳的回合回報了主要計量為0.8，目標是要最大化主要度量。 如果原則指定 `slack_factor` 0.2，則在間隔10之最佳計量小於 0.66 (0.8/ (1 +) # A3 的任何定型執行 `slack_factor` 都會終止。
* `evaluation_interval`： (選擇性) 套用原則的頻率
* `delay_evaluation`： (選擇性) 延遲指定間隔的第一個原則評估


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

在此範例中，自評估間隔 5 起，每隔一段時間回報計量時，就會套用提早終止原則。 如果任何執行的最佳計量小於效能最佳之執行的 (1/(1+0.1) 或 91%，該執行將會終止。

### <a name="median-stopping-policy"></a>中位數停止原則

[停用中間](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py&preserve-view=true) 值是一種早期終止原則，以執行所報告之主要計量的平均量為基礎。 此原則會計算所有定型回合的執行平均值，並以比平均中間值更糟的主要計量值終止執行。

此原則接受下列設定參數：
* `evaluation_interval`：套用原則的頻率 (選擇性參數)。
* `delay_evaluation`：將第一次原則評估延遲到指定間隔數目之後 (選擇性參數)。


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

在此範例中，自評估間隔 5 起，每隔一段時間，就會套用提早終止原則。 如果在所有定型回合中，執行的最佳主要計量比間隔1:5 的平均中間值更糟，則會在第5個間隔結束執行。

### <a name="truncation-selection-policy"></a>截斷選取原則

[截斷選取範圍](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py&preserve-view=true) 會在每個評估間隔取消效能最低的執行百分比。 執行會使用主要度量進行比較。 

此原則接受下列設定參數：

* `truncation_percentage`：每個評估間隔效能最低之執行所要終止的百分比。 介於1到99之間的整數值。
* `evaluation_interval`： (選擇性) 套用原則的頻率
* `delay_evaluation`： (選擇性) 延遲指定間隔的第一個原則評估


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

在此範例中，自評估間隔 5 起，每隔一段時間，就會套用提早終止原則。 如果在間隔5的效能是在間隔5的所有執行效能的最低20%，則會在第5個間隔結束執行。

### <a name="no-termination-policy-default"></a>沒有終止原則 (預設) 

如果未指定任何原則，超參數微調服務會讓所有定型執行執行到完成。

```Python
policy=None
```

### <a name="picking-an-early-termination-policy"></a>挑選提早終止原則

* 針對可節省成本而不終止任何建議作業的保守原則，請考慮使用 `evaluation_interval` 1 和5以中間值停止原則 `delay_evaluation` 。 這些是保守的設定，可在不遺失主要計量的情況下省下約 25%-35% (取決於我們的評估資料)。
* 若要更積極地節省成本，請使用 Bandit 原則，並提供較小的允許時差或截斷選取原則，且截斷百分比較大。

## <a name="allocate-resources"></a>配置資源

藉由指定定型執行數目的上限，來控制您的資源預算。

* `max_total_runs`：定型執行數目上限。 必須是介於1到1000之間的整數。
* `max_duration_minutes`： (選擇性) 超參數微調實驗的最長持續時間（以分鐘為單位）。 在此期間取消之後執行。

>[!NOTE] 
>如果同時指定 `max_total_runs` 和 `max_duration_minutes`，在達到這兩個閾值的第一個時，就會終止超參數微調實驗。

此外，也可指定要在超參數微調搜尋期間同時執行的定型執行數目上限。

* `max_concurrent_runs`： (選擇性) 可同時執行的執行數目上限。 如果未指定，則會以平行方式啟動所有執行。 如果指定的話，必須是介於1到100之間的整數。

>[!NOTE] 
>同時執行之數目會受限於指定計算目標中的可用資源。 確定計算目標具有所需並行的可用資源。

```Python
max_total_runs=20,
max_concurrent_runs=4
```

此程式碼會將超參數微調實驗設定為使用最多20個執行，一次執行四個設定。

## <a name="configure-experiment"></a>設定實驗

若要 [設定您的超參數微調](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py&preserve-view=true) 實驗，請提供下列各項：
* 定義的超參數搜尋空間
* 您的提早終止原則
* 主要度量
* 資源配置設定
* ScriptRunConfig `src`

ScriptRunConfig 是將與取樣超參數一起執行的定型腳本。 它會定義每個作業的資源 (單一或多節點) ，以及要使用的計算目標。

> [!NOTE]
>中指定的計算目標 `src` 必須有足夠的資源，才能滿足您的平行存取層級。 如需 ScriptRunConfig 的詳細資訊，請參閱 [設定定型](how-to-set-up-training-targets.md)回合。

設定您的超參數微調實驗：

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>提交實驗

在您定義超參數微調設定之後，請 [提交實驗](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truesubmit-config--tags-none----kwargs-)：

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>暖開機您的超參數微調實驗 (選擇性) 

為您的模型尋找最佳的超參數值可以是反復的流程。 您可以重複使用前五個執行的知識，以加速超參數微調。


暖開機的處理方式不同于取樣方法：
- **貝氏取樣**：先前執行的試用版會用來做為挑選新範例的先前知識，以及改善主要度量。
- **隨機取樣** 或 **方格取樣**：提早終止會使用先前回合的知識，判斷執行效能不佳的執行。 

指定您想要暖開機的父執行清單。

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

如果取消超參數微調實驗，您可以從最後一個檢查點繼續定型執行。 不過，您的定型腳本必須處理檢查點邏輯。

定型回合必須使用相同的超參數設定，並將輸出檔案夾載入。 定型腳本必須接受 `resume-from` 引數，其中包含要從中繼續定型執行的檢查點或模型檔。 您可以使用下列程式碼片段來繼續執行個別定型回合：

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

您可以設定您的超參數微調實驗，以從先前的實驗中開始暖開機，或使用選擇性參數和在設定中繼續個別定型執行 `resume_from` `resume_child_runs` ：

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             resume_from=warmstart_parents_to_resume_from,
                             resume_child_runs=child_runs_to_resume,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="visualize-experiment"></a>視覺化實驗

使用 [筆記本 widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py&preserve-view=true) 來視覺化定型執行的進度。 您可以在 Jupyter Notebook 中使用下列程式碼片段，在同一個位置視覺化您所有的超參數微調執行：

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

此程式碼會顯示一個表格，其中包含每個超參數設定的定型執行相關詳細資料。

![超參數微調表](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

您也可以依照定型進度視覺化每個執行的效能。 

![超參數微調圖](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

您可以使用平行座標圖，以視覺化方式識別效能與個別超參數值之間的關聯性。 

[![超參數微調平行座標](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

您也可以在 Azure 入口網站中，將所有的超參數微調執行視覺化。 如需如何在入口網站中查看實驗的詳細資訊，請參閱 [如何追蹤實驗](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal)。

## <a name="find-the-best-model"></a>尋找最佳模型

一旦所有的超參數微調執行完成，請找出效能最佳的設定和超參數值：

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>範例 Notebook
請參閱此資料夾中的超參數-* 筆記本：
* [how-to-use-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟
* [追蹤實驗](how-to-track-experiments.md)
* [部署定型的模型](how-to-deploy-and-where.md)
