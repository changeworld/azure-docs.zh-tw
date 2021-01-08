---
title: 將 Azure VMware 解決方案連線到您的內部部署環境
description: 了解如何將 Azure VMware 解決方案連線到您的內部部署環境。
ms.topic: tutorial
ms.date: 12/28/2020
ms.openlocfilehash: 753835b0206d8bbabe42b057fa40a2d6c4c8c414
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809678"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>將 Azure VMware 解決方案連線到您的內部部署環境

在本文中，您將繼續使用[在規劃期間所收集的資訊](production-ready-deployment-steps.md)，將 Azure VMware 解決方案連線到您的內部部署環境。

在開始之前，將 Azure VMware 解決方案連線到您的內部部署環境有兩個必要條件：

- 從您的內部部署環境到 Azure 的 ExpressRoute 電路。
- /29 的非重疊網路位址區塊，用於進行您在[規劃階段](production-ready-deployment-steps.md)中所定義的 ExpressRoute Global Reach 對等互連。

>[!NOTE]
> 您可以透過 VPN 連線，但這不在本快速入門文件的範圍內。

## <a name="establish-an-expressroute-global-reach-connection"></a>建立 ExpressRoute Global Reach 連線

若要使用 ExpressRoute Global Reach，為您的 Azure VMware 解決方案私人雲端建立內部部署連線能力，請遵循[將內部部署環境對等互連至私人雲端](tutorial-expressroute-global-reach-private-cloud.md)教學課程。

## <a name="verify-on-premises-network-connectivity"></a>確認內部部署網路連線能力

您現在應該會在 **內部部署 Edge 路由器** 中看到，而 ExpressRoute 會連線到 NSX-T 網路區段和 Azure VMware 解決方案管理區段。

>[!IMPORTANT]
>每個人員都有不同的環境，而有些則需要允許這些路由回到內部部署網路。  

某些環境會有防火牆來保護 ExpressRoute 電路。  如果沒有防火牆且沒有進行路由剪除，可以從內部部署環境偵測您的 Azure VMware 解決方案 vCenter Server 或 NSX-T 區段上的 [VM](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment)。 此外，您可以從位於 NSX-T 區段的 VM 觸達內部部署環境中的資源。

## <a name="next-steps"></a>後續步驟

繼續進行下一節，以部署及設定 VMware HCX

> [!div class="nextstepaction"]
> [部署及設定 VMWare HCX](tutorial-deploy-vmware-hcx.md)