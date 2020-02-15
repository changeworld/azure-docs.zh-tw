---
title: 什麼是 Azure Machine Learning 計算實例？
titleSuffix: Azure Machine Learning
description: 深入瞭解 Azure Machine Learning 計算實例，這是完全受控的雲端式工作站。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 280851b2fea0b8100a7d0f8ec8105109a41c8c83
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252071"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>什麼是 Azure Machine Learning 計算實例？

Azure Machine Learning 計算實例（預覽）是適用于資料科學家的完全受控雲端式工作站。 

計算實例可讓您輕鬆地開始使用 Azure Machine Learning 開發，以及為 IT 系統管理員提供管理和企業就緒功能。  

使用計算實例作為雲端中完全設定和受控的開發環境。

計算實例通常用來做為開發環境。  它們也可以當做用於開發和測試之定型和推斷的計算目標。  針對大型工作，具有多重節點調整功能的[Azure Machine Learning 計算](how-to-set-up-training-targets.md#amlcompute)叢集是較佳的計算目標選擇。


## <a name="why-use-a-compute-instance"></a>為何要使用計算實例？

計算實例是完全受控的雲端式工作站，已針對您的機器學習開發環境優化。 它提供下列優點：

|主要權益||
|----|----|
|生產力|資料科學家可以在其網頁瀏覽器中使用整合式筆記本和下列工具來建立和部署模型：<br/>-Jupyter<br/>-JupyterLab<br/>-RStudio|
|受控 & 安全|降低您的安全性使用量，並增加企業安全性需求的合規性。 計算實例提供健全的管理原則和安全的網路設定，例如：<br/><br/>-從 Resource Manager 範本或 Azure Machine Learning SDK 自動布建<br/>- 以[角色為基礎的存取控制（RBAC）](/azure/role-based-access-control/overview)<br/>- [虛擬網路支援](how-to-enable-virtual-network.md#compute-instance)<br/>-啟用/停用 SSH 存取的 SSH 原則|
|預先設定的&nbsp;或&nbsp;ML|使用預先設定的最新 ML 套件、深度學習架構、GPU 驅動程式，節省設定工作的時間。|
|可完全自訂|Azure VM 類型的廣泛支援包括 Gpu 和持續性的低層級自訂，例如安裝套件和驅動程式，可讓您輕鬆地進行先進的案例。 |

## <a name="contents"></a>工具和環境

Azure Machine Learning 計算實例可讓您在工作區中以完全整合的筆記本體驗來撰寫、定型和部署模型。


這些工具和環境會安裝在計算實例上： 

|一般工具 & 環境|詳細資料|
|----|:----:|
|驅動程式|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI 程式庫||
|Azure CLI ||
|Azure Machine Learning 範例 ||
|Azure Machine Learning EDAT 引擎 ||
|Docker||
|Nginx||
|NCCL 2。0 ||
|Protobuf|| 

|**R**工具 & 環境|詳細資料|
|----|:----:|
|RStudio 伺服器開放原始碼版本||
|R 核心||
|適用于 R 的 Azure Machine Learning SDK|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK 範例|

|**PYTHON**工具 & 環境|詳細資料|
|----|----|
|Anaconda Python||
|Jupyter 和擴充功能||
|Jupyterlab 和擴充功能||
|Visual Studio Code ||
[Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>從 PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|其他 PyPI 套件|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda 套件|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|深度學習套件|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX 套件|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning Python & R SDK 範例||

Python 套件全都安裝在**python 3.6-AzureML**環境中。  

計算實例通常用來做為開發環境。  它們也可以當做用於開發和測試之定型和推斷的計算目標。  針對大型工作，具有多重節點調整功能的[Azure Machine Learning 計算](how-to-set-up-training-targets.md#amlcompute)叢集是較佳的計算目標選擇。

### <a name="installing-packages"></a>安裝套件

您可以直接在 Jupyter 筆記本或 Rstudio 中安裝套件：

* RStudio 使用右下方的 [**套件**] 索引標籤，或左上角的 [**主控台**] 索引標籤。  
* Python：在 Jupyter 筆記本資料格中新增安裝程式碼並執行。

或者，您可以使用下列任何一種方式來存取終端機視窗：

* RStudio：選取左上方的 [**終端**機] 索引標籤。
* Jupyter Lab：選取 [啟動器] 索引標籤中 [**其他**] 標題底下的 [**終端**機] 磚。
* Jupyter：在 [檔案] 索引標籤中，選取右上方的 [**新增 > 終端**機]。
* 透過 SSH 連線到電腦。  然後將 Python 套件安裝到**python 3.6-AzureML**環境中。  在**r**環境中安裝 r 套件。

## <a name="accessing-files"></a>存取檔案

筆記本和 R 腳本會儲存在 Azure 檔案共用中工作區的預設儲存體帳戶中。  這些檔案位於您的「使用者檔案」目錄底下。 此儲存體可讓您輕鬆地在計算實例之間共用筆記本。 當您停止或刪除計算實例時，儲存體帳戶也會讓您的筆記本安全地保留。

您工作區的 Azure 檔案共用帳戶會裝載為計算實例上的磁片磁碟機。 此磁片磁碟機是 Jupyter、Jupyter Labs 和 RStudio 的預設工作目錄。

檔案共用中的檔案可從相同工作區中的所有計算實例存取。 在計算實例上對這些檔案所做的任何變更，將會可靠地保存回檔案共用。

您也可以將最新的 Azure Machine Learning 範例複製到工作區檔案共用中 [使用者檔案] 目錄下的資料夾。

在網路磁碟機機上寫入小型檔案可能會比寫入 VM 本身的速度慢。  如果您要撰寫許多小型檔案，請嘗試直接在計算實例上使用目錄，例如 `/tmp` 目錄。 請注意，這些檔案將無法從工作區中的其他計算實例存取。

## <a name="managing-a-compute-instance"></a>管理計算實例

在 Azure Machine Learning studio 的工作區中，選取 [**計算**]，然後選取頂端的 [**計算實例**]。

![管理計算實例](./media/concept-compute-instance/manage-compute-instance.png)

您可以執行下列動作：

* 建立計算實例。 指定名稱、包含 Gpu 的 Azure VM 類型（請注意，建立後不能變更 VM 類型）、啟用/停用 SSH 存取，以及選擇性地設定虛擬網路設定。 您也可以直接從整合式筆記本、Azure 入口網站、Resource Manager 範本或 Azure Machine Learning SDK 建立實例。 適用于計算實例建立的每個區域的專用核心配額會一致，並與 Azure Machine Learning 計算叢集配額共用。
* 重新整理 [計算實例] 索引標籤
* 啟動、停止和重新開機計算實例
* 刪除計算實例

針對工作區中的每個計算實例，您可以：

* 存取計算實例上的 Jupyter、JupyterLab、RStudio
* 透過 SSH 連線到計算實例。 預設會停用 SSH 存取，但可在計算實例建立時啟用。 SSH 存取是透過公開/私用金鑰機制。 此索引標籤會提供 SSH 連線的詳細資料，例如 IP 位址、使用者名稱和埠號碼。
* 取得特定計算實例的詳細資料，例如 IP 位址和區域。

[RBAC](/azure/role-based-access-control/overview)可讓您控制工作區中的哪些使用者可以建立、刪除、啟動、停止、重新開機計算實例。 工作區參與者和擁有者角色中的所有使用者都可以在工作區中建立、刪除、啟動、停止和重新開機計算實例。 不過，只有特定計算實例的建立者可以存取該計算實例上的 Jupyter、JupyterLab 和 RStudio。 計算實例的建立者具有專屬的計算實例、具有根存取權，而且可以透過 Jupyter 進行終端機。 計算實例會有單一使用者的建立者使用者登入，而且所有動作都會使用該使用者的身分識別來執行 RBAC 和屬性的實驗執行。 SSH 存取是透過公開/私密金鑰機制來控制。

您也可以建立實例
* 直接從整合式筆記本體驗
* Azure 入口網站
* 從 Azure Resource Manager 範本
* 使用 Azure Machine Learning SDK

適用于計算實例建立的每個區域的專用核心配額，會與 Azure Machine Learning 定型叢集配額整合並共用。 

## <a name="compute-target"></a>計算目標

計算實例可用來做為[訓練計算目標](concept-compute-target.md#train)，類似于 Azure Machine Learning 計算定型叢集。 布建多 GPU VM，以使用 TensorFlow/PyTorch 估算器執行分散式訓練作業。 您也可以建立回合設定，並使用它在計算實例上執行實驗。 您可以使用計算實例作為測試/偵錯工具案例的本機推斷部署目標。

## <a name="notebookvm"></a>筆記本 VM 發生什麼事？

計算實例會取代筆記本 VM。  

儲存在工作區檔案共用中的任何筆記本檔案和工作空間資料存放區中的資料，都可從計算實例存取。 不過，先前安裝在筆記本 VM 上的任何自訂套件，都必須重新安裝在計算實例上。 適用于計算叢集建立的配額限制也適用于計算實例建立。 

無法建立新的筆記本 Vm。 不過，您仍然可以存取和使用您所建立的筆記本 Vm，並提供完整的功能。 計算實例可以建立在與現有筆記本 Vm 相同的工作區中。 


## <a name="next-steps"></a>後續步驟

 * [教學課程：將您的第一個 ML 模型定型](tutorial-1st-experiment-sdk-train.md)示範如何使用具有整合式筆記本的計算實例。
