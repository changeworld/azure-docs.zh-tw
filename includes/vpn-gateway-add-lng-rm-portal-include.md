---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f708052e6c1b474d1447a352d10eb1defe311fc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025245"
---
1. 在入口網站中，按一下 [+建立資源]  。
2. 在搜尋方塊中輸入**區域網路閘道**，然後按 **Enter** 鍵進行搜尋。 這將會傳回結果清單。 按一下 [區域網路閘道]****，然後按一下 [建立]**** 按鈕以開啟 [建立區域網路閘道]**** 頁面。

   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-ip.png" alt-text="建立區域網路閘道":::

3. 在 [建立區域網路閘道]**** 頁面中，指定區域網路閘道的值。

   - **名稱：** 指定區域網路閘道物件的名稱。
   - **端點：** 選取內部部署 VPN 裝置的端點類型 - **IP 位址**或 **FQDN (完整網域名稱)** 。
      - **IP 位址**：如果您有從 VPN 裝置網際網路服務提供者配置的靜態公用 IP 位址，請選取 [IP 位址] 選項並填入 IP 位址，如此範例所示。 這是您希望 Azure VPN 閘道連線的 VPN 裝置公用 IP 位址。 如果您目前沒有 IP 位址，您可以使用範例中顯示的值，但您必須返回，並以您的 VPN 裝置的公用 IP 位址取代預留位置 IP 位址。 否則，Azure 將無法連線。
      - **FQDN**：如果您有一段特定時間之後可能變更的動態公用 IP 位址 (通常是由您的網際網路服務提供者決定)，您可以使用常數 DNS 名稱搭配動態 DNS 服務，以指向您的 VPN 裝置目前的公用 IP 位址。 您的 Azure VPN 閘道會解析 FQDN，以決定要連線的公用 IP 位址。 下列螢幕擷取畫面顯示使用 FQDN (而非 IP 位址) 的範例。
   - **** 是指此區域網路所代表之網路的位址範圍。 您可以加入多個位址空間範圍。 確定您在此指定的範圍，不會與您要連接到的其他網路範圍重疊。 Azure 會將您指定的位址範圍路由傳送至內部部署 VPN 裝置 IP 位址。 *如果您想要連線至內部部署網站，在此處請使用您自己的值，而不是範例中顯示的值*。
   - **設定 BGP 設定**：僅在設定 BGP 時才使用。 否則，請勿選取此項目。
   - **訂用帳戶︰** 請確認已顯示正確的訂用帳戶。
   - **資源群組：** 選取您想要使用的資源群組。 您可以建立新的資源群組或選取已建立的資源群組。
   - **位置：** 選取將在其中建立此物件的位置。 建議您選取 VNet 所在的相同位置，但您可以不用這麼做。

    這是相同的頁面，但是醒目提示 FQDN：
   
   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-fqdn.png" alt-text="建立區域網路閘道":::
   
   > [!NOTE]
   >
   > * Azure VPN 對每個 FQDN 僅支援一個 IPv4 位址。 如果網域名稱解析為多個 IP 位址，Azure VPN 閘道將會使用 DNS 伺服器所傳回的第一個 IP 位址。 為了消除不確定性，建議您的 FQDN 一律解析為單一 IPv4 位址。 不支援 IPv6。
   > * Azure VPN 閘道會以每 5 分鐘重新整理一次的方式維護 DNS 快取。 閘道只會嘗試解析已中斷連線通道的 FQDN。 重設閘道也會觸發 FQDN 解析。
   >

4. 當您完成指定值時，請選取 [ **建立** ] 按鈕來建立閘道。
