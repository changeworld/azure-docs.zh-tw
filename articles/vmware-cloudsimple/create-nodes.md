---
title: 為 VMware 解決方案（AVS）布建節點-Azure
description: 瞭解如何將節點新增至具有 AVS 部署的 VMWare
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 94ca681153f1e3ccd9ff628b41a9d0e547be5802
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024801"
---
# <a name="provision-nodes-for-azure-vmware-solutions-avs"></a>針對 Azure VMware 解決方案（AVS）布建節點

在 Azure 入口網站中布建節點。 接著，您可以為您的 AVS 私用雲端環境設定「隨用隨付」功能。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="add-a-node-to-your-avs-private-cloud"></a>將節點新增至您的 AVS 私人雲端

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

* [建立 AVS 私用雲端](create-private-cloud.md)
