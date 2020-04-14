---
title: 除錯&排除 ML 管道故障
titleSuffix: Azure Machine Learning
description: 在 Python 中調試 Azure 機器學習管道。 瞭解開發管道的常見缺陷,以及説明您在遠端執行之前和期間調試腳本的提示。 瞭解如何使用 Visual Studio 代碼以互動方式調試機器學習管道。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.openlocfilehash: 9c2e00ed14a45c6df7cf72845db2ecd069381ca5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257200"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>機器學習管線的偵錯和疑難排解
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中,您將瞭解如何在[Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)和 Azure[機器學習設計器(預覽)](https://docs.microsoft.com/azure/machine-learning/concept-designer)中除錯和排除[機器學習管道](concept-ml-pipelines.md)。 有關如何:

* 使用 Azure 機器學習 SDK 進行除錯
* 使用 Azure 機器學習設計器進行除錯
* 使用應用程式見解進行除錯
* 使用視覺化工作室代碼 (VS 代碼) 與 Visual Studio 的 Python 工具 (PTVSD) 進行互動除錯

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>在 Azure 機器學習 SDK 中調試和故障排除
以下各節概述了構建管道時的常見缺陷,以及調試在管道中運行的代碼的不同策略。 當您在使管道按預期運行時,請使用以下提示。

### <a name="testing-scripts-locally"></a>在本地端測試文稿

管道中最常見的故障之一是附加的腳本(數據清理腳本、評分腳本等)未按預期運行,或者包含遠端計算上下文中的運行時錯誤,這些錯誤在 Azure 機器學習工作室的工作區中難以調試。 

管道本身無法在本地運行,但在本地電腦上獨立運行文稿允許您更快地調試,因為您不必等待計算和環境生成過程。 需要一些開發工作才能做到這一點:

* 如果數據位於雲數據存儲中,則需要下載數據並將其提供給腳本。 使用少量資料範例是縮短執行時並快速取得有關文稿行為回饋的好方法
* 如果嘗試模擬中間導管步驟,則可能需要手動生成特定文稿期望從上一步中生成的物件類型
* 您還需要定義自己的環境,並複製在遠端計算環境中定義的相依項

在本地環境中執行文稿設定後,執行除錯工作要容易得多,例如:

* 附加自訂除錯設定
* 暫停執行並檢查物件狀態
* 捕捉在執行時之前不會公開的類型或邏輯錯誤

> [!TIP] 
> 驗證腳本是否按預期運行后,在下一步將在單步驟管道中運行腳本,然後再嘗試通過多個步驟在管道中運行腳本。

### <a name="debugging-scripts-from-remote-context"></a>從遠端上下文除錯文稿

在本地測試腳本是在開始構建管道之前調試主要代碼片段和複雜邏輯的好方法,但在某些時候,您可能需要在實際管道運行期間調試腳本,尤其是在診斷管道步驟之間交互期間發生的行為時。 我們建議在步驟腳本中`print()`自由使用語句,以便在遠端執行期間查看物件狀態和預期值,類似於調試 JAvaScript 代碼的方式。

紀錄檔`70_driver_log.txt`包含: 

* 文稿執行期間的所有列印敘述
* 文稿堆疊追蹤 

要在門戶中查找此日誌檔和其他日誌檔,請首先按一下工作區中運行的管道。

![導管執行清單頁](./media/how-to-debug-pipelines/pipelinerun-01.png)

導航到管道運行詳細資訊頁。

![管管執行詳細資訊頁](./media/how-to-debug-pipelines/pipelinerun-02.png)

單擊模組以執行特定步驟。 導航到 **「日誌」** 選項卡。其他日誌包括有關環境映射生成過程和步驟準備腳本的資訊。

![管管執行詳細資訊頁面紀錄選項卡](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> 可在工作區中的 **「終結點」** 選項卡中找到*已發佈管道*的運行。 在**實驗**或導管中找到*非已發布的導管***執行**。

### <a name="troubleshooting-tips"></a>疑難排解秘訣

下表包含管道開發過程中的常見問題,並帶有潛在的解決方案。

| 問題 | 可能的解決方法 |
|--|--|
| 無法將資料傳送`PipelineData`到 目錄 | 確保在腳本中創建了一個目錄,該目錄對應於管道希望步驟輸出數據的位置。 在大多數情況下,輸入參數將定義輸出目錄,然後顯式創建目錄。 用於`os.makedirs(args.output_dir, exist_ok=True)`創建輸出目錄。 有關顯示此設計模式的評分文稿範例,請參閱[教學](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script)。 |
| 相依項錯誤 | 如果您在本地開發和測試了腳本,但在管道中的遠端計算上運行時發現依賴項問題,請確保計算環境依賴項和版本與測試環境匹配。 (請參閱[環境建構、緩存和重用](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| 具有計算目標的模糊錯誤 | 刪除和重新創建計算目標可以解決計算目標的某些問題。 |
| 管管未重用步驟 | 默認情況下啟用步驟重用,但請確保未在管道步驟中禁用它。 如果關閉重用,步驟`allow_reuse`中的參數設定為`False`。 |
| 導管不必要地重新執行 | 為了確保步驟僅在其基礎數據或腳本更改時重新運行,請為每個步驟分離目錄。 如果對多個步驟使用相同的源目錄,則可能會遇到不必要的重新運行。 使用管道`source_directory`步驟物件上的參數指向該步驟的隔離目錄,並確保多個步驟不使用相同的`source_directory`路徑。 |

### <a name="logging-options-and-behavior"></a>紀錄記錄選項與行為

下表提供了管道的不同調試選項的資訊。 它不是一個詳盡的清單,因為除了此處顯示的 Azure 機器學習、Python 和 OpenCensus 之外,還存在其他選項。

| 程式庫                    | 類型   | 範例                                                          | Destination                                  | 資源                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | 計量 | `run.log(name, val)`                                             | Azure 機器學習門戶 UI             | [如何追蹤實驗](how-to-track-experiments.md#available-metrics-to-track)<br>[azureml.core.run 類](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Python 列印/紀錄記錄    | Log    | `print(val)`<br>`logging.info(message)`                          | 驅動程式紀錄,Azure 機器學習設計器 | [如何追蹤實驗](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Python 紀錄記錄](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| 開啟 Census Python          | Log    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | 應用程式見解 - 追蹤                | [對 Application Insights 中的管線進行偵錯](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure 監視器匯出工具](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python 紀錄記錄說明書](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>紀錄記錄選項範例

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

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>在 Azure 機器學習設計器(預覽)中除錯和故障排除

本節概述了如何在設計器中排除管道故障。
對於在設計器中建立的導管,您可以在創作頁或導管執行詳細資訊頁上找到**紀錄檔**。

### <a name="access-logs-from-the-authoring-page"></a>從創作頁面存取紀錄

當您提交管道運行時並停留在創作頁中時,可以找到為每個模組生成的日誌檔。

1. 選擇創作畫布中的任何模組。
1. 在模組的右側窗格中,轉到 **「輸出 + 日誌」** 選項卡。
1. 選擇紀錄檔`70_driver_log.txt`。

    ![製作頁面模組紀錄](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>從導管執行存取紀錄

您還可以在管道運行詳細資訊頁中找到管道運行詳細資訊頁中管道或**實驗**部分中特定運行**的**日誌檔。

1. 選擇在設計器中創建的管道運行。
    ![導管執行頁](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. 選擇預覽窗格中的任何模組。
1. 在模組的右側窗格中,轉到 **「輸出 + 日誌」** 選項卡。
1. 選擇紀錄檔`70_driver_log.txt`。

## <a name="debug-and-troubleshoot-in-application-insights"></a>在應用程式見解中除錯和故障排除
有關以這種方式使用 OpenCensus Python 函式庫的詳細資訊,請參閱本指南:[在應用程式見解中調試和排除機器學習管道](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>在可視化工作室代碼中調試和故障排除

在某些情況下,您可能需要以互動方式調試 ML 管道中使用的 Python 代碼。 通過使用視覺化工作室代碼 (VS 代碼) 和 Visual Studio 的 Python 工具 (PTVSD),您可以在代碼在訓練環境中執行時附加到代碼。

### <a name="prerequisites"></a>Prerequisites

* 設定為使用__Azure 虛擬網路__的__Azure 機器學習工作區__。
* 使用 Python 文稿作為導管步驟一部份的__Azure 機器學習管線__。 例如,PythonScript 步驟。
* Azure 機器學習計算叢集,__位於虛擬網路中__,__由導管用於培訓__。
* __虛擬網路中__的開發__環境__。 開發環境可能是以下環境之一:

    * 虛擬網路中的 Azure 虛擬機器
    * 虛擬網路中筆電 VM 的計算實體
    * 通過虛擬專用網路 (VPN) 連接到虛擬網路的用戶端電腦。

有關將 Azure 虛擬網路與 Azure 機器學習一起使用的詳細資訊,請參閱[Azure 虛擬網路中的安全 Azure ML 實驗和推理作業](how-to-enable-virtual-network.md)。

### <a name="how-it-works"></a>運作方式

您的 ML 管道步驟執行 Python 文稿。 變更這些文稿以執行以下操作:
    
1. 記錄正在執行的主機的 IP 位址。 您可以使用 IP 位址將除錯器連接到文稿。

2. 啟動 PTVSD 除錯元件,然後等待除錯器連接。

3. 從開發環境中,您可以監視培訓過程創建的日誌,以查找文本運行的 IP 位址。

4. 告訴 VS 代碼 IP`launch.json`位址,使用 檔案將除錯器連接到。

5. 附加調試器並交互步驟完成腳本。

### <a name="configure-python-scripts"></a>設定 Python 文稿

要啟用除錯,請對 ML 管道中的步驟所使用的 Python 文稿進行以下變更:

1. 新增以下匯入敘述:

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. 添加以下參數。 這些參數允許您根據需要啟用除錯器,並設定附加除錯器的超時:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. 添加以下語句。 這些文片載入目前執行中文,以便您可以記錄代碼上執行的節點的 IP 位址:

    ```python
    global run
    run = Run.get_context()
    ```

1. 新增啟動`if`PTVSD 並等待除錯程式附加的語句。 如果在超時之前沒有安裝調試器,則腳本將照常進行。

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

下面的 Python 範例顯示`train.py`一個支援除錯的基本檔案:

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

### <a name="configure-ml-pipeline"></a>設定 ML 導管

要提供啟動 PTVSD 並取得執行的專案所需的 Python 套件,請`pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`建立一個環境 並設定 。 更改 SDK 版本以匹配正在使用的 SDK 版本。 以下程式碼段展示如何建立環境:

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

在[「設定 Python 文稿」](#configure-python-scripts)部分中,將兩個新參數添加到 ML 管道步驟使用的文稿中。 以下代碼段演示如何使用這些參數為元件啟用調試並設置超時。 它並展示如何使用之前建立的環境設定`runconfig=run_config`:

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

管道運行時,每個步驟都會創建一個子運行。 開啟了除錯,修改後的文稿會記錄與 子執行`70_driver_log.txt`的以下文字的相資訊:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

保存`ip_address`值。 此資訊使用於下一節。

> [!TIP]
> 您還可以從子運行的子管道步驟的運行日誌中找到 IP 位址。 有關檢視此資訊的詳細資訊,請參閱[監視 Azure ML 實驗執行和指標](how-to-track-experiments.md)。

### <a name="configure-development-environment"></a>設定開發環境

1. 在 VS 代碼開發環境中安裝 Visual Studio 的 Python 工具 (PTVSD),請使用以下指令:

    ```
    python -m pip install --upgrade ptvsd
    ```

    關於將 PTVSD 與 VS 碼一起使用的詳細資訊,請參閱[遠端除錯](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)。

1. 要設定 VS 代碼以執行除錯器的 Azure 機器學習計算進行通訊,請建立新的除錯設定:

    1. 從 VS 代碼中,選擇__除錯__選單,然後選擇 __「打開設定__」 。。 將打開名為 __「啟動.json」__ 的檔。

    1. 在__啟動.json__檔中,查找包含`"configurations": [`的行,並在它之後插入以下文本。 更改上`"host": "10.3.0.5"`一節中日誌中傳回的 IP 位址的項目。 將`"localRoot": "${workspaceFolder}/code/step"`項目改變為包含正在除錯的文稿的副本的本地目錄:

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
        > 如果配置部分中已有其他條目,請在插入的代碼後添加逗號 (,)。

        > [!TIP]
        > 最佳做法是將腳本的資源保存在單獨的目錄中,這就是為什麼`localRoot`示例值引用`/code/step1`的原因。
        >
        > 如果要在不同的目錄中調試多個腳本,請為每個腳本創建單獨的配置部分。

    1. 保存__啟動.json__檔。

### <a name="connect-the-debugger"></a>連線除錯器

1. 開啟 VS 代碼並打開文稿的本地複本。
2. 設置分斷點,希望腳本在附加后停止。
3. 當子行程執行文稿,並且`Timeout for debug connection`顯示在紀錄中時,請使用 F5 鍵或選擇__除錯__。 當出現提示時,選擇__Azure 機器學習計算:遠端調試__配置。 您還可以從側欄中選擇調試圖標 Azure 機器學習:從「調試下拉菜單」中__遠端調試__條目,然後使用綠色箭頭附加調試器。

    此時,VS 代碼連接到計算節點上的 PTVSD,並在之前設置的斷點停止。 現在,您可以在代碼運行時單步執行代碼、查看變數等。

    > [!NOTE]
    > 如果日誌顯示一個條目,`Debugger attached = False`說明 , 則超時已過期,並且腳本在沒有調試器的情況下繼續。 再次提交管道,並在`Timeout for debug connection`消息發出后並在超時到期之前連接調試器。

## <a name="next-steps"></a>後續步驟

* 有關[azureml-管道-核心](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)包和[azureml-管道-步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)包的説明,請參閱 SDK 參考。

* 請參閱[設計器異常和錯誤代碼](algorithm-module-reference/designer-error-codes.md)的清單。
