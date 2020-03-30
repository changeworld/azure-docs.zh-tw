---
title: Azure 資料存儲 Gen2 JAVA SDK，用於檔& ACL
description: 使用 JAVA 的 Azure 存儲庫管理已啟用階層命名空間 （HNS） 的存儲帳戶中的目錄、檔和目錄存取控制清單 （ACL）。
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 45870dd7d3035b6b49340fd6e8016794088e775a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061564"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 JAVA 管理 Azure 資料存儲庫第 2 代中的目錄、檔和 ACL

本文介紹如何使用 JAVA 在啟用了階層命名空間 （HNS） 的存儲帳戶中創建和管理目錄、檔和許可權。 

[包（Maven）](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [樣本](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API 參考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html) | [第 1 代到第 2 代映射](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [提供回饋](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 已啟用階層命名空間 （HNS） 的存儲帳戶。 按照[這些](data-lake-storage-quickstart-create-account.md)說明創建一個。

## <a name="set-up-your-project"></a>設定專案

要開始，打開[此頁面](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)並找到最新版本的 JAVA 庫。 然後，打開文字編輯器中的*pom.xml*檔。 增加參考該版本的依賴項元素。

如果計畫使用 Azure 活動目錄 （AD） 對用戶端應用程式進行身份驗證，則向 Azure 金鑰用戶端庫添加依賴項。 請參閱[將機密用戶端庫包添加到您的專案](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project)。

接下來，將這些導入語句添加到代碼檔中。

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

## <a name="connect-to-the-account"></a>連接到帳戶 

要使用本文中的程式碼片段，您需要創建表示存儲帳戶**的 DataLakeServiceClient**實例。 

### <a name="connect-by-using-an-account-key"></a>使用帳戶金鑰進行連接

這是連接到帳戶的最簡單方法。 

本示例使用帳戶金鑰創建**DataLakeServiceClient**實例。

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

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>使用 Azure 活動目錄 （Azure AD） 進行連接

可以使用 JAVA[的 Azure 標識用戶端庫](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)使用 Azure AD 對應用程式進行身份驗證。

本示例使用用戶端 ID、用戶端機密和租戶 ID 創建**DataLakeServiceClient**實例。  要獲取這些值，請參閱[從 Azure AD 獲取權杖以授權來自用戶端應用程式的請求](../common/storage-auth-aad-app.md)。

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
> 有關詳細資訊，請參閱 Azure[標識用戶端庫以瞭解 JAVA](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)文檔。


## <a name="create-a-file-system"></a>建立檔案系統

檔案系統充當檔的容器。 您可以通過調用**DataLakeServiceClient.createSystem**方法來創建一個。

本示例創建名為 的`my-file-system`檔案系統。 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>建立目錄

通過調用**DataLakeFileSystemClient.createDirectory**方法創建目錄引用。

本示例將名為 的`my-directory`目錄添加到檔案系統，然後添加名為`my-subdirectory`的子目錄。 

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

## <a name="rename-or-move-a-directory"></a>重新命名目錄或移動目錄

通過調用**DataLakeDirectoryClient.重命名**方法重命名或移動目錄。 傳遞所需目錄的路徑參數。 

本示例將子目錄重命名為 名稱`my-subdirectory-renamed`。

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-subdirectory-renamed");
}
```

本示例將名為`my-subdirectory-renamed`的目錄移動到名為`my-directory-2`的目錄的子目錄。 

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

通過調用**DataLakeDirectoryClient.delete 與回應方法刪除**目錄。

此示例刪除名為 的`my-directory`目錄。   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>管理目錄 ACL

此示例獲取並設置名為 的`my-directory`目錄的 ACL。 此示例授予擁有的使用者讀取、寫入和執行許可權，僅授予擁有組讀取和執行許可權，並授予所有其他讀取存取許可權。

> [!NOTE]
> 如果應用程式使用 Azure 活動目錄 （Azure AD） 授權訪問，則請確保已分配應用程式用於授權訪問的安全[主體。](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) 要瞭解有關如何應用 ACL 許可權及其更改效果的更多內容，請參閱[Azure 資料湖存儲 Gen2 中的訪問控制項](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

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

## <a name="upload-a-file-to-a-directory"></a>將檔上載到目錄

首先，通過創建**DataLakeFileClient**類的實例在目標目錄中創建檔引用。 通過調用**DataLakeFileClient.append**方法上傳檔。 請確保通過調用**DataLakeFileClient.FlushAsync**方法完成上載。

本示例將文字檔上載到名為`my-directory`的目錄。

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
> 如果檔案大小較大，則代碼必須對**DataLakeFileClient.append**方法進行多次調用。 請考慮使用**DataLakeFileClient.上傳從檔**的方法。 這樣，您可以在單個調用中上載整個檔。 
>
> 如需範例，請參閱下一節。

## <a name="upload-a-large-file-to-a-directory"></a>將大型檔上載到目錄

使用**DataLakeFileClient.uploadFromFile**方法上載大型檔，而無需對**DataLakeFileClient.append**方法進行多次調用。

```java
static public void UploadFileBulk(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.getFileClient("uploaded-file.txt");

    fileClient.uploadFromFile("C:\\mytestfile.txt");

    }

```


## <a name="manage-a-file-acl"></a>管理檔 ACL

此示例獲取並設置名為`upload-file.txt`的檔的 ACL。 此示例授予擁有的使用者讀取、寫入和執行許可權，僅授予擁有組讀取和執行許可權，並授予所有其他讀取存取許可權。

> [!NOTE]
> 如果應用程式使用 Azure 活動目錄 （Azure AD） 授權訪問，則請確保已分配應用程式用於授權訪問的安全[主體。](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) 要瞭解有關如何應用 ACL 許可權及其更改效果的更多內容，請參閱[Azure 資料湖存儲 Gen2 中的訪問控制項](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

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

## <a name="download-from-a-directory"></a>從目錄中下載

首先，創建一個表示要下載的檔**的資料湖檔用戶端**實例。 使用**DataLakeFileClient.read**方法讀取檔。 使用任何 .NET 檔處理 API 將位元組從流保存到檔。 

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

此示例列印位於名為`my-directory`的目錄中的每個檔的名稱。

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
* [包裝（馬文）](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [樣品](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [第 1 代到第 2 代映射](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [已知問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [提供回饋](https://github.com/Azure/azure-sdk-for-java/issues)