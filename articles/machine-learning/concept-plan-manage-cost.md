---
title: 規劃和管理成本
titleSuffix: Azure Machine Learning
description: 使用 Azure 入口網站中的成本分析來規劃和管理 Azure Machine Learning 的成本。 當您建立機器學習模型時，深入瞭解節省成本的秘訣，以降低成本。
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: ae1beeebfddfe250ae20a70c3e78ec32774218d4
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996330"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>規劃和管理 Azure Machine Learning 的成本

本文說明如何規劃和管理 Azure Machine Learning 的成本。 首先，您會使用 Azure 定價計算機來協助規劃成本，然後再新增任何資源。 接下來，當您新增 Azure 資源時，請檢查預估成本。 最後，當您使用受控 Azure Machine Learning 計算叢集來訓練模型時，請使用節省成本的秘訣。

在您開始使用 Azure Machine Learning 資源之後，請使用成本管理功能來設定預算和監視成本。 也請檢查預測的成本，並找出支出趨勢，以識別您可能想要採取行動的區域。

瞭解 Azure Machine Learning 的成本只是 Azure 帳單中每月成本的一部分。 如果您使用其他 Azure 服務，則會向您收取 Azure 訂用帳戶中所使用的所有 Azure 服務和資源的費用，包括協力廠商服務。 本文說明如何規劃和管理 Azure Machine Learning 的成本。 在您熟悉如何管理 Azure Machine Learning 的成本之後，請套用類似的方法來管理訂用帳戶中所使用之所有 Azure 服務的成本。

當您將機器學習模型定型時，請使用受控 Azure Machine Learning 計算叢集，以利用更節省成本的秘訣：

* 設定您的定型叢集以進行自動調整
* 設定訂用帳戶和工作區的配額
* 在定型回合上設定終止原則
* 使用低優先順序的虛擬機器（VM）
* 使用 Azure 保留的 VM 實例

## <a name="prerequisites"></a>Prerequisites

成本分析支援不同的 Azure 帳戶類型。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](../cost-management-billing/costs/understand-cost-mgt-data.md)。 若要檢視成本資料，您至少需要 Azure 帳戶的讀取存取。 

如需 Azure 成本管理資料的存取權指派相關資訊，請參閱[指派資料的存取權](../cost-management-billing/costs/assign-access-acm-data.md)。

## <a name="estimate-costs"></a>預估成本

