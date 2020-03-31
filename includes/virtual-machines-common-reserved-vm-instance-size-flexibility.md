---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02-18-2020
ms.openlocfilehash: aebe8f05bd1a3607da6d1153c87490891fc6b299
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471445"
---
購買預留 VM 實例時，可以選擇優化實例大小靈活性或容量優先順序。 有關設置或更改保留 VM 實例的優化設置的詳細資訊，請參閱[更改保留 VM 實例的優化設置](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)。

通過針對實例大小靈活性進行優化的預留虛擬機器實例，您購買的預留可以應用於相同實例大小靈活地組中的虛擬機器 （VM） 大小。 例如，如果您為 DSv2 系列中列出的 VM 大小（如Standard_DS5_v2）購買預留，則預留折扣可以應用於同一實例大小靈活地組中列出的其他四種大小：

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

但是，預訂折扣不適用於在不同實例大小靈活地組中列出的 VM 大小，如 DSv2 系列高記憶體中的 SKU：Standard_DS11_v2、Standard_DS12_v2等。

在實例大小靈活性組中，預留折扣適用的 VM 數量取決於您在購買預留時選擇的 VM 大小。 此外，也取決於您執行中 VM 的大小。 比率列比較該實例大小靈活性組中每個 VM 大小的相對佔用空間。 請使用比例值來計算保留項目折扣適用於您執行中 VM 的程度。

## <a name="examples"></a>範例

下列範例使用 DSv2 系列資料表中的大小和比例。

您購買大小為 Standard_DS4_v2 的保留 VM 執行個體，其相較於該系列中其他大小的比例 (相對使用量) 為 8。

- 案例 1：以 1 的比例執行八個 Standard_DS1_v2 大小的 VM。 保留項目折扣適用於這八個 VM 全部。
- 案例 2：以各為 2 的比例執行兩個 Standard_DS2_v2 大小的 VM。 此外，以 4 的比例執行一個 Standard_DS3_v2 大小的 VM。 總使用量是 2+2+4=8。 因此，保留項目折扣適用於這三個 VM 全部。
- 案例 3：以 16 的比例執行一個 Standard_DS5_v2。 保留項目折扣適用於該 VM 計算費用的一半。

下列各節說明在購買已針對執行個體大小彈性進行最佳化的保留 VM 執行個體時，相同大小系列群組中有哪些大小。

## <a name="instance-size-flexibility-ratio-for-vms"></a>VM 的實例大小靈活性比 

下面的 CSV 具有實例大小靈活性組、ArmSkuName 和比率。  

[實例大小靈活性比](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

我們將保持檔 URL 和架構固定，以便您可以以程式設計方式使用此檔。 資料也將很快通過 API 提供。
