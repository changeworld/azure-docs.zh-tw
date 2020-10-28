---
author: baanders
description: 包含用於清除基本 Azure Digital Twins 執行個體的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 29a07ffa917153c0cb062d34e2807d43d039a373
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494642"
---
當您不再需要於本教學課程中建立的資源時，請遵循這些步驟加以刪除。

使用 [Azure Cloud Shell](https://shell.azure.com)，您可以使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) 命令刪除資源群組中的所有 Azure 資源。 這會移除資源群組及 Azure Digital Twins 執行個體。

> [!IMPORTANT]
> 刪除資源群組是無法回復的動作。 資源群組和其中包含的所有資源都將永久刪除。 請確定您不會不小心刪除錯誤的資源群組或資源。 

開啟 Azure Cloud Shell 並執行以下命令，以刪除資源群組及其包含的所有內容。

```azurecli-interactive
az group delete --name <your-resource-group>
```