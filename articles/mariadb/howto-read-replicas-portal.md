---
title: 管理讀取複本-Azure 入口網站適用於 MariaDB 的 Azure 資料庫
description: 本文說明如何使用入口網站來設定和管理適用於 MariaDB 的 Azure 資料庫中的讀取複本
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/21/2020
ms.openlocfilehash: 20d8e46d6fa6b031c809d629a6af41e8e682bcef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82025079"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>如何使用 Azure 入口網站在適用於 MariaDB 的 Azure 資料庫中建立及管理讀取複本

在本文中，您將瞭解如何使用 Azure 入口網站，在適用於 MariaDB 的 Azure 資料庫服務中建立及管理讀取複本。

## <a name="prerequisites"></a>先決條件

- 將做為主伺服器使用的[適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-portal.md)。

> [!IMPORTANT]
> 「讀取複本」功能僅適用于一般用途或記憶體優化定價層中的適用於 MariaDB 的 Azure 資料庫伺服器。 請確定主要伺服器處於這些定價層中。

## <a name="create-a-read-replica"></a>建立讀取複本

您可以使用下列步驟建立讀取複本伺服器︰

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取您想要作為主要的現有適用於 MariaDB 的 Azure 資料庫伺服器。 這個動作會開啟 [概觀]**** 頁面。

3. 選取 [設定]**** 下方功能表中的 [複寫]****。

4. 選取 [新增複本]****。

   ![適用於 MariaDB 的 Azure 資料庫-複寫](./media/howto-read-replica-portal/add-replica.png)

5. 輸入複本伺服器的名稱。

    ![適用於 MariaDB 的 Azure 資料庫-複本名稱](./media/howto-read-replica-portal/replica-name.png)

6. 選取複本伺服器的位置。 預設位置與主伺服器的相同。

    ![適用於 MariaDB 的 Azure 資料庫-複本位置](./media/howto-read-replica-portal/replica-location.png)

7. 選取 **[確定]** 以確認複本的建立。

> [!NOTE]
> 系統會以與主要伺服器相同的伺服器設定建立讀取複本。 複本伺服器設定在建立後可以變更。 建議複本伺服器設定的值應保持等於或大於主要伺服器，以確保複本伺服器能保持與主要伺服器一致。

建立複本伺服器後，可從 [複寫]**** 刀鋒視窗檢視該伺服器。

   ![適用於 MariaDB 的 Azure 資料庫-列出複本](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>停止複寫至複本伺服器

> [!IMPORTANT]
> 停止複寫至伺服器是無法復原的。 一旦停止主要與複本伺服器之間進行複寫，便無法復原。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。 此伺服器無法再次設定為複本伺服器。

若要從 Azure 入口網站停止主要和複本伺服器之間的複寫，請使用下列步驟：

1. 在 [Azure 入口網站中，選取您的主要適用於 MariaDB 的 Azure 資料庫伺服器。 

2. 選取 [設定]**** 下方功能表中的 [複寫]****。

3. 選取您想要停止複寫的複本伺服器。

   ![適用於 MariaDB 的 Azure 資料庫-停止複寫選取伺服器](./media/howto-read-replica-portal/stop-replication-select.png)

4. 選取 [**停止**複寫]。

   ![適用於 MariaDB 的 Azure 資料庫-停止複寫](./media/howto-read-replica-portal/stop-replication.png)

5. 按一下 [確定]**** 確認您要停止複寫。

   ![適用於 MariaDB 的 Azure 資料庫-停止複寫確認](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>刪除複本伺服器

若要從 Azure 入口網站刪除讀取複本伺服器，請使用下列步驟：

1. 在 [Azure 入口網站中，選取您的主要適用於 MariaDB 的 Azure 資料庫伺服器。

2. 選取 [設定]**** 下方功能表中的 [複寫]****。

3. 選取您想要刪除的複本伺服器。

   ![適用於 MariaDB 的 Azure 資料庫-刪除複本選取伺服器](./media/howto-read-replica-portal/delete-replica-select.png)

4. 選取 [**刪除複本**]

   ![適用於 MariaDB 的 Azure 資料庫-刪除複本](./media/howto-read-replica-portal/delete-replica.png)

5. 輸入複本名稱，然後按一下 [刪除]**** 確認刪除複本。  

   ![適用於 MariaDB 的 Azure 資料庫-刪除複本確認](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>刪除主要複本

> [!IMPORTANT]
> 刪除主要伺服器會停止對所有複本伺服器複寫，並刪除主要伺服器本身。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。

若要從 Azure 入口網站刪除主要伺服器，請使用下列步驟：

1. 在 [Azure 入口網站中，選取您的主要適用於 MariaDB 的 Azure 資料庫伺服器。

2. 從 [概觀]**** 中選取 [刪除]****。

   ![適用於 MariaDB 的 Azure 資料庫-刪除主要](./media/howto-read-replica-portal/delete-master-overview.png)

3. 輸入主要伺服器的名稱，然後按一下 [刪除]**** 確認刪除主要伺服器。  

   ![適用於 MariaDB 的 Azure 資料庫-刪除主要](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>監視複寫

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您要監視的複本適用於 MariaDB 的 Azure 資料庫伺服器。

2. 在提要欄位的 [監視]**** 區段下方，選取 [計量]****：

3. 從可用計量下拉式清單中選取 [複寫延遲 (秒)]****。

   ![選取複寫延遲](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. 選取您要檢視的時間範圍。 下圖選取 30 分鐘的時間範圍。

   ![選取時間範圍](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. 檢視所選時間範圍的複寫延遲。 下圖顯示大型工作負載的過去30分鐘。

   ![選取時間範圍](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>後續步驟

- 深入了解[讀取複本](concepts-read-replicas.md)