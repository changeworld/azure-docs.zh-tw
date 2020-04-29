---
title: 什麼是計算目標
titleSuffix: Azure Machine Learning
description: 定義您想要使用 Azure Machine Learning 定型或部署模型的位置。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 03/30/2020
ms.openlocfilehash: f9ca75943eaec2ae018b54145d872fc09294035e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80398189"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Azure Machine Learning 中的計算目標是什麼？ 

**計算目標**是指定的計算資源/環境，您可以在其中執行定型腳本或裝載您的服務部署。 此位置可能是您的本機電腦或雲端式計算資源。 使用計算目標可讓您更輕鬆地在稍後變更計算環境，而不需要變更您的程式碼。  

在典型的模型開發生命週期中，您可能會：
1. 從開發和實驗少量的資料開始。 在這個階段，我們建議您的本機環境（本機電腦或雲端式 VM）做為您的計算目標。 
2. 相應增加至較大的資料，或使用其中一個[訓練計算目標](#train)進行分散式訓練。  
3. 當您的模型就緒之後，請將它部署到具有其中一個[部署計算目標](#deploy)的 web 主控環境或 IoT 裝置。

您用於計算目標的計算資源會附加至[工作區](concept-workspace.md)。 工作區的使用者會共用本機電腦以外的計算資源。

## <a name="training-compute-targets"></a><a name="train"></a>訓練計算目標

Azure Machine Learning 在不同的計算資源上有不同的支援。  您也可以附加自己的計算資源，但支援不同的案例可能會有所不同。

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

深入瞭解如何[設定及使用計算目標來進行模型定型](how-to-set-up-training-targets.md)。

## <a name="deployment-targets"></a><a name="deploy"></a>部署目標

下列計算資源可以用來裝載您的模型部署。

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

瞭解[將模型部署至計算目標的位置和方式](how-to-deploy-and-where.md)。

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning 計算（受控）

受管理的計算資源是由 Azure Machine Learning 來建立和管理。 此計算已針對機器學習工作負載進行優化。 Azure Machine Learning 計算叢集和[計算實例](concept-compute-instance.md)是唯一的受控計算。 未來可能會新增其他受控計算資源。

您可以從下列來源建立 Azure Machine Learning 計算實例（預覽）或計算叢集：
* Azure Machine Learning Studio
* Azure 入口網站
* Python SDK [ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py)和[AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py)類別
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets)
* Resource Manager 範本

您也可以使用適用于 Azure CLI 的[機器學習擴充](tutorial-train-deploy-model-cli.md#create-the-compute-target-for-training)功能來建立計算叢集。

建立這些計算資源時，會自動成為您工作區的一部分，而不像其他類型的計算目標。

### <a name="compute-clusters"></a>計算叢集

您可以使用 Azure Machine Learning 計算叢集來進行定型和批次推斷（預覽）。  使用此計算資源，您可以：

* 單一或多節點叢集
* 每次提交執行時自動調整 
* 自動叢集管理和作業排程 
* 同時支援 CPU 和 GPU 資源



## <a name="unmanaged-compute"></a>非受控計算

非受控計算目標*不*受 Azure Machine Learning 管理。 您會在 Azure Machine Learning 外部建立這種類型的計算目標，然後將它附加至您的工作區。 非受控計算資源可能需要額外的步驟，才能維護或改善機器學習工作負載的效能。

## <a name="next-steps"></a>後續步驟

了解如何：
* [設定計算目標以將模型定型](how-to-set-up-training-targets.md)
* [將您的模型部署到計算目標](how-to-deploy-and-where.md)