---
title: 管理資源&配額
titleSuffix: Azure Machine Learning
description: 瞭解 Azure 機器學習的資源配額以及如何請求更多配額。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 03/05/2020
ms.openlocfilehash: 17a4652604c0faa804d24530869aac0848c972b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399140"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>管理及要求 Azure 資源的配額
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文提供有關訂閱 Azure 資源的預配置限制的詳細資訊。 還包括有關如何請求每種資源類型的配額增強的說明。 設置這些限制可防止因詐欺而導致的預算超支，並遵循 Azure 的容量條件約束。

與其他 Azure 服務一樣，與 Azure 機器學習關聯的某些資源也有限制。 這些限制的範圍從對工作區數的限制到用於模型訓練或推理/評分的實際基礎計算的限制。 

在為生產工作負荷設計和擴展 Azure 機器學習資源時，請考慮這些限制。 例如，如果群集未達到目標節點數，則可能已達到訂閱的 Azure 機器學習計算核心限制。 如果您想要將限制或配額提升到預設限制以上，您可以免費提出線上客戶支援要求。 由於 Azure 容量有其條件約束，您無法將限制提升至高於下表所示的「上限」值。 如果沒有上限欄，資源即沒有可調整的限制。

## <a name="special-considerations"></a>特殊考量

+ 配額是一種信用限制，不是容量保證。 如果您有大規模的容量需求，請連絡 Azure 支援。

+ 配額將在訂閱中的所有服務（包括 Azure 機器學習）之間共用。 唯一的例外是 Azure Machine Learning Compute，它具有核心計算配額以外的個別配額。 當評估您的容量需求時，請務必計算所有服務的配額使用量。

+ 預設限制因產品/服務類別類型而異，例如免費試用、即用即付和 VM 系列，如 Dv2、F、G 等。

## <a name="default-resource-quotas"></a>預設資源配額

以下是 Azure 訂用帳戶中各種資源類型的配額限制細目。

> [!IMPORTANT]
> 限制日後有可能會變更。 最新的限制隨時可在適用於全 Azure 的服務層級配額[文件](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/)中找到。

### <a name="virtual-machines"></a>虛擬機器
對於每個 Azure 訂閱，可以跨服務或獨立運行的虛擬機器數量有限制。 此限制是區域層級限制，並同時針對總核心數量以及每個系列進行限制。

虛擬機器內核具有區域總限制和區域/大小系列（Dv2、F 等）限制，這兩個限制都是單獨強制執行的。 例如，請考慮美國東部訂用帳戶的總計 VM 核心限制為 30、A 系列核心限制為 30，和 D 系列核心限制為 30。 此訂用帳戶會允許部署 30 個 A1 VM、30 個 D1 VM，或是兩個的組合，總計不超過 30 個核心 (例如 10 個 A1 VM 和 20 個 D1 VM)。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

如需配額限制的詳細資訊與最新清單，請查看[這裡](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)的全 Azure 配額文章。

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
就 Azure Machine Learning Compute 而言，對於訂用帳戶中的每個區域允許的核心數目與特有計算資源數目，均有預設的配額限制。 此配額與上述 VM 核心配額分開，並且核心限制不會在兩種資源類型之間共用，因為 AmlCompute 是一個託管服務，用於在託管模型中部署資源。

可用的資源：
+ 每個區域的專用內核的預設限制為 24 - 300，具體取決於您的訂閱產品/服務類型，EA 和 CSP 產品類型的預設值較高。  每個訂閱的專用內核數可以增加，並且每個 VM 系列都不同。 某些專用 VM 系列（如 NCv2、NCv3 或 ND 系列）以預設為零內核開始。 通過引發配額請求來討論增加選項，請與 Azure 支援部門聯繫。

+ 每個區域的低優先順序內核的預設限制為 100 - 3000，具體取決於您的訂閱產品/服務類型，對於 EA 和 CSP 產品類型具有較高的預設值。 每個訂閱的低優先順序內核數量可以增加，並且是跨 VM 系列的單一值。 請連絡 Azure 支援以討論增加選項。

+ 每個區域的群集的預設限制為 200。 它們在訓練群集和計算實例之間共用（出於配額目的，該實例被視為單個節點群集）。 如果您想要要求增加到超過此限制，請連絡 Azure 支援。

+ 有 #其他嚴格限制，一旦命中，不能超過。

| **資源** | **上限** |
| --- | --- |
| 每個資源群組的最大工作區 | 800 |
| 單一 Azure Machine Learning Compute (AmlCompute) 資源中的節點數上限 | 100 個節點 |
| 每個節點的最大 GPU MPI 流程數 | 1 - 4 |
| 每個節點的最大 GPU 背景工作角色數 | 1 - 4 |
| 作業存留期上限 | 90 天<sup>1</sup> |
| 低優先順序節點上的最大作業存留期 | 7 天<sup>2</sup> |
| 每個節點的最大參數伺服器數 | 1 |

<sup>1</sup> 存留期上限是指執行開始到完成的時間。 已完成的執行會無限期保留；未在存留期上限內完成的執行，其資料將無法存取。
<sup>低</sup>優先順序節點上的 2 個作業可在存在容量約束時搶佔。 我們建議您在作業中實施檢查點。

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning 管線
對於 Azure 機器學習管道，管道中的步驟數和訂閱中每個區域基於計畫的已發佈管道運行數存在配額限制。
- 管道中允許的最大步數為 30，000
- 每月每個訂閱的發佈管道的博客觸發的管道的基於計畫的運行和 blob 提取的總和的最大數量為 100，000

