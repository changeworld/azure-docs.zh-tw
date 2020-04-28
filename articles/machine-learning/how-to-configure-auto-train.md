---
title: 建立自動化機器學習實驗
titleSuffix: Azure Machine Learning
description: 自動化機器學習服務可為您挑選演算法，並產生馬上可進行部署的模型。 了解可用來設定自動化機器學習實驗的選項。
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 7b268f3efa64c2f1ae78794056fdfda2b27434e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186924"
---
# <a name="configure-automated-ml-experiments-in-python"></a>在 Python 中設定自動化 ML 實驗
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本指南中，您將瞭解如何使用[AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)來定義自動化機器學習實驗的各種設定。 自動化機器學習服務會為您挑選演算法和超參數，並產生馬上可進行部署的模型。 有數個選項可用來設定自動化機器學習實驗。

若要查看自動化機器學習實驗的範例，請參閱[教學課程：使用自動化機器學習訓練分類模型](tutorial-auto-train-models.md)或[使用雲端中的自動化機器學習來定型模型](how-to-auto-train-remote.md)。

自動化機器學習服務中的可用設定選項：

* 選取您的實驗類型： [分類]、[回歸] 或 [時間序列預測]
* 資料來源、格式和擷取資料
* 選擇您的計算目標：本機或遠端
* 自動化機器學習實驗設定
* 執行自動化機器學習實驗
* 探索模型計量
* 註冊和部署模型

如果您不想使用程式碼經驗，您也可以[在 Azure Machine Learning studio 中建立自動化的機器學習實驗](how-to-use-automated-ml-for-ml-models.md)。

## <a name="select-your-experiment-type"></a>選取您的實驗類型

在開始實驗之前，您應先決定所要解決的機器學習問題類型。 自動化機器學習服務支援分類、回歸和預測等工作類型。 深入瞭解工作[類型](how-to-define-task-type.md)。

在自動化和調整程序期間，自動化機器學習支援下列演算法。 身為使用者，您不需要指定演算法。

