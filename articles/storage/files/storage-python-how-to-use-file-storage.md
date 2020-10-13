---
title: 使用 Python 開發 Azure 檔案服務 | Microsoft Docs
description: 了解如何開發使用 Azure 檔案服務的 Python 應用程式和服務來儲存檔案資料。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/08/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: 8bef69037fad8bf8ee9537e90f26ca967560b9d2
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876092"
---
# <a name="develop-for-azure-files-with-python"></a>使用 Python 開發 Azure 檔案服務

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

瞭解使用 Python 來開發使用 Azure 檔案儲存體儲存檔案資料的應用程式或服務的基本概念。 建立簡單的主控台應用程式，並瞭解如何使用 Python 和 Azure 檔案儲存體來執行基本動作：

- 建立 Azure 檔案共用
- 建立目錄
- 列舉 Azure 檔案共用的檔案和目錄
- 上傳、下載及刪除檔案
- 使用快照集建立檔案共用備份

> [!NOTE]
> 由於 Azure 檔案可透過 SMB 來存取，因此可以使用標準 Python I/O 類別和函式撰寫簡單的應用程式，以存取 Azure 檔案共用。 本文將說明如何撰寫使用 Azure 檔案儲存體儲存體 Python SDK 的應用程式，它會使用 [Azure 檔案儲存體 REST API](/rest/api/storageservices/file-service-rest-api) 與 Azure 檔案儲存體交談。

## <a name="download-and-install-azure-storage-sdk-for-python"></a>下載並安裝 Azure Storage SDK for Python

> [!NOTE]
> 如果您要從 Azure Storage SDK for Python 版本 0.36 或更早版本升級，請先使用 `pip uninstall azure-storage` 解除安裝舊版的 SDK，再安裝最新的封裝。

# <a name="python-v12"></a>[Python v12](#tab/python)

[適用于 python 的 Azure 檔案儲存體用戶端程式庫 v12](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share)需要 Python 2.7 或3.5。

# <a name="python-v2"></a>[Python v2](#tab/python2)

