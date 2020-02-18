---
title: 教學課程：在 Python 中將您的第一個 Azure ML 模型定型
titleSuffix: Azure Machine Learning
description: 在本教學課程中，您將了解 Azure Machine Learning 中的基本設計模式，並根據糖尿病資料集來定型簡單的 Scikit-learn 模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: aa90655ecb14abe38ec8fdfc6c18e7d292abbef3
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116528"
---
# <a name="tutorial-train-your-first-ml-model"></a>教學課程：定型第一個 ML 模型

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本教學課程是**兩部分教學課程系列的第二部分**。 在上一個教學課程中，您已[建立工作區並選擇了開發環境](tutorial-1st-experiment-sdk-setup.md)。 在本教學課程中，您將了解 Azure Machine Learning 中的基本設計模式，並根據糖尿病資料集來定型簡單的 Scikit-learn 模型。 完成本教學課程之後，您將具備 SDK 的實際知識，進而開發更複雜的實驗和工作流程。

在本教學課程中，您會了解下列工作：

> [!div class="checklist"]
> * 連結您的工作區並建立實驗
> * 載入資料並定型 Scikit-learn 模型
> * 在入口網站中查看定型結果
> * 擷取最佳模型

## <a name="prerequisites"></a>Prerequisites

唯一的必要條件是執行本教學課程的第一個部分：[設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)。

在本教學課程的這個部分中，您會執行第一部分結尾處所開啟範例 Jupyter 筆記本 tutorials/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb  中的程式碼。 本文會逐步解說此 Notebook 中的相同程式碼。

## <a name="open-the-notebook"></a>開啟 Notebook

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com/)。

1. 開啟資料夾中的 **tutorial-1st-experiment-sdk-train.ipynb**，如[第一部分](tutorial-1st-experiment-sdk-setup.md#open)所說明。


> [!Warning]
> 請**不要**在 Jupyter 介面中建立新的  Notebook！ 筆記本 tutorials/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb  包含本教學課程**所需的所有程式碼和資料**。

## <a name="connect-workspace-and-create-experiment"></a>連結工作區並建立實驗

> [!Important]
> 本文的其餘部分包含與您在 Notebook 中所見相同的內容。  
>
> 如果您想要在執行程式碼時進行閱讀，請立即切換到 Jupyter Notebook。 
> 若要在 Notebook 中執行單一程式碼資料格，請按一下程式碼資料格，然後按 **Shift+Enter**。 或者，從頂端工具列中選擇 [全部執行]  ，以執行整個 Notebook。

使用 `from_config().` 函式匯入 `Workspace` 類別，並從 `config.json` 檔案載入您的訂用帳戶資訊。根據預設，此功能會在目前的目錄中尋找 JSON 檔案，但您也可以使用 `from_config(path="your/file/path")` 指定路徑參數來指向檔案。 在雲端 Notebook 伺服器中，檔案會自動位於根目錄中。

如果下列程式碼要求額外的驗證，只需在瀏覽器中貼上連結並輸入驗證權杖即可。

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

現在，您可以在工作區中建立實驗。 實驗是另一個基本雲端資源，代表著一組試用功能 (個別的模型執行)。 在本教學課程中，您會使用實驗來建立執行，並在 Azure Machine Learning Studio 中追蹤您的模型訓練。 參數包含您的工作區參考，以及實驗的字串名稱。


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>載入資料並準備進行訓練

在此教學課程中，您會使用糖尿病資料集，該資料集使用年齡、性別和 BMI 等特質來預測糖尿病的進展。 從 [Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/)類別載入資料，然後使用 `train_test_split()` 將其分割成訓練集和測試集。 此函式會隔離資料，讓模型有未顯示的資料可用於測試下列訓練。


```python
from azureml.opendatasets import Diabetes
from sklearn.model_selection import train_test_split

x_df = Diabetes.get_tabular_dataset().to_pandas_dataframe().dropna()
y_df = x_df.pop("Y")

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>將模型定型

針對小規模的訓練，您可以輕鬆地在本機進行簡單的 Scikit-learn 模型定型，但當您使用數十種不同的功能排列和超參數設定來定型許多反覆項目時，就會很容易失去已定型模型及其定型方式的追蹤。 下列設計模式說明如何利用 SDK 輕鬆地在雲端中追蹤您的訓練。

建立指令碼，以透過不同的超參數 Alpha 值，在迴圈中訓練 Ridge 模型。


```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.externals import joblib
import math

alphas = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]

for alpha in alphas:
    run = experiment.start_logging()
    run.log("alpha_value", alpha)

    model = Ridge(alpha=alpha)
    model.fit(X=X_train, y=y_train)
    y_pred = model.predict(X=X_test)
    rmse = math.sqrt(mean_squared_error(y_true=y_test, y_pred=y_pred))
    run.log("rmse", rmse)

    model_name = "model_alpha_" + str(alpha) + ".pkl"
    filename = "outputs/" + model_name

    joblib.dump(value=model, filename=filename)
    run.upload_file(name=model_name, path_or_stream=filename)
    run.complete()
