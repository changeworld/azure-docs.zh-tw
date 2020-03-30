---
title: 管理伺服器 - Azure 門戶 - MariaDB 的 Azure 資料庫
description: 瞭解如何從 Azure 門戶管理 MariaDB 伺服器的 Azure 資料庫。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 43004f8c52bbec17f78ed4be024cf75224dbd179
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530235"
---
# <a name="manage-an-azure-database-for-mariadb-server-using-the-azure-portal"></a>使用 Azure 門戶管理 MariaDB 伺服器的 Azure 資料庫
本文介紹如何管理 MariaDB 伺服器的 Azure 資料庫。 管理工作包括計算和存儲擴展、管理員密碼重設和查看伺服器詳細資訊。

## <a name="sign-in"></a>登入
登錄到 Azure[門戶](https://portal.azure.com)。

## <a name="create-a-server"></a>建立伺服器
訪問[快速入門](quickstart-create-mariadb-server-database-using-azure-portal.md)，瞭解如何創建和開始使用 MariaDB 伺服器的 Azure 資料庫。

## <a name="scale-compute-and-storage"></a>擴展計算和存儲

創建伺服器後，您可以根據需要在"通用"層和記憶體優化層之間進行擴展。 您還可以通過增加或減少 vCore 來擴展計算和記憶體。 可以擴展存儲（但是，您不能縮減存儲）。

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>在通用層和記憶體優化層之間縮放

您可以將範圍從通用擴展到記憶體優化，反之亦然。 不支援在伺服器創建後更改到和從基本層更改。 

1. 在 Azure 門戶中選擇伺服器。 選擇 **"定價層**"，位於 **"設置"** 部分。

2. 選擇 **"通用"** 或 **"記憶體優化**"，具體取決於要縮放到的內容。 

    ![變更定價層](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > 更改層會導致伺服器重新開機。

4. 選擇 **"確定"** 以保存更改。


### <a name="scale-vcores-up-or-down"></a>向上或向下縮放 vCore

1. 在 Azure 門戶中選擇伺服器。 選擇 **"定價層**"，位於 **"設置"** 部分。

2. 將滑桿移至所需的值來變更 [vCore]**** 設定。

    ![scale-compute](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > 縮放 vCore 會導致伺服器重新開機。

3. 選擇 **"確定"** 以保存更改。


### <a name="scale-storage-up"></a>擴展存儲

1. 在 Azure 門戶中選擇伺服器。 選擇 **"定價層**"，位於 **"設置"** 部分。

2. 通過將滑塊向上移動到所需的值來更改**存儲**設置。

    ![scale-storage](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > 無法縮小存儲。

3. 選擇 **"確定"** 以保存更改。


## <a name="update-admin-password"></a>更新管理員密碼
您可以使用 Azure 門戶更改管理員角色的密碼。

1. 在 Azure 門戶中選擇伺服器。 在 **"概述"** 視窗中選擇 **"重置密碼**"。

   ![概觀](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. 輸入新密碼並確認密碼。 文字方塊將提示您有關密碼複雜性要求。

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. 選擇 **"確定"** 以保存新密碼。


## <a name="delete-a-server"></a>刪除伺服器

如果不再需要伺服器，可以將其刪除。 

1. 在 Azure 門戶中選擇伺服器。 在 **"概覽"** 視窗中選擇 **"刪除**"。

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. 在輸入框中鍵入伺服器的名稱，以確認這是要刪除的伺服器。

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > 刪除伺服器是不可逆的。

3. 選擇 **"刪除**"。


## <a name="next-steps"></a>後續步驟
- 瞭解[備份和伺服器還原](howto-restore-server-portal.md)
- 瞭解[MariaDB Azure 資料庫中的調優和監視選項](concepts-monitoring.md)
