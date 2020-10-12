---
title: 存取 audit 記錄檔-Azure CLI-適用於 MySQL 的 Azure 資料庫
description: 本文說明如何從 Azure CLI 設定和存取適用於 MySQL 的 Azure 資料庫中的 audit 記錄。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 568f49565c6e6d8062f8869566cf3879b7c97eaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87503322"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>設定及存取 Azure CLI 中的 audit 記錄

您可以從 Azure CLI 設定 [適用於 MySQL 的 Azure 資料庫審核記錄](concepts-audit-logs.md) 。

## <a name="prerequisites"></a>必要條件

若要逐步執行本作法指南，您需要︰

- [適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 本操作說明指南會要求您使用 Azure CLI 2.0 版或更新版本。 若要確認版本，請在 Azure CLI 命令提示字元中輸入 `az --version`。 若要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="configure-audit-logging"></a>設定 audit 記錄

>[!IMPORTANT]
> 建議您只針對您的審核目的記錄所需的事件種類和使用者，以確保伺服器的效能不會受到嚴重的影響。

使用下列步驟來啟用和設定 audit 記錄：

1. 藉由將 **audit_logs_enabled** 參數設定為 "on" 來開啟 audit 記錄。 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. 藉由更新**audit_log_events**參數，選取要記錄的[事件種類](concepts-audit-logs.md#configure-audit-logging)。
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. 藉由更新 **audit_log_exclude_users** 參數，新增要排除在記錄之外的任何 MySQL 使用者。 提供使用者的 MySQL 使用者名稱來指定使用者。
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. 藉由更新 **audit_log_include_users** 參數，新增要包含在記錄中的任何特定 MySQL 使用者。 提供使用者的 MySQL 使用者名稱來指定使用者。
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>接下來的步驟
- 深入瞭解適用於 MySQL 的 Azure 資料庫中的[audit 記錄](concepts-audit-logs.md)
- 瞭解如何在[Azure 入口網站](howto-configure-audit-logs-portal.md)中設定 audit 記錄
