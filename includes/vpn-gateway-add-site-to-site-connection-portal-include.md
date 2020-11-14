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
ms.openlocfilehash: 48f33514510618abadf329a11a9ab71a020be0bd
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "92479539"
---
1. 開啟虛擬網路閘道的頁面。 您可以透過以下方式瀏覽至閘道：移至 [您的 VNet 名稱] -> [概觀] -> [已連線的裝置] -> [您的閘道名稱]，另外也有其他多種方式可以瀏覽至閘道。
1. 在閘道的頁面上，選取 [連線]。 在 [連線] 頁面的頂部，選取 [+新增] 以開啟 [新增連線] 頁面。

   :::image type="content" source="./media/vpn-gateway-add-site-to-site-connection-portal-include/connection.png" alt-text="站對站連線":::
1. 在 [新增連線] 頁面上，設定您連線的值。

   * **名稱：** 為您的連線命名。
   * **連線類型：** 選取 [站對站 (IPSec)]。
   * **虛擬網路閘道：** 因為您正從此閘道連線，所以值是固定的。
   * **區域網路閘道：** 選取 [選擇區域網路閘道] 並選取要使用的區域網路閘道。
   * **共用金鑰：** 這裡的值必須與您用於本機內部部署 VPN 裝置的值相符。 範例中使用的是 'abc123'，但是您可以(且應該) 使用更為複雜的值。 重要的是，您在此指定的值必須與您在設定 VPN 裝置時指定的值相同。
   * 保留 [使用 Azure 私人 IP 位址] 未核取。
   * 保留 [啟用 BGP] 未核取。
   * 選取 **IKEv2** 。
   * **訂用帳戶** 、 **資源群組** 和 **位置** 的其餘值是固定的。

1. 選取 [確定]  來建立連線。 您會看到畫面上閃爍「正在建立連線」  。
1. 您可以在虛擬網路閘道的 [連線] 頁面中檢視連線。 狀態將會從 [未知] 變成 [連線中]，然後變成 [成功]。
