---
title: 將資料集新增至現有的 Azure Data Share
description: 瞭解如何在 Azure Data Share 中將資料集新增至現有的資料共用，並與相同的收件者共用。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 3bfa911921e9bacde2649ee8c4f0d4bc31b56f54
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92910537"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>如何在 Azure Data Share 中將資料集新增至現有的共用

本文說明如何使用 Azure Data Share 將資料集新增至預先存在的資料共用。 這可讓您與相同的收件者共用更多資料，而不需要建立新的共用。

如需如何在建立共用時新增資料集的詳細資訊，請參閱 [共用資料](share-your-data.md) 教學課程。

## <a name="navigate-to-a-sent-data-share"></a>流覽至已傳送的資料共用

在 Azure Data Share 中，流覽至您已傳送的共用，然後選取 [ **資料集** ] 索引標籤。按一下 [ **+ 加入資料集** ] 按鈕，以新增更多資料集。

![螢幕擷取畫面顯示已選取 [新增資料集]。](./media/how-to/how-to-add-datasets/add-datasets.png)

在右側面板中，選取您要新增的資料集類型，然後按 **[下一步]** 。 選取您要新增之資料的訂用帳戶和資源群組。 使用下拉箭號，尋找並選取要加入之資料旁的方塊。

![螢幕擷取畫面顯示 [新增 Blob 儲存體] 窗格，您可以在其中選取資料。](./media/how-to/how-to-add-datasets/add-datasets-side.png)

一旦您按一下 [ **新增資料集** ]，就會將資料集新增至您的共用。 注意：您的取用者必須觸發快照集，才能看到新的資料集。 如果已設定快照集設定，則在下一個排程的快照集完成後，取用者會看到新的資料集。 若未設定快照集設定，取用者必須手動觸發資料的完整或增量複本，才能接收更新。 如需快照集的詳細資訊，請參閱 [快照](terminology.md)集。

## <a name="next-steps"></a>下一步
深入瞭解如何將收件者 [新增至現有的資料共用](how-to-add-recipients.md)。