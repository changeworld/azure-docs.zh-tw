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
ms.openlocfilehash: 2059c6f374e4cd5c2518e2fc0ac0da5858b99825
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131713"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Azure 虛擬機器大小命名慣例

此頁面概述用於 Azure Vm 的命名慣例。 Vm 會使用這些命名慣例來表示不同的功能和規格。

## <a name="naming-convention-explanation"></a>命名慣例說明

**[系列]**  + **[子系列 *]**  + **[# Of 個 vcpu]**  + **[附加功能]**  + **[快速鍵類型 *]**  + **[版本]**

|值 | 說明|
|---|---|
| 標準、基本或實驗性 | 「標準」是指派給所有 GA VM 大小的預設值 | 
| 系列 | 表示 VM 系列系列| 
| * 子系列 | 僅用於特製化 VM differentiations|
| # of 個 vcpu| 代表 VM 的個 vcpu 數目 |
| 附加功能 | 一或多個小寫字母表示附加功能，例如： <br> a = 以 AMD 為基礎的處理器 <br> d = 磁片（本機暫存磁片存在）;這適用于較新的 Azure Vm，請參閱[Ddv4 和 Ddsv4 系列](./ddv4-ddsv4-series.md) <br> h = 休眠功能 <br> i = 已隔離 <br> l = 記憶體不足 <br> m = 記憶體密集 <br> t = 小型記憶體 <br> r = RDMA <br> s = 進階儲存體能力，包括可能使用的[ULTRA SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) （注意：有些較新的大小，但沒有的屬性，仍然可以支援進階儲存體例如 M128、M64 等）<br> |
| * 快速鍵類型 | 表示特製化/GPU Sku 中的硬體加速器類型。 只有從第 2020 3 季啟動的新特製化/GPU Sku，名稱中會有硬體加速器。 |
| 版本 | 表示 VM 系列系列的版本 |

## <a name="example-breakdown"></a>範例細目

**[系列]**  + **[子系列 *]**  + **[# Of 個 vcpu]**  + **[附加功能]**  + **[快速鍵類型 *]**  + **[版本]**

### <a name="example-1-m416ms_v2"></a>範例1： M416ms_v2

|值 | 說明|
|---|---|
| 系列 | M | 
| # of 個 vcpu | 416 |
| 附加功能 | m = 記憶體密集 <br> s = 進階儲存體功能 |
| 版本 | v2 |

### <a name="example-2-nv16as_v4"></a>範例2： NV16as_v4

|值 | 說明|
|---|---|
| 系列 | N | 
| 子系列 | V |
| # of 個 vcpu | 16 |
| 附加功能 | a = 以 AMD 為基礎的處理器 <br> s = 進階儲存體功能 |
| 版本 | v4 |

### <a name="example-3-nc4as_t4_v3"></a>範例3： NC4as_T4_v3

|值 | 說明|
|---|---|
| 系列 | N | 
| 子系列 | C |
| # of 個 vcpu | 4 |
| 附加功能 | a = 以 AMD 為基礎的處理器 <br> s = 進階儲存體功能 |
| 快速鍵類型 | T4 |
| 版本 | v3 |

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 中可用的[VM 大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。 
