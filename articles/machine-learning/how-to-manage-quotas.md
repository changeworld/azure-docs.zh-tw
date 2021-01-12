---
title: 管理資源和配額
titleSuffix: Azure Machine Learning
description: 瞭解 Azure Machine Learning 資源的配額和限制，以及如何要求增加配額。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: nishankgu
ms.author: nigup
ms.date: 12/1/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperf-fy20q4, contperf-fy21q2
ms.openlocfilehash: 3f08f401f24d95431bcd3bba48976641cebb28e9
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98072202"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-machine-learning"></a>使用 Azure Machine Learning 管理和提高資源配額

Azure 會使用限制和配額來防止因詐騙而產生的預算溢出，並接受 Azure 容量限制。 當您針對生產工作負載進行調整時，請考慮這些限制。 在本文中，您將瞭解：

> [!div class="checklist"]
> + 與 [Azure Machine Learning](overview-what-is-azure-ml.md)相關之 Azure 資源的預設限制。
> + 正在建立工作區層級配額。
> + 查看您的配額和限制。
> + 要求增加配額。
> + 私人端點和 DNS 配額。

除了管理配額之外，您還可以瞭解如何 [規劃和管理 Azure Machine Learning 的成本](concept-plan-manage-cost.md) ，或瞭解 [Azure Machine Learning 中的服務限制](resource-limits-quotas-capacity.md)。

## <a name="special-considerations"></a>特殊考量

