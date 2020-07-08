---
title: CloudSimple 的 VMware 解決方案布建節點-Azure
description: 瞭解如何使用 CloudSimple 部署將節點新增至 VMWare
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eb033425d18b472c9da1a2d6a1bb6f166702905e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77024801"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>針對 Azure VMware Solution by CloudSimple 布建節點

在 Azure 入口網站中布建節點。 接著，您可以為 CloudSimple 私人雲端環境設定隨用隨付容量。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>將節點新增至您的 CloudSimple 私人雲端

1. 選取 [所有服務]  。
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
12. 選取 [檢閱 + 建立]****。
13. 檢查設定。 若要修改任何設定，請按一下 [**上一步**]。
14. 選取 [建立]。

## <a name="next-steps"></a>後續步驟

* [建立私用雲端](create-private-cloud.md)
