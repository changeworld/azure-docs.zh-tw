---
title: " (預覽版) 訓練和部署增強式學習模型。"
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 增強式學習 (preview) 來訓練 RL 代理程式以進行 play。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: peterlu
author: peterclu
ms.date: 05/05/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 6221b36263b55f54faef18d6596f97c5b3798d3d
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541708"
---
# <a name="reinforcement-learning-preview-with-azure-machine-learning"></a>具有 Azure Machine Learning 的增強式學習 (預覽)



> [!NOTE]
> Azure Machine Learning 增強式學習目前是一項預覽功能。 目前只支援 Ray 和 RLlib 架構。

在本文中，您將了解如何定型增強式學習 (RL) 代理程式，以進行 Pong 電玩遊戲。 您將會使用開放原始碼 Python 程式庫 [Ray RLlib](https://ray.readthedocs.io/en/master/rllib.html) 搭配 Azure Machine Learning 來管理分散式 RL 作業的複雜性。

在本文中，您將了解如何：
> [!div class="checklist"]
> * 設定實驗
> * 定義前端和背景工作節點
> * 建立 RL 估算器
> * 提交實驗以開始執行
> * 檢視結果

本文是以 [RLlib Pong 範例](https://aka.ms/azureml-rl-pong)為基礎，可在 Azure Machine Learning 筆記本 [GitHub 存放庫](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md)中找到。

## <a name="prerequisites"></a>Prerequisites

在下列其中一個環境中建立執行此程式碼。 建議您嘗試 Azure Machine Learning 計算執行個體，以獲得最快速的啟動體驗。 增強式範例筆記本可快速複製並在 Azure Machine Learning 計算執行個體上執行。

 - Azure Machine Learning 計算執行個體

     - 了解如何在下列教學課程中複製範例筆記本 - [教學課程：設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)。
         - 複製 **how-to-use-azureml** 資料夾，而不是 **教學課程**
     - 執行位於 `/how-to-use-azureml/reinforcement-learning/setup/devenv_setup.ipynb` 的虛擬網路安裝筆記本，以開啟用於分散式增強式學習的網路連接埠。
     - 執行範例筆記本 `/how-to-use-azureml/reinforcement-learning/atari-on-distributed-compute/pong_rllib.ipynb`
 
 - 您自己的 Jupyter Notebook 伺服器

    - 安裝 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)。
    - 安裝 [Azure Machine Learning RL SDK](https://docs.microsoft.com/python/api/azureml-contrib-reinforcementlearning/?view=azure-ml-py&preserve-view=true)：`pip install --upgrade azureml-contrib-reinforcementlearning`
    - 建立[工作區組態檔](how-to-configure-environment.md#workspace)。
    - 執行虛擬網路[安裝筆記本](https://aka.ms/azure-rl-env-setup)，以開啟用於分散式增強式學習的網路連接埠。


## <a name="how-to-train-a-pong-playing-agent"></a>如何訓練 Pong 遊戲代理程式

增強式學習 (RL) 是機器學習的一種方法，可讓機器學習透過實際進行來學習。 其他機器學習服務技術會被動地取得輸入資料並尋找其中的模式，而 RL 會使用**訓練代理程式**，主動做出決策並從結果中學習。

您的訓練代理程式會學習了解如何在**模擬的環境**中玩 Pong。 訓練代理程式會在遊戲的每個畫格上做出決定，以將球拍向上、向下或保持在原處。 其會查看遊戲的狀態 (螢幕的 RGB 影像) 來做出決定。

RL 會使用**報酬**，告訴代理程式其決策是否成功。 在此環境中，代理程式會在得分時取得正面獎勵，並在未得分時獲得負面獎勵。 經過多次反覆動作之後，訓練代理程式會根據其目前狀態來學習選擇動作，針對預期未來會得到的獎勵總和進行最佳化。

通常會使用**深度類神經網路** (DNN) 模型，在 RL 中執行這項最佳化。 一開始，學習代理程式的執行效能不佳，但每個遊戲都會產生額外的範例，以進一步改善模型。

當代理程式在訓練期間中達成平均 18 分的奬勵分數時，即結束訓練。 這表示代理程式在最多 21 個回合中，以至少平均 18 點的分數擊敗對手。

逐一查看模擬和重新訓練 DNN 的流程會耗用大量運算資源，而且需要大量資料。 若要改善 RL 作業的效能，其中一種方法是**平行處理工作**，讓多個訓練代理程式可以採取行動並同時學習。 不過，管理分散式 RL 環境是一項複雜的工作。

Azure Machine Learning 提供架構來管理這些複雜性，以擴增您的 RL 工作負載。

## <a name="set-up-the-environment"></a>設定 Azure 環境

透過載入所需的 Python 套件、初始化您的工作區、建立實驗，以及指定已設定的虛擬網路，來設定本機 RL 環境。

### <a name="import-libraries"></a>匯入程式庫

匯入必要的 Python 套件，以執行此範例的其餘部分。

```python
# Azure ML Core imports
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
from azureml.core.runconfig import EnvironmentDefinition
from azureml.widgets import RunDetails
from azureml.tensorboard import Tensorboard

# Azure ML Reinforcement Learning imports
from azureml.contrib.train.rl import ReinforcementLearningEstimator, Ray
from azureml.contrib.train.rl import WorkerConfiguration
```

### <a name="initialize-a-workspace"></a>初始化工作區

[Azure Machine Learning 工作區](concept-workspace.md)是 Azure Machine Learning 的最上層資源。 其可提供集中式位置以處理您建立的所有成品。

從 [必要條件一節](#prerequisites)中建立的 `config.json` 檔案初始化工作區物件。 如果您在 Azure Machine Learning 計算執行個體中執行此程式碼，系統已經為您建立了組態檔。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-reinforcement-learning-experiment"></a>建立增強式學習實驗

建立[實驗](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true)來追蹤您的增強式學習執行。 在 Azure Machine Learning 中，實驗是相關試用的邏輯集合，用來組織執行記錄、歷程記錄、輸出等等。

```python
experiment_name='rllib-pong-multi-node'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="specify-a-virtual-network"></a>指定虛擬網路

對於使用多個計算目標的 RL 作業，您必須指定具有開啟連接埠的虛擬網路，以允許背景工作節點和前端節點彼此通訊。 虛擬網路可以位於任何資源群組中，但其應該與您的工作區位於相同的區域。 如需設定虛擬網路的詳細資訊，請參閱必要條件一節中的[工作區設定筆記本](https://aka.ms/azure-rl-env-setup)。 在這裡，您會在資源群組中指定虛擬網路的名稱。

```python
vnet = 'your_vnet'
```

## <a name="define-head-and-worker-compute-targets"></a>定義前端和背景工作計算目標

這個範例會針對 Ray 前端和背景工作節點使用不同的計算目標。 這些設定可讓您根據預期的工作負載，擴大或縮小計算資源。 根據您的實驗需求，設定節點數目和每個節點的大小。

### <a name="head-computing-target"></a>前端計算目標

這個範例會使用具有 GPU 的前端叢集，將深度學習效能最佳化。 前端節點會定型代理程式用來做出決策的類神經網路。 前端節點也會從背景工作節點收集資料點，以進一步將類神經網路定型。

前端計算會使用單一 [`STANDARD_NC6` 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/nc-series) (VM)。 其有 6 個虛擬 CPU，這表示可以將工作分散到 6 個工作 CPU。


```python
from azureml.core.compute import AmlCompute, ComputeTarget

# choose a name for the Ray head cluster
head_compute_name = 'head-gpu'
head_compute_min_nodes = 0
head_compute_max_nodes = 2

# This example uses GPU VM. For using CPU VM, set SKU to STANDARD_D2_V2
head_vm_size = 'STANDARD_NC6'

if head_compute_name in ws.compute_targets:
    head_compute_target = ws.compute_targets[head_compute_name]
    if head_compute_target and type(head_compute_target) is AmlCompute:
        print(f'found head compute target. just use it {head_compute_name}')
else:
    print('creating a new head compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = head_vm_size,
                                                                min_nodes = head_compute_min_nodes, 
                                                                max_nodes = head_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    head_compute_target = ComputeTarget.create(ws, head_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    head_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(head_compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="worker-computing-cluster"></a>背景工作計算叢集

這個範例會針對背景工作角色計算目標使用四個 [`STANDARD_D2_V2` VM](https://docs.microsoft.com/azure/virtual-machines/nc-series)。 每個背景工作節點都有 2 個可用的 CPU，總共有 8 個可用的 CPU 可平行處理工作。

背景工作節點不需要 GPU，因為節點不會執行深度學習。 背景工作會執行遊戲模擬並收集資料。

```python
# choose a name for your Ray worker cluster
worker_compute_name = 'worker-cpu'
worker_compute_min_nodes = 0 
worker_compute_max_nodes = 4

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
worker_vm_size = 'STANDARD_D2_V2'

# Create the compute target if it hasn't been created already
if worker_compute_name in ws.compute_targets:
    worker_compute_target = ws.compute_targets[worker_compute_name]
    if worker_compute_target and type(worker_compute_target) is AmlCompute:
        print(f'found worker compute target. just use it {worker_compute_name}')
else:
    print('creating a new worker compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = worker_vm_size,
                                                                min_nodes = worker_compute_min_nodes, 
                                                                max_nodes = worker_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    worker_compute_target = ComputeTarget.create(ws, worker_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    worker_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(worker_compute_target.get_status().serialize())
```

## <a name="create-a-reinforcement-learning-estimator"></a>建立增強式學習估算器

在本節中，您將了解如何使用 [ReinforcementLearningEstimator](https://docs.microsoft.com/python/api/azureml-contrib-reinforcementlearning/azureml.contrib.train.rl.reinforcementlearningestimator?view=azure-ml-py&preserve-view=true)，將訓練作業提交至 Azure Machine Learning。

Azure Machine Learning 會使用估算器類別來封裝回合組態資訊。 這可讓您輕鬆地指定如何設定指令碼執行。 

### <a name="define-a-worker-configuration"></a>定義背景工作組態

WorkerConfiguration 物件會告訴 Azure Machine Learning 如何將執行輸入腳本的背景工作叢集初始化。

```python
# Pip packages we will use for both head and worker
pip_packages=["ray[rllib]==0.8.3"] # Latest version of Ray has fixes for isses related to object transfers

# Specify the Ray worker configuration
worker_conf = WorkerConfiguration(
    
    # Azure ML compute cluster to run Ray workers
    compute_target=worker_compute_target, 
    
    # Number of worker nodes
    node_count=4,
    
    # GPU
    use_gpu=False, 
    
    # PIP packages to use
    pip_packages=pip_packages
)
```

### <a name="define-script-parameters"></a>定義指令碼參數

輸入腳本 `pong_rllib.py` 會接受定義如何執行訓練作業的參數清單。 透過估算器傳遞這些參數作為封裝層，可讓您輕鬆地獨立變更指令碼參數和回合組態。

指定正確的 `num_workers` 將會充分利用您的平行處理工作。 將背景工作的數目設定為與可用的 CPU 數目相同。 在此範例中，計算方式如下：

前端節點是具有 6 個 vCPU 的 [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series)。 背景工作叢集為 4 個 [Standard_D2_V2 VMs](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs#dv2-series) 具有 2 個 CPU，總共 8 個 CPU。 不過，您必須從背景工作計數減去 1 個 CPU，因為有 1 個 CPU 必須供前端節點角色專用。 6 個 CPU + 8 個 CPU -1 個前端 CPU = 13 個同時工作的背景工作。 Azure Machine Learning 會使用前端和背景工作叢集來區別計算資源。 不過，Ray 不會區分前端和背景工作，而且所有 CPU 都是可供背景工作執行緒執行的 CPU。


```python
training_algorithm = "IMPALA"
rl_environment = "PongNoFrameskip-v4"

# Training script parameters
script_params = {
    
    # Training algorithm, IMPALA in this case
    "--run": training_algorithm,
    
    # Environment, Pong in this case
    "--env": rl_environment,
    
    # Add additional single quotes at the both ends of string values as we have spaces in the 
    # string parameters, outermost quotes are not passed to scripts as they are not actually part of string
    # Number of GPUs
    # Number of ray workers
    "--config": '\'{"num_gpus": 1, "num_workers": 13}\'',
    
    # Target episode reward mean to stop the training
    # Total training time in seconds
    "--stop": '\'{"episode_reward_mean": 18, "time_total_s": 3600}\'',
}
```

### <a name="define-the-reinforcement-learning-estimator"></a>定義增強式學習估算器

使用參數清單和背景工作組態物件來建構估算器。

```python
# RL estimator
rl_estimator = ReinforcementLearningEstimator(
    
    # Location of source files
    source_directory='files',
    
    # Python script file
    entry_script="pong_rllib.py",
    
    # Parameters to pass to the script file
    # Defined above.
    script_params=script_params,
    
    # The Azure ML compute target set up for Ray head nodes
    compute_target=head_compute_target,
    
    # Pip packages
    pip_packages=pip_packages,
    
    # GPU usage
    use_gpu=True,
    
    # RL framework. Currently must be Ray.
    rl_framework=Ray(),
    
    # Ray worker configuration defined above.
    worker_configuration=worker_conf,
    
    # How long to wait for whole cluster to start
    cluster_coordination_timeout_seconds=3600,
    
    # Maximum time for the whole Ray job to run
    # This will cut off the run after an hour
    max_run_duration_seconds=3600,
    
    # Allow the docker container Ray runs in to make full use
    # of the shared memory available from the host OS.
    shm_size=24*1024*1024*1024
)
```

### <a name="entry-script"></a>輸入腳本

[輸入腳本](https://aka.ms/azure-rl-pong-script) `pong_rllib.py` 會使用 [OpenAI Gym 環境](https://github.com/openai/gym/) `PongNoFrameSkip-v4` 來定型類神經網路。 OpenAI Gyms 是標準化的介面，可在傳統 Atari 遊戲上測試增強式學習演算法。

這個範例會使用稱為 [IMPALA](https://arxiv.org/abs/1802.01561)(重要性加權執行者-學習者架構) 的訓練演算法。 IMPALA 會平行處理每個個別的學習執行者，在多個計算節點之間進行調整，而不會犧牲速度或穩定性。

[Ray Tune](https://ray.readthedocs.io/en/latest/tune.html) 會協調 IMPALA 的背景工作。

```python
import ray
import ray.tune as tune
from ray.rllib import train

import os
import sys

from azureml.core import Run
from utils import callbacks

DEFAULT_RAY_ADDRESS = 'localhost:6379'

if __name__ == "__main__":

    # Parse arguments
    train_parser = train.create_parser()

    args = train_parser.parse_args()
    print("Algorithm config:", args.config)

    if args.ray_address is None:
        args.ray_address = DEFAULT_RAY_ADDRESS

    ray.init(address=args.ray_address)

    tune.run(run_or_experiment=args.run,
             config={
                 "env": args.env,
                 "num_gpus": args.config["num_gpus"],
                 "num_workers": args.config["num_workers"],
                 "callbacks": {"on_train_result": callbacks.on_train_result},
                 "sample_batch_size": 50,
                 "train_batch_size": 1000,
                 "num_sgd_iter": 2,
                 "num_data_loader_buffers": 2,
                 "model": {
                    "dim": 42
                 },
             },
             stop=args.stop,
             local_dir='./logs')
```

### <a name="logging-callback-function"></a>記錄回撥函式


輸入腳本會使用公用程式函式來定義 [自訂 RLlib 回撥函式](https://docs.ray.io/en/latest/rllib-training.html#callbacks-and-custom-metrics)，將計量記錄到您的 Azure Machine Learning 工作區。 在[監視和檢視結果](#monitor-and-view-results)一節中檢視這些計量。

```python
'''RLlib callbacks module:
    Common callback methods to be passed to RLlib trainer.
'''
from azureml.core import Run

def on_train_result(info):
    '''Callback on train result to record metrics returned by trainer.
    '''
    run = Run.get_context()
    run.log(
        name='episode_reward_mean',
        value=info["result"]["episode_reward_mean"])
    run.log(
        name='episodes_total',
        value=info["result"]["episodes_total"])
```

## <a name="submit-a-run"></a>提交執行

[執行](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) 會處理進行中或完成作業的執行歷程記錄。 

```python
run = exp.submit(config=rl_estimator)
```
> [!NOTE]
> 最多需要 30 到 45 分鐘才能完成此執行。

## <a name="monitor-and-view-results"></a>監視及檢視結果

使用 Azure Machine Learning Jupyter 小工具，即時查看您的執行狀態。 在此範例中，小工具會顯示兩個子執行：一個用於前端，另一個用於背景工作。 

```python
from azureml.widgets import RunDetails

RunDetails(run).show()
run.wait_for_completion()
```

1. 等待小工具載入。
1. 在執行清單中選取前端執行。

選取 [按一下這裡以查看 Azure Machine Learning Studio 中的執行] 以取得工作室中的其他執行資訊。 在執行期間或完成後，您可以存取這項資訊。

![顯示執行詳細資料小工具的折線圖](./media/how-to-use-reinforcement-learning/pong-run-details-widget.png)

**episode_reward_mean** 圖顯示每個訓練期間所獲得的平均點數。 您可以看到訓練代理程式一開始的執行成效不佳、並未獲得相符項目也未得分 (reward_mean 顯示為 -21)。 在 100 次的反覆過程中，訓練代理程式已學會以平均 18 點的分數擊敗電腦對手。

如果您瀏覽子執行的記錄，可以在 driver_log.txt 檔案中看到記錄的評估結果。 在「執行」頁面上，您可能需要等候幾分鐘，才會看到這些計量。

您無需花費太多時間，就能了解如何設定多個計算資源將增強式學習代理程式定，讓 Pong 遊戲更加上手。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何使用 IMPALA 學習代理程式將增強式學習代理程式定型。 若要查看其他範例，請移至 [Azure Machine Learning 增強式學習 GitHub 存放庫](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md)。
