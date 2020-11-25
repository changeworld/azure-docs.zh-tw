---
title: 快速入門：Azure Blob 儲存體程式庫 v12 - C++
description: 在本快速入門中，您將了解如何使用適用於 C++ 的 Azure Blob 儲存體用戶端程式庫 12 版，在 Blob (物件) 儲存體中建立容器與 Blob。 接下來，您要了解如何將 Blob 下載到本機電腦，以及如何列出容器中的所有 Blob。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/21/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 246d2677c784daee4cc6b083cff04b6993ab3818
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006484"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-c"></a>快速入門：適用於 C++ 的 Azure Blob 儲存體用戶端程式庫 v12

開始使用適用於 C++ 的 Azure Blob 儲存體用戶端程式庫 v12。 Azure Blob 儲存體是 Microsoft 針對雲端推出的物件儲存體解決方案。 請依照下列步驟來安裝套件，並試用基本工作的範例程式碼。 Blob 儲存體經過最佳化，已能妥善儲存大量的非結構化資料。

使用適用於 C++ 的 Azure Blob 儲存體用戶端程式庫 v12：

- 建立容器
- 將 Blob 上傳至 Azure 儲存體
- 列出容器中的所有 Blob
- 將 Blob 下載到本機電腦
- 刪除容器

資源：

