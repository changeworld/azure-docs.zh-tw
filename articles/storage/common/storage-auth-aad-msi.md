---
title: 使用受控識別授權存取資料
titleSuffix: Azure Storage
description: 使用適用于 Azure 資源的受控識別，從在 Azure Vm、函式應用程式及其他專案中執行的應用程式，對 blob 和佇列資料的存取進行授權。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 4b37e2530d8716f48eae696fef8f856e8334e24b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713676"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>使用適用于 Azure 資源的受控識別來授權 blob 和佇列資料的存取

Azure Blob 和佇列儲存體支援使用 [Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)來進行 Azure Active Directory (Azure AD) 驗證。 適用于 Azure 資源的受控識別可以從 Azure 虛擬機器中執行的應用程式（ (Vm) 、函式應用程式、虛擬機器擴展集和其他服務），使用 Azure AD 認證來授權存取 blob 和佇列資料。 藉由使用適用于 Azure 資源的受控識別搭配 Azure AD authentication，您可以避免將認證儲存在雲端中執行的應用程式。  

本文說明如何使用適用于 Azure 資源的受控識別，來授權從 Azure VM 存取 blob 或佇列資料。 此外，也會說明如何在開發環境中測試您的程式碼。

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上啟用受控識別

您必須先在 VM 上啟用 Azure 資源的受控識別，才能使用 Azure 資源的受控識別來授權從您的 VM 存取 blob 和佇列。 若要了解如何啟用 Azure 資源的受控識別，請參閱下列其中一篇文章：

