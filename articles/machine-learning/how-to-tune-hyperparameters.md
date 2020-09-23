---
title: 為您的模型微調超參數
titleSuffix: Azure Machine Learning
description: 使用 Azure Machine Learning 有效率地微調深度學習/機器學習模型的超參數。 您將瞭解如何定義參數搜尋空間、指定要優化的主要度量，以及提早終止效能不佳的執行。
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 04942c745548903a5f8092bc5b04ea2152029726
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885920"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>使用 Azure Machine Learning 為您的模型微調超參數


使用 Azure Machine Learning 有效率地微調模型的超參數。  超參數微調包含下列步驟：

* 定義參數搜尋空間
* 指定要最佳化的主要計量  
* 針對效能不佳的執行指定提早終止準則
* 配置用於超參數微調的資源
* 使用上述設定啟動實驗
* 視覺化定型執行
* 選擇最適合您模型的執行設定

## <a name="what-are-hyperparameters"></a>什麼是超參數？

超參數是您為定型模型所選擇的可調式參數，用以控管定型程序本身。 例如，若要定型深度類神經網路，在定型模型之前，您會決定網路中的隱藏層數目，以及每一層的節點數目。 這些值通常會在定型程序期間保持不變。

在深度學習/機器學習案例中，模型效能高度取決於所選取的超參數值。 超參數探索的目標是在各種超參數設定之間進行搜尋，以找到產生最佳效能的設定。 一般而言，超參數探索是很費力的手動程序，因為搜尋空間很大，而且評估每項設定的成本很高。

Azure Machine Learning 可讓您以有效率的方式來自動化超參數探索作業，為您省下大量時間和資源。 您可以指定超參數值範圍及定型執行數目上限。 系統會接著自動啟動多個同時但具有不同參數設定的執行，並找到產生最佳效能的設定 (以您所選擇的計量來測量)。 效能不佳的定型執行會自動提早終止，以減少計算資源浪費情形。 這些資源會改用來探索其他超參數設定。


## <a name="define-search-space"></a>定義搜尋空間

藉由探索為每個超參數定義的一組值，來自動微調超參數。

### <a name="types-of-hyperparameters"></a>超參數類型

每個超參數可以是離散或連續的，而且具有由 [參數運算式](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py&preserve-view=true)所描述的值分佈。

#### <a name="discrete-hyperparameters"></a>離散超參數 

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

在此案例中，`batch_size` 會採用 [16、32、64、128] 之中的一個值，而 `number_of_hidden_layers` 會採用 [1、2、3、4] 之中的一個值。

進階離散超參數也可以使用一項分佈來指定。 支援下列分佈：

* `quniform(low, high, q)` - 傳回 round(uniform(low, high) / q) * q 之類的值
* `qloguniform(low, high, q)` - 傳回 round(exp(uniform(low, high)) / q) * q 之類的值
* `qnormal(mu, sigma, q)` - 傳回 round(normal(mu, sigma) / q) * q 之類的值
* `qlognormal(mu, sigma, q)` - 傳回 round(exp(normal(mu, sigma)) / q) * q 之類的值

#### <a name="continuous-hyperparameters"></a>連續超參數 

連續超參數會指定為一組連續值的分佈。 支援的分佈包括：

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

您也可以指定要對超參數空間定義使用的參數取樣方法。 Azure Machine Learning 支援隨機取樣、網格取樣和貝氏取樣。

#### <a name="picking-a-sampling-method"></a>挑選取樣方法

* 如果您的超參數空間可以定義為離散值之間的選擇，而且您有足夠的預算可對定義的搜尋空間中的所有值進行徹底搜尋，則可以使用方格取樣。 此外，您還可以使用自動化的早期終止執行效能不佳的執行，這可減少資源的浪費。
* 隨機取樣可讓超參數空間包含離散和連續的超參數。 在實務上，它會在大部分的情況下產生良好的結果，而且也可讓您使用自動提早終止效能不佳的執行。 有些使用者使用隨機取樣來執行初始搜尋，然後反復地調整搜尋空間來改善結果。
* 貝氏取樣會在選擇超參數值時，利用先前範例的知識，並有效地嘗試改善回報的主要度量。 當您有足夠的預算可探索超參數空間時，建議使用貝氏取樣-為了獲得最佳結果，使用貝氏取樣時，建議使用大於或等於所微調超參數數目20倍的執行數目上限。 請注意，貝氏取樣目前不支援任何提早終止原則。

