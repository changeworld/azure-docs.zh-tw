---
title: 快速入門：Azure Blob 儲存體程式庫 v12 - Xamarin
description: 在本快速入門中，您將了解如何搭配使用 Azure Blob 儲存體用戶端程式庫 12 版與 Xamarin，在 Blob (物件) 儲存體中建立容器與 Blob。 接下來，您要了解如何將 Blob 下載到行動裝置，以及如何列出容器中的所有 Blob。
author: codemillmatt
ms.author: masoucou
ms.date: 05/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: e0845e7cdc2ce6dc57ed5a18d263f117f0c2005c
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006384"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-with-xamarin"></a>快速入門：Azure Blob 儲存體用戶端程式庫 v12 與 Xamarin

開始使用 Azure Blob 儲存體用戶端程式庫 v12 與 Xamarin。 Azure Blob 儲存體是 Microsoft 針對雲端推出的物件儲存體解決方案。 請依照下列步驟來安裝套件，並試用基本工作的範例程式碼。 Blob 儲存體經過最佳化，已能妥善儲存大量的非結構化資料。

使用 Azure Blob 儲存體用戶端程式庫 v12 與 Xamarin 來：

* 建立容器
* 將 Blob 上傳至 Azure 儲存體
* 列出容器中的所有 Blob
* 將 Blob 下載至您的裝置
* 刪除容器

[API 參考文件](/dotnet/api/azure.storage.blobs) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs) | [套件 (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs) | [範例](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* Azure 儲存體帳戶 - [建立儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Visual Studio (已安裝[適用於 .NET 工作負載的行動開發](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows)) 或 [Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)

## <a name="setting-up"></a>設定
    
本節會引導您準備專案以使用 Azure Blob 儲存體用戶端程式庫 v12 與 Xamarin。
    
### <a name="create-the-project"></a>建立專案

1. 開啟 Visual Studio 並建立空白的 Forms App。
1. 將其命名為：BlobQuickstartV12

### <a name="install-the-package"></a>安裝套件

1. 以滑鼠右鍵按一下 [方案總管] 窗格中的方案，然後選取 [管理方案的 NuGet 套件]。
1. 搜尋 **Azure.Storage.Blobs**，並將最新穩定版安裝到您方案中的所有專案。

### <a name="set-up-the-app-framework"></a>設定應用程式架構

從 **BlobQuickstartV12** 目錄：

1. 在您的編輯器中開啟 *MainPage.xaml* 檔案
1. 移除 `<ContentPage></ContentPage>` 元素之間的所有內容，並取代為下列內容：

```xaml
<StackLayout HorizontalOptions="Center" VerticalOptions="Center">

    <Button x:Name="uploadButton" Text="Upload Blob" Clicked="Upload_Clicked"  IsEnabled="False"/>
    <Button x:Name="listButton" Text="List Blobs" Clicked="List_Clicked"  IsEnabled="False" />
    <Button x:Name="downloadButton" Text="Download Blob" Clicked="Download_Clicked"  IsEnabled="False" />
    <Button x:Name="deleteButton" Text="Delete Container" Clicked="Delete_Clicked" IsEnabled="False" />

    <Label Text="" x:Name="resultsLabel" HorizontalTextAlignment="Center" Margin="0,20,0,0" TextColor="Red" />
        
</StackLayout>
```

[!INCLUDE [storage-quickstart-credentials-xamarin-include](../../../includes/storage-quickstart-credentials-xamarin-include.md)]

## <a name="object-model"></a>物件模型

Azure Blob 儲存體經過最佳化，已能妥善儲存大量的非結構化資料。 非結構化資料是指不遵守特定資料模型或定義的資料，例如文字或二進位資料。 Blob 儲存體提供三種類型資源：

* 儲存體帳戶
* 儲存體帳戶中的容器
* 容器中的 Blob

下圖顯示資源之間的關係。

![Blob 儲存體架構圖](./media/storage-blobs-introduction/blob1.png)

使用下列 .NET 類別與這些資源互動：

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient)：`BlobServiceClient` 類別可讓您操作 Azure 儲存體資源和 Blob 容器。
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient)：`BlobContainerClient` 類別可讓您操作 Azure 儲存體容器及其 Blob。
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)：`BlobClient` 類別可讓您操作 Azure 儲存體 Blob。
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo)：`BlobDownloadInfo` 類別表示從下載 Blob 傳回的屬性和內容。

## <a name="code-examples"></a>程式碼範例

這些範例程式碼片段會示範如何在 Xamarin.Forms 應用程式中使用適用於 .NET 的 Azure Blob 儲存體用戶端程式庫來執行下列工作：

