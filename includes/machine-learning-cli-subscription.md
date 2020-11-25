---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 6147dff71ff8c93c8c46b5921b37db459ea73e4a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025915"
---
> [!TIP]
> 登入之後，您會看到一份與 Azure 帳戶相關聯的訂用帳戶清單。 具有 `isDefault: true` 的訂用帳戶資訊是目前針對 Azure CLI 命令啟用的訂用帳戶。 此訂用帳戶必須是包含 Azure Machine Learning 工作區的相同訂用帳戶。 您可從 [Azure 入口網站](https://portal.azure.com)瀏覽工作區的概觀頁面來尋找訂用帳戶識別碼。 您也可以使用 SDK，從工作區物件取得訂用帳戶識別碼。 例如： `Workspace.from_config().subscription_id` 。
> 
> 若要選取另一個訂用帳戶，請使用 `az account set -s <subscription name or ID>` 命令並指定訂用帳戶名稱或識別碼以進行切換。 如需訂用帳戶選取的詳細資訊，請參閱[使用多個 Azure 訂用帳戶](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)。