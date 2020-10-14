---
title: 輸出 proxy Azure Load Balancer
description: 描述如何使用 Azure Load Balancer 作為輸出網際網路連線的 proxy
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 422f8106ac52c85f0680d54e420d0f1b4d326910
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017687"
---
# <a name="outbound-proxy-azure-load-balancer"></a>輸出 proxy Azure Load Balancer

Azure 負載平衡器可用來作為輸出網際網路連線的 proxy。 負載平衡器會提供後端實例的輸出連線能力。 

此設定會使用 **來源網路位址轉譯 (SNAT) **。 SNAT 會將後端的 IP 位址重寫為負載平衡器的公用 IP 位址。 

SNAT 可 **偽裝** 出後端實例的 IP。 這種偽裝會防止外部來源擁有後端實例的直接位址。 

在後端實例之間共用 IP 位址可降低靜態公用 Ip 的成本，並支援像是簡化 IP 允許清單以及來自已知公用 Ip 的流量等案例。 

## <a name="sharing-ports-across-resources"></a><a name ="snat"></a> 跨資源分享埠

如果負載平衡器的後端資源沒有實例層級的公用 IP (ILPIP) 位址，則會透過公用負載平衡器的前端 IP 建立輸出連線能力。

埠是用來產生用來維護相異流程的唯一識別碼。 網際網路會使用五個元組來提供這種差異。

五個元組包含：

* 目的地 IP
* 目的地連接埠
* 來源 IP
* 提供此差異的來源埠和通訊協定。

如果埠用於輸入連接，則會在該埠上具有輸入連線 **要求的接聽** 程式，且無法用於輸出連線。 

若要建立輸出連線，必須使用 **暫時埠** 來提供目的地埠，以用來進行通訊並維護不同的流量流程。 

每個 IP 位址都有65535埠。 前1024埠會保留為 **系統埠**。 每個埠都可以用於 TCP 和 UDP 的輸入或輸出連接。 

在其餘的埠中，Azure 會提供64000作為 **暫時埠**使用。 將 IP 位址新增為前端 IP 設定時，這些暫時埠可用於 SNAT。

透過輸出規則，這些 SNAT 埠可以散發到後端實例，讓它們可以共用負載平衡器的公用 IP () 輸出連線。

主機上每個後端實例的網路功能，會對屬於輸出連線一部分的封包進行 SNAT。 主機會將來源 IP 重寫為其中一個公用 Ip。 主機會將每個輸出封包的來源埠重寫為其中一個 SNAT 埠。

## <a name="exhausting-ports"></a><a name="scenarios"></a> 耗盡埠

相同目的地 IP 和目的地埠的每個連接都會使用 SNAT 埠。 此連接會維護從後端實例或**用戶端**到**伺服器**的相異**流量**。 此程式會為伺服器提供用來處理流量的不同埠。 如果沒有此程式，用戶端電腦就不會察覺封包所屬的流程。

假設有多個瀏覽器 https://www.microsoft.com 會進入，也就是：

* 目的地 IP = 23.53.254.142
* 目的地埠 = 443
* 通訊協定 = TCP

若沒有不同的目的地埠可讓傳回流量 (用來建立連線) 的 SNAT 埠，用戶端將無法與另一個查詢結果分開。

輸出連線可能會突然增加。 可以配置不足的埠給後端實例。 若未啟用連線 **重複使用** ，則會增加 SNAT **埠耗盡** 的風險。

發生埠耗盡時，目的地 IP 的新輸出連線將會失敗。 當埠變成可用時，連接就會成功。 當來自 IP 位址的64000埠分散在許多後端實例上時，就會發生這種耗盡狀況。 如需有關如何緩和 SNAT 埠耗盡的指引，請參閱 [疑難排解指南](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection)。  

針對 TCP 連接，負載平衡器會針對每個目的地 IP 和埠使用單一 SNAT 埠。 此多次使用可讓您使用相同的 SNAT 埠，對相同的目的地 IP 進行多個連接。 如果連接不是不同的目的地埠，此多次使用會受到限制。

若是 UDP 連線，負載平衡器會使用 **埠受限的錐形 NAT** 演算法，而無論目的地埠為何，每個目的地 IP 都會使用一個 SNAT 埠。 

埠會針對不限數目的連接重複使用。 只有在目的地 IP 或埠不同時，才會重複使用埠。

## <a name="port-allocation"></a><a name="preallocatedports"></a> 埠配置

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
> 如果您的後端集區的大小上限為6，則如果您定義明確的輸出規則，則每個實例都可以有 64000/10 = 6400 埠。 根據上面的表格，如果您選擇自動設定，每個都只有1024。

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> 輸出規則和虛擬網路 NAT

Azure Load Balancer 輸出規則和虛擬網路 NAT 是可從虛擬網路輸出的選項。

如需輸出規則的詳細資訊，請參閱 [輸出規則](outbound-rules.md)。

如需 Azure 虛擬網路 NAT 的詳細資訊，請參閱 [什麼是 Azure 虛擬網路 nat](../virtual-network/nat-overview.md)。

## <a name="constraints"></a>條件約束

*   如果接收或傳送 **TCP RST** ，埠將在15秒後釋出
*   如果接收或傳送 **FINACK** ，埠會在240秒後釋出
*   當連線閒置且沒有傳送新的封包時，埠會在4–120分鐘後釋出。
  * 您可以透過輸出規則設定此閾值。
*   每個 IP 位址都提供可用於 SNAT 的64000埠。
*   每個埠都可以同時用於目的地 IP 位址的 TCP 和 UDP 連接
  * 無論目的地埠是否唯一，都需要 UDP SNAT 埠。 針對目的地 IP 的每個 UDP 連線，會使用一個 UDP SNAT 埠。
  * TCP SNAT 埠可用於多個連接至相同目的地 IP 的連線，但前提是目的地埠不同。
*   當後端實例用盡指定的 SNAT 埠時，就會發生 SNAT 耗盡。 負載平衡器仍可以有未使用的 SNAT 埠。 如果後端實例的已使用 SNAT 埠超過其指定的 SNAT 埠，則將無法建立新的輸出連接。

## <a name="next-steps"></a>後續步驟

*   [針對發生 SNAT 耗盡的輸出連線失敗進行疑難排解](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection)
*   [複習 SNAT 計量](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) ，並熟悉其篩選、分割和查看的正確方式。

