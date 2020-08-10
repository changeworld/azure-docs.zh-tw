---
title: 連線現有 Azure Cosmos 帳戶與虛擬網路服務端點
description: 連線現有 Azure Cosmos 帳戶與虛擬網路服務端點
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 4fd434fcfcc560a69b1e223730021aa4dd32c334
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87432248"
---
# <a name="connect-an-existing-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>使用 Azure CLI 連線現有 Azure Cosmos 帳戶與虛擬網路服務端點

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

若選擇在本機安裝及使用 CLI，此主題需要您執行 Azure CLI 2.9.1 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

此範例旨在示範如何使用 `ignore-missing-vnet-service-endpoint` 參數，將現有的 Azure Cosmos 帳戶連線到現有新的虛擬網路，其中尚未針對服務端點設定子網路。 這可在完成虛擬網路的子網路設定之前，讓 Cosmos 帳戶的設定完成，而不會發生錯誤。 子網路設定完成後，便可透過已設定的子網路存取 Cosmos 帳戶。

> [!NOTE]
> 此範例會示範如何使用 SQL (核心) API 帳戶。 若要將此範例使用於其他 API，請將下面指令碼中的 `enable-virtual-network` 和 `virtual-network-rules` 參數套用至您的 API 專屬指令碼。

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints-ignore-missing-vnet.sh "Create an Azure Cosmos account with service endpoints.")]

## <a name="clean-up-deployment"></a>清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | 建立 Azure 虛擬網路。 |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | 建立 Azure 虛擬網路的子網路。 |
| [az network vnet subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show) | 傳回 Azure 虛擬網路的子網路。 |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | 建立 Azure Cosmos DB 帳戶。 |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) | 更新 Azure 虛擬網路的子網路。 |
| [az group delete](/cli/azure/resource#az-resource-delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure Cosmos DB CLI 的詳細資訊，請參閱 [Azure Cosmos DB CLI 文件](/cli/azure/cosmosdb)。

您可以在 [Azure Cosmos DB CLI GitHub 存放庫](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)中找到所有 Azure Cosmos DB CLI 指令碼範例。
