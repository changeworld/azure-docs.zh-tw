---
title: 管理伺服器-Azure 入口網站-適用於 PostgreSQL 的 Azure 資料庫-彈性的伺服器
description: 瞭解如何從 Azure 入口網站管理適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: mvc
ms.openlocfilehash: 1ac418d855696138341115412dc7e2601d4cf3a1
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961403"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>使用 Azure 入口網站管理適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

本文說明如何管理適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器。 管理工作包括計算和儲存體調整、管理員密碼重設，以及查看伺服器詳細資料。

## <a name="sign-in"></a>登入

登入 [Azure 入口網站](https://portal.azure.com)。 在 Azure 入口網站中，移至您的彈性伺服器資源。

## <a name="scale-compute-and-storage"></a>調整計算和儲存體

在伺服器建立之後，您可以隨著需求的變更，在各種 [定價層](https://azure.microsoft.com/pricing/details/postgresql/) 之間進行調整。 您也可以藉由增加或減少虛擬核心，擴大或縮小計算和記憶體。

> [!NOTE]
> 儲存體無法相應縮小為較低的值。

1. 在 Azure 入口網站中選取您的伺服器。 選取 [ **計算 + 儲存體**]，位於 [ **設定** ] 區段中。
2. 您可以將 **計算層** 、 **vCore**、 **儲存體** 變更為使用較高的計算層級擴大伺服器，或在相同的階層內擴大，方法是將儲存體或虛擬核心增加到您想要的值。

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

   :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="調整儲存體彈性伺服器":::

   > [!IMPORTANT]
   > 刪除伺服器無法復原。

  > [!div class="mx-imgBorder"]
  > ![刪除彈性的伺服器](./media/howto-manage-server-portal/delete-server.png)  

3. 選取 [刪除]。

## <a name="next-steps"></a>後續步驟

- [瞭解備份和還原概念](concepts-backup-restore.md)
- [調整和監視伺服器](concepts-monitoring.md)
