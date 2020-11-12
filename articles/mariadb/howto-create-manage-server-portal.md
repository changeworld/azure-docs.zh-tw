---
title: 管理伺服器-Azure 入口網站-適用於 MariaDB 的 Azure 資料庫
description: 瞭解如何從 Azure 入口網站管理適用於 MariaDB 的 Azure 資料庫伺服器。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: ade70c884a3ef01db45273e4789f34b629473661
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94537980"
---
# <a name="manage-an-azure-database-for-mariadb-server-using-the-azure-portal"></a>使用 Azure 入口網站管理適用於 MariaDB 的 Azure 資料庫伺服器
本文說明如何管理您的適用於 MariaDB 的 Azure 資料庫伺服器。 管理工作包括計算和儲存體調整、管理員密碼重設，以及查看伺服器詳細資料。

## <a name="sign-in"></a>登入
登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-server"></a>建立伺服器
流覽 [快速入門](quickstart-create-mariadb-server-database-using-azure-portal.md) ，瞭解如何建立適用於 MariaDB 的 Azure 資料庫伺服器並開始使用。

## <a name="scale-compute-and-storage"></a>調整計算和儲存體

在伺服器建立之後，您可以隨著需求的變更，在一般用途和記憶體優化層之間進行調整。 您也可以藉由增加或減少虛擬核心來調整計算和記憶體。 您可以相應增加儲存空間 (不過，您無法) 調整儲存體。

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>在一般用途和記憶體優化層之間進行調整

您可以從一般用途擴充至記憶體優化，反之亦然。 不支援在建立伺服器之後，從基本層變更為和。 

1. 在 Azure 入口網站中選取您的伺服器。 選取位於 [ **設定** ] 區段中的 [ **定價層** ]。

2. 選取 **[一般用途** ] 或 [ **記憶體優化** ] （視您要調整的內容而定）。 

    ![螢幕擷取畫面顯示已選取定價層的 Azure 入口網站，以及已選取的 [記憶體優化] 值。](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > 變更層會導致伺服器重新開機。

4. 選取 **[確定]** 以儲存變更。


### <a name="scale-vcores-up-or-down"></a>向上或向下調整虛擬核心

1. 在 Azure 入口網站中選取您的伺服器。 選取位於 [ **設定** ] 區段中的 [ **定價層** ]。

2. 將滑桿移至所需的值來變更 [vCore] 設定。

    ![scale-compute](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > 調整虛擬核心會導致伺服器重新開機。

3. 選取 **[確定]** 以儲存變更。


### <a name="scale-storage-up"></a>擴大儲存體

1. 在 Azure 入口網站中選取您的伺服器。 選取位於 [ **設定** ] 區段中的 [ **定價層** ]。

2. 將滑杆移至您想要的值，以變更 **儲存體** 設定。

    ![scale-storage](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > 存放裝置無法縮小。

3. 選取 **[確定]** 以儲存變更。


## <a name="update-admin-password"></a>更新系統管理員密碼
您可以使用 Azure 入口網站來變更系統管理員角色的密碼。

1. 在 Azure 入口網站中選取您的伺服器。 在 [ **總覽** ] 視窗中，選取 [ **重設密碼** ]。

   ![概觀](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. 輸入新密碼並確認密碼。 文字方塊會提示您輸入密碼複雜性需求。

   ![螢幕擷取畫面顯示 [使用密碼重設密碼] 對話方塊和 [確認密碼]。](./media/howto-create-manage-server-portal/reset-password.png)

3. 選取 **[確定]** 以儲存新密碼。


## <a name="delete-a-server"></a>刪除伺服器

如果您不再需要，您可以刪除伺服器。 

1. 在 Azure 入口網站中選取您的伺服器。 在 [ **總覽** ] 視窗中，選取 [ **刪除** ]。

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. 在 [輸入] 方塊中輸入伺服器的名稱，確認這是您要刪除的伺服器。

    ![螢幕擷取畫面會顯示對話方塊，確認您是否要刪除無法復原的資料庫。](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > 刪除伺服器無法復原。

3. 選取 [刪除] 。


## <a name="next-steps"></a>後續步驟
- 瞭解 [備份和伺服器還原](howto-restore-server-portal.md)
- 瞭解 [適用於 MariaDB 的 Azure 資料庫中的微調和監視選項](concepts-monitoring.md)
