---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02-18-2020
ms.openlocfilehash: aebe8f05bd1a3607da6d1153c87490891fc6b299
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77471445"
---
當您購買保留的 VM 實例時，您可以選擇優化實例大小彈性或容量優先順序。 如需設定或變更保留 VM 實例之優化設定的詳細資訊，請參閱[變更保留 vm 實例的優化設定](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)。

使用已針對實例大小彈性進行優化的保留虛擬機器實例，您購買的保留專案可套用至相同實例大小彈性群組中的虛擬機器（Vm）大小。 例如，如果您購買 DSv2 系列中所列 VM 大小的保留，例如 Standard_DS5_v2，保留折扣可以套用至相同實例大小彈性群組中所列的其他四個大小：

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

但保留折扣不適用於不同實例大小彈性群組中所列的 Vm 大小，例如 DSv2 系列高記憶體中的 Sku： Standard_DS11_v2、Standard_DS12_v2 等等。

在 [實例大小彈性] 群組中，保留折扣適用的 Vm 數目取決於您購買保留時所挑選的 VM 大小。 此外，也取決於您執行中 VM 的大小。 [比率] 資料行會比較該實例大小彈性群組中每個 VM 大小的相對使用量。 請使用比例值來計算保留項目折扣適用於您執行中 VM 的程度。

## <a name="examples"></a>範例

下列範例使用 DSv2 系列資料表中的大小和比例。

您購買大小為 Standard_DS4_v2 的保留 VM 執行個體，其相較於該系列中其他大小的比例 (相對使用量) 為 8。

- 案例 1：以 1 的比例執行八個 Standard_DS1_v2 大小的 VM。 保留項目折扣適用於這八個 VM 全部。
- 案例 2：以各為 2 的比例執行兩個 Standard_DS2_v2 大小的 VM。 此外，以 4 的比例執行一個 Standard_DS3_v2 大小的 VM。 總使用量是 2+2+4=8。 因此，保留項目折扣適用於這三個 VM 全部。
- 案例 3：以 16 的比例執行一個 Standard_DS5_v2。 保留項目折扣適用於該 VM 計算費用的一半。

下列各節說明在購買已針對執行個體大小彈性進行最佳化的保留 VM 執行個體時，相同大小系列群組中有哪些大小。

## <a name="instance-size-flexibility-ratio-for-vms"></a>Vm 的實例大小彈性比率 

下列 CSV 具有實例大小彈性群組、ArmSkuName 和比例。  

[實例大小彈性比例](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

我們會保留 [檔案 URL] 和 [架構]，讓您能夠以程式設計方式取用此檔案。 資料也會透過 API 立即提供。
