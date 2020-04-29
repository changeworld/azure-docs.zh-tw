---
title: Debug & 針對 ML 管線進行疑難排解
titleSuffix: Azure Machine Learning
description: 在 Python 中，對您的 Azure Machine Learning 管線進行偵錯工具。 瞭解開發管線的常見陷阱，以及協助您在遠端執行之前和期間進行腳本的秘訣。 瞭解如何使用 Visual Studio Code，以互動方式對您的機器學習管線進行偵錯工具。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.openlocfilehash: 9c2e00ed14a45c6df7cf72845db2ecd069381ca5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81257200"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>機器學習管線的偵錯和疑難排解
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何在[AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)和[Azure Machine Learning 設計工具（預覽）](https://docs.microsoft.com/azure/machine-learning/concept-designer)中，針對[機器學習管線](concept-ml-pipelines.md)進行調試和疑難排解。 提供資訊的方式如下：

* 使用 Azure Machine Learning SDK 進行 Debug
* 使用 Azure Machine Learning 設計工具進行 Debug
* 使用 Application Insights 進行 Debug
* 使用 Visual Studio Code （VS Code）和適用於 Visual Studio 的 Python 工具（PTVSD）以互動方式進行調試

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK 中的調試和疑難排解
下列各節概述建立管線時的常見陷阱，以及用來偵測管線中執行之程式碼的不同策略。 當您無法如預期般執行管線時，請使用下列秘訣。

### <a name="testing-scripts-locally"></a>在本機測試腳本

管線中最常見的失敗之一，就是附加的腳本（資料清理腳本、計分腳本等）並未如預期執行，或包含遠端計算內容中的執行階段錯誤，這在您的工作區中很容易在 Azure Machine Learning studio 中進行偵錯工具。 

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

在本機測試腳本是一種很好的方法，可在您開始建立管線之前，先將主要程式碼片段和複雜邏輯進行偵錯工具，但在某些情況下，您可能需要在實際管線執行期間先行編譯腳本，特別是在診斷管線步驟之間互動時所發生的行為。 我們建議您在步驟`print()`腳本中使用語句，讓您可以在遠端執行期間看到物件狀態和預期的值，這與偵錯工具代碼的方式類似。

記錄檔`70_driver_log.txt`包含： 

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

### <a name="troubleshooting-tips"></a>疑難排解秘訣

下表包含管線開發期間的常見問題，以及可能的解決方案。

| 問題 | 可能的解決方法 |
|--|--|
| 無法將資料傳遞至`PipelineData`目錄 | 請確定您已在對應至管線預期步驟輸出資料所在位置的腳本中建立目錄。 在大部分情況下，輸入引數會定義輸出目錄，然後您會明確建立目錄。 使用`os.makedirs(args.output_dir, exist_ok=True)`來建立輸出目錄。 如需顯示此設計模式的評分腳本範例，請參閱[教學](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script)課程。 |
| 相依性 bug | 如果您已在本機開發和測試腳本，但在管線的遠端計算上執行時發現相依性問題，請確定您的計算環境相依性和版本符合您的測試環境。 （請參閱[環境建立、快取和重複使用](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| 計算目標的不明確錯誤 | 刪除和重新建立計算目標可以解決計算目標的特定問題。 |
| 管線未重複使用步驟 | 預設會啟用步驟重複使用，但請確定您未在管線步驟中停用它。 如果已停用重複使用`allow_reuse` ，則步驟中的參數會設定`False`為。 |
| 管線重新執行不必要 | 若要確保步驟只會在基礎資料或腳本變更時重新執行，請將您的目錄與每個步驟分離。 如果您使用相同的來原始目錄進行多個步驟，您可能會遇到不必要的重新執行。 在管線`source_directory`步驟物件上使用參數，以指向該步驟的隔離目錄，並確保您不會針對多個步驟`source_directory`使用相同的路徑。 |

### <a name="logging-options-and-behavior"></a>記錄選項和行為

下表提供管線的不同偵錯工具選項的資訊。 這不是詳盡的清單，因為除了此處所示的 Azure Machine Learning、Python 和 OpenCensus 以外，其他選項仍存在。

| 程式庫                    | 類型   | 範例                                                          | Destination                                  | 資源                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | 計量 | `run.log(name, val)`                                             | Azure Machine Learning 入口網站 UI             | [如何追蹤實驗](how-to-track-experiments.md#available-metrics-to-track)<br>[azureml. core. 執行類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Python 列印/記錄    | Log    | `print(val)`<br>`logging.info(message)`                          | 驅動程式記錄檔，Azure Machine Learning 設計工具 | [如何追蹤實驗](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Python 記錄](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Log    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights-追蹤                | [對 Application Insights 中的管線進行偵錯](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure 監視器匯出工具](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python 記錄操作手冊](https://docs.python.org/3/howto/logging-cookbook.html) |

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
1. 在模組的右窗格中，移至 [**輸出 + 記錄**] 索引標籤。
1. 選取記錄檔`70_driver_log.txt`。

    ![撰寫頁面模組記錄](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>從管線執行存取記錄

您也可以在 [管線執行詳細資料] 頁面的 **[管線] 或 [** **實驗**] 區段中，找到特定執行的記錄檔。

1. 選取在設計工具中建立的管線執行。
    ![管線執行頁面](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. 在預覽窗格中選取任何模組。
1. 在模組的右窗格中，移至 [**輸出 + 記錄**] 索引標籤。
1. 選取記錄檔`70_driver_log.txt`。

## <a name="debug-and-troubleshoot-in-application-insights"></a>Application Insights 中的 Debug 和疑難排解
如需以這種方式使用 OpenCensus Python 程式庫的詳細資訊，請參閱此指南： [Application Insights 中的機器學習管線的 Debug 和疑難排解](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Visual Studio Code 中的 Debug 和疑難排解

在某些情況下，您可能需要以互動方式來對 ML 管線中使用的 Python 程式碼進行偵錯工具。 藉由使用 Visual Studio Code （VS Code）和適用於 Visual Studio 的 Python 工具（PTVSD），您可以附加至在定型環境中執行的程式碼。

### <a name="prerequisites"></a>先決條件

* 設定為使用__Azure 虛擬網路__的__Azure Machine Learning 工作區__。
* 在管線步驟中使用 Python 腳本的__Azure Machine Learning 管線__。 例如，PythonScriptStep。
* Azure Machine Learning 計算叢集，位於__虛擬網路中__，並__由管線用來進行定型__。
* __虛擬網路中__的__開發環境__。 開發環境可能是下列其中一項：

    * 虛擬網路中的 Azure 虛擬機器
    * 虛擬網路中筆記本 VM 的計算實例
    * 由虛擬私人網路（VPN）連線到虛擬網路的用戶端電腦。

如需有關搭配 Azure Machine Learning 使用 Azure 虛擬網路的詳細資訊，請參閱[在 azure 虛擬網路中保護 AZURE ML 實驗和推斷作業](how-to-enable-virtual-network.md)。

### <a name="how-it-works"></a>運作方式

您的 ML 管線步驟會執行 Python 腳本。 這些腳本會進行修改，以執行下列動作：
    
1. 記錄正在執行之主機的 IP 位址。 您可以使用 IP 位址，將偵錯工具連接至腳本。

2. 啟動 PTVSD debug 元件，並等候偵錯工具連接。

3. 從您的開發環境中，您可以監視定型程式所建立的記錄，以找出執行腳本的 IP 位址。

4. 您可以使用`launch.json`檔案，告訴 VS Code 要將偵錯工具連接到的 IP 位址。

5. 您會附加偵錯工具，並以互動方式逐步執行腳本。

### <a name="configure-python-scripts"></a>設定 Python 腳本

若要啟用偵錯工具，請對 ML 管線中的步驟所使用的 Python 腳本進行下列變更：

1. 新增下列 import 語句：

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. 新增下列引數。 這些引數可讓您視需要啟用偵錯工具，並設定附加偵錯工具的超時時間：

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. 加入下列語句。 這些語句會載入目前的執行內容，讓您可以記錄執行程式碼所在節點的 IP 位址：

    ```python
    global run
    run = Run.get_context()
    ```

1. 加入可`if`啟動 PTVSD 的語句，並等候偵錯工具附加。 如果偵錯工具在超時之前沒有附加，腳本會繼續正常運作。

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        ip = socket.gethostbyname(socket.gethostname())
        print(f'ip_address: {ip}')
        ptvsd.enable_attach(address=('0.0.0.0', 5678),
                            redirect_output=True)
        # Wait for the timeout for debugger to attach
        ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
        print(f'Debugger attached = {ptvsd.is_attached()}')
    ```

下列 Python 範例顯示可啟用調試`train.py`程式的基本檔案：

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import ptvsd
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    ip = socket.gethostbyname(socket.gethostname())
    print(f'ip_address: {ip}')
    ptvsd.enable_attach(address=('0.0.0.0', 5678),
                        redirect_output=True)
    # Wait for the timeout for debugger to attach
    ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
    print(f'Debugger attached = {ptvsd.is_attached()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>設定 ML 管線

若要提供啟動 PTVSD 並取得執行內容所需的 Python 套件，請建立環境並設定`pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`。 變更 SDK 版本，使其符合您所使用的版本。 下列程式碼片段示範如何建立環境：

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['ptvsd', 'azureml-sdk==1.0.83'])
```

在 [[設定 Python 腳本](#configure-python-scripts)] 區段中，已將兩個新的引數新增至 ML 管線步驟所使用的腳本。 下列程式碼片段示範如何使用這些引數來啟用元件的偵錯工具，以及設定超時。 它也會示範如何使用稍早建立的環境， `runconfig=run_config`方法是設定：

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300],
                         compute_target=aml_compute, 
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

當管線執行時，每個步驟都會建立子執行。 如果已啟用偵錯工具，修改過的腳本會在子執行的中`70_driver_log.txt`記錄類似下列文字的資訊：

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

儲存`ip_address`值。 此資訊使用於下一節。

> [!TIP]
> 您也可以從針對此管線步驟執行的子系執行記錄中，尋找 IP 位址。 如需有關如何查看此資訊的詳細資訊，請參閱[監視 AZURE ML 實驗執行和計量](how-to-track-experiments.md)。

### <a name="configure-development-environment"></a>設定開發環境

1. 若要在您的 VS Code 開發環境上安裝適用於 Visual Studio 的 Python 工具（PTVSD），請使用下列命令：

    ```
    python -m pip install --upgrade ptvsd
    ```

    如需搭配 VS Code 使用 PTVSD 的詳細資訊，請參閱[遠端偵錯](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)程式。

1. 若要設定 VS Code 與執行偵錯工具的 Azure Machine Learning 計算通訊，請建立新的 debug 設定：

    1. 從 VS Code 選取 [__調試__] 功能表，然後選取 [__開啟__設定]。 隨即開啟名為 [__啟動 json__ ] 的檔案。

    1. 在__啟動 json__檔案中，尋找包含`"configurations": [`的行，並在其後插入下列文字。 將`"host": "10.3.0.5"`專案變更為您在上一節記錄中傳回的 IP 位址。 將`"localRoot": "${workspaceFolder}/code/step"`專案變更為包含正在進行調試之腳本複本的本機目錄：

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "10.3.0.5",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > 如果 [設定] 區段中已經有其他專案，請在您插入的程式碼後面新增逗號（，）。

        > [!TIP]
        > 最佳做法是將腳本的資源保留在不同的目錄中，這就是範例`localRoot`值的參考`/code/step1`原因。
        >
        > 如果您要對多個腳本進行偵錯工具，請在不同的目錄中為每個腳本建立個別的設定區段。

    1. 儲存 [__啟動 json__檔案]。

### <a name="connect-the-debugger"></a>連接偵錯工具

1. 開啟 VS Code 並開啟腳本的本機複本。
2. 在附加之後，設定您想要讓腳本停止的中斷點。
3. 當子進程正在執行腳本，且`Timeout for debug connection`顯示在記錄中時，請使用 F5 鍵或選取 [ __Debug__]。 出現提示時，選取 [ __Azure Machine Learning 計算：遠端 debug__ ] 設定。 您也可以從側邊列選取 [debug] 圖示，從 [偵錯工具] 下拉式功能表中選取 [ __Azure Machine Learning：遠端 debug__ ] 專案，然後使用綠色箭號來附加偵錯工具。

    此時，VS Code 會連接到計算節點上的 PTVSD，並在您先前設定的中斷點停止。 您現在可以在程式碼執行時逐步執行、查看變數等。

    > [!NOTE]
    > 如果記錄檔顯示的專案指出`Debugger attached = False`，則超時時間已過期，而且腳本不會繼續使用偵錯工具。 再次提交管線，並在`Timeout for debug connection`訊息之後和超時時間到期之前，連接偵錯工具。

## <a name="next-steps"></a>後續步驟

* 如需[azureml-管線核心](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)套件和[azureml-管線-步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)套件的說明，請參閱 SDK 參考。

* 查看[設計工具例外狀況和錯誤碼](algorithm-module-reference/designer-error-codes.md)的清單。
