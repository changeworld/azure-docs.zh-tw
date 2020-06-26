---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 105ab0c71d9e7e935842550ecdc4c8d2ff2a2d8c
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977898"
---
1. 找出您所建立的虛擬 WAN。 在 [虛擬 WAN] 頁面的 [連線能力]**** 區段下，選取 [中樞]****。
2. 在 [中樞] 頁面上，選取 [+ 新增中樞]**** 來開啟 [建立虛擬中樞]**** 頁面。

    ![基本概念](./media/virtual-wan-tutorial-hub-include/basics.png "基本概念")
3. 在 [建立虛擬中樞]**** 頁面的 [基本]**** 索引標籤中，完成以下欄位：

    **專案詳細資料**

   * 區域 (先前稱為位置)
   * 名稱
   * 中樞私人位址空間。 建立中樞的最小位址空間為 /24，這表示在建立期間從 /25 到 /32 的任何範圍都會產生錯誤。 Azure 虛擬 WAN 是由 Microsoft 管理的服務，可為不同的閘道/服務 (例如 VPN 閘道、ExpressRoute 閘道、使用者 VPN/點對站閘道、防火牆、路由等) 在虛擬中樞內建立適當的子網路。 使用者不需要明確規劃虛擬中樞所含服務的子網路位址空間，因為 Microsoft 會在服務中執行此動作。
4. 完成時，選取 [下一步:**站對站]** 。

    ![站對站](./media/virtual-wan-tutorial-hub-include/site-to-site.png "網站間")

5. 在 [站對站]**** 索引標籤中，完成以下欄位：

   * 選取 [是]**** 以建立站對站 VPN。
   * 此時，虛擬中樞內無法編輯 [AS 號碼] 欄位。
   * 從下拉式清單選取 [閘道縮放單位]**** 值。 縮放單位可讓您選取要在虛擬中樞內建立的 VPN 閘道彙總輸送量，以將網站連線到該閘道。 如果您選取 1 個縮放單位 = 500 Mbps，則表示將建立兩個備援的執行個體，每個執行個體都有 500 Mbps 的最大輸送量。 例如，如果您有五個分支，每個分支執行 10 Mbps，則您需要在前端有 50 Mbps 的彙總。 在評估支援中樞分支數目所需的容量之後，應完成 Azure VPN 閘道的彙總容量規劃。
6. 選取 [檢閱 + 建立]**** 以進行驗證。
7. 選取 [建立]**** 以建立中樞。 30 分鐘後，按一下 [重新整理]**** 以檢視 [中樞]**** 頁面上的中樞。 選取 [移至資源]****，以導覽至資源。
