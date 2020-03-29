---
title: Azure 資料存儲 Gen2 .NET SDK，用於& ACL 的檔
description: 使用 Azure 存儲用戶端庫管理已啟用階層命名空間 （HNS） 的存儲帳戶中的目錄、檔和目錄存取控制清單 （ACL）。
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: b83d0d2d765b60585832f1a3e7c610f05eac075c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061574"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 .NET 管理 Azure 資料存儲單元 2 中的目錄、檔和 ACL

本文介紹如何使用 .NET 創建和管理已啟用階層命名空間 （HNS） 的存儲帳戶中的目錄、檔和許可權。 

[包 （NuGet）](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [API 參考](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake) | [第 1 代映射到第 2 代映射](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [提供回饋](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 已啟用階層命名空間 （HNS） 的存儲帳戶。 按照[這些](data-lake-storage-quickstart-create-account.md)說明創建一個。

## <a name="set-up-your-project"></a>設定專案

要開始，請安裝[Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet 包。

有關如何安裝 NuGet 包的詳細資訊，請參閱使用[NuGet 套裝軟體管理器在 Visual Studio 中安裝和管理包](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio)。

然後，將這些使用語句添加到代碼檔的頂部。

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>連接到帳戶

要使用本文中的程式碼片段，您需要創建表示存儲帳戶[的 DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient)實例。 

### <a name="connect-by-using-an-account-key"></a>使用帳戶金鑰進行連接

這是連接到帳戶的最簡單方法。 

本示例使用帳戶金鑰創建[DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?)實例。

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

### <a name="connect-by-using-azure-active-directory-ad"></a>使用 Azure 活動目錄 （AD） 進行連接

可以使用[.NET 的 Azure 標識用戶端庫](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)使用 Azure AD 對應用程式進行身份驗證。

本示例使用用戶端 ID、用戶端機密和租戶 ID 創建[DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?)實例。  要獲取這些值，請參閱[從 Azure AD 獲取權杖以授權來自用戶端應用程式的請求](../common/storage-auth-aad-app.md)。

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

> [!NOTE]
> 有關詳細資訊，請參閱[.NET 文檔的 Azure 標識用戶端庫](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)。

## <a name="create-a-file-system"></a>建立檔案系統

檔案系統充當檔的容器。 您可以通過調用[DataLakeServiceClient.CreateSystem](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync)方法來創建一個。

本示例創建名為 的`my-file-system`檔案系統。 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>建立目錄

通過調用[DataLakeFileSystemClient.CreateDirectoryAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync)方法創建目錄引用。

本示例將名為 的`my-directory`目錄添加到檔案系統，然後添加名為`my-subdirectory`的子目錄。 

```cs
public async Task<DataLakeDirectoryClient> CreateDirectory
    (DataLakeServiceClient serviceClient, string fileSystemName)
{
    DataLakeFileSystemClient fileSystemClient =
        serviceClient.GetFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        await fileSystemClient.CreateDirectoryAsync("my-directory");

    return await directoryClient.CreateSubDirectoryAsync("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>重新命名目錄或移動目錄

通過調用[DataLakeDirectoryClient.重命名 Async](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync)方法重命名或移動目錄。 傳遞所需目錄的路徑參數。 

本示例將子目錄重命名為 名稱`my-subdirectory-renamed`。

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

本示例將名為`my-subdirectory-renamed`的目錄移動到名為`my-directory-2`的目錄的子目錄。 

```cs
public async Task<DataLakeDirectoryClient> MoveDirectory
    (DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
            fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory-renamed");

    return await directoryClient.RenameAsync("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>刪除目錄

通過調用[DataLakeDirectoryClient.Delete](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete)方法刪除目錄。

此示例刪除名為 的`my-directory`目錄。  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>管理目錄 ACL

通過調用[DataLakeDirectoryClient.獲取存取控制 Async](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync)方法，通過調用[DataLakeDirectoryClient.SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist)方法來獲取目錄的存取控制清單 （ACL）。

> [!NOTE]
> 如果應用程式使用 Azure 活動目錄 （Azure AD） 授權訪問，則請確保已分配應用程式用於授權訪問的安全[主體。](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) 要瞭解有關如何應用 ACL 許可權及其更改效果的更多內容，請參閱[Azure 資料湖存儲 Gen2 中的訪問控制項](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 

此示例獲取並設置名為 的`my-directory`目錄的 ACL。 該字串`user::rwx,group::r-x,other::rw-`授予擁有的使用者讀取、寫入和執行許可權，僅授予擁有組讀取和執行許可權，並授予所有其他讀取和寫入權限。

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    directoryClient.SetAccessControlList(accessControlList);

}

```

## <a name="upload-a-file-to-a-directory"></a>將檔上載到目錄

首先，通過創建[DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient)類的實例在目標目錄中創建檔引用。 通過調用[DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)方法上傳檔。 請確保通過調用[DataLakeFileClient.FlushAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync)方法完成上載。

本示例將文字檔上載到名為 的`my-directory`目錄。    

```cs
public async Task UploadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = await directoryClient.CreateFileAsync("uploaded-file.txt");

    FileStream fileStream = 
        File.OpenRead("C:\\file-to-upload.txt");

    long fileSize = fileStream.Length;

    await fileClient.AppendAsync(fileStream, offset: 0);

    await fileClient.FlushAsync(position: fileSize);

}
```

> [!TIP]
> 如果檔案大小較大，則代碼必須對[DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)進行多次調用。 請考慮使用[DataLakeFileClient.uploadasync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_)方法。 這樣，您可以在單個調用中上載整個檔。 
>
> 如需範例，請參閱下一節。

## <a name="upload-a-large-file-to-a-directory"></a>將大型檔上載到目錄

使用[DataLakeFileClient.uploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_)方法上載大型檔，而無需對[DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)方法進行多次調用。

```cs
public async Task UploadFileBulk(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.GetFileClient("uploaded-file.txt");

    FileStream fileStream =
        File.OpenRead("C:\\file-to-upload.txt");

    await fileClient.UploadAsync(fileStream);

}

```

## <a name="manage-a-file-acl"></a>管理檔 ACL

通過調用[DataLakeFileClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync)方法獲取檔的存取控制清單 （ACL），並通過調用[DataLakeFileClient.SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist)方法設置 ACL。

> [!NOTE]
> 如果應用程式使用 Azure 活動目錄 （Azure AD） 授權訪問，則請確保已分配應用程式用於授權訪問的安全[主體。](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) 要瞭解有關如何應用 ACL 許可權及其更改效果的更多內容，請參閱[Azure 資料湖存儲 Gen2 中的訪問控制項](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 

此示例獲取並設置名為`my-file.txt`的檔的 ACL。 該字串`user::rwx,group::r-x,other::rw-`授予擁有的使用者讀取、寫入和執行許可權，僅授予擁有組讀取和執行許可權，並授予所有其他讀取和寫入權限。

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    fileClient.SetAccessControlList(accessControlList);
}
```

## <a name="download-from-a-directory"></a>從目錄中下載 

首先，創建一個表示要下載的檔[的資料湖檔用戶端](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient)實例。 使用[DataLakeFileClient.ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync)方法，並分析傳回值以獲取[流](https://docs.microsoft.com/dotnet/api/system.io.stream)物件。 使用任何 .NET 檔處理 API 將位元組從流保存到檔。 

本示例使用[二進位讀取器](https://docs.microsoft.com/dotnet/api/system.io.binaryreader)和[檔流](https://docs.microsoft.com/dotnet/api/system.io.filestream)將位元組保存到檔中。 

```cs
public async Task DownloadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("my-image.png");

    Response<FileDownloadInfo> downloadResponse = await fileClient.ReadAsync();

    BinaryReader reader = new BinaryReader(downloadResponse.Value.Content);

    FileStream fileStream = 
        File.OpenWrite("C:\\my-image-downloaded.png");

    int bufferSize = 4096;

    byte[] buffer = new byte[bufferSize];

    int count;

    while ((count = reader.Read(buffer, 0, buffer.Length)) != 0)
    {
        fileStream.Write(buffer, 0, count);
    }

    await fileStream.FlushAsync();

    fileStream.Close();
}
```

## <a name="list-directory-contents"></a>列出目錄內容

通過調用[FileSystemClient.GetPathsAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync)方法，然後枚舉結果來列出目錄內容。

此示例列印位於名為`my-directory`的目錄中的每個檔的名稱。

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.GetPathsAsync("my-directory").GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    PathItem item = enumerator.Current;

    while (item != null)
    {
        Console.WriteLine(item.Name);

        if (!await enumerator.MoveNextAsync())
        {
            break;
        }
                
        item = enumerator.Current;
    }

}
```

## <a name="see-also"></a>另請參閱

* [API 參考文件](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)
* [包裝（NuGet）](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [樣品](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [第 1 代到第 2 代映射](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [已知問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [提供回饋](https://github.com/Azure/azure-sdk-for-net/issues)

