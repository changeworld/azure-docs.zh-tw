---
title: 在 Azure 資料湖存儲 Gen2 中對檔& ACL 使用 JavaScript
description: 使用 JavaScript 的 Azure 存儲資料湖用戶端庫管理已啟用階層命名空間 （HNS） 的存儲帳戶中的目錄、檔和目錄存取控制清單 （ACL）。
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 04d0d23bdbdaeda6a4823c900badb3133ba9eeae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061548"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 JavaScript 管理 Azure 資料存儲庫 Gen2 中的目錄、檔和 ACL

本文介紹如何使用 JavaScript 在已啟用階層命名空間 （HNS） 的存儲帳戶中創建和管理目錄、檔和許可權。 

[包（節點包管理器）](https://www.npmjs.com/package/@azure/storage-file-datalake) | [示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [提供回饋](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 已啟用階層命名空間 （HNS） 的存儲帳戶。 按照[這些](data-lake-storage-quickstart-create-account.md)說明創建一個。
> * 如果在 Node.js 應用程式中使用此包，則需要 Node.js 8.0.0 或更高版本。

## <a name="set-up-your-project"></a>設定專案

通過打開終端視窗，然後鍵入以下命令，為 JavaScript 安裝資料湖用戶端庫。

```javascript
npm install @azure/storage-file-datalake
```

通過將此`storage-file-datalake`語句放在代碼檔的頂部來導入包。 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>連接到帳戶 

要使用本文中的程式碼片段，您需要創建表示存儲帳戶**的 DataLakeServiceClient**實例。 

### <a name="connect-by-using-an-account-key"></a>使用帳戶金鑰進行連接

這是連接到帳戶的最簡單方法。 

本示例使用帳戶金鑰創建**DataLakeServiceClient**實例。

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
> 這種授權方法僅適用于 Node.js 應用程式。 如果計畫在瀏覽器中運行代碼，則可以使用 Azure 活動目錄 （AD） 進行授權。 

### <a name="connect-by-using-azure-active-directory-ad"></a>使用 Azure 活動目錄 （AD） 進行連接

可以使用 JS[的 Azure 標識用戶端庫](https://www.npmjs.com/package/@azure/identity)使用 Azure AD 對應用程式進行身份驗證。

本示例使用用戶端 ID、用戶端機密和租戶 ID 創建**DataLakeServiceClient**實例。  要獲取這些值，請參閱[從 Azure AD 獲取權杖以授權來自用戶端應用程式的請求](../common/storage-auth-aad-app.md)。

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> 有關詳細資訊，請參閱 Azure[標識用戶端庫，瞭解 JS](https://www.npmjs.com/package/@azure/identity)文檔。

## <a name="create-a-file-system"></a>建立檔案系統

檔案系統充當檔的容器。 您可以通過獲取**檔案系統用戶端**實例，然後調用**FileSystemClient.Create**方法來創建一個實例。

本示例創建名為 的`my-file-system`檔案系統。 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>建立目錄

通過獲取**目錄用戶端**實例，然後調用**目錄用戶端.create**方法，創建目錄引用。

本示例向檔案系統添加名為`my-directory`的目錄。 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>重新命名目錄或移動目錄

通過調用**DirectoryClient.重命名**方法重命名或移動目錄。 傳遞所需目錄的路徑參數。 

本示例將子目錄重命名為 名稱`my-directory-renamed`。

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

本示例將名為`my-directory-renamed`的目錄移動到名為`my-directory-2`的目錄的子目錄。 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>刪除目錄

通過調用**目錄用戶端.delete**方法刪除目錄。

此示例刪除名為 的`my-directory`目錄。   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>管理目錄 ACL

此示例獲取並設置名為 的`my-directory`目錄的 ACL。 此示例授予擁有的使用者讀取、寫入和執行許可權，僅授予擁有組讀取和執行許可權，並授予所有其他讀取存取許可權。

> [!NOTE]
> 如果應用程式使用 Azure 活動目錄 （Azure AD） 授權訪問，則請確保已分配應用程式用於授權訪問的安全[主體。](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) 要瞭解有關如何應用 ACL 許可權及其更改效果的更多內容，請參閱[Azure 資料湖存儲 Gen2 中的訪問控制項](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

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

## <a name="upload-a-file-to-a-directory"></a>將檔上載到目錄

首先，讀取檔。 此示例使用 Node.js`fs`模組。 然後，通過創建**FileClient**實例，然後調用**FileClient.create**方法，在目標目錄中創建檔引用。 通過調用**FileClient.append**方法上載檔。 請確保通過調用**FileClient.flush**方法完成上載。

本示例將文字檔上載到名為`my-directory`的目錄。

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

## <a name="manage-a-file-acl"></a>管理檔 ACL

此示例獲取並設置名為`upload-file.txt`的檔的 ACL。 此示例授予擁有的使用者讀取、寫入和執行許可權，僅授予擁有組讀取和執行許可權，並授予所有其他讀取存取許可權。

> [!NOTE]
> 如果應用程式使用 Azure 活動目錄 （Azure AD） 授權訪問，則請確保已分配應用程式用於授權訪問的安全[主體。](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) 要瞭解有關如何應用 ACL 許可權及其更改效果的更多內容，請參閱[Azure 資料湖存儲 Gen2 中的訪問控制項](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

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

## <a name="download-from-a-directory"></a>從目錄中下載

首先，創建一個表示要下載的檔的**FileSystemClient**實例。 使用**FileSystemClient.read**方法讀取該檔。 然後，編寫檔。 此示例使用 Node.js`fs`模組執行此操作。 

> [!NOTE]
> 這種下載檔案的方法僅適用于 Node.js 應用程式。 如果計畫在瀏覽器中運行代碼，請參閱 JavaScript readme 檔的[Azure 存儲檔資料湖用戶端庫](https://www.npmjs.com/package/@azure/storage-file-datalake)，瞭解如何在瀏覽器中執行此操作。 

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

此示例列印位於名為`my-directory`的目錄中的每個目錄和檔的名稱。

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

* [包（節點包管理器）](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [樣品](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [提供回饋](https://github.com/Azure/azure-sdk-for-java/issues)