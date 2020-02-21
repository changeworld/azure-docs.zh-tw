---
title: 自動化機器學習中的模型 interpretability
titleSuffix: Azure Machine Learning
description: 瞭解如何取得自動化 ML 模型判斷功能重要性的說明，並在使用 Azure Machine Learning SDK 時進行預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 4ab3bc43cf8ef479cb91d187a4c177db03415b86
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525578"
---
# <a name="model-interpretability-in-automated-machine-learning"></a>自動化機器學習中的模型 interpretability

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何在 Azure Machine Learning 中啟用自動化機器學習（ML）的 interpretability 功能。 自動化 ML 可協助您瞭解原始和工程功能的重要性。 若要使用模型 interpretability，請在 `AutoMLConfig` 物件中設定 `model_explainability=True`。  

在本文中，您將學會如何：

- 在最佳模型或任何模型的定型期間執行 interpretability。
- 啟用視覺效果，協助您查看資料和說明中的模式。
- 在推斷或評分期間，執行 interpretability。

## <a name="prerequisites"></a>必要條件

- Interpretability 功能。 執行 `pip install azureml-interpret azureml-contrib-interpret` 以取得必要的套件。
- 建立自動化 ML 實驗的知識。 如需如何使用 Azure Machine Learning SDK 的詳細資訊，請完成此[回歸模型教學](tutorial-auto-train-models.md)課程，或查看如何[設定自動化 ML 實驗](how-to-configure-auto-train.md)。

## <a name="interpretability-during-training-for-the-best-model"></a>在訓練期間 Interpretability 最佳模型

從 `best_run`取出說明，其中包含工程化功能和原始功能的說明。

### <a name="download-engineered-feature-importance-from-artifact-store"></a>從成品存放區下載設計的功能重要性

您可以使用 `ExplanationClient` 從 `best_run`的成品存放區下載工程化的功能說明。 若要取得原始功能集 `raw=True`的說明。

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>在定型期間 Interpretability 任何模型 

當您計算模型說明並將其視覺化時，您不限於自動化 ML 模型的現有模型說明。 您也可以使用不同的測試資料來取得模型的說明。 本節中的步驟示範如何根據您的測試資料來計算和視覺化設計的功能重要性和原始功能的重要性。

### <a name="retrieve-any-other-automl-model-from-training"></a>從定型取得任何其他 AutoML 模型

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="set-up-the-model-explanations"></a>設定模型說明

使用 `automl_setup_model_explanations` 來取得工程化和原始功能的說明。 `fitted_model` 可以產生下列專案：

- 來自定型或測試範例的精選資料
- 工程設計和原始功能名稱清單
- 分類案例中已加上標籤的資料行中的 Findable 類別

`automl_explainer_setup_obj` 包含上述清單中的所有結構。

```python
from azureml.train.automl.runtime.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>初始化功能重要性的模擬說明

若要產生 AutoML 模型的說明，請使用 `MimicWrapper` 類別。 您可以使用下列參數來初始化 MimicWrapper：

- 說明安裝物件
- 您的工作區
- LightGBM 模型，可作為 `fitted_model` 自動化 ML 模型的代理

MimicWrapper 也會採用將上傳原始和工程說明的 `automl_run` 物件。

```python
from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.explain.model.mimic_wrapper import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel,
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names,
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>使用 MimicExplainer 進行運算，並以視覺化方式設計功能重要性

您可以使用已轉換的測試範例，在 MimicWrapper 中呼叫 `explain()` 方法，以取得所產生之工程功能的功能重要性。 您也可以使用 `ExplanationDashboard`，透過自動化 ML 有來查看所產生之工程功能的功能重要性值的儀表板視覺效果。

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>使用模擬說明來計算和視覺化原始功能重要性

您可以使用已轉換的測試範例，再次呼叫 MimicWrapper 中的 `explain()` 方法，並設定 `get_raw=True` 以取得原始功能的功能重要性。 您也可以使用 `ExplanationDashboard` 來查看原始功能的功能重要性值的儀表板視覺效果。

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>推斷期間的 Interpretability

在本節中，您將瞭解如何使用說明（用來計算上一節中的說明）來讓自動化 ML 模型。

### <a name="register-the-model-and-the-scoring-explainer"></a>註冊模型和評分說明

您可以使用 `TreeScoringExplainer` 來建立評分說明，以便在推斷階段計算原始和工程化功能的重要性值。 您可以使用先前所計算的 `feature_map` 來初始化評分說明。 評分說明會使用 `feature_map` 傳回原始功能的重要性。

儲存評分說明，然後向模型管理服務註冊模型和評分說明。 執行下列程式碼：

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>建立用於設定服務的 conda 相依性

接下來，在容器中為已部署的模型建立必要的環境相依性。 請注意，> = 1.0.45 版本的 azureml 預設值，必須列為 pip 相依性，因為它包含裝載模型做為 web 服務所需的功能。

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="deploy-the-service"></a>部署服務

使用 conda 檔案和先前步驟中的評分檔案來部署服務。

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>使用測試資料推斷

使用一些測試資料進行推斷，以查看自動化 ML 模型的預測值。 針對預測值和原始功能的重要性，請參閱已設計的功能重要性。

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered and raw explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print(output['raw_local_importance_values'])
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>視覺化以在定型時間探索資料和說明中的模式

您可以在[Azure Machine Learning studio](https://ml.azure.com)的工作區中，將功能重要性圖表視覺化。 當您的自動化 ML 執行完成之後，請選取 [**視圖模型詳細資料**] 以查看特定的執行。 選取 [**說明**] 索引標籤，以查看說明視覺效果儀表板。

[![Machine Learning Interpretability 架構](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## <a name="next-steps"></a>後續步驟

如需如何在「自動化機器學習」以外的 Azure Machine Learning SDK 區域中啟用模型說明和功能重要性的詳細資訊，請參閱[interpretability 上的概念文章](how-to-machine-learning-interpretability.md)。
