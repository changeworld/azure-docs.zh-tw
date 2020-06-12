---
title: 記錄 ML 實驗和計量
titleSuffix: Azure Machine Learning
description: 監視您的 Azure ML 實驗並監視執行計量，以強化模型建立程序。 將記錄新增至您的定型指令碼，並檢視記錄的執行結果。  請使用 run.log、Run.start_logging 或 ScriptRunConfig。
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/12/2020
ms.custom: seodec18
ms.openlocfilehash: 9613b74b727d27bd47a05fadc1398bf898f667a5
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835713"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>監視 Azure ML 實驗的執行和計量
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

追蹤您的實驗並監視執行計量，以強化模型建立程序。 在本文中，您將了解如何將記錄程式碼新增至您的定型指令碼、提交實驗執行、監視該執行，以及查看 Azure Machine Learning 中的結果。

> [!NOTE]
> Azure Machine Learning 也可能在定型期間記錄來自其他來源的資訊，例如自動化機器學習執行，或執行定型作業的 Docker 容器。 文件中並未列載這些記錄。 如果您遇到問題並聯繫 Microsoft 支援服務，他們在進行疑難排解時可能會使用這些記錄。

> [!TIP]
> 本文件中的資訊主要適用於想要監視模型定型程序的資料科學家和開發人員。 如果您是系統管理員，且想要從 Azure Machine Learning 監視資源使用量和事件 (例如配額、已完成的定型執行或已完成的模型部署)，請參閱[監視 Azure Machine Learning](monitor-azure-machine-learning.md)。

## <a name="available-metrics-to-track"></a>要追蹤的可用計量

訓練實驗時，可以將下列計量新增到執行中。 若要檢視可在回合中追蹤之內容的更詳細清單，請參閱 [Run 類別參考文件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) \(英文\)。

|類型| Python 函式 | 注意|
|----|:----|:----|
|純量值 |函式：<br>`run.log(name, value, description='')`<br><br>範例：<br>run.log("accuracy", 0.95) |使用指定名稱將數字或字串值記錄到執行中。 將計量記錄到執行中，會導致該計量儲存在實驗的執行記錄中。  您可以在執行中多次記錄相同的計量，結果會視為該計量的向量。|
|清單|函式：<br>`run.log_list(name, value, description='')`<br><br>範例：<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | 使用指定名稱將值清單記錄到執行中。|
|資料列|函式：<br>`run.log_row(name, description=None, **kwargs)`<br>範例：<br>run.log_row("Y over X", x=1, y=0.4) | 使用 log_row 建立計量，並於其中包含 kwargs 中描述的多個資料行。 每個具名的參數都會產生一個具有指定值的資料行。  可以呼叫一次 *log_row* 以記錄任意 Tuple，或者在迴圈中多次呼叫以產生完整的資料表。|
|Table|函式：<br>`run.log_table(name, value, description='')`<br><br>範例：<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | 使用指定名稱將字典物件記錄到執行中。 |
|影像|函式：<br>`run.log_image(name, path=None, plot=None)`<br><br>範例：<br>`run.log_image("ROC", plot=plt)` | 將映像記錄到執行記錄中。 使用 log_image 將 .PNG 影像檔案或 matplotlib 繪圖記錄到執行中。  這些映像會顯示在執行記錄中，並可供比較。|
|標記執行|函式：<br>`run.tag(key, value=None)`<br><br>範例：<br>run.tag("selected", "yes") | 使用字串索引鍵和可選字串值標記執行。|
|上傳檔案或目錄|函式：<br>`run.upload_file(name, path_or_stream)`<br> <br> 範例：<br>run.upload_file("best_model.pkl", "./model.pkl") | 將檔案上傳到執行記錄。 執行會自動擷取特定輸出目錄中的檔案，對於大多數執行類型，預設為「./outputs」。  只有在需要上傳其他檔案或未指定輸出目錄時，才使用 upload_file。 我們建議在名稱中加上 `outputs`，以便將其上傳到輸出目錄。 您可以透過呼叫 `run.get_file_names()`，列出與該執行記錄相關聯的所有檔案|

> [!NOTE]
> 純量、清單、資料列和資料表的計量可具有以下類型：浮點數、整數或字串。

## <a name="choose-a-logging-option"></a>選擇記錄選項

