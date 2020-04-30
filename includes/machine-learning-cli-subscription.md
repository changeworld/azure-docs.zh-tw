---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81616826"
---
> [!TIP]
> 登入之後，您會看到一份與 Azure 帳戶相關聯的訂用帳戶清單。 的訂用帳戶`isDefault: true`資訊是目前為 Azure CLI 命令啟動的訂用帳戶。 此訂用帳戶必須與包含您 Azure Machine Learning 工作區的訂用帳戶相同。 您可以造訪工作區的 [總覽] 頁面，以從[Azure 入口網站](https://portal.azure.com)找到訂用帳戶識別碼。 您也可以使用 SDK，從工作區物件取得訂用帳戶識別碼。 例如： `Workspace.from_config().subscription_id` 。
> 
> 若要選取其他訂用帳戶`az account set -s <subscription name or ID>` ，請使用命令，並指定要切換的訂用帳戶名稱或識別碼。 如需訂閱選擇的詳細資訊，請參閱[使用多個 Azure 訂用](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)帳戶。