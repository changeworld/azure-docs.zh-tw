---
title: 使用估算程式將 ML 模型定型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure 機器學習估算器類對傳統機器學習和深度學習模型執行單節點和分散式培訓
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: a9a3c10687b92d946fccb282f2eda4af637cf2ab
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686762"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>藉由估算器使用 Azure Machine Learning 將模型定型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

使用 Azure 機器學習,您可以使用[Run 設定物件](how-to-set-up-training-targets.md#whats-a-run-configuration)與[文稿 RunConfig 物件](how-to-set-up-training-targets.md#submit)輕鬆將訓練文稿提交到[各種計算目標](how-to-set-up-training-targets.md#compute-targets-for-training)。 這種模式可提升彈性並達到最大的控制度。

為協助進行深入的學習模式定型，Azure Machine Learning Python SDK 提供更高層級抽象 (亦即預估器類別)，可供使用者輕鬆地建構回合組態。 您可以建立和使用通用[估計器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py),使用您選擇的任何學習框架(如 scikit-learn)在您選擇的任何計算目標上提交訓練文稿,無論是本地電腦、Azure 中的單個 VM 還是 Azure 中的 GPU 群集。 對於 PyTorch、TensorFlow 和鏈子任務,Azure 機器學習還提供各自的[PyTorch、TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)和[鏈式估計器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py),以[PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)簡化使用這些框架。

## <a name="train-with-an-estimator"></a>使用預估器來定型

建立您的[工作區](concept-workspace.md)並設定您的[開發環境](how-to-configure-environment.md)後，請將 Azure Machine Learning 中的模型定型牽涉到下列步驟：  
1. 建立[遠端計算目標](how-to-set-up-training-targets.md) (請注意，您也可以使用本機電腦作為計算目標)
2. 將您的[定型資料](how-to-access-data.md)上傳到資料存放區 (選擇性)
3. 建立[培訓文稿](tutorial-train-models-with-aml.md#create-a-training-script)
4. 建立 `Estimator` 物件
5. 將估計器提交到工作區下的實驗物件

此文章的重點在於步驟 4-5。 針對步驟 1-3，請參閱[模型定型教學課程](tutorial-train-models-with-aml.md)以取得範例。

### <a name="single-node-training"></a>單一節點定型

使用 `Estimator` 在 Azure 中遠端計算上針對 scikit-learn 模型執行單一節點定型。 您應該建立計算[目標](how-to-set-up-training-targets.md#amlcompute)物件`compute_target`與[檔案資料集](how-to-create-register-datasets.md)物件`ds`。

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
`source_directory`| 包含定型作業所需之所有程式碼的本機目錄。 此資料夾從本地電腦複製到遠端計算。
`script_params`| 字典指定命令列參數以對的形式`entry_script``<command-line argument, value>`傳遞給訓練文本。 您可以`script_params`選擇詳細的旗標,`<command-line argument, "">`請使用 。
`compute_target`| 訓練文本將在其上運行的遠端計算目標,在這種情況下,是 Azure 機器學習計算 ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) 群集。 (注意即使 AmlCompute 群集是常用的目標,也可以選擇其他計算目標類型,如 Azure VM 甚至本地電腦。
`entry_script`| 要在遠端計算上執行之定型指令碼的檔案路徑 (相對於 `source_directory`)。 此檔及其所依賴的任何其他檔應位於此資料夾中。
`conda_packages`| 要透過 Conda 安裝的 Python 套件清單 (其中包含您的定型指令碼所需的套件)。  

建構函數具有另一個參數`pip_packages`,稱為用於任何需要的點包。

您現在已建立 `Estimator` 物件，請透過在您的 [Experiment](concept-azure-machine-learning-architecture.md#experiments) 物件 `experiment` 上呼叫 `submit` 函式，提交要在遠端計算上執行的定型作業。 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **特殊資料夾** Azure Machine Learning 會特別對待 *outputs* 與 *logs*, 這兩個資料夾。 在定型期間，當您將檔案寫入到相對於根目錄且名為 *outputs* 與 *logs* 的資料夾 (分別是 `./outputs` 與 `./logs`) 時，這些檔案會自動上傳到執行歷程記錄，因此當您的回合完成之後，您就能存取它們。
>
> 若要在定型期間建立成品 (例如模型檔案、檢查點、資料檔案或繪製的影像)，請將這些成品寫入到 `./outputs` 資料夾。
>
> 同樣地，您也可以將來自您定型回合的任何記錄寫入到 `./logs` 資料夾。 若要利用 Azure Machine Learning 的 [TensorBoard 整合](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb)，請務必將您的 TensorBoard 記錄寫入到此資料夾。 當您的回合在執行時，您將能啟動 TensorBoard 並串流這些記錄。  稍後，您也可以從任何先前的回合還原記錄。
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
`custom_docker_image`| 您要使用的映像名稱。 只提供公用 Docker 存放庫 (在此案例中是 Docker Hub) 中可用的映像。 若要使用來自私人 Docker 存放庫的映像，請改為使用建構函式的 `environment_definition` 參數。 [請參考範例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)。 | `None`
`node_count`| 用於定型作業的節點數目。 | `1`
`process_count_per_node`| 要在每個節點上執行的處理序 (或「背景工作角色」) 數目。 在此案例中，您會使用每個節點上可用的 `2` 個 GPU。| `1`
`distributed_training`| [MPI 設定](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py)物件,用於使用 MPI 後端啟動分散式訓練。  | `None`


最後，提交定型作業：
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>註冊模型

訓練模型后,可以將其保存並註冊到工作區。 模型註冊允許您在工作區中儲存和版本模型,以簡化[模型管理和部署](concept-model-management-and-deployment.md)。

運行以下代碼會將模型註冊到工作區,並使模型可用於遠端計算上下文或部署腳本中按名稱引用。 有關詳細資訊[`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)和其他參數,請參閱參考文檔。

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>GitHub 追蹤並整合

當您啟動訓練運行時,源目錄是本地 Git 儲存庫時,有關儲存庫的資訊存儲在運行歷史記錄中。 有關詳細資訊,請參閱[Azure 機器學習的 Git 整合](concept-train-model-git-integration.md)。

## <a name="examples"></a>範例
對於顯示估計器模式基礎知識的筆記本,請參閱:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

對於使用估計器訓練學習模型的筆記本,請參閱:
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

有關使用深度學習框架特定估計器的培訓模型的筆記本,請參閱:

* [如何使用-azureml/ml 框架](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [將 PyTorch 模型定型](how-to-train-pytorch.md)
* [將 TensorFlow 模型定型](how-to-train-tensorflow.md)
* [微調超參數](how-to-tune-hyperparameters.md)
* [部署已完成訓練的模型](how-to-deploy-and-where.md)
* [建立與管理用於培訓與部署的環境](how-to-use-environments.md)