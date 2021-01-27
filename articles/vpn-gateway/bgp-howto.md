---
title: 為 VPN 閘道設定 BGP：入口網站
titleSuffix: Azure VPN Gateway
description: 本文將逐步引導您完成使用 PowerShell 以 Azure VPN 閘道設定 BGP 的步驟。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: db19b1ae017fa7981747b0e7b4c82e97efc61ed3
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878879"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways"></a>如何在 Azure VPN 閘道上設定 BGP

本文將逐步引導您完成在跨單位站對站 (S2S) VPN 連線上啟用 BGP 的步驟，以及使用 Azure 入口網站的 VNet 對 VNet 連線。

## <a name="about-bgp"></a><a name="about"></a>關於 BGP

BGP 是常用於網際網路的標準路由通訊協定，可交換兩個或多個網路之間的路由和可執行性資訊。 BGP 會啟用 Azure VPN 閘道和您的內部部署 VPN 裝置（稱為 BGP 對等互連或鄰近專案）來交換「路由」，其會通知這兩個閘道對這些首碼的可用性和可執行性，以通過所涉及的閘道或路由器。 BGP 也可以傳播從一個 BGP 對等互連到所有其他 BGP 對等所識別的 BGP 閘道，來啟用多個網路之間的傳輸路由。

如需 BGP 優點的詳細資訊，並瞭解使用 BGP 的技術需求和考慮，請參閱 [使用 AZURE VPN 閘道的 BGP 總覽](vpn-gateway-bgp-overview.md)。

## <a name="getting-started"></a>開始使用

本文的每個部分都可協助您形成基本的組建區塊，以在您的網路連線能力中啟用 BGP。 如果您完成三個部分，則會建立拓撲，如圖1所示。

**圖表 1**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="顯示網路架構和設定的圖表" border="false":::

您可以將多個部分結合起來，依您的需求建立更複雜的多重躍點傳輸網路。

### <a name="prerequisites"></a>先決條件

請確認您有 Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="part-1-configure-bgp-on-the-virtual-network-gateway"></a><a name ="config"></a>第1部分：在虛擬網路閘道上設定 BGP

在本節中，您會建立和設定虛擬網路、建立及設定具有 BGP 參數的虛擬網路閘道，以及取得 Azure BGP 對等 IP 位址。 [圖 2] 顯示使用本節中的步驟時所要使用的設定。

**圖表 2**

:::image type="content" source="./media/bgp-howto/bgp-gateway.png" alt-text="顯示虛擬網路閘道設定的圖表" border="false":::

### <a name="1-create-and-configure-testvnet1"></a>1. 建立和設定 TestVNet1

在此步驟中，您會建立並設定 TestVNet1。 使用「 [建立閘道」教學](./tutorial-create-gateway-portal.md) 課程中的步驟來建立及設定您的 Azure 虛擬網路和 VPN 閘道。 使用下列螢幕擷取畫面中的參考設定。

* 虛擬網路：

   :::image type="content" source="./media/bgp-howto/testvnet-1.png" alt-text="具有對應位址首碼的 TestVNet1":::

* 子網路：

   :::image type="content" source="./media/bgp-howto/testvnet-1-subnets.png" alt-text="TestVNet1 子網":::

### <a name="2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. 使用 BGP 參數建立 TestVNet1 的 VPN 閘道

在此步驟中，您會建立具有對應 BGP 參數的 VPN 閘道。

1. 在 Azure 入口網站中，流覽至 Marketplace 中的 **虛擬網路閘道** 資源，然後選取 [ **建立**]。

1. 填入參數，如下所示：

   :::image type="content" source="./media/bgp-howto/create-gateway-1.png" alt-text="建立 VNG1":::

