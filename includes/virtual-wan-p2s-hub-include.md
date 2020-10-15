---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 134f1dc7cb6e53c181b2f518055e5cb758fccf31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812657"
---
1. 在您的虛擬 WAN 下選取 [中樞]，然後選取 [+新增中樞]。

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.jpg" alt-text="新增中樞":::

1. 在 [建立虛擬中樞] 頁面上，填入下列欄位。

   * **區域** - 選取您要在其中部署虛擬中樞的區域。
   * **名稱** - 輸入要用來稱呼虛擬中樞的名稱。
   * **中樞私人位址空間** - 採用 CIDR 標記法的中樞位址範圍。

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.jpg" alt-text="新增中樞":::

1. 在 [點對站] 索引標籤中，完成以下欄位：

   * **閘道縮放單位** - 代表使用者 VPN 閘道的彙總容量。
   * **指向網站設定** - 這是您在上一個步驟中建立的。
   * **用戶端位址集區** - 適用於遠端使用者。
   * **自訂 DNS 伺服器 IP**。

   :::image type="content" source="media/virtual-wan-p2s-hub/hub-with-p2s.png" alt-text="新增中樞":::

1. 選取 [檢閱 + 建立]。
1. 在 [驗證成功] 頁面上，選取 [建立]。