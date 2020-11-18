---
title: 主要版本升級-Azure 入口網站-適用於 MySQL 的 Azure 資料庫-單一伺服器
description: 本文說明如何使用 Azure 入口網站升級適用於 MySQL 的 Azure 資料庫單一伺服器的主要版本
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 78c35e42cefa8897d9f93c3a941b4c0e8b81e5f9
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686682"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>使用 Azure 入口網站適用於 MySQL 的 Azure 資料庫單一伺服器的主要版本升級

> [!IMPORTANT]
> 適用于 MySQL 的 Azure 資料庫單一伺服器的主要版本升級處於公開預覽狀態。

本文說明如何在適用於 MySQL 的 Azure 資料庫單一伺服器中就地升級 MySQL 主要版本。

這項功能可讓客戶在沒有任何資料移動或需要任何應用程式連接字串變更的情況下，透過按一下按鈕來執行 MySQL 5.6 伺服器至 MySQL 5.7 的就地升級。

> [!Note]
> * 主要版本升級僅適用于從 MySQL 5.6 升級至 MySQL 5.7 的主要版本<br>
> * 複本伺服器上目前不支援主要版本升級。

## <a name="prerequisites"></a>先決條件
若要完成本操作說明指南，您需要：
- [適用於 MySQL 的 Azure 資料庫單一伺服器](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>執行從 MySQL 5.6 升級至 MySQL 5.7 的主要版本

遵循下列步驟來執行適用于 MySQL 5.6 伺服器的 Azure 資料庫的主要版本升級

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您現有的適用於 MySQL 的 Azure 資料庫5.6 伺服器。

2. 在 [ **總覽** ] 頁面中，按一下工具列中的 [ **升級** ] 按鈕。

3. 在 [ **升級** ] 區段中，選取 **[確定]** ，將適用于 MySQL 的 Azure 資料庫5.6 伺服器升級至5.7 伺服器。

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="適用於 MySQL 的 Azure 資料庫-總覽-升級":::

4. 通知將會確認升級是否成功。

## <a name="next-steps"></a>後續步驟

瞭解 [適用於 MySQL 的 Azure 資料庫版本控制原則](concepts-version-policy.md)。