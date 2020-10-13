---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: affd55d65ed8454ebfcdf14f697849badf8e5d3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88657041"
---
<!-- ### Upload files with the CLI -->

下列命令會上傳單一檔案。  

變更下列值：

1. 將 `/path/to/file` 變更為檔案的本機路徑。  
1. 將 `MyContainer` 變更為您先前使用資產識別碼 (而非名稱) 所建立的資產。
1. 將 `MyBlob` 變更為您想要用於所上傳檔案的名稱。
1. 將 `MyStorageAccountName` 變更為您所使用的儲存體帳戶名稱。
1. 將 `MyStorageAccountKey` 變更為儲存體帳戶的存取金鑰。

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob --acount-name MyStorageAccountName --account-key MyStorageAccountKey
    ```
