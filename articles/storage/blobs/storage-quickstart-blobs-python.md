---
title: 快速入門：Azure Blob 儲存體程式庫 v12 - Python
description: 在本快速入門中，您將了解如何使用適用於 Python 的 Azure Blob 儲存體用戶端程式庫 12 版，在 Blob (物件) 儲存體中建立容器與 Blob。 接下來，您要了解如何將 Blob 下載到本機電腦，以及如何列出容器中的所有 Blob。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: dce42f1b8bfe58e4d147ba1c982cb770590dfa92
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87845114"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>快速入門：使用 Python v12 SDK 來管理 Blob

在本快速入門中，您將了解如何使用 Python 來管理 Blob。 Blob 是可保存大量文字或二進位資料的物件，包括影像、文件、串流媒體和封存資料。 您將上傳、下載及列出 Blob，且將建立和刪除容器。

其他資源：

* [API 參考文件](/python/api/azure-storage-blob)
* [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
* [套件 (Python 套件索引)](https://pypi.org/project/azure-storage-blob/) \(英文\)
* [範例](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Azure 儲存體帳戶。 [建立儲存體帳戶](../common/storage-account-create.md)。
- [Python](https://www.python.org/downloads/) 2.7、3.5 或更新版本。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>設定

本節會引導您準備專案以搭配適用於 Python 的 Azure Blob 儲存體用戶端程式庫 v12 使用。

### <a name="create-the-project"></a>建立專案

建立一個名為 *blob-quickstart-v12* 的 Python 應用程式。

1. 在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為專案建立一個新目錄。

    ```console
    mkdir blob-quickstart-v12
    ```

1. 切換至新建立的 *blob-quickstart-v12* 目錄。

    ```console
    cd blob-quickstart-v12
    ```

1. 在 *blob-quickstart-v12* 目錄內，建立另一個名為 *data* 的目錄。 這是將建立和儲存 Blob 資料檔案的位置。

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>安裝套件

若您仍在應用程式目錄中，請使用 `pip install` 命令安裝適用於 Python 套件的 Azure Blob 儲存體用戶端程式庫。

```console
pip install azure-storage-blob
```

此命令會安裝適用於 Python 套件的 Azure Blob 儲存體用戶端程式庫，以及它所相依的所有程式庫。 在此案例中，這只是適用於 Python 的 Azure 核心程式庫。

### <a name="set-up-the-app-framework"></a>設定應用程式架構

從專案目錄：

1. 在程式碼編輯器中開啟新的文字檔
1. 加入 `import` 陳述式
1. 建立程式的結構，包括基本例外狀況處理

    此程式碼如下：

    ```python
    import os, uuid
    from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

    try:
        print("Azure Blob storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

1. 將新檔案以 *blob-quickstart-v12.py* 儲存在 *blob-quickstart-v12* 目錄中。

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>物件模型

Azure Blob 儲存體經過最佳化，已能妥善儲存大量的非結構化資料。 非結構化資料是指不遵守特定資料模型或定義的資料，例如文字或二進位資料。 Blob 儲存體提供三種類型資源：

* 儲存體帳戶
* 儲存體帳戶中的容器
* 容器中的 Blob

下圖顯示資源之間的關係。

![Blob 儲存體架構圖](./media/storage-blobs-introduction/blob1.png)

使用下列 Python 類別與這些資源互動：

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient)：`BlobServiceClient` 類別可讓您操作 Azure 儲存體資源和 Blob 容器。
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient)：`ContainerClient` 類別可讓您操作 Azure 儲存體容器及其 Blob。
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient)：`BlobClient` 類別可讓您操作 Azure 儲存體 Blob。

## <a name="code-examples"></a>程式碼範例

這些範例程式碼片段會示範如何使用適用於 Python 的 Azure Blob 儲存體用戶端程式庫執行下列動作：

* [取得連接字串](#get-the-connection-string)
* [建立容器](#create-a-container)
* [將 Blob 上傳至容器](#upload-blobs-to-a-container)
* [列出容器中的 Blob](#list-the-blobs-in-a-container)
* [下載 Blob](#download-blobs)
* [刪除容器](#delete-a-container)

### <a name="get-the-connection-string"></a>取得連接字串

下列程式碼會從[設定儲存體連接字串](#configure-your-storage-connection-string)一節中建立的環境變數，擷取儲存體帳戶的連接字串。

在 `try` 區塊內加入此程式碼：

```python
# Retrieve the connection string for use with the application. The storage
# connection string is stored in an environment variable on the machine
# running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-container"></a>建立容器

決定新容器的名稱。 下列程式碼會將 UUID 值附加到容器名稱，以確保它是唯一的。

> [!IMPORTANT]
> 容器名稱必須是小寫字母。 如需為容器和 Blob 命名的詳細資訊，請參閱[命名和參考容器、Blob 及中繼資料](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

呼叫 [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) 方法以建立 [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) 類別的執行個體。 接著，呼叫 [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) 方法，以實際在您的儲存體帳戶中建立容器。

將此程式碼加入到 `try` 區塊的結尾處：

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>將 Blob 上傳至容器

下列程式碼片段：

1. 在本機目錄中建立一個文字檔。
1. 從[建立容器](#create-a-container)一節的 [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) 上呼叫 [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) 方法，以取得 [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) 物件的參考。
1. 呼叫 [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) 方法，將本機文字檔上傳至 Blob。

將此程式碼加入到 `try` 區塊的結尾處：

```python
# Create a file in local data directory to upload and download
local_path = "./data"
local_file_name = "quickstart" + str(uuid.uuid4()) + ".txt"
upload_file_path = os.path.join(local_path, local_file_name)

# Write text to the file
file = open(upload_file_path, 'w')
file.write("Hello, World!")
file.close()

# Create a blob client using the local file name as the name for the blob
blob_client = blob_service_client.get_blob_client(container=container_name, blob=local_file_name)

print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)

# Upload the created file
with open(upload_file_path, "rb") as data:
    blob_client.upload_blob(data)
```

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

呼叫 [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) 方法，以列出容器中的 Blob。 在此案例中，只有一個 Blob 新增至容器，所以清單作業只會傳回一個 Blob。

將此程式碼加入到 `try` 區塊的結尾處：

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>下載 Blob

呼叫 [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) 方法，以下載先前建立的 Blob。 此範例程式碼會將 "DOWNLOAD" 的尾碼加入至檔案名稱，讓您可以在本機檔案系統中看到這兩個檔案。

將此程式碼加入到 `try` 區塊的結尾處：

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in the data directory
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>刪除容器

下列程式碼會使用 [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) 方法移除整個容器，以清除應用程式所建立的資源。 您也可以視需要刪除本機檔案。

應用程式會在刪除 Blob、容器和本機檔案之前呼叫 `input()`，藉以暫停使用者輸入。 您可以利用這個機會，在刪除資源之前先確認這些資源已正確建立。

將此程式碼加入到 `try` 區塊的結尾處：

```python
# Clean up
print("\nPress the Enter key to begin clean up")
input()

print("Deleting blob container...")
container_client.delete_container()

print("Deleting the local source and downloaded files...")
os.remove(upload_file_path)
os.remove(download_file_path)

print("Done")
```

## <a name="run-the-code"></a>執行程式碼

此應用程式會在本機資料夾中建立一個測試檔案，並將其上傳到 Blob 儲存體。 範例會接著列出容器中的 Blob，並下載具有新名稱的檔案，您便可比較舊檔案和新檔案。

瀏覽至包含 *blob-quickstart-v12.py* 檔案的目錄，然後執行下列 `python` 命令來執行應用程式。

```console
python blob-quickstart-v12.py
```

應用程式的輸出類似下列範例：

```output
Azure Blob storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

在開始清除程序之前，請檢查 *data* 資料夾，找出這兩個檔案。 您可以開啟它們，並觀察它們是否相同。

確認檔案之後，按 **Enter** 鍵以刪除測試檔案並完成示範。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 Python 上傳、下載及列出 Blob。

若要查看 Blob 儲存體範例應用程式，請繼續查看：

> [!div class="nextstepaction"]
> [Azure Blob 儲存體 SDK v12 Python 範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* 若要深入了解，請參閱[適用於 Python 的 Azure 儲存體用戶端程式庫](/azure/developer/python/sdk/storage/overview?view=storage-py-v12)。
* 如需教學課程、範例、快速入門及其他文件，請瀏覽[適用於 Python 開發人員的 Azure](/azure/python/)。
