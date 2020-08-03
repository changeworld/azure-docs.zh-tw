---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6dfc96df3623e44a4ba513c238f21482c2a2989d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87013083"
---
應用程式中的 DefaultAzureCredential 方法依賴三個環境變數：、 和 。 使用 `export VARNAME=VALUE` 格式，將這些變數設定為在「建立服務主體」步驟中傳回的 clientId、clientSecret 和 tenantId 值。 (此方法只會設定您目前殼層的變數，以及從殼層建立的處理序；若要將這些變數永久新增至您的環境，請編輯您的 `/etc/environment ` 檔案)。 

您也需要將金鑰保存庫名稱儲存為稱為 `KEY_VAULT_NAME` 的環境變數。

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````
