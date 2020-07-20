---
title: Machine Learning 管線 YAML
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 YAML 檔定義機器學習管線。 YAML 管線定義會與適用于 Azure CLI 的機器學習服務延伸模組搭配使用。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: nilsp
author: NilsPohlmann
ms.date: 11/11/2019
ms.custom: tracking-python
ms.openlocfilehash: c53f6ff7fdcc861bc11fb54838f1e634fc944c6c
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86205264"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>在 YAML 中定義機器學習管線

瞭解如何在[YAML](https://yaml.org/)中定義您的機器學習管線。 使用機器學習擴充功能進行 Azure CLI 時，許多管線相關的命令都預期會有定義管線的 YAML 檔案。

下表列出在 YAML 中定義管線時，目前不支援的是和。

| 步驟類型 | 是否支援？ |
| ----- | :-----: |
| PythonScriptStep | 是 |
| ParallelRunStep | 是 |
| AdlaStep | 是 |
| 」已 azurebatchstep | 是 |
| DatabricksStep | 是 |
| DataTransferStep | 是 |
| AutoMLStep | 否 |
| HyperDriveStep \(英文\) | 否 |
| ModuleStep | 是 |
| MPIStep | 否 |
| EstimatorStep \(英文\) | 否 |

## <a name="pipeline-definition"></a>管線定義

管線定義會使用對應至[管線](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py)類別的下列索引鍵：

| YAML 索引鍵 | 描述 |
| ----- | ----- |
| `name` | 管線的描述。 |
| `parameters` | 參數 (s) 至管線。 |
| `data_reference` | 定義執行時應該如何使用資料。 |
| `default_compute` | 執行管線中所有步驟的預設計算目標。 |
| `steps` | 管線中使用的步驟。 |

## <a name="parameters"></a>參數

`parameters`區段使用下列索引鍵，其對應至[PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)類別：

| YAML 索引鍵 | 描述 |
| ---- | ---- |
| `type` | 參數的值類型。 有效的類型為 `string` 、 `int` 、 `float` 、 `bool` 或 `datapath` 。 |
| `default` | 預設值。 |

每個參數的名稱為。 例如，下列 YAML 程式碼片段會定義三個名為 `NumIterationsParameter` 、和的參數 `DataPathParameter` `NodeCountParameter` ：

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

`data_references`區段使用下列索引鍵，其對應至[DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)：

| YAML 索引鍵 | 描述 |
| ----- | ----- |
| `datastore` | 要參考的資料存放區。 |
| `path_on_datastore` | 資料參考的支援儲存體中的相對路徑。 |

每個資料參考都包含在索引鍵中。 例如，下列 YAML 程式碼片段會定義儲存在名為的索引鍵中的資料參考 `employee_data` ：

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

步驟會定義計算環境，以及要在環境上執行的檔案。 若要定義步驟的類型，請使用 `type` 下列索引鍵：

| 步驟類型 | 描述 |
| ----- | ----- |
| `AdlaStep` | 使用 Azure Data Lake Analytics 執行 U-SQL 腳本。 對應至[AdlaStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py)類別。 |
| `AzureBatchStep` | 使用 Azure Batch 執行作業。 對應至[」已 azurebatchstep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py)類別。 |
| `DatabricsStep` | 新增 Databricks 筆記本、Python 腳本或 JAR。 對應至[DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py)類別。 |
| `DataTransferStep` | 在儲存體選項之間傳輸資料。 對應至[DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)類別。 |
| `PythonScriptStep` | 執行 Python 腳本。 對應至[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)類別。 |
| `ParallelRunStep` | 執行 Python 腳本以非同步和平行處理大量資料。 對應至[ParallelRunStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py)類別。 |

### <a name="adla-step"></a>ADLA 步驟

| YAML 索引鍵 | 描述 |
| ----- | ----- |
| `script_name` |  (相對於) 的 U-SQL 腳本名稱 `source_directory` 。 |
| `compute_target` | 要用於此步驟的 Azure Data Lake 計算目標。 |
| `parameters` | 管線的[參數](#parameters)。 |
| `inputs` | 輸入可以是[InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、 [DataReference](#data-reference)、 [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、 [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、 [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 輸出可以是[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)或[OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `source_directory` | 包含腳本、元件等的目錄。 |
| `priority` | 要用於目前作業的優先順序值。 |
| `params` | 名稱/值組的字典。 |
| `degree_of_parallelism` | 要用於此作業的平行處理原則程度。 |
| `runtime_version` | Data Lake Analytics 引擎的執行階段版本。 |
| `allow_reuse` | 判斷當使用相同的設定再次執行時，步驟是否應重複使用先前的結果。 |

下列範例包含 ADLA 步驟定義：

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

### <a name="azure-batch-step"></a>Azure Batch 步驟

| YAML 索引鍵 | 描述 |
| ----- | ----- |
| `compute_target` | 要用於此步驟的 Azure Batch 計算目標。 |
| `inputs` | 輸入可以是[InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、 [DataReference](#data-reference)、 [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、 [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、 [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 輸出可以是[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)或[OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `source_directory` | 包含模組二進位檔、可執行檔、元件等的目錄。 |
| `executable` | 將在此作業中執行的命令/可執行檔名稱。 |
| `create_pool` | 布林值旗標，指出是否要在執行作業之前建立集區。 |
| `delete_batch_job_after_finish` | 布林值旗標，指出是否要在完成後從 Batch 帳戶刪除作業。 |
| `delete_batch_pool_after_finish` | 布林值旗標，指出是否要在作業完成之後刪除集區。 |
| `is_positive_exit_code_failure` | 布林值旗標，指出如果工作結束時使用正則程式碼，作業是否失敗。 |
| `vm_image_urn` | 如果 `create_pool` 為 `True` ，且 VM 使用 `VirtualMachineConfiguration` 。 |
| `pool_id` | 將執行作業之集區的識別碼。 |
| `allow_reuse` | 判斷當使用相同的設定再次執行時，步驟是否應重複使用先前的結果。 |

下列範例包含 Azure Batch 步驟定義：

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

### <a name="databricks-step"></a>Databricks 步驟

| YAML 索引鍵 | 描述 |
| ----- | ----- |
| `compute_target` | 要用於此步驟的 Azure Databricks 計算目標。 |
| `inputs` | 輸入可以是[InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、 [DataReference](#data-reference)、 [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、 [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、 [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 輸出可以是[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)或[OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `run_name` | 此執行的 Databricks 中名稱。 |
| `source_directory` | 包含腳本和其他檔案的目錄。 |
| `num_workers` | Databricks 執行叢集的靜態背景工作數目。 |
| `runconfig` | 檔案的路徑 `.runconfig` 。 此檔案是[RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)類別的 YAML 標記法。 如需此檔案結構的詳細資訊，請參閱[上的runconfigschema.js](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)。 |
| `allow_reuse` | 判斷當使用相同的設定再次執行時，步驟是否應重複使用先前的結果。 |

下列範例包含 Databricks 步驟：

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

| YAML 索引鍵 | 描述 |
| ----- | ----- |
| `compute_target` | 要用於此步驟的 Azure Data Factory 計算目標。 |
| `source_data_reference` | 作為資料傳輸作業來源的輸入連接。 支援的值為[InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、 [DataReference](#data-reference)、 [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、 [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、 [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `destination_data_reference` | 作為資料傳輸作業目的地的輸入連接。 支援的值為[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)和[OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `allow_reuse` | 判斷當使用相同的設定再次執行時，步驟是否應重複使用先前的結果。 |

下列範例包含資料傳輸步驟：

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

| YAML 索引鍵 | 描述 |
| ----- | ----- |
| `inputs` | 輸入可以是[InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、 [DataReference](#data-reference)、 [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、 [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、 [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 輸出可以是[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)或[OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `script_name` | Python 腳本的名稱， (相對於 `source_directory`) 。 |
| `source_directory` | 包含腳本、Conda 環境等的目錄。 |
| `runconfig` | 檔案的路徑 `.runconfig` 。 此檔案是[RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)類別的 YAML 標記法。 如需此檔案結構的詳細資訊，請參閱[上的runconfig.js](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)。 |
| `allow_reuse` | 判斷當使用相同的設定再次執行時，步驟是否應重複使用先前的結果。 |

下列範例包含 Python 腳本步驟：

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

### <a name="parallel-run-step"></a>平行執行步驟

| YAML 索引鍵 | 描述 |
| ----- | ----- |
| `inputs` | 輸入可以是[資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、 [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 輸出可以是[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)或[OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `script_name` | Python 腳本的名稱， (相對於 `source_directory`) 。 |
| `source_directory` | 包含腳本、Conda 環境等的目錄。 |
| `parallel_run_config` | 檔案的路徑 `parallel_run_config.yml` 。 此檔案是[ParallelRunConfig](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig?view=azure-ml-py)類別的 YAML 標記法。 |
| `allow_reuse` | 判斷當使用相同的設定再次執行時，步驟是否應重複使用先前的結果。 |

下列範例包含平行執行步驟：

```yaml
pipeline:
    description: SamplePipelineFromYaml
    default_compute: cpu-cluster
    data_references:
        MyMinistInput:
            dataset_name: mnist_sample_data
    parameters:
        PipelineParamTimeout:
            type: int
            default: 600
    steps:        
        Step1:
            parallel_run_config: "yaml/parallel_run_config.yml"
            type: "ParallelRunStep"
            name: "parallel-run-step-1"
            allow_reuse: True
            arguments:
            - "--progress_update_timeout"
            - parameter:timeout_parameter
            - "--side_input"
            - side_input:SideInputData
            parameters:
                timeout_parameter:
                    source: PipelineParamTimeout
            inputs:
                InputData:
                    source: MyMinistInput
            side_inputs:
                SideInputData:
                    source: Output4
                    bind_mode: mount
            outputs:
                OutputDataStep2:
                    destination: Output5
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="pipeline-with-multiple-steps"></a>具有多個步驟的管線 

| YAML 索引鍵 | 描述 |
| ----- | ----- |
| `steps` | 一或多個 PipelineStep 定義的順序。 請注意， `destination` 某個步驟的索引鍵 `outputs` 會成為下一個步驟之的索引 `source` 鍵 `inputs` 。| 

```yaml
pipeline:
    name: SamplePipelineFromYAML
    description: Sample multistep YAML pipeline
    data_references:
        TitanicDS:
            dataset_name: 'titanic_ds'
            bind_mode: download
    default_compute: cpu-cluster
    steps:
        Dataprep:
            type: "PythonScriptStep"
            name: "DataPrep Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "prep.py"
            arguments:
            - '--train_path'
            - output:train_path
            - '--test_path'
            - output:test_path
            allow_reuse: True
            inputs:
                titanic_ds:
                    source: TitanicDS
                    bind_mode: download
            outputs:
                train_path:
                    destination: train_csv
                    datastore: workspaceblobstore
                test_path:
                    destination: test_csv
        Training:
            type: "PythonScriptStep"
            name: "Training Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "train.py"
            arguments:
            - "--train_path"
            - input:train_path
            - "--test_path"
            - input:test_path
            inputs:
                train_path:
                    source: train_csv
                    bind_mode: download
                test_path:
                    source: test_csv
                    bind_mode: download

```

## <a name="schedules"></a>排程

定義管線的排程時，它可以是根據時間間隔觸發或週期性的資料存放區。 以下是用來定義排程的索引鍵：

| YAML 索引鍵 | 描述 |
| ----- | ----- |
| `description` | 排程的描述。 |
| `recurrence` | 包含週期設定（如果排程為週期性）。 |
| `pipeline_parameters` | 管線所需的任何參數。 |
| `wait_for_provisioning` | 是否等待布建排程完成。 |
| `wait_timeout` | 超時前等待的秒數。 |
| `datastore_name` | 要監視已修改/已新增之 blob 的資料存放區。 |
| `polling_interval` | 輪詢已修改/已新增的 blob 之間的時間（以分鐘為單位）。 預設值：5分鐘。 僅支援資料存放區排程。 |
| `data_path_parameter_name` | 要以變更的 blob 路徑設定的資料路徑管線參數的名稱。 僅支援資料存放區排程。 |
| `continue_on_step_failure` | 當步驟失敗時，是否要繼續執行已提交來擷取中的其他步驟。 如果有提供，將會覆寫 `continue_on_step_failure` 管線的設定。
| `path_on_datastore` | 選擇性。 資料存放區上要監視已修改/已新增之 blob 的路徑。 此路徑位於資料存放區的容器底下，因此排程監視的實際路徑為 container/ `path_on_datastore` 。 如果沒有，則會監視資料存放區容器。 在的子資料夾中所做的新增/修改 `path_on_datastore` 不受監視。 僅支援資料存放區排程。 |

下列範例包含資料存放區觸發排程的定義：

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

定義**週期性排程**時，請使用下列索引鍵 `recurrence` ：

| YAML 索引鍵 | 描述 |
| ----- | ----- |
| `frequency` | 排程重複出現的頻率。 有效的值為 `"Minute"` 、 `"Hour"` 、 `"Day"` 、 `"Week"` 或 `"Month"` 。 |
| `interval` | 排程的引發頻率。 整數值是要等到排程再次引發為止的時間單位數。 |
| `start_time` | 排程的開始時間。 值的字串格式為 `YYYY-MM-DDThh:mm:ss` 。 如果未提供開始時間，則會立即執行第一個工作負載，並根據排程執行未來的工作負載。 如果開始時間在過去，則會在下一個計算的執行時間執行第一個工作負載。 |
| `time_zone` | 開始時間的時區。 如果未提供時區，則會使用 UTC。 |
| `hours` | 如果 `frequency` 是 `"Day"` 或 `"Week"` ，您可以指定介於0到23之間的一或多個整數（以逗號分隔），做為管線執行的一天中的時數。 只有 `time_of_day` 或 `hours` 和 `minutes` 可以使用。 |
| `minutes` | 如果 `frequency` 是 `"Day"` 或 `"Week"` ，您可以指定從0到59的一個或多個整數（以逗號分隔），做為管線執行時的分鐘數。 只有 `time_of_day` 或 `hours` 和 `minutes` 可以使用。 |
| `time_of_day` | 如果 `frequency` 是 `"Day"` 或 `"Week"` ，您可以指定排程執行的一天時間。 值的字串格式為 `hh:mm` 。 只有 `time_of_day` 或 `hours` 和 `minutes` 可以使用。 |
| `week_days` | 如果 `frequency` 是 `"Week"` ，您可以指定排程應該執行的一或多個天數（以逗號分隔）。 有效的值為 `"Monday"` 、 `"Tuesday"` 、 `"Wednesday"` 、 `"Thursday"` 、 `"Friday"` 、 `"Saturday"` 和 `"Sunday"` 。 |

下列範例包含週期性排程的定義：

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

瞭解如何[使用適用于 Azure Machine Learning 的 CLI 擴充](reference-azure-machine-learning-cli.md)功能。
