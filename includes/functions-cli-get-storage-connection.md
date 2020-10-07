---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673332"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>擷取 Azure 儲存體連接字串

您稍早已建立可供函式應用程式使用的 Azure 儲存體帳戶。 此帳戶的連接字串會安全地儲存在 Azure 的應用程式設定中。 藉由將設定下載到 *local.settings.json* 檔案中，您可以在本機執行函式時，使用該連線寫入至相同帳戶中的儲存體佇列。 

1. 從專案的根目錄執行下列命令 (請將 `<app_name>` 取代為先前快速入門中的函式應用程式名稱)。 此命令將會覆寫檔案中任何現有的值。

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. 開啟 *local.settings.json* 並找出名為 `AzureWebJobsStorage` 的值，也就是儲存體帳戶連接字串。 您會在本文的其他章節中使用名稱 `AzureWebJobsStorage` 和連接字串。

> [!IMPORTANT]
> 由於 *local.settings.json* 中包含從 Azure 下載的祕密，因此請一律將此檔案排除在原始檔控制以外。 使用本機函式專案建立的 *.gitignore* 檔案依預設會排除該檔案。