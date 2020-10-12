---
title: 架構和重要概念
titleSuffix: Azure Machine Learning
description: 瞭解組成 Azure Machine Learning 的架構、詞彙和概念。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: 71032c49ac5164f13189baf64668f8998fdc186a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91276079"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Azure Machine Learning 的運作方式：架構和概念

瞭解 [Azure Machine Learning](overview-what-is-azure-ml.md)的架構和概念。  本文可讓您深入瞭解元件，以及它們如何一起運作，以協助建立、部署和維護機器學習模型。

## <a name="workspace"></a><a name="workspace"></a> 工作

[Machine learning 工作區](concept-workspace.md)是 Azure Machine Learning 的最上層資源。

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="圖：工作區及其元件的 Azure Machine Learning 架構":::

工作區是集中位置：

* 管理用於定型和部署模型的資源，例如 [計算](#compute-instance)
* 當您使用 Azure Machine Learning 時，儲存您所建立的資產，包括：
  * [環境](#environments)
  * [實驗](#experiments)
  * [管線](#ml-pipelines)
  * [資料集](#datasets-and-datastores)
  * [模型](#models)
  * [端點](#endpoints)

工作區包含工作區所使用的其他 Azure 資源：

+ [Azure Container Registry (ACR) ](https://azure.microsoft.com/services/container-registry/)：註冊您在定型期間以及在部署模型時使用的 docker 容器。 為了將成本降至最低，只會在建立部署映射時建立 ACR。
+ [Azure 儲存體帳戶](https://azure.microsoft.com/services/storage/)：作為工作區的預設資料存放區使用。  Jupyter 與 Azure Machine Learning 計算實例搭配使用的筆記本也會儲存在這裡。
+ [Azure 應用程式見解](https://azure.microsoft.com/services/application-insights/)：儲存有關模型的監視資訊。
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)：儲存計算目標所使用的秘密，以及工作區所需的其他機密資訊。

您可以與其他人共用工作區。

## <a name="computes"></a>計算

<a name="compute-targets"></a>[計算目標](concept-compute-target.md)是用來執行定型腳本或裝載服務部署的任何電腦或一組電腦。 您可以使用本機電腦或遠端計算資源作為計算目標。  使用計算目標時，您可以開始在本機電腦上進行定型，然後向外延展到雲端，而不需要變更訓練腳本。

Azure Machine Learning 引進兩個完全受控的雲端式虛擬機器， (VM) 針對機器學習工作進行設定：

* <a name="compute-instance"></a>**計算實例**：計算實例是 VM，其中包含針對機器學習服務安裝的多個工具和環境。 計算實例的主要用途是針對您的開發工作站。  您可以開始執行範例筆記本，而不需要設定。 計算實例也可用來做為定型和推斷作業的計算目標。

* **計算**叢集：計算叢集是具有多節點調整功能的 vm 叢集。 計算叢集較適用于大型作業和生產環境的計算目標。  叢集會在提交作業時自動相應增加。  使用作為定型計算目標或用於開發/測試部署。

如需有關定型計算目標的詳細資訊，請參閱 [定型計算目標](concept-compute-target.md#train)。  如需部署計算目標的詳細資訊，請參閱 [部署目標](concept-compute-target.md#deploy)。

## <a name="datasets-and-datastores"></a>資料集和資料存放區

[**Azure Machine Learning 資料集**](concept-data.md#datasets)  可讓您更輕鬆地存取及使用資料。 藉由建立資料集，您可以建立資料來源位置的參考，以及其中繼資料的複本。 因為資料會保留在現有的位置，所以不會產生額外的儲存成本，也不會對資料來源的完整性產生風險。

如需詳細資訊，請參閱[建立及註冊 Azure Machine Learning 資料集](how-to-create-register-datasets.md)。  如需更多使用資料集的範例，請參閱[範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial)。

資料集使用 [資料存放區](concept-data.md#datastores) 安全地連線到您的 Azure 儲存體服務。 資料存放區儲存連接資訊，而不會將您的驗證認證與原始資料來源的完整性保持在風險下。 他們會將連線資訊（例如您的訂用帳戶識別碼和權杖授權）儲存在與工作區相關聯的 Key Vault 中，以便您可以安全地存取儲存體，而不需要在腳本中進行硬程式碼撰寫。

## <a name="environments"></a>環境

[工作區](#workspace)  > **環境**

[環境](concept-environments.md)是環境的封裝，在此環境中，您的機器學習模型會定型或計分。 環境會指定訓練和評分腳本周圍的 Python 套件、環境變數和軟體設定。  

如需程式碼範例，請參閱 [如何使用環境](how-to-use-environments.md#manage-environments)的「管理環境」一節。

## <a name="experiments"></a>實驗

[工作區](#workspace)  > **實驗**

實驗是從指定之指令碼的許多回合組成的群組。 它一律屬於某個工作區。 當您提交回合時，必須提供一個實驗名稱。 回合的資訊會儲存在該實驗底下。 當您提交實驗時，如果名稱不存在，則會自動建立新的實驗。
  
如需使用實驗的範例，請參閱[教學課程：進行第一個模型的定型](tutorial-1st-experiment-sdk-train.md)。

### <a name="runs"></a>執行

[工作區](#workspace)  > [實驗](#experiments)  > **執行**

執行是一次定型指令碼的執行。 實驗一般會包含多個執行。

Azure Machine Learning 會記錄所有執行，並在實驗中儲存下列資訊：

* 與回合有關的中繼資料 (時間戳記、持續時間等等)
* 由您的指令碼記錄的計量
* 由實驗自動收集，或由您明確上傳的輸出檔案
* 包含您指令碼之目錄的快照 (執行之前)

當您提交指令碼以將模型定型時，就會產生回合。 回合可以有 0 或多個子回合。 例如，最上層回合可能有兩個子回合，其中每個可能都有自己的子回合。

### <a name="run-configurations"></a>回合組態

[工作區](#workspace)  > [實驗](#experiments)  > [執行](#runs)  > **執行**設定

回合設定會定義如何在指定的計算目標中執行腳本。 您可以使用此設定來指定要執行的腳本、計算目標和 Azure ML 環境、任何分散式工作專屬的設定，以及一些其他屬性。 如需一組完整的可設定選項的詳細資訊，請參閱 [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true)。

回合設定可保存在包含定型腳本的目錄內的檔案中。   您也可以將它視為記憶體內建物件，用來提交執行。

如需執行設定的範例，請參閱 [設定定型](how-to-set-up-training-targets.md)回合。

### <a name="snapshots"></a>快照集

[工作區](#workspace)  > [實驗](#experiments)  > [執行](#runs)  > **快照**集

提交回合時，Azure Machine Learning 會將包含指令碼的目錄壓縮成 zip 檔案，然後傳送至計算目標。 接著會將 zip 檔案解壓縮並在該處執行指令碼。 Azure Machine Learning 也會將 zip 檔案以快照方式儲存在回合記錄中。 任何擁有工作區存取權的人都能瀏覽回合記錄並下載快照集。

### <a name="logging"></a>記錄

Azure Machine Learning 會為您自動記錄標準執行計量。 不過，您也可以 [使用 PYTHON SDK 來記錄任意計量](how-to-track-experiments.md)。

有多種方式可以查看您的記錄：即時監視執行狀態，或在完成時查看結果。 如需詳細資訊，請參閱 [監視和查看 ML 執行記錄](how-to-monitor-view-training-logs.md)。


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Git 追蹤與整合

當您啟動來源目錄是本機 Git 存放庫的定型回合時，該存放庫的相關資訊會儲存在回合歷程記錄中。 這適用于使用腳本執行設定或 ML 管線提交的執行。 其也適用於從 SDK 或 Machine Learning CLI 提交的執行。

如需詳細資訊，請參閱 [Azure Machine Learning 的 Git 整合](concept-train-model-git-integration.md)。

## <a name="models"></a>模型

簡單來說，模型是可接受輸入並產生輸出的一段程式碼。 建立機器學習模型牽涉到選取演算法、為它提供資料，以及 [調整超參數](how-to-tune-hyperparameters.md)。 定型是一種反覆進行的程序，可產生定型的模型，其中會封裝模型在定型程序期間學到項目。

您可以將在 Azure Machine Learning 之外定型的模型帶入。 或者，您可以將[實驗](#experiments)的[執行](#runs)提交至 Azure Machine Learning 中的[計算目標](#compute-targets)，以定型模型。 一旦有模型之後，您就可以在工作區中 [註冊模型](#register-model) 。

Azure Machine Learning 與架構無關。 在建立模型時，您可以使用任何受歡迎的機器學習架構，例如 Scikit-learn、XGBoost、PyTorch、TensorFlow 和 Chainer。

如需使用 Scikit-learn-學習來定型模型的範例，請參閱 [教學課程：使用 Azure Machine Learning 將影像分類模型定型](tutorial-train-models-with-aml.md)。


### <a name="model-registry"></a><a name="register-model"></a> 模型登錄

[工作區](#workspace)  > **模型**

**模型**登錄可讓您追蹤 Azure Machine Learning 工作區中的所有模型。

模型是透過名稱與版本來識別的。 每次註冊與現有模型名稱相同的模型時，登錄都會假設它是一個新版本。 版本會累加，新模型則會以相同的名稱來註冊。

註冊模型時，您可以提供額外的中繼資料標記，然後在搜尋模型時使用這些標記。

> [!TIP]
> 已註冊的模型是組成模型的一或多個檔案所在的邏輯容器。 例如，如果您的模型儲存在多個檔案中，您可以將其註冊為 Azure Machine Learning 工作區中的單一模型。 註冊之後，您就可以下載或部署已註冊的模型，並接收所有已註冊的檔案。

您無法刪除作用中部署正在使用的已註冊模型。

如需模型註冊的範例，請參閱[使用 Azure Machine Learning 將映像分類模型定型](tutorial-train-models-with-aml.md)。

## <a name="deployment"></a>部署

您會將 [已註冊的模型](#register-model) 部署為服務端點。 您需要下列元件：

* **環境**。 此環境會封裝執行您的模型以進行推斷所需的相依性。
* **評分程式碼**。 此腳本會接受要求、使用模型來評分要求，並傳回結果。
* **推斷**設定。 推斷設定會指定將模型當作服務執行所需的環境、輸入腳本和其他元件。

如需這些元件的詳細資訊，請參閱 [使用 Azure Machine Learning 部署模型](how-to-deploy-and-where.md)。

### <a name="endpoints"></a>端點

[工作區](#workspace)  > **端點**

端點是指在可能裝載於雲端的 Web 服務，或在用於整合式裝置部署的 IoT 模組中將模型具現化。

#### <a name="web-service-endpoint"></a>Web 服務端點

將模型部署為 web 服務時，您可以將端點部署在 Azure 容器實例上、Azure Kubernetes Service 或 Fpga。 您可以從模型、指令碼和相關聯的檔案建立服務。 這些會放入基底容器映射中，其中包含模型的執行環境。 映像包含經過負載平衡的 HTTP 端點，可接收傳送至 Web 服務的評分要求。

您可以啟用 Application Insights 遙測或模型遙測，以監視您的 web 服務。 您只能存取遙測資料。  它會儲存在您的 Application Insights 和儲存體帳戶實例中。

如果已經啟用自動調整功能，Azure 將會自動調整您的部署。

如需將模型部署為 Web 服務的範例，請參閱[在 Azure 容器執行個體中部署映像分類模型](tutorial-deploy-models-with-aml.md)。

#### <a name="real-time-endpoints"></a>即時端點

當您在設計工具中部署定型的模型時，您可以將 [模型部署為即時端點](tutorial-designer-automobile-price-deploy.md)。 即時端點通常會透過 REST 端點接收單一要求，並即時傳回預測。 這與批次處理不同，它會一次處理多個值，並在完成後將結果儲存至資料存放區。

#### <a name="pipeline-endpoints"></a>管線端點

管線端點可讓您透過 REST 端點以程式設計方式呼叫您的 [ML 管線](#ml-pipelines) 。 管線端點可讓您將管線工作流程自動化。

管線端點是已發佈管線的集合。 此邏輯組織可讓您使用相同的端點來管理和呼叫多個管線。 管線端點中每個已發佈的管線都已建立版本。 您可以選取端點的預設管線，或在 REST 呼叫中指定版本。
 

#### <a name="iot-module-endpoints"></a>IoT 模組端點

已部署的 IoT 模組端點是一個 Docker 容器，其中包含您的模型與相關聯的指令碼或應用程式，以及任何額外的相依性。 您可以在邊緣裝置上使用 Azure IoT Edge 部署這些模型。

如果您已經啟用監視功能，Azure 就會從 Azure IoT Edge 模組內部的模型收集遙測資料。 遙測資料只有您才能存取，而且會儲存在您的儲存體帳戶執行個體中。

Azure IoT Edge 會確保模組正在執行，並監視裝載模組的裝置。 
## <a name="automation"></a>自動化

### <a name="azure-machine-learning-cli"></a>Azure Machine Learning CLI 

[AZURE MACHINE LEARNING CLI](reference-azure-machine-learning-cli.md)是 Azure CLI 的延伸模組，這是適用于 Azure 平臺的跨平臺命令列介面。 此延伸模組會提供命令，以將您的機器學習活動自動化。

### <a name="ml-pipelines"></a>ML 管線

您可以使用 [機器學習管線](concept-ml-pipelines.md) 來建立和管理將機器學習階段拼接在一起的工作流程。 例如，管線可能包含資料準備、模型訓練、模型部署和推斷/評分階段。 每個階段都可以包含多個步驟，這些步驟各自都可以在各種計算目標中自動執行。 

管線步驟可重複使用，如果前面步驟的輸出未變更，則可以在不重新執行前面步驟的情況下執行管線步驟。 例如，如果資料未變更，便可以在不必重新執行昂貴的資料準備步驟情況下將模型重新定型。 管線也可以讓資料科學家在分頭處理不同機器學習工作流程區域時共同作業。

## <a name="interacting-with-your-workspace"></a>與您的工作區互動

### <a name="studio"></a>Studio

[Azure Machine Learning studio](overview-what-is-machine-learning-studio.md) 會提供您工作區中所有成品的 web 視圖。  您可以查看資料集、實驗、管線、模型和端點的結果和詳細資料。  您也可以在 studio 中管理計算資源和資料存放區。

Studio 也可讓您存取屬於 Azure Machine Learning 一部分的互動式工具：

+ [Azure Machine Learning 設計](concept-designer.md) 工具來執行工作流程步驟，而不需要撰寫程式碼
+ [自動化機器學習](concept-automated-ml.md)的 Web 體驗
+ [Azure Machine Learning 筆記本](how-to-run-jupyter-notebooks.md) 在整合式 Jupyter 筆記本伺服器中撰寫和執行您自己的程式碼。
+ [資料標記專案](how-to-create-labeling-projects.md) ，以建立、管理及監視專案以標示您的資料

### <a name="programming-tools"></a>程式設計工具

> [!IMPORTANT]
> 以下是標示 (preview) 的工具目前處於公開預覽狀態。
> 此預覽版本會在沒有服務等級協定的情況下提供，不建議用於實際執行工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

+  使用[適用於 Python 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)，與任何 Python 環境中的服務互動。
+ 使用 [適用于 r](https://azure.github.io/azureml-sdk-for-r/reference/index.html) (preview) 的 Azure Machine Learning SDK，在任何 R 環境中與服務互動。
+ 使用 [Azure Machine Learning 設計](concept-designer.md) 工具來執行工作流程步驟，而不需要撰寫程式碼。 
+ 使用 [AZURE MACHINE LEARNING CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) 進行自動化。
+ [Many Models Solution Accelerator](https://aka.ms/many-models) (預覽) 建置在 Azure Machine Learning 中，可讓您定型、操作及管理上百個或甚至數千個機器學習模型。

## <a name="next-steps"></a>後續步驟

若要開始使用 Azure Machine Learning，請參閱：

* [什麼是 Azure Machine Learning 服務？](overview-what-is-azure-ml.md)
* [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)
* [教學課程 (第 1 部分)：將模型定型](tutorial-train-models-with-aml.md)
