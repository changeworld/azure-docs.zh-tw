---
title: 管理讀取複本-Azure 入口網站-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 瞭解如何從 Azure 入口網站管理適用於 PostgreSQL 的 Azure 資料庫單一伺服器的讀取複本。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: d0de485158477496419dc394aaf6bdd1d28e772a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535809"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>從 Azure 入口網站在適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中建立及管理讀取複本

在本文中，您將了解如何透過 Azure 入口網站，在「適用於 PostgreSQL 的 Azure 資料庫」中建立與管理讀取複本。 若要深入了解讀取複本，請參閱[概觀](concepts-read-replicas.md)。


## <a name="prerequisites"></a>必要條件
[適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-database-portal.md)作為主伺服器。

## <a name="azure-replication-support"></a>Azure 複寫支援

[讀取複本](concepts-read-replicas.md) 和 [邏輯解碼](concepts-logical.md) 都取決於 Postgres 的預先寫入記錄 (WAL) 取得資訊。 這兩個功能需要來自 Postgres 的不同記錄層級。 邏輯解碼需要比讀取複本更高層級的記錄。

若要設定正確的記錄層級，請使用 Azure 複寫支援參數。 Azure 複寫支援有三個設定選項：

* **Off** -將最少的資訊放在 WAL 中。 這項設定無法在大部分的適用於 PostgreSQL 的 Azure 資料庫伺服器上使用。  
* **複本**-更**詳細的資訊。** 這是 [讀取複本](concepts-read-replicas.md) 正常運作所需的最小記錄層級。 這項設定在大部分伺服器上是預設值。
* **邏輯** 更詳細的資訊比 **複本**更詳細。 這是要讓邏輯解碼正常運作的最小記錄層級。 讀取複本也可在此設定中運作。

此參數變更之後，必須重新開機伺服器。 就內部而言，此參數會設定 Postgres 參數 `wal_level` 、 `max_replication_slots` 和 `max_wal_senders` 。

## <a name="prepare-the-primary-server"></a>準備主伺服器

