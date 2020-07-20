---
title: '解讀 & 在 Python (preview 中說明 ML 模型) '
titleSuffix: Azure Machine Learning
description: 瞭解如何在使用 Azure Machine Learning SDK 時，取得機器學習服務模型判斷功能重要性及進行預測的說明。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: mesameki
author: mesameki
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.custom: tracking-python
ms.openlocfilehash: 3830f65a3435c1db0291811c6306ea579bf1d896
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207148"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>使用 interpretability 套件來說明 Python (preview 中 & 預測的 ML 模型) 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本操作指南中，您將瞭解如何使用 Azure Machine Learning Python SDK 的 interpretability 套件來執行下列工作：


* 在本機上說明整個模型行為或個人電腦上的個別預測。

* 啟用 interpretability 技術來設計功能。

* 說明 Azure 中整個模型和個別預測的行為。

* 使用視覺效果儀表板來與您的模型說明互動。

* 將評分說明與您的模型一起部署，以觀察推斷期間的說明。



如需有關支援的 interpretability 技術和機器學習模型的詳細資訊，請參閱[模型 interpretability （位於 Azure Machine Learning](how-to-machine-learning-interpretability.md)和[範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)中）。

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>在您的個人電腦上產生功能重要性值 
下列範例顯示如何在您的個人電腦上使用 interpretability 套件，而不需要聯絡 Azure 服務。

1. 安裝 `azureml-interpret` 和 `azureml-contrib-interpret` 套件。
    ```bash
    pip install azureml-interpret
    pip install azureml-contrib-interpret
    ```

2. 在本機 Jupyter 筆記本中訓練範例模型。

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

3. 在本機呼叫說明。
   * 若要初始化說明物件，請將您的模型和一些定型資料傳遞至說明的函式。
   * 若要讓您的說明和視覺效果更具資訊性，您可以在執行分類時選擇傳入功能名稱和輸出類別名稱。

   下列程式碼區塊示範如何使用、和在本機具現化說明物件 `TabularExplainer` `MimicExplainer` `PFIExplainer` 。
   * `TabularExplainer`在 (`TreeExplainer` 、或) 底下，呼叫三個 SHAP explainers 的其中一個 `DeepExplainer` `KernelExplainer` 。
   * `TabularExplainer`會自動為您的使用案例選取最適合的一個，但是您可以直接呼叫它的三個基礎 explainers。

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

### <a name="explain-the-entire-model-behavior-global-explanation"></a>說明 (全域說明) 的整個模型行為 

請參閱下列範例，以協助您取得匯總 (全域) 功能重要性值。

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

### <a name="explain-an-individual-prediction-local-explanation"></a>說明個別的預測 (當地說明) 
藉由呼叫個別實例或實例群組的說明，取得不同資料點的個別功能重要性值。
> [!NOTE]
> `PFIExplainer`不支援本機說明。

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>原始功能轉換

您可以選擇根據原始的未轉換功能，而不是設計的功能來取得說明。 在此選項中，您會將功能轉換管線傳遞至中的說明 `train_explain.py` 。 否則，說明會提供工程化功能的說明。

支援的轉換格式與[sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas)中所述相同。 一般而言，只要作業在單一資料行上運作，就可以支援任何轉換，讓它們清楚地是一對多的。

使用 `sklearn.compose.ColumnTransformer` 或搭配合適的轉換器元組清單，取得原始功能的說明。 下列範例會使用 `sklearn.compose.ColumnTransformer` 。

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

如果您想要以合適的轉換器元組清單來執行範例，請使用下列程式碼：

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

## <a name="generate-feature-importance-values-via-remote-runs"></a>透過遠端執行產生功能重要性值

下列範例會示範如何使用 `ExplanationClient` 類別來啟用遠端執行的模型 interpretability。 它在概念上類似于本機進程，不同之處在于：

* 使用 `ExplanationClient` 遠端執行中的來上傳 interpretability 內容。
* 稍後在本機環境中下載內容。

1. 安裝 `azureml-interpret` 和 `azureml-contrib-interpret` 套件。
    ```bash
    pip install azureml-interpret
    pip install azureml-contrib-interpret
    ```
1. 在本機 Jupyter Notebook 中建立定型指令碼。 例如，`train_explain.py`。

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

