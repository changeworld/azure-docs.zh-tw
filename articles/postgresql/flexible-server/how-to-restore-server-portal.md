---
title: Restore-Azure 入口網站-適用於 PostgreSQL 的 Azure 資料庫-彈性伺服器
description: 本文說明如何透過 Azure 入口網站在適用於 PostgreSQL 的 Azure 資料庫中執行還原作業。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e69bcb3d9e4dca4c45bf9a6fe8ed4d54e7f4a8cd
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934222"
---
# <a name="point-in-time-restore-of-a-flexible-server"></a>彈性伺服器的時間點還原

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

本文提供的逐步程式，可讓您使用備份，在彈性的伺服器中執行時間點復原。 您可以在保留期限內執行最早的還原點或自訂還原點。

## <a name="pre-requisites"></a>必要條件

若要完成本操作說明指南，您需要：

-   您必須有適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器。 相同的程式也適用于以區域冗余設定的彈性伺服器。

## <a name="restoring-to-the-earliest-restore-point"></a>還原至最早的還原點

請遵循下列步驟，使用最早的現有備份來還原您的彈性伺服器。

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選擇您想要從中還原備份的彈性伺服器。

2.  從左面板中按一下 **[總覽**]，然後按一下 [**還原**]。
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="還原總覽":::

3.  將會顯示 [還原] 頁面，並提供選項讓您選擇最早的還原點和自訂還原點。

4.  選取 **最早的還原點** ，並在 [ **還原到新伺服器** ] 欄位中提供新的伺服器名稱。 您可以還原至的最早時間戳記隨即顯示。 
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-earliest.png" alt-text="最早的還原時間":::

5.  按一下 [確定]  。

6.  系統會顯示已起始還原作業的通知。

## <a name="restoring-to-a-custom-restore-point"></a>還原至自訂還原點

請遵循下列步驟，使用最早的現有備份來還原您的彈性伺服器。

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選擇您想要從中還原備份的彈性伺服器。

2.  在 [總覽] 頁面中，按一下 [ **還原**]。
 :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="還原總覽":::
    
3.  將會顯示 [還原] 頁面，並提供選項讓您選擇最早的還原點和自訂還原點。

4.  選擇 **自訂還原點**。

5.  選取 [日期和時間]，然後在 [ **還原到新伺服器** ] 欄位中提供新的伺服器名稱。 
   
:::image type="content" source="./media/how-to-restore-server-portal/restore-custom.png" alt-text="自訂還原時間":::
 
6.  按一下 [確定]  。

7.  系統會顯示已起始還原作業的通知。

## <a name="next-steps"></a>下一步

-   瞭解 [商務持續性](./concepts-business-continuity.md)
-   深入瞭解 [區域冗余高可用性](./concepts-high-availability.md)
-   瞭解[備份和](./concepts-backup-restore.md)復原
