---
title: 存取 audit 記錄檔-Azure CLI-適用於 MariaDB 的 Azure 資料庫
description: 本文說明如何從 Azure CLI 設定和存取適用於 MariaDB 的 Azure 資料庫中的 audit 記錄。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: c1d446d8ee2863077ad84c361876758336f5a3cb
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540955"
---
# <a name="configure-and-access-azure-database-for-maria-db-audit-logs-in-the-azure-cli"></a>在 Azure CLI 中設定和存取適用于 Maria 資料庫的 Azure 資料庫審核記錄

您可以從 Azure CLI 設定 [適用於 MariaDB 的 Azure 資料庫審核記錄](concepts-audit-logs.md) 。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

若要完成本指南：

- 您需要 [適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-portal.md)。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 本文需要 Azure CLI 2.0 版或更新版本。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="configure-audit-logging"></a>設定 audit 記錄

>[!IMPORTANT]
> 建議您只針對您的審核目的記錄所需的事件種類和使用者，以確保伺服器的效能不會受到嚴重的影響。

使用下列步驟來啟用和設定 audit 記錄： 

1. 藉由將 **audit_logs_enabled** 參數設定為 "on" 來開啟 audit 記錄。 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. 藉由更新 **audit_log_events** 參數，選取要記錄的 [事件種類](concepts-audit-logs.md#configure-audit-logging)。
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. 藉由更新 **audit_log_exclude_users** 參數，新增要從記錄中排除的任何適用于 mariadb 使用者。 提供使用者的適用于 mariadb 使用者名稱來指定使用者。
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. 藉由更新 **audit_log_include_users** 參數，加入要包含在記錄中的任何特定適用于 mariadb 使用者。 提供使用者的適用于 mariadb 使用者名稱來指定使用者。
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>後續步驟

- 深入瞭解適用於 MariaDB 的 Azure 資料庫中的[audit 記錄](concepts-audit-logs.md)
- 瞭解如何在[Azure 入口網站](howto-configure-audit-logs-portal.md)中設定 audit 記錄
