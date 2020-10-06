---
title: 什麼是 Azure Machine Learning 計算執行個體？
titleSuffix: Azure Machine Learning
description: 了解 Azure Machine Learning 計算執行個體，這是一種完全受控的雲端式工作站。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 10/02/2020
ms.openlocfilehash: 88cb54a7a9e20e643d9a19f57dc83d3f1ea8004d
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761204"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>什麼是 Azure Machine Learning 計算執行個體？

Azure Machine Learning 計算實例是適用于資料科學家的受控雲端架構工作站。

計算執行個體可讓您輕鬆地開始使用 Azure Machine Learning 開發，以及為 IT 系統管理員提供管理和企業就緒功能。  

在雲端中使用計算實例作為機器學習的完整設定和受控開發環境。 它們也可用來做為用於開發和測試之定型和推斷的計算目標。  

針對生產等級模型定型，請使用具有多節點調整功能的 [Azure Machine Learning 計算](how-to-create-attach-compute-cluster.md) 叢集。 針對生產等級模型部署，請使用 [Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)叢集。

## <a name="why-use-a-compute-instance"></a>為何要使用計算執行個體？

計算實例是完全受控的雲端式工作站，最適合您的機器學習開發環境。 其提供下列優點：

|主要權益|描述|
|----|----|
|生產力|您可以使用整合式筆記本以及下列 Azure Machine Learning studio 中的工具來建立和部署模型：<br/>-  Jupyter<br/>-  JupyterLab<br/>-RStudio (preview) <br/>計算實例已與 Azure Machine Learning 工作區和 studio 完全整合。 您可以與工作區中的其他資料科學家共用筆記本和資料。 您也可以使用[SSH](how-to-set-up-vs-code-remote.md)設定 VS Code 遠端開發 |
|受控且安全|降低您的安全性磁碟使用量，並提升與企業安全性需求的合規性。 計算執行個體會提供健全的管理原則和安全的網路設定，例如：<br/><br/>-從 Resource Manager 範本或 Azure Machine Learning SDK 自動布建<br/>- [Azure 角色型存取控制 (Azure RBAC) ](/azure/role-based-access-control/overview)<br/>- [虛擬網路支援](how-to-enable-virtual-network.md#compute-instance)<br/>- 用以啟用/停用 SSH 存取的 SSH 原則<br/>已啟用 TLS 1。2 |
|&nbsp;針對 &nbsp; ML 預先設定|使用已預先設定好的最新 ML 套件、深度學習架構、GPU 驅動程式，節省設定工作的時間。|
|可完全自訂|廣泛支援各種 Azure VM 類型 (包括 GPU) 和持續性的低層級自訂，例如安裝套件和驅動程式可讓您輕鬆地建置進階案例。 |

您可以自行 [建立計算實例](how-to-create-manage-compute-instance.md?tabs=python#create) ，或系統管理員可以 [為您建立計算實例](how-to-create-manage-compute-instance.md?tabs=python#create-on-behalf-of-preview)。

## <a name="tools-and-environments"></a><a name="contents"></a>工具和環境

> [!IMPORTANT]
> 本文中標示為 (preview) 的專案目前處於公開預覽狀態。
> 此預覽版本會在沒有服務等級協定的情況下提供，不建議用於實際執行工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Machine Learning 計算執行個體可讓您在工作區中以完全整合的筆記本體驗來撰寫、定型和部署模型。

您可以 [安裝套件](how-to-create-manage-compute-instance.md#install-packages) ，並 [將核心新增](how-to-create-manage-compute-instance.md#add-new-kernels) 至您的計算實例。  

這些工具和環境已安裝在計算實例上： 

|一般工具和環境|詳細資料|
|----|:----:|
|驅動程式|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI Library||
|Azure CLI ||
|Azure Machine Learning 範例 ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|**R** 工具和環境|詳細資料|
|----|:----:|
|RStudio Server 開放原始碼版本 (預覽版) ||
|R 核心||
|適用於 R 的 Azure Machine Learning SDK|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK 範例|

|**PYTHON** 工具和環境|詳細資料|
|----|----|
|Anaconda Python||
|Jupyter 和擴充功能||
|Jupyterlab 和擴充功能||
[Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)</br>從 PyPI|包含大部分的 azureml 額外套件。  若要查看完整清單，請[在計算執行個體上開啟終端機視窗](how-to-run-jupyter-notebooks.md#terminal)並執行 <br/> `conda list -n azureml_py36 azureml*` |
|其他 PyPI 套件|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda 套件|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|深度學習套件|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX 套件|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning Python和 R SDK 範例||

Python 套件全都安裝在 **Python 3.6 - AzureML** 環境中。  

## <a name="accessing-files"></a>存取檔案

筆記本和 R 指令碼會儲存在 Azure 檔案共用中您工作區的預設儲存體帳戶內。  這些檔案位於您的「使用者檔案」目錄底下。 此儲存體可讓您輕鬆地在計算執行個體之間共用筆記本。 當您停止或刪除計算執行個體時，儲存體帳戶也會讓您的筆記本安全地保留下來。

您工作區的 Azure 檔案共用帳戶會掛接為計算執行個體上的磁碟機。 此磁碟機是 Jupyter、Jupyter Labs 和 RStudio 的預設工作目錄。 這表示您在 Jupyter、JupyterLab 或 RStudio 中建立的筆記本和其他檔案會自動儲存在檔案共用上，而且也可供其他計算實例使用。

檔案共用中的檔案可從相同工作區中的所有計算執行個體來存取。 在計算執行個體上對這些檔案所做的任何變更，將會可靠地反向保存到檔案共用。

您也可以將最新的 Azure Machine Learning 範例複製到工作區檔案共用中「使用者檔案」目錄下的資料夾。

寫入小型檔案可能會比寫入至計算實例本機磁片本身的網路磁片磁碟機更慢。  如果您要撰寫許多小型檔案，請嘗試直接在計算執行個體上使用目錄，例如 `/tmp` 目錄。 請注意，這些檔案將無法從其他計算實例存取。 

您可以使用 `/tmp` 計算實例上的目錄來取得暫存資料。  不過，請勿在計算實例的 OS 磁片上寫入大量的資料。  請改用 [資料存放區](concept-azure-machine-learning-architecture.md#datasets-and-datastores) 。 如果您已安裝 JupyterLab git 擴充功能，它也可能會導致計算實例效能變慢。

## <a name="managing-a-compute-instance"></a>管理計算執行個體

在 Azure Machine Learning Studio 的工作區中選取 [計算]，然後選取頂端的 [計算執行個體]。

![管理計算執行個體](./media/concept-compute-instance/manage-compute-instance.png)

您可以執行下列動作：

* [建立計算實例](#create)。 
* 重新整理 [計算實例] 索引標籤。
* 啟動、停止和重新開機計算實例。  當實例正在執行時，您需要支付該實例的費用。 當您未使用計算實例來降低成本時，請將它停止。 停止計算實例會將其解除配置。 然後再於需要時加以啟動。
* 刪除計算實例。
* 篩選計算實例的清單，只顯示您已建立的實例。

針對您可以使用的工作區中的每個計算實例，您可以：

* 存取計算執行個體上的 Jupyter、JupyterLab、RStudio
* 透過 SSH 連線到計算執行個體。 預設會停用 SSH 存取，但可在建立計算執行個體時加以啟用。 SSH 存取是透過公開/私密金鑰機制來加以控制的。 此索引標籤會提供 SSH 連線的詳細資料，例如 IP 位址、使用者名稱和連接埠號碼。
* 取得特定計算執行個體的詳細資料，例如 IP 位址和區域。

[RBAC](/azure/role-based-access-control/overview) 可讓您控制工作區中的哪些使用者可以建立、刪除、啟動、停止、重新啟動計算執行個體。 工作區參與者和擁有者角色中的所有使用者都可以在工作區中建立、刪除、啟動、停止和重新啟動計算執行個體。 不過，只有特定計算實例的建立者，或代表其建立的使用者指派時，才允許存取該計算實例上的 Jupyter、JupyterLab 和 RStudio。 計算實例專用於具有根存取權的單一使用者，而且可以透過 Jupyter/JupyterLab/RStudio 來進行終端機。 計算實例將會有單一使用者登入，而且所有動作都將使用該使用者的身分識別來進行 RBAC 和實驗執行的屬性。 SSH 存取是透過公開/私密金鑰機制來加以控制的。

這些動作可由 RBAC 控制：
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *MachineLearningServices/workspace/計算/開始/動作*
* *MachineLearningServices/workspace/計算/停止/動作*
* *MachineLearningServices/workspace/計算/重新開機/動作*

### <a name="create-a-compute-instance"></a><a name="create"></a>建立計算執行個體

在 Azure Machine Learning studio 的工作區中，從 [**計算**] 區段或 [**筆記本**] 區段中[建立新的計算實例](how-to-create-attach-compute-studio.md#compute-instance)（當您準備好要執行其中一個筆記本時）。 

您也可以透過下列方式建立執行個體：
* 直接從 [整合式筆記本體驗](tutorial-1st-experiment-sdk-setup.md#azure)
* Azure 入口網站
* 從 Azure Resource Manager 範本。 如需範例範本，請參閱 [建立 Azure Machine Learning 計算實例範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)。
* 使用 [AZURE MACHINE LEARNING SDK](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/concept-compute-instance.md)
* 從[適用于 Azure Machine Learning 的 CLI 擴充](reference-azure-machine-learning-cli.md#computeinstance)功能

適用于計算實例建立的每個區域每個區域的專用核心是統一的，並與 Azure Machine Learning 訓練計算叢集配額共用。 停止計算實例並不會釋放配額，以確保您能夠重新開機計算實例。


### <a name="create-on-behalf-of-preview"></a>代表 (預覽版建立) 

系統管理員可以代表資料科學家建立計算實例，並使用下列專案將實例指派給它們：
* [Azure Resource Manager 範本](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/preview/2020-09-01-preview/examples/createComputeInstance.json)。  如需有關如何尋找此範本所需之 TenantID 和 ObjectID 的詳細資訊，請參閱 [尋找驗證設定的識別物件識別碼](../healthcare-apis/find-identity-object-ids.md)。  您也可以在 Azure Active Directory 入口網站中找到這些值。
* REST API

您建立計算實例所需的資料科學家需要下列 RBAC 許可權： 
* *MachineLearningServices/workspace/計算/開始/動作*
* *MachineLearningServices/workspace/計算/停止/動作*
* *MachineLearningServices/workspace/計算/重新開機/動作*
* *MachineLearningServices/workspace/計算/applicationaccess/action*

資料科學家可以啟動、停止和重新開機計算實例。 他們可以使用的計算實例：
* Jupyter
* JupyterLab
* RStudio
* 整合式筆記本

## <a name="compute-target"></a>計算目標

計算執行個體可用來作為[定型計算目標](concept-compute-target.md#train)，情況與 Azure Machine Learning 計算定型叢集類似。 

計算實例：
* 具有作業佇列。
* 在虛擬網路環境中安全地執行工作，而不需要企業開啟 SSH 埠。 作業會在容器化環境中執行，並將您的模型相依性封裝在 Docker 容器中。
* 可以平行執行多個小型工作 (預覽) 。  每個核心的兩個工作可以平行執行，而其餘的作業會排入佇列。
* 支援單一節點多 GPU 分散式訓練作業

您可以使用計算實例作為測試/偵錯工具案例的本機推斷部署目標。


## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Notebook VM 怎麼了？

計算執行個體將會取代 Notebook VM。  

儲存在工作區檔案共用中的任何筆記本檔案以及工作區資料存放區中的資料，都可從計算執行個體來存取。 不過，先前安裝在筆記本 VM 上的任何自訂套件都必須在計算實例上重新安裝。 適用于計算叢集建立的配額限制也適用于建立計算實例。

您無法建立新的 Notebook VM。 不過，仍可存取和使用已建立的 Notebook VM，且功能完整。 計算執行個體可以建立在與現有 Notebook VM 相同的工作區中。


## <a name="next-steps"></a>後續步驟

* [建立及管理計算實例](how-to-create-manage-compute-instance.md)
* [教學課程：進行第一個 ML 模型的定型](tutorial-1st-experiment-sdk-train.md)會示範如何搭配使用計算執行個體與整合式筆記本。
