---
title: 教學課程 - 使用建議來降低 Azure 成本
description: 本教學課程可在您針對最佳化建議採取行動時協助您降低 Azure 成本。
author: bandersmsft
ms.author: banders
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 28f9369d044cf9b2713cb2f2ad42f744e517918b
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683278"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>教學課程：透過建議最佳化成本

Azure 成本管理可搭配 Azure Advisor，提供成本最佳化建議。 Azure Advisor 可協助您透過識別閒置及使用量過低的資源來最佳化及改善效率。 本教學課程會引導您完成範例，識別使用量過低的 Azure 資源，並讓您採取動作以降低成本。

觀看[在 Azure 成本管理中最佳化雲端投資](https://www.youtube.com/watch?v=cSNPoAb-TNc)影片，深入了解如何使用 Advisor 將您的成本最佳化。 若要觀看其他影片，請造訪[成本管理 YouTube 頻道](https://www.youtube.com/c/AzureCostManagement)。

>[!VIDEO https://www.youtube.com/embed/cSNPoAb-TNc]

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 檢視成本最佳化建議以檢視潛在的使用效率不足
> * 針對建議採取行動，將虛擬機器大小調整至更符合成本效益的選項
> * 驗證動作，確保虛擬機器的大小已成功調整

## <a name="prerequisites"></a>Prerequisites
建議適用於各種範圍和 Azure 帳戶類型。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](understand-cost-mgt-data.md)。 您必須至少具備一或多個下列範圍的讀取存取，才能檢視成本資料。 如需有關範圍的詳細資訊，請參閱[了解並使用範圍](understand-work-scopes.md)。

- 訂用帳戶
- 資源群組

如果您有新的訂用帳戶，您無法立即使用成本管理功能。 最多可能需要48小時的時間，才能使用所有的成本管理功能。 此外，您還必須擁有使用中的虛擬機器，且其至少需要活動 14 天。

## <a name="sign-in-to-azure"></a>登入 Azure
登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com/)。

## <a name="view-cost-optimization-recommendations"></a>檢視成本最佳化建議

若要檢視訂用帳戶的成本最佳化建議，請在 Azure 入口網站中開啟所需的範圍，然後選取 [Advisor 建議]  。

若要檢視管理群組的建議，請在 Azure 入口網站中開啟所需的範圍，然後在功能表中選取 [成本分析]  。 使用 [範圍]  框切換至不同範圍，例如管理群組。 選取功能表中的 [Advisor 建議]  。 如需有關範圍的詳細資訊，請參閱[了解並使用範圍](understand-work-scopes.md)。

![顯示在 Azure 入口網站中的成本管理 Advisor 建議](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

建議清單會識別使用效率不足的情況，或是顯示能協助您節省額外成本的購買建議。 合計 [潛在每年節省成本]  會顯示關機或解除配置您所有符合建議規則的 VM 時，所能節省的總金額。 若您不想要將它們關機，建議您考慮將其大小調整至較不昂貴的 VM SKU。

[影響]  類別與 [潛在每年節省成本]  旨在協助您識別具有潛力，可以盡量節省成本的建議。

有高度影響的建議包括：
- [購買保留執行個體以省下較隨用隨付多的成本](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [關閉使用量過低的執行個體，或者重新調整其大小或，從而將虛擬機器的費用最佳化](../../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [使用標準儲存體來儲存受控磁碟快照集](../../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

有中度影響的建議包括：
- [刪除失敗的 Azure Data Factory 管線](../../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [移除未佈建的 ExpressRoute 線路來降低成本](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [藉由刪除或重新設定閒置虛擬網路閘道來降低成本](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>針對建議採取行動

Azure Advisor 可監視過去 7 天的虛擬機器使用量，並找出使用量過低的虛擬機器。 CPU 使用量小於 (含) 5% 且網路使用量小於 (含) 7 MB 長達 4 天 (含) 以上的虛擬機器，將會視為低使用量虛擬機器。

5% 以下 (含) 的 CPU 使用量設定為預設，但您可以調整設定。 如需調整設定的詳細資訊，請參閱[針對低使用量虛擬機器建議設定平均 CPU 使用量規則](../../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation)。

雖然根據設計，某些案例可能會導致低使用量，但您通常可以藉由將虛擬機器的大小變更為較不昂貴的大小，來節省費用。 若您選擇調整大小動作，則您實際節省的成本可能會有所不同。 讓我們逐步完成調整虛擬機器大小的範例。

在建議清單中，按一下 [調整大小或關閉使用量過低的虛擬機器]  建議。 在虛擬機器候選項目清單中，選擇要調整大小的虛擬機器，然後按一下虛擬機器。 隨即顯示虛擬機器的詳細資料，讓您可以確認使用量計量。 [潛在每年節省成本]  值是關機或移除 VM 時您所能節省的金額。 調整 VM 的大小可能可以節省成本，但是您將無法節省 [潛在每年節省成本] 的完整金額。

![建議詳細資料的範例](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

在 VM 詳細資料中，檢查虛擬機器的使用量，確認其為適合的調整大小候選項目。

![顯示歷史使用率的範例 VM 詳細資料](./media/tutorial-acm-opt-recommendations/vm-details.png)

請注意目前虛擬機器的大小。 在確認虛擬機器的大小應該進行調整之後，請關閉 VM 詳細資料，讓您可以查看虛擬機器清單。

在要關閉或調整大小的候選項目清單中，選取 [從 &lt;FromVirtualMachineSKU&gt;  調整大小為 &lt;ToVirtualMachineSKU&gt;   ]。
![使用選項調整虛擬機器大小的範例建議](./media/tutorial-acm-opt-recommendations/resize-vm.png)

接下來，您會看到一份可用的調整大小選項清單。 選擇可為您帶來最佳效能，並且可讓案例最符合成本效益的選項。 在下列範例中，所選擇的選項會從 **Standard_D8s_v3** 調整大小為 **Standard_D2s_v3**。

![可供您選擇大小的可用 VM 大小範例清單](./media/tutorial-acm-opt-recommendations/choose-size.png)

在您選擇適合的大小後，按一下 [調整大小]  以開始調整大小動作。

調整大小需要重新開機正在使用中的執行中虛擬機器。 若虛擬機器位於生產環境，我們建議您在上班時間結束之後再執行調整大小作業。 排程重新開機可減少因暫時無法使用而導致的中斷。

## <a name="verify-the-action"></a>驗證動作

當 VM 成功完成調整大小之後，隨即顯示 Azure 通知。

![已成功調整大小的虛擬機器通知](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 檢視成本最佳化建議以檢視潛在的使用效率不足
> * 針對建議採取行動，將虛擬機器大小調整至更符合成本效益的選項
> * 驗證動作，確保虛擬機器的大小已成功調整

若您尚未閱讀成本管理最佳做法一文，則它可提供高層級指導及協助管理成本時需要考慮的準則。

> [!div class="nextstepaction"]
> [成本管理最佳做法](cost-mgt-best-practices.md)
