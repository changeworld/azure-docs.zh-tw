---
title: 使用 Python 管理 Azure Data Lake Storage Gen1 帳戶
description: 瞭解如何使用 Python SDK 進行 Azure Data Lake Storage Gen1 帳戶管理作業。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-python
ms.openlocfilehash: 35e71b80c6f47bb13f7a2b490b493b0cb42acf04
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103504"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>使用 Python 對 Azure Data Lake Storage Gen1 進行帳戶管理作業
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

瞭解如何使用適用于 Azure Data Lake Storage Gen1 的 Python SDK 來執行基本帳戶管理作業，例如建立 Data Lake Storage Gen1 帳戶、列出 Data Lake Storage Gen1 帳戶等等。如需有關如何使用 Python 在 Data Lake Storage Gen1 上執行檔案系統作業的指示，請參閱 [使用 python Data Lake Storage Gen1 上的檔案系統作業](data-lake-store-data-operations-python.md)。

## <a name="prerequisites"></a>必要條件

* **Python**。 您可以從[這裡](https://www.python.org/downloads/)下載 Python。 本文使用 Python 3.6.2。

* **Azure 訂用帳戶**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure 資源群組**。 如需指示，請參閱[建立 Azure 資源群組](../azure-resource-manager/management/manage-resource-groups-portal.md)。

## <a name="install-the-modules"></a>安裝模組

若要透過 Python 使用 Data Lake Storage Gen1，您需要安裝三個模組。

* `azure-mgmt-resource` 模組，這包括適用於 Active Directory 等等的 Azure 模組。
* `azure-mgmt-datalake-store` 模組包括 Azure Data Lake Storage Gen1 帳戶管理作業。 如需關於此模組的詳細資訊，請參閱 [Azure Data Lake Storage Gen1 Management module reference (Azure Data Lake Storage Gen1 管理模組參考)](/python/api/azure-mgmt-datalake-store/)。
* `azure-datalake-store` 模組，含有 Azure Data Lake Storage Gen1 檔案系統作業。 如需關於此模組的詳細資訊，請參閱 [azure-datalake-store Filesystem module reference (azure-datalake-store 檔案系統模組參考)](/python/api/azure-datalake-store/azure.datalake.store.core/)。

使用下列命令來安裝新模組。

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

1. 在您選擇的 IDE 中，建立新的 Python 應用程式，例如 **mysample.py**。

2. 新增以下程式碼片段以匯入必要模組

    ```python
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. 將變更儲存至 mysample.py。

## <a name="authentication"></a>驗證

在本節中，我們會討論向 Azure AD 進行驗證的各種方式。 可用的選項如下︰

* 如需進行應用程式的使用者驗證，請參閱[使用 Python 向 Data Lake Storage Gen1 進行使用者驗證](data-lake-store-end-user-authenticate-python.md)。
* 如需進行應用程式的服務對服務驗證，請參閱[使用 Python 向 Data Lake Storage Gen1 進行服務對服務驗證](data-lake-store-service-to-service-authenticate-python.md)。

## <a name="create-client-and-data-lake-storage-gen1-account"></a>建立用戶端與 Data Lake Storage Gen1 帳戶

下列程式碼片段會先建立 Data Lake Storage Gen1 帳戶用戶端。 其使用用戶端物件來建立 Data Lake Storage Gen1 帳戶。 最後，程式碼片段會建立檔案系統用戶端物件。

```python
## Declare variables
subscriptionId = 'FILL-IN-HERE'
adlsAccountName = 'FILL-IN-HERE'
resourceGroup = 'FILL-IN-HERE'
location = 'eastus2'

## Create Data Lake Storage Gen1 account management client object
adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

## Create a Data Lake Storage Gen1 account
adlsAcctResult = adlsAcctClient.account.create(
    resourceGroup,
    adlsAccountName,
    DataLakeStoreAccount(
        location=location
    )
).wait()
```

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>列出 Data Lake Storage Gen1 帳戶

```python
## List the existing Data Lake Storage Gen1 accounts
result_list_response = adlsAcctClient.account.list()
result_list = list(result_list_response)
for items in result_list:
    print(items)
```

## <a name="delete-the-data-lake-storage-gen1-account"></a>刪除 Data Lake Storage Gen1 帳戶

```python
## Delete an existing Data Lake Storage Gen1 account
adlsAcctClient.account.delete(adlsAccountName)
```
    

## <a name="next-steps"></a>後續步驟
* [使用 Python 對 Data Lake Storage Gen1 進行檔案系統作業](data-lake-store-data-operations-python.md)。

## <a name="see-also"></a>另請參閱

* [azure-datalake-store Python (Filesystem) reference (azure-datalake-store Python (檔案系統) 參考)](/python/api/azure-datalake-store/azure.datalake.store.core)
* [與 Azure Data Lake Storage Gen1 相容的開放原始碼巨量資料應用程式](data-lake-store-compatible-oss-other-applications.md)