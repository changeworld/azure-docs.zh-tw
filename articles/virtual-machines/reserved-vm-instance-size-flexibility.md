---
title: 虛擬機器大小彈性-Azure 保留的 VM 執行個體
description: 了解當您使用預訂的 VM 執行個體時，預訂折扣適用何種大小系列。
author: manish-shukla01
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/03/2018
ms.author: manshuk
ms.openlocfilehash: 381023a0650b078a74c01ff7b698aebacb256147
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88855513"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>利用保留 VM 執行個體獲得虛擬機器大小彈性

當您購買保留的 VM 實例時，您可以選擇針對實例大小彈性或容量優先順序進行優化。 如需有關為保留的 VM 實例設定或變更 [優化] 設定的詳細資訊，請參閱 [變更保留的 vm 實例的優化設定](../cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)。

使用已針對實例大小彈性進行優化的保留虛擬機器實例，您購買的保留可套用至虛擬機器 (Vm) 大小調整為相同的實例大小彈性群組。 例如，如果您針對 DSv2 系列中所列的 VM 大小（例如 Standard_DS5_v2）購買保留，保留折扣可以套用至相同實例大小彈性群組中列出的其他四個大小：

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

但保留折扣不適用於不同實例大小彈性群組中所列的 Vm 大小，例如 DSv2 系列中的 Sku 高記憶體： Standard_DS11_v2、Standard_DS12_v2 等等。

在 [實例大小彈性] 群組中，保留折扣適用的 Vm 數目取決於您購買保留時所選擇的 VM 大小。 此外，也取決於您執行中 VM 的大小。 比率資料行會比較該實例大小彈性群組中每個 VM 大小的相對使用量。 請使用比例值來計算保留項目折扣適用於您執行中 VM 的程度。

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

我們會保留檔案 URL 和架構的修正，讓您可以用程式設計的方式取用此檔案。 資料也會很快透過 API 提供。

## <a name="next-steps"></a>接下來的步驟

如需詳細資訊，請參閱 [什麼是 Azure 保留](../cost-management-billing/reservations/save-compute-costs-reservations.md)專案。
