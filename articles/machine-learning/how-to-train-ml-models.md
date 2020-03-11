---
title: 使用估算程式將 ML 模型定型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 估計工具類別來執行傳統機器學習和深度學習模型的單一節點和分散式訓練
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 678af1855baf52efa727444236de8a1724a7d0b0
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "79078488"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>藉由估算器使用 Azure Machine Learning 將模型定型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

透過 Azure Machine Learning，您可以使用[RunConfiguration 物件](how-to-set-up-training-targets.md#whats-a-run-configuration)和[ScriptRunConfig 物件](how-to-set-up-training-targets.md#submit)，輕鬆地將定型腳本提交至[各種計算目標](how-to-set-up-training-targets.md#compute-targets-for-training)。 這種模式可提升彈性並達到最大的控制度。

為協助進行深入的學習模式定型，Azure Machine Learning Python SDK 提供更高層級抽象 (亦即預估器類別)，可供使用者輕鬆地建構回合組態。 您可以建立和使用泛型[估計工具](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)，以在您選擇的任何計算目標上（例如 scikit-learn），使用您選擇的任何學習架構來提交定型腳本，不論是您的本機電腦、azure 中的單一 VM，或 azure 中的 GPU 叢集。 對於 PyTorch、TensorFlow 和 Chainer 工作，Azure Machine Learning 也會提供個別的[PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)、 [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)和[Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)估算器，以簡化使用這些架構的作業。

## <a name="train-with-an-estimator"></a>使用預估器來定型

建立您的[工作區](concept-workspace.md)並設定您的[開發環境](how-to-configure-environment.md)後，請將 Azure Machine Learning 中的模型定型牽涉到下列步驟：  
1. 建立[遠端計算目標](how-to-set-up-training-targets.md) (請注意，您也可以使用本機電腦作為計算目標)
2. 將您的[定型資料](how-to-access-data.md)上傳到資料存放區 (選擇性)
3. 建立您的[定型指令碼](tutorial-train-models-with-aml.md#create-a-training-script)
4. 建立 `Estimator` 物件
5. 將估計器提交到工作區下的實驗物件

此文章的重點在於步驟 4-5。 針對步驟 1-3，請參閱[模型定型教學課程](tutorial-train-models-with-aml.md)以取得範例。

### <a name="single-node-training"></a>單一節點定型

使用 `Estimator` 在 Azure 中遠端計算上針對 scikit-learn 模型執行單一節點定型。 您應該已經建立[計算目標](how-to-set-up-training-targets.md#amlcompute)物件 `compute_target` 和您的[FileDataset](how-to-create-register-datasets.md)物件 `ds`。

```Python
from azureml.train.estimator import Estimator

script_params = {
    # to mount files referenced by mnist dataset
    '--data-folder': ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

此程式碼片段會將下列參數指定給 `Estimator` 建構函式。

參數 | 描述
--|--
`source_directory`| 包含定型作業所需之所有程式碼的本機目錄。 此資料夾會從您的本機電腦複製到遠端計算。
`script_params`| 指定命令列引數的字典，`entry_script`，以 `<command-line argument, value>` 組的形式傳遞給您的定型腳本。 若要在 `script_params`中指定詳細資訊旗標，請使用 `<command-line argument, "">`。
`compute_target`| 您的定型指令碼執行所在的遠端計算目標，在此案例中為 Azure Machine Learning Compute ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) 叢集。 （請注意，即使 AmlCompute 叢集是常用的目標，也可以選擇其他計算目標型別，例如 Azure Vm 或甚至是本機電腦）。
`entry_script`| 要在遠端計算上執行之定型指令碼的檔案路徑 (相對於 `source_directory`)。 此檔案及其相依的任何其他檔案，都應該位於此資料夾中。
`conda_packages`| 要透過 Conda 安裝的 Python 套件清單 (其中包含您的定型指令碼所需的套件)。  

此函式具有另一個稱為 `pip_packages` 的參數，您可用於所需的任何 pip 套件。

您現在已建立 `Estimator` 物件，請透過在您的 `submit`Experiment[ 物件 ](concept-azure-machine-learning-architecture.md#experiments) 上呼叫 `experiment` 函式，提交要在遠端計算上執行的定型作業。 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **特殊資料夾** Azure Machine Learning 會特別對待 *outputs* 與 *logs*, 這兩個資料夾。 在定型期間，當您將檔案寫入到相對於根目錄且名為 *outputs* 與 *logs* 的資料夾 (分別是 `./outputs` 與 `./logs`) 時，這些檔案會自動上傳到執行歷程記錄，因此當您的回合完成之後，您就能存取它們。
>
> 若要在定型期間建立成品 (例如模型檔案、檢查點、資料檔案或繪製的影像)，請將這些成品寫入到 `./outputs` 資料夾。
>
> 同樣地，您也可以將來自您定型回合的任何記錄寫入到 `./logs` 資料夾。 若要利用 Azure Machine Learning 的 [TensorBoard 整合](https://aka.ms/aml-notebook-tb)，請務必將您的 TensorBoard 記錄寫入到此資料夾。 當您的回合在執行時，您將能啟動 TensorBoard 並串流這些記錄。  稍後，您也可以從任何先前的回合還原記錄。
>
> 例如，若要在您的遠端定型回合執行完成之後將寫入到 *outputs* 資料夾的檔案下載到您的本機電腦，請執行：`run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>分散式定型與自訂 Docker 映像

您可以使用 `Estimator` 執行兩個額外的定型案例：
* 使用自訂 Docker 映像
* 多節點叢集上的分散式定型

下列程式碼顯示如何執行 Keras 模型的分散式定型。 此外，這會指定來自 Docker Hub `continuumio/miniconda` 的自訂 Docker 映像進行定型，而不是使用預設的 Azure Machine Learning 映像。

您應該已建立了自己的[計算目標](how-to-set-up-training-targets.md#amlcompute)物件`compute_target`。 您會依照下列方式建立估算器：

```Python
from azureml.train.estimator import Estimator
from azureml.core.runconfig import MpiConfiguration

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),        
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_image='continuumio/miniconda')
```

上面的程式碼公開下列新參數給 `Estimator` 建構函式：

參數 | 描述 | 預設
--|--|--
`custom_docker_image`| 您要使用的映像名稱。 只提供公用 Docker 存放庫 (在此案例中是 Docker Hub) 中可用的映像。 若要使用來自私人 Docker 存放庫的映像，請改為使用建構函式的 `environment_definition` 參數。 [請參閱範例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)。 | `None`
`node_count`| 用於定型作業的節點數目。 | `1`
`process_count_per_node`| 要在每個節點上執行的處理序 (或「背景工作角色」) 數目。 在此案例中，您會使用每個節點上可用的 `2` 個 GPU。| `1`
`distributed_training`| 使用 MPI 後端啟動分散式定型的[MPIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py)物件。  | `None`


最後，提交定型作業：
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>註冊模型

定型模型之後，您可以將它儲存並註冊到您的工作區。 模型註冊可讓您在工作區中儲存模型並為其建立版本，以簡化[模型管理和部署](concept-model-management-and-deployment.md)。

執行下列程式碼將會向您的工作區註冊模型，並讓它可在遠端計算內容或部署腳本中依名稱參考。 如需詳細資訊和其他參數，請參閱參考檔中的[`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) 。

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>GitHub 追蹤與整合

當您啟動定型回合，其中來原始目錄是本機 Git 存放庫時，儲存機制的相關資訊會儲存在執行歷程記錄中。 如需詳細資訊，請參閱[Azure Machine Learning 的 Git 整合](concept-train-model-git-integration.md)。

## <a name="examples"></a>範例
如需顯示估計工具模式基本概念的筆記本，請參閱：
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

如需使用估計工具訓練 scikit-learn 學習模型的筆記本，請參閱：
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

如需使用深度學習架構特定估算器的訓練模型筆記本，請參閱：

* [使用方法-azureml/ml-架構](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [將 PyTorch 模型定型](how-to-train-pytorch.md)
* [將 TensorFlow 模型定型](how-to-train-tensorflow.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
* [建立和管理用於定型和部署的環境](how-to-use-environments.md)