```

上述程式碼會完成下列動作：

1. 針對 `alphas` 陣列中的每個 Alpha 超參數值，在實驗中建立新的執行。 系統會記錄 Alpha 值，以區別每項執行。
1. 在每項執行中，Ridge 模型都會進行具現化、定型，以及用來執行預測。 並針對實際值與預測值計算均方根誤差，然後記錄到執行中。 此時，執行會針對 Alpha 值和 RMSE 精確度附加中繼資料。
1. 接下來，每個執行的模型會進行序列化並上傳至執行。 這可讓您從入口網站中的執行中下載模型檔案。
1. 在每個反覆項目結束時，藉由呼叫 `run.complete()` 來完成執行。

訓練完成之後，呼叫 `experiment` 變數可提取入口網站中實驗的連結。

```python
experiment
```

<table style="width:100%"><tr><th>名稱</th><th>工作區</th><th>報表頁面</th><th>文件頁面</th></tr><tr><td>diabetes-experiment</td><td>your-workspace-name</td><td>Azure 入口網站的連結</td><td>文件的連結</td></tr></table>

## <a name="view-training-results-in-portal"></a>在入口網站中查看定型結果

遵循 **Azure 入口網站的連結**，即可前往主要實驗頁面。 在這裡，您會看到實驗中的所有個別執行。 任何自訂記錄的值 (在此案例中為 `alpha_value` 和 `rmse`) 都會變成每項執行的欄位，而且也會提供給實驗頁面頂端的圖表和圖格使用。 若要將記錄的計量新增至圖表或圖格，請將滑鼠停留在其上方，按一下 [編輯] 按鈕，然後尋找您的自訂記錄計量。

當您針對數百個或數千個不同的執行來訓練模型時，此頁面可讓您輕鬆查看所定型的每個模型，特別是定型方式，以及您獨特的計量如何隨著時間變更。

![入口網站中的主要實驗頁面](./media/tutorial-1st-experiment-sdk-train/experiment-main.png)

按一下 `RUN NUMBER` 資料行中的執行編號連結，即可前往每個個別執行的頁面。 預設的 [詳細資料]  索引標籤會顯示每項執行的詳細資訊。 流覽至 [輸出]  索引標籤，您會看到每次訓練反覆項目時，針對模型上傳至執行的 `.pkl` 檔案。 您可以在這裡下載模型檔案，而不必以手動方式重新進行定型。

![入口網站中的執行詳細資料頁面](./media/tutorial-1st-experiment-sdk-train/model-download.png)

## <a name="get-the-best-model"></a>取得最佳模型

除了能夠在入口網站下載實驗中的模型檔案之外，您也能夠以程式設計方式下載這些檔案。 下列程式碼會逐一查看實驗中的每項執行，並存取記錄的執行計量和執行詳細資料 (其中包含 run_id)。 這會持續追蹤最佳執行，也就是此案例中均方根誤差最小的執行。

```python
minimum_rmse_runid = None
minimum_rmse = None

for run in experiment.get_runs():
    run_metrics = run.get_metrics()
    run_details = run.get_details()
    # each logged metric becomes a key in this returned dict
    run_rmse = run_metrics["rmse"]
    run_id = run_details["runId"]

    if minimum_rmse is None:
        minimum_rmse = run_rmse
        minimum_rmse_runid = run_id
    else:
        if run_rmse < minimum_rmse:
            minimum_rmse = run_rmse
            minimum_rmse_runid = run_id

print("Best run_id: " + minimum_rmse_runid)
print("Best run_id rmse: " + str(minimum_rmse))
```

    Best run_id: 864f5ce7-6729-405d-b457-83250da99c80
    Best run_id rmse: 57.234760283951765

使用最佳執行識別碼搭配實驗物件的 `Run` 建構函式來提取個別執行。 然後呼叫 `get_file_names()`來查看可從此執行下載的所有檔案。 在此案例中，您在訓練期間只會為每個執行上傳一個檔案。

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

在執行物件上呼叫 `download()`，並指定要下載的模型檔案名稱。 根據預設，此函式會將項目下載至目前的目錄。

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>清除資源

如果您打算執行其他 Azure Machine Learning 教學課程，請不要完成本節。

### <a name="stop-the-compute-instance"></a>停止計算執行個體

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>刪除所有內容

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

您也可以保留資源群組，但刪除單一工作區。 顯示工作區屬性，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已執行下列工作：

> [!div class="checklist"]
> * 連結您的工作區並建立實驗
> * 載入資料並定型 Scikit-learn 模型
> * 在入口網站中查看定型結果並擷取模型

使用 Azure Machine Learning [部署模型](tutorial-deploy-models-with-aml.md)。
了解如何開發[自動化機器學習](tutorial-auto-train-models.md)實驗。
