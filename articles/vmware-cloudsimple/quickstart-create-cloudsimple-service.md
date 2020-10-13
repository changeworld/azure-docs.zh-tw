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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86507585"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>快速入門-建立 Azure VMware Solution by CloudSimple 服務

若要開始使用，請在 Azure 入口網站中建立 Azure VMware Solution by CloudSimple。

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware Solution by CloudSimple-服務總覽

CloudSimple 服務可讓您使用 Azure VMware Solution by CloudSimple。  建立服務可讓您布建節點、保留節點，以及建立私人雲端。  您可以在可使用 CloudSimple 服務的每個 Azure 區域中新增 CloudSimple 服務。  服務會定義 Azure VMware Solution by CloudSimple 的邊緣網路。  此邊緣網路是用於包含 VPN、ExpressRoute 和私人雲端之網際網路連線的服務。

若要新增 CloudSimple 服務，您必須建立閘道子網。 閘道子網會在建立 edge 網路時使用，而且需要/28 CIDR 區塊。 閘道子網位址空間必須是唯一的。 它不能與任何內部部署網路位址空間或 Azure 虛擬網路位址空間重迭。

## <a name="before-you-begin"></a>開始之前

配置閘道子網的/28 CIDR 區塊。  每個 CloudSimple 服務都需要閘道子網，而且在其建立所在的區域中是唯一的。 閘道子網是用於 Azure VMware Solution by CloudSimple edge 網路服務，而且需要/28 CIDR 區塊。 閘道子網位址空間必須是唯一的。 它不能與任何與 CloudSimple 環境通訊的網路重迭。  與 CloudSimple 通訊的網路包括內部部署網路和 Azure 虛擬網路。

檢查 [網路必要條件](cloudsimple-network-checklist.md)。 

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="create-the-service"></a>建立服務

1. 選取 [所有服務]。
2. 搜尋 **CloudSimple 服務**。

    ![搜尋 CloudSimple 服務](media/create-cloudsimple-service-search.png)

3. 選取 [ **CloudSimple 服務**]。
4. 按一下 **[新增]** 以建立新的服務。

    ![新增 CloudSimple 服務](media/create-cloudsimple-service-add.png)

5. 選取您要在其中建立 CloudSimple 服務的訂用帳戶。
6. 選取服務的資源群組。 若要加入新的資源群組，請按一下 [ **建立新**的]。
7. 輸入名稱以識別服務。
8. 輸入服務閘道的 CIDR。 指定/28 子網，而此子網不會與任何內部部署子網、Azure 子網或計畫的 CloudSimple 子網重迭。 建立服務之後，您就無法變更 CIDR。

    ![建立 CloudSimple 服務](media/create-cloudsimple-service.png)

9. 按一下 [確定]  。

服務會建立並新增至服務清單。

## <a name="provision-nodes"></a>佈建節點

若要設定 CloudSimple 私人雲端環境的隨用隨付容量，請先在 Azure 入口網站中布建節點。

1. 選取 [所有服務]。
2. 搜尋 **CloudSimple 節點**。

    ![搜尋 CloudSimple 節點](media/create-cloudsimple-node-search.png)

3. 選取 [ **CloudSimple 節點**]。
4. 按一下 [ **新增** ] 以建立節點。

    ![新增 CloudSimple 節點](media/create-cloudsimple-node-add.png)

5. 選取您要布建 CloudSimple 節點的訂用帳戶。
6. 選取節點的資源群組。 若要加入新的資源群組，請按一下 [ **建立新**的]。
7. 輸入用來識別節點的前置詞。
8. 選取節點資源的位置。
9. 選取裝載節點資源的專用位置。
10. 選取 [節點類型](cloudsimple-node.md)。
11. 選取要布建的節點數目。
12. 選取 [檢閱 + 建立]  。
13. 檢查設定。 若要修改任何設定，請按一下 [ **上一步**]。
14. 選取 [建立]  。

## <a name="next-steps"></a>後續步驟

* [建立私用雲端及設定環境](quickstart-create-private-cloud.md)
* 深入瞭解 [CloudSimple service](./cloudsimple-service.md)
