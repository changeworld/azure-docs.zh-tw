---
title: 撤銷 Azure 資料共用中的共用訂閱
description: 瞭解如何使用 Azure 資料共用從收件者撤銷共用訂閱。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 97f672b266c2df6956282aea6dec8d5207944943
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73476381"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>如何在 Azure 資料共用中撤銷消費者的共用訂閱

本文介紹如何使用 Azure 資料共用從一個或多個消費者撤銷共用訂閱。 這可以防止消費者觸發任何更多的快照。 如果消費者尚未觸發快照，則一旦取消共用訂閱，他們將不會接收資料。 如果他們以前觸發了快照，則他們擁有的最新資料將保留在他們的帳戶中。

## <a name="navigate-to-a-sent-data-share"></a>導航到已發送的資料共用

在 Azure 資料共用中，導航到已發送的共用並選擇"**共用訂閱**"選項卡。

![撤銷共用訂閱](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

選中要刪除其共用訂閱的收件者旁邊的核取方塊，然後按一下"**撤銷**"。 消費者將不再獲取其資料的更新。

## <a name="next-steps"></a>後續步驟
詳細瞭解如何[監視資料共用](how-to-monitor.md)。