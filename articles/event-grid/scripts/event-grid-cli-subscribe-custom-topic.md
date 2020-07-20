---
title: Azure CLI 指令碼範例 - 訂閱自訂主題 | Microsoft Docs
description: 本文提供範例 Azure CLI 指令碼，示範如何訂閱自訂主題的事件方格事件。
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: 4a3cfe132da280d6b33ddfa6c396fd651bdbd7d5
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171256"
---
# <a name="subscribe-to-events-for-a-custom-topic-with-azure-cli"></a>使用 Azure CLI 訂閱自訂主題的事件

此指令碼可針對自訂主題的事件建立 Event Grid 訂用帳戶。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

預覽範例指令碼需要事件方格延伸模組。 若要安裝，請執行 `az extension add --name eventgrid`。

## <a name="sample-script---stable"></a>範例指令碼 - Stable

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-custom-topic/subscribe-to-custom-topic.sh "Subscribe to custom topic")]

## <a name="sample-script---preview-extension"></a>範例指令碼 - 預覽版延伸模組

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-custom-topic-preview/subscribe-to-custom-topic-preview.sh "Subscribe to custom topic")]


## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立事件訂用帳戶。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | 建立事件格線訂用帳戶。 |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) - 延伸模組版本 | 建立事件格線訂用帳戶。 |

## <a name="next-steps"></a>後續步驟

* 如需查詢訂用帳戶的相關資訊，請參閱[查詢 Event Grid 訂用帳戶](../query-event-subscriptions.md)。
* 如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。
