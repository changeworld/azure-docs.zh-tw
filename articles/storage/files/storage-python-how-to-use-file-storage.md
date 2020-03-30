---
title: 使用 Python 開發 Azure 檔案服務 | Microsoft Docs
description: 了解如何開發使用 Azure 檔案服務的 Python 應用程式和服務來儲存檔案資料。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 139e3009722761172b7bbd57805a7f5b07e55fc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699386"
---
# <a name="develop-for-azure-files-with-python"></a>使用 Python 開發 Azure 檔案服務
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

本教學課程將示範基本概念，說明如何利用 Python 來開發使用 Azure 檔案服務以儲存檔案資料的應用程式或服務。 在本教學課程中，我們將建立簡單的主控台應用程式，並說明如何執行 Python 和 Azure 檔案服務的基本動作：

* 建立 Azure 檔案共用
* 建立目錄
* 列舉 Azure 檔案共用的檔案和目錄
* 上傳、下載及刪除檔案

> [!Note]  
> 由於 Azure 檔案可透過 SMB 來存取，因此可以使用標準 Python I/O 類別和函式撰寫簡單的應用程式，以存取 Azure 檔案共用。 本文將說明如何撰寫使用 Azure 儲存體 Python SDK 的應用程式，它會使用 [Azure 檔案服務 REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) 與 Azure 檔案服務通訊。

## <a name="download-and-install-azure-storage-sdk-for-python"></a>下載並安裝 Azure Storage SDK for Python

[Azure Storage SDK for Python](https://github.com/azure/azure-storage-python) 需要 Python 2.7、3.3、3.4、3.5 或 3.6。
 
## <a name="install-via-pypi"></a>透過 PyPi 安裝

若要透過 Python Package Index (PyPI) 安裝，請輸入：

```bash
pip install azure-storage-file
```

> [!NOTE]
> 如果您要從 Azure Storage SDK for Python 版本 0.36 或更早版本升級，請先使用 `pip uninstall azure-storage` 解除安裝舊版的 SDK，再安裝最新的封裝。

如需替代安裝方法，請瀏覽 [GitHub 上的 Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python/) \(英文\)。

## <a name="view-the-sample-application"></a>檢視範例應用程式
f 要查看和運行演示如何將 Python 與 Azure 檔配合使用的應用程式範例，請參閱[Azure 存儲：在 Python 中開始使用 Azure 檔](https://github.com/Azure-Samples/storage-file-python-getting-started)。 

若要執行範例應用程式，請確定您已安裝 `azure-storage-file` 和 `azure-storage-common` 封裝。

## <a name="set-up-your-application-to-use-azure-files"></a>設定您的應用程式以使用 Azure 檔案服務
將下列內容新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 Python 來源檔案內的頂端附近。

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>設定 Azure 檔案服務的連線 
`FileService` 物件可讓您使用共用、目錄和檔案。 下列程式碼會使用儲存體帳戶名稱和帳戶金鑰來建立 `FileService` 物件。 以您的帳戶名稱和金鑰取代 `<myaccount>` 和 `<mykey>`。

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用
在下列的程式碼範例中，如果共用不存在，您可以使用 `FileService` 物件建立共用。

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>建立目錄
您也可以組織儲存體，方法是將檔案放在子目錄中，而不是將所有檔案都放在根目錄中。 Azure 檔案服務可讓您建立帳戶允許數量的目錄。 下列程式碼會在根目錄底下建立名為 **sampledir** 的子目錄。

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>列舉 Azure 檔案共用的檔案和目錄
若要列出共用中的檔案和目錄，請使用 **list\_directories\_and\_files** 方法。 這個方法會傳回產生器。 下列程式碼會將共用中每個檔案和目錄的 **name** 輸出到主控台。

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>上傳檔案 
Azure 檔案共用至少包含可放置檔案的根目錄。 在本節中，您將學習如何從本機儲存體將檔案上傳至共用的根目錄。

若要建立檔案並上傳資料，請使用 `create_file_from_path`、`create_file_from_stream`、`create_file_from_bytes` 或 `create_file_from_text` 方法。 這些是高階方法，可在資料大小超過 64 MB 時執行必要的區塊化動作。

`create_file_from_path` 會從指定的路徑上傳檔案的內容，`create_file_from_stream` 會從已開啟的檔案/串流上傳內容。 `create_file_from_bytes` 會上傳位元組陣列，`create_file_from_text` 會使用指定的編碼 (預設為 UTF-8) 上傳指定的文字值。

下列範例會將 **sunset.png** 檔案的內容上傳至 **myfile** 檔案中。

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>下載檔案
若要從檔案下載資料，請使用 `get_file_to_path`、`get_file_to_stream`、`get_file_to_bytes` 或 `get_file_to_text`。 這些是高階方法，可在資料大小超過 64 MB 時執行必要的區塊化動作。

下列範例示範如何使用 `get_file_to_path` 下載 **myfile** 檔案的內容，並將其儲存至 **out-sunset.png** 檔案。

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>刪除檔案
最後，若要刪除檔案，請呼叫 `delete_file`。

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>建立共用快照集
您可以針對整個檔案共用，建立一個時間點複本。

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**使用中繼資料建立共用快照集**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>列出共用和快照集 
您可以針對特定的共用，列出所有快照集。

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>瀏覽共用快照集
您可以瀏覽每個共用快照集的內容，以擷取該時間點的檔案和目錄。

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>從共用快照集取得檔案
您可以針對還原案例，從共用快照集下載檔案。

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>刪除單一共用快照集  
您可以刪除單一共用快照集。

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>共用快照集存在時刪除共用
除非先刪除所有快照集，否則無法刪除內含快照集的共用。

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>後續步驟
您現在已經學會如何使用 Python 操作 Azure 檔案服務，請遵循這些連結深入了解。

* [Python 開發人員中心](https://azure.microsoft.com/develop/python/)
* [Azure 存儲服務 REST API](https://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python)
