---
title: 輸出連接的 SNAT
description: 說明如何使用 Azure Load Balancer 來執行輸出網際網路連線的 SNAT
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 3f4791c5cbcf731e118bac4bf692adcad7e9ff44
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483584"
---
# <a name="using-snat-for-outbound-connections"></a>使用 SNAT 進行輸出連線

Azure 公用負載平衡器的前端 Ip 可用來為後端實例提供網際網路的輸出連線能力。此設定會使用 **來源網路位址轉譯 (SNAT)**。 SNAT 會將後端的 IP 位址重寫為負載平衡器的公用 IP 位址。 

SNAT 可 **偽裝** 出後端實例的 IP。 這種偽裝會防止外部來源擁有後端實例的直接位址。 在後端實例之間共用 IP 位址可降低靜態公用 Ip 的成本，並支援像是簡化 IP 允許清單以及來自已知公用 Ip 的流量等案例。 

>[!Note]
> 對於需要大量輸出連線的應用程式，或是需要從指定虛擬網路使用一組 Ip 的企業客戶，建議使用 [虛擬網路 NAT](../virtual-network/nat-overview.md) 作為建議的解決方案。 這項動態配置可讓您進行簡單的設定，並 > 從每個 IP 位址最有效率地使用 SNAT 埠。 它也可讓虛擬網路中的所有資源分享一組 IP 位址，而不需要將它們共用 > 負載平衡器。

>[!Important]
> 即使沒有設定輸出 SNAT，相同區域內的 Azure 儲存體帳戶仍然可以存取，而後端資源仍可存取 Microsoft 服務例如 Windows Update。

>[!NOTE] 
>本文僅涵蓋 Azure Resource Manager 部署。 檢閱[輸出連線 (傳統)](/previous-versions/azure/load-balancer/load-balancer-outbound-connections-classic) 以取得 Azure 中的所有傳統部署案例。

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> 跨後端資源分享前端 IP 位址

如果負載平衡器的後端資源沒有實例層級的公用 IP (ILPIP) 位址，則會透過公用負載平衡器的前端 IP 建立輸出連線能力。 埠是用來產生用來維護相異流程的唯一識別碼。 網際網路會使用五個元組來提供這種差異。

五個元組包含：

* 目的地 IP
* 目的地連接埠
* 來源 IP
* 提供此差異的來源埠和通訊協定。

如果埠用於輸入連接，則會在該埠上具有輸入連線 **要求的接聽** 程式，且無法用於輸出連線。 若要建立輸出連線，必須使用 **暫時埠** 來提供目的地埠，以用來進行通訊並維護不同的流量流程。 當這些暫時埠用來執行 SNAT 時，它們稱為 **snat 埠** 

根據定義，每個 IP 位址都有65535埠。 每個埠都可以用於 TCP (傳輸控制通訊協定) 和 UDP (使用者資料包協定) 的輸入或輸出連線。 將公用 IP 位址新增為負載平衡器的前端 IP 時，Azure 會為64000提供適合作為 SNAT 埠使用的。 

>[!NOTE]
> 用於負載平衡或輸入 NAT 規則的每個埠都會耗用來自這些64000埠的八個埠範圍，從而減少符合 SNAT 的埠數目。 如果負載 > 平衡或 nat 規則的範圍與另一個不同，則不會使用其他埠。 

透過 [輸出規則](./outbound-rules.md) 和負載平衡規則，這些 SNAT 埠可以散發到後端實例，讓它們可以共用輸出連線的負載平衡器公用 ip。

