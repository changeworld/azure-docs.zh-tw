---
title: 快速入門:創建 VMware 雲簡單服務
titleSuffix: Azure VMware Solution by CloudSimple
description: 瞭解如何建立雲端簡單服務、購買節點和預留節點
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d7c137a75c0a021aa8bca3aec23da6c4d1ada300
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868021"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>快速入門 ─透過雲端簡單服務建立 Azure VMware 解決方案

要開始,請在 Azure 門戶中按雲端簡單創建 Azure VMware 解決方案。

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware 解決方案(按雲簡單 ) - 服務概述

雲端簡單服務允許您使用 Azure VMware 解決方案(按雲端簡單。  創建服務允許您預配節點、保留節點和創建私有雲。  在雲端簡單服務可用的每個 Azure 區域中添加雲端簡單服務。  該服務透過雲端簡單定義 Azure VMware 解決方案的邊緣網路。  此邊緣網路用於包括 VPN、ExpressRoute 和 Internet 連接到私有雲端服務。

要添加 CloudSimple 服務,必須創建閘道閘。 創建邊緣網路時使用閘道網,並且需要 /28 CIDR 塊。 閘道網地址空間必須是唯一的。 它不能與任何本地網路地址空間或 Azure 虛擬網路位址空間重疊。

## <a name="before-you-begin"></a>開始之前

為閘道配置 /28 CIDR 塊。  每個 CloudSimple 服務都需要閘道,並且對於創建閘道的區域是唯一的。 閘道網路網路網路網路網路網路網路網路網路網路服務,需要 /28 CIDR 塊)。 閘道網地址空間必須是唯一的。 它不得與任何與 CloudSimple 環境通信的網路重疊。  與 CloudSimple 通訊的網路包括本地網路和 Azure 虛擬網路。

檢視[網路先決條件](cloudsimple-network-checklist.md)。 

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="create-the-service"></a>建立服務

1. 選取 [所有服務]  。
2. 搜尋**雲簡單服務**。

    ![搜尋雲簡單服務](media/create-cloudsimple-service-search.png)

3. 選擇**雲簡單服務**。
4. 按下「**新增**」 以創建新服務。

    ![新增雲端簡單服務](media/create-cloudsimple-service-add.png)

5. 選擇要創建雲端簡單服務的訂閱。
6. 選擇服務的資源組。 要添加新資源組,請按一下**新**"。
7. 輸入名稱以標識服務。
8. 輸入服務閘道的 CIDR。 指定不與任何本地子網、Azure 子網或計劃的 CloudSimple 子網重疊的 /28 子網。 建立服務後,無法更改 CIDR。

    ![建立雲端簡單服務](media/create-cloudsimple-service.png)

9. 按一下 [確定]  。

該服務將創建並添加到服務清單中。

## <a name="provision-nodes"></a>佈建節點

要為雲簡單私有雲環境設置即付容量,首先在 Azure 門戶中預配節點。

1. 選取 [所有服務]  。
2. 搜尋**雲簡單節點**。

    ![搜尋雲簡單節點](media/create-cloudsimple-node-search.png)

3. 選擇**雲簡單節點**。
4. 按下「**新增**」 以建立節點。

    ![新增雲端簡單節點](media/create-cloudsimple-node-add.png)

5. 選擇要預配雲簡單節點的訂閱。
6. 選擇節點的資源組。 要添加新資源組,請按一下**新**"。
7. 輸入首碼以標識節點。
8. 選擇節點資源的位置。
9. 選擇託管節點資源的專用位置。
10. 選擇[節點型態](cloudsimple-node.md)。
11. 選擇要預配的節點數。
12. 選取 [檢閱 + 建立]  。
13. 查看設置。 要修改任何設置,請單擊「**上一個**」。
14. 選取 [建立]  。

## <a name="next-steps"></a>後續步驟

* [建立私有雲端並設定環境](quickstart-create-private-cloud.md)
* 瞭解有關[雲簡單服務](https://docs.microsoft.com/azure/vmware-cloudsimple/cloudsimple-service)的更多
