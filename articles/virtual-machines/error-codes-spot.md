---
title: 適用于 Azure 點 Vm 和擴展集實例的錯誤碼
description: 深入瞭解使用點 Vm 和擴展集實例時可能會看到的錯誤代碼。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: e5e621cc3763cfa7fe28790baf2f5d9866c8d618
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069792"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>適用于點 Vm 和擴展集的錯誤訊息

以下是您在使用「點 Vm」和「擴展集」時可能會收到的一些可能的錯誤代碼。


| 答案 | 訊息 | 描述 |
|-----|---------|-------------|
| SkuNotAvailable | 在訂用帳戶 ' ' 的位置 ' ' 中，資源 ' ' 的要求層 \<resource\> 目前無法使用 \<location\> \<subscriptionID\> 。 請嘗試另一層，或部署到不同的位置。 | 此位置中沒有足夠的 Azure 點容量可建立您的 VM 或擴展集實例。 |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  只能在 Azure 點虛擬機器上設定收回原則。 | 此 VM 不是點 VM，因此您無法設定收回原則。 |
| AzureSpotVMNotSupportedInAvailabilitySet  |  可用性設定組中不支援 Azure 點虛擬機器。 | 您必須選擇使用點 VM，或在可用性設定組中使用 VM，而不能選擇兩者。 |
| AzureSpotFeatureNotEnabledForSubscription  |  未啟用 Azure 位置功能的訂用帳戶。 | 使用支援點 Vm 的訂用帳戶。 |
| VMPriorityCannotBeApplied  |  無法將指定的優先順序值 ' {0} ' 套用至虛擬機器 ' {1} '，因為建立虛擬機器時未指定任何優先順序。 | 指定建立 VM 時的優先順序。 |
| SpotPriceGreaterThanProvidedMaxPrice  |  無法執行作業 ' {0} '，因為所提供的最大價格 ' {1} 美元 ' 低於 {2} Azure 點 VM 大小 ' ' 的目前位置價格 ' 美元 ' {3} 。 | 選取較高的最大價格。 如需詳細資訊，請參閱[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)或[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的定價資訊。|
| MaxPriceValueInvalid  |  不正確最大價格值。 最大價格的唯一支援值為-1 或大於零的十進位數。 最大價格值-1 表示 Azure 點虛擬機器因價格原因而無法收回。 | 請輸入有效的最大價格。 如需詳細資訊，請參閱[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)或[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的定價。 |
| MaxPriceChangeNotAllowedForAllocatedVMs | 目前已配置 VM ' ' 時，不允許最大價格變更 {0} 。 請解除配置，然後再試一次。 | Stop\Deallocate VM，讓您可以變更最大價格。 |
| MaxPriceChangeNotAllowed | 不允許最大價格變更。 | 您無法變更此 VM 的最大價格。 |
| AzureSpotIsNotSupportedForThisAPIVersion  |  此 API 版本不支援 Azure 位置。 | API 版本必須是2019-03-01。 |
| AzureSpotIsNotSupportedForThisVMSize  |  此 VM 大小不支援 Azure 位置 {0} 。 | 選取另一個 VM 大小。 如需詳細資訊，請參閱[點虛擬機器](./linux/spot-vms.md)。 |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  只有 Azure 位置虛擬機器支援最大價格。 | 如需詳細資訊，請參閱[點虛擬機器](./linux/spot-vms.md)。 |
| MoveResourcesWithAzureSpotVMNotSupported  |  移動資源要求包含 Azure 點虛擬機器。 目前不支援。 請檢查虛擬機器識別碼的錯誤詳細資料。 | 您無法移動點 Vm。 |
| MoveResourcesWithAzureSpotVmssNotSupported  |  移動資源要求包含 Azure 點虛擬機器擴展集。 目前不支援。 請檢查虛擬機器擴展集識別碼的錯誤詳細資料。 | 您無法移動位置擴展集實例。 |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | 具有 VM 協調流程模式的虛擬機器擴展集不支援 Azure 點虛擬機器。 | 將協調流程模式設定為虛擬機器擴展集，以便使用點實例。 |


**後續步驟**如需詳細資訊，請參閱[點虛擬機器](./linux/spot-vms.md)。