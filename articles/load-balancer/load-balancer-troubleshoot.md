---
title: 針對常見的問題進行疑難排解 Azure Load Balancer
description: 瞭解如何對 Azure Load Balancer 的常見問題進行疑難排解。
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
ms.openlocfilehash: cddaf1bde84d7e60eb59bd4c58c65fa889e06ae3
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028806"
---
# <a name="troubleshoot-azure-load-balancer"></a>針對 Azure Load Balancer 進行疑難排解

此頁面提供基本和標準 Azure Load Balancer 常見問題的疑難排解資訊。 如需標準 Load Balancer 的詳細資訊，請參閱[標準 Load Balancer 概觀](load-balancer-standard-diagnostics.md)。

當負載平衡器的連線無法使用時，最常見的徵兆如下︰

- Load Balancer 背後的 Vm 未回應健康情況探查 
- Load Balancer 後方的 Vm 未回應設定埠上的流量

當後端 VM 的外部用戶端通過負載平衡器時，用戶端的 IP 位址將會用於通訊。 請確定用戶端的 IP 位址已新增至 NSG 允許清單。

## <a name="no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>沒有來自標準內部負載平衡器 (ILB) 的輸出連線能力

**驗證和解決方式**

標準 Ilb **預設是安全** 的。 基本 Ilb 允許 *透過隱藏* 的公用 IP 位址連接到網際網路。 這不建議用於生產工作負載，因為 IP 位址既不是靜態，也不是透過您所擁有的 Nsg 鎖定。 如果您最近從基本 ILB 移至標準 ILB，您應該透過「 [僅限輸出](egress-only.md) 」設定明確建立公用 ip，這會透過 NSG 鎖定 IP。 您也可以使用子網上的 [NAT 閘道](../virtual-network/nat-overview.md) 。

## <a name="cant-change-backend-port-for-existing-lb-rule-of-a-load-balancer-that-has-virtual-machine-scale-set-deployed-in-the-backend-pool"></a>無法針對在後端集區中部署虛擬機器擴展集的負載平衡器，變更其現有 LB 規則的後端埠。

### <a name="cause-the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-virtual-machine-scale-set"></a>原因：無法修改虛擬機器擴展集所參考負載平衡器的健康情況探查所使用的負載平衡規則的後端埠

**解決** 方式為了變更埠，您可以藉由更新虛擬機器擴展集來移除健康情況探查、更新埠，然後再次設定健康情況探查。

## <a name="small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>從負載平衡器的後端集區移除 Vm 之後，小型流量仍會進入負載平衡器。

### <a name="cause-vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>原因：從後端集區移除的 Vm 應該不會再收到流量。 少量的網路流量可能與 Azure 中的儲存體、DNS 和其他功能相關。

若要確認，您可以進行網路追蹤。 您的 Blob 儲存體帳戶所使用的 FQDN 會列在每個儲存體帳戶的屬性內。  您可以從 Azure 訂用帳戶內的虛擬機器，執行 nslookup 來判斷指派給該儲存體帳戶的 Azure IP。

## <a name="additional-network-captures"></a>其他網路擷取

如果您決定開啟支援案例，請收集下列資訊以便更快解決問題。 選擇單一的後端 VM 來執行下列測試︰

- 從 VNet 內的其中一個後端 Vm 使用 ps ping 來測試探查埠回應 (範例： ps ping 10.0.0.4： 3389) 並記錄結果。 
- 如果這些 ping 測試沒有收到任何回應，請在執行 PsPing 時對後端 VM 和 VNet 測試 VM 同時執行 Netsh trace，然後停止 Netsh trace。

## <a name="load-balancer-in-failed-state"></a>處於失敗狀態的 Load Balancer

**解決方法**

- 一旦您識別處於失敗狀態的資源，請移至 [Azure 資源總管](https://resources.azure.com/) 並識別處於此狀態的資源。
- 將右手邊的開關更新為 [讀取/寫入]。
- 針對處於失敗狀態的資源，按一下 [編輯]。
- 依序按一下 PUT 和 GET，以確定布建狀態已更新為 [成功]。
- 然後，您可以在資源不在失敗狀態時，繼續執行其他動作。

## <a name="next-steps"></a>後續步驟

如果上述步驟無法解決問題，請開啟 [支援票證](https://azure.microsoft.com/support/options/)。
