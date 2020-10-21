---
title: 使用 .NET 開發 Azure 檔案服務 | Microsoft Docs
description: 瞭解如何開發使用 Azure 檔案儲存體儲存資料的 .NET 應用程式和服務。
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-csharp
ms.openlocfilehash: 21b407002adce01155b37321c068fb10d2c003f6
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319790"
---
# <a name="develop-for-azure-files-with-net"></a>使用 .NET 開發 Azure 檔案服務

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

瞭解開發使用 [Azure 檔案儲存體](storage-files-introduction.md) 儲存資料之 .net 應用程式的基本概念。 本文說明如何建立簡單的主控台應用程式，以使用 .NET 和 Azure 檔案儲存體來執行下列作業：

- 取得檔案的內容。
- 設定檔案共用的大小上限或配額。
- 建立檔案 (SAS) 的共用存取簽章。
- 將檔案複製到相同儲存體帳戶中的另一個檔案。
- 將檔案複製到相同儲存體帳戶中的 Blob。
- 建立檔案共用的快照集。
- 從共用快照集還原檔案。
- 使用 Azure 儲存體計量來進行疑難排解。

若要深入瞭解 Azure 檔案儲存體，請參閱 [Azure 檔案儲存體是什麼？](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>了解 .NET API

Azure 檔案服務會提供兩種廣泛的方法給用戶端應用程式：伺服器訊息區 (SMB) 和 REST。 在 .NET 中， `System.IO` 和 `Azure.Storage.Files.Shares` api 會將這些方法抽象化。

API | 使用時機 | 注意
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | 您的應用程式： <ul><li>需要使用 SMB 讀取/寫入檔案</li><li>正在可透過連接埠 445 存取您 Azure 檔案服務帳戶的裝置上執行</li><li>不需要管理檔案共用的任何系統管理設定</li></ul> | 使用 Azure 檔案儲存體 over SMB 執行的檔案 i/o 通常與任何網路檔案共用或本機存放裝置的 i/o 相同。 如需 .NET 中許多功能的簡介，包括檔案 i/o，請參閱 [主控台應用程式](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter) 教學課程。
[Azure。共用](/dotnet/api/azure.storage.files.shares) | 您的應用程式： <ul><li>因為防火牆或 ISP 條件約束，所以無法在埠445上使用 SMB 存取 Azure 檔案儲存體</li><li>需要系統管理功能，例如設定檔案共用的配額，或建立共用存取簽章的能力</li></ul> | 本文將示範如何使用 `Azure.Storage.Files.Shares` REST 而非 SMB 和檔案共用的管理來使用檔案 i/o。

## <a name="create-the-console-application-and-obtain-the-assembly"></a>建立主控台應用程式並取得組件

您可以在任何類型的 .NET 應用程式中使用 Azure 檔案儲存體用戶端程式庫。 這些應用程式包括 Azure 雲端、web、桌面和行動應用程式。 在本指南中，我們會建立簡單的主控台應用程式。

在 Visual Studio 中，建立新的 Windows 主控台應用程式。 下列步驟說明如何在 Visual Studio 2019 中建立主控台應用程式。 這些步驟類似其他 Visual Studio 版本中的步驟。

1. 啟動 Visual Studio，然後選取 [建立新專案]。
1. 在 [ **建立新專案**] 中，選擇 [ **主控台應用程式 (] .NET Framework) ** 的 c #，然後選取 **[下一步]**。
1. 在 [ **設定您的新專案**] 中，輸入應用程式的名稱，然後選取 [ **建立**]。

將本文中的所有程式碼範例新增至 `Program` *Program.cs* 檔案中的類別。

## <a name="use-nuget-to-install-the-required-packages"></a>使用 NuGet 來安裝必要的封裝

請在您的專案中參考這些套件：

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

- [適用于 .net 的 azure 核心程式庫](https://www.nuget.org/packages/Azure.Core/)：此套件是 azure 用戶端管線的執行。
- [適用于 .net 的 Azure 儲存體 Blob 用戶端程式庫](https://www.nuget.org/packages/Azure.Storage.Blobs/)：此套件可讓您以程式設計方式存取儲存體帳戶中的 Blob 資源。
- [適用于 .net 的 Azure 儲存體 Files 用戶端程式庫](https://www.nuget.org/packages/Azure.Storage.Files.Shares/)：此套件可讓您以程式設計方式存取儲存體帳戶中的檔案資源。
- [適用于 .net 的系統 Configuration Manager 程式庫](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/)：此套件提供可在設定檔中儲存和取出值的類別。

您可以使用 NuGet 來取得套件。 遵循這些步驟：

1. 在 **方案總管**中，以滑鼠右鍵按一下您的專案，然後選擇 [ **管理 NuGet 套件**]。
1. 在 [NuGet 套件管理員] 中，選取 [瀏覽]。 然後搜尋並選擇 **Azure Core**，然後選取 [ **安裝**]。

   此步驟會安裝封裝及其相依性。

1. 搜尋並安裝這些封裝：

   - **Azure. Blob**
   - **Azure。共用**
   - **System.Configuration.ConfigurationManager**

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [適用于 .net 的 Microsoft Azure 儲存體通用程式庫](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)：此套件可讓您以程式設計方式存取儲存體帳戶中的一般資源。
- [適用于 .net 的 Microsoft Azure 儲存體 blob 程式庫](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)：此封裝可讓您以程式設計方式存取儲存體帳戶中的 blob 資源。
- [適用于 .net 的 Microsoft Azure 儲存體檔案庫](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)：此套件可讓您以程式設計方式存取儲存體帳戶中的檔案資源。
- [適用于 .net 的 Microsoft Azure Configuration Manager 程式庫](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)：此套件提供一個類別，可讓您在應用程式執行所在的位置剖析設定檔中的連接字串。

您可以使用 NuGet 來取得套件。 遵循這些步驟：

1. 在 **方案總管**中，以滑鼠右鍵按一下您的專案，然後選擇 [ **管理 NuGet 套件**]。
1. 在 [NuGet 套件管理員] 中，選取 [瀏覽]。 然後搜尋並選擇 [ **Azure**]，然後選取 [ **安裝**]。

   此步驟會安裝封裝及其相依性。
1. 搜尋並安裝這些封裝：

   - **Microsoft Azure 儲存體。 Common**
   - **Microsoft Azure 檔案儲存體**
   - **Microsoft.Azure.ConfigurationManager**

---

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>將儲存體帳號憑證儲存至 App.config 檔案

接下來，將您的認證儲存在專案的 *App.config* 檔案中。 在 **方案總管**中，按兩下 `App.config` 並編輯檔案，使其類似于下列範例。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

請 `myaccount` 以您的儲存體帳戶名稱取代，並 `mykey` 以您的儲存體帳戶金鑰取代。

:::code language="xml" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/app.config" highlight="5,6,7":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

請 `myaccount` 以您的儲存體帳戶名稱取代，並 `StorageAccountKeyEndingIn==` 以您的儲存體帳戶金鑰取代。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString"
      value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
  </appSettings>
</configuration>
```

---

> [!NOTE]
> Azurite 儲存體模擬器目前不支援 Azure 檔案儲存體。 您的連接字串必須以雲端中的 Azure 儲存體帳戶為目標，才能與 Azure 檔案儲存體搭配運作。

## <a name="add-using-directives"></a>新增 using 指示詞

在 **方案總管**中，開啟 *Program.cs* 檔案，並將下列 using 指示詞新增至檔案頂端。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

---

## <a name="access-the-file-share-programmatically"></a>以程式設計方式存取檔案共用

在 *Program.cs* 檔案中，新增下列程式碼以程式設計方式存取檔案共用。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

下列方法會建立檔案共用（如果尚未存在）。 方法會從連接字串建立 [ShareClient](/dotnet/api/azure.storage.files.shares.shareclient) 物件開始。 然後，此範例會嘗試下載我們稍早建立的檔案。 從呼叫這個方法 `Main()` 。

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShare":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

接下來，將下列內容新增至 `Main()` 方法，在上方顯示的程式碼之後，以取出連接字串。 此程式碼會取得我們稍早建立之檔案的參考，並輸出其內容。

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

---

## <a name="set-the-maximum-size-for-a-file-share"></a>設定檔案共用的大小上限

從 Azure 檔案儲存體用戶端程式庫5.x 版開始，您可以設定檔案共用的配額 (大小上限) 。 您也可以檢查有多少資料目前儲存在共用上。

設定共用配額會限制儲存在共用上的檔案大小總計。 如果共用上的檔案大小總計超過配額，用戶端就無法增加現有檔案的大小。 用戶端也無法建立新檔案，除非這些檔案是空的。

下列範例示範如何檢查共用的目前使用狀況，以及如何設定共用的配額。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_SetMaxShareSize":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

---

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>產生檔案或檔案共用的共用存取簽章

從 Azure 檔案儲存體用戶端程式庫5.x 版開始，您可以為檔案共用或個別檔案產生共用存取簽章 (SAS) 。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

下列範例方法會傳回指定共用中檔案的 SAS。

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_GetFileSasUri":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

您也可以在檔案共用上建立預存存取原則，以管理共用存取簽章。 建議您建立預存存取原則，因為它會讓您在 SAS 遭到入侵時予以撤銷。 下列範例會在共用上建立預存存取原則。 此範例會使用該原則來提供共用中檔案的 SAS 條件約束。

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

---

如需建立和使用共用存取簽章的詳細資訊，請參閱 [共用存取簽章的運作方式](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works)。

## <a name="copy-files"></a>複製檔案

從 Azure 檔案儲存體用戶端程式庫5.x 版開始，您可以將檔案複製到另一個檔案、將檔案複製到 blob，或將 blob 複製到檔案。

您也可以使用 AzCopy 將檔案複製到另一個檔案，或將 blob 複製到檔案，或複製到檔案中。 請參閱[開始使用 AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

> [!NOTE]
> 如果要將 Blob 複製到檔案，或將檔案複製到 Blob，您必須使用共用存取簽章 (SAS) 來授權來源物件的存取全，即使是在相同的儲存體帳戶內進行複製也一樣。

### <a name="copy-a-file-to-another-file"></a>將檔案複製到另一個檔案

下列範例會將檔案複製到相同共用中的另一個檔案。 您可以使用 [共用金鑰驗證](/rest/api/storageservices/authorize-with-shared-key) 來執行複製作業，因為這項作業會複製相同儲存體帳戶內的檔案。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFile":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

---

### <a name="copy-a-file-to-a-blob"></a>將檔案複製到 Blob

下列範例會建立檔案，並將其複製到相同儲存體帳戶內的 Blob。 此範例會建立來源檔案的 SAS，供服務用來在複製作業期間授權來源檔案存取權。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFileToBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

---

您可以用相同方式將 Blob 複製到檔案。 如果來源物件為 Blob，則請建立 SAS，以便在複製作業期間授權該 Blob 存取權。

## <a name="share-snapshots"></a>共用快照集

從 Azure 檔案儲存體用戶端程式庫的8.5 版開始，您可以建立共用快照集。 您也可以列出或瀏覽共用快照集，並將共用快照集刪除。 一旦建立，共用快照集就會是唯讀的。

### <a name="create-share-snapshots"></a>建立共用快照集

下列範例會建立檔案共用快照集。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShareSnapshot":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

---

### <a name="list-share-snapshots"></a>列出共用快照集

下列範例會列出共用上的快照集。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListShareSnapshots":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

---

### <a name="list-files-and-directories-within-share-snapshots"></a>列出共用快照集內的檔案和目錄

下列範例會流覽共用快照集內的檔案和目錄。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListSnapshotContents":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

---

### <a name="restore-file-shares-or-files-from-share-snapshots"></a>從共用快照集還原檔案共用或檔案

取得檔案共用的快照集，可讓您復原個別檔案或整個檔案共用。

您可以從檔案共用快照集還原檔案，方法是查詢檔案共用的共用快照集。 然後，您可以取得屬於特定共用快照集的檔案。 您可以使用該版本直接讀取或還原檔案。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_RestoreFileFromSnapshot":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

---

### <a name="delete-share-snapshots"></a>刪除共用快照集

下列範例會刪除檔案共用快照集。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_DeleteSnapshot":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

---

## <a name="troubleshoot-azure-files-by-using-metrics"></a>使用計量針對 Azure 檔案儲存體進行疑難排解<a name="troubleshooting-azure-files-using-metrics"></a>

Azure 儲存體分析支援 Azure 檔案儲存體的度量。 利用度量資料，您可以追蹤要求及診斷問題。

您可以從 [Azure 入口網站](https://portal.azure.com)啟用 Azure 檔案儲存體的計量。 您也可以透過以程式設計方式啟用計量，方法是在 Azure 檔案儲存體用戶端程式庫中使用 REST API 或其中一個類比來呼叫「配置 [檔案服務屬性](/rest/api/storageservices/set-file-service-properties) 」作業。

下列程式碼範例示範如何使用 .NET 用戶端程式庫來啟用 Azure 檔案儲存體的計量。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UseMetrics":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

首先，將下列指示詞新增 `using` 至您的 *Program.cs* 檔案，以及您在上面新增的指示詞：

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

雖然 Azure Blob、Azure 資料表和 Azure 佇列會 `ServiceProperties` 在命名空間中使用共用類型 `Microsoft.Azure.Storage.Shared.Protocol` ，但 Azure 檔案儲存體會使用它自己的類型，也就是 `FileServiceProperties` 命名空間中的類型 `Microsoft.Azure.Storage.File.Protocol` 。 不過，您必須從程式碼參考這兩個命名空間，才能編譯下列程式碼。

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

---

如果您遇到任何問題，您可以參考 [Windows 中 Azure 檔案儲存體問題的疑難排解](storage-troubleshoot-windows-file-connection-problems.md)。

## <a name="next-steps"></a>後續步驟

如需 Azure 檔案儲存體的詳細資訊，請參閱下列資源：

### <a name="conceptual-articles-and-videos"></a>概念性文章和影片

- [Azure 檔案服務：相容於 Windows 和 Linux 的雲端 SMB 檔案系統](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [搭配 Linux 使用 Azure 檔案儲存體](storage-how-to-use-files-linux.md) (機器翻譯)

### <a name="tooling-support-for-file-storage"></a>檔案儲存體的工具支援

- [開始使用 AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
- [針對 Windows 中的 Azure 檔案服務問題進行疑難排解](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>參考資料

- [適用於 .NET 的 Azure 儲存體 API](/dotnet/api/overview/azure/storage)
- [檔案服務 REST API](/rest/api/storageservices/File-Service-REST-API) \(英文\)
