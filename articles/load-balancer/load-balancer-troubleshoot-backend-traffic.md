---
title: 針對 Azure Load Balancer 進行疑難排解
description: 了解如何針對 Azure Load Balancer 的已知問題進行疑難排解。
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: bfe2b21a86f2ce4b4630ba69cde87796fd367f4b
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98029176"
---
# <a name="troubleshoot-azure-load-balancer-backend-traffic-responses"></a>針對 Azure Load Balancer 後端流量回應進行疑難排解

本頁面提供 Azure Load Balancer 問題的疑難排解資訊。

## <a name="vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>負載平衡器後方的 VM 未回應所設定資料連接埠上的流量

如果後端集區 VM 列為健康狀態良好，而且會回應健康狀態探查，但仍未參與負載平衡，或未回應資料流量，可能是因為下列其中一個原因︰
* 負載平衡器後端集區 VM 未接聽資料連接埠
* 網路安全性群組封鎖負載平衡器後端集區 VM 上的連接埠  
* 從相同的 VM 和 NIC 存取負載平衡器
* 從參與的負載平衡器後端集區 VM 存取網際網路負載平衡器前端

## <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>原因 1：負載平衡器後端集區 VM 未接聽資料連接埠

如果 VM 未回應資料流量，則可能是因為參與的 VM 上未開啟目標連接埠，或 VM 未接聽該連接埠。 

**驗證和解決方式**

1. 登入後端 VM。 
2. 開啟命令提示字元並執行下列命令以驗證有應用程式正在接聽資料連接埠︰  
            netstat -an 
3. 如果連接埠未列為 "LISTENING" 狀態，請設定正確的接聽程式連接埠。 
4. 如果連接埠標示為 LISTENING，則請檢查該連接埠上的目標應用程式是否有任何可能的問題。

## <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>原因 2：網路安全性群組封鎖負載平衡器後端集區 VM 上的連接埠  

如果在子網路或 VM 上設定的一或多個網路安全性群組封鎖來源 IP 或連接埠，VM 就無法回應。

對於公用負載平衡器，網際網路用戶端的 IP 位址將會用於用戶端與負載平衡器後端 VM 之間的通訊。 請確定後端 VM 的網路安全性群組中允許用戶端的 IP 位址。

1. 列出在後端 VM 上設定的網路安全性群組。 如需詳細資訊，請參閱[管理網路安全性群組](../virtual-network/manage-network-security-group.md)
1. 從網路安全性群組的清單中，檢查︰
    - 資料連接埠上的傳入或傳出流量是否有干擾。 
    - VM 之 NIC 上或子網路上的 [全部拒絕] 網路安全性群組規則的優先順序是否高於允許負載平衡器探查與流量的預設規則 (網路安全性群組必須允許 168.63.129.16 的負載平衡器 IP，亦即探查連接埠)。
1. 如果有任何規則封鎖流量，請移除並重新設定這些規則以允許資料流量。  
1. 測試 VM 現在是否已開始回應健康狀態探查。

## <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>原因 3：從相同的 VM 和網路介面存取負載平衡器 

如果負載平衡器之後端 VM 中裝載的應用程式嘗試透過相同的網路介面存取相同後端 VM 中裝載的另一個應用程式，這是不支援的案例，因此將會失敗。 

**解決方式** 您可以透過下列其中一個方法解決這個問題：
* 每個應用程式都設定個別的後端集區 VM。 
* 在有兩個 NIC 的 VM 中設定應用程式，讓每個應用程式使用自己的網路介面和 IP 位址。 

## <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>原因 4：從參與的負載平衡器後端集區 VM 存取內部負載平衡器前端

如果在 VNet 內設定內部 Load Balancer，且其中一個參與的後端 VM 嘗試存取內部 Load Balancer 前端，則當流程對應至原始 VM 時，會發生失敗。 不支援此狀況。

**解決方式** 有數種方式可為此案例排除障礙，包括使用 Proxy。 請評估使用應用程式閘道或其他第三方 Proxy (例如 nginx 或 haproxy)。 如需應用程式閘道的詳細資訊，請參閱[應用程式閘道的概觀](../application-gateway/overview.md)

**詳細資料** 內部 Load Balancer 不會將輸出起始連線轉譯為內部 負載平衡器的前端，因為這兩者都位於私人 IP 位址空間中。 公用 Load Balancer 提供從虛擬網路內的私人 IP 位址到公用 IP 位址的[輸出連線](load-balancer-outbound-connections.md)。 對於內部 Load Balancer，此方法可避免在無需轉譯的專屬內部 IP 位址空間中將 SNAT 連接埠耗盡。

其副作用是，如果後端集區 VM 的輸出流程嘗試將流量流程至其集區中的內部 Load Balancer 前端，並且對應回本身，則這兩個流程互不相符。 由於兩者不相符，流程將會失敗。 如果流程未對應回後端集區中建立前端流程的相同 VM，流程就會成功。

當流程對應回本身時，傳出流程似乎是來自 VM 而傳至前端，而對應的傳入流程似乎來自 VM 至而傳至本身。 以客體作業系統來看，相同流程的傳入和傳出部分在虛擬機器內部不相符。 TCP 堆疊無法將其中半數的流程視為相同流程的一部分。 來源與目的地不相符。 當流程對應至後端集區中的任何其他 VM 時，半數流程將會相符，而 VM 就能回應流程。

此案例的徵兆是當流量回到其源自的相同後端時，發生間歇性連線逾時。 常見的因應措施包括在內部 Load Balancer 後方插入 Proxy 層，以及使用伺服器直接回傳 (DSR) 樣式規則。 如需詳細資訊，請參閱[多個 Azure Load Balancer 前端](load-balancer-multivip-overview.md)。

您可以結合內部 Load Balancer 與任何第三方 Proxy，或將內部[應用程式閘道](../application-gateway/overview.md)用於使用 HTTP/HTTPS 的 Proxy 案例。 儘管可以使用公用 Load Balancer 來緩解此問題，但產生的案例容易造成 [SNAT 耗盡](load-balancer-outbound-connections.md)。 除非謹慎管理，否則請避免使用此替代方法。

## <a name="next-steps"></a>後續步驟

如果上述步驟無法解決問題，請開啟 [支援票證](https://azure.microsoft.com/support/options/)。