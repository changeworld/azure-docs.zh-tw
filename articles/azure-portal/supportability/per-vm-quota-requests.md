---
title: 針對每個 Azure VM 系列要求增加 vCPU 配額限制
description: 如何要求增加 Azure 入口網站中 VM 系列的 vCPU 配額限制，這會增加相同數量的總地區 vCPU 限制。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: de06375dad5999a29691435317e62585a2ea7f64
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843704"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>標準配額：依 VM 系列增加限制

Azure Resource Manager 針對虛擬機器支援兩種類型的 vCPU 配額：

* *隨用隨付 vm*和*保留的 vm 實例*受限於*標準的 vCPU 配額*。
* *找出 vm*會受到*點 vCPU 配額*的限制。

隨用隨付和保留的虛擬機器實例的標準 vCPU 配額會針對每個區域中的每個訂用帳戶，在兩個層級強制執行：

* 第一層是所有 VM 系列中的*總區域個 vcpu 限制*。
* 第二層是*每個 VM 的系列個 vcpu 限制*，例如 Dv3 系列個 vcpu。

每當您部署新的點 VM 時，該 VM 系列的新的和現有 vCPU 使用量總計不得超過該特定 VM 系列的已核准 vCPU 配額。 此外，在所有 VM 系列上部署的新和現有個 vcpu 的總數不應超過訂用帳戶的核准區域 vCPU 配額總計。 如果超過上述任一配額，則不允許 VM 部署。

您可以使用 Azure 入口網站，為 VM 系列要求增加 vCPU 配額限制。 VM 系列配額的增加會自動增加相同數量的總區域 vCPU 限制。

若要深入瞭解標準 vCPU 配額，請參閱[虛擬機器 vCPU 配額](../../virtual-machines/windows/quotas.md)和[Azure 訂用帳戶和服務限制](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)。

若要瞭解如何針對標準配額增加依區域的 vCPU 限制，請參閱[標準配額：依區域增加限制](regional-quota-requests.md)。

若要深入瞭解增加點 VM vCPU 限制，請參閱[點配額：增加所有 VM 系列的限制](low-priority-quota.md)。

您可以使用兩種方式之一，針對每個 VM 系列要求增加標準 vCPU 配額限制，如下節所述。

## <a name="request-a-standard-quota-increase-from-help--support"></a>要求從說明 + 支援增加標準配額

若要從 [說明 **+ 支援**] 要求每個 VM 系列的標準 vCPU 配額增加：

> [!NOTE]
> 您也可以透過單一支援案例，要求增加多個區域的配額限制。 如需詳細資訊，請參閱步驟8。

1. 在 [ [Azure 入口網站](https://portal.azure.com)] 功能表上，選取 [說明 **+ 支援**]。

   ![[說明 + 支援] 連結](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. 在 [說明 **+ 支援**] 中，選取 [**新增支援要求**]。

    ![建立新的支援要求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. 針對 [問題類型]，選取 [服務與訂用帳戶限制 (配額)]。

   ![選取問題類型](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 針對 [**訂**用帳戶]，選取您想要增加其配額的訂用帳戶。

   ![選取訂用帳戶以增加配額](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 針對 [**配額類型**]，選取 **[計算-VM （核心-個 vcpu）訂用帳戶限制增加**]。

   ![選取配額類型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 選取 **[下一步]：** 用來開啟**問題詳細資料**的解決方案。 選取 [**提供詳細資料**] 以輸入其他資訊。

   ![[提供詳細資料] 連結](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. 在 [**配額詳細資料**] 中，執行下列步驟：

   ![TProvide 其他配額詳細資料](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. 針對 [**部署模型**]，選取適當的模型。

   1. 針對 [**位置**]，選取位置。 針對選取的位置，在 [**類型**] 底下的 [**選取類型**] 中，選擇 [**標準**]。

      ![配額詳細資料-配額類型](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      在 [**類型**] 底下，您可以透過多重選取支援，從單一支援案例要求標準和點配額類型。

      如需增加位置配額限制的詳細資訊，請參閱[虛擬機器擴展集的 Azure 位置 vm](../../virtual-machine-scale-sets/use-spot.md)。

   1. 在 [**標準**] 中，選取用於增加配額的 SKU 系列。

      ![配額詳細資料-SKU 系列](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. 輸入您要用於此訂用帳戶的新配額限制。 若要從清單中移除 SKU，請清除 SKU 旁的核取方塊，或選取 [捨棄 "X"] 圖示。

      ![選取新的 vCPU 限制](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 若要要求增加一個以上位置的配額，請在 [**位置**] 中選取其他位置，然後選取適當的 VM 類型。 然後，您可以輸入適用于其他位置的限制。

   ![在配額詳細資料中指定其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 選取 [**儲存並繼續**] 繼續建立支援要求。

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>要求訂用帳戶的標準配額增加

若要要求**訂用帳戶中每**個 VM 系列的標準 vCPU 配額增加：

> [!NOTE]
> 您也可以透過單一支援案例，要求增加多個區域的配額限制。 如需詳細資訊，請參閱步驟7。

1. 在  [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 **訂閱**。

   ![Azure 入口網站搜尋中的訂用帳戶](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 選取您想要增加其配額的訂用帳戶。

   ![選取要進行變更的訂閱](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 在左窗格中，選取 [**使用量 + 配額**]。

   ![[使用量 + 配額] 連結](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 在右上方，選取 [**要求增加**]。

   ![選取以增加配額](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. 針對 [**配額類型**]，選取 **[計算-VM （核心-個 vcpu）訂用帳戶限制增加**]。

   ![選取配額類型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 在 [**配額詳細資料**] 中，執行下列步驟：

   1. 針對 [**部署模型**] 選取適當的模型，然後在 [**位置**] 中選取一個位置。

      ![提供配額詳細資料](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. 針對選取的位置，在 [**類型**] 底下選取 [**選取類型**]，然後選取 [**標準**]。

      ![選取標準類型](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      在 [**類型**] 底下，您可以透過多重選取支援，從單一支援案例要求標準和點配額類型。

      如需增加位置配額限制的詳細資訊，請參閱[虛擬機器擴展集的 Azure 位置 vm](../../virtual-machine-scale-sets/use-spot.md)。

   1. 針對 [**標準**]，選取您想要增加其配額的 SKU 系列。

      ![配額詳細資料-SKU 系列](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. 輸入您要用於此訂用帳戶的新配額限制。 若要從清單中移除 SKU，請取消選取 SKU 旁的核取方塊，或選取捨棄 "X" 圖示。

      ![選取新的 vCPU 限制](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 若要要求增加一個以上位置的配額，請在 [**位置**] 中選取其他位置，然後選取適當的 VM 類型。

   此步驟會預先載入您為先前的位置選取的 SKU 系列。 輸入您想要套用至其他數列的配額限制。

   ![選取配額詳細資料中的其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 選取 [**儲存並繼續**] 繼續建立支援要求。
