---
title: 設定定型 & 推斷計算目標
titleSuffix: Azure Machine Learning
description: 將計算 () 的計算資源新增至您的工作區，以用於機器學習訓練和推斷
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 10/02/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: cc4256ae0591e9fc82dcdce7c66514710fad3f57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711184"
---
# <a name="set-up-compute-targets-for-model-training-and-deployment"></a>設定模型定型和部署的計算目標

瞭解如何將 Azure 計算資源連接到您的 Azure Machine Learning 工作區。  然後，您可以在機器學習工作中使用這些資源作為定型和推斷 [計算目標](concept-compute-target.md) 。

在本文中，您將瞭解如何設定您的工作區來使用這些計算資源：

* 您的本機電腦
* 遠端虛擬機器
* Azure HDInsight
* Azure Batch
* Azure Databricks
* Azure Data Lake Analytics
* Azure 容器執行個體

若要使用受 Azure Machine Learning 管理的計算目標，請參閱：


* [Azure Machine Learning 計算執行個體](how-to-create-manage-compute-instance.md)
* [Azure Machine Learning 計算叢集](how-to-create-attach-compute-cluster.md)
* [Azure Kubernetes Service 叢集](how-to-create-attach-kubernetes.md)

## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 如需詳細資訊，請參閱 [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

* Machine Learning 服務、 [Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)或[Azure Machine Learning Visual Studio Code 延伸](tutorial-setup-vscode-extension.md)模組的[Azure CLI 延伸](reference-azure-machine-learning-cli.md)模組。

## <a name="limitations"></a>限制

* 請勿從您的工作區**建立多個同時附加至相同計算的附件**。 例如，使用兩個不同的名稱，將一個 Azure Kubernetes Service 叢集附加至工作區。 每個新的附件都會中斷先前現有的附件 (s) 。

    如果您想要重新連接計算目標，例如變更 TLS 或其他叢集設定，您必須先移除現有的附件。

## <a name="whats-a-compute-target"></a>什麼是計算目標？

