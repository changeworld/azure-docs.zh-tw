---
title: Azure VMware 解決方案（AVS）快速入門-建立服務
description: 瞭解如何建立 AVS 服務、購買節點和保留節點
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e7eb414e51ca38f524ab83bfb51f80f771524287
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024427"
---
# <a name="quickstart---create-azure-vmware-solutions-avs-service"></a>快速入門-建立 Azure VMware 解決方案（AVS）服務

若要開始使用，請在 Azure 入口網站中建立 Azure VMware 解決方案（AVS）。

## <a name="vmware-solutions-avs---service-overview"></a>VMware 解決方案（AVS）-服務總覽

AVS 服務可讓您使用 AVS 的 Azure VMware 解決方案。 建立服務可讓您布建節點、保留節點，以及建立 AVS 私人雲端。 您會在可使用 AVS 服務的每個 Azure 區域中新增 AVS 服務。 服務會定義由 AVS 組成的 Azure VMware 解決方案 edge 網路。 此邊緣網路用於包含 VPN、ExpressRoute 和網際網路連線到您的 AVS 私人雲端的服務。

若要新增 AVS 服務，您必須建立閘道子網。 建立 edge 網路時，會使用閘道子網，而且需要/28 CIDR 區塊。 閘道子網位址空間必須是唯一的。 它不能與您的任何內部部署網路位址空間或 Azure 虛擬網路位址空間重迭。

## <a name="before-you-begin"></a>開始之前

為閘道子網配置/28 CIDR 區塊。 每個 AVS 服務都需要閘道子網，而且對其建立所在的區域而言是唯一的。 閘道子網用於由 AVS edge 網路服務提供的 Azure VMware 解決方案，而且需要/28 CIDR 區塊。 閘道子網位址空間必須是唯一的。 它不得與任何與 AVS 環境通訊的網路重迭。 與 AVS 通訊的網路包含內部部署網路和 Azure 虛擬網路。

審查[網路必要條件](cloudsimple-network-checklist.md)。 

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="create-the-service"></a>建立服務

1. 選取 [所有服務]。
2. 搜尋 [ **AVS 服務**]。

    ![搜尋 AVS 服務](media/create-cloudsimple-service-search.png)

3. 選取 [ **AVS 服務**]。
4. 按一下 [**新增**] 以建立新的服務。

    ![新增 AVS 服務](media/create-cloudsimple-service-add.png)

5. 選取您要在其中建立 AVS 服務的訂用帳戶。
6. 選取服務的資源群組。 若要新增新的資源群組，**請按一下 [新建]** 。
7. 輸入名稱以識別服務。
8. 輸入服務閘道的 CIDR。 指定/28 子網，而不會與任何內部部署子網、Azure 子網或規劃的 AVS 子網重迭。 建立服務之後，您就無法變更 CIDR。

    ![建立 AVS 服務](media/create-cloudsimple-service.png)

9. 按一下 [確定]。

隨即建立服務，並將其新增至服務清單。

## <a name="provision-nodes"></a>佈建節點

若要設定適用于 AVS 私用雲端環境的隨用隨付容量，請先在 Azure 入口網站中布建節點。

1. 選取 [所有服務]。
2. 搜尋**AVS 節點**。

    ![搜尋 AVS 節點](media/create-cloudsimple-node-search.png)

3. 選取 [ **AVS 節點**]。
4. 按一下 [**新增**] 以建立節點。

    ![新增 AVS 節點](media/create-cloudsimple-node-add.png)

5. 選取您要布建 AVS 節點的訂用帳戶。
6. 選取節點的資源群組。 若要新增新的資源群組，**請按一下 [新建]** 。
7. 輸入前置詞以識別節點。
8. 選取節點資源的 [位置]。
9. 選取要裝載節點資源的專用位置。
10. 選取[節點類型](cloudsimple-node.md)。
11. 選取要布建的節點數目。
12. 選取 [檢閱 + 建立]。
13. 檢查設定。 若要修改任何設定，請按一下 [**上一步**]。
14. 選取 [建立]。

## <a name="next-steps"></a>後續步驟

* [建立 AVS 私人雲端並設定環境](quickstart-create-private-cloud.md)
* 深入瞭解[AVS 服務](https://docs.azure.cloudsimple.com/cloudsimple-service)
