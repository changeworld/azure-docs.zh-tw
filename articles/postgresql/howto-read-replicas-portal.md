---
title: 管理讀取複本-Azure 入口網站-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 瞭解如何從 Azure 入口網站管理適用於 PostgreSQL 的 Azure 資料庫-單一伺服器的讀取複本。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: dd79618b8d9f016c92166edb9ecdb0bfb113947e
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768952"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>從 Azure 入口網站建立和管理適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的讀取複本

在本文中，您將了解如何透過 Azure 入口網站，在「適用於 PostgreSQL 的 Azure 資料庫」中建立與管理讀取複本。 若要深入了解讀取複本，請參閱[概觀](concepts-read-replicas.md)。


## <a name="prerequisites"></a>必要條件
使用[適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-database-portal.md)作為主要伺服器。

## <a name="prepare-the-master-server"></a>準備主要伺服器
若要在一般用途和記憶體最佳化層中準備主要伺服器，這些是必要步驟。 主伺服器已準備好進行複寫，方法是設定 azure. replication_support 參數。 當複寫參數變更時，需要重新開機伺服器，變更才會生效。 在 Azure 入口網站中，這兩個步驟是透過單一按鈕封裝，**啟用複寫支援**。

1. 在 Azure 入口網站中，選取要作為主要伺服器的現有「適用於 PostgreSQL 的 Azure 資料庫」伺服器。

2. 在 [伺服器] 提要欄位的 [**設定**] 底下 **，選取 [** 複寫]。

> [!NOTE] 
> 如果您看到 [停用複寫**支援**] 呈現灰色，則預設會在您的伺服器上設定複寫設定。 您可以略過下列步驟，並移至建立讀取複本。 

3. 選取 [**啟用複寫支援**]。 

   ![啟用複寫支援](./media/howto-read-replicas-portal/enable-replication-support.png)

