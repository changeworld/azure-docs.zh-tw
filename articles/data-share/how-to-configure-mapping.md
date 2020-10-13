---
title: 在 Azure Data Share 中設定資料集對應
description: 瞭解如何使用 Azure Data Share 為已接收的共用設定資料集對應。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: eed3e8275400a3e677df53b9d62cf0e0bc70271c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88257840"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>如何在 Azure Data Share 中為已接收的共用設定資料集對應

本文說明如何使用 Azure Data Share 為已接收的共用設定資料集對應。 您可能會想要設定資料集對應，以指定要接收資料的目標資料存放區，或如果您需要變更目標資料存放區。

## <a name="navigate-to-a-received-data-share"></a>流覽至已接收的資料共用

在 Azure Data Share 服務中，流覽至您收到的共用，然後選取 [ **資料集** ] 索引標籤。 

![資料集對應](./media/dataset-mapping.png "資料集對應") 

核取您想要指派目的地的資料集旁邊的方塊。 選取 [ **+ 對應至目標** ] 以選擇新的目的地存放區。 如果資料集已對應，而您想要變更目標資料存放區 **，請先** 選取 [取消對應]。

![對應到目標](./media/dataset-map-target.png "對應到目標") 

## <a name="select-a-target-store"></a>選取目標存放區

選取您想要存放資料的目標資料存放區類型。 針對以快照集為基礎的共用，任何先前對應之儲存體帳戶中已存在的資料，都不會自動移至新的目標存放區。 針對就地共用，請選取指定位置中的資料存放區。 位置是資料提供者來源資料存放區所在的 Azure 資料中心。

![目標儲存體帳戶](./media/dataset-map-target-sql.png "目標儲存體") 

## <a name="select-a-file-format-sql-sources-only"></a>只 (SQL 來源選取檔案格式) 

如果來源資料來自以 SQL 為基礎的來源，而您想要以檔案的形式接收它，您可以選擇要在其中接收的格式。 

![選擇格式](./media/sql-file-formats.png "SQL 檔案格式")

## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。



