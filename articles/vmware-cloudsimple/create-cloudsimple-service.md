---
title: Azure VMware 解決方案（按雲簡單 ） - 創建雲簡單服務
description: 介紹如何在 Azure 門戶中創建雲簡單服務
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8648f2c9cc0175050d4b7642f5235d47159ecfaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024818"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>按雲簡單服務創建 Azure VMware 解決方案

要開始使用 Azure VMware 解決方案（按雲簡單，請通過在 Azure 門戶中創建雲簡單服務）Azure VMware 解決方案。

## <a name="before-you-begin"></a>開始之前

為閘道子網分配 /28 CIDR 塊。 每個 CloudSimple 服務都需要閘道子網，並且對於創建閘道子網的區域是唯一的。 閘道子網用於邊緣網路服務，需要 /28 CIDR 塊。 閘道子網位址空間必須是唯一的。 它不得與任何與 CloudSimple 環境通信的網路重疊。 與 CloudSimple 通信的網路包括本地網路和 Azure 虛擬網路。

## <a name="sign-in-to-azure"></a>登入 Azure

登錄到 Azure[門戶](https://portal.azure.com)。

## <a name="create-the-service"></a>建立服務

1. 選擇**所有服務**。
2. 搜索**雲簡單服務**。
    ![搜索雲簡單服務](media/create-cloudsimple-service-search.png)
3. 選擇**雲簡單服務**。
4. 按一下"**添加**"以創建新服務。
    ![添加雲簡單服務](media/create-cloudsimple-service-add.png)
5. 選擇要創建雲簡單服務的訂閱。
6. 選擇服務的資源組。 要添加新資源組，請按一下"**新建**"。
7. 輸入名稱以標識服務。
8. 輸入服務閘道的 CIDR。 指定不與任何本地子網、Azure 子網或計畫的 CloudSimple 子網重疊的 /28 子網。 創建服務後，無法更改 CIDR。

    ![創建雲簡單服務](media/create-cloudsimple-service.png)
9. 按一下 [確定]****。

該服務將創建並添加到服務清單中。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[預配節點](create-nodes.md)
* 瞭解如何[創建私有雲](create-private-cloud.md)
* 瞭解如何[配置私有雲環境](quickstart-create-private-cloud.md)
