---
title: 本地和遠端運行的可解釋性模型
titleSuffix: Azure Machine Learning
description: 瞭解如何獲取有關機器學習模型如何確定功能重要性的說明，並在使用 Azure 機器學習 SDK 時進行預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: a479982eeac325c9774e3858ec51643e8ba699c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064049"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>本地和遠端運行的可解釋性模型

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將學習使用 Azure 機器學習 Python SDK 的可解釋性包來瞭解模型進行預測的原因。 您會了解如何：

* 解釋在本地和遠端計算資源上培訓的機器學習模型。
* 在 Azure 執行歷程記錄上存儲本地和全域說明。
* 在[Azure 機器學習工作室](https://ml.azure.com)中查看可解釋性視覺化效果。
* 使用模型部署評分解譯器。

有關詳細資訊，請參閱[Azure 機器學習中的模型可解釋性](how-to-machine-learning-interpretability.md)。

## <a name="local-interpretability"></a>本地可解釋性

下面的示例演示如何在不聯繫 Azure 服務的情況下在本地使用可解釋性包。

1. 如果需要，請使用`pip install azureml-interpret`獲取可解釋性包。

1. 在本地聚居器筆記本中訓練示例模型。

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

1. 在本地呼叫解釋者。
   * 要初始化解譯器物件，請將模型和一些訓練資料傳遞給解譯器的建構函式。
   * 為了使解釋和視覺化更加豐富，如果執行分類，可以選擇傳遞要素名稱和輸出類名稱。

   以下代碼塊演示如何具現化解譯器物件，`TabularExplainer``MimicExplainer`以及`PFIExplainer`本地。
   * `TabularExplainer`調用下面三個 SHAP 解譯器之一`TreeExplainer` `DeepExplainer`（，`KernelExplainer`或 ）。
   * `TabularExplainer`自動為用例選擇最合適的一個，但您可以直接調用其三個基礎解譯器中的每個。

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    或

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns.
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```

    或

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="overall-global-feature-importance-values"></a>總體來說，全域特徵重要性值

請參閱以下示例，以説明您獲取全域要素重要性值。

```python

# you can use the training data or the test data here
global_explanation = explainer.explain_global(x_train)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_test)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="instance-level-local-feature-importance-values"></a>實例級本地要素重要性值

通過調用單個實例或一組實例的說明來獲取本地要素重要性值。
> [!NOTE]
> `PFIExplainer`不支援本地解釋。

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>遠端運行的可解釋性

下面的示例演示如何使用 類`ExplanationClient`為遠端運行啟用模型可解釋性。 它在概念上類似于本地流程，但您除外：

* 使用`ExplanationClient`遠端運行中上載可解釋性上下文。
* 稍後在本地環境中下載上下文。

1. 如果需要，請使用`pip install azureml-contrib-interpret`獲取所需的包。