使用[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)來預估成本，再于 Azure Machine Learning 帳戶中建立資源。 在左側選取 [ **AI + Machine Learning**]，然後選取 [ **Azure Machine Learning**以開始。  

下列螢幕擷取畫面顯示使用計算機的成本估計：

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure 計算機中的成本預估":::

當您將新資源新增至工作區時，請返回此計算機並在此新增相同的資源，以更新您的成本預估值。

雖然 Enterprise edition 處於預覽階段，但沒有任何 ML 費用。 當 Enterprise edition 正式推出時，將會有額外費用（用於定型和推斷）。  如需詳細資訊，請參閱[Azure Machine Learning 定價](https://azure.microsoft.com/pricing/details/machine-learning/)。

## <a name="get-cost-alerts"></a>取得成本警示

建立[預算](../cost-management/tutorial-acm-create-budgets.md)來管理成本，並建立[警示](../cost-management/cost-mgt-alerts-monitor-usage-spending.md)，以自動通知專案關係人如何耗費異常和超支風險。 警示是以支出為基礎 (相較於預算和成本閾值)。 預算和警示是針對 Azure 訂用帳戶和資源群組所建立，因此在整體成本監視策略中相當有用。 不過，預算和警示的功能可能有限，無法管理個別的 Azure 服務成本，因為它們是設計來追蹤較高層級的成本。

## <a name="monitor-costs"></a>監視成本

當您將資源與 Azure Machine Learning 搭配使用時，會產生成本。 Azure 資源使用量單位成本會因時間間隔（秒、分鐘、小時、天）或要求單位使用量而異。 一旦開始使用 Azure Machine Learning，就會產生成本。 在 Azure 入口網站的 [[成本分析](../cost-management/quick-acm-cost-analysis.md)] 窗格中，查看這些成本。

您可以在圖表和資料表中，以不同的時間間隔來查看成本。 您也可以根據預算和預測成本來查看成本。 隨著時間切換到較長的 views 有助於識別支出趨勢，並查看超支可能發生的位置。 如果您已建立預算，請參閱其超過的位置。  

您不會看到 Machine Learning 的個別服務區域。  相反地，您會看到已新增至 Machine Learning 工作區的各種資源。

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>使用 Azure Machine Learning 計算叢集（AmlCompute）

有了不斷變化的資料，您需要快速且有效率的模型定型和重新訓練，才能維護正確的模型。 不過，持續訓練會有成本，特別是針對 Gpu 的深度學習模型。 

Azure Machine Learning 使用者可以使用受控 Azure Machine Learning 計算叢集，也稱為 AmlCompute。 AmlCompute 支援各種 GPU 和 CPU 選項。 AmlCompute 是由 Azure Machine Learning，代表您的訂用帳戶裝載于內部。 它在 Azure IaaS 雲端規模上提供相同的企業級安全性、合規性和治理。

因為這些計算集區位於 Azure 的 IaaS 基礎結構內，所以您可以使用與基礎結構其餘部分相同的安全性和合規性需求來部署、調整及管理您的訓練。  這些部署會在您的訂用帳戶中進行，並遵守您的治理規則。 深入瞭解[Azure Machine Learning 計算](how-to-set-up-training-targets.md#amlcompute)。

## <a name="configure-training-clusters-for-autoscaling"></a>設定自動調整的定型叢集

根據您的工作負載需求自動調整叢集有助於降低成本，因此您只需要使用所需的資訊。

AmlCompute 叢集是設計來根據您的工作負載動態擴充。 叢集可以相應增加至您設定的節點數目上限。 當每次執行完成時，叢集就會釋放節點，並調整為您設定的最小節點計數。

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

您也可以設定在相應減少之前，節點閒置的時間量。 根據預設，相應減少之前的閒置時間會設定為120秒。

+ 如果您執行較少的反復實驗，請減少這段時間來節省成本。 
+ 如果您執行高度反復的開發/測試實驗，您可能需要增加時間，以便在每次變更訓練腳本或環境之後，不需支付持續的相應縮小和減少。

您可以使用[AMLCOMPUTE SDK 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) [AmlCompute CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)搭配[REST api](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable)，針對 Azure 入口網站中變更的工作負載需求設定 AmlCompute 叢集。

```azure cli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>設定資源的配額

AmlCompute 隨附[配額（或限制）](how-to-manage-quotas.md#azure-machine-learning-compute)設定。 此配額是依 VM 系列（例如，Dv2 系列、NCv3 系列）而有所不同，並依每個訂用帳戶的地區而異。 訂用帳戶會以小型預設值開始進行，但使用此設定可控制可在您的訂用帳戶中啟動的 Amlcompute 資源量。 

也針對訂用帳戶中的每個工作區，設定[VM 系列的工作區層級配額](how-to-manage-quotas.md#workspace-level-quota)。 這麼做可讓您更細微地控制每個工作區可能會產生的成本，並限制特定的 VM 系列。 

若要在工作區層級設定配額，請從[Azure 入口網站](https://portal.azure.com)開始。  選取訂用帳戶中的任何工作區，然後在左窗格中選取 [**使用方式 + 配額**]。 然後選取 [**設定配額**] 索引標籤來查看配額。 您需要訂用帳戶範圍的許可權來設定配額，因為它是會影響多個工作區的設定。

## <a name="set-run-autotermination-policies"></a>設定執行 autotermination 原則 

在某些情況下，您應該將訓練回合設定為限制其持續時間，或提早終止。 例如，當您使用 Azure Machine Learning 的內建超參數微調或自動化機器學習服務時。

以下是一些您可以使用的選項：
* 在您的 RunConfiguration `max_run_duration_seconds`中定義名為的參數，以控制可在您選擇的計算（本機或遠端雲端計算）上擴充執行的最長持續時間。
* 針對[超參數微調](how-to-tune-hyperparameters.md#early-termination)，請從 Bandit 原則、中間值停止原則或截斷選取原則定義提早終止原則。 若要進一步控制超參數掃描，請使用`max_total_runs`或`max_duration_minutes`之類的參數。
* 針對[自動化機器學習](how-to-configure-auto-train.md#exit)，請使用`enable_early_stopping`旗標來設定類似的終止原則。 也可以使用`iteration_timeout_minutes`和`experiment_timeout_minutes`等屬性來控制執行或整個實驗的最長持續時間。

## <a name="use-low-priority-vms"></a>使用低優先順序 VM

Azure 可讓您使用超額的未運用容量作為虛擬機器擴展集、批次和 Machine Learning 服務之間的低優先順序 Vm。 這些配置會預先 emptible，但相較于專用 Vm，其價格會降低。 一般來說，我們建議針對 Batch 工作負載使用低優先順序的 Vm。 您也應該使用它們，透過重新提交（適用于批次推斷）或透過重新開機（適用于使用檢查點的深度學習訓練）來中斷可復原。

低優先順序 Vm 的單一配額會與專用配額值（依 VM 系列而不同）。 深入瞭解[AmlCompute 配額](how-to-manage-quotas.md)。

以下列任何方式設定 VM 的優先順序：

* 在 studio 中，當您建立 VM 時，請選擇 [**低優先順序**]。

* 使用 Python SDK，在您的`vm_priority`布建配置中設定屬性。  

    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                               vm_priority='lowpriority',
                                                               max_nodes=4)
    ```

* 使用 CLI，設定`vm-priority`：

    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 低優先順序的 Vm 不適用於計算實例，因為它們需要支援互動式筆記本體驗。 

## <a name="use-reserved-instances"></a>使用保留執行個體

另一種節省計算資源費用的方法是 Azure 保留的 VM 實例。 在此供應專案中，您承諾使用一年或三年期的條款。 這些折扣的範圍高達72% 的隨用隨付價格，並會直接套用至您的每月 Azure 帳單。

Azure Machine Learning 計算原本就支援保留實例。 如果您購買一年或三年期的保留實例，我們會自動根據您的 Azure Machine Learning 受控計算來套用折扣。


## <a name="next-steps"></a>後續步驟

深入了解：
* [管理及增加資源配額](how-to-manage-quotas.md)
* [使用[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md)來管理成本。
* [Azure Machine Learning 計算](how-to-set-up-training-targets.md#amlcompute)。
