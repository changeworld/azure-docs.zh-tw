---
title: 共置 Linux Vm
description: 深入瞭解如何將適用于 Linux 的 Azure VM 資源共同定位，以改善延遲。
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 304623ca50fd030ab6e016b940f8be52819c161a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500626"
---
# <a name="co-locate-resources-for-improved-latency"></a>共置資源以改善延遲

在 Azure 中部署應用程式時，跨區域或可用性區域分散實例會建立網路延遲，這可能會影響應用程式的整體效能。 

## <a name="proximity-placement-groups"></a>鄰近位置群組

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>後續步驟

使用 Azure CLI 將 VM 部署至 [鄰近位置群組](proximity-placement-groups.md) 。

瞭解如何 [測試網路延遲](../../virtual-network/virtual-network-test-latency.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

瞭解如何將 [網路輸送量優化](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。  

瞭解如何搭配 [使用鄰近位置群組和 SAP 應用程式](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。