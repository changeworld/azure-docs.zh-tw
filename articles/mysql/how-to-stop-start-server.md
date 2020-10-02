---
title: 停止/啟動-Azure 入口網站-適用於 MySQL 的 Azure 資料庫 server
description: 本文說明如何在適用於 MySQL 的 Azure 資料庫中停止/啟動作業。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 706748018c9f32ab2300b290c6929d344b82e0b0
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653089"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>停止/啟動適用於 MySQL 的 Azure 資料庫

> [!IMPORTANT]
> 適用於 MySQL 的 Azure 資料庫的停止/啟動功能目前處於公開預覽狀態。

本文提供逐步執行程式，以執行單一伺服器的停止和啟動。

## <a name="prerequisites"></a>Prerequisites

若要完成本操作說明指南，您需要：

-   您必須有適用於 MySQL 的 Azure 資料庫單一伺服器。

> [!NOTE]
> 請參閱使用[停止/啟動](concepts-servers.md#limitations-of-stopstart-operation)的限制

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>如何使用 Azure 入口網站停止/啟動適用於 MySQL 的 Azure 資料庫

### <a name="stop-a-running-server"></a>停止正在執行的伺服器

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選擇您想要停止的 MySQL 伺服器。

2.  在 [ **總覽** ] 頁面中，按一下工具列中的 [ **停止** ] 按鈕。

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="適用於 MySQL 的 Azure 資料庫停止伺服器":::

    > [!NOTE]
    > 停止伺服器之後，就無法使用單一伺服器的其他管理作業。

### <a name="start-a-stopped-server"></a>啟動已停止的伺服器

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選擇您想要啟動的單一伺服器。

2.  在 [ **總覽** ] 頁面中，按一下工具列中的 [ **開始** ] 按鈕。

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="適用於 MySQL 的 Azure 資料庫停止伺服器":::

    > [!NOTE]
    > 一旦啟動伺服器，所有的管理作業現在都可供單一伺服器使用。

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>如何使用 CLI 停止/啟動適用於 MySQL 的 Azure 資料庫

### <a name="stop-a-running-server"></a>停止正在執行的伺服器

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選擇您想要停止的 MySQL 伺服器。

2.  在 [ **總覽** ] 頁面中，按一下工具列中的 [ **停止** ] 按鈕。

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > 停止伺服器之後，就無法使用單一伺服器的其他管理作業。

### <a name="start-a-stopped-server"></a>啟動已停止的伺服器

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選擇您想要啟動的單一伺服器。

2.  在 [ **總覽** ] 頁面中，按一下工具列中的 [ **開始** ] 按鈕。

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > 一旦啟動伺服器，所有的管理作業現在都可供單一伺服器使用。

## <a name="next-steps"></a>後續步驟
瞭解 [如何建立計量警示](howto-alert-on-metric.md)。
