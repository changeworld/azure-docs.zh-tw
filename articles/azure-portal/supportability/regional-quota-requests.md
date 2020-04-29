---
title: 要求增加 Azure 區域 vCPU 配額限制
description: 如何要求增加 Azure 入口網站中某個區域的 vCPU 配額限制。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9236989f7bbb4695db24f86a2a049f5bb58f302a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76843679"
---
# <a name="standard-quota-increase-limits-by-region"></a>標準配額：依區域增加限制

Azure Resource Manager 針對虛擬機器支援兩種類型的 vCPU 配額：

* *隨用隨付 vm*和*保留的 vm 實例*受限於*標準的 vCPU 配額*。
* *找出 vm*會受到*點 vCPU 配額*的限制。

隨用隨付和保留的虛擬機器實例的標準 vCPU 配額會針對每個區域中的每個訂用帳戶，在兩個層級強制執行：

* 第一層是所有 VM 系列中的*總區域個 vcpu 限制*。
* 第二層是*每個 VM 的系列個 vcpu 限制*，例如 D 系列個 vcpu。

每當您部署新的點 VM 時，該 VM 系列的新的和現有 vCPU 使用量總計不得超過該特定 VM 系列的已核准 vCPU 配額。 此外，在所有 VM 系列上部署的新和現有個 vcpu 的總數不應超過訂用帳戶的核准區域 vCPU 配額總計。 如果超過上述任一配額，則不允許 VM 部署。

您可以使用 Azure 入口網站，為 VM 系列要求增加 vCPU 配額限制。 VM 系列配額的增加會自動增加相同數量的總區域 vCPU 限制。

當您建立新的訂用帳戶時，區域個 vcpu 的預設總數目可能不等於所有個別 VM 系列的預設 vCPU 配額總計。 這項差異可能會導致訂用帳戶具有足夠的配額，可供您想要部署的每個個別 VM 系列使用。 但是，可能沒有足夠的配額可容納所有部署的總區域個 vcpu。 在此情況下，您必須提交要求，以明確增加區域個 vcpu 總數的限制。 區域 vCPU 限制總計不得超過該區域所有 VM 系列的核准配額總計。

若要深入瞭解標準 vCPU 配額，請參閱[虛擬機器 vCPU 配額](../../virtual-machines/windows/quotas.md)和[Azure 訂用帳戶和服務限制、配額和條件約束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

若要深入瞭解增加點 VM vCPU 限制，請參閱[點配額：增加所有 VM 系列的限制](low-priority-quota.md)。

您可以透過兩種方式的其中一種，要求以區域增加 vCPU 標準配額限制。

## <a name="request-a-quota-increase-by-region-from-help--support"></a>從 [說明 + 支援] 中依區域要求增加配額

若要在 [說明 **+ 支援**] 中依區域要求增加 vCPU 配額：

1. 從 [ [Azure 入口網站](https://portal.azure.com)] 功能表中，選取 [說明 **+ 支援**]。

   ![[說明 + 支援] 連結](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. 在 [說明 **+ 支援**] 中，選取 [**新增支援要求**]。

    ![新增支援要求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. 針對 [問題類型]****，選取 [服務與訂用帳戶限制 (配額)]****。

   ![選取問題類型](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 針對 [**訂**用帳戶]，選取您想要增加其配額的訂用帳戶。

   ![選取一個訂用帳戶](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 針對 [**配額類型**]，選取 [**其他要求**]。

   ![選取配額類型](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. 選取 **[下一步]：** 用來開啟**問題詳細資料**的解決方案。 在 [**描述**] 中，提供下列資訊：

    1. 針對 [**部署模型**]，指定**Resource Manager**。  
    1. 針對 [**區域**]，指定您所需的區域，例如 [**美國東部 2**]。  
    1. 針對 [**新限制**]，為區域指定新的 vCPU 限制。 此值不應超過此訂用帳戶的個別 SKU 系列已核准配額的總和。

    ![輸入配額要求的詳細資料](./media/resource-manager-core-quotas-request/regional-details.png)

1. 選取 [**審查 + 建立**] 繼續建立支援要求。

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>依區域的訂用帳戶要求增加配額

若要依**區域的訂**用帳戶要求增加 vCPU 配額：

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [**訂閱**]。

   ![前往 Azure 入口網站中的 [訂用帳戶]](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 選取您想要增加其配額的訂用帳戶。

   ![選取要修改的訂用帳戶](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 在左窗格中，選取 [**使用量 + 配額**]。

   ![追蹤使用方式和配額連結](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 在右上方，選取 [**要求增加**]。

   ![選取以增加配額](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. 從 [**配額類型**] 選取 [**其他要求**]。

   ![選取配額類型](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. 選取 **[下一步]：** 用來開啟**問題詳細資料**的解決方案。 在 [**描述**] 方塊中，提供下列其他資訊：

    1. 針對 [**部署模型**]，指定**Resource Manager**。  
    1. 針對 [**區域**]，指定您所需的區域，例如 [**美國東部 2**]。  
    1. 針對 [**新限制**]，為區域指定新的 vCPU 限制。 此值不應超過此訂用帳戶的個別 SKU 系列已核准配額的總和。

    ![在詳細資料中輸入資訊](./media/resource-manager-core-quotas-request/regional-details.png)

1. 選取 [**審查 + 建立**] 繼續建立支援要求。