[Azure Storage SDK for Python](https://github.com/azure/azure-storage-python) 需要 Python 2.7、3.3、3.4、3.5 或 3.6。

---

## <a name="install-via-pypi"></a>透過 PyPI 安裝

若要透過 Python Package Index (PyPI) 安裝，請輸入：

# <a name="python-v12"></a>[Python v12](#tab/python)

```console
pip install azure-storage-file-share
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-file
```

### <a name="view-the-sample-application"></a>檢視範例應用程式

若要查看並執行示範如何使用 Python 搭配 Azure 檔案儲存體的範例應用程式，請參閱 [Azure 儲存體：使用 python 中的 Azure 檔案儲存體消費者入門](https://github.com/Azure-Samples/storage-file-python-getting-started)。

若要執行範例應用程式，請確定您已安裝 `azure-storage-file` 和 `azure-storage-common` 套件。

---

## <a name="set-up-your-application-to-use-azure-files"></a>設定您的應用程式以使用 Azure 檔案服務

將下列程式碼新增至 Python 來源檔案的頂端附近，以使用本文中的程式碼片段。

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_Imports":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
from azure.storage.file import FileService
```

---

## <a name="set-up-a-connection-to-azure-files"></a>設定 Azure 檔案服務的連線

# <a name="python-v12"></a>[Python v12](#tab/python)

[ShareServiceClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareserviceclient) 可讓您使用共用、目錄和檔案。 下列程式碼會 `ShareServiceClient` 使用儲存體帳戶連接字串來建立物件。

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateShareServiceClient":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true)物件可讓您使用共用、目錄和檔案。 下列程式碼會使用儲存體帳戶名稱和帳戶金鑰來建立 `FileService` 物件。 以您的帳戶名稱和金鑰取代 `<myaccount>` 和 `<mykey>`。

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

---

## <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用

# <a name="python-v12"></a>[Python v12](#tab/python)

下列程式碼範例會使用 [ShareClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient) 物件來建立共用（如果不存在的話）。

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateFileShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

下列程式碼範例會使用 [FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) 物件來建立共用（如果不存在的話）。

```python
file_service.create_share('myshare')
```

---

## <a name="create-a-directory"></a>建立目錄

您可以組織儲存體，方法是將檔案放在子目錄中，而不是將所有檔案都放在根目錄中。

# <a name="python-v12"></a>[Python v12](#tab/python)

下列方法會使用 [ShareDirectoryClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharedirectoryclient) 物件，在指定之檔案共用的根目錄中建立目錄。

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateDirectory":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

下列程式碼會在根目錄底下建立名為 *>sampledir* 的子目錄。

```python
file_service.create_directory('myshare', 'sampledir')
```

---

## <a name="upload-a-file"></a>上傳檔案

在本節中，您將瞭解如何將檔案從本機儲存體上傳至 Azure 檔案儲存體。

# <a name="python-v12"></a>[Python v12](#tab/python)

下列方法會將指定檔案的內容上傳到指定的 Azure 檔案共用中的指定目錄。

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_UploadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Azure 檔案共用至少包含一個可存放檔案的根目錄。 若要建立檔案並上傳資料，請使用 [create_file_from_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-path-share-name--directory-name--file-name--local-file-path--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object---timeout-none-)、 [create_file_from_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-stream-share-name--directory-name--file-name--stream--count--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--)、 [create_file_from_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-bytes-share-name--directory-name--file-name--file--index-0--count-none--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--)或 [create_file_from_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-text-share-name--directory-name--file-name--text--encoding--utf-8---content-settings-none--metadata-none--validate-content-false--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--) 方法。 它們是高階方法，可在資料大小超過 64 MB 時執行必要的區塊化。

`create_file_from_path` 會從指定的路徑上傳檔案的內容，`create_file_from_stream` 會從已開啟的檔案/串流上傳內容。 `create_file_from_bytes` 會上傳位元組陣列，`create_file_from_text` 會使用指定的編碼 (預設為 UTF-8) 上傳指定的文字值。

下列範例會將 *sunset.png* 檔案的內容上傳至 **myfile** 檔案中。

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this file in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>列舉 Azure 檔案共用的檔案和目錄

# <a name="python-v12"></a>[Python v12](#tab/python)

若要列出子目錄中的檔案和目錄，請使用 [list_directories_and_files](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#list-directories-and-files-directory-name-none--name-starts-with-none--marker-none----kwargs-) 方法。 這個方法會傳回自動分頁可反復執行。 下列程式碼會將指定目錄中的每個檔案和子目錄的 **名稱** 輸出到主控台。

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListFilesAndDirs":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

若要列出共用中的檔案和目錄，請使用 [list_directories_and_files](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#list-directories-and-files-share-name--directory-name-none--num-results-none--marker-none--timeout-none--prefix-none--snapshot-none-) 方法。 這個方法會傳回產生器。 下列程式碼會將共用中每個檔案和目錄的 **name** 輸出到主控台。

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

---

## <a name="download-a-file"></a>下載檔案

# <a name="python-v12"></a>[Python v12](#tab/python)

若要從檔案下載資料，請使用 [download_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#download-file-offset-none--length-none----kwargs-)。

下列範例將示範 `download_file` 如何使用來取得指定檔案的內容，並將其儲存在**DOWNLOADED-** 本機，並將其儲存在檔案名的前面。

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

若要從檔案下載資料，請使用 [get_file_to_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-path-share-name--directory-name--file-name--file-path--open-mode--wb---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)、 [get_file_to_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-stream-share-name--directory-name--file-name--stream--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)、 [get_file_to_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-bytes-share-name--directory-name--file-name--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)或 [get_file_to_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-text-share-name--directory-name--file-name--encoding--utf-8---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)。 它們是高階方法，可在資料大小超過 64 MB 時執行必要的區塊化。

下列範例示範如何使用 `get_file_to_path` 下載 **myfile** 檔案的內容，並將其儲存至 *out-sunset.png* 檔案。

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

---

## <a name="create-a-share-snapshot"></a>建立共用快照集

您可以針對整個檔案共用，建立一個時間點複本。

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**使用中繼資料建立共用快照集**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

---

## <a name="list-shares-and-snapshots"></a>列出共用和快照集

您可以針對特定的共用，列出所有快照集。

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListSharesAndSnapshots":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

---

## <a name="browse-share-snapshot"></a>瀏覽共用快照集

您可以流覽每個共用快照集，以從該時間點取出檔案和目錄。

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_BrowseSnapshotDir":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

---

## <a name="get-file-from-share-snapshot"></a>從共用快照集取得檔案

您可以從共用快照集下載檔案。 這可讓您還原檔案的先前版本。

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadSnapshotFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

---

## <a name="delete-a-single-share-snapshot"></a>刪除單一共用快照集
您可以刪除單一共用快照集。

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

---

## <a name="delete-a-file"></a>刪除檔案

# <a name="python-v12"></a>[Python v12](#tab/python)

若要刪除檔案，請呼叫 [delete_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#delete-file---kwargs-)。

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

若要刪除檔案，請呼叫 [delete_file](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#delete-file-share-name--directory-name--file-name--timeout-none-)。

```python
file_service.delete_file('myshare', None, 'myfile')
```

---

## <a name="delete-share-when-share-snapshots-exist"></a>共用快照集存在時刪除共用

# <a name="python-v12"></a>[Python v12](#tab/python)

若要刪除包含快照集的共用，請使用呼叫 [delete_share](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#delete-share-delete-snapshots-false----kwargs-) `delete_snapshots=True` 。

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

除非先刪除所有快照集，否則無法刪除內含快照集的共用。

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

---

## <a name="next-steps"></a>後續步驟

您現在已經學會如何使用 Python 操作 Azure 檔案服務，請遵循這些連結深入了解。

- [Python 開發人員中心](/azure/developer/python/)
- [Azure 儲存體服務 REST API](/rest/api/azure/)
- [Microsoft Azure Storage SDK for Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