1. 在本機 Jupyter Notebook 中建立定型指令碼。 例如： `train_explain.py` 。

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. 將 Azure 機器學習計算設置為計算目標，並提交培訓運行。 請參閱[為模型培訓設置計算目標](how-to-set-up-training-targets.md#amlcompute)，瞭解說明。 您可能還會發現[示例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation)很有用。

1. 在您當地的 Jupyter 筆記本中下載說明。

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="raw-feature-transformations"></a>原始特徵轉換

您可以選擇從原始、未轉換的功能（而非工程特徵）獲得解釋。 對於此選項，您將功能轉換管道傳遞給 中的`train_explain.py`解譯器。 否則，解譯器會根據工程特徵提供解釋。

支援的轉換格式與["學習熊貓"](https://github.com/scikit-learn-contrib/sklearn-pandas)中所述的格式相同。 通常，只要任何轉換在單個列上操作，即可支援任何轉換，以便它們顯然是一對多的。

通過使用 或帶有已安裝變壓器元數`sklearn.compose.ColumnTransformer`清單，獲取有關原始功能的說明。 下面的示例使用`sklearn.compose.ColumnTransformer`。

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

如果要使用已安裝的變壓器包清單運行示例，請使用以下代碼：

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="visualizations"></a>視覺效果

在本地 Jupyter 筆記本中下載說明後，可以使用視覺化儀表板來理解和解釋模型。

### <a name="global-visualizations"></a>全球視覺化

以下圖提供了已訓練的模型的全域視圖及其預測和說明。

|情節|描述|
|----|-----------|
|資料探索| 顯示資料集的概覽以及預測值。|
|全球重要性|全域顯示 K（可配置 K）重要功能。 説明瞭解基礎模型的全球行為。|
|解釋探索|演示要素如何影響模型預測值的變化或預測值的概率。 顯示要素交互的影響。|
|摘要重要性|在所有資料點上使用局部要素重要性值來顯示每個要素對預測值的影響分佈。|

[![視覺化儀表板全域](./media/how-to-machine-learning-interpretability-aml/global-charts.png)](./media/how-to-machine-learning-interpretability-aml/global-charts.png#lightbox)

### <a name="local-visualizations"></a>本地視覺化

您可以通過在圖中選擇單個資料點來載入任何資料點的本地要素重要性圖。

|情節|描述|
|----|-----------|
|本地重要性|全域顯示前 K（可配置 K）重要功能。 有助於說明基礎模型在特定資料點上的局部行為。|
|擾動探索|允許更改所選資料點的要素值，並觀察對預測值的結果更改。|
|個人條件期望 （ICE）| 允許要素值從最小值更改為最大值。 有助於說明當要素更改時資料點的預測如何變化。|

[![視覺化儀表板本地功能重要性](./media/how-to-machine-learning-interpretability-aml/local-charts.png)](./media/how-to-machine-learning-interpretability-aml/local-charts.png#lightbox)


[![視覺化儀表板功能擾動](./media/how-to-machine-learning-interpretability-aml/perturbation.gif)](./media/how-to-machine-learning-interpretability-aml/perturbation.gif#lightbox)


[![視覺化儀表板 ICE 繪圖](./media/how-to-machine-learning-interpretability-aml/ice-plot.png)](./media/how-to-machine-learning-interpretability-aml/ice-plot.png#lightbox)

> [!NOTE]
> 在 Jupyter 內核啟動之前，請確保為視覺化儀表板啟用小部件擴展。

* Jupyter Notebook

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* 朱皮特拉布

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

要載入視覺化儀表板，請使用以下代碼。

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Azure 機器學習工作室中的視覺化

如果完成[遠端可解釋性](#interpretability-for-remote-runs)步驟，則可以在[Azure 機器學習工作室](https://ml.azure.com)中查看視覺化儀表板。 此儀表板是上面解釋的視覺化儀表板的較簡單的版本。 它僅支援兩個選項卡：

|情節|描述|
|----|-----------|
|全球重要性|全域顯示 K（可配置 K）重要功能。 説明瞭解基礎模型的全球行為。|
|摘要重要性|在所有資料點上使用局部要素重要性值來顯示每個要素對預測值的影響分佈。|

如果全域和本地說明都可用，則資料將填充這兩個選項卡。 如果只有全域說明可用，則禁用"摘要重要性"選項卡。

按照以下路徑之一訪問 Azure 機器學習工作室中的視覺化儀表板：

* **實驗**窗格（預覽）
  1. 在左側窗格中選擇 **"實驗"** 以查看在 Azure 機器學習上運行的實驗清單。
  1. 選擇特定實驗以查看該實驗中的所有運行。
  1. 選擇運行，然後選擇"**解釋"** 選項卡到解釋視覺化儀表板。

   [![視覺化儀表板本地功能重要性](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png)](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png#lightbox)

* **模型**窗格
  1. 如果按照[Azure 機器學習中的"部署模型"](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)中的步驟註冊原始模型，則可以在左側窗格中選擇 **"模型**"來查看它。
  1. 選擇模型，然後選擇 **"說明"** 選項卡以查看解釋視覺化儀表板。

## <a name="interpretability-at-inference-time"></a>推理時的解釋性

您可以將解譯器與原始模型一起部署，並在推理時使用它來提供本地解釋資訊。 我們還提供較輕的評分解譯器，以提高推理時的解釋性能。 部署較輕的評分解譯器的過程類似于部署模型，包括以下步驟：

1. 創建解釋物件。 例如，可以使用`TabularExplainer`：

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. 使用解釋物件創建評分解譯器。

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. 配置並註冊使用評分解譯器模型的圖像。

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. 作為可選步驟，可以從雲檢索評分解譯器並測試說明。

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. 按照以下步驟將映射部署到計算目標：

   1. 如果需要，請按照[Azure 機器學習中部署模型](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)中的步驟註冊原始預測模型。

   1. 創建評分檔。

         ```python
         %%writefile score.py
         import json
         import numpy as np
         import pandas as pd
         import os
         import pickle
         from sklearn.externals import joblib
         from sklearn.linear_model import LogisticRegression
         from azureml.core.model import Model
          
         def init():
         
            global original_model
            global scoring_model
             
            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

         def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
         ```
   1. 定義部署設定。

         此配置取決於模型的要求。 下面的示例定義使用一個 CPU 內核和一 GB 記憶體的配置。

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. 創建具有環境依賴性的檔。

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. 創建安裝 g# 的自訂 Docker 檔。

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. 部署創建的映射。
   
         此過程大約需要五分鐘。

         ```python
         from azureml.core.webservice import Webservice
         from azureml.core.image import ContainerImage

         # use the custom scoring, docker, and conda files we created above
         image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                         docker_file="dockerfile",
                                                         runtime="python",
                                                         conda_file="myenv.yml")

         # use configs and models generated above
         service = Webservice.deploy_from_model(workspace=ws,
                                             name='model-scoring-service',
                                             deployment_config=aciconfig,
                                             models=[scoring_explainer_model, original_model],
                                             image_config=image_config)

         service.wait_for_deployment(show_output=True)
         ```

1. 測試部署。

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. 清除。

   若要刪除已部署的 Web 服務，請使用 `service.delete()`。

## <a name="next-steps"></a>後續步驟

[瞭解有關模型可解釋性的更多](how-to-machine-learning-interpretability.md)