---
title: 存取 audit 記錄-Azure CLI-適用於 MySQL 的 Azure 資料庫
description: 本文說明如何從 Azure CLI 設定和存取適用於 MySQL 的 Azure 資料庫中的 audit 記錄。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/24/2020
ms.openlocfilehash: 1a26f3c1b57fd750cbb59f32ddc8bd0ea85293ca
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86112709"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>設定和存取 Azure CLI 中的 audit 記錄

您可以從 Azure CLI 設定[適用於 MySQL 的 Azure 資料庫的審核記錄](concepts-audit-logs.md)。

## <a name="prerequisites"></a>必要條件

若要逐步執行本作法指南，您需要︰

- [適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 本操作說明指南會要求您使用 Azure CLI 2.0 版或更新版本。 若要確認版本，請在 Azure CLI 命令提示字元中輸入 `az --version`。 若要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="configure-audit-logging"></a>設定審核記錄

>[!IMPORTANT]
> 建議您只記錄您的審核目的所需的事件種類和使用者，以確保伺服器的效能不會受到嚴重影響。

使用下列步驟來啟用及設定 audit 記錄：

1. 藉由將**audit_logs_enabled**參數設為 "on" 來開啟 audit 記錄。 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. 藉由更新**audit_log_events**參數來選取要記錄的[事件種類](concepts-audit-logs.md#configure-audit-logging)。
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. 藉由更新**audit_log_exclude_users**參數，新增要排除在記錄之外的任何 MySQL 使用者。 藉由提供 MySQL 使用者名稱來指定使用者。
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. 藉由更新**audit_log_include_users**參數，新增要包含在記錄中的任何特定 MySQL 使用者。 藉由提供 MySQL 使用者名稱來指定使用者。
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>後續步驟
- 深入瞭解適用於 MySQL 的 Azure 資料庫中的[audit 記錄](concepts-audit-logs.md)
- 瞭解如何在[Azure 入口網站](howto-configure-audit-logs-portal.md)中設定 audit 記錄