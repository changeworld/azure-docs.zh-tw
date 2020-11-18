---
title: 輸出規則 Azure Load Balancer
description: 本文說明如何使用 Azure Load Balancer 來設定輸出規則，以控制網際網路流量的輸出。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 98bc962c0c57716cee9339056b0793bfe4bcb0ea
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694723"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>輸出規則 Azure Load Balancer

輸出規則可讓您明確地定義公用標準負載平衡器的 SNAT (來源網路位址轉譯) 。 這項設定可讓您使用負載平衡器的公用 IP () ，為您的後端實例提供輸出網際網路連線能力。

此設定可啟用：

* IP 偽裝
* 簡化您的允許清單。
* 減少部署的公用 IP 資源數量。

使用輸出規則時，您可以完整地以宣告方式控制輸出網際網路連線能力。 輸出規則可讓您調整及調整此能力，以滿足您的特定需求。 

只有當後端 VM 沒有 (ILPIP) 的實例層級公用 IP 位址時，才會遵循輸出規則。

![Load Balancer 輸出規則](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

使用輸出規則，您可以明確地定義輸出 **SNAT** 行為。

輸出規則可讓您控制：

* **哪些虛擬機器會轉譯為哪些公用 IP 位址。**
     * 有兩個規則是後端集區 A 使用 IP 位址 A 和 B，後端集區 B 使用 IP 位址 C 和 D。
* **輸出 SNAT 埠的配置方式。**
     * 後端集區 B 是進行輸出連線的唯一集區，提供所有 SNAT 埠給後端集區 B，而不是後端集區 A。
* **提供輸出轉譯的通訊協定。**
     * 後端集區 B 需要 UDP 埠才能進行輸出。 後端集區 A 需要 TCP。 將 A 和 UDP 埠的 TCP 通訊埠授與 B。
* **用於輸出連線閒置超時的持續時間 (4-120 分鐘) 。**
     * 如果有長時間執行的 keepalive 連線，請為長時間執行的連接保留閒置的埠，最多可達120分鐘。 假設過時的連線已放棄，並在4分鐘內針對新的連線釋放埠 
* **是否要在閒置的超時時間傳送 TCP 重設。**
     * 當閒置連線超時時，是否要將 TCP RST 傳送給用戶端和伺服器，讓他們知道流程已放棄？

## <a name="outbound-rule-definition"></a>輸出規則定義

輸出規則遵循與負載平衡和輸入 NAT 規則相同的熟悉語法：**前端**  +  **參數**  +  **後端集** 區。 

輸出規則會將「後端集區所識別的所有虛擬機器」的輸出 NAT 設定為轉譯成「前端」。  

這些 _參數_ 可讓您更精細地控制輸出 NAT 演算法。

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> 使用多個 IP 位址擴充輸出 NAT

前端提供的每個額外 IP 位址都會提供額外的64000暫時埠，以供負載平衡器用來作為 SNAT 埠。 

使用多個 IP 位址來規劃大規模案例。 使用輸出規則來減輕 [SNAT 耗盡](troubleshoot-outbound-connection.md#snatexhaust)的問題。 

您也可以直接使用 [公用 IP 前置](./load-balancer-outbound-connections.md#outboundrules) 詞和輸出規則。 

公用 IP 首碼可增加部署的規模。 前置詞可以新增到源自您 Azure 資源的流程允許清單中。 您可以設定負載平衡器內的前端 IP 設定，以參考公用 IP 位址首碼。  

負載平衡器具有公用 IP 前置詞的控制權。 輸出規則會自動使用公用 IP 首碼中包含的所有公用 IP 位址來進行輸出連線。 

公用 IP 首碼中的每個 IP 位址都會為每個 IP 位址提供額外的64000暫時埠，以供負載平衡器用來作為 SNAT 埠。

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> 輸出流量閒置超時和 TCP 重設

輸出規則提供設定參數來控制輸出流程閒置逾時，並使其符合您應用程式的需求。 輸出閒置逾時預設為 4 分鐘。 如需詳細資訊，請參閱 [設定閒置的超時](load-balancer-tcp-idle-timeout.md)時間。 

負載平衡器的預設行為是在達到輸出閒置超時時，以無訊息方式卸載流程。 `enableTCPReset`參數會啟用可預測的應用程式行為和控制項。 此參數會指定是否要在輸出閒置超時時間的超時時，傳送雙向 TCP 重設 (TCP RST) 。 

如需詳細資訊（包括區域可用性），請參閱 [閒置 timeout 的 TCP 重設](./load-balancer-tcp-reset.md) 。

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>明確保護和控制輸出連線能力

負載平衡規則會提供輸出 NAT 的自動程式設計。 某些案例受益或要求您停用負載平衡規則的輸出 NAT 的自動程式設計。 透過規則停用可讓您控制或精簡行為。  

您可以透過兩種方式來使用此參數：

1. 防止輸出 SNAT 的輸入 IP 位址。 在負載平衡規則中停用輸出 SNAT。
  
2. 同時調整用於輸入和輸出之 IP 位址的輸出 **SNAT** 參數。 必須停用自動輸出 NAT，才能讓輸出規則獲得控制權。 若要變更位址也用於輸入的 SNAT 埠配置， `disableOutboundSnat` 參數必須設定為 true。 

如果您嘗試重新定義用於輸入的 IP 位址，則設定輸出規則的作業將會失敗。  先停用負載平衡規則的輸出 NAT。

>[!IMPORTANT]
> 如果您將此參數設定為 true，且沒有可定義輸出連線能力的輸出規則，則您的虛擬機器將不會有輸出連線能力。  您 VM 或應用程式的某些作業可能取決於可用輸出連線。 請確定您了解情節的相依性，並考慮這項變更的影響。

有時候，VM 不需要建立輸出流程。 可能需要管理哪些目的地接收輸出流程，或哪些目的地會開始輸入流量。 使用 [網路安全性群組](../virtual-network/network-security-groups-overview.md) 來管理 VM 抵達的目的地。 使用 Nsg 來管理哪些公用目的地啟動輸入流程。

當您將 NSG 套用到經過負載平衡的虛擬機器時，請注意[服務標記](../virtual-network/network-security-groups-overview.md#service-tags)和[預設安全性規則](../virtual-network/network-security-groups-overview.md#default-security-rules)。 

確定 VM 可以從 Azure Load Balancer 接收健康情況探查要求。

如果 NSG 封鎖來自 AZURE_LOADBALANCER 預設標記的健康情況探查要求，則您的 VM 健康情況探查會失敗，而且 VM 會標示為無法使用。 負載平衡器會停止將新流量傳送到該 VM。

## <a name="scenarios-with-outbound-rules"></a>具有輸出規則的案例
        

### <a name="outbound-rules-scenarios"></a>輸出規則案例


* 設定一組特定公用 Ip 或首碼的輸出連線。
* 修改 [SNAT](load-balancer-outbound-connections.md) 埠配置。
* 僅啟用輸出。
* Vm 的輸出 NAT 只 (沒有任何輸入) 。
* 內部標準負載平衡器的輸出 NAT。
* 使用公用標準負載平衡器啟用輸出 NAT 的 TCP & UDP 通訊協定。


### <a name="scenario-1-configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>案例1：設定特定一組公用 Ip 或首碼的輸出連接


#### <a name="details"></a>詳細資料


您可以使用此案例，將輸出連線量身打造為源自一組公用 IP 位址。 根據來源將公用 Ip 或首碼新增至允許或拒絕清單。


此公用 IP 或首碼可以與負載平衡規則所使用的相同。 


使用與負載平衡規則所使用的不同公用 IP 或首碼： 


1. 建立公用 IP 首碼或公用 IP 位址。
2. 建立公用標準負載平衡器 
3. 建立參考公用 IP 首碼或您想要使用之公用 IP 位址的前端。 
4. 重複使用後端集區，或建立後端集區，並將 Vm 放入公用負載平衡器的後端集區
5. 在公用負載平衡器上設定輸出規則，以使用前端為 Vm 啟用輸出 NAT。 不建議針對輸出使用負載平衡規則，在負載平衡規則上停用輸出 SNAT。


### <a name="scenario-2-modify-snatport-allocation"></a><a name="scenario2out"></a>案例2：修改 [SNAT](load-balancer-outbound-connections.md)埠配置


#### <a name="details"></a>詳細資料


您可以 [根據後端集區大小](load-balancer-outbound-connections.md#preallocatedports)，使用輸出規則來調整自動 SNAT 埠配置。 


如果您遇到 SNAT 耗盡的情況，請增加提供預設值1024的 [snat](load-balancer-outbound-connections.md)埠數目。 


每個公用 IP 位址最多可占64000個暫時埠。 後端集區中的 Vm 數目會決定分散至每部 VM 的埠數目。 後端集區中的一個 VM 可以存取最多64000個埠。 針對兩個 Vm，可以使用輸出規則來指定最多32000個 [SNAT](load-balancer-outbound-connections.md)埠， (2x 32000 = 64000) 。 


您可以使用輸出規則來微調預設提供的 SNAT 埠。 您可以提供比預設 [SNAT](load-balancer-outbound-connections.md)埠配置更多或更少的配置。 來自輸出規則前端的每個公用 IP 位址，最多可提供64000個暫時埠，作為 [SNAT](load-balancer-outbound-connections.md)埠使用。 


負載平衡器會以8的倍數提供 [SNAT](load-balancer-outbound-connections.md)埠。 如果您提供的值無法與 8 整除，則會拒絕設定作業。 每個負載平衡規則和輸入 NAT 規則都會耗用8個埠的範圍。 如果負載平衡或輸入 NAT 規則與另一個相同的8範圍共用，將不會使用其他埠。


如果您嘗試提供的 [SNAT](load-balancer-outbound-connections.md)埠數目超過公用 IP 位址的可用數量，則會拒絕設定作業。 例如，如果您為每個 VM 提供10000個埠，且後端集區中有七個 Vm 共用單一公用 IP，則會拒絕設定。 七乘以10000超過64000埠的限制。 將更多公用 IP 位址新增至輸出規則的前端以啟用此案例。 


針對埠數目指定0，以還原為 [預設的埠配置](load-balancer-outbound-connections.md#preallocatedports) 。 第一個 50 VM 實例將會取得1024埠，51-100 VM 實例會取得512到最大實例。 如需預設 SNAT 埠配置的詳細資訊，請參閱 [snat 埠配置表](./load-balancer-outbound-connections.md#preallocatedports)。


### <a name="scenario-3-enable-outbound-only"></a><a name="scenario3out"></a>案例3：僅啟用輸出


#### <a name="details"></a>詳細資料


使用公用標準負載平衡器，為一組 Vm 提供輸出 NAT。 在此案例中，您可以單獨使用輸出規則，而不需要設定任何其他規則。


> [!NOTE]
> **Azure 虛擬網路 NAT** 可以為虛擬機器提供輸出連線能力，而不需要負載平衡器。 如需詳細資訊，請參閱 [什麼是 Azure 虛擬網路 NAT？](../virtual-network/nat-overview.md) 。

### <a name="scenario-4-outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>案例4： Vm 的輸出 NAT 僅 (沒有輸入) 


> [!NOTE]
> **Azure 虛擬網路 NAT** 可以為虛擬機器提供輸出連線能力，而不需要負載平衡器。 如需詳細資訊，請參閱 [什麼是 Azure 虛擬網路 NAT？](../virtual-network/nat-overview.md) 。

#### <a name="details"></a>詳細資料


針對此案例： Azure Load Balancer 輸出規則和虛擬網路 NAT 是可從虛擬網路輸出的選項。


1. 建立公用 IP 或首碼。
2. 建立公用標準負載平衡器。 
3. 建立與公用 IP 或用於輸出的首碼相關聯的前端。
4. 建立 Vm 的後端集區。
5. 將 Vm 放入後端集區。
6. 設定輸出規則以啟用輸出 NAT。



使用前置詞或公用 IP 來調整 [SNAT](load-balancer-outbound-connections.md)埠。 將輸出連接的來源新增至允許或拒絕清單。



### <a name="scenario-5-outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>案例5：內部標準負載平衡器的輸出 NAT


> [!NOTE]
> **Azure 虛擬網路 NAT** 可以為利用內部標準負載平衡器的虛擬機器提供輸出連線能力。 如需詳細資訊，請參閱 [什麼是 Azure 虛擬網路 NAT？](../virtual-network/nat-overview.md) 。

#### <a name="details"></a>詳細資料


輸出連線能力在透過實例層級的公用 Ip 或虛擬網路 NAT 明確宣告，或將後端集區成員與僅限輸出的負載平衡器設定產生關聯，而無法用於內部標準負載平衡器。 


如需詳細資訊，請參閱 [僅限輸出的負載平衡器](./egress-only.md)設定。




### <a name="scenario-6-enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>案例6：使用公用標準負載平衡器啟用輸出 NAT 的 TCP & UDP 通訊協定


#### <a name="details"></a>詳細資料


使用公用標準負載平衡器時，所提供的自動輸出 NAT 會符合負載平衡規則的傳輸通訊協定。 


1. 在負載平衡規則上停用輸出 [SNAT](load-balancer-outbound-connections.md)。 
2. 在相同的負載平衡器上設定輸出規則。
3. 重複使用 VM 已使用的後端集區。 
4. 指定 "protocol": "All" 作為輸出規則的一部分。 


僅使用輸出 NAT 規則時，不會提供輸出 NAT。 


1. 將 VM 放入後端集區中。
2. 使用公用 IP 位址 (es) 或公用 IP 首碼來定義一或多個前端 IP 設定 
3. 在相同的負載平衡器上設定輸出規則。 
4. 指定 "protocol": "All" 作為輸出規則的一部分


## <a name="limitations"></a>限制

- 每個前端 IP 位址的可用暫時連接埠數目上限為 64,000。
- 可設定輸出閒置逾時的範圍為 4 到 120 分鐘 (240 到 7200 秒)。
- 負載平衡器不支援輸出 NAT 的 ICMP。
- 輸出規則只能套用至 NIC 的主要 IP 設定。  您無法為 VM 或 NVA 的次要 IP 建立輸出規則。 支援多個 Nic。
- **可用性設定組** 內的所有虛擬機器都必須新增至後端集區，才能進行輸出連線。 
- **虛擬機器擴展集** 內的所有虛擬機器都必須新增至後端集區，才能進行輸出連線。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure Standard Load Balancer](load-balancer-overview.md)
- 查看[Azure Load Balancer](load-balancer-faqs.md)的常見問題