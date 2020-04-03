---
title: MLOps:ML 模型管理
titleSuffix: Azure Machine Learning
description: '使用 Azure 機器學習 (MOps) 瞭解模型管理。 部署、管理和監視模型以持續改進模型。 '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: f5aaf8adf33d27f8ebb99c8ca3a873d958632a4f
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616839"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MOPS:使用 Azure 機器學習進行模型管理、部署和監視

在本文中,瞭解如何使用 Azure 機器學習來管理模型的生命週期。 Azure 機器學習使用機器學習操作 (MLOps) 方法。 MLOps 提高了機器學習解決方案的品質和一致性。 

## <a name="what-is-mlops"></a>什麼是 MLOps?

機器學習操作 (MLOps) 基於提高工作流效率的[DevOps](https://azure.microsoft.com/overview/what-is-devops/)原則和實踐。 例如,持續整合、交付和部署。 MOps 將這些原則應用於機器學習過程,目標是:

* 更快的模型實驗和開發
* 更快地將模型部署到生產中
* 品質保證

Azure 機器學習提供以下 MOps 功能:

- **建立可重現的 ML 管道**。 機器學習管道允許您為數據準備、培訓和評分過程定義可重複和可重用的步驟。
- **創建可重用的軟體環境**,用於培訓和部署模型。
- **從任何位置註冊、打包和部署模型**。 您還可以追蹤使用模型所需的關聯元數據。
- **擷取端介面到端 ML 生命週期的治理資料**。 記錄的資訊可以包括發佈模型的人員、更改的原因以及模型在生產中部署或使用時。
- **通知與警示 ML 生命週期中的事件**。 例如,實驗完成、模型註冊、模型部署和數據漂移檢測。
- **監控 ML 應用程式,以尋找操作與 ML 相關問題**。 比較訓練和推理之間的模型輸入,探索特定於模型的指標,並在 ML 基礎結構上提供監視和警報。
- **使用 Azure 機器學習與 Azure 管道自動執行端到端 ML 生命週期**。 使用管道允許您經常更新模型、測試新模型,並與其他應用程式和服務一起不斷推出新的 ML 模型。

## <a name="create-reproducible-ml-pipelines"></a>建立可重現的 ML 管道

使用 Azure 機器學習的 ML 管道將模型培訓過程中涉及的所有步驟拼接在一起。

ML 管道可以包含從數據準備到特徵提取到超參數調優到模型評估的步驟。 有關詳細資訊,請參閱[ML 管道](concept-ml-pipelines.md)。

如果使用[設計器](concept-designer.md)創建 ML 管道,您可以隨時按下"..."設計器頁面右上角的 **"...",** 然後選擇 **"克隆**"。 通過克隆管道,您可以反覆運算管道設計,而不會丟失舊版本。  

## <a name="create-reusable-software-environments"></a>建立可重用的軟體環境

Azure 機器學習環境允許您跟蹤和複製專案的軟體依賴關係,使其隨著它們的發展而變化。 環境允許您確保在沒有手動軟體配置的情況下可重現生成。

環境描述專案的 pip 和 Conda 依賴項,可用於模型的培訓和部署。 有關詳細資訊,請參閱什麼是[Azure 機器學習環境](concept-environments.md)。

## <a name="register-package-and-deploy-models-from-anywhere"></a>隨時隨地註冊、打包和部署模型

### <a name="register-and-track-ml-models"></a>註冊和追蹤 ML 型號

模型註冊可讓您在 Azure 雲端自己的工作區中儲存模型和控制模型版本。 模型註冊可讓您更輕鬆地組織和追蹤已定型模型。

> [!TIP]
> 已註冊的模型是構成模型的一個或多個文件的邏輯容器。 例如,如果模型存儲在多個檔中,則可以將它們註冊為 Azure 機器學習工作區中的單個模型。 註冊后,您可以下載或部署已註冊的模型,並接收已註冊的所有檔。

已註冊的模型是透過名稱和版本來識別。 每次註冊與現有模型名稱相同的模型時，登錄都會遞加版本。 註冊期間可以提供其他元數據標記。 然後,在搜索模型時使用這些標記。 Azure 機器學習支援可以使用 Python 3.5.2 或更高版本載入的任何模型。

> [!TIP]
> 還可以註冊在 Azure 機器學習之外訓練的模型。

不能刪除在活動部署中使用的已註冊模型。
如需詳細資訊，請參閱[部署模型](how-to-deploy-and-where.md#registermodel)的註冊模型一節。

### <a name="profile-models"></a>設定檔模型

Azure 機器學習可説明您瞭解部署模型時將創建的服務的 CPU 和記憶體要求。 分析測試運行模型的服務,並返回 CPU 使用率、記憶體使用方式和回應延遲等資訊。 它還根據資源使用方式提供 CPU 和記憶體建議。
有關詳細資訊,請參閱[部署模型](how-to-deploy-and-where.md#profilemodel)的分析部分。

### <a name="package-and-debug-models"></a>包裝與除錯模型

在將模型部署到生產中之前,它將打包到 Docker 映射中。 在大多數情況下,映射創建在部署期間會自動在後台進行。 您可以手動指定圖像。

如果部署遇到問題,則可以在本地開發環境中部署以進行故障排除和調試。

有關詳細資訊,請參閱[部署模型](how-to-deploy-and-where.md#registermodel)和[故障排除部署](how-to-troubleshoot-deployment.md)。

### <a name="convert-and-optimize-models"></a>轉換並優化模型

將模型轉換為[打開神經網路交換](https://onnx.ai)(ONNX) 可能會提高性能。 平均而言,轉換為 ONNX 可以帶來 2 倍的性能提升。

有關使用 Azure 機器學習的 ONNX 的詳細資訊,請參閱[創建和加速 ML 模型](concept-onnx.md)一文。

### <a name="use-models"></a>使用模型

經過培訓的機器學習模型在雲或本地部署為 Web 服務。 還可以將模型部署到 Azure IoT 邊緣設備。 部署使用 CPU、GPU 或現場可程式設計閘陣列 (FPGA) 進行推斷。 您還可以使用 Power BI 的模型。

將模型用作 Web 服務或 IoT Edge 裝置時,提供以下專案:

* 用於對提交到服務/設備的數據進行評分的模型。
* 輸入指令碼。 此腳本接受請求,使用模型對數據進行評分,並返回回應。
* Azure 機器學習環境,用於描述模型和條目腳本所需的 pip 和 Conda 依賴項。
* 模型和條目腳本所需的任何其他資產,如文本、數據等。

您還可以提供目標部署平臺的配置。 例如,部署到 Azure 庫伯奈斯服務時,VM 系列類型、可用記憶體和內核數。

創建映射時,還會添加 Azure 機器學習所需的元件。 例如,運行 Web 服務和與 IoT Edge 互動所需的資產。

#### <a name="batch-scoring"></a>批次評分
通過 ML 管道支援批處理評分。 有關詳細資訊,請參閱[對大數據的批處理預測](how-to-use-parallel-run-step.md)。

#### <a name="real-time-web-services"></a>即時 Web 服務

您可以在**Web 服務**中使用模型,具有以下計算目標:

* Azure 容器執行個體
* Azure Kubernetes Service
* 本機開發環境

要將模型部署為 Web 服務,必須提供以下項:

* 模型或模型的組。
* 使用模型所需的依賴項。 例如,接受請求並調用模型、conda 依賴項等的腳本。
* 描述如何部署模型的位置的部署配置。

有關詳細資訊,請參閱[部署模型](how-to-deploy-and-where.md)。

#### <a name="iot-edge-devices"></a>IoT 邊緣裝置

您可以通過**Azure IoT 邊緣模組**將模型與 IoT 設備一起使用。 IoT Edge 模組部署到硬體設備,從而在設備上啟用推理或模型評分。

有關詳細資訊,請參閱[部署模型](how-to-deploy-and-where.md)。

### <a name="analytics"></a>分析

Microsoft Power BI 支援使用機器學習模型進行數據分析。 有關詳細資訊,請參閱[Power BI 中的 Azure 機器學習整合(預覽版)。](https://docs.microsoft.com/power-bi/service-machine-learning-integration)

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>擷取擷取的處理到端 ML 生命週期所需的治理資料

Azure ML 使您能夠追蹤所有 ML 資產的端到端審核追蹤。 具體來說：

- Azure ML[與 Git 整合](how-to-set-up-training-targets.md#gitintegration),以追蹤代碼來自哪個儲存庫/分支/提交的資訊。
- [Azure ML 資料集](how-to-create-register-datasets.md)可協助您追蹤、設定檔和版本資料。 
- Azure ML 執行歷史記錄儲存用於訓練模型的代碼、數據和計算的快照。
- Azure ML 模型註冊錶捕獲與模型關聯的所有元數據(如果模型的部署正常,則實驗訓練了模型,部署位置。

## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>通知、自動執行與警示 ML 生命週期中的事件
Azure ML 將關鍵事件發布到 Azure 事件網格,可用於通知和自動執行 ML 生命週期中的事件。 有關詳細資訊,請參閱[本文件](how-to-use-event-grid.md)。


## <a name="monitor-for-operational--ml-issues"></a>監控操作& ML 問題

通過監視,您可以瞭解要發送到模型的數據以及返回的預測。

此資訊可説明您瞭解模型的使用方式。 收集的輸入數據在訓練模型的未來版本時也很有用。

如需詳細資訊，請參閱[如何啟用模型資料收集](how-to-enable-data-collection.md)。

## <a name="retrain-your-model-on-new-data"></a>從新資料重新訓練模型

通常,當您收到新資訊時,您需要更新模型,甚至從頭開始重新訓練模型。 有時,接收新數據是域的預期部分。 其他時間,如[在數據集中檢測數據漂移(預覽)](how-to-monitor-datasets.md)中所述,面對特定感測器的更改、自然數據變化(如季節性影響)或與其他要素相關的特徵轉移等情況,模型性能可能會降低。 

"我如何知道我是否應該重新訓練? 但前面討論的 Azure ML 事件和監視工具是自動化的良好起點。 一旦您決定重新培訓,您應該: 

- 使用可重複的自動化流程預處理資料
- 訓練您的新型號
- 將新型號的輸出與舊型號的輸出進行比較
- 使用預先定義條件選擇是否取代舊模型 

上述步驟的主題是,您的再培訓應該是自動化的,而不是臨時的。 [Azure 機器學習管道](concept-ml-pipelines.md)是創建與數據準備、培訓、驗證和部署相關的工作流的一個很好的答案。 使用[Azure 機器學習設計器(預覽)讀取重新訓練模型,](how-to-retrain-designer.md)瞭解管道和 Azure 機器學習設計器如何適應重新訓練方案。 

## <a name="automate-the-ml-lifecycle"></a>自動化 ML 生命週期 

可以使用 GitHub 和 Azure 管道創建連續整合過程來訓練模型。 在典型方案中,當數據科學家檢查專案的 Git 存儲庫中的更改時,Azure 管道將開始訓練運行。 然後可以檢查運行結果以查看已訓練的模型的性能特徵。 您還可以創建將模型部署為 Web 服務的管道。

[Azure 機器學習擴展](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml)使使用 Azure 管道變得更加容易。 它為 Azure 管道提供以下增強功能:

* 在定義服務連接時啟用工作區選擇。
* 使發佈管道能夠由在訓練管道中創建的經過訓練的模型觸發。

有關將 Azure 管道與 Azure 機器學習一起使用的詳細資訊,請參閱以下連結:

* [與 Azure 管道持續整合和部署 ML 模型](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Azure 機器學習 MLOps](https://aka.ms/mlops)儲存庫。
* [Azure 機器學習 MLOpsPython](https://github.com/Microsoft/MLOpspython)儲存庫。

您還可以使用 Azure 資料工廠創建數據引入管道,以準備數據以用於培訓。 有關詳細資訊,請參閱[資料引入管道](how-to-cicd-data-ingestion.md)。

## <a name="next-steps"></a>後續步驟

透過閱讀及瀏覽以下資源瞭解更多資訊:

+ 如何使用 Azure 機器學習[&部署模型的位置](how-to-deploy-and-where.md)

+ [教學:在 ACI 中部署映像類別模型](tutorial-deploy-models-with-aml.md)。

+ [端到端 MLOps 範例](https://github.com/microsoft/MLOps)

+ [帶有 Azure 管道的 ML 模型的 CI/CD](/azure/devops/pipelines/targets/azure-machine-learning)

+ 建立[使用已部署模型的](how-to-consume-web-service.md)用戶端

+ [大規模機器學習](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Azure AI 參考體系結構&最佳實踐代表](https://github.com/microsoft/AI)