使用 Azure Machine Learning，您可以在各種資源或環境 (統稱為[__計算目標__](concept-azure-machine-learning-architecture.md#compute-targets)) 上將您的模型定型。 計算目標可以是本機電腦或雲端資源，例如 Azure Machine Learning Compute、Azure HDInsight 或遠端虛擬機器。  您也可以使用模型部署的計算目標，如「 [部署模型的位置和方式](how-to-deploy-and-where.md)」中所述。


## <a name="local-computer"></a><a id="local"></a>本機電腦

當您使用本機電腦進行 **訓練**時，不需要建立計算目標。  只需從您的本機電腦 [提交定型](how-to-set-up-training-targets.md) 回合。

當您使用本機電腦進行 **推斷**時，必須安裝 Docker。 若要執行部署，請使用 [LocalWebservice.deploy_configuration ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py&preserve-view=true#deploy-configuration-port-none-) 來定義 web 服務將使用的埠。 然後使用一般部署程式，如使用 [Azure Machine Learning 部署模型](how-to-deploy-and-where.md)中所述。

## <a name="remote-virtual-machines"></a><a id="vm"></a>遠端虛擬機器

Azure Machine Learning 也支援提供您自己的計算資源，並將其附加到您的工作區。 其中一種資源類型是任意遠端 VM，只要可以從 Azure Machine Learning 存取即可。 此資源可以是 Azure VM，也可以是在您組織或內部部署的遠端伺服器。 具體來說，只要有 IP 位址和認證 (使用者名稱和密碼或 SSH 金鑰)，即可對於遠端執行使用任何可存取的 VM。

您可以使用系統建立的 Conda 環境、現有的 Python 環境或 Docker 容器。 若要在 Docker 容器上執行，您必須在虛擬機器上執行的 Docker 引擎。 您想要比本機電腦的更有彈性、以雲端為基礎的開發/測試環境時，這項功能特別有用。

針對此案例，請使用 Azure 資料科學虛擬機器 (DSVM) 作為選擇的 Azure VM。 此虛擬機器是 Azure 中預先設定的資料科學和 AI 開發環境。 VM 會針對整個生命週期的機器學習開發，提供精心選擇的工具和架構。 如需有關如何使用 DSVM 搭配 Azure Machine Learning 的詳細資訊，請參閱[設定開發環境](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm)。

1. **建立**：先建立 DSVM，才能使用它定型模型。 若要建立此資源，請參閱[佈建適用於 Linux (Ubuntu) 的資料科學虛擬機器](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)。

    > [!WARNING]
    > Azure Machine Learning 僅支援執行 **Ubuntu**的虛擬機器。 當您建立 VM 或選擇現有的 VM 時，您必須選取使用 Ubuntu 的 VM。
    > 
    > Azure Machine Learning 也需要虛擬機器具有 __公用 IP 位址__。

1. **連結**：若要附加現有的虛擬機器作為計算目標，您必須提供虛擬機器的資源識別碼、使用者名稱與密碼。 您可以使用訂用帳戶識別碼、資源群組名稱和 VM 名稱，以下列字串格式建構 VM 的資源識別碼：`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   或者，您也可以[使用 Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#attached-compute) 將 DSVM 連結至您的工作區。

    > [!WARNING]
    > 請勿從您的工作區建立多個同時附加至相同 DSVM 的附件。 每個新的附件都會中斷先前現有的附件 (s) 。

1. **設定**：為 DSVM 計算目標建立回合組態。 Docker 和 Conda 用來建立和設定 DSVM 上的定型環境。

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight 是巨量資料分析的常用平台。 此平台會提供 Apache Spark，可用來將您的模型定型。

1. **建立**：先建立 HDInsight 叢集，才能使用它定型模型。 若要在 HDInsight 叢集上建立 Spark，請參閱[在 HDInsight 中建立 Spark 叢集](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)。 

    > [!WARNING]
    > Azure Machine Learning 要求 HDInsight 叢集必須有 __公用 IP 位址__。

    建立叢集時，必須指定 SSH 使用者名稱與密碼。 請記下這些值，因為使用 HDInsight 作為計算目標時會需要它們。
    
    叢集建立之後，請使用主機名稱 ssh.azurehdinsight.net 連接到該叢集 \<clustername> ，其中 \<clustername> 是您為叢集提供的名稱。 

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

   或者，您也可以[使用 Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#attached-compute) 將 HDInsight 叢集連結至您的工作區。

    > [!WARNING]
    > 請勿從您的工作區建立多個同時附加至相同 HDInsight 的附件。 每個新的附件都會中斷先前現有的附件 (s) 。

1. **設定**：為 HDI 計算目標建立回合組態。 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


現在您已連結計算並設定執行，下一步是[提交定型回合](how-to-set-up-training-targets.md)。

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

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

> [!WARNING]
> 請勿從您的工作區建立多個同時附加至相同 Azure Batch 的附件。 每個新的附件都會中斷先前現有的附件 (s) 。

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks 是 Azure 雲端中的 Apache Spark 型環境。 它可與 Azure Machine Learning 管線搭配使用作為計算目標。

使用 Azure Databricks 之前，請先建立其工作區。 若要建立工作區資源，請參閱在 [Azure Databricks 檔上執行 Spark 作業](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) 。

若要連結 Azure Databricks 作為計算目標，請提供下列資訊：

* __Databricks 計算名稱__：您要指派給這個計算資源的名稱。
* __Databricks 工作區名稱__： Azure Databricks 工作區的名稱。
* __Databricks 存取權杖__：用來向 Azure Databricks 驗證的存取權杖。 若要產生存取權杖，請參閱[驗證](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html)文件。

下列程式碼示範如何使用 Azure Machine Learning SDK 來附加 Azure Databricks 作為計算目標 (__Databricks 工作區必須存在於與 AML 工作區) 相同的訂用帳戶中__ ：

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

如需更詳細的範例，請參閱 GitHub 上的 [範例筆記本](https://aka.ms/pl-databricks) 。

> [!WARNING]
> 請勿從您的工作區建立多個同時附加至相同 Azure Databricks 的附件。 每個新的附件都會中斷先前現有的附件 (s) 。

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics 是 Azure 雲端中的巨量資料分析平台。 它可與 Azure Machine Learning 管線搭配使用作為計算目標。

在使用 Azure Data Lake Analytics 之前，請先建立其帳戶。 若要建立此資源，請參閱[開始使用 Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) 文件。

若要連結 Data Lake Analytics 來作為計算目標，您必須使用 Azure Machine Learning SDK，並提供下列資訊：

* __計算名稱__：您想要指派給這個計算資源的名稱。
* __資源群組__：包含 Data Lake Analytics 帳戶的資源群組。
* __帳戶名稱__： Data Lake Analytics 的帳戶名稱。

下列程式碼示範如何連結 Data Lake Analytics 來作為計算目標：

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

如需更詳細的範例，請參閱 GitHub 上的 [範例筆記本](https://aka.ms/pl-adla) 。

> [!WARNING]
> 請勿從您的工作區建立多個同時附加至相同 ADLA 的附件。 每個新的附件都會中斷先前現有的附件 (s) 。

> [!TIP]
> Azure Machine Learning 管線只能使用 Data Lake Analytics 帳戶的預設資料存放區中所儲存的資料來運作。 如果您需要使用的資料是在非預設的存放區中，您可以使用在 [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py&preserve-view=true) 定型之前複製資料。

## <a name="azure-container-instance"></a><a id="aci"></a>Azure 容器執行個體

當您部署模型時，會以動態方式建立 Azure 容器實例 (ACI) 。 您無法以任何其他方式建立或附加 ACI 至您的工作區。 如需詳細資訊，請參閱 [將模型部署至 Azure 容器實例](how-to-deploy-azure-container-instance.md)。

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Azure Kubernetes Service (AKS) 與 Azure Machine Learning 搭配使用時，可提供各種不同的設定選項。 如需詳細資訊，請參閱 [如何建立和附加 Azure Kubernetes Service](how-to-create-attach-kubernetes.md)。


## <a name="notebook-examples"></a>筆記本範例

如需具有各種計算目標的定型範例，請參閱下列筆記：
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

* 使用計算資源來 [設定和提交定型](how-to-set-up-training-targets.md)回合。
* [教學課程：定型模型](tutorial-train-models-with-aml.md)會使用受控計算目標來定型模型。
* 了解如何[有效率地微調超參數](how-to-tune-hyperparameters.md)以便建置更好的模型。
* 擁有定型的模型之後，請了解[部署模型的方式和位置](how-to-deploy-and-where.md)。
* [搭配使用 Azure Machine Learning 與 Azure 虛擬網路](how-to-enable-virtual-network.md)
