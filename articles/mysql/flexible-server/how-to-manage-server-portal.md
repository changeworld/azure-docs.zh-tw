---
title: 管理伺服器-Azure 入口網站適用於 MySQL 的 Azure 資料庫彈性的伺服器
description: 瞭解如何從 Azure 入口網站管理適用於 MySQL 的 Azure 資料庫彈性的伺服器。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 7a01863b3a0c29e94550be67ca957655cff32660
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933905"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>使用 Azure 入口網站管理適用於 MySQL 的 Azure 資料庫彈性的伺服器 (預覽) 


> [!IMPORTANT]
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。

本文說明如何 (Preview) 來管理適用於 MySQL 的 Azure 資料庫彈性的伺服器。 管理工作包括計算和儲存體調整、rest 伺服器管理員密碼，以及刪除您的伺服器。

## <a name="sign-in"></a>登入
登入 [Azure 入口網站](https://portal.azure.com)。 在 Azure 入口網站中，移至您的彈性伺服器資源。

## <a name="scale-compute-and-storage"></a>調整計算和儲存體

在伺服器建立之後，您可以隨著需求的變更，在各種 [定價層](https://azure.microsoft.com/pricing/details/mysql/) 之間進行調整。 您也可以藉由增加或減少虛擬核心，擴大或縮小計算和記憶體。

1. 在 Azure 入口網站中選取您的伺服器。 選取 [ **計算 + 儲存體**]，位於 [ **設定** ] 區段中。

2. 您可以將 **計算層**、 **vCore**、 **儲存體** 變更為使用較高的計算層級擴大伺服器，或在相同階層內擴大，方法是將儲存體或虛擬核心增加到您想要的值。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="調整儲存體彈性伺服器":::

   > [!Important]
   > - 存放裝置無法縮小。
   > - 調整虛擬核心會導致伺服器重新開機。

3. 選取 **[確定]** 以儲存變更。

## <a name="reset-admin-password"></a>重設系統管理員密碼

您可以使用 Azure 入口網站來變更系統管理員角色的密碼。

1. 在 Azure 入口網站中選取您的伺服器。 在 [ **總覽** ] 視窗中，選取 [ **重設密碼**]。

2. 輸入新密碼並確認密碼。 文字方塊會提示您輸入密碼複雜性需求。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="調整儲存體彈性伺服器":::

3. 選取 [ **儲存** ] 以儲存新密碼。

## <a name="delete-a-server"></a>刪除伺服器

如果您不再需要，您可以刪除伺服器。

1. 在 Azure 入口網站中選取您的伺服器。 在 [ **總覽** ] 視窗中，選取 [ **刪除**]。

2. 在 [輸入] 方塊中輸入伺服器的名稱，以確認您要刪除伺服器。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="調整儲存體彈性伺服器":::

   > [!NOTE]
   > 刪除伺服器無法復原。

3. 選取 [刪除]  。

## <a name="next-steps"></a>後續步驟
- [瞭解如何啟動或停止伺服器](how-to-stop-start-server-portal.md)
- [瞭解如何還原伺服器](how-to-restore-server-portal.md)
- [連線問題疑難排解](how-to-troubleshoot-common-connection-issues.md)