- [Azure 入口網站](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 範本](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 用戶端程式庫](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

如需受控識別的詳細資訊，請參閱 [適用于 Azure 資源的受控](../../active-directory/managed-identities-azure-resources/overview.md)識別。

## <a name="authenticate-with-the-azure-identity-library"></a>使用 Azure 身分識別程式庫進行驗證

Azure 身分識別用戶端程式庫提供 azure [SDK](https://github.com/Azure/azure-sdk)的 azure Azure AD 權杖驗證支援。 適用于 .NET、JAVA、Python 和 JavaScript 的 Azure 儲存體用戶端程式庫的最新版本會與 Azure 身分識別程式庫整合，以提供簡單且安全的方法來取得 OAuth 2.0 權杖，以授權 Azure 儲存體要求。

Azure 身分識別用戶端程式庫的優點是，它可讓您使用相同的程式碼來驗證您的應用程式是在開發環境中或在 Azure 中執行。 適用于 .NET 的 Azure 身分識別用戶端程式庫會驗證安全性主體。 當您的程式碼在 Azure 中執行時，安全性主體是適用于 Azure 資源的受控識別。 在開發環境中，受控識別並不存在，因此用戶端程式庫會驗證使用者或服務主體，以供測試之用。

驗證之後，Azure 身分識別用戶端程式庫會取得權杖認證。 然後，此權杖認證會封裝在您所建立的服務用戶端物件中，以針對 Azure 儲存體執行作業。 程式庫會取得適當的權杖認證，以順暢地處理這項工作。

如需適用于 .NET 的 Azure 身分識別用戶端程式庫的詳細資訊，請參閱 [適用于 .net 的 Azure identity client 程式庫](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)。 如需 Azure 身分識別用戶端程式庫的參考檔，請參閱 [azure 身分識別命名空間](/dotnet/api/azure.identity)。

### <a name="assign-azure-roles-for-access-to-data"></a>指派 Azure 角色以存取資料

當 Azure AD 安全性主體嘗試存取 blob 或佇列資料時，該安全性主體必須具有該資源的許可權。 無論安全性主體是 Azure 中的受控識別，或是在開發環境中執行程式碼 Azure AD 使用者帳戶，都必須將 Azure 角色指派給安全性主體，以授與 Azure 儲存體中 blob 或佇列資料的存取權。 如需透過 Azure RBAC 指派許可權的相關資訊，請參閱[使用 Azure Active Directory 來授與 azure blob 和佇列的存取](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights)權的「**指派 azure 角色的存取權**」一節。

### <a name="authenticate-the-user-in-the-development-environment"></a>在開發環境中驗證使用者

當您的程式碼在開發環境中執行時，可以自動處理驗證，也可能需要瀏覽器登入，視您使用的工具而定。 例如，Microsoft Visual Studio 支援 (SSO) 的單一登入，讓 active Azure AD 使用者帳戶自動用於驗證。 如需 SSO 的詳細資訊，請參閱 [應用程式的單一登入](../../active-directory/manage-apps/what-is-single-sign-on.md)。

其他開發工具可能會提示您透過網頁瀏覽器登入。

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>在開發環境中驗證服務主體

如果您的開發環境不支援單一登入或透過網頁瀏覽器登入，則您可以使用服務主體從開發環境進行驗證。

#### <a name="create-the-service-principal"></a>建立服務主體

若要使用 Azure CLI 建立服務主體並指派 Azure 角色，請呼叫 [az ad sp 建立-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) 命令。 提供要指派給新服務主體的 Azure 儲存體資料存取角色。 此外，請提供角色指派的範圍。 如需有關為 Azure 儲存體提供之內建角色的詳細資訊，請參閱 [Azure 內建角色](../../role-based-access-control/built-in-roles.md)。

如果您沒有足夠的許可權可將角色指派給服務主體，您可能需要要求帳戶擁有者或系統管理員執行角色指派。

下列範例會使用 Azure CLI 來建立新的服務主體，並使用帳戶範圍將 **儲存體 Blob 資料讀取器** 角色指派給它

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

此 `az ad sp create-for-rbac` 命令會以 JSON 格式傳回服務主體屬性的清單。 複製這些值，讓您可以在下一個步驟中使用它們來建立必要的環境變數。

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
> Azure 角色指派可能需要數分鐘的時間傳播。

#### <a name="set-environment-variables"></a>設定環境變數

Azure 身分識別用戶端程式庫會在執行時間從三個環境變數讀取值，以驗證服務主體。 下表說明針對每個環境變數所設定的值。

|環境變數|值
|-|-
|`AZURE_CLIENT_ID`|服務主體的應用程式識別碼
|`AZURE_TENANT_ID`|服務主體的 Azure AD 租使用者識別碼
|`AZURE_CLIENT_SECRET`|為服務主體產生的密碼

> [!IMPORTANT]
> 設定環境變數之後，請關閉並重新開啟主控台視窗。 如果您使用 Visual Studio 或其他開發環境，您可能需要重新開機開發環境，才能讓它註冊新的環境變數。

如需詳細資訊，請參閱 [在入口網站中建立 Azure 應用程式](../../active-directory/develop/howto-create-service-principal-portal.md)的身分識別。

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET 程式碼範例：建立區塊 Blob

將下列指示詞新增 `using` 至您的程式碼，以使用 Azure 身分識別和 Azure 儲存體用戶端程式庫。

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

若要取得您的程式碼可以用來授權 Azure 儲存體之要求的權杖認證，請建立 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 類別的實例。 下列程式碼範例顯示如何取得已驗證的權杖認證，並使用它來建立服務用戶端物件，然後使用服務用戶端上傳新的 blob：

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
> 若要使用 Azure AD 對 blob 或佇列資料的要求進行授權，您必須針對這些要求使用 HTTPS。

## <a name="next-steps"></a>後續步驟

- [使用 AZURE RBAC 管理儲存體資料的存取權限](storage-auth-aad-rbac.md)。
- 搭配[使用 Azure AD 與儲存體應用程式](storage-auth-aad-app.md)。
- [使用 Azure AD 認證來執行 PowerShell 命令以存取 blob 資料](../blobs/authorize-active-directory-powershell.md)
