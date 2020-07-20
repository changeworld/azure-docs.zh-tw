---
title: 指令碼範例 - 停用檔案共用的虛刪除
description: 了解如何使用指令碼以針對儲存體帳戶中的檔案共用停用虛刪除。
ms.topic: sample
ms.date: 02/02/2020
ms.openlocfilehash: 172a7bd19ffd07ca7900a457c447362343e5f185
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84121280"
---
# <a name="disable-soft-delete-for-file-shares-in-a-storage-account"></a>針對儲存體帳戶中的檔案共用停用虛刪除

此文件說明針對儲存體帳戶中的檔案共用停用虛刪除的程序。

請遵循下列步驟：

1. 安裝 armclient。 若要了解如何加以安裝，請瀏覽[此連結](https://github.com/projectkudu/ARMClient)。

2. 將下列兩個要求本文檔案儲存至您電腦上的資料夾。

    ```json
    rqbody-enableSoftDelete.json

    {
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled":true,
        "days": 14
        }
    },
    "cors": {
        "corsRules": []
    }
    }

    rqbody-disableSoftDelete.json

    {
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled":false,
        "days": 0
        }
    },
    "cors": {
        "corsRules": []
    }
    }
    ```

3. 讓您的儲存體帳戶 Azure Resource Manager (ARM) 識別碼保存在隨手可及的位置。 例如： `/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare/providers/Microsoft.Storage/storageAccounts/inquirytest`

4. 透過執行 **armclient login**，使用您的認證來登入。

5. 在儲存體帳戶中，取得目前的檔案共用虛刪除屬性。

    下列 GET 作業會在 *inquirytest* 帳戶中，擷取檔案共用的虛刪除屬性：

    ```cmd
    armclient get /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01
    ```

    ```output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/de
    fault",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "cors": {
        "corsRules": []
        },
        "shareDeleteRetentionPolicy": {
        "enabled": true,
        "days": 14
        }
    }
    }
    ```

6. 針對儲存體帳戶中的檔案共用停用虛刪除。

    下列 PUT 作業會在 *inquirytest* 帳戶中，停用檔案共用的虛刪除屬性：

    ```cmd
    armclient put /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01 .\rqbody-disableSoftDelete.json
    ```

    ```Output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/de
    fault",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled": false,
        "days": 0
        }
    }
    }
    ```

7. 如果您想要重新啟用虛刪除，請使用下列範例。

    下列 PUT 作業會在 "inquirytest " 帳戶中，啟用檔案共用的虛刪除屬性。

    ```cmd
    armclient put /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01 .\rqbody-EnableSoftDelete.json
    ```

    ```Output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled": true,
        "days": 14
        }
    }
    }
    ```
