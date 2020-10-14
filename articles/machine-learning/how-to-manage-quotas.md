---
title: 管理資源與配額
titleSuffix: Azure Machine Learning
description: 了解 Azure Machine Learning 資源的配額，以及如何要求更多配額。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 10/13/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4, contperfq2
ms.openlocfilehash: a81af14992c8557c245ab3a1073f031a6c505084
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019387"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>管理及增加 Azure Machine Learning 資源的配額

Azure 會使用限制和配額來防止因詐騙而無法執行預算，以及接受 Azure 容量限制。 當您針對生產工作負載進行調整時，請考慮這些限制。 在本文中，您將瞭解：

> [!div class="checklist"]
> + 與 [Azure Machine Learning](overview-what-is-azure-ml.md)相關之 Azure 資源的預設限制。
> + 查看您的配額和限制。
> + 建立工作區層級配額。
> + 要求配額增加。
> + 私人端點和 DNS 配額。

除了管理配額，您也可以瞭解如何 [規劃 & 管理 Azure Machine Learning 的成本](concept-plan-manage-cost.md)。

## <a name="special-considerations"></a>特殊考量

+ 配額是一種信用限制，不是容量保證。 如果您有大規模的容量需求， [請聯絡 Azure 支援以增加您的配額](#request-quota-increases)。

+ 配額會在您訂用帳戶中的所有服務之間共用，包括 Azure Machine Learning。 評估容量時，計算所有服務的使用量。
    + Azure Machine Learning 計算是例外狀況，且具有與核心計算配額不同的配額。 

+ 預設限制會因供應專案類別類型（如免費試用、隨用隨付）和虛擬機器 (VM) 系列（例如 Dv2、F、G 等等）而有所不同。

## <a name="default-resource-quotas"></a>預設資源配額

在本節中，您會瞭解下列資源的預設和最大配額限制：

+ 虛擬機器
+ Azure Machine Learning Compute
+ Azure Machine Learning 管線
+ 容器執行個體
+ 儲存體

> [!IMPORTANT]
> 限制日後有可能會變更。 最新的限制隨時可在適用於全 Azure 的服務層級配額[文件](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/)中找到。

### <a name="virtual-machines"></a>虛擬機器
每個 Azure 訂用帳戶對於所有服務的虛擬機器數目都有限制。 虛擬機器核心有區域總計限制和每個大小系列的區域限制 (Dv2、F 等 ) 。 這兩項限制會分別強制執行。

例如，請考慮美國東部訂用帳戶的總計 VM 核心限制為 30、A 系列核心限制為 30，和 D 系列核心限制為 30。 此訂用帳戶可部署 30 A1 Vm 或30個 D1 Vm，或兩個未超過總計30個核心的組合。

虛擬機器的限制無法在下表所示的值上產生。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
[Azure Machine Learning 計算](concept-compute-target.md#azure-machine-learning-compute-managed) 對於訂用帳戶中每個區域所允許的核心數目和唯一計算資源數目有預設的配額限制。 此配額與上一節中的 VM 核心配額不同。

[要求增加配額](#request-quota-increases) 來提高此區段中的限制，直到資料表中顯示的 **上限** 為止。

可用的資源：
+ 根據您的訂用帳戶供應專案類型而定，**每個區域的專用核心**預設限制為 24-300。  每一訂用帳戶的專用核心數目可以針對每個 VM 系列增加。 特製化 VM 系列（例如 NCv2、NCv3 或 ND 系列）開頭為零核心的預設值。

+ 依訂用帳戶供應專案類型而定，**每個區域的低優先順序核心**預設限制為 100-3000。 您可以增加每個訂用帳戶的低優先順序核心數目，而這是跨 VM 系列的單一值。

+ **每個區域** 的叢集預設限制為200。 這些數目會在訓練叢集和計算執行個體 (視為用於配額的單一節點叢集) 之間共用。

下表顯示無法超過的其他限制。

| **Resource** | **上限** |
| --- | --- |
| 每個資源群組的工作區 | 800 |
| 單一 Azure Machine Learning 計算 (AmlCompute) 資源中的節點 | 100 個節點 |
| 每個節點的 GPU MPI 進程 | 1 - 4 |
| 每個節點的 GPU 背景工作 | 1 - 4 |
| 作業存留期 | 21天<sup>1</sup> |
| Low-Priority 節點上的作業存留期 | 7 天<sup>2</sup> |
| 每個節點的參數伺服器 | 1 |

<sup>1</sup> 最大存留期指的是執行開始和完成之間的持續時間。 已完成的執行會無限期保存。 無法存取未在最長存留期內完成的執行資料。
<sup>2</sup> 有容量限制時，低優先順序節點上的作業可能會被佔用。 建議您在作業中執行檢查點。

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning 管線
[Azure Machine Learning 管線](concept-ml-pipelines.md) 有下列限制。

| **Resource** | **限制** |
| --- | --- |
| 管線中的步驟 | 30,000 |
| 每個資源群組的工作區 | 800 |

### <a name="container-instances"></a>容器執行個體

如需詳細資訊，請參閱 [容器實例限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits)。

### <a name="storage"></a>儲存體
針對每個訂用帳戶，Azure 儲存體帳戶的每個區域都有250個儲存體帳戶的限制。 這包括標準和進階儲存體帳戶。

若要增加限制，請透過 [Azure 支援](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)提出要求。 Azure 儲存體小組將會審查您的案例，並可核准最多250個區域的儲存體帳戶。


## <a name="workspace-level-quota"></a>工作區層級配額

使用工作區層級配額來管理相同訂用帳戶中多個 [工作區](concept-workspace.md) 之間的 Azure Machine Learning 計算目標配置。

根據預設，所有工作區都會共用與 VM 系列的訂用帳戶層級配額相同的配額。 不過，您可以在訂用帳戶中的工作區上設定個別 VM 系列的最大配額。 這可讓您共用容量並避免資源競爭問題：

1. 流覽至您訂用帳戶中的任何工作區。
1. 在左窗格中，選取 [ **使用量 + 配額**]。
1. 選取 [ **設定配額** ] 索引標籤以查看配額。
1. 展開 VM 系列。
1. 在該 VM 系列下所列的任何工作區上設定配額限制。

您無法設定負數值或高於訂用帳戶層級配額的值。

[![Azure Machine Learning 工作區層級配額](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> 您需要訂用帳戶層級的權限，才能在工作區層級上設定配額。

## <a name="view-your-usage-and-quotas"></a>檢視使用量和配額

若要查看各種 Azure 資源（例如虛擬機器、儲存體、網路）的配額，請使用 Azure 入口網站：

1. 在左窗格中，選取 [所有服務]，然後選取 [一般] 類別底下的 [訂用帳戶]。

2. 從訂用帳戶清單中，選取您要尋找其配額的訂用帳戶。

3. 選取 [ **使用量 + 配額** ] 以查看目前的配額限制和使用量。 使用篩選器來選取提供者和位置。 

訂用帳戶上的 Azure Machine Learning 計算配額會與其他 Azure 配額分開管理。 

1. 流覽至 Azure 入口網站中的 **Azure Machine Learning** 工作區。

2. 在左窗格的 [ **支援 + 疑難排解] 區段** 下，選取 [ **使用量 + 配額** ] 以查看目前的配額限制和使用量。

3. 選取訂用帳戶以檢視配額限制。 請記得篩選出您感興趣的區域。

4. 您可以在訂用帳戶層級視圖和工作區層級的視圖之間切換。

## <a name="request-quota-increases"></a>要求增加配額

若要將限制或配額提高到超過預設限制，請免費 [開啟線上客戶支援要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) 。

限制不能超過上述表格中顯示的最大限制值。 如果沒有上限，則您無法調整資源的限制。

要求增加配額時，請選取您要對其提出配額要求的服務。 例如 Azure Machine Learning、容器實例、儲存體等。針對 Azure Machine Learning 計算，您可以在依照上述步驟來查看配額時選取 [ **要求配額** ] 按鈕。

> [!NOTE]
> [免費試用訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0044p)無法增加限制或配額。 如果您有[免費試用訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0044p)，則可以升級到[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)訂用帳戶。 如需詳細資訊，請參閱[將 Azure 免費試用升級至隨用隨付](../billing/billing-upgrade-azure-subscription.md)和[免費試用訂用帳戶常見問題集](https://azure.microsoft.com/free/free-account-faq)。

## <a name="private-endpoint-and-private-dns-quota-increases"></a>私人端點和私人 DNS 配額增加

可以在訂用帳戶中建立的私人端點和私人 DNS 區域數目有一些限制。

雖然 Azure Machine Learning 會在 (客戶) 訂用帳戶中建立資源，但在某些情況下，會在 Microsoft 擁有的訂用帳戶中建立資源。

 在下列案例中，您可能需要在 Microsoft 擁有的訂用帳戶中要求配額額度：

* __使用客戶管理的金鑰 Private Link 啟用的工作區 (CMK) __
* __虛擬網路背後工作區的 Azure Container Registry__
* 將__已啟用 Private Link 的 Azure Kubernetes Service 叢集附加至您的工作區__。

若要要求這些案例的額度，請使用下列步驟：

1. [建立 Azure 支援要求](/azure/azure-portal/supportability/how-to-create-azure-support-request#create-a-support-request) ，並從 [ __基本__ ] 區段中選取下列選項：

    | 欄位 | 選取項目 |
    | ----- | ----- |
    | 問題類型 | 技術 |
    | 服務 | 我的服務。 在下拉式清單中選取 __Machine Learning__ 。 |
    | 問題類型 | 工作區設定、SDK 和 CLI |
    | 問題子類型 | 佈建或管理工作區時發生問題 |

2. 在 [ __詳細資料__ ] 區段中，使用 [ __描述__ ] 欄位來提供您想要使用的 Azure 區域，以及您打算使用的案例。 如果您需要針對多個訂用帳戶要求增加配額，請同時列出此欄位中的訂用帳戶識別碼。

3. 選取 [ __建立__ ] 以建立要求。

## <a name="next-steps"></a>後續步驟

+ [規劃與管理 Azure Machine Learning 的成本](concept-plan-manage-cost.md)
