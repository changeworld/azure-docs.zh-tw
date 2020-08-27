---
title: 教學課程：在 Azure VMware 解決方案中建立 NSX-T 網路區段
description: 在本教學課程中，您已建立用於 vCenter 中 VM 的 NSX-T 網路區段
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: cee65211cbef25ec029c68888bc8e6059f7c7896
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750474"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>教學課程：在 Azure VMware 解決方案中建立 NSX-T 網路區段

在 NSX-T Manager 中建立的網路區段會當作 vCenter 中虛擬機器 (VM) 的網路使用。 在 vCenter 中建立的 VM 會放置在 NSX-T 中建立的網路區段上，並且會在 vCenter 中顯示。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 在 NSX-T Manager 中瀏覽以新增網路區段
> * 新增網路區段
> * 觀察 vCenter 中新的網路區段

## <a name="prerequisites"></a>必要條件

必須有可存取 vCenter 和 NSX-T Manager 管理介面的 Azure VMware 解決方案私人雲端，才能完成本教學課程。 請參閱[教學課程：在 Azure 中設定 VMWare 私人雲端的網路功能](tutorial-configure-networking.md)。

## <a name="provision-a-network-segment-in-nsx-t"></a>在 NSX-T 中佈建網路區段

1. 在私人雲端的 vCenter 中，選取 [SDDC-Datacenter > 網路]，並請注意尚無網路。

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="在私人雲端的 vCenter 中，選取 [SDDC-Datacenter > 網路]，並請注意尚無網路。":::

1. 在您私人雲端的 NSX-T Manager 中，選取 [網路]。

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="在您私人雲端的 NSX-T Manager 中，選取 [網路]。":::

1. 選取 [區段]。

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="選取 [網路概觀] 頁面中的 [區段]。":::

1. 在 [NSX-T 區段概觀] 頁面中，選取 [新增區段]。 有三個區段會建立為私人雲端佈建的一部分，而且無法用於 VM。  您將需要為此目的新增網路區段。

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="在 [NSX-T 區段概觀] 頁面中，選取 [新增區段]。":::

1. 命名區段、選擇預先設定的第 1 層閘道 (TNTxx-T1) 作為 [連線的閘道]、讓 [類型] 保持 [彈性]、選擇預先設定的重疊 [傳輸區域] (TNTxx-OVERLAY-TZ)，然後選取 [設定子網路]。 本節中的所有其他設定以及 [連接埠] 和 [區段設定檔] 可以保留為預設值，如同組態一般。

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="設定 [區段名稱]、[連線的閘道] 和 [類型]，以及 [傳輸區域]，然後選取 [設定子網路]。":::

1. 為新的區段設定閘道的 IP 位址，然後選取 [新增]。 您使用的 IP 位址必須位於非重疊的 RFC1918 位址區塊上，以確保您可以連線到新區段上的 VM。

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="為新的區段設定閘道的 IP 位址，然後選取 [新增]。":::

1. 選取 [套用] 以套用新的網路區段，然後使用 [儲存] 以儲存組態。

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="使用 [套用] 將新的網路區段套用至 NSX-T 組態。":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="使用 [儲存] 將新的網路區段儲存至 NSX-T 組態。":::

1. 現在已建立新的網路區段，而且您將會選取 [否] 來拒絕繼續設定區段的選項。

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="選取 [否] 以拒絕進一步設定新建立的網路區段。":::

1. 選取 [網路 > 區段]，並查看新區段是否位於清單中 (此案例中的 "ls01")，確認新的網路區段存在於 NSX-T 中。

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="確認新的網路區段存在於 NSX-T 中。":::

1. 選取 [網路 > SDDC-Datacenter]，並觀察新區段是否位於清單中 (此案例中的 "ls01")，確認新的網路區段存在於 vCenter 中。

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="確認新的網路區段存在於 vCenter 中。":::

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立用於 vCenter 中 VM 的 NSX-T 網路區段。 您現在可以使用[教學課程：建立內容庫以在 Azure VMware 解決方案中部署 VM](tutorial-deploy-vm-content-library.md)，在 vCenter 中建立內容庫，以及在您於本教學課程中建立的網路上佈建 VM。

<!-- LINKS - external-->

<!-- LINKS - internal -->
