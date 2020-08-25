---
title: 使用入口網站部署 Azure 位置 Vm
description: 如何使用 Azure PowerShell 部署現成的 Vm 以節省成本。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/17/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: ef5fa0aafca1312480f51614a1ba1692c09a13b8
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816570"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>使用 Azure 入口網站部署現成的 Vm

使用現成的 [vm](../spot-vms.md) 可讓您以大幅節省的成本來利用未使用的容量。 Azure 基礎結構會在任何時間點恢復容量，以找出 Vm。 因此，找出 Vm 很適合用來處理中斷的工作負載，例如批次處理作業、開發/測試環境、大型計算工作負載等。

現成 Vm 的定價是根據區域和 SKU 的變數。 如需詳細資訊，請參閱 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定價。 如需設定最大價格的詳細資訊，請參閱 [找出 vm-定價](../spot-vms.md#pricing)。

您可以選擇針對 VM 設定您願意支付的最高價格（每小時）。 您可以使用最多5個小數位數，將位置 VM 的最大價格設定為美元 (USD) 。 例如，值的 `0.05701` 最大價格為每小時 $0.05701 美元。 如果您將最大價格設定為 `-1` ，將不會根據價格來收回 VM。 如果有可用的容量和配額，則 VM 的價格將是標準 VM 的目前價格或價格的價格。

當 VM 收回時，您可以選擇刪除 VM 和基礎磁片，或將 VM 解除配置，以便稍後重新開機。


## <a name="create-the-vm"></a>建立 VM

建立使用點 Vm 的 VM 的程式，與 [快速入門](quick-create-portal.md)中所述的程式相同。 當您部署 VM 時，可以選擇使用 Azure 位置實例。


在 [ **基本** ] 索引標籤的 [ **實例詳細資料** ] 區段中，[ **否** ] 是使用 Azure 位置實例的預設值。

![選擇 [否] 的螢幕擷取畫面，請勿使用 Azure 位置實例](media/spot-portal/no.png)

您可以選取 [ **是]**，區段會展開，而且您可以選擇收回 [類型和收回原則](../spot-vms.md#eviction-policy)。 

![選擇 [是] 的螢幕擷取畫面，請使用 Azure 位置實例](media/spot-portal/yes.png)


## <a name="next-steps"></a>後續步驟

您也可以使用 [PowerShell](spot-powershell.md)來建立點 vm。