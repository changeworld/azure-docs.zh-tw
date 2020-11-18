---
title: 什麼是工作區
titleSuffix: Azure Machine Learning
description: 工作區是 Azure Machine Learning 的最上層資源。 它會保留所有定型執行的歷程記錄、記錄、計量、輸出，以及腳本的快照。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/22/2020
ms.openlocfilehash: 7acb555ddde20d6512119af85a8db055bcd9eeed
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701648"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>什麼是 Azure Machine Learning 工作區？

工作區是 Azure Machine Learning 的最上層資源，可提供集中的位置來處理您使用 Azure Machine Learning 時所建立的所有成品。  工作區會保留所有定型執行的歷程記錄，包括記錄、計量、輸出，以及腳本的快照。 您可以使用此資訊來判斷哪一個定型回合會產生最佳模型。  

當您有想要的模型之後，您可以向工作區註冊該模型。 然後，您可以使用已註冊的模型和評分腳本來部署至 Azure 容器實例、Azure Kubernetes Service，或部署至可現場程式化閘道陣列， (FPGA) 作為 REST 型 HTTP 端點。 您也可以將模型部署到 Azure IoT Edge 裝置作為模組。

## <a name="taxonomy"></a>分類法 

下圖說明工作區的分類：

[![工作區分類法](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

下圖顯示工作區的下列元件：

+ 工作區可以包含 [Azure Machine Learning 計算實例](concept-compute-instance.md)、使用執行 Azure Machine Learning 所需的 Python 環境設定的雲端資源。

+ [使用者角色](how-to-assign-roles.md) 可讓您與其他使用者、小組或專案共用您的工作區。
+ [計算目標](concept-azure-machine-learning-architecture.md#compute-targets) 是用來執行您的實驗。
+ 當您建立工作區時，也會為您建立 [相關聯的資源](#resources) 。
+ [實驗](concept-azure-machine-learning-architecture.md#experiments) 是您用來建立模型的定型回合。  
+ [管線](concept-azure-machine-learning-architecture.md#ml-pipelines) 是可重複使用的工作流程，用於定型和重新定型您的模型。
+ 資料[集](concept-azure-machine-learning-architecture.md#datasets-and-datastores)可協助您管理用於模型定型和管線建立的資料。
+ 當您擁有想要部署的模型之後，就可以建立已註冊的模型。
+ 使用已註冊的模型和評分腳本來建立 [部署端點](concept-azure-machine-learning-architecture.md#endpoints)。

## <a name="tools-for-workspace-interaction"></a>工作區互動的工具

您可以透過下列方式與您的工作區互動：

> [!IMPORTANT]
> 以下是標示 (preview) 的工具目前處於公開預覽狀態。
> 此預覽版本會在沒有服務等級協定的情況下提供，不建議用於實際執行工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

+ 在網站上：
    + [Azure Machine Learning studio ](https://ml.azure.com) 
    + [Azure Machine Learning 設計工具](concept-designer.md) 
+ 在任何具有 [適用于 python 之 AZURE MACHINE LEARNING SDK 的](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)Python 環境中。
+ 在任何具有 [適用于 r 的 AZURE MACHINE LEARNING SDK (preview) ](https://azure.github.io/azureml-sdk-for-r/reference/index.html)的 r 環境中。
+ 在命令列上使用 Azure Machine Learning [CLI 擴充](./reference-azure-machine-learning-cli.md)功能
+ [Azure Machine Learning VS Code 延伸模組](how-to-manage-resources-vscode.md#workspaces)


## <a name="machine-learning-with-a-workspace"></a>使用工作區進行機器學習

機器學習工作會讀取及/或寫入構件至您的工作區。

+ 執行實驗來定型模型-將實驗執行結果定型至工作區。
+ 使用自動化 ML 將模型寫入定型結果定型至工作區。
+ 在工作區中註冊模型。
+ 部署模型-使用已註冊的模型來建立部署。
+ 建立並執行可重複使用的工作流程。
+ 查看機器學習構件，例如實驗、管線、模型、部署。
+ 追蹤和監視模型。

## <a name="workspace-management"></a>工作區管理

您也可以執行下列工作區管理工作：

| 工作區管理工作   | 入口網站              | Studio | Python SDK/R SDK       | CLI        | VS 程式碼
|---------------------------|---------|---------|------------|------------|------------|
| 建立工作區        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| 管理工作區存取    | **&check;**   || |  **&check;**    ||
| 建立及管理計算資源    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| 建立筆記本 VM |   | **&check;** | |     ||

> [!WARNING]
> 不支援將 Azure Machine Learning 工作區移至不同的訂用帳戶，或將擁有的訂用帳戶移至新租用戶。 這麼做可能會導致錯誤。

## <a name="create-a-workspace"></a><a name='create-workspace'></a> 建立工作區

有多種方式可建立工作區：  

* 使用點按一下介面的 [Azure 入口網站](how-to-manage-workspace.md?tabs=azure-portal#create-a-workspace) ，逐步引導您完成每個步驟。
* 使用 [適用于 python 的 AZURE MACHINE LEARNING SDK](how-to-manage-workspace.md?tabs=python#create-a-workspace) ，從 Python 腳本或 Jupyter 筆記本即時建立工作區
* 當您需要使用企業安全性標準來自動化或自訂建立時，請使用 [Azure Resource Manager 範本](how-to-create-workspace-template.md) 或 [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) 。
* 如果您在 Visual Studio Code 中工作，請使用 [VS Code 擴充](how-to-manage-resources-vscode.md#create-a-workspace)功能。

> [!NOTE]
> 工作區名稱不區分大小寫。

## <a name="associated-resources"></a><a name="resources"></a> 相關聯的資源

建立新的工作區時，會自動建立工作區使用的幾個 Azure 資源：

+ [Azure 儲存體帳戶](https://azure.microsoft.com/services/storage/)：作為工作區的預設資料存放區使用。  Jupyter 與 Azure Machine Learning 計算實例搭配使用的筆記本也會儲存在這裡。 
  
  > [!IMPORTANT]
  > 根據預設，儲存體帳戶是一般用途 v1 帳戶。 建立工作區之後，您可以 [將此升級為一般用途 v2](../storage/common/storage-account-upgrade.md) 。 升級至一般用途 v2 之後，請勿在儲存體帳戶上啟用階層式命名空間。

  若要使用現有的 Azure 儲存體帳戶，它不能是 premium 帳戶 (Premium_LRS 和 Premium_GRS) 。 它也不能有階層命名空間 (用於 Azure Data Lake Storage Gen2) 。 工作區的 _預設_ 儲存體帳戶不支援 premium 儲存體或階層命名空間。 您可以搭配使用 premium 儲存體或階層命名空間與 _非預設_ 儲存體帳戶。
  
+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)：註冊您在定型期間以及在部署模型時使用的 docker 容器。 為了將成本降至最低，ACR 會 **延遲載入** ，直到建立部署映射為止。

+ [Azure 應用程式見解](https://azure.microsoft.com/services/application-insights/)：儲存有關模型的監視資訊。

+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)：儲存計算目標所使用的秘密，以及工作區所需的其他機密資訊。

> [!NOTE]
> 當您使用 [PYTHON SDK](how-to-manage-workspace.md?tabs=python#create-a-workspace)、 [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/create_workspace.html)或 [使用 ARM 範本](how-to-create-workspace-template.md)的 Azure Machine Learning CLI 建立工作區時，可以改為使用現有的 Azure 資源實例。

<a name="wheres-enterprise"></a>

## <a name="what-happened-to-enterprise-edition"></a>Enterprise edition 發生什麼事

從2020年9月開始，企業版工作區中提供的所有功能現在也可在基本版的工作區中使用。 無法再建立新的企業工作區。  使用參數的任何 SDK、CLI 或 Azure Resource Manager 呼叫 `sku` 都將繼續運作，但將會布建基本工作區。

自12月21日起，所有企業版工作區將會自動設定為基本版，其具有相同的功能。 在此過程中不會發生停機。 在2021年1月1日，企業版將正式淘汰。 

在這兩個版本中，客戶須負責使用 Azure 資源的費用，而且不需要支付任何額外的 Azure Machine Learning 費用。 如需詳細資訊，請參閱 [Azure Machine Learning 定價頁面](https://azure.microsoft.com/pricing/details/machine-learning/) 。

## <a name="next-steps"></a>後續步驟

若要開始使用 Azure Machine Learning，請參閱：

+ [Azure Machine Learning 總覽](overview-what-is-azure-ml.md)
+ [建立及管理工作區](how-to-manage-workspace.md)
+ [教學課程：開始在開發環境中使用 Azure Machine Learning](tutorial-1st-experiment-sdk-setup-local.md)
+ [教學課程：開始在計算實例上建立您的第一個 ML 實驗](tutorial-1st-experiment-sdk-setup.md)
+ [教學課程：開始使用 R SDK 的 Azure Machine Learning](tutorial-1st-r-experiment.md)
+ [教學課程：使用自動化機器學習建立第一個分類模型](tutorial-first-experiment-automated-ml.md) 
+ [教學課程：使用設計工具預測汽車價格](tutorial-designer-automobile-price-train-score.md)
