---
title: 使用 .NET 定義預存存取原則-Azure 儲存體
description: 瞭解如何使用 .NET 用戶端程式庫定義預存存取原則。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "68990737"
---
# <a name="define-a-stored-access-policy-with-net"></a>使用 .NET 定義預存存取原則

儲存的存取原則可對伺服器端上的服務層級共用存取簽章（SAS）提供額外層級的控制。 定義儲存的存取原則可將共用存取簽章分組，並針對原則所系結的共用存取簽章提供額外的限制。 您可以使用預存存取原則來變更 SAS 的開始時間、到期時間或許可權，或在發行之後將其撤銷。
  
 下列儲存體資源支援儲存的存取原則：  
  
- Blob 容器  
- 檔案共用  
- 佇列  
- 資料表  
  
> [!NOTE]
> 容器上的預存存取原則可以與共享存取簽章相關聯，該簽章會授與容器本身或其包含之 blob 的許可權。 同樣地，檔案共用上的預存存取原則可以與共享存取簽章相關聯，授與共享本身或其內含檔案的許可權。  
>
> 只有服務 SAS 支援儲存的存取原則。 帳戶 SAS 或使用者委派 SAS 不支援儲存的存取原則。  

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

- [使用共用存取簽章（SAS）授與 Azure 儲存體資源的有限存取權](storage-sas-overview.md)
- [定義預存的存取原則](/rest/api/storageservices/define-stored-access-policy)

