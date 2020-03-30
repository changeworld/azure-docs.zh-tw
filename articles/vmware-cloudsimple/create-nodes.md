---
title: 按雲簡單配置 VMware 解決方案 - Azure
description: 瞭解如何使用 CloudSimple 部署將節點添加到 VMWare
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eb033425d18b472c9da1a2d6a1bb6f166702905e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024801"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>按雲簡單調配 Azure VMware 解決方案

在 Azure 門戶中預配節點。 然後，您可以為雲簡單私有雲環境設置即付容量。

## <a name="sign-in-to-azure"></a>登入 Azure

登錄到 中的[https://portal.azure.com](https://portal.azure.com)Azure 門戶。

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>將節點添加到雲簡單私有雲

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

* [創建私有雲](create-private-cloud.md)
