---
title: Azure 計算單位的總覽
description: Azure 計算單位概念的總覽。 ACU 提供一種比較各個 Azure SKU 之 CPU 效能的方式。
author: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.reviewer: davberg
ms.openlocfilehash: ba8c3f730897c01c52aaf4499269425426d0b7b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91667175"
---
# <a name="azure-compute-unit-acu"></a>Azure 計算單位 (ACU)

「Azure 計算單位」(ACU) 的概念可提供一種比較各個 Azure SKU 之計算 (CPU) 效能的方法。 這將可協助您輕鬆識別哪個 SKU 最可能符合您的效能需求。 ACU 目前標準化于小型 (Standard_A1) VM 為100，而所有其他 Sku 則表示該 SKU 可執行標準基準測試的速度大約快得多

*ACU 使用「Intel® 渦輪」技術來提高 CPU 頻率及提升效能。  效能提升的程度會依據 VM 大小、工作負載及在相同主機上執行的其他工作負載而有所不同。

**ACU 使用 AMD® Boost 技術來提高 CPU 頻率及提升效能。  效能提升的程度會依據 VM 大小、工作負載及在相同主機上執行的其他工作負載而有所不同。

***超執行緒且能夠執行巢狀虛擬化

> [!IMPORTANT]
> ACU 只是一個指導方針。 您工作負載的結果可能會有所不同。
<br>

| SKU 系列 | ACU \ vCPU | vCPU：核心 |
| --- | --- |---|
| [A0](sizes-previous-gen.md) |50 | 1:1 |
| [A1 - A4](sizes-previous-gen.md) |100 | 1:1 |
| [A5 - A7](sizes-previous-gen.md) |100 | 1:1 |
| [A1_v2 - A8_v2](sizes-general.md) |100 | 1:1 |
| [A2m_v2 - A8m_v2](sizes-general.md) |100 | 1:1 |
| [A8 - A11](sizes-previous-gen.md) |225* | 1:1 |
| [B](sizes-b-series-burstable.md) |不定 | 不定 |
| [D1 - D14](sizes-previous-gen.md) |160-250 | 1:1 |
| [D1_v2 - D15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [DS1 - DS14](sizes-previous-gen.md) |160-250 | 1:1 |
| [DS1_v2 - DS15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Dav4](dav4-dasv4-series.md) |230-260 * * | 2:1 |
| [Dasv4](dav4-dasv4-series.md) |230-260 * * | 2:1 |
| [Dv4](dv4-dsv4-series.md) | 195 - 210 | 2:1\*\*\* |
| [Dsv4](dv4-dsv4-series.md) | 195 - 210 | 2:1\*\*\* |
| [Ddv4](ddv4-ddsv4-series.md) | 195-210 * | 2:1\*\*\* |
| [Ddsv4](ddv4-ddsv4-series.md) | 195-210 * | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Eav4](eav4-easv4-series.md) |230-260 * * | 2:1 |
| [Easv4](eav4-easv4-series.md) | 230-260 * * | 2:1 |
| [Ev4](ev4-esv4-series.md) | 195 - 210 | 2:1\*\*\* |
| [Esv4](ev4-esv4-series.md) | 195 - 210 | 2:1\*\*\* |
| [Edv4](edv4-edsv4-series.md) | 195-210 * | 2:1\*\*\* |
| [Edsv4](edv4-edsv4-series.md) | 195-210 * | 2:1\*\*\* |
| [F2s_v2 - F72s_v2](fsv2-series.md) |195-210 * | 2:1\*\*\* |
| [F1 - F16](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [F1s - F16s](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [G1 - G5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [GS1 - GS5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [H](h-series.md) |290 - 300* | 1:1 |
| [Hb](hb-series.md) |199-216 * * | 1:1 |
| [Hc](hc-series.md) |297-315 * | 1:1 |
| [L4s - L32s](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [L8s_v2 - L80s_v2](lsv2-series.md) |150 - 175** | 2:1 |
| [M](m-series.md) | 160-180 | 2:1\*\*\* |
| [NVv4](nvv4-series.md) |230-260 * * | 2:1 |

以下是有關不同大小的詳細資訊連結：

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [計算最佳化](sizes-compute.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [儲存體最佳化](sizes-storage.md)
