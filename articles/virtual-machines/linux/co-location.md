---
title: 共同尋找 Linux Vm
description: 瞭解如何共同尋找 Azure VM 資源，以改善延遲。
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 000c8c8955314060ae9bdedb96b086a509fb3e96
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87368637"
---
# <a name="co-locate-resources-for-improved-latency"></a>共同找出資源以改善延遲

在 Azure 中部署您的應用程式時，將實例分散到不同區域或可用性區域會建立網路延遲，這可能會影響應用程式的整體效能。 

## <a name="proximity-placement-groups"></a>鄰近位置群組

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>後續步驟

使用 Azure CLI 將 VM 部署至[鄰近放置群組](proximity-placement-groups.md)。

瞭解如何[測試網路延遲](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

瞭解如何[優化網路輸送量](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。  

瞭解如何搭配[SAP 應用程式使用鄰近放置群組](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
