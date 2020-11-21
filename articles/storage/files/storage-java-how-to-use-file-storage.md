---
title: 使用 Java 開發 Azure 檔案服務 | Microsoft Docs
description: 了解如何開發使用 Azure 檔案服務來儲存檔案資料的 Java 應用程式和服務。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 25baa278961b93b04e60f2e997b98753cb6cf3ab
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024104"
---
# <a name="develop-for-azure-files-with-java"></a>使用 Java 開發 Azure 檔案服務

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

瞭解開發使用 Azure 檔案儲存體儲存資料的 JAVA 應用程式的基本概念。 建立主控台應用程式，並瞭解使用 Azure 檔案儲存體 Api 的基本動作：

- 建立及刪除 Azure 檔案共用
- 建立及刪除目錄
- 列舉 Azure 檔案共用的檔案和目錄
- 上傳、下載及刪除檔案

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="create-a-java-application"></a>建立 Java 應用程式

若要建立範例，您將需要 JAVA 開發工具組 (JDK) 和 [適用于 java 的 AZURE 儲存體 SDK](https://github.com/azure/azure-sdk-for-java)。 您也應該建立 Azure 儲存體帳戶。

## <a name="set-up-your-application-to-use-azure-files"></a>設定您的應用程式以使用 Azure 檔案服務

若要使用 Azure 檔案儲存體 Api，請將下列程式碼新增至您想要從中存取 Azure 檔案儲存體的 JAVA 檔案頂端。

# <a name="java-v12"></a>[JAVA v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v11"></a>[JAVA v11](#tab/java11)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>設定 Azure 儲存體連接字串

若要使用 Azure 檔案服務，您必須連接到您的 Azure 儲存體帳戶。 設定連接字串，並使用它來連接到您的儲存體帳戶。 定義靜態變數來保存連接字串。

# <a name="java-v12"></a>[JAVA v12](#tab/java)

*\<storage_account_name\>* *\<storage_account_key\>* 以您儲存體帳戶的實際值取代和。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v11"></a>[JAVA v11](#tab/java11)

以儲存體帳戶的實際值取代 *your_storage_account_name* 和 *your_storage_account_key* 。

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-azure-files-storage"></a>存取 Azure 檔案儲存體儲存體

# <a name="java-v12"></a>[JAVA v12](#tab/java)

若要存取 Azure 檔案儲存體，請建立 [ShareClient](/java/api/com.azure.storage.file.share.shareclient) 物件。 使用 [ShareClientBuilder](/java/api/com.azure.storage.file.share.shareclientbuilder) 類別來建立新的 **ShareClient** 物件。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="java-v11"></a>[JAVA v11](#tab/java11)

若要存取您的儲存體帳戶，請使用 **>cloudstorageaccount** 物件，並將連接字串傳遞至其 **parse** 方法。

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** 會擲回 InvalidKeyException，因此您必須將其放在 try/catch 區塊內。

---

## <a name="create-a-file-share"></a>建立檔案共用

Azure 檔案儲存體中的所有檔案和目錄都會儲存在名為共用的容器中。

# <a name="java-v12"></a>[JAVA v12](#tab/java)

如果共用已存在，則 [ShareClient](/java/api/com.azure.storage.file.share.shareclient.create) 方法會擲回例外狀況。 在區塊中放置要 **建立** 的呼叫 `try/catch` ，並處理例外狀況。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="java-v11"></a>[JAVA v11](#tab/java11)

若要取得共用及其內容的存取權，請建立 Azure 檔案儲存體用戶端。

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

使用 Azure 檔案服務用戶端時，您即可取得共用的參考。

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

若要實際建立共用，請使用 **CloudFileShare** 物件的 **>createifnotexists** 方法。

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

此時， **共用** 會保留名為 **sample share** 的共用參考。

---

## <a name="delete-a-file-share"></a>刪除檔案共用

下列範例程式碼會刪除檔案共用。

# <a name="java-v12"></a>[JAVA v12](#tab/java)

藉由呼叫 [ShareClient](/java/api/com.azure.storage.file.share.shareclient.delete) 方法來刪除共用。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="java-v11"></a>[JAVA v11](#tab/java11)

在 **CloudFileShare** 物件上呼叫 **deleteIfExists** 方法，以刪除共用。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

---

## <a name="create-a-directory"></a>建立目錄

將檔案放在子目錄中，而不是將所有檔案都放在根目錄中，藉以整理儲存體。

# <a name="java-v12"></a>[JAVA v12](#tab/java)

下列程式碼會藉由呼叫 [ShareDirectoryClient](/java/api/com.azure.storage.file.share.sharedirectoryclient.create)來建立目錄。 範例方法 `Boolean` 會傳回值，指出是否已成功建立目錄。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="java-v11"></a>[JAVA v11](#tab/java11)

下列程式碼會在根目錄底下建立名為 **>sampledir** 的子目錄。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

---

## <a name="delete-a-directory"></a>刪除目錄

刪除目錄是一項簡單的工作。 您無法刪除仍然包含檔案或子目錄的目錄。

# <a name="java-v12"></a>[JAVA v12](#tab/java)

如果目錄不存在或不是空的，則 [ShareDirectoryClient](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete) 方法會擲回例外狀況。 在區塊中放置要 **刪除** 的呼叫 `try/catch` ，並處理例外狀況。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="java-v11"></a>[JAVA v11](#tab/java11)

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>列舉 Azure 檔案共用的檔案和目錄

# <a name="java-v12"></a>[JAVA v12](#tab/java)

藉由呼叫 [ShareDirectoryClient listFilesAndDirectories](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories)，取得檔案和目錄的清單。 方法會傳回您可以逐一查看的 [ShareFileItem](/java/api/com.azure.storage.file.share.models.sharefileitem) 物件清單。 下列程式碼會列出 *dirName* 參數所指定之目錄內的檔案和目錄。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="java-v11"></a>[JAVA v11](#tab/java11)

藉由呼叫 **CloudFileDirectory** 參考上的 **listFilesAndDirectories** ，取得檔案和目錄的清單。 方法會傳回您可以逐一查看的 **ListFileItem** 物件清單。 下列程式碼會列出根目錄內的檔案和目錄。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

---

## <a name="upload-a-file"></a>上傳檔案

瞭解如何從本機儲存體上傳檔案。

# <a name="java-v12"></a>[JAVA v12](#tab/java)

下列程式碼會藉由呼叫 [ShareFileClient uploadFromFile](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile) 方法，將本機檔案上傳至 Azure 檔案儲存體。 下列範例方法 `Boolean` 會傳回值，指出是否已成功上傳指定的檔案。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="java-v11"></a>[JAVA v11](#tab/java11)

藉由呼叫共用物件上的 **getRootDirectoryReference** 方法，取得將上傳檔案之目錄的參考。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

現在您具有共用之根目錄的參考，您可以使用下列程式碼上傳檔案。

```java
// Define the path to a local file.
final String filePath = "C:\\temp\\Readme.txt";

CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
cloudFile.uploadFromFile(filePath);
```

---

## <a name="download-a-file"></a>下載檔案

其中一個較常見的作業是從 Azure 檔案儲存體儲存體下載檔案。

# <a name="java-v12"></a>[JAVA v12](#tab/java)

下列範例會將指定的檔案下載至 *>destdir* 參數中指定的本機目錄。 範例方法會在前面加上日期和時間，以使下載的檔案名成為唯一的。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="java-v11"></a>[JAVA v11](#tab/java11)

下列範例會下載 SampleFile.txt，並顯示其內容。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

---

## <a name="delete-a-file"></a>刪除檔案

另一個常見的 Azure 檔案服務作業是刪除檔案。

# <a name="java-v12"></a>[JAVA v12](#tab/java)

下列程式碼會刪除指定的指定檔案。 首先，此範例會根據 *dirName* 參數來建立 [ShareDirectoryClient](/java/api/com.azure.storage.file.share.sharedirectoryclient) 。 然後，程式碼會根據 *fileName* 參數，從目錄用戶端取得 [ShareFileClient](/java/api/com.azure.storage.file.share.sharefileclient) 。 最後，範例方法會呼叫 [ShareFileClient](/java/api/com.azure.storage.file.share.sharefileclient.delete) 來刪除檔案。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="java-v11"></a>[JAVA v11](#tab/java11)

下列程式碼會刪除儲存在名為 **sampledir** 之目錄內名為 SampleFile.txt 的檔案。

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

---

## <a name="next-steps"></a>下一步

如果您想要深入了解其他 Azure 儲存體 API，請參考下列連結。

- [適用於 Java 開發人員的 Azure](/azure/developer/java)
- [Azure SDK for Java](https://github.com/azure/azure-sdk-for-java)
- [適用于 Android 的 Azure SDK](https://github.com/azure/azure-sdk-for-android)
- [適用于 JAVA SDK 的 Azure 檔案共用用戶端程式庫參考](/java/api/overview/azure/storage-file-share-readme)
- [Azure 儲存體服務 REST API](/rest/api/storageservices/)
- [Azure 儲存體團隊部落格](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [使用 AzCopy 命令列公用程式傳輸資料](../common/storage-use-azcopy-v10.md)
- [針對 Azure 檔案服務問題進行疑難排解 - Windows](storage-troubleshoot-windows-file-connection-problems.md)
