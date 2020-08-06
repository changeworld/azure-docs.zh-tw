---
title: Azure CLI 指令碼範例 - 訂閱 Blob 儲存體帳戶 | Microsoft Docs
description: 本文提供範例 Azure CLI 指令碼，示範如何訂閱 Azure Blob 儲存體帳戶的事件。
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: b7648a8ef71b151c94fac4fa28cfa8eafa239c68
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494140"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-azure-cli"></a>使用 Azure CLI 訂閱 Blob 儲存體帳戶的事件

此指令碼可針對 Blob 儲存體帳戶的事件建立 Event Grid 訂用帳戶。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.sh "Subscribe to Blob storage")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立事件訂用帳戶。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | 建立事件格線訂用帳戶。 |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) - 延伸模組版本 | 建立事件格線訂用帳戶。 |

## <a name="next-steps"></a>後續步驟

* 如需查詢訂用帳戶的相關資訊，請參閱[查詢 Event Grid 訂用帳戶](../query-event-subscriptions.md)。
* 如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。
