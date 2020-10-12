---
title: Azure VMware Solution by CloudSimple-建立 CloudSimple 服務
description: 瞭解如何在 Azure 入口網站中建立 CloudSimple 服務。 開始之前，請先檢查所需的設定。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2a196e717dd6e02570e4bdf830a24ce342b47ece
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88140542"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>建立 Azure VMware Solution by CloudSimple 服務

若要開始使用 Azure VMware Solution by CloudSimple，請在 Azure 入口網站中建立 Azure VMware Solution by CloudSimple service。

## <a name="before-you-begin"></a>開始之前

配置閘道子網的/28 CIDR 區塊。 每個 CloudSimple 服務都需要閘道子網，而且在其建立所在的區域中是唯一的。 閘道子網用於 edge 網路服務，而且需要/28 CIDR 區塊。 閘道子網位址空間必須是唯一的。 它不能與任何與 CloudSimple 環境通訊的網路重迭。 與 CloudSimple 通訊的網路包括內部部署網路和 Azure 虛擬網路。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

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
9. 按一下 [確定]。

服務會建立並新增至服務清單。

## <a name="next-steps"></a>後續步驟

* 瞭解如何布建 [節點](create-nodes.md)
* 瞭解如何 [建立私用雲端](create-private-cloud.md)
* 瞭解如何 [設定私人雲端環境](quickstart-create-private-cloud.md)
