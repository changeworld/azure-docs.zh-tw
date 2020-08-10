---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: efa380ac243338a91354f948fdc77b2da8dd5406
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2020
ms.locfileid: "87512789"
---
讓我們建立一個名為  的祕密，其值為 **Success!** 。 祕密可以是密碼、SQL 連接字串，也可以是任何您必須安全保存並可供應用程式使用的其他資訊。 

請使用 Azure CLI [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) 命令將祕密新增至新建立的金鑰保存庫：

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```