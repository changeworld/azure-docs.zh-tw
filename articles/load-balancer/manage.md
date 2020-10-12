---
title: Azure Load Balancer 入口網站設定
description: 開始瞭解 Azure Load Balancer 入口網站設定
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/8/2020
ms.author: allensu
ms.openlocfilehash: e1080aea12e70f4312fbee07b063d5a5cfbd1201
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89596262"
---
# <a name="azure-load-balancer-portal-settings"></a>Azure Load Balancer 入口網站設定

當您建立 Azure Load Balancer 時，本文中的資訊將協助您深入瞭解個別設定以及適合您的設定。

## <a name="create-load-balancer"></a>建立負載平衡器

Azure Load Balancer 是網路負載平衡器，會將流量分散至後端集區中的 VM 實例。

### <a name="basics"></a>基本概念

在 [建立負載平衡器入口網站] 頁面的 [ **基本** ] 索引標籤中，您會看到下列資訊：



| 設定 |  詳細資料 |
| ---------- | ---------- |
| 訂用帳戶  | 選取您的訂用帳戶。 此選項是您想要部署負載平衡器的訂用帳戶。 |
| 資源群組 | 選取 [ **建立新** 的]，然後在文字方塊中輸入資源群組的名稱。 如果您已建立現有的資源群組，請選取它。 |
| 名稱 | 這項設定是您 Azure Load Balancer 的名稱。 |
| 區域 | 選取您要部署負載平衡器的 Azure 區域。 |
| 類型 | 負載平衡器有兩種類型： </br> **內部 (私用) ** </br> **Public (External) **。</br> 內部負載平衡器 (ILB) 透過私人 IP 位址將流量路由至後端集區成員。</br> 公用負載平衡器會將來自用戶端的要求導向至後端集區。</br> 深入瞭解 [負載平衡器類型](components.md#frontend-ip-configuration-)。|
| SKU  | 選取 [標準]。 </br> 負載平衡器有兩個 Sku： **基本** 和 **標準**。 </br> 基本功能有限。 </br> 建議將**標準**用於生產工作負載。 </br> 深入瞭解 [sku](skus.md)。 |

如果您選取 [ **公用** ] 作為您的類型，您會看到下列資訊：

| 設定 |  詳細資料 |
| ---------- | ---------- |
| 公用 IP 位址 | 選取 [ **建立新** 的] 以建立公用負載平衡器的公用 IP 位址。 </br> 如果您有現有的公用 IP，請選取 [ **使用現有**的]。  |
| 公用 IP 位址名稱 | 公用 IP 位址的名稱。|
| 公用 IP 位址 SKU | 公用 IP 位址有兩個 Sku： **基本** 和 **標準**。 </br> 基本不支援區域復原和區域性屬性。 </br> 建議將**標準**用於生產工作負載。 </br> 負載平衡器和公用 IP 位址 Sku **必須相符**。 |
| 指派 | 標準會自動選取 [**靜態**]。 </br> 基本公用 Ip 有兩種類型： **動態** 和 **靜態**。 </br> 在建立之前，不會指派動態公用 IP 位址。 </br> 如果資源已刪除，Ip 可能會遺失。 </br> 建議使用靜態 IP 位址。 |
| 可用性區域 | 選取「區域備援」以建立復原性負載平衡器。 </br> 若要建立區域性負載平衡器，請選取 **1**、 **2**或 **3**的特定區域。 </br> 標準負載平衡器和公用 Ip 支援區域。 </br> 深入瞭解 [負載平衡器和可用性區域](load-balancer-standard-availability-zones.md)。 </br> 您看不到 [基本] 的區域選擇。 基本負載平衡器不支援區域。 |
| 新增公用 IPv6 位址 | 負載平衡器支援您前端的 **IPv6** 位址。 </br> 深入瞭解 [負載平衡器和 IPv6](load-balancer-ipv6-overview.md)
| 路由喜好設定 | 選取 [ **Microsoft 網路**]。 </br> Microsoft Network 表示流量會透過 Microsoft 全球網路路由傳送。 </br> 網際網路表示流量會透過網際網路服務提供者網路路由傳送。 </br> 深入瞭解[路由喜好](../virtual-network/routing-preference-overview.md)設定|

:::image type="content" source="./media/manage/create-public-load-balancer-basics.png" alt-text="建立負載平衡器公用。" border="true":::

如果您在 [類型] 中選取 [ **內部** ]，您將會看到下列資訊：

| 設定 |  詳細資料 |
| ---------- | ---------- |
| 虛擬網路 | 您要作為內部負載平衡器一部分的虛擬網路。 </br> 您為內部負載平衡器選取的私人前端 IP 位址會來自此虛擬網路。 |
| IP 位址指派 | 您的選項為 **靜態** 或 **動態**。 </br> 靜態可確保 IP 不會變更。 動態 IP 可能會變更。 |
| 可用性區域 | 選項包括： </br> **區域備援** </br> **區域 1** </br> **區域 2** </br> **區域 3** </br> 若要建立高度可用且可在可用性區域失敗時復原的負載平衡器，請選取 **區域冗余** IP。 |

:::image type="content" source="./media/manage/create-internal-load-balancer-basics.png" alt-text="建立負載平衡器公用。" border="true":::

## <a name="frontend-ip-configuration"></a>前端 IP 組態

Azure Load Balancer 的 IP 位址。 這是用戶端的連絡點。 

您可以有一或多個前端 IP 設定。 如果您已完成上述的基本概念一節，您可能已為負載平衡器建立前端。 

如果您想要將前端 IP 設定新增至負載平衡器，請移至 Azure 入口網站的負載平衡器，選取 [ **前端 ip**設定]，然後選取 [ **+ 新增**]。

| 設定 |  詳細資料 |
| ---------- | ---------- |
| 名稱 | 前端 IP 設定的名稱。 |
| IP 版本 | 您希望前端擁有的 IP 位址版本。 </br> 負載平衡器支援 IPv4 和 IPv6 前端 IP 設定。 |
| IP 類型 | IP 類型會判斷單一 IP 位址是否與您的前端相關聯，或使用 IP 首碼的 IP 位址範圍。 </br> 當您需要重複連接至相同端點時， [公用 IP 前置](../virtual-network/public-ip-address-prefix.md) 詞可協助您。 前置詞可確保提供足夠的埠來協助 SNAT 埠問題。 |
| 公用 IP 位址 (或前置詞（如果您選取上方的前置詞）)  | 為您的負載平衡器前端選取或建立新的公用 IP (或首碼) 。 |

:::image type="content" source="./media/manage/frontend.png" alt-text="建立負載平衡器公用。" border="true":::

## <a name="backend-pools"></a>後端集區

後端位址集區包含後端集區中虛擬網路介面的 IP 位址。 

如果您想要將後端集區新增至負載平衡器，請移至 Azure 入口網站的負載平衡器，選取 [ **後端**集區]，然後選取 [ **+ 新增**]。

| 設定 | 詳細資料 |
| ---------- |  ---------- |
| 名稱 | 您後端集區的名稱。 |
| 虛擬網路 | 您的後端實例所在的虛擬網路。 |
| IP 版本 | 您的選項是 **IPv4** 或 **IPv6**。 |

您可以將虛擬機器或虛擬機器擴展集新增至 Azure Load Balancer 的後端集區。 先建立虛擬機器或虛擬機器擴展集。 接下來，將它們新增至入口網站中的負載平衡器。

:::image type="content" source="./media/manage/backend.png" alt-text="建立負載平衡器公用。" border="true":::

## <a name="health-probes"></a>健康狀態探查

健康情況探查可用來監視後端 Vm 或實例的狀態。 健康情況探查狀態會根據健康情況檢查來決定何時將新的連接傳送至實例。 

如果您想要將健康情況探查新增至負載平衡器，請移至 Azure 入口網站的負載平衡器，選取 [ **健康情況探查**]，然後選取 [ **+ 新增**]。

| 設定 | 詳細資料 |
| ---------- | ---------- |
| 名稱 | 健康情況探查的名稱。 |
| 通訊協定 | 您選取的通訊協定會決定用來判斷後端實例 () 是否狀況良好的檢查類型。 </br> 選項包括： </br> **TCP** </br> **HTTPS** </br> **HTTP** </br> 確定您使用的是正確的通訊協定。 這項選擇取決於您的應用程式本質。 </br> 健康情況探查和探查回應的設定會決定要接收新流量的後端集區實例。 </br> 您可以使用健康情況探查來偵測後端端點上的應用程式失敗。 </br> 深入瞭解 [健康情況探查](load-balancer-custom-probe-overview.md)。 |
| 連接埠 | 健康情況探查的目的地埠。 </br> 這項設定是健康情況探查將用來判斷實例健康情況的後端實例上的埠。 |
| 間隔 | 探查嘗試之間的秒數。 </br> 此間隔會決定健康情況探查嘗試到達後端實例的頻率。 </br> 如果您選取 [5]，則會在5秒後進行第二次探查嘗試，依此類推。 |
| 狀況不良臨界值 | 在 VM 被視為狀況不良之前，必須發生的連續探查失敗次數。</br> 如果您選取2，在連續兩次失敗後，就不會將任何新的流程設定為這個後端實例。 |

:::image type="content" source="./media/manage/health-probe.png" alt-text="建立負載平衡器公用。" border="true":::

## <a name="load-balancing-rules"></a>負載平衡規則

定義連入流量如何分散至後端集區中的所有實例。 負載平衡規則會將指定的前端 IP 設定和埠對應至多個後端 IP 位址和埠。

如果您想要將負載平衡器規則新增至負載平衡器，請移至 Azure 入口網站的負載平衡器，選取 [ **負載平衡規則**]，然後選取 [ **+ 新增**]。
    
| 設定 | 詳細資料 |
| ---------- | ---------- |
| 名稱 | 負載平衡器規則的名稱。 |
| IP 版本 | 您的選項是 **IPv4** 或 **IPv6**。  |
| 前端 IP 位址 | 選取前端 IP 位址。 </br> 您要與負載平衡器規則相關聯的負載平衡器前端 IP 位址。|
| 通訊協定 | Azure Load Balancer 是第4層網路負載平衡器。 </br> 您的選項為： **TCP** 或 **UDP**。 |
| 連接埠 | 這項設定是與前端 IP 相關聯的埠，您想要根據此負載平衡規則來散發流量。 |
| 後端連接埠 | 這項設定是您想要讓負載平衡器將流量傳送到後端集區中實例上的埠。 如果您需要應用程式的彈性，此設定可以與前端埠相同或不同。 |
| 後端集區 | 您要套用此負載平衡器規則的後端集區。 |
| 健全狀況探查 | 您建立來檢查後端集區中實例狀態的健康狀態探查。 </br> 只有狀況良好的實例會收到新的流量。 |
| 工作階段持續性 |  選項包括： </br> **None** </br> **用戶端 IP** </br> **用戶端 IP 和通訊協定**</br> </br> 維護從用戶端到後端集區中相同虛擬機器的流量。 此流量會在會話的持續時間內進行維護。 </br> **None** 指定來自相同用戶端的後續要求可能會由任何虛擬機器處理。 </br> **用戶端 ip** 指定來自相同用戶端 ip 位址的後續要求會由相同的虛擬機器處理。 </br> **用戶端 ip 和通訊協定** 可確保相同的虛擬機器會處理來自相同用戶端 ip 位址和通訊協定的後續要求。 </br> 深入瞭解 [散發模式](load-balancer-distribution-mode.md)。 |
| 閒置逾時 (分鐘) | 讓 **TCP** 或 **HTTP** 連線保持開啟，而不依賴用戶端傳送 keep-alive 訊息 |  
| TCP 重設 | 負載平衡器可以傳送 **TCP 重設** ，以在連線閒置時，協助建立更容易預測的應用程式行為。 </br> 深入瞭解 [TCP 重設](load-balancer-tcp-reset.md)|
| 浮動 IP | 浮動 IP 是 Azure 的術語，其中部分稱為「 **伺服器直接回傳」 (DSR) **。 </br> DSR 包括兩個部分： <br> 1. 流程拓撲 </br> 2. 平台層級的 IP 位址對應配置。 </br></br> 無論是否啟用浮動 IP，Azure Load Balancer 一律會在 DSR 流程拓撲中操作。 </br> 這項作業表示流程的輸出部分一律會正確地重寫，以直接流回原始來源。 </br> 如果沒有浮動 IP，Azure 會公開傳統的負載平衡 IP 位址對應配置，也就是 VM 實例的 IP。 </br> 啟用浮動 IP 會將 IP 位址對應變更為負載平衡器的前端 IP，以提供更大的彈性。 </br> 如需詳細資訊，請參閱 [Azure Load Balancer 的多個前端](load-balancer-multivip-overview.md)。|
| 建立隱含輸出規則 | 請選取 [否] 。 </br> 預設值： **disableOutboundSnat = false**  </br> 在此情況下，輸出會透過相同的前端 IP 進行。 </br></br> **disableOutboundSnat = true** </br>在此情況下，輸出規則需要輸出規則。 |

:::image type="content" source="./media/manage/load-balancing-rule.png" alt-text="建立負載平衡器公用。" border="true":::

## <a name="inbound-nat-rules"></a>傳入的 NAT 規則

輸入 NAT 規則會將傳送的連入流量轉送到前端 IP 位址和埠組合。 

流量會傳送至後端集區中的特定虛擬機器或執行個體。 連接埠轉送作業會使用與負載平衡相同的雜湊式分送來完成。

如果您的案例需要遠端桌面通訊協定 (RDP) 或安全殼層 (SSH) 會話，以在後端集區中分隔 VM 實例。 您可以將多個內部端點對應至相同前端 IP 位址上的埠。 

前端 IP 位址可以用來從遠端系統管理 Vm，而不需要額外的跳箱。

如果您想要將輸入 nat 規則新增至負載平衡器，請移至 Azure 入口網站的負載平衡器，選取 [ **輸入 nat 規則**]，然後選取 [ **+ 新增**]。

| 設定 | 詳細資料 |
| ---------- | ---------- |
| 名稱 | 輸入 NAT 規則的名稱 |
| 前端 IP 位址 | 選取前端 IP 位址。 </br> 您要與輸入 NAT 規則相關聯的負載平衡器前端 IP 位址。 |
| IP 版本 | 您的選項為 IPv4 和 IPv6。 |
| 服務 | 您將在 Azure Load Balancer 上執行的服務類型。 </br> 這裡的選取專案會適當地更新埠資訊。 |
| 通訊協定 | Azure Load Balancer 是第4層網路負載平衡器。 </br> 您的選項為： TCP 或 UDP。 |
| 閒置逾時 (分鐘) | 讓 TCP 或 HTTP 連線保持開啟，而不依賴用戶端來傳送 keep-alive 訊息。 |
| TCP 重設 | Load Balancer 可以傳送 TCP 重設，以在連線閒置時，協助建立更容易預測的應用程式行為。 </br> 深入瞭解 [TCP 重設](load-balancer-tcp-reset.md) |
| 連接埠 | 這項設定是與前端 IP 相關聯的埠，您想要根據此輸入 NAT 規則來散發流量。 |
| 目標虛擬機器 | 您要與此規則相關聯的後端集區的虛擬機器部分。 |
| 埠對應 | 根據您的應用程式喜好設定，此設定可以是預設或自訂的。 |

:::image type="content" source="./media/manage/inbound-nat-rule.png" alt-text="建立負載平衡器公用。" border="true":::

## <a name="outbound-rules"></a>輸出規則

負載平衡器輸出規則會在後端集區中設定 VM 的輸出 SNAT。

如果您想要將輸出規則新增至負載平衡器，請移至 Azure 入口網站的負載平衡器，選取 [ **輸出規則**]，然後選取 [ **+ 新增**]。

| 設定 | 詳細資料 |
| ------- | ------ |
| 名稱 | 輸出規則的名稱。 |
| 前端 IP 位址 | 選取前端 IP 位址。 </br> 您要讓輸出規則與之產生關聯的負載平衡器前端 IP 位址。 |
| 通訊協定 | Azure Load Balancer 是第4層網路負載平衡器。 </br> 您的選項包括： **All**、 **TCP**或 **UDP**。 |
| 閒置逾時 (分鐘) | 讓 **TCP** 或 **HTTP** 連線保持開啟，而不依賴用戶端來傳送 keep-alive 訊息。 |
| TCP 重設 | 負載平衡器可以傳送 **TCP 重設** ，以在連線閒置時，協助建立更容易預測的應用程式行為。 </br> 深入瞭解 [TCP 重設](load-balancer-tcp-reset.md) |
| 後端集區 | 您要套用此輸出規則的後端集區。 |

### <a name="port-allocation"></a>埠配置

| 設定 | 詳細資料 |
| ------- | ------ |
| 埠配置 | 建議您選取 **[手動選擇輸出埠數目**]。|

### <a name="outbound-ports"></a>輸出連接埠

| 設定 | 詳細資料 |
| ------- | ------ |
| 選擇依據 | 選取 [每個執行個體的連接埠數] |
| 每個實例的埠 | 輸入 **10000**。 |

:::image type="content" source="./media/manage/outbound-rule.png" alt-text="建立負載平衡器公用。" border="true":::

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解 Azure Load Balancer Azure 入口網站中的不同詞彙和設定。

* [深入瞭解](./load-balancer-overview.md) Azure Load Balancer。
* Azure Load Balancer 的[常見問題](./load-balancer-faqs.md)。