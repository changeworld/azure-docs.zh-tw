---
title: CLI 指令碼 - 建立伺服器 - 適用於 MariaDB 的 Azure 資料庫
description: 此範例 CLI 指令碼會建立「適用於 MariaDB 的 Azure 資料庫」伺服器，並設定伺服器等級防火牆規則。
author: savjani
ms.author: pariks
ms.service: jroth
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 11/28/2018
ms.openlocfilehash: 8b414b5d483f4662bf8fdeb144a9cc8ef99d02dd
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664610"
---
# <a name="create-a-mariadb-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>使用 Azure CLI 建立 MariaDB 伺服器並設定防火牆規則
此範例 CLI 指令碼會建立「適用於 MariaDB 的 Azure 資料庫」伺服器，並設定伺服器等級防火牆規則。 成功執行指令碼後，即可從所有 Azure 服務和已設定的 IP 位址存取 MariaDB 伺服器。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 2.0 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="sample-script"></a>範例指令碼
在此範例指令碼中，請編輯醒目提示的命令列，將系統管理員使用者名稱和密碼更新為您自己的使用者名稱和密碼。
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/create-mariadb-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for mariadb, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>清除部署
執行指令碼之後，請使用下列命令來移除資源群組及其相關聯的所有資源。
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/delete-mariadb.sh "Delete the resource group.")]

## <a name="script-explanation"></a>指令碼說明
此指令碼會使用下表中簡述的命令：

| **命令** | **注意事項** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | 建立託管資料庫的 MariaDB 伺服器。 |
| [az mariadb server firewall create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) | 建立防火牆規則以允許從輸入的 IP 位址範圍存取伺服器及其之下的資料庫。 |
| [az group delete](/cli/azure/group#az-group-delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟
- 了解 Azure CLI 的詳細資訊：[Azure CLI 文件](/cli/azure)。
- 嘗試額外的指令碼：[「適用於 MariaDB 的 Azure 資料庫」的 Azure CLI 範例](../sample-scripts-azure-cli.md)
