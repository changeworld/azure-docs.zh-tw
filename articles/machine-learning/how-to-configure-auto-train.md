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
ms.openlocfilehash: 18de50473e3dd6ca8ddda9575a247e00530032e8
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115408"
---
# <a name="configure-automated-ml-experiments-in-python"></a>在 Python 中設定自動化 ML 實驗
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本指南中,瞭解如何使用 Azure 機器學習[SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)定義自動機器學習實驗的各種配置設置。 自動化機器學習服務會為您挑選演算法和超參數，並產生馬上可進行部署的模型。 有數個選項可用來設定自動化機器學習實驗。

要查看自動機器學習實驗的範例,請參閱[教學:使用自動機器學習訓練分類模型](tutorial-auto-train-models.md),或在[雲端中使用自動機器學習培訓模型](how-to-auto-train-remote.md)。

自動化機器學習服務中的可用設定選項：

* 選擇您的實驗類型:分類、回歸或時間序列預測
* 資料來源、格式和擷取資料
* 選擇您的計算目標：本機或遠端
* 自動化機器學習實驗設定
* 執行自動化機器學習實驗
* 探索模型計量
* 註冊和部署模型

如果您更喜歡沒有代碼體驗,還可以在 Azure[機器學習工作室中建立自動機器學習實驗](how-to-use-automated-ml-for-ml-models.md)。

## <a name="select-your-experiment-type"></a>選取您的實驗類型

在開始實驗之前，您應先決定所要解決的機器學習問題類型。 自動機器學習支援分類、回歸和預測的任務類型。 瞭解有關[任務類型](how-to-define-task-type.md)的更多資訊。

在自動化和調整程序期間，自動化機器學習支援下列演算法。 身為使用者，您不需要指定演算法。

