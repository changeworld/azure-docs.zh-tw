---
title: 要求每個 Azure VM 系列的 vCPU 配額限制增加
description: 如何在 Azure 入口網站中要求增加 VM 系列的 vCPU 配額限制，這會增加相同數量的總區域 vCPU 限制。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e00b1cf11b906390335f257b20787d394b3a8ed4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85117160"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>標準配額：調高 VM 系列的限制

Azure Resource Manager 針對虛擬機器支援兩種類型的 vCPU 配額：

* *隨用隨付 vm* 和 *保留的 vm 實例* 受限於 *標準 vCPU 配額*。
* 現成的*vm*會受到*點 vCPU 配額*的限制。

隨用隨付和保留的虛擬機器實例的標準 vCPU 配額，會針對每個區域中的每個訂用帳戶在兩個層級上強制執行：

* 第一層是跨所有 VM 系列的 *區域個 vcpu 限制總計*。
* 第二層是 *每個 VM 的系列個 vcpu 限制*，例如 Dv3 系列個 vcpu。

當您部署新的點 VM 時，所有點 VM 實例的新的和現有的 vCPU 使用量總計不得超過核准的點 vCPU 配額限制。 如果超過了位置配額，則不允許點 VM 部署。

您可以使用 Azure 入口網站來要求增加 VM 系列的 vCPU 配額限制。 VM 系列配額的增加會自動增加相同數量的總區域 vCPU 限制。

若要深入瞭解標準 vCPU 配額，請參閱 [虛擬機器 vCPU 配額](../../virtual-machines/windows/quotas.md) 和 [Azure 訂用帳戶和服務限制](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)。

若要瞭解如何針對標準配額增加依區域的 vCPU 限制，請參閱 [標準配額：依區域增加限制](regional-quota-requests.md)。

若要深入瞭解如何增加點 VM vCPU 限制，請參閱「 [找出配額：提高所有 VM 系列的限制](low-priority-quota.md)」。

您可以使用下列兩種方式的其中一種，要求每個 VM 系列的標準 vCPU 配額限制增加（如下列各節所述）。

## <a name="request-a-standard-quota-increase-from-help--support"></a>從 [說明 + 支援] 要求增加標準配額

若要從 [說明 **+ 支援**] 要求每個 VM 系列的標準 vCPU 配額增加：

> [!NOTE]
> 您也可以透過單一支援案例，要求增加多個區域的配額限制。 如需詳細資訊，請參閱步驟8。

1. 在 [Azure 入口網站](https://portal.azure.com)功能表上，選取 [説明 + 支援]。

   ![[説明 + 支援] 連結](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. 在 [說明 + 支援] 中，選取 [新增支援要求]。

    ![建立新的支援要求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. 針對 [ **問題類型**]，選取 [ **服務與訂用帳戶限制] (配額) **。

   ![選取問題類型](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 針對 [ **訂**用帳戶]，選取您要增加其配額的訂用帳戶。

   ![選取提高配額的訂用帳戶](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 針對 [ **配額類型**]，選取 [ **計算-VM (核心-個 vcpu) 訂用帳戶限制增加**]。

   ![選取配額類型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 選取 **[下一步]：** 開啟 **問題詳細資料**的解決方案。 選取 [ **提供詳細資料** ] 以輸入其他資訊。

   ![「提供詳細資料」連結](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. 在 [ **配額詳細資料**] 中，執行下列步驟：

   ![TProvide 其他配額詳細資料](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. 針對 **部署模型**，選取適當的模型。

   1. 針對 [ **位置**]，選取位置。 針對選取的位置，在 [ **類型**] 下的 [ **選取類型**] 中，選擇 [ **標準**]。

      ![配額詳細資料-配額類型](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      在 [ **類型**] 下，您可以透過多重選取支援，從單一支援案例要求標準和點配額類型。

      如需增加位置配額限制的詳細資訊，請參閱 [適用于虛擬機器擴展集的 Azure 現成 vm](../../virtual-machine-scale-sets/use-spot.md)。

   1. 在 [ **標準**] 中，選取要增加配額的 SKU 系列。

      ![配額詳細資料-SKU 系列](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. 輸入您想要用於此訂用帳戶的新配額限制。 若要從清單中移除 SKU，請清除 SKU 旁的核取方塊，或選取捨棄 "X" 圖示。

      ![選取新的 vCPU 限制](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 若要為一個以上的位置要求增加配額，請在 [ **位置**] 中選取其他位置，然後選取適當的 VM 類型。 然後，您可以輸入適用于其他位置的限制。

   ![在配額詳細資料中指定其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 選取 [ **儲存並繼續** ] 以繼續建立支援要求。

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>要求從訂用帳戶增加標準配額

若要從訂用帳戶要求每個 VM 系列 **的標準**vCPU 配額增加：

> [!NOTE]
> 您也可以透過單一支援案例，要求增加多個區域的配額限制。 如需詳細資訊，請參閱步驟7。

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [ **訂閱**]。

   ![Azure 入口網站搜尋中的訂閱](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 選取您要增加其配額的訂用帳戶。

   ![要選取變更的訂閱](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 在左窗格中，選取 [ **使用量 + 配額**]。

   ![[使用量 + 配額] 連結](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 在右上方，選取 [ **要求增加**]。

   ![選取以增加配額](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. 針對 [ **配額類型**]，選取 [ **計算-VM (核心-個 vcpu) 訂用帳戶限制增加**]。

   ![選取配額類型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 在 [ **配額詳細資料**] 中，執行下列步驟：

   1. 針對 [ **部署模型**] 選取適當的模型，並針對 [ **位置**] 選取位置。

      ![提供配額詳細資料](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. 針對選取的位置，在 [ **類型**] 下，選取 [ **選取類型**]，然後選取 [ **標準**]。

      ![選取標準類型](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      在 [ **類型**] 下，您可以透過多重選取支援，從單一支援案例要求標準和點配額類型。

      如需增加位置配額限制的詳細資訊，請參閱 [適用于虛擬機器擴展集的 Azure 現成 vm](../../virtual-machine-scale-sets/use-spot.md)。

   1. 針對 [ **標準**]，選取您要增加其配額的 SKU 系列。

      ![配額詳細資料-SKU 系列](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. 輸入您想要用於此訂用帳戶的新配額限制。 若要從清單中移除 SKU，請取消選取 SKU 旁的核取方塊，或選取捨棄 "X" 圖示。

      ![選取新的 vCPU 限制](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 若要為一個以上的位置要求增加配額，請在 [ **位置**] 中選取其他位置，然後選取適當的 VM 類型。

   此步驟會預先載入您為先前的位置選取的 SKU 系列。 輸入您想要套用至其他數列的配額限制。

   ![在配額詳細資料中選取其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 選取 [ **儲存並繼續** ] 以繼續建立支援要求。
