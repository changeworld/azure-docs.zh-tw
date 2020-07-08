---
title: 在 Python 中評估 ML 模型的公平
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 中評估模型的公平
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 06/30/2020
ms.custom: tracking-python
ms.openlocfilehash: 9e88b87a7f6471f6c7344cc5548c37e947f18791
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85660762"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models"></a>使用 Azure Machine Learning 搭配 Fairlearn 開放原始碼套件，以評估 ML 模型的公平  

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本操作指南中，您將瞭解如何搭配 Azure Machine Learning 使用[Fairlearn](https://fairlearn.github.io/)開放原始碼 Python 套件來執行下列工作：

* 評估模型預測的公平。 若要深入瞭解機器學習服務中的公平，請參閱[機器學習服務中的公平一文](concept-fairness-ml.md)。
* 上傳、列出及下載 Azure Machine Learning studio 的公平評量深入解析。
* 請參閱 Azure Machine Learning studio 中的公平評量儀表板，與您的模型公平深入解析互動。

>[!NOTE]
> 公平評估並非純粹的技術練習。 **此套件可協助您評估機器學習模型的公平，但只有您可以設定和做出模型執行方式的決策。**  雖然此封裝有助於識別量化計量以評估公平，但機器學習模型的開發人員也必須執行定性分析，以評估其專屬模型的公平。

## <a name="azure-machine-learning-fairness-sdk"></a>Azure Machine Learning 公平 SDK 

Azure Machine Learning 公平 SDK，會 `azureml-contrib-fairness` 整合 Azure Machine Learning 中的開放原始碼 Python 套件[Fairlearn](http://fairlearn.github.io)。 若要深入瞭解 Fairlearn 在 Azure Machine Learning 內的整合，請參閱這些[範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)。 如需 Fairlearn 的詳細資訊，請參閱[範例指南](https://fairlearn.github.io/auto_examples/notebooks/index.html)和[範例筆記本](https://github.com/fairlearn/fairlearn/tree/master/notebooks)。 

使用下列命令來安裝 `azureml-contrib-fairness` 和 `fairlearn` 封裝：
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```



## <a name="upload-fairness-insights-for-a-single-model"></a>上傳單一模型的公平深入解析

下列範例示範如何使用公平套件，將模型公平深入解析上傳至 Azure Machine Learning，並查看 Azure Machine Learning studio 中的公平評量儀表板。

1. 訓練 Jupyter 筆記本中的範例模型。 

    針對資料集，我們會使用我們用來載入的知名成人人口普查資料集 `shap` （為了方便起見）。 基於此範例的目的，我們會將此資料集視為貸款決策問題，並假設該標籤指出每個個體是否重金換回過去的貸款。 我們將使用資料來定型預測，以預測先前看不見的個人是否會 repay 貸款。 假設模型預測是用來決定是否要為個人提供貸款。

    ```python
    from sklearn.model_selection import train_test_split
    from fairlearn.widget import FairlearnDashboard
    from sklearn.linear_model import LogisticRegression
    from sklearn.preprocessing import LabelEncoder, StandardScaler
    import pandas as pd
    import shap

    # Load the census dataset
    X_raw, Y = shap.datasets.adult()
    X_raw["Race"].value_counts().to_dict()
    

    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    A = X_raw[['Sex','Race']]
    X = X_raw.drop(labels=['Sex', 'Race'],axis = 1)
    X = pd.get_dummies(X)
    
    sc = StandardScaler()
    X_scaled = sc.fit_transform(X)
    X_scaled = pd.DataFrame(X_scaled, columns=X.columns)

    # Perform some standard data preprocessing steps to convert the data into a format suitable for the ML algorithms
    le = LabelEncoder()
    Y = le.fit_transform(Y)

    # Split data into train and test
    from sklearn.model_selection import train_test_split
    from sklearn.model_selection import train_test_split
    X_train, X_test, Y_train, Y_test, A_train, A_test = train_test_split(X_scaled, 
                                                        Y, 
                                                        A,
                                                        test_size = 0.2,
                                                        random_state=0,
                                                        stratify=Y)

    # Work around indexing issue
    X_train = X_train.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Improve labels
    A_test.Sex.loc[(A_test['Sex'] == 0)] = 'female'
    A_test.Sex.loc[(A_test['Sex'] == 1)] = 'male'


    A_test.Race.loc[(A_test['Race'] == 0)] = 'Amer-Indian-Eskimo'
    A_test.Race.loc[(A_test['Race'] == 1)] = 'Asian-Pac-Islander'
    A_test.Race.loc[(A_test['Race'] == 2)] = 'Black'
    A_test.Race.loc[(A_test['Race'] == 3)] = 'Other'
    A_test.Race.loc[(A_test['Race'] == 4)] = 'White'


    # Train a classification model
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Sex', 'Race'],
                       y_true=Y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. 登入 Azure Machine Learning 並註冊您的模型。
   
    公平儀表板可以與已註冊或未註冊的模型整合。 使用下列步驟在 Azure Machine Learning 中註冊您的模型：
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
    lr_reg_id = register_model("fairness_linear_regression", unmitigated_predictor)
    ```

3. 預先計算的公平計量。

    使用 Fairlearn 的套件建立儀表板字典 `metrics` 。 `_create_group_metric_set`方法的引數與儀表板的函式相似，不同之處在于機密功能會當做字典傳遞（以確保名稱可供使用）。 呼叫這個方法時，也必須指定預測的類型（在此案例中為二元分類）。

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex}
    ys_pred = unmitigated_predictor.predict(X_test)
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. 上傳預先計算公平計量。
    
    現在，匯入 `azureml.contrib.fairness` 封裝以執行上傳：

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    建立實驗，然後執行並將儀表板上傳至其中：
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
5. 從 Azure Machine Learning studio 檢查公平儀表板

    如果您完成先前的步驟（將產生的公平見解上傳至 Azure Machine Learning），您可以在[Azure Machine Learning studio](https://ml.azure.com)中查看公平儀表板。 此儀表板是 Fairlearn 中提供的相同視覺效果儀表板，可讓您分析敏感性功能的子群組（例如男性與女性）之間的 disparities。
    遵循下列其中一個路徑來存取 Azure Machine Learning studio 中的視覺效果儀表板：

    * **實驗窗格（預覽）**
    1. 在左窗格中選取 [**實驗**]，以查看您在 Azure Machine Learning 上執行的實驗清單。
    1. 選取特定的實驗，以在該實驗中查看所有執行。
    1. 選取 [執行]，然後在 [說明視覺效果儀表板] 的 [**公平**] 索引標籤。


    [![公平儀表板](./media/how-to-machine-learning-fairness-aml/dashboard.png)](./media/how-to-machine-learning-fairness-aml/dashboard.png#lightbox)
    
    * **模型窗格**
    1. 如果您依照先前的步驟註冊原始模型，您可以選取左窗格中的 [**模型**] 來進行查看。
    1. 選取模型，然後選取 [**公平**] 索引標籤，以查看 [說明視覺效果] 儀表板。

    若要深入瞭解視覺效果儀表板及其包含的內容，請查看 Fairlearn 的[使用者指南](https://fairlearn.github.io/user_guide/assessment.html#fairlearn-dashboard)。

s # # 上傳多個模型的公平深入解析

如果您想要比較多個模型，並查看其公平評量有何不同，您可以將多個模型傳遞至視覺效果儀表板，並流覽其效能公平取捨。

1. 訓練您的模型：
    
    除了先前的羅吉斯回歸模型，我們現在會根據支援向量機器估計工具建立第二個分類器，並使用 Fairlearn 的套件上傳公平儀表板字典 `metrics` 。 請注意，我們會略過載入和前置處理資料的步驟，直接移至模型定型階段。


    ```python
    # Train your first classification model
    from sklearn.linear_model import LogisticRegression
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # Train your second classification model
    from sklearn import svm
    svm_predictor = svm.SVC()
    svm_predictor.fit(X_train, Y_train)
    ```

2. 註冊您的模型

    接下來，在 Azure Machine Learning 中註冊這兩個模型。 為了方便在後續的方法呼叫中，將結果儲存在字典中，將 `id` 已註冊模型的（格式為字串 `name:version` ）對應到預測器本身：

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_linear_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. 在本機載入 Fairlearn 儀表板

    將公平深入解析上傳至 Azure Machine Learning 之前，您可以在本機叫用的 Fairlearn 儀表板中檢查這些預測。 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Sex', 'Race'],
                    y_true=Y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. 預先計算的公平計量。

    使用 Fairlearn 的套件建立儀表板字典 `metrics` 。

    ```python
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. 上傳預先計算公平計量。
    
    現在，匯入 `azureml.contrib.fairness` 封裝以執行上傳：

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    建立實驗，然後執行並將儀表板上傳至其中：
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


    類似上一節，您可以遵循 Azure Machine Learning studio 中所述的其中一個路徑（透過**實驗**或**模型**）來存取視覺效果儀表板，並根據公平和效能來比較這兩個模型。


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>上傳 unmitigated 並降低公平深入解析

您可以使用 Fairlearn 的[緩和演算法](https://fairlearn.github.io/user_guide/mitigation.html)，比較其產生的降低模型與原始 unmitigated 模型，並在比較的模型之間流覽效能/公平取捨。

若要查看示範如何使用[格線搜尋](https://fairlearn.github.io/user_guide/mitigation.html#grid-search)風險降低演算法的範例（這會建立具有不同公平和效能取捨的緩和模型集合），請參閱此[範例筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb)。 

在單一回合中上傳多個模型的公平深入解析，可讓您根據公平和效能來比較模型。 您可以進一步按一下模型比較圖表中顯示的任何模型，以便查看特定模型的詳細公平深入解析。


[![模型比較 Fairlearn 儀表板](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>後續步驟

[深入瞭解模型公平](concept-fairness-ml.md)

[查看 Azure Machine Learning 的公平範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
