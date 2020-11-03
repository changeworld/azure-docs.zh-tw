---
title: Restore-Azure 入口網站-適用於 MySQL 的 Azure 資料庫-彈性伺服器
description: 本文說明如何透過 Azure 入口網站在適用於 MySQL 的 Azure 資料庫中執行還原作業。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 062d53fcb122ebacd004d7dca5e11f5a883354cd
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241951"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview"></a>適用於 MySQL 的 Azure 資料庫彈性的伺服器 (預覽) 的時間點還原


> [!IMPORTANT]
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。

本文提供的逐步程式，可讓您使用備份，在彈性的伺服器中執行時間點復原。

## <a name="prerequisites"></a>先決條件

若要完成本操作說明指南，您需要：

-   您必須有適用於 MySQL 的 Azure 資料庫彈性的伺服器。

## <a name="restore-to-the-latest-restore-point"></a>還原至最新的還原點

請遵循下列步驟，使用最早的現有備份來還原您的彈性伺服器。

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選擇您想要從中還原備份的彈性伺服器。

2.  從左面板中按一下 **[總覽** ]。

3.  在 [總覽] 頁面中，按一下 [ **還原** ]。

    存放

4.  將會顯示 [還原] 頁面，其中包含在 **最新的還原點** 和自訂還原點之間選擇的選項。

5.  選取 **最新的還原點** 。


6.  在 [ **還原到新伺服器** ] 欄位中提供新的伺服器名稱。

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="最早的還原時間":::

8.  按一下 [確定]  。

9.  系統會顯示已起始還原作業的通知。

## <a name="restoring-to-a-custom-restore-point"></a>還原至自訂還原點

請遵循下列步驟，使用最早的現有備份來還原您的彈性伺服器。

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選擇您想要從中還原備份的彈性伺服器。

2.  在 [總覽] 頁面中，按一下 [ **還原** ]。

    存放

3.  將會顯示 [還原] 頁面，並提供選項讓您選擇最早的還原點和自訂還原點。

4.  選擇 **自訂還原點** 。

5.  選取日期和時間。

6.  在 [ **還原到新伺服器** ] 欄位中提供新的伺服器名稱。

6.  在 [ **還原到新伺服器** ] 欄位中提供新的伺服器名稱。 
   
    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="最早的還原時間":::
 
7.  按一下 [確定]  。

8.  系統會顯示已起始還原作業的通知。

## <a name="next-steps"></a>下一步

預留位置
