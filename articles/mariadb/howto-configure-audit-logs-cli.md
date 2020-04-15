---
title: 存取稽核紀錄 ─ Azure CLI - MariaDB 的 Azure 資料庫
description: 本文介紹如何從 Azure CLI 配置和訪問 MariaDB Azure 資料庫中的審核日誌。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: e9716f0fa8e0ae44d614bbb28ed6846105e683d6
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384190"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>在 Azure CLI 中設定及存取稽核紀錄

可以從 Azure CLI[為 MariaDB 審核紀錄](concepts-audit-logs.md)配置 Azure 資料庫。

> [!IMPORTANT]
> 審核日誌功能當前處於預覽狀態。

## <a name="prerequisites"></a>Prerequisites

若要逐步執行本作法指南，您需要︰

- [適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 本操作說明指南會要求您使用 Azure CLI 2.0 版或更新版本。 若要確認版本，請在 Azure CLI 命令提示字元中輸入 `az --version`。 若要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="configure-audit-logging"></a>設定稽核記錄記錄

使用以下步驟啟用與設定稽核紀錄記錄: 

1. 通過將**audit_logs_enabled**參數設置為「ON」來打開審核日誌。 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. 透過更新**audit_log_egitvents**參數選擇要記錄[的事件型態](concepts-audit-logs.md#configure-audit-logging)。
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. 通過更新**audit_log_exclude_users**參數,添加要從日誌記錄中排除的任何 MariaDB 使用者。 通過提供 MariaDB 使用者名指定使用者。
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. 通過更新**audit_log_include_users**參數添加要包括的任何特定 MariaDB 使用者進行日誌記錄。 通過提供 MariaDB 使用者名指定使用者。
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>後續步驟

- 瞭解有關 MariaDB Azure 資料庫中[的稽核紀錄](concepts-audit-logs.md)的詳細資訊
- 瞭解如何在[Azure 門戶](howto-configure-audit-logs-portal.md)中設定稽核紀錄