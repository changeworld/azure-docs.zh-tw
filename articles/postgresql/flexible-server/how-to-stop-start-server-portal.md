---
title: 停止/啟動-Azure 入口網站-適用於 PostgreSQL 的 Azure 資料庫彈性伺服器
description: 本文說明如何透過 Azure 入口網站停止/啟動適用於 PostgreSQL 的 Azure 資料庫中的作業。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 4c393e0048a0058ebe0fbf2b0ee65f6ae2e184c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934218"
---
# <a name="stopstart-an-azure-database-for-postgresql---flexible-server-preview"></a>停止/啟動適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器 (Preview) 

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器目前為預覽狀態。

本文提供的逐步指示可讓您停止和啟動有彈性的伺服器。

## <a name="pre-requisites"></a>必要條件

若要完成本操作說明指南，您需要：

-   您必須有適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器。

## <a name="stop-a-running-server"></a>停止正在執行的伺服器

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選擇您想要停止的彈性伺服器。

2.  在 [ **總覽** ] 頁面中，按一下工具列中的 [ **停止** ] 按鈕。

> [!NOTE]
> 停止伺服器之後，就無法使用彈性伺服器的其他管理作業。

請注意，已停止的伺服器將會在七天后自動重新開機。 下次啟動伺服器時，將會套用任何擱置中的維護更新。

## <a name="start-a-stopped-server"></a>啟動已停止的伺服器

1.  在 [ [Azure 入口網站](https://portal.azure.com/)中，選擇您想要啟動的彈性伺服器。

2.  在 [ **總覽** ] 頁面中，按一下工具列中的 [ **開始** ] 按鈕。

> [!NOTE]
> 一旦啟動伺服器，所有的管理作業現在都可供彈性的伺服器使用。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [適用於 PostgreSQL 的 Azure 資料庫彈性伺服器中的計算和儲存體選項](./concepts-compute-storage.md)。
