---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: df4735699f71256598bfd418a505ae8d3e5b816c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296891"
---
登入之後，您會看到一份與 Azure 帳戶相關聯的訂用帳戶清單。 使用的`isDefault: true`訂閱資訊是當前啟動的 Azure CLI 命令的訂閱。 此訂閱必須與包含 Azure 機器學習工作區的訂閱相同。 您可以通過訪問工作區的概述頁從[Azure 門戶](https://portal.azure.com)找到訂閱 ID。 您還可以使用 SDK 從工作區物件獲取訂閱 ID。 例如： `Workspace.from_config().subscription_id` 。
    
若要選取另一個訂用帳戶，請使用 [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) 命令搭配訂用帳戶識別碼以進行切換。 有關訂閱選擇的詳細資訊，請參閱[使用多個 Azure 訂閱](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)。