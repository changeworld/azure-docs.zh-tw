---
title: 什麼是計算目標
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 來指定計算資源或環境，以定型或部署您的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/29/2020
ms.openlocfilehash: 2a19183f5ba5b1ea4d50cd5cfba6115f7dede6cd
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540041"
---
# <a name="what-are-compute-targets-in-azure-machine-learning"></a>Azure Machine Learning 中的計算目標是什麼？

*計算目標* 是您執行定型腳本或裝載服務部署的指定計算資源或環境。 這個位置可能是您的本機電腦或雲端式計算資源。 使用計算目標可讓您輕鬆地在稍後變更您的計算環境，而不需要變更您的程式碼。

在一般模型開發生命週期中，您可能會：

1. 從開發和試驗少量的資料開始著手。 在這個階段中，請使用您的本機環境，例如本機電腦或雲端式虛擬機器 (VM) ，作為您的計算目標。
1. 擴大為較大的資料，或使用其中一種 [定型計算目標](#train)進行分散式訓練。
1. 當您的模型就緒後，請將其部署至具有其中一個 [部署計算目標](#deploy)的 web 裝載環境或 IoT 裝置。

您用於計算目標的計算資源會附加至 [工作區](concept-workspace.md)。 本機電腦以外的計算資源會由工作區的使用者共用。

## <a name="training-compute-targets"></a><a name="train"></a> 訓練計算目標

Azure Machine Learning 在不同計算目標上提供不同的支援。 一般模型開發生命週期是從開發或實驗少量的資料開始。 在這個階段中，請使用本機環境，例如您的本機電腦或雲端式 VM。 當您在較大型的資料集上擴大訓練或執行分散式訓練時，請使用 Azure Machine Learning 計算來建立單一或多節點的叢集，以在每次提交執行時自動調整。 您也可以附加自己的計算資源，但不同案例的支援可能會有所不同。

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

深入瞭解如何將 [訓練回合提交至計算目標](how-to-set-up-training-targets.md)。

## <a name="compute-targets-for-inference"></a><a name="deploy"></a> 推斷的計算目標

您可以使用下列計算資源來裝載您的模型部署。

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

執行推斷時，Azure Machine Learning 會建立 Docker 容器來裝載模型，以及使用它所需的相關聯資源。 然後，此容器會在下列其中一個部署案例中使用：

* 作為用於即時推斷的 *web 服務* 。 Web 服務部署會使用下列其中一個計算目標：

    * [本機電腦](how-to-attach-compute-targets.md#local)
    * [Azure Machine Learning 計算執行個體](how-to-create-manage-compute-instance.md)
    * [Azure 容器執行個體](how-to-attach-compute-targets.md#aci)
    * [Azure Kubernetes Service](how-to-create-attach-kubernetes.md)
    * Azure Functions (preview) 。 部署至函式只依賴 Azure Machine Learning 來建立 Docker 容器。 從該處，它是使用函數進行部署。 如需詳細資訊，請參閱 [將機器學習模型部署至 Azure Functions (preview) ](how-to-deploy-functions.md)。

* 作為用來定期處理資料批次的 _批次推斷_ 端點。 Batch 推斷會使用 [Azure Machine Learning 計算](how-to-create-attach-compute-cluster.md)叢集。

* _IoT 裝置_ (預覽) 。 部署至 IoT 裝置只依賴 Azure Machine Learning 來建立 Docker 容器。 從該處，它是使用 Azure IoT Edge 來部署。 如需詳細資訊，請參閱 [ (preview) 部署為 IoT Edge 模組 ](../iot-edge/tutorial-deploy-machine-learning.md)。

瞭解 [將模型部署至計算目標的位置和方式](how-to-deploy-and-where.md)。

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning 計算 (受控) 

受控計算資源是由 Azure Machine Learning 所建立和管理。 此計算已針對機器學習工作負載進行優化。 Azure Machine Learning 計算叢集和 [計算實例](concept-compute-instance.md) 是唯一的受控計算。

您可以從下列來源建立 Azure Machine Learning 計算實例或計算叢集：

* [Azure Machine Learning studio](how-to-create-attach-compute-studio.md)。
* Python SDK 和 CLI：
    * [計算實例](how-to-create-manage-compute-instance.md)。
    * [計算](how-to-create-attach-compute-cluster.md)叢集。
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (preview) 。
* Azure Resource Manager 範本。 如需範例範本，請參閱 [建立 Azure Machine Learning 計算](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute)叢集。
* Azure CLI 的機器學習 [擴充](reference-azure-machine-learning-cli.md#resource-management)功能。

建立時，這些計算資源會自動納入您的工作區，而不像其他類型的計算目標。


|功能  |計算叢集  |計算執行個體  |
|---------|---------|---------|
|單一或多節點叢集     |    **&check;**       |         |
|每次提交執行時自動調整     |     **&check;**      |         |
|自動叢集管理和作業排程     |   **&check;**        |     **&check;**      |
|同時支援 CPU 和 GPU 資源     |  **&check;**         |    **&check;**       |


> [!NOTE]
> 當計算叢集閒置時， *它會自動調整* 至0個節點，所以您不需支付未使用的費用。 計算 *實例* 一律為開啟，且不會自動調整。 當您未使用 [計算實例](how-to-create-manage-compute-instance.md#manage) 時，您應該將其停止，以避免產生額外的成本。

### <a name="supported-vm-series-and-sizes"></a>支援的 VM 系列和大小

當您在 Azure Machine Learning 中為受控計算資源選取節點大小時，您可以從 Azure 提供的選取 VM 大小中選擇。 Azure 針對不同的工作負載提供適用于 Linux 和 Windows 的各種大小。 若要深入瞭解，請參閱 [VM 類型和大小](../virtual-machines/sizes.md)。

選擇 VM 大小時有幾個例外狀況和限制：

* Azure Machine Learning 中不支援某些 VM 系列。
* 部分 VM 系列受限制。 若要使用受限制的系列，請聯絡支援人員，並要求增加此系列的配額。 如需有關如何聯繫支援的詳細資訊，請參閱 [Azure 支援選項](https://azure.microsoft.com/support/options/)。

若要深入瞭解支援的系列和限制，請參閱下表。

| **支援的 VM 系列**  | **限制** |
|------------|------------|
| D | 無。 |
| Dv2 | 無。 |  
| Dv3 | 無。|
| DSv2 | 無。 | 
| DSv3 | 無。|
| FSv2 | 無。 | 
| HBv2 | 需要核准。 |  
| HCS | 需要核准。 |  
| M | 需要核准。 |
| NC | 無。 |    
| NCsv2 | 需要核准。 |
| NCsv3 | 需要核准。 |  
| NDs | 需要核准。 |
| NDv2 | 需要核准。 |
| NV | 無。 |
| NVv3 | 需要核准。 | 


雖然 Azure Machine Learning 支援這些 VM 系列，但它們可能無法在所有 Azure 區域中使用。 若要檢查是否有可用的 VM 系列，請參閱 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)。

### <a name="compute-isolation"></a>計算隔離

Azure Machine Learning 計算提供隔離于特定硬體類型的 VM 大小，而且專用於單一客戶。 隔離的 VM 大小最適合需要與其他客戶工作負載高度隔離的工作負載，原因包括符合規範和法規需求。 使用隔離大小可保證您的 VM 將會是在該特定伺服器實例上執行的唯一一種。

目前隔離的 VM 供應專案包括：

* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3*

*支援 RDMA

若要深入瞭解隔離，請參閱 [Azure 公用雲端中的隔離](../security/fundamentals/isolation-choices.md)。

## <a name="unmanaged-compute"></a>非受控計算

未受管理的計算目標 *不* 受 Azure Machine Learning 管理。 您可以在 Azure Machine Learning 外部建立這種類型的計算目標，然後將其附加至您的工作區。 非受控計算資源可能需要額外的步驟，以維護或改善機器學習工作負載的效能。

## <a name="next-steps"></a>後續步驟

了解如何：
* [使用計算目標來定型您的模型](how-to-set-up-training-targets.md)
* [將您的模型部署至計算目標](how-to-deploy-and-where.md)