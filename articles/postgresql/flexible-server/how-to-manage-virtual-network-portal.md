---
title: 管理虛擬網路-Azure 入口網站-適用於 PostgreSQL 的 Azure 資料庫-彈性的伺服器
description: 使用 Azure 入口網站為適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器建立及管理虛擬網路
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 46d8fe6427b2a3e7811719792ac4bf67ddbcc3c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936597"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>使用 Azure 入口網站為適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器建立及管理虛擬網路

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器支援兩種互斥的網路連線方法，以連接到您的彈性伺服器。 這兩個選項是：

* 公用存取 (允許的 IP 位址)
* 私人存取 (VNet 整合)

在本文中，我們將著重于使用 ** (VNet 整合) ** 使用 Azure 入口網站來建立于 postgresql 伺服器。 使用私人存取 (VNet 整合) ，您可以將彈性的伺服器部署到您自己的 [Azure 虛擬網路](../../virtual-network/virtual-networks-overview.md)。 Azure 虛擬網路提供私人且安全的網路通訊。 使用私用存取時，于 postgresql 伺服器的連線會限制為您的虛擬網路。 若要深入瞭解，請參閱 [私人存取 (VNet 整合) ](./concepts-networking.md#private-access-vnet-integration)。

您可以在伺服器建立期間，將彈性伺服器部署至虛擬網路和子網路中。 部署彈性伺服器之後，您就無法將其移至另一個虛擬網路、子網路或*公用存取 (允許的 IP 位址)* 中。

## <a name="prerequisites"></a>必要條件
若要在虛擬網路中建立彈性的伺服器，您需要：
- [虛擬網路](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > 虛擬網路和子網應該與您彈性的伺服器位於相同的區域和訂用帳戶中。

-  將 [子網委派](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) 給 **DBforPostgreSQL/flexibleServers**。 此委派表示只有適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器可以使用該子網。 委派的子網路中不可以有其他 Azure 資源類型。

## <a name="create-azure-database-for-postgresql---flexible-server-in-an-already-existing-virtual-network"></a>在已經存在的虛擬網路中建立適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器

1. 在入口網站的左上角，選取 [建立資源]  (+)。
2. 選取 [資料庫]   > [Azure Database for PostgreSQL]  。 您也可以在 [搜尋] 方塊中輸入 **于 postgresql** 來尋找服務。
3. 選取 [彈性伺服器] 作為部署選項。
4. 填寫 **基本** 形式。
5. 移至 [ **網路** ] 索引標籤，設定您要連接到伺服器的方式。
6. 在 [連線 **方法**] 中，選取 [ **私人存取 (VNet 整合) **。 移至 [ **虛擬網路** ]，然後選取現有的 *虛擬網路* 和 *子網* （在上述必要條件中建立）。
7. 選取 [檢閱 + 建立]，檢閱彈性伺服器組態。
8. 選取 [建立] 以佈建伺服器。 佈建這可能需要數分鐘的時間。

>[!Note]
> 將彈性伺服器部署到虛擬網路和子網之後，您就無法將它移至公用存取 (允許的 IP 位址) 。
## <a name="next-steps"></a>後續步驟
- [使用 Azure CLI 建立及管理適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器虛擬網路](./how-to-manage-virtual-network-cli.md)。
- 深入瞭解 [適用於 PostgreSQL 的 Azure 資料庫彈性伺服器中的網路功能](./concepts-networking.md)
- 深入瞭解 [適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器虛擬網路](./concepts-networking.md#private-access-vnet-integration)。