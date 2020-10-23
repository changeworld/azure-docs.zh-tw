---
title: Microsoft Azure Stack Edge Blob 儲存體需求 |Microsoft Docs
description: 瞭解適用于 Azure Stack Edge Blob 儲存體之 Api、Sdk 和用戶端程式庫的支援版本
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: f99a3110880626b3a809e6bab5edc02398094547
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426217"
---
# <a name="azure-stack-edge-blob-storage-requirements"></a>Azure Stack Edge Blob 儲存體需求

本文列出 Azure Stack Edge Blob 儲存體所支援的 Azure Api、Azure 用戶端程式庫和工具的版本。 Azure Stack Edge Blob 儲存體使用 Azure 一致的語義來提供 Blob 管理功能。 本文也會摘要說明與 Azure 儲存體服務的已知 Azure Stack Edge Blob 儲存體差異。

建議您在連接到 Azure Stack Edge Blob 儲存體之前仔細檢查資訊，然後視需要回頭查看。

## <a name="storage-differences"></a>儲存體差異

|     功能                                             |     Azure 儲存體                                     |     Azure Stack Edge Blob 儲存體 |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure 檔案儲存體                                   |    支援雲端式 SMB 檔案共用              |    不支援      |
|    儲存體帳戶類型                                 |    一般用途和 Azure Blob 儲存體帳戶    |    僅限一般用途 v1|
|    Blob 名稱                                            |    1,024 個字元 (2,048 個位元組)                     |    880 個字元 (1,760 個位元組)|
|    區塊 Blob 大小上限                              |    4.75 TB (100 MB X 50,000 個區塊)                   |    4.75 TB (100 MB x 50000 區塊) 適用于 Azure Stack Edge|
|    分頁 Blob 大小上限                               |    8 TB                                               |    1 TB                   |
|    分頁 Blob 分頁大小                                  |    512 個位元組                                          |    4 KB                   |

## <a name="supported-api-versions"></a>支援的 API 版本

Azure Stack Edge Blob 儲存體支援下列 Azure 儲存體服務 Api 版本。

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge 2.1.1377.2170

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>支援的 Azure 用戶端程式庫

針對 Azure Stack Edge Blob 儲存體，有特定的用戶端程式庫和特定的端點尾碼需求。 Azure Stack Edge Blob 儲存體端點與最新版的 Azure Blob 儲存體 REST API 沒有完全相同的相同;如 Azure Stack Edge，請參閱 [支援的 API 版本](#supported-api-versions)。 對於儲存體用戶端程式庫，您需要知道與 REST API 相容的版本。

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge 2.1.1377.2170

Azure Stack Edge Blob 儲存體支援下列 Azure 用戶端程式庫版本。

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-the-php-client-via-composer---current"></a>透過編輯器安裝 PHP 用戶端-目前

若要透過編輯器安裝 PHP 用戶端：

1. 使用下列程式碼，在專案的根目錄中建立名為 composer.js的檔案 (範例會使用 Azure 儲存體 Blob service) 。

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. 將 `composer.phar` 下載至專案根目錄。

3. 執行：php composer.phar install。


## <a name="endpoint-declaration"></a>端點宣告

在 Azure Stack Edge Blob 儲存體 SDK 中，端點尾碼- `<device serial number>.microsoftdatabox.com` -識別 Azure Stack Edge 網域。 如需 blob 服務端點的詳細資訊，請移至 [使用 Azure Stack Edge PRO GPU 的儲存體帳戶傳輸資料](azure-stack-edge-j-series-deploy-add-storage-accounts.md)。


## <a name="examples"></a>範例

### <a name="net"></a>.NET

針對 Azure Stack Edge Blob 儲存體，會在檔案中指定端點尾碼 `app.config` ：

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

針對 Azure Stack Edge Blob 儲存體，在連接字串設定中會指定端點尾碼：

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

針對 Azure Stack Edge Blob 儲存體，端點尾碼會指定于宣告實例中：

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

針對 Azure Stack Edge Blob 儲存體，在連接字串設定中會指定端點尾碼：

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

針對 Azure Stack Edge Blob 儲存體，在連接字串設定中會指定端點尾碼：

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

針對 Azure Stack Edge Blob 儲存體，端點尾碼會指定于宣告實例中：

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

針對 Azure Stack Edge Blob 儲存體，在連接字串設定中會指定端點尾碼：

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>後續步驟

* [準備使用 GPU 部署 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)
