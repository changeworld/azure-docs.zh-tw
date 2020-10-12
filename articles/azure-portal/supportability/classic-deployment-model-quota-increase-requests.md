---
title: Azure 傳統部署模型
description: 傳統部署模型現在已由 Resource Manager 模型取代，會強制執行 Vm 和虛擬機器擴展集的全域 vCPU 配額限制。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: afdb8b2b677321239f240e74d49711195ac186c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763902"
---
# <a name="classic-deployment-model"></a>傳統部署模型

傳統部署模型是舊版的 Azure 部署模型。 它會強制執行虛擬機器和虛擬機器擴展集的全域 vCPU 配額限制。 不再建議使用傳統部署模型，而且現在已由 Resource Manager 模型取代。

若要深入瞭解這兩種部署模型，以及使用 Resource Manager 的優點，請參閱 [Resource Manager 和傳統部署](../../azure-resource-manager/management/deployment-models.md)。

建立新的訂用帳戶時，會指派預設的個 vcpu 配額。 每當有新的虛擬機器要使用傳統部署模型進行部署時，在所有區域中新的和現有 vCPU 使用量的總和不得超過針對傳統部署模型核准的 vCPU 配額。

若要深入瞭解配額，請參閱 [Azure 訂用帳戶和服務限制、配額和條件約束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

您可以要求增加傳統部署模型的 vCPU 配額限制。 使用 Azure 入口網站中的 [說明 **+ 支援** ] 或 [ **使用量 + 配額** ]。

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>使用 [說明 + 支援]，依訂用帳戶層級的每個 VM 系列要求增加 vCPU 配額

依照下列指示，使用 Azure 入口網站中的 [說明 **+ 支援** ] 來建立支援要求。

1. 從 [Azure 入口網站](https://portal.azure.com)功能表中，選取 [説明 + 支援]。

   ![選取 Azure 入口網站中的 [說明 + 支援]](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. 選取 [新增支援要求]。

   ![在 Azure 入口網站中建立新的支援要求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. 在 [ **問題類型**] 中，選擇 [ **服務與訂用帳戶限制] (配額) **。

   ![選取 [配額] 作為問題類型](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 選取您要增加其配額的訂用帳戶。

   ![選取要增加配額的訂用帳戶](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 針對 [ **配額類型**]，選取 [ **計算-VM (核心-個 vcpu) 訂用帳戶限制增加**]。

   ![選取要增加的配額類型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 選取 **[下一步]：** 開啟 **問題詳細資料**的解決方案。 選取 [ **提供詳細資料** ] 以提供其他資訊。

   ![提供詳細資料以協助您提出要求](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. 在 [ **配額詳細資料**] 中，選取 [ **傳統** ] 並選取 **位置**。

   ![新增詳細資料，包括部署模型和位置](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. 針對 **sku 系列**，請選取一或多個要增加的 sku 系列。

   ![指定要增加的 SKU 系列](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. 輸入您想要對訂用帳戶採取的新限制。 若要移除行，請取消選取 **sku 系列** 中的 sku，或選取捨棄 "X" 圖示。 在您為每個 SKU 系列輸入配額之後，請選取 [ **儲存並繼續** ] **配額詳細資料** ，以繼續進行支援要求。

   ![要求新的限制](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>使用使用量 + 配額在訂用帳戶層級增加每個 VM 系列的要求 vCPU 配額

依照下列指示，使用 Azure 入口網站中的 **使用量 + 配額** 來建立支援要求。

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [ **訂閱**]。

   ![移至 Azure 入口網站中的訂用帳戶](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 選取您要增加其配額的訂用帳戶。

   ![選取要修改的訂用帳戶](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 選取 [ **使用量 + 配額**]。

   ![選取訂用帳戶的使用量和配額](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 選取右上角的 [ **要求增加**]。

   ![選取以增加配額](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. 選取 **計算-VM (核心-個 vcpu) 訂用帳戶限制增加** 為 **配額類型**。

   ![選取配額類型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 選取 **[下一步]：** 開啟 **問題詳細資料**的解決方案。 選取 [ **提供詳細資料** ] 以提供其他資訊。

   ![為您的要求提供詳細資料](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. 在 [ **配額詳細資料**] 中，選取 [ **傳統** ] 和 [ **位置**]。

   ![選取配額詳細資料，包括部署模型和位置](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. 選取一或多個 SKU 系列以增加。

   ![選取要增加的 SKU 系列](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. 輸入您想要對訂用帳戶採取的新限制。 若要移除行，請取消選取 **sku 系列** 中的 sku，或選取捨棄 "X" 圖示。 在您為每個 SKU 系列輸入配額之後，請選取 [ **儲存並繼續** ] **配額詳細資料** ，以繼續進行支援要求。

   ![輸入新的配額](./media/resource-manager-core-quotas-request/new-limits-classic.png)

