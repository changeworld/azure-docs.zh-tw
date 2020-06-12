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
ms.date: 12/13/2019
ms.openlocfilehash: 8c03df8fb0cd8f5f092450ebe4c66266d2ff4293
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816347"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>什麼是 Azure Machine Learning 計算執行個體？

Azure Machine Learning 計算執行個體 (預覽) 是提供給資料科學家使用的完全受控雲端式工作站。 

計算執行個體可讓您輕鬆地開始使用 Azure Machine Learning 開發，以及為 IT 系統管理員提供管理和企業就緒功能。  

請在雲端中使用計算執行個體來作為已完整設定和完全受控的開發環境。

計算執行個體一般會作為開發環境。  其也可作為用於開發和測試的定型和推斷計算目標。  針對大型工作，具有多節點擴縮功能的 [Azure Machine Learning 計算叢集](how-to-set-up-training-targets.md#amlcompute)會是比較好的計算目標選擇。


## <a name="why-use-a-compute-instance"></a>為何要使用計算執行個體？

計算執行個體是完全受控的雲端式工作站，並已針對機器學習開發環境予以最佳化。 其提供下列優點：

|主要權益||
|----|----|
|生產力|資料科學家可以在其網頁瀏覽器中使用整合式筆記本和下列工具來建置和部署模型：<br/>-  Jupyter<br/>-  JupyterLab<br/>-  RStudio|
|受控且安全|降低您的安全性磁碟使用量，並提升與企業安全性需求的合規性。 計算執行個體會提供健全的管理原則和安全的網路設定，例如：<br/><br/>- 從 Resource Manager 範本或 Azure Machine Learning SDK 自動佈建<br/>- [角色型存取控制 (RBAC)](/azure/role-based-access-control/overview)<br/>- [虛擬網路支援](how-to-enable-virtual-network.md#compute-instance)<br/>- 用以啟用/停用 SSH 存取的 SSH 原則|
|已預先設定&nbsp;或&nbsp;ML|使用已預先設定好的最新 ML 套件、深度學習架構、GPU 驅動程式，節省設定工作的時間。|
|可完全自訂|廣泛支援各種 Azure VM 類型 (包括 GPU) 和持續性的低層級自訂，例如安裝套件和驅動程式可讓您輕鬆地建置進階案例。 |

## <a name="tools-and-environments"></a><a name="contents"></a>工具和環境

Azure Machine Learning 計算執行個體可讓您在工作區中以完全整合的筆記本體驗來撰寫、定型和部署模型。


下列工具和環境會安裝在計算執行個體上： 

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
|RStudio Server 開放原始碼版本||
|R 核心||
|適用於 R 的 Azure Machine Learning SDK|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK 範例|

|**PYTHON** 工具和環境|詳細資料|
|----|----|
|Anaconda Python||
|Jupyter 和擴充功能||
|Jupyterlab 和擴充功能||
[Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>從 PyPI|包含大部分的 azureml 額外套件。  若要查看完整清單，請[在計算執行個體上開啟終端機視窗](how-to-run-jupyter-notebooks.md#terminal)並執行 <br/> `conda list -n azureml_py36 azureml*` |
|其他 PyPI 套件|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda 套件|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|深度學習套件|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX 套件|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning Python和 R SDK 範例||

Python 套件全都安裝在 **Python 3.6 - AzureML** 環境中。  

計算執行個體一般會作為開發環境。  其也可作為用於開發和測試的定型和推斷計算目標。  針對大型工作，具有多節點擴縮功能的 [Azure Machine Learning 計算叢集](how-to-set-up-training-targets.md#amlcompute)會是比較好的計算目標選擇。

### <a name="installing-packages"></a>安裝套件

您可以直接在 Jupyter 筆記本或 Rstudio 中安裝套件：

* RStudio：使用右下角的 [套件] 索引標籤，或左上角的 [主控台] 索引標籤。  
* Python：在 Jupyter 筆記本資料格中新增安裝程式碼並執行。

或者，您也可以使用下列任何一種方式來存取終端機視窗：

* RStudio：選取左上角的 [終端機] 索引標籤。
* Jupyter Lab：在 [啟動器] 索引標籤中的 [其他] 標題下，選取 [終端機] 圖格。
* Jupyter：在 [檔案] 索引標籤的右上角選取 [新增] > [終端機]。
* 透過 SSH 連線到電腦。  然後將 Python 套件安裝到 **Python 3.6 - AzureML** 環境中。  將 R 套件安裝到 **R** 環境中。

## <a name="accessing-files"></a>存取檔案

筆記本和 R 指令碼會儲存在 Azure 檔案共用中您工作區的預設儲存體帳戶內。  這些檔案位於您的「使用者檔案」目錄底下。 此儲存體可讓您輕鬆地在計算執行個體之間共用筆記本。 當您停止或刪除計算執行個體時，儲存體帳戶也會讓您的筆記本安全地保留下來。

您工作區的 Azure 檔案共用帳戶會掛接為計算執行個體上的磁碟機。 此磁碟機是 Jupyter、Jupyter Labs 和 RStudio 的預設工作目錄。

檔案共用中的檔案可從相同工作區中的所有計算執行個體來存取。 在計算執行個體上對這些檔案所做的任何變更，將會可靠地反向保存到檔案共用。

您也可以將最新的 Azure Machine Learning 範例複製到工作區檔案共用中「使用者檔案」目錄下的資料夾。

在網路磁碟機上寫入小型檔案的速度，會比寫入到 VM 之中還慢。  如果您要撰寫許多小型檔案，請嘗試直接在計算執行個體上使用目錄，例如 `/tmp` 目錄。 請注意，這些檔案將無法從工作區中的其他計算執行個體來存取。

## <a name="managing-a-compute-instance"></a>管理計算執行個體

在 Azure Machine Learning Studio 的工作區中選取 [計算]，然後選取頂端的 [計算執行個體]。

![管理計算執行個體](./media/concept-compute-instance/manage-compute-instance.png)

您可以執行下列動作：

* 建立計算執行個體。 指定名稱、包含 GPU 在內的 Azure VM 類型 (請注意，建立後就無法變更 VM 類型)、啟用/停用 SSH 存取，以及選擇性地設定虛擬網路設定。 您也可以直接從整合式筆記本、Azure 入口網站、Resource Manager 範本或 Azure Machine Learning SDK 建立執行個體。 適用於計算執行個體建立的專用核心每個區域配額會統一，並與 Azure Machine Learning 計算叢集的配額共用。
* 重新整理 [計算執行個體] 索引標籤
* 啟動、停止和重新啟動計算執行個體。 請停止未使用的 VM 以降低成本。 然後再於需要時加以啟動。
* 刪除計算執行個體

針對工作區中的每個計算執行個體，您可以：

* 存取計算執行個體上的 Jupyter、JupyterLab、RStudio
* 透過 SSH 連線到計算執行個體。 預設會停用 SSH 存取，但可在建立計算執行個體時加以啟用。 SSH 存取是透過公開/私密金鑰機制來加以控制的。 此索引標籤會提供 SSH 連線的詳細資料，例如 IP 位址、使用者名稱和連接埠號碼。
* 取得特定計算執行個體的詳細資料，例如 IP 位址和區域。

[RBAC](/azure/role-based-access-control/overview) 可讓您控制工作區中的哪些使用者可以建立、刪除、啟動、停止、重新啟動計算執行個體。 工作區參與者和擁有者角色中的所有使用者都可以在工作區中建立、刪除、啟動、停止和重新啟動計算執行個體。 不過，只有特定計算執行個體的建立者可以存取該計算執行個體上的 Jupyter、JupyterLab 和 RStudio。 計算執行個體的建立者具有專屬的計算執行個體、具有根存取權，而且可以透過 Jupyter 進入終端機。 計算執行個體只會有一個建立者使用者登入，而且所有動作都會使用該使用者的身分識別來獲得實驗回合的 RBAC 和屬性。 SSH 存取是透過公開/私密金鑰機制來加以控制的。

您也可以透過下列方式建立執行個體：
* 直接從整合式筆記本體驗
* Azure 入口網站
* 從 Azure Resource Manager 範本
* 使用 Azure Machine Learning SDK

適用於計算執行個體建立的專用核心每個區域配額會統一，並與 Azure Machine Learning 定型叢集的配額共用。 

## <a name="compute-target"></a>計算目標

計算執行個體可用來作為[定型計算目標](concept-compute-target.md#train)，情況與 Azure Machine Learning 計算定型叢集類似。 佈建多 GPU VM 以使用 TensorFlow/PyTorch 估算器來執行分散式定型作業。 您也可以建立回合組態，並將其用來在計算執行個體上執行實驗。 您可以使用計算執行個體作為測試/偵錯案例的本機推斷部署目標。

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Notebook VM 怎麼了？

計算執行個體將會取代 Notebook VM。  

儲存在工作區檔案共用中的任何筆記本檔案以及工作區資料存放區中的資料，都可從計算執行個體來存取。 不過，先前安裝在 Notebook VM 上的任何自訂套件，都必須重新安裝到計算執行個體上。 適用於計算叢集建立的配額限制也適用於計算執行個體建立。 

您無法建立新的 Notebook VM。 不過，仍可存取和使用已建立的 Notebook VM，且功能完整。 計算執行個體可以建立在與現有 Notebook VM 相同的工作區中。 


## <a name="next-steps"></a>後續步驟

 * [教學課程：進行第一個 ML 模型的定型](tutorial-1st-experiment-sdk-train.md)會示範如何搭配使用計算執行個體與整合式筆記本。
