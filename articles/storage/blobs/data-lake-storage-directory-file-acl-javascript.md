---
title: 在 Azure Data Lake Storage Gen2 中使用 & Acl 的 JavaScript
description: 使用 Azure 儲存體 Data Lake 適用于 JavaScript 的用戶端程式庫，來管理已啟用階層命名空間（HNS）之儲存體帳戶中的目錄和檔案和目錄存取控制清單（ACL）。
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-javascript
ms.openlocfilehash: 648a47b451e8857c1cd84c2bcecdaae7fcaa065d
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87430542"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 JavaScript 管理 Azure Data Lake Storage Gen2 中的目錄、檔案和 Acl

本文說明如何使用 JavaScript 來建立和管理已啟用階層命名空間（HNS）之儲存體帳戶中的目錄、檔案和許可權。 

[封裝（Node Package Manager）](https://www.npmjs.com/package/@azure/storage-file-datalake)  | [範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  | [提供意見](https://github.com/Azure/azure-sdk-for-java/issues)反應

## <a name="prerequisites"></a>必要條件

> [!div class="checklist"]
> * Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 已啟用階層命名空間 (HNS) 的儲存體帳戶。 遵循[下列](data-lake-storage-quickstart-create-account.md)指示以建立帳戶。
> * 如果您在 Node.js 應用程式中使用此套件，您將需要 Node.js 8.0.0 版或更高版本。

## <a name="set-up-your-project"></a>設定專案

開啟終端機視窗，然後輸入下列命令，以安裝適用于 JavaScript 的 Data Lake 用戶端程式庫。

```javascript
npm install @azure/storage-file-datalake
```

將 `storage-file-datalake` 此語句放在程式碼檔案的頂端，以匯入封裝。 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>連線到帳戶 

若要使用本文中的程式碼片段，您必須建立代表儲存體帳戶的**DataLakeServiceClient**實例。 

### <a name="connect-by-using-an-account-key"></a>使用帳戶金鑰進行連接

這是連接到帳戶最簡單的方式。 

這個範例會使用帳戶金鑰來建立**DataLakeServiceClient**實例。

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```
> [!NOTE]
> 這種授權方法僅適用于 Node.js 應用程式。 如果您打算在瀏覽器中執行程式碼，您可以使用 Azure Active Directory （AD）來進行授權。 

### <a name="connect-by-using-azure-active-directory-ad"></a>使用 Azure Active Directory （AD）進行連接

您可以使用[適用于 JS 的 Azure 身分識別用戶端程式庫](https://www.npmjs.com/package/@azure/identity)，透過 Azure AD 來驗證您的應用程式。

這個範例會使用用戶端識別碼、用戶端密碼和租使用者識別碼來建立**DataLakeServiceClient**實例。  若要取得這些值，請參閱[從 Azure AD 取得權杖，以從用戶端應用程式授權要求](../common/storage-auth-aad-app.md)。

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> 如需更多範例，請參閱[適用于 JS 的 Azure 身分識別用戶端程式庫](https://www.npmjs.com/package/@azure/identity)檔。

## <a name="create-a-container"></a>建立容器

容器會作為檔案的檔案系統。 您可以藉由取得**FileSystemClient**實例，然後呼叫**FileSystemClient. create**方法來建立一個。

這個範例會建立名為的容器 `my-file-system` 。 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>建立目錄

藉由取得**DirectoryClient**實例，然後呼叫**DirectoryClient. create**方法來建立目錄參考。

這個範例會將名為的目錄新增 `my-directory` 至容器。 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>重新命名或移動目錄

藉由呼叫 DirectoryClient 重新命名或移動目錄 **。** 傳遞所需目錄的路徑 a 參數。 

這個範例會將子目錄重新命名為名稱 `my-directory-renamed` 。

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

這個範例會將名為的目錄移 `my-directory-renamed` 至名為之目錄的 `my-directory-2` 子目錄。 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>刪除目錄

藉由呼叫**DirectoryClient**方法來刪除目錄。

此範例刪除名為 `my-directory` 的目錄。   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>管理目錄 ACL

這個範例會取得並設定名為之目錄的 ACL `my-directory` 。 這個範例提供擁有使用者的讀取、寫入和執行許可權，授與擁有群組 [讀取] 和 [執行] 許可權，並提供其他所有讀取權限。

> [!NOTE]
> 如果您的應用程式使用 Azure Active Directory （Azure AD）來授權存取，請確定您的應用程式用來授權存取的安全性主體已獲指派[儲存體 Blob 資料擁有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要深入了解如何套用 ACL 權限以及變更權限的效果，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

您也可以取得並設定容器根目錄的 ACL。 若要取得根目錄，請將空字串（ `/` ）傳遞至**DataLakeFileSystemClient. getDirectoryClient**方法。

## <a name="upload-a-file-to-a-directory"></a>將檔案上傳至目錄

首先，讀取檔案。 這個範例會使用 Node.js `fs` 模組。 然後，藉由建立**FileClient**實例，然後呼叫**FileClient. create**方法，在目標目錄中建立檔案參考。 藉由呼叫**FileClient. append**方法來上傳檔案。 請務必呼叫**FileClient**方法來完成上傳。

這個範例會將文字檔上傳至名為的目錄 `my-directory` 。

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="manage-a-file-acl"></a>管理檔案 ACL

這個範例會取得並設定名為之檔案的 ACL `upload-file.txt` 。 這個範例提供擁有使用者的讀取、寫入和執行許可權，授與擁有群組 [讀取] 和 [執行] 許可權，並提供其他所有讀取權限。

> [!NOTE]
> 如果您的應用程式使用 Azure Active Directory （Azure AD）來授權存取，請確定您的應用程式用來授權存取的安全性主體已獲指派[儲存體 Blob 資料擁有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要深入了解如何套用 ACL 權限以及變更權限的效果，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="download-from-a-directory"></a>從目錄下載

首先，建立代表您要下載之檔案的**FileSystemClient**實例。 使用**FileSystemClient**讀取檔案。 然後，寫入檔案。 這個範例會使用 Node.js `fs` 模組來執行此動作。 

> [!NOTE]
> 此下載檔案的方法僅適用于 Node.js 應用程式。 如果您打算在瀏覽器中執行程式碼，請參閱 JavaScript 讀我檔案的[Azure 儲存體檔案 Data Lake 用戶端程式庫](https://www.npmjs.com/package/@azure/storage-file-datalake)，以取得如何在瀏覽器中執行這項操作的範例。 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>列出目錄內容

這個範例會列印每個目錄的名稱，以及位於名為的目錄中的檔案名 `my-directory` 。

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>另請參閱

* [套件 (節點套件管理員)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [提供意見反應](https://github.com/Azure/azure-sdk-for-java/issues)