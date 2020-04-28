---
title: 如何使用 C++ 的物件 (Blob) 儲存體 - Azure | Microsoft Docs
description: 使用 Azure Blob (物件) 儲存體在雲端中儲存非結構化資料。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/21/2018
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 0a9015e33f5456efeac7f7c887995ac4a69f0259
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75941817"
---
# <a name="how-to-use-blob-storage-from-c"></a>如何使用 C++ 的 Blob 儲存體

本指南會示範如何使用 Azure Blob 儲存體儲存體執行一般案例。 範例將說明如何上傳、列出、下載及刪除 Blob。 這些範例均以 C++ 撰寫，並使用 [Azure Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)。   

若要深入了解 Blob 儲存體，請參閱 [Azure Blob 儲存體簡介](storage-blobs-introduction.md)。

> [!NOTE]
> 本指南以 Azure Storage Client Library for C++ 1.0.0 版和更新版本為對象。 Microsoft 建議使用可透過 [NuGet](https://www.nuget.org/packages/wastorage) 或 [GitHub](https://github.com/Azure/azure-storage-cpp) 取得的 C++ 最新版儲存體用戶端程式庫。

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>建立 C++ 應用程式
在本指南中，您將使用可以在 C++ 應用程式內執行的儲存體功能。  

若要這樣做，您需要安裝 Azure Storage Client Library for C++，並在 Azure 訂用帳戶中建立 Azure 儲存體帳戶。   

若要安裝 Azure Storage Client Library for C++，您可以使用下列方法：

* **Linux：** 請遵循[Azure 儲存體 Client Library For c + + 讀我檔案：在 Linux 上消費者入門](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux)頁面中提供的指示。
* **Windows：** 在 Windows 上，請使用[vcpkg](https://github.com/microsoft/vcpkg)做為相依性管理員。 請依照[快速入門](https://github.com/microsoft/vcpkg#quick-start)來初始化 vcpkg。 然後，使用下列命令安裝二進位檔：

```powershell
.\vcpkg.exe install azure-storage-cpp
```

您可以在[自述](https://github.com/Azure/azure-storage-cpp#download--install)檔中找到如何建立原始程式碼並匯出至 NuGet 的指南。

## <a name="configure-your-application-to-access-blob-storage"></a>設定您的應用程式以存取 Blob 儲存體
在您要使用 Azure 儲存體 API 來存取 Blob 的 C++ 檔案頂端，加入下列 include 陳述式：  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>設定 Azure 儲存體連接字串
Azure 儲存體用戶端會使用儲存體連接字串來儲存存取資料管理服務時所用的端點與認證。 在用戶端應用程式中執行時，您必須以下列格式提供儲存體連接字串 (其中的 *AccountName* 和 *AccountKey* 值要使用您儲存體帳戶的名稱，以及在 [Azure 入口網站](https://portal.azure.com)中針對該儲存體帳戶而列出的儲存體存取金鑰)。 如需有關儲存體帳戶和存取金鑰的資訊，請參閱[關於 Azure 儲存體帳戶](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。 本範例將示範如何宣告靜態欄位來存放連接字串：  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

若要在本機 Windows 電腦中測試您的應用程式，可以使用隨 [Azure SDK](https://azure.microsoft.com/downloads/) 一起安裝的 Microsoft Azure [儲存體模擬器](../storage-use-emulator.md)。 儲存體模擬器是一個公用程式，可在本機開發電腦上模擬 Azure 提供的 Blob、佇列和表格服務。 下列範例示範如何宣告靜態欄位以便將連接字串存放到本機儲存體模擬器中：

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

若要啟動 Azure 儲存體模擬器，請選取 [開始]**** 按鈕或按 **Windows** 鍵。 開始輸入 **Azure 儲存體模擬器**，然後從應用程式清單選取 [Microsoft Azure 儲存體模擬器]****。  

下列範例假設您已經使用這兩個方法之一來取得儲存體連接字串。  

## <a name="retrieve-your-storage-account"></a>取得您的儲存體帳戶
您可以使用**cloud_storage_account**類別來代表您的儲存體帳戶資訊。 若要從儲存體連接字串擷取儲存體帳戶資訊，您可以使用 **parse** 方法。  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

接著，取得 **cloud_blob_client** 類別的參考，因為這可讓您擷取代表 Blob 儲存體中儲存的容器和 Blob 的物件。 下列程式碼會使用我們在前面擷取的儲存體帳戶物件，建立 **cloud_blob_client** 物件：  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>作法：建立容器
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

此範例說明如何建立尚不存在的容器：  

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

根據預設，新容器屬私人性質，您必須指定儲存體存取金鑰才能從此容器下載 Blob。 若要讓所有人都能使用容器中的檔案 (Blob)，您可以使用下列程式碼將容器設定為公用容器：  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

網際網路上的任何人都可以看到公用容器中的 Blob，但要有適當的存取金鑰，才能修改或刪除這些 Blob。  

## <a name="how-to-upload-a-blob-into-a-container"></a>作法：將 Blob 上傳到容器中
Azure Blob 儲存體支援區塊 Blob 和頁面 Blob。 在大多數情況下，建議使用區塊 Blob 的類型。  

若要將檔案上傳至區塊 Blob，請取得容器參照，並使用該參照來取得區塊 Blob 參照。 取得 Blob 參考後，即可藉由呼叫 **upload_from_stream** 方法，將任何資料流上傳至 Blob。 此操作會建立 Blob (如果其並不存在) 或覆寫 Blob (如果其已存在)。 下列範例顯示如何將 Blob 上傳到容器，並假設已建立該容器。  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

或者，您可以使用 **upload_from_file** 方法，將檔案上傳至區塊 Blob。

## <a name="how-to-list-the-blobs-in-a-container"></a>作法：列出容器中的 Blob
若要列出容器中的 Blob，請先取得容器參照。 然後，您可以使用容器的**list_blobs**方法來抓取 blob 和（或）其中的目錄。 若要存取傳回的 **list_blob_item** 中豐富的屬性和方法，您必須呼叫 **list_blob_item.as_blob** 方法來取得 **cloud_blob** 物件，或呼叫 **list_blob.as_directory** 方法來取得 cloud_blob_directory 物件。 下列程式碼示範如何擷取和輸出 **my-sample-container** 容器中每個項目的 URI：

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

如需列出作業的詳細資訊，請參閱 [以 C++ 列出 Azure 儲存體資源](../storage-c-plus-plus-enumeration.md)。

## <a name="how-to-download-blobs"></a>作法：下載 Blob
若要下載 blob，請先取出 blob 參考，然後再呼叫**download_to_stream**方法。 下列範例會使用**download_to_stream**方法，將 blob 內容傳送給資料流程物件，然後您可以將它保存到本機檔案。  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

或者，您可以使用 **download_to_file** 方法，將 Blob 的內容下載到檔案。
此外，您也可以使用 **download_text** 方法，將 Blob 的內容當成文字字串下載。  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>作法：刪除 Blob
若要刪除 Blob，請先取得 Blob 參考，然後針對該參考呼叫 **delete_blob** 方法。  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>後續步驟
了解 Blob 儲存體的基礎概念之後，請依照下列連結深入了解 Azure 儲存體。  

* [如何使用 C++ 的佇列儲存體](../storage-c-plus-plus-how-to-use-queues.md)
* [如何使用 c + + 的表格儲存體](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [列出 c + + 中的 Azure 儲存體資源](../storage-c-plus-plus-enumeration.md)
* [Storage Client Library for c + + 參考](https://azure.github.io/azure-storage-cpp)
* [Azure 儲存體檔](https://azure.microsoft.com/documentation/services/storage/)
* [使用 AzCopy 命令列公用程式傳輸資料](../storage-use-azcopy.md)

