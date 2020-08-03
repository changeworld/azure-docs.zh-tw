---
title: Azure Load Balancer 概念
description: Azure Load Balancer 概念的概觀
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 40b738c0f074f06b2f15a260cacda876aa78daf6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87060808"
---
# <a name="azure-load-balancer-concepts"></a>Azure Load Balancer 概念

負載平衡器為 UDP 和 TCP 應用程式提供數種功能。 

## <a name="load-balancing-algorithm"></a>負載平衡演算法
您可以建立負載平衡規則，將來自前端的流量分散到後端集區。 Azure Load Balancer 使用雜湊演算法來散發輸入流量 (不是位元組)。 負載平衡器會將流程的標題重寫到後端集區執行個體。 當健康情況探查表示後端端點狀況良好時，伺服器便可以接收新的流程。

根據預設，負載平衡器會使用 5 個元組的雜湊。

雜湊包含：

- **來源 IP 位址**
- **來源連接埠**
- **目的地 IP 位址**
- **目的地連接埠**
- **將流量對應至可用伺服器的 IP 通訊協定編號**

使用兩個或三個元組雜湊來建立來源 IP 位址的親和性。 相同流程的封包會抵達相同執行個體，執行個體位於已負載平衡的前端之後。 

當用戶端從相同來源 IP 起始新的流程時，來源連接埠將會變更。 因此，五個元組的雜湊可能會使流量送往不同的後端端點。
如需詳細資訊，請參閱[設定 Azure Load Balancer 的分送模式](./load-balancer-distribution-mode.md)。

下列影像顯示雜湊型分送：

![雜湊型分散](./media/load-balancer-overview/load-balancer-distribution.png)

*圖：雜湊型分散*

## <a name="application-independence-and-transparency"></a>應用程式獨立性和透明度

負載平衡器不會直接與 TCP 或 UDP 或應用程式層互動。 任何 TCP 或 UDP 應用程式案例均受支援。 負載平衡器不會關閉或產生流程，也不會與流程的承載互動。 負載平衡器不會提供應用程式層閘道功能。 通訊協定交握一律會在用戶端與後端集區執行個體之間直接執行。 輸入流程的回應一律是來自虛擬機器的回應。 當流程到達虛擬機器時，也會保留原始的來源 IP 位址。

* 每個端點只會由一個 VM 回應。 例如，TCP 交握是在用戶端和選取的後端 VM 之間發生。 前端要求的回應是由後端 VM 所產生的回應。 當您已順利驗證前端連線時，您至少要驗證一個後端虛擬機器的連線。
* 對負載平衡器而言，應用程式承載是透明可見的。 任何 UDP 或 TCP 應用程式均受支援。
* 由於負載平衡器不會與 TCP 裝載互動及提供 TLS 卸載，您可以建置全面加密的案例。 使用負載平衡器，可藉由終止 VM 本身的 TLS 連線，讓 TLS 應用程式進行大規模的向外延展。 舉例而言，您的 TLS 工作階段金鑰處理容量只會受限於您新增至後端集區的 VM 類型和數量。

## <a name="outbound-connections"></a>輸出連線 

從後端集區到公用 IP 的流程會對應到前端。 Azure 會透過負載平衡輸出規則，將輸出連線轉譯成公用前端 IP 位址。 此組態有下列優點。 能夠輕鬆進行服務的升級及災害復原，因為前端可以動態對應到服務的另一個執行個體。 讓存取控制清單 (ACL) 管理變得更容易。 當服務擴大、縮小或重新部署時，以前端 IP 表示的 ACL 不會變更。 將輸出連線轉譯成數量比機器更少的 IP 位址，可以減輕實作安全收件者清單的負擔。 若要深入了解來源網路位址轉譯 (SNAT) 和 Azure Load Balancer，請參閱 [SNAT 和 Azure Load Balancer](load-balancer-outbound-connections.md)。

## <a name="availability-zones"></a>可用性區域 

標準負載平衡器在可使用「可用性區域」的區域中可支援較多功能。 公用和內部標準負載平衡器兩種類型皆可進行可用性區域設定。 區域備援前端不受區域失敗的影響，且所有區域中的專用基礎結構會同時為其提供服務。 此外，您可以確保前端可用於特定區域。 區域性前端是由單一區域中的專用基礎結構提供服務。 跨區域負載平衡適用於後端集區。 虛擬網路中的任何虛擬機器資源都可以是後端集區的一部分。 基本負載平衡器不支援區域。 如需詳細資訊，請檢閱[可用性區域相關功能的詳細討論](load-balancer-standard-availability-zones.md)和[可用性區域概觀](../availability-zones/az-overview.md)。

