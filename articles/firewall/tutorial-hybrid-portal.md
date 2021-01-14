---
title: 教學課程：使用 Azure 入口網站在混合式網路中部署及設定 Azure 防火牆
description: 在本教學課程中，您將了解如何使用 Azure 入口網站部署及設定 Azure 防火牆。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/17/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 86e27c190b269763d8dd2f562a207b3f2020da29
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051066"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-the-azure-portal"></a>教學課程：使用 Azure 入口網站在混合式網路中部署及設定 Azure 防火牆

當您將內部部署網路連線到 Azure 虛擬網路以建立混合式網路時，控制 Azure 網路資源存取的能力是整體安全性計劃的重要部分。

您可以使用定義允許和拒絕網路流量的規則，在混合式網路中使用 Azure 防火牆來控制網路存取。

在本教學課程中，您會建立三個虛擬網路：

- **VNet-Hub** - 防火牆位於此虛擬網路中。
- **VNet-Spoke** - 輪輻虛擬網路代表位於 Azure 的工作負載。
- **VNet-Onprem** - 內部部署虛擬網路代表內部部署網路。 在實際部署中，它可經由 VPN 或 ExpressRoute 連線來連線。 為了簡單起見，本教學課程使用 VPN 閘道連線，而位於 Azure 的虛擬網路用來代表內部部署網路。

