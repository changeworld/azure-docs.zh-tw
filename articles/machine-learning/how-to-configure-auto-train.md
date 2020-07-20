---
title: 建立自動化機器學習實驗
titleSuffix: Azure Machine Learning
description: 自動化機器學習服務可為您挑選演算法，並產生馬上可進行部署的模型。 了解可用來設定自動化機器學習實驗的選項。
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 05/20/2020
ms.custom: seodec18, tracking-python
ms.openlocfilehash: 528696daf4bddd1f448266243b511e600351606a
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86202601"
---
# <a name="configure-automated-ml-experiments-in-python"></a>在 Python 中設定自動化 ML 實驗
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本指南中，您將了解如何使用 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 定義自動化機器學習實驗的各種組態設定。 自動化機器學習服務會為您挑選演算法和超參數，並產生馬上可進行部署的模型。 有數個選項可用來設定自動化機器學習實驗。

若要檢視自動化機器學習實驗的範例，請參閱[教學課程：使用自動化機器學習訓練分類模型](tutorial-auto-train-models.md)或[使用雲端中的自動化機器學習來訓練模型](how-to-auto-train-remote.md)。

自動化機器學習服務中的可用設定選項：

* 選取您的實驗類型：分類、迴歸或時間序列預測
* 資料來源、格式和擷取資料
* 選擇您的計算目標：本機或遠端
* 自動化機器學習實驗設定
* 執行自動化機器學習實驗
* 探索模型計量
* 註冊和部署模型

如果您不想使用程式碼，您也可以[在 Azure Machine Learning Studio 中建立自動化機器學習實驗](how-to-use-automated-ml-for-ml-models.md)。

## <a name="select-your-experiment-type"></a>選取您的實驗類型

在開始實驗之前，您應先決定所要解決的機器學習問題類型。 自動化機器學習支援分類、迴歸和預測等工作類型。 深入了解[工作類型](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast)。

