---
title: Azure Data Lake Storage Gen2 .NET SDK for files & Acl
description: 使用 Azure 儲存體用戶端程式庫，在已啟用階層命名空間 (HNS) 的儲存體帳戶中，管理目錄和檔案和目錄存取控制清單 (ACL) 。
author: normesta
ms.service: storage
ms.date: 08/26/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 5af1c656699a7c60ad4f93beb43b603bdc6e3be7
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935099"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 .NET 來管理 Azure Data Lake Storage Gen2 中的目錄、檔案和 Acl

本文說明如何使用 .NET 來建立和管理已啟用階層命名空間 (HNS) 之儲存體帳戶中的目錄、檔案和許可權。 

[封裝 (NuGet) ](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  | [範例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  | [API 參考](/dotnet/api/azure.storage.files.datalake)  | [Gen1 至 Gen2 對應](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  | [提供意見](https://github.com/Azure/azure-sdk-for-net/issues)反應

## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 已啟用階層命名空間 (HNS) 的儲存體帳戶。 遵循[下列](../common/storage-account-create.md)指示以建立帳戶。

## <a name="set-up-your-project"></a>設定專案

若要開始使用，請安裝 [DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet 套件。

如需有關如何安裝 NuGet 套件的詳細資訊，請參閱 [使用 nuget 封裝管理員在 Visual Studio 中安裝和管理套件](/nuget/consume-packages/install-use-packages-visual-studio)。

然後，將這些 using 語句新增至程式碼檔案的頂端。

```csharp
using Azure;
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;

```

## <a name="connect-to-the-account"></a>連線到帳戶

若要使用本文中的程式碼片段，您必須建立代表儲存體帳戶的 [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 實例。 

### <a name="connect-by-using-an-account-key"></a>使用帳戶金鑰進行連接

這是連接到帳戶的最簡單方式。 

此範例會使用帳戶金鑰建立 [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 實例。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-ad"></a>使用 Azure Active Directory (AD) 連接

您可以使用 [適用于 .net 的 Azure 身分識別用戶端程式庫](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) ，透過 Azure AD 來驗證您的應用程式。

此範例會使用用戶端識別碼、用戶端密碼和租使用者識別碼來建立 [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 實例。  若要取得這些值，請參閱 [從 Azure AD 取得權杖，以授權用戶端應用程式的要求](../common/storage-auth-aad-app.md)。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> 如需更多範例，請參閱 [適用于 .net 的 Azure 身分識別用戶端程式庫](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) 檔。

## <a name="create-a-container"></a>建立容器

容器可作為檔案的檔案系統。 您可以藉由呼叫 [DataLakeServiceClient. CreateFileSystem](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) 方法來建立一個。

這個範例會建立名為的容器 `my-file-system` 。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>建立目錄

藉由呼叫 [DataLakeFileSystemClient. CreateDirectoryAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) 方法來建立目錄參考。

此範例會將名為 `my-directory` 的目錄新增至容器，然後新增名為的子目錄 `my-subdirectory` 。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>重新命名或移動目錄

藉由呼叫 [DataLakeDirectoryClient. RenameAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) 方法來重新命名或移動目錄。 傳遞所需目錄的路徑給參數。 

此範例會將子目錄重新命名為名稱 `my-subdirectory-renamed` 。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_RenameDirectory":::

此範例會將名為的目錄移 `my-subdirectory-renamed` 至名為之目錄的子目錄 `my-directory-2` 。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>刪除目錄

藉由呼叫 [DataLakeDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) 方法來刪除目錄。

此範例刪除名為 `my-directory` 的目錄。  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>將檔案上傳至目錄

首先，藉由建立 [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) 類別的實例，在目標目錄中建立檔案參考。 藉由呼叫 [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) 方法來上傳檔案。 請務必藉由呼叫 [DataLakeFileClient. FlushAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) 方法來完成上傳。

此範例會將文字檔上傳至名為的目錄 `my-directory` 。 
   
:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFile":::

> [!TIP]
> 如果您的檔案大小很大，您的程式碼就必須對 DataLakeFileClient 進行多次呼叫 [AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)。 請考慮改為使用 [DataLakeFileClient. >uploadasync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) 方法。 如此一來，您就可以在單一呼叫中上傳整個檔案。 
>
> 如需範例，請參閱下一節。

## <a name="upload-a-large-file-to-a-directory"></a>將大型檔案上傳至目錄

使用 [DataLakeFileClient. >uploadasync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) 方法上傳大型檔案，而不需要對 [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) 方法進行多次呼叫。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>從目錄下載 

首先，建立代表您要下載之檔案的 [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) 實例。 使用 [DataLakeFileClient. system.io.stream.readasync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync)  方法，並剖析傳回值以取得 [資料流程](/dotnet/api/system.io.stream) 物件。 使用任何 .NET 檔案處理 API，將資料流程中的位元組儲存至檔案。 

此範例會使用 [BinaryReader](/dotnet/api/system.io.binaryreader) 和 [FileStream](/dotnet/api/system.io.filestream) 將位元組儲存至檔案。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DownloadBinaryFromDirectory":::

## <a name="list-directory-contents"></a>列出目錄內容

藉由呼叫 [FileSystemClient GetPathsAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) 方法來列出目錄內容，然後列舉結果。

此範例會列印名為的目錄中每個檔案的名稱 `my-directory` 。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_ListFilesInDirectory":::

## <a name="manage-access-control-lists-acls"></a> (Acl) 管理存取控制清單

您可以取得、設定及更新目錄和檔案的存取權限。

> [!NOTE]
> 如果您使用 Azure Active Directory (Azure AD) 來授與存取權，請確定已將 [儲存體 Blob 資料擁有者角色](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)指派給您的安全性主體。 若要深入了解如何套用 ACL 權限以及變更權限的效果，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](./data-lake-storage-access-control.md)。

### <a name="manage-a-directory-acl"></a>管理目錄 ACL

藉由呼叫 [DataLakeDirectoryClient GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) 方法，取得目錄 (acl) 的存取控制清單，並藉由呼叫 [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) 方法來設定 acl。

> [!NOTE]
> 如果您的應用程式使用 Azure Active Directory (Azure AD) 來授與存取權，請確定您的應用程式用來授與存取權的安全性主體已獲指派 [儲存體 Blob 資料擁有者角色](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)。 若要深入了解如何套用 ACL 權限以及變更權限的效果，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](./data-lake-storage-access-control.md)。 

這個範例會取得並設定名為之目錄的 ACL `my-directory` 。 字串 `user::rwx,group::r-x,other::rw-` 提供擁有使用者的讀取、寫入和執行許可權，僅提供擁有群組的讀取和執行許可權，並提供所有其他的讀取和寫入權限。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ACLDirectory":::

您也可以取得及設定容器根目錄的 ACL。 若要取得根目錄，請將空字串 (`""`) 傳遞至 [DataLakeFileSystemClient. GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) 方法。

### <a name="manage-a-file-acl"></a>管理檔案 ACL

藉由呼叫 [DataLakeFileClient GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) 方法，取得檔案 (acl) 的存取控制清單，並藉由呼叫 [DataLakeFileClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) 方法來設定 acl。

> [!NOTE]
> 如果您的應用程式使用 Azure Active Directory (Azure AD) 來授與存取權，請確定您的應用程式用來授與存取權的安全性主體已獲指派 [儲存體 Blob 資料擁有者角色](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)。 若要深入了解如何套用 ACL 權限以及變更權限的效果，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](./data-lake-storage-access-control.md)。 

這個範例會取得並設定名為之檔案的 ACL `my-file.txt` 。 字串 `user::rwx,group::r-x,other::rw-` 提供擁有使用者的讀取、寫入和執行許可權，僅提供擁有群組的讀取和執行許可權，並提供所有其他的讀取和寫入權限。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_FileACL":::

### <a name="set-an-acl-recursively"></a>以遞迴方式設定 ACL

您可以在父目錄的現有子專案上以遞迴方式新增、更新和移除 Acl，而不需要為每個子專案個別進行這些變更。 如需詳細資訊，請參閱 [設定 (acl 的存取控制清單) 以遞迴方式進行 Azure Data Lake Storage Gen2](recursive-access-control-lists.md)。

## <a name="see-also"></a>請參閱

* [API 參考文件](/dotnet/api/azure.storage.files.datalake)
* [套件 (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [範例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Gen1 至 Gen2 對應](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [已知問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [提供意見反應](https://github.com/Azure/azure-sdk-for-net/issues)