+ 配額是一種信用限制，不是容量保證。 如果您有大規模的容量需求， [請聯絡 Azure 支援以增加您的配額](#request-quota-increases)。

+ 配額會在您訂用帳戶中的所有服務之間共用，包括 Azure Machine Learning。 當您在評估容量時，計算所有服務的使用量。
 
  Azure Machine Learning 計算是例外狀況。 它與核心計算配額具有不同的配額。 

+ 預設限制會因供應專案類別類型而異，例如免費試用、隨用隨付，以及虛擬機器 (VM) 系列 (例如 Dv2、F 和 G) 。

## <a name="default-resource-quotas"></a>預設資源配額

在本節中，您會瞭解下列資源的預設和最大配額限制：

+ Azure Machine Learning 資產
  + Azure Machine Learning Compute
  + Azure Machine Learning 管線
+ 虛擬機器
+ Azure Container Instances
+ Azure 儲存體

> [!IMPORTANT]
> 限制日後有可能會變更。 如需最新資訊，請參閱  [Azure Machine Learning 中的服務限制](resource-limits-quotas-capacity.md)。



### <a name="azure-machine-learning-assets"></a>Azure Machine Learning 資產
下列資產限制適用于每個工作區。 

| **Resource** | **上限** |
| --- | --- |
| 資料集 | 1000 萬 |
| 執行 | 1000 萬 |
| 模型 | 1000 萬|
| Artifacts | 1000 萬 |

此外， **執行時間** 上限為30天，而 **每次執行所記錄的度量** 數目上限為1000000。

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
[Azure Machine Learning 計算](concept-compute-target.md#azure-machine-learning-compute-managed) 的核心數目 (依每個 VM 系列分割的核心數目和累積的總) 核心數，以及訂用帳戶中每個區域所允許的唯一計算資源數目。 此配額與上一節所列的 VM 核心配額不同，因為它只適用于 Azure Machine Learning 的受控計算資源。

[要求增加配額](#request-quota-increases) 來提高不同 VM 系列核心配額的限制，此區段中的訂用帳戶核心配額和資源總數。

可用的資源：
+ 根據您的訂用帳戶供應專案類型而定，**每個區域的專用核心** 預設限制為24到300。 您可以為每個 VM 系列增加每個訂用帳戶的專用核心數目。 特製化 VM 系列（例如 NCv2、NCv3 或 ND 系列）開頭為零核心的預設值。

+ 依訂用帳戶供應專案類型而 **定，每個區域的低優先順序核心** 預設限制為100到3000。 您可以增加每個訂用帳戶的低優先順序核心數目，而這是跨 VM 系列的單一值。

+ **每個區域** 的叢集預設限制為200。 這些會在定型叢集和計算實例之間共用。 針對配額用途， (計算實例視為單一節點叢集。 ) 

> [!TIP]
> 若要深入瞭解哪些 VM 系列要要求增加配額，請查看 [Azure 中的虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/sizes)。 例如，GPU VM 系列的系列名稱開頭為 "N" (例如 NCv3 系列) 

下表顯示平臺的其他限制。 請透過 **技術** 支援票證與 AzureML 產品小組聯繫，以要求例外狀況。

| **資源或動作** | **上限** |
| --- | --- |
| 每個資源群組的工作區 | 800 |
| 單一 Azure Machine Learning 計算中的節點 (**AmlCompute) 叢集** 設定為未啟用通訊的集區 (亦即無法執行 MPI 作業)  | 100節點，但可設定為最多65000個節點 |
| 單一平行執行步驟中的節點會在 Azure Machine Learning Compute (AmlCompute) 叢集上 **執行** | 100節點，但如果您的叢集設定為依上述比例進行調整，最多可設定65000個節點 |
| 單一 Azure Machine Learning 計算中的節點 (**AmlCompute) 叢集** 設定為已啟用通訊的集區 | 300節點，但可設定為最多4000個節點 |
| 單一 Azure Machine Learning 計算中的節點 (**AmlCompute) 叢集** 設定，作為已啟用 RDMA 的 VM 系列上已啟用通訊的集區 | 100 個節點 |
| 單一 MPI 中的節點會在 Azure Machine Learning 計算 (AmlCompute) 叢集上 **執行** | 100節點，但可以增加至300節點 |
| 每個節點的 GPU MPI 進程 | 1 - 4 |
| 每個節點的 GPU 背景工作 | 1 - 4 |
| 作業存留期 | 21天<sup>1</sup> |
| 低優先順序節點上的作業存留期 | 7 天<sup>2</sup> |
| 每個節點的參數伺服器 | 1 |

<sup>1</sup> 最大存留期是執行開始和完成時之間的持續時間。 已完成的執行會無限期保存。 無法存取未在最長存留期內完成的執行資料。
如果有容量限制，則低優先順序節點上的<sup>2</sup>個作業可被優先佔用。 建議您在作業中執行檢查點。

#### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning 管線
[Azure Machine Learning 管線](concept-ml-pipelines.md) 有下列限制。

| **Resource** | **限制** |
| --- | --- |
| 管線中的步驟 | 30,000 |
| 每個資源群組的工作區 | 800 |

### <a name="virtual-machines"></a>虛擬機器
每個 Azure 訂用帳戶對於所有服務的虛擬機器數目都有限制。 虛擬機器核心有區域總計限制和每個大小系列的區域限制。 這兩項限制會分別強制執行。

例如，請考慮美國東部訂用帳戶的總計 VM 核心限制為 30、A 系列核心限制為 30，和 D 系列核心限制為 30。 此訂用帳戶可部署 30 A1 Vm 或30個 D1 Vm，或兩個未超過總計30個核心的組合。

您無法針對下表所顯示的值，提高虛擬機器的限制。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="container-instances"></a>容器執行個體

如需詳細資訊，請參閱 [容器實例限制](../azure-resource-manager/management/azure-subscription-service-limits.md#container-instances-limits)。

### <a name="storage"></a>儲存體
針對每個訂用帳戶，Azure 儲存體的每個區域都有250個儲存體帳戶的限制。 這項限制包括標準和 Premium 儲存體帳戶。

若要增加限制，請透過 [Azure 支援](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)提出要求。 Azure 儲存體小組將會審查您的案例，並可核准最多250個區域的儲存體帳戶。


## <a name="workspace-level-quotas"></a>工作區層級配額

使用工作區層級配額來管理相同訂用帳戶中多個 [工作區](concept-workspace.md) 之間的 Azure Machine Learning 計算目標配置。

根據預設，所有工作區都會共用與 VM 系列的訂用帳戶層級配額相同的配額。 不過，您可以在訂用帳戶中的工作區上設定個別 VM 系列的最大配額。 這可讓您共用容量並避免資源爭用問題。

1. 移至訂用帳戶中的任何工作區。
1. 在左窗格中，選取 [ **使用量 + 配額**]。
1. 選取 [ **設定配額** ] 索引標籤以查看配額。
1. 展開 VM 系列。
1. 在該 VM 系列下所列的任何工作區上設定配額限制。

您無法設定負數值或高於訂用帳戶層級配額的值。

[![顯示 Azure Machine Learning 工作區層級配額的螢幕擷取畫面。](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> 您需要訂用帳戶層級許可權，才能在工作區層級設定配額。

## <a name="view-your-usage-and-quotas"></a>檢視使用量和配額

若要查看各種 Azure 資源（例如虛擬機器、儲存體或網路）的配額，請使用 Azure 入口網站：

1. 在左窗格中，選取 [**所有服務**]，然後選取 [**一般**] 類別底下的 [**訂用帳戶**]。

2. 從訂用帳戶清單中，選取您要尋找其配額的訂用帳戶。

3. 選取 [ **使用量 + 配額** ] 以查看目前的配額限制和使用量。 使用篩選器來選取提供者和位置。 

您可以與其他 Azure 配額分開管理訂用帳戶上的 Azure Machine Learning 計算配額： 

1. 移至 Azure 入口網站中的 **Azure Machine Learning** 工作區。

2. 在左窗格的 [ **支援 + 疑難排解** ] 區段中，選取 [ **使用量 + 配額** ] 以查看目前的配額限制和使用量。

3. 選取訂用帳戶以檢視配額限制。 篩選至您感興趣的區域。

4. 您可以在訂用帳戶層級視圖和工作區層級視圖之間切換。

## <a name="request-quota-increases"></a>要求增加配額

若要將限制或配額提高到超過預設限制，請免費 [開啟線上客戶支援要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) 。

您無法將上述表格中所顯示的最大值限制提高。 如果沒有上限，您就無法調整資源的限制。

當您要求增加配額時，請選取您要考慮的服務。 例如，選取 [Azure Machine Learning]、[容器實例] 或 [儲存體]。 針對 Azure Machine Learning 計算，您可以在上一步中查看配額時選取 [ **要求配額** ] 按鈕。

> [!NOTE]
> [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p) 訂用帳戶不符合限制或配額增加的資格。 如果您有免費試用訂用帳戶，您可以升級為 [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/) 訂用帳戶。 如需詳細資訊，請參閱 [將 azure 免費試用版升級至隨用隨付](../cost-management-billing/manage/upgrade-azure-subscription.md) 和 [azure 免費帳戶的常見問題](https://azure.microsoft.com/free/free-account-faq)。

## <a name="private-endpoint-and-private-dns-quota-increases"></a>私人端點和私人 DNS 配額增加

您可以在訂用帳戶中建立的私人端點和私人 DNS 區域數目有限制。

Azure Machine Learning 會在 (客戶) 訂用帳戶中建立資源，但在某些案例中，會在 Microsoft 擁有的訂用帳戶中建立資源。

 在下列案例中，您可能需要在 Microsoft 擁有的訂用帳戶中要求配額額度：

* 使用客戶管理的金鑰 Azure Private Link 啟用的工作區 (CMK) 
* 虛擬網路背後工作區的 Azure Container Registry
* 將已啟用 Private Link 的 Azure Kubernetes Service 叢集連結至您的工作區

若要要求這些案例的額度，請使用下列步驟：

1. [建立 Azure 支援要求](../azure-portal/supportability/how-to-create-azure-support-request.md#create-a-support-request) ，並在 [ __基本__ ] 區段中選取下列選項：

    | 欄位 | 選取 |
    | ----- | ----- |
    | 問題類型 | **技術** |
    | 服務 | **我的服務**。 然後，在下拉式清單中選取 [ __Machine Learning__ ]。 |
    | 問題類型 | **工作區設定和安全性** |
    | 問題子類型 | **私人端點和私人 DNS 區域額度要求** |

2. 在 [ __詳細資料__ ] 區段中，使用 [ __描述__ ] 欄位提供您打算使用的 Azure 區域和案例。 如果您需要針對多個訂用帳戶要求增加配額，請在此欄位中列出訂用帳戶識別碼。

3. 選取 [ __建立__ ] 以建立要求。

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="私人端點和私人 DNS 配額增加要求的螢幕擷取畫面。":::

## <a name="next-steps"></a>後續步驟

+ [規劃和管理 Azure Machine Learning 的成本](concept-plan-manage-cost.md)
+ [Azure Machine Learning 中的服務限制](resource-limits-quotas-capacity.md)
