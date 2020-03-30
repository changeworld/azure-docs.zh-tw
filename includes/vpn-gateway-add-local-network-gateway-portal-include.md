---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9648f4c16e8d266bbdd504d4a7599b67a5c846b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73522437"
---
1. 在[Azure 門戶](https://portal.azure.com)功能表中，選擇 **"創建資源**"。 

   ![在 Azure 入口網站中建立資源](./media/vpn-gateway-add-local-network-gateway-portal-include/azure-portal-create-resource.png)
2. 在 **"搜索市場**"欄位中，鍵入**本地網路閘道**，然後按**Enter**進行搜索。 這將會傳回結果清單。 按一下 [區域網路閘道]****，然後按一下 [建立]**** 按鈕以開啟 [建立區域網路閘道]**** 頁面。

   ![創建本地網路閘道](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "建立區域網路閘道")

3. 在 [建立區域網路閘道]**** 頁面中，指定區域網路閘道的值。

   - **名稱：** 指定區域網路閘道物件的名稱。
   - **IP 位址：** 這是您希望 Azure 連線之 VPN 裝置的公用 IP 位址。 指定有效的公用 IP 位址。 如果您目前沒有 IP 位址，您可以使用範例中顯示的值，但您必須返回，並以您的 VPN 裝置的公用 IP 位址取代預留位置 IP 位址。 否則，Azure 將無法連線。
   - **** 是指此區域網路所代表之網路的位址範圍。 您可以加入多個位址空間範圍。 確定您在此指定的範圍，不會與您要連接到的其他網路範圍重疊。 Azure 會將您指定的位址範圍路由傳送至內部部署 VPN 裝置 IP 位址。 *如果要連接到本地網站，而不是示例中顯示的值，請在此處使用您自己的值*。
   - **配置 BGP 設置：** 僅在配置 BGP 時使用。 否則，請勿選取此項目。
   - **訂用帳戶：** 確認顯示的是正確的訂用帳戶。
   - **資源群組：** 選取要使用的資源群組。 您可以建立新的資源群組或選取已建立的資源群組。
   - **位置：** 該位置與其他設置中的 **"區域"** 相同。 選取將要建立此物件的位置。 建議您選取 VNet 所在的相同位置，但您可以不用這麼做。

4. 當您完成值的指定時，按一下頁面底部的 [建立]**** 按鈕，以建立區域網路閘道。
