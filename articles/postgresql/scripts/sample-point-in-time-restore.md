---
title: Azure CLI 指令碼 - 還原適用於 PostgreSQL 的 Azure 資料庫伺服器
description: 此範例 Azure CLI 指令碼示範如何將「適用於 PostgreSQL 的 Azure 資料庫」伺服器及其資料庫還原至先前的時間點。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 7b02b81e650eabea6f3f5f09347dc4aa2382671a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496503"
---
# <a name="restore-an-azure-database-for-postgresql-server-using-azure-cli"></a>使用 Azure CLI 還原「適用於 PostgreSQL 的 Azure 資料庫」伺服器
此範例 CLI 指令碼會將單一「適用於 PostgreSQL 的 Azure 資料庫」伺服器還原至先前的時間點。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本執行 CLI，本文需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以檢查版本。 請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli) 以安裝或升級 Azure CLI 版本。

## <a name="sample-script"></a>範例指令碼
在此範例指令碼中，請編輯醒目提示的命令列，將系統管理員使用者名稱和密碼更新為您自己的使用者名稱和密碼。 請以您自己的訂用帳戶識別碼取代 `az monitor` 命令中使用的訂用帳戶識別碼。
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/backup-restore.sh?highlight=15-16 "Restore Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>清除部署
執行指令碼之後，請使用下列命令來移除資源群組及其相關聯的所有資源。 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>指令碼說明
此指令碼會使用下表中簡述的命令：

| **命令** | **注意事項** |
|---|---|
| [az group create](/cli/azure/group) | 建立用來存放所有資源的資源群組。 |
| [az postgresql server create](/cli/azure/postgres/server#az-postgres-server-create) | 建立主控資料庫的 PostgreSQL 伺服器。 |
| [az postgresql server restore](/cli/azure/postgres/server#az-postgres-server-restore) | 從備份還原伺服器。 |
| [az group delete](/cli/azure/group) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟
- 了解 Azure CLI 的詳細資訊：[Azure CLI 文件](/cli/azure)。
- 嘗試額外的指令碼：[Azure Database for PostgreSQL 的 Azuer CLI 範例](../sample-scripts-azure-cli.md)
- [如何使用 Azure 入口網站，在適用於 PostreSQL 的 Azure 資料庫中備份和還原伺服器](../howto-restore-server-portal.md)
