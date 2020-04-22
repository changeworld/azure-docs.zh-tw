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
ms.openlocfilehash: 000971878e24c46892aaef1fa0c65237a4219883
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678553"
---
成功部署**對等資源**后,可以通過執行以下步驟查看它。

1. 轉到**資源群組**,然後選擇創建**對等資源**時選擇的資源組。 如果資源組太多,請使用 **「篩選器」** 框。

    > [!div class="mx-imgBorder"]
    > ![資源群組](../media/setup-direct-get-resourcegroup.png)

1. 選擇您建立的**資源**。

    > [!div class="mx-imgBorder"]
    > ![對等資源檢視](../media/setup-direct-get-open.png)

1. **概述頁**顯示高級資訊,如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等資源概述窗格](../media/setup-exchange-get-overview.png)

1. 在左側,選擇**ASN 資訊**以查看創建 PeerAsn 時提交的資訊。

    > [!div class="mx-imgBorder"]
    > ![對等資源 ASN 資訊](../media/setup-direct-get-asninfo.png)

1. 在左邊選擇 **。** 在螢幕頂部,您會看到 ASN 和 Microsoft 之間跨地鐵內不同設施的對等互連連接的摘要。 您還可以通過選擇中心窗格中的 **「連接**」來從 **「概述」** 頁存取連接摘要,如圖所示。

    > [!div class="mx-imgBorder"]
    > ![端連線](../media/setup-exchange-get-connectionssummary.png)

    * **連接狀態**對應於對等互連連接設置的狀態。 此欄位中顯示的狀態遵循[Exchange 對等演練](../walkthrough-exchange-all.md)中顯示的狀態圖。
    * **IPv4 作業階段狀態**和**IPv6會話狀態**分別對應於IPv4和IPv6 BGP工作階段狀態。  
    * 當您在螢幕頂部選擇一行時,底部的 **「連接**」部分將顯示每個連接的詳細資訊。 選擇要展開**配置****、IPv4 位址**和**IPv6 位址**的箭頭。

    > [!div class="mx-imgBorder"]
    > ![對等資源檢視](../media/setup-exchange-get-connectionsipv4.png)
