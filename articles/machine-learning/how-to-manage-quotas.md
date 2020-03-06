---
title: '& 配額管理資源'
titleSuffix: Azure Machine Learning
description: 瞭解 Azure Machine Learning 資源的配額，以及如何要求更多配額。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 03/05/2020
ms.openlocfilehash: 17a4652604c0faa804d24530869aac0848c972b3
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399140"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>管理及要求 Azure 資源的配額
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文提供針對您的訂用帳戶預先設定 Azure 資源限制的詳細資料。 另外也包含如何要求每種資源類型的配額增強功能的指示。 設置這些限制可防止因詐欺而導致的預算超支，並遵循 Azure 的容量條件約束。

與其他 Azure 服務一樣，與 Azure Machine Learning 相關聯的特定資源有一些限制。 這些限制的範圍是從工作區數目的上限，到用於模型定型或推斷/評分的實際基礎計算限制。 

當您為生產工作負載設計和調整 Azure Machine Learning 資源時，請考慮這些限制。 例如，如果您的叢集未達到目標節點數目，則您可能已達到訂用帳戶的 Azure Machine Learning 計算核心限制。 如果您想要將限制或配額提升到預設限制以上，您可以免費提出線上客戶支援要求。 由於 Azure 容量有其條件約束，您無法將限制提升至高於下表所示的「上限」值。 如果沒有上限欄，資源即沒有可調整的限制。

## <a name="special-considerations"></a>特殊考量

+ 配額是一種信用限制，不是容量保證。 如果您有大規模的容量需求，請連絡 Azure 支援。

+ 您的配額會在訂用帳戶中的所有服務之間共用，包括 Azure Machine Learning。 唯一的例外是 Azure Machine Learning Compute，它具有核心計算配額以外的個別配額。 當評估您的容量需求時，請務必計算所有服務的配額使用量。

+ 預設限制會因供應專案類別類型（如免費試用、隨用隨付和 VM 系列，例如 Dv2、F、G 等等）而有所不同。

## <a name="default-resource-quotas"></a>預設資源配額

以下是 Azure 訂用帳戶中各種資源類型的配額限制細目。

> [!IMPORTANT]
> 限制日後有可能會變更。 最新的限制隨時可在適用於全 Azure 的服務層級配額[文件](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/)中找到。

### <a name="virtual-machines"></a>虛擬機器
針對每個 Azure 訂用帳戶，您可以在服務或獨立中擁有的虛擬機器數目有限制。 此限制是區域層級限制，並同時針對總核心數量以及每個系列進行限制。

虛擬機器核心具有區域總計限制和每個大小的區域（Dv2、F 等）限制，這兩者都是分別強制執行。 例如，請考慮美國東部訂用帳戶的總計 VM 核心限制為 30、A 系列核心限制為 30，和 D 系列核心限制為 30。 此訂用帳戶會允許部署 30 個 A1 VM、30 個 D1 VM，或是兩個的組合，總計不超過 30 個核心 (例如 10 個 A1 VM 和 20 個 D1 VM)。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

如需配額限制的詳細資訊與最新清單，請查看[這裡](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)的全 Azure 配額文章。

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
就 Azure Machine Learning Compute 而言，對於訂用帳戶中的每個區域允許的核心數目與特有計算資源數目，均有預設的配額限制。 此配額與上述 VM 核心配額不同，而且這兩個資源類型之間不會共用核心限制，因為 AmlCompute 是一種受控服務，可將資源部署在託管代理的模型中。

可用的資源：
+ 根據您的訂用帳戶供應專案類型，針對 EA 和 CSP 供應專案類型具有較高的預設值，每個區域的專用核心預設限制為 24-300。  您可以增加每個訂用帳戶的專用核心數目，而且每個 VM 系列各有不同。 某些特製化的 VM 系列（例如 NCv2、NCv3 或 ND 系列）開頭為零核心的預設值。 藉由引發配額要求來討論增加選項，以聯繫 Azure 支援。

