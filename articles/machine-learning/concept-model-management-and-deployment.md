---
title: MLOps： ML 模型管理
titleSuffix: Azure Machine Learning
description: '瞭解 Azure Machine Learning 的模型管理（MLOps）。 部署、管理及監視您的模型，以持續改進它們。 '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: d1d14fa9730e3ddd47378a45ff7a1442bdee69ac
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543379"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps：使用 Azure Machine Learning 進行模型管理、部署和監視

在本文中，您將瞭解如何使用 Azure Machine Learning 來管理模型的生命週期。 Azure Machine Learning 使用 Machine Learning 作業（MLOps）方法。 MLOps 可改善機器學習解決方案的品質與一致性。 

## <a name="what-is-mlops"></a>什麼是 MLOps?

Machine Learning 作業（MLOps）是以[DevOps](https://azure.microsoft.com/overview/what-is-devops/)原則和實務為基礎，可提高工作流程的效率。 例如，持續整合、傳遞和部署。 MLOps 會將這些原則套用至機器學習程式，其目標為：

* 更快速地測試和開發模型
* 更快速地將模型部署至生產環境
* 品質保證

Azure Machine Learning 提供下列 MLOps 功能：

- **建立可重現的 ML 管線**。 Machine Learning 管線可讓您針對資料準備、定型和評分程式，定義可重複且可重複使用的步驟。
- **建立可重複使用的軟體環境**，以定型和部署模型。
- **從任何地方註冊、封裝和部署模型**。 您也可以追蹤使用模型所需的相關中繼資料。
- **捕捉端對端 ML 生命週期的治理資料**。 記錄的資訊可能包括正在發行模型的物件、進行變更的原因，以及模型部署或用於生產環境中的時間。
- **通知並警示 ML 生命週期中的事件**。 例如，實驗完成、模型註冊、模型部署和資料漂移偵測。
- **監視 ml 應用程式的操作和 ml 相關問題**。 比較定型和推斷之間的模型輸入、探索模型特定計量，以及提供 ML 基礎結構的監視和警示。
- **使用 Azure Machine Learning 和 Azure Pipelines 自動化端對端 ML 生命週期**。 使用管線可讓您經常更新模型、測試新的模型，以及持續向其他應用程式和服務推出新的 ML 模型。

## <a name="create-reproducible-ml-pipelines"></a>建立可重現的 ML 管線

使用 Azure Machine Learning 中的 ML 管線，將您的模型定型程式所涉及的所有步驟結合在一起。

ML 管線可以包含從資料準備到功能解壓縮到超參數微調到模型評估的步驟。 如需詳細資訊，請參閱[ML 管線](concept-ml-pipelines.md)。

如果您使用[設計](concept-designer.md)工具來建立 ML 管線，您隨時都可以按一下設計工具頁面右上方的 [ **...]** ，然後選取 [**複製**]。 複製管線可讓您反復執行管線設計，而不會遺失舊版本。  

## <a name="create-reusable-software-environments"></a>建立可重複使用的軟體環境

Azure Machine Learning 的環境可讓您追蹤和重現專案的軟體相依性。 環境可讓您確保組建可重現，而不需要手動軟體設定。

環境會描述專案的 pip 和 Conda 相依性，並可用於定型和部署模型。 如需詳細資訊，請參閱[什麼是 Azure Machine Learning 環境](concept-environments.md)。

## <a name="register-package-and-deploy-models-from-anywhere"></a>從任何地方註冊、封裝和部署模型

### <a name="register-and-track-ml-models"></a>註冊和追蹤 ML 模型

模型註冊可讓您在 Azure 雲端自己的工作區中儲存模型和控制模型版本。 模型註冊可讓您更輕鬆地組織和追蹤已定型模型。

> [!TIP]
> 已註冊的模型是組成模型的一或多個檔案所在的邏輯容器。 例如，如果您的模型儲存在多個檔案中，您可以將其註冊為 Azure Machine Learning 工作區中的單一模型。 註冊之後，您就可以下載或部署已註冊的模型，並接收所有已註冊的檔案。

已註冊的模型是透過名稱和版本來識別。 每次註冊與現有模型名稱相同的模型時，登錄都會遞加版本。 註冊期間可以提供額外的元資料標記。 然後在搜尋模型時使用這些標記。 Azure Machine Learning 支援可使用 Python 3.5.2 或更高版本載入的任何模型。

> [!TIP]
> 您也可以註冊在 Azure Machine Learning 外部定型的模型。

您無法刪除使用中部署的已註冊模型。
如需詳細資訊，請參閱[部署模型](how-to-deploy-and-where.md#registermodel)的註冊模型一節。

### <a name="profile-models"></a>設定檔模型

Azure Machine Learning 可以協助您瞭解當您部署模型時，將會建立之服務的 CPU 和記憶體需求。 分析會測試執行模型的服務，並傳回如 CPU 使用量、記憶體使用量和回應延遲的資訊。 它也會根據資源使用量提供 CPU 和記憶體建議。
如需詳細資訊，請參閱[部署模型](how-to-deploy-profile-model.md)的分析一節。

### <a name="package-and-debug-models"></a>封裝和調試模型

將模型部署到生產環境之前，它會封裝成 Docker 映射。 在大部分情況下，映射建立會在部署期間自動在背景中進行。 您可以手動指定映射。

如果您在部署時遇到問題，您可以在本機開發環境上部署，以進行疑難排解和偵錯工具。

如需詳細資訊，請參閱[部署模型](how-to-deploy-and-where.md#registermodel)和[疑難排解部署](how-to-troubleshoot-deployment.md)。

### <a name="convert-and-optimize-models"></a>轉換和優化模型

將模型轉換成[Open Neural Network Exchange](https://onnx.ai) （ONNX）可能會改善效能。 平均來說，轉換成 ONNX 可能會增加2倍的效能。

如需 ONNX 與 Azure Machine Learning 的詳細資訊，請參閱[建立和加速 ML 模型](concept-onnx.md)一文。

### <a name="use-models"></a>使用模型

定型的機器學習模型會部署為雲端或本機的 web 服務。 您也可以將模型部署到 Azure IoT Edge 裝置。 部署會使用 CPU、GPU 或可現場程式化閘道陣列（FPGA）來進行推斷。 您也可以使用 Power BI 的模型。

使用模型作為 Web 服務或 IoT Edge 裝置時，您會提供下列項目：

* 用來對提交給服務/裝置的資料進行評分的模型。
* 輸入指令碼。 此腳本會接受要求，使用模型來對資料進行評分，並傳迴響應。
* Azure Machine Learning 環境，描述模型和專案腳本所需的 pip 和 Conda 相依性。
* 模型和專案腳本所需的任何其他資產，例如文字、資料等等。

您也會提供目標部署平台的設定。 例如，VM 系列類型、可用記憶體，以及部署到 Azure Kubernetes Service 時的核心數目。

建立映像時，也會新增 Azure Machine Learning 所需的元件。 例如，執行 Web 服務及與 IoT Edge 互動時所需的資產。

#### <a name="batch-scoring"></a>批次評分
透過 ML 管線支援批次評分。 如需詳細資訊，請參閱[海量資料的批次預測](how-to-use-parallel-run-step.md)。

#### <a name="real-time-web-services"></a>即時 web 服務

您可以在**web 服務**中使用您的模型，並搭配下列計算目標：

* Azure 容器執行個體
* Azure Kubernetes Service
* 本機開發環境

若要將模型部署為 web 服務，您必須提供下列專案：

* 模型或模型的集團。
* 使用模型所需的相依性。 例如，接受要求並叫用模型、conda 相依性等的腳本。
* 部署設定，描述部署模型的方式和位置。

如需詳細資訊，請參閱[部署模型](how-to-deploy-and-where.md)。

#### <a name="controlled-rollout"></a>控制首度發行

部署到 Azure Kubernetes Service 時，您可以使用受控制的推出來啟用下列案例：

* 為部署建立端點的多個版本
* 藉由將流量路由傳送至不同的端點版本來執行 A/B 測試。
* 藉由更新端點設定中的流量百分比，在端點版本之間切換。

如需詳細資訊，請參閱[受控制的 ML 模型推出](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview)。

#### <a name="iot-edge-devices"></a>IoT Edge 裝置

您可以透過**Azure IoT Edge 模組**，使用 IoT 裝置的模型。 IoT Edge 模組會部署到裝置上啟用推斷或模型評分的硬體裝置。

如需詳細資訊，請參閱[部署模型](how-to-deploy-and-where.md)。

### <a name="analytics"></a>分析

Microsoft Power BI 支援使用機器學習模型來進行資料分析。 如需詳細資訊，請參閱[Power BI 中的 Azure Machine Learning 整合（預覽）](https://docs.microsoft.com/power-bi/service-machine-learning-integration)。

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>取得捕獲端對端 ML 生命週期所需的治理資料

Azure ML 可讓您使用中繼資料來追蹤所有 ML 資產的端對端審核記錄。

- Azure ML 會[與 Git 整合](how-to-set-up-training-targets.md#gitintegration)，以追蹤您的程式碼來自哪個存放庫/分支/認可的資訊。
- [AZURE ML 資料集](how-to-create-register-datasets.md)可協助您追蹤、分析和版本資料。
- [Interpretability](how-to-machine-learning-interpretability.md)可讓您對模型進行說明、符合法規合規性，並瞭解模型如何抵達給定輸入的結果。
- Azure ML 執行歷程記錄會儲存用來定型模型之程式碼、資料和計算的快照集。
- Azure ML 模型登錄會捕捉與您的模型相關聯的所有中繼資料（如果其部署狀況良好，則會在部署時進行定型）。
- [與 Azure 整合](how-to-use-event-grid.md)可讓您對 ML 生命週期中的事件採取行動。 例如，模型註冊、部署、資料漂移和定型（執行）事件。

> [!TIP]
> 雖然會自動捕捉模型和資料集的某些資訊，但您可以使用__標記__來新增其他資訊。 在您的工作區中尋找已註冊的模型和資料集時，您可以使用標記做為篩選準則。
>
> 將資料集與已註冊的模型產生關聯是選擇性步驟。 如需註冊模型時參考資料集的詳細資訊，請參閱[模型](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py)類別參考。


## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>通知、自動化和警示 ML 生命週期中的事件
Azure ML 會將主要事件發佈到 Azure EventGrid，可用來通知和自動化 ML 生命週期中的事件。 如需詳細資訊，請參閱[這份檔](how-to-use-event-grid.md)。


## <a name="monitor-for-operational--ml-issues"></a>適用于操作 & ML 問題的監視

監視可讓您瞭解哪些資料要傳送至您的模型，以及它所傳回的預測。

此資訊可協助您瞭解模型的使用方式。 收集的輸入資料可能也有助於定型模型的未來版本。

如需詳細資訊，請參閱[如何啟用模型資料收集](how-to-enable-data-collection.md)。

## <a name="retrain-your-model-on-new-data"></a>針對新資料重新定型您的模型

通常，您會想要驗證您的模型、進行更新，或甚至從頭重新訓練，因為您會收到新的資訊。 有時候，接收新資料是網域的預期部分。 如在[資料集上偵測資料漂移（預覽）](how-to-monitor-datasets.md)中所討論的其他時間，模型效能可能會因為特定感應器的變更、自然資料變更（例如季節性效果）或功能在其與其他功能之間的關聯性轉移而降低。 

「如何? 知道是否應該重新訓練？」的通用解答 但是先前所討論的 Azure ML 事件和監視工具是自動化的好起點。 一旦您決定重新定型，您應該： 

- 使用可重複的自動化程式來前置處理您的資料
- 訓練您的新模型
- 將新模型的輸出與舊模型的輸出進行比較
- 使用預先定義的準則，選擇是否要取代舊的模型 

上述步驟的主題是，您的重新定型應該是自動化的，而不是特定的。 [Azure Machine Learning 管線](concept-ml-pipelines.md)是建立與資料準備、訓練、驗證和部署相關之工作流程的好辦法。 閱讀[使用 Azure Machine Learning 設計工具（預覽）](how-to-retrain-designer.md)重新定型模型，以瞭解管線和 Azure Machine Learning 設計工具如何融入重新定型案例。 

## <a name="automate-the-ml-lifecycle"></a>自動化 ML 生命週期 

您可以使用 GitHub 和 Azure Pipelines 來建立可訓練模型的連續整合程式。 在典型的案例中，當資料科學家將變更簽入專案的 Git 存放庫時，Azure 管線就會開始執行定型。 然後，可以檢查執行的結果，以查看定型模型的效能特性。 您也可以建立管線，將模型部署為 web 服務。

[Azure Machine Learning 延伸](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml)模組可讓您更輕鬆地使用 Azure Pipelines。 它提供 Azure Pipelines 的下列增強功能：

* 在定義服務連接時啟用工作區選取。
* 可讓發行管線由定型管線中建立的定型模型觸發。

如需有關搭配 Azure Machine Learning 使用 Azure Pipelines 的詳細資訊，請參閱下列連結：

* [使用 Azure Pipelines 持續整合和部署 ML 模型](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Azure Machine Learning MLOps](https://aka.ms/mlops)存放庫。
* [Azure Machine Learning MLOpsPython](https://github.com/Microsoft/MLOpspython)存放庫。

您也可以使用 Azure Data Factory 來建立資料內嵌管線，以準備要與定型搭配使用的資料。 如需詳細資訊，請參閱[資料內嵌管線](how-to-cicd-data-ingestion.md)。

## <a name="next-steps"></a>後續步驟

若要深入瞭解，請閱讀並探索下列資源：

+ [如何使用 Azure Machine Learning & 部署模型的位置](how-to-deploy-and-where.md)

+ [教學課程：在 ACI 中部署影像分類模型](tutorial-deploy-models-with-aml.md)。

+ [端對端 MLOps 範例存放庫](https://github.com/microsoft/MLOps)

+ [具有 Azure Pipelines 的 ML 模型 CI/CD](/azure/devops/pipelines/targets/azure-machine-learning)

+ 建立使用已[部署模型](how-to-consume-web-service.md)的用戶端

+ [大規模機器學習](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Azure AI 參考架構 & 最佳做法代表](https://github.com/microsoft/AI)
