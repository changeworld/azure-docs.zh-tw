---
title: Azure 快速入門 - 使用 Go 在物件儲存體中建立 Blob | Microsoft Docs
description: 在本快速入門中，您會在物件 (Blob) 儲存體中建立儲存體帳戶和容器。 然後，使用 Go 的儲存體用戶端程式庫將 blob 上傳至 Azure 儲存體、下載 blob，以及列出容器中的 blob。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: bf0e868e9ee746da1dfe1b03403d21f7edb3bd5e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544644"
---
# <a name="quickstart-upload-download-and-list-blobs-using-go"></a>快速入門：使用 Go 上傳、下載及列出 Blob

在本快速入門中，您會了解如何使用 Go 程式設計語言在 Azure Blob 儲存體容器中上傳、下載及列出區塊 Blob。 

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

請確定您已安裝下列額外的必要條件：
 
* [Go 1.8 或更新版本](https://golang.org/dl/)
* 使用下列命令，安裝 [Azure Storage Blob SDK for Go](https://github.com/azure/azure-storage-blob-go/)：

    ```
    go get -u github.com/Azure/azure-storage-blob-go/azblob
    ``` 

    > [!NOTE]
    > 務必將 URL 中的 `Azure` 變成大寫，以避免在使用 SDK 時發生與大小寫相關的匯入問題。 此外，將您匯入陳述式中的 `Azure` 變成大寫。
    
## <a name="download-the-sample-application"></a>下載範例應用程式
本快速入門中使用的[範例應用程式](https://github.com/Azure-Samples/storage-blobs-go-quickstart.git)是基本的 Go 應用程式。  

使用 [git](https://git-scm.com/) 將應用程式的複本下載至您的開發環境。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-go-quickstart 
```

此命令會將存放庫複製到本機的 git 資料夾。 若要開啟 Blob 儲存體的 Go 範例，請尋找 storage-quickstart.go 檔案。  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>設定儲存體連接字串
此解決方案要求將儲存體帳戶名稱和金鑰安全地儲存在執行範例之電腦本機的環境變數。 視您的作業系統而定，遵循以下其中一個範例來建立環境變數。

# <a name="linux"></a>[Linux](#tab/linux)

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

# <a name="windows"></a>[Windows](#tab/windows)

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-sample"></a>執行範例
此範例會在目前的資料夾中建立測試檔案、將測試檔案上傳至 Blob 儲存體、列出容器中的 Blob，並將檔案下載到緩衝區。 

若要執行範例，請發出以下命令： 

```go run storage-quickstart.go```

下列輸出是執行應用程式時所傳回的輸出範例：
  
```
Azure Blob storage quick start sample
Creating a container named quickstart-5568059279520899415
Creating a dummy file to test the upload and download
Uploading the file with blob name: 630910657703031215
Blob name: 630910657703031215
Downloaded the blob: hello world
this is a blob
Press the enter key to delete the sample files, example container, and exit the application.
```
當您按任意鍵繼續時，範例程式會刪除儲存體容器和檔案。 

> [!TIP]
> 您也可以使用 [Azure 儲存體總管](https://storageexplorer.com) 之類的工具來檢視 Blob 儲存體中的檔案。 Azure 儲存體總管是免費的跨平台工具，可讓您存取儲存體帳戶資訊。 
>

## <a name="understand-the-sample-code"></a>了解範例程式碼

接下來，我們將透過範例程式碼來了解其運作方式。

### <a name="create-containerurl-and-bloburl-objects"></a>建立 ContainerURL 和 BlobURL 物件
第一個動作就是建立用來存取和管理 Blob 儲存體的 ContainerURL 和 BlobURL 物件參考。 這些物件會提供低階 API (例如 Create、Upload 和 Download) 以發出 REST API。

* 使用 [**SharedKeyCredential**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#SharedKeyCredential) 結構來儲存您的認證。 

* 使用認證和選項建立 [**管線**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#NewPipeline)。 管線可指定下列項目：HTTP 回應承載的重試原則、記錄、還原序列化等等。  

* 將新的 [**ContainerURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#ContainerURL) 及新的 [**BlobURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#BlobURL) 物件具現化，以在容器 (Create) 和 blob (Upload 和 Download) 上執行作業。


一旦有了 ContainerURL，您就可以將 **BlobURL** 物件具現化，該物件會指向一個 Blob 並執行上傳、下載和複製等作業。

> [!IMPORTANT]
> 容器名稱必須是小寫字母。 如需有關容器和 Blob 名稱的詳細資訊，請參閱[命名和參考容器、Blob 及中繼資料](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

在這一節中，您會建立新容器。 此容器名為 **quickstartblobs-[random string]** 。 

```go 
// From the Azure portal, get your storage account name and key and set environment variables.
accountName, accountKey := os.Getenv("AZURE_STORAGE_ACCOUNT"), os.Getenv("AZURE_STORAGE_ACCESS_KEY")
if len(accountName) == 0 || len(accountKey) == 0 {
    log.Fatal("Either the AZURE_STORAGE_ACCOUNT or AZURE_STORAGE_ACCESS_KEY environment variable is not set")
}

// Create a default request pipeline using your storage account name and account key.
credential, err := azblob.NewSharedKeyCredential(accountName, accountKey)
if err != nil {
    log.Fatal("Invalid credentials with error: " + err.Error())
}
p := azblob.NewPipeline(credential, azblob.PipelineOptions{})

// Create a random string for the quick start container
containerName := fmt.Sprintf("quickstart-%s", randomString())

// From the Azure portal, get your storage account blob service URL endpoint.
URL, _ := url.Parse(
    fmt.Sprintf("https://%s.blob.core.windows.net/%s", accountName, containerName))

// Create a ContainerURL object that wraps the container URL and a request
// pipeline to make requests.
containerURL := azblob.NewContainerURL(*URL, p)

// Create the container
fmt.Printf("Creating a container named %s\n", containerName)
ctx := context.Background() // This example uses a never-expiring context
_, err = containerURL.Create(ctx, azblob.Metadata{}, azblob.PublicAccessNone)
handleErrors(err)
```
### <a name="upload-blobs-to-the-container"></a>將 Blob 上傳到容器

Blob 儲存體支援區塊 Blob、附加 Blob 和分頁 Blob。 最常使用的是區塊 Blob，這也是本快速入門中所使用的。  

若要將檔案上傳至 blob，請使用 **os.Open** 開啟檔案。 您可以接著使用以下其中一個 REST API，將檔案上傳到指定的路徑：Upload (PutBlob)、StageBlock/CommitBlockList (PutBlock/PutBlockList)。 

此外，SDK 還提供以低階 REST API 為基礎的 [高階 API](https://github.com/Azure/azure-storage-blob-go/blob/master/azblob/highlevel.go)。 例如，*UploadFileToBlockBlob_ 函式會使用 StageBlock (PutBlock) 作業同時在多個區塊中上傳一個檔案，以達到最佳輸送量。 如果檔案小於 256 MB，它會改用 Upload (PutBlob )來完成在單一交易中傳輸。

下列範例會將檔案上傳到名為 _*quickstartblobs-[randomstring]** 的容器。

```go
// Create a file to test the upload and download.
fmt.Printf("Creating a dummy file to test the upload and download\n")
data := []byte("hello world this is a blob\n")
fileName := randomString()
err = ioutil.WriteFile(fileName, data, 0700)
handleErrors(err)

// Here's how to upload a blob.
blobURL := containerURL.NewBlockBlobURL(fileName)
file, err := os.Open(fileName)
handleErrors(err)

// You can use the low-level Upload (PutBlob) API to upload files. Low-level APIs are simple wrappers for the Azure Storage REST APIs.
// Note that Upload can upload up to 256MB data in one shot. Details: https://docs.microsoft.com/rest/api/storageservices/put-blob
// To upload more than 256MB, use StageBlock (PutBlock) and CommitBlockList (PutBlockList) functions. 
// Following is commented out intentionally because we will instead use UploadFileToBlockBlob API to upload the blob
// _, err = blobURL.Upload(ctx, file, azblob.BlobHTTPHeaders{ContentType: "text/plain"}, azblob.Metadata{}, azblob.BlobAccessConditions{})
// handleErrors(err)

// The high-level API UploadFileToBlockBlob function uploads blocks in parallel for optimal performance, and can handle large files as well.
// This function calls StageBlock/CommitBlockList for files larger 256 MBs, and calls Upload for any file smaller
fmt.Printf("Uploading the file with blob name: %s\n", fileName)
_, err = azblob.UploadFileToBlockBlob(ctx, file, blobURL, azblob.UploadToBlockBlobOptions{
    BlockSize:   4 * 1024 * 1024,
    Parallelism: 16})
handleErrors(err)
```

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

在 **ContainerURL** 上使用 **ListBlobs** 方法，可取得容器中的檔案清單。 ListBlobs 會從指定的 **Marker** 開始傳回單一 blob 區段 (上限為 5000)。 使用空的 Marker，從頭開始列舉。 系統會依照詞典編纂順序傳回 blob 名稱。 取得區段之後，加以處理，然後再次呼叫 ListBlob 以傳遞先前傳回的 Marker。  

```go
// List the container that we have created above
fmt.Println("Listing the blobs in the container:")
for marker := (azblob.Marker{}); marker.NotDone(); {
    // Get a result segment starting with the blob indicated by the current Marker.
    listBlob, err := containerURL.ListBlobsFlatSegment(ctx, marker, azblob.ListBlobsSegmentOptions{})
    handleErrors(err)

    // ListBlobs returns the start of the next segment; you MUST use this to get
    // the next segment (after processing the current result segment).
    marker = listBlob.NextMarker

    // Process the blobs returned in this result segment (if the segment is empty, the loop body won't execute)
    for _, blobInfo := range listBlob.Segment.BlobItems {
        fmt.Print(" Blob name: " + blobInfo.Name + "\n")
    }
}
```

### <a name="download-the-blob"></a>下載 blob

在 BlobURL 上使用 **Download** 低階函式來下載 blob。 這會傳回 **DownloadResponse** 結構。 在結構上執行 **Body** 函式，以取得 **RetryReader** 串流來讀取資料。 如果讀取時連線失敗，此串流就會發出重新建立連線的要求，並繼續讀取。 若指定 MaxRetryRequests 設為 0 (預設值) 的 RetryReaderOption，則會傳回原始回應主體，而且不會執行重試。 或者，您可以使用高階 API **DownloadBlobToBuffer** 或 **DownloadBlobToFile** 來簡化程式碼。

下列程式碼會使用 **Download** 函式來下載 Blob。 Blob 的內容會寫入緩衝區並顯示於主控台。

```go
// Here's how to download the blob
downloadResponse, err := blobURL.Download(ctx, 0, azblob.CountToEnd, azblob.BlobAccessConditions{}, false)

// NOTE: automatically retries are performed if the connection fails
bodyStream := downloadResponse.Body(azblob.RetryReaderOptions{MaxRetryRequests: 20})

// read the body into a buffer
downloadedData := bytes.Buffer{}
_, err = downloadedData.ReadFrom(bodyStream)
handleErrors(err)
```

### <a name="clean-up-resources"></a>清除資源
如果不再需要本快速入門中上傳的 Blob，您可以使用 **Delete** 方法來刪除整個容器。 

```go
// Cleaning up the quick start by deleting the container and the file created locally
fmt.Printf("Press enter key to delete the sample files, example container, and exit the application.\n")
bufio.NewReader(os.Stdin).ReadBytes('\n')
fmt.Printf("Cleaning up.\n")
containerURL.Delete(ctx, azblob.ContainerAccessConditions{})
file.Close()
os.Remove(fileName)
```

## <a name="resources-for-developing-go-applications-with-blobs"></a>可供使用 Blob 開發 Go 應用程式的資源

請參閱以下可供使用 Go 儲存體進行 PHP 開發的額外資源：

- 檢視及安裝 GitHub 上適用於 Azure 儲存體的 [Go 用戶端程式庫原始程式碼](https://github.com/Azure/azure-storage-blob-go)。
- 探索使用 Go 用戶端程式庫所撰寫的 [Blob 儲存體範例](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#pkg-examples)。

## <a name="next-steps"></a>後續步驟
 
在此快速入門中，您已了解如何使用 Go 在本機磁碟和 Azure Blob 儲存體之間傳輸檔案。 如需有關 Azure 儲存體 Blob SDK 的詳細資訊，請檢視[原始程式碼](https://github.com/Azure/azure-storage-blob-go/)和 [API 參考](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob)。