如果您想要追蹤或監視您的實驗，必須新增程式碼，以在提交執行時開始記錄。 以下是觸發執行提交的方法：
* __Run.start_logging__ - 將記錄函式加入您的定型指令碼中，並在指定的實驗中，啟動互動式記錄工作階段。 **start_logging** 可建立互動式執行，以便用於 Notebook 等環境中。 工作階段期間記錄的所有計量都會加入實驗的執行記錄中。
* __ScriptRunConfig__ - 將記錄函式加入定型指令碼中，並在執行時載入整個指令碼資料夾。  **ScriptRunConfig** 是一種用於設定指令碼執行設定的類別。 使用此選項，您可以新增監視程式碼以通知完成，或取得視覺小工具以進行監視。
* __設計工具記錄__ - 使用__執行 Python 指令碼__模組，將記錄函式新增至拖放設計工具管線。 請將 Python 程式碼新增至記錄設計工具實驗。 

## <a name="set-up-the-workspace"></a>設定工作區
新增記錄並提交實驗之前，您必須先設定工作區。

1. 使用工作區。 若要深入了解如何設定工作區設定，請參閱[工作區組態檔](how-to-configure-environment.md#workspace)。

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_ws)]


## <a name="option-1-use-start_logging"></a>選項 1：使用 start_logging

**start_logging** 可建立互動式執行，以便用於 Notebook 等環境中。 工作階段期間記錄的所有計量都會加入實驗的執行記錄中。

以下範例在本機 Jupyter Notebook 中，將簡單的 sklearn Ridge 模型定型。 若要深入了解將實驗提交至不同的環境，請參閱[使用 Azure Machine Learning 設定模型定型的計算目標](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets)。

### <a name="load-the-data"></a>載入資料

此範例會使用糖尿病資料集，這是 scikit-learn 隨附的知名小型資料集。 此儲存格會載入資料集，並將其分割成隨機定型和測試集。

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_data)]

### <a name="add-tracking"></a>新增追蹤
使用 Azure Machine Learning SDK 新增實驗定型，並將保存的模型上傳至實驗執行記錄中。 以下程式碼新增標記、記錄，並將模型檔案上傳到實驗執行中。

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=create_experiment)]

指令碼以 ```run.complete()``` 結尾，完成時會標記執行。  此函式通常用於互動式 Notebook 案例中。

## <a name="option-2-use-scriptrunconfig"></a>選項 2：使用 ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) 是一種用來設定指令碼執行設定的類別。 使用此選項，您可以新增監視程式碼以通知完成，或取得視覺小工具以進行監視。

此範例擴展上述的基本 sklearn Ridge 模型。 它執行簡單的參數掃掠以掃掠模型的 Alpha 值，以在實驗下的執行中擷取計量和定型的模型。 此範例會針對使用者管理的環境在本機上執行。 

1. 建立定型指令碼 `train.py`。

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]

2. `train.py` 指令碼參考 `mylib.py`，可讓您取得要在 Ridge 模型中使用的 Alpha 值清單。

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/mylib.py)] 

3. 設定使用者管理的本機環境。

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=user_managed_env)]


4. 提交要在使用者管理環境中執行的 ```train.py``` 指令碼。 提交整個指令碼資料夾進行定型，包括 ```mylib.py``` 檔案。

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

## <a name="option-3-log-designer-experiments"></a>選項 3：記錄設計工具實驗

使用__執行 Python 指令碼__模組，將記錄邏輯新增至您的設計工具實驗。 您可以使用此工作流程記錄任何值，但特別適合用來記錄來自__評估模型__模組的計量，以追蹤不同執行間的模型效能。

