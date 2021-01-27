---
title: 從估算器遷移至 ScriptRunConfig
titleSuffix: Azure Machine Learning
description: 從估算器遷移至 ScriptRunConfig 以設定定型作業的遷移指南。
services: machine-learning
author: mx-iao
ms.author: minxia
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 12/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: d603a12f851dac5b7cefc5bad728d42967bb27dc
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878590"
---
# <a name="migrating-from-estimators-to-scriptrunconfig"></a>從估算器遷移至 ScriptRunConfig

到目前為止，有多種方法可透過 SDK 在 Azure Machine Learning 中設定定型作業，包括估算器、ScriptRunConfig 和較低層級的 RunConfiguration。   為了解決這種不一致和不一致的情況，我們將簡化 Azure ML 中的作業設定流程。  您現在應該使用 ScriptRunConfig 作為設定定型作業的建議選項。 

估算器已在 Python SDK 的1.19.0 版本中淘汰。 您通常也可以自行避免明確具現化 RunConfiguration 物件，而改為使用 ScriptRunConfig 類別來設定作業。

本文涵蓋從估算器遷移至 ScriptRunConfig 時的一般考慮。

> [!IMPORTANT]
> 若要從估算器遷移至 ScriptRunConfig，請確定您使用的是 Python SDK 的 >= 1.15.0。

## <a name="scriptrunconfig-documentation-and-samples"></a>ScriptRunConfig 檔和範例
Azure Machine Learning 的檔和範例已更新為使用 [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?preserve-view=true&view=azure-ml-py) 進行作業設定和提交。

如需使用 ScriptRunConfig 的詳細資訊，請參閱下列檔：
* [設定和提交定型執行](how-to-set-up-training-targets.md)
* [設定 PyTorch 訓練回合](how-to-train-pytorch.md)
* [設定 TensorFlow 訓練回合](how-to-train-tensorflow.md)
* [設定 scikit-learn-學習訓練回合](how-to-train-scikit-learn.md)

此外，請參閱下列範例 & 教學課程：
* [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)
* [Azure/azureml-examples](https://github.com/Azure/azureml-examples)

## <a name="defining-the-training-environment"></a>定義定型環境
雖然不同的架構估算器具有 Docker 映射所支援的預先設定環境，但這些映射的 Dockerfile 是私用的。  因此，您對這些環境所包含的內容並沒有太大的透明度。 此外，估算器會將環境相關的設定視為個別參數 (例如 `pip_packages` ， `custom_docker_image`) 在各自的函式上。

使用 ScriptRunConfig 時，所有環境相關的設定都會封裝在 `Environment` 傳遞至 ScriptRunConfig 函式之參數的物件中 `environment` 。 若要設定定型作業，請提供具有訓練腳本所需之所有相依性的環境。 如果未提供任何環境，Azure ML 會使用其中一個 Azure ML 基底映射（特別是所定義的映射） `azureml.core.environment.DEFAULT_CPU_IMAGE` 作為預設環境。 有幾種方式可以提供環境：

* [使用策劃環境](how-to-use-environments.md#use-a-curated-environment) -策劃環境是您的工作區中預設可用的預先定義環境。 每個已預先設定的架構/版本 Docker 映射都有對應的策劃環境，每個都支援架構估算器。
* [定義您自己的自訂環境](how-to-use-environments.md)

以下是使用策劃 PyTorch 1.6 環境進行訓練的範例：

```python
from azureml.core import Workspace, ScriptRunConfig, Environment

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)

compute_target = ws.compute_targets['my-cluster']
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env)
```

如果您想要指定將在執行定型腳本的程式上設定的 **環境變數** ，請使用環境物件：
```
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

如需設定和管理 Azure ML 環境的詳細資訊，請參閱：
* [如何使用環境](how-to-use-environments.md)
* [策展的環境](resource-curated-environments.md)
* [使用自訂 Docker 映射進行定型](how-to-train-with-custom-image.md)

## <a name="using-data-for-training"></a>使用資料進行定型
### <a name="datasets"></a>資料集
如果您使用 Azure ML 資料集進行定型，請使用參數將資料集當作引數傳遞至您的腳本 `arguments` 。 如此一來，您就能透過引數，在定型腳本中取得 (掛接點或下載路徑) 的資料路徑。

下列範例會設定定型作業，其中 FileDataset `mnist_ds` 會裝載于遠端計算上。
```python
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', mnist_ds.as_mount()], # or mnist_ds.as_download() to download
                      compute_target=compute_target,
                      environment=pytorch_env)
```

### <a name="datareference-old"></a>DataReference (舊) 
雖然我們建議您以舊的 DataReference 方式使用 Azure ML 資料集，但如果您基於任何原因仍在使用 DataReferences，則必須設定您的作業，如下所示：
```python
# if you want to pass a DataReference object, such as the below:
datastore = ws.get_default_datastore()
data_ref = datastore.path('./foo').as_mount()

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', str(data_ref)], # cast the DataReference object to str
                      compute_target=compute_target,
                      environment=pytorch_env)
src.run_config.data_references = {data_ref.data_reference_name: data_ref.to_config()} # set a dict of the DataReference(s) you want to the `data_references` attribute of the ScriptRunConfig's underlying RunConfiguration object.
```

如需使用資料進行定型的詳細資訊，請參閱：
* [使用 Azure ML 中的資料集進行定型](./how-to-train-with-datasets.md)

## <a name="distributed-training"></a>分散式定型
如果您需要設定用於定型的分散式工作，請在 ScriptRunConfig 函式中指定參數來進行 `distributed_job_config` 。 針對個別類型的分散式作業傳入 [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py)、 [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py)或 [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py) 。

下列範例會設定 PyTorch 訓練作業，以搭配 MPI/Horovod 使用分散式訓練：
```python
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2, process_count_per_node=2))
```

如需詳細資訊，請參閱
* [使用 PyTorch 的分散式訓練](how-to-train-pytorch.md#distributed-training)
* [使用 TensorFlow 的分散式訓練](how-to-train-tensorflow.md#distributed-training)

## <a name="miscellaneous"></a>其他
如果您基於任何原因而需要存取 ScriptRunConfig 的基礎 RunConfiguration 物件，您可以如下所示：
```
src.run_config
```

## <a name="next-steps"></a>後續步驟

* [設定和提交定型執行](how-to-set-up-training-targets.md)