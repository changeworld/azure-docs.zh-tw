---
title: CLI 指令碼 - 變更伺服器參數 - 適用於 MariaDB 的 Azure 資料庫
description: 這個範例 CLI 指令碼會列出適用於 MariaDB 的 Azure 資料庫所有可用伺服器組態和更新。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: ce27dc3008803faf548c10f4fc2ab3b579af2ba0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90525026"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>使用 Azure CLI 列出及更新適用於 MariaDB 的 Azure 資料庫伺服器組態
這個範例 CLI 指令碼會列出所有可用的組態參數，以及其對於「適用於 MariaDB 的 Azure 資料庫」伺服器的允許值，並將 innodb_lock_wait_timeout** 設定為預設值以外的值。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本執行 CLI，本文需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以檢查版本。 請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli) 以安裝或升級 Azure CLI 版本。 

## <a name="sample-script"></a>範例指令碼
在此範例指令碼中，請編輯醒目提示的命令列，將系統管理員使用者名稱和密碼更新為您自己的使用者名稱和密碼。
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>清除部署
執行指令碼之後，請使用下列命令來移除資源群組及其相關聯的所有資源。
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>指令碼說明
此指令碼會使用下表中簡述的命令：

| **命令** | **注意事項** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | 建立託管資料庫的 MariaDB 伺服器。 |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | 列出適用於 MariaDB 的 Azure 資料庫伺服器組態。 |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | 更新適用於 MariaDB 的 Azure 資料庫伺服器組態。 |
| [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | 顯示適用於 MariaDB 的 Azure 資料庫伺服器組態。 |
| [az group delete](/cli/azure/group#az-group-delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟
- 了解 Azure CLI 的詳細資訊：[Azure CLI 文件](/cli/azure)。
- 嘗試額外的指令碼：[「適用於 MariaDB 的 Azure 資料庫」的 Azure CLI 範例](../sample-scripts-azure-cli.md)
- 如需有關伺服器參數的詳細資訊，請參閱[如何在適用於 MariaDB 的 Azure 資料庫中設定伺服器參數](../howto-server-parameters.md)。
