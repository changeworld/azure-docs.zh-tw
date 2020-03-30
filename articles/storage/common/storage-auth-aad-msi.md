---
title: 使用託管標識授權訪問資料
titleSuffix: Azure Storage
description: 瞭解如何為 Azure 資源使用託管標識來授權訪問 Azure 虛擬機器、函數應用、虛擬機器縮放集和其他應用程式中的 Blob 和佇列資料。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f3bac0d47a53da1ec4d1fa08b5f0933f5f65dc56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255336"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>使用 Azure 資源的託管標識授權訪問 Blob 和佇列資料

Azure Blob 和佇列儲存體支援使用 [Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)來進行 Azure Active Directory (Azure AD) 驗證。 Azure 資源的託管標識可以使用 Azure 虛擬機器 （VM）、功能應用、虛擬機器縮放集和其他服務中運行的應用程式使用 Azure AD 憑據訪問 Blob 和佇列資料。 通過將 Azure 資源的託管標識與 Azure AD 身份驗證一起使用，可以避免將憑據存儲在雲中運行的應用程式。  

本文演示如何使用 Azure 資源的託管標識授權訪問 Azure VM 中的 Blob 或佇列資料。 它還介紹了如何在開發環境中測試代碼。

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上啟用受控識別

在可以使用 Azure Resources 的託管標識授權從 VM 訪問 Blob 和佇列之前，必須首先在 VM 上為 Azure 資源啟用託管標識。 若要了解如何啟用 Azure 資源的受控識別，請參閱下列其中一篇文章：

- [Azure 門戶](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure 電源外殼](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure 資源管理器範本](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 資源管理器用戶端庫](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

有關託管標識的詳細資訊，請參閱 Azure[資源的託管標識](../../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="authenticate-with-the-azure-identity-library"></a>使用 Azure 標識庫進行身份驗證

Azure 標識用戶端庫為[Azure SDK](https://github.com/Azure/azure-sdk)提供 Azure Ad 權杖身份驗證支援。 .NET、JAVA、Python 和 JavaScript 的 Azure 存儲用戶端庫的最新版本與 Azure 標識庫集成，以提供獲取 OAuth 2.0 權杖以授權 Azure 存儲請求的簡單安全方法。

Azure 標識用戶端庫的優點是，它使您能夠使用相同的代碼來驗證應用程式是在開發環境還是 Azure 中運行。 .NET 的 Azure 標識用戶端庫對安全主體進行身份驗證。 當代碼在 Azure 中運行時，安全主體是 Azure 資源的託管標識。 在開發環境中，託管標識不存在，因此用戶端庫對使用者或服務主體進行身份驗證以進行測試。

身份驗證後，Azure 標識用戶端庫將獲得權杖憑據。 然後，此權杖憑據封裝在您為執行針對 Azure 存儲的操作而創建的服務用戶端物件中。 庫通過獲取適當的權杖憑據來無縫地為您處理此問題。

有關 .NET 的 Azure 標識用戶端庫的詳細資訊，請參閱[.NET 的 Azure 標識用戶端庫](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)。 有關 Azure 標識用戶端庫的參考文檔，請參閱[Azure.標識命名空間](/dotnet/api/azure.identity)。

### <a name="assign-role-based-access-control-rbac-roles-for-access-to-data"></a>分配基於角色的存取控制 （RBAC） 角色以訪問資料

當 Azure AD 安全主體嘗試訪問 Blob 或佇列資料時，該安全主體必須具有對資源的許可權。 無論安全主體是 Azure 中的託管標識還是開發環境中運行代碼的 Azure AD 使用者帳戶，都必須為安全主體分配一個 RBAC 角色，該角色授予對 Azure 存儲中的 Blob 或佇列資料的訪問。 有關通過 RBAC 分配許可權的資訊，請參閱標題為 **"分配 RBAC 角色以使用** [Azure 活動目錄 訪問 Azure blob 和佇列](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)"中的存取權限的部分。

### <a name="authenticate-the-user-in-the-development-environment"></a>在開發環境中對使用者進行身份驗證

當您的代碼在開發環境中運行時，身份驗證可能會自動處理，或者可能需要瀏覽器登錄，具體取決於您使用的工具。 例如，Microsoft Visual Studio 支援單一登入 （SSO），以便活動 Azure AD 使用者帳戶自動用於身份驗證。 有關 SSO 的詳細資訊，請參閱[對應用程式的單一登入](../../active-directory/manage-apps/what-is-single-sign-on.md)。

其他開發工具可能會提示您通過 Web 瀏覽器登錄。

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>在開發環境中驗證服務主體

如果開發環境不支援單一登入或通過 Web 瀏覽器登錄，則可以使用服務主體從開發環境進行身份驗證。

#### <a name="create-the-service-principal"></a>建立服務主體

要使用 Azure CLI 創建服務主體並分配 RBAC 角色，請調用[az ad sp 創建 rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)命令。 提供 Azure 存儲資料訪問角色以分配給新的服務主體。 此外，提供角色指派的範圍。 有關為 Azure 存儲提供的內置角色的詳細資訊，請參閱 Azure[資源的內置角色](../../role-based-access-control/built-in-roles.md)。

如果您沒有足夠的許可權將角色指派給服務主體，則可能需要要求帳戶擁有者或管理員執行角色指派。

下面的示例使用 Azure CLI 創建新的服務主體，並將**存儲 Blob 資料讀取器**角色指派給具有帳戶範圍的服務主體

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

該`az ad sp create-for-rbac`命令返回 JSON 格式的服務主體屬性的清單。 複製這些值，以便可以使用它們在下一步中創建必要的環境變數。

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> RBAC 角色指派可能需要幾分鐘才能傳播。

#### <a name="set-environment-variables"></a>設定環境變數

Azure 標識用戶端庫在運行時從三個環境變數讀取值以對服務主體進行身份驗證。 下表描述了要為每個環境變數設置的值。

|環境變數|值
|-|-
|`AZURE_CLIENT_ID`|服務主體的應用 ID
|`AZURE_TENANT_ID`|服務主體的 Azure AD 租戶 ID
|`AZURE_CLIENT_SECRET`|為服務主體生成的密碼

> [!IMPORTANT]
> 設置環境變數後，關閉並重新打開主控台視窗。 如果使用 Visual Studio 或其他開發環境，則可能需要重新開機開發環境，以便註冊新的環境變數。

有關詳細資訊，請參閱[在門戶中為 Azure 應用創建標識](../../active-directory/develop/howto-create-service-principal-portal.md)。

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET 程式碼範例：建立區塊 Blob

將以下`using`指令添加到代碼中，以使用 Azure 標識和 Azure 存儲用戶端庫。

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

要獲取代碼可用於授權對 Azure 存儲的請求的權杖憑據，請創建[預設 Azure 憑據](/dotnet/api/azure.identity.defaultazurecredential)類的實例。 以下代碼示例演示如何獲取經過身份驗證的權杖憑據並使用它創建服務用戶端物件，然後使用服務用戶端上載新的 Blob：

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> 要使用 Azure AD 授權針對 Blob 或佇列資料的請求，必須對這些請求使用 HTTPS。

## <a name="next-steps"></a>後續步驟

- [使用 RBAC 管理存儲資料的存取權限](storage-auth-aad-rbac.md)。
- [將 Azure AD 與存儲應用程式一起](storage-auth-aad-app.md)使用 。
- [使用 Azure AD 憑據運行 Azure CLI 或 PowerShell 命令以訪問 Blob 或佇列資料](authorize-active-directory-powershell.md)。
