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
ms.openlocfilehash: 231b6ffa3730721d4e44ecb15c2fc58591b80178
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92314805"
---
# <a name="troubleshoot-azure-load-balancer"></a>針對 Azure Load Balancer 進行疑難排解

此頁面提供基本和標準 Azure Load Balancer 常見問題的疑難排解資訊。 如需標準 Load Balancer 的詳細資訊，請參閱[標準 Load Balancer 概觀](load-balancer-standard-diagnostics.md)。

當負載平衡器的連線無法使用時，最常見的徵兆如下︰ 

- 負載平衡器後方的 VM 未回應健康狀態探查 
- 負載平衡器後方的 VM 未回應設定連接埠的流量

當後端 VM 的外部用戶端通過負載平衡器時，用戶端的 IP 位址將會用於通訊。 請確定用戶端的 IP 位址已新增至 NSG 允許清單。 

## <a name="symptom-no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>徵兆：沒有來自標準內部負載平衡器 (ILB) 的輸出連線能力

**驗證和解決方式**

標準 Ilb **預設是安全**的。 基本 Ilb 允許 *透過隱藏* 的公用 IP 位址連接到網際網路。 這不會針對生產工作負載建議您使用，因為 IP 位址不是靜態，也不會透過您所擁有的 Nsg 鎖定。 如果您最近從基本 ILB 移至標準 ILB，您應該透過 [僅限輸出](egress-only.md) 的設定（透過 NSG 鎖定 IP）來明確建立公用 IP。 

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>徵兆：負載平衡器後方的 VM 未回應健康狀態探查
如果後端伺服器要參與負載平衡器集合，就必須通過探查檢查。 如需健康狀態探查的詳細資訊，請參閱[了解負載平衡器探查](load-balancer-custom-probe-overview.md)。 

負載平衡器後端集區 VM 可能因為下列任何一個原因未回應探查︰ 
- 負載平衡器後端集區 VM 的健康狀態不良 
- 負載平衡器後端集區 VM 未接聽探查連接埠 
- 防火牆或網路安全性群組封鎖負載平衡器後端集區 VM 上的連接埠 
- 負載平衡器中的其他設定錯誤

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>原因 1：負載平衡器後端集區 VM 的健康狀態不良 

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
* 此外，檢查 VM 之 NIC 上或子網路上的 [全部拒絕] 網路安全性群組規則的優先順序是否高於允許 LB 探查與流量的預設規則 (網路安全性群組必須允許 168.63.129.16 的負載平衡器 IP)。 
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
        - 如果後端集區 VM 上觀察不到任何傳出封包，則必須檢查 VM 是否有任何相關問題 (例如，應用程式封鎖探查連接埠)。 
    - 確認探查封包是否在到達負載平衡器之前被強制傳送到另一個目的地 (可能是透過 UDR 設定)。 這可能會導致流量永遠都無法到達後端 VM。 
* 變更探查類型 (例如，將 HTTP 變更為 TCP)，並在網路安全性群組 ACL 和防火牆中設定對應的連接埠以驗證探查回應的組態是否有問題。 如需健康狀態探查組態的詳細資訊，請參閱[端點負載平衡健康狀態探查組態 (英文)](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/)。

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>徵兆：負載平衡器後方的 VM 未回應所設定資料連接埠上的流量

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
3. 如果連接埠未列為 "LISTENING" 狀態，請設定正確的接聽程式連接埠。 
4. 如果連接埠標示為 LISTENING，則請檢查該連接埠上的目標應用程式是否有任何可能的問題。

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>原因 2：網路安全性群組封鎖負載平衡器後端集區 VM 上的連接埠  

如果在子網路或 VM 上設定的一或多個網路安全性群組封鎖來源 IP 或連接埠，VM 就無法回應。

對於公用負載平衡器，網際網路用戶端的 IP 位址將會用於用戶端與負載平衡器後端 VM 之間的通訊。 請確定後端 VM 的網路安全性群組中允許用戶端的 IP 位址。

1. 列出在後端 VM 上設定的網路安全性群組。 如需詳細資訊，請參閱[管理網路安全性群組](../virtual-network/manage-network-security-group.md)
1. 從網路安全性群組的清單中，檢查︰
    - 資料連接埠上的傳入或傳出流量是否有干擾。 
    - VM 之 NIC 上或子網路上的 [全部拒絕] 網路安全性群組規則的優先順序是否高於允許負載平衡器探查與流量的預設規則 (網路安全性群組必須允許 168.63.129.16 的負載平衡器 IP，亦即探查連接埠)。
1. 如果有任何規則封鎖流量，請移除並重新設定這些規則以允許資料流量。  
1. 測試 VM 現在是否已開始回應健康狀態探查。

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>原因 3：從相同的 VM 和網路介面存取負載平衡器 

如果負載平衡器之後端 VM 中裝載的應用程式嘗試透過相同的網路介面存取相同後端 VM 中裝載的另一個應用程式，這是不支援的案例，因此將會失敗。 

**解決方式** 您可以透過下列其中一個方法解決這個問題：
* 每個應用程式都設定個別的後端集區 VM。 
* 在有兩個 NIC 的 VM 中設定應用程式，讓每個應用程式使用自己的網路介面和 IP 位址。 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>原因 4：從參與的負載平衡器後端集區 VM 存取內部負載平衡器前端

