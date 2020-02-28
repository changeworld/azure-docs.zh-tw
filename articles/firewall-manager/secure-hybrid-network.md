---
title: 教學課程：使用 Azure 防火牆管理員預覽來保護您的中樞虛擬網路
description: 在此教學課程中，您將了解如何使用 Azure 入口網站以 Azure 防火牆管理員保護您的虛擬網路。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: cdd416bdb833e4784334a6847d724a7375e2ef8d
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459948"
---
# <a name="tutorial-secure-your-hub-virtual-network-using-azure-firewall-manager-preview"></a>教學課程：使用 Azure 防火牆管理員預覽來保護您的中樞虛擬網路 

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

當您將內部部署網路連線到 Azure 虛擬網路以建立混合式網路時，控制 Azure 網路資源存取的能力是整體安全性計劃的重要部分。

您可以使用 Azure 防火牆管理員預覽來建立中樞虛擬網路，以保護以私人 IP 位址、Azure PaaS 與網際網路為目標的混合式網路流量。 您可以使用可定義允許和拒絕網路流量的規則，在混合式網路中使用 Azure 防火牆管理員來控制網路存取。

防火牆管理員也支援安全虛擬中樞架構。 如需安全虛擬中樞和中樞虛擬網路架構類型的比較，請參閱[什麼是 Azure 防火牆管理員架構選項？](vhubs-and-vnets.md)

在本教學課程中，您會建立三個虛擬網路：

- **VNet-Hub** - 防火牆位於此虛擬網路中。
- **VNet-Spoke** - 輪輻虛擬網路代表位於 Azure 的工作負載。
- **VNet-Onprem** - 內部部署虛擬網路代表內部部署網路。 在實際部署中，它可經由 VPN 或 ExpressRoute 連線來連線。 為了簡單起見，本教學課程使用 VPN 閘道連線，而位於 Azure 的虛擬網路用來代表內部部署網路。

![混合式網路](media/tutorial-hybrid-portal/hybrid-network-firewall.png)

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立防火牆原則
> * 建立虛擬網路
> * 設定及部署防火牆
> * 建立及連線 VPN 閘道
> * 對等互連中樞與輪輻虛擬網路
> * 建立路由
> * 建立虛擬機器
> * 測試防火牆


## <a name="prerequisites"></a>Prerequisites

混合式網路會使用中樞和輪輻架構模型來路由傳送 Azure VNet 與內部部署網路之間的流量。 中樞和輪輻架構具有下列需求：

- 將 VNet-Hub 對等互連至 VNet-Spoke 時設定 **AllowGatewayTransit**。 在中樞與輪輻式 (hub-and-spoke) 網路架構中，閘道傳輸會讓輪輻虛擬網路共用中樞內的 VPN 閘道，而不是將 VPN 閘道部署在每個輪輻虛擬網路中。 

   此外，通往閘道連線虛擬網路或內部部署網路的路由，將會自動傳播到使用閘道傳輸的對等虛擬網路路由表。 如需詳細資訊，請參閱[為虛擬網路對等互連設定 VPN 閘道傳輸](../vpn-gateway/vpn-gateway-peering-gateway-transit.md)。

- 當您將 VNet-Spoke 對等互連至 VNet-Hub 時，請設定 **UseRemoteGateways**。 如果已設定 **UseRemoteGateways**，也已在遠端對等互連上設定 **AllowGatewayTransit**，則輪輻虛擬網路會使用遠端虛擬網路的閘道進行傳輸。
- 若要透過中樞防火牆路由傳送輪輻子網路流量，您需要一個使用者定義的路由 (UDR)，其指向已停用 [虛擬網路閘道路由傳播]  設定的防火牆。 這個選項會防止將路由散發到輪輻子網路。 這可防止已學習的路由與您的 UDR 衝突。
- 在中樞閘道子網路上設定可指向防火牆 IP 位址的 UDR，作為輪輻網路的下一個躍點。 Azure 防火牆子網路不需要任何 UDR，因為可從 BGP 得知路由。

