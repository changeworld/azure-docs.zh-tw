---
title: CLI 指令碼 - 還原伺服器 - 適用於 MariaDB 的 Azure 資料庫
description: 此範例 Azure CLI 指令碼示範如何將「適用於 MariaDB 的 Azure 資料庫」伺服器及其資料庫還原至先前的時間點。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: a4fc645f43858e80eeffc6c1672f6bd4369e7290
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536756"
---
# <a name="restore-an-azure-database-for-mariadb-server-using-azure-cli"></a>使用 Azure CLI 還原適用於 MariaDB 的 Azure 資料庫伺服器
此範例 CLI 指令碼會將單一「適用於 MariaDB 的 Azure 資料庫」伺服器還原至先前的時間點。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 2.0 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。 

## <a name="sample-script"></a>範例指令碼
在此範例指令碼中，請編輯醒目提示的命令列，將系統管理員使用者名稱和密碼更新為您自己的使用者名稱和密碼。 請以您自己的訂用帳戶識別碼取代 `az monitor` 命令中使用的訂用帳戶識別碼。
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/backup-restore-pitr/backup-restore.sh?highlight=15-16 "Restore Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>清除部署
執行指令碼之後，請使用下列命令來移除資源群組及其相關聯的所有資源。 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/backup-restore-pitr/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>指令碼說明
此指令碼會使用下表中簡述的命令：

| **命令** | **注意事項** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | 建立託管資料庫的 MariaDB 伺服器。 |
| [az mariadb server restore](/cli/azure/mariadb/server#az-mariadb-server-restore) | 從備份還原伺服器。 |
| [az group delete](/cli/azure/group#az-group-delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟
- 了解 Azure CLI 的詳細資訊：[Azure CLI 文件](/cli/azure)。
- 嘗試額外的指令碼：[「適用於 MariaDB 的 Azure 資料庫」的 Azure CLI 範例](../sample-scripts-azure-cli.md)
