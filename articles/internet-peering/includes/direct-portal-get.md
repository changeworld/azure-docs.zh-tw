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
ms.openlocfilehash: 3507aacc68de25f7368cbe3cda917077564c56eb
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356239"
---
1. 移至 [ **資源群組**]，然後選取您在建立對 **等互連** 資源時選取的資源群組。 如果您的資源群組太多，請使用 [ **篩選** ] 方塊。

    > [!div class="mx-imgBorder"]
    > ![資源群組](../media/setup-direct-get-resourcegroup.png)

1. 選取您所建立的對 **等互連** 資源。

    > [!div class="mx-imgBorder"]
    > ![您可以在左窗格中選取 [總覽] 頁面。 它會顯示 PeeringResourceGroup 的相關資訊。 在對等清單中，AshburnPeering 已反白顯示。](../media/setup-direct-get-open.png)

1. [ **總覽** ] 頁面會顯示高層級的資訊，如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等互連資源總覽窗格](../media/setup-direct-get-overview.png)

1. 選取左側的 [ **ASN 資訊** ]，以查看您在建立 PeerAsn 時提交的資訊。

    > [!div class="mx-imgBorder"]
    > ![對等互連資源 ASN 資訊](../media/setup-direct-get-asninfo.png)

1. 選取左側的 [ **連接**]。 在畫面頂端，您會看到您的 ASN 與 Microsoft 之間，在 metro 內的不同設施之間的對等互連連線摘要。 您也可以藉由選取窗格中央的 [**連接**]，從 [**總覽**] 頁面存取連線摘要，如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等互連資源連接](../media/setup-direct-get-connectionssummary.png)

    * **連接狀態** 對應至對等互連連線設定的狀態。 此欄位中顯示的狀態會遵循 [直接對等互連逐步](../walkthrough-direct-all.md)解說中所示的狀態圖表。
    * **Ipv4 會話狀態** 和 **ipv6 會話狀態** 分別對應到 ipv4 和 ipv6 BGP 會話狀態。 
    * 當您選取畫面頂端的資料列時，底部的 [ **連接** ] 區段會顯示每個連接的詳細資料。 選取箭號 **以展開 [** 設定]、[ **IPv4 位址**] 和 [ **IPv6 位址**]。