> [!NOTE]
> 如果您計劃將自動 ML 創建的模型匯出到[ONNX 模型](concept-onnx.md),則只有使用 * 指示的演演演算法才能轉換為 ONNX 格式。 瞭解有關[將模型轉換為 ONNX](concept-automated-ml.md#use-with-onnx)的更多資訊。 <br> <br> 另請注意,ONNX 目前僅支援分類和回歸任務。 

分類 | 迴歸 | 時間序列預測
|-- |-- |--
[邏輯回歸](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [彈性網](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [彈性網路](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[輕型 GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[輕型 GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[漸層提升](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[漸層提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[漸層停駐提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[決策樹](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[決策樹](https://scikit-learn.org/stable/modules/tree.html#regression)* |[決策樹](https://scikit-learn.org/stable/modules/tree.html#regression)
[K 最近鄰](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K 最近鄰](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K 最近鄰演算法](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[線性 SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[拉爾斯·拉索](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[支援向量類別 (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[隨機林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[隨機林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[隨機林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[極隨機的樹](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[極隨機的樹](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 類別器](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier) |[DNN 回歸器](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN 回歸器](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN 線性類別器](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[線性迴歸器](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor) |[線性迴歸器](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[天真的貝葉斯](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[快速線性迴充器](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[自動 ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* |[線上漸層下降迴歸器](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[先知](https://facebook.github.io/prophet/docs/quick_start.html)
|[平均感知器分類器](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||預測TCN
|[線性 SVM 分類器](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)* ||

使用建構`task`函數`AutoMLConfig`中的 參數指定實驗類型。

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>資料來源和格式

自動化機器學習支援位於本機桌面或雲端中 (例如 Azure Blob 儲存體) 所包含的資料。 數據可以讀取到**貓熊資料框架**或**Azure 機器學習表格資料集**中。  [瞭解有關數據集的更多。](how-to-create-register-datasets.md)

訓練資料要求:
- 數據必須以表格形式顯示。
- 要預測的值(目標列)必須位於數據中。

以下代碼示例演示如何以這些格式存儲數據。

* 表格資料集

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

對於遠端執行,必須從遠端計算訪問訓練數據。 SDK[`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)中的類別將功能公開給:

* 輕鬆將資料從靜態檔案或網址來源到工作區
* 在雲端運算資源上運行時,將數據提供給訓練腳本

如需使用`Dataset`類別將資料掛接至計算目標的範例，請參閱 [how to](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target)。

## <a name="train-and-validation-data"></a>訓練和驗證資料

可以直接在`AutoMLConfig`建構函數中指定單獨的訓練與驗證集。

### <a name="k-folds-cross-validation"></a>k 疊交叉驗證

使用 `n_cross_validations` 設定來指定交叉驗證的數目。 訓練資料集會隨機分割為相同大小的 `n_cross_validations` 疊。 在每個交叉驗證回合中，將使用其中一個疊來驗證以其餘幾疊進行訓練的模型。 此程序會重複執行 `n_cross_validations` 回合，直到每一疊都已作為驗證集執行一次。 將會報告 `n_cross_validations` 個回合的總平均分數，並以整個訓練資料集重新訓練對應的模型。

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>蒙特卡羅交叉驗證(重複隨機子取樣)

使用 `validation_size` 指定應該用於驗證的訓練資料集百分比，並使用 `n_cross_validations` 指定交叉驗證的數目。 在每個交叉驗證回合中，將隨機選取 `validation_size` 大小的子集來驗證以其餘資料進行訓練的模型。 最後將會報告 `n_cross_validations` 個回合的總平均分數，並以整個訓練資料集重新訓練對應的模型。 蒙特卡羅不支援時間序列預測。

### <a name="custom-validation-dataset"></a>自訂驗證資料集

如果不接受隨機拆分(通常是時間序列數據或不平衡數據),請使用自定義驗證數據集。 您可以指定自己的驗證資料集。 系統會根據指定的驗證資料集來評估模型，而不是隨機的資料集。

## <a name="compute-to-run-experiment"></a>要執行實驗的計算

接下來，請決定要在何處訓練模型。 自動化機器學習訓練實驗可在下列計算選項上執行：
* 您的本機電腦，例如本機桌上型電腦或膝上型電腦 – 通常適用於資料集並不大，且您尚在探索階段時。
* 雲端中的遠端機器 – [Azure Machine Learning 受控計算](concept-compute-target.md#amlcompute)是一項受控服務，能夠在 Azure 虛擬機器叢集上定型機器學習模型。

  有關具有本地和遠端計算目標的筆記本的範例,請參閱此[GitHub 網站](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)。

* Azure 訂閱中的 Azure 數據磚塊群集。 您可以找到更多詳細資訊 ─[為自動 ML 設定 Azure 資料塊叢集](how-to-configure-environment.md#azure-databricks)

  有關具有 Azure 資料磚塊的筆記本的範例,請參閱此[GitHub 網站](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl)。

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>設定您的實驗設定

有數個選項可用來設定自動化機器學習實驗。 將 `AutoMLConfig` 物件具現化即可設定這些參數。 如需完整的參數清單，請參閱 [AutoMLConfig 類別](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)。

部分範例包括：

1. 使用 AUC 加權作為主要指標的分類實驗,實驗超時分鐘設置為 30 分鐘,2 個交叉驗證摺疊。

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
2. 下面是一個回歸實驗示例,該實驗設置為在 60 分鐘后結束,有五個驗證交叉摺疊。

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

三個不同的`task`參數值(第三個任務類型`forecasting`是 ,並使用類似的演`regression`演演算法池作為 任務)確定要應用的模型清單。 使用`whitelist``blacklist`或 參數進一步修改具有要包含或排除的可用模型的反覆運算。 支援模型的清單可以在[支援模型類](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels)([分類](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification)、[預測](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)和[回歸](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression))中找到。

為了説明避免實驗超時失敗,自動 ML 的驗證服務`experiment_timeout_minutes`將要求 將至少 15 分鐘設置為 60 分鐘,如果按列大小按行大小超過 1000 萬次,則需要設置為 60 分鐘。

### <a name="primary-metric"></a>主要計量
主指標確定在模型培訓期間要使用的優化指標。 可以選擇的可用指標由您選擇的任務類型確定,下表顯示每個任務類型的有效主指標。

|分類 | 迴歸 | 時間序列預測
|-- |-- |--
|精確度| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

瞭解這些指標的具體定義,[瞭解自動化機器學習結果](how-to-understand-automated-ml.md)。

### <a name="data-featurization"></a>數據壯舉

在每個自動機器學習實驗中,您的數據[都會自動縮放和規範化](concept-automated-ml.md#preprocess),以説明*某些*對不同比例的要素敏感的演演演算法。  但是,您還可以啟用其他任務化,例如缺少值歸因、編碼和轉換。 [詳細瞭解包含哪些壯舉](how-to-use-automated-ml-for-ml-models.md#featurization)。

設定實驗時,可以啟用進階`featurization`設定 。 下表顯示了[`AutoMLConfig`類](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)中用於執行的已接受設置。

|技術化設定 | 描述 |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 指示應使用自定義的壯舉步驟。 [瞭解如何自定義壯舉](how-to-configure-auto-train.md#customize-feature-engineering)。|
|`"featurization": 'off'`| 指示不應自動執行壯舉步驟。|
|`"featurization": 'auto'`| 指示做為預處理的一部份,[會自動執行資料護欄和壯舉步驟](how-to-use-automated-ml-for-ml-models.md#advanced-featurization-options)。|

> [!NOTE]
> 自動化機器學習壯舉步驟(功能規範化、處理缺失數據、將文本轉換為數位等)成為基礎模型的一部分。 使用模型進行預測時,訓練期間應用的相同壯舉步驟將自動應用於輸入數據。

### <a name="time-series-forecasting"></a>時間序列預測
時間序列`forecasting`工作需要在設定物件中執行其他參數:

1. `time_column_name`:在訓練數據中定義包含有效時間序列的列名稱的必需參數。
1. `max_horizon`:根據訓練數據的週期定義要預測的時間長度。 例如,如果您有具有每日時粒的訓練數據,則可以定義您希望模型訓練天數的遠數。
1. `grain_column_names`:定義在訓練數據中包含單個時間序列數據的列的名稱。 例如,如果要按商店預測特定品牌的銷售,則可以將商店和品牌列定義為粒標。 將為每個顆粒/分組創建單獨的時間序列和預測。 

有關下面使用的設定的範例,請參閱[範例筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)。

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

### <a name="ensemble-configuration"></a><a name="ensemble"></a>組合設定

默認情況下啟用組合模型,並在自動機器學習運行中顯示為最終運行反覆運算。 當前支援的合奏方法是投票和堆疊。 投票使用加權平均線實現為軟投票,堆疊實現使用兩層實現,其中第一層具有與投票組相同的模型,第二層模型用於從第一層找到模型的最佳組合。 如果您使用的是 ONNX 模型,**或者**啟用了模型解釋性,則將禁用堆疊,並且僅使用投票。

可以像在物件中一`kwargs``AutoMLConfig`樣提供多個默認參數來更改預設堆疊集合行為。

* `stack_meta_learner_type`:元學習者是訓練在單個異構模型輸出上的模型。 默認元學員`LogisticRegression`用於分類任務(`LogisticRegressionCV`或 啟用了交叉驗證`ElasticNet`)和回歸/預測任務(`ElasticNetCV`或者如果 啟用了交叉驗證)。 這裡可以是`LogisticRegression`以下字串之`LogisticRegressionCV`一: 、 、 `LightGBMClassifier` `ElasticNet` `ElasticNetCV` `LightGBMRegressor`、 `LinearRegression`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、
* `stack_meta_learner_train_percentage`:指定為培訓元學員保留的訓練集的比例(選擇訓練和驗證培訓類型時)。 預設值為 `0.2`。
* `stack_meta_learner_kwargs`:可選參數,以傳遞給元學習者的初始化器。 這些參數和參數類型反映相應模型建構函數的參數和參數類型,並轉發給模型構造函數。

以下代碼顯示了在物件中指定自訂集合行為的範例`AutoMLConfig`。

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

默認情況下啟用組合訓練,但可以使用`enable_voting_ensemble``enable_stack_ensemble`和布爾參數禁用它。

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

對於自動 ML,您將創建`Experiment`一個物件,該物件是用於運行`Workspace`實驗 的命名物件。

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

### <a name="exit-criteria"></a>離開準則
您可以定義幾個選項以結束實驗。
1. 無條件:如果不定義任何退出參數,實驗將繼續,直到主指標沒有進一步進展。
1. 一段時間後退出:在`experiment_timeout_minutes`設置中使用允許您定義實驗在運行中繼續運行時在幾分鐘內的時間。
1. 達到分數後退出:使用`experiment_exit_score`將在達到主要指標分數後完成實驗。

### <a name="explore-model-metrics"></a>探索模型計量

如果您在筆記本中,則可以在小部件中或內聯查看培訓結果。 請參閱[追蹤和評估模型](how-to-track-experiments.md#view-run-details)以取得更多詳細資料。

## <a name="understand-automated-ml-models"></a>瞭解自動 ML 型號

使用自動 ML 生產的任何型號包括以下步驟:
+ 自動化功能專案 (`"featurization": 'auto'`如果 )
+ 具有超參數值的縮放/規範化和演演算法

我們透明地從自動 ML 的fitted_model輸出中獲取此資訊。

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>自動化功能工程

請參考預先處理與[自動功能專案](concept-automated-ml.md#preprocess)的清單,這些專案發生在`"featurization": 'auto'`時 。

請思考此範例：
+ 有四個輸入功能:A(數位)、B(數位)、C(數位)、D(日期時間)
+ 數位要素 C 被丟棄,因為它是具有所有唯一值的 ID 列
+ 數值特徵 A 和 B 缺少值,因此按平均值進行歸因
+ DateTime 功能 D 被加工成 11 種不同的專案功能

在擬合模型的第一步使用這兩個 API 來瞭解更多。  請參考[此範例的筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)。

+ API 1:`get_engineered_feature_names()`傳回專案特徵名稱的清單。

  Usage :
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  此清單包括所有工程特徵名稱。

  >[!Note]
  >使用「時間序列轉換器」進行任務_"預測",否則對"回歸"或"分類"任務使用"資料轉換器"。

+ API 2:`get_featurization_summary()`傳回所有輸入功能的壯舉摘要。

  Usage :
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >使用「時間序列轉換器」進行任務_"預測",否則對"回歸"或"分類"任務使用"資料轉換器"。

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
   |RawFeatureName|從提供的數據集輸入要素/列名稱。|
   |TypeDetected|檢測到輸入功能的數據類型。|
   |卸除|指示輸入要素是已丟棄還是已使用。|
   |EngineeringFeatureCount|通過自動功能工程轉換生成的要素數。|
   |轉換|應用於輸入要素以生成工程特徵的轉換清單。|
   
### <a name="customize-feature-engineering"></a>自訂功能專案
要自訂要素專案,請 `"featurization": FeaturizationConfig`指定 。

支援的自訂包括:

|自訂|定義|
|--|--|
|欄用途更新|覆蓋指定列的功能類型。|
|變壓器參數更新 |更新指定變壓器的參數。 目前支援 Putputer(平均值、最頻繁的&中位數)和哈希OneHotEncodeer。|
|刪除欄位 |要從壯舉中掉落的列。|
|塊變壓器| 用於壯舉過程的塊變壓器。|

使用 API 呼叫建立分項目「設定物件:
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

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>具有超參數值的縮放/規範化和演演算法:

要瞭解管道的縮放/規範化和演演演算法/超參數值,請使用fitted_model。"步驟"。 [瞭解有關縮放/規範化的更多](concept-automated-ml.md#preprocess)。 以下是範例輸出：

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

要取得更多詳細資訊,請使用[此範例筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb)中顯示的此幫助器功能。

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


print_model(fitted_model)
```

以下範例輸出適用於使用特定演演演算法的管道(本例中採用RobustScalr的 Logistic回歸)。

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

### <a name="predict-class-probability"></a>預測類概率

使用自動 ML 生成的模型都有包裝物件,這些物件可鏡像開源源類中的功能。 自動 ML 返回的大多數分類模型包裝`predict_proba()`物件實現 函數,該函數接受要素的陣列或稀疏矩陣數據樣本(X 值),並返回每個樣本的 n 維陣組及其相應的類概率。

假設您使用上面相同的調用檢索了最佳運行和擬合模型,則可以直接從擬合型號調用`predict_proba()`,根據型號類型以適當的格式`X_test`提供 樣本。

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

如果基礎模型不支援`predict_proba()`函數或格式不正確,則將引發特定於模型類的異常。 有關如何為不同的模型類型實現此函數的範例,請參閱[隨機林分類器](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba)和[XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html)參考文檔。

<a name="explain"></a>

## <a name="model-interpretability"></a>模型可解釋性

模型可解釋性使您能夠瞭解模型進行預測的原因,以及基礎要素重要性值。 SDK 包括各種包,用於在訓練和推理時為本地和部署的模型啟用模型可解釋性功能。

有關如何在自動機器學習實驗中啟用可解釋性功能的代碼示例[,請參閱操作操作。](how-to-machine-learning-interpretability-automl.md)

有關如何在自動化機器學習之外的 SDK 其他區域啟用模型說明和功能重要性的一般資訊,請參閱有關可解釋性[的概念](how-to-machine-learning-interpretability.md)文章。

## <a name="next-steps"></a>後續步驟

深入了解[模型部署的方式和位置](how-to-deploy-and-where.md)。

詳細瞭解[如何使用自動機器學習訓練回歸模型](tutorial-auto-train-models.md),以及如何[在遠端資源上使用自動機器學習進行培訓](how-to-auto-train-remote.md)。