* [建立類別層級變數](#create-class-level-variables)
* [建立容器](#create-a-container)
* [將 Blob 上傳至容器](#upload-blobs-to-a-container)
* [列出容器中的 Blob](#list-the-blobs-in-a-container)
* [下載 Blob](#download-blobs)
* [刪除容器](#delete-a-container)

### <a name="create-class-level-variables"></a>建立類別層級變數

下列程式碼會宣告數個類別層級變數。 在此範例的其餘部分，這些變數需要與 Azure Blob 儲存體通訊。

這些是除了在[設定儲存體連接字串](#configure-your-storage-connection-string)一節所設定的儲存體帳戶連接字串以外的變數。

將此程式碼新增為 *MainPage.xaml.cs* 檔案內的類別層級變數：

```csharp
string storageConnectionString = "{set in the Configure your storage connection string section}";
string fileName = $"{Guid.NewGuid()}-temp.txt";

BlobServiceClient client;
BlobContainerClient containerClient;
BlobClient blobClient;
```

### <a name="create-a-container"></a>建立容器

決定新容器的名稱。 下列程式碼會將 GUID 值附加到容器名稱，以確保它是唯一的。

> [!IMPORTANT]
> 容器名稱必須是小寫字母。 如需為容器和 Blob 命名的詳細資訊，請參閱[命名和參考容器、Blob 及中繼資料](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

建立 [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) 類別的執行個體。 接著，呼叫 [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) 方法，以便在您的儲存體帳戶中建立容器。

將此程式碼新增至 *MainPage.xaml.cs* 檔案：

```csharp
protected async override void OnAppearing()
{            
    string containerName = $"quickstartblobs{Guid.NewGuid()}";
    
    client = new BlobServiceClient(storageConnectionString);
    containerClient = await client.CreateBlobContainerAsync(containerName);

    resultsLabel.Text = "Container Created\n";

    blobClient = containerClient.GetBlobClient(fileName);

    uploadButton.IsEnabled = true;
}
```

### <a name="upload-blobs-to-a-container"></a>將 Blob 上傳至容器

下列程式碼片段：

1. 建立文字的 `MemoryStream`。
1. 藉由呼叫 [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) 類別的 [UploadAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.uploadblobasync?view=azure-dotnet#Azure_Storage_Blobs_BlobContainerClient_UploadBlobAsync_System_String_System_IO_Stream_System_Threading_CancellationToken_) 函式，並同時向其傳遞檔案名稱定義的類別層級變數和文字的 `MemoryStream`，以將文字上傳至 Blob。 如果 Blob 不存在，此方法會建立 Blob，若已存在，則會加以覆寫。

將此程式碼新增至 *MainPage.xaml.cs* 檔案：

```csharp
async void Upload_Clicked(object sender, EventArgs e)
{                                    
    using MemoryStream memoryStream = new MemoryStream(Encoding.UTF8.GetBytes("Hello World!"));

    await containerClient.UploadBlobAsync(fileName, memoryStream);

    resultsLabel.Text += "Blob Uploaded\n";

    uploadButton.IsEnabled = false;
    listButton.IsEnabled = true;
}
```

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

呼叫 [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) 方法，以列出容器中的 Blob。 在此案例中，只有一個 Blob 新增至容器，所以清單作業只會傳回一個 Blob。

將此程式碼新增至 *MainPage.xaml.cs* 檔案：

```csharp
async void List_Clicked(object sender, EventArgs e)
{            
    await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
    {
        resultsLabel.Text += blobItem.Name + "\n";                
    }

    listButton.IsEnabled = false;
    downloadButton.IsEnabled = true;
}
```

### <a name="download-blobs"></a>下載 Blob

呼叫 [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) 方法，以下載先前建立的 Blob。 範例程式碼會先將 Blob 的 `Stream` 表示法複製到 `MemoryStream`，然後再複製到 `StreamReader`，以便顯示文字。

將此程式碼新增至 *MainPage.xaml.cs* 檔案：

```csharp
async void Download_Clicked(object sender, EventArgs e)
{
    BlobDownloadInfo downloadInfo = await blobClient.DownloadAsync();

    using MemoryStream memoryStream = new MemoryStream();
    
    await downloadInfo.Content.CopyToAsync(memoryStream);
    memoryStream.Position = 0;

    using StreamReader streamReader = new StreamReader(memoryStream);

    resultsLabel.Text += "Blob Contents: \n";
    resultsLabel.Text += await streamReader.ReadToEndAsync();
    resultsLabel.Text += "\n";

    downloadButton.IsEnabled = false;
    deleteButton.IsEnabled = true;
}
```

### <a name="delete-a-container"></a>刪除容器

下列程式碼會使用 [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync) 刪除整個容器，以清除應用程式所建立的資源。

應用程式會先提示確認，然後才刪除 Blob 和容器。 您可以利用這個機會，在刪除資源之前先確認這些資源已正確建立。

將此程式碼新增至 *MainPage.xaml.cs* 檔案：

```csharp
async void Delete_Clicked(object sender, EventArgs e)
{            
    var deleteContainer = await Application.Current.MainPage.DisplayAlert("Delete Container",
        "You are about to delete the container proceeed?", "OK", "Cancel");

    if (deleteContainer == false)
        return;

    await containerClient.DeleteAsync();

    resultsLabel.Text += "Container Deleted";

    deleteButton.IsEnabled = false;
}
```

## <a name="run-the-code"></a>執行程式碼

應用程式啟動時會先在顯示時建立容器。 然後您必須按一下按鈕，才能上傳、列出、下載 Blob，以及刪除容器。

若要在 Windows 上執行應用程式，請按 F5。 若要在 Mac 上執行應用程式，請按 Cmd+Enter。

應用程式會在每次作業之後寫入至畫面。 應用程式的輸出類似下列範例：

```output
Container Created
Blob Uploaded
98d9a472-8e98-4978-ba4f-081d69d2e6f8-temp.txt
Blob Contents:
Hello World!
Container Deleted
```

開始清理程序之前，請先確認畫面上 Blob 內容的輸出與已上傳的值相符。

確認值之後，請再確認要刪除容器的提示，然後完成示範。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 Azure Blob 儲存體用戶端程式庫 v12 與 Xamarin 來上傳、下載及列出 Blob。

若要查看 Blob 儲存體範例應用程式，請繼續查看：

> [!div class="nextstepaction"]
> [Azure Blob 儲存體 SDK v12 Xamarin 範例](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

* 如需教學課程、範例、快速入門及其他文件，請瀏覽[適用於行動開發人員的 Azure](/azure/mobile-apps)。
* 若要深入了解 Xamarin，請參閱[開始使用 Xamarin](/xamarin/get-started/)。