---
title: Azure CLI 指令碼範例 - 訂閱 Azure 訂用帳戶 | Microsoft Docs
description: 本文提供範例 Azure CLI 指令碼，示範如何使用 Azure CLI 訂閱 Azure 事件方格事件。
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 9d40367c8d0f4f1250504c30fcd6c4846921a830
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76720889"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-azure-cli"></a>使用 Azure CLI 訂閱 Azure 訂用帳戶的事件

此指令碼可針對 Azure 訂用帳戶的事件建立 Event Grid 訂用帳戶。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

預覽範例指令碼需要事件方格延伸模組。 若要安裝，請執行 `az extension add --name eventgrid`。

## <a name="sample-script---stable"></a>範例指令碼 - Stable

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.sh "Subscribe to Azure subscription")]

## <a name="sample-script---preview-extension"></a>範例指令碼 - 預覽版延伸模組

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.sh "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立事件訂用帳戶。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | 建立事件格線訂用帳戶。 |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) - 延伸模組版本 | 建立事件格線訂用帳戶。 |

## <a name="next-steps"></a>後續步驟

* 如需查詢訂用帳戶的相關資訊，請參閱[查詢 Event Grid 訂用帳戶](../query-event-subscriptions.md)。
* 如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。
