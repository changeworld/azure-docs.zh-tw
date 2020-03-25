---
title: CLI 指令碼 - 還原伺服器 - 適用於 MySQL 的 Azure 資料庫
description: 此範例 Azure CLI 指令碼示範如何將「適用於 MySQL 的 Azure 資料庫」伺服器及其資料庫還原至先前的時間點。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: 5a84a44a4ea941bf3b77f0762fc764ec069919c7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74771323"
---
# <a name="restore-an-azure-database-for-mysql-server-using-azure-cli"></a>使用 Azure CLI 還原「適用於 MySQL 的 Azure 資料庫」伺服器
此範例 CLI 指令碼會將單一「適用於 MySQL 的 Azure 資料庫」伺服器還原至先前的時間點。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本執行 CLI，本文需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以檢查版本。 請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli) 以安裝或升級 Azure CLI 版本。 

## <a name="sample-script"></a>範例指令碼
在此範例指令碼中，請編輯醒目提示的命令列，將系統管理員使用者名稱和密碼更新為您自己的使用者名稱和密碼。 請以您自己的訂用帳戶識別碼取代 `az monitor` 命令中使用的訂用帳戶識別碼。
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/backup-restore.sh?highlight=15-16 "Restore Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>清除部署
執行指令碼之後，請使用下列命令來移除資源群組及其相關聯的所有資源。 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>指令碼說明
此指令碼會使用下表中簡述的命令：

| **命令** | **注意** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | 建立主控資料庫的 MySQL 伺服器。 |
| [az mysql server restore](/cli/azure/mysql/server#az-mysql-server-restore) | 從備份還原伺服器。 |
| [az group delete](/cli/azure/group#az-group-delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟
- 了解 Azure CLI 的詳細資訊：[Azure CLI 文件](/cli/azure)。
- 嘗試其他指令碼：[「適用於 MySQL 的 Azure 資料庫」的 Azure CLI 範例](../sample-scripts-azure-cli.md)