+ 根據您的訂用帳戶供應專案類型，具有較高的 EA 和 CSP 供應專案類型預設值，每個區域的低優先順序核心預設限制為 100-3000。 每個訂用帳戶的低優先順序核心數目可以增加，而且是跨 VM 系列的單一值。 請連絡 Azure 支援以討論增加選項。

+ 每個區域的叢集都有預設的限制為200。 這些會在定型叢集和計算實例之間共用（視為用於配額用途的單一節點叢集）。 如果您想要要求增加到超過此限制，請連絡 Azure 支援。

+ 有 * * 其他嚴格限制無法超過一次。

| **Resource** | **上限** |
| --- | --- |
| 每個資源群組的最大工作區 | 800 |
| 單一 Azure Machine Learning Compute (AmlCompute) 資源中的節點數上限 | 100 個節點 |
| 每個節點的最大 GPU MPI 流程數 | 1 - 4 |
| 每個節點的最大 GPU 背景工作角色數 | 1 - 4 |
| 作業存留期上限 | 90天<sup>1</sup> |
| 低優先順序節點上的最大工作存留期 | 7天<sup>2</sup> |
| 每個節點的最大參數伺服器數 | 1 |

<sup>1</sup> 存留期上限是指執行開始到完成的時間。 已完成的執行會無限期保留；未在存留期上限內完成的執行，其資料將無法存取。
當有容量限制時，可能會佔用低優先順序節點上的<sup>2</sup>個工作。 我們建議您在作業中執行檢查點。

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning 管線
針對 Azure Machine Learning 管線，管線中的步驟數目以及訂用帳戶中每個區域的排程型執行數目會有配額限制。
- 管線中允許的最大步驟數目是30000
- 針對每個訂用帳戶每個訂閱的已發佈管線，以排程為基礎的執行總和和 blob 提取的最大數目為100000

