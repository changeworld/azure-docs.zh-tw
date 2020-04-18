---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616826"
---
> [!TIP]
> 登入之後，您會看到一份與 Azure 帳戶相關聯的訂用帳戶清單。 使用的`isDefault: true`訂閱資訊是當前啟動的 Azure CLI 命令的訂閱。 此訂閱必須與包含 Azure 機器學習工作區的訂閱相同。 您可以通過存取工作區的概述頁從[Azure 門戶](https://portal.azure.com)找到訂閱 ID。 您還可以使用 SDK 從工作區物件獲取訂閱 ID。 例如： `Workspace.from_config().subscription_id` 。
> 
> 要選擇其他訂閱,請使用`az account set -s <subscription name or ID>`命令並指定要切換到的訂閱名稱或 ID。 有關訂閱選擇的詳細資訊,請參閱[使用多個 Azure 訂閱](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)。