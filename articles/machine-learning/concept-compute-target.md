---
title: 什麼是計算目標
titleSuffix: Azure Machine Learning
description: 使用 Azure Machine Learning 定義您想要定型或部署模型的位置。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/27/2020
ms.openlocfilehash: 3e1832d656ea2bad772a686e52a357c1daf0ded5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90883207"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Azure Machine Learning 中的計算目標是什麼？ 

**計算目標**是您執行定型腳本或裝載服務部署的指定計算資源/環境。 這個位置可能是您的本機電腦或雲端式計算資源。 使用計算目標可讓您輕鬆地在稍後變更您的計算環境，而不需要變更您的程式碼。  

在一般模型開發生命週期中，您可能會：
1. 從開發和試驗少量的資料開始著手。 在這個階段，我們建議您的本機環境 (本機電腦或雲端式 VM) 作為計算目標。 
2. 擴大至較大的資料，或使用其中一個 [訓練計算目標](#train)進行分散式訓練。  
3. 當您的模型就緒後，請將其部署至具有其中一個 [部署計算目標](#deploy)的 web 裝載環境或 IoT 裝置。

您用於計算目標的計算資源會附加至 [工作區](concept-workspace.md)。 本機電腦以外的計算資源會由工作區的使用者共用。

## <a name="training-compute-targets"></a><a name="train"></a> 訓練計算目標

Azure Machine Learning 在不同的計算資源之間有不同的支援。  您也可以附加自己的計算資源，但支援各種案例可能會有所不同。

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

深入瞭解如何 [使用計算目標進行模型定型](how-to-set-up-training-targets.md)。

## <a name="deployment-targets"></a><a name="deploy"></a>部署目標

您可以使用下列計算資源來裝載您的模型部署。

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

瞭解 [將模型部署至計算目標的位置和方式](how-to-deploy-and-where.md)。

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning 計算 (受控) 

受控計算資源是由 Azure Machine Learning 所建立和管理。 此計算已針對機器學習工作負載進行優化。 Azure Machine Learning 計算叢集和 [計算實例](concept-compute-instance.md) 是唯一的受控計算。 

您可以從下列來源建立 Azure Machine Learning 計算實例或計算叢集：
* [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md)
* Azure 入口網站
* Python SDK [ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py&preserve-view=true) 和 [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py&preserve-view=true) 類別
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (預覽) 
* Resource Manager 範本。 如需範例範本，請參閱 [建立 Azure Machine Learning 計算範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute)。
* [Azure CLI 的](reference-azure-machine-learning-cli.md#resource-management)機器學習擴充功能。  

建立這些計算資源時，會自動成為工作區的一部分，與其他類型的計算目標不同。


|功能  |計算叢集  |計算執行個體  |
|---------|---------|---------|
|單一或多節點叢集     |    **&check;**       |         |
|每次提交執行時自動調整     |     **&check;**      |         |
|自動叢集管理和作業排程     |   **&check;**        |     **&check;**      |
|同時支援 CPU 和 GPU 資源     |  **&check;**         |    **&check;**       |


> [!NOTE]
> 當計算叢集閒置時，它會自動調整至0個節點，所以您不需支付未使用的費用。  不過，計算 *實例*一律為開啟，且不會自動調整。  當您未使用 [計算實例](concept-compute-instance.md#managing-a-compute-instance) 時，應該將其停止，以避免產生額外的成本。 

### <a name="supported-vm-series-and-sizes"></a>支援的 VM 系列和大小

當您在 Azure Machine Learning 中為受控計算資源選取節點大小時，您可以從 Azure 提供的選取 VM 大小中選擇。 Azure 針對不同的工作負載提供適用于 Linux 和 Windows 的各種大小。 請參閱這裡以深入瞭解不同的 [VM 類型和大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)。

選擇 VM 大小時有幾個例外狀況和限制：
* Azure Machine Learning 中不支援某些 VM 系列。
* 部分 VM 系列受限制。 若要使用受限制的系列，請聯絡支援人員，並要求增加此系列的配額。 如需聯絡支援的相關資訊，請參閱 [Azure 支援選項](https://azure.microsoft.com/support/options/)

若要深入瞭解支援的系列和限制，請參閱下表。 

| **支援的 VM 系列**  | **限制** |
|------------|------------|
| D | 無 |
| Dv2 | 無 |  
| DSv2 | 無 |  
| FSv2 | 無 | 
| HBv2 | 需要核准 |  
| HCS | 需要核准 |  
| M | 需要核准 |
| NC | 無 |    
| NCsv2 | 需要核准 |
| NCsv3 | 需要核准 |  
| NDs | 需要核准 |
| NDv2 | 需要核准 |
| NV | 無 |
| NVv3 | 需要核准 | 


雖然 Azure Machine Learning 支援這些 VM 系列，但它們可能無法在所有 Azure 區域中使用。 您可以在這裡查看可用的 VM 系列： [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)。

## <a name="unmanaged-compute"></a>非受控計算

未受管理的計算目標 *不* 受 Azure Machine Learning 管理。 您可以在 Azure Machine Learning 之外建立這種類型的計算目標，然後將其附加至您的工作區。 非受控計算資源可能需要額外的步驟，以維護或改善機器學習工作負載的效能。

## <a name="next-steps"></a>後續步驟

了解如何：
* [使用計算目標來定型您的模型](how-to-set-up-training-targets.md)
* [將您的模型部署至計算目標](how-to-deploy-and-where.md)
