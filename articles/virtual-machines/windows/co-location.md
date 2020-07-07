---
title: 共置 Vm 以改善延遲
description: 瞭解如何共同尋找 Azure VM 資源，以改善延遲。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 066b6d10dbe07b902abfd15565c5ccf1a5e9c115
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82083167"
---
# <a name="co-locate-resource-for-improved-latency"></a>共同尋找資源以改善延遲

在 Azure 中部署您的應用程式時，將實例分散到不同區域或可用性區域會建立網路延遲，這可能會影響應用程式的整體效能。 


## <a name="proximity-placement-groups"></a>鄰近位置群組 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>後續步驟

使用 Azure PowerShell 將 VM 部署至[鄰近放置群組](proximity-placement-groups.md)。

瞭解如何[測試網路延遲](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

瞭解如何[優化網路輸送量](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。  

瞭解如何搭配[SAP 應用程式使用鄰近放置群組](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。