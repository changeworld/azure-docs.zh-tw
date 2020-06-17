---
title: 管理資源與配額
titleSuffix: Azure Machine Learning
description: 了解 Azure Machine Learning 資源的配額，以及如何要求更多配額。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 05/08/2020
ms.custom: contperfq4
ms.openlocfilehash: c5862ee90a12240e7293647fae6af4f18f30c164
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680384"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>管理及增加 Azure Machine Learning 資源的配額
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文將為 [Azure Machine Learning](overview-what-is-azure-ml.md) 使用者詳細說明訂用帳戶上 Azure 資源的預先設定限制。 另外也包含如何針對每種資源類型要求增加配額的指示。 設置這些限制可防止因詐欺而導致的預算超支，並遵循 Azure 的容量條件約束。

如同使用其他 Azure 服務，對於與 Azure Machine Learning 相關聯的特定資源有一些限制。 這些限制的範圍，包括[工作區](concept-workspace.md)數目上限，到用於模型訓練或推斷/評分的實際基礎計算上的限制。 

當您為生產工作負載設計和調整 Azure Machine Learning 資源時，請考慮這些限制。 例如，如果您的叢集未達到目標節點數目，表示您可能已達到訂用帳戶的 Azure Machine Learning Compute 核心限制。 如果您想要將限制或配額提升到預設限制以上，您可以免費提出線上客戶支援要求。 由於 Azure 容量有其條件約束，您無法將限制提升至高於下表所示的「上限」值。 如果沒有上限欄，資源即沒有可調整的限制。

## <a name="special-considerations"></a>特殊考量

+ 配額是一種信用限制，不是容量保證。 如果您有大規模的容量需求，請連絡 Azure 支援。

+ 您的配額會由訂用帳戶中的所有服務所共用，包括 Azure Machine Learning。 唯一的例外是 Azure Machine Learning Compute，它具有核心計算配額以外的個別配額。 當評估您的容量需求時，請務必計算所有服務的配額使用量。

+ 預設限制會因供應項目類別類型 (例如免費試用、隨用隨付) 和 VM 系列 (例如 Dv2、F、G 等) 而有所差異。

## <a name="default-resource-quotas"></a>預設資源配額

以下是 Azure 訂用帳戶中各種資源類型的配額限制細目。

> [!IMPORTANT]
> 限制日後有可能會變更。 最新的限制隨時可在適用於全 Azure 的服務層級配額[文件](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/)中找到。

### <a name="virtual-machines"></a>虛擬機器
在每個 Azure 訂用帳戶上，都有可跨服務佈建，或以獨立方式佈建的虛擬機器數目限制。 此限制是區域層級限制，並同時針對總核心數量以及每個系列進行限制。

虛擬機器核心具有個別強制執行的區域總數限制，以及區域的每個大小系列 (Dv2、F 等等) 限制。 例如，請考慮美國東部訂用帳戶的總計 VM 核心限制為 30、A 系列核心限制為 30，和 D 系列核心限制為 30。 此訂用帳戶會允許部署 30 個 A1 VM、30 個 D1 VM，或是兩個的組合，總計不超過 30 個核心 (例如 10 個 A1 VM 和 20 個 D1 VM)。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

