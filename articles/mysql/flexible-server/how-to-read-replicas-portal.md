---
title: 管理讀取複本-Azure 入口網站-適用於 MySQL 的 Azure 資料庫-彈性伺服器
description: 瞭解如何使用 Azure 入口網站在適用於 MySQL 的 Azure 資料庫彈性的伺服器中設定和管理讀取複本。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: cd2d3363b9c035987280eb27632c470c012b8bbb
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795189"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-portal"></a>如何使用 Azure 入口網站在適用於 MySQL 的 Azure 資料庫彈性的伺服器中建立及管理讀取複本

> [!IMPORTANT]
> 在適用於 MySQL 的 Azure 資料庫彈性的伺服器中讀取複本處於預覽階段。

在本文中，您將瞭解如何使用 Azure 入口網站，在適用於 MySQL 的 Azure 資料庫彈性的伺服器中建立及管理讀取複本。

> [!Note]
> 在啟用高可用性的伺服器上不支援複本。 

## <a name="prerequisites"></a>Prerequisites

- 將作為來源伺服器使用 [適用於 MySQL 的 Azure 資料庫伺服器彈性的伺服器](quickstart-create-server-portal.md) 。

## <a name="create-a-read-replica"></a>建立讀取複本

> [!IMPORTANT]
> 當您為沒有現有複本的來源建立複本時，來源會先重新開機以準備複寫。 請考慮這一點，並在離峰期間執行這些作業。

您可以使用下列步驟建立讀取複本伺服器︰

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取您要作為來源的現有適用於 MySQL 的 Azure 資料庫彈性伺服器。 這個動作會開啟 [概觀] 頁面。

3. 選取 [設定] 下方功能表中的 [複寫]。

4. 選取 [新增複本]。

   :::image type="content" source="./media/how-to-read-replica-portal/add-replica.png" alt-text="適用於 MySQL 的 Azure 資料庫 -複寫":::

5. 輸入複本伺服器的名稱。

    :::image type="content" source="./media/how-to-read-replica-portal/replica-name.png" alt-text="適用於 MySQL 的 Azure 資料庫 -複寫":::

6. 選取 [確定] 來確認建立複本。

> [!NOTE]
> 使用與來源相同的伺服器設定來建立讀取複本。 複本伺服器設定在建立後可以變更。 複本伺服器一律會建立在相同的資源群組、與來源伺服器相同的位置和相同的訂用帳戶中。 如果您想要將複本伺服器建立到不同的資源群組或不同的訂閱，您可以在建立後[移動複本伺服器](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription)。 建議將複本伺服器的設定保留為等於或大於來源的值，以確保複本能夠跟上來源。

建立複本伺服器後，可從 [複寫] 刀鋒視窗檢視該伺服器。

   [:::image type="content" source="./media/how-to-read-replica-portal/list-replica.png" alt-text="適用於 MySQL 的 Azure 資料庫 -複寫":::](./media/how-to-read-replica-portal/list-replica.png#lightbox)

## <a name="stop-replication-to-a-replica-server"></a>停止複寫至複本伺服器

> [!IMPORTANT]
> 停止複寫至伺服器是無法復原的。 一旦來源與複本之間的複寫停止，就無法復原。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。 此伺服器無法再次設定為複本伺服器。

若要從 Azure 入口網站停止來源與複本伺服器之間的複寫，請使用下列步驟：

1. 在 Azure 入口網站中，選取您的來源適用於 MySQL 的 Azure 資料庫彈性的伺服器。 

2. 選取 [設定] 下方功能表中的 [複寫]。

3. 選取您想要停止複寫的複本伺服器。

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-select.png" alt-text="適用於 MySQL 的 Azure 資料庫 -複寫":::](./media/how-to-read-replica-portal/stop-replication-select.png#lightbox)

4. 選取 [停止複寫]。

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication.png" alt-text="適用於 MySQL 的 Azure 資料庫 -複寫":::](./media/how-to-read-replica-portal/stop-replication.png#lightbox)

5. 按一下 [確定] 確認您要停止複寫。

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-confirm.png" alt-text="適用於 MySQL 的 Azure 資料庫 -複寫":::](./media/how-to-read-replica-portal/stop-replication-confirm.png#lightbox)

## <a name="delete-a-replica-server"></a>刪除複本伺服器

若要從 Azure 入口網站刪除讀取複本伺服器，請使用下列步驟：

1. 在 Azure 入口網站中，選取您的來源適用於 MySQL 的 Azure 資料庫彈性的伺服器。

2. 選取 [設定] 下方功能表中的 [複寫]。

3. 選取您想要刪除的複本伺服器。

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-replica-select.png" alt-text="適用於 MySQL 的 Azure 資料庫 -複寫":::](./media/how-to-read-replica-portal/delete-replica-select.png#lightbox)

4. 選取 [刪除複本]

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica.png" alt-text="適用於 MySQL 的 Azure 資料庫 -複寫":::

5. 輸入複本名稱，然後按一下 [刪除] 確認刪除複本。  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica-confirm.png" alt-text="適用於 MySQL 的 Azure 資料庫 -複寫":::

## <a name="delete-a-source-server"></a>刪除來源伺服器

> [!IMPORTANT]
> 刪除來源伺服器會停止對所有複本伺服器複寫，並刪除來源伺服器本身。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。

若要從 Azure 入口網站中刪除來源伺服器，請使用下列步驟：

1. 在 Azure 入口網站中，選取您的來源適用於 MySQL 的 Azure 資料庫彈性的伺服器。

2. 從 [概觀] 中選取 [刪除]。

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-master-overview.png" alt-text="適用於 MySQL 的 Azure 資料庫 -複寫":::](./media/how-to-read-replica-portal/delete-master-overview.png#lightbox)

3. 輸入來源伺服器的名稱，然後按一下 [ **刪除** ] 以確認刪除來源伺服器。  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-master-confirm.png" alt-text="適用於 MySQL 的 Azure 資料庫 -複寫":::

## <a name="monitor-replication"></a>監視複寫

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您要監視的複本適用於 MySQL 的 Azure 資料庫彈性伺服器。

2. 在提要欄位的 [監視] 區段下方，選取 [計量]：

3. 從可用計量下拉式清單中選取 [複寫延遲 (秒)]。

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-select-replication-lag.png" alt-text="適用於 MySQL 的 Azure 資料庫 -複寫":::](./media/how-to-read-replica-portal/monitor-select-replication-lag.png#lightbox)

4. 選取您要檢視的時間範圍。 下圖選取 30 分鐘的時間範圍。

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="適用於 MySQL 的 Azure 資料庫 -複寫":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png#lightbox)

5. 檢視所選時間範圍的複寫延遲。 下圖顯示過去 30 分鐘。

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="適用於 MySQL 的 Azure 資料庫 -複寫":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png#lightbox)

## <a name="next-steps"></a>後續步驟

- 深入了解[讀取複本](concepts-read-replicas.md)
