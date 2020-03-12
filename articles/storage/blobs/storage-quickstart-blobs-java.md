---
title: 快速入門：Azure Blob 儲存體程式庫 v12 - Java
description: 在本快速入門中，您將了解如何使用適用於 Java 的 Azure Blob 儲存體用戶端程式庫 12 版，在 Blob (物件) 儲存體中建立容器與 Blob。 接下來，您要了解如何將 Blob 下載到本機電腦，以及如何列出容器中的所有 Blob。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/27/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: c883653754e1f69d3b2d79b256d57a036c70b58e
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330129"
---
# <a name="quickstart-manage-blobs-with-java-v12-sdk"></a>快速入門：使用 Java v12 SDK 來管理 Blob

在本快速入門中，您將了解如何使用 Java 來管理 Blob。 Blob 是可保存大量文字或二進位資料的物件，包括影像、文件、串流媒體和封存資料。 您將上傳、下載及列出 Blob，且將建立和刪除容器。

[API 參考文件](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/index.html) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob) | [套件 (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-blob?repo=jcenter) | [範例](https://docs.microsoft.com/azure/storage/common/storage-samples-java?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Azure 儲存體帳戶。 [建立儲存體帳戶](../common/storage-account-create.md)。
- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) 第 8 版或更新版本。
- [Apache Maven](https://maven.apache.org/download.cgi)。

> [!NOTE]
> 若要開始使用舊版 SDK，請參閱[快速入門：使用 Java v8 SDK 來管理 Blob](storage-quickstart-blobs-java-legacy.md)。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>設定

本節會引導您準備專案以搭配適用於 Java 的 Azure Blob 儲存體用戶端程式庫 v12 使用。

### <a name="create-the-project"></a>建立專案

建立名為 *blob-quickstart-v12* 的 Java 應用程式。

1. 在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 Maven 建立名為 *blob-quickstart-v12* 的新主控台應用程式。 鍵入下列 **mvn** 命令，以建立 "Hello world!" Java 專案。

   ```console
   mvn archetype:generate -DgroupId=com.blobs.quickstart \
                          -DartifactId=blob-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
   ```

1. 產生專案的輸出應該看起來像這樣：

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/blobs/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\QuickStarts\blob-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  7.056 s
    [INFO] Finished at: 2019-10-23T11:09:21-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *blob-quickstart-v12* folder.

   ```console
   cd blob-quickstart-v12
   ```

1. 在 *blob-quickstart-v12* 目錄內，建立另一個名為 *data* 的目錄。 這是將建立和儲存 Blob 資料檔案的位置。

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>安裝套件

在文字編輯器中開啟 *pom.xml* 檔案。 將下列相依性元素加入至相依性群組。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>設定應用程式架構

從專案目錄：

1. 瀏覽至 */src/main/java/com/blobs/quickstart* 目錄
1. 在編輯器中開啟 *App.java* 檔案
1. 刪除 `System.out.println("Hello world!");` 陳述式
1. 新增 `import` 指示詞

此程式碼如下：

```java
package com.blobs.quickstart;

/**
 * Azure blob storage v12 SDK quickstart
 */
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>物件模型

Azure Blob 儲存體經過最佳化，已能妥善儲存大量的非結構化資料。 非結構化資料是指不遵守特定資料模型或定義的資料，例如文字或二進位資料。 Blob 儲存體提供三種類型資源：

* 儲存體帳戶
* 儲存體帳戶中的容器
* 容器中的 Blob

下圖顯示資源之間的關係。

![Blob 儲存體架構圖](./media/storage-blobs-introduction/blob1.png)

使用下列 Java 類別與這些資源互動：

* [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClient.html)：`BlobServiceClient` 類別可讓您操作 Azure 儲存體資源和 Blob 容器。 儲存體帳戶會為 Blob 服務提供最上層命名空間。
* [BlobServiceClientBuilder](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClientBuilder.html)：`BlobServiceClientBuilder` 類別會提供 Fluent 產生器 API，協助設定和具現化 `BlobServiceClient` 物件。
* [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html)：`BlobContainerClient` 類別可讓您操作 Azure 儲存體容器及其 Blob。
* [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html)：`BlobClient` 類別可讓您操作 Azure 儲存體 Blob。
* [BlobItem](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/models/BlobItem.html)：`BlobItem` 類別代表從呼叫 `listBlobsFlat` 傳回的個別 Blob。

## <a name="code-examples"></a>程式碼範例

這些範例程式碼片段會示範如何使用適用於 Java 的 Azure Blob 儲存體用戶端程式庫執行下列動作：

* [取得連接字串](#get-the-connection-string)
* [建立容器](#create-a-container)
* [將 Blob 上傳至容器](#upload-blobs-to-a-container)
* [列出容器中的 Blob](#list-the-blobs-in-a-container)
* [下載 Blob](#download-blobs)
* [刪除容器](#delete-a-container)

### <a name="get-the-connection-string"></a>取得連接字串

下列程式碼會從[設定儲存體連接字串](#configure-your-storage-connection-string)一節中建立的環境變數，擷取儲存體帳戶的連接字串。

在 `Main` 方法內新增此程式碼：

```java
System.out.println("Azure Blob storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>建立容器

決定新容器的名稱。 下列程式碼會將 UUID 值附加到容器名稱，以確保它是唯一的。

> [!IMPORTANT]
> 容器名稱必須是小寫字母。 如需為容器和 Blob 命名的詳細資訊，請參閱[命名和參考容器、Blob 及中繼資料](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

接下來，建立 [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html) 類別的執行個體，然後呼叫 [create](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#create--) 方法，以實際在您的儲存體帳戶中建立容器。

將此程式碼加入到 `Main` 方法的結尾處：

```java
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClientBuilder().connectionString(connectStr).buildClient();

//Create a unique name for the container
String containerName = "quickstartblobs" + java.util.UUID.randomUUID();

// Create the container and return a container client object
BlobContainerClient containerClient = blobServiceClient.createBlobContainer(containerName);
```

### <a name="upload-blobs-to-a-container"></a>將 Blob 上傳至容器

下列程式碼片段：

1. 在本機 *data* 目錄中建立一個文字檔。
1. 從[建立容器](#create-a-container)一節的容器上呼叫 [getBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#getBlobClient-java.lang.String-) 方法，以取得 [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html) 物件的參考。
1. 呼叫 [uploadFromFile](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html#uploadFromFile-java.lang.String-) 方法，將本機文字檔上傳至 Blob。 如果 Blob 不存在，此方法會建立 Blob，若已存在，則不會加以覆寫。

將此程式碼加入到 `Main` 方法的結尾處：

```java
// Create a local file in the ./data/ directory for uploading and downloading
String localPath = "./data/";
String fileName = "quickstart" + java.util.UUID.randomUUID() + ".txt";
File localFile = new File(localPath + fileName);

// Write text to the file
FileWriter writer = new FileWriter(localPath + fileName, true);
writer.write("Hello, World!");
writer.close();

// Get a reference to a blob
BlobClient blobClient = containerClient.getBlobClient(fileName);

System.out.println("\nUploading to Blob storage as blob:\n\t" + blobClient.getBlobUrl());

// Upload the blob
blobClient.uploadFromFile(localPath + fileName);
```

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

呼叫 [list_blobs](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#listBlobs--) 方法，以列出容器中的 Blob。 在此案例中，只有一個 Blob 新增至容器，所以清單作業只會傳回一個 Blob。

將此程式碼加入到 `Main` 方法的結尾處：

```java
System.out.println("\nListing blobs...");

// List the blob(s) in the container.
for (BlobItem blobItem : containerClient.listBlobs()) {
    System.out.println("\t" + blobItem.getName());
}
```

### <a name="download-blobs"></a>下載 Blob

呼叫 [downloadToFile](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/specialized/BlobClientBase.html#downloadToFile-java.lang.String-) 方法，以下載先前建立的 Blob。 此範例程式碼會將 "DOWNLOAD" 的尾碼加入至檔案名稱，讓您可以在本機檔案系統中看到這兩個檔案。

將此程式碼加入到 `Main` 方法的結尾處：

```java
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so that you can see both files.
String downloadFileName = fileName.replace(".txt", "DOWNLOAD.txt");
File downloadedFile = new File(localPath + downloadFileName);

System.out.println("\nDownloading blob to\n\t " + localPath + downloadFileName);

blobClient.downloadToFile(localPath + downloadFileName);
```

### <a name="delete-a-container"></a>刪除容器

下列程式碼會使用 [delete](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#delete--) 方法移除整個容器，以清除應用程式所建立的資源。 它也會刪除應用程式所建立的本機檔案。

應用程式會在刪除 Blob、容器和本機檔案之前呼叫 `System.console().readLine()`，藉以暫停使用者輸入。 您可以利用這個機會，在刪除資源之前先確認這些資源已正確建立。

將此程式碼加入到 `Main` 方法的結尾處：

```java
// Clean up
System.out.println("\nPress the Enter key to begin clean up");
System.console().readLine();

System.out.println("Deleting blob container...");
containerClient.delete();

System.out.println("Deleting the local source and downloaded files...");
localFile.delete();
downloadedFile.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>執行程式碼

此應用程式會在本機資料夾中建立一個測試檔案，並將其上傳到 Blob 儲存體。 範例會接著列出容器中的 Blob，並下載具有新名稱的檔案，您便可比較舊檔案和新檔案。

瀏覽至包含 *pom.xml* 檔案的目錄，然後使用下列 `mvn` 命令來編譯專案。

```console
mvn compile
```

接著，建置封裝。

```console
mvn package
```

執行下列 `mvn` 命令以執行應用程式。

```console
mvn exec:java -Dexec.mainClass="com.blobs.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

應用程式的輸出類似下列範例：

```output
Azure Blob storage v12 - Java quickstart sample

Uploading to Blob storage as blob:
        https://mystorageacct.blob.core.windows.net/quickstartblobsf9aa68a5-260e-47e6-bea2-2dcfcfa1fd9a/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Listing blobs...
        quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Downloading blob to
        ./data/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

在開始清除程序之前，請檢查 *data* 資料夾，找出這兩個檔案。 您可以開啟它們，並觀察它們是否相同。

確認檔案之後，按 **Enter** 鍵以刪除測試檔案並完成示範。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 Java 上傳、下載及列出 Blob。

若要查看 Blob 儲存體範例應用程式，請繼續查看：

> [!div class="nextstepaction"]
> [Azure Blob 儲存體 SDK v12 Java 範例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

* 若要深入了解，請參閱[適用於 Java 的 Azure SDK](https://github.com/Azure/azure-sdk-for-java/blob/master/README.md) \(英文\)。
* 如需教學課程、範例、快速入門及其他文件，請瀏覽[適用於 Java 雲端開發人員的 Azure](/azure/java/)。
