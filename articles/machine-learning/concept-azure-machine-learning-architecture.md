---
title: 體系結構&關鍵概念
titleSuffix: Azure Machine Learning
description: 瞭解構成 Azure 機器學習的體系結構、術語、概念和工作流。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/17/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: f779781eee81bf85f6420e5bae6b0feb62680b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064179"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Azure 機器學習的工作原理：體系結構和概念

瞭解 Azure 機器學習的體系結構、概念和工作流。 服務的主要元件以及使用服務時的一般工作流程顯示在下圖中：

![Azure Machine Learning 架構與工作流程](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>工作流程

機器學習模型工作流通常遵循以下順序：

1. **火車**
    + 在**Python、R**或視覺**R**設計器中開發機器學習培訓腳本。
    + 建立並設定**計算目標**。
    + **將腳本提交**到配置的計算目標，以便在該環境中運行。 在培訓期間，腳本可以從**資料存儲**讀取或寫入。 訓練期間生成的日誌和輸出將保存為**工作區**中的**運行**，並在**實驗**下分組。

1. **包**- 找到令人滿意的運行後，在**模型註冊表**中註冊持久化模型。

1. **驗證** - **查詢當前**和過去運行中記錄指標的實驗。 如果計量未指出所要的結果，請回到步驟 1 並逐一查看您的指令碼。

1. **部署**- 開發一個評分腳本，該腳本使用模型，並將**模型部署**為 Azure 中的**Web 服務**，或部署到**IoT 邊緣設備**。

1. **監視器**- 監視訓練資料集和已部署模型的推理資料之間的**資料漂移**。 必要時，迴圈回步驟 1 以使用新的訓練資料重新訓練模型。

## <a name="tools-for-azure-machine-learning"></a>Azure 機器學習工具

使用這些工具進行 Azure 機器學習：

+  使用適用于 Python 的[Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)在任何 Python 環境中與服務交互。
+ 使用用於 R 的[Azure 機器學習 SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html)在任何 R 環境中與服務交互。
+ 使用[Azure 機器學習 CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli)自動執行機器學習活動。
+ 使用[Azure 機器學習設計器（預覽）](concept-designer.md)在不編寫代碼的情況下執行工作流步驟。


> [!NOTE]
> 儘管本文定義了 Azure 機器學習使用的術語和概念，但它不定義 Azure 平臺的術語和概念。 如需有關 Azure 平台技術的詳細資訊，請參閱 [Microsoft Azure 詞彙](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)。

## <a name="glossary"></a>字彙表

* [活動](#activities)
* [工作區](#workspaces)
    * [實驗](#experiments)
        * [運行](#runs) 
            * [回合組態](#run-configurations)
            * [快照](#snapshots)
            * [Git 跟蹤](#github-tracking-and-integration)
            * [記錄](#logging)
    * [ML 管道](#ml-pipelines)
    * [模型](#models)
        * [環境](#environments)
        * [定型指令碼](#training-scripts)
        * [估計](#estimators)
    * [端點](#endpoints)
        * [Web 服務](#web-service-endpoint)
        * [物聯網模組](#iot-module-endpoints)
    * [資料集&資料存儲](#datasets-and-datastores)
    * [計算目標](#compute-targets)

### <a name="activities"></a>活動

活動代表長時間執行的作業。 下列作業為活動範例：

* 建立或刪除計算目標
* 在計算目標上執行指令碼

活動可透過 SDK 或 Web UI 提供通知，方便您輕鬆監視這些作業的進度。

### <a name="workspaces"></a>工作區

[工作區](concept-workspace.md)是 Azure 機器學習的頂級資源。 它提供了一個集中的位置，用於處理在使用 Azure 機器學習時創建的所有專案。 您可以與他人共用工作區。 有關工作區的詳細說明，請參閱[什麼是 Azure 機器學習工作區？](concept-workspace.md)

### <a name="experiments"></a>實驗

實驗是從指定之指令碼的許多回合組成的群組。 它一律屬於某個工作區。 當您提交回合時，必須提供一個實驗名稱。 回合的資訊會儲存在該實驗底下。 如果提交回合並指定不存在的實驗名稱，就會自動以新指定的名稱建立一個新的實驗。

有關使用實驗的示例，請參閱[教程：訓練第一個模型](tutorial-1st-experiment-sdk-train.md)。

### <a name="runs"></a>執行

運行是訓練腳本的單一執行。 實驗通常包含多個運行。

Azure 機器學習記錄所有運行並在實驗中存儲以下資訊：

* 與回合有關的中繼資料 (時間戳記、持續時間等等)
* 由您的指令碼記錄的計量
* 由實驗自動收集，或由您明確上傳的輸出檔案
* 包含您指令碼之目錄的快照 (執行之前)

當您提交指令碼以將模型定型時，就會產生回合。 回合可以有 0 或多個子回合。 例如，最上層回合可能有兩個子回合，其中每個可能都有自己的子回合。

### <a name="run-configurations"></a>回合組態

回合組態是定義指令碼在指定的計算目標中應如何執行的一組指示。 此組態包含一組豐富的行為定義，例如是否使用現有的 Python 環境，或使用依據規格建立的 Conda 環境。

回合組態可保存在檔案 (位於包含您定型指令碼的目錄內) 中，或者可以建構為記憶體內部物件並用來提交回合。

如需回合組態的範例，請參閱[選取及使用計算目標將模型定型](how-to-set-up-training-targets.md)。

### <a name="snapshots"></a>快照集

提交回合時，Azure Machine Learning 會將包含指令碼的目錄壓縮成 zip 檔案，然後傳送至計算目標。 接著會將 zip 檔案解壓縮並在該處執行指令碼。 Azure Machine Learning 也會將 zip 檔案以快照方式儲存在回合記錄中。 任何擁有工作區存取權的人都能瀏覽回合記錄並下載快照集。

> [!NOTE]
> 要防止不必要的檔包含在快照中，請創建一個忽略檔（.gitignore 或 .amlignore）。 將此檔放在快照目錄中，並添加要忽略的檔案名。 .amlignore 檔使用與[.gitignore 檔相同的語法和模式](https://git-scm.com/docs/gitignore)。 如果兩個檔都存在，則 .amlignore 檔優先。

### <a name="github-tracking-and-integration"></a>GitHub 跟蹤和集成

當您啟動訓練運行時，原始目錄是本地 Git 存儲庫時，有關存儲庫的資訊存儲在執行歷程記錄中。 這適用于使用估計器、ML 管道或腳本運行提交的運行。 它還適用于從 SDK 或機器學習 CLI 提交的運行。

有關詳細資訊，請參閱[Azure 機器學習的 Git 集成](concept-train-model-git-integration.md)。

### <a name="logging"></a>記錄

開發解決方案時，請在 Python 指令碼中使用 Azure Machine Learning Python SDK 以記錄任意計量。 在該回合之後，請查詢計量以判斷該回合是否產生您要部署的模型。

### <a name="ml-pipelines"></a>ML 管線

您可以使用機器學習管線來建立和管理結合多個機器學習階段的工作流程。 例如，管道可能包括資料準備、模型培訓、模型部署和推理/評分階段。 每個階段都可以包含多個步驟，這些步驟各自都可以在各種計算目標中自動執行。 

管道步驟是可重用的，如果這些步驟的輸出未更改，則無需重新運行上述步驟即可運行。 例如，如果資料未更改，則可以重新訓練模型，而無需重新運行代價高昂的資料準備步驟。 管道還允許資料科學家在研究機器學習工作流的單獨區域時進行協作。

如需有關機器學習管線與此服務的詳細資訊，請參閱[管線和 Azure Machine Learning](concept-ml-pipelines.md)。

### <a name="models"></a>模型

簡單來說，模型是可接受輸入並產生輸出的一段程式碼。 機器學習服務模型的建立作業涵蓋選取演算法、提供資料給演算法，以及調整超參數。 定型是一種反覆進行的程序，可產生定型的模型，其中會封裝模型在定型程序期間學到項目。

模型是由 Azure Machine Learning 中的回合所產生的。 您也可以使用在 Azure Machine Learning 外部定型的模型。 可以在 Azure 機器學習工作區中註冊模型。

Azure Machine Learning 與架構無關。 創建模型時，可以使用任何流行的機器學習框架，如 Scikit 學習、XGBoost、PyTorch、TensorFlow 和鏈子。

有關使用 Scikit 學習和估算器訓練模型的示例，請參閱[教程：使用 Azure 機器學習訓練圖像分類模型](tutorial-train-models-with-aml.md)。

**模型註冊表**跟蹤 Azure 機器學習工作區中的所有模型。

模型是透過名稱與版本來識別的。 每次註冊與現有模型名稱相同的模型時，登錄都會假設它是一個新版本。 版本會累加，新模型則會以相同的名稱來註冊。

註冊模型時，您可以提供額外的中繼資料標記，然後在搜尋模型時使用這些標記。

> [!TIP]
> 已註冊的模型是構成模型的一個或多個檔的邏輯容器。 例如，如果模型存儲在多個檔中，則可以將它們註冊為 Azure 機器學習工作區中的單個模型。 註冊後，您可以下載或部署已註冊的模型，並接收已註冊的所有檔。

不能刪除活動部署正在使用的已註冊模型。

如需模型註冊的範例，請參閱[使用 Azure Machine Learning 將映像分類模型定型](tutorial-train-models-with-aml.md)。

### <a name="environments"></a>環境

Azure ML 環境用於指定用於創建資料準備、模型培訓和模型服務的可重現環境的配置（Docker / Python / Spark / 等）。 它們是 Azure 機器學習工作區中的管理和版本化實體，可跨不同的計算目標啟用可重現、可審核和可移植的機器學習工作流。

您可以使用本地計算上的環境物件來開發訓練腳本，在 Azure 機器學習計算上重用相同的環境進行大規模模型培訓，甚至使用相同的環境部署模型。 

[瞭解如何創建和管理可重用的 ML 環境](how-to-use-environments.md)以進行培訓和推理。

### <a name="training-scripts"></a>將指令碼定型

您可以指定包含定型指令碼與相關聯檔案的目錄，以將模型定型。 您也可以指定用來儲存定型期間所收集資訊的實驗名稱。 在定型期間，整個目錄會複製到定型環境 (計算目標)，然後啟動回合組態指定的指令碼。 目錄的快照集也會儲存在工作區中的實驗底下。

例如，請參閱[教程：使用 Azure 機器學習訓練圖像分類模型](tutorial-train-models-with-aml.md)。

### <a name="estimators"></a>估計

為了便於使用常用框架進行模型訓練，估計器類允許您輕鬆構造回合組態。 您可以創建和使用通用[估計器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)提交使用您選擇的任何學習框架（如 scikit 學習）的培訓腳本。

對於 PyTorch、TensorFlow 和鏈子任務，Azure 機器學習還提供各自的[PyTorch、TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)和[鏈式估計器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)，以簡化使用這些框架。 [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)

如需詳細資訊，請參閱下列文章：

* [使用估算器訓練 ML 模型](how-to-train-ml-models.md)。
* [使用 Azure 機器學習大規模訓練 Pytorch 深度學習模型](how-to-train-pytorch.md)。
* [使用 Azure 機器學習大規模訓練和註冊 TensorFlow 模型](how-to-train-tensorflow.md)。
* [使用 Azure 機器學習大規模訓練和註冊鏈器模型](how-to-train-chainer.md)。

### <a name="endpoints"></a>端點

終結點是將模型具現化到可在雲中託管的 Web 服務或用於集成設備部署的 IoT 模組中。

#### <a name="web-service-endpoint"></a>Web 服務終結點

將模型部署為 Web 服務時，可以在 Azure 容器實例、Azure 庫伯奈斯服務或 FPGA 上部署終結點。 從模型、腳本和相關檔創建服務。 這些被放置在包含模型執行環境的基本容器映射中。 映像包含經過負載平衡的 HTTP 端點，可接收傳送至 Web 服務的評分要求。

Azure 通過收集應用程式見解遙測或模型遙測（如果已選擇啟用此功能）來説明您監視 Web 服務。 遙測資料只有您才能存取，而且會儲存在您的 Application Insights 與儲存體帳戶執行個體中。

如果已經啟用自動調整功能，Azure 將會自動調整您的部署。

有關將模型部署為 Web 服務的示例，請參閱在[Azure 容器實例中部署映射分類模型](tutorial-deploy-models-with-aml.md)。

#### <a name="iot-module-endpoints"></a>IoT 模組終結點

已部署的 IoT 模組終結點是 Docker 容器，其中包含模型和關聯的腳本或應用程式以及任何其他依賴項。 通過在邊緣設備上使用 Azure IoT 邊緣來部署這些模組。

如果您已經啟用監視功能，Azure 就會從 Azure IoT Edge 模組內部的模型收集遙測資料。 遙測資料只有您才能存取，而且會儲存在您的儲存體帳戶執行個體中。

Azure IoT Edge 會確保模組正在執行，並監視裝載模組的裝置。


### <a name="compute-instance-preview"></a><a name="compute-instance"></a>計算實例（預覽）

**Azure 機器學習計算實例**（以前是筆記本 VM）是一個完全託管的基於雲的工作站，其中包含為機器學習安裝的多個工具和環境。 計算實例可用作訓練和推斷作業的計算目標。 對於大型任務，具有多節點縮放功能的[Azure 機器學習計算群集](how-to-set-up-training-targets.md#amlcompute)是更好的計算目標選擇。

瞭解有關[計算實例的更多。](concept-compute-instance.md)

### <a name="datasets-and-datastores"></a>資料集和資料存儲

**Azure 機器學習資料集**（預覽版）使訪問和使用資料變得更加容易。 資料集管理各種方案（如模型培訓和管道創建）中的資料。 使用 Azure 機器學習 SDK，可以訪問基礎存儲、流覽資料並管理不同資料集定義的生命週期。

資料集提供使用常用格式的資料的方法，例如使用`from_delimited_files()`或`to_pandas_dataframe()`。

有關詳細資訊，請參閱[創建和註冊 Azure 機器學習資料集](how-to-create-register-datasets.md)。  有關使用資料集的更多示例，請參閱[示例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets)。

**資料存儲**是 Azure 存儲帳戶的存儲抽象。 資料存放區可以使用 Azure Blob 容器或 Azure 檔案共用作為後端儲存體。 每個工作區都有預設資料存放區，而且您可以註冊額外的資料存放區。 使用 Python SDK API 或 Azure Machine Learning CLI 在資料存放區中儲存及擷取檔案。

### <a name="compute-targets"></a>計算目標

[計算目標](concept-compute-target.md)允許您指定運行訓練腳本或託管服務部署的計算資源。 此位置可能是本地電腦或基於雲的計算資源。

詳細瞭解[用於培訓和部署的可用計算目標](concept-compute-target.md)。

### <a name="next-steps"></a>後續步驟

要開始使用 Azure 機器學習，請參閱：

* [什麼是 Azure Machine Learning 服務？](overview-what-is-azure-ml.md)
* [創建 Azure 機器學習工作區](how-to-manage-workspace.md)
* [教程（第 1 部分）：培訓模型](tutorial-train-models-with-aml.md)
