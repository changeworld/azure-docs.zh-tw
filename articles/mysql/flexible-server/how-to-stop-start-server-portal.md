---
title: 停止/啟動-Azure 入口網站-適用於 MySQL 的 Azure 資料庫彈性伺服器
description: 本文說明如何透過 Azure 入口網站停止/啟動適用於 MySQL 的 Azure 資料庫中的作業。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: 15b08ea67afe0d307470b5a4fb0f7d26e0f4ea82
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241917"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server-preview"></a>停止/啟動適用於 MySQL 的 Azure 資料庫彈性的伺服器 (Preview) 

> [!IMPORTANT]
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。

本文提供的逐步程式，可讓您執行彈性伺服器的停止和啟動。

## <a name="prerequisites"></a>先決條件

若要完成本操作說明指南，您需要：

-   您必須有適用於 MySQL 的 Azure 資料庫彈性的伺服器。

## <a name="stop-a-running-server"></a>停止正在執行的伺服器

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選擇您想要停止的彈性伺服器。

2.  在 [ **總覽** ] 頁面中，按一下工具列中的 [ **停止** ] 按鈕。
    
    :::image type="content" source="media/how-to-stop-start-server-portal/stop-server.png" alt-text="停止彈性的伺服器。"::: 

3.  按一下 **[是]** 以確認停止您的伺服器。

    :::image type="content" source="media/how-to-stop-start-server-portal/confirm-stop.png" alt-text="停止彈性的伺服器。"::: 

> [!NOTE]
> 停止伺服器之後，就無法使用彈性伺服器的其他管理作業。

## <a name="start-a-stopped-server"></a>啟動已停止的伺服器

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選擇您想要啟動的彈性伺服器。

2.  在 [ **總覽** ] 頁面中，按一下工具列中的 [ **開始** ] 按鈕。

    :::image type="content" source="media/how-to-stop-start-server-portal/start-server.png" alt-text="停止彈性的伺服器。":::  

> [!NOTE]
> 一旦啟動伺服器，所有的管理作業現在都可供彈性的伺服器使用。

## <a name="next-steps"></a>下一步
- 深入瞭解[適用於 MySQL 的 Azure 資料庫彈性伺服器中的網路](./concepts-networking.md)功能
- [使用 Azure 入口網站建立和管理適用於 MySQL 的 Azure 資料庫彈性伺服器虛擬網路](./how-to-manage-virtual-network-portal.md)。

