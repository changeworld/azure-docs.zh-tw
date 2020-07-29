---
title: 找出虛擬機器配額-Azure
description: 增加點 Vm 的配額限制，以提供 Azure 使用量模型，讓您在 exchange 中取得較低的成本，讓 Azure 視需要移除 Vm。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 513bde930f9c7c8892164c50c3866ff6e948ed4e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763868"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>點配額：增加所有 VM 系列的限制

點虛擬機器（Vm）提供不同的 Azure 使用量模型。 它們可讓您在 exchange 中承擔較低的成本，讓 Azure 在隨用隨付或保留的 VM 實例部署時，視需要移除虛擬機器。 如需有關如何找出 Vm 的詳細資訊，請參閱[虛擬機器擴展集的 Azure 位置 vm](../../virtual-machine-scale-sets/use-spot.md)。

Azure Resource Manager 針對虛擬機器支援兩種類型的 vCPU 配額：

* *隨用隨付 vm*和*保留的 vm 實例*受限於*標準的 vCPU 配額*。
* *找出 vm*會受到*點 vCPU 配額*的限制。

對於 [點 vCPU] 配額類型，Resource Manager vCPU 配額會在所有可用的虛擬機器系列中強制執行，做為單一地區限制。

每當您部署新的點 VM 時，所有點 VM 實例的新的和現有的 vCPU 使用量，都不得超過已核准的點 vCPU 配額限制。 如果超過了位置配額，則不允許點 VM 部署。

本文討論如何使用 Azure 入口網站來要求增加點 vCPU 配額限制。

若要深入瞭解標準 vCPU 配額，請參閱[虛擬機器 vCPU 配額](../../virtual-machines/windows/quotas.md)和[Azure 訂用帳戶和服務限制、配額和條件約束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

若要瞭解如何依區域增加 vCPU 限制，請參閱[標準配額：依區域增加限制](regional-quota-requests.md)。

## <a name="request-a-quota-limit-increase-from-help--support"></a>要求從說明 + 支援增加配額限制

若要使用 [說明 **+ 支援**] 來要求所有虛擬機器系列的位置配額限制增加：

> [!NOTE]
> 您也可以透過單一支援案例，要求增加多個區域的配額限制。 如需詳細資訊，請參閱步驟8。

1. 從 [Azure 入口網站](https://portal.azure.com)功能表中，選取 [説明 + 支援]。

   ![[説明 + 支援] 連結](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. 在 [說明 + 支援] 中，選取 [新增支援要求]。

    ![建立新的支援要求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. 針對 [**問題類型**]，選取 **[服務和訂用帳戶限制（配額）**]。

   ![選取問題類型](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 針對 [**訂**用帳戶]，選取您想要增加其配額的訂用帳戶。

   ![選取訂用帳戶以增加配額](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 針對 [**配額類型**]，選取 **[計算-VM （核心-個 vcpu）訂用帳戶限制增加**]。

   ![選取配額類型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 選取 **[下一步]：** 用來開啟**問題詳細資料**的解決方案。 選取 [**提供詳細資料**] 以輸入其他資訊。

   ![「提供詳細資料」連結](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. 在 [**配額詳細資料**] 中，執行下列步驟：

   1. 針對 [**部署模型**] 選取適當的模型，然後在 [**位置**] 中選取一個位置。

      ![提供額外的配額詳細資料](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. 針對選取的位置，在 [**類型**] 底下的 [**選取類型**] 中，選擇 [**點**]。

      ![選取位置類型](./media/resource-manager-core-quotas-request/select-spot-type.png)

       在 [**類型**] 底下，您可以透過多重選取支援，從單一支援案例要求標準和點配額類型。

       如需詳細資訊，請參閱[標準配額：依 VM 系列增加限制](per-vm-quota-requests.md)。

   1. 輸入您要用於此訂用帳戶的新配額限制。

      ![為點 VM 選取新的配額](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. 若要要求增加一個以上位置的配額，請在 [**位置**] 中選取其他位置，然後選取適當的 VM 類型。 然後，您可以輸入適用于其他位置的限制。

   ![在配額詳細資料中指定其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 選取 [**儲存並繼續**] 繼續建立支援要求。

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>要求從 [訂用帳戶] 窗格增加配額限制

若要針對 [訂用帳戶] 窗格**中的所有**VM 系列要求增加位置配額限制：

> [!NOTE]
> 您也可以透過單一支援案例，要求增加多個區域的配額限制。 如需詳細資訊，請參閱步驟7。

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [**訂閱**]。

   ![Azure 入口網站搜尋中的訂用帳戶](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 選取您想要增加其配額的訂用帳戶。

   ![選取要進行變更的訂閱](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 在左窗格中，選取 [**使用量 + 配額**]。

   ![[使用量 + 配額] 連結](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 在右上方，選取 [**要求增加**]。

   ![選取以增加配額](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. 針對 [**配額類型**]，選取 **[計算-VM （核心-個 vcpu）訂用帳戶限制增加**]。

   ![選取配額類型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 選取 **[下一步]：** 用來開啟**問題詳細資料**的解決方案。 選取 [**提供詳細資料**] 以輸入其他資訊。 在 [**配額詳細資料**] 中，輸入下列資訊：

   1. 針對 [**部署模型**] 選取適當的模型，然後在 [**位置**] 中選取一個位置。

      ![提供配額詳細資料](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. 針對選取的位置，在 [**類型**] 底下的 [**選取類型**] 中，選擇 [**點**]。

      ![選取位置類型](./media/resource-manager-core-quotas-request/select-spot-type.png)

      如需詳細資訊，請參閱[標準配額：依 VM 系列增加限制](per-vm-quota-requests.md)。

   1. 輸入您要用於此訂用帳戶的新配額限制。

      ![針對 [vCPU 限制] 輸入新的值](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. 若要要求增加一個以上位置的配額，請在 [**位置**] 中選取其他位置，然後選取適當的 VM 類型。 然後，您可以輸入適用于其他位置的限制。

   ![選取配額詳細資料中的其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 選取 [**儲存並繼續**] 繼續建立支援要求。
