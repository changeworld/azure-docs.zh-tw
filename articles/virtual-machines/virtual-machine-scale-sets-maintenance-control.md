---
title: Azure 虛擬機器擴展集上的 OS 映射升級的維護控制總覽
description: 瞭解如何使用維護控制來控制何時將自動 OS 映射升級推出至您的 Azure 虛擬機器擴展集。
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 55cbc5db534dd1b05f91a24e0104b1f2dc110547
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532605"
---
# <a name="preview-maintenance-control-for-azure-virtual-machine-scale-sets"></a>預覽： Azure 虛擬機器擴展集的維護控制 

使用維護控制來管理虛擬機器擴展集的 [自動 OS 映射升級](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) 。

維護控制可讓您決定何時要透過更簡單且更容易預測的體驗，將更新套用到虛擬機器擴展集中的 OS 磁片。 

維護設定可以跨訂用帳戶和資源群組運作。

整個工作流程可歸結為下列步驟： 
- 建立維護設定。
- 將虛擬機器擴展集與維護設定產生關聯。
- 啟用自動 OS 升級。

> [!IMPORTANT]
> Azure 虛擬機器擴展集上的 OS 映射升級維護控制目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="limitations"></a>限制

- Vm 必須位於擴展集中。
- 使用者必須擁有 **資源參與者** 存取權。
- 維護期間必須有5小時或更長的維護期間。
- 維護設定中的維護週期必須設定為 [天]


## <a name="management-options"></a>管理選項

您可以使用下列任何選項來建立和管理維護設定：

- [Azure PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)


## <a name="next-steps"></a>後續步驟

瞭解如何使用維護控制和 PowerShell 來控制何時將維護套用至您的 Azure 虛擬機器擴展集。

> [!div class="nextstepaction"]
> [使用 PowerShell 的虛擬機器擴展集維護控制](virtual-machine-scale-sets-maintenance-control-powershell.md)
