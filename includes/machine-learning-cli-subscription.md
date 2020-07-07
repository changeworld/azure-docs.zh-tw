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
> 登入之後，您會看到一份與 Azure 帳戶相關聯的訂用帳戶清單。 具有 `isDefault: true` 的訂用帳戶資訊是目前針對 Azure CLI 命令啟用的訂用帳戶。 此訂用帳戶必須是包含 Azure Machine Learning 工作區的相同訂用帳戶。 您可從 [Azure 入口網站](https://portal.azure.com)瀏覽工作區的概觀頁面來尋找訂用帳戶識別碼。 您也可以使用 SDK，從工作區物件取得訂用帳戶識別碼。 例如： `Workspace.from_config().subscription_id` 。
> 
> 若要選取另一個訂用帳戶，請使用 `az account set -s <subscription name or ID>` 命令並指定訂用帳戶名稱或識別碼以進行切換。 如需訂用帳戶選取的詳細資訊，請參閱[使用多個 Azure 訂用帳戶](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)。