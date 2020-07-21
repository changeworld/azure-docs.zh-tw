---
title: 快速入門：建立 VMware CloudSimple 服務
titleSuffix: Azure VMware Solution by CloudSimple
description: 瞭解如何建立 CloudSimple 服務、購買節點和保留節點
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 14df0f131aaef8a4c24e2d1eb242a9b440e7c7b0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86507585"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>快速入門-建立 Azure VMware Solution by CloudSimple 服務

若要開始使用，請在 Azure 入口網站中建立 Azure VMware Solution by CloudSimple。

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware Solution by CloudSimple-服務總覽

CloudSimple 服務可讓您使用 CloudSimple 的 Azure VMware 解決方案。  建立服務可讓您布建節點、保留節點，以及建立私人雲端。  您會在可使用 CloudSimple 服務的每個 Azure 區域中新增 CloudSimple 服務。  服務會依 CloudSimple 定義 Azure VMware 解決方案的邊緣網路。  此 edge 網路適用于包含 VPN、ExpressRoute 和網際網路連線的服務，可連接到您的私人雲端。

若要新增 CloudSimple 服務，您必須建立閘道子網。 建立 edge 網路時，會使用閘道子網，而且需要/28 CIDR 區塊。 閘道子網位址空間必須是唯一的。 它不能與您的任何內部部署網路位址空間或 Azure 虛擬網路位址空間重迭。

## <a name="before-you-begin"></a>開始之前

為閘道子網配置/28 CIDR 區塊。  每個 CloudSimple 服務都需要閘道子網，而且對其建立所在的區域而言是唯一的。 閘道子網可用於 Azure VMware 解決方案，方法是 CloudSimple edge 網路服務，而且需要/28 CIDR 區塊。 閘道子網位址空間必須是唯一的。 它不得與任何與 CloudSimple 環境通訊的網路重迭。  與 CloudSimple 通訊的網路包括內部部署網路和 Azure 虛擬網路。

審查[網路必要條件](cloudsimple-network-checklist.md)。 

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="create-the-service"></a>建立服務

1. 選取 [所有服務]。
2. 搜尋**CloudSimple 服務**。

    ![搜尋 CloudSimple 服務](media/create-cloudsimple-service-search.png)

3. 選取 [ **CloudSimple 服務**]。
4. 按一下 [**新增**] 以建立新的服務。

    ![新增 CloudSimple 服務](media/create-cloudsimple-service-add.png)

5. 選取您要在其中建立 CloudSimple 服務的訂用帳戶。
6. 選取服務的資源群組。 若要新增新的資源群組，**請按一下 [新建]**。
7. 輸入名稱以識別服務。
8. 輸入服務閘道的 CIDR。 指定/28 子網，而不會與任何內部部署子網、Azure 子網或規劃的 CloudSimple 子網重迭。 建立服務之後，您就無法變更 CIDR。

    ![建立 CloudSimple 服務](media/create-cloudsimple-service.png)

9. 按一下 [確定]。

隨即建立服務，並將其新增至服務清單。

## <a name="provision-nodes"></a>佈建節點

若要設定 CloudSimple 私用雲端環境的隨用隨付容量，請先在 Azure 入口網站中布建節點。

1. 選取 [所有服務]。
2. 搜尋**CloudSimple 節點**。

    ![搜尋 CloudSimple 節點](media/create-cloudsimple-node-search.png)

3. 選取 [ **CloudSimple 節點**]。
4. 按一下 [**新增**] 以建立節點。

    ![新增 CloudSimple 節點](media/create-cloudsimple-node-add.png)

5. 選取您要布建 CloudSimple 節點的訂用帳戶。
6. 選取節點的資源群組。 若要新增新的資源群組，**請按一下 [新建]**。
7. 輸入前置詞以識別節點。
8. 選取節點資源的 [位置]。
9. 選取要裝載節點資源的專用位置。
10. 選取[節點類型](cloudsimple-node.md)。
11. 選取要布建的節點數目。
12. 選取 [檢閱 + 建立]。
13. 檢查設定。 若要修改任何設定，請按一下 [**上一步**]。
14. 選取 [建立]。

## <a name="next-steps"></a>後續步驟

* [建立私用雲端及設定環境](quickstart-create-private-cloud.md)
* 深入瞭解[CloudSimple 服務](./cloudsimple-service.md)
