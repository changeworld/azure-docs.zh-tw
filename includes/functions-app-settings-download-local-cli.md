---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "72329554"
---
您已經在 Azure 中建立函數應用程式以及必要的儲存體帳戶。 此帳戶的連接字串會安全地儲存在 Azure 的應用程式設定中。 在本文中，您會將訊息寫入至相同帳戶中的儲存體佇列。 在本機執行函式時若要連線至儲存體帳戶，您必須將應用程式設定下載到 local.settings.json 檔案。 

從專案的根目錄，執行下列 Azure Functions Core Tools 命令以將設定下載至 local.settings.json，並將 `<APP_NAME>` 取代為上一篇文章中的函式應用程式名稱：

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

您可能需要登入您的 Azure 帳戶。

> [!IMPORTANT]  
> 此命令會以 Azure 中函式應用程式的值覆寫任何現有的設定。  
>
> local.settings.json 檔案中包含秘密，因此絕不可發行，且應從原始檔控制中排除。  
> 

您需要值 `AzureWebJobsStorage`，這是儲存體帳戶的連接字串。 您將使用此連線來確認輸出繫結會如預期般運作。