#### <a name="random-sampling"></a>隨機取樣

在隨機取樣中，超參數值會從定義的搜尋空間隨機選取。 [隨機取樣](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py&preserve-view=true) 可讓搜尋空間包含離散和連續超參數。

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

[方格取樣](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py&preserve-view=true) 會對已定義之搜尋空間中的所有可行值執行簡單的方格搜尋。 它只能搭配使用 `choice` 指定的超參數運作。 例如，下列空間共有六個樣本：

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

[貝氏取樣](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py&preserve-view=true) 是以貝氏優化演算法為基礎，可讓您在接下來的超參數值上做出明智的選擇。 它會根據先前樣本的執行方式來挑選樣本，以確保新樣本可改善回報的主要計量。

使用貝氏取樣時，同時執行的數目會影響微調程序有效性。 一般而言，較少的並行執行數目可產生較好的取樣收斂，因為較低程度的平行處理可讓更多執行受益於先前已完成的執行。

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

> [!NOTE]
> 貝氏取樣不支援任何提早終止原則 (請參閱[指定提早終止原則](#early-termination))。 使用貝氏參數取樣時，請設定 `early_termination_policy = None`，或停用 `early_termination_policy` 參數。

## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> 指定主要度量

指定您想要讓超參數微調實驗優化的 [主要度量](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py&preserve-view=true) 。 每個定型執行會針對此主要計量進行評估。 效能不佳的執行 (其主要計量不符合提早終止原則所設定的準則) 將會終止。 除了主要計量名稱，您也會指定最佳化的目標 (要最大化或最小化主要計量)。

* `primary_metric_name`：要最佳化的主要計量名稱。 主要計量名稱必須與定型指令碼所記錄的計量名稱完全相符。 請參閱[記錄用於超參數微調的計量](#log-metrics-for-hyperparameter-tuning)。
* `primary_metric_goal`：它可以是 `PrimaryMetricGoal.MAXIMIZE` 或 `PrimaryMetricGoal.MINIMIZE`，而且會在評估執行時，決定要最大化或最小化主要計量。 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

最佳化執行以將「正確性」提高到最大。  請務必記錄定型指令碼中的此值。

### <a name="specify-primary-metric"></a><a name="log-metrics-for-hyperparameter-tuning"></a> 指定主要度量

您模型的定型指令碼必須在模型定型期間記錄相關計量。 設定超參數微調時，您會指定用於評估執行效能的主要計量。  (，請參閱 [指定要優化的主要](#specify-primary-metric-to-optimize)計量。 ) 在您的定型腳本中，您必須記錄此計量，才能讓超參數微調程式使用該度量。

使用下列範例程式碼片段，將此計量記錄在定型指令碼中：

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

定型指令碼會計算 `val_accuracy` 並將其記錄為「正確性」，以用作主要計量。 每次記錄計量時，超參數微調服務都會收到該計量。 模型開發人員必須決定回報此計量的頻率。

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> 指定提早終止原則

使用提早終止原則來自動終止效能不佳的執行。 終止作業可減少資源浪費情形，並將這些資源改用於探索其他參數設定。

使用提早終止原則時，您可以設定下列參數，控制何時套用原則：

* `evaluation_interval`：套用原則的頻率。 每次定型指令碼記錄主要計量都算是一個間隔。 因此，`evaluation_interval` 為 1 表示只要定型指令碼回報主要計量就套用原則。 `evaluation_interval` 為 2 表示會在定型指令碼回報主要計量的隔次套用原則。 如果未指定，`evaluation_interval` 會預設為 1。
* `delay_evaluation`：將第一次原則評估延遲到指定間隔數目之後。 這是選擇性參數，允許所有設定執行最小的初始間隔數目，以避免定型執行過早終止。 如果指定，則會每隔 evaluation_interval (大於或等於 delay_evaluation) 的倍數套用原則一次。

Azure Machine Learning 支援下列提早終止原則。

### <a name="bandit-policy"></a>Bandit 原則

[Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#&preserve-view=truedefinition) 是以時差/時差和評估間隔為基礎的終止原則。 如果任何執行的主要計量不在為取得效能最佳之定型執行所指定的寬限時間因數/寬限時間數量內，原則會提早終止該執行。 它接受下列設定參數：

* `slack_factor` 或 `slack_amount`：為取得效能最佳之定型執行所允許的寬限時間。 `slack_factor` 將允許的寬限時間指定為小數比。 `slack_amount` 將允許的寬限時間指定為絕對數量，而不是小數比。

    例如，請思考在間隔 10 套用的 Bandit 原則。 假設在間隔 10 之效能最佳的執行回報主要計量為 0.8，而目標是要最大化主要計量。 如果指定 `slack_factor` 為 0.2 的原則，在間隔 10 之最佳計量小於 0.66 (0.8/(1+`slack_factor`)) 的任何定型執行都會終止。 相反地，如果指定 `slack_amount` 為 0.2 的原則，在間隔 10 之最佳計量小於 0.6 (0.8 - `slack_amount`) 的任何定型執行都會終止。
* `evaluation_interval`：套用原則的頻率 (選擇性參數)。
* `delay_evaluation`：將第一次原則評估延遲到指定間隔數目之後 (選擇性參數)。


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

在此範例中，自評估間隔 5 起，每隔一段時間回報計量時，就會套用提早終止原則。 如果任何執行的最佳計量小於效能最佳之執行的 (1/(1+0.1) 或 91%，該執行將會終止。

### <a name="median-stopping-policy"></a>中位數停止原則

[停用中間](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py&preserve-view=true) 值是一種早期終止原則，以執行所報告之主要計量的平均量為基礎。 此原則會跨所有定型執行與終止執行計算移動平均，這些執行的效能比移動平均的中位數還要差。 此原則接受下列設定參數：
* `evaluation_interval`：套用原則的頻率 (選擇性參數)。
* `delay_evaluation`：將第一次原則評估延遲到指定間隔數目之後 (選擇性參數)。


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

在此範例中，自評估間隔 5 起，每隔一段時間，就會套用提早終止原則。 如果執行的最佳計量在所有定型執行之間比間隔 1:5 的移動平均中位數還要差，該執行將會在間隔 5 終止。

### <a name="truncation-selection-policy"></a>截斷選取原則

[截斷選取範圍](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py&preserve-view=true) 會在每個評估間隔取消指定的最低執行執行百分比。 執行會根據其主要計量效能進行比較，並終止最低的 X%。 它接受下列設定參數：

* `truncation_percentage`：每個評估間隔效能最低之執行所要終止的百分比。 指定 1 到 99 之間的整數值。
* `evaluation_interval`：套用原則的頻率 (選擇性參數)。
* `delay_evaluation`：將第一次原則評估延遲到指定間隔數目之後 (選擇性參數)。


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

在此範例中，自評估間隔 5 起，每隔一段時間，就會套用提早終止原則。 如果在間隔5的效能是在間隔5的所有執行效能的最低20%，則會在第5個間隔結束執行。

### <a name="no-termination-policy"></a>無終止原則

如果您希望所有定型執行完成執行，請將原則設定為 None。 其效果相當於不套用任何提早終止原則。

```Python
policy=None
```

### <a name="default-policy"></a>預設原則

如果未指定任何原則，超參數微調服務會讓所有定型執行執行到完成。

### <a name="picking-an-early-termination-policy"></a>挑選提早終止原則

* 如果您在尋求可節省成本，但不會終止大有可為作業的保守原則，您可以使用「中位數停止原則」搭配 `evaluation_interval` 1 和 `delay_evaluation` 5。 這些是保守的設定，可在不遺失主要計量的情況下省下約 25%-35% (取決於我們的評估資料)。
* 如果您要尋找提早終止的更積極節省成本，可以使用 Bandit 原則搭配更嚴格的 (較大的) 允許的可執行空間或截斷選取原則，且截斷百分比較大。

## <a name="allocate-resources"></a>配置資源

指定定型執行數目的上限，可控制您超參數微調實驗的資源預算。  選擇性地指定超參數微調實驗的持續時間上限。

* `max_total_runs`：要建立的定型執行總數上限。 上限 - 如果超參數空間有限並具有較少的樣本，可能會有較少的執行。 必須是介於 1 到 1000 的數字。
* `max_duration_minutes`：超參數微調實驗的持續時間上限 (以分鐘為單位)。 這是選擇性參數，如果存在，在此持續時間之後執行的任何執行都會自動取消。

>[!NOTE] 
>如果同時指定 `max_total_runs` 和 `max_duration_minutes`，在達到這兩個閾值的第一個時，就會終止超參數微調實驗。

此外，也可指定要在超參數微調搜尋期間同時執行的定型執行數目上限。

* `max_concurrent_runs`：可在任何指定時間同時執行的執行數目上限。 如果未指定，則會同時啟動所有 `max_total_runs`。 如果指定，必須是介於 1 到 100 的數字。

>[!NOTE] 
>同時執行之數目會受限於指定計算目標中的可用資源。 因此，您必須確保計算目標有資源可用於所需的並行作業。

配置用於超參數微調的資源：

```Python
max_total_runs=20,
max_concurrent_runs=4
```

此程式碼會將超參數微調實驗設定為使用最多20個執行，一次執行四個設定。

## <a name="configure-experiment"></a>設定實驗

使用已定義的超參數搜尋空間、提早終止原則、主要計量，以及上述各節的資源配置，來[設定您的超參數微調](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py&preserve-view=true)實驗。 此外，請提供要透過取樣超參數來呼叫的 `estimator`。 `estimator` 描述您執行的定型指令碼、每個作業的資源 (單一或多個 GPU)，以及要使用的計算資源。 由於超參數微調實驗的並行處理受限於可用的資源，因此請確保 `estimator` 中指定的計算目標有足夠資源來進行所需的並行處理。 (如需估算器的詳細資訊，請參閱[如何將模型定型](how-to-train-ml-models.md))。

設定您的超參數微調實驗：

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>提交實驗

當您定義超參數微調設定之後，請 [提交實驗](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-)：

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name` 是您指派給超參數微調實驗的名稱，也 `workspace` 是您要在其中建立實驗的工作區 (如需實驗的詳細資訊，請參閱 [Azure Machine Learning 如何運作](concept-azure-machine-learning-architecture.md)) 

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>暖開機您的超參數微調實驗 (選擇性) 

通常，找出模型的最佳超參數值可能是反復的程式，需要從先前的超參數微調執行學習的多個微調回合。 重複使用這些先前回合中的知識將加快超參數微調程式的速度，進而降低調整模型的成本，而且可能會改善產生之模型的主要度量。 當您使用貝氏取樣開始進行超參數微調實驗時，會使用先前執行的試用版作為先前的知識，以智慧方式挑選新的範例，以改善主要度量。 此外，使用隨機或方格取樣時，任何早期終止決策都會利用先前回合中的計量，以判斷執行效能不佳的定型執行。 

Azure Machine Learning 可讓您從最多5個先前已完成/取消的超參數調整父執行的知識，來開始超參數微調執行。 您可以使用下列程式碼片段指定您要暖開機的父執行清單：

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

此外，在某些情況下，超參數微調實驗的個別定型回合會因為預算限制而取消，或因其他原因而失敗。 現在可以從最後一個檢查點繼續進行這類個別定型回合 (假設您的定型腳本會處理) 的檢查點。 繼續個別定型回合將會使用相同的超參數設定，並掛接該執行所使用的輸出檔案夾。 定型腳本應接受 `resume-from` 引數，其中包含要從中繼續定型執行的檢查點或模型檔。 您可以使用下列程式碼片段來繼續執行個別定型回合：

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

您可以設定您的超參數微調實驗，以從先前的實驗中開始暖開機，或使用選擇性參數和在設定中繼續個別定型執行 `resume_from` `resume_child_runs` ：

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
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

Azure Machine Learning SDK 提供可哥視化定型執行進度的 [筆記本 widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py&preserve-view=true) 。 您可以在 Jupyter Notebook 中使用下列程式碼片段，在同一個位置視覺化您所有的超參數微調執行：

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

此程式碼會顯示一個表格，其中包含每個超參數設定的定型執行相關詳細資料。

![超參數微調表](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

您也可以依照定型進度視覺化每個執行的效能。 

![超參數微調圖](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

此外，您可以使用平行座標圖，以視覺化方式識別效能與個別超參數值之間的關聯性。 

[![超參數微調平行座標](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

您也可以在 Azure 入口網站中，視覺化您所有的超參數微調執行。 如需如何在入口網站中檢視實驗的詳細資訊，請參閱[如何追蹤實驗](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal)。

## <a name="find-the-best-model"></a>尋找最佳模型

一旦所有的超參數微調執行完成，請 [找出效能最佳的](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#&preserve-view=trueget-best-run-by-primary-metric-include-failed-true--include-canceled-true--include-resume-from-runs-true-----typing-union-azureml-core-run-run--nonetype-) 設定和對應的超參數值：

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
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>下一步
* [追蹤實驗](how-to-track-experiments.md)
* [部署定型的模型](how-to-deploy-and-where.md)
