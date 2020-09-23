---
title: 調試 & 針對 ML 管線進行疑難排解
titleSuffix: Azure Machine Learning
description: 在 Python 中進行 Azure Machine Learning 管線的偵錯工具。 瞭解開發管線的常見陷阱，以及協助您在遠端執行前後進行腳本的偵錯工具。 瞭解如何使用 Visual Studio Code 以互動方式來偵測機器學習管線。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: troubleshooting, devx-track-python
ms.openlocfilehash: 616cdb1d0940ea6f64c3be3d687adaa9c2a98cc2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90889975"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>機器學習管線的偵錯和疑難排解

在本文中，您將瞭解如何在[AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)和[Azure Machine Learning 設計](https://docs.microsoft.com/azure/machine-learning/concept-designer)工具中，對[機器學習管線](concept-ml-pipelines.md)進行調試和疑難排解。 資訊提供的方式如下：

## <a name="troubleshooting-tips"></a>疑難排解提示

下表包含管線開發期間的常見問題，並提供潛在的解決方案。

| 問題 | 可能的解決方法 |
|--|--|
| 無法將資料傳遞至 `PipelineData` 目錄 | 確定您已在腳本中建立一個目錄，該目錄會對應至管線預期步驟輸出資料的位置。 在大部分的情況下，輸入引數會定義輸出目錄，然後您會明確建立目錄。 使用 `os.makedirs(args.output_dir, exist_ok=True)` 建立輸出目錄。 如需顯示此設計模式的評分腳本範例，請參閱 [教學](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) 課程。 |
| 相依性 bug | 如果您在本機測試時看到遠端管線中的相依性錯誤，請確認您的遠端環境相依性和版本符合您測試環境中的相依性錯誤。  (查看 [環境建立、快取和重複使用](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| 計算目標有不明確的錯誤 | 請嘗試刪除並重新建立計算目標。 重新建立計算目標很快速，可解決一些暫時性問題。 |
| 管線未重複使用步驟 | 預設會啟用步驟重複使用，但請確定您未在管線步驟中將它停用。 如果已停用重複使用， `allow_reuse` 則步驟中的參數會設定為 `False` 。 |
| 不必要地重新執行管線 | 為確保只有在基礎資料或腳本變更時才會重新執行步驟，請將每個步驟的原始程式碼目錄分離。 如果您使用相同的來原始目錄進行多個步驟，可能會遇到不必要的重新執行。 `source_directory`在管線步驟物件上使用參數，以指向該步驟的隔離目錄，並確保您不會 `source_directory` 針對多個步驟使用相同的路徑。 |
| 逐步減緩定型 epoch 或其他迴圈行為 | 請嘗試將任何檔案寫入（包括記錄）切換 `as_mount()` 至 `as_upload()` 。 **掛接**模式使用遠端虛擬化檔案系統，並在每次將檔案附加至時，上傳整個檔案。 |

## <a name="debugging-techniques"></a>調試技術

有三種主要的方法可以用來調試管線： 

* 在本機電腦上進行個別管線步驟的偵錯工具
* 使用記錄和 Application Insights 來隔離及診斷問題來源
* 將遠端偵錯程式附加至在 Azure 中執行的管線

### <a name="debug-scripts-locally"></a>在本機調試腳本

管線中最常見的失敗之一，是網域腳本不會如預期執行，或在遠端計算內容中包含難以進行調試的執行階段錯誤。

管線本身無法在本機執行，但是在您的本機電腦上隔離執行腳本可讓您更快速地進行偵錯工具，因為您不需要等待計算和環境建立程式。 需要進行一些開發工作才能執行這項作業：

* 如果您的資料位於雲端資料存放區，您將需要下載資料，並將其提供給您的腳本。 使用較小的資料範例，是在執行時間上剪下並快速取得腳本行為意見的好方法
* 如果您嘗試模擬中繼管線步驟，您可能需要手動建立特定腳本預期來自先前步驟的物件類型。
* 您也必須定義自己的環境，並複寫您的遠端計算環境中定義的相依性

在您的本機環境中執行腳本設定之後，您就可以更輕鬆地執行偵錯工具，例如：

* 附加自訂的 debug configuration
* 暫停執行並檢查物件狀態
* 捕捉在執行時間之前不會公開的類型或邏輯錯誤

> [!TIP] 
> 一旦您可以驗證腳本是否如預期般執行，下一步就是在單一步驟管線中執行腳本，然後再嘗試使用多個步驟在管線中執行腳本。

## <a name="configure-write-to-and-review-pipeline-logs"></a>設定、寫入和審核管線記錄

在本機測試腳本是一個很好的方法，可在您開始建立管線之前，先將主要的程式碼片段和複雜邏輯進行偵錯工具，但在某些情況下，您可能需要在實際管線執行期間先行編譯腳本，尤其是在診斷管線步驟之間的互動時所發生的行為時。 建議您 `print()` 在步驟腳本中使用語句，讓您可以在遠端執行期間查看物件狀態和預期的值，類似于如何進行 JavaScript 程式碼的偵錯工具。

### <a name="logging-options-and-behavior"></a>記錄選項和行為

下表提供管線的不同偵錯工具選項的資訊。 這並不是詳盡的清單，因為除了此處顯示的 Azure Machine Learning、Python 和 OpenCensus 之外，還有其他選項存在。

| 程式庫                    | 類型   | 範例                                                          | Destination                                  | 資源                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | 計量 | `run.log(name, val)`                                             | Azure Machine Learning 入口網站 UI             | [如何追蹤實驗](how-to-track-experiments.md)<br>[azureml. 核心. 執行類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Python 列印/記錄    | 記錄檔    | `print(val)`<br>`logging.info(message)`                          | 驅動程式記錄，Azure Machine Learning 設計工具 | [如何追蹤實驗](how-to-track-experiments.md)<br><br>[Python 記錄](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | 記錄檔    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights-追蹤                | [對 Application Insights 中的管線進行偵錯](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure 監視器匯出工具](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python 記錄操作手冊](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>記錄選項範例

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning 設計工具

針對在設計工具中建立的管線，您可以在 [撰寫] 頁面或 [管線執行詳細資料] 頁面中找到 **70_driver_log** 檔案。

### <a name="enable-logging-for-real-time-endpoints"></a>啟用即時端點的記錄

為了在設計工具中疑難排解和偵測即時端點，您必須使用 SDK 啟用應用程式深入解析記錄。 記錄功能可讓您針對模型部署和使用問題進行疑難排解及進行調試。 如需詳細資訊，請參閱已 [部署模型的記錄](how-to-enable-logging.md#logging-for-deployed-models)。 

### <a name="get-logs-from-the-authoring-page"></a>從撰寫頁面取得記錄

當您提交管線執行並停留在 [撰寫中] 頁面時，您可以在每個模組執行完成時，找到針對每個模組產生的記錄檔。

1. 選取在撰寫畫布中完成執行的模組。
1. 在模組的右窗格中，移至 [  **輸出 + 記錄** ] 索引標籤。
1. 展開右窗格，然後選取 [ **70_driver_log.txt** ]，以在瀏覽器中查看檔案。 您也可以在本機下載記錄。

    ![設計工具中的展開輸出窗格](./media/how-to-debug-pipelines/designer-logs.png)？ view = azure-ml-.py&preserve-view = true) ？ view = azure-ml-.py&preserve = true) 

### <a name="get-logs-from-pipeline-runs"></a>從管線執行取得記錄

您也可以在 [管線執行詳細資料] 頁面中找到特定回合的記錄檔，這可以在 studio 的 [ **管線** ] 或 [ **實驗** ] 區段中找到。

1. 選取在設計工具中建立的管線執行。

    ![管線執行頁面](./media/how-to-debug-pipelines/designer-pipelines.png)

1. 在預覽窗格中選取模組。
1. 在模組的右窗格中，移至 [  **輸出 + 記錄** ] 索引標籤。
1. 展開右窗格以查看瀏覽器中的 **70_driver_log.txt** 檔案，或選取要在本機下載記錄檔的檔案。

> [!IMPORTANT]
> 若要從 [管線執行詳細資料] 頁面更新管線，您必須將管線執行 **複製** 到新的管線草稿。 管線執行是管線的快照。 它類似于記錄檔，而且無法變更。 

## <a name="application-insights"></a>Application Insights
如需以這種方式使用 OpenCensus Python 程式庫的詳細資訊，請參閱本指南： [在 Application Insights 中進行機器學習管線的偵錯工具和疑難排解](how-to-debug-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>使用 Visual Studio Code 的互動式調試

在某些情況下，您可能需要以互動方式來偵測 ML 管線中使用的 Python 程式碼。 藉由使用 Visual Studio Code (VS Code) 和 debugpy，您可以在定型環境中執行時附加至程式碼。 如需詳細資訊，請造訪 [VS Code 指南中的互動式調試](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines)程式。

## <a name="next-steps"></a>下一步

* 請參閱 SDK 參考以取得 [azureml-管線-核心](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py&preserve-view=true) 封裝和 [azureml 管線-步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py&preserve-view=true) 套件的說明。

* 請參閱設計工具 [例外狀況和錯誤碼](algorithm-module-reference/designer-error-codes.md)的清單。