> [!NOTE]
> 如果您想要上調此限制，請連絡 [Microsoft 支援服務](https://azure.microsoft.com/support/options/)。

### <a name="container-instances"></a>容器執行個體

針對您在指定期間 (以每小時為範圍) 或整個訂用帳戶中可以啟動的容器執行個體數目也有限制。

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

如需配額限制的詳細資訊與最新清單，請查看[這裡](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits)的全 Azure 配額文章。

### <a name="storage"></a>儲存體
針對每個區域以及指定的訂用帳戶中的儲存體帳戶數目有限制。 預設限制為250，同時包含標準和進階儲存體帳戶。 如果您在指定區域中需要超過250個儲存體帳戶，請透過[Azure 支援](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)提出要求。 Azure 儲存體小組將會檢閱您的商務案例，而且可以針對指定區域核准多達 250 個儲存體帳戶。


## <a name="workspace-level-quota"></a>工作區層級配額

為了更有效地管理各種工作區之間 Amlcompute 的資源配置，我們引進了一項功能，可讓您散發訂用帳戶層級配額（依 VM 系列），並在工作區層級進行設定。 預設行為是所有工作區的配額與任何 VM 系列的訂用帳戶層級配額相同。 不過，隨著工作區數目的增加，和不同優先順序的工作負載會開始共用相同的資源，使用者想要能夠更清楚地共用容量並避免資源爭用問題。 Azure Machine Learning 可讓使用者在每個工作區上設定特定 VM 系列的最大配額，以提供其受控計算供應專案的解決方案。 這類似于在工作區之間散發您的容量，而使用者也可以選擇過度配置來驅動最大使用率。 

若要在工作區層級設定配額，請移至訂用帳戶中的任何工作區，然後按一下左窗格中的 [**使用方式 + 配額**]。 然後選取 [**設定配額**] 索引標籤來查看配額、展開任何 vm 系列，然後在該 VM 系列底下列出的任何工作區上設定配額限制。 請記住，您不能設定負值或高於訂用帳戶層級配額的值。 此外，如您所觀察，預設會將整個訂用帳戶配額指派給所有工作區，以允許完整使用配置的配額。

[![Azure Machine Learning 工作區層級配額](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> 這只是 Enterprise edition 的功能。 如果您的訂用帳戶中有基本和企業版的工作區，您可以使用此功能，只在您的企業工作區上設定配額。 您的基本工作區會繼續擁有訂用帳戶層級配額，這是預設行為。
>
> 您需要訂用帳戶層級許可權，才能在工作區層級設定配額。 這會強制執行，讓個別的工作區擁有者不會編輯或增加其配額，也不會開始超越另一個工作區的資源。 因此，訂用帳戶管理員最適合跨工作區配置和散發這些配額。



## <a name="view-your-usage-and-quotas"></a>檢視使用量和配額

透過 Azure 入口網站檢視各種資源 (例如虛擬機器、儲存體、網路) 的配額非常簡單。

1. 在左窗格中，選取 [所有服務]，然後選取 [一般] 類別底下的 [訂用帳戶]。

1. 從訂用帳戶清單中，選取您要尋找其配額的訂用帳戶。

   **有一點需要注意**，具體而言是檢視 Azure Machine Learning Compute 配額時。 如上所述，該配額與您訂用帳戶上的計算配額是分開的。

1. 在左窗格中，選取 [ **Machine Learning 服務**]，然後從顯示的清單中選取任何工作區

1. 在下一個刀鋒視窗的 [支援與疑難排解] 區段下方，選取 [使用量 + 配額]，以檢視目前配額限制與使用量。

1. 選取訂用帳戶以檢視配額限制。 請記得篩選出您感興趣的區域。

1. 您現在可以在訂用帳戶層級視圖和工作區層級視圖之間切換：
    + **訂用帳戶視圖：** 這可讓您依 VM 系列來查看核心配額的使用量、依工作區展開它，並以實際的叢集名稱進一步擴充。 此視圖最適合快速進入特定 VM 系列的核心使用量詳細資料，以查看工作區的分解，以及每個工作區的基礎叢集。 此視圖中的一般慣例是（使用量/配額），其中使用量是目前的相應增加核心數目，而配額是資源可調整的核心最大邏輯數目。 針對每個**工作區**，配額會是工作區層級配額（如上面所述），代表您可以針對特定 VM 系列擴充的核心數目上限。 對於叢集**的相似之**處，配額實際上是對應到叢集可調整為 max_nodes 屬性所定義的節點數目上限的核心。

    + **工作區視圖：** 這可讓您依工作區來查看核心配額的使用量、依 VM 系列擴充它，並以實際的叢集名稱進一步擴充。 此視圖最適合快速進入特定工作區的核心使用量詳細資料，以查看 VM 系列的細分，以及每個系列的基礎叢集的詳細資訊。

## <a name="request-quota-increases"></a>要求增加配額

如果您想要將限制或配額提升到預設限制以上，您可以免費[建立線上客戶支援要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)。

您無法將限制提升到高於下表中所示的上限值。 如果沒有上限，資源即沒有可調整的限制。 [這篇](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)文章涵蓋配額增加程序的詳細資料。

要求增加配額時，您必須選取您要求提升配額的服務，這可以是 Machine Learning 服務配額、容器執行個體或儲存體的配額。 除了 Azure Machine Learning 計算以外，您也可以按一下 [**要求配額**] 按鈕，並遵循上述步驟來查看配額。

> [!NOTE]
> [免費試用訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0044p)無法增加限制或配額。 如果您有[免費試用訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0044p)，則可以升級到[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)訂用帳戶。 如需詳細資訊，請參閱[將 Azure 免費試用升級至隨用隨付](../billing/billing-upgrade-azure-subscription.md)和[免費試用訂用帳戶常見問題集](https://azure.microsoft.com/free/free-account-faq)。
