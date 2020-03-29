---
title: 將資料集添加到現有 Azure 資料共用
description: 瞭解如何將資料集添加到 Azure 資料共用中的現有資料共用，並與相同的收件者共用。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 00c96950565b077e65f84e2d8b4977092df5e317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490544"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>如何將資料集添加到 Azure 資料共用中的現有共用

本文介紹如何使用 Azure 資料共用將資料集添加到預先存在的資料共用。 這允許您與同一收件者共用更多資料，而無需創建新共用。

有關如何在創建共用時添加資料集的資訊，請參閱[共用資料](share-your-data.md)教程。

## <a name="navigate-to-a-sent-data-share"></a>導航到已發送的資料共用

在 Azure 資料共用中，導航到已發送的共用並選擇**資料集**選項卡。按一下 **"添加資料集"** 按鈕可添加更多資料集。

![新增資料集](./media/how-to/how-to-add-datasets/add-datasets.png)

在右側的面板中，選擇要添加的資料集類型，然後按一下"**下一步**"。 選擇要添加資料的訂閱和資源組。 使用下拉箭頭，查找並選中要添加的資料旁邊的核取方塊。

![新增資料集](./media/how-to/how-to-add-datasets/add-datasets-side.png)

按一下"**添加資料集**"後，資料集將添加到共用中。 注意：您的消費者必須觸發快照，以便他們查看新資料集。 如果配置了快照設置，則消費者將在下一個計畫快照完成後看到新資料集。 如果未配置快照設置，消費者必須手動觸發資料的完整或增量副本才能接收更新。 有關快照的詳細資訊，請參閱[快照](terminology.md)。

## <a name="next-steps"></a>後續步驟
詳細瞭解如何將[收件者添加到現有資料共用](how-to-add-recipients.md)。