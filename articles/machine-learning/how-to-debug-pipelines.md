---
title: 對機器學習管線進行調試和疑難排解
titleSuffix: Azure Machine Learning
description: 在適用于 Python 的 Azure Machine Learning SDK 中，針對機器學習管線進行 Debug 和疑難排解。 瞭解開發管線的常見陷阱，以及協助您在遠端執行之前和期間進行腳本的秘訣。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 12/12/2019
ms.openlocfilehash: 5ba26584f08e705b24749a76d6f607aa84b48fab
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76769116"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>對機器學習管線進行調試和疑難排解
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何在[AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)和[Azure Machine Learning 設計工具（預覽）](https://docs.microsoft.com/azure/machine-learning/concept-designer)中，針對[機器學習管線](concept-ml-pipelines.md)進行調試和疑難排解。

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK 中的調試和疑難排解
下列各節概述建立管線時的常見陷阱，以及用來偵測管線中執行之程式碼的不同策略。 當您無法如預期般執行管線時，請使用下列秘訣。

### <a name="testing-scripts-locally"></a>在本機測試腳本

管線中最常見的失敗之一，就是附加的腳本（資料清理腳本、計分腳本等）並未如預期執行，或包含遠端計算內容中的執行階段錯誤，這在 Azure 機器的工作區中很容易就能進行調試學習 studio。 

管線本身無法在本機執行，但在您的本機電腦上隔離執行腳本，可讓您更快速地進行調試，因為您不需要等候計算和環境建立程式。 需要進行一些開發工作才能執行此作業：

* 如果您的資料位於雲端資料存放區中，您將需要下載資料，並將其提供給您的腳本使用。 使用資料的小型範例，是在執行時間縮減並快速取得腳本行為的意見反應的好方法
* 如果您嘗試模擬中繼管線步驟，您可能需要手動建立特定腳本預期來自前一個步驟的物件類型。
* 您也需要定義您自己的環境，並複寫在遠端計算環境中定義的相依性

一旦您在本機環境上安裝了腳本，就可以更輕鬆地執行如下的偵錯工具：

* 附加自訂的 debug 設定
* 暫停執行並檢查物件狀態
* 攔截在執行時間之前不會公開的類型或邏輯錯誤

> [!TIP] 
> 一旦您可以驗證您的腳本是否如預期般執行，在嘗試使用多個步驟在管線中執行腳本之前，一個好的下一個步驟是在單一步驟管線中執行腳本。

### <a name="debugging-scripts-from-remote-context"></a>從遠端內容調試腳本

在本機測試腳本是一種很好的方法，可在您開始建立管線之前，先將主要程式碼片段和複雜邏輯進行偵錯工具，但在某些情況下，您可能需要在實際管線執行期間先行編譯腳本，特別是在診斷發生的行為時管線步驟之間的互動期間。 我們建議您在步驟腳本中使用 `print()` 語句，讓您可以在遠端執行期間看到物件狀態和預期的值，這類似于偵錯工具程式碼的方式。

記錄檔 `70_driver_log.txt` 包含： 

* 腳本執行期間所有列印的語句
* 腳本的堆疊追蹤 

若要在入口網站中尋找此檔案和其他記錄檔，請先在您的工作區中按一下管線執行。

![管線執行清單頁面](./media/how-to-debug-pipelines/pipelinerun-01.png)

流覽至管線執行詳細資料頁面。

![管線執行詳細資料頁面](./media/how-to-debug-pipelines/pipelinerun-02.png)

按一下模組以取得特定步驟。 流覽至 [**記錄**] 索引標籤。其他記錄檔包含您環境映射建立程式和步驟準備腳本的相關資訊。

![管線執行詳細資料頁面記錄索引標籤](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> 可以在工作區的 [**端點**] 索引標籤中找到*已發行管線*的執行。 在**實驗**或**管線**中可以找到*非已發行管線*的執行。

### <a name="troubleshooting-tips"></a>疑難排解提示

下表包含管線開發期間的常見問題，以及可能的解決方案。

| 問題 | 可能的解決方案 |
|--|--|
| 無法將資料傳遞至 `PipelineData` 目錄 | 請確定您已在對應至管線預期步驟輸出資料所在位置的腳本中建立目錄。 在大部分情況下，輸入引數會定義輸出目錄，然後您會明確建立目錄。 使用 `os.makedirs(args.output_dir, exist_ok=True)` 來建立輸出目錄。 如需顯示此設計模式的評分腳本範例，請參閱[教學](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script)課程。 |
| 相依性 bug | 如果您已在本機開發和測試腳本，但在管線的遠端計算上執行時發現相依性問題，請確定您的計算環境相依性和版本符合您的測試環境。 |
| 計算目標的不明確錯誤 | 刪除和重新建立計算目標可以解決計算目標的特定問題。 |
| 管線未重複使用步驟 | 預設會啟用步驟重複使用，但請確定您未在管線步驟中停用它。 如果已停用重複使用，步驟中的 `allow_reuse` 參數將會設定為 `False`。 |
| 管線重新執行不必要 | 若要確保步驟只會在基礎資料或腳本變更時重新執行，請將您的目錄與每個步驟分離。 如果您使用相同的來原始目錄進行多個步驟，您可能會遇到不必要的重新執行。 使用管線步驟物件上的 `source_directory` 參數，以指向該步驟的隔離目錄，並確保不會針對多個步驟使用相同的 `source_directory` 路徑。 |

### <a name="logging-options-and-behavior"></a>記錄選項和行為

下表提供管線的不同偵錯工具選項的資訊。 這不是詳盡的清單，因為除了此處所示的 Azure Machine Learning、Python 和 OpenCensus 以外，其他選項仍存在。

| 程式庫                    | 類型   | 範例                                                          | 目的地                                  | 資源                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | 計量 | `run.log(name, val)`                                             | Azure Machine Learning 入口網站 UI             | [如何追蹤實驗](how-to-track-experiments.md#available-metrics-to-track)<br>[azureml. core. 執行類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Python 列印/記錄    | 記錄    | `print(val)`<br>`logging.info(message)`                          | 驅動程式記錄檔，Azure Machine Learning 設計工具 | [如何追蹤實驗](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Python 記錄](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | 記錄    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights-追蹤                | [Application Insights 中的 Debug 管線](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure 監視器匯出工具](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python 記錄操作手冊](https://docs.python.org/3/howto/logging-cookbook.html) |

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

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>在 Azure Machine Learning 設計工具中進行調試和疑難排解（預覽）

本節提供如何在設計工具中針對管線進行疑難排解的總覽。
針對在設計工具中建立的管線，您可以在 [撰寫] 頁面或 [管線執行詳細資料] 頁面中找到**記錄**檔。

### <a name="access-logs-from-the-authoring-page"></a>從撰寫頁面存取記錄

當您提交管線執行並停留在 [撰寫中] 頁面時，您可以找到為每個模組產生的記錄檔。

1. 選取撰寫畫布中的任何模組。
1. 在 [屬性] 窗格中，移至 [**記錄**] 索引標籤。
1. 選取記錄檔 `70_driver_log.txt`

    ![撰寫頁面模組記錄](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>從管線執行存取記錄

您也可以在 [管線執行詳細資料] 頁面的 **[管線] 或 [** **實驗**] 區段中，找到特定執行的記錄檔。

1. 選取在設計工具中建立的管線執行。
    ![管線執行頁面](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. 在預覽窗格中選取任何模組。
1. 在 [屬性] 窗格中，移至 [**記錄**] 索引標籤。
1. 選取記錄檔 `70_driver_log.txt`

## <a name="debug-and-troubleshoot-in-application-insights"></a>Application Insights 中的 Debug 和疑難排解
如需以這種方式使用 OpenCensus Python 程式庫的詳細資訊，請參閱此指南： [Application Insights 中的機器學習管線的 Debug 和疑難排解](how-to-debug-pipelines-application-insights.md)

## <a name="next-steps"></a>後續步驟

* 如需[azureml-管線核心](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)套件和[azureml-管線-步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)套件的說明，請參閱 SDK 參考。

* 查看[設計工具例外狀況和錯誤碼](algorithm-module-reference/designer-error-codes.md)的清單。
