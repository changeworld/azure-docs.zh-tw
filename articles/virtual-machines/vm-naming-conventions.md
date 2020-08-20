---
title: Azure VM 大小命名慣例
description: 說明用於 Azure VM 大小的命名慣例
ms.service: virtual-machines
subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.date: 7/22/2020
ms.author: mimckitt
ms.custom: sttsinar
ms.openlocfilehash: 13894e534dc8d6dd89baf75ea2bd3b6500b718f7
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650956"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Azure 虛擬機器大小命名慣例

此頁面概述 Azure Vm 所使用的命名慣例。 Vm 會使用這些命名慣例來表示不同的功能和規格。

## <a name="naming-convention-explanation"></a>命名慣例說明

**[系列]**  + **[子系列 *]**  + **[# Of 個 vcpu]**  + **[加法特徵]**  + **[加速器類型 *]**  + **[版本]**

|值 | 說明|
|---|---|
| 系列 | 表示 VM 系列系列| 
| * 子系列 | 僅用於特製化 VM 差異|
| # of 個 vcpu| 代表 VM 的個 vcpu 數目 |
| 加法特徵 | 一或多個小寫字母表示附加功能，例如： <br> a = 以 AMD 為基礎的處理器 <br> d = 磁片 (本機暫存磁片存在) ;這適用于較新的 Azure Vm，請參閱 [Ddv4 和 Ddsv4 系列](./ddv4-ddsv4-series.md) <br> h = 休眠功能 <br> i = 隔離大小 <br> l = 低記憶體;記憶體數量較高的記憶體數量 <br> m = 耗用海量儲存體;特定大小的最大記憶體數量 <br> t = 小型記憶體;特定大小的最小記憶體數量 <br> r = 支援 RDMA <br> s = 進階儲存體的功能，包括可能使用的 [ULTRA SSD](./disks-types.md#ultra-disk) (附注：某些較新的大小若沒有的屬性，仍可支援進階儲存體例如 M128、m64 可使用等） ) <br> |
| * 加速器類型 | 代表特製化/GPU Sku 中的硬體加速器類型。 只有從2020年第3季啟動的新特製化/GPU Sku 會在名稱中包含硬體加速器。 |
| 版本 | 代表 VM 系列系列的版本 |

## <a name="example-breakdown"></a>範例細目

**[系列]**  + **[子系列 *]**  + **[# Of 個 vcpu]**  + **[加法特徵]**  + **[加速器類型 *]**  + **[版本]**

### <a name="example-1-m416ms_v2"></a>範例1： M416ms_v2

|值 | 說明|
|---|---|
| 系列 | M | 
| # of 個 vcpu | 416 |
| 加法特徵 | m = 耗用海量儲存體 <br> s = 進階儲存體支援 |
| 版本 | v2 |

### <a name="example-2-nv16as_v4"></a>範例2： NV16as_v4

|值 | 說明|
|---|---|
| 系列 | N | 
| 子系列 | V |
| # of 個 vcpu | 16 |
| 加法特徵 | a = 以 AMD 為基礎的處理器 <br> s = 進階儲存體支援 |
| 版本 | v4 |

### <a name="example-3-nc4as_t4_v3"></a>範例3： NC4as_T4_v3

|值 | 說明|
|---|---|
| 系列 | N | 
| 子系列 | C |
| # of 個 vcpu | 4 |
| 加法特徵 | a = 以 AMD 為基礎的處理器 <br> s = 進階儲存體支援 |
| 加速器類型 | T4 |
| 版本 | v3 |

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 中可用的 [VM 大小](./sizes.md) 。 