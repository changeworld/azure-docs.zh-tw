---
title: 建立自動化機器學習實驗
titleSuffix: Azure Machine Learning
description: 瞭解如何定義自動化機器學習實驗的資料來源、計算和設定。
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperfq1, automl
ms.openlocfilehash: b49b9f710a98495342687c4ce1dc702078b27246
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535328"
---
# <a name="configure-automated-ml-experiments-in-python"></a>在 Python 中設定自動化 ML 實驗


在本指南中，您將了解如何使用 [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) 定義自動化機器學習實驗的各種組態設定。 自動化機器學習服務會為您挑選演算法和超參數，並產生馬上可進行部署的模型。 有數個選項可用來設定自動化機器學習實驗。

若要查看自動化機器學習實驗的範例，請參閱 [教學課程：使用自動化機器學習將分類模型定型](tutorial-auto-train-models.md) ，或 [使用雲端中的自動化機器學習來定型模型](how-to-auto-train-remote.md)。

自動化機器學習服務中的可用設定選項：

* 選取您的實驗類型：分類、迴歸或時間序列預測
* 資料來源、格式和擷取資料
* 選擇您的計算目標：本機或遠端
* 自動化機器學習實驗設定
* 執行自動化機器學習實驗
* 探索模型計量
* 註冊和部署模型

如果您不想使用程式碼，您也可以[在 Azure Machine Learning Studio 中建立自動化機器學習實驗](how-to-use-automated-ml-for-ml-models.md)。

## <a name="prerequisites"></a>必要條件

針對本文，您需要 
* Azure Machine Learning 工作區。 若要建立工作區，請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

