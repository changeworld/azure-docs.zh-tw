---
title: 輸出連線
titleSuffix: Azure Load Balancer
description: 本文說明如何 Azure 如何讓 VM 與公用網際網路服務進行通訊。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: contperfq1
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2020
ms.author: allensu
ms.openlocfilehash: 79399d0890f61d723f371528408d226f6a192ce4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336491"
---
# <a name="outbound-connections"></a>輸出連線

Azure Load Balancer 透過不同的機制提供輸出連線能力。 本文說明案例以及如何管理它們。 

## <a name="outbound-connections-scenario-overview"></a><a name="scenarios"></a>輸出連接案例總覽

在這些案例中使用的詞彙。 如需詳細資訊，請參閱 [術語](#terms)：

* [來源網路位址轉譯 (SNAT) ](#snat)
* [埠偽裝 (PAT) ](#pat)
* 傳輸控制通訊協定 (TCP)
* 使用者資料包通訊協定 (UDP)
* 網路位址轉譯
* 網際網路控制訊息通訊協定
* 封裝安全性通訊協定

### <a name="scenarios"></a>案例

* [案例 1](#scenario1) -具有公用 IP 的虛擬機器。
* [案例 2](#scenario2) -沒有公用 IP 的虛擬機器。
* [案例 3](#scenario3) -沒有公用 IP 的虛擬機器，且沒有標準負載平衡器。

### <a name="scenario-1---virtual-machine-with-public-ip"></a><a name="scenario1"></a>案例 1-具有公用 IP 的虛擬機器

| 關聯 | 方法 | IP 通訊協定 |
| ---------- | ------ | ------------ |
| 公用負載平衡器或獨立 | [SNAT](#snat) </br> 未使用[埠偽裝](#pat)。 | TCP </br> UDP </br> ICMP </br> ESP |

#### <a name="description"></a>說明

Azure 會使用指派給實例 NIC 之 IP 設定的公用 IP 來進行所有輸出流程。 執行個體有所有可用的暫時連接埠。 VM 是否負載平衡並不重要。 此案例的優先順序高於其他案例。 

指派給虛擬機器的公用 IP 是 1:1 關聯 (而非 1:多) 而且會實作為無狀態 1:1 NAT。

### <a name="scenario-2---virtual-machine-without-public-ip"></a><a name="scenario2"></a>案例 2-沒有公用 IP 的虛擬機器

| 關聯 | 方法 | IP 通訊協定 |
| ------------ | ------ | ------------ |
| 公用 Load Balancer | 使用適用于 [SNAT](#snat) 的負載平衡器前端搭配 [埠偽裝 (PAT) ](#pat)。| TCP </br> UDP |

#### <a name="description"></a>說明

負載平衡器資源會設定負載平衡器規則。 此規則可用來建立公用 IP 前端與後端集區之間的連結。 

如果您未完成此規則設定，則行為會如案例3所述。 

不需要具有接聽程式的規則，健康情況探查才會成功。

當 VM 建立輸出流程時，Azure 會將來源 IP 位址轉譯為公用負載平衡器前端的公用 IP 位址。 這項轉譯是透過 [SNAT](#snat)完成的。 

負載平衡器前端公用 IP 位址的暫時埠用來區分 VM 所產生的個別流程。 建立輸出流程時，SNAT 會動態使用[預先配置暫時連接埠](#preallocatedports)。 

在此情況下，用於 SNAT 的暫時連接埠稱為 SNAT 連接埠。 SNAT 埠會預先配置，如 [預設的 snat 埠配置表](#snatporttable)所述。

### <a name="scenario-3---virtual-machine-without-public-ip-and-without-standard-load-balancer"></a><a name="scenario3"></a> 案例 3-沒有公用 IP 且沒有標準負載平衡器的虛擬機器

| 關聯 | 方法 | IP 通訊協定 |
| ------------ | ------ | ------------ |
|無 </br> 基本負載平衡器 | [SNAT](#snat) 與 [埠偽裝 (PAT) ](#pat)| TCP </br> UDP | 

#### <a name="description"></a>說明

當 VM 建立輸出流程時，Azure 會將輸出流量的來源 IP 位址轉譯為公用來源 IP 位址。 此公用 **IP 位址無法** 設定且無法保留。 此位址不會計入訂用帳戶的公用 IP 資源限制。 

如果您重新部署，將會釋出公用 IP 位址並要求新的公用 IP： 

* 虛擬機器
* 可用性設定組
* 虛擬機器擴展集  

請勿使用此案例將 Ip 新增至允許清單。 使用案例1或2，您可以在其中明確宣告輸出行為。 [Snat](#snat) 埠會預先配置，如 [預設的 snat 埠配置表](#snatporttable)所述。



## <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>埠配置演算法

Azure 會使用演算法來判斷可用的預先配置 [SNAT](#snat) 埠數目。 演算法會以後端集區大小為基礎的埠數目作為基礎。 

針對與負載平衡器相關聯的每個公用 IP 位址，64000埠可作為 [SNAT](#snat) 埠。 相同數目的 [SNAT](#snat) 埠會針對 UDP 和 TCP 預先配置。 每個 IP 通訊協定都使用個別的埠。 

根據流程是 UDP 或 TCP， [SNAT](#snat) 埠的使用方式會有所不同。 

埠會動態取用，最多可達預先配置的限制。  當流程關閉或發生閒置的超時時，就會釋放埠。

如需有關閒置超時的詳細資訊，請參閱 [Azure Load Balancer 中的輸出連接疑難排解](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) 

只有在必須使用連接埠來讓流程具有唯一性的情況下，才會取用連接埠。

### <a name="dynamic-snat-ports-preallocation"></a><a name="snatporttable"></a> 動態 SNAT 埠預先配置

下表顯示後端集區大小層級的 [SNAT](#snat) 埠預先配置：

| 集區大小 (VM 執行個體) | 對每個 IP 設定預先配置 SNAT 連接埠 |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

變更後端集區的大小可能會影響您建立的部分流程：

- 後端集區大小會將觸發程式轉換增加到下一層。 在轉換到下一層期間，會回收一半預先配置的 [SNAT](#snat) 埠。 

- 與已回收的 [SNAT](#snat) 埠超時和關閉相關聯的流程。 您必須重新建立這些流程。 嘗試建立新流程時，只要預先配置的連接埠可用，流程就會立即成功。

- 如果後端集區大小減少並轉換成較低的層級，可用的 [SNAT](#snat) 埠數目就會增加。 現有的特定 [SNAT](#snat) 埠及其各自的流程不會受到影響。

## <a name="outbound-rules"></a><a name="outboundrules"></a>輸出規則

 輸出規則可讓您設定公用 [標準負載平衡器](load-balancer-standard-overview.md)的輸出網路位址轉譯。  

> [!NOTE]
> **Azure 虛擬網路 NAT** 可以為虛擬網路中的虛擬機器提供輸出連線能力。  如需詳細資訊，請參閱 [什麼是 Azure 虛擬網路 NAT？](../virtual-network/nat-overview.md) 。

您對輸出連線具有完整的宣告式控制權，可調整並調整此能力以滿足您的需求。 本節將擴充案例2，如上所述。

![負載平衡器輸出規則](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

使用輸出規則，您可以使用負載平衡器從頭定義輸出 NAT。 您也可以調整和調整現有輸出 NAT 的行為。

輸出規則可讓您控制：

- 哪些虛擬機器應轉譯為哪些公用 IP 位址。
- 應如何指定輸出 [SNAT](#snat) 埠。
- 提供輸出轉譯的通訊協定。
- 用於輸出連線閒置超時的持續時間 (4-120 分鐘) 。
- 是否要在閒置超時時傳送 TCP 重設
- 使用單一規則的 TCP 和 UDP 傳輸通訊協定

### <a name="outbound-rule-definition"></a>輸出規則定義

輸出規則遵循與負載平衡和輸入 NAT 規則相同的熟悉語法：**前端**  +  **參數**  +  **後端集**區。 輸出規則會將「後端集區所識別的所有虛擬機器」__ 的輸出 NAT 設定為轉譯成「前端」__。  這些 _參數_ 可讓您更精細地控制輸出 NAT 演算法。

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> 使用多個 IP 位址擴充輸出 NAT

前端提供的每個額外 IP 位址都會提供額外的64000暫時埠，以供負載平衡器用來作為 SNAT 埠。 

使用多個 IP 位址來規劃大規模案例。 使用輸出規則來減輕 [SNAT 耗盡](troubleshoot-outbound-connection.md#snatexhaust)的問題。 

您也可以直接使用 [公用 IP 前置](https://aka.ms/lbpublicipprefix) 詞和輸出規則。 

公用 IP 首碼可增加部署的規模。 前置詞可以新增到源自您 Azure 資源的流程允許清單中。 您可以設定負載平衡器內的前端 IP 設定，以參考公用 IP 位址首碼。  

負載平衡器具有公用 IP 前置詞的控制權。 輸出規則會自動使用公用 IP 首碼中包含的所有公用 IP 位址來進行輸出連線。 

公用 IP 首碼中的每個 IP 位址都會為每個 IP 位址提供額外的64000暫時埠，以供負載平衡器用來作為 SNAT 埠。

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> 輸出流量閒置超時和 TCP 重設

輸出規則提供設定參數來控制輸出流程閒置逾時，並使其符合您應用程式的需求。 輸出閒置逾時預設為 4 分鐘。 如需詳細資訊，請參閱 [設定閒置的超時](load-balancer-tcp-idle-timeout.md#tcp-idle-timeout)時間。 

負載平衡器的預設行為是在達到輸出閒置超時時，以無訊息方式卸載流程。 `enableTCPReset`參數會啟用可預測的應用程式行為和控制項。 此參數會指定是否要在輸出閒置超時時間的超時時，傳送雙向 TCP 重設 (TCP RST) 。 

如需詳細資訊（包括區域可用性），請參閱 [閒置 timeout 的 TCP 重設](https://aka.ms/lbtcpreset) 。

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>防止輸出連線

負載平衡規則會提供輸出 NAT 的自動程式設計。 某些案例受益或要求您停用負載平衡規則的輸出 NAT 的自動程式設計。 透過規則停用可讓您控制或精簡行為。  

您可以透過兩種方式來使用此參數：

1. 防止輸出 SNAT 的輸入 IP 位址。 在負載平衡規則中停用輸出 SNAT。
  
2. 同時調整用於輸入和輸出之 IP 位址的輸出 [SNAT](#snat) 參數。 必須停用自動輸出 NAT，才能讓輸出規則獲得控制權。 若要變更位址也用於輸入的 SNAT 埠配置， `disableOutboundSnat` 參數必須設定為 true。 

如果您嘗試重新定義用於輸入的 IP 位址，則設定輸出規則的作業將會失敗。  先停用負載平衡規則的輸出 NAT。

>[!IMPORTANT]
> 如果您將此參數設定為 true，且沒有可定義輸出連線能力的輸出規則，則您的虛擬機器將不會有輸出連線能力。  您 VM 或應用程式的某些作業可能取決於可用輸出連線。 請確定您了解情節的相依性，並考慮這項變更的影響。

有時候，VM 不需要建立輸出流程。 可能需要管理哪些目的地接收輸出流程，或哪些目的地會開始輸入流量。 使用 [網路安全性群組](../virtual-network/security-overview.md) 來管理 VM 抵達的目的地。 使用 Nsg 來管理哪些公用目的地啟動輸入流程。

當您將 NSG 套用到經過負載平衡的虛擬機器時，請注意[服務標記](../virtual-network/security-overview.md#service-tags)和[預設安全性規則](../virtual-network/security-overview.md#default-security-rules)。 確定 VM 可以從 Azure Load Balancer 接收健康情況探查要求。

如果 NSG 封鎖來自 AZURE_LOADBALANCER 預設標籤的健全狀況探查要求，您的 VM 健全狀況探查會失敗，且會將 VM 標示為離線。 負載平衡器會停止將新的流程傳送到該 VM。

## <a name="scenarios-with-outbound-rules"></a>具有輸出規則的案例

### <a name="outbound-rules-scenarios"></a>輸出規則案例

* [案例 1](#scenario1out) -設定對一組特定公用 ip 或首碼的輸出連線。
* [案例 2](#scenario2out) -修改 [SNAT](#snat) 埠配置。
* [案例 3](#scenario3out) -僅啟用輸出。
* [案例 4](#scenario4out) -vm 的輸出 NAT 只 (沒有任何輸入) 。
* [案例 5](#scenario5out) ：內部標準負載平衡器的輸出 NAT。
* [案例 6](#scenario6out) -啟用輸出 NAT 的 TCP & UDP 通訊協定與公用標準負載平衡器。

### <a name="scenario-1"></a><a name="scenario1out"></a>案例1

| 狀況 |
| -------- |
| 設定一組特定公用 Ip 或首碼的輸出連接|

#### <a name="details"></a>詳細資料

您可以使用此案例，將輸出連線量身打造為源自一組公用 IP 位址。 根據來源將公用 Ip 或首碼新增至允許或拒絕清單。

此公用 IP 或首碼可以與負載平衡規則所使用的相同。 

使用與負載平衡規則所使用的不同公用 IP 或首碼：  

1. 建立公用 IP 首碼或公用 IP 位址。
2. 建立公用標準負載平衡器 
3. 建立參考公用 IP 首碼或您想要使用之公用 IP 位址的前端。 
4. 重複使用後端集區，或建立後端集區，並將 Vm 放入公用負載平衡器的後端集區
5. 在公用負載平衡器上設定輸出規則，以使用前端為 Vm 啟用輸出 NAT。 如果您不想要將負載平衡規則用於輸出，請在負載平衡規則上停用輸出 SNAT。

### <a name="scenario-2"></a><a name="scenario2out"></a>案例2

| 狀況 |
| -------- |
| 修改 [SNAT](#snat) 埠配置 |

#### <a name="details"></a>詳細資料

您可以使用輸出規則來調整[根據後端集區大小的自動 SNAT 連接埠配置](load-balancer-outbound-connections.md#preallocatedports)。 

如果您遇到 SNAT 耗盡的情況，請增加提供預設值1024的 [snat](#snat) 埠數目。 

每個公用 IP 位址最多可占64000個暫時埠。 後端集區中的 Vm 數目會決定分散至每部 VM 的埠數目。 後端集區中的一個 VM 可以存取最多64000個埠。 針對兩個 Vm，可以使用輸出規則來指定最多32000個 [SNAT](#snat) 埠， (2x 32000 = 64000) 。 

您可以使用輸出規則來微調預設提供的 SNAT 埠。 您可以提供比預設 [SNAT](#snat) 埠配置更多或更少的配置。 來自輸出規則前端的每個公用 IP 位址，最多可提供64000個暫時埠，作為 [SNAT](#snat) 埠使用。  

負載平衡器會以8的倍數提供 [SNAT](#snat) 埠。 如果您提供的值無法與 8 整除，則會拒絕設定作業。 

如果您嘗試提供的 [SNAT](#snat) 埠數目超過公用 IP 位址的可用數量，則會拒絕設定作業。

如果您為每個 VM 提供10000個埠，且後端集區中有七個 Vm 共用單一公用 IP，則會拒絕設定。 七乘以10000超過64000埠的限制。 將更多公用 IP 位址新增至輸出規則的前端以啟用此案例。 

針對埠數目指定0，以還原為 [預設的埠配置](load-balancer-outbound-connections.md#preallocatedports) 。 第一個 50 VM 實例將會取得1024埠，51-100 VM 實例會取得512到最大實例。  如需預設 SNAT 埠配置的詳細資訊，請參閱 [上面](#snatporttable)的。

### <a name="scenario-3"></a><a name="scenario3out"></a>案例 3

| 狀況 |
| -------- |
|  僅啟用輸出 |

#### <a name="details"></a>詳細資料

您可以使用公用標準負載平衡器，為一組 Vm 提供輸出 NAT。 在此案例中，您可以單獨使用輸出規則，而不需要任何額外的規則。

> [!NOTE]
> **Azure 虛擬網路 NAT** 可以為虛擬機器提供輸出連線能力，而不需要負載平衡器。  如需詳細資訊，請參閱 [什麼是 Azure 虛擬網路 NAT？](../virtual-network/nat-overview.md) 。

### <a name="scenario-4"></a><a name="scenario4out"></a>案例 4

| 狀況 |
| -------- |
| 僅 VM 的輸出 NAT (無輸入) |

> [!NOTE]
> **Azure 虛擬網路 NAT** 可以為虛擬機器提供輸出連線能力，而不需要負載平衡器。  如需詳細資訊，請參閱 [什麼是 Azure 虛擬網路 NAT？](../virtual-network/nat-overview.md) 。

#### <a name="details"></a>詳細資料

針對此案例：

1. 建立公用 IP 或首碼。
2. 建立公用標準負載平衡器。 
3. 建立與公用 IP 或用於輸出的首碼相關聯的前端。
4. 建立 Vm 的後端集區。
5. 將 Vm 放入後端集區。
6. 設定輸出規則以啟用輸出 NAT。

使用前置詞或公用 IP 來調整 [SNAT](#snat) 埠。 將輸出連接的來源新增至允許或拒絕清單。

### <a name="scenario-5"></a><a name="scenario5out"></a>案例 5

| 狀況 |
| -------- |
| 內部標準負載平衡器的輸出 NAT |

> [!NOTE]
> **Azure 虛擬網路 NAT** 可以為利用內部標準負載平衡器的虛擬機器提供輸出連線能力。  如需詳細資訊，請參閱 [什麼是 Azure 虛擬網路 NAT？](../virtual-network/nat-overview.md) 。

#### <a name="details"></a>詳細資料

除非已明確宣告，否則內部標準負載平衡器無法使用輸出連線能力。 

如需詳細資訊，請參閱 [僅限輸出的負載平衡器](https://docs.microsoft.com/azure/load-balancer/egress-only)設定。


### <a name="scenario-6"></a><a name="scenario6out"></a>案例6

| 狀況 |
| -------- |
| 使用公用標準負載平衡器啟用輸出 NAT 的 TCP & UDP 通訊協定 |

#### <a name="details"></a>詳細資料

使用公用標準負載平衡器時，所提供的自動輸出 NAT 會符合負載平衡規則的傳輸通訊協定。 

1. 在負載平衡規則上停用輸出 [SNAT](#snat) 。 
2. 在相同的負載平衡器上設定輸出規則。
3. 重複使用 VM 已使用的後端集區。 
4. 指定 "protocol": "All" 作為輸出規則的一部分。 

僅使用輸出 NAT 規則時，不會提供輸出 NAT。 

1. 將 VM 放入後端集區中。
2. 使用公用 IP 位址 (es) 或公用 IP 首碼來定義一或多個前端 IP 設定 
3. 在相同的負載平衡器上設定輸出規則。 
4. 指定 "protocol": "All" 作為輸出規則的一部分

## <a name="terminology"></a><a name="terms"></a> 術語

### <a name="source-network-address-translation"></a><a name="snat"></a>來源網路位址轉譯

| 適用的通訊協定 (s)  |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>詳細資料

Azure 中的部署可以與公用 IP 位址空間中的 Azure 外部端點進行通訊。

當實例以公用 ip 位址啟動連至目的地的輸出流量時，Azure 會以動態方式將資源的私人 IP 位址對應至公用 IP 位址。 

建立此對應之後，會傳回此輸出產生流程的流量，以達到流程來源的私人 IP 位址。 

Azure 會使用 **來源網路位址轉譯 (SNAT) ** 來進行此功能。

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>連接埠偽裝 SNAT (PAT)

| 適用的通訊協定 (s)  |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>詳細資料

當私人 ip 位於單一公用 IP 位址後方時，Azure 會使用 **埠位址轉譯 (PAT) ** 來隱藏私人 IP 位址。 

暫時連接埠會用於 PAT，並且根據集區大小[預先配置](#preallocatedports)。 

當公用負載平衡器與沒有公用 Ip 的 Vm 相關聯時，會重寫每個輸出連接來源。 

來源會從虛擬網路私人 IP 位址重新寫入至負載平衡器的前端公用 IP 位址。 

在公用 IP 位址空間中，以下流程的五個元組必須是唯一的：

* 來源 IP 位址
* 來源連接埠
* IP 傳輸通訊協定
* 目的地 IP 位址
* 目的地連接埠 

埠偽裝 SNAT 可搭配 TCP 或 UDP 通訊協定使用。 重寫私人來源 IP 位址之後，會使用 SNAT 埠，因為多個流程來自單一公用 IP 位址。 埠偽裝 SNAT 演算法會以不同的方式為 UDP 與 TCP 提供 SNAT 埠。

### <a name="snat-ports-tcp"></a> (TCP) 的 SNAT 埠

| 適用的通訊協定 (s)  |
|------------------------|
| TCP |

#### <a name="details"></a>詳細資料

SNAT 埠是公用 IP 來源位址可用的暫時埠。 每個流程都會取用一個 SNAT 連接埠至單一目的地 IP 位址和連接埠。 

針對相同目的地 IP 位址、連接埠和通訊協定的多個 TCP 流程，每個 TCP 流程都會取用單一 SNAT 連接埠。 

這種耗用量可確保流程在源自相同的公用 IP 位址並傳送至時，是唯一的：

* 相同的目的地 IP 位址
* 連接埠
* 通訊協定 

多個流程會共用單一 SNAT 埠。 

目的地 IP 位址、連接埠及通訊協定可讓流程成為唯一的，而無須使用額外的來源連接埠來區別公用 IP 位址空間中的流程。


### <a name="snat-ports-udp"></a>SNAT 埠 (UDP) 

| 適用的通訊協定 (s)  |
|------------------------|
| UDP |

#### <a name="details"></a>詳細資料

UDP SNAT 連接埠是由與 TCP SNAT 連接埠不同的演算法管理。  負載平衡器會使用名為「埠受限的錐形 NAT」的演算法進行 UDP。

每個流量的任何目的地 IP 位址和埠都會取用一個 SNAT 埠。


### <a name="exhaustion"></a><a name="exhaustion"></a>耗盡

| 適用的通訊協定 (s)  |
|------------------------|
| N/A |

#### <a name="details"></a>詳細資料

當 SNAT 連接埠資源耗盡時，輸出流程會失敗，直到現有的流程釋出 SNAT 連接埠為止。 負載平衡器會在流程關閉時回收 SNAT 埠。

負載平衡器會使用4分鐘的 [閒置 timeout](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) 來回收 SNAT 埠。

UDP SNAT 埠的速度通常會比 TCP SNAT 埠快，因為使用的演算法有所差異。 因為這項差異，所以設計和調整測試。

### <a name="snat-port-release-behavior-tcp"></a> (TCP) 的 SNAT 埠發行行為

| 適用的通訊協定 (s)  |
|------------------------|
| TCP |

#### <a name="details"></a>詳細資料

當伺服器或用戶端傳送 FINACK 時，會在240秒後釋出 SNAT 埠。 如果出現 RST，則會在15秒後釋出 SNAT 埠。 如果已達到閒置超時，則會釋放埠。

### <a name="snat-port-release-behavior-udp"></a>SNAT 埠發行行為 (UDP) 

| 適用的通訊協定 (s)  |
|------------------------|
| TCP |

#### <a name="details"></a>詳細資料

如果已達到閒置超時，則會釋放埠。

### <a name="snat-port-reuse"></a>重複使用 SNAT 連接埠

| 適用的通訊協定 (s)  |
|------------------------|
| TCP </br> UDP |

#### <a name="details"></a>詳細資料

一旦釋放埠之後，埠就可供重複使用。 SNAT 埠是指定案例的最低到最高可用的順序，而第一個可用的 SNAT 埠則用於新的連接。

## <a name="limitations"></a>限制

- 每個前端 IP 位址的可用暫時連接埠數目上限為 64,000。
- 可設定輸出閒置逾時的範圍為 4 到 120 分鐘 (240 到 7200 秒)。
- 負載平衡器不支援輸出 NAT 的 ICMP。
- 輸出規則只能套用至 NIC 的主要 IP 設定。  您無法為 VM 或 NVA 的次要 IP 建立輸出規則。 支援多個 Nic。
- 使用內部標準負載平衡器時，可以存取沒有虛擬網路和其他 Microsoft 平臺服務的 Web 背景工作角色。 此協助工具是因為預先 VNet 服務和其他平臺服務運作方式的副作用。 請勿依賴此副作用作為個別服務本身，否則基礎平臺可能會變更，恕不另行通知。 如果只在使用內部標準負載平衡器時需要，請一律假設您需要明確建立輸出連線能力。 本文所述的案例3無法使用。

## <a name="next-steps"></a>後續步驟

如果您遇到透過 Azure Load Balancer 的輸出連線問題，請參閱輸出連線的 [疑難排解指南](../load-balancer/troubleshoot-outbound-connection.md)。

- 深入瞭解 [標準負載平衡器](load-balancer-standard-overview.md)。
- 請參閱 [關於 Azure Load Balancer](load-balancer-faqs.md)的常見問題。
- 深入瞭解標準公用負載平衡器的 [輸出規則](load-balancer-outbound-rules-overview.md) 。

