---
title: 規劃和管理成本
titleSuffix: Azure Machine Learning
description: 使用 Azure 入口網站的成本分析來規劃和管理 Azure Machine Learning 成本。 深入瞭解節省成本的秘訣，以在建立 ML 模型時降低成本。
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 5be02ff698dac02d702e47f8929c6f8ddf2adbb7
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602542"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>規劃和管理 Azure Machine Learning 的成本

本文說明如何規劃和管理 Azure Machine Learning 的成本。 首先，您會使用 Azure 定價計算機來協助您在新增任何資源之前規劃成本。 接下來，當您新增 Azure 資源時，請檢查預估成本。 最後，當您使用受控 Azure Machine Learning 計算叢集來定型模型時，請使用節省成本的秘訣。

當您開始使用 Azure Machine Learning 資源之後，請使用成本管理功能來設定預算和監視成本。 此外，也請檢查預測成本，並找出花費趨勢來識別您可能想要採取行動的領域。

瞭解 Azure Machine Learning 的成本只是您 Azure 帳單中的每月成本的一部分。 如果您使用其他 Azure 服務，您必須針對 Azure 訂用帳戶中使用的所有 Azure 服務和資源（包括協力廠商服務）支付費用。 本文說明如何規劃和管理 Azure Machine Learning 的成本。 在您熟悉 Azure Machine Learning 的管理成本之後，請套用類似的方法來管理訂用帳戶中所使用之所有 Azure 服務的成本。

當您將機器學習模型定型時，請使用受控 Azure Machine Learning 計算叢集來利用更多節省成本的秘訣：

* 設定您的定型叢集以進行自動調整
* 在您的訂用帳戶和工作區上設定配額
* 在定型回合上設定終止原則
* 使用低優先順序的虛擬機器 (VM) 
* 使用 Azure 保留的 VM 實例

## <a name="prerequisites"></a>必要條件

成本分析支援不同的 Azure 帳戶類型。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要檢視成本資料，您至少需要 Azure 帳戶的讀取存取。 