1. 在頁面的醒目提示 [ **設定 BGP** ] 區段中，設定下列設定：

   :::image type="content" source="./media/bgp-howto/create-gateway-1-bgp.png" alt-text="設定 BGP":::

   * 選取 [**設定 bgp**  -  **已啟用**]，以顯示 [bgp 設定] 區段。

   * 填入您的 ASN (自發系統編號) 。

   * [ **AZURE APIPA BGP IP 位址** ] 欄位是選擇性欄位。 如果您的內部部署 VPN 裝置使用適用于 BGP 的 APIPA 位址，您必須從 Azure 保留的 APIPA 位址範圍（從 **169.254.21.0** 到 **169.254.22.255**）選取位址。 此範例使用169.254.21.11。

   * 如果您要建立主動-主動 VPN 閘道，BGP 區段會顯示額外的 **第二個自訂 AZURE APIPA BGP IP 位址**。 從允許的 APIPA 範圍 (**169.254.21.0** 到 **169.254.22.255**) 指定不同的位址。

   > [!IMPORTANT]
   >
   > * 根據預設，Azure 會將 GatewaySubnet 首碼範圍中的私人 IP 位址自動指派為 Azure VPN 閘道上的 Azure BGP IP 位址。 當內部部署 VPN 裝置使用 APIPA 位址 (169.254.0.1 到 169.254.255.254) 作為 BGP IP 時，需要自訂 Azure APIPA BGP 位址。 如果對應的局域網路閘道資源 (內部部署網路) 具有作為 BGP 對等 IP 的 APIPA 位址，Azure VPN 閘道將會選擇自訂的 APIPA 位址。 如果局域網路閘道使用一般 IP 位址 (非 APIPA) ，Azure VPN 閘道將會從 GatewaySubnet 範圍還原為私人 IP 位址。
   >
   > * APIPA BGP 位址不能在內部部署 VPN 裝置與所有連線的 Azure VPN 閘道之間重迭。
   >

1. 選取 [檢閱 + 建立] 以執行驗證。 驗證通過後，選取 [建立] 以部署 VPN 閘道。 要完整建立和部署閘道，最多可能需要 45 分鐘的時間。 您可以在閘道的 [概觀] 頁面上看到部署狀態。

### <a name="3-obtain-the-azure-bgp-peer-ip-addresses"></a>3. 取得 Azure BGP 對等 IP 位址

建立閘道之後，您可以在 Azure VPN 閘道上取得 BGP 對等 IP 位址。 設定內部部署 VPN 裝置以使用 Azure VPN 閘道建立 BGP 會話時，需要這些位址。

1. 流覽至虛擬網路閘道資源，然後選取 [設定 **] 頁面以** 查看 BGP 設定資訊，如下列螢幕擷取畫面所示。 在此頁面上，您可以在 azure 端上查看 Azure VPN 閘道上的所有 BGP 設定資訊： ASN、公用 IP 位址，以及 Azure 端的對應 BGP 對等 IP 位址 (預設和 APIPA) 。

   :::image type="content" source="./media/bgp-howto/vnet-1-gw-bgp.png" alt-text="BGP 閘道":::

1. **在 [設定] 頁面上**，您可以進行下列設定變更：

   * 您可以視需要更新 ASN 或 APIPA BGP IP 位址。
   * 如果您有主動-主動 VPN 閘道，此頁面將會顯示第二個 Azure VPN 閘道實例的公用 IP 位址、預設和 APIPA BGP IP 位址。

1. 如果您進行了任何變更，請選取 [ **儲存** ]，將變更認可至您的 Azure VPN 閘道。

## <a name="part-2-configure-bgp-on-cross-premises-s2s-connections"></a><a name ="crosspremises"></a>第2部分：在跨單位 S2S 連線上設定 BGP

若要建立跨單位連線，您需要建立 *局域網路閘道* 來代表您的內部部署 VPN 裝置， *並建立連線以連接* VPN 閘道與局域網路閘道，如 [建立站對站](./tutorial-site-to-site-portal.md)連線中所述。 本文包含指定 BGP 設定參數所需的其他屬性。

**圖表 3**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises.png" alt-text="顯示 IPsec 的圖表" border="false":::

### <a name="1-configure-bgp-on-the-local-network-gateway"></a>1. 在局域網路閘道上設定 BGP

在此步驟中，您會在局域網路閘道上設定 BGP。 使用下列螢幕擷取畫面作為範例。 螢幕擷取畫面顯示 [局域網路閘道] (Site5) ，其中包含 [圖 3] 中指定的參數。

