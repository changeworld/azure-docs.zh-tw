---
title: Azure 中的輸出連線
titleSuffix: Azure Load Balancer
description: 本文說明如何 Azure 如何讓 VM 與公用網際網路服務進行通訊。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/24/2020
ms.author: allensu
ms.openlocfilehash: ea73a4164a2f23a51ce1702cd85d29d7fb4c6790
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650395"
---
# <a name="outbound-connections-in-azure"></a>Azure 中的輸出連線

Azure Load Balancer 透過不同的機制提供輸出連線能力。 本文說明案例以及如何管理它們。 如果您遇到透過 Azure Load Balancer 的輸出連線問題，請參閱輸出連線的 [疑難排解指南](../load-balancer/troubleshoot-outbound-connection.md)。

>[!NOTE]
>本文涵蓋 Resource Manager 部署。 Microsoft 建議 Resource Manager 生產工作負載。

## <a name="terminology"></a>詞彙

| 詞彙 | 適用的通訊協定 (s)  | 詳細資料|
| ---------|---------| -------|
| 來源網路位址轉譯 (SNAT)  | TCP、UDP | Azure 中的部署可以與公用 IP 位址空間中的 Azure 外部端點進行通訊。 當執行個體向公用 IP 位址空間中的目的地起始輸出流程時，Azure 會動態地將私人 IP 位址對應至公用 IP 位址。 建立此對應之後，此輸出起源流程的傳回流量便也可以觸達流程起源的私人 IP 位址。 Azure 會使用 **來源網路位址轉譯 (SNAT) ** 來執行此功能。|
| 連接埠偽裝 SNAT (PAT)| TCP、UDP |  當多個私人 IP 位址偽裝在單一公用 IP 位址後方時，Azure 會使用 **埠位址轉譯 (PAT) ** 來偽裝/隱藏私人 ip 位址。 暫時連接埠會用於 PAT，並且根據集區大小[預先配置](#preallocatedports)。 當公用負載平衡器資源與沒有專用公用 IP 位址的 VM 執行個體建立關聯時，則系統會重寫每個連出的連線來源。 來源會從虛擬網路私人 IP 位址空間改寫成負載平衡器的前端「公用 IP」位址。 在公用 IP 位址空間中，5 tuple 流程 (來源 IP 位址、來源連接埠、IP 傳輸通訊協定、目的地 IP 位址、目的地連接埠) 必須是唯一的。 連接埠偽裝 SNAT 可與 TCP 或 UDP IP 通訊協定搭配使用。 在重寫私人來源 IP 位址之後，會使用暫時連接埠 (SNAT 連接埠) 來達到這個目的，因為多個流程來自單一公用 IP 位址。 連接埠偽裝 SNAT 演算法會以不同的方式針對 UDP 與 TCP 配置 SNAT 連接埠。|
| SNAT 埠| TCP | SNAT 連接埠是可供特定公用 IP 來源位址使用的暫時連接埠。 每個流程會取用一個 SNAT 連接埠至單一目的地 IP 位址、連接埠。 針對相同目的地 IP 位址、連接埠和通訊協定的多個 TCP 流程，每個 TCP 流程都會取用單一 SNAT 連接埠。 這可確保流程在從相同公用 IP 位址產生並前往相同目的地 IP 位址、連接埠及通訊協定時，會是唯一的。 多個各自前往不同目的地 IP 位址、連接埠及通訊協定的流程會共用單一 SNAT 連接埠。 目的地 IP 位址、連接埠及通訊協定可讓流程成為唯一的，而無須使用額外的來源連接埠來區別公用 IP 位址空間中的流程。|
|SNAT 埠 | UDP | UDP SNAT 連接埠是由與 TCP SNAT 連接埠不同的演算法管理。  Load Balancer 會對 UDP 使用稱為 "Port-Restricted cone NAT" 的演算法。  每個流程都會取用一個 SNAT 連接埠 (不管目的地 IP 位址、連接埠為何)。|
| 耗盡 | - | 當 SNAT 連接埠資源耗盡時，輸出流程會失敗，直到現有的流程釋出 SNAT 連接埠為止。 當流程關閉並使用 [4 分鐘閒置逾時](../load-balancer/troubleshoot-outbound-connection.md#idletimeout)來從閒置流程回收 SNAT 連接埠時，Load Balancer 會回收 SNAT 連接埠。 UDP SNAT 連接埠通常比 TCP SNAT 連接埠更快耗盡，因為使用的演算法有所差異。 您必須在設計及調整測試時記住此差異。|
| SNAT 埠釋放行為 | TCP | 如果伺服器/用戶端傳送 FINACK，則會在 240 秒後釋出 SNAT 連接埠。 如果看到 RST，則會在 15 秒之後釋出 SNAT 連接埠。 如已達到閒置逾時，則會釋放連接埠。|
| SNAT 埠釋放行為 | UDP |如已達到閒置逾時，則會釋放連接埠。|
| 重複使用 SNAT 連接埠 | TCP、UDP | 連接埠一經釋放，即可視需要重複使用。  您可將 SNAT 連接埠想成順序由低到高可供指定案例使用的連接埠，第一個可用的 SNAT 連接埠供新連線使用。|

### <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>埠配置演算法

Azure 會根據使用 PAT 時的後端集區大小，使用演算法來判斷可用的預先配置 SNAT 埠數目。 每個與負載平衡器建立關聯的公用 IP 位址，都有 64,000 個連接埠可作為每個 IP 傳輸通訊協定的 SNAT 連接埠。 分別針對 UDP 和 TCP 預先配置相同數目的 SNAT 連接埠，並且對每個 IP 傳輸通訊協定個別使用。  不過，視流程為 UDP 或 TCP 而定，SNAT 連接埠的使用方式會有所不同。 建立輸出流程時，這些埠會動態取用 (高達預先配置的限制) 並在流程關閉或 [閒置超時](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) 時釋出。 只有在必須使用連接埠來讓流程具有唯一性的情況下，才會取用連接埠。

#### <a name="default-snat-ports-allocated"></a><a name="snatporttable"></a> 配置的預設 SNAT 埠

下表說明各個後端集區大小層級的 SNAT 連接埠預先配置：

| 集區大小 (VM 執行個體) | 對每個 IP 設定預先配置 SNAT 連接埠 |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

變更後端集區的大小可能會影響您建立的部分流程：

- 如果後端集區大小增加並轉換到下一層，在轉換至下一個較大後端集區層的期間，會回收您預先配置的一半 SNAT 連接埠。 與已回收 SNAT 連接埠關聯的流程會逾時，而必須重新建立。 嘗試建立新流程時，只要預先配置的連接埠可用，流程就會立即成功。
- 如果後端集區大小縮減而轉換成較低的層級，可用的 SNAT 連接埠數目就會增加。 在此情況下，現有已配置 SNAT 連接埠及其各自流程都不會受到影響。

## <a name="outbound-connections-scenario-overview"></a><a name="scenarios"></a>輸出連接案例總覽

| 狀況 | 方法 | IP 通訊協定 | 描述 |
|  --- | --- | --- | --- |
|  1. 具有公用 IP 位址的 VM (有或沒有 Azure Load Balancer | SNAT，未使用連接埠偽裝 | TCP、UDP、ICMP、ESP | Azure 會使用指派給實例 NIC 之 IP 設定的公用 IP 來進行所有輸出流程。 執行個體有所有可用的暫時連接埠。 VM 是否負載平衡並不重要。 此案例的優先順序高於其他案例。 指派給虛擬機器的公用 IP 是 1:1 關聯 (而非 1:多) 而且會實作為無狀態 1:1 NAT。 |
| 2. 與 VM 相關聯的公用 Load Balancer (VM/實例上沒有公用 IP 位址)  | SNAT 搭配使用 Load Balancer 前端的連接埠偽裝 (PAT) | TCP、UDP | 在此案例中，必須使用負載平衡器規則設定 Load Balancer 資源，以在公用 IP 前端與後端集區之間建立連結。 如果您未完成此規則設定，則行為會如案例3所述。 規則不需要後端集區中有可運作的接聽程式，即可成功探查健康狀態。 當 VM 建立輸出流程時，Azure 會透過 SNAT 將輸出流程的私人來源 IP 位址轉譯為公用 Load Balancer 前端的公用 IP 位址。 負載平衡器前端公用 IP 位址的暫時埠用來區分 VM 所產生的個別流程。 建立輸出流程時，SNAT 會動態使用[預先配置暫時連接埠](#preallocatedports)。 在此情況下，用於 SNAT 的暫時連接埠稱為 SNAT 連接埠。 SNAT 埠會預先配置，如 [預設的 snat 埠配置資料表](#snatporttable)中所述。 |
| 3. VM (沒有 Load Balancer、沒有公用 IP 位址) 或與基本內部 Load Balancer 相關聯的 VM | SNAT 與連接埠偽裝 (PAT) | TCP、UDP | 當 VM 建立輸出流程時，Azure 會將輸出流量的公用來源 IP 位址轉譯為私用來源 IP 位址。 此公用 IP 位址無法設定、無法保留，且不會計入訂用 **帳戶**的公用 ip 資源限制。 如果您重新部署 VM 或可用性設定組或虛擬機器擴展集，此公用 IP 位址將會釋出，並要求新的公用 IP 位址。 請勿使用此案例來將 IP 位址加入允許清單。 相反地，請使用您明確宣告輸出行為的案例1或2。 SNAT 埠會預先配置，如 [預設 snat 埠配置資料表](#snatporttable)中所述。

## <a name="outbound-rules"></a><a name="outboundrules"></a>輸出規則

 輸出規則可以簡單地設定公用 [Standard Load Balancer](load-balancer-standard-overview.md) 的輸出網路位址轉譯。  您可以完全透過宣告來控制輸出連線，以針對您的特定需求擴充和調整此功能。 本節展開上述的案例 2 (B) 。

![Load Balancer 輸出規則](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

使用輸出規則，您可以使用 Load Balancer 從頭定義輸出 NAT。 您也可以調整和調整現有輸出 NAT 的行為。

輸出規則可讓您控制：

- 哪些虛擬機器應該轉譯成哪些公用 IP 位址。
- 應該如何配置輸出 SNAT 連接埠。
- 要提供其輸出轉譯的通訊協定。
- 要用於輸出連線閒置逾時的持續時間 (4-120 分鐘)。
- 是否要在閒置超時時傳送 TCP 重設
- 使用單一規則的 TCP 和 UDP 傳輸通訊協定

### <a name="outbound-rule-definition"></a>輸出規則定義

如同所有 Load Balancer 規則，輸出規則會遵循與負載平衡和輸入 NAT 規則相同的熟悉語法：**前端**  +  **參數**  +  **後端集**區。 輸出規則會將「後端集區所識別的所有虛擬機器」__ 的輸出 NAT 設定為轉譯成「前端」__。  這些 _參數_ 可讓您更精細地控制輸出 NAT 演算法。

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> 使用多個 IP 位址擴充輸出 NAT

前端提供的每個額外 IP 位址都會提供額外的64000暫時埠，供 Load Balancer 用來作為 SNAT 埠。 您可以使用多個 IP 位址來規劃大規模案例，也可以使用輸出規則來減輕易於 [SNAT 消耗](troubleshoot-outbound-connection.md#snatexhaust)模式。  

您也可以直接使用 [公用 IP 前置](https://aka.ms/lbpublicipprefix) 詞和輸出規則。  使用公用 IP 首碼可以更輕鬆地擴充和簡化源自 Azure 部署的流程允許清單。 您可以在 Load Balancer 資源內設定前端 IP 設定，直接參考公用 IP 位址首碼。  這可讓 Load Balancer 獨佔公用 IP 首碼，而輸出規則將會自動使用輸出連線之公用 IP 首碼內包含的所有公用 IP 位址。  公用 IP 首碼中的每個 IP 位址都會為每個 IP 位址提供額外64000個暫時埠，供 Load Balancer 用來作為 SNAT 埠。

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> 輸出流量閒置超時和 TCP 重設

輸出規則提供設定參數來控制輸出流程閒置逾時，並使其符合您應用程式的需求。 輸出閒置逾時預設為 4 分鐘。 您可以瞭解如何 [設定閒置的超時](load-balancer-tcp-idle-timeout.md#tcp-idle-timeout)時間。 Load Balancer 的預設行為，是在達到輸出閒置逾時的當時以無訊息方式捨棄流程。  使用 `enableTCPReset` 參數，您可以啟用更可預測的應用程式行為，並控制是否要在輸出閒置超時時，將雙向 Tcp 重設 (TCP RST) 。 如需詳細資訊（包括區域可用性），請參閱 [閒置 timeout 的 TCP 重設](https://aka.ms/lbtcpreset) 。

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>防止輸出連線

負載平衡規則會提供輸出 NAT 的自動程式設計。 不過，某些情節有所助益，或需要您透過負載平衡規則停用輸出 NAT 的自動程式設計，以控制或精簡行為。  
您可以透過兩種方式來使用此參數：

1. 選擇性隱藏使用輸出 SNAT 的輸入 IP 位址，透過停用負載平衡規則的輸出 SNAT
  
2. 同時調整用於輸入和輸出之 IP 位址的輸出 SNAT 參數。  必須停用自動輸出 NAT 程式設計，以允許輸出規則取得控制。  例如，為了變更位址也用於輸入的 SNAT 埠配置， `disableOutboundSnat` 參數必須設定為 true。  如果您嘗試使用輸出規則來重新定義也用於輸入的 IP 位址參數，但尚未發行負載平衡規則的輸出 NAT 程式設計，則設定輸出規則的作業會失敗。

>[!IMPORTANT]
> 如果您將此參數設定為 true，且沒有可定義輸出連線能力的輸出規則，則您的虛擬機器將不會有輸出連線能力。  您 VM 或應用程式的某些作業可能取決於可用輸出連線。 請確定您了解情節的相依性，並考慮這項變更的影響。

有時，您會不想要允許 VM 建立輸出流程。 或是可能需要管理可使用輸出流程來連線到哪些目的地，或哪些目的地可以發起輸入流程。 在此情況下，您可以使用[網路安全性群組](../virtual-network/security-overview.md)來管理 VM 可到連線的目的地。 您也可以使用 NSG 來管理哪個公用目的地可以起始輸入流程。

當您將 NSG 套用到經過負載平衡的虛擬機器時，請注意[服務標記](../virtual-network/security-overview.md#service-tags)和[預設安全性規則](../virtual-network/security-overview.md#default-security-rules)。 您必須確定 VM 可以從 Azure Load Balancer 接收健康情況探查要求。

如果 NSG 封鎖來自 AZURE_LOADBALANCER 預設標籤的健全狀況探查要求，您的 VM 健全狀況探查會失敗，且會將 VM 標示為離線。 負載平衡器會停止將新的流程傳送到該 VM。

## <a name="scenarios-with-outbound-rules"></a>具有輸出規則的案例

| # | 案例| 詳細資料|
|---|---|---|
| I |  清理與一組特定公用 IP 位址的輸出連線| 您可以使用輸出規則來清理似乎源自一組特定公用 IP 位址的輸出連線，以簡化允許清單情節。  此來源公用 IP 位址可以與負載平衡規則所使用的來源公用 IP 位址相同，或與負載平衡規則所使用的一組不同公用 IP 位址。  1. 從公用 IP 首碼) 2 建立 [公用 ip 首碼](https://aka.ms/lbpublicipprefix) (或公用 ip 位址。 建立公用 Standard Load Balancer 3。 建立參考公用 IP 首碼 (或您想要使用的公用 ip 位址) 的前端。 重複使用後端集區，或建立後端集區，並將 Vm 放入公用 Load Balancer 5 的後端集區。 在公用 Load Balancer 上設定輸出規則，以使用前端為這些 Vm 的輸出 NAT 進行程式設計。 如果您不想要將負載平衡規則用於輸出，則需要在負載平衡規則上停用輸出 SNAT。
| II |  修改 SNAT 連接埠配置| 您可以使用輸出規則來調整[根據後端集區大小的自動 SNAT 連接埠配置](load-balancer-outbound-connections.md#preallocatedports)。 例如，如果您有兩部虛擬機器共用輸出 NAT 的單一公用 IP 位址，則建議您在遇到 SNAT 消耗時增加從預設 1024 個連接埠配置的 SNAT 連接埠數目。 每個公用 IP 位址都會提供最多 64,000 個暫時連接埠。  如果您使用單一公用 IP 位址前端來設定輸出規則，則可以將共 64,000 個 SNAT 連接埠散發到後端集區中的 VM。  針對兩個 VM，最多可以使用輸出規則來配置 32,000 個 SNAT 連接埠 (2x 32,000 = 64,000)。 您可以使用輸出規則來微調預設配置的 SNAT 埠。 您可以配置比預設 SNAT 埠配置更多或更少的配置。來自輸出規則之所有前端的每個公用 IP 位址，最多可提供64000個暫時埠，作為 SNAT 埠使用。  Load Balancer 會以 8 的倍數來配置 SNAT 連接埠數目。 如果您提供的值無法與 8 整除，則會拒絕設定作業。  如果您嘗試根據公用 IP 位址數目來配置比可用 SNAT 連接埠更多的 SNAT 連接埠，則會拒絕設定作業。  例如，如果您為每個 VM 配置10000個埠，且後端集區中有7個 Vm 會共用單一公用 IP 位址，則會拒絕設定 (7 x 10000 SNAT 埠 > 64000 SNAT 埠) 。  您可以將更多公用 IP 位址新增至輸出規則的前端，以啟用此情節。 您可以針對埠數目指定0，以 [根據後端集區大小還原回預設的 SNAT 埠配置](load-balancer-outbound-connections.md#preallocatedports) 。 在這種情況下，第一個 50 VM 實例將會取得1024埠，51-100 VM 實例會根據 [資料表](#snatporttable)取得512。|
| III|  僅啟用輸出 | 您可以使用公用 Standard Load Balancer 提供一組 VM 的輸出 NAT。 在此情節中，您可以使用輸出規則本身，而不需要任何其他規則。|
| IV | 僅 VM 的輸出 NAT (無輸入) | 定義公用 Standard Load Balancer，並將 VM 放入後端集區，以及設定輸出規則以對輸出 NAT 進行程式設計並清理輸出連線以源自特定公用 IP 位址。 您也可以使用公用 IP 首碼，簡化將輸出連線來源設為允許清單。 1. 建立公用 Standard Load Balancer。 2. 建立後端集區，並將 VM 放入公用 Load Balancer 的後端集區。 3. 在公用 Load Balancer 上設定輸出規則，以對這些 VM 的輸出 NAT 進行程式設計。
| V| 內部 Standard Load Balancer 情節的輸出 NAT| 使用內部 Standard Load Balancer 時，在明確宣告輸出連線之前，無法使用輸出 NAT。 您可以使用輸出規則來定義輸出連線能力，以針對內部 Standard Load Balancer 後方的 Vm 建立輸出連線能力，請執行下列步驟：1。 建立公用 Standard Load Balancer。 2. 建立後端集區，並將 VM 放入公用及內部 Load Balancer 的後端集區。 3. 在公用 Load Balancer 上設定輸出規則，以對這些 VM 的輸出 NAT 進行程式設計。 如需此案例的詳細資訊，請參閱 [此範例](https://docs.microsoft.com/azure/load-balancer/egress-only)。 |
| VI | 使用公用 Standard Load Balancer 啟用輸出 NAT 的 TCP 和 UDP 通訊協定 | 使用公用 Standard Load Balancer 時，所提供的自動輸出 NAT 程式設計會符合負載平衡規則的傳輸通訊協定。 1. 在負載平衡規則上停用輸出 SNAT。 2. 在相同的 Load Balancer 上設定輸出規則。 3. 重複使用 VM 已使用的後端集區。 4. 指定 "protocol": "All" 作為輸出規則的一部分。 僅使用輸出 NAT 規則時，不會提供輸出 NAT。 1. 將 VM 放入後端集區中。 2. 使用公用 IP 位址 (es) 或公用 IP 首碼3來定義一或多個前端 IP 設定。 在相同的 Load Balancer 上設定輸出規則。 4. 指定 "protocol": "All" 作為輸出規則的一部分 |


## <a name="limitations"></a>限制

- 每個前端 IP 位址的可用暫時連接埠數目上限為 64,000。
- 可設定輸出閒置逾時的範圍為 4 到 120 分鐘 (240 到 7200 秒)。
- Load Balancer 不支援輸出 NAT 的 ICMP。
- 輸出規則只能套用至 NIC 的主要 IP 設定。  支援多個 Nic。
- 由於預先 VNet 服務和其他平台服務的運作方式產生副作用，而只使用內部標準 Load Balancer 時，才可存取沒有 VNet 和其他 Microsoft 平台服務的 Web 背景工作角色。 請勿以此副作用作為個別服務本身，否則基礎平台可能會在不經通知的情況下變更。 如果在只使用內部標準 Load Balancer 時有需要，請一律假設您需要明確建立輸出連線。 本文所述的案例3無法使用。

## <a name="next-steps"></a>後續步驟

- 深入了解[標準負載平衡器](load-balancer-standard-overview.md)。
- 請參閱 [關於 Azure Load Balancer](load-balancer-faqs.md)的常見問題。
- 深入了解標準公用 Load Balancer 的[輸出規則](load-balancer-outbound-rules-overview.md)。
- 深入了解 [Load Balancer](load-balancer-overview.md)。
- 深入了解[網路安全性群組](../virtual-network/security-overview.md)。
- 了解 Azure 中的一些其他重要[網路功能](../networking/networking-overview.md)。
