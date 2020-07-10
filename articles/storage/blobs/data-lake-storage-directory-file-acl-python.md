---
title: Azure Data Lake Storage Gen2 適用于檔案 & Acl 的 Python SDK
description: 在已啟用階層式命名空間 (HNS) 的儲存體帳戶中，使用 Python 管理目錄和檔案和目錄存取控制清單 (ACL) 。
author: normesta
ms.service: storage
ms.date: 04/10/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: tracking-python
ms.openlocfilehash: 07708de1326e0aba6485b2cf1fb0610d9710cdf7
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142479"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 Python 來管理 Azure Data Lake Storage Gen2 中的目錄、檔案和 Acl

本文說明如何使用 Python 來建立和管理已啟用階層命名空間 (HNS) 的儲存體帳戶中的目錄、檔案和許可權。 

[套件 (Python 套件索引) ](https://pypi.org/project/azure-storage-file-datalake/)  | [範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  | [API 參考](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0/azure.storage.filedatalake.html)  | [Gen1 至 Gen2 對應](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  | [提供意見](https://github.com/Azure/azure-sdk-for-python/issues)反應

## <a name="prerequisites"></a>必要條件

> [!div class="checklist"]
> * Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 已啟用階層命名空間 (HNS) 的儲存體帳戶。 遵循[下列](data-lake-storage-quickstart-create-account.md)指示以建立帳戶。

## <a name="set-up-your-project"></a>設定專案

使用[pip](https://pypi.org/project/pip/)安裝適用于 Python 的 Azure Data Lake Storage 用戶端程式庫。

```
pip install azure-storage-file-datalake
```

將這些匯入語句新增至程式碼檔案的頂端。

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>連線到帳戶

若要使用本文中的程式碼片段，您必須建立代表儲存體帳戶的**DataLakeServiceClient**實例。 

### <a name="connect-by-using-an-account-key"></a>使用帳戶金鑰進行連接

這是連接到帳戶最簡單的方式。 

這個範例會使用帳戶金鑰來建立**DataLakeServiceClient**實例。

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

### <a name="connect-by-using-azure-active-directory-ad"></a>使用 Azure Active Directory (AD) 進行連接

您可以使用[適用于 Python 的 Azure 身分識別用戶端程式庫](https://pypi.org/project/azure-identity/)，透過 Azure AD 來驗證您的應用程式。

這個範例會使用用戶端識別碼、用戶端密碼和租使用者識別碼來建立**DataLakeServiceClient**實例。  若要取得這些值，請參閱[從 Azure AD 取得權杖，以從用戶端應用程式授權要求](../common/storage-auth-aad-app.md)。

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
> 如需更多範例，請參閱[適用于 Python 的 Azure 身分識別用戶端程式庫](https://pypi.org/project/azure-identity/)檔。

## <a name="create-a-container"></a>建立容器

容器會作為檔案的檔案系統。 您可以藉由呼叫 FileSystemDataLakeServiceClient 來建立一個。 **create_file_system**方法。

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

藉由呼叫 FileSystemClient 來建立目錄參考。 **create_directory**方法。

這個範例會將名為的目錄新增 `my-directory` 至容器。 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>重新命名或移動目錄

藉由呼叫 DataLakeDirectoryClient 來重新命名或移動目錄 **。 rename_directory**方法。 傳遞所需目錄的路徑 a 參數。 

這個範例會將子目錄重新命名為名稱 `my-subdirectory-renamed` 。

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

藉由呼叫 DataLakeDirectoryClient 來刪除目錄。 **delete_directory**方法。

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

藉由呼叫**DataLakeDirectoryClient. get_access_control**方法來取得目錄 (acl) 的存取控制清單，並藉由呼叫**DataLakeDirectoryClient. set_access_control**方法來設定 acl。

> [!NOTE]
> 如果您的應用程式使用 Azure Active Directory (Azure AD) 來授權存取，請確定您的應用程式用來授權存取的安全性主體已獲指派[儲存體 Blob 資料擁有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要深入了解如何套用 ACL 權限以及變更權限的效果，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

這個範例會取得並設定名為之目錄的 ACL `my-directory` 。 此字串 `rwxr-xrw-` 提供擁有使用者的讀取、寫入和執行許可權，授與擁有群組 [讀取] 和 [執行] 許可權，並提供所有其他讀取和寫入權限。

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

## <a name="upload-a-file-to-a-directory"></a>將檔案上傳至目錄 

首先，建立**DataLakeFileClient**類別的實例，以建立目標目錄中的檔案參考。 藉由呼叫 DataLakeFileClient 來上傳檔案。 **append_data**方法。 請務必呼叫 DataLakeFileClient 來完成上傳 **。 flush_data**方法。

這個範例會將文字檔上傳至名為的目錄 `my-directory` 。   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

> [!TIP]
> 如果您的檔案大小很大，您的程式碼就必須對**Append_data DataLakeFileClient**方法進行多次呼叫。 請考慮改為使用**Upload_data DataLakeFileClient**方法。 如此一來，您就可以在單一呼叫中上傳整個檔案。 

## <a name="upload-a-large-file-to-a-directory"></a>將大型檔案上傳至目錄

使用**Upload_data DataLakeFileClient**方法來上傳大型檔案，而不需要對**append_data DataLakeFileClient**方法進行多次呼叫。

```python
def upload_file_to_directory_bulk():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.upload_data(file_contents, overwrite=True)

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>管理檔案許可權

藉由呼叫**DataLakeFileClient. get_access_control**方法來取得檔案 (acl) 的存取控制清單，並藉由呼叫**DataLakeFileClient. set_access_control**方法來設定 acl。

> [!NOTE]
> 如果您的應用程式使用 Azure Active Directory (Azure AD) 來授權存取，請確定您的應用程式用來授權存取的安全性主體已獲指派[儲存體 Blob 資料擁有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要深入了解如何套用 ACL 權限以及變更權限的效果，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

這個範例會取得並設定名為之檔案的 ACL `my-file.txt` 。 此字串 `rwxr-xrw-` 提供擁有使用者的讀取、寫入和執行許可權，授與擁有群組 [讀取] 和 [執行] 許可權，並提供所有其他讀取和寫入權限。

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

開啟本機檔案進行寫入。 然後，建立代表您要下載之檔案的**DataLakeFileClient**實例。 呼叫**Read_file DataLakeFileClient**來讀取檔案中的位元組，然後將這些位元組寫入本機檔案。 

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

藉由呼叫**Get_paths FileSystemClient**方法來列出目錄內容，然後逐一列舉結果。

這個範例會列印位於名為的目錄中的每個子目錄和檔案的路徑 `my-directory` 。

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

## <a name="see-also"></a>另請參閱

* [API 參考文件](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [套件 (Python 套件索引) ](https://pypi.org/project/azure-storage-file-datalake/)
* [範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Gen1 至 Gen2 對應](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [已知問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [提供意見反應](https://github.com/Azure/azure-sdk-for-python/issues)
