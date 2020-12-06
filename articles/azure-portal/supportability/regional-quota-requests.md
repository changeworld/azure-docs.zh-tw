---
title: 要求增加 Azure 區域 vCPU 配額限制
description: 如何要求增加 Azure 入口網站中區域的 vCPU 配額限制。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: eadf740c6b5caccbf678a1238f993d4ec0b34095
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745412"
---
# <a name="standard-quota-increase-limits-by-region"></a>標準配額：依區域增加限制

Azure Resource Manager 針對虛擬機器支援兩種類型的 vCPU 配額：

* *隨用隨付 vm* 和 *保留的 vm 實例* 受限於 *標準 vCPU 配額*。
* 現成的 *vm* 會受到 *點 vCPU 配額* 的限制。

隨用隨付和保留的虛擬機器實例的標準 vCPU 配額，會針對每個區域中的每個訂用帳戶在兩個層級上強制執行：

* 第一層是跨所有 VM 系列的 *區域個 vcpu 限制總計*。
* 第二層是 *每個 VM 的系列個 vcpu 限制*，例如 D 系列個 vcpu。

每當您部署新的點 VM 時，該 VM 系列的新的和現有的 vCPU 使用量總計不得超過該特定 VM 系列的已核准 vCPU 配額。 此外，在所有 VM 系列上部署的新的和現有的個 vcpu 總數不應超過訂用帳戶的核准區域總 vCPU 配額。 如果超過其中一項配額，則不允許 VM 部署。

您可以使用 Azure 入口網站來要求增加 VM 系列的 vCPU 配額限制。 VM 系列配額的增加會自動增加相同數量的總區域 vCPU 限制。

當您建立新的訂用帳戶時，預設的區域個 vcpu 總數可能不等於所有個別 VM 系列的預設總 vCPU 配額。 這項差異可能會導致每個您想要部署之個別 VM 系列的訂用帳戶具有足夠的配額。 但可能沒有足夠的配額可容納所有部署的區域總個 vcpu。 在此情況下，您必須提交要求以明確增加區域個 vcpu 總數的限制。 區域 vCPU 限制總計不能超過區域所有 VM 系列的核准總配額。

若要深入瞭解標準 vCPU 配額，請參閱 [虛擬機器 vCPU 配額](../../virtual-machines/windows/quotas.md) 和 [Azure 訂用帳戶和服務限制、配額和條件約束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

若要深入瞭解如何增加點 VM vCPU 限制，請參閱「 [找出配額：提高所有 VM 系列的限制](low-priority-quota.md)」。

您可以透過下列兩種方式的其中一種，要求依區域的 vCPU 標準配額限制增加。

## <a name="request-a-quota-increase-by-region-from-help--support"></a>依區域的 [說明 + 支援] 要求增加配額

若要從 [說明 **+ 支援**] 要求區域增加 vCPU 配額：

1. 從 [Azure 入口網站](https://portal.azure.com)功能表中，選取 [説明 + 支援]。

   ![[說明 + 支援] 連結](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. 在 [說明 + 支援] 中，選取 [新增支援要求]。

    ![新增支援要求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. 針對 [ **問題類型**]，選取 [ **服務與訂用帳戶限制] (配額)**。

   ![選取問題類型](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 針對 [ **訂** 用帳戶]，選取您要增加其配額的訂用帳戶。

   ![選取一個訂用帳戶](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 針對 [ **配額類型**]，選取 [ **其他要求**]。

   ![選取配額類型](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. 選取 **[下一步]：** 開啟 **問題詳細資料** 的解決方案。 在 [ **描述**] 中，提供下列資訊：

    1. 針對 **部署模型**，指定 **Resource Manager**。  
    1. 針對 [ **區域**]，指定所需的區域，例如「 **美國東部 2**」。  
    1. 針對 **新的限制**，請指定區域的新 vCPU 限制。 此值不應超過此訂用帳戶的個別 SKU 系列之核准配額的總和。

    ![輸入配額要求的詳細資料](./media/resource-manager-core-quotas-request/regional-details.png)

1. 選取 [ **審核 + 建立** ] 以繼續建立支援要求。

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>依區域的訂用帳戶要求增加配額

若 **要從訂用帳戶** 依區域要求增加 vCPU 配額：

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [ **訂閱**]。

   ![移至 Azure 入口網站中的訂用帳戶](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 選取您要增加其配額的訂用帳戶。

   ![選取要修改的訂用帳戶](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 在左窗格中，選取 [ **使用量 + 配額**]。

   ![遵循使用量和配額連結](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 在右上方，選取 [ **要求增加**]。

   ![選取以增加配額](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. 在 [ **配額類型**] 中，選取 [ **其他要求**]。

   ![選取配額類型](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. 選取 **[下一步]：** 開啟 **問題詳細資料** 的解決方案。 在 [ **描述** ] 方塊中，提供下列其他資訊：

    1. 針對 **部署模型**，指定 **Resource Manager**。  
    1. 針對 [ **區域**]，指定所需的區域，例如「 **美國東部 2**」。  
    1. 針對 **新的限制**，請指定區域的新 vCPU 限制。 此值不應超過此訂用帳戶的個別 SKU 系列之核准配額的總和。

    ![在詳細資料中輸入資訊](./media/resource-manager-core-quotas-request/regional-details.png)

1. 選取 [ **審核 + 建立** ] 以繼續建立支援要求。
