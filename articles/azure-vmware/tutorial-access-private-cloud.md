---
title: 教學課程 - 存取您的私人雲端
description: 了解如何存取 Azure VMware 解決方案私人雲端
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: b7f21dbba5256712607241bb1258962fc22418fb
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578408"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>教學課程：存取 Azure VMware 解決方案私人雲端

Azure VMware 解決方案不允許您使用內部部署 vCenter 管理私人雲端。 您必須透過 jumpbox 來執行本機 vCenter 執行個體的額外設定和連線。 

在本教學課程中，您將在先前建立於[上一個教學課程](tutorial-configure-networking.md)的資源群組中建立跳躍箱，並登入 vCenter。 跳躍箱是一個 Windows 虛擬機器 (VM)，位於您所建立的相同虛擬網路上。  它可讓您存取 vCenter 和 NSX Manager。 

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立用來連線到 vCenter 的 Windows 虛擬機器
> * 從您的虛擬機器登入 vCenter

## <a name="create-a-new-windows-virtual-machine"></a>建立新的 Windows 虛擬機器

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>連線到私人雲端的本機 vCenter

1. 從跳躍箱中，使用雲端管理員使用者名稱透過 VMware vCenter SSO 登入 vSphere Client，並確認使用者介面已成功顯示。

1. 在 Azure 入口網站中選取您的私人雲端，然後在**概觀**檢視中選取 [識別 > 預設]。 

   此時會顯示私人雲端 vCenter 和 NSX-T Manager 的 URL 和使用者認證。

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="顯示私人雲端 vCenter 和 NSX 管理員 URL 和認證。" border="true":::

1. 瀏覽至您在上一個步驟建立的 VM，並連線至虛擬機器。 

   如果您需要連線至 VM 方面的協助，請參閱[連線至虛擬機器](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)以取得詳細資料。

1. 在 Windows VM 中開啟瀏覽器，並在兩個索引標籤中瀏覽至 vCenter 和 NSX-T 管理員 URL。 

1. 在 vCenter 索引標籤中，輸入上一個步驟中的 `cloudadmin@vmcp.local` 使用者認證。

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="顯示私人雲端 vCenter 和 NSX 管理員 URL 和認證。" border="true":::

   :::image type="content" source="media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="顯示私人雲端 vCenter 和 NSX 管理員 URL 和認證。" border="true":::

1. 在瀏覽器的第二個索引標籤中，登入 NSX-T 管理員。

   :::image type="content" source="media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="顯示私人雲端 vCenter 和 NSX 管理員 URL 和認證。" border="true":::



## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立用來連線到 vCenter 的 Windows 虛擬機器
> * 從您的虛擬機器登入 vCenter

繼續進行下一個教學課程，了解如何建立虛擬網路以設定私人雲端叢集的本機管理。

> [!div class="nextstepaction"]
> [建立虛擬網路](tutorial-configure-networking.md)