1. 設定 Azure Machine Learning 計算作為計算目標，並提交定型回合。 如需相關指示，請參閱為[模型定型設定計算目標](how-to-set-up-training-targets.md#amlcompute)。 您可能也會發現[範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation)很有用。

1. 在您的本機 Jupyter 筆記本中下載說明。

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


## <a name="visualizations"></a>視覺效果

在您的本機 Jupyter 筆記本中下載說明之後，您可以使用 [視覺效果儀表板] 來瞭解並解讀您的模型。

### <a name="understand-entire-model-behavior-global-explanation"></a>瞭解完整的模型行為 (全域說明)  

下圖提供定型模型的整體觀點，以及其預測和說明。

|圖|描述|
|----|-----------|
|資料探索| 顯示資料集和預測值的總覽。|
|全球重要性|匯總個別資料點的特徵重要性值，以顯示模型的整體頂端 K (可設定的 K) 重要功能。 有助於瞭解基礎模型的整體行為。|
|說明探索|示範功能如何影響模型預測值的變更，或預測值的機率。 顯示功能互動的影響。|
|摘要重要性|在所有資料點上使用個別功能重要性值，以顯示每項功能對預測值的影響分佈。 您可以使用此圖表來調查功能值影響預測值的方向。
|

[![視覺效果儀表板全域](./media/how-to-machine-learning-interpretability-aml/global-charts.png)](./media/how-to-machine-learning-interpretability-aml/global-charts.png#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>瞭解個別的預測 (當地說明)  

您可以按一下任何一種整體繪圖中的任何個別資料點，為任何資料點載入個別功能的重要性圖。

|圖|描述|
|----|-----------|
|本機重要性|針對個別預測，顯示前 K 個 (可設定的 K) 重要功能。 協助說明特定資料點上基礎模型的本機行為。|
|Perturbation 探索 (假設分析) |允許變更所選資料點的功能值，並觀察對預測值所產生的變更。|
|個別條件式預期 (ICE) | 允許將功能值從最小值變更為最大值。 協助說明當功能變更時，資料點的預測如何改變。|

[![視覺效果儀表板區域功能重要性](./media/how-to-machine-learning-interpretability-aml/local-charts.png)](./media/how-to-machine-learning-interpretability-aml/local-charts.png#lightbox)


[![視覺效果儀表板功能 Perturbation](./media/how-to-machine-learning-interpretability-aml/perturbation.gif)](./media/how-to-machine-learning-interpretability-aml/perturbation.gif#lightbox)


[![視覺效果儀表板 ICE 繪圖](./media/how-to-machine-learning-interpretability-aml/ice-plot.png)](./media/how-to-machine-learning-interpretability-aml/ice-plot.png#lightbox)

> [!NOTE]
> 在 Jupyter 核心啟動之前，請確定您已啟用視覺效果儀表板的 widget 延伸模組。

* Jupyter 筆記本

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* JupyterLab

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

若要載入視覺效果儀表板，請使用下列程式碼。

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Azure Machine Learning studio 中的視覺效果

如果您完成[遠端 interpretability](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs)步驟 (將產生的說明上傳至 Azure Machine Learning 執行歷程記錄) ，您可以在[Azure Machine Learning studio](https://ml.azure.com)中查看視覺效果儀表板。 此儀表板是較簡單的視覺效果儀表板版本， (說明探索和 ICE 繪圖已停用，因為在 studio 中沒有作用中的計算可以執行其即時計算) 。

如果有資料集、全域和本機說明，資料會填入所有索引標籤 (除了 Perturbation 探索和 ICE) 以外。 如果只有全域說明可供使用，則會停用 [摘要重要性] 索引標籤和所有 [本機說明] 索引標籤。

遵循下列其中一個路徑來存取 Azure Machine Learning studio 中的視覺效果儀表板：

*  (預覽) 的**實驗**窗格
  1. 在左窗格中選取 [**實驗**]，以查看您在 Azure Machine Learning 上執行的實驗清單。
  1. 選取特定的實驗，以在該實驗中查看所有執行。
  1. 依序選取 [執行] 和 [說明] 視覺效果儀表板的 [**說明**] 索引標籤。

   [![視覺效果儀表板區域功能重要性](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png)](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png#lightbox)

* **模型**窗格
  1. 如果您[依照使用 Azure Machine Learning 部署模型](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)中的步驟來註冊原始模型，您可以選取左窗格中的 [**模型**] 來進行查看。
  1. 選取模型，然後選取 [**說明**] 索引標籤，以查看說明視覺效果儀表板。

## <a name="interpretability-at-inference-time"></a>在推斷階段 Interpretability

您可以將說明與原始模型一起部署，並在推斷階段使用它來提供個別功能的重要性值 (本機說明) 新的任何新的時間點。 我們也提供較輕量的評分 explainers，以在推斷階段改善 interpretability 效能。 部署較輕量計分說明的程式類似于部署模型，並包含下列步驟：

1. 建立說明物件。 例如，您可以使用 `TabularExplainer` ：

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. 使用說明物件建立評分說明。

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. 設定並註冊使用評分說明模型的影像。

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. 您可以選擇從雲端抓取評分說明並測試說明，這是選擇性步驟。

   ```python
   from azureml.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. 依照下列步驟，將映射部署到計算目標：

   1. 如有需要，請遵循[使用 Azure Machine Learning 部署模型](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)中的步驟來註冊您的原始預測模型。

   1. 建立評分檔案。

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

         此設定取決於您模型的需求。 下列範例會定義使用一個 CPU 核心和一個 GB 記憶體的設定。

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. 建立具有環境相依性的檔案。

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

   1. 建立已安裝 g + + 的自訂 dockerfile。

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. 部署所建立的映射。
   
         此程式大約需要五分鐘的時間。

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

[深入瞭解模型 interpretability](how-to-machine-learning-interpretability.md)

[查看 Azure Machine Learning Interpretability 範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