請參閱本教學課程中的[建立路由](#create-the-routes)一節，了解如何建立這些路由。

>[!NOTE]
>「Azure 防火牆」必須能夠直接連線到網際網路。 如果您的 AzureFirewallSubnet 學習到透過 BGP 連至您內部部署網路的預設路由，您必須將其覆寫為 0.0.0.0/0 UDR，且 **NextHopType** 值必須設為 [網際網路]  ，以保有直接網際網路連線。
>
>您可將 Azure 防火牆設定為支援強制通道。 如需詳細資訊，請參閱 [Azure 防火牆強制通道](../firewall/forced-tunneling.md)。

>[!NOTE]
>即使 UDR 指向「Azure 防火牆」作為預設閘道，系統仍會直接路由直接對等互連之 VNet 間的流量。 在此案例中若要將子網路對子網路流量傳送到防火牆，UDR 必須在這兩個子網路上同時明確包含目標子網路網路首碼。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-firewall-policy"></a>建立防火牆原則

1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。
2. 在 Azure 入口網站搜尋列中，輸入 [防火牆管理員]  ，然後按 **Enter**。
3. 在 Azure 防火牆管理員頁面上，選取 [檢視 Azure 防火牆原則]  。

   ![防火牆原則](media/tutorial-hybrid-portal/firewall-manager-policy.png)

1. 選取 [建立 Azure 防火牆原則]  。
1. 選取您的訂用帳戶，然後針對 [資源群組]，選取 [新建]  並建立名為 **FW-Hybrid-Test**的資源群組。
2. 針對 [原則名稱]，輸入 **Pol-Net01**。
3. 針對 [區域]，選取 [美國東部]  。
4. 選取 [下一步: 規則]  。
5. 選取 [新增規則集合]  。
6. 在 [名稱]  中，輸入 **RCNet01**。
7. 針對 [規則集合類型]  ，選取 [網路]  。
8. 在 [優先順序]  中，輸入 **100**。
9. 在 [動作]  中，選取 [允許]  。
10. 在 [規則]  底下的 [名稱]  中，輸入 **AllowWeb**。
11. 在 [來源位址]  中，輸入 **192.168.1.0/24**。
12. 在 [通訊協定]  中，選取 [TCP]  。
13. 在 [目的地連接埠]  中，輸入 **80**。
14. 針對 [目的地類型]  ，選取 [IP 位址]  。
15. 針對 [目的地]  ，輸入 **10.6.0.0/16**。
16. 在下一個規則資料列中，輸入下列資訊：
 
    名稱：輸入 **AllowRDP**<br>
    來源 IP 位址：輸入 **192.168.1.0/24**。<br>
    通訊協定：選取 [TCP] <br>
    目的地連接埠：輸入 **3389**<br>
    目的地類型：選取 [IP 位址] <br>
    針對 [目的地]，輸入 **10.6.0.0/16**

1. 選取 [新增]  。
2. 選取 [檢閱 + 建立]  。
3. 檢閱詳細資料，然後選取 [建立]  。

## <a name="create-the-firewall-hub-virtual-network"></a>建立防火牆中樞虛擬網路

> [!NOTE]
> AzureFirewallSubnet 子網路的大小是 /26。 如需有關子網路大小的詳細資訊，請參閱 [Azure 防火牆的常見問題集](../firewall/firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)。

1. 從 Azure 入口網站首頁，選取 [建立資源]  。
2. 在 [網路]  底下，選取 [虛擬網路]  。
4. 在 [名稱]  中，輸入 **VNet-hub**。
5. 在 [位址空間]  中，輸入 **10.5.0.0/16**。
6. 在 [訂用帳戶]  中，選取您的訂用帳戶。
7. 在 [資源群組]  中，選取 **FW-Hybrid-Test**。
8. 在 [位置]  中，選取 [美國東部]  。
9. 在 [子網路]  底下的 [名稱]  中，鍵入 **AzureFirewallSubnet**。 防火牆會在此子網路中，且子網路名稱**必須**是 AzureFirewallSubnet。
10. 在 [位址範圍]  中，輸入 **10.5.0.0/26**。 
11. 接受其他預設設定，然後選取 [建立]  。

## <a name="create-the-spoke-virtual-network"></a>建立輪輻虛擬網路

1. 從 Azure 入口網站首頁，選取 [建立資源]  。
2. 在 [網路]  底下，選取 [虛擬網路]  。
4. 在 [名稱]  中，輸入 **VNet-Spoke**。
5. 在 [位址空間]  中，輸入 **10.6.0.0/16**。
6. 在 [訂用帳戶]  中，選取您的訂用帳戶。
7. 在 [資源群組]  中，選取 **FW-Hybrid-Test**。
8. 在 [位置]  中，選取您先前使用的相同位置。
9. 在 [子網路]  底下的 [名稱]  中，輸入 **SN-Workload**。
10. 在 [位址範圍]  中，輸入 **10.6.0.0/24**。
11. 接受其他預設設定，然後選取 [建立]  。

## <a name="create-the-on-premises-virtual-network"></a>建立內部部署虛擬網路

1. 從 Azure 入口網站首頁，選取 [建立資源]  。
2. 在 [網路]  底下，選取 [虛擬網路]  。
4. 在 [名稱]  中，輸入 **VNet-OnPrem**。
5. 在 [位址空間]  中，輸入 **192.168.0.0/16**。
6. 在 [訂用帳戶]  中，選取您的訂用帳戶。
7. 在 [資源群組]  中，選取 **FW-Hybrid-Test**。
8. 在 [位置]  中，選取您先前使用的相同位置。
9. 在 [子網路]  底下的 [名稱]  中，輸入 **SN-Corp**。
10. 在 [位址範圍]  中，輸入 **192.168.1.0/24**。
11. 接受其他預設設定，然後選取 [建立]  。

部署虛擬網路之後，為閘道建立第二個子網路。

1. 在 [VNet-Onprem]  頁面上，選取 [子網路]  。
2. 選取 [+ 子網路]  。
3. 在 [名稱]  中，輸入 **GatewaySubnet**。
4. 在 [位址範圍 (CIDR 區塊)]  中輸入 **192.168.2.0/24**。
5. 選取 [確定]  。

### <a name="create-a-public-ip-address"></a>建立公用 IP 位址

這是用於內部部署閘道的公用 IP 位址。

1. 從 Azure 入口網站首頁，選取 [建立資源]  。
2. 在搜尋文字方塊中，輸入**公用 IP 位址**，然後按 **Enter**。
3. 選取 [公用 IP 位址]  ，然後選取 [建立]  。
4. 在 [名稱] 中，輸入 **VNet-Onprem-GW-pip**。
5. 在 [資源群組] 中，輸入 **FW-Hybrid-Test**。
6. 針對 [位置]  ，選取 [美國東部]  。
7. 接受其他預設值，然後選取 [建立]  。

## <a name="configure-and-deploy-the-firewall"></a>設定及部署防火牆

當安全性原則與中樞相關聯時，我們即稱之為「安全虛擬網路」  。

將 [VNet 中樞]  虛擬網路轉換為 [中樞虛擬網路]  ，並使用 Azure 防火牆加以保護。

1. 在 Azure 入口網站搜尋列中，輸入 [防火牆管理員]  ，然後按 **Enter**。
3. 在 Azure 防火牆管理員頁面的 [增加虛擬網路的安全性]  之下，選取 [檢視中樞虛擬網路]  。
4. 選取 [轉換虛擬網路]  。
5. 選取 VNet-hub  ，然後選取 下一步:  Azure 防火牆。
6. 針對 [防火牆原則]  ，選取 [Pol-Net01]  。
7. 選取 [下一步：檢閱 + 確認]  。
8. 檢閱詳細資料，然後選取 [確認]  。


   部署需要幾分鐘的時間。
7. 部署完成之後，請前往 **FW-Hybrid-Test** 資源群組，然後選取防火牆。
9. 請注意 [概觀]  頁面上的 [防火牆私人 IP]  位址。 稍後當您建立預設路由時將使用到它。

## <a name="create-and-connect-the-vpn-gateways"></a>建立及連線 VPN 閘道

中樞和內部部署虛擬網路都是透過 VPN 閘道連線。

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>建立中樞虛擬網路的 VPN 閘道

現在建立中樞虛擬網路的 VPN 閘道。 網路對網路組態需要 RouteBased VpnType。 建立 VPN 閘道通常可能需要 45 分鐘或更久，視選取的 VPN 閘道 SKU 而定。

1. 從 Azure 入口網站首頁，選取 [建立資源]  。
2. 在搜尋文字方塊中，輸入**輸入虛擬網路閘道**，然後按 **Enter**。
3. 選取 [虛擬網路閘道]  ，然後選取 [建立]  。
4. 在 [名稱]  中，輸入 **GW-hub**。
5. 針對 [區域]  ，選取 [(美國) 美國東部]  。
6. 在 [閘道類型]  中，選取 [VPN]  。
7. 在 [VPN 類型]  中，選取 [路由式]  。
8. 在 [SKU]  中，選取 [基本]  。
9. 在 [虛擬網路]  中，選取 [VNet-hub]  。
10. 在 [公用 IP 位址]  中，選取 [新建]  ，然後輸入 **VNet-hub-GW-pip** 作為名稱。
11. 接受其餘的預設值，然後選取 [檢閱 + 建立]  。
12. 檢閱設定，然後選取 [建立]  。

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>建立虛擬網路的 VPN 內部部署閘道

現在建立虛擬網路的 VPN 內部部署閘道。 網路對網路組態需要 RouteBased VpnType。 建立 VPN 閘道通常可能需要 45 分鐘或更久，視選取的 VPN 閘道 SKU 而定。

1. 從 Azure 入口網站首頁，選取 [建立資源]  。
2. 在搜尋文字方塊中，輸入**輸入虛擬網路閘道**，然後按 **Enter**。
3. 選取 [虛擬網路閘道]  ，然後選取 [建立]  。
4. 在 [名稱]  中，輸入 **GW-Onprem**。
5. 針對 [區域]  ，選取 [(美國) 美國東部]  。
6. 在 [閘道類型]  中，選取 [VPN]  。
7. 在 [VPN 類型]  中，選取 [路由式]  。
8. 在 [SKU]  中，選取 [基本]  。
9. 在 [虛擬網路]  中，選取 [VNet-Onprem]  。
10. 針對 [公用 IP 位址]  ，選取 [使用現有]  ，然後選取 **VNet-Onprem-GW-pip**作為名稱。
11. 接受其餘的預設值，然後選取 [檢閱 + 建立]  。
12. 檢閱設定，然後選取 [建立]  。

### <a name="create-the-vpn-connections"></a>建立 VPN 連線

您現在可以建立中樞與內部部署閘道之間的 VPN 連線。

在此步驟中，您會建立從中樞虛擬網路到內部部署虛擬網路的連線。 您會看到範例使用共用金鑰。 您可以使用自己的值，作為共用金鑰。 但請務必確認該共用金鑰必須適用於這兩個連線。 建立連線可能需要一段時間才能完成。

1. 開啟 **FW-Hybrid-Test** 資源群組，然後選取 **GW-hub** 閘道。
2. 選取左欄中的 [連線]  。
3. 選取 [新增]  。
4. 輸入 **Hub-to-Onprem** 作為連線名稱。
5. 選取 [VNet 對 VNet]  作為 [連線類型]  。
6. 在 [第二個虛擬網路閘道]  中，選取 [GW-Onprem]  。
7. 在 [共用金鑰 (PSK)]  中，輸入 **AzureA1b2C3**。
8. 選取 [確定]  。

建立內部部署對中樞虛擬網路連線。 此步驟類似前一個步驟，只不過您是建立 VNet-Onprem 到 VNet-hub 的連線。 請確認共用的金鑰相符。 稍候幾分鐘就會建立連線。

1. 開啟 **FW-Hybrid-Test** 資源群組，然後選取 **GW-Onprem** 閘道。
2. 選取左欄中的 [連線]  。
3. 選取 [新增]  。
4. 輸入 **Onprem-to-Hub** 作為連線名稱。
5. 選取 [VNet 對 VNet]  作為 [連線類型]  。
6. 在 [第二個虛擬網路閘道]  中，選取 [GW-hub]  。
7. 在 [共用金鑰 (PSK)]  中，輸入 **AzureA1b2C3**。
8. 選取 [確定]  。


#### <a name="verify-the-connection"></a>驗證連線

大約五分鐘之後，這兩個連線的狀態應該是 [已連線]  。

![閘道連線](media/secure-hybrid-network/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>對等互連中樞與輪輻虛擬網路

現在對等互連中樞與輪輻虛擬網路。

1. 開啟 **FW-Hybrid-Test** 資源群組，然後選取 **VNet-hub** 虛擬網路。
2. 在左欄中選取 [對等互連]  。
3. 選取 [新增]  。
4. 在 [名稱]  中，輸入 **HubtoSpoke**。
5. 在 [虛擬網路]  中，選取 [VNet-spoke] 
6. 針對從 VNetSpoke 到 VNet-hub 的對等互連名稱，輸入 **SpoketoHub**。
7. 選取 [允許閘道傳輸]  。
8. 選取 [確定]  。

### <a name="configure-additional-settings-for-the-spoketohub-peering"></a>設定 SpoketoHub 對等互連的其他設定

您必須啟用 SpoketoHub 對等互連上的 [允許轉送的流量]  。

1. 開啟 **FW-Hybrid-Test** 資源群組，然後選取 **VNet-Spoke** 虛擬網路。
2. 在左欄中選取 [對等互連]  。
3. 選取 **SpoketoHub** 對等互連。
4. 在 [允許從 VNet-hub 到 VNet-Spoke 的轉送流量]  中，選取 [啟用]  。
5. 選取 [儲存]  。

## <a name="create-the-routes"></a>建立路由

接下來，建立幾個路由：

- 透過防火牆 IP 位址，從中樞閘道子網路到輪輻子網路的路由
- 透過防火牆 IP 位址，從輪輻子網路開始的預設路由

1. 從 Azure 入口網站首頁，選取 [建立資源]  。
2. 在搜尋文字方塊中，輸入**路由表**，然後按 **Enter**。
3. 選取 [路由表]  。
4. 選取 [建立]  。
5. 在 [名稱] 中，輸入 **UDR-Hub-Spoke**。
6. 選取 **FW-Hybrid-Test**作為資源群組。
8. 針對 [位置]  ，選取 [(美國) 美國東部]  。
9. 選取 [建立]  。
10. 建立路由表之後，請將其選取，以開啟 [路由表] 頁面。
11. 選取左欄中的 [路由]  。
12. 選取 [新增]  。
13. 在 [路由名稱] 中，輸入 **ToSpoke**。
14. 在 [位址首碼] 中，輸入 **10.6.0.0/16**。
15. 在 [下一個躍點類型] 中，選取 [虛擬設備]  。
16. 在 [下一個躍點位址] 中，輸入您先前記下的防火牆私人 IP 位址。
17. 選取 [確定]  。

現在將路由與子網建立關聯。

1. 在 [UDR-Hub-Spoke - Routes]  頁面上，選取 [子網路]  。
2. 選取 [關聯]  。
4. 在 [虛擬網路]  之下，選取 [VNet-hub]  。
5. 在 [子網路]  之下，選取 [GatewaySubnet]  。
6. 選取 [確定]  。

現在，從輪輻子網路中建立預設路由。

1. 從 Azure 入口網站首頁，選取 [建立資源]  。
2. 在搜尋文字方塊中，輸入**路由表**，然後按 **Enter**。
3. 選取 [路由表]  。
5. 選取 [建立]  。
6. 在 [名稱] 中，輸入 **UDR-DG**。
7. 選取 **FW-Hybrid-Test**作為資源群組。
8. 針對 [位置]  ，選取 [(美國) 美國東部]  。
4. 在 [虛擬網路閘道路由傳播]  中，選取 [停用]  。
1. 選取 [建立]  。
2. 建立路由表之後，請將其選取，以開啟 [路由表] 頁面。
3. 選取左欄中的 [路由]  。
4. 選取 [新增]  。
5. 針對路由名稱，輸入 **ToHub**。
6. 在 [位址首碼] 中，輸入 **0.0.0.0/0**。
7. 在 [下一個躍點類型] 中，選取 [虛擬設備]  。
8. 在 [下一個躍點位址] 中，輸入您先前記下的防火牆私人 IP 位址。
9. 選取 [確定]  。

現在將路由與子網建立關聯。

1. 在 [UDR-DG - Routes]  頁面上，選取 [子網路]  。
2. 選取 [關聯]  。
4. 在 [虛擬網路]  之下，選取 [VNet-spoke]  。
5. 在 [子網路]  之下，選取 [SN-Workload]  。
6. 選取 [確定]  。

## <a name="create-virtual-machines"></a>建立虛擬機器

現在建立輪輻工作負載和內部部署虛擬機器，並將它們放在適當的子網路中。

### <a name="create-the-workload-virtual-machine"></a>建立工作負載虛擬機器

在輪輻虛擬網路中建立虛擬機器，該虛擬機器會執行 IIS、沒有公用 IP 位址。

1. 從 Azure 入口網站首頁，選取 [建立資源]  。
2. 在 [熱門]  底下，選取 [Windows Server 2016 Datacenter]  。
3. 依虛擬機器輸入這些值：
    - [資源群組]  - 選取 **FW-Hybrid-Test**。
    - **虛擬機器名稱**：VM-Spoke-01  。
    - **區域** -  *(美國) 美國東部)* 。
    - **使用者名稱**：azureuser  。
    - **密碼**：輸入您的密碼

4. 選取 [下一步：磁碟]  。
5. 接受預設值，然後選取 [下一步：  網路]。
6. 選取 [VNet-Spoke]  作為虛擬網路，而子網路為 **SN-Workload**。
7. 在 [公用 IP]  中，選取 [無]  。
8. 在 [公用輸入連接埠]  中選取 [允許選取的連接埠]  ，然後選取 [HTTP (80)]  和 [RDP (3389)] 
9. 選取 [下一步：管理]  。
10. 在 [開機診斷]  中，選取 [關閉]  。
11. 選取 [檢閱+建立]  ，檢閱 [摘要] 頁面上的設定，然後選取 [建立]  。

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

1. 從 Azure 入口網站首頁，選取 [建立資源]  。
2. 在 [熱門]  底下，選取 [Windows Server 2016 Datacenter]  。
3. 依虛擬機器輸入這些值：
    - [資源群組]  - 選取 [使用現有的]，然後選取 [FW-Hybrid-Test]  。
    - **虛擬機器名稱** - VM-Onprem  。
    - **區域** -  *(美國) 美國東部)* 。
    - **使用者名稱**：azureuser  。
    - **密碼**：輸入您的密碼。