:::image type="content" source="./media/bgp-howto/create-local-bgp.png" alt-text="設定局域網路閘道的 BGP":::

#### <a name="important-configuration-considerations"></a>重要的設定考慮

* ASN 和 BGP 對等 IP 位址必須符合您的內部部署 VPN 路由器設定。
* 只有當您使用 BGP 連接到此網路時，才可以將 **位址空間** 保留空白。 Azure VPN 閘道會在內部將您 BGP 對等互連 IP 位址的路由新增至對應的 IPsec 通道。 如果您 **未** 在 Azure VPN 閘道與此特定網路之間使用 BGP，則 **必須** 提供 **位址空間** 的有效位址首碼清單。
* 您可以視需要使用 (169.254.) 的 **APIPA ip 位址** 作為內部部署 BGP 對等 ip。 但是，您也必須為您的 Azure VPN 閘道指定如本文稍早所述的 APIPA IP 位址，否則就無法為此連線建立 BGP 會話。
* 您可以在建立局域網路閘道期間輸入 BGP 設定資訊，也可以從局域網路閘道資源的 [設定] **頁面新增** 或變更 bgp 設定。

**範例**

此範例使用 (169.254.100.1) 作為內部部署 BGP 對等 IP 位址的 APIPA 位址：

:::image type="content" source="./media/bgp-howto/local-apipa.png" alt-text="局域網路閘道 APIPA 和 BGP":::

### <a name="2-configure-a-s2s-connection-with-bgp-enabled"></a>2. 設定已啟用 BGP 的 S2S 連接

在此步驟中，您會建立已啟用 BGP 的新連接。 如果您已經有連線，而且您想要在其上啟用 BGP，則可以 [更新現有的連接](#update)。

#### <a name="to-create-a-connection-with-bgp-enabled"></a>若要建立已啟用 BGP 的連接

若要建立已啟用 BGP 的新連線，請在 [ **新增** 連線] 頁面上填入值，然後核取 [ **啟用 bgp** ] 選項，以在此連線上啟用 bgp。 選取 [OK] \(確定\) 以建立連線。

:::image type="content" source="./media/bgp-howto/ipsec-connection-bgp.png" alt-text="使用 BGP 的 IPsec 跨單位連接":::

#### <a name="to-update-an-existing-connection"></a><a name ="update"></a>更新現有的連接

如果您想要變更連線上的 BGP 選項，請流覽至連線資源的 [設定 **] 頁面，** 然後切換 **BGP** 選項，如下列範例中所強調。 選取 [儲存]  以儲存任何變更。

:::image type="content" source="./media/bgp-howto/update-bgp.png" alt-text="更新連接的 BGP":::

## <a name="part-3-configure-bgp-on-vnet-to-vnet-connections"></a><a name ="v2v"></a>第3部分：設定 VNet 對 VNet 連線上的 BGP

在 VNet 對 VNet 連線上啟用或停用 BGP 的步驟，與 [第2部分](#crosspremises)中的 S2S 步驟相同。 您可以在建立連線時啟用 BGP，也可以在現有的 VNet 對 VNet 連接上更新設定。

>[!NOTE] 
>沒有 BGP 的 VNet 對 VNet 連線會限制只有兩個連接 Vnet 的通訊。 啟用 BGP 以允許傳輸路由功能傳送至這兩個 Vnet 的其他 S2S 或 VNet 對 VNet 連線。
>

針對內容，參考 **圖表 4**，如果在 TestVNet2 和 TestVNet1 之間停用 BGP，TestVNet2 將不會學習內部部署網路的路由，Site5，因此無法與網站5通訊。 一旦您啟用 BGP （如圖4所示），所有三個網路都可以透過 IPsec 和 VNet 對 VNet 連線進行通訊。

**圖表 4**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="顯示完整網路的圖表" border="false":::

## <a name="next-steps"></a>後續步驟

一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。 請參閱 [建立網站的虛擬機器](../virtual-machines/windows/quick-create-portal.md) 以取得相關步驟。