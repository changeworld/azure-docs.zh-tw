---
title: Azure VMware 解決方案（AVS）-建立 AVS 服務
description: 說明如何在 Azure 入口網站中建立 AVS 服務
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 468ef8751438812422d7eee83d98acc9e3aedb82
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024818"
---
# <a name="create-the-azure-vmware-solutions-avs-service"></a>建立 Azure VMware 解決方案（AVS）服務

若要開始使用 Azure VMware 解決方案（AVS），請在 Azure 入口網站中建立 Azure VMware 解決方案（AVS）服務。

## <a name="before-you-begin"></a>開始之前

為閘道子網配置/28 CIDR 區塊。 每個 AVS 服務都需要閘道子網，而且對其建立所在的區域而言是唯一的。 閘道子網用於邊緣網路服務，且需要/28 CIDR 區塊。 閘道子網位址空間必須是唯一的。 它不得與任何與 AVS 環境通訊的網路重迭。 與 AVS 通訊的網路包含內部部署網路和 Azure 虛擬網路。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

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

## <a name="next-steps"></a>後續步驟

* 瞭解如何布建[節點](create-nodes.md)
* 瞭解如何[建立 AVS 私用雲端](create-private-cloud.md)
* 瞭解如何[設定 AVS 私用雲端環境](quickstart-create-private-cloud.md)