![混合式網路中的防火牆](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 宣告變數
> * 建立防火牆中樞虛擬網路
> * 建立輪輻虛擬網路
> * 建立內部部署虛擬網路
> * 設定及部署防火牆
> * 建立及連線 VPN 閘道
> * 對等互連中樞與輪輻虛擬網路
> * 建立路由
> * 建立虛擬機器
> * 測試防火牆

如果您想要改為使用 Azure PowerShell 來完成此程序，請參閱[使用 Azure PowerShell 在混合式網路中部署和設定 Azure 防火牆](tutorial-hybrid-ps.md)。

## <a name="prerequisites"></a>必要條件

混合式網路會使用中樞和輪輻架構模型來路由傳送 Azure VNet 與內部部署網路之間的流量。 中樞和輪輻架構具有下列需求：

- 將 VNet-Hub 對等互連至 VNet-Spoke 時設定 **AllowGatewayTransit**。 在中樞與輪輻式 (hub-and-spoke) 網路架構中，閘道傳輸會讓輪輻虛擬網路共用中樞內的 VPN 閘道，而不是將 VPN 閘道部署在每個輪輻虛擬網路中。 

   此外，通往閘道連線虛擬網路或內部部署網路的路由，將會自動傳播到使用閘道傳輸的對等虛擬網路路由表。 如需詳細資訊，請參閱[為虛擬網路對等互連設定 VPN 閘道傳輸](../vpn-gateway/vpn-gateway-peering-gateway-transit.md)。

- 當您將 VNet-Spoke 對等互連至 VNet-Hub 時，請設定 **UseRemoteGateways**。 如果已設定 **UseRemoteGateways**，也已在遠端對等互連上設定 **AllowGatewayTransit**，則輪輻虛擬網路會使用遠端虛擬網路的閘道進行傳輸。
- 若要透過中樞防火牆路由傳送輪輻子網路流量，您可以使用使用者定義的路由 (UDR)，其指向已停用 [虛擬網路閘道路由傳播] 選項的防火牆。 [虛擬網路閘道路由傳播] 選項停用時，會防止將路由散發到輪輻子網路。 這可防止已學習的路由與您的 UDR 衝突。 如果您想要將 [虛擬網路閘道路由傳播] 保留為啟用狀態，請務必定義防火牆的特定路由，以覆寫透過 BGP 從內部部署發佈的路由。
- 在中樞閘道子網路上設定可指向防火牆 IP 位址的 UDR，作為輪輻網路的下一個躍點。 Azure 防火牆子網路不需要任何 UDR，因為可從 BGP 得知路由。

請參閱本教學課程中的[建立路由](#create-the-routes)一節，了解如何建立這些路由。

>[!NOTE]
>「Azure 防火牆」必須能夠直接連線到網際網路。 如果您的 AzureFirewallSubnet 學習到透過 BGP 連至您內部部署網路的預設路由，您必須將其覆寫為 0.0.0.0/0 UDR，且 **NextHopType** 值必須設為 [網際網路]，以保有直接網際網路連線。
>
>您可將 Azure 防火牆設定為支援強制通道。 如需詳細資訊，請參閱 [Azure 防火牆強制通道](forced-tunneling.md)。

>[!NOTE]
>即使 UDR 指向「Azure 防火牆」作為預設閘道，系統仍會直接路由直接對等互連之 VNet 間的流量。 在此案例中若要將子網路對子網路流量傳送到防火牆，UDR 必須在這兩個子網路上同時明確包含目標子網路網路首碼。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-the-firewall-hub-virtual-network"></a>建立防火牆中樞虛擬網路

首先，建立資源群組，以包含本教學課程中的資源：

1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。
2. 在 Azure 入口網站首頁上，選取 [資源群組] > [新增]。
3. 在 [訂用帳戶] 中，選取您的訂用帳戶。
1. 在 [資源群組名稱] 中，輸入 **FW-Hybrid-Test**。
2. 針對 [區域]，選取 [(美國) 美國東部]。 您稍後建立的所有資源都必須位在相同的位置。
3. 選取 [檢閱 + 建立]。
4. 選取 [建立]。

現在，您可以開始建立 VNet：

> [!NOTE]
> AzureFirewallSubnet 子網路的大小是 /26。 如需有關子網路大小的詳細資訊，請參閱 [Azure 防火牆的常見問題集](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size)。

1. 從 Azure 入口網站首頁，選取 [建立資源]。
2. 在 [網路] 底下，選取 [虛擬網路]。
1. 選取 [建立]。
1. 在 [資源群組] 中，選取 **FW-Hybrid-Test**。
1. 在 [名稱] 中，輸入 **VNet-hub**。
1. 完成時，選取 [下一步:IP 位址]。
1. 針對 **IPv4 位址空間**，刪除預設位址，並輸入 **10.5.0.0/16**。
1. 在 [子網路名稱] 下，選取 [新增子網路]。
1. 針對 [子網路名稱]，輸入 **AzureFirewallSubnet**。 防火牆會在此子網路中，且子網路名稱 **必須** 是 AzureFirewallSubnet。
1. 針對 [子網路位址範圍]，輸入 **10.5.0.0/26**。 
1. 選取 [新增]。
1. 選取 [檢閱 + 建立]  。
1. 選取 [建立]。

## <a name="create-the-spoke-virtual-network"></a>建立輪輻虛擬網路

1. 從 Azure 入口網站首頁，選取 [建立資源]。
2. 在 [網路] 中，選取 [虛擬網路]。
7. 在 [資源群組] 中，選取 **FW-Hybrid-Test**。
1. 在 [名稱] 中，輸入 **VNet-Spoke**。
2. 針對 [區域]，選取 [(美國) 美國東部]。
3. 完成時，選取 [下一步:IP 位址]。
4. 針對 **IPv4 位址空間**，刪除預設位址，並輸入 **10.6.0.0/16**。
6. 在 [子網路名稱] 下，選取 [新增子網路]。
7. 針對 [子網路名稱]，輸入 **SN-Workload**。
8. 針對 [子網路位址範圍]，輸入 **10.6.0.0/24**。 
9. 選取 [新增]。
10. 選取 [檢閱 + 建立]  。
11. 選取 [建立]。

## <a name="create-the-on-premises-virtual-network"></a>建立內部部署虛擬網路

1. 從 Azure 入口網站首頁，選取 [建立資源]。
2. 在 [網路] 中，選取 [虛擬網路]。
7. 在 [資源群組] 中，選取 **FW-Hybrid-Test**。
1. 在 [名稱] 中，輸入 **VNet-OnPrem**。
2. 針對 [區域]，選取 [(美國) 美國東部]。
3. 選取 下一步：IP 位址
4. 針對 **IPv4 位址空間**，刪除預設位址，並輸入 **192.168.0.0/16**。
5. 在 [子網路名稱] 下，選取 [新增子網路]。
7. 針對 [子網路名稱]，輸入 **SN-Corp**。
8. 針對 [子網路位址範圍]，輸入 **192.168.1.0/24**。 
9. 選取 [新增]。
10. 選取 [檢閱 + 建立]  。
11. 選取 [建立]。

現在，為閘道建立第二個子網路。

1. 在 [VNet-Onprem] 頁面上，選取 [子網路]。
2. 選取 [+ 子網路]。
3. 在 [名稱] 中，輸入 **GatewaySubnet**。
4. 針對 [子網路位址範圍]，輸入 **192.168.2.0/24**。
5. 選取 [確定]。

## <a name="configure-and-deploy-the-firewall"></a>設定及部署防火牆

現在將防火牆部署到防火牆中樞虛擬網路中。

1. 從 Azure 入口網站首頁，選取 [建立資源]。
2. 在左欄中，選取 [網路]，然後搜尋並選取 [防火牆]。
4. 在 [建立防火牆]  頁面上，使用下表來設定防火牆：

   |設定  |值  |
   |---------|---------|
   |訂用帳戶     |\<your subscription\>|
   |資源群組     |**FW-Hybrid-Test** |
   |名稱     |**AzFW01**|
   |區域     |美國東部|
   |選擇虛擬網路     |**使用現有項目**︰<br> **VNet-hub**|
   |公用 IP 位址     |新增： <br>**fw-pip**。 |

5. 選取 [檢閱 + 建立]。
6. 檢閱摘要，然後選取 [建立] 來建立防火牆。

   部署需要幾分鐘的時間。
7. 部署完成之後，請前往 **FW-Hybrid-Test** 資源群組，然後選取 **AzFW01** 防火牆。
8. 請記下私人 IP 位址。 稍後當您建立預設路由時將使用到它。

### <a name="configure-network-rules"></a>設定網路規則

首先，新增網路規則以允許 Web 流量。

1. 在 [AzFW01] 頁面上，選取 [規則]。
2. 選取 [網路規則集合] 索引標籤。
3. 選取 [新增網路規則集合]。
4. 在 [名稱] 中，輸入 **RCNet01**。
5. 在 [優先順序] 中，輸入 **100**。
6. 在 [動作] 中，選取 [允許]。
6. 在 [規則] 底下的 [名稱] 中，輸入 **AllowWeb**。
7. 在 [通訊協定] 中，選取 [TCP]。
8. 針對 [來源類型]，選取 [IP 位址]。
9. 針對 [來源]，輸入 **192.168.1.0/24**。
10. 針對 [目的地類型]，選取 [IP 位址]。
11. 針對 [目的地位址]，輸入 **10.6.0.0/16**
12. 在 [目的地連接埠] 中，輸入 **80**。

現在新增規則以允許 RDP 流量。

在第二個規則資料列上，輸入下列資訊：

1. 在 [名稱] 中輸入 **AllowRDP**。
2. 在 [通訊協定] 中，選取 [TCP]。
3. 針對 [來源類型]，選取 [IP 位址]。
4. 針對 [來源]，輸入 **192.168.1.0/24**。
5. 針對 [目的地類型]，選取 [IP 位址]。
6. 針對 [目的地位址]，輸入 **10.6.0.0/16**
7. 在 [目的地連接埠] 中，輸入 **3389**。
8. 選取 [新增]。

## <a name="create-and-connect-the-vpn-gateways"></a>建立及連線 VPN 閘道

中樞和內部部署虛擬網路都是透過 VPN 閘道連線。

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>建立中樞虛擬網路的 VPN 閘道

現在建立中樞虛擬網路的 VPN 閘道。 網路對網路組態需要 RouteBased VpnType。 建立 VPN 閘道通常可能需要 45 分鐘或更久，視選取的 VPN 閘道 SKU 而定。

1. 從 Azure 入口網站首頁，選取 [建立資源]。
2. 在搜尋文字方塊中，輸入 **虛擬網路閘道**。
3. 選取 [虛擬網路閘道]，然後選取 [建立]。
4. 在 [名稱] 中，輸入 **GW-hub**。
5. 在 [區域] 中，選取您先前使用的相同區域。
6. 在 [閘道類型] 中，選取 [VPN]。
7. 在 [VPN 類型] 中，選取 [路由式]。
8. 在 [SKU]中，選取 [基本]。
9. 在 [虛擬網路] 中，選取 [VNet-hub]。
10. 在 [公用 IP 位址] 中，選取 [新建]，然後輸入 **VNet-hub-GW-pip** 作為名稱。
11. 接受其餘的預設值，然後選取 [檢閱 + 建立]。
12. 檢閱設定，然後選取 [建立]。

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>建立虛擬網路的 VPN 內部部署閘道

現在建立虛擬網路的 VPN 內部部署閘道。 網路對網路組態需要 RouteBased VpnType。 建立 VPN 閘道通常可能需要 45 分鐘或更久，視選取的 VPN 閘道 SKU 而定。

1. 從 Azure 入口網站首頁，選取 [建立資源]。
2. 在搜尋文字方塊中，輸入 **輸入虛擬網路閘道**，然後按 **Enter**。
3. 選取 [虛擬網路閘道]，然後選取 [建立]。
4. 在 [名稱] 中，輸入 **GW-Onprem**。
5. 在 [區域] 中，選取您先前使用的相同區域。
6. 在 [閘道類型] 中，選取 [VPN]。
7. 在 [VPN 類型] 中，選取 [路由式]。
8. 在 [SKU]中，選取 [基本]。
9. 在 [虛擬網路] 中，選取 [VNet-Onprem]。
10. 在 [公用 IP 位址] 中，選取 [新建]，然後輸入 **VNet-Onprem-GW-pip** 作為名稱。
11. 接受其餘的預設值，然後選取 [檢閱 + 建立]。
12. 檢閱設定，然後選取 [建立]。

### <a name="create-the-vpn-connections"></a>建立 VPN 連線

您現在可以建立中樞與內部部署閘道之間的 VPN 連線。

在此步驟中，您會建立從中樞虛擬網路到內部部署虛擬網路的連線。 您會看到範例使用共用金鑰。 您可以使用自己的值，作為共用金鑰。 但請務必確認該共用金鑰必須適用於這兩個連線。 建立連線可能需要一段時間才能完成。

1. 開啟 **FW-Hybrid-Test** 資源群組，然後選取 **GW-hub** 閘道。
2. 選取左欄中的 [連線]。
3. 選取 [新增]。
4. 輸入 **Hub-to-Onprem** 作為連線名稱。
5. 選取 [VNet 對 VNet] 作為 [連線類型]。
6. 在 [第二個虛擬網路閘道] 中，選取 [GW-Onprem]。
7. 在 [共用金鑰 (PSK)] 中，輸入 **AzureA1b2C3**。
8. 選取 [確定]。

建立內部部署對中樞虛擬網路連線。 此步驟類似前一個步驟，只不過您是建立 VNet-Onprem 到 VNet-hub 的連線。 請確認共用的金鑰相符。 稍候幾分鐘就會建立連線。

1. 開啟 **FW-Hybrid-Test** 資源群組，然後選取 **GW-Onprem** 閘道。
2. 選取左欄中的 [連線]。
3. 選取 [新增]。
4. 針對連線名稱，輸入 **Onprem-to-Hub**。
5. 選取 [VNet 對 VNet] 作為 [連線類型]。
6. 在 [第二個虛擬網路閘道] 中，選取 [GW-hub]。
7. 在 [共用金鑰 (PSK)] 中，輸入 **AzureA1b2C3**。
8. 選取 [確定]。


#### <a name="verify-the-connection"></a>驗證連線

大約五分鐘之後，這兩個連線的狀態應該是 [已連線]。

![閘道連線](media/tutorial-hybrid-portal/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>對等互連中樞與輪輻虛擬網路

現在對等互連中樞與輪輻虛擬網路。

1. 開啟 **FW-Hybrid-Test** 資源群組，然後選取 **VNet-hub** 虛擬網路。
2. 在左欄中選取 [對等互連]。
3. 選取 [新增]。
4. 在 [此虛擬網路] 底下：
 
   
   |設定名稱  |值  |
   |---------|---------|
   |對等互連連結名稱| HubtoSpoke|
   |連到遠端虛擬網路的流量|   允許 (預設)      |
   |從遠端虛擬網路轉送的流量    |   允許 (預設)      |
   |虛擬網路閘道     |  使用此虛擬網路的閘道       |
    
5. 在 [遠端虛擬網路] 底下：

   |設定名稱  |值  |
   |---------|---------|
   |對等互連連結名稱 | SpoketoHub|
   |虛擬網路部署模型| 資源管理員|
   |訂用帳戶|\<your subscription\>|
   |虛擬網路| VNet-Spoke
   |連到遠端虛擬網路的流量     |   允許 (預設)      |
   |從遠端虛擬網路轉送的流量    |   允許 (預設)      |
   |虛擬網路閘道     |  使用遠端虛擬網路的閘道       |

5. 選取 [新增]  。

   :::image type="content" source="media/tutorial-hybrid-portal/firewall-peering.png" alt-text="Vnet 對等互連":::

## <a name="create-the-routes"></a>建立路由

接下來，建立幾個路由：

- 透過防火牆 IP 位址，從中樞閘道子網路到輪輻子網路的路由
- 透過防火牆 IP 位址，從輪輻子網路開始的預設路由

1. 從 Azure 入口網站首頁，選取 [建立資源]。
2. 在搜尋文字方塊中，輸入 **路由表**，然後按 **Enter**。
3. 選取 [路由表]。
4. 選取 [建立]。
6. 選取 **FW-Hybrid-Test** 作為資源群組。
8. 針對 [區域]  ，選取您先前使用的相同位置。
1. 在 [名稱] 中，輸入 **UDR-Hub-Spoke**。
9. 選取 [檢閱 + 建立]  。
10. 選取 [建立]。
11. 建立路由表之後，請將其選取，以開啟 [路由表] 頁面。
12. 選取左欄中的 [路由]。
13. 選取 [新增]。
14. 在 [路由名稱] 中，輸入 **ToSpoke**。
15. 在 [位址首碼] 中，輸入 **10.6.0.0/16**。
16. 在 [下一個躍點類型] 中，選取 [虛擬設備]。
17. 在 [下一個躍點位址] 中，輸入您先前記下的防火牆私人 IP 位址。
18. 選取 [確定]。

現在將路由與子網建立關聯。

1. 在 [UDR-Hub-Spoke - Routes] 頁面上，選取 [子網路]。
2. 選取 [關聯]。
3. 在 [虛擬網路] 之下，選取 [VNet-hub]。
1. 在 [子網路] 之下，選取 [GatewaySubnet]。
2. 選取 [確定]。

現在，從輪輻子網路中建立預設路由。

1. 從 Azure 入口網站首頁，選取 [建立資源]。
2. 在搜尋文字方塊中，輸入 **路由表**，然後按 **Enter**。
3. 選取 [路由表]。
5. 選取 [建立]。
7. 選取 **FW-Hybrid-Test** 作為資源群組。
8. 針對 [區域]  ，選取您先前使用的相同位置。
1. 在 [名稱] 中，輸入 **UDR-DG**。
4. 針對 [傳播閘道路由]，選取 [否]。
5. 選取 [檢閱 + 建立]  。
6. 選取 [建立]。
7. 建立路由表之後，請將其選取，以開啟 [路由表] 頁面。
8. 選取左欄中的 [路由]。
9. 選取 [新增]。
10. 針對路由名稱，輸入 **ToHub**。
11. 在 [位址首碼] 中，輸入 **0.0.0.0/0**。
12. 在 [下一個躍點類型] 中，選取 [虛擬設備]。
13. 在 [下一個躍點位址] 中，輸入您先前記下的防火牆私人 IP 位址。
14. 選取 [確定]。

現在將路由與子網建立關聯。

1. 在 [UDR-DG - Routes] 頁面上，選取 [子網路]。
2. 選取 [關聯]。
3. 在 [虛擬網路] 之下，選取 [VNet-spoke]。
1. 在 [子網路] 之下，選取 [SN-Workload]。
2. 選取 [確定]。

## <a name="create-virtual-machines"></a>建立虛擬機器

現在建立輪輻工作負載和內部部署虛擬機器，並將它們放在適當的子網路中。

### <a name="create-the-workload-virtual-machine"></a>建立工作負載虛擬機器

在輪輻虛擬網路中建立虛擬機器，該虛擬機器會執行 IIS、沒有公用 IP 位址。

1. 從 Azure 入口網站首頁，選取 [建立資源]。
2. 在 [熱門] 底下，選取 [Windows Server 2016 Datacenter]。
3. 依虛擬機器輸入這些值：
    - [資源群組] - 選取 **FW-Hybrid-Test**。
    - **虛擬機器名稱**：VM-Spoke-01。
    - **區域** - 您先前使用的相同區域。
    - **使用者名稱**：\<type a user name\>。
    - **密碼**：\<type a password\>
4. 在 [公用輸入連接埠] 中選取 [允許選取的連接埠]，然後選取 [HTTP (80)] 和 [RDP (3389)]
4. 選取 [下一步：磁碟]。
5. 接受預設值，然後選取 [下一步：網路]。
6. 選取 [VNet-Spoke] 作為虛擬網路，而子網路為 **SN-Workload**。
7. 在 [公用 IP] 中，選取 [無]。 
9. 選取 [下一步：管理]。
10. 針對 [開機診斷]，選取 [停用]。
11. 選取 [檢閱+建立]，檢閱 [摘要] 頁面上的設定，然後選取 [建立]。

### <a name="install-iis"></a>安裝 IIS

1. 從 Azure 入口網站中開啟 Cloud Shell，並確定其已設定為 **PowerShell**。
2. 執行下列命令以在虛擬機器上安裝 IIS 並且視需要變更位置：

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>建立內部部署虛擬機器

這是一部虛擬機器，您可使用遠端桌面連線到公用 IP 位址。 從該處，您可透過防火牆接著連線到內部部署伺服器。

1. 從 Azure 入口網站首頁，選取 [建立資源]。
2. 在 [熱門] 底下，選取 [Windows Server 2016 Datacenter]。
3. 依虛擬機器輸入這些值：
    - [資源群組] - 選取 [使用現有的]，然後選取 [FW-Hybrid-Test]。
    - **虛擬機器名稱** - VM-Onprem。
    - **區域** - 您先前使用的相同區域。
    - **使用者名稱**：\<type a user name\>。
    - **密碼**：\<type a user password\>。
7. 在 [公用輸入連接埠] 中選取 [允許選取的連接埠]，然後選取 [RDP (3389)]
4. 選取 [下一步：磁碟]。
5. 接受預設值，然後選取 [下一步：網路]。
6. 選取 [VNet-Onprem] 作為虛擬網路，而子網路為 **SN-Corp**。
8. 選取 [下一步：管理]。
10. 針對 [開機診斷]，選取 [停用]。
10. 選取 [檢閱+建立]，檢閱 [摘要] 頁面上的設定，然後選取 [建立]。

## <a name="test-the-firewall"></a>測試防火牆

1. 首先，請記下 **VM-spoke-01** 虛擬機器的私人 IP 位址。

2. 從 Azure 入口網站，連線到 **VM-Onprem** 虛擬機器。
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. 在 **VM-Onprem** 上開啟網頁瀏覽器，並瀏覽至 http://\<VM-spoke-01 private IP\>。

   您應該會看到 **VM-spoke-01** 網頁：![VM-Spoke-01 網頁](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

4. 從 **VM-Onprem** 虛擬機器中，針對位於私人 IP 位址的 **VM-spoke-01** 開啟遠端桌面。

   您的連線應會成功，而且應該能夠登入。

因此，現在您已確認防火牆規則正在運作：

<!---- You can ping the server on the spoke VNet.--->
- 您可以瀏覽輪輻虛擬網路上的網頁伺服器。
- 您可以使用 RDP 連線到輪輻虛擬網路上的伺服器。

接下來，將防火牆網路規則集合動作變更為 [拒絕]，確認防火牆規則會如預期般運作。

1. 選取 **AzFW01** 防火牆。
2. 選取 [規則]。
3. 選取 [網路規則集合] 索引標籤，然後選取 [RCNet01] 規則集合。
4. 在 [動作] 中，選取 [拒絕]。
5. 選取 [儲存]。

在測試已變更的規則之前，請關閉任何現有的遠端桌面。 現在再次執行測試。 這次所有測試應該都會失敗。

## <a name="clean-up-resources"></a>清除資源

您可以保留防火牆資源供下一個教學課程使用，若不再需要，則可刪除 **FW-Hybrid-Test** 資源群組來刪除所有防火牆相關資源。

## <a name="next-steps"></a>後續步驟

接下來，您可以監視 Azure 防火牆記錄。

> [!div class="nextstepaction"]
> [教學課程：監視 Azure 防火牆記錄](./firewall-diagnostics.md)