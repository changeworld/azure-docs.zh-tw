---
title: 使用 .NET 開發 Azure 檔案服務 | Microsoft Docs
description: 了解如何開發使用 Azure 檔案服務來儲存檔案資料的 .NET 應用程式和服務。
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 361ee5179b20d9488bb477a4e3c9fc0f0e6f266e
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190655"
---
# <a name="develop-for-azure-files-with-net"></a>使用 .NET 開發 Azure 檔案服務

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

本教學課程將示範基本概念，說明如何利用 .NET 開發使用 [Azure 檔案服務](storage-files-introduction.md)來儲存檔案資料的應用程式。 本教學課程會建立簡單的主控台應用程式，以使用 .NET 和 Azure 檔案儲存體執行基本動作：

* 取得檔案的內容。
* 設定檔案共用的大小上限或*配額*。
* 為使用共用上所定義之預存存取原則的檔案建立共用存取簽章（SAS 金鑰）。
* 將檔案複製到相同儲存體帳戶中的另一個檔案。
* 將檔案複製到相同儲存體帳戶中的 Blob。
* 使用 Azure 儲存體計量進行疑難排解。

若要深入瞭解 Azure 檔案儲存體，請參閱[什麼是 Azure 檔案儲存體？](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>了解 .NET API

Azure 檔案服務會提供兩種廣泛的方法給用戶端應用程式：伺服器訊息區 (SMB) 和 REST。 在 .NET 中，`System.IO` 和 `WindowsAzure.Storage` Api 會將這些方法抽象化。

API | 使用時機 | 注意事項
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | 您的應用程式： <ul><li>需要使用 SMB 來讀取/寫入檔案</li><li>正在可透過連接埠 445 存取您 Azure 檔案服務帳戶的裝置上執行</li><li>不需要管理檔案共用的任何系統管理設定</li></ul> | 透過 SMB Azure 檔案儲存體執行的檔案 i/o，通常與任何網路檔案共用或本機儲存裝置的 i/o 相同。 如需 .NET 中一些功能的簡介（包括檔案 i/o），請參閱[主控台應用程式](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter)教學課程。
[Microsoft. Azure 儲存檔案](/dotnet/api/overview/azure/storage?view=azure-dotnet#version-11x) | 您的應用程式： <ul><li>因為防火牆或 ISP 的條件約束，所以無法使用埠445上的 SMB 存取 Azure 檔案儲存體</li><li>需要系統管理功能，例如設定檔案共用的配額，或建立共用存取簽章的能力</li></ul> | 本文示範如何使用 REST 來執行檔案 i/o 的 `Microsoft.Azure.Storage.File`，而不是 SMB 及檔案共用的管理。

## <a name="create-the-console-application-and-obtain-the-assembly"></a>建立主控台應用程式並取得組件

在 Visual Studio 中，建立新的 Windows 主控台應用程式。 下列步驟說明如何在 Visual Studio 2019 中建立主控台應用程式。 這些步驟類似其他 Visual Studio 版本中的步驟。

1. 啟動 Visual Studio，然後選取 [建立新專案]。
1. 在 [**建立新專案**] 中，選擇 [**主控台應用程式（.NET Framework）** ] C#，然後選取 **[下一步]** 。
1. 在 [**設定您的新專案**] 中，輸入應用程式的名稱，然後選取 [**建立**]。

您可以將本教學課程中的所有程式碼範例新增至主控台應用程式 `Program.cs` 檔案的 `Main()` 方法。

您可以在任何類型的 .NET 應用程式中使用 Azure 儲存體用戶端程式庫。 這些類型包括 Azure 雲端服務或 web 應用程式，以及桌面和行動應用程式。 在本指南中，為求簡化，我們會使用主控台應用程式。

## <a name="use-nuget-to-install-the-required-packages"></a>使用 NuGet 來安裝必要的封裝

請參閱您專案中的這些套件，以完成本教學課程：

* [適用于 .NET 的 Microsoft Azure 儲存體通用程式庫](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
  
  此套件可讓您以程式設計方式存取儲存體帳戶中的一般資源。
* [適用于 .NET 的 Microsoft Azure 儲存體 Blob 程式庫](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)

  此套件可讓您以程式設計方式存取儲存體帳戶中的 blob 資源。
* [適用于 .NET 的 Microsoft Azure 儲存體檔案程式庫](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

  此套件可讓您以程式設計方式存取儲存體帳戶中的檔案資源。
* [適用于 .NET 的 Microsoft Azure Configuration Manager 程式庫](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)

  此套件提供一個類別，用於剖析設定檔中的連接字串，無論您的應用程式在何處執行。

您可以使用 NuGet 來取得這兩個封裝。 請遵循下列步驟：

1. 在**方案總管**中，以滑鼠右鍵按一下您的專案，然後選擇 [**管理 NuGet 封裝**]。
1. 在**NuGet 套件管理員**中，選取 **[流覽]** 。 然後，搜尋並選擇 [ **Microsoft. Azure**]，然後選取 [**安裝**]。

   此步驟會安裝封裝及其相依性。
1. 搜尋並安裝這些封裝：

   * **Microsoft. Azure 儲存體。通用**
   * **Microsoft. Azure 儲存檔案**
   * **ConfigurationManager**

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>將您的儲存體帳號憑證儲存到 App.config 檔案

接下來，將您的認證儲存在專案的 `App.config` 檔案中。 在**方案總管**中，按兩下 `App.config` 並編輯檔案，使其類似下列範例。 將 `myaccount` 取代為您的儲存體帳戶名稱，並使用您的儲存體帳戶金鑰來 `mykey`。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> 最新版本的 Azure 儲存體模擬器不支援 Azure 檔案服務。 您的連接字串必須以雲端中的 Azure 儲存體帳戶為目標，才能與 Azure 檔案服務搭配使用。

## <a name="add-using-directives"></a>新增 using 指示詞

在**方案總管**中，開啟 `Program.cs` 檔案，然後將下列 using 指示詞新增至檔案頂端。

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>以程式設計方式存取檔案共用

接下來，在上方顯示的程式碼之後，將下列內容新增至 `Main()` 方法，以取得連接字串。 這段程式碼會取得我們稍早建立之檔案的參考，並輸出其內容。

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

執行主控台應用程式以查看此輸出。

## <a name="set-the-maximum-size-for-a-file-share"></a>設定檔案共用的大小上限

從 Azure 儲存體用戶端程式庫的5.x 版開始，您可以設定檔案共用的配額（大小上限）。 您也可以檢查有多少資料目前儲存在共用上。

設定共用的配額會限制儲存在共用上的檔案大小總計。 如果共用上的檔案大小總計超過共用上設定的配額，則用戶端無法增加現有檔案的大小。 除非這些檔案是空的，否則用戶端無法建立新檔案。

下列範例示範如何檢查共用的目前使用狀況，以及如何設定共用的配額。

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>產生檔案或檔案共用的共用存取簽章

從 Azure 儲存體用戶端程式庫 5.x 版開始，您可以產生檔案共用或個別檔案的共用存取簽章 (SAS)。 您也可以在檔案共用上建立預存存取原則，以管理共用存取簽章。 我們建議您建立預存存取原則，因為它可讓您在 SAS 遭到入侵時撤銷它。

下列範例會在共用上建立預存存取原則。 此範例會使用該原則，為共用中的檔案提供 SAS 的條件約束。

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

如需建立和使用共用存取簽章的詳細資訊，請參閱[共用存取簽章的運作方式](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works)。

## <a name="copy-files"></a>複製檔案

從 Azure 儲存體用戶端程式庫 5.x 版開始，您可以將檔案複製到另一個檔案、將檔案複製到 Blob 或將 Blob 複製到檔案。 在接下來的章節中，我們會示範如何以程式設計方式執行這些複製作業。

您也可以使用 AzCopy 將一個檔案複製到另一個檔案，或將 blob 複製到檔案或另一種方式。 請參閱[開始使用 AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

> [!NOTE]
> 如果要將 Blob 複製到檔案，或將檔案複製到 Blob，您必須使用共用存取簽章 (SAS) 來授權來源物件的存取全，即使是在相同的儲存體帳戶內進行複製也一樣。
>

### <a name="copy-a-file-to-another-file"></a>將檔案複製到另一個檔案

下列範例會將檔案複製到相同共用中的另一個檔案。 因為這項複製作業會在相同儲存體帳戶中的檔案之間複製，所以您可以使用共用金鑰驗證來進行複製。

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

### <a name="copy-a-file-to-a-blob"></a>將檔案複製到 blob

下列範例會建立檔案，並將其複製到相同儲存體帳戶內的 Blob。 此範例會建立來源檔案的 SAS，供服務用來在複製作業期間授權來源檔案存取權。

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

您可以用相同方式將 Blob 複製到檔案。 如果來源物件為 Blob，則請建立 SAS，以便在複製作業期間授權該 Blob 存取權。

## <a name="share-snapshots"></a>共用快照集

從 Azure 儲存體用戶端程式庫的 8.5 版開始，您可以建立共用快照集。 您也可以列出或瀏覽共用快照集，並將共用快照集刪除。 共用快照集是唯讀的，因此共用快照集上不允許任何寫入作業。

### <a name="create-share-snapshots"></a>建立共用快照集

下列範例會建立檔案共用快照集。

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

### <a name="list-share-snapshots"></a>列出共用快照集

下列範例會列出共用上的共用快照集。

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

### <a name="browse-files-and-directories-within-share-snapshots"></a>流覽共用快照集內的檔案和目錄

下列範例會瀏覽共用快照集內的檔案和目錄。

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

### <a name="list-shares-and-share-snapshots-and-restore-file-shares-or-files-from-share-snapshots"></a>列出共用和共用快照集，並從共用快照集還原檔案共用或檔案

擷取檔案共用的快照集可讓您在未來將個別檔案或整個檔案共用復原。

您可以從檔案共用快照集還原檔案，方法是查詢檔案共用的共用快照集。 然後，您就可以抓取屬於特定共用快照集的檔案。 請使用該版本直接讀取和比較或進行還原。

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

### <a name="delete-share-snapshots"></a>刪除共用快照集

下列範例會刪除檔案共用快照集。

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## 使用計量針對 Azure 檔案儲存體進行疑難排解<a name="troubleshooting-azure-files-using-metrics"></a>

Azure 儲存體分析現在支援 Azure 檔案服務的計量。 利用度量資料，您可以追蹤要求及診斷問題。

您可以從[Azure 入口網站](https://portal.azure.com)啟用 Azure 檔案儲存體的計量。 您也可以用程式設計方式啟用計量，方法是在儲存體用戶端程式庫中，使用 REST API 或其中一個類比呼叫 [設定檔案服務屬性] 作業。

下列程式碼範例會示範如何使用適用於 .NET 的儲存體用戶端程式庫，啟用 Azure 檔案服務的計量。

首先，將下列 `using` 指示詞新增至您的 `Program.cs` 檔案，以及您在上方新增的指示詞：

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

雖然 Azure Blob、Azure 資料表和 Azure 佇列會在 `Microsoft.Azure.Storage.Shared.Protocol` 命名空間中使用共用的 `ServiceProperties` 類型，Azure 檔案儲存體會使用其本身的類型，也就是 `Microsoft.Azure.Storage.File.Protocol` 命名空間中的 `FileServiceProperties` 類型。 不過，您必須從程式碼參考這兩個命名空間，才能編譯下列程式碼。

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

如果您遇到任何問題，您可以參閱[疑難排解 Windows 中的 Azure 檔案儲存體問題](storage-troubleshoot-windows-file-connection-problems.md)。

## <a name="next-steps"></a>後續步驟

如需 Azure 檔案儲存體的詳細資訊，請參閱下列資源：

### <a name="conceptual-articles-and-videos"></a>概念性文章和影片

* [Azure 檔案服務：相容於 Windows 和 Linux 的雲端 SMB 檔案系統](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [搭配 Linux 使用 Azure 檔案儲存體](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>檔案儲存體的工具支援

* [開始使用 AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [使用 Azure CLI 搭配 Azure 儲存體](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [針對 Windows 中的 Azure 檔案服務問題進行疑難排解](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>參考

* [適用于 .NET 的 Azure 儲存體 Api](/dotnet/api/overview/azure/storage)
* [檔案服務 REST API](/rest/api/storageservices/File-Service-REST-API)

### <a name="blog-posts"></a>部落格文章

* [Azure 檔案儲存體現已正式推出](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Azure 檔案儲存體內部](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Microsoft Azure Files 服務簡介](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [保留與 Microsoft Azure 檔案的連線](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