4. 確認您想要啟用複寫支援。 此操作將會重新開機主伺服器。 

   ![確認啟用複寫支援](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. 作業完成後，您會收到兩個 Azure 入口網站通知。 有一個補救伺服器參數的通知。 接下來會出現另一個伺服器重新開機的通知。

   ![成功通知-啟用](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. 重新整理 [Azure 入口網站] 頁面，以更新 [複寫] 工具列。 您現在可以建立此伺服器的讀取複本。

   ![更新的工具列](./media/howto-read-replicas-portal/updated-toolbar.png)
   
啟用複寫支援是每一主伺服器一次的作業。 為了方便起見，會提供 [停用複寫**支援**] 按鈕。 我們不建議停用複寫支援，除非您確定不會在此主伺服器上建立複本。 當您的主伺服器具有現有複本時，您無法停用複寫支援。


## <a name="create-a-read-replica"></a>建立讀取複本
若要建立讀取複本，請遵循下列步驟：

1. 選取要作為主要伺服器的現有「適用於 PostgreSQL 的 Azure 資料庫」。 

2. 在 [伺服器] 提要欄位的 [**設定**] 底下 **，選取 [** 複寫]。

3. 選取 [新增複本]。

   ![新增複本](./media/howto-read-replicas-portal/add-replica.png)

4. 輸入讀取複本的名稱。 

    ![複本的名稱](./media/howto-read-replicas-portal/name-replica.png)

5. 選取複本的 [位置]。 預設位置與主伺服器的相同。

    ![選取位置](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > 若要深入瞭解您可以在哪些區域中建立複本，請造訪[讀取複本概念一文](concepts-read-replicas.md)。 

6. 選取 [確定] 來確認建立複本。

使用與主伺服器相同的計算和儲存設定來建立複本。 建立複本之後，以下設定可以個別地從主要伺服器進行變更：計算世代、虛擬核心、儲存體及備份保留期間。 定價層也可以個別變更，但不能變更為基本層，或從基本層變更為別的層。

> [!IMPORTANT]
> 將主伺服器設定更新為新值之前，請將複本設定更新為相等或更大的值。 此動作可協助複本跟上對主伺服器所做的任何變更。

建立讀取複本之後，可從 [複寫] 視窗檢視該複本：

![在 [複寫] 視窗中檢視新複本](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>停止複寫
您可以停止主要伺服器與讀取複本之間的複寫。

> [!IMPORTANT]
> 停止主要伺服器和讀取複本的複寫之後，即無法再復原。 讀取複本會變成支援讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。

若要從 Azure 入口網站停止主要與讀取複本之間的複寫，請遵循下列步驟：

1. 在 Azure 入口網站中，選取適用於 PostgreSQL 的 Azure 資料庫的主要伺服器。

2. 在伺服器功能表的 [設定] 下方，選取 [複寫]。

3. 選取要停止複寫的複本伺服器。

   ![選取複本](./media/howto-read-replicas-portal/select-replica.png)
 
4. 選取 [停止複寫]。

   ![選取 [停止複寫]](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. 選取 [確定] 以停止複寫。

   ![確認停止複寫](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>刪除主要複本
若要刪除主要伺服器，所用步驟與刪除獨立的「適用於 PostgreSQL 的 Azure 資料庫」相同。 

> [!IMPORTANT]
> 刪除主要伺服器時，所有讀取複本上的複寫都會停止。 讀取複本會變成獨立伺服器，進而支援讀取和寫入。

若要從 Azure 入口網站刪除伺服器，請遵循下列步驟：

1. 在 Azure 入口網站中，選取適用於 PostgreSQL 的 Azure 資料庫的主要伺服器。

2. 開啟該伺服器的**概觀**頁面。 選取 [刪除]。

   ![在伺服器的 [概觀] 頁面中，選取要刪除主要伺服器](./media/howto-read-replicas-portal/delete-server.png)
 
3. 輸入要刪除的主要伺服器名稱。 選取 [刪除] 以確認刪除主要伺服器。

   ![確認刪除主要伺服器](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>刪除複本伺服器
刪除讀取複本的方式類似於刪除主要伺服器。

- 在 Azure 入口網站中，開啟讀取複本的 [概觀] 頁面。 選取 [刪除]。

   ![在複本的概觀頁面上，選取要刪除複本](./media/howto-read-replicas-portal/delete-replica.png)
 
您也可以透過下列步驟，從 [複寫] 視窗中刪除讀取複本：

1. 在 Azure 入口網站中，選取適用於 PostgreSQL 的 Azure 資料庫的主要伺服器。

2. 在伺服器功能表的 [設定] 下方，選取 [複寫]。

3. 選取要刪除的讀取複本。

   ![選取要刪除的複本](./media/howto-read-replicas-portal/select-replica.png)
 
4. 選取 [刪除複本]。

   ![選取 [刪除複本]](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. 輸入要刪除的複本名稱。 選取 [刪除] 以確認刪除複本。

   ![確認刪除複本](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>監視複本
有兩個計量可用來監視讀取複本。

### <a name="max-lag-across-replicas-metric"></a>「複本之間的最大延隔時間」計量
[複本之間的最大延隔時間] 計量會顯示主要伺服器和最大延隔複本之間的延隔 (位元組)。 

1.  在 Azure 入口網站中，選取「適用於 PostgreSQL 的 Azure 資料庫」的主要伺服器。

2.  選取 [計量]。 在 [計量] 視窗中，選取 [複本之間的最大延隔時間]。

    ![監視複本之間的最大延隔時間](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  選取 [最大值] 作為 [彙總]。


### <a name="replica-lag-metric"></a>「複本延隔時間」計量
**複本延隔時間**計量顯示在自最後一次重新執行複本上交易的時間。 如果主要伺服器上沒有發生交易，計量會反映此時間延隔。

1. 在 Azure 入口網站中，選取「適用於 PostgreSQL 的 Azure 資料庫」讀取複本。

2. 選取 [計量]。 在 [計量] 視窗中，選取 [複本延隔時間]。

   ![監視複本延隔時間](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. 選取 [最大值] 作為 [彙總]。 
 
## <a name="next-steps"></a>後續步驟
* 深入了解[適用於 PostgreSQL 的 Azure 資料庫中的讀取複本](concepts-read-replicas.md)。
* 瞭解如何[在 Azure CLI 和 REST API 中建立及管理讀取複本](howto-read-replicas-cli.md)。