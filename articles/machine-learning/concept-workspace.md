---
title: 什麼是工作區
titleSuffix: Azure Machine Learning
description: 工作區是 Azure 機器學習的頂級資源。 它保留所有培訓運行的歷史記錄，包括日誌、指標、輸出和腳本快照。 使用此資訊確定哪個訓練運行生成最佳模型
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.openlocfilehash: 03bc49c24e3c2d32e97f3e5e03bd39da63428a6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505570"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>什麼是 Azure 機器學習工作區？

工作區是 Azure 機器學習的頂級資源，它提供了一個集中的位置，用於處理使用 Azure 機器學習時創建的所有專案。  工作區保留所有培訓運行的歷史記錄，包括日誌、指標、輸出和腳本快照。 您可以使用此資訊來判斷哪一個定型回合會產生最佳模型。  

獲得喜歡的模型後，即可將其註冊到工作區。 然後，使用已註冊的模型和評分腳本部署到 Azure 容器實例、Azure 庫伯奈斯服務或欄位可程式設計閘陣列 （FPGA） 作為基於 REST 的 HTTP 終結點。 還可以將模型作為模組部署到 Azure IoT 邊緣設備。

可用的定價和功能取決於是為工作區選擇["基本版"還是"企業版](overview-what-is-azure-ml.md#sku)"。 創建[工作區](#create-workspace)時選擇版本。  [您還可以從](#upgrade)基本版升級到企業版。

## <a name="taxonomy"></a>分類法 

下圖說明工作區的分類：

[![工作區分類](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

該圖顯示了工作區的以下元件：

+ 工作區可以包含[Azure 機器學習計算實例](concept-compute-instance.md)，即配置運行 Azure 機器學習所需的 Python 環境的雲資源。

+ [使用者角色](how-to-assign-roles.md)使您能夠與其他使用者、團隊或專案共用工作區。
+ [計算目標](concept-azure-machine-learning-architecture.md#compute-targets)用於運行實驗。
+ 創建工作區時，還會為您創建[關聯的資源](#resources)。
+ [實驗](concept-azure-machine-learning-architecture.md#experiments)是您用來構建模型的訓練運行。  
+ [管道](concept-azure-machine-learning-architecture.md#ml-pipelines)是可重用的工作流，用於培訓和重新訓練模型。
+ [資料集](concept-azure-machine-learning-architecture.md#datasets-and-datastores)有助於管理用於模型訓練和創建管道的資料。
+ 獲得要部署的模型後，將創建已註冊的模型。
+ 使用已註冊的模型和評分腳本創建[部署終結點](concept-azure-machine-learning-architecture.md#endpoints)。

## <a name="tools-for-workspace-interaction"></a>工作區交互工具

您可以通過以下方式與工作區進行交互：

+ 在網路上：
    + [Azure 機器學習工作室](https://ml.azure.com) 
    + [Azure 機器學習設計器（預覽版）](concept-designer.md) - 僅在[企業版](overview-what-is-azure-ml.md#sku)工作區中可用。
+ 在任何具有[Python 的 Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)的 Python 環境中。
+ 在任何 R 環境中，使用[用於 R 的 Azure 機器學習 SDK。](https://azure.github.io/azureml-sdk-for-r/reference/index.html)
+ 使用 Azure 機器學習[CLI 擴展](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli)名的命令列上

## <a name="machine-learning-with-a-workspace"></a>帶工作區的機器學習

機器學習任務讀取和/或將工件寫入工作區。

+ 運行實驗以訓練模型 - 將實驗運行結果寫入工作區。
+ 使用自動 ML 訓練模型 - 將培訓結果寫入工作區。
+ 在工作區中註冊模型。
+ 部署模型 - 使用已註冊的模型創建部署。
+ 創建並運行可重用的工作流。
+ 查看機器學習專案，如實驗、管道、模型和部署。
+ 跟蹤和監控模型。

## <a name="workspace-management"></a>工作區管理

您還可以執行以下工作區管理工作：

| 工作區管理工作   | 入口網站              | Studio | Python SDK / R SDK       | CLI        |
|---------------------------|---------|---------|------------|------------|
| 建立工作區        | **&check;**     | | **&check;** | **&check;** |
| 管理工作區訪問    | **&check;**   || |  **&check;**    |
| 升級到企業版    | **&check;** | **&check;**  | |     |
| 創建和管理計算資源    | **&check;**   | **&check;** | **&check;** |  **&check;**   |
| 創建筆記本 VM |   | **&check;** | |     |

> [!WARNING]
> 不支援將 Azure 機器學習工作區移動到其他訂閱，或將所屬訂閱移動到新租戶。 這樣做可能會導致錯誤。

## <a name="create-a-workspace"></a><a name='create-workspace'></a>創建工作區

創建工作區時，可以決定是否使用["基本"版還是企業版](overview-what-is-azure-ml.md#sku)創建工作區。 該版本確定工作區中可用的功能。 除其他功能外，企業版允許您訪問[Azure 機器學習設計器](concept-designer.md)和構建[自動機器學習實驗](tutorial-first-experiment-automated-ml.md)的工作室版本。  有關詳細資訊和定價資訊，請參閱[Azure 機器學習定價](https://azure.microsoft.com/pricing/details/machine-learning/)。

創建工作區的方法有多種：  

* 使用[Azure 門戶](how-to-manage-workspace.md)進行點擊和按一下介面，引導您完成每個步驟。
* 使用[Python 的 Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace)從 Python 腳本或木星筆記本動態創建工作區
* 當您需要使用公司安全標準自動或自訂創建時，請使用[Azure 資源管理器範本](how-to-create-workspace-template.md)或[Azure 機器學習 CLI。](reference-azure-machine-learning-cli.md)
* 如果您在視覺化工作室代碼中工作，請使用[VS 代碼擴展](tutorial-setup-vscode-extension.md)名 。

> [!NOTE]
> 工作區名稱不區分大小寫。

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>升級到企業版

您可以使用 Azure 門戶[將工作區從"基本"版升級到企業版](how-to-manage-workspace.md#upgrade)。 不能將企業版工作區降級為基本版工作區。 

## <a name="associated-resources"></a><a name="resources"></a>關聯資源

建立新的工作區時，會自動建立工作區使用的幾個 Azure 資源：

+ [Azure 容器註冊表](https://azure.microsoft.com/services/container-registry/)：註冊在訓練期間和部署模型時使用的 Docker 容器。 為了將成本降至最低，在創建部署映射之前，ACR 會**延遲載入**。
+ [Azure 存儲帳戶](https://azure.microsoft.com/services/storage/)：用作工作區的預設資料存儲。  與 Azure 機器學習計算實例一起使用的 Jupyter 筆記本也存儲在此處。
+ [Azure 應用程式見解](https://azure.microsoft.com/services/application-insights/)：存儲有關模型的監視資訊。
+ [Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/)：存儲計算目標使用的秘密以及工作區需要的其他敏感資訊。

> [!NOTE]
> 除了建立新的版本之外，您也可以使用現有的 Azure 服務。

## <a name="next-steps"></a>後續步驟

要開始使用 Azure 機器學習，請參閱：

+ [Azure 機器學習概述](overview-what-is-azure-ml.md)
+ [創建工作區](how-to-manage-workspace.md)
+ [管理工作區](how-to-manage-workspace.md)
+ [教程：開始使用 Python SDK 創建第一個 ML 實驗](tutorial-1st-experiment-sdk-setup.md)
+ [教程：使用 R SDK 開始使用 Azure 機器學習](tutorial-1st-r-experiment.md)
+ [教程：使用自動機器學習創建第一個分類模型](tutorial-first-experiment-automated-ml.md)（僅適用于[企業版](overview-what-is-azure-ml.md#sku)工作區）
+ [教程：與設計師一起預測汽車價格](tutorial-designer-automobile-price-train-score.md)（僅適用于[企業版](overview-what-is-azure-ml.md#sku)工作區）