## <a name="ha-ports"></a>HA 連接埠

您可以設定 HA 連接埠負載平衡規則，讓應用程式縮放並變得高度可靠。 在內部負載平衡器前端 IP 的短期連接埠上，每個流程的負載平衡都是由這些規則提供。 若無法或不想指定個別連接埠的案例，此功能相當實用。 HA 連接埠規則可讓您建立主動-被動或主動-主動 n+1 案例。 這些案例適用於網路虛擬設備和任何應用程式，其需要大量的輸入連接埠。 健康情況探查可用來判斷哪個後端應接收新的流量。  您可以使用網路安全性群組來模擬連接埠範圍案例。 基本負載平衡器不支援 HA 連接埠。 請檢閱 [HA 連接埠的詳細討論](load-balancer-ha-ports-overview.md)。

## <a name="multiple-frontends"></a>多個前端 

負載平衡器支援為多個前端使用多項規則。  Standard Load Balancer 可將此功能擴及至輸出案例。 輸出規則與輸入規則相反。 輸出規則會為輸出連線建立關聯。 標準負載平衡器會透過負載平衡規則使用所有與虛擬機器資源相關聯的前端。 此外，您可以透過負載平衡規則的參數來抑制輸出連線用途的負載平衡規則，而使特定前端的可用選項變成空的。 為了比較，基本負載平衡器會隨機選取單一前端。 您無法控制要選取哪個前端。

## <a name="floating-ip"></a>浮動 IP

在某些應用程式案例中，後端集區中單一 VM 上的多個應用程式執行個體偏好或必須使用相同連接埠。 連接埠重複使用的常見範例包括提供高可用性的叢集、網路虛擬裝置、公開多個不會重新加密的 TLS 端點。 如果您想要在多個規則重複使用後端連接埠，必須啟用規則定義中的浮動 IP。

**浮動 IP** 是 Azure 術語，屬於伺服器直接回傳 (DSR) 的一部分。 DSR 包括兩個部分： 

- 流程拓撲
- IP 位址對應配置

在平台層級，Azure Load Balancer 一律在 DSR 流程拓撲中運作，無論是否已啟用浮動 IP。 這表示流程的輸出部分一律會正確重寫為直接流回原始來源。
若沒有浮動 IP，Azure 會公開傳統負載平衡 IP 位址對應配置以利使用的方便性 (VM 執行個體的 IP)。 啟用浮動 IP 會將 IP 位址對應變更為負載平衡器的前端 IP，以提供更大的彈性。 [在此](load-balancer-multivip-overview.md)深入了解。


## <a name="limitations"></a><a name = "limitations"></a>限制

- 內部負載平衡案例的次要 IP 組態目前不支援浮動 IP。

- 負載平衡器規則無法跨越兩個虛擬網路。  前端和其後端執行個體必須位於相同的虛擬網路。  

- 您可以從僅在一個標準內部負載平衡器後面的執行個體，存取沒有虛擬網路和其他 Microsoft 平台服務的 Web 背景工作角色。 請勿依賴此協助工具，否則基礎平台可能會在不經通知的情況下變更。 如果使用標準內部負載平衡器時需要輸出連線能力，必須設 [輸出連線](load-balancer-outbound-connections.md)。

- 負載平衡器提供針對特定 TCP 或 UDP 通訊協定進行負載平衡和連接埠轉送的功能。 負載平衡規則和輸入 NAT 規則支援 TCP 和 UDP，但不支援其他 IP 通訊協定，包括 ICMP。

- 從後端 VM 至內部負載平衡器前端的輸出流程將會失敗。

- 負載平衡規則不支援轉送 IP 片段。 負載平衡規則不支援 UDP 和 TCP 封包的 IP 分散。 HA 連接埠負載平衡規則可用來轉送現有的 IP 片段。 如需詳細資訊，請參閱[高可用性連接埠概觀](load-balancer-ha-ports-overview.md)。

## <a name="next-steps"></a>後續步驟

- 請參閱[建立公用 Standard Load Balancer](quickstart-load-balancer-standard-public-portal.md)，以開始使用 Load Balancer：先建立 Load Balancer，再建立安裝好自訂 IIS 擴充功能的 VM，並對 VM 之間的 Web 應用程式進行負載平衡。
- 深入了解 [Azure Load Balancer 輸出連線](load-balancer-outbound-connections.md)。
- 深入了解 [Azure Load Balancer](load-balancer-overview.md)。
- 深入了解[健康情況探查](load-balancer-custom-probe-overview.md)。
- 了解[標準 Load Balancer 診斷](load-balancer-standard-diagnostics.md)。
- 深入了解[網路安全性群組](../virtual-network/security-overview.md)。
