---
title: 可共同定位 VM,提高延遲
description: 瞭解共同定位 Azure VM 資源如何提高延遲。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 066b6d10dbe07b902abfd15565c5ccf1a5e9c115
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083167"
---
# <a name="co-locate-resource-for-improved-latency"></a>共存資源,提高延遲

在 Azure 中部署應用程式時,跨區域或可用性區域展開實例會造成網路延遲,這可能會影響應用程式的整體性能。 


## <a name="proximity-placement-groups"></a>鄰近位置群組 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>後續步驟

使用 Azure PowerShell 將 VM 部署到[鄰近放置群組](proximity-placement-groups.md)。

瞭解如何[測試網路延遲](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

瞭解如何[優化網路輸送量](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。  

瞭解如何[將接近放置群組與 SAP 應用程式使用](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。