如果在 VNet 內設定內部 Load Balancer，且其中一個參與的後端 VM 嘗試存取內部 Load Balancer 前端，則當流程對應至原始 VM 時，會發生失敗。 不支援此狀況。

**解決方式** 有數種方式可為此案例排除障礙，包括使用 Proxy。 請評估使用應用程式閘道或其他第三方 Proxy (例如 nginx 或 haproxy)。 如需應用程式閘道的詳細資訊，請參閱[應用程式閘道的概觀](../application-gateway/application-gateway-introduction.md)

**詳細資料**內部 Load Balancer 不會將輸出起始連線轉譯為內部 負載平衡器的前端，因為這兩者都位於私人 IP 位址空間中。 公用 Load Balancer 提供從虛擬網路內的私人 IP 位址到公用 IP 位址的[輸出連線](load-balancer-outbound-connections.md)。 對於內部 Load Balancer，此方法可避免在無需轉譯的專屬內部 IP 位址空間中將 SNAT 連接埠耗盡。

其副作用是，如果後端集區 VM 的輸出流程嘗試將流量流程至其集區中的內部 Load Balancer 前端，並且對應回本身，則這兩個流程互不相符。 由於兩者不相符，流程將會失敗。 如果流程未對應回後端集區中建立前端流程的相同 VM，流程就會成功。

當流程對應回本身時，傳出流程似乎是來自 VM 而傳至前端，而對應的傳入流程似乎來自 VM 至而傳至本身。 以客體作業系統來看，相同流程的傳入和傳出部分在虛擬機器內部不相符。 TCP 堆疊無法將其中半數的流程視為相同流程的一部分。 來源與目的地不相符。 當流程對應至後端集區中的任何其他 VM 時，半數流程將會相符，而 VM 就能回應流程。

此案例的徵兆是當流量回到其源自的相同後端時，發生間歇性連線逾時。 常見的因應措施包括在內部 Load Balancer 後方插入 Proxy 層，以及使用伺服器直接回傳 (DSR) 樣式規則。 如需詳細資訊，請參閱[多個 Azure Load Balancer 前端](load-balancer-multivip-overview.md)。

您可以結合內部 Load Balancer 與任何第三方 Proxy，或將內部[應用程式閘道](../application-gateway/application-gateway-introduction.md)用於使用 HTTP/HTTPS 的 Proxy 案例。 儘管可以使用公用 Load Balancer 來緩解此問題，但產生的案例容易造成 [SNAT 耗盡](load-balancer-outbound-connections.md)。 除非謹慎管理，否則請避免使用此替代方法。

## <a name="symptom-cannot-change-backend-port-for-existing-lb-rule-of-a-load-balancer-which-has-vm-scale-set-deployed-in-the-backend-pool"></a>徵兆：無法針對已在後端集區中部署 VM 擴展集的負載平衡器，變更其現有 LB 規則的後端連接埠。 
### <a name="cause--the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-vm-scale-set"></a>原因：在健康情況探查針對 VM 擴展集參考的負載平衡器所使用的負載平衡規則中，無法修改後端連接埠。
**解決方法** 若要變更連接埠，您可藉由更新 VM 擴展集來移除健康情況探查、更新連接埠，然後再次設定健康情況探查。

## <a name="symptom-small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>徵兆：從負載平衡器的後端集區中移除 VM 之後，少量流量仍會經過負載平衡器。 
### <a name="cause--vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>原因：從後端集區移除的 VM 應該不會再接收流量。 少量的網路流量可能與 Azure 中的儲存體、DNS 和其他功能相關。 
若要確認，您可以進行網路追蹤。 您的 Blob 儲存體帳戶所使用的 FQDN 會列在每個儲存體帳戶的屬性內。  從您的 Azure 訂用帳戶內的虛擬機器，您可以執行 nslookup 來判斷指派給該儲存體帳戶的 Azure IP。

## <a name="additional-network-captures"></a>其他網路擷取
如果您決定開啟支援案例，請收集下列資訊以便更快解決問題。 選擇單一的後端 VM 來執行下列測試︰
- 從 VNet 內的其中一個後端 VM 使用 Psping 測試探查連接埠回應 (範例︰psping 10.0.0.4:3389) 並記錄結果。 
- 如果這些 ping 測試沒有收到任何回應，請在執行 PsPing 時對後端 VM 和 VNet 測試 VM 同時執行 Netsh trace，然後停止 Netsh trace。 
 
## <a name="symptom-load-balancer-in-failed-state"></a>徵兆： Load Balancer 處於失敗狀態 

**解決方法**

- 一旦您識別處於失敗狀態的資源，請移至 [Azure 資源總管](https://resources.azure.com/) 並識別處於此狀態的資源。 
- 將右上角的切換開關更新為 [讀取/寫入]。
- 針對處於失敗狀態的資源，按一下 [編輯]。
- 依序按一下 PUT 和 GET，以確定布建狀態已更新為 [成功]。
- 然後，您可以繼續進行其他動作，因為資源已不處於失敗狀態。


## <a name="next-steps"></a>後續步驟

如果上述步驟無法解決問題，請開啟 [支援票證](https://azure.microsoft.com/support/options/)。

