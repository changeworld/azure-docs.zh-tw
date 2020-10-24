---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c538c510eeafacd1596fdeb1b4593919e6236cf0
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487036"
---
1. 從 [Azure 入口網站](https://portal.azure.com)的 [搜尋資源]、[服務] 和 [檔] ** (G +/) ** 輸入 **局域網路閘道**。 在搜尋結果中找出**Marketplace**下的**局域網路閘道**，然後選取它。 這會開啟 [ **建立局域網路閘道** ] 頁面。
1. 在 [建立區域網路閘道]**** 頁面中，指定區域網路閘道的值。

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-ip-empty/create-ip.png" alt-text="使用 IP 位址建立局域網路閘道":::

   * **名稱：** 指定區域網路閘道物件的名稱。
   * **端點：** 選取內部部署 VPN 裝置的端點類型 - **IP 位址**或 **FQDN (完整網域名稱)** 。
      * **IP 位址**：如果您有從 VPN 裝置網際網路服務提供者配置的靜態公用 IP 位址，請選取 [IP 位址] 選項並填入 IP 位址，如此範例所示。 這是您希望 Azure VPN 閘道連線的 VPN 裝置公用 IP 位址。 如果您目前沒有 IP 位址，您可以使用範例中顯示的值，但您必須返回，並以您的 VPN 裝置的公用 IP 位址取代預留位置 IP 位址。 否則，Azure 將無法連線。
   * **** 是指此區域網路所代表之網路的位址範圍。 您可以加入多個位址空間範圍。 確定您在此指定的範圍，不會與您要連接到的其他網路範圍重疊。 Azure 會將您指定的位址範圍路由傳送至內部部署 VPN 裝置 IP 位址。 *如果您想要連線至內部部署網站，在此處請使用您自己的值，而不是範例中顯示的值*。
   * **設定 BGP 設定**：僅在設定 BGP 時才使用。 否則，請勿選取此項目。
   * **訂用帳戶︰** 請確認已顯示正確的訂用帳戶。
   * **資源群組：** 選取您想要使用的資源群組。 您可以建立新的資源群組或選取已建立的資源群組。
   * **位置：** 在其他設定中，位置與 [區域] 相同。 選取將要建立此物件的位置。 建議您選取 VNet 所在的相同位置，但您可以不用這麼做。

1. 當您完成值的指定時，請選取頁面底部的 [建立]**** 按鈕，以建立區域網路閘道。
