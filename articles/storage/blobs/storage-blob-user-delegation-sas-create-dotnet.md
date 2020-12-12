---
title: 使用 .NET 建立容器或 blob 的使用者委派 SAS
titleSuffix: Azure Storage
description: 瞭解如何使用適用于 Azure 儲存體的 .NET 用戶端程式庫，建立具有 Azure Active Directory 認證的使用者委派 SAS。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 8667fc3c5224b1f75a9beeb95b6e1261c768c14d
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347909"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>使用 .NET 建立容器或 blob 的使用者委派 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文說明如何使用 Azure Active Directory (Azure AD) 認證，以使用適用于 .NET 版本12的 Azure 儲存體用戶端程式庫建立容器或 blob 的使用者委派 SAS。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-azure-roles-for-access-to-data"></a>指派 Azure 角色以存取資料

當 Azure AD 安全性主體嘗試存取 blob 資料時，該安全性主體必須具有該資源的許可權。 無論安全性主體是 Azure 中的受控識別，或是在開發環境中執行程式碼的 Azure AD 使用者帳戶，都必須將 Azure 角色指派給安全性主體，以授與 Azure 儲存體中 blob 資料的存取權。 如需透過 Azure RBAC 指派許可權的相關資訊，請參閱 [使用 Azure Active Directory 來授與 azure blob 和佇列的存取](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights)權的「**指派 azure 角色的存取權**」一節。

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

若要深入瞭解如何向 Azure 儲存體驗證 Azure 身分識別用戶端程式庫，請參閱標題為使用 **Azure 身分識別程式庫進行驗證** 的一節， [其中包含 azure 資源的 Azure Active Directory 和受控識別授權存取 blob 和佇列](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)。

## <a name="get-an-authenticated-token-credential"></a>取得已驗證的權杖認證

若要取得您的程式碼可以用來授權 Azure 儲存體之要求的權杖認證，請建立 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 類別的實例。

下列程式碼片段說明如何取得已驗證的權杖認證，並使用它來建立 Blob 儲存體的服務用戶端：

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>取得使用者委派金鑰

每個 SAS 都會以金鑰簽署。 若要建立使用者委派 SAS，您必須先要求使用者委派金鑰，然後使用該金鑰來簽署 SAS。 使用者委派金鑰類似于用來簽署服務 SAS 或帳戶 SAS 的帳戶金鑰，不同之處在于它依賴您的 Azure AD 認證。 當用戶端使用 OAuth 2.0 權杖要求使用者委派金鑰時，Azure 儲存體會代表使用者傳回使用者委派金鑰。

擁有使用者委派金鑰之後，您就可以使用該金鑰，在金鑰存留期內建立任意數量的使用者委派共用存取簽章。 使用者委派金鑰與用來取得它的 OAuth 2.0 權杖無關，因此只要金鑰仍有效，就不需要更新權杖。 您可以指定金鑰有效期限最多7天。

您可以使用下列其中一種方法來要求使用者委派金鑰：

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

下列程式碼片段會取得使用者委派金鑰，並寫出其屬性：

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="get-a-user-delegation-sas-for-a-blob"></a>取得 blob 的使用者委派 SAS

下列程式碼範例顯示驗證安全性主體以及為 blob 建立使用者委派 SAS 的完整程式碼：

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasBlob":::

下列範例會測試從模擬用戶端應用程式在上一個範例中建立的使用者委派 SAS。 如果 SAS 有效，用戶端應用程式可以讀取 blob 的內容。 如果 SAS 無效，例如，如果 SAS 已過期，Azure 儲存體會傳回錯誤碼 403 (禁止的) 。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ReadBlobWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-container"></a>取得容器的使用者委派 SAS

下列程式碼範例示範如何產生容器的使用者委派 SAS：

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasContainer":::

下列範例會測試從模擬用戶端應用程式在上一個範例中建立的使用者委派 SAS。 如果 SAS 有效，用戶端應用程式可以讀取 blob 的內容。 如果 SAS 無效，例如，如果 SAS 已過期，Azure 儲存體會傳回錯誤碼 403 (禁止的) 。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListBlobsWithSasAsync":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>請參閱

- [使用 (SAS) 的共用存取簽章來授與 Azure 儲存體資源的有限存取權 ](../common/storage-sas-overview.md)
- [取得使用者委派金鑰操作](/rest/api/storageservices/get-user-delegation-key)
- [建立使用者委派 SAS (REST API) ](/rest/api/storageservices/create-user-delegation-sas)
