---
title: Azure Data Lake Storage Gen2 JAVA SDK for files & Acl
description: 使用適用于 JAVA 的 Azure 儲存體程式庫來管理已啟用階層命名空間（HNS）之儲存體帳戶中的目錄和檔案和目錄存取控制清單（ACL）。
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 15bdcbfc8e02ff06e09cb1e2a3d0621cb50e4da4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84466097"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 JAVA 來管理 Azure Data Lake Storage Gen2 中的目錄、檔案和 Acl

本文說明如何使用 JAVA 來建立和管理已啟用階層命名空間（HNS）之儲存體帳戶中的目錄、檔案和許可權。 

[封裝（Maven）](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  | [範例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  | [API 參考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html)  | [Gen1 至 Gen2 對應](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  | [提供意見](https://github.com/Azure/azure-sdk-for-java/issues)反應

## <a name="prerequisites"></a>必要條件

> [!div class="checklist"]
> * Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 已啟用階層命名空間 (HNS) 的儲存體帳戶。 遵循[下列](data-lake-storage-quickstart-create-account.md)指示以建立帳戶。

## <a name="set-up-your-project"></a>設定專案

若要開始使用，請開啟[此頁面](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)，並尋找最新版本的 JAVA 程式庫。 然後，在文字編輯器中開啟*pom.xml*檔案。 加入參考該版本的 dependency 元素。

如果您打算使用 Azure Active Directory （AD）驗證您的用戶端應用程式，請將相依性新增至 Azure 密碼用戶端程式庫。 請參閱[將密碼用戶端程式庫套件新增至您的專案](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project)。

接下來，將這些 imports 語句加入至您的程式碼檔案。

```java
import com.azure.core.credential.TokenCredential;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.RolePermissions;
```

## <a name="connect-to-the-account"></a>連線到帳戶 

若要使用本文中的程式碼片段，您必須建立代表儲存體帳戶的**DataLakeServiceClient**實例。 

### <a name="connect-by-using-an-account-key"></a>使用帳戶金鑰進行連接

這是連接到帳戶最簡單的方式。 

這個範例會使用帳戶金鑰來建立**DataLakeServiceClient**實例。

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>使用 Azure Active Directory （Azure AD）連接

您可以使用[適用于 JAVA 的 Azure 身分識別用戶端程式庫](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)，透過 Azure AD 來驗證您的應用程式。

這個範例會使用用戶端識別碼、用戶端密碼和租使用者識別碼來建立**DataLakeServiceClient**實例。  若要取得這些值，請參閱[從 Azure AD 取得權杖，以從用戶端應用程式授權要求](../common/storage-auth-aad-app.md)。

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> 如需更多範例，請參閱[適用于 JAVA 的 Azure 身分識別用戶端程式庫](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)檔。


## <a name="create-a-file-system"></a>建立檔案系統

檔案系統可做為您的檔案的容器。 您可以藉由呼叫**DataLakeServiceClient. createFileSystem**方法來建立一個。

此範例會建立名為 `my-file-system` 的檔案系統。 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>建立目錄

藉由呼叫**DataLakeFileSystemClient. createDirectory**方法來建立目錄參考。

這個範例會將名為的目錄新增 `my-directory` 至檔案系統，然後新增名為的子目錄 `my-subdirectory` 。 

```java
static public DataLakeDirectoryClient CreateDirectory
(DataLakeServiceClient serviceClient, String fileSystemName){
    
    DataLakeFileSystemClient fileSystemClient =
    serviceClient.getFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.createDirectory("my-directory");

    return directoryClient.createSubDirectory("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>重新命名或移動目錄

藉由呼叫 DataLakeDirectoryClient 重新命名或移動目錄 **。** 傳遞所需目錄的路徑 a 參數。 

這個範例會將子目錄重新命名為名稱 `my-subdirectory-renamed` 。

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-subdirectory-renamed");
}
```

這個範例會將名為的目錄移 `my-subdirectory-renamed` 至名為之目錄的 `my-directory-2` 子目錄。 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>刪除目錄

藉由呼叫**DataLakeDirectoryClient. deleteWithResponse**方法來刪除目錄。

此範例刪除名為 `my-directory` 的目錄。   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>管理目錄 ACL

這個範例會取得並設定名為之目錄的 ACL `my-directory` 。 這個範例提供擁有使用者的讀取、寫入和執行許可權，授與擁有群組 [讀取] 和 [執行] 許可權，並提供其他所有讀取權限。

> [!NOTE]
> 如果您的應用程式使用 Azure Active Directory （Azure AD）來授權存取，請確定您的應用程式用來授權存取的安全性主體已獲指派[儲存體 Blob 資料擁有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要深入了解如何套用 ACL 權限以及變更權限的效果，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);
  
    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);
  
    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);
  
    PathPermissions permissions = new PathPermissions();
  
    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

## <a name="upload-a-file-to-a-directory"></a>將檔案上傳至目錄

首先，建立**DataLakeFileClient**類別的實例，以建立目標目錄中的檔案參考。 藉由呼叫**DataLakeFileClient. append**方法來上傳檔案。 請務必呼叫**DataLakeFileClient. FlushAsync**方法來完成上傳。

這個範例會將文字檔上傳至名為的目錄 `my-directory` 。

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new FileInputStream(file);

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

> [!TIP]
> 如果您的檔案大小很大，您的程式碼就必須對**DataLakeFileClient**方法進行多次呼叫。 請考慮改為使用**DataLakeFileClient. uploadFromFile**方法。 如此一來，您就可以在單一呼叫中上傳整個檔案。 
>
> 如需範例，請參閱下一節。

## <a name="upload-a-large-file-to-a-directory"></a>將大型檔案上傳至目錄

您可以使用**DataLakeFileClient. uploadFromFile**方法來上傳大型檔案，而不需要對**DataLakeFileClient**進行多次呼叫。

```java
static public void UploadFileBulk(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.getFileClient("uploaded-file.txt");

    fileClient.uploadFromFile("C:\\mytestfile.txt");

    }

```


## <a name="manage-a-file-acl"></a>管理檔案 ACL

這個範例會取得並設定名為之檔案的 ACL `upload-file.txt` 。 這個範例提供擁有使用者的讀取、寫入和執行許可權，授與擁有群組 [讀取] 和 [執行] 許可權，並提供其他所有讀取權限。

> [!NOTE]
> 如果您的應用程式使用 Azure Active Directory （Azure AD）來授權存取，請確定您的應用程式用來授權存取的安全性主體已獲指派[儲存體 Blob 資料擁有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要深入了解如何套用 ACL 權限以及變更權限的效果，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

```java
static public void ManageFileACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    PathAccessControl fileAccessControl =
        fileClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = fileAccessControl.getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
           
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);

    PathPermissions permissions = new PathPermissions();

    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

## <a name="download-from-a-directory"></a>從目錄下載

首先，建立代表您要下載之檔案的**DataLakeFileClient**實例。 使用**DataLakeFileClient**讀取檔案。 使用任何 .NET 檔案處理 API，將資料流程中的位元組儲存至檔案。 

```java
static public void DownloadFile(DataLakeFileSystemClient fileSystemClient)
    throws FileNotFoundException, java.io.IOException{

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    File file = new File("C:\\downloadedFile.txt");

    OutputStream targetStream = new FileOutputStream(file);

    fileClient.read(targetStream);

    targetStream.close();
      
}

```

## <a name="list-directory-contents"></a>列出目錄內容

這個範例會列印位於名為的目錄中的每個檔案的名稱 `my-directory` 。

```java
static public void ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient){
        
    ListPathsOptions options = new ListPathsOptions();
    options.setPath("my-directory");
     
    PagedIterable<PathItem> pagedIterable = 
    fileSystemClient.listPaths(options, null);

    java.util.Iterator<PathItem> iterator = pagedIterable.iterator();
       
    PathItem item = iterator.next();

    while (item != null)
    {
        System.out.println(item.getName());


        if (!iterator.hasNext())
        {
            break;
        }
            
        item = iterator.next();
    }

}
```

## <a name="see-also"></a>另請參閱

* [API 參考文件](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html)
* [封裝（Maven）](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [範例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Gen1 至 Gen2 對應](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [已知問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [提供意見反應](https://github.com/Azure/azure-sdk-for-java/issues)