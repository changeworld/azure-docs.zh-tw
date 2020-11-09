---
title: 新增 NSX-T 網路區段
description: 為 Azure VMware 解決方案新增 NSX-T 網路區段的步驟
ms.topic: include
ms.date: 11/09/2020
ms.openlocfilehash: 5b97f0b280fa12eff39c9601bb73e439dba8e9fd
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335041"
---
<!-- Used in manage-dhcp.md and tutorial-nsx-t-network-segment.md -->

1. 在 [NSX-T Manager] 中，選取 [網路] > [區段]，然後選取 [新增區段]。 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="顯示如何新增區段的螢幕擷取畫面":::

1. 選取 [新增區段] 並輸入區段的名稱。

1. 選取 [第 1 層閘道 (TNTxx-T1)] 作為 **連線的閘道** ，並讓 [類型]保持為 [彈性]。

1. 選取預先設定的重疊 [傳輸區域 (TNTxx-OVERLAY-TZ)]，然後選取 [設定子網路]。 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="設定 [區段名稱]、[連線的閘道] 和 [類型]，以及 [傳輸區域]，然後選取 [設定子網路]。":::

1. 輸入該閘道的 IP 位址，然後選取 [新增]。 

   >[!IMPORTANT]
   >該 IP 位址必須位於非重疊的 RFC1918 位址區塊上，以確保可以連線到新區段上的 VM。

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="為新的區段設定閘道的 IP 位址，然後選取 [新增]。":::

1. 選取 [套用]，然後選取 [儲存]。

1. 選取 [否] 以拒絕繼續設定區段的選項。 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-continue-no.png" alt-text="選取 [否] 以拒絕進一步設定新建立的網路區段。":::

1. 確認新網路區段是否存在。 在此範例中， **ls01** 是新的網路區段。

   1. 在 [NSX-T Manager] 中，選取 [網路] > [區段]。 

      :::image type="content" source="../media/nsxt/nsxt-new-segment-overview-2.png" alt-text="確認新的網路區段存在於 NSX-T 中。":::

   1. 在 vCenter 中，選取 [Networking] > [SDDC-Datacenter]。

      :::image type="content" source="../media/nsxt/vcenter-with-ls01-2.png" alt-text="確認新的網路區段存在於 vCenter 中。":::