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
ms.openlocfilehash: 51810876e3636b7023ce9c9318a071636bb00c4c
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002629"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>輸出規則 Azure Load Balancer

輸出規則可讓您設定公用標準負載平衡器輸出 SNAT (來源網路位址轉譯) 。 這項設定可讓您使用負載平衡器的公用 IP (s) 作為 proxy。

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
* **如何指定輸出 SNAT 埠。**
     * 後端集區 B 是進行輸出連線的唯一集區，提供所有 SNAT 埠給後端集區 B，而不是後端集區 A。
* **提供輸出轉譯的通訊協定。**
     * 後端集區 B 需要 UDP 埠才能進行輸出。 後端集區 A 需要 TCP。 將 A 和 UDP 埠的 TCP 通訊埠授與 B。
* **用於輸出連線閒置超時的持續時間 (4-120 分鐘) 。**
     * 如果有長時間執行的 keepalive 連線，請為長時間執行的連接保留閒置的埠，最多可達120分鐘。 假設過時的連線已放棄，並在4分鐘內針對新的連線釋放埠 
* **是否要在閒置的超時時間傳送 TCP 重設。**
     * 當閒置連線超時時，是否要將 TCP RST 傳送給用戶端和伺服器，讓他們知道流程已放棄？

## <a name="outbound-rule-definition"></a>輸出規則定義

輸出規則遵循與負載平衡和輸入 NAT 規則相同的熟悉語法：**前端**  +  **參數**  +  **後端集**區。 

輸出規則會將「後端集區所識別的所有虛擬機器」__ 的輸出 NAT 設定為轉譯成「前端」__。  

這些 _參數_ 可讓您更精細地控制輸出 NAT 演算法。

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> 使用多個 IP 位址擴充輸出 NAT

前端提供的每個額外 IP 位址都會提供額外的64000暫時埠，以供負載平衡器用來作為 SNAT 埠。 

使用多個 IP 位址來規劃大規模案例。 使用輸出規則來減輕 [SNAT 耗盡](troubleshoot-outbound-connection.md#snatexhaust)的問題。 

您也可以直接使用 [公用 IP 前置](https://aka.ms/lbpublicipprefix) 詞和輸出規則。 

公用 IP 首碼可增加部署的規模。 前置詞可以新增到源自您 Azure 資源的流程允許清單中。 您可以設定負載平衡器內的前端 IP 設定，以參考公用 IP 位址首碼。  

負載平衡器具有公用 IP 前置詞的控制權。 輸出規則會自動使用公用 IP 首碼中包含的所有公用 IP 位址來進行輸出連線。 

公用 IP 首碼中的每個 IP 位址都會為每個 IP 位址提供額外的64000暫時埠，以供負載平衡器用來作為 SNAT 埠。

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> 輸出流量閒置超時和 TCP 重設

輸出規則提供設定參數來控制輸出流程閒置逾時，並使其符合您應用程式的需求。 輸出閒置逾時預設為 4 分鐘。 如需詳細資訊，請參閱 [設定閒置的超時](load-balancer-tcp-idle-timeout.md)時間。 

負載平衡器的預設行為是在達到輸出閒置超時時，以無訊息方式卸載流程。 `enableTCPReset`參數會啟用可預測的應用程式行為和控制項。 此參數會指定是否要在輸出閒置超時時間的超時時，傳送雙向 TCP 重設 (TCP RST) 。 

如需詳細資訊（包括區域可用性），請參閱 [閒置 timeout 的 TCP 重設](https://aka.ms/lbtcpreset) 。

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>明確保護和控制輸出連線能力

負載平衡規則會提供輸出 NAT 的自動程式設計。 某些案例受益或要求您停用負載平衡規則的輸出 NAT 的自動程式設計。 透過規則停用可讓您控制或精簡行為。  

您可以透過兩種方式來使用此參數：

1. 防止輸出 SNAT 的輸入 IP 位址。 在負載平衡規則中停用輸出 SNAT。
  
2. 同時調整用於輸入和輸出之 IP 位址的輸出 **SNAT** 參數。 必須停用自動輸出 NAT，才能讓輸出規則獲得控制權。 若要變更位址也用於輸入的 SNAT 埠配置， `disableOutboundSnat` 參數必須設定為 true。 

如果您嘗試重新定義用於輸入的 IP 位址，則設定輸出規則的作業將會失敗。  先停用負載平衡規則的輸出 NAT。

>[!IMPORTANT]
> 如果您將此參數設定為 true，且沒有可定義輸出連線能力的輸出規則，則您的虛擬機器將不會有輸出連線能力。  您 VM 或應用程式的某些作業可能取決於可用輸出連線。 請確定您了解情節的相依性，並考慮這項變更的影響。

有時候，VM 不需要建立輸出流程。 可能需要管理哪些目的地接收輸出流程，或哪些目的地會開始輸入流量。 使用 [網路安全性群組](../virtual-network/security-overview.md) 來管理 VM 抵達的目的地。 使用 Nsg 來管理哪些公用目的地啟動輸入流程。

當您將 NSG 套用到經過負載平衡的虛擬機器時，請注意[服務標記](../virtual-network/security-overview.md#service-tags)和[預設安全性規則](../virtual-network/security-overview.md#default-security-rules)。 

確定 VM 可以從 Azure Load Balancer 接收健康情況探查要求。

如果 NSG 封鎖來自 AZURE_LOADBALANCER 預設標記的健康情況探查要求，則您的 VM 健康情況探查會失敗，而且 VM 會標示為無法使用。 負載平衡器會停止將新流量傳送到該 VM。

## <a name="limitations"></a>限制

- 每個前端 IP 位址的可用暫時連接埠數目上限為 64,000。
- 可設定輸出閒置逾時的範圍為 4 到 120 分鐘 (240 到 7200 秒)。
- 負載平衡器不支援輸出 NAT 的 ICMP。
- 輸出規則只能套用至 NIC 的主要 IP 設定。  您無法為 VM 或 NVA 的次要 IP 建立輸出規則。 支援多個 Nic。
- **可用性設定組**內的所有虛擬機器都必須新增至後端集區，才能進行輸出連線。 
- **虛擬機器擴展集**內的所有虛擬機器都必須新增至後端集區，才能進行輸出連線。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure Standard Load Balancer](load-balancer-overview.md)
- 查看[Azure Load Balancer](load-balancer-faqs.md)的常見問題

