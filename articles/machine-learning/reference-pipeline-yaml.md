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
ms.openlocfilehash: 40e6d7f3d9c28708c5adec26ddc3c0463e75adc0
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529700"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>在 YAML 中定義機器學習導管

瞭解如何在[YAML](https://yaml.org/)中定義機器學習管道。 當對 Azure CLI 使用機器學習擴展時,許多與管道相關的命令都希望有一個 YAML 檔來定義管道。

下表列在 YAML 中定義導管時目前支援與不支援的內容:

| 步驟類型 | 是否支援？ |
| ----- | :-----: |
| PythonScript 步驟 | 是 |
| 阿德拉步 | 是 |
| AzureBatch 步驟 | 是 |
| 資料磚步驟 | 是 |
| 資料傳輸步驟 | 是 |
| 自動ML步驟 | 否 |
| HyperDriveStep \(英文\) | 否 |
| 模組步驟 | 是 |
| MPI 步 | 否 |
| EstimatorStep \(英文\) | 否 |

## <a name="pipeline-definition"></a>管線定義

管線定義使用以下鍵,這些鍵對應於[管道](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py)類:

| YAML 鍵 | 描述 |
| ----- | ----- |
| `name` | 管道的說明。 |
| `parameters` | 到管道的參數。 |
| `data_reference` | 定義在運行中數據應如何以及從何處可用。 |
| `default_compute` | 默認計算目標,其中管道中的所有步驟都運行。 |
| `steps` | 管道中使用的步驟。 |

## <a name="parameters"></a>參數

本節`parameters`使用以下鍵,這些鍵對應於[管道參數](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)類:

| YAML 鍵 | 描述 |
| ---- | ---- |
| `type` | 參數的值類型。 合法型態`string``int``float`為`bool``datapath`、、、、、 或 。 |
| `default` | 預設值。 |

每個參數都已命名。 例如,以下 YAML 程式碼欄位定義`NumIterationsParameter`了`DataPathParameter`名為`NodeCountParameter`的三個 參數, 與 :

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

本節`data_references`使用以下鍵,對應於[資料參考](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py):

| YAML 鍵 | 描述 |
| ----- | ----- |
| `datastore` | 要引用的數據存儲。 |
| `path_on_datastore` | 數據引用的備份存儲中的相對路徑。 |

每個數據引用都包含在一個鍵中。 例如,以下 YAML 代碼段定義儲存在`employee_data`名為 : 的鍵中的資料參考:

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

步驟定義計算環境,以及要在環境中運行的檔。 要定義步驟的類型,請使用`type`鍵:

| 步驟類型 | 描述 |
| ----- | ----- |
| `AdlaStep` | 使用 Azure 數據湖分析運行 U-SQL 腳本。 對應於[AdlaStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py)類。 |
| `AzureBatchStep` | 使用 Azure 批次處理執行作業。 對應於[AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py)類。 |
| `DatabricsStep` | 添加數據磚塊筆記本、Python 文本或 JAR。 對應於[DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py)類。 |
| `DataTransferStep` | 在存儲選項之間傳輸數據。 對應於[數據傳輸步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)類。 |
| `PythonScriptStep` | 運行 Python 文稿。 對應於[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)類。 |

### <a name="adla-step"></a>ADLA 步驟

| YAML 鍵 | 描述 |
| ----- | ----- |
| `script_name` | U-SQL 文稿的名稱(相`source_directory`對於 。 |
| `compute_target` | 要用於此步驟的 Azure 數據湖計算目標。 |
| `parameters` | [導管的參數](#parameters)。 |
| `inputs` | 輸入您輸入[連接埠,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)[資料參考](#data-reference)、[連接埠資料參考](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、[導管資料](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py),[資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py),[資料集定義](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[導管資料集](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 輸出可以是[導管資料](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)或[輸出連接埠的埠 。](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py) |
| `source_directory` | 包含腳本、程式集等的目錄。 |
| `priority` | 用於當前作業的優先順序值。 |
| `params` | 名稱值對的字典。 |
| `degree_of_parallelism` | 用於此作業的並行性程度。 |
| `runtime_version` | 數據湖分析引擎的運行時版本。 |
| `allow_reuse` | 確定當使用相同的設置再次運行時,步驟是否應重用以前的結果。 |

以下範例包含 ADLA 步驟定義:

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
| `inputs` | 輸入您輸入[連接埠,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)[資料參考](#data-reference)、[連接埠資料參考](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、[導管資料](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py),[資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py),[資料集定義](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[導管資料集](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 輸出可以是[導管資料](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)或[輸出連接埠的埠 。](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py) |
| `source_directory` | 包含模組二進位檔、可執行檔、程式集等的目錄。 |
| `executable` | 將作為此作業的一部分運行的命令/可執行檔的名稱。 |
| `create_pool` | 布爾標誌,用於指示在運行作業之前是否創建池。 |
| `delete_batch_job_after_finish` | 布爾標誌,指示在批處理帳戶完成後是否從該作業中刪除該作業。 |
| `delete_batch_pool_after_finish` | 布爾標誌,指示在作業完成後是否刪除池。 |
| `is_positive_exit_code_failure` | 布爾標誌,用於指示作業是否失敗,如果任務退出時帶有正代碼。 |
| `vm_image_urn` | 如果`create_pool``True`為 ,`VirtualMachineConfiguration`則 VM 使用 。 |
| `pool_id` | 作業將運行的池的 ID。 |
| `allow_reuse` | 確定當使用相同的設置再次運行時,步驟是否應重用以前的結果。 |

以下範例包含 Azure 批次處理步驟定義:

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
| `compute_target` | 要用於此步驟的 Azure 數據磚塊計算目標。 |
| `inputs` | 輸入您輸入[連接埠,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)[資料參考](#data-reference)、[連接埠資料參考](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、[導管資料](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py),[資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py),[資料集定義](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[導管資料集](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 輸出可以是[導管資料](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)或[輸出連接埠的埠 。](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py) |
| `run_name` | 此運行的數據磚塊中的名稱。 |
| `source_directory` | 包含文稿和其他檔的目錄。 |
| `num_workers` | 數據磚塊運行群集的靜態工作數。 |
| `runconfig` | `.runconfig`檔的路徑。 此檔是[Run 配置](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)類的 YAML 表示形式。 有關此檔案結構的詳細資訊,請參閱[runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)。 |
| `allow_reuse` | 確定當使用相同的設置再次運行時,步驟是否應重用以前的結果。 |

以下範例包含資料磚塊步驟:

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
| `compute_target` | 要用於此步驟的 Azure 數據工廠計算目標。 |
| `source_data_reference` | 作為數據傳輸操作源的輸入連接。 支援的值是[輸入連接埠,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)[或是資料參考](#data-reference)、[連接埠資料參考](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、[導管資料](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、[資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py),[資料集定義](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[導管資料集](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `destination_data_reference` | 作為數據傳輸操作目標的輸入連接。 支援的值是[導管資料和](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)[輸出連接埠 。](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py) |
| `allow_reuse` | 確定當使用相同的設置再次運行時,步驟是否應重用以前的結果。 |

以下範例包含資料傳輸步驟:

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

### <a name="python-script-step"></a>Python 文稿步驟

| YAML 鍵 | 描述 |
| ----- | ----- |
| `inputs` | 輸入您輸入[連接埠,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)[資料參考](#data-reference)、[連接埠資料參考](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、[導管資料](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py),[資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py),[資料集定義](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[導管資料集](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 輸出可以是[導管資料](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)或[輸出連接埠的埠 。](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py) |
| `script_name` | Python 文本的名稱(相`source_directory`對於 )。 |
| `source_directory` | 包含腳本、Conda 環境等的目錄。 |
| `runconfig` | `.runconfig`檔的路徑。 此檔是[Run 配置](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)類的 YAML 表示形式。 有關此檔案結構的詳細資訊,請參閱[runconfig.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)。 |
| `allow_reuse` | 確定當使用相同的設置再次運行時,步驟是否應重用以前的結果。 |

以下範例包含 Python 文稿步驟:

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

### <a name="pipeline-with-multiple-steps"></a>具有多個步驟的導管 

| YAML 鍵 | 描述 |
| ----- | ----- |
| `steps` | 一個或多個管道步驟定義的序列。 請注意,一`destination`個步驟`outputs`的鍵將成為`inputs`的 鍵。| 

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

定義管道的計劃時,可以基於時間間隔觸發數據存儲,也可以定期進行。 以下是定義的計劃的關鍵:

| YAML 鍵 | 描述 |
| ----- | ----- |
| `description` | 排程的描述。 |
| `recurrence` | 如果計劃是重複的,則包含定期設置。 |
| `pipeline_parameters` | 管道所需的任何參數。 |
| `wait_for_provisioning` | 是否等待計劃預配完成。 |
| `wait_timeout` | 超時前要等待的秒數。 |
| `datastore_name` | 要監視已修改/添加的 blob 的數據儲存。 |
| `polling_interval` | 對已修改/添加的 blob 輪詢之間多長時間(以分鐘)表示。 默認值:5 分鐘。 僅支援數據存儲計劃。 |
| `data_path_parameter_name` | 要使用已更改的 Blob 路徑設定的資料路徑管道參數的名稱。 僅支援數據存儲計劃。 |
| `continue_on_step_failure` | 如果步驟失敗,是否繼續執行提交的管道運行中的其他步驟。 如果提供,將覆蓋管道`continue_on_step_failure`的設置。
| `path_on_datastore` | 選擇性。 要監視已修改/添加的 blob 的數據儲存上的路徑。 路徑位於資料儲存的容器下,因此計劃監視器的實際路徑是容器/`path_on_datastore`。 如果沒有,則監視數據存儲容器。 在子`path_on_datastore`資料夾中所做的添加/修改不會受到監視。 僅支援數據存儲計劃。 |

以下範例包含資料儲存觸發計劃的定義:

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

定義**定期排**程時,請使用`recurrence`以下鍵:

| YAML 鍵 | 描述 |
| ----- | ----- |
| `frequency` | 計劃重複的頻率。 合法值為`"Minute"``"Hour"``"Day"``"Week"`、、、、`"Month"`或 。 |
| `interval` | 計劃觸發的頻率。 整數值是等待計劃再次觸發的時間單位數。 |
| `start_time` | 計劃的開始時間。 值的字串格式為`YYYY-MM-DDThh:mm:ss`。 如果未提供開始時間,則立即運行第一個工作負載,並根據計劃運行將來的工作負載。 如果開始時間是過去的,則第一個工作負載在下一個計算的運行時間運行。 |
| `time_zone` | 開始時間的時區。 如果未提供時區,則使用UTC。 |
| `hours` | 如果`frequency``"Day"``"Week"`是或,則可以指定從 0 到 23 的一個或多個整數(用逗號分隔)作為管道應運行的一天中的小時數。 只能`time_of_day`或`hours``minutes`和可以使用。 |
| `minutes` | 如果`frequency``"Day"``"Week"`是或,則可以指定從 0 到 59 的一個或多個整數(用逗號分隔)作為管道應運行的小時分鐘數。 只能`time_of_day`或`hours``minutes`和可以使用。 |
| `time_of_day` | 如果是`frequency``"Day"`或`"Week"`,則可以指定計劃運行的一天中的時間。 值的字串格式為`hh:mm`。 只能`time_of_day`或`hours``minutes`和可以使用。 |
| `week_days` | 如果是`frequency``"Week"`,則可以指定計劃應運行時用逗號分隔的一個或多個天數。 Valid values `"Monday"` `"Tuesday"`are `"Wednesday"` `"Thursday"`, `"Friday"` `"Saturday"`, `"Sunday"`, , , , and . |

以下範例包含定期計劃的定義:

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

瞭解如何將[CLI 延伸用於 Azure 機器學習](reference-azure-machine-learning-cli.md)。
