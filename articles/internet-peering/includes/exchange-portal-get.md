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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81678553"
---
成功部署對 **等互連** 資源之後，您可以依照下列步驟來加以查看。

1. 移至 [ **資源群組**]，然後選取您在建立對 **等互連** 資源時選取的資源群組。 如果您的資源群組太多，請使用 [ **篩選** ] 方塊。

    > [!div class="mx-imgBorder"]
    > ![資源群組](../media/setup-direct-get-resourcegroup.png)

1. 選取您所建立的對 **等互連** 資源。

    > [!div class="mx-imgBorder"]
    > ![對等互連資源查看](../media/setup-direct-get-open.png)

1. [ **總覽** ] 頁面會顯示高層級的資訊，如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等互連資源總覽窗格](../media/setup-exchange-get-overview.png)

1. 選取左側的 [ **ASN 資訊** ]，以查看您在建立 PeerAsn 時提交的資訊。

    > [!div class="mx-imgBorder"]
    > ![對等互連資源 ASN 資訊](../media/setup-direct-get-asninfo.png)

1. 選取左側的 [ **連接**]。 在畫面頂端，您會看到您的 ASN 與 Microsoft 之間，在 metro 內的不同設施之間的對等互連連線摘要。 您也可以在中央窗格中選取 [**連接**]，以從 [**總覽**] 頁面存取連線摘要，如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等互連資源連接](../media/setup-exchange-get-connectionssummary.png)

    * **連接狀態** 對應至對等互連連線設定的狀態。 此欄位中顯示的狀態會遵循 [Exchange 對等互連逐步](../walkthrough-exchange-all.md)解說中所示的狀態圖表。
    * **Ipv4 會話狀態** 和 **ipv6 會話狀態** 分別對應到 ipv4 和 ipv6 BGP 會話狀態。  
    * 當您選取畫面頂端的資料列時，底部的 [ **連接** ] 區段會顯示每個連接的詳細資料。 選取箭號 **以展開 [** 設定]、[ **IPv4 位址**] 和 [ **IPv6 位址**]。

    > [!div class="mx-imgBorder"]
    > ![對等互連資源查看](../media/setup-exchange-get-connectionsipv4.png)
