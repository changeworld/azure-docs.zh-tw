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
ms.openlocfilehash: 6f910dbe91ed8e1cb65eefa6dfc48c72a689bf25
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "73491498"
---
1. 找出您所建立的虛擬 WAN。 在 [虛擬 WAN] 頁面的 [連線能力]  區段下，選取 [中樞]  。
2. 在 [中樞] 頁面上，選取 [+ 新增中樞]  來開啟 [建立虛擬中樞]  頁面。
3. 在 [建立虛擬中樞]  頁面的 [基本]  索引標籤中，完成以下欄位：

   ![基本概念](./media/virtual-wan-tutorial-er-hub-include/hub1.png "基本概念")

    **專案詳細資料**

   * 區域 (先前稱為位置)
   * 名稱
   * 中樞私人位址空間。 建立中樞的最小位址空間為 /24，這表示在建立期間從 /25 到 /32 的任何範圍都會產生錯誤。
4. 選取 [ExpressRoute]  索引標籤。

5. 在 [ExpressRoute]  索引標籤上，完成下欄欄位：

   ![ExpressRoute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * 選取 [是]  建立 **ExpressRoute** 閘道。
   * 從下拉式清單選取 [閘道縮放單位]  值。
6. 選取 [檢閱 + 建立]  以進行驗證。
7. 選取 [建立]  以建立中樞。 30 分鐘後，按一下 [重新整理]  以檢視 [中樞]  頁面上的中樞。 選取 [移至資源]  ，以導覽至資源。