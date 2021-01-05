---
title: '在 Python (預覽版中評估 ML 模型的公平) '
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Fairlearn 和 Azure Machine Learning Python SDK 來評定和緩和機器學習模型的公平。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 057f965c67c25d486771a4c037e13153a7f2cf66
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901142"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>使用 Azure Machine Learning 搭配 Fairlearn 開放原始碼套件，以評估 ML 模型 (預覽的公平) 

在本操作指南中，您將瞭解如何使用 [Fairlearn](https://fairlearn.github.io/) 的開放原始碼 Python 套件搭配 Azure Machine Learning 來執行下列工作：

* 評估模型預測的公平。 若要深入瞭解機器學習服務的公平，請參閱 [機器學習服務文章中的公平](concept-fairness-ml.md)。
* 上傳、列出及下載 Azure Machine Learning studio 中的公平評量見解。
* 請參閱 Azure Machine Learning studio 中的公平評量儀表板，以與您的模型 () 的公平見解進行互動。

>[!NOTE]
> 公平評估並非純粹的技術練習。 **此套件可協助您評估機器學習模型的公平，但只有您可以設定和決定模型執行的方式。**  雖然此封裝有助於識別量化計量來評估公平，但機器學習模型的開發人員也必須執行定性分析，以評估其專屬模型的公平。

## <a name="azure-machine-learning-fairness-sdk"></a>Azure Machine Learning 公平 SDK 

Azure Machine Learning 公平 SDK，會 `azureml-contrib-fairness` 在 Azure Machine Learning 內整合開放原始碼 Python 套件 [Fairlearn](http://fairlearn.github.io)。 若要深入瞭解 Azure Machine Learning 內的 Fairlearn 整合，請參閱這些 [範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)。 如需 Fairlearn 的詳細資訊，請參閱 [範例指南](https://fairlearn.github.io/master/auto_examples/) 和 [範例筆記本](https://github.com/fairlearn/fairlearn/tree/master/notebooks)。 

使用下列命令來安裝 `azureml-contrib-fairness` 和 `fairlearn` 封裝：
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```
較新版本的 Fairlearn 也應該在下列範例程式碼中運作。



## <a name="upload-fairness-insights-for-a-single-model"></a>上傳單一模型的公平見解

下列範例顯示如何使用公平套件。 我們會將模型公平見解上傳至 Azure Machine Learning，並查看 Azure Machine Learning studio 中的公平評量儀表板。

1. 在 Jupyter Notebook 中將範例模型定型。 

    針對資料集，我們會使用已知的成人人口普查資料集，這是我們從 OpenML 提取的資料集。 我們假設有一個貸款決策問題，而此標籤指出某個人是否重金換回了先前的貸款。 我們會將模型定型，以預測先前看不見的個人是否會 repay 貸款。 這類模型可能用於進行貸款決策。

    ```python
    import copy
    import numpy as np
    import pandas as pd

    from sklearn.compose import ColumnTransformer
    from sklearn.datasets import fetch_openml
    from sklearn.impute import SimpleImputer
    from sklearn.linear_model import LogisticRegression
    from sklearn.model_selection import train_test_split
    from sklearn.preprocessing import StandardScaler, OneHotEncoder
    from sklearn.compose import make_column_selector as selector
    from sklearn.pipeline import Pipeline
    
    from fairlearn.widget import FairlearnDashboard

    # Load the census dataset
    data = fetch_openml(data_id=1590, as_frame=True)
    X_raw = data.data
    y = (data.target == ">50K") * 1
    
    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    X_raw = data.data
    y = (data.target == ">50K") * 1
    A = X_raw[["race", "sex"]]
    X = X_raw.drop(labels=['sex', 'race'],axis = 1)
    
    # Split the data in "train" and "test" sets
    (X_train, X_test, y_train, y_test, A_train, A_test) = train_test_split(
        X_raw, y, A, test_size=0.3, random_state=12345, stratify=y
    )

    # Ensure indices are aligned between X, y and A,
    # after all the slicing and splitting of DataFrames
    # and Series
    X_train = X_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    y_train = y_train.reset_index(drop=True)
    y_test = y_test.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Define a processing pipeline. This happens after the split to avoid data leakage
    numeric_transformer = Pipeline(
        steps=[
            ("impute", SimpleImputer()),
            ("scaler", StandardScaler()),
        ]
    )
    categorical_transformer = Pipeline(
        [
            ("impute", SimpleImputer(strategy="most_frequent")),
            ("ohe", OneHotEncoder(handle_unknown="ignore")),
        ]
    )
    preprocessor = ColumnTransformer(
        transformers=[
            ("num", numeric_transformer, selector(dtype_exclude="category")),
            ("cat", categorical_transformer, selector(dtype_include="category")),
        ]
    )

    # Put an estimator onto the end of the pipeline
    lr_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                LogisticRegression(solver="liblinear", fit_intercept=True),
            ),
        ]
    )

    # Train the model on the test data
    lr_predictor.fit(X_train, y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Race', 'Sex'],
                       y_true=y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. 登入 Azure Machine Learning 並註冊您的模型。
   
    公平儀表板可以與已註冊或未註冊的模型整合。 使用下列步驟，在 Azure Machine Learning 中註冊您的模型：
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    ```

3. 預先計算的公平度量。

    使用 Fairlearn 的套件建立儀表板字典 `metrics` 。 `_create_group_metric_set`方法的引數類似于儀表板的函式，不同之處在于機密功能會以字典的形式傳遞 (，以確保名稱可) 。 在此情況下，我們也必須指定 (二元分類的預測類型) 在呼叫這個方法時。

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.race, 'Sex': A_test.sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. 上傳預先計算公平度量。
    
    現在，匯入 `azureml.contrib.fairness` 套件以執行上傳：

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    建立實驗，然後執行並上傳儀表板：
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. 檢查 Azure Machine Learning studio 中的公平儀表板

    如果您完成上述步驟 (上傳產生的公平見解至 Azure Machine Learning) ，您可以在 [Azure Machine Learning studio](https://ml.azure.com)中查看公平儀表板。 此儀表板是 Fairlearn 中提供的相同視覺效果儀表板，可讓您在機密功能的子群組之間分析 disparities， (例如男性與女性) 。
    遵循下列其中一個路徑，以存取 Azure Machine Learning studio 中的視覺效果儀表板：

    * **實驗窗格 (預覽)**
    1. 選取左窗格中的 [ **實驗** ]，以查看您在 Azure Machine Learning 上執行的實驗清單。
    1. 選取特定實驗來查看該實驗中的所有執行。
    1. 選取 [執行]，然後選取 [說明視覺效果] 儀表板的 [ **公平** ] 索引標籤。


    [![公平儀表板](./media/how-to-machine-learning-fairness-aml/dashboard.png)](./media/how-to-machine-learning-fairness-aml/dashboard.png#lightbox)
    
    * **模型窗格**
    1. 如果您遵循先前的步驟來註冊原始模型，您可以在左窗格中選取 **模型** 來加以查看。
    1. 選取模型，然後選取 [ **公平** ] 索引標籤，以查看 [說明視覺效果] 儀表板。

    若要深入瞭解視覺效果儀表板及其包含的內容，請參閱 Fairlearn 的 [使用者指南](https://fairlearn.github.io/master/user_guide/assessment.html#fairlearn-dashboard)。

## <a name="upload-fairness-insights-for-multiple-models"></a>針對多個模型上傳公平見解

若要比較多個模型，並查看其公平評定有何不同，您可以傳遞多個模型至視覺效果儀表板，並比較其效能公平取捨。

1. 定型您的模型：
    
    我們現在會根據支援向量機器估算器建立第二個分類器，並使用 Fairlearn 的套件上傳公平的儀表板字典 `metrics` 。 我們假設先前定型的模型仍可供使用。


    ```python
    # Put an SVM predictor onto the preprocessing pipeline
    from sklearn import svm
    svm_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                svm.SVC(),
            ),
        ]
    )

    # Train your second classification model
    svm_predictor.fit(X_train, y_train)
    ```

2. 註冊您的模型

    接下來，在 Azure Machine Learning 中註冊兩個模型。 為了方便起見，請將結果儲存在字典中，這會將 `id` 已註冊模型的)  (格式的字串對應 `name:version` 至預測項本身：

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. 在本機載入 Fairlearn 儀表板

    將公平見解上傳至 Azure Machine Learning 之前，您可以在本機叫用的 Fairlearn 儀表板中檢查這些預測。 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Race', 'Sex'],
                    y_true=y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. 預先計算的公平度量。

    使用 Fairlearn 的套件建立儀表板字典 `metrics` 。

    ```python
    sf = { 'Race': A_test.race, 'Sex': A_test.sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. 上傳預先計算公平度量。
    
    現在，匯入 `azureml.contrib.fairness` 套件以執行上傳：

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    建立實驗，然後執行並上傳儀表板：
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    類似于上一節，您可以遵循上述的其中一個路徑， (透過 Azure Machine Learning studio 中的 **實驗** 或 **模型**) 來存取視覺效果儀表板，並根據公平和效能來比較這兩個模型。


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>上傳 unmitigated 並減輕公平見解

您可以使用 Fairlearn 的 [風險降低演算法](https://fairlearn.github.io/master/user_guide/mitigation.html)、將 (s) 所產生的緩和模型，與原始 unmitigated 模型進行比較，並在比較的模型之間導覽效能/公平取捨。

若要查看示範如何使用 [方格搜尋](https://fairlearn.github.io/master/user_guide/mitigation.html#grid-search) 緩和演算法的範例 (使用不同的公平和效能取捨來建立緩和模型的集合) 請參閱此 [範例筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb)。 

在單一回合中上傳多個模型的公平見解，可讓模型與公平和效能相比較。 您可以按一下模型比較圖中顯示的任何模型，以查看特定模型的詳細公平見解。


[![模型比較 Fairlearn 儀表板](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>後續步驟

[深入瞭解模型公平](concept-fairness-ml.md)

[查看 Azure Machine Learning 公平範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
