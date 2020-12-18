---
title: 監視和查看 ML 執行記錄 & 計量
titleSuffix: Azure Machine Learning
description: 監視您的 ML 實驗，並使用 Jupyter widget 和 Azure Machine Learning studio 來查看執行計量。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 0dd5f6a48175bad35b37155c8ff881e352922ca7
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674460"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>監視和查看 ML 執行記錄和計量

瞭解如何監視 Azure Machine Learning 執行和查看其記錄。 

當您執行實驗時，系統會為您串流記錄和計量。  此外，您可以新增自己的。  若要深入瞭解，請參閱 [在 AZURE ML 定型回合中啟用記錄](how-to-track-experiments.md)。

這些記錄可協助您診斷執行的錯誤和警告。 效能標準（例如參數和模型精確度）可協助您追蹤和監視您的執行。

在本文中，您將瞭解如何使用下列方法來查看記錄：

> [!div class="checklist"]
> * 在 studio 中執行監視
> * 使用 Jupyter Notebook widget 監視執行
> * 監視自動化機器學習執行
> * 完成時查看輸出記錄
> * 在 studio 中查看輸出記錄

如需如何管理實驗的一般資訊，請參閱 [啟動、監視和取消定型](how-to-manage-runs.md)回合。

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>使用 Jupyter 筆記本 widget 執行監視

當您使用 **ScriptRunConfig** 方法來提交執行時，可以使用 [Jupyter 小工具](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py)來監看執行的進度。 就像執行提交一樣，小工具為非同步工作，並每隔 10 至 15 秒提供即時更新，直到工作完成為止。

等待執行完成時，檢視 Jupyter 小工具。
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Jupyter Notebook 小工具的螢幕擷取畫面](./media/how-to-track-experiments/run-details-widget.png)

您也可以在工作區中取得相同顯示的連結。

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>監視自動化機器學習執行