當您設定以下的 [案例 2](#scenario2) 時，每個後端實例的主機都會針對屬於輸出連線一部分的封包執行 SNAT。 在來自後端實例的輸出連接上執行 SNAT 時，主機會將來源 IP 重寫為其中一個前端 Ip。 為了維持唯一的流程，主機會將每個輸出封包的來源埠重寫為配置給後端實例的其中一個 SNAT 埠。

## <a name="outbound-connection-behavior-for-different-scenarios"></a>不同案例的輸出連接行為
  * 具有公用 IP 的虛擬機器。
  * 沒有公用 IP 的虛擬機器。
  * 沒有公用 IP 的虛擬機器，而且沒有標準負載平衡器。
        

 ### <a name="scenario-1-virtual-machine-with-public-ip"></a><a name="scenario1"></a> 案例1：具有公用 IP 的虛擬機器


 | 關聯 | 方法 | IP 通訊協定 |
 | ---------- | ------ | ------------ |
 | 公用負載平衡器或獨立 | [SNAT (來源網路位址轉譯) ](#snat) </br> 未使用。 | TCP (傳輸控制通訊協定)  </br> UDP (使用者資料包協定)  </br> ICMP (網際網路控制訊息通訊協定)  </br> ESP (封裝安全性承載)  |


 #### <a name="description"></a>描述


 Azure 會使用指派給實例 NIC 之 IP 設定的公用 IP 來進行所有輸出流程。 執行個體有所有可用的暫時連接埠。 VM 是否負載平衡並不重要。 此案例的優先順序高於其他案例。 


 指派給虛擬機器的公用 IP 是 1:1 關聯 (而非 1:多) 而且會實作為無狀態 1:1 NAT。


 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>案例2：沒有公用 IP 的虛擬機器和標準公用 Load Balancer 背後


 | 關聯 | 方法 | IP 通訊協定 |
 | ------------ | ------ | ------------ |
 | 公用 Load Balancer | 將負載平衡器前端 Ip 用於 [SNAT](#snat)。| TCP </br> UDP |


 #### <a name="description"></a>描述


 負載平衡器資源是以輸出規則或啟用預設 SNAT 的負載平衡規則來設定。 此規則可用來建立公用 IP 前端與後端集區之間的連結。 


 如果您未完成此規則設定，則行為會如案例3所述。 


 不需要具有接聽程式的規則，健康情況探查才會成功。


 當 VM 建立輸出流程時，Azure 會將來源 IP 位址轉譯為公用負載平衡器前端的公用 IP 位址。 這項轉譯是透過 [SNAT](#snat)完成的。 


 負載平衡器前端公用 IP 位址的暫時埠用來區分 VM 所產生的個別流程。 建立輸出流程時，SNAT 會動態使用[預先配置暫時連接埠](#preallocatedports)。 


 在此情況下，用於 SNAT 的暫時連接埠稱為 SNAT 連接埠。 強烈建議您明確設定 [輸出規則](./outbound-rules.md) 。 如果透過負載平衡規則使用預設 SNAT，則會預先配置 SNAT 埠，如 [預設的 snat 埠配置表](#snatporttable)所述。


 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario3"></a>案例3：沒有公用 IP 的虛擬機器和基本 Load Balancer 背後


 | 關聯 | 方法 | IP 通訊協定 |
 | ------------ | ------ | ------------ |
 |無 </br> 基本負載平衡器 | 具有實例層級動態 IP 位址的[SNAT](#snat)| TCP </br> UDP | 

 #### <a name="description"></a>描述


 當 VM 建立輸出流程時，Azure 會將來源 IP 位址轉譯為動態配置的公用來源 IP 位址。 此公用 **IP 位址無法** 設定且無法保留。 此位址不會計入訂用帳戶的公用 IP 資源限制。 


 如果您重新部署，將會釋出公用 IP 位址並要求新的公用 IP： 


 * 虛擬機器
 * 可用性設定組
 * 虛擬機器擴展集 


 請勿使用此案例將 Ip 新增至允許清單。 使用案例1或2，您可以在其中明確宣告輸出行為。 [Snat](#snat) 埠會預先配置，如 [預設的 snat 埠配置表](#snatporttable)所述。


## <a name="exhausting-ports"></a><a name="scenarios"></a> 耗盡埠

相同目的地 IP 和目的地埠的每個連接都會使用 SNAT 埠。 此連接會維護從後端實例或 **用戶端** 到 **伺服器** 的相異 **流量**。 此程式會為伺服器提供用來處理流量的不同埠。 如果沒有此程式，用戶端電腦就不會察覺封包所屬的流程。

假設有多個瀏覽器 https://www.microsoft.com 會進入，也就是：

* 目的地 IP = 23.53.254.142
* 目的地埠 = 443
* 通訊協定 = TCP

若沒有不同的目的地埠可讓傳回流量 (用來建立連線) 的 SNAT 埠，用戶端將無法與另一個查詢結果分開。

輸出連線可能會突然增加。 可以配置不足的埠給後端實例。 若未啟用連線 **重複使用** ，則會增加 SNAT **埠耗盡** 的風險。

發生埠耗盡時，目的地 IP 的新輸出連線將會失敗。 當埠變成可用時，連接就會成功。 當來自 IP 位址的64000埠分散在許多後端實例上時，就會發生這種耗盡狀況。 如需有關如何緩和 SNAT 埠耗盡的指引，請參閱 [疑難排解指南](./troubleshoot-outbound-connection.md)。  

針對 TCP 連接，負載平衡器會針對每個目的地 IP 和埠使用單一 SNAT 埠。 此多次使用可讓您使用相同的 SNAT 埠，對相同的目的地 IP 進行多個連接。 如果連接不是不同的目的地埠，此多次使用會受到限制。

若是 UDP 連線，負載平衡器會使用 **埠受限的錐形 NAT** 演算法，而無論目的地埠為何，每個目的地 IP 都會使用一個 SNAT 埠。 

埠會針對不限數目的連接重複使用。 只有在目的地 IP 或埠不同時，才會重複使用埠。

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> 預設埠配置

指派為負載平衡器前端 IP 的每個公用 IP，都會針對其後端集區成員提供64000個 SNAT 埠。 無法與後端集區成員共用埠。 只有單一後端實例可以使用 SNAT 埠範圍，以確保正確路由傳送封包。 

建議您使用明確的輸出規則來設定 SNAT 埠配置。 此規則會將每個後端實例可用於輸出連線的 SNAT 埠數目最大化。 

如果您透過負載平衡規則使用輸出 SNAT 的自動設定，配置資料表將會定義您的埠配置。

下 <a name="snatporttable"></a> 表顯示後端集區大小層級的 SNAT 埠預先配置：

| 集區大小 (VM 執行個體) | 對每個 IP 設定預先配置 SNAT 連接埠 |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 | 

>[!NOTE]
> 如果您的後端集區的大小上限為10，則如果您定義明確的輸出規則，則每個實例都可以有 64000/10 = 6400 埠。 根據上面的表格，如果您選擇自動設定，每個都只有1024。

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> 輸出規則和虛擬網路 NAT

Azure Load Balancer 輸出規則和虛擬網路 NAT 是可從虛擬網路輸出的選項。

如需輸出規則的詳細資訊，請參閱 [輸出規則](outbound-rules.md)。

如需 Azure 虛擬網路 NAT 的詳細資訊，請參閱 [什麼是 Azure 虛擬網路 nat](../virtual-network/nat-overview.md)。

## <a name="constraints"></a>條件約束

*   當連線閒置且沒有傳送新的封包時，埠會在4–120分鐘後釋出。
  * 您可以透過輸出規則設定此閾值。
*   每個 IP 位址都提供可用於 SNAT 的64000埠。
*   每個埠都可以同時用於目的地 IP 位址的 TCP 和 UDP 連接
  * 無論目的地埠是否唯一，都需要 UDP SNAT 埠。 針對目的地 IP 的每個 UDP 連線，會使用一個 UDP SNAT 埠。
  * TCP SNAT 埠可用於多個連接至相同目的地 IP 的連線，但前提是目的地埠不同。
*   當後端實例用盡指定的 SNAT 埠時，就會發生 SNAT 耗盡。 負載平衡器仍可以有未使用的 SNAT 埠。 如果後端實例的已使用 SNAT 埠超過其指定的 SNAT 埠，則將無法建立新的輸出連接。

## <a name="next-steps"></a>後續步驟

*   [針對發生 SNAT 耗盡的輸出連線失敗進行疑難排解](./troubleshoot-outbound-connection.md)
*   [複習 SNAT 計量](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) ，並熟悉其篩選、分割和查看的正確方式。