- [API 參考文件](https://azure.github.io/azure-sdk-for-cpp/storage.html)
- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage)
- [範例](../common/storage-samples-c-plus-plus.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>必要條件

- [Azure 訂用帳戶](https://azure.microsoft.com/free/)
- [Azure 儲存體帳戶](../common/storage-account-create.md)
- [C++ 編譯器](https://azure.github.io/azure-sdk/cpp_implementation.html#supported-platforms)
- [CMake](https://cmake.org/)
- [Vcpkg - C 和 C++ 套件管理員](https://github.com/microsoft/vcpkg/blob/master/docs/index.md)
- [LibCurl](https://curl.haxx.se/libcurl/)
- [LibXML2](http://www.xmlsoft.org/)

## <a name="setting-up"></a>設定

本節會引導您準備專案以搭配適用於 C++ 的 Azure Blob 儲存體用戶端程式庫 v12 使用。

### <a name="install-the-packages"></a>安裝套件

如果您還沒有這麼做，請使用 `vcpkg install` 命令來安裝 LibCurl 和 LibXML2 套件。

```console
vcpkg.exe install libxml2:x64-windows curl:x64-windows
```

遵循 GitHub 上的指示，取得並建置[適用於 C++ 的 Azure SDK](https://github.com/Azure/azure-sdk-for-cpp/)。

### <a name="create-the-project"></a>建立專案

在 Visual Studio 中，為 Windows 建立名為 BlobQuickstartV12 的新 C++ 主控台應用程式。

:::image type="content" source="./media/quickstart-blobs-c-plus-plus/vs-create-project.jpg" alt-text="用於設定新 C++ Windows 主控台應用程式的 Visual Studio 對話方塊":::

在專案中新增下列程式庫：

- libcurl.lib
- libxml2.lib
- bcrypt.lib
- Crypt32.Lib
- WS2_32.Lib
- azure-core.lib
- azure-storage-common.lib
- azure-storage-blobs.lib

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>物件模型

Azure Blob 儲存體經過最佳化，已能妥善儲存大量的非結構化資料。 非結構化資料是指不遵守特定資料模型或定義的資料，例如文字或二進位資料。 Blob 儲存體提供三種類型資源：

- 儲存體帳戶
- 儲存體帳戶中的容器
- 容器中的 Blob

下圖顯示資源之間的關係。

![Blob 儲存體架構圖](./media/storage-blobs-introduction/blob1.png)

使用下列 C++ 類別與這些資源互動：

- [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_service_client.html)：`BlobServiceClient` 類別可讓您操作 Azure 儲存體資源和 Blob 容器。
- [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html)：`BlobContainerClient` 類別可讓您操作 Azure 儲存體容器及其 Blob。
- [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html)：`BlobClient` 類別可讓您操作 Azure 儲存體 Blob。 這是所有特製化 Blob 類別的基底類別。
- [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html)：`BlockBlobClient` 類別可讓您操作 Azure 儲存體區塊 Blob。

## <a name="code-examples"></a>程式碼範例

這些範例程式碼片段會示範如何使用適用於 C++ 的 Azure Blob 儲存體用戶端程式庫執行下列工作：

- [新增 Include 檔案](#add-include-files)
- [取得連接字串](#get-the-connection-string)
- [建立容器](#create-a-container)
- [將 Blob 上傳至容器](#upload-blobs-to-a-container)
- [列出容器中的 Blob](#list-the-blobs-in-a-container)
- [下載 Blob](#download-blobs)
- [刪除容器](#delete-a-container)

### <a name="add-include-files"></a>新增 Include 檔案

從專案目錄：

1. 在 Visual Studio 中開啟 BlobQuickstartV12.sln 解決方案檔案
1. 在 Visual Studio 中，開啟 BlobQuickstartV12.cpp 來源檔案
1. 移除 `main` 中所有自動產生的程式碼
1. 加入 `#include` 陳述式

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_Includes":::

### <a name="get-the-connection-string"></a>取得連接字串

下列程式碼會從[設定儲存體連接字串](#configure-your-storage-connection-string)中建立的環境變數，擷取儲存體帳戶的連接字串。

將此程式碼新增到 `main()` 中：

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>建立容器

呼叫 [CreateFromConnectionString](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ad103d1e3a7ce7c53a82da887d12ce6fe) 方法以建立 [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html) 類別的執行個體。 接著，呼叫 [Create](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#a22a1e001068a4ec52bb1b6bd8b52c047)，以在您的儲存體帳戶中建立實際的容器。

> [!IMPORTANT]
> 容器名稱必須是小寫字母。 如需為容器和 Blob 命名的詳細資訊，請參閱[命名和參考容器、Blob 及中繼資料](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

將此程式碼新增到 `main()` 的結尾處：

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>將 Blob 上傳至容器

下列程式碼片段：

1. 宣告包含 "Hello Azure！" 的字串。
1. 從[建立容器](#create-a-container)一節的容器上呼叫 [GetBlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#acd8c68e3f37268fde0010dd478ff048f)，以取得 [BlockBlobClientt](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html) 物件的參考。
1. 呼叫 [UploadFrom](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html#af93af7e37f8806e39481596ef253f93d) 函式，將字串上傳至 Blob。 如果 Blob 不存在，此函式會建立 Blob；若已存在，則會加以更新。

將此程式碼新增到 `main()` 的結尾處：

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_UploadBlob":::

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

呼叫 [ListBlobsFlatSegment](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ac7712bc46909dc061d6bf554b496550c) 方法，以列出容器中的 Blob。 只有一個 Blob 新增至容器，所以作業只會傳回該 Blob。

將此程式碼新增到 `main()` 的結尾處：

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>下載 Blob

取得已上傳之 Blob 的屬性。 然後，使用上傳之 Blob 的屬性，宣告新的 `std::string` 物件並調整大小。 藉由在 [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html) 基底類別中呼叫 [DownloadTo](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#aa844f37a8c216f3cb0f27912b114c4d2) 函式，將先前建立的 Blob 下載至新的 `std::string` 物件。 最後，顯示已下載的 Blob 資料。

將此程式碼新增到 `main()` 的結尾處：

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DownloadBlob":::

### <a name="delete-a-blob"></a>刪除 Blob

下列程式碼會藉由呼叫 [BlobClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#a621eabcc8d23893ca1eb106494198615) 函式，從 Azure Blob 儲存體容器中刪除 Blob。

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteBlob":::

### <a name="delete-a-container"></a>刪除容器

下列程式碼會使用 [BlobContainerClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#aa6b1db52697ae92e9a1227e2e02a5178) 刪除整個容器，以清除應用程式所建立的資源。

將此程式碼新增到 `main()` 的結尾處：

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>執行程式碼

此應用程式會建立容器，並將文字檔上傳至 Azure Blob 儲存體。 接著，範例會列出容器中的 Blob、下載檔案，並顯示檔案內容。 最後，應用程式會刪除 Blob 和容器。

應用程式的輸出類似下列範例：

```output
Azure Blob Storage v12 - C++ quickstart sample
Creating container: myblobcontainer
Uploading blob: blob.txt
Listing blobs...
Blob name: blob.txt
Downloaded blob contents: Hello Azure!
Deleting blob: blob.txt
Deleting container: myblobcontainer
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 C++ 上傳、下載及列出 Blob。 您也了解如何建立和刪除 Azure Blob 儲存體容器。

若要查看 C++ Blob 儲存體範例，請繼續參閱：

> [!div class="nextstepaction"]
> [適用於 C++ 的 Azure Blob 儲存體 SDK v12 範例](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-blobs/sample)