針對自動化機器學習執行，若要從先前的執行存取圖表，請以 `<<experiment_name>>` 適當的實驗名稱取代：

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![適用於自動化機器學習的 Jupyter Notebook 小工具](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>完成時顯示輸出

當您使用 **ScriptRunConfig** 時，可以使用 ```run.wait_for_completion(show_output = True)``` 以顯示模型定型完成的時間。 ```show_output``` 旗標為您提供詳細資訊輸出。 如需詳細資訊，請參閱 [如何啟用記錄](how-to-track-experiments.md#scriptrun-logs)的 ScriptRunConfig 一節。

<a id="queryrunmetrics"></a>

## <a name="query-run-metrics"></a>查詢執行計量

您可以使用 ```run.get_metrics()``` 檢視定型模型的計量。 例如，您可以使用此範例搭配上述範例，藉由尋找具有最低 mean 平方誤差 (mse) 值的模型，來判斷最佳的模型。

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-records-in-the-studio"></a>在 studio 中查看執行記錄

您可以在 [Azure Machine Learning studio](https://ml.azure.com)中流覽已完成的執行記錄（包括記錄的計量）。

流覽至 [ **實驗** ] 索引標籤。若要跨實驗查看工作區中的所有執行，請選取 [ **所有執行** ] 索引標籤。您可以在頂端功能表列中套用實驗篩選，以向下切入特定實驗的執行。

針對個別實驗視圖，請選取 [ **所有實驗** ] 索引標籤。在 [實驗執行] 儀表板上，您可以看到每個執行的追蹤計量和記錄。 

您也可以編輯執行清單資料表來選取多個回合，並顯示最新的執行記錄值的最大值或最大值。 自訂您的圖表，以比較記錄的計量值和跨多個執行的匯總。 

![Azure Machine Learning Studio 中的執行詳細資料](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="format-charts"></a>格式化圖表 

在記錄 Api 中使用下列方法，以影響度量視覺效果。

|記錄的值|程式碼範例| 入口網站中的格式|
|----|----|----|
|記錄數值的陣列| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|單一變數的折線圖|
|透過重複使用的相同計量名稱，記錄單一數值 (例如，從 for 迴圈中)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| 單一變數的折線圖|
|使用 2 個數字資料行重複記錄資料列|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|兩個變數的折線圖|
|使用 2 個數字資料行記錄資料表|`run.log_table(name='Sine Wave', value=sines)`|兩個變數的折線圖|


### <a name="view-log-files-for-a-run"></a>查看執行的記錄檔 

記錄檔是用來偵測 Azure ML 工作負載的重要資源。 向下切入到特定執行，以查看其記錄和輸出：  

1. 流覽至 [ **實驗** ] 索引標籤。
1. 選取特定執行的 runID。
1. 選取頁面頂端的 **輸出和記錄** 。

:::image type="content" source="media/how-to-monitor-view-training-logs/view-logs.png" alt-text="執行之輸出和記錄區段的螢幕擷取畫面":::

下表顯示您將在本節中看到的資料夾中的記錄檔內容。

> [!NOTE]
> 即使 skimmingYou 不一定會在每次執行時看到每個檔案，使用者也應注意的資訊。 例如，20_image_build_log * .txt 只會在建立新映射時出現 (例如，當您變更環境) 時。

#### <a name="azureml-logs-folder"></a>`azureml-logs` 資料夾

|檔案  |描述  |
|---------|---------|
|20_image_build_log.txt     | 適用于定型環境的 Docker 映射建立記錄（選擇性），每次執行一個。 只有在更新您的環境時才適用。 否則 AML 會重複使用快取的映射。 如果成功，則包含對應映射的映射登錄詳細資料。         |
|55_azureml-執行-<node_id # C1.txt     | 主機工具的 stdout/stderr 記錄，每個節點一個。 將映射提取至計算目標。 請注意，只有在您擁有受保護的計算資源之後，才會顯示此記錄。         |
|65_job_prep-<node_id # C1.txt     |   作業準備腳本的 stdout/stderr 記錄，每個節點一個。 下載您的程式碼，以計算目標並資料存放區 (是否) 要求。       |
|70_driver_log (_x) .txt      |  AML/stderr 控制項腳本和客戶訓練腳本的 stdout/stderr 記錄，每個進程一次。 **這是您的腳本的標準輸出。這是您的程式碼記錄檔 (例如，print 語句) 顯示。** 在大部分的情況下，您會在這裡監視記錄。       |
|70_mpi_log.txt     |   MPI 架構記錄檔（選擇性），每次執行一個記錄。 僅適用于 MPI 執行。   |
|75_job_post-<node_id # C1.txt     |  作業版本腳本的 stdout/stderr 記錄，每個節點一個記錄。 傳送記錄，將計算資源釋放回 Azure。        |
|process_info.js開啟      |   顯示正在哪個節點上執行的進程。  |
|process_status.js開啟      | 顯示進程狀態，亦即，如果進程未啟動、執行中或已完成。         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` 資料夾

|檔案  |描述  |
|---------|---------|
|110_azureml .log      |         |
|job_prep_azureml .log     |   作業準備的系統記錄檔        |
|job_release_azureml .log     | 作業版本的系統記錄檔        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` 資料夾

啟用側車時，會在側車容器內執行作業準備和作業發行腳本。  每個節點都有一個資料夾。 

|檔案  |描述  |
|---------|---------|
|start_cms.txt     |  側車容器啟動時啟動的進程記錄       |
|prep_cmd.txt      |   CoNtextManagers 在執行時輸入的記錄檔 `job_prep.py` (部分將會串流至 `azureml-logs/65-job_prep`)        |
|release_cmd.txt     |  ComtextManagers 在執行時結束的記錄 `job_release.py`        |

#### <a name="other-folders"></a>其他資料夾

針對多個計算叢集上的作業定型，每個節點 IP 都會有記錄。 每個節點的結構與單一節點工作相同。 針對整體執行、stderr 和 stdout 記錄，還有一個額外的 [記錄檔] 資料夾。

Azure Machine Learning 在定型期間記錄來自各種來源的資訊，例如 AutoML 或執行定型作業的 Docker 容器。 其中有許多記錄並未記載。 如果您遇到問題並聯繫 Microsoft 支援服務，他們在進行疑難排解時可能會使用這些記錄。

## <a name="monitor-a-compute-cluster"></a>監視計算叢集

若要從您的瀏覽器監視特定計算目標的執行，請使用下列步驟：

1. 在 [Azure Machine Learning studio](https://ml.azure.com/)中，選取您的工作區，然後從頁面左側選取 [ __計算__ ]。

1. 選取 [定型叢集]，以顯示用於定型的計算目標清單。 然後，選取叢集。

    ![選取定型叢集](./media/how-to-track-experiments/select-training-compute.png)

1. 選取 [執行]。 使用此叢集的執行清單隨即顯示。 若要檢視特定執行的詳細資料，請使用 [執行] 資料行中的連結。 若要檢視實驗的詳細資料，請使用 [實驗] 資料行中的連結。

    ![選取定型叢集的執行](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > 由於訓練計算目標是共用資源，因此可以在指定時間將多個執行排入佇列或作用中。
    > 
    > 執行可以包含子執行，因此一個定型作業可能會產生多個項目。

執行完成後，就不會再顯示於此頁面上。 若要檢視已完成執行的資訊，請瀏覽 Studio 的 [實驗] 區段，然後選取實驗並加以執行。 如需詳細資訊，請參閱瞭解 [已完成執行的度量](#view-the-experiment-in-the-web-portal)區段。


## <a name="next-steps"></a>後續步驟

請嘗試下列後續步驟，以瞭解如何使用 Azure Machine Learning：

* 瞭解如何 [在 Azure Machine Learning 設計工具中追蹤實驗和啟用記錄](how-to-track-designer-experiments.md)。

* 關於如何註冊最佳模型，並在教學課程中加以部署的範例，請參閱[使用 Azure Machine Learning 定型映像分類模型](tutorial-train-models-with-aml.md)。