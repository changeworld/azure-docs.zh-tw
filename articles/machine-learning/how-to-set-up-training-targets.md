---
title: 將計算目標用於模型培訓
titleSuffix: Azure Machine Learning
description: 設定機器學習服務模型定型的定型環境 (計算目標)。 您可以輕鬆地在定型環境之間切換。 在本機開始定型作業。 如果您需要相應放大，請切換至雲端式計算目標。
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/13/2020
ms.custom: seodec18
ms.openlocfilehash: 24c0d9955a857e8bbc1e1c09e600031a7541026c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296953"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>設置和使用計算目標進行模型培訓 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

使用 Azure 機器學習，可以根據各種資源或環境（統稱為[__計算目標__](concept-azure-machine-learning-architecture.md#compute-targets)）對模型進行訓練。 計算目標可以是本機電腦或雲端資源，例如 Azure Machine Learning Compute、Azure HDInsight 或遠端虛擬機器。  您也可以建立用於部署模型的計算目標，如[模型的部署位置和方法](how-to-deploy-and-where.md)中所述。

您可以使用 Azure 機器學習 SDK、Azure 機器學習工作室、Azure CLI 或 Azure 機器學習 VS 代碼擴展創建和管理計算目標。 如果具有通過其他服務（例如 HDInsight 群集）創建的計算目標，則可以通過將目標附加到 Azure 機器學習工作區來使用它們。
 
在本文中，您將了解如何使用各種計算目標來訓練模型。  所有計算目標的步驟皆遵循相同的工作流程：
1. 如果還沒有計算目標，請__創建__計算目標。
2. 將計算目標__連結__至您的工作區。
3. __設定__計算目標，以便其包含您的指令碼所需的 Python 環境和封裝相依性。


>[!NOTE]
> 本文中的代碼已使用 Azure 機器學習 SDK 版本 1.0.74 進行了測試。

## <a name="compute-targets-for-training"></a>訓練用的計算目標

Azure 機器學習在不同的計算目標之間具有不同的支援。 一般模型開發生命週期會先開始開發/測試少量的資料。 在這個階段，我們建議使用本機環境。 例如，您的本機電腦或雲端式虛擬機器。 您對於較大的資料集相應增加您的定型或進行分散式定型時，建議使用 Azure Machine Learning Compute 建立單一或多重節點叢集，在每次提交執行時自動調整。 您也可以附加您自己的計算資源，不過支援的各種情節可能會有所不同，詳述如下：

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> 您可以將 Azure Machine Learning Compute 建立成持續性資源，也可以在要求回合時才以動態方式建立。 回合型建立會在定型回合完成後移除計算目標，因此您無法重複使用以此方式建立的計算目標。

## <a name="whats-a-run-configuration"></a>什麼是回合組態？

定型時，通常在本機電腦上啟動，然後在不同的計算目標上執行該定型指令碼。 使用 Azure 機器學習，您可以在各種計算目標上運行腳本，而無需更改腳本。

您只需在**回合組態**中為每個計算目標定義環境。  然後，當您想要在不同的計算目標上執行定型實驗時，為該計算指定回合組態。 有關指定環境並將其綁定為回合組態的詳細資訊，請參閱[創建和管理用於培訓和部署的環境](how-to-use-environments.md)。

深入了解本文末尾的[提交實驗](#submit)。

## <a name="whats-an-estimator"></a>什麼是估算器？

為了方便使用常用框架進行模型培訓，Azure 機器學習 Python SDK 提供了一種替代的更高級抽象（估計器類）。  此類允許您輕鬆構造回合組態。 您可以創建和使用通用[估計器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)提交使用您選擇的任何學習框架（如 scikit 學習）的培訓腳本。 我們建議使用評估器進行培訓，因為它會自動為您構造嵌入的物件（如環境或 Run設定物件）。 如果希望對這些物件的創建方式有更多的控制，並指定為實驗運行安裝的套裝程式，請按照[以下步驟](#amlcompute)使用 Azure 機器學習計算上的 Run設定物件提交訓練實驗。

對於 PyTorch、TensorFlow 和鏈子任務，Azure 機器學習還提供各自的[PyTorch、TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)和[鏈式估計器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)，以簡化使用這些框架。 [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)

有關詳細資訊，請參閱[使用估計值訓練 ML 模型](how-to-train-ml-models.md)。

## <a name="whats-an-ml-pipeline"></a>什麼是 ML 管道？

借助 ML 管道，您可以簡化、快速、可移植和重用來優化工作流。 使用 Azure 機器學習構建管道時，可以專注于專業知識、機器學習，而不是基礎結構和自動化。

ML 管道由多個**步驟**構造，這些步驟是管道中不同的計算單元。 每個步驟都可以獨立運行並使用隔離的計算資源。 這允許多個資料科學家在同一管道上同時工作，而不會對計算資源進行過重的徵稅，並且可以輕鬆地為每個步驟使用不同的計算類型/大小。

> [!TIP]
> 在訓練模型時，ML 管道可以使用回合組態或估計器。

雖然 ML 管道可以培訓模型，但它們也可以在培訓前準備資料，並在培訓後部署模型。 管道的主要用例之一是批次處理評分。 有關詳細資訊，請參閱[管道：優化機器學習工作流](concept-ml-pipelines.md)。

## <a name="set-up-in-python"></a>在 Python 中設置

使用各節來設定這些計算目標：

* [本機電腦](#local)
* [Azure Machine Learning Compute](#amlcompute)
* [遠端虛擬機器](#vm)
* [Azure HDInsight](#hdinsight)


### <a name="local-computer"></a><a id="local"></a>本機電腦

1. **創建和附加**：無需創建或附加計算目標，將本地電腦用作訓練環境。  

1. **配置**：當您使用本地電腦作為計算目標時，訓練代碼將運行在[開發環境中](how-to-configure-environment.md)。  如果該環境已經有您需要的 Python 封裝，請使用使用者管理的環境。

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

現在，您已經附加了計算並配置了運行，下一步是[提交訓練運行](#submit)。

### <a name="azure-machine-learning-compute"></a><a id="amlcompute"></a>Azure Machine Learning Compute

Azure Machine Learning Compute 是一種受控的計算基礎結構，可讓使用者輕鬆建立單一或多重節點計算。 計算會在工作區的區域內建立為能夠與工作區中的其他使用者共用的資源。 計算會在提交作業時自動相應增加，而且可以放在 Azure 虛擬網路中。 計算在容器化環境中執行，並將模型依賴項打包到[Docker 容器](https://www.docker.com/why-docker)中。

您可以使用 Azure Machine Learning Compute 在雲端中的 CPU 或 GPU 計算節點叢集散發定型程序。 如需包含 GPU 的 VM 大小有關的詳細資訊，請參閱 [GPU 最佳化虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)。

Azure Machine Learning Compute 有預設限制，例如可配置的核心數目。 如需詳細資訊，請參閱[管理和要求 Azure 資源的配額](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas)。


當您排定回合時，可以隨需建立 Azure Machine Learning 計算環境，也可以建立 Azure Machine Learning 計算環境作為持續性資源。

#### <a name="run-based-creation"></a>執行式建立

您可以建立 Azure Machine Learning Compute 做為執行階段的計算目標。 系統會自動為您的執行建立計算。 執行完成之後，就會自動刪除計算。 

> [!IMPORTANT]
> Azure Machine Learning Compute 的執行式建立目前為「預覽」狀態。 如果您使用自動化超參數調整或自動化機器學習，請勿使用回合式建立。 若要使用超參數微調或自動化機器學習，請改為建立[持續性計算](#persistent)目標。

1.  **創建、附加和配置**：基於運行的創建執行所有必要的步驟，使用回合組態創建、附加和配置計算目標。  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


現在，您已經附加了計算並配置了運行，下一步是[提交訓練運行](#submit)。

#### <a name="persistent-compute"></a><a id="persistent"></a>持久計算

持續性 Azure Machine Learning Compute 可以跨作業重複使用。 計算可以與工作區中的其他使用者共用，並且在作業之間保留。

1. **創建並附加**：要在 Python 中創建持久的 Azure 機器學習計算資源，請指定**vm_size**和**max_nodes**屬性。 Azure Machine Learning 接著會對於其他屬性使用智慧型預設值。 未使用時，計算會自動向下調整為零節點。   視需要建立專用的虛擬機器以執行您的作業。
    
    * **vm_size**： Azure 機器學習計算創建的節點的 VM 系列。
    * **max_nodes**： 在 Azure 機器學習計算上運行作業時，要自動縮放的最大節點數。
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   建立 Azure Machine Learning Compute 時，您也可以設定多個進階屬性。 這些屬性可讓您建立固定大小的持續性叢集，也可以在您訂用帳戶中現有的 Azure 虛擬網路內建立。  如需詳細資料，請參閱 [AmlCompute 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    )。
    
   或者，您可以在 Azure 機器學習[工作室](#portal-create)中創建並附加持久性 Azure 機器學習計算資源。

1. **配置**： 為持久計算目標創建回合組態。

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

現在，您已經附加了計算並配置了運行，下一步是[提交訓練運行](#submit)。


### <a name="remote-virtual-machines"></a><a id="vm"></a>遠端虛擬機器

Azure Machine Learning 也支援提供您自己的計算資源，並將其附加到您的工作區。 其中一種資源類型是任意遠端 VM，只要可從 Azure 機器學習訪問。 此資源可以是 Azure VM，也可以是在您組織或內部部署的遠端伺服器。 具體來說，只要有 IP 位址和認證 (使用者名稱和密碼或 SSH 金鑰)，即可對於遠端執行使用任何可存取的 VM。

您可以使用系統建立的 Conda 環境、現有的 Python 環境或 Docker 容器。 若要在 Docker 容器上執行，您必須在虛擬機器上執行的 Docker 引擎。 您想要比本機電腦的更有彈性、以雲端為基礎的開發/測試環境時，這項功能特別有用。

使用 Azure 資料科學虛擬機器 (DSVM) 作為對於此案例選擇的 Azure 虛擬機器。 此虛擬機器是 Azure 中預先設定的資料科學和 AI 開發環境。 VM 會針對整個生命週期的機器學習開發，提供精心選擇的工具和架構。 如需有關如何使用 DSVM 搭配 Azure Machine Learning 的詳細資訊，請參閱[設定開發環境](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm)。

1. **創建**：在使用它訓練模型之前創建 DSVM。 若要建立此資源，請參閱[佈建適用於 Linux (Ubuntu) 的資料科學虛擬機器](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)。

    > [!WARNING]
    > Azure Machine Learning 只支援執行 Ubuntu 的虛擬機器。 當您建立 VM 或選擇現有的 VM 時，您必須選取使用 Ubuntu 的 VM。

1. **附加**： 要將現有虛擬機器附加到計算目標，必須為虛擬機器提供完全限定的功能變數名稱 （FQDN）、使用者名和密碼。 在範例中，請以 VM 的公用 FQDN 或公用 IP 位址取代 \<fqdn>。 將 \<username> 和 \<password> 取代為 VM 的 SSH 使用者名稱和密碼。

    > [!IMPORTANT]
    > 以下 Azure 區域不支援使用 VM 的公共 IP 位址附加虛擬機器。 而是將 VM 的 Azure 資源管理器 ID`resource_id`與參數一起使用：
    >
    > * 美國東部
    > * 美國西部 2
    > * 美國中南部
    >
    > 可以使用以下字串格式使用訂閱 ID、資源組名稱和 VM 名稱構造 VM 的資源識別碼： `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`。


   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   attach_config = RemoteCompute.attach_configuration(address='<fqdn>',
                                                    ssh_port=22,
                                                    username='<username>',
                                                    password="<password>")
   # If in US East, US West 2, or US South Central, use the following instead:
   # attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
   #                                                 ssh_port=22,
   #                                                 username='<username>',
   #                                                 password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   或者，您可以使用[Azure 機器學習工作室](#portal-reuse)將 DSVM 附加到工作區。

1. **配置**：為 DSVM 計算目標創建回合組態。 Docker 和 Conda 用來建立和設定 DSVM 上的定型環境。

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


現在，您已經附加了計算並配置了運行，下一步是[提交訓練運行](#submit)。

### <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight 是巨量資料分析的常用平台。 此平台會提供 Apache Spark，可用來將您的模型定型。

1. **創建**：在使用 HDInsight 群集訓練模型之前，先創建該群集。 若要在 HDInsight 叢集上建立 Spark，請參閱[在 HDInsight 中建立 Spark 叢集](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)。 

    建立叢集時，必須指定 SSH 使用者名稱與密碼。 請記下這些值，因為使用 HDInsight 作為計算目標時會需要它們。
    
    建立叢集之後，使用下列主機名稱連結至叢集：\<clustername>-ssh.azurehdinsight.net，其中 \<clustername> 是您提供的叢集名稱。 

1. **附加**： 要將 HDInsight 群集附加到計算目標，必須為 HDInsight 群集提供主機名稱、使用者名和密碼。 下列範例會使用 SDK 將叢集附加到您的工作區。 在此範例中，將 \<clustername> 取代為叢集的名稱。 將 \<username> 和 \<password> 取代為叢集的 SSH 使用者名稱和密碼。

    > [!IMPORTANT]
    > 以下 Azure 區域不支援使用群集的公共 IP 位址附加 HDInsight 群集。 而是使用群集的 Azure 資源管理器 ID 與參數`resource_id`一起：
    >
    > * 美國東部
    > * 美國西部 2
    > * 美國中南部
    >
    > 可以使用以下字串格式構造群集的資源識別碼。這些格式為 ：。 `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azurehdinsight.net', 
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    # If you are in US East, US West 2, or US South Central, use the following instead:
    # attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
    #                                                      ssh_port=22, 
    #                                                      username='<ssh-username>', 
    #                                                      password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   或者，您可以使用[Azure 機器學習工作室](#portal-reuse)將 HDInsight 群集附加到工作區。

1. **配置**：為 HDI 計算目標創建回合組態。 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


現在，您已經附加了計算並配置了運行，下一步是[提交訓練運行](#submit)。


### <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch 用於在雲中高效地運行大規模並行和高性能計算 （HPC） 應用程式。 AzureBatchStep 可用於 Azure 機器學習管道，將作業提交到電腦的 Azure 批次處理池。

要將 Azure 批次處理附加到計算目標，必須使用 Azure 機器學習 SDK 並提供以下資訊：

-    **Azure 批次處理計算名稱**：用於工作區內計算的易記名稱
-    **Azure 批次處理帳戶名稱**：Azure 批次處理帳戶的名稱
-    **資源組**：包含 Azure 批次處理帳戶的資源組。

以下代碼演示如何將 Azure 批次處理附加到計算目標：

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

## <a name="set-up-in-azure-machine-learning-studio"></a>在 Azure 機器學習工作室中設置

您可以在 Azure 機器學習工作室中訪問與工作區關聯的計算目標。  您可以使用工作室：

* [檢視連結至工作區的計算目標](#portal-view)
* 在工作區中[建立計算目標](#portal-create)
* [連結已在工作區外建立的計算目標](#portal-reuse)


建立目標並連結至工作區之後，您會搭配 `ComputeTarget` 物件，將目標用於回合組態中： 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a name="view-compute-targets"></a><a id="portal-view"></a>檢視計算目標


若要查看您工作區的計算目標，請使用下列步驟：

1. 導航到[Azure 機器學習工作室](https://ml.azure.com)。
 
1. 在 [應用程式]____ 底下，選取 [計算]____。

    [![[檢視計算] 索引標籤](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a name="create-a-compute-target"></a><a id="portal-create"></a>建立計算目標

依照上述步驟來檢視計算目標的清單。 接著，使用下列步驟建立計算目標： 

1. 選取加號 (+) 以新增計算目標。

    ![新增計算目標](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. 輸入計算目標的名稱。 

1. 選取 [Machine Learning Compute]**** 作為要用於 [定型]____ 的計算類型。 

    >[!NOTE]
    >Azure 機器學習計算是可在 Azure 機器學習工作室中創建的唯一託管計算資源。  建立之後，即可連結所有其他計算資源。

1. 填寫表單。 針對必要的屬性 (特別是 **VM 系列**) 和**節點上限**提供值，以便向上微調計算。  

1. 選取 [建立]____。


1. 從清單選取計算目標以檢視建立作業的狀態：

    ![選取計算目標來檢視建立作業狀態](./media/how-to-set-up-training-targets/View_list.png)

1. 接著，您會看到該計算目標的詳細資料： 

    ![檢視電腦目標的詳細資料](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a name="attach-compute-targets"></a><a id="portal-reuse"></a>連結計算目標

要使用在 Azure 機器學習工作區之外創建的計算目標，必須附加它們。 連結計算目標可讓您的工作區使用它們。

依照稍早所述的步驟來檢視計算目標的清單。 然後使用下列步驟來連結計算目標： 

1. 選取加號 (+) 以新增計算目標。 
1. 輸入計算目標的名稱。 
1. 選擇要附加到__培訓__的計算類型：

    > [!IMPORTANT]
    > 並非所有計算類型都可以從 Azure 機器學習工作室連接。 目前可以附加以進行定型的目標類型包括：
    >
    > * 遠端 VM
    > * Azure Databricks (適用於機器學習管線)
    > * Azure Data Lake Analytics (適用於機器學習管線)
    > * Azure HDInsight

1. 填寫表單，並提供必要屬性的值。

    > [!NOTE]
    > Microsoft 建議使用 SSH 金鑰，因為它們比密碼更安全。 密碼很容易遭受暴力密碼破解攻擊。 SSH 金鑰需要密碼編譯簽章。 如需有關如何建立 SSH 金鑰以搭配 Azure 虛擬機器使用的資訊，請參閱下列文件：
    >
    > * [在 Linux 或 macOS 上建立及使用 SSH 金鑰](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [在 Windows 上建立及使用 SSH 金鑰](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. 選取 [附加]____。 
1. 從清單選取計算目標以檢視附加作業的狀態。

## <a name="set-up-with-cli"></a>使用 CLI 設置

您可以使用 Azure 機器學習的[CLI 副檔名](reference-azure-machine-learning-cli.md)訪問與工作區關聯的計算目標。  您可以使用 CLI 執行下列操作：

* 建立受控計算目標
* 更新受控計算目標
* 連結非受控計算目標

如需詳細資訊，請參閱[資源管理](reference-azure-machine-learning-cli.md#resource-management)。

## <a name="set-up-with-vs-code"></a>使用 VS 代碼進行設置

您可以使用 Azure 機器學習的[VS 代碼擴展來](tutorial-train-deploy-image-classification-model-vscode.md#configure-compute-targets)訪問、創建和管理與工作區關聯的計算目標。

## <a name="submit-training-run-using-azure-machine-learning-sdk"></a><a id="submit"></a>使用 Azure 機器學習 SDK 提交培訓運行

建立回合組態之後，會使用該回合組態以執行您的實驗。  提交定型回合的程式碼模式對於所有類型的計算目標皆相同：

1. 建立要執行的實驗
1. 提交回合。
1. 等待回合完成。

> [!IMPORTANT]
> 提交訓練運行時，將創建包含訓練腳本的目錄快照並將其發送到計算目標。 它還作為實驗的一部分存儲在工作區中。 如果更改檔並再次提交運行，則僅上載已更改的檔。
>
> 要防止檔包含在快照中，請在目錄中創建[.gitignore](https://git-scm.com/docs/gitignore) `.amlignore`或檔，並將檔添加到其中。 該檔`.amlignore`使用與[.gitignore](https://git-scm.com/docs/gitignore)檔相同的語法和模式。 如果兩個檔都存在，`.amlignore`則檔優先。
> 
> 如需詳細資訊，請參閱[快照集](concept-azure-machine-learning-architecture.md#snapshots)。

### <a name="create-an-experiment"></a>建立實驗

首先，在您的工作區中建立實驗。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>提交實驗

使用 `ScriptRunConfig` 物件來提交實驗。  此物件包含：

* **source_directory**： 包含訓練腳本的原始目錄
* **腳本**： 確定培訓腳本
* **run_config**： 回合組態，進而定義訓練將發生的位置。

例如，若要使用[本機目標](#local)組態：

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

藉由使用不同的回合組態 (例如 [amlcompute 目標](#amlcompute))，切換相同實驗以在不同的計算目標中執行：

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> 此示例預設僅使用計算目標的一個節點進行訓練。 要使用多個節點，可以將`node_count`回合組態設置為所需的節點數。 例如，以下代碼將用於定型的節點數設置為四個：
>
> ```python
> src.run_config.node_count = 4
> ```

或者您可以：

* 使用 `Estimator` 物件提交實驗，如[使用估算程式將 ML 模型定型](how-to-train-ml-models.md)中所示。
* 提交 HyperDrive 運行以[進行超參數調優](how-to-tune-hyperparameters.md)。
* 通過[VS 代碼擴展](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)提交實驗。

有關詳細資訊，請參閱[腳本配置](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py)和[回合組態](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)文檔。

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>使用 Azure 機器學習 CLI 創建回合組態並提交運行

可以使用[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)和[機器學習 CLI 擴展](reference-azure-machine-learning-cli.md)創建回合組態，並在不同的計算目標上提交運行。 以下示例假定您具有現有的 Azure 機器學習工作區，並且已使用`az login`CLI 命令登錄到 Azure。 

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

### <a name="create-run-configuration"></a>創建回合組態

創建回合組態的最簡單方法是導航包含機器學習 Python 腳本的資料夾，並使用 CLI 命令

```azurecli
az ml folder attach
```

此命令創建一個子`.azureml`資料夾，其中包含針對不同計算目標的範本回合組態檔。 您可以複製和編輯這些檔以自訂配置，例如添加 Python 包或更改 Docker 設置。  

### <a name="structure-of-run-configuration-file"></a>回合組態檔的結構

回合組態檔採用 YAML 格式，包含以下部分
 * 要運行的腳本及其參數
 * 計算工作區下的計算目標名稱，即"本地"或計算的名稱。
 * 用於執行運行的參數：框架、分散式運行的通信器、最長持續時間和計算節點數。
 * 環境部分。 有關本節中欄位的詳細資訊，請參閱[創建和管理有關培訓和部署的環境](how-to-use-environments.md)。
   * 要指定要為運行安裝的 Python 包，請創建[conda 環境檔](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)，並設置__conda 依賴檔__欄位。
 * 執行歷程記錄詳細資訊以指定日誌檔資料夾，並啟用或禁用輸出集合並執行歷程記錄快照。
 * 特定于所選框架的配置詳細資訊。
 * 資料引用和資料存儲詳細資訊。
 * 特定于機器學習計算的配置詳細資訊，用於創建新群集。

有關完整回合組態架構，請參閱[示例 JSON 檔](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)。

### <a name="create-an-experiment"></a>建立實驗

首先，為跑步創建實驗

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>腳本運行

要提交腳本運行，請執行命令

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>超級磁碟機運行

您可以將 HyperDrive 與 Azure CLI 配合使用來執行參數調優運行。 首先，以以下格式創建 HyperDrive 設定檔。 有關超參數調優參數的詳細資訊，請參閱[模型文章的"調高超參數](how-to-tune-hyperparameters.md)"。

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

將此檔與回合組態檔一起添加。 然後，使用以下功能提交 HyperDrive 運行：
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

請注意"超磁碟機配置"中的 runconfig 和*參數空間*中的*參數*部分。它們包含要傳遞給訓練腳本的命令列參數。 每次反覆運算運行時配置中的值保持不變，而 HyperDrive 配置中的範圍則反覆運算。 不要在兩個檔中指定相同的參數。

有關這些```az ml```CLI 命令和完整參數集的更多詳細資訊，請參閱[參考文檔](reference-azure-machine-learning-cli.md)。

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git 跟蹤和集成

當您啟動訓練運行時，原始目錄是本地 Git 存儲庫時，有關存儲庫的資訊存儲在執行歷程記錄中。 有關詳細資訊，請參閱[Azure 機器學習的 Git 集成](concept-train-model-git-integration.md)。

## <a name="notebook-examples"></a>筆記本範例

如需具有各種計算目標的定型範例，請參閱下列筆記：
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

* [教程：訓練模型](tutorial-train-models-with-aml.md)使用託管計算目標來訓練模型。
* 瞭解如何[有效地調整超參數](how-to-tune-hyperparameters.md)以構建更好的模型。
* 擁有定型的模型之後，請了解[部署模型的方式和位置](how-to-deploy-and-where.md)。
* 檢視 [RunConfiguration 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) SDK 參考。
* [將 Azure 機器學習與 Azure 虛擬網路一起使用](how-to-enable-virtual-network.md)
