---
title: 使用 .NET - Azure 存儲定義存儲的訪問策略
description: 瞭解如何使用 .NET 用戶端庫定義存儲的訪問策略。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68990737"
---
# <a name="define-a-stored-access-policy-with-net"></a>使用 .NET 定義存儲的訪問策略

存儲的訪問策略為伺服器端的服務等級共用訪問簽名 （SAS） 提供了額外的控制級別。 定義存儲的訪問策略用於對共用訪問簽名進行分組，並為受該策略約束的共用訪問簽名提供其他限制。 您可以使用存儲的訪問策略更改 SAS 的開始時間、到期時間或許可權，或在頒發 SAS 後撤消它。
  
 下列儲存體資源支援儲存的存取原則：  
  
- Blob 容器  
- 檔案共用  
- 佇列  
- 資料表  
  
> [!NOTE]
> 容器上的存儲訪問策略可以與共享訪問簽名相關聯，該簽名授予容器本身或其包含的 Blob 的許可權。 同樣，檔共用上的存儲訪問策略可以與共享訪問簽名相關聯，該簽名授予共用本身或其包含的檔的許可權。  
>
> 僅支援服務 SAS 的存儲訪問策略。 帳戶 SAS 或使用者委派 SAS 不支援存儲的訪問策略。  

## <a name="create-a-stored-access-policy"></a>建立預存的存取原則

下列程式碼會在容器上建立預存的存取原則。 您可以使用存取原則，對於容器上的服務 SAS 或其 Blob 指定條件約束。

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

## <a name="see-also"></a>另請參閱

- [使用共用訪問簽名 （SAS） 授予對 Azure 存儲資源的有限存取權限](storage-sas-overview.md)
- [定義預存的存取原則](/rest/api/storageservices/define-stored-access-policy)

