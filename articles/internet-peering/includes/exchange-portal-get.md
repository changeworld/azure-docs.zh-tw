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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81678553"
---
對**等互連**資源成功部署之後，您可以遵循下列步驟來加以查看。

1. 移至 [**資源群組**]，然後選取您在建立對**等互連**資源時選取的資源群組。 如果您有太多的資源群組，請使用 [**篩選**] 方塊。

    > [!div class="mx-imgBorder"]
    > ![資源群組](../media/setup-direct-get-resourcegroup.png)

1. 選取您建立的對**等互連**資源。

    > [!div class="mx-imgBorder"]
    > ![對等互連資源檢視](../media/setup-direct-get-open.png)

1. [**總覽**] 頁面會顯示高階資訊，如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等互連資源總覽窗格](../media/setup-exchange-get-overview.png)

1. 在左側選取 [ **ASN 資訊**]，以查看您在建立 PeerAsn 時所提交的資訊。

    > [!div class="mx-imgBorder"]
    > ![對等互連資源 ASN 資訊](../media/setup-direct-get-asninfo.png)

1. 在左側選取 [**連接**]。 在畫面頂端，您會看到您的 ASN 與 Microsoft 之間的對等互連連線摘要，橫跨 metro 內的不同設施。 您也可以在中間窗格中選取 [**連接**]，從 [**總覽**] 頁面存取 [連線摘要]，如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等互連資源連線](../media/setup-exchange-get-connectionssummary.png)

    * 連線**狀態**對應至對等互連連線設定的狀態。 此欄位中顯示的狀態會遵循[Exchange 對等互連逐步](../walkthrough-exchange-all.md)解說中所示的狀態圖表。
    * **Ipv4 會話狀態**和**ipv6 會話狀態**會分別對應至 ipv4 和 ipv6 BGP 會話狀態。  
    * 當您選取畫面頂端的資料列時，底部的 [**連接**] 區段會顯示每個連接的詳細資料。 選取箭號**以展開 [** 設定]、[ **IPv4 位址**] 和 [ **IPv6 位址**]。

    > [!div class="mx-imgBorder"]
    > ![對等互連資源檢視](../media/setup-exchange-get-connectionsipv4.png)
