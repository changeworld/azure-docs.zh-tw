---
title: 針對 Azure Load Balancer 健康情況探查狀態進行疑難排解
description: 瞭解如何針對 Azure Load Balancer 健康情況探查狀態的已知問題進行疑難排解。
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
ms.date: 12/02/2020
ms.author: allensu
ms.openlocfilehash: 28823c997cd974d5061829df88680ed52075caa0
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98029182"
---
# <a name="troubleshoot-azure-load-balancer-health-probe-status"></a>針對 Azure Load Balancer 健康情況探查狀態進行疑難排解

此頁面提供常見 Azure Load Balancer 健康情況探查問題的疑難排解資訊。

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
2. 開啟命令提示字元並執行下列命令，以驗證有應用程式正在接聽探查埠： netstat-a
3. 如果連接埠狀態未列為 **LISTENING**，請設定適當的連接埠。 
4. 或者，選取其他已列為 **LISTENING** 的連接埠，並據此更新負載平衡器組態。

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>原因 3：防火牆或網路安全性群組封鎖負載平衡器後端集區 VM 上的連接埠
如果 VM 上的防火牆封鎖探查連接埠，或者子網路或 VM 上已設定一或多個網路安全性群組不允許探查連接埠，VM 就無法回應健康狀態探查。

**驗證和解決方式**

1. 如果已啟用防火牆，請檢查它是否已設定為允許探查連接埠。 如果不允許，請將防火牆設定為允許探查連接埠上的流量，然後重新測試。
2. 從網路安全性群組的清單中，檢查探查連接埠上的傳入或傳出流量是否有干擾。
3. 此外，檢查 VM 之 NIC 上或子網路上的 [全部拒絕] 網路安全性群組規則的優先順序是否高於允許 LB 探查與流量的預設規則 (網路安全性群組必須允許 168.63.129.16 的負載平衡器 IP)。
4. 如果有任何規則封鎖探查流量，請移除並重新設定規則以允許探查流量。  
5. 測試 VM 現在是否已開始回應健康狀態探查。

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>原因 4：負載平衡器中的其他設定錯誤
如果上述所有原因都已驗證且正確解決，但後端 VM 仍然沒有回應健康狀態探查，則請手動測試連線，並收集一些追蹤資料以了解連線狀況。

**驗證和解決方式**

1. 從 VNet 內的其中一個其他 VM 使用 **Psping** 測試探查連接埠回應 (範例︰.\psping.exe -t 10.0.0.4:3389) 並記錄結果。 
2. 從 VNet 內的其中一個其他 VM 使用 **TCPing** 測試探查連接埠回應 (範例︰.\tcping.exe 10.0.0.4 3389) 並記錄結果。 
3. 如果這些 ping 測試都沒有收到任何回應，則
    - 在目標後端集區 VM 上以及從相同 VNet 的另一個測試 VM 同時執行 Netsh trace。 現在，執行 PsPing 測試一段時間、收集一些網路追蹤資料，然後停止測試。 
    - 分析網路擷取，並查看是否有與 ping 查詢相關的傳入和傳出封包。 
        - 如果後端集區 VM 上觀察不到任何傳入封包，則可能是網路安全性群組或 UDR 設定錯誤而封鎖流量。 
        - 如果後端集區 VM 上觀察不到任何傳出封包，則必須檢查 VM 是否有任何相關問題 (例如，應用程式封鎖探查連接埠)。 
    - 確認探查封包是否在到達負載平衡器之前被強制傳送到另一個目的地 (可能是透過 UDR 設定)。 這可能會導致流量永遠都無法到達後端 VM。 
4. 變更探查類型 (例如，將 HTTP 變更為 TCP)，並在網路安全性群組 ACL 和防火牆中設定對應的連接埠以驗證探查回應的組態是否有問題。 如需健康狀態探查組態的詳細資訊，請參閱[端點負載平衡健康狀態探查組態 (英文)](/archive/blogs/mast/endpoint-load-balancing-heath-probe-configuration-details)。

## <a name="next-steps"></a>後續步驟

如果上述步驟無法解決問題，請開啟 [支援票證](https://azure.microsoft.com/support/options/)。