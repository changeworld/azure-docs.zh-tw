---
title: Azure 虛擬機器網路輸送量 | Microsoft Docs
description: 了解 Azure 虛擬機器網路輸送量。
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: steveesp
ms.reviewer: kumud, mareat
ms.openlocfilehash: 47f58b25b082784177910d14ab95d8d242fda71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245430"
---
# <a name="virtual-machine-network-bandwidth"></a>虛擬機器網路頻寬

Azure 提供各種 VM 大小和類型，各有不同的效能功能組合。 其中一個功能是網路輸送量 (或頻寬)，測量單位是 MB/秒 (Mbps)。 因為虛擬機器裝載於共用硬體，網路容量必須平均分給共用相同硬體的虛擬機器。 相對於較小的虛擬機器，大型的虛擬機器會配置較多的頻寬。
 
配置給每個虛擬機器的網路頻寬是依據從虛擬機器的流出 (輸出) 流量來計量。 所有離開虛擬機器的網路流量都會計算到配置的限制，不會因為目的地不同而有差異。 例如，如果虛擬機器有 1,000 Mbps 的限制，則無論輸出流量的目的地是相同虛擬網路中的另一部虛擬機器，或是 Azure 之外的虛擬機器，皆會套用該限制。
 
輸入則不會直接計量或限制。 不過，還有其他因素，例如 CPU 和儲存體限制可能會影響虛擬機器處理內送資料的能力。

加速網路是設計來改善網路效能 (包括延遲、輸送量和 CPU 使用率) 的功能。 雖然加速網路可提升虛擬機器的輸送量，但不能超過虛擬機器的配置頻寬。 若要深入了解加速網路，請參閱適用於 [Windows](create-vm-accelerated-networking-powershell.md) 或 [Linux](create-vm-accelerated-networking-cli.md) 虛擬機器的＜加速網路＞。
 
Azure 虛擬機器必須連結一個 (但可以有數個) 網路介面。 配置給虛擬機器的頻寬，是虛擬機器上所有網路介面的所有輸出流量總和。 換句話說，配置的頻寬是以每個虛擬機器為依據，跟連結多少個網路介面到虛擬機器無關。 若要了解不同 Azure 虛擬機器大小支援的網路介面有多少，請參閱 Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器大小。 

## <a name="expected-network-throughput"></a>預期的網路輸送量

預期的輸出輸送量和每個虛擬機器大小支援的網路介面數目，皆詳細列在 Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器大小中。 選取類型，例如一般用途，然後在產生的頁面上選取大小的系列，例如 Dv2 系列。 每個數列都有資料表，且最後一個資料行包含網路規格，標題為**最大 NIC 數 / 預期的網路效能 (Mbps)**。 

輸送量限制會套用至虛擬機器。 輸送量不會受到下列因素影響：
- **網路介面數目**：頻寬限制是虛擬機器所有輸出流量的累計。
- **加速網路**：雖然此功能有助於達到已發佈的限制，但不會變更限制。
- **流量目的地**：所有目的地都會計入輸出限制。
- **通訊協定**：通過所有通訊協定的所有輸出流量都會計入限制。

## <a name="network-flow-limits"></a>網路流量限制

除了頻寬之外，任何給定時間 VM 上存在的網路連接數可能會影響其網路性能。 Azure 網路堆疊在稱為"流"的資料結構中維護 TCP/UDP 連接的每個方向的狀態。 典型的 TCP/UDP 連接將創建 2 個流，一個用於入站，另一個用於出站方向。 

端點之間的資料傳輸除了需要創建多個流外，還需要創建多個流。 以下是為 DNS 解析度創建的流，以及為負載等化器運行狀況探測器創建的流。 另請注意，網路虛擬裝置 （NVA）（如閘道、代理、防火牆）將看到為設備終止並由設備啟動的連接創建流。 

![通過轉發設備進行 TCP 對話的流量計數](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>流量限制和建議

如今，Azure 網路堆疊支援 250K 網路流，對於 CPU 內核大於 8 的 VM 和 100k 總流具有良好性能的 VM，對於 CPU 內核少於 8 的 VM，性能良好。 超過此限制的網路性能會正常降低，因為額外的流高達 500K 總流量、250K 入站和 250K 出站硬限制，之後將丟棄其他流。

||具有 <8 個 CPU 內核的 VM|具有 8 個 CPU 內核的 VM|
|---|---|---|
|<b>良好的性能</b>|100K 流量 |250K 流量|
|<b>性能下降</b>|超過 100k 流量|超過 250K 流量|
|<b>流量限制</b>|500K 流量|500K 流量|

在[Azure 監視器](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)中提供指標，用於跟蹤 VM 或 VMSS 實例上的網路流數和流創建速率。

![azure 監視器-流度量.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

連接建立和終止率也會影響網路性能，因為連接建立和終止將 CPU 與資料包處理常式共用。 我們建議您根據預期的流量模式對工作負載進行基準測試，並適當擴展工作負載，以滿足您的性能需求。 

## <a name="next-steps"></a>後續步驟

- [最佳化虛擬機器作業系統的網路輸送量](virtual-network-optimize-network-bandwidth.md)
- 針對虛擬機器，[測試網路輸送量](virtual-network-bandwidth-testing.md)。
