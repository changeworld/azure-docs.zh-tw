---
title: 架構和重要概念
titleSuffix: Azure Machine Learning
description: 了解組成 Azure Machine Learning 的架構、術語、概念和工作流程。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 05/13/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: 49c23774fe16c24ba90daa02cdda1688b79b12d3
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683052"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Azure Machine Learning 的運作方式：架構和概念

了解 Azure Machine Learning 的架構、概念和工作流程。 服務的主要元件以及使用服務時的一般工作流程顯示在下圖中：

![Azure Machine Learning 架構與工作流程](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>工作流程

機器學習模型工作流程一般會遵循下列順序：

1. **訓練**
    + 使用 **Python**、**R** 或視覺化設計工具來開發機器學習定型指令碼。
    + 建立並設定**計算目標**。
    + **提交指令碼**至設定的計算目標以在該環境中執行。 在訓練期間，可以從**資料存放區**讀取或寫入指令碼。 定型期間所產生的記錄和輸出會儲存為**工作區**中的**執行**，並群組到**實驗**底下。

1. **封裝** - 找到令人滿意的回合之後，請在**模型登錄**中註冊保存的模型。

1. **驗證** - **查詢實驗**取得目前和過去回合的記錄計量。 如果計量未指出所要的結果，請回到步驟 1 並逐一查看您的指令碼。

1. **部署** - 開發會使用模型的評分指令碼，並**將模型部署**為 Azure 中的 **Web 服務** 或將模型部署至 **IoT Edge 裝置**。

1. **監視** - 監視定型資料集與已部署模型的推斷資料之間所產生的**資料漂移**。 必要時，請回到步驟 1，以使用新的定型資料來將模型重新定型。

## <a name="tools-for-azure-machine-learning"></a>適用於 Azure Machine Learning 的工具

請使用下列適用於 Azure Machine Learning 的工具：

+  使用[適用於 Python 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，與任何 Python 環境中的服務互動。
+ 使用[適用於 R 的 Azure Machine Learning SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html)，與任何 R 環境中的服務互動。
+ 使用 [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli)，將您的機器學習活動自動化。
+ 使用 [Azure Machine Learning 設計工具 (預覽)](concept-designer.md) 來執行工作流程步驟，而不需要撰寫程式碼。
+ [Many Models Solution Accelerator](https://aka.ms/many-models) (預覽) 建置在 Azure Machine Learning 中，可讓您定型、操作及管理上百個或甚至數千個機器學習模型。

> [!NOTE]
> 雖然本文定義了 Azure Machine Learning 使用的術語與概念，但並沒有定義 Azure 平台的術語與概念。 如需有關 Azure 平台技術的詳細資訊，請參閱 [Microsoft Azure 詞彙](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)。

## <a name="glossary"></a>詞彙

* [活動](#activities)
* [工作區](#workspaces)
    * [實驗](#experiments)
        * [執行](#runs) 
            * [回合組態](#run-configurations)
            * [快照式](#snapshots)
            * [Git 追蹤](#github-tracking-and-integration)
            * [Logging](#logging)
    * [ML 管線](#ml-pipelines)
    * [模型](#models)
        * [環境](#environments)
        * [定型指令碼](#training-scripts)
        * [估算器](#estimators)
    * [端點](#endpoints)
        * [Web 服務](#web-service-endpoint)
        * [IoT 模組](#iot-module-endpoints)
    * [資料集和資料存放區](#datasets-and-datastores)
    * [計算目標](#compute-targets)

### <a name="activities"></a>活動

活動代表長時間執行的作業。 下列作業為活動範例：

* 建立或刪除計算目標
* 在計算目標上執行指令碼

活動可透過 SDK 或 Web UI 提供通知，方便您輕鬆監視這些作業的進度。

### <a name="workspaces"></a>工作區

[工作區](concept-workspace.md)是 Azure Machine Learning 的最上層資源。 其可以在您使用 Azure Machine Learning 時，提供集中式位置以處理您建立的所有成品。 您可以與其他人共用工作區。 如需工作區的詳細說明，請參閱[什麼是 Azure Machine Learning 工作區？](concept-workspace.md)。

### <a name="experiments"></a>實驗

實驗是從指定之指令碼的許多回合組成的群組。 它一律屬於某個工作區。 當您提交回合時，必須提供一個實驗名稱。 回合的資訊會儲存在該實驗底下。 如果提交回合並指定不存在的實驗名稱，就會自動以新指定的名稱建立一個新的實驗。

如需使用實驗的範例，請參閱[教學課程：進行第一個模型的定型](tutorial-1st-experiment-sdk-train.md)。

### <a name="runs"></a>執行

執行是一次定型指令碼的執行。 實驗一般會包含多個執行。

Azure Machine Learning 會記錄所有執行，並在實驗中儲存下列資訊：

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
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="github-tracking-and-integration"></a>GitHub 追蹤與整合

當您啟動來源目錄是本機 Git 存放庫的定型回合時，該存放庫的相關資訊會儲存在回合歷程記錄中。 這適用於使用估算器、ML 管線或指令碼執行所提交的執行。 其也適用於從 SDK 或 Machine Learning CLI 提交的執行。

如需詳細資訊，請參閱 [Azure Machine Learning 的 Git 整合](concept-train-model-git-integration.md)。

### <a name="logging"></a>記錄

開發解決方案時，請在 Python 指令碼中使用 Azure Machine Learning Python SDK 以記錄任意計量。 在該回合之後，請查詢計量以判斷該回合是否產生您要部署的模型。

### <a name="ml-pipelines"></a>ML 管線

您可以使用機器學習管線來建立和管理結合多個機器學習階段的工作流程。 例如，管線可能包含資料準備、模型訓練、模型部署和推斷/評分階段。 每個階段都可以包含多個步驟，這些步驟各自都可以在各種計算目標中自動執行。 

管線步驟可重複使用，如果前面步驟的輸出未變更，則可以在不重新執行前面步驟的情況下執行管線步驟。 例如，如果資料未變更，便可以在不必重新執行昂貴的資料準備步驟情況下將模型重新定型。 管線也可以讓資料科學家在分頭處理不同機器學習工作流程區域時共同作業。

如需有關機器學習管線與此服務的詳細資訊，請參閱[管線和 Azure Machine Learning](concept-ml-pipelines.md)。

### <a name="models"></a>模型

簡單來說，模型是可接受輸入並產生輸出的一段程式碼。 機器學習服務模型的建立作業涵蓋選取演算法、提供資料給演算法，以及調整超參數。 定型是一種反覆進行的程序，可產生定型的模型，其中會封裝模型在定型程序期間學到項目。

模型是由 Azure Machine Learning 中的回合所產生的。 您也可以使用在 Azure Machine Learning 外部定型的模型。 您可以在 Azure Machine Learning 工作區中註冊模型。

Azure Machine Learning 與架構無關。 在建立模型時，您可以使用任何受歡迎的機器學習架構，例如 Scikit-learn、XGBoost、PyTorch、TensorFlow 和 Chainer。

如需使用 Scikit-learn 和估算器來將模型定型的範例，請參閱[教學課程：使用 Azure Machine Learning 將影像分類模型定型](tutorial-train-models-with-aml.md)。

**模型登錄**可記錄您 Azure Machine Learning 工作區中的所有模型。

模型是透過名稱與版本來識別的。 每次註冊與現有模型名稱相同的模型時，登錄都會假設它是一個新版本。 版本會累加，新模型則會以相同的名稱來註冊。

註冊模型時，您可以提供額外的中繼資料標記，然後在搜尋模型時使用這些標記。

> [!TIP]
> 已註冊的模型是組成模型的一或多個檔案所在的邏輯容器。 例如，如果您的模型儲存在多個檔案中，您可以將其註冊為 Azure Machine Learning 工作區中的單一模型。 註冊之後，您就可以下載或部署已註冊的模型，並接收所有已註冊的檔案。

您無法刪除作用中部署正在使用的已註冊模型。

如需模型註冊的範例，請參閱[使用 Azure Machine Learning 將映像分類模型定型](tutorial-train-models-with-aml.md)。

### <a name="environments"></a>環境

Azure ML 環境可用來指定設定 (Docker/Python/Spark 等等)，以供建立可重現的環境來進行資料準備、模型定型和模型服務。 這些環境是在您 Azure Machine Learning 工作區內已設定版本的受控實體，可在不同計算目標之間啟用可重現、可稽核且可攜帶的機器學習工作流程。

您可以在本機計算上使用環境物件來開發您的定型指令碼、在 Azure Machine Learning Compute 上重複使用相同的環境以進行大規模的模型定型，甚至使用該相同環境來部署您的模型。 

了解[如何建立和管理可重複使用的 ML 環境](how-to-use-environments.md)，以進行定型和推斷。

### <a name="training-scripts"></a>將指令碼定型

您可以指定包含定型指令碼與相關聯檔案的目錄，以將模型定型。 您也可以指定用來儲存定型期間所收集資訊的實驗名稱。 在定型期間，整個目錄會複製到定型環境 (計算目標)，然後啟動回合組態指定的指令碼。 目錄的快照集也會儲存在工作區中的實驗底下。

如需範例，請參閱[教學課程：使用 Azure Machine Learning 將影像分類模型定型](tutorial-train-models-with-aml.md)。

### <a name="estimators"></a>估算器

為了能夠利用熱門架構來將模型定型，您可以使用估算器類別來輕鬆地建立回合組態。 您可以建立和使用一般的[估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)來提交定型指令碼，以使用您選擇的任何學習架構 (例如 scikit-learn)。

針對 PyTorch、TensorFlow 和 Chainer作業，Azure Machine Learning 也會提供個別的 [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)、[TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 和 [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) 估算器來簡化這些架構的使用。

如需詳細資訊，請參閱下列文章：

* [使用估算器將 ML 模型定型](how-to-train-ml-models.md)。
* [使用 Azure Machine Learning 大規模地將 Pytorch 深度學習模型定型](how-to-train-pytorch.md)。
* [使用 Azure Machine Learning 大規模地將 TensorFlow 模型定型並加以註冊](how-to-train-tensorflow.md)。
* [使用 Azure Machine Learning 大規模地將 Chainer 模型定型並加以註冊](how-to-train-ml-models.md)。

### <a name="endpoints"></a>端點

端點是指在可能裝載於雲端的 Web 服務，或在用於整合式裝置部署的 IoT 模組中將模型具現化。

#### <a name="web-service-endpoint"></a>Web 服務端點

將模型部署為 Web 服務時，可以在 Azure 容器執行個體、Azure Kubernetes Service 或 FPGA 上部署端點。 您可以從模型、指令碼和相關聯的檔案建立服務。 這些項目會放入基底容器映像，其內含模型的執行環境。 映像包含經過負載平衡的 HTTP 端點，可接收傳送至 Web 服務的評分要求。

如果已選擇啟用 Application Insight 遙測或模型遙測收集功能，Azure 將可以透過此功能協助您監視 Web 服務。 遙測資料只有您才能存取，而且會儲存在您的 Application Insights 與儲存體帳戶執行個體中。

如果已經啟用自動調整功能，Azure 將會自動調整您的部署。

如需將模型部署為 Web 服務的範例，請參閱[在 Azure 容器執行個體中部署映像分類模型](tutorial-deploy-models-with-aml.md)。

#### <a name="iot-module-endpoints"></a>IoT 模組端點

已部署的 IoT 模組端點是一個 Docker 容器，其中包含您的模型與相關聯的指令碼或應用程式，以及任何額外的相依性。 您可以在邊緣裝置上使用 Azure IoT Edge 部署這些模型。

如果您已經啟用監視功能，Azure 就會從 Azure IoT Edge 模組內部的模型收集遙測資料。 遙測資料只有您才能存取，而且會儲存在您的儲存體帳戶執行個體中。

Azure IoT Edge 會確保模組正在執行，並監視裝載模組的裝置。


### <a name="compute-instance-preview"></a><a name="compute-instance"></a>計算執行個體 (預覽)

**Azure Machine Learning 計算執行個體** (先前稱為 Notebook VM) 是完全受控的雲端式工作站，其內含針對機器學習所安裝的多個工具和環境。 計算執行個體可用來作為定型作業和推斷作業的計算目標。 針對大型工作，具有多節點擴縮功能的 [Azure Machine Learning 計算叢集](how-to-set-up-training-targets.md#amlcompute)會是比較好的計算目標選擇。

深入了解[計算執行個體](concept-compute-instance.md)。

### <a name="datasets-and-datastores"></a>資料集和資料存放區

**Azure Machine Learning 資料集** (預覽) 可讓您更輕鬆地存取和使用資料。 資料集會管理各種案例中的資料，例如模型定型和管線建立。 使用 Azure Machine Learning SDK，您就可以存取基礎儲存體、探索資料，以及管理不同資料集定義的生命週期。

資料集會提供可使用常用格式資料的方法，例如使用 `from_delimited_files()` 或 `to_pandas_dataframe()`。

如需詳細資訊，請參閱[建立及註冊 Azure Machine Learning 資料集](how-to-create-register-datasets.md)。  如需更多使用資料集的範例，請參閱[範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial)。

**資料存放區**是 Azure 儲存體帳戶的儲存體抽象概念。 資料存放區可以使用 Azure Blob 容器或 Azure 檔案共用作為後端儲存體。 每個工作區都有預設資料存放區，而且您可以註冊額外的資料存放區。 使用 Python SDK API 或 Azure Machine Learning CLI 在資料存放區中儲存及擷取檔案。

### <a name="compute-targets"></a>計算目標

[計算目標](concept-compute-target.md)可讓您指定用來執行定型指令碼或裝載服務部署的計算資源。 這個位置可能是您的本機電腦或雲端式計算資源。

深入了解[可用於定型和部署的計算目標](concept-compute-target.md)。

### <a name="next-steps"></a>後續步驟

若要開始使用 Azure Machine Learning，請參閱：

* [什麼是 Azure Machine Learning 服務？](overview-what-is-azure-ml.md)
* [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)
* [教學課程 (第 1 部分)：將模型定型](tutorial-train-models-with-aml.md)
