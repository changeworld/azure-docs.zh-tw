---
title: Azure CLI - 訂閱資源群組和依資源進行篩選
description: 本文提供範例 Azure CLl 指令碼，示範如何訂閱資源的事件方格事件以及對資源進行篩選。
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: 11935866f37dfaae15f25dac6deeace4e89547fc
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171290"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-azure-cli"></a>使用 Azure CLI 訂閱資源群組的事件和篩選資源

此指令碼可針對資源群組的事件建立 Event Grid 訂用帳戶。 它會使用篩選條件以便只取得資源群組中指定資源的事件。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

預覽範例指令碼需要事件方格延伸模組。 若要安裝，請執行 `az extension add --name eventgrid`。

## <a name="sample-script---stable"></a>範例指令碼 - Stable

[!code-azurecli[main](../../../cli_scripts/event-grid/filter-events/filter-events.sh "Subscribe to Azure subscription")]

## <a name="sample-script---preview-extension"></a>範例指令碼 - 預覽版延伸模組

[!code-azurecli[main](../../../cli_scripts/event-grid/filter-events-preview/filter-events-preview.sh "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立事件訂用帳戶。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | 建立事件格線訂用帳戶。 |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) - 延伸模組版本 | 建立事件格線訂用帳戶。 |

## <a name="next-steps"></a>後續步驟

* 如需查詢訂用帳戶的相關資訊，請參閱[查詢 Event Grid 訂用帳戶](../query-event-subscriptions.md)。
* 如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。
