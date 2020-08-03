---
title: 撤銷 Azure 資料共用中的共用訂用帳戶
description: 瞭解如何使用 Azure 資料共用來撤銷收件者的共用訂用帳戶。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 102a0099c95073793ba6a1a85f518c7539327bf3
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2020
ms.locfileid: "87511848"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>如何在 Azure 資料共用中撤銷取用者的共用訂用帳戶

本文說明如何使用 Azure 資料共用，從您的一或多個取用者撤銷共用訂用帳戶。 這可防止取用者觸發其他快照集。 如果取用者尚未觸發快照集，則一旦撤銷共用訂閱之後，就不會收到資料。 如果他們先前已觸發快照集，則其所擁有的最新資料將會保留在其帳戶中。

## <a name="navigate-to-a-sent-data-share"></a>流覽至已傳送的資料共用

在 Azure 資料共用中，流覽至您已傳送的共用，然後選取 [**共用訂閱**] 索引標籤。

![撤銷共用訂用帳戶](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

勾選您要刪除其共用訂閱的收件者旁的方塊，然後按一下 **[撤銷]**。 取用者將不再取得其資料的更新。

## <a name="next-steps"></a>後續步驟
深入瞭解如何[監視您的資料共用](how-to-monitor.md)。