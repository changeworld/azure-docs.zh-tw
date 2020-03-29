---
title: 在 Azure 資料共用中配置資料集映射
description: 瞭解如何使用 Azure 資料共用為收到的共用配置資料集映射。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 1ff432c2073536448c8194ebe537c8bf8cf00663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964238"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>如何為 Azure 資料共用中接收的共用配置資料集映射

本文介紹如何使用 Azure 資料共用為接收共用配置資料集映射。 如果您接受了資料共用邀請，但選擇"稍後接受和配置"，或者資料就地共用，則需要執行此操作。 如果需要更改要與您共用的資料的目標，或者要將資料接收到 SQL Server 中，則可能需要配置資料集映射。 

## <a name="navigate-to-a-received-data-share"></a>導航到接收的資料共用

在 Azure 資料共用服務中，導航到收到的共用並選擇"**詳細資訊**"選項卡。 

![資料集映射](./media/dataset-mapping.png "資料集映射") 

選中要將目標分配給的資料集旁邊的核取方塊。 選擇 **"取消映射**"以取消映射現有地圖。 選擇 **" 地圖"以選擇要定位**的目標。 

![映射到目標](./media/dataset-map-target.png "映射到目標") 

## <a name="select-a-new-target-store"></a>選擇新的目標存儲

選擇要將資料輸入的目標資料類型。 對於基於快照的共用，任何以前映射的存儲帳戶中已存在的任何資料都不會自動移動到新的目標存儲。 對於就地共用，請選擇指定位置中的資料存儲。 位置是資料提供程式的來源資料存儲所在的 Azure 資料中心。

![目標儲存體帳戶](./media/dataset-map-target-sql.png "目標儲存體") 

## <a name="select-a-file-format-sql-sources-only"></a>選擇檔案格式（僅限 SQL 源）

如果來源資料來自基於 SQL 的源，則可以選擇接收該資料來源的格式。 

![選擇格式](./media/sql-file-formats.png "SQL 檔案格式")

## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。