* 安裝的 Azure Machine Learning Python SDK。
    若要安裝 SDK，您可以： 
    * 建立計算實例，此實例會自動安裝 SDK，並針對 ML 工作流程進行預先設定。 如需詳細資訊，請參閱 [建立和管理 Azure Machine Learning 計算實例](how-to-create-manage-compute-instance.md) 。 

    * [自行安裝 `automl` 套件](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment)，其中包括 SDK 的 [預設安裝](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py#default-install&preserve-view=true) 。

## <a name="select-your-experiment-type"></a>選取您的實驗類型

在開始實驗之前，您應先決定所要解決的機器學習問題類型。 自動化機器學習支援、和的工作類型 `classification` `regression` `forecasting` 。 深入了解[工作類型](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast)。

下列程式碼會使用函式 `task` 中的參數 `AutoMLConfig` ，將實驗類型指定為 `classification` 。

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>資料來源和格式

自動化機器學習支援位於本機桌面或雲端中 (例如 Azure Blob 儲存體) 所包含的資料。 資料可以讀入 **Pandas DataFrame** 或 **Azure Machine Learning TabularDataset** 中。 [深入了解資料集](how-to-create-register-datasets.md)。

定型資料的需求：
- 資料必須是表格形式。
- 要預測的值 (目標資料行) 必須位於資料中。

**針對遠端實驗** ，必須可從遠端計算存取定型資料。 AutoML 在處理遠端計算時只會接受 [Azure Machine Learning TabularDatasets](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py)。 

Azure Machine Learning 資料集會公開功能：

* 輕鬆地將資料從靜態檔案或 URL 來源傳送至您的工作區。
* 讓您的資料在執行於雲端計算資源時，可供定型指令碼使用。 如需使用類別將資料掛接至遠端計算目標的範例，請參閱如何使用 [資料集進行定型](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) `Dataset` 。

下列程式碼會從 web url 建立 TabularDataset。 如需如何從其他來源（例如本機檔案和資料存放區）建立資料集的程式碼範例，請參閱 [建立 TabularDatasets](how-to-create-register-datasets.md#create-a-tabulardataset) 。

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**針對本機計算實驗** ，建議您 pandas 資料框架以加快處理時間。

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>訓練、驗證和測試資料

您可以直接在函式中指定個別的 **定型和驗證集** `AutoMLConfig` 。 深入瞭解如何設定 AutoML 實驗的 [資料分割和交叉驗證](how-to-configure-cross-validation-data-splits.md) 。 

如果您未明確指定 `validation_data` 或 `n_cross_validation` 參數，AutoML 會套用預設的技巧，以決定如何執行驗證。 這項決定取決於資料集內指派給您的參數的資料列數目 `training_data` 。 

|定型 &nbsp; 資料 &nbsp; 大小| 驗證技術 |
|---|-----|
|**大於 &nbsp; 20000 的資料 &nbsp; &nbsp; 列**| 套用定型/驗證資料分割。 預設值是以10% 的初始訓練資料集做為驗證集。 接著，該驗證集會用於計算度量。
|**小於 &nbsp; 20000 的資料 &nbsp; &nbsp; 列**| 套用交叉驗證方法。 預設的折迭數目取決於資料列數。 <br> **如果資料集小於1000個數據列** ，則會使用10個折迭。 <br> **如果資料列介於1000到20000之間** ，則會使用三個折迭。

目前，您需要提供自己的 **測試資料** 來進行模型評估。 如需將您自己的測試資料用於模型評估的程式碼範例，請參閱 [此 Jupyter 筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)的 **test** 區段。

## <a name="compute-to-run-experiment"></a>要執行實驗的計算

接下來，請決定要在何處訓練模型。 自動化機器學習訓練實驗可在下列計算選項上執行。 了解[本機和遠端計算選項的優缺點](concept-automated-ml.md#local-remote)。 

* 您的 **本機** 電腦，例如本機桌上型電腦或膝上型電腦–通常是當您有小型資料集，而您仍在探索階段時。 如需本機計算範例，請參閱[此筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb)。 
 
* 雲端中的 **遠端** 電腦- [Azure Machine Learning 受控計算](concept-compute-target.md#amlcompute) 是一項受控服務，可讓您在 Azure 虛擬機器叢集上定型機器學習模型。 

    如需使用 Azure Machine Learning 受控計算的遠端範例，請參閱[此筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)。 

* Azure 訂用帳戶中的 **Azure Databricks** 叢集。 您可以在 [設定自動化 ML 的 Azure Databricks 叢集中](how-to-configure-databricks-automl-environment.md)找到更多詳細資料。 如需 Azure Databricks 的筆記本範例，請參閱此 [GitHub 網站](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl)。

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>設定您的實驗設定

有數個選項可用來設定自動化機器學習實驗。 將 `AutoMLConfig` 物件具現化即可設定這些參數。 如需完整的參數清單，請參閱 [AutoMLConfig 類別](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)。

部分範例包括：

1. 使用加權的 AUC 作為主要計量的分類實驗，實驗逾時分鐘數設定為 30 分鐘，交叉驗證摺疊設為 2。

   ```python
       automl_classifier=AutoMLConfig(
       task='classification',
       primary_metric='AUC_weighted',
       experiment_timeout_minutes=30,
       blocked_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
1. 下列範例是設定為在60分鐘之後結束的回歸實驗，其中五個驗證交叉折迭。

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      allowed_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```


1. 預測工作需要額外的設定，如需詳細資料，請參閱 [自動定型時間序列預測模型](how-to-auto-train-forecast.md) 文章。 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'drop_column_names': ['logQuantity'],
        'forecast_horizon': n_test_periods
    }
    
    automl_config = AutoMLConfig(task = 'forecasting',
                                 debug_log='automl_oj_sales_errors.log',
                                 primary_metric='normalized_root_mean_squared_error',
                                 experiment_timeout_minutes=20,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 path=project_folder,
                                 verbosity=logging.INFO,
                                 **time_series_settings)
    ```
    
### <a name="supported-models"></a>支援的模型

自動化機器學習會在自動化和調整程式期間嘗試不同的模型和演算法。 身為使用者，您不需要指定演算法。 

三個不同的 `task` 參數值決定要套用的演算法或模型清單。 請使用 `allowed_models` 或 `blocked_models` 參數，透過要包含或排除的可用模型進一步修改反覆項目。 

下表摘要說明依工作類型支援的模型。 

> [!NOTE]
> 如果您想要將自動 ML 建立的模型匯出至 [ONNX 模型](concept-onnx.md)，只有以 * 表示的演算法才能轉換為 ONNX 格式。 深入了解[將模型轉換為 ONNX](concept-automated-ml.md#use-with-onnx)。 <br> <br> 另請注意，ONNX 目前僅支援分類和迴歸工作。 

分類 | 迴歸 | 時間序列預測
|-- |-- |--
[羅吉斯迴歸](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [彈性網路](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [彈性網路](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[梯度提升](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[梯度提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[漸層停駐提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[決策樹](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[決策樹](https://scikit-learn.org/stable/modules/tree.html#regression)* |[決策樹](https://scikit-learn.org/stable/modules/tree.html#regression)
[K 最近鄰項目](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K 最近鄰項目](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K 最近鄰演算法](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[線性 SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[支援向量分類 (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[平均感知分類器](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[線上梯度下降迴歸輸入變數](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[貝氏機率分類](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[快速線性迴歸輸入變數](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[線性 SVM 分類器](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>主要計量
`primary metric`參數會決定要在模型定型期間用來優化的度量。 可選取的可用計量取決於您所選擇的工作類型，下表顯示每種工作類型的有效主要計量。

請在[了解自動化機器學習結果](how-to-understand-automated-ml.md)中了解這些計量的特定定義。

|分類 | 迴歸 | 時間序列預測
|--|--|--
|精確度| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-featurization"></a>資料特徵化

在每個自動化機器學習實驗中，您的資料都會自動調整並標準化，以利易受不同規模的特徵所影響的 *特定* 演算法能妥善運作。 此調整和正規化稱為特徵化。 如需詳細資訊和程式碼範例，請參閱 [AutoML 中的特徵化](how-to-configure-auto-features.md#) 。 

在物件中設定您的實驗時 `AutoMLConfig` ，您可以啟用/停用設定 `featurization` 。 下表顯示 [AutoMLConfig 物件](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)中特徵化的已接受設定。 

|特徵化設定 | 描述 |
| ------------- | ------------- |
|`"featurization": 'auto'`| 指出在前置處理過程中，會自動執行[資料護欄和特徵化步驟](how-to-configure-auto-features.md#featurization)。 **預設設定** 。|
|`"featurization": 'off'`| 表示特徵化步驟不應自動完成。|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 指出應使用自訂的特徵化步驟。 [了解如何自訂特徵化](how-to-configure-auto-features.md#customize-featurization)。|

> [!NOTE]
> 自動化機器學習特徵化步驟 (功能標準化、處理遺漏的資料、將文字轉換為數值等等) 會成為基礎模型的一部分。 使用模型進行預測時，定型期間所套用的相同特徵化步驟會自動套用至您的輸入資料。

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a> 集團設定

集團模型預設會啟用，並在 AutoML 執行中顯示為最終執行反復專案。 目前支援 **VotingEnsemble** 和 **StackEnsemble** 。 

投票會執行使用加權平均值的軟投票。 堆疊的執行會使用兩個層級的實作為，第一層的模型與投票集團相同，而第二層模型則是用來從第一層找出最佳的模型組合。 

如果您使用 ONNX 模型， **或** 啟用模型可解釋性，則會停用堆疊，而且只會使用投票。

您可以使用 `enable_voting_ensemble` 和布林值參數來停用集團定型 `enable_stack_ensemble` 。

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

若要變更預設的集團行為，可以在物件中提供多個預設引數 `kwargs` `AutoMLConfig` 。

> [!IMPORTANT]
>  下列參數不是 AutoMLConfig 類別的明確參數。 

* `ensemble_download_models_timeout_sec`:在 **VotingEnsemble** 和 **StackEnsemble** 模型產生期間，會從先前的子執行中下載多個適合的模型。 如果發生 `AutoMLEnsembleException: Could not find any models for running ensembling` 錯誤，您可能需要提供更多時間來下載模型。 以平行方式下載這些模型的預設值為 300 秒，且沒有最大逾時限制。 如果需要更多時間，請將此參數設定為高於 300 秒的值。 

  > [!NOTE]
  >  在到達逾時後，如果有已下載的模型，則會以已下載的所有模型繼續進行集團化。 需要下載的所有模型不一定要在該逾時內完成。

下列參數僅適用於 **StackEnsemble** 模型： 

* `stack_meta_learner_type`：中繼學習模組是以個別異質模型的輸出定型的模型。 分類工作的預設中繼學習模組是 `LogisticRegression` (如果已啟用交叉驗證，則為 `LogisticRegressionCV`)，迴歸/預測工作的預設模組為 `ElasticNet` (如果已啟用交叉驗證，則為 `ElasticNetCV`)。 這個參數可以是下列其中一個字串：`LogisticRegression`、`LogisticRegressionCV`、`LightGBMClassifier`、`ElasticNet`、`ElasticNetCV`、`LightGBMRegressor` 或 `LinearRegression`。

* `stack_meta_learner_train_percentage`：指定要保留用來定型中繼學習模組的定型集比例 (在選擇定型和定型的驗證類型時)。 預設值為 `0.2`。 

* `stack_meta_learner_kwargs`：要傳至中繼學習模組之初始設定式的選擇性參數。 這些參數和參數類型會與對應模型建構函式中的參數和參數類型相對映，且會轉送至模型建構函式。

下列程式碼顯示在 `AutoMLConfig` 物件中指定自訂集團行為的範例。

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

## <a name="run-experiment"></a>執行實驗

針對自動化 ML，您可以建立 `Experiment` 物件，作為 `Workspace` 中用來執行實驗的具名物件。

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

提交實驗以執行與產生模型。 將 `AutoMLConfig` 傳至 `submit` 方法以產生模型。

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>相依性會先安裝在新的機器上。  最多可能需要 10 分鐘才會顯示輸出。
>將 `show_output` 設定為 `True`，會使輸出顯示在主控台上。

 <a name="exit"></a> 

### <a name="exit-criteria"></a>允出準則

您可以定義幾個選項來結束實驗。

|準則| description
|----|----
無 &nbsp; 準則 | 如果您未定義任何結束參數，實驗會繼續進行，直到您的主要計量沒有進一步的進度為止。
經過 &nbsp; 一 &nbsp; 段 &nbsp; &nbsp; 時間之後| 在您的設定中使用， `experiment_timeout_minutes` 以定義您的實驗應該繼續執行的時間（以分鐘為單位）。 <br><br> 如果您的資料行大小超過10000000，則為避免實驗超時失敗，最少15分鐘或60分鐘。
已 &nbsp; &nbsp; 達到分數 &nbsp; &nbsp;| `experiment_exit_score`在達到指定的主要度量分數之後，請使用完成實驗。

## <a name="explore-models-and-metrics"></a>探索模型和計量

如果您使用筆記本，您可以在小工具中或以內嵌方式檢視定型結果。 請參閱[追蹤和評估模型](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs)以取得更多詳細資料。

請參閱 [評估自動化機器學習實驗的結果](how-to-understand-automated-ml.md) ，以瞭解每次執行所提供的效能圖表和度量的定義和範例。 

若要取得特徵化摘要，並瞭解已新增至特定模型的功能，請參閱 [特徵化透明度](how-to-configure-auto-features.md#featurization-transparency)。 

> [!NOTE]
> 自動化 ML 採用的演算法具有固有的隨機性，可能會導致建議模型的最終計量分數稍微變化，例如精確度。 自動化 ML 也會在必要時對資料（例如，訓練-測試分割、定型驗證分割或交叉驗證）執行作業。 因此，如果您多次執行具有相同設定和主要計量的實驗，您可能會在每個實驗中看到由於這些因素的最終計量分數變化。 

## <a name="register-and-deploy-models"></a>註冊和部署模型

如需如何下載或註冊模型以部署至 web 服務的詳細資訊，請參閱 [部署模型的方式和位置](how-to-deploy-and-where.md)。

<a name="explain"></a>

## <a name="model-interpretability"></a>模型可解釋性

模型可解釋性可讓您了解模型進行預測的原因，以及基礎特徵重要性的值。 SDK 包含多種套件，可讓您在定型和推斷期間，對本機和已部署的模型啟用模型可解釋性特徵。

若要了解如何在自動化機器學習實驗中明確啟用可解釋性特徵，請參閱[操作說明](how-to-machine-learning-interpretability-automl.md)中的程式碼範例。

如需如何在自動化機器學習以外的其他 SDK 區域中啟用模型說明和特徵重要性的一般資訊，請參閱[概念](how-to-machine-learning-interpretability.md)一文中有關於可解釋性的部分。

> [!NOTE]
> 說明用戶端目前不支援 ForecastTCN 模型。 如果傳回的是最佳模型，此模型將不會傳回說明儀表板，而且不支援隨選說明執行。

## <a name="next-steps"></a>後續步驟

+ 深入了解[模型部署的方式和位置](how-to-deploy-and-where.md)。

+ 深入了解[如何使用自動化機器學習定型迴歸模型](tutorial-auto-train-models.md)或[如何使用自動化機器學習以遠端資源定型](how-to-auto-train-remote.md)。

+ 瞭解如何在 [許多模型解決方案加速器](https://aka.ms/many-models)中使用 AutoML 來訓練多個模型。
