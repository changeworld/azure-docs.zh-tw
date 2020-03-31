---
title: 什麼是 Azure 機器學習計算實例？
titleSuffix: Azure Machine Learning
description: 瞭解 Azure 機器學習計算實例，即完全管理的基於雲的工作站。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 280851b2fea0b8100a7d0f8ec8105109a41c8c83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283923"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>什麼是 Azure 機器學習計算實例？

Azure 機器學習計算實例（預覽）是資料科學家完全管理的基於雲的工作站。 

通過計算實例，可以輕鬆開始使用 Azure 機器學習開發，並為 IT 管理員提供管理和企業就緒功能。  

在雲中使用計算實例作為完全配置和管理的開發環境。

計算實例通常用作開發環境。  它們還可用作開發和測試培訓和推斷的計算目標。  對於大型任務，具有多節點縮放功能的[Azure 機器學習計算群集](how-to-set-up-training-targets.md#amlcompute)是更好的計算目標選擇。


## <a name="why-use-a-compute-instance"></a>為什麼要使用計算實例？

計算實例是針對機器學習開發環境優化的完全管理的基於雲的工作站。 它提供了下列優點：

|主要權益||
|----|----|
|生產力|資料科學家可以使用集成的筆記本電腦和以下工具在其 Web 瀏覽器中構建和部署模型：<br/>- 朱皮特<br/>- 朱皮特拉布<br/>- RStudio|
|託管&安全|減少安全佔用空間，增加企業安全要求的合規性。 計算實例提供可靠的管理原則和安全的網路設定，例如：<br/><br/>- 從資源管理器範本或 Azure 機器學習 SDK 自動預配<br/>- [基於角色的存取控制 （RBAC）](/azure/role-based-access-control/overview)<br/>- [虛擬網路支援](how-to-enable-virtual-network.md#compute-instance)<br/>- SSH 策略，以啟用/禁用 SSH 訪問|
|預配置&nbsp;或&nbsp;ML|使用預先配置和最新的 ML 包、深度學習框架和 GPU 驅動程式，節省設置任務的時間。|
|完全可定制|對 Azure VM 類型（包括 GPU）和持久化低級自訂（如安裝套裝程式和驅動程式）的廣泛支援使高級方案變得輕而易舉。 |

## <a name="tools-and-environments"></a><a name="contents"></a>工具和環境

Azure 機器學習計算實例使您能夠在工作區中完全集成的筆記本體驗中創作、訓練和部署模型。


這些工具和環境安裝在計算實例上： 

|&一般工具|詳細資料|
|----|:----:|
|驅動程式|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|英特爾 MPI 庫||
|Azure CLI ||
|Azure 機器學習示例 ||
|Azure 機器學習 EDAT 引擎 ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|普羅托布夫|| 

|**&環境中的 R**工具|詳細資料|
|----|:----:|
|RStudio 伺服器開源版||
|R 內核||
|用於 R 的 Azure 機器學習 SDK|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK 範例|

|**PYTHON**工具&環境|詳細資料|
|----|----|
|Anaconda Python||
|朱皮特和擴展||
|朱皮拉布和擴展||
|Visual Studio Code ||
[用於 Python 的 Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>從 PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|其他 PyPI 包|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|康達包|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|深度學習包|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX 套裝軟體|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure 機器學習 Python & R SDK 示例||

Python 套裝軟體都安裝在**Python 3.6 - AzureML**環境中。  

計算實例通常用作開發環境。  它們還可用作開發和測試培訓和推斷的計算目標。  對於大型任務，具有多節點縮放功能的[Azure 機器學習計算群集](how-to-set-up-training-targets.md#amlcompute)是更好的計算目標選擇。

### <a name="installing-packages"></a>安裝套件

您可以直接在聚居筆記本或 Rstudio 中安裝套裝軟體：

* RStudio 使用右下角的 **"包"** 選項卡，或左上角的 **"主控台"** 選項卡。  
* Python：添加安裝代碼並在 Jupyter 筆記本單元中執行。

或者，您可以通過以下任何方式訪問終端視窗：

* RStudio：選擇左上角的 **"終端**"選項卡。
* 朱比特實驗室：選擇啟動器選項卡中 **"其他**"標題下的 **"終端**"磁貼。
* 聚居器：在"檔"選項卡中右上角選擇 **"新>終端**"。
* SSH 到機器。  然後安裝 Python 包到**Python 3.6 - AzureML**環境中。  將 R 包安裝到**R**環境中。

## <a name="accessing-files"></a>存取檔案

筆記本和 R 腳本存儲在 Azure 檔共用中的工作區的預設存儲帳戶中。  這些檔位於"使用者檔"目錄下。 此存儲使計算實例之間共用筆記本變得容易。 當您停止或刪除計算實例時，存儲帳戶還會安全地保留筆記本。

工作區的 Azure 檔共用帳戶作為磁碟機裝載到計算實例上。 此磁碟機是朱皮特、朱皮特實驗室和 RStudio 的預設工作目錄。

檔共用中的檔可從同一工作區中的所有計算實例訪問。 對計算實例上這些檔的任何更改都將可靠地保留回檔共用。

您還可以將最新的 Azure 機器學習示例克隆到工作區檔共用中的使用者檔目錄下的資料夾。

在網路磁碟機上寫入小檔可能比寫入 VM 本身要慢。  如果要編寫許多小檔，請嘗試直接在計算實例（如`/tmp`目錄）上使用目錄。 請注意，這些檔將無法訪問從工作區中的其他計算實例。

## <a name="managing-a-compute-instance"></a>管理計算實例

在 Azure 機器學習工作室的工作區中，選擇 **"計算**"，然後選擇頂部的 **"計算實例**"。

![管理計算實例](./media/concept-compute-instance/manage-compute-instance.png)

您可以執行下列動作：

* 建立計算執行個體。 指定名稱、Azure VM 類型（包括 GPU）（請注意，創建後無法更改 VM 類型）、啟用/禁用 SSH 訪問以及選擇配置虛擬網路設置。 還可以直接從集成筆記本、Azure 門戶、資源管理器範本或 Azure 機器學習 SDK 創建實例。 適用于計算實例創建的每個區域的專用內核與 Azure 機器學習計算群集配額是統一和共用的。
* 刷新計算實例選項卡
* 啟動、停止和重新開機計算實例
* 刪除計算實例

對於工作區中的每個計算實例，您可以：

* 在計算實例上訪問朱皮特、朱皮特實驗室、RStudio
* SSH 進入計算實例。 預設情況下禁用 SSH 訪問，但可以在計算實例創建時啟用。 SSH 訪問是通過公共/私密金鑰機制。 該選項卡將為您提供 SSH 連接的詳細資訊，如 IP 位址、使用者名和埠號。
* 獲取有關特定計算實例（如 IP 位址和區域）的詳細資訊。

[RBAC](/azure/role-based-access-control/overview)允許您控制工作區中哪些使用者可以創建、刪除、啟動、停止、重新開機計算實例。 工作區參與者和擁有者角色的所有使用者都可以在整個工作區中創建、刪除、啟動、停止和重新開機計算實例。 但是，只允許特定計算實例的建立者訪問該計算實例上的 Jupyter、JupyterLab 和 RStudio。 計算實例的建立者具有專用於它們的計算實例，具有根存取權限，並且可以通過 Jupyter 終端。 計算實例將具有建立者使用者的單使用者登錄名，所有操作都將使用該使用者的身份進行 RBAC 和實驗運行的歸因。 SSH 訪問通過公開金鑰/私密金鑰機制進行控制。

您還可以創建實例
* 直接從集成筆記本體驗
* Azure 入口網站
* 從 Azure 資源管理器範本
* 使用 Azure 機器學習 SDK

每個區域的專用內核配額（適用于計算實例創建）與 Azure 機器學習培訓群集配額統一併共用。 

## <a name="compute-target"></a>計算目標

計算實例可用作類似于 Azure 機器學習[計算訓練群集的訓練計算目標](concept-compute-target.md#train)。 預配多 GPU VM 以使用 TensorFlow/PyTorch 估計器運行分散式培訓作業。 您還可以創建回合組態，並使用它在計算實例上運行實驗。 您可以將計算實例用作測試/調試方案的本地推斷部署目標。

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>筆記本 VM 發生了什麼情況？

計算實例正在替換筆記本 VM。  

存儲在工作區檔共用中的任何筆記本檔和工作區資料存儲中的資料都將從計算實例訪問。 但是，以前安裝在筆記本 VM 上的任何自訂包都需要在計算實例上重新安裝。 應用於計算群集創建的配額限制也適用于計算實例創建。 

無法創建新的筆記本 VM。 但是，您仍可以使用已創建的筆記本 VM，並具有全部功能。 計算實例可以與現有筆記本 VM 在同一工作區中創建。 


## <a name="next-steps"></a>後續步驟

 * [教程：訓練第一個 ML 模型](tutorial-1st-experiment-sdk-train.md)演示如何將計算實例與集成筆記本一起使用。
