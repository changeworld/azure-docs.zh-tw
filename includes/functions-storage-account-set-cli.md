---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "78262524"
---
### <a name="set-the-storage-account-connection"></a>設定儲存體帳戶連線

開啟 local.settings.json 檔案並複製 `AzureWebJobsStorage` 的值，這是儲存體帳戶的連接字串。 請使用下列 Bash 命令，將 `AZURE_STORAGE_CONNECTION_STRING` 環境變數設為此連接字串：

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

在 `AZURE_STORAGE_CONNECTION_STRING` 環境變數中設定連接字串時，您將可直接存取您的儲存體帳戶，而不需要每次都提供驗證。