---
title: Azure Data Lake Storage Gen2 Python SDK for files & Acl
description: 在已啟用階層命名空間 (HNS) 的儲存體帳戶中，使用 Python [管理目錄] 和 [檔案和目錄存取控制清單 (ACL) 。
author: normesta
ms.service: storage
ms.date: 09/10/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: fc99bc645b48739d6d6339111780047496c1984d
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90017110"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 Python 來管理 Azure Data Lake Storage Gen2 中的目錄、檔案和 Acl

本文說明如何使用 Python 來建立和管理已啟用階層命名空間 (HNS) 之儲存體帳戶中的目錄、檔案和許可權。 

[套件 (Python 套件索引) ](https://pypi.org/project/azure-storage-file-datalake/)  | [範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  | [API 參考](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  | [Gen1 至 Gen2 對應](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  | [提供意見](https://github.com/Azure/azure-sdk-for-python/issues)反應

## <a name="prerequisites"></a>必要條件

> [!div class="checklist"]
> * Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 已啟用階層命名空間 (HNS) 的儲存體帳戶。 遵循[下列](data-lake-storage-quickstart-create-account.md)指示以建立帳戶。

## <a name="set-up-your-project"></a>設定專案

使用 [pip](https://pypi.org/project/pip/)安裝適用于 Python 的 Azure Data Lake Storage 用戶端程式庫。

```
pip install azure-storage-file-datalake
```

將這些 import 語句新增至程式碼檔案的頂端。

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>連線到帳戶

若要使用本文中的程式碼片段，您必須建立代表儲存體帳戶的 **DataLakeServiceClient** 實例。 

### <a name="connect-by-using-an-account-key"></a>使用帳戶金鑰進行連接

這是連接到帳戶的最簡單方式。 

此範例會使用帳戶金鑰建立 **DataLakeServiceClient** 實例。

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- 使用您的儲存體帳戶名稱取代 `storage_account_name` 預留位置值。

- 將 `storage_account_key` 預留位置值取代為您的儲存體帳戶存取金鑰。

### <a name="connect-by-using-azure-active-directory-ad"></a>使用 Azure Active Directory (AD) 連接

您可以使用 [適用于 Python 的 Azure 身分識別用戶端程式庫](https://pypi.org/project/azure-identity/) ，以 Azure AD 來驗證您的應用程式。

此範例會使用用戶端識別碼、用戶端密碼和租使用者識別碼來建立 **DataLakeServiceClient** 實例。  若要取得這些值，請參閱 [從 Azure AD 取得權杖，以授權用戶端應用程式的要求](../common/storage-auth-aad-app.md)。

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> 如需更多範例，請參閱 [適用于 Python 的 Azure 身分識別用戶端程式庫](https://pypi.org/project/azure-identity/) 檔。

## <a name="create-a-container"></a>建立容器

容器可作為檔案的檔案系統。 您可以藉由呼叫 FileSystemDataLakeServiceClient 來建立一個。 **create_file_system** 方法。

這個範例會建立名為的容器 `my-file-system` 。

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>建立目錄

藉由呼叫 FileSystemClient 來建立目錄參考。 **create_directory** 方法。

此範例會將名為 `my-directory` 的目錄新增至容器。 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>重新命名或移動目錄

藉由呼叫 DataLakeDirectoryClient 來重新命名或移動目錄 **。 rename_directory** 方法。 傳遞所需目錄的路徑給參數。 

此範例會將子目錄重新命名為名稱 `my-subdirectory-renamed` 。

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>刪除目錄

藉由呼叫 DataLakeDirectoryClient 來刪除目錄 **。 delete_directory** 方法。

此範例刪除名為 `my-directory` 的目錄。  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>管理目錄許可權

藉由呼叫 **Get_access_control DataLakeDirectoryClient** ，來取得目錄 (acl) 的存取控制清單，並藉由呼叫 **DataLakeDirectoryClient set_access_control** 方法來設定 acl。

> [!NOTE]
> 如果您的應用程式使用 Azure Active Directory (Azure AD) 來授與存取權，請確定您的應用程式用來授與存取權的安全性主體已獲指派 [儲存體 Blob 資料擁有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要深入了解如何套用 ACL 權限以及變更權限的效果，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

這個範例會取得並設定名為之目錄的 ACL `my-directory` 。 字串 `rwxr-xrw-` 提供擁有使用者的讀取、寫入和執行許可權，僅提供擁有群組的讀取和執行許可權，並提供所有其他的讀取和寫入權限。

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

您也可以取得及設定容器根目錄的 ACL。 若要取得根目錄，請呼叫 **_Get_root_directory_client FileSystemClient** 方法。

## <a name="upload-a-file-to-a-directory"></a>將檔案上傳至目錄 

首先，藉由建立 **DataLakeFileClient** 類別的實例，在目標目錄中建立檔案參考。 藉由呼叫 DataLakeFileClient 方法來上傳檔案。 **append_data** 方法。 請務必藉由呼叫 DataLakeFileClient 方法來完成上傳。 **flush_data** 方法。

此範例會將文字檔上傳至名為的目錄 `my-directory` 。   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'rb')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

> [!TIP]
> 如果您的檔案大小很大，您的程式碼就必須對 DataLakeFileClient 進行多次呼叫 **。 append_data** 方法。 請考慮改為使用 **Upload_data DataLakeFileClient** 方法。 如此一來，您就可以在單一呼叫中上傳整個檔案。 

## <a name="upload-a-large-file-to-a-directory"></a>將大型檔案上傳至目錄

使用 **Upload_data DataLakeFileClient** 方法來上傳大型檔案，而不需要對 **append_data DataLakeFileClient** 進行多次呼叫。

```python
def upload_file_to_directory_bulk():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        local_file = open("C:\\file-to-upload.txt",'rb')

        file_contents = local_file.read()

        file_client.upload_data(file_contents, overwrite=True)

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>管理檔案許可權

藉由呼叫 **Get_access_control DataLakeFileClient** 來取得檔案 (acl) 的存取控制清單，並藉由呼叫 **DataLakeFileClient set_access_control** 方法來設定 acl。

> [!NOTE]
> 如果您的應用程式使用 Azure Active Directory (Azure AD) 來授與存取權，請確定您的應用程式用來授與存取權的安全性主體已獲指派 [儲存體 Blob 資料擁有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要深入了解如何套用 ACL 權限以及變更權限的效果，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

這個範例會取得並設定名為之檔案的 ACL `my-file.txt` 。 字串 `rwxr-xrw-` 提供擁有使用者的讀取、寫入和執行許可權，僅提供擁有群組的讀取和執行許可權，並提供所有其他的讀取和寫入權限。

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## <a name="download-from-a-directory"></a>從目錄下載 

開啟要寫入的本機檔案。 然後，建立代表您要下載之檔案的 **DataLakeFileClient** 實例。 呼叫 **Read_file DataLakeFileClient** ，以從檔案讀取位元組，然後將這些位元組寫入本機檔案。 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        download = file_client.download_file()

        downloaded_bytes = download.readall()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>列出目錄內容

藉由呼叫 **Get_paths FileSystemClient** 方法來列出目錄內容，然後再列舉結果。

此範例會列印每個子目錄和檔案的路徑，該目錄位於名為的目錄中 `my-directory` 。

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="set-an-acl-recursively-preview"></a>以遞迴方式設定 ACL (預覽) 

您可以在父目錄的現有子專案上以遞迴方式新增、更新和移除 Acl，而不需要為每個子專案個別進行這些變更。 如需詳細資訊，請參閱 [設定 (acl 的存取控制清單) 以遞迴方式進行 Azure Data Lake Storage Gen2](recursive-access-control-lists.md)。

## <a name="see-also"></a>另請參閱

* [API 參考文件](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
* [套件 (Python 套件索引)](https://pypi.org/project/azure-storage-file-datalake/) \(英文\)
* [範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Gen1 至 Gen2 對應](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [已知問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [提供意見反應](https://github.com/Azure/azure-sdk-for-python/issues)