1. 在 [Azure 入口網站中，選取要作為主伺服器使用的現有適用於 PostgreSQL 的 Azure 資料庫伺服器。

2. 從伺服器的功能表中 **，選取 [** 複寫]。 如果 Azure 複寫支援至少設定為 **複本**，則您可以建立讀取複本。 

3. 如果 Azure 複寫支援未設定為至少 **複本**，請加以設定。 選取 [儲存]。

   :::image type="content" source="./media/howto-read-replicas-portal/set-replica-save.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-設定複本並儲存":::

4. 選取 **[是]**，重新開機伺服器以套用變更。

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-restart.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-設定複本並儲存":::

5. 當作業完成時，您會收到兩個 Azure 入口網站通知。 有一個補救伺服器參數的通知。 接下來會有另一個伺服器重新開機通知。

   :::image type="content" source="./media/howto-read-replicas-portal/success-notifications.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-設定複本並儲存":::

6. 重新整理 Azure 入口網站頁面，以更新複寫工具列。 您現在可以建立此伺服器的讀取複本。
   

## <a name="create-a-read-replica"></a>建立讀取複本
若要建立讀取複本，請遵循下列步驟：

1. 選取現有的適用於 PostgreSQL 的 Azure 資料庫伺服器作為主伺服器使用。 

2. 在 [伺服器] 側邊欄的 [ **設定**] 底下 **，選取 [** 複寫]。

3. 選取 [新增複本]。

   :::image type="content" source="./media/howto-read-replicas-portal/add-replica.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-設定複本並儲存":::

4. 輸入讀取複本的名稱。 

    :::image type="content" source="./media/howto-read-replicas-portal/name-replica.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-設定複本並儲存":::

5. 選取複本的位置。 預設位置與主伺服器相同。

    :::image type="content" source="./media/howto-read-replicas-portal/location-replica.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-設定複本並儲存":::

   > [!NOTE]
   > 若要深入瞭解您可以在哪些區域中建立複本，請造訪[參閱複本概念文章](concepts-read-replicas.md)。 

6. 選取 [確定]**** 來確認建立複本。

建立讀取複本之後，可從 [複寫]**** 視窗檢視該複本：

:::image type="content" source="./media/howto-read-replicas-portal/list-replica.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-設定複本並儲存":::
 

> [!IMPORTANT]
> 請參閱 [讀取複本總覽的考慮一節](concepts-read-replicas.md#considerations)。
>
> 將主伺服器設定更新為新值之前，請將複本設定更新為相等或更大的值。 此動作可協助複本跟上對主伺服器所做的任何變更。

## <a name="stop-replication"></a>停止複寫
您可以停止主伺服器和讀取複本之間的複寫。

> [!IMPORTANT]
> 停止複寫到主伺服器和讀取複本之後，就無法復原。 讀取複本會變成支援讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。

若要停止 Azure 入口網站的主伺服器和讀取複本之間的複寫，請遵循下列步驟：

1. 在 [Azure 入口網站中，選取您的主要適用於 PostgreSQL 的 Azure 資料庫伺服器。

2. 在伺服器功能表的 [設定]**** 下方，選取 [複寫]****。

3. 選取要停止複寫的複本伺服器。

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-設定複本並儲存":::
 
4. 選取 [停止複寫]。

   :::image type="content" source="./media/howto-read-replicas-portal/select-stop-replication.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-設定複本並儲存":::
 
5. 選取 [確定]**** 以停止複寫。

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-stop-replication.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-設定複本並儲存":::
 

## <a name="delete-a-primary-server"></a>刪除主伺服器
若要刪除主伺服器，您可以使用與刪除獨立適用於 PostgreSQL 的 Azure 資料庫伺服器相同的步驟。 

> [!IMPORTANT]
> 當您刪除主伺服器時，將會停止複寫至所有讀取複本。 讀取複本會變成獨立伺服器，進而支援讀取和寫入。

若要從 Azure 入口網站刪除伺服器，請遵循下列步驟：

1. 在 [Azure 入口網站中，選取您的主要適用於 PostgreSQL 的 Azure 資料庫伺服器。

2. 開啟該伺服器的**概觀**頁面。 選取 [刪除]  。

   :::image type="content" source="./media/howto-read-replicas-portal/delete-server.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-設定複本並儲存":::
 
3. 輸入要刪除之主伺服器的名稱。 選取 [ **刪除** ] 以確認刪除主伺服器。

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-設定複本並儲存":::
 

## <a name="delete-a-replica"></a>刪除複本伺服器
您可以刪除讀取複本，類似于刪除主伺服器的方式。

- 在 Azure 入口網站中，開啟讀取複本的 [概觀]**** 頁面。 選取 [刪除]  。

   :::image type="content" source="./media/howto-read-replicas-portal/delete-replica.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-設定複本並儲存":::
 
您也可以透過下列步驟，從 [複寫]**** 視窗中刪除讀取複本：

1. 在 [Azure 入口網站中，選取您的主要適用於 PostgreSQL 的 Azure 資料庫伺服器。

2. 在伺服器功能表的 [設定]**** 下方，選取 [複寫]****。

3. 選取要刪除的讀取複本。

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-設定複本並儲存":::
 
4. 選取 [ **刪除複本**]。

   :::image type="content" source="./media/howto-read-replicas-portal/select-delete-replica.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-設定複本並儲存":::
 
5. 輸入要刪除的複本名稱。 選取 [刪除]**** 以確認刪除複本。

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete-replica.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-設定複本並儲存":::
 

## <a name="monitor-a-replica"></a>監視複本
有兩個計量可用來監視讀取複本。

### <a name="max-lag-across-replicas-metric"></a>「複本之間的最大延隔時間」計量
[ **複本之間的最大延隔** 時間] 計量會顯示主伺服器和最延遲複本之間的延遲（以位元組為單位）。 

1.  在 [Azure 入口網站中，選取主要適用於 PostgreSQL 的 Azure 資料庫伺服器。

2.  選取 [計量]。 在 [計量]**** 視窗中，選取 [複本之間的最大延隔時間]****。

    :::image type="content" source="./media/howto-read-replicas-portal/select-max-lag.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-設定複本並儲存":::
 
3.  選取 [最大值]**** 作為 [彙總]****。


### <a name="replica-lag-metric"></a>「複本延隔時間」計量
**複本延隔時間**計量顯示在自最後一次重新執行複本上交易的時間。 如果主要伺服器上沒有發生交易，計量會反映此時間延隔。

1. 在 Azure 入口網站中，選取「適用於 PostgreSQL 的 Azure 資料庫」讀取複本。

2. 選取 [計量]。 在 [計量]**** 視窗中，選取 [複本延隔時間]****。

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica-lag.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫複寫-設定複本並儲存":::
 
3. 選取 [最大值]**** 作為 [彙總]****。 
 
## <a name="next-steps"></a>後續步驟
* 深入了解[適用於 PostgreSQL 的 Azure 資料庫中的讀取複本](concepts-read-replicas.md)。
* 瞭解如何 [在 Azure CLI 和 REST API 中建立及管理讀取複本](howto-read-replicas-cli.md)。