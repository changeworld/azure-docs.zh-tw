---
title: '自動化 ML (預覽版中的可解釋性) '
titleSuffix: Azure Machine Learning
description: 瞭解如何在使用 Azure Machine Learning SDK 時，取得自動化 ML 模型如何判斷功能重要性以及進行預測的說明。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: mithigpe
author: minthigpen
ms.date: 07/09/2020
ms.openlocfilehash: 7cb40df6a4619e11694e65020bfcb560cf695795
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897446"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning-preview"></a>可解譯性：自動化機器學習中的模型說明 (預覽) 



在本文中，您將瞭解如何在 Azure Machine Learning 中取得自動化機器學習 (ML) 的說明。 自動化 ML 可協助您瞭解設計的功能重要性。 

1.0.85 預設設定之後的所有 SDK 版本 `model_explainability=True` 。 在 SDK 版本1.0.85 和較早的版本中，使用者必須在物件中設定，才能 `model_explainability=True` `AutoMLConfig` 使用 [模型可解譯性]。 

在本文中，您將學會如何：

- 在定型期間，針對最佳模型或任何模型執行可解譯性。
- 啟用視覺效果，以協助您查看資料中的模式和說明。
- 在推斷或評分期間執行可解譯性。

## <a name="prerequisites"></a>必要條件

- 可解譯性功能。 執行 `pip install azureml-interpret azureml-contrib-interpret` 以取得必要的套件。
- 建立自動化 ML 實驗的知識。 如需有關如何使用 Azure Machine Learning SDK 的詳細資訊，請完成此 [回歸模型教學](tutorial-auto-train-models.md) 課程，或瞭解如何 [設定自動化 ML 實驗](how-to-configure-auto-train.md)。

## <a name="interpretability-during-training-for-the-best-model"></a>在訓練期間可解譯性最佳模型

從取得的說明 `best_run` ，其中包括設計功能的說明。

> [!Warning]
> 可解譯性，最佳模型說明無法用於建議下列演算法做為最佳模型的自動 ML 預測實驗： 
> * ForecastTCN
> * 平均 
> * 貝氏
> * 季節性平均 
> * 季節性貝氏

### <a name="download-engineered-feature-importance-from-artifact-store"></a>從成品存放區下載設計的功能重要性

您可以使用 `ExplanationClient` 從的成品存放區下載設計的功能說明 `best_run` 。 

```python
from azureml.explain.model._internal.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>在定型期間可解譯性任何模型 

當您計算模型說明並將其視覺化時，不限於自動化 ML 模型的現有模型說明。 您也可以使用不同的測試資料來取得模型的說明。 本節中的步驟說明如何根據您的測試資料來計算和視覺化設計功能重要性。

### <a name="retrieve-any-other-automl-model-from-training"></a>從定型取出任何其他 AutoML 模型

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>設定模型說明

使用 `automl_setup_model_explanations` 來取得工程的說明。 `fitted_model`可以產生下列專案：

- 來自定型或測試範例的精選資料
- 設計功能名稱清單
- 分類案例中標示資料行的 Findable 類別

`automl_explainer_setup_obj`包含上述清單中的所有結構。

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>初始化功能重要性的模仿說明

若要產生 AutoML 模型的說明，請使用 `MimicWrapper` 類別。 您可以使用下列參數來初始化 MimicWrapper：

- 說明安裝物件
- 您的工作區
- 用來說明 `fitted_model` 自動化 ML 模型的代理模型

MimicWrapper 也會採用將 `automl_run` 上傳設計說明的物件。

```python
from azureml.explain.model.mimic_wrapper import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator,
                         explainable_model=automl_explainer_setup_obj.surrogate_model, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes,
                         explainer_kwargs=automl_explainer_setup_obj.surrogate_model_params)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>使用 MimicExplainer 進行運算，並以視覺化方式設計功能重要性

您可以使用已 `explain()` 轉換的測試範例，在 MimicWrapper 中呼叫方法，以取得所產生之工程功能的重要性。 您也可以使用 `ExplanationDashboard` 來查看自動化 ML 有所產生之設計功能的功能重要性值的儀表板視覺效果。

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-inference"></a>在推斷期間可解譯性

在本節中，您將瞭解如何使用說明來讓自動化 ML 模型，以用來計算上一節中的說明。

### <a name="register-the-model-and-the-scoring-explainer"></a>註冊模型和評分說明

使用 `TreeScoringExplainer` 建立計分說明，以在推斷時計算設計的特徵重要性值。 您可以使用先前計算的來初始化評分說明 `feature_map` 。 

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

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>建立 conda 相依性來設定服務

接下來，在已部署模型的容器中建立必要的環境相依性。 請注意，>= 1.0.45 版 azureml-defaults 版的 azureml 預設值必須列為 pip 相依性，因為它包含將模型裝載為 web 服務所需的功能。

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

### <a name="inference-with-test-data"></a>使用測試資料的推斷

使用某些測試資料進行推斷，以查看自動化 ML 模型的預測值。 查看預測值的工程功能重要性。

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>視覺化以探索資料中的模式和定型時間的說明

您可以在 [Azure Machine Learning studio](https://ml.azure.com)中，將工作區中的功能重要性圖表視覺化。 在自動化 ML 執行完成後，請選取 [ **視圖模型詳細資料** ] 來查看特定執行。 選取 [ **說明** ] 索引標籤，以查看 [說明視覺效果] 儀表板。

[![Machine Learning 可解譯性架構](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## <a name="next-steps"></a>下一步

如需如何在自動化機器學習以外的 Azure Machine Learning SDK 區域中啟用模型說明和功能重要性的詳細資訊，請參閱 [可解譯性上的概念文章](how-to-machine-learning-interpretability.md)。