如需 Azure 成本管理資料的存取權指派相關資訊，請參閱[指派資料的存取權](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="estimate-costs-before-using-azure-machine-learning"></a>使用 Azure Machine Learning 前先預估成本

在 Azure Machine Learning 帳戶中建立資源之前，請先使用 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 來預估成本。 選取左側的 [ **AI + Machine Learning**，然後選取 **Azure Machine Learning** 以開始。  

下列螢幕擷取畫面顯示使用計算機的成本估計：

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure 計算機中的成本預估":::

當您將新的資源新增至您的工作區時，請返回此計算機並在此新增相同的資源，以更新您的成本預估。

如需詳細資訊，請參閱 [Azure Machine Learning 定價](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>瞭解 Azure Machine Learning 的完整計費模型

Azure Machine Learning 會在 Azure 基礎結構上執行，此基礎結構會在您部署新的資源時，與 Azure Machine Learning 一起計算成本。 請務必瞭解，額外的基礎結構可能會產生成本。 當您對已部署的資源進行變更時，必須管理成本。 

### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>通常會隨 Azure Machine Learning 累積的成本

當您建立 Azure Machine Learning 工作區的資源時，也會建立其他 Azure 服務的資源。 這些包括：

* [Azure Container Registry](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 基本帳戶
* [Azure 區塊 Blob 儲存體](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (一般用途 v1) 
* [金鑰保存庫](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>資源刪除之後可能會累積成本

當您刪除 Azure 入口網站或 Azure CLI 中的 Azure Machine Learning 工作區時，下列資源將會繼續存在。 它們會繼續累積成本，直到您將其刪除為止。

* Azure Container Registry
* Azure 區塊 Blob 儲存體
* Key Vault
* Application Insights

若要刪除工作區以及這些相依的資源，請使用 SDK：

```python
ws.delete(delete_dependent_resources=True)
```

如果您在工作區中建立 Azure Kubernetes Service (AKS) ，或是將任何計算資源附加到您的工作區，則必須在 [Azure 入口網站](https://portal.azure.com)中個別刪除它們。

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>使用 Azure Machine Learning 的 Azure 預付款點數

您可以使用 Azure 預付金支付 Azure Machine Learning 費用 (之前稱為預付金) 點數。 不過，您無法使用 Azure 預付款來支付協力廠商產品和服務（包括來自 Azure Marketplace 的產品）的費用。


## <a name="create-budgets"></a>建立預算

您可以建立 [預算](../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 來管理成本，並建立 [警示](../cost-management/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 來自動通知專案關係人有關消費異常和超支風險。 警示是以支出為基礎 (相較於預算和成本閾值)。 系統會為 Azure 訂用帳戶和資源群組建立預算和警示，使其在整體成本監視策略中很有用。 

如果您想要在監視中有更多細微性，可以使用 Azure 中特定資源或服務的篩選來建立預算。 篩選器可協助確保您不會意外地建立新的資源，而需要支付額外的費用。 如需建立預算時篩選選項的詳細資訊，請參閱 [群組和篩選選項](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>匯出成本資料

您也可以將 [成本資料匯出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 至儲存體帳戶。 當您需要或其他人對成本進行額外的資料分析時，這會很有説明。 例如，財務小組可以使用 Excel 或 Power BI 來分析資料。 您可以根據每日、每週或每月排程來匯出成本，並設定自訂日期範圍。 匯出成本資料集是建議的方式，以取得成本資料集。

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>管理和降低 Azure Machine Learning 成本的其他方式

使用這些秘訣來包含機器學習計算資源的成本。

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>使用 Azure Machine Learning compute cluster (AmlCompute) 

透過不斷變化的資料，您需要快速且簡化的模型定型和重新定型，以維護準確的模型。 不過，持續訓練會產生費用，特別是針對 Gpu 的深度學習模型。 

Azure Machine Learning 使用者可以使用受控 Azure Machine Learning 計算叢集，也稱為 AmlCompute。 AmlCompute 支援各種 GPU 和 CPU 選項。 AmlCompute 由 Azure Machine Learning 在內部裝載，代表您的訂用帳戶。 它提供相同的企業級安全性、合規性和 Azure IaaS 雲端規模的治理。

因為這些計算集區位於 Azure 的 IaaS 基礎結構內，所以您可以使用與其他基礎結構相同的安全性和合規性需求來部署、調整及管理定型。  這些部署會在您的訂用帳戶中進行，並遵守您的治理規則。 深入瞭解 [Azure Machine Learning 計算](how-to-create-attach-compute-cluster.md)。

### <a name="configure-training-clusters-for-autoscaling"></a>設定自動調整的定型群集

以工作負載需求為基礎的自動調整叢集有助於降低成本，因此您只需要使用所需的功能即可。

AmlCompute 叢集是設計來根據您的工作負載進行動態調整。 叢集可相應增加到您設定的節點數目上限。 每次執行完成時，叢集將會釋放節點，並調整為您設定的最小節點計數。

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

您也可以設定節點在縮小之前閒置的時間量。 依預設，相應減少前的閒置時間會設定為120秒。

+ 如果您執行較少的反復實驗，請縮短這段時間來節省成本。
+ 如果您執行高度反復的開發/測試測試，您可能需要增加時間，如此您就不會在每次定型腳本或環境變更之後，支付持續的相應增加和減少。

您可以使用 [AMLCOMPUTE SDK 類別](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py) [AmlCompute CLI](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)搭配 [REST api](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable)，針對 Azure 入口網站中變更的工作負載需求設定 AmlCompute 叢集。

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

### <a name="set-quotas-on-resources"></a>設定資源的配額

AmlCompute 隨附 [配額 (或限制) ](how-to-manage-quotas.md#azure-machine-learning-compute)設定。 此配額是由 VM 系列 (例如，Dv2 系列、NCv3 系列) ，而且每個訂用帳戶各有不同的區域。 訂用帳戶以小預設值開始進行，但使用此設定可控制可在訂用帳戶中啟動的 Amlcompute 資源數量。 

也針對訂用帳戶內的每個工作區， [依 VM 系列設定工作區層級配額](how-to-manage-quotas.md#workspace-level-quotas)。 這樣做可讓您更細微地控制每個工作區可能產生的成本，並限制特定的 VM 系列。 

若要在工作區層級設定配額，請從 [Azure 入口網站](https://portal.azure.com)開始。  選取訂用帳戶中的任何工作區，然後選取左窗格中的 [ **使用方式 + 配額** ]。 然後，選取 [ **設定配額** ] 索引標籤以查看配額。 您需要訂用帳戶範圍的許可權才能設定配額，因為這是影響多個工作區的設定。

### <a name="set-run-autotermination-policies"></a>設定執行 autotermination 原則 

在某些情況下，您應該設定定型回合來限制其持續時間，或提早終止。 例如，當您使用 Azure Machine Learning 的內建超參數微調或自動化機器學習時。

以下是您可以使用的幾個選項：
* 定義 `max_run_duration_seconds` 在您的 RunConfiguration 中呼叫的參數，以控制執行可延伸到您所選計算上的最長持續時間， (本機或遠端雲端計算) 。
* 針對 [超參數微調](how-to-tune-hyperparameters.md#early-termination)，請從 Bandit 原則、中位數停止原則或截斷選取原則定義提早終止原則。 若要進一步控制超參數的掃描，請使用參數，例如 `max_total_runs` 或 `max_duration_minutes` 。
* 針對 [自動化機器學習](how-to-configure-auto-train.md#exit)，請使用旗標來設定類似的終止原則  `enable_early_stopping` 。 也請使用和等 `iteration_timeout_minutes` 屬性 `experiment_timeout_minutes` 來控制執行或整個實驗的持續時間上限。

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> 使用低優先順序的 Vm

Azure 可讓您在虛擬機器擴展集、Batch 和 Machine Learning 服務之間，使用超出未運用的容量來 Low-Priority Vm。 這些配置已預先 emptible，但相較于專用 Vm，價格較低。 一般來說，我們建議您針對 Batch 工作負載使用 Low-Priority Vm。 您也應該使用它們來復原中斷時，可以透過重新提交 (針對 Batch 推斷) 或透過重新開機 (進行使用檢查點) 的深度學習訓練。

Low-Priority Vm 的單一配額與專用配額值（依 VM 系列）不同。 深入瞭解 [AmlCompute 配額](how-to-manage-quotas.md)。

 Low-Priority Vm 不適用於計算實例，因為它們需要支援互動式筆記本體驗。

### <a name="use-reserved-instances"></a>使用保留執行個體

另一種節省計算資源成本的方法是 Azure 保留的 VM 實例。 這項供應專案可讓您承諾一年或三年期。 這些折扣的範圍最高可達72% 的隨用隨付價格，並會直接套用至您的每月 Azure 帳單。

Azure Machine Learning 計算原本就支援保留實例。 如果您購買一年或三年期的保留實例，我們會針對您的 Azure Machine Learning 受控計算自動套用折扣。


## <a name="next-steps"></a>後續步驟

- 瞭解 [如何使用 Azure 成本管理來優化您的雲端投資](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 深入瞭解如何使用 [成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)來管理成本。
- 瞭解如何避免非 [預期的成本](../cost-management-billing/manage/getting-started.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 採用 [成本管理](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 引導式學習課程。