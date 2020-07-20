---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0fba49ec5a610d03ece2dce7fa5bb0d9d094ab4c
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203119"
---
## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#az-group-create) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源 (如函式應用程式、資料庫和儲存體帳戶) 的邏輯容器。

下列範例會建立名為 `myResourceGroup` 的資源群組。  
如果您未使用 Cloud Shell，請先使用 `az login` 登入。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

您通常會在附近的[區域](https://azure.microsoft.com/global-infrastructure/regions/)中建立資源群組和資源。 
