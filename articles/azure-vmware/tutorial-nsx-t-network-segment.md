---
title: 教學課程 - 在 Azure VMware 解決方案中新增 NSX-T 網路區段
description: 了解如何建立 NSX-T 網路區段以用於 vCenter 中的虛擬機器 (VM)。
ms.topic: tutorial
ms.date: 11/09/2020
ms.openlocfilehash: 8ecb37a42e2986bd1c6261b8fe6c23382323b31d
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335040"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>教學課程：在 Azure VMware 解決方案中新增網路區段 

在 vCenter 中建立的虛擬機器 (VM) 會放置在 NSX-T 中建立的網路區段上，並且會在 vCenter 中顯示。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 在 NSX-T Manager 中瀏覽以新增網路區段
> * 新增網路區段
> * 觀察 vCenter 中新的網路區段

## <a name="prerequisites"></a>必要條件

可存取 vCenter 和 NSX-T Manager 介面的 Azure VMware 解決方案私人雲端。 如需詳細資訊，請參閱[設定網路功能](tutorial-configure-networking.md)教學課程。

## <a name="add-a-network-segment"></a>新增網路區段

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立用於 vCenter 中 VM 的 NSX-T 網路區段。 

您現在可以： 

- [建立和管理 Azure VMware 解決方案的 DHCP](manage-dhcp.md)
- [建立內容庫以在 Azure VMware 解決方案中部署 VM](deploy-vm-content-library.md) 
- [將內部部署環境對等互連至私人雲端](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
