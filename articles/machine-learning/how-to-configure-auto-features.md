---
title: AutoML 實驗中的特徵化
titleSuffix: Azure Machine Learning
description: 瞭解 Azure Machine Learning 提供的特徵化設定，以及自動化 ML 實驗中支援特徵工程的方式。
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 05/28/2020
ms.openlocfilehash: e5ed84c6daaf01deb67d39bd13de1498dca131c5
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750877"
---
# <a name="featurization-in-automated-machine-learning"></a>自動化機器學習中的特徵化

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本指南中，您將瞭解：

- Azure Machine Learning 提供的特徵化設定。
- 如何為您的 [自動化機器學習實驗](concept-automated-ml.md)自訂這些功能。

*特徵工程* 是使用資料的領域知識來建立功能的程式，以協助機器學習服務 (ML) 演算法來學習更好的學習。 在 Azure Machine Learning 中，會套用資料調整和正規化技術，讓特徵設計更容易。 這些技術和此功能工程統稱統稱在自動化機器學習或*AutoML*實驗中*特徵化*。

## <a name="prerequisites"></a>Prerequisites

本文假設您已經知道如何設定 AutoML 實驗。 如需設定的相關資訊，請參閱下列文章：

- 針對程式碼優先體驗： [使用適用于 Python 的 AZURE MACHINE LEARNING SDK 來設定自動化 ML 實驗](how-to-configure-auto-train.md)。
- 針對低程式碼或無程式碼體驗： [使用 Azure Machine Learning Studio 建立、檢查和部署自動化機器學習模型](how-to-use-automated-ml-for-ml-models.md)。

## <a name="configure-featurization"></a>設定特徵化

