---
title: 以遞迴方式設定 Azure Data Lake Storage Gen2 的 Acl |Microsoft Docs
description: 您可以針對父目錄的現有子專案，以遞迴方式新增、更新和移除存取控制清單。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 08/26/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 53ff86f7dc5dfd6b7b60f99848c48f4e44f5a8a1
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948839"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a> (Acl) 遞迴方式設定存取控制清單 Azure Data Lake Storage Gen2

在上層目錄下建立的新子專案已可使用 ACL 繼承。 您現在也可以針對父目錄的現有子專案，以遞迴方式新增、更新和移除 Acl，而不需要為每個子專案個別進行這些變更。

> [!NOTE]
> 以遞迴方式設定存取清單的功能處於公開預覽狀態，並可在所有區域中使用。  

連結[庫](#libraries)  | [範例](#code-samples)  | [最佳做法](#best-practice-guidelines)  | [提供意見](#provide-feedback)反應

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

- 已啟用階層命名空間 (HNS) 的儲存體帳戶。 遵循[下列](data-lake-storage-quickstart-create-account.md)指示以建立帳戶。

- 執行遞迴 ACL 進程的正確許可權。 正確的許可權包含下列其中一項： 

  - 已布建的 Azure Active Directory (AD) [安全性主體](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) ，在目標容器、父資源群組或訂用帳戶的範圍中，已將 [儲存體 Blob 資料擁有](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 者角色指派給該角色。   

  - 您打算套用遞迴 ACL 進程之目標容器或目錄的擁有使用者。 這包括目標容器或目錄中的所有子專案。 

- 瞭解如何將 Acl 套用至目錄和檔案。 請參閱 [Azure Data Lake Storage Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 

請參閱本文的 **設定專案** 一節，以查看 PowerShell、.net SDK 和 Python SDK 的安裝指引。

## <a name="set-up-your-project"></a>設定專案

安裝必要的程式庫。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 確定已安裝 .NET framework。 請參閱 [下載 .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework)。
 
2. 使用下列命令確認已安裝的 PowerShell 版本 `5.1` 或更高版本。    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   若要升級您的 PowerShell 版本，請參閱 [升級現有的 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
3. 安裝最新版本的 PowershellGet 模組。

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

4. 關閉，然後重新開啟 PowerShell 主控台。

5. 安裝 **Az. Storage** preview 模組。

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   如需如何安裝 PowerShell 模組的詳細資訊，請參閱 [安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

### <a name="net"></a>[.NET](#tab/dotnet)

1. 開啟命令視窗 (例如： Windows PowerShell) 。

2. 從您的專案目錄中，使用命令來安裝 DataLake preview 套件。 `dotnet add package`

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.3.0-dev.20200811.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. 將這些 using 語句新增至程式碼檔案的頂端。

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="python"></a>[Python](#tab/python)

1. 下載 [適用于 Python 的 Azure Data Lake Storage 用戶端程式庫](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)。

2. 安裝您使用 [pip](https://pypi.org/project/pip/)下載的程式庫。

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

將這些 import 語句新增至程式碼檔案的頂端。

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

---

## <a name="connect-to-your-account"></a>連接至您的帳戶

您可以使用 Azure Active Directory (AD) 或使用帳戶金鑰進行連接。 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

開啟 Windows PowerShell 命令視窗，然後使用命令登入 Azure 訂用帳戶， `Connect-AzAccount` 並遵循畫面上的指示。

```powershell
Connect-AzAccount
```

如果您的身分識別與多個訂用帳戶相關聯，請將您的使用中訂用帳戶設定為您想要在其中建立及管理目錄的儲存體帳戶訂用帳戶。 在此範例中，請將 `<subscription-id>` 預留位置值取代為您的訂用帳戶識別碼。

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

接下來，選擇您要命令如何取得儲存體帳戶的授權。 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>選項1：使用 Azure Active Directory (AD) 取得授權

使用此方法時，系統會確保您的使用者帳戶具有適當的角色型存取控制， (RBAC) 指派和 ACL 許可權。 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

下表顯示每個支援的角色及其 ACL 設定功能。

|角色|ACL 設定功能|
|--|--|
|[儲存體 Blob 資料擁有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|帳戶中的所有目錄和檔案。|
|[儲存體 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|只有安全性主體所擁有的目錄和檔案。|

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>選項2：使用儲存體帳戶金鑰取得授權

使用此方法時，系統不會檢查 RBAC 或 ACL 許可權。

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

### <a name="net"></a>[.NET](#tab/dotnet)

若要使用本文中的程式碼片段，您必須建立代表儲存體帳戶的 [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 實例。

#### <a name="connect-by-using-azure-active-directory-ad"></a>使用 Azure Active Directory (AD) 連接

您可以使用 [適用于 .net 的 Azure 身分識別用戶端程式庫](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) ，透過 Azure AD 來驗證您的應用程式。

安裝封裝之後，請將此 using 語句新增至程式碼檔案的頂端。

```csharp
using Azure.Identity;
```

取得用戶端識別碼、用戶端密碼和租使用者識別碼。 若要這樣做，請參閱 [從用戶端應用程式授權要求取得 Azure AD 的權杖](../common/storage-auth-aad-app.md)。 在該程式中，您必須將下列其中一個以 [角色為基礎的存取控制 (RBAC) ](../../role-based-access-control/overview.md) 角色指派給您的安全性主體。 

|角色|ACL 設定功能|
|--|--|
|[儲存體 Blob 資料擁有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|帳戶中的所有目錄和檔案。|
|[儲存體 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|只有安全性主體所擁有的目錄和檔案。|

此範例會使用用戶端識別碼、用戶端密碼和租使用者識別碼來建立 [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) 實例。  

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

``` 

#### <a name="connect-by-using-an-account-key"></a>使用帳戶金鑰進行連接

這是連接到帳戶最簡單的方法。 

此範例會使用帳戶金鑰建立 [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) 實例。

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

> [!NOTE]
> 如需更多範例，請參閱 [適用于 .net 的 Azure 身分識別用戶端程式庫](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) 檔。

### <a name="python"></a>[Python](#tab/python)

若要使用本文中的程式碼片段，您必須建立代表儲存體帳戶的 **DataLakeServiceClient** 實例。 

### <a name="connect-by-using-azure-active-directory-ad"></a>使用 Azure Active Directory (AD) 連接

您可以使用 [適用于 Python 的 Azure 身分識別用戶端程式庫](https://pypi.org/project/azure-identity/) ，以 Azure AD 來驗證您的應用程式。

此範例會使用用戶端識別碼、用戶端密碼和租使用者識別碼來建立 **DataLakeServiceClient** 實例。  若要取得這些值，請參閱 [從 Azure AD 取得權杖，以授權用戶端應用程式的要求](../common/storage-auth-aad-app.md)。 在該程式中，您必須將下列其中一個以 [角色為基礎的存取控制 (RBAC) ](../../role-based-access-control/overview.md) 角色指派給您的安全性主體。 

|角色|ACL 設定功能|
|--|--|
|[儲存體 Blob 資料擁有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|帳戶中的所有目錄和檔案。|
|[儲存體 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|只有安全性主體所擁有的目錄和檔案。|

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> 如需更多範例，請參閱 [適用于 Python 的 Azure 身分識別用戶端程式庫](https://pypi.org/project/azure-identity/) 檔。

### <a name="connect-by-using-an-account-key"></a>使用帳戶金鑰進行連接

這是連接到帳戶的最簡單方式。 

此範例會使用帳戶金鑰建立 **DataLakeServiceClient** 實例。

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- 使用您的儲存體帳戶名稱取代 `storage_account_name` 預留位置值。

- 將 `storage_account_key` 預留位置值取代為您的儲存體帳戶存取金鑰。

---

## <a name="set-an-acl-recursively"></a>以遞迴方式設定 ACL

您可以遞迴方式設定 Acl。  

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 Cmdlet 以遞迴方式設定 ACL `Set-AzDataLakeGen2AclRecursive` 。

此範例會設定名為之目錄的 ACL `my-parent-directory` 。 這些專案會提供擁有使用者的讀取、寫入和執行許可權，僅授與擁有群組讀取和執行許可權，並提供所有其他人無存取權。 此範例中的最後一個 ACL 專案會提供物件識別碼為 "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" 的讀取和執行許可權的特定使用者。

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

### <a name="net"></a>[.NET](#tab/dotnet)

藉由呼叫 **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** 方法，以遞迴方式設定 ACL。 將[PathAccessControlItems](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)的[清單](/dotnet/api/system.collections.generic.list-1)傳遞給這個方法。 每個 [PathAccessControlItems](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 都會定義 ACL 專案。

此範例會設定名為之目錄的 ACL `my-parent-directory` 。 這些專案會提供擁有使用者的讀取、寫入和執行許可權，僅授與擁有群組讀取和執行許可權，並提供所有其他人無存取權。 此範例中的最後一個 ACL 專案會提供物件識別碼為 "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "read and execute 許可權的特定使用者。

```cs
public async void SetACLRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlList = 
        new List<PathAccessControlItem>() 
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Write | 
            RolePermissions.Execute),
                    
        new PathAccessControlItem(AccessControlType.Group, 
            RolePermissions.Read | 
            RolePermissions.Execute),
                    
        new PathAccessControlItem(AccessControlType.Other, 
            RolePermissions.None),

        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Execute, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.SetAccessControlRecursiveAsync
        (accessControlList, null);
}

```

### <a name="python"></a>[Python](#tab/python)

藉由呼叫 DataLakeDirectoryClient，以遞迴方式設定 ACL。 **set_access_control_recursive** 方法。

此範例會設定名為之目錄的 ACL `my-parent-directory` 。 這些專案會提供擁有使用者的讀取、寫入和執行許可權，僅授與擁有群組讀取和執行許可權，並提供所有其他人無存取權。 此範例中的最後一個 ACL 專案會提供物件識別碼為 "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "read and execute 許可權的特定使用者。

```python
def set_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

---

## <a name="update-an-acl-recursively"></a>以遞迴方式更新 ACL

您可以遞迴方式更新現有的 ACL。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用  **AzDataLakeGen2AclRecursive** Cmdlet 以遞迴方式更新 ACL。 

此範例會更新具有寫入權限的 ACL 專案。

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

### <a name="net"></a>[.NET](#tab/dotnet)

藉由呼叫 **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** 方法，以遞迴方式更新 ACL。 

此範例會更新具有寫入權限的 ACL 專案。 

```cs
public async void UpdateACLsRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
        GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlListUpdate = 
        new List<PathAccessControlItem>()
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read |
            RolePermissions.Write | 
            RolePermissions.Execute, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.UpdateAccessControlRecursiveAsync
        (accessControlListUpdate, null);

}
```

### <a name="python"></a>[Python](#tab/python)

藉由呼叫 DataLakeDirectoryClient，以遞迴方式更新 ACL。 **update_access_control_recursive** 方法。 

此範例會更新具有寫入權限的 ACL 專案。 

```python
def update_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

---

## <a name="remove-acl-entries-recursively"></a>以遞迴方式移除 ACL 專案

您可以以遞迴方式移除一或多個 ACL 專案。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 **AzDataLakeGen2AclRecursive** CMDLET 移除 ACL 專案。 

此範例會從容器的根目錄移除 ACL 專案。  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

### <a name="net"></a>[.NET](#tab/dotnet)

藉由呼叫 **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** 方法來移除 ACL 專案。 

此範例會從名為的目錄的 ACL 中移除 ACL 專案 `my-parent-directory` 。 

```cs
public async void RemoveACLsRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<RemovePathAccessControlItem> accessControlListForRemoval = 
        new List<RemovePathAccessControlItem>()
        {
            new RemovePathAccessControlItem(AccessControlType.User, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
        };

    await directoryClient.RemoveAccessControlRecursiveAsync
        (accessControlListForRemoval, null);

}
```

### <a name="python"></a>[Python](#tab/python)

藉由呼叫 DataLakeDirectoryClient 方法來移除 ACL 專案。 **remove_access_control_recursive** 方法。 

此範例會從名為的目錄的 ACL 中移除 ACL 專案 `my-parent-directory` 。 

```python
def remove_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:4a9028cf-f779-4032-b09d-970ebe3db258'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

---

## <a name="recover-from-failures"></a>從失敗中復原

您可能會遇到執行時間或許可權錯誤。 如果是執行階段錯誤，請從一開始重新開機進程。 如果安全性主體沒有足夠的許可權可修改目錄或正在修改之目錄階層中的檔案 ACL，則可能會發生許可權錯誤。 解決許可權問題，然後選擇使用接續 token 從失敗點繼續處理常式，或從一開始就重新開機進程。 如果您想要從頭開始重新開機，就不需要使用接續 token。 您可以重新套用 ACL 專案，而不會產生負面影響。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

將結果傳回至變數。 將失敗的專案輸送至格式化的表格。

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

根據資料表的輸出，您可以修正任何許可權錯誤，然後使用接續 token 來繼續執行。

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

## <a name="net"></a>[.NET](#tab/dotnet)

此範例會在發生失敗時傳回接續 token。 應用程式可以在解決錯誤之後，再次呼叫這個範例方法，然後傳入接續 token。 如果第一次呼叫此範例方法，應用程式可以傳入的值做 ``null`` 為接續 token 參數。 

```cs
public async Task<string> ResumeAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient,
    List<PathAccessControlItem> accessControlList, 
    string continuationToken)
{
    try
    {
        var accessControlChangeResult =
            await directoryClient.SetAccessControlRecursiveAsync(
                accessControlList, null, continuationToken: continuationToken);

        if (accessControlChangeResult.Value.Counters.FailedChangesCount > 0)
        {
            continuationToken =
                accessControlChangeResult.Value.ContinuationToken;
        }

        return continuationToken;
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
        return continuationToken;
    }

}
```

### <a name="python"></a>[Python](#tab/python)

此範例會在發生失敗時傳回接續 token。 應用程式可以在解決錯誤之後，再次呼叫這個範例方法，然後傳入接續 token。 如果第一次呼叫此範例方法，應用程式可以傳入的值做 ``None`` 為接續 token 參數。 

```python
def resume_set_acl_recursive(continuation_token):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl, continuation=continuation_token)

        continuation_token = acl_change_result.continuation

        return continuation_token
        
    except Exception as e:
     print(e) 
     return continuation_token
```

---

## <a name="resources"></a>資源

本章節包含程式庫和程式碼範例的連結。

#### <a name="libraries"></a>程式庫

- [PowerShell](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.powershellgallery.com%2Fpackages%2FAz.Storage%2F2.5.2-preview&data=02%7C01%7Cnormesta%40microsoft.com%7Ccdabce06132c42132b4008d849a2dfb1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637340311173215017&sdata=FWynO9UKTt7ESMCFgkWaL7J%2F%2BjODaRo5BD6G6yCx9os%3D&reserved=0)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)

#### <a name="code-samples"></a>程式碼範例

- PowerShell：[讀我檔案](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)  |  [範例](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- NET：[讀我檔案](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%252FSc2aDVW3De4A%252Fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)  |  [範例](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python：[讀我檔案](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)  |  [範例](https://recursiveaclpr.blob.core.windows.net/privatedrop/datalake_samples_access_control_async.py?sv=2019-02-02&st=2020-08-24T07%3A48%3A10Z&se=2021-08-25T07%3A48%3A00Z&sr=b&sp=r&sig=%2F1c540%2BpXYyNcuTmWPWHg2m9SyClXLIMw7ChLZGsyD0%3D)

## <a name="best-practice-guidelines"></a>最佳做法指南

本節提供您以遞迴方式設定 Acl 的一些最佳做法指導方針。 

#### <a name="handling-runtime-errors"></a>處理執行階段錯誤

有許多原因可能會發生執行階段錯誤 (例如：中斷或用戶端連線問題) 。 如果您遇到執行階段錯誤，請重新開機遞迴 ACL 進程。 Acl 可以重新套用至專案，而不會造成負面影響。 

#### <a name="handling-permission-errors-403"></a>處理 (403) 的許可權錯誤

如果您在執行遞迴 ACL 程式時遇到存取控制例外狀況，則您的 AD [安全性主體](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) 可能沒有足夠的許可權可將 ACL 套用至目錄階層中的一個或多個子專案。 發生許可權錯誤時，進程會停止，並提供接續 token。 修正許可權問題，然後使用接續 token 來處理剩餘的資料集。 已成功處理的目錄和檔案不需要重新處理。 您也可以選擇重新開機遞迴 ACL 處理常式。 Acl 可以重新套用至專案，而不會造成負面影響。 

#### <a name="credentials"></a>認證 

建議您在目標儲存體帳戶或容器的範圍中，布建已獲派 [儲存體 Blob 資料擁有](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 者角色的 Azure AD 安全性主體。 

#### <a name="performance"></a>效能 

若要減少延遲，建議您在 Azure 虛擬機器中執行遞迴 ACL 程式， (VM) 位於與儲存體帳戶相同的區域中。 

#### <a name="acl-limits"></a>ACL 限制

您可以套用至目錄或檔案的 Acl 數目上限為32存取 Acl 和32預設 Acl。 如需詳細資訊，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

<a id="provide-feedback"></a>

### <a name="provide-feedback-or-report-issues"></a>提供意見反應或報告問題

您可以使用下列任何頁面來提供您的意見反應或回報問題： [PowerShell](https://github.com/Azure/azure-powershell/issues/new?assignees=&labels=triage&template=az-module-bug-report.md&title=)、 [.net](https://github.com/Azure/azure-sdk-for-net/issues/new?assignees=&labels=&template=bug_report.md&title=)、 [Python](https://github.com/Azure/azure-sdk-for-python/issues/new?assignees=&labels=&template=bug_report.md&title=)

## <a name="see-also"></a>另請參閱

- [Azure Data Lake Storage Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
- [已知問題](data-lake-storage-known-issues.md)