1. 將__執行 Python 指令碼__模組連線至__評估模型__模組的輸出。

    ![將執行 Python 指令碼模組連線至評估模型模組](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. 將下列程式碼貼到__執行 Python 指令碼__程式碼編輯器中，以記錄定型模型的平均絕對誤差：

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1 = None, dataframe2 = None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')

        from azureml.core import Run

        run = Run.get_context()

        # Log the mean absolute error to the current run to see the metric in the module detail pane.
        run.log(name='Mean_Absolute_Error', value=dataframe1['Mean_Absolute_Error'])

        # Log the mean absolute error to the parent run to see the metric in the run details page.
        run.parent.log(name='Mean_Absolute_Error', value=dataframe1['Mean_Absolute_Error'])
    
        return dataframe1,
    ```

## <a name="manage-a-run"></a>管理執行

[啟動、監視和取消定型執行](how-to-manage-runs.md)一文會詳細說明用來管理實驗的特定 Azure Machine Learning 工作流程。

## <a name="view-run-details"></a>檢視執行詳細資料

### <a name="view-activequeued-runs-from-the-browser"></a>從瀏覽器檢視作用中/已排入佇列的執行

用來定型模型的計算目標是共用資源。 因此，這些資源在指定的時間可能會有多個已排入佇列或作用中的執行。 若要從瀏覽器查看特定計算目標的執行，請使用下列步驟：

1. 從 [Azure Machine Learning Studio](https://ml.azure.com/) 選取您的工作區，然後在頁面左側選取 [計算]。

1. 選取 [定型叢集]，以顯示用於定型的計算目標清單。 然後，選取叢集。

    ![選取定型叢集](./media/how-to-track-experiments/select-training-compute.png)

1. 選取 [執行]。 使用此叢集的執行清單隨即顯示。 若要檢視特定執行的詳細資料，請使用 [執行] 資料行中的連結。 若要檢視實驗的詳細資料，請使用 [實驗] 資料行中的連結。

    ![選取定型叢集的執行](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > 執行可以包含子執行，因此一個定型作業可能會產生多個項目。

執行完成後，就不會再顯示於此頁面上。 若要檢視已完成執行的資訊，請瀏覽 Studio 的 [實驗] 區段，然後選取實驗並加以執行。 如需詳細資訊，請參閱[查詢執行計量](#queryrunmetrics)一節。

### <a name="monitor-run-with-jupyter-notebook-widget"></a>使用 Jupyter 筆記本小工具監視執行
當您使用 **ScriptRunConfig** 方法提交執行時，您可以使用 [Jupyter 小工具](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)查看執行的進度。 就像執行提交一樣，小工具為非同步工作，並每隔 10 至 15 秒提供即時更新，直到工作完成為止。

1. 等待執行完成時，檢視 Jupyter 小工具。

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Jupyter Notebook 小工具的螢幕擷取畫面](./media/how-to-track-experiments/run-details-widget.png)

   您也可以在工作區中取得相同顯示的連結。

   ```python
   print(run.get_portal_url())
   ```

2. **[適用於自動化機器學習回合]** 存取來自上一個回合的圖表。 請將 `<<experiment_name>>` 取代為適當的實驗名稱：

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![適用於自動化機器學習的 Jupyter Notebook 小工具](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


若要進一步檢視管線的詳細資料，在表格中按一下您想要探索的管線，而圖表將呈現在 Azure Machine Learning Studio 的快顯視窗中。

### <a name="get-log-results-upon-completion"></a>在完成時取得記錄檔結果

模型定型和監視在背景進行，以便您可以在等待時執行其他工作。 您也可以等到模型完成定型，再執行更多程式碼。 當您使用 **ScriptRunConfig** 時，可以使用 ```run.wait_for_completion(show_output = True)``` 以顯示模型定型完成的時間。 ```show_output``` 旗標為您提供詳細資訊輸出。 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>查詢執行計量

您可以使用 ```run.get_metrics()``` 檢視定型模型的計量。 您現在可以取得上述範例中記錄的所有計量，以確定最佳模型。

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>在 [Azure Machine Learning Studio](https://ml.azure.com) 中檢視您工作區中的實驗

當實驗完成執行時，您可以瀏覽記錄的實驗執行記錄。 您可以從 [Azure Machine Learning Studio](https://ml.azure.com) 存取歷程記錄。

瀏覽至 [實驗] 索引標籤，然後選取您的實驗。 您會進入 [實驗執行] 儀表板，您可以在其中查看每次執行所記錄的追蹤計量和圖表。 在此情況下，我們會記錄 MSE 和 Alpha 值。

  ![Azure Machine Learning Studio 中的執行詳細資料](./media/how-to-track-experiments/experiment-dashboard.png)

您可以向下切入至特定執行以檢視其輸出或記錄，或下載您所提交之實驗的快照集，以便與其他人共用實驗資料夾。

### <a name="viewing-charts-in-run-details"></a>檢視執行詳細資料中的圖表

有多種方式可以在執行期間使用記錄 API 記錄不同類型的計量，並以 Azure Machine Learning Studio 中的圖表形式加以檢視。

|記錄的值|程式碼範例| 在入口網站中檢視|
|----|----|----|
|記錄數值的陣列| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|單一變數的折線圖|
|透過重複使用的相同計量名稱，記錄單一數值 (例如，從 for 迴圈中)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| 單一變數的折線圖|
|使用 2 個數字資料行重複記錄資料列|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|兩個變數的折線圖|
|使用 2 個數字資料行記錄資料表|`run.log_table(name='Sine Wave', value=sines)`|兩個變數的折線圖|


## <a name="example-notebooks"></a>Notebook 範例
下列 Notebook 示範了此文章中說明的概念：
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

請嘗試下列步驟，了解如何使用適用於 Python 的 Azure Machine Learning SDK：

* 關於如何註冊最佳模型，並在教學課程中加以部署的範例，請參閱[使用 Azure Machine Learning 定型映像分類模型](tutorial-train-models-with-aml.md)。

* 了解如何[使用 Azure Machine Learning 定型 PyTorch 模型](how-to-train-pytorch.md)。