在每個自動化機器學習實驗中，預設會將 [自動調整和正規化技術](#featurization) 套用至您的資料。 這些技術是特徵化的類型，可協助 *特定* 的演算法，而這些演算法對不同規模的功能很敏感。 不過，您也可以啟用其他特徵化，例如 *遺漏值插補*、 *編碼*和 *轉換*。

> [!NOTE]
> 自動化機器學習特徵化的步驟 (例如功能正規化、處理遺漏的資料，或將文字轉換成數值) 成為基礎模型的一部分。 當您使用模型進行預測時，定型期間所套用的相同特徵化步驟會自動套用至您的輸入資料。

針對您使用 Python SDK 設定的實驗，您可以啟用或停用特徵化設定，並進一步指定要用於實驗的特徵化步驟。 如果您是使用 Azure Machine Learning studio，請參閱 [啟用特徵化的步驟](how-to-use-automated-ml-for-ml-models.md#customize-featurization)。

下表顯示 `featurization` [AutoMLConfig 類別](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)中的已接受設定：

|特徵化設定 | 描述|
------------- | ------------- |
|`"featurization": 'auto'`| 指定在前置處理過程中，會自動執行 [資料護欄和特徵化步驟](#featurization) 。 這項設定是預設值。|
|`"featurization": 'off'`| 指定不會自動執行特徵化步驟。|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 指定要使用的自訂特徵化步驟。 [了解如何自訂特徵化](#customize-featurization)。|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>自動特製化

下表摘要說明自動套用至您資料的技術。 這些技術適用于使用 SDK 或 studio 設定的實驗。 若要停用此行為，請 `"featurization": 'off'` 在物件中設定 `AutoMLConfig` 。

> [!NOTE]
> 如果您打算將 AutoML 建立的模型匯出至 [ONNX 模型](concept-onnx.md)，則 ONNX 格式僅支援以星號表示的特徵化選項 ( "*" ) 。 深入了解[將模型轉換為 ONNX](concept-automated-ml.md#use-with-onnx)。

|特徵化 &nbsp; 步驟| 描述 |
| ------------- | ------------- |
|**捨棄高基數或沒有變異數功能*** |從定型和驗證集卸載這些功能。 適用于所有遺漏值的功能，在所有資料列中都具有相同的值，或是具有高基數 (例如，雜湊、識別碼或 Guid) 。|
|**插補遺漏值*** |若為數值特徵，則會以資料行中的平均值來插補。<br/><br/>針對類別功能，以最頻繁的值插補。|
|**產生額外的功能*** |針對 DateTime 特徵：年、月、日、星期幾、幾月幾日、季、第幾週、小時、分鐘、秒。<br><br> *針對預測工作，* 會建立這些額外的日期時間功能： ISO 年、半半年、日曆月份，以字串、周、周中的日、每週的日期、年中的日、上午/下午 (0。如果小時是在中午 () 下午12點之前，則 (為 1) ;<br/><br/>針對文字功能：根據 unigrams、雙字母組和 trigrams 的詞彙頻率。 深入瞭解[如何使用 BERT 完成這](#bert-integration)項工作。|
|**轉換和編碼***|將有幾個唯一值的數值特徵轉換成類別特徵。<br/><br/>一種經常性編碼用於低基數類別功能。 一種熱雜湊編碼用於高基數類別功能。|
|**字組內嵌**|文字 featurizer 使用預先定型的模型，將文字標記的向量轉換成句子向量。 檔中每個單字的內嵌向量都會使用 rest 來匯總，以產生檔功能向量。|
|**目標編碼**|針對類別功能，此步驟會將每個類別對應至回歸問題的平均目標值，並對應至每個類別的類別機率，以解決分類問題。 會套用以頻率為基礎的加權和 k 折迭交叉驗證，以減少由稀疏資料類別造成的對應和雜訊過度學習。|
|**文字目標編碼**|針對文字輸入，會使用具備文字袋 (bag-of-words) 的堆疊線性模型來產生每個類別其機率。|
|**證據權數 (WoE)**|將 WoE 作為類別資料行相互關聯與目標資料行相互關聯的量值計算。 WoE 的計算方式是依類別和類別外機率的比率記錄。 此步驟會針對每個類別產生一個數值特徵資料行，並免除明確插補遺漏值和極端值處理的需求。|
|**叢集距離**|在所有數值資料行上訓練 k 表示叢集模型。 會為每個叢集產生 *k* 個新功能 (一個新的數值功能，) 包含每個範例與每個叢集的距心之間的距離。|

## <a name="data-guardrails"></a>資料護欄

*資料護欄* 可協助您找出資料的潛在問題 (例如，遺漏值或 [類別不平衡](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)) 。 它們也可協助您採取矯正措施來改善結果。

適用資料護欄：

- **針對 SDK 實驗**：當您在 `"featurization": 'auto'` `validation=auto` 物件中指定參數時 `AutoMLConfig` 。
- **針對 studio 實驗**：啟用自動特徵化時。

您可以檢查實驗的資料護欄：

- 藉由 `show_output=True` 使用 SDK 來提交實驗時的設定。

- 在 studio 中，于自動化 ML 執行的 [ **資料護欄** ] 索引標籤上。

### <a name="data-guardrail-states"></a>資料 guardrail 狀態

資料護欄會顯示三種狀態之一：

|State| 描述 |
|----|---- |
|**通過**| 未偵測到任何資料問題，您不需要採取任何動作。 |
|**完成**| 已成功將變更套用到資料。 建議您複習 AutoML 所採取的更正動作，以確保變更與預期的結果相符。 |
|**警示**| 偵測到資料問題，但無法補救。 我們建議您修訂並修正問題。|

### <a name="supported-data-guardrails"></a>支援的資料護欄

下表描述目前支援的資料護欄，以及您在提交實驗時可能會看到的相關聯狀態：

護欄|狀態|觸發條件&nbsp;&nbsp;
---|---|---
**遺漏特徵值插補** |已通過 <br><br><br> 完成| 在訓練資料中沒有偵測到任何遺漏特徵值。 深入瞭解 [遺漏值插補。](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> 在定型資料中偵測到遺漏的功能值，而且已插補。
**高基數特徵處理** |已通過 <br><br><br> 完成| 已分析您的輸入，但未偵測到高基數功能。 <br><br> 在您的輸入中偵測到高基數功能，並已處理。
**驗證分割處理** |完成| 驗證設定已設定為 `'auto'` ，且定型資料包含 *少於20000個數據列*。 <br> 定型模型的每個反復專案都是使用交叉驗證進行驗證。 深入瞭解 [驗證資料](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data)。 <br><br> 驗證設定已設定為 `'auto'` ，且定型資料包含 *20000 個以上*的資料列。 <br> 輸入資料已分割成訓練資料集和驗證資料集，以用於模型驗證。
**類別平衡偵測** |已通過 <br><br><br><br>警示 <br><br><br>完成 | 已分析輸入，且訓練資料中所有類別都是平衡的。 如果每個類別在資料集中都有良好的標記法（以樣本的數目和比率測量），則會將資料集視為對稱。 <br><br> 在輸入中偵測到不平衡的類別。 若要修正模型偏差，請修正平衡問題。 深入瞭解 [不平衡資料](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)。<br><br> 在您的輸入中偵測到不平衡類別，而且清除的邏輯已判定要套用平衡。
**記憶體問題偵測** |已通過 <br><br><br><br> 完成 |<br> 選取的值 (範圍、延遲、滾動時間範圍) 已分析，且未偵測到潛在的記憶體不足問題。 深入瞭解時間序列 [預測](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment)設定。 <br><br><br>選取的值 (範圍、延遲、滾動時間範圍) 已分析，而且可能會導致您的實驗用盡記憶體。 延遲或滾動視窗設定已關閉。
**頻率偵測** |已通過 <br><br><br><br> 完成 |<br> 系統會分析時間序列，並將所有資料點與偵測到的頻率對齊。 <br> <br> 分析時間序列，並偵測到未與偵測到的頻率一致的資料點。 這些資料點已從資料集移除。 深入瞭解 [時間序列預測的資料準備](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)。

## <a name="customize-featurization"></a>自訂特徵化

您可以自訂特徵化設定，以確保用來定型 ML 模型的資料和功能會產生相關的預測。

若要自訂 featurizations，請  `"featurization": FeaturizationConfig` 在物件中指定 `AutoMLConfig` 。 如果您在實驗中使用 Azure Machine Learning studio，請參閱操作 [說明文章](how-to-use-automated-ml-for-ml-models.md#customize-featurization)。 若要自訂 forecastings 工作類型的特徵化，請參閱「 [預測操作說明](how-to-auto-train-forecast.md#customize-featurization)」。

支援的自訂項目包含：

|自訂|定義|
|--|--|
|**資料行用途更新**|覆寫指定之資料行的 autodetected 功能類型。|
|**轉換器參數更新** |更新指定轉換器的參數。 目前支援 *Imputer* (mean、最頻繁和中位數) 和 *HashOneHotEncoder*。|
|**卸除資料行** |指定要從特徵化中捨棄的資料行。|
|**區塊轉換器**| 指定要在特徵化進程中使用的區塊轉換器。|

`FeaturizationConfig`使用 API 呼叫來建立物件：

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

## <a name="featurization-transparency"></a>特徵化透明度

每個 AutoML 模型都會自動套用特徵化。  特徵化包括自動化功能工程 (在 `"featurization": 'auto'`) 和調整和正規化時，會影響所選取的演算法及其超參數值。 AutoML 支援不同的方法，以確保您可以看見已套用至模型的內容。

請考慮此預測範例：

+ 輸入功能有四種： (數值) 、B (數值) 、C (數值) 、D (DateTime) 。
+ 數值特徵 C 已卸載，因為它是具有所有唯一值的識別碼資料行。
+ 數值特徵 A 和 B 有遺漏值，因此是由平均值所插補。
+ DateTime 功能 D 特徵化為11個不同的設計功能。

若要取得此資訊，請使用 `fitted_model` 自動化 ML 實驗執行的輸出。

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```
### <a name="automated-feature-engineering"></a>自動化特徵工程 
會傳回 `get_engineered_feature_names()` 工程功能名稱的清單。

  >[!Note]
  >對於「預測」工作，請使用 'timeseriestransformer'，對於「迴歸」或「分類」工作則使用 'datatransformer'。

  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

此清單包含所有工程特徵名稱。 

  ```
  ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

會 `get_featurization_summary()` 取得所有輸入功能的特徵化摘要。

  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

輸出

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

   |輸出|定義|
   |----|--------|
   |RawFeatureName|提供的資料集中的輸入特徵/資料行名稱。|
   |TypeDetected|偵測到輸入特徵的資料類型。|
   |Dropped|指出是已卸除還是使用了輸入特徵。|
   |EngineeringFeatureCount|透過自動化特徵工程轉換而產生的特徵數目。|
   |轉換|套用至輸入特徵以產生工程特徵的轉換清單。|

### <a name="scaling-and-normalization"></a>調整和正規化

若要瞭解調整/正規化和所選取的演算法及其超參數值，請使用 `fitted_model.steps` 。 

下列範例輸出是針對選擇的執行而執行的 `fitted_model.steps` ：

```
[('RobustScaler', 
  RobustScaler(copy=True, 
  quantile_range=[10, 90], 
  with_centering=True, 
  with_scaling=True)), 

  ('LogisticRegression', 
  LogisticRegression(C=0.18420699693267145, class_weight='balanced', 
  dual=False, 
  fit_intercept=True, 
  intercept_scaling=1, 
  max_iter=100, 
  multi_class='multinomial', 
  n_jobs=1, penalty='l2', 
  random_state=None, 
  solver='newton-cg', 
  tol=0.0001, 
  verbose=0, 
  warm_start=False))
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

此 helper 函式會使用 `LogisticRegression with RobustScalar` 做為特定的演算法，傳回特定執行的下列輸出。

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

## <a name="bert-integration"></a>BERT 整合

[BERT](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657) 用於 AutoML 的特徵化層。 在這一層中，如果資料行包含任意文字或其他類型的資料（例如時間戳記或簡單數位），則會據以套用特徵化。

針對 BERT，模型會經過微調，並利用使用者提供的標籤進行定型。 從這裡開始，檔內嵌會以功能和其他的形式輸出，例如以時間戳記為基礎的功能、周中的日。 


### <a name="bert-steps"></a>BERT 步驟

若要叫用 BERT，您必須  `enable_dnn: True` 在 automl_settings 中設定，並使用 gpu 計算 (例如 `vm_size = "STANDARD_NC6"` ，或較高的 gpu) 。 如果使用 CPU 計算，而不是 BERT，AutoML 就會啟用 BiLSTM DNN featurizer。

AutoML 會採取下列步驟來進行 BERT。 

1. **所有文字資料行的**前置處理和 token 化。 例如，"StringCast" 轉換器可以在最終模型的特徵化摘要中找到。 您可以在 [此筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)中找到如何產生模型特徵化摘要的範例。

2. 將**所有文字資料行串連成單一文字資料行**，因此 `StringConcatTransformer` 在最終模型中。 

    我們的 BERT 會將定型範例的文字長度總計限制為128個權杖。 也就是說，串連所有的文字資料行時，最理想的長度應該是最多128的權杖。 如果有多個資料行存在，則應該將每個資料行剪除，如此就能滿足這種狀況。 否則，針對長度 >128 token 的串連資料行，BERT 的 tokenizer 層會將此輸入截斷為 128 token。

3. **在功能整理的過程中，AutoML 會比較 BERT 與資料範例) 的基準 (包功能。** 這項比較會決定 BERT 是否會提供精確度的改進。 如果 BERT 效能優於基準，AutoML 接著會針對整個資料使用 BERT for text 特徵化。 在這種情況下，您將會 `PretrainedTextDNNTransformer` 在最終模型中看到。

BERT 的執行時間通常比其他有長。 為了獲得更好的效能，建議使用「STANDARD_NC24r」或「STANDARD_NC24rs_V3」作為其 RDMA 功能。 

AutoML 會在多個節點之間散發 BERT 定型， (最多可有八個節點) 。 將 `AutoMLConfig` `max_concurrent_iterations` 參數設定為大於1，即可在您的物件中完成這項操作。 
### <a name="supported-languages"></a>支援的語言

AutoML 目前支援100種語言，而且根據資料集的語言而定，AutoML 會選擇適當的 BERT 模型。 針對德文資料，我們會使用德文 BERT 模型。 針對英文，我們使用英文 BERT 模型。 針對其他所有語言，我們會使用多語系 BERT 模型。

在下列程式碼中，會觸發德文 BERT 模型，因為資料集語言已指定為 `deu` ，因此根據 [ISO 分類](https://iso639-3.sil.org/code/deu)，德文的三個字母語言代碼：

```python
from azureml.automl.core.featurization import FeaturizationConfig

featurization_config = FeaturizationConfig(dataset_language='deu')

automl_settings = {
    "experiment_timeout_minutes": 120,
    "primary_metric": 'accuracy', 
# All other settings you want to use 
    "featurization": featurization_config,
    
  "enable_dnn": True, # This enables BERT DNN featurizer
    "enable_voting_ensemble": False,
    "enable_stack_ensemble": False
}
```

## <a name="next-steps"></a>後續步驟

* 瞭解如何設定自動化 ML 實驗：

    * 針對程式碼優先體驗： [使用 AZURE MACHINE LEARNING SDK 來設定自動化 ML 實驗](how-to-configure-auto-train.md)。
    * 針對低程式碼或無程式碼體驗： [在 Azure Machine Learning studio 中建立自動化 ML 實驗](how-to-use-automated-ml-for-ml-models.md)。

* 深入了解[模型部署的方式和位置](how-to-deploy-and-where.md)。

* 深入瞭解 [如何使用自動化機器學習來定型回歸模型](tutorial-auto-train-models.md) ，或 [如何在遠端資源上使用自動化機器學習來定型](how-to-auto-train-remote.md)。
