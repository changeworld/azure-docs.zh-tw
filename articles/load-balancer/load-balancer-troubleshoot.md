---
title: 針對 Azure Load Balancer 進行疑難排解
description: 瞭解如何對 Azure Load Balancer 的已知問題進行疑難排解。
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: cf9fa48019ab88190175131b27f4a40e29eb5ed0
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801717"
---
# <a name="troubleshoot-azure-load-balancer"></a>針對 Azure Load Balancer 進行疑難排解
<p class="alert is-flex is-primary"><span class="has-padding-left-medium has-padding-top-extra-small"><a class="button is-primary" href="https://azurevirtualsupportagent.services.microsoft.com?content=fb23185b-6c56-d9f1-7ce1-758c978e08e1" target='_blank'> </a></span><span class="has-padding-small">使用虛擬代理程式執行<b>自動化診斷</b>，開始快速解決您的問題。</span><div align="right"> <sub>Privacy Statement</sub>隱私權聲明<span class="has-padding-small"> <a href="https://privacy.microsoft.com/privacystatement" target='_blank'></div></a></span></p>
本頁面提供基本和標準一般 Azure Load Balancer 問題的疑難排解資訊。 如需標準 Load Balancer 的詳細資訊，請參閱[標準 Load Balancer 概觀](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)。

當負載平衡器的連線無法使用時，最常見的徵兆如下︰ 

- 負載平衡器後方的 VM 未回應健康狀態探查 
- 負載平衡器後方的 VM 未回應設定連接埠的流量

當後端 Vm 的外部用戶端通過負載平衡器時，用戶端的 IP 位址將會用於通訊。 請確定用戶端的 IP 位址已新增至 NSG 允許清單。 

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>徵兆：負載平衡器後方的 VM 都沒有回應健康狀態探查
如果後端伺服器要參與負載平衡器集合，就必須通過探查檢查。 如需健康狀態探查的詳細資訊，請參閱[了解負載平衡器探查](load-balancer-custom-probe-overview.md)。 

負載平衡器後端集區 VM 可能因為下列任何一個原因未回應探查︰ 
- 負載平衡器後端集區 VM 的健康狀態不良 
- 負載平衡器後端集區 VM 未接聽探查連接埠 
- 防火牆或網路安全性群組封鎖負載平衡器後端集區 VM 上的連接埠 
- 負載平衡器中的其他設定錯誤

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>原因 1︰負載平衡器後端集區 VM 的健康狀態不良 

**驗證和解決方式**

若要解決此問題，請登入參與的 VM，檢查 VM 的健康狀態是否良好，而且可以從集區中的另一個 VM 回應 **PsPing** 或 **TCPing**。 如果 VM 的健康狀態不良，或無法回應探查，您必須修正問題並且讓 VM 回復健康狀態，VM 才能再參與負載平衡。

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>原因 2：負載平衡器後端集區 VM 未接聽探查連接埠
如果 VM 健康狀態良好，但未回應探查，則其中一個原因可能是參與的 VM 上未開啟探查連接埠，或 VM 未接聽該連接埠。

**驗證和解決方式**

1. 登入後端 VM。 
2. 開啟命令提示字元並執行下列命令以驗證有應用程式正在接聽探查連接埠︰   
            netstat -an
3. 如果連接埠狀態未列為 **LISTENING**，請設定適當的連接埠。 
4. 或者，選取其他已列為 **LISTENING** 的連接埠，並據此更新負載平衡器組態。              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>原因 3：防火牆或網路安全性群組封鎖負載平衡器後端集區 VM 上的連接埠  
如果 VM 上的防火牆封鎖探查連接埠，或者子網路或 VM 上已設定一或多個網路安全性群組不允許探查連接埠，VM 就無法回應健康狀態探查。          

**驗證和解決方式**

* 如果已啟用防火牆，請檢查它是否已設定為允許探查連接埠。 如果不允許，請將防火牆設定為允許探查連接埠上的流量，然後重新測試。 
* 從網路安全性群組的清單中，檢查探查連接埠上的傳入或傳出流量是否有干擾。 
* 此外，檢查 VM 之 NIC 上或子網路上的 [全部拒絕]**** 網路安全性群組規則的優先順序是否高於允許 LB 探查與流量的預設規則 (網路安全性群組必須允許 168.63.129.16 的負載平衡器 IP)。 
* 如果有任何規則封鎖探查流量，請移除並重新設定規則以允許探查流量。  
* 測試 VM 現在是否已開始回應健康狀態探查。 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>原因 4：負載平衡器中的其他設定錯誤
如果上述所有原因都已驗證且正確解決，但後端 VM 仍然沒有回應健康狀態探查，則請手動測試連線，並收集一些追蹤資料以了解連線狀況。

**驗證和解決方式**

* 從 VNet 內的其中一個其他 VM 使用 **Psping** 測試探查連接埠回應 (範例︰.\psping.exe -t 10.0.0.4:3389) 並記錄結果。 
* 從 VNet 內的其中一個其他 VM 使用 **TCPing** 測試探查連接埠回應 (範例︰.\tcping.exe 10.0.0.4 3389) 並記錄結果。 
* 如果這些 ping 測試都沒有收到任何回應，則
    - 在目標後端集區 VM 上以及從相同 VNet 的另一個測試 VM 同時執行 Netsh trace。 現在，執行 PsPing 測試一段時間、收集一些網路追蹤資料，然後停止測試。 
    - 分析網路擷取，並查看是否有與 ping 查詢相關的傳入和傳出封包。 
        - 如果後端集區 VM 上觀察不到任何傳入封包，則可能是網路安全性群組或 UDR 設定錯誤而封鎖流量。 
        - 如果在後端集區 VM 上觀察不到任何輸出封包，則必須檢查 VM 是否有任何不相關的問題（例如，應用程式封鎖探查埠）。 
    - 確認探查封包是否在到達負載平衡器之前被強制傳送到另一個目的地 (可能是透過 UDR 設定)。 這可能會導致流量永遠都無法到達後端 VM。 