> [!NOTE]
> 如果您打算將自動 ML 建立的模型匯出至[ONNX 模型](concept-onnx.md)，則只有以 * 表示的演算法可以轉換成 ONNX 格式。 深入瞭解如何[將模型轉換成 ONNX](concept-automated-ml.md#use-with-onnx)。 <br> <br> 另請注意，ONNX 目前僅支援分類和回歸工作。 

分類 | 迴歸 | 時間序列預測
|-- |-- |--
[羅吉斯回歸](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [彈性網路](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [彈性網路](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[梯度提升](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[梯度提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[漸層停駐提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[決策樹](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[決策樹](https://scikit-learn.org/stable/modules/tree.html#regression)* |[決策樹](https://scikit-learn.org/stable/modules/tree.html#regression)
[K 最近的鄰近專案](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K 最近的鄰近專案](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K 最近鄰演算法](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[線性 SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS 套索](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[支援向量分類（SVC）](https://scikit-learn.org/stable/modules/svm.html#classification)* |[隨機梯度下降（SGD）](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[極隨機的樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[極隨機的樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 分類器](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier) |[DNN 回歸輸入變數](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN 回歸輸入變數](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN 線性分類器](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[線性回歸輸入變數](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor) |[線性回歸輸入變數](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[貝氏貝氏機率分類](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[快速線性回歸輸入變數](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[自動 ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[隨機梯度下降（SGD）](https://scikit-learn.org/stable/modules/sgd.html#sgd)* |[線上梯度下降回歸輸入變數](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
|[平均認知分類器](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ForecastTCN
|[線性 SVM 分類器](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)* ||

請在`task`函式中`AutoMLConfig`使用參數來指定您的實驗類型。

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>資料來源和格式

自動化機器學習支援位於本機桌面或雲端中 (例如 Azure Blob 儲存體) 所包含的資料。 資料可以讀入**Pandas 資料框架**或**Azure Machine Learning 的 TabularDataset**中。  [深入瞭解資料集](how-to-create-register-datasets.md)。

定型資料的需求：
- 資料必須是表格式格式。
- 要預測的值（目標資料行）必須在資料中。

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

針對遠端執行，必須可從遠端計算存取訓練資料。 SDK 中[`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)的類別會公開功能給：

* 輕鬆地將靜態檔案或 URL 來源中的資料傳輸至您的工作區
* 在雲端計算資源上執行時，將您的資料提供給訓練腳本

如需使用`Dataset`類別將資料掛接至計算目標的範例，請參閱 [how to](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target)。

## <a name="train-and-validation-data"></a>訓練和驗證資料

您可以直接在此函式中指定個別的`AutoMLConfig`定型和驗證集。

### <a name="k-folds-cross-validation"></a>k 疊交叉驗證

使用 `n_cross_validations` 設定來指定交叉驗證的數目。 訓練資料集會隨機分割為相同大小的 `n_cross_validations` 疊。 在每個交叉驗證回合中，將使用其中一個疊來驗證以其餘幾疊進行訓練的模型。 此程序會重複執行 `n_cross_validations` 回合，直到每一疊都已作為驗證集執行一次。 將會報告 `n_cross_validations` 個回合的總平均分數，並以整個訓練資料集重新訓練對應的模型。

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo 交叉驗證（重複的隨機子取樣）

使用 `validation_size` 指定應該用於驗證的訓練資料集百分比，並使用 `n_cross_validations` 指定交叉驗證的數目。 在每個交叉驗證回合中，將隨機選取 `validation_size` 大小的子集來驗證以其餘資料進行訓練的模型。 最後將會報告 `n_cross_validations` 個回合的總平均分數，並以整個訓練資料集重新訓練對應的模型。 時間序列預測不支援 Monte Carlo。

### <a name="custom-validation-dataset"></a>自訂驗證資料集

如果不接受隨機分割，通常是時間序列資料或不平衡資料，請使用自訂驗證資料集。 您可以指定自己的驗證資料集。 系統會根據指定的驗證資料集來評估模型，而不是隨機的資料集。

## <a name="compute-to-run-experiment"></a>要執行實驗的計算

接下來，請決定要在何處訓練模型。 自動化機器學習訓練實驗可在下列計算選項上執行：
* 您的本機電腦，例如本機桌上型電腦或膝上型電腦 – 通常適用於資料集並不大，且您尚在探索階段時。
* 雲端中的遠端機器 – [Azure Machine Learning 受控計算](concept-compute-target.md#amlcompute)是一項受控服務，能夠在 Azure 虛擬機器叢集上定型機器學習模型。

  如需具有本機和遠端計算目標的筆記本範例，請參閱此[GitHub 網站](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)。

* Azure 訂用帳戶中的 Azure Databricks 叢集。 您可以在這裡找到更多詳細資料-[為自動化 ML 設定 Azure Databricks](how-to-configure-environment.md#azure-databricks)叢集

  如需具有 Azure Databricks 的筆記本範例，請參閱此[GitHub 網站](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl)。

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>設定您的實驗設定

有數個選項可用來設定自動化機器學習實驗。 將 `AutoMLConfig` 物件具現化即可設定這些參數。 如需完整的參數清單，請參閱 [AutoMLConfig 類別](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)。

部分範例包括：

1. 使用 AUC 加權作為主要計量的分類實驗，其實驗超時分鐘數設定為30分鐘，2個交叉驗證折迭。

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
2. 以下是回歸實驗的範例，其設定為在60分鐘後結束，並具有五個驗證交叉折迭。

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

三個不同`task`的參數值（第三個工作類型`forecasting`為，並使用類似的演算法集`regression`區做為工作）決定要套用的模型清單。 使用`whitelist`或`blacklist`參數來進一步修改反復專案，以及要包含或排除的可用模型。 支援的模型清單可以在的[SupportedModels 類別](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels)（[分類](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification)、[預測](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)和[回歸](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)）中找到。

為了協助避免實驗超時失敗，自動化 ML 的驗證服務將需要`experiment_timeout_minutes`設定為至少15分鐘，或60分鐘（如果資料列大小超過10000000）。

### <a name="primary-metric"></a>主要計量
主要度量會決定要在模型定型期間使用的計量，以進行優化。 您可以選取的可用計量取決於您選擇的工作類型，下表顯示每種工作類型的有效主要度量。

|分類 | 迴歸 | 時間序列預測
|-- |-- |--
|精確度| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

瞭解[自動化機器學習結果](how-to-understand-automated-ml.md)中這些計量的特定定義。

### <a name="data-featurization"></a>資料特徵化

在每個自動化的機器學習實驗中，您的資料都會[自動調整並正規化](concept-automated-ml.md#preprocess)，以協助*特定*的演算法，而這些演算法會受到不同規模的功能所影響。  不過，您也可以啟用其他特徵化，例如遺漏值插補、編碼和轉換。 [深入瞭解包含的特徵化](how-to-use-automated-ml-for-ml-models.md#featurization)。

在設定您的實驗時，您可以啟用 [ `featurization`advanced] 設定。 下表顯示[AutoMLConfig 類別](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)中特徵化的已接受設定。

|特徵化設定 | 描述 |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 表示應該使用自訂的特徵化步驟。 [瞭解如何自訂特徵化](how-to-configure-auto-train.md#customize-feature-engineering)。|
|`"featurization": 'off'`| 表示不應自動執行特徵化步驟。|
|`"featurization": 'auto'`| 指出在前置處理過程中，會自動執行[資料護欄和特徵化步驟](how-to-use-automated-ml-for-ml-models.md#advanced-featurization-options)。|

> [!NOTE]
> 自動化機器學習特徵化步驟（功能正規化、處理遺漏的資料、將文字轉換成數值等等）會成為基礎模型的一部分。 使用模型進行預測時，在定型期間所套用的相同特徵化步驟會自動套用至您的輸入資料。

### <a name="time-series-forecasting"></a>時間序列預測
時間序列`forecasting`工作需要 configuration 物件中的其他參數：

1. `time_column_name`：必要參數，定義包含有效時間序列之定型資料中的資料行名稱。
1. `max_horizon`：根據定型資料的週期，定義您想要預測的時間長度。 例如，如果您有每日時間粒紋的定型資料，您就會定義要將模型定型的天數。
1. `grain_column_names`：定義資料行的名稱，其中包含定型資料中的個別時間序列資料。 例如，如果您是依商店預測特定品牌的銷售量，您會將商店和品牌資料行定義為您的資料行。 系統會針對每個細微性/群組建立個別的時間序列和預測。 

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

### <a name="ensemble-configuration"></a><a name="ensemble"></a>集團設定

預設會啟用集團模型，並在自動化機器學習執行中顯示為最後的執行反復專案。 目前支援的集團方法為投票和堆疊。 投票會使用加權的平均值實作為軟投票，而堆疊的執行則是使用兩層的執行，其中第一層的模型與投票集團相同，而第二層模型則是用來尋找第一層中的最佳模型組合。 如果您使用 ONNX 模型，**或**已啟用模型可解釋性，將會停用堆疊，而且只會使用投票。

您可以`kwargs`在`AutoMLConfig`物件中提供多個預設引數，以改變預設堆疊集團行為。

* `stack_meta_learner_type`：中繼學習模組是在個別異類模型的輸出上定型的模型。 預設的中繼學習工具`LogisticRegression`適用于分類工作（ `LogisticRegressionCV`或者，如果已啟用交叉驗證） `ElasticNet`和回歸/預測工作（ `ElasticNetCV`如果已啟用交叉驗證）。 這個參數可以是下列其中一個`LogisticRegression`字串：、 `LogisticRegressionCV`、 `LightGBMClassifier`、 `ElasticNet`、 `ElasticNetCV` `LightGBMRegressor`、或。 `LinearRegression`
* `stack_meta_learner_train_percentage`：指定要保留以定型學習模組的定型集比例（選擇定型和驗證類型時）。 預設值為 `0.2`。
* `stack_meta_learner_kwargs`：要傳遞至中繼學習模組之初始化運算式的選擇性參數。 這些參數和參數型別會從對應的模型的「參數」鏡像參數和參數型別，並將它們轉送到模型的「函式」。

下列程式碼顯示在`AutoMLConfig`物件中指定自訂集團行為的範例。

```python
ensemble_settings = {
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

集團訓練預設為啟用，但可以使用`enable_voting_ensemble`和`enable_stack_ensemble`布林值參數來停用。

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

針對自動化 ML，您會建立`Experiment`物件，這是中`Workspace`用來執行實驗的已命名物件。

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

### <a name="exit-criteria"></a><a name="exit"></a>結束準則

您可以定義幾個選項來結束實驗。
1. 無準則：如果您未定義任何結束參數，實驗會繼續進行，直到您的主要計量沒有進一步的進度為止。
1. 在一段時間後結束：在`experiment_timeout_minutes`您的設定中使用，可讓您定義實驗繼續執行的時間長度（以分鐘為單位）。
1. 達到分數後結束：使用`experiment_exit_score`會在達到主要度量分數後完成實驗。

### <a name="explore-model-metrics"></a>探索模型計量

如果您在筆記本中，您可以在 widget 或內嵌中查看定型結果。 請參閱[追蹤和評估模型](how-to-track-experiments.md#view-run-details)以取得更多詳細資料。

## <a name="understand-automated-ml-models"></a>瞭解自動化 ML 模型

任何使用自動化 ML 所產生的模型都包含下列步驟：
+ 自動化功能工程（if `"featurization": 'auto'`）
+ 具有超參數值的縮放/正規化和演算法

我們會將其透明化，以從自動化 ML 的 fitted_model 輸出取得此資訊。

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>自動化功能工程

查看發生的前置處理和[自動化功能工程](concept-automated-ml.md#preprocess)的清單`"featurization": 'auto'`。

請思考此範例：
+ 有四種輸入功能： A （數值）、B （數值）、C （數值）、D （DateTime）
+ 數值特徵 C 已卸載，因為它是具有所有唯一值的識別碼資料行
+ 數值特徵 A 和 B 具有遺漏值，因此會插補平均值
+ DateTime 功能 D 特徵化為11個不同的工程功能

在適合模型的第一個步驟中使用這2個 Api，以深入瞭解。  請參閱[此範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)。

+ API 1： `get_engineered_feature_names()`傳回工程化功能名稱的清單。

  Usage :
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  此清單包含所有已設計的功能名稱。

  >[!Note]
  >請為工作 = ' 預測 ' 使用 ' timeseriestransformer '，否則請使用 ' datatransformer ' 進行「回歸」或「分類」工作。

+ API 2： `get_featurization_summary()`傳回所有輸入功能的特徵化摘要。

  Usage :
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >請為工作 = ' 預測 ' 使用 ' timeseriestransformer '，否則請使用 ' datatransformer ' 進行「回歸」或「分類」工作。

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
   |RawFeatureName|提供的輸入功能/資料行名稱。|
   |TypeDetected|偵測到輸入功能的資料類型。|
   |卸除|指出輸入功能是否已卸載或使用。|
   |EngineeringFeatureCount|透過自動化功能工程轉換所產生的功能數目。|
   |轉換|套用至輸入功能以產生工程功能的轉換清單。|
   
### <a name="customize-feature-engineering"></a>自訂功能工程
若要自訂功能工程 `"featurization": FeaturizationConfig`，請指定。

支援的自訂包括：

|自訂|定義|
|--|--|
|資料行目的更新|覆寫指定之資料行的功能類型。|
|轉換器參數更新 |更新指定之轉換器的參數。 目前支援 Imputer （mean、最常 & 中位數）和 HashOneHotEncoder。|
|卸載資料行 |要從特徵化中捨棄的資料行。|
|區塊轉換器| 封鎖要在特徵化進程上使用的轉換器。|

使用 API 呼叫來建立 FeaturizationConfig 物件：
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>具有超參數值的縮放/正規化和演算法：

若要瞭解管線的縮放/正規化和演算法/超參數值，請使用 fitted_model。 [深入瞭解調整/](concept-automated-ml.md#preprocess)正規化。 以下是範例輸出：

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

若要取得更多詳細資料，請使用此 helper 函式： 

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

下列範例輸出適用于使用特定演算法的管線（在此案例中為 LogisticRegression with RobustScalar）。

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

使用自動化 ML 所產生的模型都有包裝函式物件，可從其開放原始碼原始類別中鏡像功能。 自動化 ML 所傳回的大部分分類模型包裝函式`predict_proba()`物件都會執行函式，以接受您的功能（X 值）類似陣列或 sparse 的矩陣資料範例，並傳回每個樣本的一維陣列及其各自的類別機率。

假設您已使用上述相同的呼叫來抓取最佳的執行和調整模型，您可以直接`predict_proba()`從適當的模型呼叫，並根據`X_test`模型類型，提供適當格式的範例。

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

如果基礎模型不支援`predict_proba()`函數，或格式不正確，則會擲回模型類別特定的例外狀況。 如需如何針對不同模型類型來執行此函式的範例，請參閱[RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba)和[XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html)參考檔。

<a name="explain"></a>

## <a name="model-interpretability"></a>模型可解釋性

[模型 interpretability] 可讓您瞭解模型進行預測的原因，以及基礎功能重要性值。 SDK 包含各種封裝，可讓您在定型和推斷階段，針對本機和已部署的模型啟用模型 interpretability 功能。

如需如何在自動化機器學習實驗中明確啟用 interpretability 功能的程式碼範例，請參閱操作[說明](how-to-machine-learning-interpretability-automl.md)。

如需如何在自動化機器學習以外的 SDK 其他區域中啟用模型說明和功能重要性的一般資訊，請參閱 interpretability 上的[概念](how-to-machine-learning-interpretability.md)文章。

## <a name="next-steps"></a>後續步驟

深入了解[模型部署的方式和位置](how-to-deploy-and-where.md)。

深入瞭解[如何使用自動化機器學習來定型回歸模型](tutorial-auto-train-models.md)，或[如何在遠端資源上使用自動化機器學習來定型](how-to-auto-train-remote.md)。