4. 選取 [下一步：磁碟]  。
5. 接受預設值，然後選取 [下一步：網路]  。
6. 選取 [VNet-Onprem]  作為虛擬網路，並確認子網路為 [SN-Corp]  。
7. 在 [公用輸入連接埠]  中選取 [允許選取的連接埠]  ，然後選取 [RDP (3389)] 
8. 選取 [下一步：管理]  。
9. 針對 [開機診斷]  ，選取 [關閉]  。
10. 選取 [檢閱+建立]  ，檢閱 [摘要] 頁面上的設定，然後選取 [建立]  。

## <a name="test-the-firewall"></a>測試防火牆

1. 首先，記下 [VM-Spoke-01 概觀] 頁面上 VM-spoke-01 虛擬機器的私人 IP 位址。

2. 從 Azure 入口網站，連線到 **VM-Onprem** 虛擬機器。
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. 在 **VM-Onprem** 上開啟網頁瀏覽器，並瀏覽至 http://\<VM-spoke-01 private IP\>。

   您應該會看到 **VM-spoke-01**網頁：![VM-Spoke-01 網頁](media/secure-hybrid-network/vm-spoke-01-web.png)

4. 從 **VM-Onprem** 虛擬機器中，針對位於私人 IP 位址的 **VM-spoke-01** 開啟遠端桌面。

   您的連線應會成功，而且應該能夠登入。

