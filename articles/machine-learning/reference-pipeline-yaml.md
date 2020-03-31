---
title: 機器學習管道 YAML
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 YAML 檔定義機器學習管道。 YAML 管道定義與 Azure CLI 的機器學習擴展一起使用。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 11/11/2019
ms.openlocfilehash: a677aaa891e21f4c9eeda02eebcb94e9d79a55ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368820"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>在 YAML 中定義機器學習管道

瞭解如何在[YAML](https://yaml.org/)中定義機器學習管道。 當對 Azure CLI 使用機器學習擴展時，許多與管道相關的命令都希望有一個 YAML 檔來定義管道。

下表列出了在 YAML 中定義管道時當前支援和不支援的內容：

| 步驟類型 | 是否支援？ |
| ----- | :-----: |
| PythonScript步驟 | 是 |
| 阿德拉步 | 是 |
| AzureBatch 步驟 | 是 |
| 資料磚步驟 | 是 |
| 資料傳輸步驟 | 是 |
| 自動ML步驟 | 否 |
| HyperDriveStep \(英文\) | 否 |
| 模組步驟 | 是 |
| MPI步 | 否 |
| EstimatorStep \(英文\) | 否 |

## <a name="pipeline-definition"></a>管線定義

管道定義使用以下鍵，這些鍵對應于[管道](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py)類：

| YAML 鍵 | 描述 |
| ----- | ----- |
| `name` | 管道的說明。 |
| `parameters` | 到管道的參數。 |
| `data_reference` | 定義在運行中資料應如何以及從何處可用。 |
| `default_compute` | 預設計算目標，其中管道中的所有步驟都運行。 |
| `steps` | 管道中使用的步驟。 |

## <a name="parameters"></a>參數

本節`parameters`使用以下鍵，這些鍵對應于[管道參數](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)類：

| YAML 鍵 | 描述 |
| ---- | ---- |
| `type` | 參數的值類型。 有效類型為`string` `int` `float`、、、、、`bool`或`datapath`。 |
| `default` | 預設值。 |

每個參數都已命名。 例如，以下 YAML 程式碼片段定義了名為 的三`NumIterationsParameter`個`DataPathParameter`參數`NodeCountParameter`， 和 ：

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>資料參考

本節`data_references`使用以下鍵，對應于[資料參考](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)：

| YAML 鍵 | 描述 |
| ----- | ----- |
| `datastore` | 要引用的資料存儲。 |
| `path_on_datastore` | 資料引用的備份存儲中的相對路徑。 |

每個資料引用都包含在一個鍵中。 例如，以下 YAML 程式碼片段定義存儲在名為 ：`employee_data`的鍵中的資料引用：

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>步驟

步驟定義計算環境，以及要在環境中運行的檔。 要定義步驟的類型，請使用`type`鍵：

| 步驟類型 | 描述 |
| ----- | ----- |
| `AdlaStep` | 使用 Azure 資料湖分析運行 U-SQL 腳本。 對應于[AdlaStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py)類。 |
| `AzureBatchStep` | 使用 Azure 批次處理運行作業。 對應于[AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py)類。 |
| `DatabricsStep` | 添加資料磚塊筆記本、Python 腳本或 JAR。 對應于[DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py)類。 |
| `DataTransferStep` | 在存儲選項之間傳輸資料。 對應于[資料傳輸步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)類。 |
| `PythonScriptStep` | 運行 Python 腳本。 對應于[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)類。 |

### <a name="adla-step"></a>ADLA 步驟

| YAML 鍵 | 描述 |
| ----- | ----- |
| `script_name` | U-SQL 腳本的名稱（相對於`source_directory`。 |
| `compute_target` | 要用於此步驟的 Azure 資料湖計算目標。 |
| `parameters` | [管道的參數](#parameters)。 |
| `inputs` | 輸入可以是[輸入埠綁定](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、[資料引用](#data-reference)、[埠資料引用](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、[管道資料](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、[資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、[資料集定義](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[管道資料集](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 輸出可以是[管道資料](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)或[輸出埠綁定](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `source_directory` | 包含腳本、程式集等的目錄。 |
| `priority` | 用於當前作業的優先順序值。 |
| `params` | 名稱值對的字典。 |
| `degree_of_parallelism` | 用於此作業的並行性程度。 |
| `runtime_version` | 資料湖分析引擎的執行階段版本。 |
| `allow_reuse` | 確定當使用相同的設置再次運行時，步驟是否應重用以前的結果。 |

以下示例包含 ADLA 步驟定義：

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Azure 批次處理步驟

| YAML 鍵 | 描述 |
| ----- | ----- |
| `compute_target` | 要用於此步驟的 Azure 批次處理計算目標。 |
| `inputs` | 輸入可以是[輸入埠綁定](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、[資料引用](#data-reference)、[埠資料引用](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、[管道資料](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、[資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、[資料集定義](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[管道資料集](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 輸出可以是[管道資料](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)或[輸出埠綁定](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `source_directory` | 包含模組二進位檔案、可執行檔、程式集等的目錄。 |
| `executable` | 將作為此作業的一部分運行的命令/可執行檔的名稱。 |
| `create_pool` | 布林標誌，用於指示在運行作業之前是否創建池。 |
| `delete_batch_job_after_finish` | 布林標誌，指示在批次處理帳戶完成後是否從該作業中刪除該作業。 |
| `delete_batch_pool_after_finish` | 布林標誌，指示在作業完成後是否刪除池。 |
| `is_positive_exit_code_failure` | 布林標誌，用於指示作業是否失敗，如果任務退出時帶有正代碼。 |
| `vm_image_urn` | 如果`create_pool``True`為 ，則`VirtualMachineConfiguration`VM 使用 。 |
| `pool_id` | 作業將運行的池的 ID。 |
| `allow_reuse` | 確定當使用相同的設置再次運行時，步驟是否應重用以前的結果。 |

以下示例包含 Azure 批次處理步驟定義：

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>資料磚步驟

| YAML 鍵 | 描述 |
| ----- | ----- |
| `compute_target` | 要用於此步驟的 Azure 資料磚塊計算目標。 |
| `inputs` | 輸入可以是[輸入埠綁定](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、[資料引用](#data-reference)、[埠資料引用](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、[管道資料](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、[資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、[資料集定義](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[管道資料集](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 輸出可以是[管道資料](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)或[輸出埠綁定](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `run_name` | 此運行的資料磚塊中的名稱。 |
| `source_directory` | 包含腳本和其他檔的目錄。 |
| `num_workers` | 資料磚塊運行群集的靜態工作數。 |
| `runconfig` | `.runconfig`檔的路徑。 此檔是[Run 配置](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)類的 YAML 表示形式。 有關此檔結構的詳細資訊，請參閱[runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)。 |
| `allow_reuse` | 確定當使用相同的設置再次運行時，步驟是否應重用以前的結果。 |

以下示例包含資料磚塊步驟：

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>資料傳輸步驟

| YAML 鍵 | 描述 |
| ----- | ----- |
| `compute_target` | 要用於此步驟的 Azure 資料工廠計算目標。 |
| `source_data_reference` | 作為資料傳輸操作源的輸入連接。 支援的值是[輸入埠綁定](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、[資料引用](#data-reference)、[埠資料引用](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、[管道資料](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、[資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、[資料集定義](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[管道資料集](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `destination_data_reference` | 作為資料傳輸操作目標的輸入連接。 支援的值是[管道資料和](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)[輸出埠綁定](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `allow_reuse` | 確定當使用相同的設置再次運行時，步驟是否應重用以前的結果。 |

以下示例包含資料傳輸步驟：

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Python 腳本步驟

| YAML 鍵 | 描述 |
| ----- | ----- |
| `compute_target` | 用於此步驟的計算目標。 計算目標可以是 Azure 機器學習計算、虛擬機器（如資料科學 VM）或 HDInsight。 |
| `inputs` | 輸入可以是[輸入埠綁定](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、[資料引用](#data-reference)、[埠資料引用](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、[管道資料](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、[資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、[資料集定義](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[管道資料集](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 輸出可以是[管道資料](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)或[輸出埠綁定](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `script_name` | Python 腳本的名稱（相對於`source_directory`）。 |
| `source_directory` | 包含腳本、Conda 環境等的目錄。 |
| `runconfig` | `.runconfig`檔的路徑。 此檔是[Run 配置](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)類的 YAML 表示形式。 有關此檔結構的詳細資訊，請參閱[runconfig.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)。 |
| `allow_reuse` | 確定當使用相同的設置再次運行時，步驟是否應重用以前的結果。 |

以下示例包含 Python 腳本步驟：

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

## <a name="schedules"></a>排程

定義管道的計畫時，可以基於時間間隔觸發資料存儲，也可以定期進行。 以下是用於定義計畫的關鍵：

| YAML 鍵 | 描述 |
| ----- | ----- |
| `description` | 排程的描述。 |
| `recurrence` | 如果計畫是重複的，則包含定期設置。 |
| `pipeline_parameters` | 管道所需的任何參數。 |
| `wait_for_provisioning` | 是否等待計畫預配完成。 |
| `wait_timeout` | 超時前要等待的秒數。 |
| `datastore_name` | 要監視已修改/添加的 blob 的資料存儲。 |
| `polling_interval` | 對已修改/添加的 blob 輪詢之間多長時間（以分鐘）表示。 預設值：5 分鐘。 僅支援資料存儲計畫。 |
| `data_path_parameter_name` | 要使用已更改的 Blob 路徑設置的資料路徑管道參數的名稱。 僅支援資料存儲計畫。 |
| `continue_on_step_failure` | 如果步驟失敗，是否繼續執行提交的管道運行中的其他步驟。 如果提供，將覆蓋管道`continue_on_step_failure`的設置。
| `path_on_datastore` | 選擇性。 要監視已修改/添加的 blob 的資料存儲上的路徑。 路徑位於資料存儲的容器下，因此計畫監視器的實際路徑是容器/`path_on_datastore`。 如果沒有，則監視資料存儲容器。 在 子`path_on_datastore`資料夾中所做的添加/修改不會受到監視。 僅支援資料存儲計畫。 |

以下示例包含資料存儲觸發計畫的定義：

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

定義**定期計畫**時，請使用 以下鍵： `recurrence`

| YAML 鍵 | 描述 |
| ----- | ----- |
| `frequency` | 計畫重複的頻率。 有效值為`"Minute"` `"Hour"`、、、、`"Day"``"Week"`或`"Month"`。 |
| `interval` | 計畫觸發的頻率。 整數值是等待計畫再次觸發的時間單位數。 |
| `start_time` | 計畫的開始時間。 值的字串格式為`YYYY-MM-DDThh:mm:ss`。 如果未提供開始時間，則立即運行第一個工作負載，並根據計畫運行將來的工作負載。 如果開始時間是過去的，則第一個工作負載在下一個計算的執行時間運行。 |
| `time_zone` | 開始時間的時區。 如果未提供時區，則使用 UTC。 |
| `hours` | 如果`frequency``"Day"`是`"Week"`或 ，則可以指定從 0 到 23 的一個或多個整數（用逗號分隔）作為管道應運行的一天中的小時數。 只能`time_of_day`或`hours``minutes`和 可以使用。 |
| `minutes` | 如果`frequency``"Day"`是`"Week"`或 ，則可以指定從 0 到 59 的一個或多個整數（用逗號分隔）作為管道應運行的小時分鐘數。 只能`time_of_day`或`hours``minutes`和 可以使用。 |
| `time_of_day` | 如果是`frequency``"Day"`或`"Week"`，則可以指定計劃運行的一天中的時間。 值的字串格式為`hh:mm`。 只能`time_of_day`或`hours``minutes`和 可以使用。 |
| `week_days` | 如果是`frequency` `"Week"`，則可以指定計劃應運行時用逗號分隔的一個或多個天數。 Valid values are `"Monday"`, `"Tuesday"`, `"Wednesday"`, `"Thursday"`, `"Friday"`, `"Saturday"`, and `"Sunday"`. |

以下示例包含定期計畫的定義：

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>後續步驟

瞭解如何將[CLI 擴展用於 Azure 機器學習](reference-azure-machine-learning-cli.md)。
