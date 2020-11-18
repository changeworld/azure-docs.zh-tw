---
title: CLI 指令碼 - 調整伺服器 - 適用於 MySQL 的 Azure 資料庫
description: 此範例 CLI 指令碼會在查詢計量之後，將「適用於 MySQL 的 Azure 資料庫」伺服器調整為不同的效能等級。
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 31cf6d1f3bfcc44887ec939cb2f27df77f12cea3
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539255"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>使用 Azure CLI 監視和調整適用於 MySQL 的 Azure 資料庫伺服器
此範例 CLI 指令碼會在查詢計量之後，對單一「適用於 MySQL 的 Azure 資料庫」伺服器調整計算和儲存。 計算可以擴大或縮小。 儲存體只能擴大。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 2.0 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。 

## <a name="sample-script"></a>範例指令碼
使用您的訂用帳戶識別碼來更新指令碼。
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>清除部署
執行指令碼之後，請使用下列命令來移除資源群組及其相關聯的所有資源。 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>指令碼說明
此指令碼會使用下表中簡述的命令：

| **命令** | **注意事項** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | 建立主控資料庫的 MySQL 伺服器。 |
| [az mysql server update](/cli/azure/mysql/server#az-mysql-server-update) | 更新 MySQL 伺服器的屬性。 |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | 列出資源的度量值。 |
| [az group delete](/cli/azure/group#az-group-delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟
- 深入了解[適用於 MySQL 的 Azure 資料庫的計算和儲存](../concepts-pricing-tiers.md)
- 嘗試額外的指令碼：[Azure Database for MySQL 的 Azuer CLI 範例](../sample-scripts-azure-cli.md)
- 深入了解 [Azure CLI](/cli/azure)