因此，現在您已確認防火牆規則正在運作：

<!---- You can ping the server on the spoke VNet.--->
- 您可以瀏覽輪輻虛擬網路上的網頁伺服器。
- 您可以使用 RDP 連線到輪輻虛擬網路上的伺服器。

接下來，將防火牆網路規則集合動作變更為 [拒絕]  ，確認防火牆規則會如預期般運作。

1. 開啟 **FW-Hybrid-Test** 資源群組，然後選取 **Pol-Net01** 防火牆原則。
2. 在 [設定]  之下，選取 [規則]  。
3. 在 [網路規則]  之下，選取 **RCNet01** 規則集合，選取省略符號 (...)，然後選取 [編輯]  。
4. 針對 [規則集合動作]  ，選取 [拒絕]  。
5. 選取 [儲存]  。

在測試已變更的規則之前，請關閉 **VM-Onprem** 上任何現有的遠端桌面和瀏覽器。 完成規則集合更新之後，請再次執行測試。 這次所有測試應該都會失敗。

## <a name="clean-up-resources"></a>清除資源

您可以保留防火牆資源供下一個教學課程使用，若不再需要，則可刪除 **FW-Hybrid-Test** 資源群組來刪除所有防火牆相關資源。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 Azure 防火牆管理員預覽來保護您的虛擬 WAN](secure-cloud-network.md)