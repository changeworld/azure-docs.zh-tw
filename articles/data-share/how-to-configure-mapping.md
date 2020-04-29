---
title: 設定 Azure 資料共用中的資料集對應
description: 瞭解如何使用 Azure 資料共用來設定已接收共用的資料集對應。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 1ff432c2073536448c8194ebe537c8bf8cf00663
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76964238"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>如何為 Azure 資料共用中已接收的共用設定資料集對應

本文說明如何使用 Azure 資料共用來設定已接收共用的資料集對應。 如果您接受資料共用邀請，但選擇「接受並設定之後」，或如果資料是就地共用，則您會想要這麼做。 如果您需要變更與您共用之資料的目的地，或想要將資料接收到 SQL Server，您可能會想要設定資料集對應。 

## <a name="navigate-to-a-received-data-share"></a>流覽至接收的資料共用

在 Azure 資料共用服務中，流覽至您收到的共用，然後選取 [**詳細資料**] 索引標籤。 

![資料集對應](./media/dataset-mapping.png "資料集對應") 

勾選您想要指派目的地的資料集旁的方塊。 選取 **[** 取消對應] 以取消現有對應的對應。 選取 [ **+ 對應至目標**] 以選擇新的目的地存放區。 

![對應至目標](./media/dataset-map-target.png "對應至目標") 

## <a name="select-a-new-target-store"></a>選取新的目標存放區

選取您想要讓資料進入的目標資料類型。 針對以快照集為基礎的共用，任何先前對應的儲存體帳戶中現有的資料，都不會自動移至新的目標存放區。 針對 [就地共用]，請選取指定位置中的資料存放區。 位置是資料提供者的來源資料存放區所在的 Azure 資料中心。

![目標儲存體帳戶](./media/dataset-map-target-sql.png "目標儲存體") 

## <a name="select-a-file-format-sql-sources-only"></a>選取檔案格式（僅限 SQL 來源）

如果來源資料來自于以 SQL 為基礎的來源，您可以選擇收到的格式。 

![選擇格式](./media/sql-file-formats.png "SQL 檔案格式")

## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。