> [!NOTE]
> 如果您想要上調此限制，請連絡 [Microsoft 支援服務](https://azure.microsoft.com/support/options/)。

### <a name="container-instances"></a>容器執行個體

針對您在指定期間 (以每小時為範圍) 或整個訂用帳戶中可以啟動的容器執行個體數目也有限制。

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

如需配額限制的詳細資訊與最新清單，請查看[這裡](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits)的全 Azure 配額文章。

### <a name="storage"></a>存放裝置
針對每個區域以及指定的訂用帳戶中的儲存體帳戶數目有限制。 預設限制為 250，包括標準存儲帳戶和高級存儲帳戶。 如果給定區域中需要 250 多個存儲帳戶，請通過 Azure[支援](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)發出請求。 Azure 儲存體小組將會檢閱您的商務案例，而且可以針對指定區域核准多達 250 個儲存體帳戶。


## <a name="workspace-level-quota"></a>工作區級別配額

為了更好地管理各種工作區之間的 Aml 計算資源配置，我們引入了一項功能，允許您分發訂閱級別配額（按 VM 系列），並在工作區級別配置它們。 預設行為是，所有工作區的配額與任何 VM 系列的訂閱級別配額相同。 但是，隨著工作區數量的增加，不同優先順序的工作負載開始共用相同的資源，使用者希望有一種方式更好地共用容量並避免資源爭用問題。 Azure 機器學習通過允許使用者為每個工作區上的特定 VM 系列設置最大配額，從而提供了具有託管計算產品的解決方案。 這類似于在工作區之間分配容量，使用者可以選擇過度分配以推動最大利用率。 

要在工作區級別設置配額，請轉到訂閱中的任何工作區，然後按一下左側窗格中的 **"使用方式 " 和配額**。 然後選擇"**配置配額**"選項卡以查看配額、展開任何 VM 系列，並設置該 VM 系列下列出的任何工作區的配額限制。 請記住，您不能設置負值或高於訂閱級別配額的值。 此外，正如您所觀察到的，預設情況下，所有工作區都分配了整個訂閱配額，以便充分利用分配的配額。

[![Azure 機器學習工作區級別配額](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> 這只是企業版功能。 如果訂閱中同時具有"基本"和"企業版"工作區，則可以使用此選項僅在企業工作區上設置配額。 基本工作區將繼續具有訂閱級別配額，這是預設行為。
>
> 您需要訂閱級別許可權才能在工作區級別設置配額。 強制實施此操作，以便單個工作區擁有者不編輯或增加其配額，並開始侵佔為另一個工作區預留的資源。 因此，訂閱管理員最適合在工作區中分配和分發這些配額。



## <a name="view-your-usage-and-quotas"></a>檢視使用量和配額

透過 Azure 入口網站檢視各種資源 (例如虛擬機器、儲存體、網路) 的配額非常簡單。

1. 在左窗格中，選取 [所有服務]****，然後選取 [一般] 類別底下的 [訂用帳戶]****。

1. 從訂用帳戶清單中，選取您要尋找其配額的訂用帳戶。

   **有一點需要注意**，具體而言是檢視 Azure Machine Learning Compute 配額時。 如上所述，該配額與您訂用帳戶上的計算配額是分開的。

1. 在左側窗格中，選擇**機器學習服務**，然後從顯示的清單中選擇任何工作區

1. 在下一個刀鋒視窗的 [支援與疑難排解]**** 區段下方，選取 [使用量 + 配額]****，以檢視目前配額限制與使用量。

1. 選取訂用帳戶以檢視配額限制。 請記得篩選出您感興趣的區域。

1. 現在，您可以在訂閱級別視圖和工作區級別視圖之間切換：
    + **訂閱視圖：** 這允許您按 VM 系列查看核心配額的使用方式，按工作區擴展核心配額，然後按實際群集名稱進一步擴展核心配額。 此視圖最適合快速瞭解特定 VM 系列的核心使用方式的詳細資訊，以查看工作區的分解，以及每個工作區的基礎群集的中斷。 此視圖中的一般約定是（使用/配額），其中用法是當前向上擴展內核的數量，而配額是資源可以縮放到的邏輯最大內核數。 對於每個**工作區**，配額將是工作區級別配額（如上所述），表示可以縮放到特定 VM 系列的最大內核數。 **對於類似**群集，配額實際上是與群集可以縮放到由max_nodes屬性定義的最大節點數對應的核心。

    + **工作區視圖：** 這允許您按工作區查看核心配額的使用方式，按 VM 系列擴展核心配額，並通過實際群集名稱進一步擴展核心配額。 此視圖最適合快速瞭解特定工作區的核心使用方式的詳細資訊，以查看 VM 族的分解以及每個族的基礎群集的中斷。

## <a name="request-quota-increases"></a>要求增加配額

如果要將限制或配額提高到預設限制以上，[請免費打開線上客戶支援請求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)。

您無法將限制提升到高於下表中所示的上限值。 如果沒有上限，資源即沒有可調整的限制。 [這篇](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)文章涵蓋配額增加程序的詳細資料。

要求增加配額時，您必須選取您要求提升配額的服務，這可以是 Machine Learning 服務配額、容器執行個體或儲存體的配額。 此外，對於 Azure 機器學習計算，您可以按一下 **"請求配額"** 按鈕，同時按照上述步驟查看配額。

> [!NOTE]
> [免費試用訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0044p)無法增加限制或配額。 如果您有[免費試用訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0044p)，則可以升級到[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)訂用帳戶。 如需詳細資訊，請參閱[將 Azure 免費試用升級至隨用隨付](../billing/billing-upgrade-azure-subscription.md)和[免費試用訂用帳戶常見問題集](https://azure.microsoft.com/free/free-account-faq)。