如需配額限制的詳細資訊與最新清單，請查看[全 Azure 配額文章](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
就 [Azure Machine Learning Compute](concept-compute-target.md#azure-machine-learning-compute-managed) 而言，對於訂用帳戶中的每個區域允許的核心數目與特有計算資源數目，均有預設的配額限制。 此配額與上述 VM 核心配額不同，而且這兩個資源類型之間不會共用核心限制，因為 AmlCompute 是一種受控服務，其會將資源部署在託管的代理模型中。

可用的資源：
+ 每個區域的專用核心都有 24-300 個的預設限制，視您的訂用帳戶供應項目類型而定，而 EA 和 CSP 供應項目類型具有較高的預設值。  您可以增加每個訂用帳戶的專用核心數目，而且每個 VM 系列各有不同。 某些特製化的 VM 系列 (例如 NCv2、NCv3 或 ND 系列) 會以零個核心的預設值開始。 您可以藉由提出配額要求來連絡 Azure 支援，並與之討論增加選項。

+ 每個區域的低優先順序核心都有 100 - 3000 個的預設限制，視您的訂用帳戶供應項目類型而定，而 EA 和 CSP 供應項目類型具有較高的預設值。 您可以增加每個訂用帳戶的低優先順序核心數目，而這是跨 VM 系列的單一值。 請連絡 Azure 支援以討論增加選項。

+ 每個區域的叢集數目都有 200 個的預設限制。 這些數目會在訓練叢集和計算執行個體 (視為用於配額的單一節點叢集) 之間共用。 如果您想要要求增加到超過此限制，請連絡 Azure 支援。

+ 另外還有不可超過的其他嚴格限制。

| **Resource** | **上限** |
| --- | --- |
| 每個資源群組的最大工作區 | 800 |
| 單一 Azure Machine Learning Compute (AmlCompute) 資源中的節點數上限 | 100 個節點 |
| 每個節點的最大 GPU MPI 流程數 | 1 - 4 |
| 每個節點的最大 GPU 背景工作角色數 | 1 - 4 |
| 作業存留期上限 | 90 天<sup>1</sup> |
| 低優先順序節點上的作業存留期上限 | 7 天<sup>2</sup> |
| 每個節點的最大參數伺服器數 | 1 |

<sup>1</sup> 存留期上限是指執行開始到完成的時間。 已完成的執行會無限期保留；未在存留期上限內完成的執行，其資料將無法存取。
<sup>2</sup> 有容量限制時，低優先順序節點上的作業可能會被佔用。 我們建議您在作業中實作檢查點。

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning 管線
針對 [Azure Machine Learning 管線](concept-ml-pipelines.md)，管線中的步驟數目會有配額限制，而在訂用帳戶中的每個區域上，已發佈管線的排程型執行數目也會有配額限制。
- 管線中允許的步驟數目上限是 30,000 個
- 在每個訂用帳戶上，已發佈管線的排程型執行數目和用於 Blob 觸發排程的 Blob 提取數目的總和上限為每月 100,000 個

> [!NOTE]
> 如果您想要上調此限制，請連絡 [Microsoft 支援服務](https://azure.microsoft.com/support/options/)。

### <a name="container-instances"></a>容器執行個體

針對您在指定期間 (以每小時為範圍) 或整個訂用帳戶中可以啟動的容器執行個體數目也有限制。

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

如需配額限制的詳細資訊與最新清單，請查看[這裡](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits)的全 Azure 配額文章。

### <a name="storage"></a>儲存體
針對每個區域以及指定的訂用帳戶中的儲存體帳戶數目有限制。 預設限制為 250 個，包括標準與進階儲存體帳戶兩者。 如果您在指定區域需要超過 250 個儲存體帳戶，請透過 [Azure 支援](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)提出要求。 Azure 儲存體小組將會檢閱您的商務案例，而且可以針對指定區域核准多達 250 個儲存體帳戶。


## <a name="workspace-level-quota"></a>工作區層級配額

為了更有效地管理各種[工作區](concept-workspace.md)之間的 Azure Machine Learning Compute 目標 (Amlcompute) 資源配置，我們引進了一項功能，可讓您分配訂用帳戶層級配額 (依據 VM 系列)，並在工作區層級進行設定。 預設行為是所有工作區的配額與任何 VM 系列的訂用帳戶層級配額相同。 不過，隨著工作區數目的增加，以及優先順序不同的工作負載會開始共用相同資源，使用者需要更好的容量共用方式，以避免資源爭用問題。 Azure Machine Learning 可透過其受控計算供應項目來提供解決方案，讓使用者在每個工作區上，為特定 VM 系列設定配額上限。 這類似於在工作區之間分配您的容量，而使用者也可以選擇利用過度配置來驅動最大使用率。 

若要在工作區層級上設定配額，請移至訂用帳戶中的任何工作區，然後按一下左側窗格中的 [使用量 + 配額]。 然後選取 [設定配額] 索引標籤來查看配額，然後展開任何 VM 系列，在該 VM 系列底下列出的任何工作區上設定配額限制。 請記住，您不能設定負值或高於訂用帳戶層級配額的值。 此外，如您所見，預設中會將整個訂用帳戶配額指派給所有工作區，以允許完整使用配置的配額。

[![Azure Machine Learning 工作區層級配額](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> 這只是企業版功能。 如果您的訂用帳戶中同時有[基本版和企業版](overview-what-is-azure-ml.md#sku)工作區，您只能使用此功能來設定企業版工作區的配額。 您的基本版工作區會繼續擁有訂用帳戶層級配額，這是預設行為。
>
> 您需要訂用帳戶層級的權限，才能在工作區層級上設定配額。 這是強制執行的設定，如此一來，個別的工作區擁有者就不會編輯或增加其配額，也不會開始侵占為另一個工作區保留的資源。 因此，訂用帳戶管理員最適合在各工作區之間配置和散發這些配額。



## <a name="view-your-usage-and-quotas"></a>檢視使用量和配額

Azure Machine Learning Compute 會與您訂用帳戶中的其他 Azure 資源配額分開管理。 若要查看此配額，您必須向下切入到 Machine Learning 服務。  

1. 在左窗格中選取 [Machine Learning 服務]，然後從顯示的清單中選取任何工作區。

1. 在下一個刀鋒視窗的 [支援與疑難排解] 區段下方，選取 [使用量 + 配額]，以檢視目前配額限制與使用量。

1. 選取訂用帳戶以檢視配額限制。 請記得篩選出您感興趣的區域。

1. 您現在可以在訂用帳戶層級檢視和工作區層級檢視之間切換：
    + **訂用帳戶檢視：** 這可讓您依據 VM 系列來查看核心配額的使用量，然後依工作區來將其展開，並依據實際的叢集名稱進一步展開內容。 此檢視可讓您快速取得特定 VM 系列的核心使用量詳細資料，以查看各工作區的分類項目，以及這每個工作區的基礎叢集細項。 此檢視中的一般慣例是 (使用量/配額)，其中使用量是目前已擴增的核心數目，而配額是資源可調整的核心數目邏輯上限。 針對每個**工作區**，配額都是工作區層級配額 (如上面所述)，代表您可以針對特定 VM 系列擴充的核心數目上限。 同樣地，針對**叢集**，配額實際上就是核心，與叢集可調整的節點數目上限相對應，如 max_nodes 屬性所定義。

    + **工作區檢視：** 這可讓您依據工作區來查看核心配額的使用量，然後依 VM 系列來將其展開，並依據實際的叢集名稱進一步展開內容。 此檢視可讓您快速取得特定工作區的核心使用量詳細資料，以查看各 VM 系列的分類項目，以及這每個 VM 系列的基礎叢集細項。

透過 Azure 入口網站檢視其他各種 Azure 資源 (例如虛擬機器、儲存體、網路) 的配額非常簡單。

1. 在左窗格中，選取 [所有服務]，然後選取 [一般] 類別底下的 [訂用帳戶]。

1. 從訂用帳戶清單中，選取您要尋找其配額的訂用帳戶。

## <a name="request-quota-increases"></a>要求增加配額

如果您想要將限制或配額提升到預設限制以上，您可以免費[建立線上客戶支援要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)。

您無法將限制提升到高於下表中所示的上限值。 如果沒有上限，資源即沒有可調整的限制。 [請參閱逐步指示，了解如何增加您的配額](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)。

要求增加配額時，您必須選取您要求提升配額的服務，這可以是 Machine Learning 服務配額、容器執行個體或儲存體的配額。 除了 Azure Machine Learning Compute 以外，您也可以在依照前述步驟檢視配額時按一下 [要求配額] 按鈕。

> [!NOTE]
> [免費試用訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0044p)無法增加限制或配額。 如果您有[免費試用訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0044p)，則可以升級到[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)訂用帳戶。 如需詳細資訊，請參閱[將 Azure 免費試用升級至隨用隨付](../billing/billing-upgrade-azure-subscription.md)和[免費試用訂用帳戶常見問題集](https://azure.microsoft.com/free/free-account-faq)。

## <a name="next-steps"></a>後續步驟

請透過下列文章來深入了解：

+ [規劃與管理 Azure Machine Learning 的成本](concept-plan-manage-cost.md)

+ [如何增加您的配額](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)。
