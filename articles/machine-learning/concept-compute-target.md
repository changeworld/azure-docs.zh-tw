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
ms.date: 07/27/2020
ms.openlocfilehash: 27c129af9fbf3e76c6c57fbf084596876b51955b
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141920"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Azure Machine Learning 中的計算目標是什麼？ 

**計算目標**是指定的計算資源/環境，您可以在其中執行定型腳本或裝載您的服務部署。 這個位置可能是您的本機電腦或雲端式計算資源。 使用計算目標可讓您更輕鬆地在稍後變更計算環境，而不需要變更您的程式碼。  

在典型的模型開發生命週期中，您可能會：
1. 從開發和實驗少量的資料開始。 在此階段中，我們建議您的本機環境 (本機電腦或雲端式 VM) 作為計算目標。 
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
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning 計算 (受控) 

受管理的計算資源是由 Azure Machine Learning 來建立和管理。 此計算已針對機器學習工作負載進行優化。 Azure Machine Learning 計算叢集和[計算實例](concept-compute-instance.md)是唯一的受控計算。 未來可能會新增其他受控計算資源。

您可以從下列來源建立 Azure Machine Learning 計算實例或計算叢集：
* Azure Machine Learning Studio
* Azure 入口網站
* Python SDK [ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py)和[AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py)類別
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (預覽) 
* Resource Manager 範本。 如需範例範本，請參閱[建立 Azure Machine Learning 計算範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute)。
* [Azure CLI 的](reference-azure-machine-learning-cli.md#resource-management)機器學習服務延伸模組。  

建立這些計算資源時，會自動成為您工作區的一部分，而不像其他類型的計算目標。


|功能  |計算叢集  |計算執行個體  |
|---------|---------|---------|
|單一或多節點叢集     |    **&check;**       |         |
|每次提交執行時自動調整     |     **&check;**      |         |
|自動叢集管理和作業排程     |   **&check;**        |     **&check;**      |
|同時支援 CPU 和 GPU 資源     |  **&check;**         |    **&check;**       |


> [!NOTE]
> 當計算叢集閒置時，它會自動調整為0個節點，因此您不需支付其使用時間。  不過，計算*實例*一律為開啟，且不會自動調整。  當您未使用[計算實例](tutorial-1st-experiment-sdk-train.md#stop-the-compute-instance)來避免產生額外成本時，您應該將它停止。

### <a name="supported-vm-series-and-sizes"></a>支援的 VM 系列和大小

當您在 Azure Machine Learning 中選取受控計算資源的節點大小時，您可以從 Azure 中可用的選取 VM 大小中選擇。 Azure 針對不同的工作負載提供適用于 Linux 和 Windows 的各種大小。 請參閱這裡以深入瞭解不同的[VM 類型和大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)。

選擇 VM 大小時有幾個例外狀況和限制：
* Azure Machine Learning 中不支援某些 VM 系列。
* 有些 VM 系列受到限制。 若要使用受限制的系列，請聯絡支援人員，並要求增加此系列的配額。 如需有關聯繫支援的詳細資訊，請參閱[Azure 支援選項](https://azure.microsoft.com/support/options/)

若要深入瞭解支援的系列和限制，請參閱下表。 

| **支援的 VM 系列**  | **限制** |
|------------|------------|
| D | None |
| Dv2 | None |  
| DSv2 | None |  
| FSv2 | None | 
| HBv2 | 需要核准 |  
| HCS | 需要核准 |  
| M | 需要核准 |
| NC | None |    
| NCsv2 | 需要核准 |
| NCsv3 | 需要核准 |  
| NDs | 需要核准 |
| NDv2 | 需要核准 |
| NV | None |
| NVv3 | 需要核准 | 


雖然 Azure Machine Learning 支援這些 VM 系列，但它們可能無法在所有 Azure 區域中使用。 您可以在這裡查看是否有可用的 VM 系列：[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)。

## <a name="unmanaged-compute"></a>非受控計算

非受控計算目標*不*受 Azure Machine Learning 管理。 您會在 Azure Machine Learning 外部建立這種類型的計算目標，然後將它附加至您的工作區。 非受控計算資源可能需要額外的步驟，才能維護或改善機器學習工作負載的效能。

## <a name="next-steps"></a>後續步驟

了解如何：
* [設定計算目標以將模型定型](how-to-set-up-training-targets.md)
* [將您的模型部署到計算目標](how-to-deploy-and-where.md)
