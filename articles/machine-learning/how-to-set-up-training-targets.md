---
title: 使用計算目標來將模型定型
titleSuffix: Azure Machine Learning
description: 設定機器學習服務模型定型的定型環境 (計算目標)。 您可以輕鬆地在定型環境之間切換。 在本機開始定型作業。 如果您需要擴增，請切換至雲端式計算目標。
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/13/2020
ms.custom: seodec18
ms.openlocfilehash: 69d4b1d6c67dc63347ec4fb8043427ddf0a42ae1
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83702122"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>設定及使用計算目標來將模型定型 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

使用 Azure Machine Learning，您可以在各種資源或環境 (統稱為[__計算目標__](concept-azure-machine-learning-architecture.md#compute-targets)) 上將您的模型定型。 計算目標可以是本機電腦或雲端資源，例如 Azure Machine Learning Compute、Azure HDInsight 或遠端虛擬機器。  您也可以建立用於部署模型的計算目標，如[模型的部署位置和方法](how-to-deploy-and-where.md)中所述。

您可以使用 Azure Machine Learning SDK、Azure Machine Learning Studio、Azure CLI 或 Azure Machine Learning VS Code 擴充功能來建立和管理計算目標。 如果您有透過其他服務 (例如 HDInsight 叢集) 建立的計算目標，可以將其附加至 Azure Machine Learning 工作區來使用這些計算目標。
 
在本文中，您將了解如何使用各種計算目標來訓練模型。  所有計算目標的步驟皆遵循相同的工作流程：
1. 如果您沒有計算目標，請__建立__一個。
2. 將計算目標__連結__至您的工作區。
3. __設定__計算目標，以便其包含您的指令碼所需的 Python 環境和封裝相依性。


>[!NOTE]
> 此文章中的程式碼已進行過 Azure Machine Learning SDK 1.0.74 版的測試。

## <a name="compute-targets-for-training"></a>訓練用的計算目標

Azure Machine Learning 在不同計算目標上提供不同的支援。 一般模型開發生命週期會先開始開發/測試少量的資料。 在這個階段，我們建議使用本機環境。 例如，您的本機電腦或雲端式虛擬機器。 當您在較大的資料集上擴大定型規模或執行分散式定型時，建議您使用 Azure Machine Learning Compute 建立單一或多重節點叢集，在每次提交執行時自動調整。 您也可以附加您自己的計算資源，不過支援的各種情節可能會有所不同，詳述如下：

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> 您可以將 Azure Machine Learning Compute 建立成持續性資源，也可以在要求回合時才以動態方式建立。 回合型建立會在定型回合完成後移除計算目標，因此您無法重複使用以此方式建立的計算目標。

## <a name="whats-a-run-configuration"></a>什麼是回合組態？

定型時，通常在本機電腦上啟動，然後在不同的計算目標上執行該定型指令碼。 使用 Azure Machine Learning 時，您無須變更指令碼，即可在各種計算目標上執行指令碼。

您需要做的是在**回合組態**內為每個計算目標定義環境。  然後，當您想要在不同的計算目標上執行定型實驗時，為該計算指定回合組態。 如需如何指定環境並將其繫結至回合組態的詳細資訊，請參閱 [建立和管理用於定型和部署的環境](how-to-use-environments.md)。

深入了解本文末尾的[提交實驗](#submit)。

## <a name="whats-an-estimator"></a>什麼是估算器？

為了能夠使用熱門架構來將模型定型，Azure Machine Learning Python SDK 提供了較高層級的替代抽象概念，那就是估算器類別。  此類別可讓您輕鬆地建構回合組態。 您可以建立和使用一般的[估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)來提交定型指令碼，以使用您選擇的任何學習架構 (例如 scikit-learn)。 建議您針對定型使用估算器，讓估算器自動為您建構環境或 RunConfiguration 物件等内嵌物件。 如果您想要更充分地掌控這些物件的建立方式，並指定要為實驗回合安裝哪些套件，請遵循[這些步驟](#amlcompute)以提交會在 Azure Machine Learning Compute 上使用 RunConfiguration 物件的定型實驗。

Azure Machine Learning 針對 [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)、[TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)、[Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) 和 [Ray RLlib](how-to-use-reinforcement-learning.md) 提供了專屬的估算器。

如需詳細資訊，請參閱[使用估算器將 ML 模型定型](how-to-train-ml-models.md)。

## <a name="whats-an-ml-pipeline"></a>什麼是 ML 管線？

透過 ML 管線，您可以從簡易性、速度、可攜性和重複使用率方面來最佳化工作流程。 使用 Azure Machine Learning 建置管線時，您可以專注於您的專業知識 (機器學習)，而非基礎結構和自動化。

ML 管線是由多個**步驟**所建構而成，這些步驟是管線中的相異計算單位。 每個步驟都可以獨立執行，並使用隔離的計算資源。 這種方法可讓多名資料科學家同時在同一個管線上工作，又不會讓計算資源負擔過重，同時也可讓您輕鬆地針對每個步驟使用不同的計算類型/大小。

> [!TIP]
> 在將模型定型時，ML 管線可以使用回合組態或估算器。

雖然 ML 管線可以將模型定型，但也可以先準備資料再定型，然後於定型後再部署模型。 管線的主要使用案例之一是批次評分。 如需詳細資訊，請參閱[管線：將機器學習工作流程最佳化](concept-ml-pipelines.md)。

## <a name="set-up-in-python"></a>在 Python 中設定

使用各節來設定這些計算目標：

* [本機電腦](#local)
* [Azure Machine Learning Compute](#amlcompute)
* [遠端虛擬機器](#vm)
* [Azure HDInsight](#hdinsight)


### <a name="local-computer"></a><a id="local"></a>本機電腦

1. **建立並連結**：無須建立或連結計算目標，即可將本機電腦作為定型環境。  

1. **設定**：當您使用本機電腦作為計算目標時，會在您的[開發環境](how-to-configure-environment.md)中執行定型程式碼。  如果該環境已經有您需要的 Python 封裝，請使用使用者管理的環境。

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

現在您已連結計算並設定執行，下一步是[提交定型回合](#submit)。

### <a name="azure-machine-learning-compute"></a><a id="amlcompute"></a>Azure Machine Learning Compute

Azure Machine Learning Compute 是一種受控的計算基礎結構，可讓使用者輕鬆建立單一或多重節點計算。 計算會在工作區的區域內建立為能夠與工作區中的其他使用者共用的資源。 計算會在提交作業時自動相應增加，而且可以放在 Azure 虛擬網路中。 計算會在容器化環境中執行，並在 [Docker 容器](https://www.docker.com/why-docker)中封裝模型的相依性。

您可以使用 Azure Machine Learning Compute 在雲端中的 CPU 或 GPU 計算節點叢集散發定型程序。 如需包含 GPU 的 VM 大小有關的詳細資訊，請參閱 [GPU 最佳化虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)。 

Azure Machine Learning Compute 有預設限制，例如可配置的核心數目。 如需詳細資訊，請參閱[管理和要求 Azure 資源的配額](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas)。

您也可以選擇使用低優先順序的 VM 來執行部分或所有的工作負載。 這些 VM 沒有保證可用性，可能會在使用時被佔用。 被佔用的作業會重新開機，不會繼續。  相較於一般 VM，低優先順序 VM 有費率折扣，請參閱[計畫和管理成本](https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost)。

> [!TIP]
> 只要有足夠的配額可滿足所需的核心數目，叢集一般可以擴大為 100 個節點。 例如，叢集預設會設定為已在叢集節點之間啟用節點間通訊，以便支援 MPI 作業。 不過，您也可以將叢集擴大為數千個節點，只要[提出支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，並要求將訂用帳戶 (或工作區) 列入白名單，或要求特定叢集以停用節點間通訊即可。 

Azure Machine Learning Compute 可以跨回合重複使用。 計算可與工作區中的其他使用者共用，並在回合之間保留，且會根據所提交的回合數目以及叢集上設定的 max_nodes 自動擴大或縮小節點。 min_nodes 設定可以控制可用的節點數目下限。

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

1. **建立並連結**：若要使用 Python 建立持續性 Azure Machine Learning Compute 資源，請指定 **vm_size** 和 **max_nodes** 屬性。 Azure Machine Learning 接著會對於其他屬性使用智慧型預設值。 未使用時，計算會自動向下調整為零節點。   視需要建立專用的虛擬機器以執行您的作業。
    
    * **vm_size**：Azure Machine Learning Compute 建立的節點虛擬機器系列。
    * **max_nodes**：在 Azure Machine Learning Compute 上執行作業時，自動向上調整的最大節點數。
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   建立 Azure Machine Learning Compute 時，您也可以設定多個進階屬性。 這些屬性可讓您建立固定大小的持續性叢集，也可以在您訂用帳戶中現有的 Azure 虛擬網路內建立。  如需詳細資料，請參閱 [AmlCompute 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    )。
    
   或者，您也可以在 [Azure Machine Learning Studio](#portal-create) 中建立並連結持續性 Azure Machine Learning Compute 資源。

1. **設定**：為持續性計算目標建立回合組態。

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

現在您已連結計算並設定執行，下一步是[提交定型回合](#submit)。


### <a name="remote-virtual-machines"></a><a id="vm"></a>遠端虛擬機器

Azure Machine Learning 也支援提供您自己的計算資源，並將其附加到您的工作區。 其中一種資源類型是任意遠端 VM，只要可以從 Azure Machine Learning 存取即可。 此資源可以是 Azure VM，也可以是在您組織或內部部署的遠端伺服器。 具體來說，只要有 IP 位址和認證 (使用者名稱和密碼或 SSH 金鑰)，即可對於遠端執行使用任何可存取的 VM。

您可以使用系統建立的 Conda 環境、現有的 Python 環境或 Docker 容器。 若要在 Docker 容器上執行，您必須在虛擬機器上執行的 Docker 引擎。 您想要比本機電腦的更有彈性、以雲端為基礎的開發/測試環境時，這項功能特別有用。

使用 Azure 資料科學虛擬機器 (DSVM) 作為對於此案例選擇的 Azure 虛擬機器。 此虛擬機器是 Azure 中預先設定的資料科學和 AI 開發環境。 VM 會針對整個生命週期的機器學習開發，提供精心選擇的工具和架構。 如需有關如何使用 DSVM 搭配 Azure Machine Learning 的詳細資訊，請參閱[設定開發環境](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm)。

1. **建立**：先建立 DSVM，才能使用它定型模型。 若要建立此資源，請參閱[佈建適用於 Linux (Ubuntu) 的資料科學虛擬機器](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)。

    > [!WARNING]
    > Azure Machine Learning 只支援執行 Ubuntu 的虛擬機器。 當您建立 VM 或選擇現有的 VM 時，您必須選取使用 Ubuntu 的 VM。

1. **連結**：若要附加現有的虛擬機器作為計算目標，您必須提供虛擬機器的資源識別碼、使用者名稱與密碼。 您可以使用訂用帳戶識別碼、資源群組名稱和 VM 名稱，以下列字串格式建構 VM 的資源識別碼：`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

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

   或者，您也可以[使用 Azure Machine Learning Studio](#portal-reuse) 將 DSVM 連結至您的工作區。

1. **設定**：為 DSVM 計算目標建立回合組態。 Docker 和 Conda 用來建立和設定 DSVM 上的定型環境。

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


現在您已連結計算並設定執行，下一步是[提交定型回合](#submit)。

### <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight 是巨量資料分析的常用平台。 此平台會提供 Apache Spark，可用來將您的模型定型。

1. **建立**：先建立 HDInsight 叢集，才能使用它定型模型。 若要在 HDInsight 叢集上建立 Spark，請參閱[在 HDInsight 中建立 Spark 叢集](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)。 

    建立叢集時，必須指定 SSH 使用者名稱與密碼。 請記下這些值，因為使用 HDInsight 作為計算目標時會需要它們。
    
    建立叢集之後，使用下列主機名稱連結至叢集：\<clustername>-ssh.azurehdinsight.net，其中 \<clustername> 是您提供的叢集名稱。 

1. **連結**：若要連結 HDInsight 叢集作為計算目標，必須提供 HDInsight 叢集的資源識別碼、使用者名稱與密碼。 您可以使用訂用帳戶識別碼、資源群組名稱和 HDInsight 叢集名稱，以下列字串格式建構 HDInsight 叢集的資源識別碼：`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   或者，您也可以[使用 Azure Machine Learning Studio](#portal-reuse) 將 HDInsight 叢集連結至您的工作區。

1. **設定**：為 HDI 計算目標建立回合組態。 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


現在您已連結計算並設定執行，下一步是[提交定型回合](#submit)。


### <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch 可用來在雲端有效率地執行大規模的平行和高效能運算 (HPC) 應用程式。 AzureBatchStep 可在 Azure Machine Learning 管線中用來將作業提交至 Azure Batch 電腦集區。

若要連結 Azure Batch 來作為計算目標，您必須使用 Azure Machine Learning SDK，並提供下列資訊：

-    **Azure Batch 計算名稱**：要用於工作區內計算的自訂名稱
-    **Azure Batch 帳戶名稱**：Azure Batch 帳戶的名稱
-    **資源群組**：包含 Azure Batch 帳戶的資源群組。

下列程式碼示範如何連結 Azure Batch 來作為計算目標：

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

## <a name="set-up-in-azure-machine-learning-studio"></a>在 Azure Machine Learning Studio 中設定

您可以在 Azure Machine Learning Studio 中，存取與您工作區相關聯的計算目標。  您可以使用 Studio 來執行下列動作：

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

1. 瀏覽至 [Azure Machine Learning Studio](https://ml.azure.com)。
 
1. 在 [應用程式] 底下，選取 [計算]。

    [![[檢視計算] 索引標籤](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a name="create-a-compute-target"></a><a id="portal-create"></a>建立計算目標

依照上述步驟來檢視計算目標的清單。 接著，使用下列步驟建立計算目標： 

1. 選取加號 (+) 以新增計算目標。

    ![新增計算目標](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. 輸入計算目標的名稱。 

1. 選取 [Machine Learning Compute] 作為要用於 [定型] 的計算類型。 

    >[!NOTE]
    >Azure Machine Learning Compute 是您可以在 Azure Machine Learning Studio 中建立的唯一受控計算資源。  建立之後，即可連結所有其他計算資源。

1. 填寫表單。 針對必要的屬性 (特別是 **VM 系列**) 和**節點上限**提供值，以便向上微調計算。  

1. 選取 [建立]。


1. 從清單選取計算目標以檢視建立作業的狀態：

    ![選取計算目標來檢視建立作業狀態](./media/how-to-set-up-training-targets/View_list.png)

1. 接著，您會看到該計算目標的詳細資料： 

    ![檢視電腦目標的詳細資料](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a name="attach-compute-targets"></a><a id="portal-reuse"></a>連結計算目標

若要使用在 Azure Machine Learning 外建立的計算目標，您必須進行連結。 連結計算目標可讓您的工作區使用它們。

依照稍早所述的步驟來檢視計算目標的清單。 然後使用下列步驟來連結計算目標： 

1. 選取加號 (+) 以新增計算目標。 
1. 輸入計算目標的名稱。 
1. 選取要附加以進行__定型__的計算類型：

    > [!IMPORTANT]
    > 並非所有計算類型都可以從 Azure Machine Learning Studio 連結。 目前可以附加以進行定型的目標類型包括：
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

1. 選取 [附加]。 
1. 從清單選取計算目標以檢視附加作業的狀態。

## <a name="set-up-with-cli"></a>使用 CLI 進行設定

您可以使用適用於 Azure Machine Learning 的 [CLI 擴充功能](reference-azure-machine-learning-cli.md)來存取與您工作區相關聯的計算目標。  您可以使用 CLI 執行下列操作：

* 建立受控計算目標
* 更新受控計算目標
* 連結非受控計算目標

如需詳細資訊，請參閱[資源管理](reference-azure-machine-learning-cli.md#resource-management)。

## <a name="set-up-with-vs-code"></a>使用 VS Code 進行設定

您可以使用適用於 Azure Machine Learning 的 [VS Code 擴充功能](tutorial-train-deploy-image-classification-model-vscode.md#configure-compute-targets)來存取、建立和管理與您工作區相關聯的計算目標。

## <a name="submit-training-run-using-azure-machine-learning-sdk"></a><a id="submit"></a>使用 Azure Machine Learning SDK 提交定型回合

建立回合組態之後，會使用該回合組態以執行您的實驗。  提交定型回合的程式碼模式對於所有類型的計算目標皆相同：

1. 建立要執行的實驗
1. 提交回合。
1. 等待回合完成。

> [!IMPORTANT]
> 當您提交定型回合時，系統會對包含定型指令碼的目錄建立快照集，並傳送到計算目標。 其也會儲存為工作區中實驗的一部分。 如果您變更檔案並再次提交回合，則只會上傳已變更的檔案。
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> 如需詳細資訊，請參閱[快照集](concept-azure-machine-learning-architecture.md#snapshots)。

### <a name="create-an-experiment"></a>建立實驗

首先，在您的工作區中建立實驗。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>提交實驗

使用 `ScriptRunConfig` 物件來提交實驗。  此物件包含：

* **source_directory**：包含定型指令碼的來源目錄
* **指令碼**：識別定型指令碼
* **run_config**：回合組態，可依序定義定型的發生位置。

例如，若要使用[本機目標](#local)組態：

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

藉由使用不同的回合組態 (例如 [amlcompute 目標](#amlcompute))，切換相同實驗以在不同的計算目標中執行：

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> 此範例預設為僅使用計算目標的一個節點來進行定型。 若要使用多個節點，請將回合組態的 `node_count` 設定為所需的節點數目。 例如，下列程式碼會將用於定型的節點數目設定為四個：
>
> ```python
> src.run_config.node_count = 4
> ```

或者您可以：

* 使用 `Estimator` 物件提交實驗，如[使用估算程式將 ML 模型定型](how-to-train-ml-models.md)中所示。
* 提交用於[超參數微調](how-to-tune-hyperparameters.md)的 HyperDrive 回合。
* 透過 [VS Code 擴充功能](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)來提交實驗。

如需詳細資訊，請參閱 [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) 和 [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) 文件。

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>使用 Azure Machine Learning CLI 來建立回合組態並提交回合

您可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 和 [Machine Learning CLI 擴充功能](reference-azure-machine-learning-cli.md)來建立回合組態，並在不同的計算目標上提交回合。 下列範例假設您有現有的 Azure Machine Learning 工作區，而且您已使用 `az login` CLI 命令登入 Azure。 

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

### <a name="create-run-configuration"></a>建立回合組態

若要建立回合組態，最簡單的方式就是瀏覽機器學習 Python 指令碼所在的資料夾，並使用 CLI 命令

```azurecli
az ml folder attach
```

此命令會建立子資料夾 `.azureml`，並於其中包含不同計算目標的範本回合組態檔案。 您可以複製並編輯這些檔案以自訂回合組態，例如為了新增 Python 套件或變更 Docker 設定。  

### <a name="structure-of-run-configuration-file"></a>回合組態檔案的結構

回合組態檔案採用 YAML 格式，內有下列區段
 * 所要執行的指令碼及其引數
 * 計算目標名稱，也就是工作區下計算的「本機」或名稱。
 * 用於執行回合的參數：架構、分散式回合的 Communicator、持續時間上限，以及計算節點的數目。
 * 環境區段。 如需本節中欄位的詳細資訊，請參閱[建立和管理用於定型和部署的環境](how-to-use-environments.md)。
   * 若要指定要針對回合來安裝的 Python 套件，請建立 [conda 環境檔案](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)，然後設定 __condaDependenciesFile__ 欄位。
 * 回合歷程記錄詳細資料，用以指定記錄檔資料夾，以及啟用或停用輸出集合和回合歷程記錄快照集。
 * 所選架構特有的組態詳細資料。
 * 資料參考和資料存放區詳細資料。
 * 用來建立新叢集的 Machine Learning Compute 特有組態詳細資料。

如需完整的 runconfig 結構描述，請參閱 [JSON 檔案](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)範例。

### <a name="create-an-experiment"></a>建立實驗

首先，為您的回合建立實驗

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>指令碼回合

若要提交指令碼回合，請執行命令

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>HyperDrive 回合

您可以搭配使用 HyperDrive 與 Azure CLI 來執行參數微調回合。 首先，以下列格式建立 HyperDrive 組態檔。 如需超參數微調參數的詳細資訊，請參閱[為您的模型微調超參數](how-to-tune-hyperparameters.md)一文。

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

連同回合組態檔一起新增此檔案。 然後，使用下列命令提交 HyperDrive 回合：
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

請注意 runconfig 中的 *arguments* 區段，以及 HyperDrive config 中的 *parameter space*。其包含要傳遞至定型指令碼的命令列引數。 runconfig 中的值會在每次反覆運算時維持不變，而 HyperDrive config 中的範圍則會遭到反覆處理。 請勿在這兩個檔案中指定相同的引數。

如需這些 ```az ml``` CLI 命令的詳細資訊，請參閱[參考文件](reference-azure-machine-learning-cli.md)。

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git 追蹤與整合

當您啟動來源目錄是本機 Git 存放庫的定型回合時，該存放庫的相關資訊會儲存在回合歷程記錄中。 如需詳細資訊，請參閱 [Azure Machine Learning 的 Git 整合](concept-train-model-git-integration.md)。

## <a name="notebook-examples"></a>筆記本範例

如需具有各種計算目標的定型範例，請參閱下列筆記：
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

* [教學課程：定型模型](tutorial-train-models-with-aml.md)會使用受控計算目標來定型模型。
* 了解如何[有效率地微調超參數](how-to-tune-hyperparameters.md)以便建置更好的模型。
* 擁有定型的模型之後，請了解[部署模型的方式和位置](how-to-deploy-and-where.md)。
* 檢視 [RunConfiguration 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) SDK 參考。
* [搭配使用 Azure Machine Learning 與 Azure 虛擬網路](how-to-enable-virtual-network.md)
