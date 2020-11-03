---
title: 教學課程：執行 "Hello world!" Python 指令碼
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 入門系列的第 2 部分，說明如何提交簡單的 "Hello world!" Python 指令碼到雲端。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: afc851be08e6708efc0138dc45931cda147c67c1
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895880"
---
# <a name="tutorial-run-a-hello-world-python-script-part-2-of-4"></a>教學課程：執行 "Hello world!" Python 指令碼 (第 2 部分，共 4 部分)

在本教學課程中，您將了解如何使用 Azure Machine Learning SDK for Python 來提交和執行 Python "Hello world!" 指令碼。

本教學課程是 *四部分教學課程系列的第 2 部分* ，您會在其中了解 Azure Machine Learning 的基本概念，以及在 Azure 中完成作業型機器學習工作。 本教學課程建基於您在[第 1 部分：設定您 Azure Machine Learning 的本機電腦](tutorial-1st-experiment-sdk-setup-local.md)中所完成的工作。

在本教學課程中，您將：

> [!div class="checklist"]
> * 本機建立並執行 "Hello world!" Python 指令碼。
> * 建立 Python 控制指令碼，將 "Hello world" 提交至 Azure Machine Learning。
> * 了解控制指令碼中的 Azure Machine Learning 概念。
> * 提交並執行 "Hello world!" 指令碼。
> * 在雲端中檢視程式碼輸出。

## <a name="prerequisites"></a>必要條件

- 如果您還沒有 Azure Machine Learning 工作區，請完成[第 1 部分](tutorial-1st-experiment-sdk-setup-local.md)。
- Python 語言和機器學習工作流程的簡介知識。
- Visual Studio Code、Jupyter 或 PyCharm 等本機開發環境。
- Python (版本 3.5 至 3.7)。

## <a name="create-and-run-a-python-script-locally"></a>在本機建立及執行 Python 指令碼

在 `tutorial` 目錄下建立名為 `src` 的新子目錄，以儲存您想要在 Azure Machine Learning 計算叢集上執行的程式碼。 在 `src` 子目錄中，建立 `hello.py` Python 指令碼：

```python
# src/hello.py
print("Hello world!")
```

您的專案目錄結構現在看起來會像這樣：

```Bash
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
└──01-create-workspace.py
└──02-create-compute.py
```

### <a name="test-your-script-locally"></a>在本機測試您的指令碼

您可以使用您最愛的 IDE 或終端機，在本機執行您的程式碼。 在本機執行程式碼具有互動式程式碼偵錯的優點。

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

## <a name="create-a-control-script"></a>建立控制指令碼

「控制指令碼」可讓您在雲端中執行 `hello.py` 指令碼。 您可以使用控制指令碼來控制機器學習程式碼的執行方式和位置。  

在您的教學課程目錄中，建立名為 `03-run-hello.py` 的新 Python 檔案，然後複製/貼上下列程式碼至該檔案：

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>了解程式碼

以下說明控制指令碼的運作方式：

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [工作區](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true)會連線到您的 Azure Machine Learning 工作區，讓您可以與 Azure Machine Learning 資源進行通訊。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [實驗](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true)會提供在單一名稱下組織多個執行的簡單方式。 稍後您將了解實驗如何讓數十個執行之間的計量比較變得簡單。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) 會包裝您的 `hello.py` 程式碼，並將其傳遞至您的工作區。 如其名所示，您可以使用此類別來「設定」指令碼在 Azure Machine Learning 中的「執行」方式。 也會指定要對其執行指令碼的計算目標。 在此程式碼中，目標是您在[設定教學課程](tutorial-1st-experiment-sdk-setup-local.md)中建立的計算叢集。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       提交您的指令碼。 此提交行為稱為[回合 (Run)](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true)。 一個回合會封裝程式碼的單次執行。 使用回合來監視指令碼進度、擷取輸出、分析結果、將計量視覺化等等。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        `run` 物件會提供程式碼執行的控制代碼。 使用從 Python 指令碼列印的 URL，從 Azure Machine Learning Studio 監視其進度。  
   :::column-end:::
:::row-end:::

## <a name="submit-and-run-your-code-in-the-cloud"></a>在雲端中提交並執行您的程式碼

執行您的控制指令碼，該程式碼會在您於[設定教學課程](tutorial-1st-experiment-sdk-setup-local.md)中建立的計算叢集上執行 `hello.py`。

```bash
python 03-run-hello.py
```

> [!TIP]
> 如果執行此程式碼會顯示無法存取訂用帳戶的錯誤，請參閱[連線到工作區](how-to-manage-workspace.md?tab=python#connect-multi-tenant)以取得驗證選項的相關資訊。

## <a name="monitor-your-code-in-the-cloud-by-using-the-studio"></a>使用 Studio 在雲端中監視您的程式碼

輸出會包含 Studio 的連結，看起來像這樣：`https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>`。

遵循連結，然後移至 [輸出 + 記錄] 索引標籤。您可以看到如下所示的 `70_driver_log.txt` 檔案：

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

在第 8 行上，您會看到 "Hello world!" 輸出。

`70_driver_log.txt` 檔案包含回合的標準輸出。 您在雲端中偵錯遠端回合時，此檔案很有用。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立簡單的 "hello world" 指令碼，並在 Azure 上執行該指令碼。 您已了解如何連線到您的 Azure Machine Learning 工作區、建立實驗，以及將您的 `hello.py` 程式碼提交至雲端。

在下一個教學課程中，您會以這些學習成果為基礎，執行比 `print("Hello world!")` 更有趣的內容。

> [!div class="nextstepaction"]
> [教學課程：將模型定型](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> 如果您想要在此完成教學課程系列，而不是前往下一個步驟，請記得[清除您的資源](tutorial-1st-experiment-bring-data.md#clean-up-resources)。