* 變更探查類型 (例如，將 HTTP 變更為 TCP)，並在網路安全性群組 ACL 和防火牆中設定對應的連接埠以驗證探查回應的組態是否有問題。 如需健康狀態探查組態的詳細資訊，請參閱[端點負載平衡健康狀態探查組態 (英文)](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/)。

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>徵兆：負載平衡器後方的 VM 未回應設定資料連接埠的流量

如果後端集區 VM 列為健康狀態良好，而且會回應健康狀態探查，但仍未參與負載平衡，或未回應資料流量，可能是因為下列其中一個原因︰ 
* 負載平衡器後端集區 VM 未接聽資料連接埠 
* 網路安全性群組封鎖負載平衡器後端集區 VM 上的連接埠  
* 從相同的 VM 和 NIC 存取負載平衡器 
* 從參與的負載平衡器後端集區 VM 存取網際網路負載平衡器前端 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>原因 1：負載平衡器後端集區 VM 未接聽資料連接埠 
如果 VM 未回應資料流量，則可能是因為參與的 VM 上未開啟目標連接埠，或 VM 未接聽該連接埠。 

**驗證和解決方式**

1. 登入後端 VM。 
2. 開啟命令提示字元並執行下列命令，以驗證是否有應用程式正在接聽資料連接埠︰  netstat -an 
3. 如果埠並未以「正在接聽」狀態列出，請設定適當的接聽程式埠 
4. 如果連接埠標示為 LISTENING，則請檢查該連接埠上的目標應用程式是否有任何可能的問題。

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>原因 2：網路安全性群組封鎖負載平衡器後端集區 VM 上的連接埠  

如果在子網路或 VM 上設定的一或多個網路安全性群組封鎖來源 IP 或連接埠，VM 就無法回應。

對於公用負載平衡器，網際網路用戶端的 IP 位址將會用於用戶端與負載平衡器後端 Vm 之間的通訊。 請確定後端 VM 的網路安全性群組中允許用戶端的 IP 位址。

1. 列出在後端 VM 上設定的網路安全性群組。 如需詳細資訊，請參閱[管理網路安全性群組](../virtual-network/manage-network-security-group.md)
1. 從網路安全性群組的清單中，檢查︰
    - 資料連接埠上的傳入或傳出流量是否有干擾。 
    - VM 之 NIC 上或子網路上的 [全部拒絕]**** 網路安全性群組規則的優先順序是否高於允許負載平衡器探查與流量的預設規則 (網路安全性群組必須允許 168.63.129.16 的負載平衡器 IP，亦即探查連接埠)。
1. 如果有任何規則封鎖流量，請移除並重新設定這些規則以允許資料流量。  
1. 測試 VM 現在是否已開始回應健康狀態探查。

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>原因 3：從相同的 VM 和網路介面存取負載平衡器 

如果負載平衡器之後端 VM 中裝載的應用程式嘗試透過相同的網路介面存取相同後端 VM 中裝載的另一個應用程式，這是不支援的案例，因此將會失敗。 

**解決方式** 您可以透過下列其中一個方法解決這個問題：
* 每個應用程式都設定個別的後端集區 VM。 
* 在有兩個 NIC 的 VM 中設定應用程式，讓每個應用程式使用自己的網路介面和 IP 位址。 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>原因 4：從參與的負載平衡器後端集區 VM 存取內部負載平衡器前端

如果在 VNet 內設定內部 Load Balancer，且其中一個參與的後端 VM 嘗試存取內部 Load Balancer 前端，則當流程對應至原始 VM 時，會發生失敗。 不支援此狀況。 請檢閱[限制](concepts.md#limitations)以取得詳細討論。

**解決方式** 有數種方式可為此案例排除障礙，包括使用 Proxy。 請評估使用應用程式閘道或其他第三方 Proxy (例如 nginx 或 haproxy)。 如需應用程式閘道的詳細資訊，請參閱[應用程式閘道的概觀](../application-gateway/application-gateway-introduction.md)

## <a name="symptom-cannot-change-backend-port-for-existing-lb-rule-of-a-load-balancer-which-has-vm-scale-set-deployed-in-the-backend-pool"></a>徵兆：無法針對已在後端集區中部署 VM 擴展集的負載平衡器，變更其現有 LB 規則的後端埠。 
### <a name="cause--the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-vm-scale-set"></a>原因：無法針對 VM 擴展集所參考的負載平衡器所使用的負載平衡規則，修改後端埠。
**解決**方式若要變更埠，您可以藉由更新 VM 擴展集來移除健康情況探查、更新埠，然後再次設定健康情況探查。

## <a name="additional-network-captures"></a>其他網路擷取
如果您決定開啟支援案例，請收集下列資訊以便更快解決問題。 選擇單一的後端 VM 來執行下列測試︰
- 從 VNet 內的其中一個後端 VM 使用 Psping 測試探查連接埠回應 (範例︰psping 10.0.0.4:3389) 並記錄結果。 
- 如果這些 ping 測試沒有收到任何回應，請在執行 PsPing 時對後端 VM 和 VNet 測試 VM 同時執行 Netsh trace，然後停止 Netsh trace。 
  
## <a name="next-steps"></a>後續步驟

如果上述步驟無法解決問題，請開啟 [支援票證](https://azure.microsoft.com/support/options/)。