在自動化和調整程序期間，自動化機器學習支援下列演算法。 身為使用者，您不需要指定演算法。

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
[平均感知分類器](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)|[線上梯度下降迴歸輸入變數](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[貝氏機率分類](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[快速線性迴歸輸入變數](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[線性 SVM 分類器](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)*||

在 `AutoMLConfig` 建構函式中使用 `task` 參數指定您的實驗類型。

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>資料來源和格式

自動化機器學習支援位於本機桌面或雲端中 (例如 Azure Blob 儲存體) 所包含的資料。 資料可以讀入 **Pandas DataFrame** 或 **Azure Machine Learning TabularDataset** 中。  [深入了解資料集](how-to-create-register-datasets.md)。

定型資料的需求：
- 資料必須是表格形式。
- 要預測的值 (目標資料行) 必須位於資料中。

下列程式碼範例示範如何以這些格式儲存資料。

* TabularDataset

  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* Pandas 資料框架

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>擷取用來對遠端計算執行實驗的資料

若要進行遠端執行，必須可從遠端計算存取定型資料。 SDK 中的類別 [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) 會公開下列功能：

* 輕鬆地將靜態檔案或 URL 來源中的資料傳輸至您的工作區
* 讓您的資料在執行於雲端計算資源時可供定型指令碼使用

如需使用 `Dataset` 類別將資料掛接至計算目標的範例，請參閱[操作說明](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets)。

## <a name="train-and-validation-data"></a>訓練和驗證資料

您可以使用下列選項，直接在此函式中指定個別的定型和驗證集 `AutoMLConfig` 。 深入瞭解如何為您的 AutoML 實驗[設定資料分割和交叉驗證](how-to-configure-cross-validation-data-splits.md)。 

### <a name="k-folds-cross-validation"></a>k 疊交叉驗證

使用 `n_cross_validations` 設定來指定交叉驗證的數目。 訓練資料集會隨機分割為相同大小的 `n_cross_validations` 疊。 在每個交叉驗證回合中，將使用其中一個疊來驗證以其餘幾疊進行訓練的模型。 此程序會重複執行 `n_cross_validations` 回合，直到每一疊都已作為驗證集執行一次。 將會報告 `n_cross_validations` 個回合的總平均分數，並以整個訓練資料集重新訓練對應的模型。

深入了解自動 ML 如何套用交叉驗證以[防止過度調整模型](concept-manage-ml-pitfalls.md#prevent-over-fitting)。

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>蒙地卡羅交叉驗證 (重複隨機子取樣)

使用 `validation_size` 指定應該用於驗證的訓練資料集百分比，並使用 `n_cross_validations` 指定交叉驗證的數目。 在每個交叉驗證回合中，將隨機選取 `validation_size` 大小的子集來驗證以其餘資料進行訓練的模型。 最後將會報告 `n_cross_validations` 個回合的總平均分數，並以整個訓練資料集重新訓練對應的模型。 時間序列預測不支援蒙地卡羅。

### <a name="custom-validation-dataset"></a>自訂驗證資料集

如果隨機分割不可行，請使用自訂驗證資料集 (通常是時間序列資料或不平衡的資料)。 您可以指定自己的驗證資料集。 系統會根據指定的驗證資料集來評估模型，而不是隨機的資料集。 深入瞭解[如何使用 SDK 設定自訂驗證集](how-to-configure-cross-validation-data-splits.md#provide-validation-data)。

## <a name="compute-to-run-experiment"></a>要執行實驗的計算

接下來，請決定要在何處訓練模型。 自動化機器學習訓練實驗可在下列計算選項上執行：
* 您的本機電腦，例如本機桌上型電腦或膝上型電腦 – 通常適用於資料集並不大，且您尚在探索階段時。
* 雲端中的遠端機器 – [Azure Machine Learning 受控計算](concept-compute-target.md#amlcompute)是一項受控服務，能夠在 Azure 虛擬機器叢集上定型機器學習模型。 

  如需本機與遠端計算目標的筆記本範例，請參閱此 [GitHub 網站](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)。

* Azure 訂用帳戶中的 Azure Databricks 叢集。 您可以在這裡找到更多詳細資料 - [設定用於自動化 ML 的 Azure Databricks 叢集](how-to-configure-environment.md#azure-databricks)

  如需 Azure Databricks 的筆記本範例，請參閱此 [GitHub 網站](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl)。

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
       blacklist_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
2. 以下是迴歸實驗的範例，設定為在 60 分鐘後結束，且具有五個驗證交叉摺疊。

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      whitelist_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```

有三個不同的 `task` 參數值 (第三個工作類型為 `forecasting`，會以類似的演算法集區作為 `regression` 工作) 會決定要套用的模型清單。 請使用 `whitelist` 或 `blacklist` 參數，透過要包含或排除的可用模型進一步修改反覆項目。 支援的模型清單可以在 [SupportedModels 類別](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) ([分類](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification)、[預測](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)和[迴歸](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)) 中找到。

為了避免發生實驗逾時失敗，自動化 ML 的驗證服務會要求將 `experiment_timeout_minutes` 設定為至少 15 分鐘，或者，如果資料列乘以資料行的大小超過 1000 萬，則須設為 60 分鐘。

### <a name="primary-metric"></a>主要計量
主要計量會決定在模型定型期間要用於最佳化的計量。 可選取的可用計量取決於您所選擇的工作類型，下表顯示每種工作類型的有效主要計量。

|分類 | 迴歸 | 時間序列預測
|-- |-- |--
|精確度| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

請在[了解自動化機器學習結果](how-to-understand-automated-ml.md)中了解這些計量的特定定義。

### <a name="data-featurization"></a>資料特徵化

在每個自動化機器學習實驗中，您的資料都會[自動調整並標準化](how-to-configure-auto-features.md#)，以利易受不同規模的特徵所影響的*特定*演算法能妥善運作。  不過，您也可以啟用其他特徵化，例如遺漏值插補、編碼和轉換。

在物件中設定您的實驗時 `AutoMLConfig` ，您可以啟用/停用此設定 `featurization` 。 下表顯示 [AutoMLConfig 類別](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)中已接受的特徵化設定。

|特徵化設定 | 描述 |
| ------------- | ------------- |
|`"featurization": 'auto'`| 指出在前置處理過程中，會自動執行[資料護欄和特徵化步驟](how-to-configure-auto-features.md#featurization)。 **預設設定**|
|`"featurization": 'off'`| 指出不應自動執行特徵化步驟。|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 指出應使用自訂的特徵化步驟。 [了解如何自訂特徵化](how-to-configure-auto-features.md#customize-featurization)。|

> [!NOTE]
> 自動化機器學習特徵化步驟 (功能標準化、處理遺漏的資料、將文字轉換為數值等等) 會成為基礎模型的一部分。 使用模型進行預測時，定型期間所套用的相同特徵化步驟會自動套用至您的輸入資料。

### <a name="time-series-forecasting"></a>時間序列預測
時間序列 `forecasting` 工作需要在設定物件中使用其他參數：

1. `time_column_name`:此為必要參數，會定義您的定型資料中包含有效時間序列的資料行名稱。
1. `max_horizon`:定義您想要根據定型資料的週期性預測的時間長度。 例如，如果您有每日時間粒紋的定型資料，則應定義要定型模型的天數。
1. `grain_column_names`:定義您的定型資料中包含個別時間序列資料的資料行名稱。 例如，如果您要依商店預測特定品牌的銷售量，則應將商店和品牌資料行定義為粒紋資料行。 系統會針對每個粒紋/群組建立個別的時間序列和預測。 

如需以下使用之設定的範例，請參閱[範例筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)。

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
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

### <a name="ensemble-configuration"></a><a name="ensemble"></a> 集團設定

依預設會啟用集團模型，並在自動化機器學習執行中顯示為最後一回合的反覆運算。 目前支援的集團方法為投票和堆疊。 投票會使用加權平均值以軟投票的形式實作，而堆疊則是使用兩層實作來執行，其中，第一層模型與投票集團相同，第二層模型則用來尋找第一層中的最佳模型組合。 如果您使用 ONNX 模型，**或**已啟用模型說明能力，則會停用堆疊，而僅使用投票。

有多個預設引數可提供作為 `AutoMLConfig` 物件中的 `kwargs`，以改變預設集團行為。

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

依預設會啟用集團定型，但可以使用 `enable_voting_ensemble` 和 `enable_stack_ensemble` 布林值參數加以停用。

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

### <a name="exit-criteria"></a><a name="exit"></a> 允出準則

您可以定義幾個選項來結束實驗。
1. 無準則：若未定義任何結束參數，實驗將會繼續進行，直到主要計量沒有進一步的進度為止。
1. 在一段時間後結束：在設定中使用 `experiment_timeout_minutes`，可讓您定義實驗繼續執行的時間長度 (以分鐘為單位)。
1. 在到達分數後結束：使用 `experiment_exit_score` 將會在到達主要計量分數後完成實驗。

### <a name="explore-model-metrics"></a>探索模型計量

如果您使用筆記本，您可以在小工具中或以內嵌方式檢視定型結果。 請參閱[追蹤和評估模型](how-to-track-experiments.md#view-run-details)以取得更多詳細資料。

如需有關如何下載或註冊模型以部署至 web 服務的詳細資訊，請參閱[如何和部署模型的位置](how-to-deploy-and-where.md)。

## <a name="understand-automated-ml-models"></a>了解自動化 ML 模型

任何使用自動化 ML 產生的模型都包含下列步驟：
+ 自動化特徵工程 (如果 `"featurization": 'auto'`)
+ 使用超參數值的調整/標準化和演算法

我們會以透明的方式從自動化 ML 的 fitted_model 輸出中取得這項資訊。

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>自動化特徵工程

請參閱在 `"featurization": 'auto'` 時發生的前置處理和[自動化特徵工程]()的清單。

請思考此範例：
+ 輸入特徵共有四種：A (數值)、B (數值)、C (數值)、D (日期時間)
+ 數值特徵 C 是具有所有唯一值的識別碼資料行，因此遭到卸除
+ 數值特徵 A 和 B 具有遺漏值，因此會以平均值插補
+ 日期時間特徵 D 特徵化為 11 個不同的工程特徵

請在適合模型的第一個步驟中使用以下 2 個 API，以深入了解。  請參閱[此範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)。

+ API 1：`get_engineered_feature_names()` 會傳回工程特徵名稱的清單。

  Usage :
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  此清單包含所有工程特徵名稱。

  >[!Note]
  >對於「預測」工作，請使用 'timeseriestransformer'，對於「迴歸」或「分類」工作則使用 'datatransformer'。

+ API 2：`get_featurization_summary()` 會傳回所有輸入特徵的特徵化摘要。

  Usage :
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >對於「預測」工作，請使用 'timeseriestransformer'，對於「迴歸」或「分類」工作則使用 'datatransformer'。

  輸出：
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   其中：

   |輸出|定義|
   |----|--------|
   |RawFeatureName|提供的資料集中的輸入特徵/資料行名稱。|
   |TypeDetected|偵測到輸入特徵的資料類型。|
   |Dropped|指出是已卸除還是使用了輸入特徵。|
   |EngineeringFeatureCount|透過自動化特徵工程轉換而產生的特徵數目。|
   |轉換|套用至輸入特徵以產生工程特徵的轉換清單。|
### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>使用超參數值的調整/標準化和演算法：

若要了解管線的調整/標準化和演算法/超參數值，請使用 fitted_model.steps。 [深入了解調整/標準化](how-to-configure-auto-features.md)。 以下是範例輸出：

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

若要取得更多詳細資料，請使用此協助程式函式： 

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(model)
```

下列範例輸出適用於使用特定演算法的管線 (在此案例中為使用 RobustScalar 的 LogisticRegression)。

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>預測類別機率

使用自動化 ML 產生的模型都有包裝函式物件，會從其開放原始碼原始類別中鏡像處理功能。 自動化 ML 傳回的分類模型包裝函式物件大多會執行 `predict_proba()` 函式，而接受特徵 (X 值) 的類陣列或疏鬆矩陣資料範例，並傳回每個範例及其個別類別機率的 N 維陣列。

假設您已使用前述的相同呼叫來擷取最佳執行和適合的模型，您可以直接從適當的模型呼叫 `predict_proba()`，並根據模型類型，以適當的格式提供 `X_test` 範例。

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

如果基礎模型不支援 `predict_proba()` 函式，或格式不正確，則會擲回模型類別特定的例外狀況。 如需如何對不同的模型類型實作此函式的範例，請參閱 [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) 和 [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) 參考文件。

<a name="explain"></a>

## <a name="model-interpretability"></a>模型可解釋性

模型可解釋性可讓您了解模型進行預測的原因，以及基礎特徵重要性的值。 SDK 包含多種套件，可讓您在定型和推斷期間，對本機和已部署的模型啟用模型可解釋性特徵。

若要了解如何在自動化機器學習實驗中明確啟用可解釋性特徵，請參閱[操作說明](how-to-machine-learning-interpretability-automl.md)中的程式碼範例。

如需如何在自動化機器學習以外的其他 SDK 區域中啟用模型說明和特徵重要性的一般資訊，請參閱[概念](how-to-machine-learning-interpretability.md)一文中有關於可解釋性的部分。

> [!NOTE]
> 說明用戶端目前不支援 ForecastTCN 模型。 如果將 [說明] 儀表板當做最佳模型傳回，且不支援隨選說明執行，此模型將不會傳回。

## <a name="next-steps"></a>後續步驟

+ 深入了解[模型部署的方式和位置](how-to-deploy-and-where.md)。

+ 深入了解[如何使用自動化機器學習定型迴歸模型](tutorial-auto-train-models.md)或[如何使用自動化機器學習以遠端資源定型](how-to-auto-train-remote.md)。
+ 了解如何在[多種模型解決方案加速器](https://aka.ms/many-models)中使用 autoML 定型多個模型。
