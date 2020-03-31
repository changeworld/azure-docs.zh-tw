---
title: 快速入門：創建 VMware 雲簡單服務
titleSuffix: Azure VMware Solution by CloudSimple
description: 瞭解如何創建雲簡單服務、購買節點和預留節點
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ca8c5cacd2b1a1a7b4f70615831d2901510045e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024427"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>快速入門 - 通過雲簡單服務創建 Azure VMware 解決方案

要開始，請在 Azure 門戶中按雲簡單創建 Azure VMware 解決方案。

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware 解決方案（按雲簡單 ） - 服務概述

雲簡單服務允許您使用 Azure VMware 解決方案（按雲簡單。  創建服務允許您預配節點、保留節點和創建私有雲。  在雲簡單服務可用的每個 Azure 區域中添加雲簡單服務。  該服務通過雲簡單定義 Azure VMware 解決方案的邊緣網路。  此邊緣網路用於包括 VPN、ExpressRoute 和 Internet 連接到私有雲的服務。

要添加 CloudSimple 服務，必須創建閘道子網。 創建邊緣網路時使用閘道子網，並且需要 /28 CIDR 塊。 閘道子網位址空間必須是唯一的。 它不能與任何本地網路位址空間或 Azure 虛擬網路位址空間重疊。

## <a name="before-you-begin"></a>開始之前

為閘道子網分配 /28 CIDR 塊。  每個 CloudSimple 服務都需要閘道子網，並且對於創建閘道子網的區域是唯一的。 閘道子網用於 Azure VMware 解決方案（由雲簡單邊緣網路服務，需要 /28 CIDR 塊）。 閘道子網位址空間必須是唯一的。 它不得與任何與 CloudSimple 環境通信的網路重疊。  與 CloudSimple 通信的網路包括本地網路和 Azure 虛擬網路。

查看[網路先決條件](cloudsimple-network-checklist.md)。 

## <a name="sign-in-to-azure"></a>登入 Azure

登錄到 中的[https://portal.azure.com](https://portal.azure.com)Azure 門戶。

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

## <a name="provision-nodes"></a>佈建節點

要為雲簡單私有雲環境設置即付容量，首先在 Azure 門戶中預配節點。

1. 選擇**所有服務**。
2. 搜索**雲簡單節點**。

    ![搜索雲簡單節點](media/create-cloudsimple-node-search.png)

3. 選擇**雲簡單節點**。
4. 按一下"**添加**"以創建節點。

    ![添加雲簡單節點](media/create-cloudsimple-node-add.png)

5. 選擇要預配雲簡單節點的訂閱。
6. 選擇節點的資源組。 要添加新資源組，請按一下"**新建**"。
7. 輸入首碼以標識節點。
8. 選擇節點資源的位置。
9. 選擇託管節點資源的專用位置。
10. 選擇[節點類型](cloudsimple-node.md)。
11. 選擇要預配的節點數。
12. 選擇 **"審閱 " 創建**。
13. 查看設置。 要修改任何設置，請按一下"**上一個**"。
14. 選取 [建立]****。

## <a name="next-steps"></a>後續步驟

* [創建私有雲並配置環境](quickstart-create-private-cloud.md)
* 瞭解有關[雲簡單服務](https://docs.azure.cloudsimple.com/cloudsimple-service)的更多
