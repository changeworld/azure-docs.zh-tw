---
title: 包含檔案
titleSuffix: Azure
description: 包含檔案
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: b967c844ab145074490e931122cbe092d67de0c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774545"
---
成功部署**對等資源**後，您可以按照以下步驟查看它。

1. 轉到**資源組**，然後按一下創建**對等資源**時選擇的資源組。 如果資源組太多，則可以使用 *"篩選器"* 欄位。

    > [!div class="mx-imgBorder"]
    > ![對等資源組](../media/setup-direct-get-resourcegroup.png)

1. 按一下您創建的**對等資源**。

    > [!div class="mx-imgBorder"]
    > ![對等資源檢視](../media/setup-direct-get-open.png)

1. **"概述"** 頁顯示高級資訊。 請遵守下面突出顯示的資訊。

    > [!div class="mx-imgBorder"]
    > ![對等資源檢視](../media/setup-exchange-get-overview.png)

1. 在左側，按一下**ASN 資訊**以查看創建 PeerAsn 時提交的資訊

    > [!div class="mx-imgBorder"]
    > ![對等資源檢視](../media/setup-direct-get-asninfo.png)

1. 在左側，按一下**連接**。 在上面查看 ASN 和 Microsoft 之間跨地鐵內不同設施的對等互連連接的摘要。 您還可以通過按一下中心窗格中的**連接**（如上所示）從 **"概述"** 頁面到達連接摘要。

    > [!div class="mx-imgBorder"]
    > ![對等資源檢視](../media/setup-exchange-get-connectionssummary.png)

    * **連接狀態**對應于建立的對等互連連接的狀態。 此欄位中顯示的狀態遵循[Exchange 對等式演練](../walkthrough-exchange-all.md)中顯示的狀態圖
    * **IPv4 會話狀態**和**IPv6 會話狀態**分別對應于 IPv4 和 IPv6 BGP 會話狀態。  
    * 選擇頂部的行時，底部的 ***"連接***"部分將顯示每個連接的詳細資訊。 您可以按一下箭頭標記來展開子節***配置******、IPv4 位址***和***IPv6 位址***

    > [!div class="mx-imgBorder"]
    > ![對等資源檢視](../media/setup-exchange-get-connectionsipv4.png)
