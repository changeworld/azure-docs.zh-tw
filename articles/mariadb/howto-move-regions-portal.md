---
title: 移動 Azure 區域-Azure 入口網站-適用於 MariaDB 的 Azure 資料庫
description: 使用讀取複本和 Azure 入口網站將適用於 MariaDB 的 Azure 資料庫伺服器從一個 Azure 區域移至另一個區域。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/29/2020
ms.openlocfilehash: f4ce34bc1a1af7b2c0ee57a3297415bd9d033517
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540819"
---
# <a name="move-an-azure-database-for-mariadb-server-to-another-region-by-using-the-azure-portal"></a>使用 Azure 入口網站將適用於 MariaDB 的 Azure 資料庫伺服器移至另一個區域

將現有適用於 MariaDB 的 Azure 資料庫伺服器從一個區域移至另一個區域的案例有很多種。 例如，您可能會想要將實際執行伺服器移至另一個區域，作為嚴重損壞修復計畫的一部分。

您可以使用適用於 MariaDB 的 Azure 資料庫的 [跨區域讀取複本](concepts-read-replicas.md#cross-region-replication) 來完成移至另一個區域的工作。 若要這樣做，請先在目的地區域中建立讀取複本。 接下來，停止複寫到讀取複本伺服器，使其成為同時接受讀取和寫入流量的獨立伺服器。 

> [!NOTE]
> 本文著重于將您的伺服器移至不同的區域。 如果您想要將您的伺服器移至不同的資源群組或訂用帳戶，請參閱 [移動](../azure-resource-manager/management/move-resource-group-and-subscription.md) 文章。 

## <a name="prerequisites"></a>必要條件

- 讀取複本功能僅適用于一般用途或記憶體優化定價層中的適用於 MariaDB 的 Azure 資料庫伺服器。 確定來源伺服器是在其中一個定價層。

- 請確定您的適用於 MariaDB 的 Azure 資料庫來源伺服器位於您想要移動的 Azure 區域中。

## <a name="prepare-to-move"></a>準備移動

若要使用 Azure 入口網站在目的地區域中建立跨區域讀取複本伺服器，請使用下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取您要用來作為來源伺服器的現有適用於 MariaDB 的 Azure 資料庫伺服器。 這個動作會開啟 [概觀] 頁面。
1. 選取 [設定] 下方功能表中的 [複寫]。
1. 選取 [新增複本]。
1. 輸入複本伺服器的名稱。
1. 選取複本伺服器的位置。 預設位置與來源伺服器相同。 確認您已選取要部署複本的目標位置。
1. 選取 [確定] 來確認建立複本。 在建立複本期間，會從來源伺服器將資料複製到複本。 建立時間可能會持續幾分鐘或更久，與來源伺服器的大小成正比。

>[!NOTE]
> 當您建立複本時，它不會繼承來源伺服器的 VNet 服務端點。 您必須個別針對複本設定這些規則。

## <a name="move"></a>移動

> [!IMPORTANT]
> 獨立伺服器無法再次設定為複本。
> 在您停止讀取複本上的複寫之前，請確定該複本上已經有您所需要的所有資料。

停止複寫到複本伺服器會使其成為獨立伺服器。 若要停止從 Azure 入口網站複寫至複本，請使用下列步驟：

1. 建立複本之後，請找出並選取您的適用於 MariaDB 的 Azure 資料庫來源伺服器。 
1. 選取 [設定] 下方功能表中的 [複寫]。
1. 選取複本伺服器。
1. 選取 [停止複寫]。
1. 按一下 [確定] 確認您要停止複寫。

## <a name="clean-up-source-server"></a>清除來源伺服器

您可能會想要刪除來源適用於 MariaDB 的 Azure 資料庫伺服器。 若要這樣做，請使用下列步驟：

1. 建立複本之後，請找出並選取您的適用於 MariaDB 的 Azure 資料庫來源伺服器。
1. 在 [ **總覽** ] 視窗中，選取 [ **刪除** ]。
1. 輸入來源伺服器的名稱，以確認您想要刪除。
1. 選取 [刪除] 。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用 Azure 入口網站將適用於 MariaDB 的 Azure 資料庫伺服器從一個區域移至另一個區域，然後清除不需要的來源資源。 

- 深入了解[讀取複本](concepts-read-replicas.md)
- 深入瞭解如何 [管理 Azure 入口網站中的讀取複本](howto-read-replicas-portal.md)
- 深入瞭解 [商務持續性](concepts-business-continuity.md) 選項