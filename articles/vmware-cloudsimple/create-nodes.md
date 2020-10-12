---
title: 依 CloudSimple 布建 VMware 解決方案的節點-Azure
description: 瞭解如何在 Azure 入口網站中使用 CloudSimple 部署將節點新增至 VMWare。 您可以為私用雲端環境設定隨用隨付容量。
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ccff5b4dc6ed5a571e25ab0569b9fa92674e1340
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88140729"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>布建 Azure VMware Solution by CloudSimple 的節點

在 Azure 入口網站中布建節點。 然後，您可以為 CloudSimple 私用雲端環境設定隨用隨付容量。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>將節點新增至您的 CloudSimple 私人雲端

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

* [建立私人雲端](create-private-cloud.md)
