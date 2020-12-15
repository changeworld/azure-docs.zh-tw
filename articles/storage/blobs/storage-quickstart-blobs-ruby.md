---
title: 快速入門：Azure Blob 儲存體用戶端程式庫 - Ruby
description: 在物件 Azure Blob 儲存體中建立儲存體帳戶和容器。 使用適用於 Ruby 的儲存體用戶端程式庫建立 Blob、下載 Blob，以及列出容器中的 Blob。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/04/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: ec3fc490466f5fce36b67b2f3744e4ee5cc0ae79
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96781091"
---
# <a name="quickstart-azure-blob-storage-client-library-for-ruby"></a>快速入門：適用於 Ruby 的 Azure Blob 儲存體用戶端程式庫

了解如何使用 Ruby 在 Microsoft Azure Blob 儲存體中建立、下載及列出區塊中的 Blob。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

請確定您已安裝下列額外的必要條件：

- [Ruby](https://www.ruby-lang.org/en/downloads/)
- 使用 [RubyGem 套件](https://rubygems.org/gems/azure-storage-blob)，安裝[適用於 Ruby 的 Azure 儲存體程式庫](https://github.com/azure/azure-storage-ruby)：

    ```console
    gem install azure-storage-blob
    ```

## <a name="download-the-sample-application"></a>下載範例應用程式

本快速入門中使用的[範例應用程式](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git)是基本的 Ruby 應用程式。

使用 [Git](https://git-scm.com/) 將應用程式的複本下載至您的開發環境。 此命令會將存放庫複製到本機的本機電腦：

```console
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git
```

瀏覽至 storage-blobs-ruby-quickstart 資料夾，並在程式碼編輯器中開啟 example.rb 檔案。

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>設定儲存體連接字串

提供儲存體帳戶名稱和帳戶金鑰，以建立應用程式的 [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) 執行個體。

example.rb 檔案中的下列程式碼會將新的 [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) 物件具現化。 使用您的帳戶名稱和金鑰取代 *accountname* 和 *accountkey*。

```ruby
# Create a BlobService object
account_name = "accountname"
account_key = "accountkey"

blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
)
```

## <a name="run-the-sample"></a>執行範例

此範例會在 Blob 儲存體中建立容器、在容器中建立新的 Blob、列出容器中的 Blob，並將 Blob 下載至本機檔案。

執行範例。 以下是執行應用程式的輸出範例：

```console
C:\azure-samples\storage-blobs-ruby-quickstart> ruby example.rb

Creating a container: quickstartblobs18cd9ec0-f4ac-4688-a979-75c31a70503e

Creating blob: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

List blobs in the container following continuation token
        Blob name: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Downloading blob to C:/Users/azureuser/Documents/QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Paused, press the Enter key to delete resources created by the sample and exit the application
```

按 Enter 繼續時，範例程式會刪除儲存體容器和本機檔案。 繼續之前，請檢查已下載檔案的「文件」資料夾。

您也可以使用 [Azure 儲存體總管](https://storageexplorer.com) 檢視 Blob 儲存體帳戶中的檔案。 Azure 儲存體總管是免費的跨平台工具，可讓您存取儲存體帳戶資訊。

確認檔案之後，按 Enter 鍵以刪除測試檔案並結束示範。 開啟 example.rb 檔案以查看程式碼。

## <a name="understand-the-sample-code"></a>了解範例程式碼

接下來，我們將透過範例程式碼來了解其運作方式。

### <a name="get-references-to-the-storage-objects"></a>取得儲存體物件的參考

首先建立用來存取和管理 Blob 儲存體的物件執行個體。 這些物件互為建置基礎。 各物件分別由清單中的下一個物件所使用。

- 建立 Azure 儲存體 [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) 物件的執行個體來設定連線認證。
- 建立 [Container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container/Container) 物件，代表您要存取的容器。 容器是用來組織 Blob，就像在電腦上用資料夾組織檔案一樣。

一旦有了容器物件，您就可以建立 [Block](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) Blob 物件，以指向您感興趣的特定 Blob。 使用 [Block](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) 物件來建立、下載和複製 Blob。

> [!IMPORTANT]
> 容器名稱必須是小寫字母。 如需關於容器和 Blob 名稱的詳細資訊，請參閱[命名和參考容器、Blob 及中繼資料](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

下列範例程式碼：

- 建立新容器
- 設定容器權限，讓 Blob 可供公開使用。 此容器名為 quickstartblobs，並附加唯一的識別碼。

```ruby
# Create a container
container_name = "quickstartblobs" + SecureRandom.uuid
puts "\nCreating a container: " + container_name
container = blob_client.create_container(container_name)

# Set the permission so the blobs are public
blob_client.set_container_acl(container_name, "container")
```

### <a name="create-a-blob-in-the-container"></a>在容器中建立 Blob

Blob 儲存體支援區塊 Blob、附加 Blob 和分頁 Blob。 若要建立 Blob，請呼叫傳入 Blob 資料的 [create_block_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#create_block_blob-instance_method) 方法。

下列範例會在稍早建立的容器中，建立名為 QuickStart_ 的 Blob，其具有唯一識別碼和 *.txt* 副檔名。

```ruby
# Create a new block blob containing 'Hello, World!'
blob_name = "QuickStart_" + SecureRandom.uuid + ".txt"
blob_data = "Hello, World!"
puts "\nCreating blob: " + blob_name
blob_client.create_block_blob(container.name, blob_name, blob_data)
```

區塊 Blob 的大小上限為 4.7 TB，而且可以是 Excel 試算表或大型影片檔案的任何一種。 分頁 Blob 主要用於備份 IaaS 虛擬機器的 VHD 檔案。 附加 Blob 通常用於記錄，例如想寫入檔案，並繼續新增更多資訊時。

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

使用 [list_blobs](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#list_blobs-instance_method) 方法，取得容器中的檔案清單。 下列程式碼會擷取 Blob 清單，然後顯示其名稱。

```ruby
# List the blobs in the container
puts "\nList blobs in the container following continuation token"
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name: #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-a-blob"></a>下載 Blob

使用 [get_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#get_blob-instance_method) 方法，將 Blob 下載至本機磁碟。 下列程式碼會下載前一節中建立的 Blob。

```ruby
# Download the blob

# Set the path to the local folder for downloading
if(is_windows)
    local_path = File.expand_path("~/Documents")
else 
    local_path = File.expand_path("~/")
end

# Create the full path to the downloaded file
full_path_to_file = File.join(local_path, blob_name)

puts "\nDownloading blob to " + full_path_to_file
blob, content = blob_client.get_blob(container_name, blob_name)
File.open(full_path_to_file,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>清除資源

如果不再需要 Blob，請使用 [delete_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#delete_blob-instance_method) 移除。 使用 [delete_container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#delete_container-instance_method) 方法刪除整個容器。 刪除容器也會刪除儲存在容器中的任何 Blob。

```ruby
# Clean up resources, including the container and the downloaded file
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
```

## <a name="resources-for-developing-ruby-applications-with-blobs"></a>可供使用 Blob 開發 Ruby 應用程式的資源

請參閱可使用 Ruby 開發的額外資源：

- 檢視及下載 GitHub 上適用於 Azure 儲存體的 [Ruby 用戶端程式庫原始程式碼](https://github.com/Azure/azure-storage-ruby)。
- 探索使用 Ruby 用戶端程式庫所撰寫的 [Azure 範例](/samples/browse/?products=azure&languages=ruby)。
- [範例：開始以 Ruby 使用 Azure 儲存體](https://github.com/Azure-Samples/storage-blob-ruby-getting-started) \(英文\)

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何使用 Ruby 在 Azure Blob 儲存體和本機磁碟之間傳送檔案。 若要深入了解 Blob 儲存體的用法，請繼續參閱儲存體帳戶概觀。

> [!div class="nextstepaction"]
> [儲存體帳戶概觀](../common/storage-account-overview.md)

如需儲存體總管和 Blob 的詳細資訊，請參閱[使用儲存體總管管理 Azure Blob 儲存體資源](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。
