---
title: 使用 NAT 閘道資源設計虛擬網路
titleSuffix: Azure Virtual Network NAT
description: 了解如何使用 NAT 閘道資源來設計虛擬網路。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about how to design virtual networks with NAT gateway resources.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 9d9033689472fbc462eefd3d31c2b36a19add81a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589465"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources-public-preview"></a>使用 NAT 閘道資源設計虛擬網路 (公開預覽版)

NAT 閘道資源是[虛擬網路 NAT](nat-overview.md) 的一部分，可為虛擬網路的一或多個子網提供輸出網際網路連線能力。 虛擬網路的子網路會指出將使用哪個 NAT 閘道。 NAT 會為子網路提供來源網路位址轉譯 (SNAT)。  NAT 閘道資源會指定虛擬機器在建立輸出流量時所使用的靜態 IP 位址。 靜態 IP 位址來自公用 IP 位址資源、公用 IP 前置詞資源或兩者。 NAT 閘道資源最多可以使用來自任一資源的 16 個靜態 IP 位址。


<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="可供輸出到網際網路的虛擬網路 NAT">
</p>

*圖：可供輸出到網際網路的虛擬網路 NAT*


>[!NOTE] 
>虛擬網路 NAT 目前可作為公開預覽版提供。 其目前僅適用於一組有限的[區域](nat-overview.md#region-availability)。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.comsupport/legal/preview-supplemental-terms)。

## <a name="how-to-deploy-nat"></a>如何部署 NAT

已刻意簡化 NAT 閘道的設定和使用：  

NAT 閘道資源：
- 建立地區性或區域性 (區域隔離) NAT 閘道資源，
- 指派 IP 位址，
- 修改閒置逾時 (選擇性)。

虛擬網路：
- 設定要使用 NAT 閘道的虛擬網路子網路。

不需要使用者定義的路由。

## <a name="resource"></a>資源

您可以從使用類似範本格式的下列 Azure Resource Manager 範例中看出，資源的設計簡單明瞭。  此類似範本的格式如下所示，以說明概念和結構。  修改範例以滿足您的需求。  本文件不打算作為教學課程。

下圖顯示不同 Azure Resource Manager 資源之間的可寫入參考。  箭號表示參考的方向，其源自可寫入的位置。 檢閱 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" width="256" title="虛擬網路 NAT 物件模型">
</p>

*圖：虛擬網路 NAT 物件模型*

除非您有特定的[集區型負載平衡器輸出連線](../load-balancer/load-balancer-outbound-connections.md)相依性，否則建議對大部分的工作負載使用 NAT。  

您可以從標準負載平衡器案例 (包括[輸出規則](../load-balancer/load-balancer-outbound-rules-overview.md)) 遷移到 NAT 閘道。 若要遷移，請將公用 IP 和公用 IP 前置詞資源從負載平衡器前端移到 NAT 閘道。 不需要 NAT 閘道的新 IP 位址。 只要總計不超過 16 個 IP 位址，就可以重複使用標準公用 IP 和前置詞。 在轉換期間，請針對已考量服務中斷情形的移轉進行規劃。  您可藉由程序自動化將中斷情形降至最低。 先在預備環境中測試移轉。  在轉換期間，源自的輸入流量不受影響。

下列範例會建立一個名為 myNATGateway  的 NAT 閘道資源，其建立於「美國東部 2，AZ 1」  地區，且閒置逾時為「4 分鐘」  。 提供的輸出 IP 位址如下：
- 一組公用 IP 位址資源 _myIP1_ 和 _myIP2_，以及 
- 一組公用 IP 前置詞資源 _myPrefix1_ 和 _myPrefix2_。 

所有四個 IP 位址資源所提供的 IP 位址總數不能超過 16 個 IP 位址總計。 允許的 IP 位址數目介於 1 到 16 之間。

```json
{
"name": "myNATGateway",
   "type": "Microsoft.Network/natGateways",
   "apiVersion": "2018-11-01",
   "location": "East US 2",
   "sku": { "name": "Standard" },
   "zones": [ "1" ],
   "properties": {
      "idleTimeoutInMinutes": 4, 
      "publicIPPrefixes": [
         {
            "id": "ref to myPrefix1"
         },
         {
            "id": "ref to myPrefix2"
         }
      ],
      "publicIPAddresses": [
         {
            "id": "ref to myIP1"
         },
         {
            "id": "ref to myIP2"
         }
      ]
   }
}
```

建立 NAT 閘道資源後，即可將其使用於虛擬網路的一或多個子網路。 指定哪些子網路使用此 NAT 閘道資源。 NAT 閘道無法跨越一個以上的虛擬網路。 不需要將相同的 NAT 閘道指派給虛擬網路的所有子網路。 可以設定具有不同 NAT 閘道資源的個別子網路。

未使用可用性區域的案例會是地區性 (未指定區域)。 如果您使用可用性區域，則可指定區域以將 NAT 隔離到特定區域。 不支援區域備援。 檢閱 NAT [可用性區域](#availability-zones)。


```json
{
   "name": "myVNet",
   "apiVersion": "2018-11-01",
   "type": "Microsoft.Network/virtualNetworks",
   "location": "myRegion", 
   "properties": {
      "addressSpace": {
          "addressPrefixes": [
           "192.168.0.0/16"
          ]
      },
      "subnets": [
         {
            "name": "mySubnet1",
            "properties": {
               "addressPrefix": "192.168.0.0/24",
               "natGateway": {
                  "id": "ref to myNATGateway"
               }
            }
         } 
      ]
   }
}
```
NAT 閘道是以虛擬網路內子網路上的屬性來定義。 虛擬機器在虛擬網路 myVNet  的子網路 mySubnet1  上建立的流量將會使用 NAT 閘道。 所有輸出連線都會使用與 myNatGateway  相關聯的 IP 位址作為來源 IP 位址。


## <a name="design-guidance"></a>設計指導

請回顧這一節，以熟悉使用 NAT 設計虛擬網路時的考慮事項。  

1. [成本最佳化](#cost-optimization)
1. [輸入和輸出共存](#coexistence-of-inbound-and-outbound)
2. [管理基本資源](#managing-basic-resources)
3. [可用性區域](#availability-zones)

### <a name="cost-optimization"></a>成本最佳化

[服務端點](virtual-network-service-endpoints-overview.md)和[私人連結](../private-link/private-link-overview.md)是在不需要 NAT 的情況下，將成本最佳化所要考慮的兩個選項。  虛擬網路的 NAT 不會處理任何導向服務端點或私人連結的流量。  

服務端點會將 Azure 服務資源繫結至您的虛擬網路，並控制對您 Azure 服務資源的存取。 例如，當您存取 Azure 儲存體時，請使用儲存體的服務端點來避免已資料處理的 NAT 費用。 服務端點是免費的。

私人連結會公開 Azure PaaS 服務 (或其他以私人連結裝載的服務) 作為虛擬網路內的私人端點。  私人連結會根據持續時間和已處理的資料來計費。

評估這兩種方法是否適合您的案例，並視需要使用。

### <a name="coexistence-of-inbound-and-outbound"></a>輸入和輸出共存

NAT 閘道與下列項目相容：

 - 標準負載平衡器
 - 標準公用 IP
 - 標準公用 IP 前置詞

開發新的部署時，請從標準 SKU 開始。

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="可供輸出到網際網路的虛擬網路 NAT">
</p>

*圖：可供輸出到網際網路的虛擬網路 NAT*

NAT 閘道所提供的僅限網際網路輸出案例，可以使用從網際網路輸入功能進行擴充。 每個資源都能感知流量源自的方向。 在具有 NAT 閘道的子網路上，NAT 閘道會取代所有輸出到網際網路的案例。 從網際網路輸入案例是由各自的資源所提供。

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>具有執行個體層級公用 IP 的 NAT 和 VM

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" width="300" title="具有執行個體層級公用 IP 的虛擬網路 NAT 和 VM">
</p>

*圖：具有執行個體層級公用 IP 的虛擬網路 NAT 和 VM*

| 方向 | 資源 |
|:---:|:---:|
| 輸入 | 具有執行個體層級公用 IP 的 VM |
| 輸出 | NAT 閘道 |

VM 將使用 NAT 閘道進行輸出。  源自的輸入不受影響。

#### <a name="nat-and-vm-with-public-load-balancer"></a>具有公用負載平衡器的 NAT 和 VM

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" width="350" title="具有公用負載平衡器的虛擬網路 NAT 和 VM">
</p>

*圖：具有公用負載平衡器的虛擬網路 NAT 和 VM*

| 方向 | 資源 |
|:---:|:---:|
| 輸入 | 公用負載平衡器 |
| 輸出 | NAT 閘道 |

負載平衡規則或輸出規則中的任何輸出組態都會由 NAT 閘道取代。  源自的輸入不受影響。

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>具有執行個體層級公用 IP 和公用負載平衡器的 NAT 和 VM

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" width="425" title="具有執行個體層級公用 IP 和公用負載平衡器的虛擬網路 NAT 和 VM">
</p>

*圖：具有執行個體層級公用 IP 和公用負載平衡器的虛擬網路 NAT 和 VM*

| 方向 | 資源 |
|:---:|:---:|
| 輸入 | 具有執行個體層級公用 IP 和公用負載平衡器的 VM |
| 輸出 | NAT 閘道 |

負載平衡規則或輸出規則中的任何輸出組態都會由 NAT 閘道取代。  VM 也會使用 NAT 閘道進行輸出。  源自的輸入不受影響。

### <a name="managing-basic-resources"></a>管理基本資源

標準負載平衡器、公用 IP 和公用 IP 前置詞都與 NAT 閘道相容。 NAT 閘道會在子網路的範圍內運作。 這些服務的基本 SKU 都必須部署在沒有 NAT 閘道的子網路上。 此種區隔可讓這兩個 SKU 變體共存於相同的虛擬網路中。

NAT 閘道的優先順序高於子網路的輸出案例。 基本負載平衡器或公用 IP (及任何以其建置的受控服務) 都無法透過正確的轉譯進行調整。 NAT 閘道會控制子網路上的輸出到網際網路流量。 無法使用對基本負載平衡器和公用 IP 的輸入流量。 對基本負載平衡器的輸入流量，或對 VM 上所設公用 IP 的輸入流量將無法使用。

### <a name="availability-zones"></a>可用性區域

即使沒有可用性區域，NAT 也具有復原性，且可能在多個基礎結構元件失敗中倖存。 當可用性區域屬於您的案例時，您應該為特定區域設定 NAT。  控制平面作業和資料平面會受限於指定的區域。 不是您的案例所在區域中的失敗，應該不會對 NAT 造成影響。 因為區域隔離，來自相同區域中虛擬機器的輸出流量將會失敗。

<p align="center">
  <img src="media/nat-overview/az-directions.svg" width="425" title="具有可用性區域的虛擬網路 NAT">
</p>

*圖：具有可用性區域的虛擬網路 NAT*

區域隔離的 NAT 閘道會要求 IP 位址符合 NAT 閘道的區域。 不支援 IP 位址來自不同區域或沒有區域的 NAT 閘道資源。

虛擬網路和子網路是地區性，而不是區域性對齊。 VM 必須位於與 NAT 閘道相同的區域，以達成輸出連線的區域性承諾。 為每個可用性區域建立區域性「堆疊」，進而建立區域隔離。 跨區域性 NAT 閘道的區域，或使用地區性 NAT 閘道搭配區域性 VM 時，不會有區域性承諾。

當您部署要搭配 NAT 使用的虛擬機器擴展集時，您會在其自己的子網路上部署區域性擴展集，並將相符的區域 NAT 閘道連結至該子網路。 如果您使用跨區域的擴展集 (兩個或多個區域中的擴展集)，NAT 將不會提供區域性承諾。  NAT 不支援區域備援。  僅支援地區性或區域隔離。

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" width="425" title="跨區域虛擬網路 NAT">
</p>

*圖：跨區域虛擬網路 NAT*

區域屬性不可變。  使用預定地區性或區域喜好設定來重新部署 NAT 閘道資源。

>[!NOTE] 
>如果未指定任何區域，IP 位址本身不支援區域備援。  如果未在特定區域中建立 IP 位址，則[標準負載平衡器的前端支援區域備援](../load-balancer/load-balancer-standard-availability-zones.md#frontend)。  這不適用於 NAT。  僅支援地區性或區域隔離。

## <a name="source-network-address-translation"></a>來源網路位址轉譯

來源網路位址轉譯 (SNAT) 會將流量來源重寫為源自不同的 IP 位址。  NAT 閘道資源會使用 SNAT 的變體，通常稱為連接埠位址轉換 (PAT)。 PAT 會重寫來源位址和來源連接埠。 使用 SNAT，私人位址數與其轉譯的公用位址數之間沒有固定的關聯性。  

### <a name="fundamentals"></a>基礎

讓我們看一下四個流量的範例，以說明基本概念。  NAT 閘道正在使用公用 IP 位址資源 65.52.0.2。

| Flow | 來源元組 | 目的地元組 |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

進行 PAT 之後，這些流量可能如下所示：

| Flow | 來源元組 | 已進行 SNAT 的來源元組 | 目的地元組 | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.2:234 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.2:235 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.2:236 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:237 | 65.52.0.2:80 |

目的地會看到流量的來源為 65.52.0.2 (SNAT 來源元組)，並顯示指派的連接埠。  上表所示的 PAT 也稱為連接埠偽裝 SNAT。  有多個私人來源會偽裝在 IP 和連接埠後面。

請勿依賴於特定指派來源連接埠的方式。  上述只是基本概念的說明。

NAT 所提供的 SNAT 不同於數個層面的 [Load Balancer](../load-balancer/load-balancer-outbound-connections.md)。

### <a name="on-demand"></a>隨選

NAT 會針對新的輸出流量提供隨選 SNAT 連接埠。 透過 NAT 設定的子網路上的任何虛擬機器都會使用庫存中所有可用的 SNAT 連接埠。 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" width="550" title="虛擬網路 NAT 隨選輸出 SNAT">
</p>

*圖：虛擬網路 NAT 隨選輸出 SNAT*

虛擬機器的任何 IP 組態均可視需要建立隨選輸出流量。  不需要根據執行個體規劃 (包括根據執行個體最差案例過度佈建) 預先配置。  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" width="550" title="消耗案例的差異">
</p>

*圖：消耗案例的差異*

SNAT 連接埠一旦發行，即可供透過 NAT 設定的子網路上的任何虛擬機器使用。  隨選配置可讓子網路上的動態和分歧工作負載，視需要使用 SNAT 連接埠。  只要有 SNAT 連接埠庫存可用，SNAT 流量就會成功。 而 SNAT 連接埠作用點受惠於較大量的庫存。 SNAT 連接埠不會針對沒有積極需求的虛擬機器保留未使用。

### <a name="scaling"></a>調整大小

NAT 對於完整輸出案例需要足夠的 SNAT 連接埠庫存。 調整 NAT 主要是一項管理共用、可用 SNAT 連接埠庫存的功能。 必須要有足夠的庫存，才能解決連結到 NAT 閘道資源的所有子網路尖峰輸出流量。

SNAT 會將多個私人位址對應到一個公用位址，並使用多個公用 IP 進行調整。

NAT 閘道資源將使用公用 IP 位址的 64000 個連接埠 (SNAT 連接埠)。  這些 SNAT 連接埠會成為私人到公用流量對應的可用庫存。 新增更多公用 IP 位址會增加可用的庫存 SNAT 連接埠。 NAT 閘道資源可以擴充至 16 個 IP 位址和 1 百萬個 SNAT 連接埠。  TCP 和 UDP 是不同的 SNAT 連接埠清查而且不相關。

NAT 閘道資源會伺機重複使用來源連接埠。 基於調整目的，您應該假設每個流量都需要新的 SNAT 連接埠，並調整輸出流量的可用 IP 位址總數。

### <a name="protocols"></a>通訊協定

NAT 閘道資源會與 UDP 和 TCP 流量的 IP 和 IP 傳輸標頭互動，而且與應用程式層承載無關。  不支援其他 IP 通訊協定。

### <a name="timers"></a>計時器

所有流量的閒置逾時可以從 4 分鐘 (預設值) 調整為 120 分鐘 (2 小時)。  此外，您可以使用流量來重設閒置計時器。  重新整理長時間閒置連線和端點活躍度偵測的建議模式為 TCP Keepalive。  TCP Keepalive 會顯示為端點的重複 ACK、低負擔，且對應用程式層而言不可見。

下列計時器用於 SNAT 連接埠版本：

| 計時器 | 值 |
|---|---|
| TCP FIN | 60 秒 |
| TCP RST | 10 秒 |
| TCP 半開放 | 30 秒 |

在 5 秒後，SNAT 連接埠可供重複用於相同的目的地 IP 位址和目的地連接埠。

>[!NOTE] 
>這些計時器設定可能隨時變更。 系統會提供設定值來協助進行疑難排解，而您此時不得依賴於特定的計時器。

## <a name="limitations"></a>限制

- NAT 與標準 SKU 公用 IP、公用 IP 前置詞和負載平衡器資源相容。   基本資源 (例如基本負載平衡器) 及其衍生的所有產品都不會與 NAT 相容。  基本資源必須置於未透過 NAT 設定的子網路上。
- 支援 IPv4 位址系列。  NAT 不會與 IPv6 位址系列互動。
- 使用 NAT 時，不支援 NSG 流量記錄。
- NAT 無法跨越多個虛擬網路。

## <a name="preview-participation"></a>預覽參與

依照[指示，啟用您的訂用帳戶](nat-overview.md#public-preview-participation)。

## <a name="feedback"></a>意見反應

我們想要知道如何改善服務。 與我們分享您對[公開預覽版的意見反應](https://aka.ms/natfeedback)。  您也可針對我們接下來應在[適用於 NAT 的 UserVoice](https://aka.ms/natuservoice) 建置的項目，提出建議和投票。

## <a name="next-steps"></a>後續步驟

- 深入了解[虛擬網路 NAT](nat-overview.md)。
- 驗證 NAT 閘道的教學課程
  * [Azure CLI](tutorial-create-validate-nat-gateway-cli.md)，
  * [PowerShell](tutorial-create-validate-nat-gateway-cli.md)，
  * [入口網站](tutorial-create-validate-nat-gateway-cli.md)
- 部署 NAT 閘道資源的快速入門
  * [Azure CLI](./quickstart-create-nat-gateway-cli.md)，
  * [PowerShell](./quickstart-create-nat-gateway-powershell.md)，
  * [入口網站](./quickstart-create-nat-gateway-portal.md)。
- 深入了解[可用性區域](../availability-zones/az-overview.md)。
- 深入了解[標準負載平衡器](../load-balancer/load-balancer-standard-overview.md)。
- 深入了解[可用性區域和標準負載平衡器](../load-balancer/load-balancer-standard-availability-zones.md)。
- 深入了解 NAT 閘道資源 API
  * [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/natgateways)，
  * [Azure CLI](https://docs.microsoft.com/cli/azure/network/nat/gateway?view=azure-cli-latest)，
  * [PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway)。
- [告訴我們接下來要在 UserVoice 中建置的項目](https://aka/natuservoice)。
- [提供有關公開預覽版的意見反應](https://aka.ms/natfeedback)。
