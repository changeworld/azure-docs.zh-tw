---
title: 使用 .NET 建立預存存取原則
titleSuffix: Azure Storage
description: 使用 Azure 儲存體和 .NET 來建立預存的存取原則。 針對伺服器上的服務層級共用存取簽章，執行其他層級的控制。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: c9c267097f681a78ba940283096b2c18d692e9cc
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185361"
---
# <a name="create-a-stored-access-policy-with-net"></a>使用 .NET 建立預存存取原則

儲存的存取原則可讓您在伺服器端 (SAS) ，對服務層級的共用存取簽章提供額外的控制層級。 定義儲存的存取原則可將共用存取簽章分組，並針對原則所系結的共用存取簽章提供額外的限制。 您可以使用預存存取原則來變更 SAS 的開始時間、到期時間或許可權，或在發行之後將其撤銷。
  
下列 Azure 儲存體資源支援儲存的存取原則：  
  
- Blob 容器  
- 檔案共用  
- 佇列  
- 資料表  
  
> [!NOTE]
> 容器上的預存存取原則可以與共享存取簽章相關聯，該簽章會授與容器本身或其包含之 blob 的許可權。 同樣地，檔案共用上的預存存取原則可以與共享存取簽章相關聯，授與共享本身或其內含檔案的許可權。  
>
> 只有服務 SAS 支援儲存的存取原則。 帳戶 SAS 或使用者委派 SAS 不支援儲存的存取原則。  

如需預存存取原則的詳細資訊，請參閱[定義預存存取原則](/rest/api/storageservices/define-stored-access-policy)。

## <a name="create-a-stored-access-policy"></a>建立預存的存取原則

建立預存存取原則的基礎 REST 作業是[設定容器 ACL](/rest/api/storageservices/set-container-acl)。 您必須使用連接字串中的帳戶存取金鑰，透過共用金鑰授權作業來建立預存存取原則。 不支援使用 Azure AD 認證授權**設定容器 ACL**操作。 如需詳細資訊，請參閱[呼叫 blob 和佇列資料作業的許可權](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。

下列程式碼範例會在容器上建立預存存取原則。 您可以使用存取原則，對於容器上的服務 SAS 或其 Blob 指定條件約束。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

若要在 .NET 用戶端程式庫12版的容器上建立預存存取原則，以進行 Azure 儲存體，請呼叫下列其中一個方法：

- [BlobContainerClient.SetAccessPolicy](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy)
- [BlobContainerClient.SetAccessPolicyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicyasync)

下列範例會建立一天生效的預存存取原則，並授與讀取/寫入權限：

```csharp
async static Task CreateStoredAccessPolicyAsync(string containerName)
{
    string connectionString = "";

    // Use the connection string to authorize the operation to create the access policy.
    // Azure AD does not support the Set Container ACL operation that creates the policy.
    BlobContainerClient containerClient = new BlobContainerClient(connectionString, containerName);

    try
    {
        await containerClient.CreateIfNotExistsAsync();

        // Create one or more stored access policies.
        List<BlobSignedIdentifier> signedIdentifiers = new List<BlobSignedIdentifier>
        {
            new BlobSignedIdentifier
            {
                Id = "mysignedidentifier",
                AccessPolicy = new BlobAccessPolicy
                {
                    StartsOn = DateTimeOffset.UtcNow.AddHours(-1),
                    ExpiresOn = DateTimeOffset.UtcNow.AddDays(1),
                    Permissions = "rw"
                }
            }
        };
        // Set the container's access policy.
        await containerClient.SetAccessPolicyAsync(permissions: signedIdentifiers);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.ErrorCode);
        Console.WriteLine(e.Message);
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

若要在 .NET 用戶端程式庫12版的容器上建立預存存取原則，以進行 Azure 儲存體，請呼叫下列其中一個方法：

- [CloudBlobContainer. SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [CloudBlobContainer. SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

下列範例會建立一天生效的預存存取原則，並授與讀取、寫入和列出許可權：

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
            SharedAccessBlobPermissions.Write
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

---

## <a name="see-also"></a>請參閱

- [使用 (SAS 的共用存取簽章，授與 Azure 儲存體資源的有限存取權) ](storage-sas-overview.md)
- [定義預存的存取原則](/rest/api/storageservices/define-stored-access-policy)
- [設定 Azure 儲存體連接字串](storage-configure-connection-string.md)
